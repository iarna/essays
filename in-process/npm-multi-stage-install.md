Hi everyone! I'm the new programmer at npm working on the CLI. I'm really
excited that my first major project is going to be a substantial refactor to
how npm handles dependency trees.  We've been calling this thing
[multi-stage install] but really it covers more than just installs.

[multi-stage install]: https://github.com/npm/npm/milestones/multi-stage%20install

Multi-stage installs will touch and improve all of the actions npm takes
relating to dependencies and mutating your node_modules directory.  This
affects [install], [uninstall], [dedupe], [shrinkwrap] and, obviously,
[dependencies] (including [optionalDependencies], [peerDependencies],
[bundledDependencies] and [devDependencies]).

[dependencies]: https://www.npmjs.org/doc/files/package.json.html#dependencies
[optionalDependencies]: https://www.npmjs.org/doc/files/package.json.html#optionaldependencies
[peerDependencies]: https://www.npmjs.org/doc/files/package.json.html#peerdependencies
[devDependencies]: https://www.npmjs.org/doc/files/package.json.html#devdependencies
[bundledDependencies]: https://www.npmjs.org/doc/files/package.json.html#bundleddependencies

[install]: https://www.npmjs.org/doc/cli/npm-install.html
[uninstall]: https://www.npmjs.org/doc/cli/npm-uninstall.html
[dedupe]: https://www.npmjs.org/doc/cli/npm-dedupe.html
[shrinkwrap]: https://www.npmjs.org/doc/cli/npm-shrinkwrap.html

The idea is simple enough: Build an in-memory model of how we want the
node_modules directories to look.  Compare that model to what's on disk,
producing a list of steps to change the disk version into the memory model.
Finally, we execute the steps in the list.

The refactor gives several needed improvements: It gives us knowledge of the
dependency tree and what we need to do prior to touching your node_modules
directory.  This means we can give simple errors, earlier, much improving
the experience of this failure case.  Further, deduping and recursive
dependency resolution are then easy to include.  And by breaking down the
actual act of installing new modules into functional pieces, we eliminate
the opportunity for many of the race conditions that have plagued us
recently.

Breaking changes: The refactor will likely result in a new major version as
we will almost certainly be tweaking [lifecycle script] behavior.  At the very
least, we'll be running each lifecycle step as its own stage in the multi-stage
install.

[lifecycle script]: https://www.npmjs.org/doc/misc/npm-scripts.html

But wait, there's more! The refactor will make implementing a number of
oft-requested features a lot easier– some of the issues we intend to address
are:

* Progress bars! [#1257], [#5340]
* Automatic/intrinsic dedupe, across all module source types [#4761], [#5827]
* Errors if we can't find compatible versions MUCH earlier, before any changes
  to your node_modules directory have happened [#5107]
* Better diagnostics when peerDependencies produce impossible to resolve scenarios.
* Better use of bundledDependencies
* Recursively resolving missing dependencies [#1341]
* Better shrinkwrap [#2649]
* Fixes some icky edge cases [#3124], [#5698], [#5655], [#5400]
* Better shrinkwrap support, including updating of shrinkwrap file when you use
  –save on your installs and uninstalls [#5448], [#5779]
* Closer to transactional installs [#5984]

[#1257]: https://github.com/npm/npm/issues/1257
[#1341]: https://github.com/npm/npm/issues/1341
[#2649]: https://github.com/npm/npm/issues/2649
[#4761]: https://github.com/npm/npm/issues/4761
[#5107]: https://github.com/npm/npm/issues/5107
[#5340]: https://github.com/npm/npm/issues/5340
[#5698]: https://github.com/npm/npm/issues/5698
[#5655]: https://github.com/npm/npm/issues/5655
[#5400]: https://github.com/npm/npm/issues/5400
[#5448]: https://github.com/npm/npm/issues/5448
[#5779]: https://github.com/npm/npm/issues/5779
[#5827]: https://github.com/npm/npm/issues/5827
[#5984]: https://github.com/npm/npm/issues/5984

So when will you get to see this? I don't have a timeline yet– I'm still in
the part of the project where everything I look at fractally expands into
yet more work.  You can follow along with progress on what will be its [pull
request](https://github.com/npm/npm/pull/6191)

If you're interested in that level of detail, you may also be interested in
reading [@izs]'s and [@othiym23]'s [thoughts](https://github.com/npm/npm/issues/5919).

[@izs]: https://twitter.com/izs
[@othiym23]: https://twitter.com/othiym23
