<!-- Please leave this template, updating the placeholders `____` with its proper value according with the current update -->

- Update from `v____` to `v____`
- Link to release in mailing list: ____
- Common ancestor between versions: `sha-1` ____
- Last split commit: `sha-1` ____

<!-- Please check the options that applies -->

## Performed checks

- [ ] I updated `Makefile` with the new `SUPERSET_VERSION`
- [ ] I updated the `CHANGELOG` file
- Migrations:
  - [ ] I merged migrations
  - [ ] No migration merge was needed
- I checked the config files (and ported if needed):
  - superset/contib/docker
    - [ ] changes merged
    - [ ] nothing to be merged
  - superset/superset/config.py
    - [ ] changes merged
    - [ ] nothing to be merged
  - superset/.travis.yml
    - [ ] changes merged
    - [ ] nothing to be merged
- [ ] Tested the upgrade building & checking it with `src-d/sourced-ce`

<!-- Please add below this template whatever other info that could be relevant to the reviewer -->
