# ReScript RFCs

The "RFC" (request for comments) process is intended to provide a consistent and controlled path for new features to enter the project.

Many changes, including bug fixes and documentation improvements can be implemented and reviewed via the normal GitHub pull request workflow.

Some changes though are "substantial", and we ask that these be put through a bit of a design process and produce a consensus among the ReScript core team.

## When to follow this process

You should consider using this process if you intend to make "substantial" changes to ReScript or its documentation. Some examples that would benefit from an RFC are:

- Any semantic or syntactic change to the language that is not a bugfix.
- Removing language features, including those that are feature-gated.
- The introduction of new toolchain that brings about a significant change in the ReScript experience. (e.g. GenType, Reanalyze)

Some changes do not require an RFC:

- Rephrasing, reorganizing, refactoring, or otherwise "changing shape does not change meaning".
- Additions that strictly improve objective, numerical quality criteria (warning removal, speedup, better platform coverage, more parallelism, trap more errors, etc.)
- Additions only likely to be noticed by other developers-of-rescript, invisible to users-of-rescript.

## Before creating an RFC

A hastily-proposed RFC can hurt its chances of acceptance. Low quality proposals, proposals for previously-rejected features, or those that don't fit into the near-term roadmap, may be quickly rejected, which can be demotivating for the unprepared contributor. Laying some groundwork ahead of the RFC can make the process smoother.

Although there is no single way to prepare for submitting an RFC, it is generally a good idea to pursue feedback from other project developers beforehand, to ascertain that the RFC may be desirable; having a consistent impact on the project requires concerted effort toward consensus-building.

The most common preparations for writing and submitting an RFC include talking the idea over on the official [forum](https://forum.rescript-lang.org/). You may file issues on this repo for discussion.

As a rule of thumb, receiving encouraging feedback from long-standing project developers, and particularly members of the relevant core team is a good indication that the RFC is worth pursuing.

## What the process is

- Fork the RFC repo https://github.com/rescript-lang/rfcs
- Copy `0000-template.md` to `text/0000-my-feature.md` (where 'my-feature' is descriptive. Don't assign an RFC number yet).
- Fill in the RFC. Put care into the details: **RFCs that do not present convincing motivation, demonstrate understanding of the impact of the design, or are disingenuous about the drawbacks or alternatives tend to be poorly-received.**
- Submit a pull request. As a pull request the RFC will receive design feedback from the larger community, and the author should be prepared to revise it in response.
- Now that your RFC has an open pull request, use the issue number of the PR to rename the file: update your `0000-` prefix to that number. Also update the "RFC PR" link at the top of the file.
- Build consensus and integrate feedback. RFCs that have broad support are much more likely to make progress than those that don't receive any comments.
- Eventually, the team will decide whether the RFC is a candidate for inclusion in ReScript. Note that the team review may take a long time, and we suggest that you ask members of the community to review it first.
- RFCs that are candidates for inclusion in ReScript will enter a "final comment period" 7 calendar days. The beginning of this period will be signaled with a comment and tag on the RFC's pull request.
- An RFC can be modified based upon feedback from the team and community. Significant modifications may trigger a new final comment period.
- An RFC may be rejected by the team after public discussion has settled and comments have been made summarizing the rationale for rejection. A member of the team should then close the RFCs associated pull request.
- An RFC may be accepted at the close of its final comment period. A team member will merge the RFCs associated pull request, at which point the RFC will become 'active'.

## The RFC lifecycle

Once an RFC becomes active, then authors may implement it and submit the feature as a pull request to the ReScript repo. Becoming 'active' is not a rubber stamp, and in particular still does not mean the feature will ultimately be merged; it does mean that the core team has agreed to it in principle and are amenable to merging it.

Furthermore, the fact that a given RFC has been accepted and is 'active' implies nothing about what priority is assigned to its implementation, nor whether anybody is currently working on it.

Modifications to active RFCs can be done in followup PRs. We strive to write each RFC in a manner that it will reflect the final design of the feature; but the nature of the process means that we cannot expect every merged RFC to actually reflect what the end result will be at the time of the next major release; therefore we try to keep each RFC document somewhat in sync with the language feature as planned, tracking such changes via followup pull requests to the document.

## Implementing an RFC

The author of an RFC is not obligated to implement it. Of course, the RFC author (like any other developer) is welcome to post an implementation for review after the RFC has been accepted.

If you are interested in working on the implementation for an 'active' RFC, but cannot determine if someone else is already working on it, feel free to ask (e.g. by leaving a comment on the associated issue).

## Reviewing RFCs

Currently, the ReScript team cannot commit to reviewing RFCs in a timely manner. When you submit an RFC, your primary goal should be to solicit community feedback and generate a rich discussion. The ReScript team will attempt to review some set of open RFC pull requests as often as possible.

We try to make sure that any RFC that we accept is accepted at the monthly team meeting, and reported in core team notes. Every accepted feature should have a core team champion, who will represent the feature and its progress.

## Inspiration

ReScript's RFC process owes its inspiration to the RFC process adopted by [Rust](https://github.com/rust-lang/rfcs), [Yarn](https://github.com/yarnpkg/rfcs), [React](https://github.com/reactjs/rfcs), and the [Ember](https://github.com/emberjs/rfcs).
