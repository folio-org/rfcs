
- Start Date: 2019-06-20
- RFC PR: 
- FOLIO Issue: 

# PubSub support in FOLIO (module implementation)

## Summary
This RFC proposes to add event-driven architecture capabilities to the FOLIO platform.

## Motivation

The Folio Platform is organized as a set of microservices which are organized around a central proxying API Gateway. Folio modules implement these microservices through RESTful API interfaces. In order to implement complex library workflows it is necessary to distribute the work to individual modules to perform the specialized tasks.

The approach described here is to provide a messaging system whereby modules publish relevant data when ready, for other modules to consume and do their part. Modules which participate in a particular workflow can subscribe to the relevant messages. The advantage of this particular solution is that it fits well with the microservices architecture and preserves the independence of the modules. Each module plays its part but remains unaware of any role played by other modules.

## Detailed Explanation/Design

### Requirements
#### Functional Requirements
- A business module (back-end) can define a set of events it can send or receive.

- An “Event Descriptor” must be provided for each kind of events.
  1. The event is defined by the “Event Type”
  2. The “Event Descriptor” must have a detailed description for the event type and payloads which can be received within events of this type.
  3. “Event Descriptor” does not imply explicit tracking of event type versions. To do this a new “Event Descriptor” must be declared with a suffix in the “Event Type” that explains the difference between the two “Event Descriptors”. The description of the new “Event Descriptor” must reflect this change as well.
  4. The “Event Descriptor” can define a default TTL for events of this type.
  5. The “Event Descriptor” can optionally define data structure definitions or schemas for an event payload.
  6. The “Event Descriptor” can define events as “Signed” of “Unsigned” (not in scope of MVP, but the basis for that must be provided). “Signed” means that all events of this event type must be digitally signed by the publisher.

- Each concrete event must have at least a UUID. A payload of the event is optional and can be of any type of serializable data and it is up to a publisher and a consumer to agree on the data structures they want to exchange. If an event descriptor contains data structure definitions or schemas, they can be used for validation, marshalling/unmarshalling.

- It should be possible to notify a publisher that there are no subscribers for an event type it is trying to send. It could be included in a response to an event publishing call.

- Time to live (TTL) must be set up for each event sent. It optionally allows a publisher to provide a callback endpoint or an error “Event Type” to be notified that despite the fact that there are subscribers for such an event type no one has received the event within the specified period of time.

- Cross tenant events exchange is assumed in the future, but not in the scope of MVP (v1).

- Integration with external/third party systems should be allowed (not in scope of MVP, but the basis for that must be provided)

#### Non Functional Requirements
- Direct access from backend modules to the underlying messaging engine (KAFKA, RabbitMQ, etc.)

### Target Solution Architecture

#### Solution Overview
- A new module is introduced, mod-pub-sub (name tbd). It is responsible for maintaining the registrations of event types and for coordinating distribution of events to the appropriate subscribers. Folio modules may publish events and/or subscribe to events of specific event types. It is the responsibility of publisher modules to define the event types that they will provide, which is accomplished through the use of an event descriptor. Subscribing modules will register themselves to specific event types, by providing a URI that will be invoked by the event manager when pushing events of matching event types.  Event subscription is done on a per tenant basis.

- The pub-sub module is also responsible for maintaining a trail of activities: event publication; subscription distribution; errors; non-delivery; etc. By using the correlationID embedded in events it is possible to reconstruct the sequence of operations that form a particular workflow.

- By configuring specific event subscriptions for each tenant it is possible to implement specific cases of inter-app coordination.

- The pub-sub module is implemented using a message queue (Kafka) which will provide the necessary persistence.

#### High-level solution structure

There are three global interfaces defined in the scope of this solution:
- Event Manager
  - Defines the API for event type registration by publishers and subscribers. This API must be called by back-end modules.
  - Defines the API used by publishers to send events. This API must be called by back-end modules which act as publishers
  - API to retrieve existing registered event types.
  - API to retrieve the registered subscriptions for a specified tenant and event type(s)
  - API to retrieve the activity history performed by the event manager. A correlationID can optionally be provided to limit context.

