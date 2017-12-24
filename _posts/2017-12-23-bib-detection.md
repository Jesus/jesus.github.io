---
layout: post
title:  Bib detection
date:   2017-12-24 10:39:00 +0000
categories: machine-learning
---

I've completed my first sort-of-useful AI program. Woo hoo!

So here's how to build a program to identify and recognize bib numbers
in three simple steps:

 1. Train a model to detect bibs.
 2. Train another model to recognize the number in each bib.
 3. Build a small web service to use both models and respond with the result.

That easy? Well...

These weren't three simple steps, it took me a few months to complete during
many hours of my spare time.

I was lucky enough to be able to use two models already implemented in
Tensorflow.
Despite it took me so long to figure everything out, the most
important challenges were already solved by the people who created these models.
So a good bit of my time was spent just familiarizing with the API.

At the time of writing this post, it's possible to detect bibs and their
numbers. I created two repositories:

 1. [`bibo-lab`][bibo-lab], this is used for training. Its purpose is to
    create a frozen inference graph that can be used by others for inference.
    Unfortunately, this is super chaotic at the moment, I'll sort that out
    soon.
 2. [`bibo`][bibo], this contains the code for inference. It should be
    relatively easy for anyone to clone and run it, questions are welcome.

Here's an example of the result:

<img src="/assets/images/ssc.jpg" class="inline-text large-image" />

Some of the predictions aren't accurate, but I think this isn't bad for a start.

The final goal is to build a web application which can be used by runners after
a race to find the photos where they appear. Every running competition may have
hundreds of photos of the event, so not many people will spend the time it
to find themselves. Using this bib detector, it'll be possible to just visit
the website and type your bib number to find all the photos where you appear, or
where your bib appears actually!

There's a lot of work remaining to achieve that goal. I'll be publishing more
news on this project. For now, I'm just happy to to have put together the whole
pipeline. There're many things we can do at this point:

  - Make a proper measurement of the model's accuracy, so it can be
    improved over time.
  - Update the program so it can be used as a service by a standalone web
    application. I think I'll create a simple HTTP API.
  - Buid a small web application so this can be used online by end users, the
    runners.

I'm looking forward to release further news on this topic. So keep tuned!

<!-- The people who are leading the steps are
constantly publishing their advances and sharing .


I think we're very lucky to be able to access the newest papers in the field.
Never before in history this has been so easy. What a time to be alive! -->

[bibo]: https://github.com/Jesus/bibo
[bibo-lab]: https://github.com/Jesus/bibo-lab
