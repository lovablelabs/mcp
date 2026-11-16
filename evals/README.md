# Evals

One trigger case per skill. Each case sends the sentence a person would actually say, with only the `Skill` tool available, and checks two things: the right skill fired, and the response stopped where the skill says to stop (collecting facts, asking once, waiting for a yes) instead of claiming work it could not have done.

Run all:

```sh
claude plugin eval . --trust-plugin --no-publish
```

Run one:

```sh
claude plugin eval . --case launch --runs 1 --trust-plugin --no-publish
```

No Lovable tools are started in these runs, so they cost nothing in credits. They do use the Claude API on your account.
