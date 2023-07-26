---
title: "Understanding the shift of Frontend Development towards Declarative UI and redux-like state management"
datePublished: Wed Jul 26 2023 12:12:39 GMT+0000 (Coordinated Universal Time)
cuid: clkjoru5z000l09jxecoa0tna
slug: understanding-the-shift-of-frontend-development-towards-declarative-ui
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/qWwpHwip31M/upload/4ee4fbedb3b7399090e6c582c2595e39.jpeg
tags: flutter, reactjs, frontend-development

---

Declarative UI and one-way flow of state is something that invariably developers seem to prefer and all frontend engineering platforms have been progressively moving towards it.

The problem though lies that most developers starting out are not coached properly for it.

## How UI is represented ?

Let's try to understand the problem here.

Classically how UI is represented and how it triggers logic is a bit like this

UI interacts with some "controller" like layer which in turn interacts with local DBs or APIs.

That's pretty much the basic 'structure' of this model.

![Image](https://pbs.twimg.com/media/F01230TX0AkPjro?format=jpg&name=medium align="left")

As UIs keep updating, more and more complexities take place.

More UI elements are 'interactive' - clicking, toggling, typing, and scrolling them trigger different functions on the controllers, they fetch/save more stuff to the DB/API, and more things in UI updates.

Over time as the complexity grows and grows, problems arise. What if when the user is not logged in, someone scrolls the feed? We want only 2 folds of info for guest users or else trigger login.

Ok after login when they come back to this screen how to handle that?

That's just one example, but what keeps happening is that these different UI elements which trigger logic in different 'controller' like places need to have co-dependency on each other. Sometimes cyclic dependencies hit your performance with a ton of bricks.

Eventually, some of your senior developers - the ones that keep a copy of SICP under their pillow, and keep uttering "monad" and "closure" with an expression like food bloggers do when reviewing European dishes - want 'state management' to become 'declarative'.

Time to unpack.

## Understanding State

So to understand what 'state' is, and get the essence of the rest of what I'll be saying, it might serve well to go for a quick trip to a state visualiser, like say - XState.

Here go play around with the visualiser here (you can add more states)

[https://stately.ai/viz](https://stately.ai/viz)

![Image](https://pbs.twimg.com/media/F02AhzmWwAEqK62?format=jpg&name=medium align="left")

Attaching here a few framework diagrams that you might have encountered in your life which try to sell the idea of 'one way flow of state' or some similar idea (in different words).

Recognise any? (read the alts, if you can't)

![Flux design pattern by Facebook](https://pbs.twimg.com/media/F02At14WIAIMX9r?format=png&name=small align="left")

![VueJS official docs of Vuex state management](https://pbs.twimg.com/media/F02A5cKWIAQ1lAY?format=png&name=900x900 align="left")

![Flutter Bloc design pattern](https://pbs.twimg.com/media/F02A255WYAgQdEV?format=png&name=small align="left")

![Apple official docs for state management with SwiftUI](https://pbs.twimg.com/media/F02A8kMWwAESwKx?format=png&name=medium align="left")

This is typically held in contrast to 'bad', 'older' state management philosophies like "2-way data binding" or "MVC"

The OG guys who promulgated that was Microsoft & Angular

I think the simple theory - "these have more arrows, i.e. must be bad" holds good collective wisdom 😂

![Angular docs describing 2-way bindings
](https://pbs.twimg.com/media/F02BN6zXoAEMZpi?format=png&name=small align="left")

![Flux vs MVC from Facebook's doc on Flux](https://pbs.twimg.com/media/F02CSLAXoAMwaT3?format=png&name=small align="left")

![MVC model described on MDN official docs](https://pbs.twimg.com/media/F02CVeCagAE-D6T?format=png&name=small align="left")

## A better way to work with state ?

What are the wise, senior, developers who have spent a lot of years wrangling with the basic problem of "users do something on UI, some logic runs, the UI changes to something else" saying though?

And why is the utopia they are showing still not lead to performant UIs?

What they (or 'we', I am part of this group) are saying is

"whatever you see on the UI is a representation of state"

Everything. Buttons being disabled or enabled, to the data the user fills in the input boxes, to what shows up on the Twitter app's feed - it is all state.

Now if we can make this little magic box, which, regardless of whatever weirdass complexities we bring into the 'business logic' of your program, can just chuck a "state" into, and it chucks out the "UI" that should represent that state, we are golden!

![Image](https://pbs.twimg.com/media/F02Ck2qaIAE7ckj?format=jpg&name=medium align="left")

Never will the UI chuck something back into the magic box though. And never will the state change because the magic box said so. The magic box has but one function.

Take the 'state' in a machine-readable format (JSON, XML, objects, whatever) and turn it into user interface.

But there is a problem with this approach

See the thing is user interfaces are not like frames of a movie. We do not (and should not) generate the whole damn frame every time something changes

You clicked like on this tweet? Cool just update the ♡ to ❤️, not the whole page!

The utopia of declarative UI lies in aligning "view hierarchy" with "information hierarchy".

This is something that is visualised by nothing better than 2 images from VueJS's official docs.

If you look at the UI (grey, left) do you immediately see the code I have written?

![Image](https://pbs.twimg.com/media/F02EBzUXgAAeflj?format=png&name=large align="left")

![Image](https://pbs.twimg.com/media/F02EDGGXgAMOjmU?format=png&name=900x900 align="left")

![Image](https://pbs.twimg.com/media/F02FA1CacAUpqwm?format=png&name=small align="left")

The key to understanding why the 2 hierarchies must align (in the minds of those who are creating this) is because of the aforemention problem - clicking the like button shouldn't 'redraw' my whole screen.

For example, refreshing the articles should do this

![Image](https://pbs.twimg.com/media/F02GaarakAQB45Q?format=jpg&name=medium align="left")

Or more precisely, if I clicked like on a particular post, even more specific set of things to update.

![Image](https://pbs.twimg.com/media/F02G278akAMWnZ-?format=jpg&name=medium align="left")

## Caveats and Dragons Beware

Apart from the 'creators' of this product - which include the designers of the UI to a degree, and not only the engineers of the UI but those 'generating' the objects that constitute state (which could be happening partly on the backend) - aligning on this hierarchy which itself is not the easiest thing to happen (when a group of 20 people are building technically complex things, everyone visualising the exact same hierarchy is not that easy), there also are certain things we need from that 'magical box' we talked about.

The magic box must be smart enough to actually see which parts of the information tree actually changed, and accordingly, on the UI actually change only those parts of the view tree (for eg. highlighted in pink)

Some frameworks put the onus of the 'smartness' on devs, some don't

![Image](https://pbs.twimg.com/media/F02I1eGaUAAyHqB?format=jpg&name=medium align="left")

Putting the onus of the smartness on the devs has it's downsides of coaching the devs about this whole thing (so far I have been talking in this thread) - they need to be made cognizant of the 'cost of drawing' elements, and 'lifting up the state' term starts to appear soon.

Being able to untangle a thread of state from a mess of highly co-dependent variables in the memory, 6 screens deep in the flow of an app with 50 screens, is not exactly easy to wrangle for new developers, especially after parachuting into an already complex project.

Frameworks like Flutter put the onus on the developers, and all online articles which cover "improving performance on Flutter" will always start with the question "are you updating more stateful widgets than you need?"

Some others do some smartness on your behalf, like React+Redux where there are magical "dom diffing" operations under the hood which will diff the new state with old state to figure out which elements need drawing again, or Jetpack Compose where Compose internally does this.

The framework doing the smartness for you has 2 downsides.

1. you still need to architect the information hierarchy properly. if the object tree itself gets updated right from the root (or is undiffable), framework cannot do any magic
    
2. more magic == less debugability. simple