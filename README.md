# zopf

[![js-standard-style](https://img.shields.io/badge/code%20style-standard-brightgreen.svg?style=flat)](https://github.com/feross/standard)
[![Build Status](https://travis-ci.org/itchio/zopf.svg)](https://travis-ci.org/itchio/zopf)
[![Dependency Status](https://david-dm.org/itchio/zopf.svg)](https://david-dm.org/itchio/zopf)

If you're running a bunch of tests:

  - Transpiled from another languages (ES2015 to ES5 via babel, for example)
    and for which you'd like stack traces in the original language
  - That use [sinon][sinon] to spy, stub and mock things
  - That sometimes return promises
  - That should fail individually when they throw

...and you want to avoid setting all that up yourself for every test case
or file, then zopf just might be for you.

## Usage & examples

zopf is actually a thin wrapper over [AVA][], which means you can use it
pretty much like [tape][]:

```javascript
var test = require('zopf')

test('basic test', function (t) {
  t.equal(true, true)
})
```

The first difference, you don't need to call `t.end()`. This is no problem
for synchronous tests. For async tests, you need to return a promise. If it
resolves, the test will pass, if it rejects, the test will fail. Anything
thrown in the test body is treated as a rejection.

The second difference is, the context, `t`, is augmented with a [sinon][]
sandbox that lets you create spies, stubs and mocks, and verifies all of them
at the end of the test.

```javascript
import test from 'zopf'
import Promise from 'bluebird'

let foo = {
  bar: () => Promise.resolve('Uh oh.')
}

test('using promises', t => {
  let mock = t.mock(foo)
  mock.expects('bar').returns(Promise.resolve('Success!'))

  return foo.bar().then(res => {
    t.equal(res, 'Success!')
  })
})
```

In that last example:

  * If `foo.bar()` rejects, the test will fail
  * If `foo.bar()` resolves without calling our mock, the test will
    fail when the sinon sandbox is verified
  * If `foo.bar()` resolves, our mock has been called, but the return
    value is wrong, the test will fail

[AVA]: https://ava.li
[tape]: https://www.npmjs.com/package/tape
[sinon]: http://sinonjs.org/

## Running tests

I (@fasterthanlime) recommend writing your tests in ES2015 and transpiling
them using something like [babel][], perhaps together with [grunt][].

[babel]: https://babeljs.io/
[grunt]: http://gruntjs.com/

Then, you can simply use [AVA][] to run your zopf tests - as long as you
`require('zopf')` instead of `require('ava')` you'll get all the zopf niceties
for free.

## Name meaning / pronunciation

[Zopf][] is a type of Swiss bread, which basically means `braid`. It seemed
appropriate for this module as it braids together several test facilities
and was short and available on npm.

[Zopf]: https://en.wikipedia.org/wiki/Zopf

## License

Licensed under MIT License, see `LICENSE` for details.

