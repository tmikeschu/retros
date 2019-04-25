# Reserved Slot Names (improvement)

4/18/19 - 4/24/19

## Original Task Description

```
[bug] creating a slot titled `name` doesn't work and doesn't throw an error

"number" too.
```

## Motivation

The goal was to essentially update the client side to give feedback to users
when they attempt to add a reserved slot name when creating or editing a
competency.

## Overall Summary

* Add two identical lists of reserved slot names for python and javascript
  stacks

* Restore view edit modal selenium test

* Add thorough selenium test for reserved slot names

* Update reserved slot name API tests

* Add/update client side validations to create and edit forms

  * Create composable validation mixins

* Add new UI to show entire list of reserved slot names

* Update cl-dialogs to use cl-dialog-content instead of `slot="content"`

## Process Notes

After grepping through the codebase, three files defined reserved slot names:

* web/adminconsole/src/static/js/components/CompetencyEditDialog/CompetencyEditDialog.vue

* clincapi/finie/competency_creation/views.py

* bankutils/observables/utilities/pipeline.py

  * This file had two different lists of predefined/hard-coded slot names.

Each of the lists differed in length and content.

The original goal was to create one list that could be used by both the server
and client. Using that list, we could coordinate three related bits of logic:

* Client side validations before requests are made

* Additional validations on the API endpoints

* Ensuring future additions to reserved slot names are added to the single
  source of truth list (i.e., `pipeline.py`).

The create form originally did not have any client side validations, but the
edit form did. Since creating and editing forms are very similar, I took steps
to share validation logic and testing identifiers (`T_`) to make sure these two
features have the same behavior.

## Overall 📈

* Efficient UI development using clui. Ben was super helpful to make quick
  updates.

* Created a very thorough selenium test in a relatively declarative way.

* Extracted shared logic for create and edit dialogs.

* Added more convenience methods to the base web tests page.

## Overall 📉

* No design specs.

* Not stoked about having to use two lists.

* Testing feedback loop took forever.

* I discussed how to achieve a single list with Ben and Logan. I originally
  thought of storing the list in the back-end and using an API endpoint that the
  client could hit (but I wasn't crazy about the idea). After hashing it out
  with them, we landed on using an env variable, which seemed elegant to me.

  I built out the first iteration that way and save for a Vue process.env
  hiccup, it felt pretty sound and clean. Unfortunately, the selenium test
  passed every time locally but would fail in the pipeline tests. In addition to
  that, there was some push back on using and environment variable for something
  that wouldn't vary between environments, so I ended up going the less DRY path
  (but easier to test) and implemented two identical JSON lists.

* Barclays pipeline still fails for some reason. Very frustrating to always have
  Barclays be an issue.

* process.env.VUE_APP_RESERVED_SLOT_NAMES would be blank when trying to examine
  process.env in a debugger repl, but would compile in the bundle. Spent a lot
  of time thinking I didn't have the connections right when I actually did.

* Spent a lot of time trying to read files outside the project directory using
  node/fs to no avail.

* Storybook development wasn't working in the platform

## Overall 🔺

* Communicate with infra and/or back-end people to validate ideas that involve
  env variables

* Test process.env type stuff via DOM content instead of an interactive debugger

* Don't go down a rabbit hole of trying to use file system reading between
  separate repos. Treat it more like microservices that would have to talk
  through an API and be loosely coupled.

* Add a test to validate that the two files are the same.

* Design create and edit forms to be more dynamic based composed inputs.

* See if there is a way to run a specific test in isolation in the pipelines?

* Have a cleanup MR that deletes all the unused jquery logic. There is a lot of
  false positive grep results for legacy markup.

* Add pylint-quotes to linter to avoid useless quote comments on MRs.

* Fix storybook development
