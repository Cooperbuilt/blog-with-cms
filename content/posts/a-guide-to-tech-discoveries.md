---
draft: false
tags:
  - "Agile"
  - "Tech Lead"
  - "Engineering Teams"
description: "This guide takes an in-depth look at tech discoveries as a tool to build trust and confidence within Agile teams. Learn how to make a great Tech Discovery ticket, what your deliverables should be, and more."
title: A Guide to Technical Discovery
template: "post"
slug: "/posts/guide-to-tech-discoveries"
date: "2019-07-1"
category: "Team Building"
---
An agile software team without trust is dead in the water. 

Imagine a team receives a high priority story - 

The product manager is under pressure from stakeholders. They ask for a quick scope to get it into the sprint. The developers aren’t sure how to get it done but, under pressure from the PM, scope it with a best guess. The PM goes back to stakeholders with the estimate and sets a deadline. 

The devs soon realize the problem is far harder than they thought. They don’t communicate well. The deadline comes and goes with no deliverables.

The PM pressures the devs to get it done the next sprint. The devs resent that, and especially resent the inevitable compromise on quality. The feature goes out but needs fast follows to fix bugs.

Next story - the devs inflate estimates to protect themselves. The PM creates an imaginary deadline to speed up development. Things get worse. More deadlines miss.

The next retrospective feels like a witch hunt - or worse, a silent study hall. Children all over the world cry from the loss of engineering throughput.

Raise your hand if you’ve lived this one before ✋.

There’s good news, you can fix it.

It’s sort of simple - developers need challenging work with the time to **do it the right way**. Product managers need accurate estimates to **better serve the user**.

The next time the team faces a story without a clear path to completion, don’t scope it based on what you think you know.  

Build trust with a tech discovery (aka, a spike). 

### What is a Tech Discovery

A time-bound research project that yields a **plan for future work**.

>A tech discovery doesn't result in completed work, but documentation, knowledge, and tasks. 

A tech discovery is an investment by the Product Manager. You spend feature time right now to help figure out the what, how, and why of future work. From this investment, the team receives deliverables that are:

**Demonstrable** - a presentation, prototype, document, slide deck, etc.

**Actionable** - breaks the story down into small tasks. Each task represents one isolated module of work. 

**Estimable** - each task is groomed with well-defined acceptance criteria.

### When to Start a Tech Discovery
1. **Technical Unfamiliarity:** working in a new domain / with a new technology. 

2. **Oversized Story:** this is a gut thing. If the story feels like more than a few days of engineering work it may need a discovery to break it down.

3. **Technical Risk:** the work revolves around critical systems (ex. checkout, payment systems) and the team must minimize impact.

4. **Cross-Functional Effort:** Any story that needs a significant effort across more than one person (back-end to front-end, engineer to business stakeholders, team to team etc.). Use a tech discovery to build a communication plan, data contracts, etc. 

### Anatomy of a Tech Discovery “Ticket”
NOTE: A ticket can mean a lot of different things. I am describing the actual ticket in our project tracker system at Wayfair. Apply this to how you track engineering work where you are. This could mean a ticket in Jira, a Trello card, a post-it-note etc. 

A tech discovery *ticket* should include:

1. A time limit
   - This work should not go on forever. Start with a full engineering days as a baseline. Scope this down or up based on iteration and feedback.
2. Implementation details of current experience and new experience (if applicable)
   - URLs
   - Variations
   - A/B Tests
   - File names
   - Site locations (basket, search pages, etc)
3. Well described user experience for all potential changes
4. Expected deliverables:** 
    * What does your team need out of this discovery to be most effective. This could include: 
       1. Tickets
       * Architecture documentation
        * Data contracts
        * Meeting cadences
        * Contact lists
        * All the above

**All tech discoveries should still result in something demonstrable, estimable, and actionable.

### Tech Discovery Process

How should a developer approach a tech discovery? 

1. Outline the story in “user” terms 
    - “As a user, I want to be able to schedule my delivery in checkout”
2. Outline new experience in technical terms
    - Include what data you will need that don't currently have
    - Include what functionality you need that you don’t currently have
3. Research. Methods include:
    - White-boarding
    - Meeting with subject matter experts.
    - Architecture reviews.
    - The good old google machine.
4. (if applicable) Prototyping
    - Build a low-fidelity proof of concept for your chosen direction. 

### Tech Discovery Deliverables

The medium, method, and content of your deliverables depend on your team's needs. There's one constant - 

**They must be  demonstrable, estimable, and actionable, and appropriate for their audience***.

*Appropriate for their audience means understandable. The burden is on you to make your research findings understandable for all audiences. This includes stakeholders and product managers. 

Here are some possibilities:

#### Documentation

You should always include documentation as one of your deliverables. That documentation should cover: 

1. Recommendations for how to get from the current experience to the new experience. 
    - If new - how to get from greenfield to the new experience.
    - What endpoints/data I will need, what UI we may need, data expectations/contracts, tests, etc.

2. Required architecture changes and impact
3. Asset/Resource requirements 
4. Do you need work from design, quality assurance, business stakeholders.
5. Risk expectations for the suggested path to include impact on other teams and users.
6. Description of work to achieve desired path.
7. This description should be in the form of small, modular tasks. Each task must have defined acceptance criteria.
8. Engineering plan.
    - How we will handle each discrete piece of work.
    - Determine what work you can parallelize.
    - Consider a Gantt (or something similar) chart for larger stories but only if asked for. Reserve for only the largest of projects. 

#### Prototype

Consider a prototype if:

1. You learned a new technology.
2. You need to justify using a new library/tech.
3. Your project is visual.

This can take whatever form you want. Recently, I’ve been using codesandbox.io to demo frontend related prototypes. Anything that can show a proof of concept works.

NOTE: Don’t strive for clean code or stable solutions here. Your goal is to illuminate a path to completion, not complete the story work. 

### Presentations

Consider a presentation if:

1. You must present your findings to people with varied backgrounds. For example, a mix of developers and business analysts. 
2. You need to persuade a stakeholder of something. For example, asking Product for more time to build a cleaner solution. 

Try to give the presentation early in the tech discovery. Presentations always bring comments and questions. 
Use that feedback to hone in your tech discovery even more before the end of the sprint cycle. 

#### Communication Plans

Consider a communication plan if the project needs significant cross-functional work. When you need to work with another team, create a communication plan. 

Communication plans can be standups, consistent e-mail cadences, slack channels, etc. Whatever medium/frequency you determine necessary for heading off potential issues and making the process as smooth as possible. 

This may be one of the more important parts of a tech discovery. Not accounting for cross-functional communication overhead is a chronic offender in under-estimating stories. 

**Main things to include**

1. Data contracts between collaborating groups (new endpoints, data shape, timing, etc).
2. Meeting/check-in cadences with other teams.
3. Ad-hoc standups, team embeds, etc.
4. Points of contact and best contact mediums (slack, email, etc)

#### Delivering Trust

We’ve all lived the story at the start of this article. If you haven’t, are you hiring? (kidding). Now you have the power to change it. 

The next time you run into a story that a feels a little too big or a little too vague, reach for a tech discovery. Yes, it can feel like a lot of time...sometimes it can even feel like wasted time. 

It rarely is, though.

Tech discoveries give developers breathing room to research, think, and craft. They give PMs the power to parallelize work and craft solid timelines. 

They build two of the most important resources on an Agile team: 

**confidence and trust.**


