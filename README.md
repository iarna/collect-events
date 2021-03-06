collect-callbacks
-----------------

Aggregate many callbacks into one

Synopsis
--------

    var collect = require('collect-callbacks');

    collect.all( function(aggregate) {
        setTimeout(aggregate(function(){ console.log("two") }),2000);
        setTimeout(aggregate(function(){ console.log("three") }),3000);
    }, function (err, args) {
        // This won't execute until both of the timeouts above have completed
    });

    collect.all( function(aggregate) {
        setImmediate(aggregate(),'test','this');
        setImmediate(aggregate(),1,2,3);
    }, function (err, args) {
        // args is an an array of arguments objects, the first with
        // ['test,'this'] and the second with [1,2,3]
    });

    collect.all( function(aggregate) {
        setImmediate(aggregate(),'test','this');
        setImmediate(aggregate(),1,2,3);
    }.then(args) {
        // args is an an array of arguments objects, the first with
        // ['test,'this'] and the second with [1,2,3]
    });

    collect.any( function(aggregate) {
        setTimeout(aggregate(function(){ console.log("two") }),2000);
        setTimeout(aggregate(function(){ console.log("three") }),3000);
    }, function (err, args) {
        // This will be called as soon as the shorter timer triggers.
        // Note that the longer one will still trigger, just after this is executed.
    });

Description
-----------

Reduce a group of unrelated callbacks into a single callback. Either when the
first event is emitted, or after all callbacks have been emitted at least once.

Functions
---------

`var collect = require('collect-callbacks');`

* collect.all( setup[, alldone] )[ -> Promise]

`setup` is a function with a signature of (event) that should setup whatever
event listeners you want to be aggregated into the collection.  The event
argument is a function with a signature of ([listener]) that you should call
to generate event handlers that will be part of this aggregation.  You can
optionally pass through your usual callback function in the listener argument.

collect.all will call alldone (if passed) and fullfill the Promise when
ALL of the callbacks associated with it have triggered at least once.

The promise is resolved with the arguments of all of the callbacks it
collected.

* collect.any( setup[, alldone] )[ -> Promise]

Like collect.all but it triggers on the first of any of the aggregated
callbacks to complete.

Prior Art
---------

[https://metacpan.org/pod/AnyEvent::Collect](AnyEvent::Collect)
