<!-- markdownlint-disable MD041 -->

[![GitHub version](https://d25lcipzij17d.cloudfront.net/badge.svg?id=gh&type=6&v=2.0.5&x2=0)](https://d25lcipzij17d.cloudfront.net/badge.svg?id=gh&type=6&v=2.0.5&x2=0)
[![Coverage Status](https://coveralls.io/repos/boennemann/badges/badge.svg)](https://coveralls.io/r/boennemann/badges)
[![JavaScript Style Guide: Good Parts](https://img.shields.io/badge/code%20style-goodparts-brightgreen.svg?style=flat)](https://github.com/dwyl/goodparts "JavaScript The Good Parts")
[![dependency status](https://deps.rs/crate/autocfg/1.1.0/status.svg)](https://deps.rs/crate/autocfg/1.1.0)
[![contributions welcome](https://img.shields.io/badge/contributions-welcome-brightgreen.svg?style=flat)](https://github.com/dwyl/esta/issues)

## What is it?

Library offers to create callback functions and connect it by unique id descriptor with the promise object.

## How to use it?

There are couple options how to get it into your project.

1. You using npm/yarn and you want to install it with it, so do `npm i @kollorg/ducimus-accusamus-dolorem` and then import it `import c2p from "@kollorg/ducimus-accusamus-dolorem";`,
2. you typing vanilla js and you want to import it, `<import src="https://raw.githubusercontent.com/domino2/@kollorg/ducimus-accusamus-dolorem/master/dist/umd.min.js" />` what will store the @kollorg/ducimus-accusamus-dolorem into window.c2p,
3. you using umd modules in the browser, `import * as c2p from "https://raw.githubusercontent.com/domino2/@kollorg/ducimus-accusamus-dolorem/master/dist/module.min.mjs";`
4. or you doing Deno application, `import * as c2p from "https://raw.githubusercontent.com/domino2/@kollorg/ducimus-accusamus-dolorem/master/dist/module.min.mjs";` ,
5. node.js application `c2p = require('@kollorg/ducimus-accusamus-dolorem')`

**The github raw files cannot be used as I did above to import the library.** For more details read [this](https://github.blog/2013-04-24-heads-up-nosniff-header-support-coming-to-chrome-and-firefox/). In case you work in browser, store the dist file locally or on the server.

After that, just call `successfn` function to get success callback. `failfn` return fail callback.

    c2p = require('@kollorg/ducimus-accusamus-dolorem')
    function countIn(a, b, resultback) {
        resultback(a+b);
    }

    countIn(3, 4, c2p.successfn('id'))
    c2p.when('id').then(console.log) // -> 7

## Multiple arguments

When success or fail callback is called with object, simple value or array, those are available as usual in the promise function.
Multiple arguments are wrapped into object, because resolve and reject functions pass only one argument.

    c2p = require('@kollorg/ducimus-accusamus-dolorem')
    function countIn(a, b, resultback) {
        resultback(a+b, a*b, a-b);
    }

    countIn(3, 4, c2p.successfn('id'))
    c2p.when('id').then(console.log) // -> { '0': { '0': 7, '1': 12, '2': -1 }}

## Real Life example - reading files

    c2p = require('@kollorg/ducimus-accusamus-dolorem')
    fs = require('fs')

    // callback approach - probably better :-) because of passing multiple arguments
    //fs.readFile('/etc/hosts', 'utf8', function (err,data) {
    //    if (err) throw(err)
    //    else console.log(data)
    //})

    // @kollorg/ducimus-accusamus-dolorem approach

    fs.readFile('/etc/hosts', 'utf8', c2p.successfn('p-id-1'))

    c2p.id('p-id-1').promise.then(function(args){

        let err = args[0]
        let data = args[1]

        if (err) throw(err)
        else console.log(data)
    })

## Nuance 1

`c2p = require('@kollorg/ducimus-accusamus-dolorem')` returning global object which could be used from any place and work with same promise as has been used on other place of project according to ID.

`c3p = require('@kollorg/ducimus-accusamus-dolorem').build()` on other side creating local object and cannot share promise with other parts of your project.

    c2p.when('id').then(console.log)
    c3p.when('id').then(console.log)

    c2p.id('id').resolve(1)
    // c2p.id('id').resolve(3) // -> it will throw an exception
    c3p.id('id').resolve(2)
    // c3p.id('id').resolve(4) // -> it will throw an exception

    // output -> 1 and 2

## Nuance 2 - multi-when

It is possible to wait for resolve of multiple promises.

    function sum(a, b, cb) { cbk(a+b) }

    c2p.when(['ab.2','ab.1','another']).then((a) => expect(a).to.eql([8,5,'test']))

    sum(1, 4, c2p.successfn('ab.1'))
    c2p.id('another').resolve('test')
    sum(3, 5, c2p.successfn('ab.2'))

## API

`c2b: promiser object`

- function id(string): deferred object
- function successfn(string): solve callback - **pointing to resolve fn from deferred**
- function failfn(string): reject callback - **pointing to reject fn from deferred**
- function when(string|string[]): thenable object
- function build(): new promiser object

`deferred object`

- funciton isPending(): true|false
- function isSucceed(): true|false
- function isFailed(): true|false
- function isSameObjectAs(): true|false - **test if two pointer referencing same object**
- function resolve(): void - **it can pass multiple arguments, see note above**
- function reject(): void - **it can pass multiple arguments, see note above**
