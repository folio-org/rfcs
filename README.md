# Folio RFCs

For process overview, please see https://wiki.folio.org/display/TC/RFC+Process

## Overview

* Folio RFCs are stored as MarkDown documents as part of the GitHub RFC Repo.
* RFCs evolve through a life-cycle from “submitted” to either “active” or “rejected”.
* The RFC approval process is managed as a pull request and undergoes a formal review
* An open final review period is provided for community feedback before acceptance.
* RFCs are available to be implemented once they become “active”

## When you need to follow this process

You need to follow this process if you intend to make "substantial" changes to Folio the platform, including but not limited to: Okapi, Stripes and Folio Core Modules, and Folio Architecture. What constitutes a "substantial" change is evolving based on community norms, but may include the following.

* Any changes to the Folio infrastructure including Okapi and Stripes
* The addition or removal of conventions, patterns and protocols that are key to the interoperability of Folio components.
* The removal of features that already part of a Folio release.
* Any change required for integration with systems external to Folio
* Changes that require the coordination between multiple Folio components or services.
* A new feature which might be considered redundant to existing features.

Some changes do not require an RFC:

* Rephrasing, reorganizing, refactoring
* Additions that strictly improve objective, numerical quality criteria (warning removal, speedup, better platform coverage, more parallelism, trap more errors, etc.)
* Changes that are scoped entirely to a single component or service of Folio
* Additions only likely to be noticed by other developers-of-Folio, invisible to users-of-Folio.

If you submit a pull request to implement a new feature without going through the RFC process, it may be closed with a polite request to submit an RFC first.

## Before Creating an RFC

A hastily-proposed RFC can hurt its chances of acceptance. Low quality proposals, proposals for previously-rejected features, or those that don't fit into the near-term roadmap, may be quickly rejected, which can be demotivating for the unprepared contributor. Laying some groundwork ahead of the RFC can make the process smoother.

Although there is no single way to prepare for submitting an RFC, it is generally a good idea to pursue feedback from other project developers beforehand, to ascertain that the RFC may be desirable; having a consistent impact on the project requires concerted effort toward consensus-building.

The most common preparations for writing and submitting an RFC include talking the idea over on Folio Slack, discussing the topic on Folio Discus, if applicable presenting the proposal to the relevant SIG(s) and occasionally fully describing more complex concepts on the Folio Wiki.

As a rule of thumb, receiving encouraging feedback from long-standing project developers, and members of relevant SIGs is a good indication that the RFC is worth pursuing.

## What the Process Is

* Fork the RFC repo RFC repository: https://github.com/folio-org/rfcs
* Copy '0000-template.md' to 'text/0000-my-feature.md' (where "my-feature" is descriptive. don't assign an RFC number yet).
* Fill in the RFC. Put care into the details: RFCs that do not present convincing motivation, do not demonstrate understanding of the impact of the design, or are disingenuous about the drawbacks or alternatives tend to be poorly-received.
* Submit a pull request. As a pull request the RFC will receive design feedback from the larger community, and the author should be prepared to revise it in response.
* Build consensus and integrate feedback. RFCs that have broad support are much more likely to make progress than those that don't receive any comments. Feel free to reach out to the RFC assignee in particular to get help identifying stakeholders and obstacles.
* The Technical Council will assign one or more reviewers to the RFC pull requests. 
* The Technical Council may reject the RFC at this point or refer it back to its author(s) for modifications before further consideration.
* The reviewers will discuss the RFC pull request, as much as possible in the comment thread of the pull request itself. Offline discussion will be summarized on the pull request comment thread.
* Eventually, the Technical Council will decide whether the RFC is a candidate for inclusion in Folio.
* RFCs that are candidates for inclusion in Folio will enter an open "final comment period" lasting a specified number of days depending on the scope of the RFC. The beginning of this period will be signaled with a comment and tag on the RFC's pull request. 
* An RFC can be modified based upon feedback from reviewers, the Technical Council and/or the Folio community. Significant modifications may trigger a new final comment period.
* An RFC may be rejected by the Technical Council after public discussion has settled and comments have been made summarizing the rationale for rejection. A member of the Technical Council should then close the RFC's associated pull request.
* An RFC may be accepted at the close of its final comment period. A Technical Council member will merge the RFC's associated pull request, at which point the RFC will become “accepted”.
RFC’s may be assigned a timeline that could delay their implementation. When ready for implementation the RFC becomes “active”.
Rejected RFC’s will not be reopened for reconsideration. Instead a new RFC, containing substantial modifications, must be submitted for consideration.

## The RFC Lifecycle

The following states represent the lifecycle of the RFC. The states are implemented as “Labels” on the GitHub pull request corresponding to the RFC draft.

* **Submitted**: initial state of RFC when submitted by their authors
* **Under Review**: RFC has been assigned to one or more reviewers
* **In Final Review**: RFC is in the final review period
* **Accepted**: RFC has been accepted but is not yet ready for implementation.
* **Active**: RFC has been accepted and is now ready for implementation
* **Rejected**: RFC has been rejected and is now effectively closed.

## Reviewing RFCs

Each week the Technical Council will attempt to process some set of open RFC pull requests. This would include assigning reviewers, accepting, rejecting or activating RFCs. 
Implementing an RFC

The author of an RFC is not obligated to implement it. Of course, the RFC author (like any other developer) is welcome to post an implementation for review after the RFC has been accepted.
Furthermore, the fact that a given RFC is “active” implies nothing about what priority is assigned to its implementation, nor whether anybody is currently working on it.

**Folio’s RFC process owes its inspiration to the [Ember] and [Rust] RFC processes.**

[Ember]: https://github.com/emberjs/rfcs
[Rust]: https://github.com/rust-lang/rfcs

