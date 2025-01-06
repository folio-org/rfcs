* Start Date: 11/13/2023
* Contributors:
  * [Craig McNally](cmcnally@ebsco.com)
  * [Vince Bareau](vbareau@ebsco.com)
* RFC PRs:
  * PRELIMINARY REVIEW: https://github.com/folio-org/rfcs/pull/20
  * DRAFT REFINEMENT: TBD
  * PUBLIC REVIEW: TBD
  * FINAL REVIEW: TBD
* Outcome: Cancelled - The Technical Council has decided that the best path forward is to close these smaller, focused RFCs in favor of a single, wider-scoped RFC.

# Bounded contexts

## Summary
The term bounded context often appears in discussions about decomposing a monolithic system into multiple microservices.  Instead of all services sharing a single database, the system is partitioned into several bounded contexts, each with it's own database, data models, etc.  In Folio's case, we're working the other direction.  When the project started, it was decided that each module should have their own database, and cross-module database access must be avoided.  Coupling this with another practice the project has followed, separating business logic and storage modules, we wind up with suboptimal service/storage interactions.  It is proposed that we introduce the concept of bounded contexts, which would align with Application boundaries.  Any of the backend modules in a given Application would be able to directly access the storage layer.

## Scope
* Cross-module database access
  * Data "ownership" and read vs write access
* Ensuring data integrity
* Example use cases
* Practical considerations for how this will work
* Backward compatibility with Folio instances not adopting Applications
* Access to any databases outside of the bounded context / Application is ***out of scope***

## Timing
* Fourth in the sequence.
* RFC Submission: ~Early-Mid January '24
