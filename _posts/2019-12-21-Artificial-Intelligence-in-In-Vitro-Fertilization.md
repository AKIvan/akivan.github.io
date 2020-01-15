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
As we all know AI helps a lot in different fields, especially in medicine. So this is just another field where we try to implement this AI concept and be of help to embriologists as another technology that they can use in order to increase the success rate in IVF.
<p>&nbsp;</p>


### How it starts:

We start with a simple experiment, trying do detect mature eggs. Together with my wife, Mimoza Adji - Krsteva, who is an embryologist and a guide in IVF Procedure in this project, we spent some time (a lot), on more than 6500 images of eggs, trying to train the model to detect the quality. After some time we did the same for embrios, trying to detect in which day they are. Now we are working on detecting not just the quality but other details too, on the eggs and the embrios. The same methods will be applied on the sperm too. One thing to another we develop this concept and app that can predict the IVF outcome. You know how hard it can get when you work with someone who is always right 😃 
<p>&nbsp;</p>


### What is EMBRY:
EMBRY is an application that follows the whole IVF Process and predicts the outcome. Embriologists write information about the patients and follow the whole cycle. Based on the information and patient history data we can calculate the outcome. On the other hand using the camera from the microscope, we developed another tool called CEMBRY, option for detection, that can help embriologists to faster detect the quality and any anomaly if they have.
<p>&nbsp;</p>

<center>
<img src="/static/assets/img/blog/ai/embry/mature_egg.png" width="600" height="550" />
<figcaption>We start with detection of mature eggs (The first picture)</figcaption>
</center>
<p>&nbsp;</p>
 
CEMBRY can detect mature eggs, detect how old are the embrios, and currently we are finishing the process on detecting not just the quality of the embrios but other details, which currently are written manually by the embriologists.
As I mentioned above, all this information is written and taken in calculation.
The prediction is the outcome of the calculation, based on the information from the patients in percentages. Beside the prediction part, we are opening another door for research. Hospitals, Clinics and Research centers can analyze patients data and try to find other problems for solving.
<p>&nbsp;</p> 
 
<center>
<img src="/static/assets/img/blog/ai/embry/day3.png" width="600" height="550" /><img src="/static/assets/img/blog/ai/embry/day5.png" width="600" height="550"/>
<figcaption>Detection of the day on Embrios</figcaption>
</center>
<p>&nbsp;</p>

### Benefits

With this kind of method and technology, we increase the success rate of IVF, the speed, and embriologists are more accurate in their choosing.
Saving the materials for further attempts if needed. Research centers even hospitals and clinics can participate in solving new problems. They can use this tool for educational purpose too.
Using this kind of technique we can save money that the goverment is giving for the first tree attempts and money that the patient is giving …
The next phases are detection of morphology, getting details, and suming the quality of the eggs and embrios, which can later be applied to the sperm too.

The whole application is written in Python and Django as a framework. The hospital/clinics will have overview of the patients and results. No private data is stored, like Name and surnames, just results and numbers. Each hospital/clinic will have their own ID for each patient. They can re-calculate each attempt if they need to.


Any IVF Hospital or clinic that wants to have or be part of this interesting research and prediction concept, can feel free to e-mail me.
<p>&nbsp;</p>

<center>
<img src="/static/assets/img/blog/ai/embry/overview.png" width="800" height="450" /><img src="/static/assets/img/blog/ai/embry/rezults.png" width="500" height="650"/>
<figcaption>Some overview of the app</figcaption>
</center>