<h1 align="center">🪄📗 - spellstore</h1>
<p align="center">
    <em>Basic, Barebones Feature store using SQLAlchemy</em>
</p>

Why deploy any new infrastructure at all? spellstore attempts to bridge the feature store production lifecycle to day 0 - just install the python library and go!

Status: _design phase_

Goals:

*  Enable a feature store experience without write access using `sqlalchemy`
*  Define features in YAML

Limitations:

*  Performance is limited to Pandas and SQLAlchemy

# Usage

TBC - this is proposed CLI design

```console
$ spellstore get meta all --metadata metadata.yml
$ spellstore get meta entity --metadata metadata.yml
$ spellstore get meta feature --metadata metadata.yml
$ spellstore get meta group --metadata metadata.yml
$ spellstore export --feature <list of features> --snapshot-date <date/datetime> --output <(optional)>
$ spellstore join <labels.csv> --entity-column <column:str> --features <list of features> --output <(optional)>
```

Convenience utilities - this is a wrapper around `pandas` to write to the underlying database, but not needed. It is provided so that the user never needs to leave CLI.

```console
$ spellstore load <input file.csv> --group <feature group>
```

Python API: TBC, should mirror CLI usage

```py
from spellstore.feature_store import FeatureStore
from datetime import datetime

feature_store = FeatureStore(repo_config, engine)
print(feature_store.export(["table1.feat1", "table1.feat2"], snapshot_date=datetime.now()))
```

## CLI Coverage

- [x] `spellstore get meta all`
- [x] `spellstore get meta entity`
- [x] `spellstore get meta feature`
- [x] `spellstore get meta group`
- [x] `spellstore export`
- [x] `spellstore join`
- [x] `spellstore load`


## Things to Implement

- [x] TTL support similar to Feast (untested)
- [ ] Testing on variety of databases
- [ ] Documentation and better usage examples
- [ ] Benchmarks and performance
- [ ] Clean up package requirements
- [x] Require fallback if the language doesn't support `partition` `over`
- [ ] Support feature name aliasing

## Tech Debt

A lot of the interfaces aren't cleanly separated. Need to separate:

*  things which build queries (these should be functional) - should not have branching logic
*  things which process data (these should be imperative) - you can have branching logic

Get rid of copy+paste code (theres multiple instances of it)

# Architecture

Well...the difference between spellstore with other approaches is there is no data created or stored on disc unless you explicitly tell it to write to disc. We don't create a database containing metadata or keep track of revisions. We assume that is handled elsewhere. Metadata is assumed to be stored directly in version control, and we provide `cli` tools to explore the metadata. 

# Why not Feast?

Feast is great, you probably should use Feast rather than spellstore. This project exists more as an exercise for me, as Feast often requires write access to databases, which may not be available to teams depending on organisational structures. 

spellstore hopefully provides data scientists a "lightweight feature store" experience in constrained environments.

# Inspirations

Full credit needs to be given to the following projects:

*  Feast - https://github.com/oom-ai/oomstore
*  Oomstore - https://github.com/oom-ai/oomstore
