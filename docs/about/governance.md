# Project ownership and hierarchy

The project at the time of writing (2024-04-22) follows the model of
[benevolent dictator for life](https://en.wikipedia.org/wiki/Benevolent_dictator_for_life), which is
Julian Ospald at the moment.

Ideally, the ownership should be shared across a core team of collaborators sharing the same vision and
engagement in the future.

I do not believe in people making decisions over projects they are barely involved in. Instead I believe
in listening to end users needs very carefully and making decisions based on that. People who want a direct
influence on the decision process have to both demonstrate they share the vision of GHCup and do the actual
work.

"Work" here doesn't have to be "writing code". There are many ways to be engaged in a project.

## Transition plan in case of maintainer absence

In case the current maintainer of GHCup (Julian Ospald) is unreachable for a prolonged period of time (3 months),
the ownership of this project will be automatically transferred to the following individuals:

- [Moritz Angerman](https://github.com/angerman)
- [Andrew Lelechenko](https://github.com/Bodigrim)

They will be tasked with finding new maintainers in whatever way they see fit (be it appointing themselves or asking HF for help).

The appointed owners may choose to stay owners after the transition period (whether it's in a passive or active capacity) or
fully transfer ownership to someone else or an organization.

The community shall be informed about this process.

If not otherwise specified by the newly appointed owners, the following principles shall apply to the *transition period*:

### During the transition period

During the transition period, no other individual or organization is allowed to drive changes to
[ghcup-hs](https://github.com/haskell/ghcup-hs) repository, unless they are explicitly allowed to do so by the appointed owners.

The following people (in addition to the owners) shall have full write access to the
[ghcup-metadata](https://github.com/haskell/ghcup-metadata) repository
(all files) for the length of the transition period, unless otherwise specified by the appointed owners:

- [Ben Gamari](https://github.com/bgamari)
- [Hécate Moonlight](https://github.com/Kleidukos)
- [Mike Pilgrem](https://github.com/mpilgrem)
- [Jens Petersen](https://github.com/juhp)

Contributions to the metadata are expected to follow a review process. If that turns out to be impractical due to lack of engagement, a wait
time of 2 days before merging shall be followed anyway, except for the `-vanilla` files, which may be merged at any time.

### Access

The GHCup website, various scripts and unofficial bindists are hosted on haskell.org infrastructure. Contact the
[Haskell.org committee](https://www.haskell.org/haskell-org-committee/) for access.

The backup owners should already have admin rights on the GHCup repositories, which are hosted on the
[Github haskell namespace](https://github.com/haskell). In case of issues contact one of the
organization admins for access, e.g.:

- [Andrew Lelechenko](https://github.com/orgs/haskell/people/Bodigrim)
- [gbaz](https://github.com/orgs/haskell/people/gbaz)
- [Hécate Moonlight](https://github.com/Kleidukos)
- [davean](https://github.com/orgs/haskell/people/davean)
- [chessai](https://github.com/orgs/haskell/people/chessai)

The owners should already have access to the [hackage package](https://hackage.haskell.org/package/ghcup/maintainers/).
In case of issues contact the [hackage trustees](https://github.com/haskell-infra/hackage-trustees).

### Private runners

Private runners maintained by Julian Ospald may cease to work. Moritz Angerman will have SSH access to the machines.
However, no one will have access to the Hetzner account and billing information. As such, those runners will simply
have to be replaced.
