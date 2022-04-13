
- Start Date: 03-29-2022
- RFC PR: 
- FOLIO Issue: 

# Localizing API (Backend) Messages

## Summary
  The purpose of this RFC is to define an approach that allows API developer to provide
messages in the API response depending on the language preference set in the request. 


## Motivation

- Help users understand the information coming from the application backend using a language they understand better
- To allow FOLIO platform to be used by users across the world 
- To create a tremendous opportunity for growth that we can never could have achieved within just one country.

## Detailed Explanation/Design

### Terminology
- Static Message - E.g. _This is something you need translate_
- Static Message with placeholder - _You can ONLY renew {n} times_
 

### In Scope Requirements/Use cases
- Return localized messages based on the value passed in the accept-language header
- Handle static messages and static messages with placeholders
- Message categories
  - application
  - validation
  - informational
  - system error
- Define naming convention to be used for keys
- [Plural Syntax](https://wiki.folio.org/display/I18N/How+To+translate+FOLIO#HowTotranslateFOLIO-Pluralsyntax)
- Runtime values that are to be localized (E.g. Patron Groups) 

### Out of Scope Requirements/Use cases
- Process for managing (decoupled from lokalise.com) translations
- Returning formatted (HTML/Markdown) messages
- Usage of [soft hyphen](https://wiki.folio.org/display/I18N/How+To+translate+FOLIO#HowTotranslateFOLIO-Softhyphentobreakwords)to break messages
- Support customization per tenant
- Process for backporting API/Backend messages similar to what we have for the front end 
- Support for controlled vocabulary (runtime data/)


### Design Notes
- STOP using the lang parameter.
- accept-language header value MUST be in ll_CC format. , where ll is a two-letter language code, 
  and CC is a two-letter country code.

## Risks and Drawbacks

Why should we not do this? 

A genuine and thoughtful consideration to risks and drawbacks is essential for a well-rounded proposal. 

## Rationale and Alternatives

Following options were considered. All options except Option 4 involves stripes in some manner.
Coupling the API backend to a specific front end framework severely limits the usage of FOLIO API to
only clients that are built using Stripes. 

### Option 1
Distribute them over the existing front-end modules:

ui-checkin with translation key ui-checkin.mod-circulation.itemNotFound
ui-requests with translation key ui-requests.mod-circulation.patronHasItemOnLoad

*Pros*
- Uses existing front-end modules

*Cons*
- Cannot disable a front-end module for a tenant if it hosts a mod-circulation translation key that is needed by another enabled front-end module.

### Option 2a
For each back-end module with translation keys create a dedicated front-end module with language files:

For example create ui-mod-circulation module, and use translation keys

ui-mod-circulation.itemNotFound
ui-mod-circulation.patronHasItemOnLoad

*Pros*
- Any front-end module that uses mod-circulation can require ui-mod-circulation to load the translation files.
- All mod-circulation translation strings go into a single module.
*Cons*


### Option 2b
For each back-end interface with translation keys create a dedicated front-end module with language files:

For example create i18n-circulation module, and use translation keys

i18n-circulation.itemNotFound
i18n-circulation.patronHasItemOnLoad

*Pros*
- Any front-end module that uses the circulation API can require i18n-circulation to load the translation files.
- All circulation translation strings go into a single module.
*Cons*

### Option 3
The back-end module hosts the language files.

mod-circulation creates a translations/mod-circulation/ directory.

Stripes fetches the translation files from the back-end module and can process them the same way as translation files from frone-end modules.

*Pros*
- Software and language files are in the same repository. Options 1 and 2 require to add the message string to some other repository using a second pull request.

*Cons*
- Is this technically feasible? How can Stripes fetch languages files from a non-Stripes module?

### Option 4:
When calling the back-end pass the required locale. The back-end maintains the translations files, replaces the placeholders and puts the final string into the "message" property. A lang query parameter that many FOLIO API interfaces already have or an "accept-language" header line might be used.

*Pros*
- Java code and translation files live in the same repository. No work for the front-end.

*Cons*

## Unresolved Questions