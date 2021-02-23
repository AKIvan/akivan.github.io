---
layout: post
title:  "AWS CodePipeline Multi Account Cross Region"
date:   2021-02-09
desc: "Multi Account Cross Region CodePipeline"
keywords: "AWS, CodePipeline, multi region, cross accounts"
categories: [HTML,DevOps]
tags: [AWS, CodePipeline, multi region, cross account]
icon: icon-html
---
<center>
<img src="/static/assets/img/blog/awspipeline/deploy.png"  />
</center>
<p>&nbsp;</p>

### Why do we need this?


There are many more use-cases where multi-account and cross-region CloudFormation stacks can be useful.
It happens a lot when you have one pipeline but the same deployment need to be done on different region, and also to cover the concept of test and production.
This is some main steps that can help you start from some Account like (Tools) and deployment goes to Test and Production Account but on two regions or at least can help you to build similar one. 

_AWS CodePipeline is a fully managed continuous delivery service that helps you automate your release pipelines for fast and reliable application and infrastructure updates. CodePipeline automates the build, test, and deploy phases of your release process every time there is a code change, based on the release model you define. This enables you to rapidly and reliably deliver features and updates._

So the first thing is S3 Bucket on each region that we desire to deploy. After the buckets are created in their respective region, I decided to use SSM Parameters to provide the Pipeline with the buckets.
Pipeline needs 1 bucket per target region. After this we can start with the pipeline itself.
<p>&nbsp;</p>


### CodeBuild

_AWS CodeBuild is a fully managed build service in the cloud. CodeBuild compiles your source code, runs unit tests, and produces artifacts that are ready to deploy._

The most relevant attributes here are the environment variables containing each region bucket and the buildSpec path which should be placed inside the source code that the Pipeline will fetch from GitHub template and have it as ready to deploy part. 
Prepare your environments to have S3 bucket to store the templates or source of the builds there. And if you are using encryption for it, that means you should crate KMS Keys for each region too.

The buildspec.yaml is responsible for building the template and put it in S3 bucket so later one we can use those templates and create the CloudFormation Stacks. 
The main command and stuff is happening in the build configuration section in this file.

    version: 0.2
    
    env:
      parameter-store:
        GIT_USER: "git-user"
        GIT_PASS: "git-pass"

      build:
        commands:
          - sam build -t template.yml
          - sam package --template template.yml --s3-bucket "$S3_BUCKET_region1" --s3-prefix "$PROJECT_ID/builds" --output-template template-export-region1.yml --region eu-west-1
          - sam package --template template.yml --s3-bucket "$S3_BUCKET_region2" --s3-prefix "$PROJECT_ID/builds" --output-template template-export-region2.yml --region ap-southeast-1

      post_build:
        commands:
    
    artifacts:
      type: zip
      files:
        - template-export-region1.yml
        - template-export-region2.yml

As you can see there is a different template export for each region and for each region there is a separate S3 bucket to store it, this can be put as one template but if you have different templates you can play it like this. 
<p>&nbsp;</p>


### CloudFormation

Back to the cloudformation template.
From the resource perspective we will start with defining the CodeBuild service. Note here that we are using this build for out template and as mention above, we need to create environment variables for each region/S3 bucket where we going to deploy it.

    BuildProject:
        Type: AWS::CodeBuild::Project
        Properties:
          Description: !Sub Code Build project generated for ${ProjectName}
          ServiceRole: !Ref BuildRole
          EncryptionKey: !Ref KMSKEY
          Environment:
            Type: linuxContainer
            ComputeType: BUILD_GENERAL1_SMALL
            Image: aws/codebuild/amazonlinux2-x86_64-standard:3.0
            EnvironmentVariables:
              - Name: S3_BUCKET_region1
                Value: !Ref S3BUCKETregion1
              - Name: KMSKey_region1
                Value: !Ref KMSKEYregion1
              
              - Name: S3_BUCKET_region2
                Value: !Ref S3BUCKETregion2
              - Name: KMSKey_region2
                Value: !Ref KMSKEYregion2
              
              - Name: ACCOUNT_ID
                Value: !Sub ${AWS::AccountId}
              - Name: AWSREGION
                Value: !Sub ${AWS::Region}
          Source:
            Type: CODEPIPELINE
          TimeoutInMinutes: 10
          Tags:
            - Key: Name
              Value: !Ref ProjectName
          Artifacts:
            Type: CODEPIPELINE
          Cache:
            Type: LOCAL
            Modes:
              - LOCAL_CUSTOM_CACHE
<p>&nbsp;</p>


### CodePipeline

Then we starting with the pipeline service or CodePipeline

      Pipeline:
        Type: AWS::CodePipeline::Pipeline
        Properties:
          RoleArn: !Ref PipelineRole
          Name: !Ref AWS::StackName
          Stages:
            - Name: Source
              Actions:
                - Name: Source
                  ActionTypeId:
                    Category: Source
                    Owner: ThirdParty
                    Version: 1
                    Provider: GitHub
                  Configuration:
                    Owner: GitHubOwner
                    Repo: !Sub ${ProjectName}
                    PollForSourceChanges: false
                    Branch: !Ref Branch
                    OAuthToken: "{{resolve:secretsmanager:GitHubOAuthToken:SecretString:GitHubOAuthToken}}"
                  OutputArtifacts:
                    - Name: SourceArtifact
                  RunOrder: 1
