+++
title = "I Vibecoded the Software to Run My Own Bakery"
date = 2026-07-13

[taxonomies]
tags = ["ai", "building", "baking", "personal"]

[extra]
lang = "en"
toc = false
copy = false
comment = false
+++

A few months ago I said I was starting a YouTube channel about AI while making cupcakes. What I did not say, mostly because I did not know it yet, was that I'd end up building an actual piece of software to run an actual little bakery. But here we are.

It's called [Let That Bake](https://let-that-bake.suyan.dev), same as the channel, and it's the tool I wished existed every time I had three cakes due in the same week and a brain that could not keep track of which one needed to start chilling tonight.

## The premise is dumb and I love it

I'm a software engineer who bakes. So instead of using a spreadsheet like a normal person, I built an app that takes an order, figures out the whole backwards timeline (bake, chill, decorate, the whole dance), and fits it around the days I've already blocked off. It tells me what to start today. That's the whole pitch. It turns out "what do I need to start today" is the only question that actually matters when you're juggling more than one bake.

And yes, I built it mostly by vibecoding. Describing what I wanted, letting the model draft it, poking at what came back, learning the parts I didn't understand as I went. Half of what I now know about shipping a real web app I learned the same way I learned to pipe a buttercream rosette: YouTube, at an hour I should have been asleep.

## The part I'm actually proud of

Here's the feature that made it feel alive.

The app uses AI to draft a production plan for every order. Great, except the AI's first guess is generic. It doesn't know that *my* vanilla layers need an extra day, or that I like to crumb coat the night before, not the morning of.

So I made it learn. When I tweak the steps on a real order, I can hit "save as default," and those edits become the recipe's new timeline. The next time someone orders that same cake, it doesn't start from the generic template anymore. It starts from *my* version. The AI drafts the first draft; one real bake tunes it; the item just... remembers.

No model retraining, no magic. Just a tight little loop where the software gets a bit more like me every time I use it. That's the thing I keep thinking about. The AI didn't replace my judgment. It gave me a fast first draft that I get to correct, and it keeps the correction.

## The bigger thing I keep circling

When I started the channel I said I was standing at a crossroads, kind of excited, kind of nervous, mostly trying to keep up. A few months of building this hasn't resolved that. But it's pointed at something.

Software is getting absurdly cheap to make. I built a real tool for a real (tiny, one-person, not-yet-profitable, let's be honest) business in the cracks of my evenings. Meanwhile the thing that's getting *more* valuable is the human part. Someone you trust made your kid's birthday cake by hand. The tools that matter are the ones that take the annoying operational overhead off an artisan's plate without taking the craft out of it.

That's the bet. Compress the boring parts, protect the good parts. Let That Bake is my first swing at it.

The bakery is not profitable. The betrayal of my software engineering career is, however, going great.

Let that bake.

---

*The app is live at [let-that-bake.suyan.dev](https://let-that-bake.suyan.dev). More on the channel at [letthatbake.ai](https://letthatbake.ai).*
