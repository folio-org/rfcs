* Start Date: 11/13/2023
* Contributors:
  * [Craig McNally](cmcnally@ebsco.com)
  * [Vince Bareau](vbareau@ebsco.com)
* RFC PRs:
  * PRELIMINARY REVIEW: https://github.com/folio-org/rfcs/pull/19
  * DRAFT REFINEMENT: TBD
  * PUBLIC REVIEW: TBD
  * FINAL REVIEW: TBD
* Outcome: 

# Application-specific UI bundles

## Summary
The current Folio (flower) release consists of a monolithic, ever increasing, set of modules. Furthermore, there is a high level of separation between the frontend and backend modules. Backend modules will tend to be deployed as a set into each multi-tenant Folio installation, whereupon individual tenants may have only a subset of the modules enabled for them. Frontend modules are aggregated together into a single bundle to be delivered to the user’s browser. These bundles are specific to each tenant, consisting of only those UI modules that they are configured for. These are thus monolithic and tenant-specific.

In order to fulfill the vision of Applications, it is necessary to better align the related frontend and backend modules. Bundling the frontend and backend components together into an Application package will help allow the applications to enjoy individual development and release lifecycles. It is proposed that the bundling of UI modules be changed from tenant-specific bundles to application-specific bundles. Rather than receiving a single large bundle containing all the front-end modules that they are entitled to, a user will receive a number of smaller, shared bundles: one for each application they are entitled to.

## Scope
* Build-time considerations
* Inter-module linking
* Static asset loading
* Initial loading of the slim tenant bundle
* Entitlement change detection and dynamic loading/unloading of application bundles
* Impact on/adjustments to user experience (UX)
* Backward compatibility with Folio instances not adopting Applications

## Timing
* Third in the sequence.
* RFC Submission: ~Mid-Late December '23
