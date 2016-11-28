# Singularity Web
Did you know that you can put a webby things inside of a container? Did you know you can go further, and take user inputs to run an analysis and then present the result? Or give them a container with the exact dependencies for your software, and provide an interface to it? I have come up with this repository, `singularity-web` to show how easy it is to dump your analysis, application, static web files, whatever, into a container. You can share the entire container, or just the specification file for it, and it's a big leap in the direction of reproducbility. Here are some examples you might be interested in:


## How does it work?
The only pre-requisite is that you should [install Singularity](http://singularity.lbl.gov/install-linux). Singularity is already available on just [over 40 supercomputer centers](https://docs.google.com/spreadsheets/d/1Vc_1prq_1WHGf0LWtpUBY-tfKdLLM_TErjnCe1mY5m0/pub?gid=1407658660&single=true&output=pdf) all over the place. How is this working? We basically follow these steps:

 1. create a container
 2. add files and software to it
 3. tell it what to run

In the case of this example repo, we are interested in things that produce a web-based output. You could go old school and do this on a command by command basis, but I (personally) find it easiest to create a little build file to preserve my work. I'm also a big fan of bootstrapping Docker images, since there are ample around. If you want to bootstrap something else, please look at our [folder of examples](https://github.com/singularityware/singularity/tree/master/examples). :)

### The Singularity Build file

### The Header
The First line `bootstrap` says that we are going to bootstrap a `docker` image, specifically using the (`From` field) `ubuntu:16.04`. You couldn't choose another distribution that you like, I just happen to like Debian.

### %post
Post is the section where you put commands you want to run once to create your image. This includes:

- installation of software
- creation of files or folders
- moving data, files into the container image
- analysis things

The list is pretty obvious, but what about the last one, analysis things? Yes, let's say that we had a script thing that we wanted to run just once to produce a result that would live in the container. In this case, we would have that thing run in %post, and then give some interactive access to the result via the `%runscript`. In the case that you want your image to be more like a function and run the analysis (for example, if you want your container to take input arguments, run something, and deliver a result), then this command should go in the `%runscript`.

In our case, since we are going to serve a simple web-based thing, we create a directory to work with (`/data` is easy to remember), put some web file things there, and then (the strategy I used in the examples) was to install python, because it has a nice command for bringing up a quick web server.

### %runscript
The `%runscript` is the thing executed when we run our container. For the [nginx-basic](nginx-basic) example, we basically change directories to data, and then use python to start up a little server on port 9999 to serve that folder. Anything in that folder will then be available to our local machine on port 9999, meaning the address `localhost:9999` or `127.0.0.1:9999`.

We recommend you look at our [nginx-basic](nginx-basic) example for the full example, and modify some of the examples below to suit your own needs. If you use any of these templates in your work, please ping us at [researchapps@googlegroups.com](researchapps@googlegroups.com) so that we can showcase your work.


## Examples

### nginx-basic
The [nginx-basic](nginx-basic) example will walk you through creating a container that serves static files, either within the container (files generated at time of build and served) or outside the container (files in a folder bound to the container at run time),

### nginx-expfactory
The [nginx-expfactory](nginx-expfactory) example takes a [software that I published in graduate school](http://journal.frontiersin.org/article/10.3389/fpsyg.2016.00610/full) and shows an example of how to wrap a bunch of dependencies in a container, and then allow the user to use it like a function with input arguments.

### nginx-jupyter
I use ipython notebook / jupyter notebook sometimes, and I thought it would be nice to have an image that could easily bring up a server, either for files in the container or a folder mapped from the outside. Behold, [nginx-jupyter](nginx-jupyter)
 

## How do I share them?
You have a few options! 

### Share the image
If you want absolute reproducibility, meaning that the container that you built is set in stone, never to be changed, and you want to hand it to someone, have them [install singularity]() and run, then you probably want to build the container yourself and give it to them. It might look something like this:

      sudo singularity create theultimate.img
      sudo singularity bootstrap theultimate.img Singularity

In the example above I am creating an image called `theultimate.img` and then building it from a specification file, `Singularity`. I would then give someone the image itself, and they would run it like an executable, which you can do in many ways:

      
      singularity run theultimate.img
      ./theultimate.img

They could also shell into it to look around, with or without sudo to make changes (breaks reproducibility). Note that we are considering an addition to the Singularity software that will give control to do this or not, but nothing is final yet.


      singularity shell --writable theultimate.img
      sudo singularity shell --writable theultimate.img
      

### Share the build file Singularity
In the case that the image is too big to attach to an email, you can send the user the build file `Singularity` and he/she can run the same steps to build and run the image.


### Singularity Hub
Also under development is a Singularity Hub that will automatically build images from the `Singularity` files upon pushes to connected Github repos. This will hopefully be offered to the larger community in the coming year, 2017.

