# Development Best Practices

This guide is a set of "best practices" for development.
It will mostly concentrate on how our Go code is developed, and
will expand over time to include other platforms.

## Source Control

### Branch Naming

Each JIRA ticket (or whatever other tool used) picked up will get its
own branch created before starting work.  If it is a new feature, the branch
should be named `feature/{ticket-number}-brief-explanation`, a hotfix would be
`hotfix/{ticket-number}-brief-explanation`, and a bugfix would be
`bugfix/brief-explanation`. The main difference between a hotfix and bugfix is timing.
A hotfix is created for code changes after a feature branch is approved into default
but cannot pass acceptance without a change (e.g. the code runs but the layout is wrong).
A bugfix is created when a bug is found in default or production after passing acceptance
(e.g. A form field is not visible on a certain screen size that wasn't tested for).

All work against this ticket would be committed against this branch.  Each commit
message should be something meaningful.  A good guideline can be found [here](http://git-scm.com/book/ch5-2.html).
The important note is this:

```
Short (50 chars or less) summary of changes


More detailed explanatory text, if necessary.  Wrap it to
about 72 characters or so.  In some contexts, the first
line is treated as the subject of an email and the rest of
the text as the body.  The blank line separating the
summary from the body is critical (unless you omit the body
entirely); tools like rebase can get confused if you run
the two together.

Further paragraphs come after blank lines.

  - Bullet points are okay, too

  - Typically a hyphen or asterisk is used for the bullet,
    preceded by a single space, with blank lines in
    between, but conventions vary here
```

This leads to less ambiguity when people are trawling through old code.  When
development is complete, a pull request should be submitted for the working branch.

Some things you should *not* do:

* Just put in the ticket number.  This forces someone to go to JIRA, or whatever
  tool currently being used, to see what the story was about.  The check-in should
  be self-contained.  Also, if you switch ticket tracking tools, now you have an
  unexplainable check-in, since the orginal ticket is unavailable.
* Don't list the files changed, unless you are providing details about what changed.
  We can see what files changed as part of the commit, we don't need a redundant
  list.
* Don't check in large blocks of commented-out code.  If it's important, we have
  the history in source control.  If it's not, throw it away.  Most code is not
  a special snowflake that must be preserved.

## Code Review

Once a pull request is submitted, this is the opportunity for code review.  Everyone
is encouraged to look at the pull requests, and to add comments to them.  Any
blocking issues should be resolved against the working branch, and re-pushed to the
main repo.  At least two other devs should add their :shipit: comments to the PR
before it gets merged.  Every comment should be addressed, even if it is only to say
why you are currently ignoring it.  Once the code has been OK'd by the team, then
it can be merged back into the `main` branch.  The person who submitted the pull
request is responsible for making sure it gets back in to the `main` branch.
Once you assign a story/bug to yourself, you assume responsibility for making sure
it gets delivered all the way through the process.

Things to look for while reviewing code:

* Any new functionality should have meaningful tests added also.  There are almost
  no good reasons to not add a test.
* Make sure the code conforms to our style guide (if applicable).  Our code should
  appear to be largely uniform.
* Names matter.  Ambiguous variable or function names help no one.
* Understandable code.  If the code requires a large comment block to explain what
  it does, it probably should be re-written to be more clear.

## Build Process

Every change to `main` should trigger a build in the CI system, or other tool.  If the
build fails, the person responsible for the breaking change should troubleshoot and
provide a fix, creating a `hotfix` branch to do so.  The usual PR and code review
principles still apply.
