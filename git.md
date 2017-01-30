# A guide to using Git

[Git] is one of the tools that we use at Mossio. It gives us a way to
keep track of changes made to projects. This guide is mainly intended for
developers; if you're a designer, you'll want to check out the [guide to using
GitHub].

[Git]: http://git-scm.org
[guide to using Github]: /github.md

## Guidelines

* [Write commit messages] that communicate the intent of the commit
  rather than outright listing the specific changes inside that commit. More
  detail is generally better -- don't be shy about providing bullet points or
  paragraphs if you need to!
* Use the imperative in the subject of your commit message (e.g. "Fix the
  sidebar", not "Fixes the sidebar" or some other variant).
* When working on a task, always commit to a branch. You can then use that
  branch to make a pull request on GitHub when you're finished to allow others
  to review your work.
* If possible, keep the number of commits per branch to one. (GitHub has some
  special behavior around pull requests: when you create a pull request, if your
  branch only has one commit, the message for that commit will automatically be
  pulled into the description for the PR.)
* If you need to bring a branch up to date with another branch, prefer [rebasing
  over merging] so as to create a [clean line of history].
* If you need to change a commit you've just made, [amend the commit] instead of
  making a new commit.
* Delete branches from your computer and from GitHub after you merge them so as
  not to promote clutter.
* Follow best security practices:
  * Keep API keys and other credentials out of source control by extracting
    them to environment variables.
  * Keep files that could be used to personally identify you, such as SSH keys,
    out of source control.

[Write commit messages]: #writing-good-commit-messages
[rebasing over merging]: #rebasing-vs-merging
[amend the commit]: #amending-a-commit
[clean line of history]: https://www.reviewboard.org/docs/codebase/dev/git/clean-commits/

## Workflow

1. Before starting work on a card in Trello, cut a branch off of master to
   represent that card: `git checkout -b my-branch`
1. Make small commits as you work.
1. When you're finished, look up the branch on GitHub, and make a pull request
   for it.
   1. Follow the guidelines for [writing a pull request].
   1. Post a link to the pull request in Slack and paste a link to it in a new
      comment in the relevant Trello card.
1. Wait for someone to approve your pull request (a `:+1` or `LGTM` is
   sufficient).
1. Make additional commits to your branch in response to pull request feedback.
1. As soon as you're ready to merge your branch into master:
   1. Ensure that master is up to date:
      `git checkout master && git pull && git checkout my-branch`
   1. [Squash] any related commits together: `git rebase -i master`
1. Merge the branch into master: `git merge master --ff-only`
1. Remove the branch from your computer and from GitHub:
   `git branch -d my-branch && git push origin --delete my-branch`

[writing a pull request]: /pull-requests.md
[Squash]: #squashing-commits

## The master branch

The **master** branch is the default branch in any project. The code present in
this branch is "canon": this is the code that will be deployed to staging and
production versions of the app.

This means that master should always be in a deployable state; it should never
have broken functionality or styles.

## Writing good commit messages

There are two parts to a commit message: the _subject_ and the _body_.

When you use `git commit` with the `-m` option, you are specifying only the
subject:

    git commit -m "Fix sidebar so it is responsive"

This is fine for commits that are so small that they require no explanation. But
the majority of the time, you will want to use the `-e` option:

    git commit -e

When you run this command, your editor will open up a special file. It's here
that you can supply a complete commit message -- subject and body -- and
therefore provide some context around the change you're making. Saving and
closing the file will then apply the commit.

This is what a full message looks like:

    A quick summary of the changes (50 characters or less)

    A description of the commit, wrapped to 72 characters per line, with
    anything readers (or your future self) might want to know in order to better
    understand the changes here. What was the reason behind the decisions you
    made?

    * You can put bullet points if you want.
    * Here's another bullet point.

So, for instance:

    Add the ability for users to create pages

    When a user clicks on "Add Page", this will place the user into a two-step
    process. In the first step, they will upload a cover image. In the second
    step, they can provide a cover title, a cover introduction, and a cover
    color. They should also be able to provide the content for the page,
    although this commit doesn't implement that yet. 

## Amending a commit

Sometimes you need to change something about the last commit you just made.
Instead of making a new commit, you can use the `--amend` flag to take changes
you made and merge them with the previous commit.

For instance, say you add a new stylesheet:

    .btn {
      color: red;
    }

and commit it:

    git commit -m "Add new stylesheet"

Now you decide that you actually wanted to name the class "button". So you
change it:

    .button {
      color: red;
    }

But now, instead of making a new commit, you can simply amend the previous
commit:

    git commit --amend

Note that this will bring up the commit message in my code editor where you can
choose to update the message, if you so choose.

## Rebasing vs. merging

If you have a branch that you've cut off of master, and someone has made changes
to master while you've been working on that branch, you need some way to bring
your branch up to date with those changes. There are two ways to do this:
merging and rebasing.

What's the difference? Before I talk about that, I need to talk about how
commits are represented in Git.

### History

A commit is a set of changes at a point in time. A commit may be connected to
other commits, made at some past points in time. These are its _parent_ commits.
So a project consists of a commit, connected to its parent(s), which are
connected to _their_ parent(s), stretching backward through time all the way to
the very first commit (which is connected to nothing). This is the _history_ of
the project, and we can draw it out like this:

    x ------------ Add ability for users to sign in
    |
    x ------------ Set up project
    |
    x ------------ Initial commit

This particular project happens to be pretty simple: every commit has only one
parent. So the history of that project looks like a straight line. But now look
at this one:

    x ------------ Merge pull request #4
    | \
    x -\---------- Merge pull request #2
    | \ \
    |  x \-------- Add mockup for users page
    | /   x ------ Clean up stuff
    x ----|------- Merge pull request #1
    | \   x ------ Add initial cut of page editor
    |  x /-------- Set up project
    | / /
    || /
    / /
    x ------------ Initial commit

In this case, the history of this project isn't a line, but there are branches
going this way and that, sort of like a tree. Why? Because some commits are
connected to more than one commit. But how did that come about?

### Merging

When you use `git merge`, you are actually tying two versions of history together
into a larger, more complete version.

Let me give you an example. Say we have a project with a `master` branch. But we
also have two other branches: Dave is working on an `add-marketing-page` branch,
and Sally is working on a `fix-homepage` branch.

Dave and Sally both cut their branches off of `master`, and they both make some
commits to their branch. Dave happens to finish first, and merges his branch
into master:

    add-marketing-page$ git commit -m "Add marketing page"
    add-marketing-page$ git checkout master
    master$ git merge add-marketing-page
    master$ git push

But now Sally's branch is out of date. She needs to bring in Dave's changes so
that she is able to properly merge her branch into `master` later. So she does:

    fix-homepage$ git checkout master
    master$ git pull
    master$ git checkout fix-homepage
    fix-homepage$ git merge master

What happens in this case? Well, there are actually two (possibly conflicting)
versions of history here. In one version, Dave cut a branch off of `master` and
made his change. In an alternate version, Sally cut a branch off of `master` and
made her change.

We need to join these branches together. How does Git do this? By creating a
merge commit:

       x --------- Merge commit ("Merge fix-homepage into master")
     /   \
    |     |
    |     x ------ Sally's commit ("Fix homepage")
    x ----|------- Dave's commit ("Add marketing page")
     \   /
       x --------- (Last commit on master before cut)

Notice how Dave's commit and Sally's commit both have one connection to the last
commit on `master` before they cut their branches. But the merge commit actually
has two connections: one to master (which actually used to be Dave's branch
before Dave merged it in) and the other to Sally's branch.

This is all well and good, but let's say that instead of two developers working
on the project, we have three or four, and all of them are using `git merge` to
consolidate their changes into master. Now let's say you're trying to view the
history of the project because you want to know how a particular file got
changed. Can you see how it might be complicated to do that with all of these
lines flowing in and out of each other?

What if there was a way to ensure that our project always had a simple, straight
line of history?

## Rebasing

That's where rebasing comes in. To illustrate how this compares to merging,
let's go back to our example.

Sally wants to bring her branch up to date with the new changes that Dave has
added to master. But now let's say instead of using `git merge`, she uses `git
rebase` instead:

    fix-homepage$ git rebase master
    fix-homepage$ git checkout master
    master$ git merge fix-homepage

What does Git do in this case? Well, instead of tying both master and Sally's
versions of history together, it will take Sally's version of history, remove it
from space and time, and rewrite history _as though the commits took place after
the most recent commit on `master` all along_.

So prior to rebasing, the project's complete history looked like:

          x ------ Sally's commit ("Fix homepage")
    x ----|------- Dave's commit ("Add marketing page")
     \   /
       x --------- (Last commit on master before cut)

But after rebasing, the history looks like:

    x ------------ Sally's commit ("Fix homepage")
    |
    x ------------ Dave's commit ("Add marketing page")
    |
    x ------------ (Last commit on master before cut)

Notice how the parent of Sally's commit is no longer the last commit on master,
but Dave's commit instead. That's how we know that history was rewritten.

## Resources

* [Pro Git]: This free online book is a great way to understand how Git works
  under the hood.
* [Writing sensible commit messages]

[Pro Git]: https://git-scm.com
[Writing sensible commit messages]: http://chris.beams.io/posts/git-commit/
