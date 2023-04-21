---
layout: post
title: "Manim - Tutorial 1"
date: 2023-04-21 11:39:00 +0100
categories: programming
author: "M Tandy"
usemathjax: true
---
# My Experience with Manim
I am in the lucky position of having a PhD project that benefits
from having animations when presenting my work. 

I've always been a fan of [Matplotlib](https://matplotlib.org/),
however recently I found it wasn't a fun  experience using it for
complicated animations, requiring displayed text and pausing.

I got recommended by a 
[very intelligent person](https://www.ntnu.edu/employees/martilud)
to try out
[Manim, community edition](https://www.manim.community/).
To those not in the know, this a community driven fork of
the Python package used by
[3Blue1Brown](https://www.youtube.com/c/3blue1brown)
to render the animations in his videos.

This caused me to enter into a day long quest to get one 
animation working, mostly due to a lack of clear
documentation and tutorials at the time of writing, with a nice
helping of my own mistakes. This is not
to be too critical, however. This is an open source community
based package. And seeing as I have now made use of the community
resources, I feel it only acceptable that I provide some sort of
contribution in the form of a breakdown of what I did.

So, I now endeavour to explain to you how I created the following
animation, with all the pitfalls and confusion that happened along
the way.

{% include video.html name="FunctionPlot.mp4" %}

*Don't worry, I won't talk about what the animation is actually
showing.*


{% include callouts/note.html title="Note"
	content="At the time of writing, the Github page of Manim say's that it
	is going through a major rewrite, so it is entirely possible that
	this guide is no longer relevant when you read this. 
	So note that the version ```0.17.3``` of Manim being used"
%}
In this first article, I introduce Manim and how it works in 
hopefully a digestible way.

### Final Output 
At the end of this article, we will code the following
<details>

{% highlight python %}
import manim as mn

class FunctionPlot2(mn.Scene):
    def construct(self):
        f = lambda x: x**2
        g = lambda x: 0
        h = lambda x: -x**2
        x_range = (-2, 2)

        f_graph = mn.FunctionGraph(f, x_range=x_range, color=mn.RED)
        g_graph = mn.FunctionGraph(g, x_range=x_range, color=mn.BLUE)
        h_graph = mn.FunctionGraph(h, x_range=x_range, color=mn.WHITE)

        transform_f_to_g = mn.Transform(f_graph, g_graph)
        transform_f_to_h = mn.Transform(f_graph, h_graph)

        self.add(f_graph)
        self.play(transform_f_to_g, run_time=2)
        self.wait(1)
        self.play(transform_f_to_h, run_time=2)
        self.wait(2)
{% endhighlight %}

 {% include video.html name="FunctionPlot2%201.mp4" %}
</details>
---
## Installation
Here, I'm going to refer you to the 
[official documentation](https://docs.manim.community/en/stable/installation.html).

I' on my Mac, so I have no advice for users of other platforms
in this case.
To sum it up, you are going to need [ffmpeg](https://ffmpeg.org/),
which can be installed via [Homebrew](https://brew.sh/),
```
brew install ffmpeg
```
and  ```py3cairo```
```
brew install py3cairo
```
You can then install Manim via pip,
```
pip3 install manim
```

>[!warning]
>The normal recommendation of installing this in a python
>environment, though whichever manager you prefer, still applies.
>I installed in an conda environment, and things worked out fine.

---
## Setting up your workspace
First, create a new directory wherever you desire. I'm going to call
mine ```animations```. Inside this directory, create a new python
file. I'm going to call mine ```manimAnim.py```, short for
*manim animation*, which is fun to say. So we end up with a
directory of the form
```
animations/
└─manimAnim.py
```

## Starting simple
Start editing ```manimAnim.py``` in which editor you prefer. We
are going to begin by importing Manim. The official documention
does the thing you should never do in Python and imports the contents
into the local namespace i.e. ```import manim as *```. We are not going
to do this, and instead write
{% highlight python %}
import manim as mn
{% endhighlight %}

Creating visualisations is done via creating a child
of Manim's ```Scene``` class.
The objects that you wish to render, and the order of the 
rendering, is then done via the classes ```construct``` function.
So, to ```manimAnim.py```, we add
{% highlight python %}
class FunctionPlot(mn.Scene):
	def construct(self):
		pass
{% endhighlight %}

We now need to add something to render. Our first goal is to plot a
simple function . Let's go with the parabola,

$$ f(x) = x^2, \quad\ \text{ for } x \in (-2, 2). $$

This is achieved by building up our ```construct``` function, as
follows
{% highlight python %}
class FunctionPlot(mn.Scene):
    def construct(self):
        f = lambda x: x**2
        x_range = (-2, 2)

        f_graph = mn.FunctionGraph(f, x_range=x_range, color=mn.RED)

        self.add(f_graph)
        self.wait(2)
{% endhighlight %}

Before we get to rendering this into a video file, let's break
downs whats going on there. We begin by:
- Assigning to ```f``` the function we wish to render;
- Assigning to ```x_range```, via a tuple,
  the minimum and maximum values of the domain on which we
  want to plot ```f```.

The next step is where Manim gets involved. ```FunctionGraph``` is a 
child of the ```Mobject``` class. ```Mobject```'s are the building 
blocks used by Manim to create animations. The base class
can be extended to form renderable objects. ```FunctionGraph``` is
one such extension, and is the basic object used when rendering a one
parameter curve to the scene.

In our case, we assign a ```FunctionGraph``` of ```f``` to the variable
```f_graph```. When doing so, we say we want it to be restricted to 
domain defined by ```x_range```, and the curve to be the colour
```mn.RED``` (which is ```'#FC6255'```).

Finally, we use the ```add``` function inherited from the ```Scene```
class,  which say's we want to render our ```f_graph``` object to the 
scene. We then wait for ```2``` seconds with the inherited ```wait```
function. If we didn't add the wait time, the video would be of 
length $0$, and Manim would end up outputting an image.

### Rendering our video
Our scene is ready, let's render the video. 
Via the command line, e.g. Terminal on macOS,
navigate to the directory where your
python file is. In my case, this is the ```animations``` directory.

Running the following command will render our scene into 
an ```.mp4``` video, and automatically play it
```
manim -p -ql YOUR_PYTHON_FILE.py FunctionPlot
```
where ```YOUR_PYTHON_FILE.py``` is the name of your python file (duh.),
in my case ```manimAnim.py```.

The following video should then automatically appear
{% include video.html name="FunctionPlot2.mp4" %}

Breaking down our command:
- ```manim``` is used instead of python when render your videos;
- ```-p``` says we want to play the file when it is rendered;
- ```-ql``` says the rendering should be "quality low". This is good when you are testing things out, as it is quicker to render.
  When you are happy, render it with the ```-qh``` option, for "quality high".
- ```FunctionPlot``` tells manim to render the ```FunctionPlot``` class
  we defined in the file.

Manim will automatically create a bunch of directories, as follows
```
animations/
└─media
	└─images
	└─videos
		└─YOUR_PYTHON_FILE
			└─480p15
				└─FunctionPlot.mp4
				└─partial_movie_files
			└─1080p60
				└─partial_movie_files
```
The rendered video is saved in the ```480p15``` directory, as we
rendered it at low quality. If rendered at high quality, it lands
in the other directory ```1080p60```. The partial movie files
directory can be ignored for now. When we start to create
longer videos with multiple steps, this directory contains 
each steps individual video that are then stitched together to form
the final video.

## Make it move. I want it to move
So it's a video of a curve, so what? Ok, ok, let's play
with some of the basic animation functionality. Consider two
functions,

$$
	g(x) = 0, \quad\ h(x) = -x^2.
$$

We are going to transform the curve $f$ to look like $g$, and from
$f$'s new form to $h$. Expanding our construct function, we have
{% highlight python %}
class FunctionPlot(mn.Scene):
    def construct(self):
        f = lambda x: x**2
        g = lambda x: 0
        h = lambda x: -x**2
        x_range = (-2, 2)

        f_graph = mn.FunctionGraph(f, x_range=x_range, color=mn.RED)
        g_graph = mn.FunctionGraph(g, x_range=x_range, color=mn.BLUE)
        h_graph = mn.FunctionGraph(h, x_range=x_range, color=mn.WHITE)

        transform_f_to_g = mn.Transform(f_graph, g_graph)
        transform_f_to_h = mn.Transform(f_graph, h_graph)

        self.add(f_graph)
        self.play(transform_f_to_g, run_time=2)
        self.wait(1)
        self.play(transform_f_to_h, run_time=2)
        self.wait(2)
{% endhighlight %}
Here we have introduced ```g```, ```h``` and the respective
```g_graph``` and ```h_graph``` in the same way we 
introduced ```f```, ```f_graph```.

We now have introduced instructions to transform between
```f``` and ```g``` via the ```Transform``` animation class, and
assigned these instructions to ```transform_f_to_g```. Similarly,
we have ```transform_f_to_h```.

We add instructions to render these transforms via the ```play```
function inherited from the ```Scene``` class. We say that these 
transforms should take $2$ seconds to complete. 

Rendering again via the command
```
manim -p -ql YOUR_PYTHON_FILE.py FunctionPlot
```
we get
{% include video.html name="FunctionPlot2%201.mp4" %}

>[!note]
>Here we transform our ```f_graph``` to transform to look like
>```g_graph```. A mistake is then to think you need to include
>the animation to transform ```g_graph``` to ```h_graph``` via
>```
>mn.Transform(g_graph, h_graph)
>```
>Try doing this instead. You will see that you end up with a line
>left on the screen. This is the original ```f_graph```.
>To put it simply, ```Transform``` changes how an object looks, and tells the video to interpolate between the two when played.

---
# Bonus: A little fun to end
The ```Transform``` animation can be used to move between any two
```MObjects```. 
Try rendering out the following code:
{% highlight python %}
import manim as mn

class FunctionToCircle(mn.Scene):
    def construct(self):
        f = lambda x: x**2
        x_range = (-2, 2)

        f_graph = mn.FunctionGraph(f, x_range=x_range, color=mn.RED)

        circle = mn.Circle(radius = 2, color=mn.BLUE)

        transform_f_to_circle = mn.Transform(f_graph, circle)

        self.add(f_graph)
        self.play(transform_f_to_circle, run_time=2)
        self.wait(2)
{% endhighlight %}
