# Creating PRs on the NVDA project

This page is meant to serve as an explanation for how to fill out
[our Github pull request template](https://github.com/nvaccess/nvda/blob/master/.github/PULL_REQUEST_TEMPLATE.md)

## The title

Please use a descriptive title of the changes.
Avoid relying on references to issues or PRs, ensure the title is self-descriptive.
To automatically generate a title, set the title to `@coderabbitai title`.

## The template

At the start of the template, there is an HTML comment block (starting with `<!--`), which points to this page.
It can be left in place and will not appear once the issue is saved.
However, you may delete it (i.e. all text up to and including `-->`) if you like.

### Link to issue number:

Please include the issue number here, including information on how this pull request is related to it.
This helps us to keep the information linked together.
If this is a minor/trivial change an issue does not need to be created.
If in doubt, please create one.

Note that Github [allows you to automatically close issues using keywords](https://help.github.com/en/articles/closing-issues-using-keywords).
For example, when writing `closes #7777` or `fixes #4242` in the body of the description, the mentioned issue will automatically be closed when the pull request is merged into the master branch.
If your pull request is merged into another branch, such as beta, the particular issue will be closed automatically after merging the pull request into master from beta.

### Summary of the issue:

A quick summary of the problem you are trying to solve.

### Description of user facing changes:

Please include a short explanation of how the user experience has changed to address the issue.

### Description of developer facing changes:

Include a short description of how the developer experience has changed as a result of fixing a user-facing issue, or to fix a developer-facing issue.
For example, if call signatures have changed, or new ways of solving a problem have been introduced.

### Description of development approach

Provide a description of the technical changes.
Please also include any links or external information you may have used in order to address the issue.
This helps others to have the same background as you and learn from this work.

Include reasoning as to why the approach followed is the best approach compared to other ways of fixing the issue.
It may be worth including a summary of the history of the technical changes, and how the final approach was determined.

Example: a new app module was created, which handles an event raised by UIA.

### Testing strategy:

Outline the steps you took to test the change.
This should allow someone else to reproduce your testing.

More broadly, try to answer the following questions:

- How have you tested to ensure that your change works as intended?
- Have you ensured testing coverage across all supported operating systems?
- Have you considered possible regressions (related features or behaviours that may break)?

Please use this section as an opportunity to try to convince us (and yourself) that your proposed change should be merged.

Often in face to face development it's useful to demonstrate a change, quite often bugs are noticed at this point when the new person asks for some variation in testing approach.
Since we are unlikely to be able to demonstrate a feature in an interactive way, an easy-to-follow list of steps for a "demo" allows others to check for themselves without having to work out all the details.
It also serves as a starting point for members of the community who are testing the changes that go into NVDA.

Example:

> In NVDA settings ensure that:
>
> - Keyboard category
>   - "speak typed characters" is unchecked
>   - "speak typed words" is checked
>
> 1. Open notepad
> 2. Type "hello"
> 3. Press space
>
> Expect "hello" to be announced.

- If many NVDA settings are required, consider attaching a sample `nvda.ini` file to the PR.
- If a complicated document is required to test with a 3rd party application, consider attaching it to the PR for others to test with.

### Known issues with pull request:

Are there any known issues or downsides of this approach?
For instance, "Will not work with UIA enabled in advanced settings".

## Code Review Checklist

Code must be reviewed (via a Pull Request on GitHub) before it can be accepted into the project.
The [Pull Request template](.github/PULL_REQUEST_TEMPLATE.md) asks authors (and reviewers) to consider several aspects of the change.

The aim of this checklist is to ensure each item has been considered by both the author and the reviewer.
Hopefully it helps to prevent items being forgotten.
After reviewing the checklist, the reviewer and author need to use their best judgement on whether they think further changes need to be made.
Reviewers are invited to start a conversation about items in the list, to provide guidance on how to improve the PR.
Not all items will be applicable for all situations, in this case checking the item lets reviewers know it's been considered.
If the reviewer reaches the same conclusion as the author, no further work is necessary.

Most items in the checklist have a section in the PR template where you can add your thoughts.
Doing so may pre-empt questions from the reviewer ensuring you are on the same page and speed up the review process.

### Testing:

Discuss under "testing strategy" heading:

- Unit tests
  - Describe the coverage of automated unit tests?
  - Is the changed code covered already, or can it be covered by automated unit tests?
- System tests
  - Describe the coverage of automated system tests?
  - Is the changed code covered already, or can it be covered by automated system tests?
- Manual tests
  - Have you followed any relevant test plans in [the manual test documentation](../../tests/manual/README.md)?
  - How did you manually test the change?
    - Be clear on steps another user can take to replicate your testing.
    - Clearly describing this helps alpha testers, and future developers.
  - Is this a commonly tested part of NVDA?
  If so, please add your manual test steps to [the manual test documentation](../../tests/manual/README.md).
  - As a reviewer, please use this description to replicate the testing (if possible).

### API is compatible with existing add-ons

- If this is not a `.1` breaking release, ensure that all API changes are backwards compatible with existing add-ons.
- Ensure proposed API changes are included in the change log under "Changes for Developers".
- See [Deprecations](./deprecations.md) for more information.

### Documentation

- Change log entries
  [Ensure there is a change log entry if required](./contributing.md#change-log-entry).
- User Documentation
  Does the user documentation need updating?
- Context sensitive help for GUI changes.
  New GUI options require context sensitive help assignment.
- Developer / Technical Documentation
  Does the developer or technical documentation need updating?

### UX of all users considered

- Users of NVDA are diverse, and rely on different parts of NVDA.
  Ensure the change caters to users of the following:
  - Speech
  - Braille
  - Low Vision
  - Different web browsers (Firefox, Chrome, Edge)
  - Localization in other languages / culture than English
- When one of these can not be supported with this change, highlight it under the "Known issues" heading.

### Security precautions taken

Windows allows NVDA to access secure information from a user's desktop while the lock screen is activated.
When handling NVDAObjects, code should check if an object should be available while the lock screen is activated.
This can be done checking `utils.security._isSecureObjectWhileLockScreenActivated`.
If this function returns `True`, code should not process the NVDAObject any further, and return gracefully.
It is important that information from the object does not reach the user.

Any code which should not be performed on [secure screens](https://download.nvaccess.org/documentation/userGuide.html#SecureScreens) should check `globalVars.appArgs.secure` and return gracefully.
If this is a user initiated action, `gui.blockAction` should be used to notify the user that the action cannot be performed.
This can be done by decorating the function with `@blockAction.when(blockAction.Context.SECURE_MODE)`.