<p>&nbsp;</p>
As a first action in this stage we defined the source, GitHub. There can be different ways to defined, this is one template.
Note here that we defined the output artifact that later we going to reference to. 
The second action is the build.
<p>&nbsp;</p>

          Name: Build
          Actions:
          -
            Name: BuildAction
            ActionTypeId:
              Category: Build
              Owner: AWS
              Version: 1
              Provider: CodeBuild
            Configuration:
              ProjectName: !Ref BuildProject
            RunOrder: 1
            InputArtifacts:
              - Name: SourceArtifact
            OutputArtifacts:
              - Name: BuildOutput

<p>&nbsp;</p>
The main stuff here to point are InputArtifacts and OutputArtifacts. As input, we have the source from the previous action "SourceArtifiact" and as output we have already build stuff named as "BuildOutput".
Step three, we have the deployment processes.
<p>&nbsp;</p>

    - Name: Deploy
        Actions:

            - Name: CreateChangeSet-region1
              Region: region1
              ActionTypeId:
                Category: Deploy
                Owner: AWS
                Version: 1
                Provider: CloudFormation
              InputArtifacts:
                - Name: BuildOutput
              Configuration:
                ChangeSetName: project-name-stack
                ActionMode: CHANGE_SET_REPLACE
                StackName: !Sub '${ProjectName}-stack'
                Capabilities: CAPABILITY_NAMED_IAM,CAPABILITY_AUTO_EXPAND
                TemplatePath: BuildOutput::template-export-region1.yml
                RoleArn: !Ref DeployRole
              RunOrder: 1
              RoleArn: !Ref PipelineRole

        # *** Duplicate / edit / delete these stacks as necessary for your desired regions ***
          # *** Change this name to match desired region
        - Name: DeployToRegion1
          # *** Change this for desired region
          Region: region1
          ActionTypeId:
            Category: Deploy
            Owner: AWS
            Provider: CloudFormation
            Version: '1'
          InputArtifacts:
            - Name: BuildOutput
          Configuration:
            ActionMode: CREATE_UPDATE
            Capabilities: CAPABILITY_IAM,CAPABILITY_AUTO_EXPAND
            RoleArn: !GetAtt CloudformationRole.Arn
            StackName: !Ref ApplicationStackName
            # *** Change this to match the region of this stack, 
            # *** using the file generated in the build step
            TemplatePath: BuildOutput::template-export-region1.yml
          RunOrder: 2
          # *** Change this name to match desired region

            - Name: CreateChangeSet-region2
              Region: region2
              ActionTypeId:
                Category: Deploy
                Owner: AWS
                Version: 1
                Provider: CloudFormation
              InputArtifacts:
                - Name: BuildOutput
              Configuration:
                ChangeSetName: project-name-stack
                ActionMode: CHANGE_SET_REPLACE
                StackName: !Sub '${ProjectName}-stack'
                Capabilities: CAPABILITY_NAMED_IAM,CAPABILITY_AUTO_EXPAND
                TemplatePath: BuildOutput::template-export-region2.yml
                RoleArn: !Ref DeployRole
              RunOrder: 1
              RoleArn: !Ref PipelineRole

        - Name: DeployToRegion2
          # *** Change this for desired region
          Region: region2
          ActionTypeId:
            Category: Deploy
            Owner: AWS
            Provider: CloudFormation
            Version: '1'
          InputArtifacts:
            - Name: BuildOutput
          Configuration:
            ActionMode: CREATE_UPDATE
            Capabilities: CAPABILITY_IAM,CAPABILITY_AUTO_EXPAND
            RoleArn: !GetAtt CloudformationRole.Arn
            StackName: !Ref ApplicationStackName
            # *** Change this to match the region of this stack, 
            # *** using the file generated in the build step
            TemplatePath: BuildOutput::template-export-region2.yml
          RunOrder: 2
<p>&nbsp;</p>
So as first Action here is creating changeset. This is very useful for testing it and to see what kind of changes are going to be deployed. You can put approval action too.
As InputArtifacts we have the BuildOutput from the Build Action provided. Two main variables in this section is the "Region: region" and "RunOrder" (A positive integer that indicates the run order within the stage.)
Also the TemplatePath is the template that was defined in the buildspec file. This can be the same template or different one depending of what you are trying to do.  
<p>&nbsp;</p>

      ArtifactStores: 
        -
          Region: region1
          ArtifactStore:
            Type: "S3"
            Location: !Ref S3BUCKETregion1
            EncryptionKey:
              Id: !Ref KMSKEYregion1
              Type: KMS
        -
          Region: region2
          ArtifactStore:
            Type: "S3"
            Location: !Ref S3BUCKETregion2
            EncryptionKey:
              Id: !Ref KMSKEYregion2
              Type: KMS
<p>&nbsp;</p>

At the end we end up with defining the artifact or the artifact store for each region (with their corresponding kms keys).
This should be the end results image of the pipeline

<center>
<img src="/static/assets/img/blog/awspipeline/img.png" width="400" height="550" />
</center>
<p>&nbsp;</p>


<center>
<img src="/static/assets/img/blog/awspipeline/img_1.png" width="400" height="550" />
</center>
<p>&nbsp;</p>


Note: There should be IAM Roles and Assume Role for the AWS Account (Tools) to have access to other environments and Roles for the pipelines to execute the code. 


Source:
[Cross Region deployments with AWS CodePipeline](https://blog.deleu.dev/cross-region-deployments-with-aws-codepipeline/)

Thanks to [@mikebroberts](https://twitter.com/mikebroberts) for some tips.