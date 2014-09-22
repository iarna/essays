Hi everyone! I'm the new programmer at npm working on the cli. I'm really
excited that my first major project is going to be a substantial refactor to
how npm handles dependency trees.  We've been calling this thing
[multi-stage install] but really it covers more than just installs.

[multi-stage install]: https://github.com/npm/npm/milestones/multi-stage%20install

Multi-stage installs will touch and improve all of all actions npm takes
relating to dependencies and mutating your node_modules folder.  This
affects install, uninstall, dedup, shrinkwrap and, obviously, dependencies
(including optionalDependencies, peerDependencies, bundledDependencies and
devDependencies).

The idea is simple enough: We'll build an in memory model of how we want the
node_modules folders to look.  Compare that model to what's on disk,
producing a list of steps to change the disk version into the memory model.
Finally, we execute the steps in the list.

The refactor gives several needed improvements: It gives us knowledge of the
dependency tree and what we need to do prior to doing anything that will
need to touch your node_modules folder.  This means we can give simple
errors, earlier, much improving the experience of this failure case.
Further, deduping and recursive dependency resolution are then easy to
include.  And by breaking down the actual act of installing new modules into
functional pieces, we eliminate the opportunity for many of the race
conditions that have plagued us recently.

But wait, there's more! The refactor will make implementing a number of oft
requested features a lot easier– some of the issues we intend to address
are:

* Progress bars! [#1257], [#5340]
* Automatic/intrinsic dedup, across all module source types [#4761], [#5827]
* Errors if we can't find compatible versions MUCH earlier, before any changes
  to your node_modules folder have happened [#5107]
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
yet more work.  You can follow along with progress on what will be its pull
request:

https://github.com/npm/npm/pull/6191

If you're interested in that level of detail, you may also be interested in
reading @izs's and @othiym23's thoughts here:

https://github.com/npm/npm/issues/5919
