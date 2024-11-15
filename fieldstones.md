## Integration vs Unit Testing

The debate about integration testing vs. "unit" testing continues to rage on. It's not really an either-or choice&mdash;both have their place.

We like having tons of tests that verify small behaviors ("units"), because they're easy to write, maintain, and provide very fast feedback. But they're insufficient. We need to also verify that end-to-end functionality works, and that configurable things actually work. It's dumb, for example, to try to verify that CORS code works without making actual HTTP calls.

We like having integration tests. They're not usually as easy to write, usually much harder to maintain, and provide slower feedback. As such, they're not the best choice when it comes to verifying the nuances of small little computational variances (how does the system handle nulls, for example, and how do we blast through verifying the dozens of validations we likely have on a given set of inputs). But integration tests cover concerns that unit tests cannot, and are essential for having the confidence to deploy to production.
[ end aside? ]

[ odd thought ]
(Never mind that measuring some of these characteristics might be near impossible. How, for example, do you compare the size of one feature to another?)  [[ maybe reference Capers Jones, "Programming Productivity"? or something from Brooks, "no silver bullet"? ]])
[ end odd thought]

[ some history ]
Our programming history has seemed a pendulum: Early on, we got code to work, with little consideration for how it was structured. As systems grew in complexity and size, we realized that maintaining them was becoming increasingly expensive. In response, we introduced increasingly strict guidelines on how to structure code, plus increasingly heavy speculative planning about how that structure should be designed.

We went too far. [brief bit on waterfall / misunderstanding of Royce]

The pendulum swung back to a process that rejected the heavy-up-front speculation, whether around project planning or the system's needs and design. [describe agile origination briefly here]

The outcome of the meetings-of-minds was the consensus named "agile software development." It was the lowest-common denominator that the minds could agree upon. Unfortunately, there was apparently little LCD for the topic of how to address design in a software project.

Never mind that there was still value in considering design throughout a project. XP, for example, taught us how to incrementally and iteratively produce a quality design by employing quality controls like TDD and pair programming. Such controls continue to help us safely address continuous changes to design.

Still, agile never rejected all the useful things we'd learned about design to that point. Wiser teams knew that poor design slowed things down, and maintained a high level of awareness about the system's current design. They even continued to draw quick UML sketches to help streamline design discussions. What they didn't do was invest copious up-front effort in a speculative design and consider it complete.

The pendulum started falling off the hook, unfortunately, around the end of the first decade of the new millenium. As demand for programmers grew rapidly, web searches and the need for quick answers overtook principled, conceptual, and comprehensive learning. Dramatically increasing numbers of developers read less and knew less, particularly about design (a much harder topic to learn than, say, the new cool Javascript framework of the week).

We were also betrayed by the fact that agile seemingly de-emphasized design. (It didn't. Read the dozen principles behind the agile manifesto: https://agilemanifesto.org/principles. At least one principle directly states the importance of design: "Continuous attention to technical excellence and good design enhances agility." Other principles implicitly speak toward good design, and some aren't at all possible without it.) The one agile process that captured the most attention suggested that you should sprint and slap out software each month. But it never said anything from a technical perspective about how to do that successfully. (Notably, one of Scrum's proponents went so far as to suggest that all you had to do was throw a bunch of developers in a room, and that they were smart enough to figure all that stuff out.)

If anything, an iterative-incremental process will exacerbate the results of not heeding design. You'll create a steaming scrum-pile of unmaintainable software in 6 to 18 months, maybe sooner, if you ignore design as you sprint toward a solution.

It's not that Scrum's proponents said to ignore design, however. When pressed, they most certainly said that design is important, that Scrum's goal was to provide a project framework, and that you should learn quality design elsewhere.

Unfortunately, all this ended up being heard as "agile says we don't have to worry about design." In the last 15 years, teams have returned to off-the-cuff coding, with just about everybody for themselves deciding what a good design was. The things that stuck seemed to be least meaningful (if not entirely useless)&mdash;just about everyone knows that you *must* have curly braces around even a single-line block of code, even if they're fuzzy about why. Or fuzzy about how a test-driven approach might make that ABSOLUTE RULE OF PROGRAMMING moot. Cargo culting for the win!


## anti-anti-SOLID rant. Postmodernist nihilism

A few years back, it became chic to dismiss aforementioned time-tested design perspectives such as SOLID.

Some folks said things like "everything in SOLID is wrong." [ref: North article] If this is correct, it's for but a small number of contexts and for a peculiar definition of "wrong."

Another way to phrase this cynical take is to suggest that everything is wrong, it's just a matter of degree. All estimates are wrong, for example; it's just a matter of degree--weeks, days, minutes, hours, seconds. That doesn't mean, however, that estimates aren't useful in certain contexts.

The better mindset suggests that, most of the time, the principles we've learned will help you develop software more easily.

blah blah -- all accepted design perspectives (solid, code smells, simple design, ...) lead to the same outcome: clarity, conciseness, cohesiveness, and confirmability.


## Method Design vs. Class Design

We split method and class considerations into two chapters within Clean Code. The reality is that neither can be addressed in isolation. Your system's design is a careful balance of method and class (or function and module) implementation.
