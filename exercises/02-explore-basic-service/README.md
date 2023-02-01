# Exercise 02 - Explore the basic service

In this CodeJam repository there's a basic "Incidents Management" service that's supplied, that will be the starter for your explorations. It's in the [incidents/](../../incidents/) directory. At the end of this exercise, you'll feel comfortable with what that service offers, and have explored its components.

> All activities in this and subsequent exercises, unless otherwise stated, will be in the context of this `incidents/` directory.

## Take a quick tour of the service

The service deals with incidents, that are raised and discussed, and that are ultimately resolved by service worker personnel attending appointments to address and repair whatever is needed.

As a classic CAP based service, the core components are to be found within the lower two of the three normal organizational layers, which are presented here in an order that represents how one would normally view the layers of an application in general:

|Organizational Layer|Directory|Content|
|-|-|-|
|Application|`app/`|Very little, as this is a "headless" service, although some annotations are provided at this layer (in [fiori.cds](../../incidents/app/fiori.cds)) to augment the SAP Fiori elements frontend pages which CAP provides out of the box).|
|Service|`srv/`|The single service, defined in [incidents-service.cds](../../incidents/srv/incidents-service.cds), exposing entities from the persistence layer below, in a lightweight and fairly "transparent" way.|
|Persistence|`db/`|The actual entity definitions complete with property details, relationships, supporting artifacts such as enumerations, and even some basic annotations, defined in [schema.cds](../../incidents/db/schema.cds) (there are CSV files providing some sample data for each entity at this layer too).|

👉 Take a few moments to look through these directories and become familiar with their contents.

