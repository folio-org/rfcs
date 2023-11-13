* Start Date: 11/13/2023
* Contributors:
  * [Craig McNally](cmcnally@ebsco.com)
  * [Vince Bareau](vbareau@ebsco.com)
* RFC PRs:
  * DRAFT REVIEW: TBD
  * PRELIMINARY REVIEW: TBD
  * PUBLIC REVIEW: TBD
  * FINAL REVIEW: TBD
* Outcome: 

# Bounded contexts

## Summary
It is proposed that the boundary of the microservices, and thus the bounded context, be extended beyond the module. The new bounded context would be found at the Application level. Thus, any of the backend modules in the Application would be able to directly access the storage layer.

## Scope
* Cross-module database access 
* Ensuring data integrity
* Example use cases
* Practical considerations for how this will work

## Timing
* Fourth in the sequence.
* RFC Submission: ~Early-Mid January '24
