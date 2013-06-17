# qlobber&nbsp;&nbsp;&nbsp;[![Build Status](https://travis-ci.org/davedoesdev/qlobber.png)](https://travis-ci.org/davedoesdev/qlobber)

Node.js globbing for amqp-like topics.

Example:

```javascript
Qlobber = require('qlobber').Qlobber;
matcher = new Qlobber();
matcher.add('foo.*', 'it matched!', function ()
{
    matcher.match('foo.bar', function (err, vals)
    {
        assert.deepEqual(vals, ['it matched!']);
    });
});
```

The API is described [here](#tableofcontents).

qlobber is implemented using a trie, as described in the RabbitMQ blog posts [here](http://www.rabbitmq.com/blog/2010/09/14/very-fast-and-scalable-topic-routing-part-1/) and [here](http://www.rabbitmq.com/blog/2011/03/28/very-fast-and-scalable-topic-routing-part-2/).

## Installation

```shell
npm install qlobber
```

## Another Example

A more advanced example using topics from the [RabbitMQ topic tutorial](http://www.rabbitmq.com/tutorials/tutorial-five-python.html):

```javascript
async.parallel(
    [matcher.add.bind(matcher, '*.orange.*', 'Q1'),
     matcher.add.bind(matcher, '*.*.rabbit', 'Q2'),
     matcher.add.bind(matcher, 'lazy.#', 'Q2')],
    async.mapSeries.bind(async,
        ['quick.orange.rabbit',
         'lazy.orange.elephant',
         'quick.orange.fox',
         'lazy.brown.fox',
         'lazy.pink.rabbit',
         'quick.brown.fox',
         'orange',
         'quick.orange.male.rabbit',
         'lazy.orange.male.rabbit'],
        matcher.match,
        function (err, vals)
        {
            assert.deepEqual(vals,
                [['Q1', 'Q2'],
                 ['Q1', 'Q2'],
                 ['Q1'],
                 ['Q2'],
                 ['Q2'],
                 [],
                 [],
                 [],
                 ['Q2']]);
        }));
```

## Licence

[MIT](LICENCE)

## Tests

qlobber passes the [RabbitMQ topic tests](https://github.com/rabbitmq/rabbitmq-server/blob/master/src/rabbit_tests.erl) (I converted them from Erlang to Javascript).

To run the tests:

```javascript
grunt test
```

## Lint

```javascript
grunt lint
```

# API

_Source: [lib/qlobber.js](lib/qlobber.js)_

<a name="tableofcontents"></a>

- <a name="toc_qlobberoptions"></a>[Qlobber](#qlobberoptions)
- <a name="toc_qlobberobjectaddtopic-val-cb"></a><a name="toc_qlobberobject"></a>[QlobberObject.add](#qlobberobjectaddtopic-val-cb)
- <a name="toc_qlobberobjectremovetopic-val-cb"></a>[QlobberObject.remove](#qlobberobjectremovetopic-val-cb)
- <a name="toc_qlobberobjectmatchtopic-cb"></a>[QlobberObject.match](#qlobberobjectmatchtopic-cb)
- <a name="toc_qlobberobjectclearcb"></a>[QlobberObject.clear](#qlobberobjectclearcb)

# Qlobber([options])

> Creates a new qlobber.

**Parameters:**

- `{Object} [options]` Configures the globber. Use the following properties:


  - `{String} separator` The character to use for separating words in topics. Defaults to '.'. MQTT uses '/' as the separator, for example.

  - `{String} wildcard_one` The character to use for matching exactly one word in a topic. Defaults to '*'. MQTT uses '+', for example.

  - `{String} wildcard_some` The character to use for matching zero or more words in a topic. Defaults to '#'. MQTT uses '#' too.

  - `{String | false} compare` The function to use for sorting matches in order to remove duplicates. Defaults to lexicographical string compare. Specify `false` to turn off duplicate removal. If you store values other than strings in qlobber, pass in your own compare function.

<sub>Go: [TOC](#tableofcontents)</sub>

<a name="qlobberobject"></a>

# QlobberObject.add(topic, val, cb)

> Add a topic matcher to the qlobber.

Note you can match more than one value against a topic by calling `add` multiple times with the same topic and different values.

**Parameters:**

- `{String} topic` The topic to match against.
- `{Any} val` The value to return if the topic is matched. `undefined` is not supported.
- `{Function} cb` Called when the matcher has been added.

<sub>Go: [TOC](#tableofcontents) | [QlobberObject](#toc_qlobberobject)</sub>

# QlobberObject.remove(topic, [val], cb)

> Remove a topic matcher from the qlobber.

**Parameters:**

- `{String} topic` The topic that's being matched against.
- `{Any} [val]` The value that's being matched. If you don't specify `val` then all matchers for `topic` are removed.
- `{Function} cb` Called when the matcher has been removed.

<sub>Go: [TOC](#tableofcontents) | [QlobberObject](#toc_qlobberobject)</sub>

# QlobberObject.match(topic, cb)

> Match a topic.

**Parameters:**

- `{String} topic` The topic to match against.
- `{Function} cb` Called with two arguments when the match has completed:


  - `{Any} err` `null` or an error, if one occurred.
  - `{Array} vals` List of values that matched the topic. `vals` will be sorted and have duplicates removed unless you configured [Qlobber](#qlobberoptions) otherwise.

<sub>Go: [TOC](#tableofcontents) | [QlobberObject](#toc_qlobberobject)</sub>

# QlobberObject.clear(cb)

> Reset the qlobber.

Removes all topic matchers from the qlobber.

**Parameters:**

- `{Function} cb` Called when the qlobber has been reset.

<sub>Go: [TOC](#tableofcontents) | [QlobberObject](#toc_qlobberobject)</sub>

_&mdash;generated by [apidox](https://github.com/codeactual/apidox)&mdash;_
