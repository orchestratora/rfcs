# orchestrator RFC Process

This document describes the RFC process for the orchestrator,
and provides a way for the orchestrator team and the wider community
to have discussions about the features and direction of the orchestrator!
It is based on the WeAllJS RFC process and the Rust RFC process.

## What's an RFC?

The name itself is a reference to the IETF's Request For Comments process, and basically involves a document or series of documents which are drafted, reviewed, and eventually ratified (approved) by the orchestrator team through discussion among those interested, both within and outside of the orchestrator team.

An RFC can propose any change to the orchestrator itself.

This RFC process replaces feature requests in the main orchestrator repository, and feature requests made there will be redirected to the RFCs repository.

## How do I create an RFC?

- Fork https://github.com/orchestratora/rfcs
- Copy accepted/0000-template.md into accepted/0000-your-rfc-name.md
- Fill in and edit the template with your own proposal
- Submit a PR to the npm/rfcs repo
- Tell all your friends! (don't ping orchestrator folks -- we already get immediately notified about RFCs)

## How does review work?

The official place for discussion for a proposed RFC is its pull request. Anyone, both orchestrator collaborators and non-collaborators, may participate in the discussion and ask questions and provide (constructive) feedback. Keep in mind that only orchestrator collaborators are able to ratify the RFC itself, even if other users can comment.

Side discussion can happen externally, in public or private.

All discussions surrounding an RFC are covered by the [orchestrator Code of Conduct](https://github.com/orchestratora/orchestrator/blob/develop/CODE_OF_CONDUCT.md). Please keep conversations constructive, civil, and low-temperature. If tensions flare during discussion, the orchestrator team may, at its own discretion, moderate, remove, or edit posts, as well as locking the discussion on that PR or the entire RFCs repository.

## How do RFCs get ratified?

An RFC is ratified when there is consensus among orchestrator collaborators that it should be accepted. Once all collaborators have become aware of the RFC and had a chance to comment on it, the PR, along with the RFC, will be merged and the RFC will be considered ratified.

Until an RFC is ratified, it's expected that its original author continue discussing it and integrating feedback into the document until it's ready.

RFCs have a minimum 24 hour waiting period before being accepted or rejected. Once an RFC has been reviewed on Github, with all interested collaborators having an opportunity to review it, and at least one orchestrator collaborator has signed off on the changes, the PR will be accepted and all its connected changes merged.

If it's specifically requested, or if the orchestrator team determines that the topic of the RFC demands extra attention and care because of its potential impact, an RFC's "ratification period" may be extended for as long as the participants and admins feel is a reasonable length of time for consideration.

The RFC may be rejected altogether at the discretion of orchestrator collaborators. They may also be rejected if consensus has not been reached and discussion and progress on the RFC itself remain inactive for too long.

## What happens after ratification?

Once an RFC is ratified, the orchestrator team agrees to merge a corresponding PR implementing the described changes, provided it passes a standard code review by the maintainers. It is not a guarantee of implementation, nor does it obligate the orchestrator team itself to implement the requested changes. Actual integration into the orchestrator project may also be deferred to a later date, or a later semver-major release, at the orchestrator collaborators' discretion. All the RFC does is communicate the team's consensus to accept a change.

When the changes described in an RFC have been implemented and merged into the relevant repository (and thus, due to be released), the corresponding RFC will be moved from accepted/ to implemented/. If you'd like to implement an accepted RFC, please make a PR in the appropriate repository and mention the RFC and the RFC PR. Feel free to do this even for work-in-progress code!

## How do I change an RFC after ratification?

RFCs themselves cannot be modified after ratification, but new RFCs can be proposed and ratified to amend or remove a change previously ratified through the RFC process. These amendments will involve the exact same process as a regular RFC.
