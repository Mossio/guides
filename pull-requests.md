# Pull Requests

Projects aren't built by one person, but by a team of people. That's why [pull
requests] are part of our development process -- they help to ensure that your
teammates are aware of the work you've done and provide a way to present
meaningful feedback. Therefore, it's important to understand how to write good
pull requests and how to properly give -- and receive -- feedback.

[pull requests]: https://help.github.com/articles/using-pull-requests/

## Writing a pull request

_Note: if you follow the [one commit per branch] rule, then the message for your
commit will automatically become the PR description. So all of the guidelines
for [writing a good commit message] apply here._

* When creating a pull request straight from a branch, replace the name that
  GitHub generates with your own name.
* Always fill in the description box for the pull request.
  * Explain the context behind the changes you've made as though your reviewer
    knows nothing about the project.
  * Provide a description of any changes in particular that the reviewer would
    not be able to easily glean from reading through the code.
  * If you've made any UI changes, take a screenshot of the before and after, or
    record an animated GIF of the changes, and drag the file(s) into the
    description.
* Be explicit about what you want feedback on. If necessary, highlight specific
  lines by adding comments to them.
* Be explicit about *when* you want feedback as well. For instance, add a
  `[WIP]` to the beginning of the PR name if you're not quite ready for people
  to review it yet.

[one commit per branch]: /git.md#guidelines
[writing a good commit message]: /git.md#writing-good-commit-messages

## Participating in the feedback process

* Understand that that many programming decisions are opinions, a problem may
  have many solutions or implementations, and that every option has tradeoffs.
* Be courteous and respectful of your teammates. Assume that they are
  intelligent and that their decisions and opinions are well-meaning.

## Giving feedback

* Parlay your opinions not as commands ("This is wrong, you should do this
  instead"), but as suggestions ("What do you think about making this change?")
* Ask the author kindly for any context that you may perceive as missing.
* Review not just details such as style, but also the overall purpose behind the
  changes and the approach that the author has taken to implement them.
* Stop and take a second to think before responding to a change that you
  strongly disagree with.
