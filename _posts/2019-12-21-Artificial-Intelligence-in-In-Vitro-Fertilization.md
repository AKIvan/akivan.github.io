---
layout: post
title:  "Artificial Intelligence in In Vitro Fertilization"
date:   2019-12-20
desc: "How AI find its way to IVF"
keywords: "AI, Artificial Intelligence, IVF, In Vitro Fertilization"
categories: [HTML,Ai]
tags: [AI, Artificial Intelligence, IVF, In Vitro Fertilization]
icon: icon-html
---
<center>
<img src="/static/assets/img/blog/ai/embry/ai-ivf-09.png"  />
</center>
<p>&nbsp;</p>



In vitro fertilisation is a process of fertilisation where an egg is combined with sperm outside the body, in vitro. The process involves monitoring and stimulating a woman's ovulatory process, removing an ovum or ova from the woman's ovaries and letting sperm fertilise them in a liquid, in a laboratory.
<p>&nbsp;</p>


### How it starts:
We start with a simple experiment, trying do detect mature eggs. Together with my wife, Mimoza Adji - Krsteva, (who is an Embryologist and a guide in IVF Procedure in this project), we spent some time (a lot), on more than 3500 images, trying to train the model. After some time we did the same for embrios, trying to detect in which day it is. Now we are working on detecting the quality and detecting some other points on the eggs and the embrios. After that we plan on doing the same for the sperm. You know how hard it can get when you work with someone who is always right 😃 
<p>&nbsp;</p>


### What is EMBRY:
EMBRY is an application that follows the whole IVF Process and predicts the outcome. Embryologist take information from the patients in EMBRY like: 
Age, Cause of infertility, IVF attempts, successful attempts, number of eggs and embrios and the quality of it (the quality is manually entered from the Embryologist). On the other hand using the camera from the microscope, we develop another option for detection. 
<p>&nbsp;</p>

<center>
<img src="/static/assets/img/blog/ai/embry/mature_egg.png" width="600" height="550" />
<figcaption>We start with detection of mature eggs (The first picture)</figcaption>
</center>
<p>&nbsp;</p>
 
EMBRY can detect mature eggs, detect how old are the embrios, and currently we are working on detecting the quality. As I mentioned above, all this information is written and taken in calculation. The prediction is the outcome of the calculation, based on the information from the patients. 
<p>&nbsp;</p> 
 
<center>
<img src="/static/assets/img/blog/ai/embry/day3.png" width="600" height="550" /><img src="/static/assets/img/blog/ai/embry/day5.png" width="600" height="550"/>
<figcaption>Detection of the day on Embrios</figcaption>
</center>
<p>&nbsp;</p>

### Next Steps and features

One of the priorities we have is to finish the detection of the quality. With this phase we are aditionally giving the embriologists another tool so they can be faster and ease the steps they are making. As we've already done it at some points. 

The other step is to make it easy for integration with other applications.


### Benefits

With this kind of method and technology, we increase the success rate of IVF, the speed, and Embriologyist are more accurate in choosing.
Also we save money that the goverment is giving for the first tree attempts and money that the patient is giving ... 
As I mentioned above, the next phases are detection of morphology, getting details, and suming the quality of the eggs and embrios, which can later be applied to the sperm too.  

The whole application is writen in Python and Django as framework. The hospital/clinics will have overview of the patients and results. No private data is stored, like Name and surenames. Just results and numbers. They can re-calculate each atempt if they need to. 
<p>&nbsp;</p>

<center>
<img src="/static/assets/img/blog/ai/embry/overview.png" width="800" height="450" /><img src="/static/assets/img/blog/ai/embry/rezults.png" width="500" height="650"/>
<figcaption>Some overview of the app</figcaption>
</center>