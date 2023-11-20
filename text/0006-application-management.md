* Start Date: 11/13/2023
* Contributors:
  * [Craig McNally](cmcnally@ebsco.com)
  * [Vince Bareau](vbareau@ebsco.com)
* RFC PRs:
  * PRELIMINARY REVIEW: https://github.com/folio-org/rfcs/pull/18
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
* How this is a step towards Application Stores & Marketplaces
* How movement of modules between Applications will work
* Module deployment
* Backward compatibility with Folio instances not adopting Applications

## Terminology
* Application entitlement - refers to the set of applications enabled for a tenant.  If app-foo and app-bar are enabled for tenant diku, then it could be stated that: the diku tenant is entitled to app-foo and app-bar, or diku's application entitlements include app-foo and app-bar.  By extension, the diku tenant would be entitled to the modules which comprise both app-foo and app-bar.

## Timing
* Second in the sequence.
* RFC Submission: ~Late November '23