- Event Publisher
  - Provides general implementation that allows a back-end module to send an event.

- Event Subscriber
  - Defines the API used by “Event Manager” to deliver events. This API must be called by an implementation of the “Event Manager” to deliver events to which this particular consumer is subscribed.
  - Since OKAPI provides a feature like “Multiple” interfaces such an interface can be defined to consume events. Also a default implementation for this interface can be provided to simplify subscribers’ development. This interface defines a set of requirements that must be followed in case a module needs to provide its own “Subscriber endpoints” and implementation. The basic set of requirements:
    - HTTP Method - POST
    - A set of required HTTP header. `To be defined further if it is needed.`
    - A payload structure - JSON which represents an event. See [Event data structure](#####Event data structure) 
  
The “Event Publisher” & “Event Subscriber” also provide default implementations that allow a back-end module to be registered as a publisher or a subscriber respectively.

A dedicated module (TODO: module-name - mod-pub-sub) must implement and encapsulate all logic related to event sending and delivery - “Event Manager”. All interactions between this module and back-end business modules will be done via HTTP. The HTTP method for event sending and delivery is limited to POST.

#### Registration

Each module which acts as a publisher or a subscriber must contain a configuration or property with a set of event types it deals with. Publishers should provide event descriptors. For subscribers, it is enough to specify event types together with endpoints for event delivery. Details of that configuration must be defined by the interfaces “Event Publisher” & “Event Subscriber” respectively.

##### Publisher registration
The set of event descriptors will be registered by a publisher at the time when the module is enabled for a tenant. To do that the publisher must call “Event Manager” API for registration. This API call should be determined by the “Event Publisher” interface. The payload for that call is a set of event descriptors which define events this particular module is able to publish.

At this stage it is possible to check if another module has already been registered as a publisher for the same event descriptors.

It is worth mentioning that at this stage only event descriptors will be registered. To allow a module to publish events further steps optionally should be taken. See [Activation / deactivation](#Activation-/-deactivation)

##### Subscriber registration
A back-end module can register event types it can consume at the time when the module is enabled for a tenant. To do that it must call the “Event Manager” API for registration. The payload for that call is a set of event types augmented with endpoints provided by this module. These endpoints will be used to deliver events of respective types. In most cases, a default implementation of the “multiple” interface must be sufficient. But it is allowed for the module to provide its own endpoints in case the default implementation can’t be reused. To allow a module to receive events of registered types further steps optionally should be taken. See [Activation / deactivation](#Activation-/-deactivation)

##### Activation / deactivation
When a module is up and running and is enabled for a tenant, a tenant administrator can define types of events which must (or must not) be published and received in scope of this tenant (subscribe a tenant to event types) using either UI Admin console/Settings Page or HTTP requests to Event Manager’s endpoint for bulk operations. 

It is suggested that by default all event types are activated for a tenant.

##### Cancellation
When a module is disabled for a tenant whether it is a publisher or a subscriber the module must unregister event types it deals with calling API provided by Event Manager.

##### Event Manager responsibilities
The Event Manager must keep track of registered event types, publishers and subscribers which are able to send and receive events of those types.


#### Data structures
##### Event descriptor data structure
Name | Type
--- | ---
Event type | String
Description | String
Default TTL | Period (1 minute, 1 hour, 1 day, 1 week, etc.)
Signed event | Boolean (true/false)

##### Event data structure
Name | Type
--- | ---
Event ID | UUID 
Event type | String
Metadata | A set of named attributes <table><tr><td>Tenant ID</td><td>String</td><td>Required</td></tr><tr><td>Event TTL</td><td>Period</td><td>Required</td></tr><tr><td>CorrelationID (could be used to track related events)</td><td>UUID</td><td>Optional</td></tr><tr><td>Original event ID</td><td>UUID</td><td>Optional</td></tr></table> 
Event payload | Arbitrary JSON (String representation)

#### Event publishing
There is a high-level sequence diagram below which represent the event publishing flow.

![Event publishing Image](http://www.plantuml.com/plantuml/png/ZPHTRzem58Rl_IkEu88jWehs4dL8MqkdITrMhT5kA1ScEG6lnixy0KBYn_Su2Kdu61rI59kyvpd7xx7jX9C8apKg1xcVIs6NGYqWDpf1Qsd86FTEAx-Qeu7ExNmy7Gw7imvZEJTE92Bd5DbPwNHmMyMZ6NU0MyWPIxIKc3YXbIqr91bOFo--OugCzKunzDqcHb2-mNL9ijVlw6ugtGqZ8gU4Q-uGSkZUS_FwpELAAoeO1cF8H_1aa608N06Mw-PRgNbQ2-xqAjUs9T3pFxGAnXP6-_oYMK0_maoxPDBmuM56G5hc2A9eElaz-H7FecX4uoWg8F4snpvyHyTJxdWVdif2eeXa1OfYOO-uMsBo2Y83jnGMQg-19WONmZeqcBuehb9IO5Mvemn1iVSaXKmRuE2GXAx8mQM3c08EY4gx5hNGrVLKBhuZHqaEnYU516LTp3dkvD_oqzf3aaF8WLb5wfw5E7xSmwXNQv7kZR98U4Wnupwu46GPjNrxTHRwv5tBMg6S3jCNvLMAHcwhhH3mfXUQOhKt2S87ftEHzE6lHq_jHfTSVRnGT5XauoM4CXi7cZrlQqJLmB2ZwL7whAZEDUt-D5mjS5RQtNHeIGTeZgZ859fjX5l2XXj5gx8jEq9Ndnj-aJBJvC2vJ5yDyIudQg4hHdY7X_LIR8uoOn8NUydk4pmAXU4TZX3iod_X5pdqsHYzeV4tHVufBY7XKjfQSgzTyF6Bz5XBgwrSbHVqn_8sU4Blq26Rqws6G9kWEJb7OQNJJUJnnP5ebGcpuk62F7Jyc5Gu-P-WWyakU0cLTsy0)

#### Event delivery
There is a high-level sequence diagram below which represent the event delivery flow.

![Event delivery Image](http://www.plantuml.com/plantuml/png/jLPDJnin4BtxLup2WJOY4LmHHOgWQYiX3YKtmi5PJrWBPxorlQ4hzSTtl5_PP4a8bEQoc_NupRmtuna77Gp2rnLI08L6hz0La3cReFUQ7eMGUR2KB-VeO70nlxm_FJrz60Xnd4aNTA8poHi7Iwj65ra-Dzgdw2u783raGYJM64o2jkGh6pOP-NnUhgD28VeelEmJfzdVQ581-REzPkFnkBPJ--5kQpDwoMq_U44s85WMf0mLO3YS60DhG0yE4sJ50ADyZAoNkGcIqERU5CEXG58GKOHuT3RFQfaxwWB_61n6b_5n3ZynECre8Aa2XOMpImWfGauAdIburumOOQNvxt4CmTlS9GYVWirqSWA_Sipn130auyypcXXl8R6vKJ17M6Y7_59i0KA3ruld3cF-G6xodj5pgfCKfED0ERqh6HXsOHr824WigYYHf0amPKcnj2JRnIWDjjrTfPWyLSIPsdHwqrf5g4ezxQEaWB-pIAOrw2lfanHLRkhijCNDmUl0iSSFibY7OJokFRo4NgLmuWjADhwtdjbrTWwtDkX5mVXjHK4USrscr3aMAM4vvvA9rhAhyIOBsitgclF1HHdJLx8IsvjA6clDkyMw8rERzAeQZn5w27PpF-Dbw7ZpaZ5wjTuDcOzaZu8TtHzzSuZqm6JyMSab9cHD9ZgK7PSR44tql1MEhgLxkfDcQMd3XfUwJah5fYrwoiTwpalDgJqykn6tYlcx96Kw-dEhu0sfirJAi3UF2ZiStYYNUhzSQhTSwZ_9fQ2E-QFaKdlbUbq-Uhzyj5i-WXfxcsRFx0qgSvjGpOJ0tOgGsALRLur-RtrECSIM90COb3w0fMwNc54Cg8sbpwXkwGVLo66DgsvO7SJOtWz3tJql3fuP_pet3fiThnsPZvuXBnN6mIdPtXpVHVDI_Z7us1Zy-G8tcck8dzCYusVrbNLKCE5Gd_dz1mLq99kvqfXSsn2dKTVogg5L7iur-pYAucMgvR-54OzN2x9rcOq7QbtXV1aYlWOjPRoLCan4hUc8lm00)

#### Security
Calls to the “Event Manager” can be done either on behalf of a user or a backend module if this is a result of the processing of another event. While all calls from the “Event Manager” to subscribers will be done on behalf of the “Event Manager”. For these cases we can’t use or apply permissions granted to a user, because such calls can be treated as “System” or “Internal”. Since FOLIO Platform currently does not provide such type of users (a System/Internal level user) it makes sense to create an ordinary user that will be assigned to the “Event Manager” to perform all actions on behalf of that. Also, it means that all data required by OKAPI to be provided in the HTTP headers must be transferred as attributes in the metadata section of an event payload (X-Okapi-Tenant, etc.). The drawback of this approach is that the dedicated user can be accidentally deleted by a FOLIO administrator. Also, it is allowed to login from UI using that user’s credentials. The pub-sub module should be able to obtain a JWT token from mod-authtoken on behalf of that “technical” user as well.

A special set of FOLIO permissions should be defined to allow to publish and deliver events.

Permission name | Description
--- | ---
event.publish.post | This permission is required by the “Event Manager” in order to publish an event. So each backend module which acts as a publisher must have this as a module level permission. 
event.deliver.post | This permission must be granted to the “Event Manager” by default as a module level permission. So backend modules can protects its “Delivery endpoints” with this permission.
event.callback.post | This permission must be granted to the “Event Manager” by default as a module level permission. Backend modules can protect its callback endpoints used for notifications with this permission.

#### Implementation notes
The “Event Manager” implementation (mod-pub-sub) must have its own persistent storage to store all registered event descriptors with publishers and subscribers linked to them.

A dedicated git repository can be used to store event descriptors and it must be added as a git submodule to a back-end module’s repositories. It will allow to manage event descriptors easily.

## Risks and Drawbacks
This RFC introduces the new functionality to the FOLIO platform, that leads to the increasing platform complexity.

Also, it adds a dependency on the third-party platform (KAFKA). But having that behind the mod-pub-sub allows substituting messaging vendor with a minimal impact on the FOLIO platform.

Some HTTP calls overhead will be present because all interactions between mod-pub-sub, publishers and subscribers will be done via the HTTP protocol through the OKAPI module. 

## Rationale and Alternatives
It is possible to implement "Event Manager" functionality in the scope of OKAPI, but it this will unnecessarily increase the complexity of the OKAPI module, which is currently quite complex. 

As an alternative, it is possible to use a messaging platform (KAFKA, RabbitMQ) directly without a proxy module. But it will add an explicit dependency on the selected messaging engine. 
Also, it forces to add some boilerplate code snippets to all modules which need to deal with publish/subscribe functionality, or at least it will require to create some sort of shared library to be used by backend modules.
But since FOLIO is a language agnostic platform it will also have its limitations.
  

## Unresolved Questions

Now the least developed area is questions related to the security, permissions, receiving JWT tokens, etc. 