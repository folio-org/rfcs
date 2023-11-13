* Start Date: 11/13/2023
* Contributors:
  * [Craig McNally](cmcnally@ebsco.com)
  * [Vince Bareau](vbareau@ebsco.com)
* RFC PRs:
  * PRELIMINARY REVIEW: https://github.com/folio-org/rfcs/pull/15
  * DRAFT REFINEMENT: TBD
  * PUBLIC REVIEW: TBD
  * FINAL REVIEW: TBD
* Outcome: 

# Application Management

## Summary
The introduction of formalized Applications introduces the need to manage such applications in a reliable fashion.  This would also be the first step in being able to move towards a comprehensive App Store or Marketplace vision. Management starts at the application-level.  These manager components will provide APIs working at the application-level, while consuming the lower level Okapi APIs for module-level, tenant, and tenant entitlement operations.

## Scope
* Application registration
* Tenant creation and management
* Application/Tenant entitlement
* Application Stores & Marketplaces
* How movement of modules between Applications will work

## Timing
* Second in the sequence.
* RFC Submission: ~Late November '23