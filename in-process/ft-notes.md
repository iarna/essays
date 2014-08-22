I've been fiddling lately with implementing filesystem transactions for the
Node fs library.

Would use commit-time locking and file timestamps to verify that nothing has
changed since the initial read was done.

Tricky bits:
    * path manipulation and use of manipulated paths later on.
    * modifying (rather than replacing) existing files, in particular,
      reading modifications back in later adds wrinkles or speed/space concerns.

The result would be a drop in replacement for fs that has something like
this added:

```javascript
fs.atomic(function actions(commit,abort,retry){
    // do fs stuff
    // if all goes well, call commit()
    // if we won't ever be able to do this, call abort()
    // if we should try the transaction again from thestart retry()
    // if none of these, you leak a transaction, which WOULD BE BAD
}, function rollback(){ //optional
    // rollback any non-fs related actions, fs layer stuff will
    // automatically be rolled back.
});
```