While it's important to feel comfortable reading and writing declaratively in [CDS's definition language](https://cap.cloud.sap/docs/cds/cdl) (CDL), we can use an editor extension to view the same information graphically. 

### Examine the schema at the persistence layer

👉 In your workspace, find the `db/schema.cds` file and use the context menu item "Open With...", selecting the "CDS Graphical Modeler" item in the selection presented to you:

![Opening the schema.cds file with the CDS Graphical Modeler](assets/schema-open-with.png)

You will be presented with a graphical model representing the details in `db/schema.cds`, looking something like this:

![The schema presented graphically](assets/graphical-schema.png)

👉 Take a few moments to [stare](https://qmacro.org/blog/posts/2017/02/19/the-beauty-of-recursion-and-list-machinery/#initialrecognition) at this to see what reveals itself. Here are few pointers to what's available here:

* in the right hand column the major components are listed, including entities and enumerations
* in the main graphical display there are entities, such as `Incidents` and `ServiceWorkers` (note the icon denoting entities)
* the `Incidents` entity is further decorated with the `cuid` and `managed` aspects
* there are relationships shown between the entities, such as the one-to-many relationship between `ServiceWorkers` and `Appointments`
* the enumerations (enums) are used to provide possible values for certain properties (the `urgency` property of the `Incidents` entity in this example is linked to the `Incidents_urgency` enumeration)
* the main namespace is `acme.incmgt` but there are also namespaces from the imported items (via the CDS `using` statement at the beginning of `db/schema.cds`)

    ![The selectable main and imported namespaces](assets/namespaces.png)

👉 Try to relate what you have discovered in this graphical display to the definitions in the `db/schema.cds` file.

### Examine the service definition at the service layer

Now it's time to move up a layer, from persistence to service.

👉 Find the `srv/incidents.cds` file, and open it with the CDS Graphical Modeler. Like before, take a few moments to stare at the graphical representation of the service layer; here are some pointers to what you should notice:

* this time the main namespace is `IncidentsService` and there are imported namespaces too
* in contrast to the entities shown in the graphical display of `db/schema.cds` we now see projections
* there isn't a one-to-one mapping of projections to entities
* but where there is a mapping, there's a one-to-one property correlation (i.e. all the properties at the persistence layer are available at this service layer)

### Run the service

Now you feel more familiar with the baseline service for this CodeJam, why not start it up? 

👉 Make sure you're in the `incidents/` directory and start things up like this:

```bash
cds watch
```

👉 Observe how the service starts up, and examine the messages emitted. They should be similar to this:

```text
cds serve all --with-mocks --in-memory? 
watching: cds,csn,csv,ts,mjs,cjs,js,json,properties,edmx,xml,env,css,gif,html,jpg,png,svg... 
live reload enabled for browsers 

        ___________________________

 
[cds] - loaded model from 4 file(s):

  db/schema.cds
  srv/incidents-service.cds
  app/fiori.cds
  ../../../usr/local/share/npm-global/lib/node_modules/@sap/cds-dk/node_modules/@sap/cds/common.cds

[cds] - connect using bindings from: { registry: '~/.cds-services.json' }
[cds] - connect to db > sqlite { url: ':memory:' }
 > init from db/data/acme.incmgt-Appointments.csv
 > init from db/data/acme.incmgt-Incidents.conversation.csv
 > init from db/data/acme.incmgt-Incidents.csv
 > init from db/data/acme.incmgt-Incidents_status.csv
 > init from db/data/acme.incmgt-Incidents_urgency.csv
 > init from db/data/acme.incmgt-Incidents_urgency.texts.csv
 > init from db/data/acme.incmgt-ServiceWorkers.csv
 > init from db/data/acme.incmgt-TeamCalendar.csv
/> successfully deployed to sqlite in-memory db

[cds] - serving IncidentsService { path: '/incidents', impl: 'srv/incidents-service.js' }

[cds] - server listening on { url: 'http://localhost:4004' }
[cds] - launched at 1/27/2023, 1:47:49 PM, version: 6.4.1, in: 1.355s
[cds] - [ terminate with ^C ]
```

You should see a message appear in your workspace to the effect that port 4004 is open and available; here's an example from VS Code:

![port 4004 available in VS Code](assets/port-available-vscode.png)

👉 Open <http://localhost:4004> in your browser, and examine the resources available. You should see something similar to this:

![Basic service](assets/basic-service.png)

## Summary

At this point you've examined and started up the base CAP service that you'll be working with and extending in this CodeJam. If you were to sketch a simple entity relationship diagram it would look something like this:

```text
┌────────────────┐       ┌────────────────┐
│                │*     1│                │
│ Conversations  ├───────┤   Incidents    │
│                │       │                │
└────────────────┘       └───────┬────────┘
                                 │
                                 │
                                1│
                         ┌───────┴────────┐
                         │                │
                         │  Appointments  │
                         │                │
                         └───────┬────────┘
                                *│
                                 │
                                1│
                         ┌───────┴────────┐
                         │                │
                         │ ServiceWorkers │
                         │                │
                         └────────────────┘
```

## Further reading

* [Domain Modeling with CDS](https://cap.cloud.sap/docs/guides/domain-models)
* [An introduction to CDS Graphical Modeler for SAP Business Application Studio](https://blogs.sap.com/2021/04/23/an-introduction-to-cds-graphical-modeler-for-sap-business-application-studio/)

---

## Questions

If you finish earlier than your fellow participants, you might like to ponder these questions. There isn't always a single correct answer and there are no prizes - they're just to give you something else to think about.

1. While there are two imported namespaces shown when examining the `db/schema.cds` contents (`global` and `sap.common`), there are three shown when examining the `srv/incidents-service.cds` contents. What is the third, and where does that come from?

1. In looking at the graphical display of the `srv/incidents-service.cds` contents, one of the entities (from the `db/schema.cds` layer) wasn't shown. Which one, and why?

1. There's a lot to unpack from the initial output of `cds watch`. What does the output tell you?

1. `cds watch` is actually just a shortcut for another `cds` command. What is it?

1. Are the names of the enumerations that you saw in the graphical display of `db/schema.cds` actually specified anywhere explicitly? 

1. There are four types of resources available at <http://localhost:4004>. What are they?

---

[Next exercise](../03-import-odata-api/)