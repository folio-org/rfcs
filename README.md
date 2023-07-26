# Folio RFCs

For process overview, please see https://wiki.folio.org/display/TC/RFC+Process

## Overview

* Folio RFCs are stored as MarkDown documents as part of the GitHub RFC Repo.
* RFCs evolve through a life-cycle from “submitted” to either “active” or “rejected”.
* The RFC approval process is managed as a pull request and undergoes a formal review
* An open final review period is provided for community feedback before acceptance.
* RFCs are available to be implemented once they become “active”


## Getting started

* Fork the RFC repo RFC repository: https://github.com/folio-org/rfcs
* Copy '0000-template.md' to 'text/0000-my-feature.md' (where "my-feature" is descriptive. don't assign an RFC number yet).
* Fill in the RFC. Put care into the details: RFCs that do not present convincing motivation, do not demonstrate understanding of the impact of the design, or are disingenuous about the drawbacks or alternatives tend to be poorly-received.
* Submit a pull request. As a pull request the RFC will receive design feedback from the larger community, and the author should be prepared to revise it in response.
* The Technical Council will assign one or more reviewers to the RFC pull requests and the feedback process will begin. See the wiki for more details: https://wiki.folio.org/display/TC/RFC+Process 
  

## The RFC Lifecycle

The following states represent the lifecycle of the RFC. The states are implemented as “Labels” on the GitHub pull request corresponding to the RFC draft.

* **Submitted**: initial state of RFC when submitted by their authors
* **Under Review**: RFC has been assigned to one or more reviewers
* **In Final Review**: RFC is in the final review period
* **Accepted**: RFC has been accepted but is not yet ready for implementation.
* **Active**: RFC has been accepted and is now ready for implementation
* **Rejected**: RFC has been rejected and is now effectively closed.


**Folio’s RFC process owes its inspiration to the [Ember] and [Rust] RFC processes.**

[Ember]: https://github.com/emberjs/rfcs
[Rust]: https://github.com/rust-lang/rfcs

