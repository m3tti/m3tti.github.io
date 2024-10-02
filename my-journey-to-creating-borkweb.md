# My Journey to Creating Borkweb
I've been a developer for over 13 years, and for a long time, I felt like I was just going through the motions. I'd worked with all the big-name frameworks - Ruby on Rails, Next.js, Laravel - but none of them ever really felt like home. They all had their strengths, but they also had their weaknesses, and I often found myself wishing for something more.

One of the things that always bugged me was the need to switch between languages. I'd be working on a project, cruising along in my favorite language, and then suddenly I'd have to switch to something else to get the frontend working. It was like hitting a speed bump - it would slow me down and take me out of my flow.

I started to wonder if there was a better way. Was it possible to create a framework that would let me write both frontend and backend code in a single language? I started digging around, looking for inspiration, and that's when I stumbled upon Lisp.

I was blown away by the power and flexibility of Lisp, particularly Common Lisp and its Parenscript library. It was like a whole new world had opened up to me.

But, as much as I loved Lisp, I knew it wasn't the answer. Its adoption had declined, and it wouldn't be the choice for a modern web framework with high adoption due to its past. So, I kept looking.

That's when I discovered a new Lisp that runs on the JVM, and even a native binary that could run my code without the need of the JVM. And then I found Squint too, a compiler that lets you write frontend code in the same language. It was like the pieces were finally falling into place.

## The Birth of Borkweb
With Squint and Clojure, I had the tools I needed to create a framework that would let me write both frontend and backend code in a single language. I started building, and Borkweb was born.

Borkweb is built on top of Babashka and Squint, which allows me to write rich javascript components and sprinkle them into my application without worrying about complex state handling and serialization. And with HTMX, I can provide a seamless SPA-like navigation experience.

I didn't create Borkweb just to create another framework. I created it because I wanted to make web development easier and more enjoyable for myself and others. I wanted to create a framework that would let developers focus on writing code, not on setting up complex build pipelines and worrying about state handling or even implement user registration and login over and over again.

I also wanted to create a framework that would appeal to newcomers and showcase the awesomeness of our beloved Clojure language. I believe that Clojure is an amazing language, and I want to share that with the world.

## What's Next for Borkweb
Borkweb is still a work in progress, but I'm excited about its potential. I'm excited to see where it will go and how it will evolve. I'm excited to share it with the world and see what other developers think.

If you're interested in learning more about Borkweb, I'd love to hear from you. Let's chat about it and see where it takes us.

No Build. No switching between languages. Just enjoying the ride. 
