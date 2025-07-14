## [1.1.1](https://github.com/wormholelabs-xyz/dev-config/compare/v1.1.0...v1.1.1) (2025-07-14)


### Bug Fixes

* correct semantic-release output handling in release workflow ([4f5ae2a](https://github.com/wormholelabs-xyz/dev-config/commit/4f5ae2aed41aae924715b7817806a4b8897f3d14))



## [1.1.0](https://github.com/wormholelabs-xyz/dev-config/compare/v1.0.0...v1.1.0) (2025-07-11)


### Bug Fixes

* add workflow_dispatch trigger to release workflow ([5de4e08](https://github.com/wormholelabs-xyz/dev-config/commit/5de4e0810073bef97a25b31425beaadbffe5f525))
* show only recent changes in release preview ([1c40d03](https://github.com/wormholelabs-xyz/dev-config/commit/1c40d0343465354016ac04c51905ad845875b98f))
* use git tags for version calculation in CI release preview ([0f3ed05](https://github.com/wormholelabs-xyz/dev-config/commit/0f3ed050ba7665fee4351b653885ebf31f8c4820))


### Features

* improve workflow automation and version determination ([318af00](https://github.com/wormholelabs-xyz/dev-config/commit/318af0043f839972fe91f0e1b165de786d5a2df3))
* split release process into prepare and publish workflows ([49cfa5b](https://github.com/wormholelabs-xyz/dev-config/commit/49cfa5b4e57b5bc07c78b21b0514ded649bb2df5))
* test commit for semantic-release dry run ([ae8b30c](https://github.com/wormholelabs-xyz/dev-config/commit/ae8b30c4ec014e818368664830048592ee4f28bc))



# [1.0.0](https://github.com/wormholelabs-xyz/dev-config/compare/fe358372bf1e6f90c3fab262b641e31f71f03d5a...v1.0.0) (2025-07-11)


### Features

* implement automated semantic release system ([5ab611a](https://github.com/wormholelabs-xyz/dev-config/commit/5ab611a0a8f531399c1d34ce0e11195010010dea))
* initial setup of shared dev configuration package ([fe35837](https://github.com/wormholelabs-xyz/dev-config/commit/fe358372bf1e6f90c3fab262b641e31f71f03d5a))


### BREAKING CHANGES

* Migrate from manual releases to automated semantic-release system

- Configure semantic-release with branch-specific workflows
- Add GitHub Actions for prerelease and stable release automation
- Implement security-first approach with approval gates for production releases
- Set up development prereleases on main, release candidates on rc/* branches
- Add comprehensive audit logging and NPM provenance attestation

This establishes a complete automated release pipeline with proper version management.

