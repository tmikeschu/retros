# Overfit CLF Data by State (feature)

4/2/19 - 4/17/19 (sprint + 3 days)

## Original Task Description

```
aiutils.queryclassifier.identify_key_data_features.py

Mocks coming from Marissa Henri eta Thursday morning.

https://gitlab.clinc.ai/clinc/lucida-clinc/merge_requests/5195<Paste>
```

(rather terse)

## Overall Summary

Expose an overfit analysis function in the platform for eligible states.

This ended up requiring three distinct steps

1. Extend the command line utility script to expose an input-driven interface

2. Expose and API endpoint that the client can reach to analyse CLF data 

3. Add a "more" menu to state listings and a modal to display overfit results.

## Overal 📈

* Satisfying to stitch together AI utils, django back-end, and Vue front-end

* Great design spec from Marissa

* Andrew and Jonathan were very helpful

## Overall 📉

* Requirements and timeline could have been scoped better
	* Hopefully this retro will help with future tasks

* Super slow feedback loop with api and selenium tests

* Unclear what the ideal candidate for analysis was. A state? What kind of
state? How many transitions? How many intents?

* Last minute ask for grouped intents (e.g., `get_balance_start&get_balance_update`)

* I did not complete the task by the end of the sprint

## Overall 🔺

* Encourage AI devs to build out http-friendly interfaces along with any command line utils

* Implement DB factory pattern for more declarative testing
	* I should be able to do something like

```py
Factory.create_state_graph()
Factory.create_intent_with_utterances()
```

* Start implementing a GraphQL endpoint

* Update CLUI bugs

* Have AI team identify the specific use cases that make sense

## Topic Breakdown

### aiutils/

#### Summary

* Added `api()` interface to `identify_key_data_features.py`
	* takes CLF data and options as inputs

* Modified the `main` interface to reuse logic between it and `api`

* Fixed a bug involving integer-indexed `labels`

#### 📈

* Relatively easy to understand and extend the code

#### 📉

* Inconsistent code standards throughout. For example, lots of single letter
variable names, which I am down with, but when I continued this pattern in
places I was asked to change it in the review.

* Naming was not consistent for options. E.g., `other_intents_cutoff` -> `max_where`

#### 🔺

* If exposing these tools in the platform is likely, it would be great for
future scripts to be written for both command line and HTTP interfaces.

* Push for unit tests on the core logic

### clincapi/

#### Summary

* Added `GET /versions/:version_id/states/:state_id/overfit-clf` endpoint
* Added request specs for different scenarios
* Added SOCKS config for insomnia testing

#### 📈

* Lots of existing endpoints to glean a pattern from

* HTTP unit test interface already configured

#### 📉

* Slow feedback loop on even a single unit test

* A lot of manual DB object creation to get the context set up

* A lot of work to get to a reasonable "state graph with competencies,
transitions, and utterance data" state

* Hot reloading w/ wsgi didn't seem to work very well (or at all)

* No formal documentation on creating a new endpoint

* Originally exposed the endpoint as a `POST` listener, where CLF data was
passed in as a request body. This was easier to test the endpoint, as it was
loosely coupled from the database. As we discovered performance issues for big
CLF payloads, I switched it to be a GET resource, which ultimately made more
semantic sense semantically. This made it simpler for the client, but harder to
test.

#### 🔺

* Configure some kind of DB factory testing utility such as:
  https://github.com/FactoryBoy/factory_boy

* Create a schema diagram for the state graph data to point out all the objects
required for typical state graph data

* Create documentation for creating and testing a new endpoint

* Improve testing standards (more than just asserting http status codes)

### web/adminconsole/

#### Summary

* Add node endpoint for overfit CLF data

* Add DataInsights component

* Add unit tests for core logic

* Add more menu to state listing in DetailsSidebar

* Add selenium test for viewing data insights

* Make insights disabled ineligible states

#### 📈

* Storybook style development was fast and efficient

* Existing vuex patterns were easy to extend

#### 📉

* The clinc_api.js file seems like it could be abstracted. With over 2k lines I
think it's safe to say we've settled on a pattern

* CLUI tooltip on a disabled cl-menu-item button does not work

* Earlier design feedback from stakeholders

* Sorting ended up getting thrown out

#### 🔺

* GraphQL implementation in the Django server
	* https://github.com/graphql-python/graphene-django

* Make cl-dialog header a slot to account for subtext

* Enable a common pattern for disabled menu buttons in clui

* Create common abstraction for setting UI state by object ID 
	* where the ids are keys to useful UI data such as functions or booleans to
represent state (e.g., open/closed)
