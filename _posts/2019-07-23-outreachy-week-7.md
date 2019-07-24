---
layout: post
title:  "Titless"
date:   2019-07-23
excerpt: "Everything I've completed till now and things to come"
tag:
- open source
- outreachy
- technology
- gsoc
- research
- python
- jekyll
- fedora
- modularity
feature: https://s3.amazonaws.com/com.twilio.prod.twilio-docs/images/UvUUs1WXEBgwWcMbhbQ_JB5tScafJWbz95oNsfYnIyQQWW.width-808.jpg
comments: false
---

Hi! I'm VERY excited to be writing this blog post. I have essentially only a month left fro my Outreachy internsip with The Fedora Project to end. Surprisingly, this realization is of a bitter-sweet one. The amount of skills I've picked up duroing my 2 months of Outreachy are simply priceless. I tried to code within deadlines, learnt new concepts and technology, implemeted it to reflect into my code, networked with different people as pasrt of fedora community bonding, made major mistakes, tried to redeem myself from them (still working on that :shy:), and Im DEFINITELY not done just yet! 

## Optimististic timelines I create for myself :')

Outreachy mentors and interns start the internship with a specific set of project goals. These timelines are ususally a very optimistic view of what could happen if everything goes exactly as planned. It often doesn't, but people still make optimistic plans. This concept is called as [Planning Fallacy](https://en.wikipedia.org/wiki/Planning_fallacy). Projects always feel easy to work on initially. But delays to projects happen. Maybe your project turned out to be more complicated than you or your mentor anticipated. Maybe you needed to learn some concepts before you could tackle project tasks. Maybe the community documention wasn't up-to-date or was wrong.

Keeping all this in mind, this was my initial timeline:

May 20, 2019 - May 25, 2019 - Relocating to a new city, hence won't be completely active. Shall participate via community bonding. Will continuously be in touch with the mentor to break down the internship tasks into steps for the coming days. Will go through the code base thoroughly to look for vulnerabilities, potential bugs, and redundancies.

May 26, 2019 - June 10, 2019: Implement any test that exists only in Python, in C, to ensure that they are getting properly tested by the static analysis and memory-leak checkers (valgrind). This would essentially involve copying already written python tests to C.

June 11, 2019 - July 10, 2019:  Write code for a set of new tests which willl be provided by the mentor.

July 11, 2019 - Aug 11, 2019:  Scan the library for any leftover tests, or vulnerabilities. Write exhaustive unit tests for the leftover code blocks and extend some already written tests (if necessary). Try to make the tests modular by breaking them into smaller tests for maintainability.	

Aug. 12, 2019 - Aug. 20, 2019: Update documentation for all the changes made during the internship.


And then my project changed completely. This time, my mentor made my new timeline: 

5. There's two parts to the task:
	1. **Phase 1**: Extract all translatable strings from the modules that have been built for each Fedora release and submit them to the translation tool, Zanata, for the translators to work on.

	2. **Phase 2**: Retrieve the finished translations and use the libmodulemd API to turn them into modulemd-translations documents.

6. **Stretch Goal**: Include the code from Phase 2 into Fedora's repo creation automation so that it gets updated automatically every day.

I must say this timeline seemed very relaxed and exciting to me. This project felt like I was a consumer And a developer for libmodulemd both at the same time. 

## Approach to any task I undertook

Discuss goal
Write a design document
Write test
Write code
Ask doubts and do thorough readin throughout. 

## Task 1
 > Extract all translatable strings from the modules that have been built for each Fedora release and submit them to the translation tool, Zanata, for the translators to work on.

To put this in terms of code, I was given a ModulemdIndex object as input, and I needed to return a Babel catalog as the output. 

Module stream pair. hierarchy. What all strings. get the latest module stream pair version because nsvca cpould be all reoplaced. gettext .po file takes only unique strings. store locations. multiple occurences. find a way to retrioeve the locations back. 
explain .po files and babel catalog

explain code thouroughy.

## Task 2
> Retrieve the finished translations and use the libmodulemd API to turn them into modulemd-translations documents.

Given a list of .po files, and initiak ModulemdIndex object, ctreate modulemdTranslations objects and add them to the index. This index would later be injected into modified yamls and shipped out as new fedora releases. 

explain these differnt po. files and their attributes.. 
Realized my tests writtenf for task 1 were wrong. Fixed them. Task 1 code was breaking. Fixed that. resumed task 2. 
explain code flow for this. explain data strictures if needed.

PR merged! 2 months, 2 tasks completed, we were right on time till now!

## Stretch goal task 3
> Include the code from Phase 2 into Fedora's repo creation automation so that it gets updated automatically every day.

Till now everything were just functions being called on static files. Now we would pull actual yamls from fedora's repo creation tool, Koji, and then apply the process to obtain translations. WIP

Koji calls are netwrok dependednt, Tests were failing sue to timeout. Needed to mock a koji session. 
exlaoin code

## Goal to wrap up

Will write thorough documentation for all of this. Looking foward to speak about this in Flock. such a rewadring experience. definitely involving more people from uni into this. 

