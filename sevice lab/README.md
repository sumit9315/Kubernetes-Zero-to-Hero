![image](https://github.com/user-attachments/assets/6dbb1db1-9f64-437f-a8da-918a19f35e0c)
![image](https://github.com/user-attachments/assets/10b55b28-3f8e-428e-bade-c1da2f0dad07)
![image](https://github.com/user-attachments/assets/76f88840-2d1c-48b6-a585-dc2b51df4fbc)
![image](https://github.com/user-attachments/assets/fb34965d-c342-40d9-8968-4e42e286fa48)
![image](https://github.com/user-attachments/assets/c4c5f78a-d1c3-46a7-918a-208ba38c0740)
![image](https://github.com/user-attachments/assets/df09c1ea-bb2c-4828-9976-e2e76c4e275f)
![image](https://github.com/user-attachments/assets/9cbbfb88-8c95-49f0-9193-34eee8821055)
![image](https://github.com/user-attachments/assets/04661139-d4ad-4592-ab5c-774f529548be)
![image](https://github.com/user-attachments/assets/11bccefd-e977-43c7-aa44-89fd26bc4963)
![image](https://github.com/user-attachments/assets/62c0f6d9-14c2-43a2-aa96-1d81592ff617)
![image](https://github.com/user-attachments/assets/09fd8cd6-681d-435f-ae15-eea8b220d7b1)
![image](https://github.com/user-attachments/assets/3d4baf0e-3407-4058-b2e5-c689835eb826)
hello everyone my name is Abhishek and welcome back to my channel so today we are at day 37 of our complete devops
course and in this class we will Deep dive into kubernetes services that means we'll be doing practical session on
kubernetes service where you will see the aspects that we were talking about like the load balancing service
Discovery as well as how to expose your applications to outside world in kubernetes
so everything will be practical I'll recommend everybody to watch the video till the end because we are doing
practical uh traffic viewing using Cube shark so cubesark is a tool which will
help you to understand how traffic is Flowing within the kubernetes how each component of kubernetes is talking uh
like you know how one component is talking to the other component so it will be a very interesting session and
you will see all the capabilities using cubeshaft like the how Services doing the load balancing within multiple pods
how uh you know service is able to discover the pods and also we will see
how to expose the applications to outside world as well as within the kubernetes cluster and within your
organization perfect so without wasting any time I'll quickly jump onto the video but disclaimer and very important
point is watch the video till the end because even if you know the concept of services even if you understand
kubernetes using the cube shark I am going to show you how the traffic is
Flowing so so it is very useful session okay perfect so let me stop this share
and go on to the kubernetes cluster uh where is it perfect so here for the
purpose of demo I already have a kubernetes cluster let me clear this thing
yeah so this is the kubernetes cluster that I have it's a mini Cube kubernetes cluster if you just see mini Cube status
you will see that the kubernetes cluster is already up and running for instance if you don't know how to create a
kubernetes cluster you can watch my previous videos where I explained how to create a kubernetes cluster both using
minicube and also if you want to create on AWS if you have some free coupons or the resources then you can use cops to
create the kubernetes cluster which I explained in the last classes perfect so I have the mini Cube
kubernetes cluster running and let me clear up all the resources that I currently have okay so if I just do Cube
CTL get all so I was just using uh the default namespace for my other activities so let me just clear all of
these things I just have a deployment and a service so Cube CTL delete deploy let me delete this deployment that I
have and then I'll also delete the service that I have Cube CTL delete uh SVC uh
this is the service that I have and you will not remove the default service that is kubernetes service itself so now if I
just do Cube CTL get all I should see just the kubernetes default service that is running perfect so
I think we are good for the demo so for the demo what I've done is uh you know in the previous classes we use the
repository called Docker 0 to 0 so I'll use the same repository uh you can either use that repository or you can
use your own images if you have one so this is the repository Docker 0 to 0 so
you can also get that repository from my uh GitHub so you can simply go here GitHub
um Docker 0 to 0 so this is the repository guys where you have uh real time
practical uh python as well as golang images which are basically uh you know
front end and back-end based applications so either you can use these things or you can personally use your
own ones but uh if you want to use then you can go to this GitHub repository called uh this is my username in GitHub
and Docker 0 to hero is the link I'll also put the link in the description okay let me go back to the screen so
here now let's start from the uh scratch where I'll create a deployment first and
you know a deployment is something that creates your replica set which indeed creates a pods but these parts are only
accessible within your kubernetes cluster so we have seen that in the last classes because those pods come up with
a default cluster IP address and if you are using the cluster IP address then the problem is that the cluster IP is
only accessible within the kubernetes cluster so firstly let me uh you know
go to that folder examples and inside examples I have uh either you can go to
python application or you can go to golang based application so go to python based application uh in the demo I'll
use Python and you know here I have the docker file this file let me remove this
so that I can to write from the scratch and you people can understand so here I just have a Docker file and this is the
code and service.aml also we can delete so you will not have these files in the
repository if you go to the repository you will see devops folder which is the application itself and you will have a
Docker file and the requirements.txt so now the thing that we will do is we will create a deployment here and we will
deploy this application as a deployment onto the kubernetes cluster so this is a Docker file guys
so it's a very uh simple python Django based application and the application has an entry point and CMD so you don't
have to pass any arguments commands uh it will self-execute when you uh run the
container so for that uh firstly let's build this Docker image so let me call this as uh
Docker build right so I'm giving a tag called uh python sample application demo
and V1 right this is the image name and this is the tag so we will create the
image so that we will do the things right from zero so the image is created now I have the image ready here now the
thing is we have to start with the deployment because I want to deploy this onto the kubernetes cluster so as I told
you in the last classes you don't have to remember any syntaxes just go here search for kubernetes deployment
so you will go to a page called deployments in kubernetes and here just take the example that is available okay
just copy this example onto the terminal
let me call this file as deployment.yaml perfect so let me paste it here so this
is the deployment we need to edit the fields so as I told you in the previous classes you don't have to remember
anything you just have to know which Fields have to be modified right so I don't want three replicas let me just
choose uh two replicas for the demo so that I can show you the load balancing as well with the service so I'm just
creating two replicas of my uh pod so name I'll just modify the name as uh let
me call this as sample python app okay and we have to
choose the labels guys so labels is important because uh let's say someone wants to use this deployment or
you know you want a selector so I explained the concept of labels and selectors so this applies for every
resource in kubernetes so every time you create a resource in kubernetes whether it is deployment or whether it is any
kind of resource try to put some labels on top of them okay so here I'll say a label as uh just
again I'll use the same label called sample python app okay replicas has two and uh selector we can
use the same thing so this selector is required for the deployment to actually look into uh you
know labels and selectors concept where this is the selector which will uh look for the labels called app sample python
application okay so that's why what I'll do is inside the uh this is the Pod
template right so inside the Pod template also you can choose the same label okay sample python application now
who will be looking for this label service will also be looking for this label because service works on the concept of labels and selectors so
whenever I'm I'm going to create service after this I'll show you when we create service we have to remember that we have
to copy this label as is and we have to use this inside the selector field of the service only then your service will
be able to find out this part for example if I remove this and let's say it it is conflicting information in
service as well as in pod then you know your service will not be able to find the point and you will see a traffic
loss so we can also try that as an example no problem now here you can call it as a python app
just a name of the container it does not matter but the main thing is you have to replace with the image that we have just
created okay so let me save this and what was the image that we created
so this is the image right so let me open this one and put the image name
here okay so this is what I'm going to uh show you guys that you don't have to
remember any Syntax for the deployments or Services because the file will remain always the same and on which Port is the
application running my application is running on the 8000 Port okay so how do you know this it's very simple you can
open the docker file and you know you will know on which Port your application is running so either it will be part of
the Expo statement or you can also find that as part of the commander okay so whenever you are running the application
so as developers or devops Engineers you should know on which Port your application is running
got it so now this is the deployment.aml I have uh successfully updated the
container Port I've updated the image then I've updated some labels and selectors so this is it now you can go
ahead and create the deployment Cube CDL apply minus F deployment.yaml so if I
create this deployment so you will see that you know it says that the deployment is created and you can also
use the cube CTL get deploy what does Cube CTL get deployed to cube CTL will
talk to your kubernetes API server and it will get the information of deploy okay so here you will see that Cube CTL
get deployed returns saying that okay I've created a deployment and there are two pods that you have requested and
both of the pods are available so if you don't believe Cube CTL you can just say Cube Cube CTL get pods which will show
you the two pods that are created as well okay so this is how you can get the information of the pods that are running
but if you want to get the IP addresses of this pods as well what you can do Cube CTL get pods
minus o wide which will give you the information of your IP address of the pods okay so if you are Keen enough uh
to I mean if you are Keen to understand uh what exactly is happening when you run this Cube CTL commands you can
simply add a verbose statement like you know instead of just saying cubicle get pods you can say Cube CTL get pods minus
V is equals to 7 for example okay so it will give you the information what is it
saying firstly loaded the cubeconfig file okay after that it is connecting to
the API server okay so here this is the API server and it is trying to use this
API call with the kubernetes to get the list of parts then it says that the request headers are accepted and it got
the response as 200 so it has written you the information of the pods as you increase the verbosity level you will
get more information about this kubernetes spots like you can do 9 which is the maximum verbosity level then you
get more information about the API call like the Json that it is passing and
what is the response what is the request so this is only if you are curious to understand how Cube CTL is talking to
the kubernetes API server and what is happening behind the scenes when you run the cube CTL get pods command okay so
now this is not relevant to our class today so you can just do Cube CTL get parts minus so wide now deployment has
created two parts and we all know the Practical use cases of deployments right so what it does it's a high level
wrapper and you know it rolls out a replica set and you know replica set is
a controller which makes sure that the state of the pods is matching according to the deployment.yaml that we have
created so for example if I delete one of these pods you already know that okay let me delete this Cube CTL uh Delete
pod then replica set will create a new pod we have already seen this in the last classes with uh practicals as well
so if I again do Cube CTL get points you will see that the two parts are running and this time probably the IP address
might change okay so if I just say Cube CTL get pods minus o wide
see here the IP addresses were 0.5 0.6 now the IP address has changed to 0.7
and 0.5 so this is the problem that we were discussing about kubernetes deployments right so if the IP address
has changed now the user who was trying to access the application on 0.6 they will say that oh I was using 0.6 and I'm
getting a traffic loss but as devops engineer we will say no no uh there are two pods expected expectation is two
parts and two parts are running so I am not responsible so whose problem is this the problem is with respect to
kubernetes because kubernetes whenever it has created a new replica okay it has
changed the IP address because kubernetes does a dynamic allocation of IP address it's not a static allocation
if it was a static allocation so whenever a new pod comes up every time it comes up with the same IP address but
in case of dynamic allocation the IP address might change so now this is the reason why you need a service Discovery
mechanism okay so if kubernetes Services was identifying the parts okay using the
IP address what happens then you know it becomes wrong right so what I means to say is
you will face a traffic loss because the IP address has changed so that's why as I explained you in the last classes we
use a concept called labels and selectors so using labels and selectors
what you do is you identify like kubernetes service identifies the pods
using the labels and selector Concepts so that every time a new pod comes up its label will remain the same right
because the label is same label is just like a stamp or you can understand it as a tag so every time a pod comes up it
definitely comes up with the same tag IP address might change but the tag or the stamp or the label is always the same so
service will say that oh okay so I notice that a new pod came up and let me
check the label okay label is correct uh this is what information the devops engineer has given me that uh this is my
selector selector should match the label of the Pod and a new pod is created so this part belongs to me so I can send
the traffic to this new product as well so that is how it works and I explain this thing in the last class using uh
Theory and the diagrams as well so now we will go ahead and see this
Behavior okay firstly uh what happens is if you want to stop here let's say if
you just want to use the deployments then what you can do is you can just say mini Cube SSH and use one of these IP
address right and probably you can access them using a curl command you can
just say curl HTTP followed by this uh specific IP address just use minus L
because the application that I have written it requires a redirect so you will notice okay call an 8000 right
because the application was running on the port 8000. so you will notice that there is a traffic here so what what is
happening here slash demo as well sorry guys so the application which I have
written is running on the context root called slash demo so you have to use this specific thing coil followed by the
IP address call and put on which the application is running and the context root of the application okay so don't
worry why I am changing this information I am not changing anything you can just go to this python web application it's a
Django based application if you have knowledge on Django you can just go here and you can see the context root of the
application if you go to the urls.py you will see uh here actually uh
yeah if you go here to the urls.py you will see that the context root is slash
demo okay so that's why I'm accessing the application on slash demo I'm not changing anything don't worry so you
just have to access it on 172.17.0.5 followed by the port of the container followed by the context order
this slash demo now you will see that there is a traffic that you are trying to access what does it say uh learn
devops with strong foundational knowledge and practical understand understanding please share the Channel with your friends and colleagues so this
is a very simple uh static application that I have written now the problem you all know that okay uh if you use the
same IP address and try to access it let's say use this IP address say use
the same command Okay so curl minus L http
colon double slash colon 8000 slash demo
you will see that there is no traffic we were getting only uh you know we were able to access the application and we
were able to get the response only inside the kubernetes cluster this is because a pod by default will have only
the cluster IP addresses that is I mean a pod by default will just have the cluster network attached to it so if it
is a cluster Network you have to access it using the cluster itself right you have to log into the cluster and access
it but this is not expected your customers will be definitely if you have internal customers internal customers
can be within your organization but if you have external customers there will be even outside your organization so you
have to to solve the two problems here right one is people within your organization okay for people within the
organization like I told you you can use the kubernetes service concept let's say this is your kubernetes cluster for
example uh where is this
okay so uh I was trying to draw here but I hope you uh understood that uh if you
are trying to use people within your organization um okay let me try to grab something
here yeah probably I can write here so let's say this is your kubernetes cluster okay or
let me uh take a external diagram itself
draw Auto draw we can use this Auto draw to explain
okay clear this thing start over right so let's say this is
your kubernetes cluster okay and this is your organization okay so this is your
organization and this is your kubernetes cluster and this is your application so you can have people within your
organization trying to access this application or you can my you you might also have people who are outside the
organization itself right so if you are building applications for uh your organization if there are internal
applications then what you need is you have to expose this application on the kubernetes worker node IP addresses so
that you know these people can directly access using the kubernetes worker node IP address if you want to use uh I mean
if you want this application to be used by external customers they don't even have access to your organization so you
need to create a public IP address for this application so that you know everybody in the world can access your
applications so these two cases can be solved even in yesterday's interview question I explained if you want to
solve this problem one you have to use node Port mode right and for two you
have to use load balancer mode so let us see both of these cases okay
let us try to understand a node Port mode and let us also try to understand the load balancer mode perfect
so if you want to learn these things in detail you can watch my previous videos where I've explained all of these things
in detail but today's video will be going to be a practical video and I'm also going to show you the same things
using virus Arc first of all let us proceed with the creation of service so service.yaml let me create this file
here and again I will not remember anything I'll just go to the kubernetes website itself and say kubernetes
service okay so if I am doing a kubernetes service here uh let us let me
clear this diagram uh go to annotate and clear
perfect so if you go to kubernetes service so here you will notice that just copy this uh I mean go to this page
and here you have multiple example of the services so the default one like I told you it's just search for the
cluster IP address I don't want it so firstly let us demonstrate the node port example okay what happens in node Port
your application will be exposed on the Node IP address okay so in my case the
node IP address is the mini Cube node IP address because I am using minicube right so let me just copy this one right
because this is the example for node port and paste it here
let me delete this so that it will be clear okay perfect now what are the things that I
have to change firstly you can give any name to for your service you can also keep it as is my service let me change
this thing to python Django app service you can give any
random name okay don't worry about it now the most important thing is to keep this selector similar to the
deployment or the pods that you have created not the deployment to the parts because service will be directly looking
at the pods using the selectors if there are 100 parts then this selector will be looking at 100 parts that have this
label okay it doesn't matter tomorrow if you have 200 Parts 300 parts what service says is I don't bother about the
number I'll be only looking at pods that has this label if any pod let's say uh unexpectedly someone else
has created a pod with this specific thing then your service will forward the traffic to that Port as well if it is in
the same namespace okay so that's how the service works so service will only bother about the labels and selector
okay so what you need to do is you have to go back to your kubernetes pod or the
deployment or yaml file inside the deployment inside the Pod template okay make sure that you are copying it from
here okay sometimes for the deployments you might have different labels and selectors okay so always pick from the
template section inside the templates you have this label pick from here go back to the service example oh sorry
I did not save it my bad no problem I'll just go back to the page and save it one
more time copy it one more time so this is the service copy it from here
copy and then delete this command delete this command so that it is clear now
copy this uh thing app python so always make sure that you uh copy the right
thing because if you don't copy the right ones you will land into some problems with the labels and selectors
and it will be difficult to debug okay so that's why try to copy it as is
now I have copied this one okay so app sample python application perfect I have
copied now choose any node Port that you want uh I can keep as is as well I can
use 3007 port number and one important thing is to change the Target Port what
is Target Port Target Port is basically the port on which your application is running so my application is running on
Port 8000 so I'll choose this as the Target Port and do I need to change anything I don't
uh like I can change this one uh python
Django sample app it can be anything now let me just save this
Cube CTL apply minus F service dot yaml
okay so as soon as you apply this the service will be created again if you want to debug or understand more then
what you can do just say Cube CTL get SVC minus V is equals to 9 you will get
the entire information how the call is uh how the traffic is going within the
cluster how the cube CTL get is working and you will get the all the information but if you just ignore for the purpose
of the demo you can just say Cube CTL get SVC and you will get the application that is running this is the cluster IP
don't get confused because you have created the service using the node Port you will see that there is a port
mapping that is done so the cluster IP colon ET Port is mapped
with the node IP address 3007 okay what does this mean this means to say that either you can do minicube
SSH copy this IP address okay and you know you can also access your application using this cluster IP
address of the service that is HTTP colon this IP address colon 80 slash
demo okay even if you do this you will get the traffic minus L I have to pass okay even using this you will get the
traffic or what you can do is you can otherwise use the node code IP address now why
this is not recommended because you can already do this using the Pod IP addresses right so a service or any
resource that you are creating in kubernetes whether you are creating a node Port mode whether you are creating in load balancer mode or anything
cluster IP will definitely be there right so Additionally you are creating
node Port additionally when you are creating service in node Port mode you will get a port mapping and that Port
mapping is nothing but what kubernetes service has done for you it says that okay if you don't want to
access using the cluster IP address you can use the node IP address and I have mapped the port that is on Port 80 with
3007 Port that you have provided in the service.ml so now I can simply say minicube IP uh to get the IP address of
the mini Cube node if it is a ec2 instance you can get the IP address of the ec2 instance you already know how to
get the IP address of easy easy to instance right so this is the IP address what I can simply do is I can say curl
minus l so for other applications you might not need this minus L but for my
application because there is a redirect happening within the application I require this and now HTTP colon
this is the node IP address so this is the interesting thing guys now I'm going to show you how the application is
accessed from the uh node IP address okay so now I'm not logging into the kubernetes cluster I
can also access this using the browser I'll show you that as well okay so why I can access using the browser because it
is the same laptop if you access from your browser you will not get the traffic if you want to access using from
other people's browser then you have to use the load balancer IP address because it becomes external right it becomes
external traffic but if you are accessing from your laptop the mini Cube IP address you can do that because you are already in the same laptop you know
the uh right your laptop can connect to mini Cube because it is part of the what
it is end of the word mini cube is doing it is just installing a virtual machine on top of your laptop and uh you know
because both of them are in the same network you can access but outside people cannot access it because you have
just exposed using the node Port mode so what I'll do is call an 80 watch this carefully I'm not using colon
8000 because I am basically you know what service is doing is this is the
node IP address and this is the port when you do it it maps to even if you don't use it uh there is no
problem uh sorry you have to use thirty thousand seven right you cannot use 80 because if you use 80 uh Service uh
sorry the node IP address would be looking for applications on 18 nothing will be running so if you have if you do
3007 then this is how the service will route the traffic to the pods okay so
this is the node IP address this is the node Port followed by slash demo now you will see that the application is
accessible you can use the same IP address okay just let me copy the same thing and you can also access from your
browser this is from the browser right so this
is the application that is running now if you take this same URL you are watching this video right if you take
the same URL and if you try to access this from outside it will not work okay why will it why will it not work because
what is the reason you have not exposed your application to outside world so this is how you expose
your applications to other people in your organization or somebody who has access to your node IP addresses or
someone who has access to your ec2 instances or your virtual machines now how to access the application to outside
world okay so to do that you will make a very simple change just go to your service called Cube CTL edit
service okay and what was the name of the service sorry I don't remember so let me do Cube CTL get service and let
me edit this service okay Cube CTL edit SVC and once you edit you will see uh
the type as node Port mode right so in one of the places we have selected the type as node port
just simply make the modification and change it to load balancer
now this will not work here because we are using minicube okay if just the same
thing just modify to type load balancer and it will work for you if you are on your ec2 instance or if you are on any
cloud provider because load balancer type is only uh you know supported on
the cloud providers right and who does that the thing is done by your
uh sorry Cloud control manager right so what you need to do is just go back here
Cube CTL edit SVC and search for node port and just modify the thing to your
load balancer okay what is the mode that you will change load balancer
I hope the syntax is right perfect so now if you do Cube CTL get
SVC the IP address will not be allocated the external IP will remain pending here
because this is mini Cube if it was AWS or if it was Azure or if it was gcp you
will get the IP address here and who is generating that IP address for you the cloud control manager of kubernetes why
Cloud control manager is generating because the people of AWS Azure and gcp have told the cloud control managers
they have contributed to the cloud control manager saying that if you find a service of type load balancer then use
the internal components of AWS or Azure and gcp and generate a IP address okay so that is why the external IP will be
generated in my case it will not be generated there is a project called a metal lb uh using which you can expose
the applications on minicube as well you can search for the project called metal lb
okay so you can expose the uh it it can generate you one uh public IP address as
well but this is still a beta project uh you know and you don't have to try that as well because if you know the concept
that's more than enough you can uh if you have a ec2 instance you can try it or else just understand that you will
get a public IP address something like uh 32.48 or 100 or something and you can
share this IP address to your customers or someone and they can access that using the public IP address okay so this
is the concept of how to expose your applications okay but I told you about three concepts right whenever we are
talking about the service I promise you that a service can do three things one is load balancing
two is service discovery and three is exposing the applications
okay so third part is clear to you till now right because I showed you using note Port I showed you using uh load
balancer mode how does this work and all so now let us see the second part that is service Discovery okay so to
understand the service Discovery just make a very simple change Cube CTL edit
SVC ah sorry again we need the service name if not we
will get both the services in the edit and it will just be confusing so Cube CTL get service this is the uh
name of the services Cube CTL edit service followed by the name of the service and now what you will do to
understand the concept of Discovery just modify the selector okay search for uh
selector see if you are not comfortable with the cube CTL edit what you can also do is just use the same service.aml file
that you have created okay but the only condition is whenever you have created this you must have used the apply
command Okay so either if you have used the cubesatel apply command to create the service then you can just say vim
and edit the service and reapply the service or else you can also use the cube CTL
edit command okay whichever is easy for you but I will recommend this one uh always create your services using the
apply so that in future you can modify it now what I'll do is I'll just come here and remove one character let us see
if even if you can access your applications like this okay then Cube
CTL apply minus F service dot yaml okay so now the
labels and selectors are different so the labels of your pod is sample
application what was that so the label that you have on your uh pod is sample
python app but here on the service it is sample python AP so let us see if the
service Discovery uh will be able to detect the pods what I'm saying is it
shouldn't because what is the reason why it shouldn't
detect because the labels and selectors are different so what was the curl command that I used let me show you from
the browser itself this is the curl Command right so let me
just copy this one more time and let me try from the browser so this time you'll notice ah sorry I just need this one
right I don't need curl so this time you'll notice that the application is
not accessible why is it not accessible even using the curl you can see that you
will not couldn't connect to the server so by just changing the labels and selector by
just changing the selector you understood that the service is not discoverable so again go back and modify
so you will understand the concept of how service is using the service Discovery concept using the labels and
selector so now let's let me reapply Cube CTL apply minus F so just give it
one minute right because the queue proxy has to update the rules uh the iptables
and all the things have to be updated so just give it a minute uh don't get a pan I mean don't go into a situation of
thinking oh this is not working it will just take a minute or not not even minute sometime so sometimes the refresh
will take time so you will notice that the service Discovery is done so now what are the two things that you have
already learned one is service Discovery and one is how to expose your application so finally now I'll show you
the load balancing as well so you have two applications right Cube CTL get pods
why you need load balancing I explained like uh if there was only one replica if there are 100 requests it will be
difficult for one uh replica to serve all the requests so if you have depending upon the load of your
application you can create multiple replicas but by default the deployments or the pods do not have load balancing
if you create service you will get the load balancing this is what I explained now let us see that in Practical so for
that I have the cube shark as well so what I am doing Cube shark is a very
simple application uh you can also install the cube shark and I recommend you to install cubesark I'll make a full detailed video on cubeshark as well but
if you want to install the installation is very simple just go to uh the cubeshark documentation
okay so you will understand a lot about kubernetes if you uh you know if you have this Cube shark uh because it
explains you how the traffic is Flowing within the cluster and all of the things so go to the install and run page here
and this is the simple curl command just execute this call command or if you are on Mac you can just run these two
commands and your Cube shark is up then you just have to run this specific thing
called cubeshark tab minus a or you can do Cube shock tab but this will only be
limiting your Cube shot to one single name space if you want to expose cubeshack to all the namespaces or if
you want to understand the kubernetes uh traffic flow for all the namespaces just
run this command called cubeshar tap minus a and you will see this page okay so you can access the cube shark browser
on the port called double eight double nine localhost colon double eight double nine it will automatically open in your
browser so you will get this beautiful page where you know you can do lot of things on cubeshark so this video I am
not going to talk about the details of cubesark I'm just using this to explain you the concept of load balancing in
service but I'll do a dedicated video on cubeshark where I'll explain you how does the traffic flow and all but here
I'll just spend two minutes to explain you okay now let me run this curl command
five times okay so this one I'll just copy it and I'll run for five times so that you can unders let me run for six
times and show you the load balancing one uh okay let me remove this so that it
will not print the output let me just remove minus l
1 2 3 4 5 and 6. now what should be the
expected output is the kubernetes service has to send request uh three times or you know uh
using the round robin it has to send request to 170 to 17 0.7 as well as 172
17 0.5 let us see we made six requests let us see if the requests are you know
segregated amongst these two parts or not okay let me go back here and let me see so once the request went to 172 17
0.5 then okay let me just refresh this page called apply just let me apply and
show you um perfect
let me rerun sorry I had some old data let me do it one more time one two three
uh four five and six okay so sorry guys I had some refresh information uh so
because of which it was not showing but now what you will see here is when you
apply this thing uh I think it is taking some time to refresh
and get the data just give a minute foreign
so I just created it before the demo and for some time uh you know the proxy was
disconnected so what I did was I went back and restarted the cube shark so I'll show you how to restart these
things and all uh in the video where I uh demonstrate about the cube shark but what happened was this is a cube shot
which is running and if you see here error while proxying the request and context cancel okay so this was the
error I got and what I did was I have re-established a connection with uh between the cubeshark and my kubernetes
cluster okay so this is the command uh in general called Cube sharp proxy and what it will do is it will re-establish
the connection so I just did not want to go into the details but uh yeah
unfortunately because the uh the connection was disabled so I had to
explain you all of these things perfect now but the demo that I wanted to show you here is now the cube shark is back
you will see that I have sent six requests right so these are the six requests that I've sent and see what is
happening so once the request into 172 170.5 and again the request went to 170
to 17 0.7 again it went to 172 0.170.5 172 170.7 again it went to 170 to 170.5
so what is happening is kubernetes service is doing the load balancing okay so what I did is I tried to access the
application on this specific Port called 192.168.64.10 and using which the
request actually this is the service and it it is once sending the request to 170 to 170.7 and another time again you hit
the same URL and it is sending the request to 170 to 170.5 so this is how the packet is actually uh traveling
within your kubernetes cluster or you know this is the packet flow what is happening is uh so if you take this is
the start of the request I as a user I executed 192.168 64.10 and this is the
IP address so it went from my machine so this is my machine IP address from my machine because I am using the browser
or the curl command so it went from my browser all the curl command this is the source right so what is the source
source is the point where you have started the execution okay so from Source if you look at the ifconfig
and if you just grab for this IP address 192 168
192.160 okay let me even search it here so just run ifconfig and if you search
here you will notice that this is my machine IP address 192.168.64.1 whether you are doing from
curl or whether you are doing from the browser you will see that this is my source or this is my origin so from my
laptop I have executed this specific IP address that is 192.168.64.10 what happened is from here
the request went to 172 170.1 this is my mini Cube IP address okay from here it
went into the service so this is the packet flow guys and if you want to understand the packet flow in detail
this is the tool called Cube shark and I'll explain you when we actually deal with this uh specific Cube shark
individual video so I'll explain you how this packet is traveling okay so this is the request and this is the response
from there it went to mini Cube what happened once it went to minicube like you know this is the URL context path
this is the host IP address and then it went to you know it sent this response and from there it sent the request to
170 to 170.7 so you can understand these things in detail you can replay right
when you replay you know you can do this action one more time or you can also uh you know capture the packet and you can
debug it with some external tools so you all know about virus Arc probably
you can capture this packet and you can execute against Wireshark as well so that you understand more details about
the packet you can use TCP dump so these are some of the things let's not go into the detail of this tool but we have
understood the three concepts right so here using cubeshark I explained you the concept of load balancing then using uh
you know uh the browser and the terminal itself I explained to the service Discovery concept and also the
what was the other thing I explained you how to expose the application so these are the three things that I wanted to
cover as part of this video I hope you enjoyed the demo and Cube shark I'll do a dedicated video because this is a must
have tool for every devops engineer this explains the traffic viewer of kubernetes and most of your kubernetes
Concepts will become clear here you can also do a service map where you can see what are the different Services how one
service is talking to the other service then you know you can look into list of pods in the name spaces here and you can
uh you know blog sorry access understand the traffic depending upon the TCP
request HTTP request you can do layer 4 layer 7 all of the things so I'll explain uh this in detail but for now
this is the video for today like if you enjoyed the video click on the like button if you have any feedback for me
put that in the comment section and finally don't forget to share this video with your friends and colleagues thank
you so much guys I'll see you in the next video take care everyone bye
