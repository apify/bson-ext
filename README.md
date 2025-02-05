
# Apify notes
We forked C++ BSON because we need to enlarge the buffer limit to 32MB for BSON.
[This issue](https://github.com/mongodb-js/bson-ext/issues/42) needs to be fixed to be able to use the forked repo in the apify-core repository.

Meantime we are publishing this forked repo in the @apify-packages NPM repository.

If we need to merge changes from [original repo](https://github.com/mongodb-js/bson-ext):
1) Merge changes into this repository.
2) Run `npm install`
3) Run `npm publish --tag latest`
3) Install updated version in apify-core repository

# BSON parser

BSON is short for Bin­ary JSON and is the bin­ary-en­coded seri­al­iz­a­tion of JSON-like doc­u­ments. You can learn more about it in [the specification](http://bsonspec.org).

This module is a C++ addon for Node.js that implements the BSON parsing and serialization outside of the JavaScript environment in order to increase speed and memory efficiency for certain workloads. It uses the same types as the pure JavaScript implementation, which can be found at [mongodb/js-bson](https://github.com/mongodb/js-bson).

## Usage

To build a new version perform the following operation.

```
npm install
npm run build
```

A simple example of how to use BSON in `Node.js`:

```js
// Get BSON parser class
var BSON = require('bson-ext')
// Get the Long type
var Long = BSON.Long;
// Create a bson parser instance, passing in all the types
// This is needed so the C++ parser has references to the classes and can
// use them to serialize and deserialize the types.
var bson = new BSON([BSON.Binary, BSON.Code, BSON.DBRef, BSON.Decimal128, BSON.Double, BSON.Int32, BSON.Long, BSON.Map, BSON.MaxKey, BSON.MinKey, BSON.ObjectId, BSON.BSONRegExp, BSON.Symbol, BSON.Timestamp]);

// Serialize document
var doc = { long: Long.fromNumber(100) }

// Serialize a document
var data = bson.serialize(doc)
console.log('data:', data)

// Deserialize the resulting Buffer
var doc_2 = bson.deserialize(data)
console.log('doc_2:', doc_2)
```

## Installation

`npm install bson-ext`

## API

### BSON types

For all BSON types documentation, please refer to the documentation for the [MongoDB Node.js driver](https://github.com/mongodb/node-mongodb-native).

### BSON serialization and deserialiation

**`new BSON()`** - Creates a new BSON serializer/deserializer you can use to serialize and deserialize BSON.

#### BSON.serialize

The BSON `serialize` method takes a JavaScript object and an optional options object and returns a Node.js Buffer.

  * `BSON.serialize(object, options)`
    * @param {Object} object the JavaScript object to serialize.
    * @param {Boolean} [options.checkKeys=false] the serializer will check if keys are valid.
    * @param {Boolean} [options.serializeFunctions=false] serialize the JavaScript. functions.
    * @param {Boolean} [options.ignoreUndefined=true]
    * @return {Buffer} returns a Buffer instance.

#### BSON.serializeWithBufferAndIndex

The BSON `serializeWithBufferAndIndex` method takes an object, a target buffer instance and an optional options object and returns the end serialization index in the final buffer.

  * `BSON.serializeWithBufferAndIndex(object, buffer, options)`
    * @param {Object} object the JavaScript object to serialize.
    * @param {Buffer} buffer the Buffer you pre-allocated to store the serialized BSON object.
    * @param {Boolean} [options.checkKeys=false] the serializer will check if keys are valid.
    * @param {Boolean} [options.serializeFunctions=false] serialize the JavaScript functions.
    * @param {Boolean} [options.ignoreUndefined=true] ignore undefined fields.
    * @param {Number} [options.index=0] the index in the buffer where we wish to start serializing into.
    * @return {Number} returns the index pointing to the last written byte in the buffer.

#### BSON.calculateObjectSize

The BSON `calculateObjectSize` method takes a JavaScript object and an optional options object and returns the size of the BSON object.

  * `BSON.calculateObjectSize(object, options)`
    * @param {Object} object the JavaScript object to serialize.
    * @param {Boolean} [options.serializeFunctions=false] serialize the JavaScript. functions.
    * @param {Boolean} [options.ignoreUndefined=true]
    * @return {Buffer} returns a Buffer instance.

#### BSON.deserialize

The BSON `deserialize` method takes a Node.js Buffer and an optional options object and returns a deserialized JavaScript object.

  * `BSON.deserialize(buffer, options)`
    * @param {Object} [options.evalFunctions=false] evaluate functions in the BSON document scoped to the object deserialized.
    * @param {Object} [options.cacheFunctions=false] cache evaluated functions for reuse.
    * @param {Object} [options.cacheFunctionsCrc32=false] use a crc32 code for caching, otherwise use the string of the function.
    * @param {Object} [options.promoteLongs=true] when deserializing a Long will fit it into a Number if it's smaller than 53 bits
    * @param {Object} [options.promoteBuffers=false] when deserializing a Binary will return it as a Node.js Buffer instance.
    * @param {Object} [options.promoteValues=false] when deserializing will promote BSON values to their Node.js closest equivalent types.
    * @param {Object} [options.fieldsAsRaw=null] allow to specify if there what fields we wish to return as unserialized raw buffer.
    * @param {Object} [options.bsonRegExp=false] return BSON regular expressions as BSONRegExp instances.
    * @return {Number} returns the next index in the buffer after deserialization **x** numbers of documents.

#### BSON.deserializeStream

The BSON `deserializeStream` method takes a Node.js Buffer, `startIndex` and allow more control over deserialization of a Buffer containing concatenated BSON documents.

  * `BSON.deserializeStream(buffer, startIndex, numberOfDocuments, documents, docStartIndex, options)`
    * @param {Buffer} buffer the buffer containing the serialized set of BSON documents.
    * @param {Number} startIndex the start index in the data Buffer where the deserialization is to start.
    * @param {Number} numberOfDocuments number of documents to deserialize.
    * @param {Array} documents an array where to store the deserialized documents.
    * @param {Number} docStartIndex the index in the documents array from where to start inserting documents.
    * @param {Object} [options.evalFunctions=false] evaluate functions in the BSON document scoped to the object deserialized.
    * @param {Object} [options.cacheFunctions=false] cache evaluated functions for reuse.
    * @param {Object} [options.cacheFunctionsCrc32=false] use a crc32 code for caching, otherwise use the string of the function.
    * @param {Object} [options.promoteLongs=true] when deserializing a Long will fit it into a Number if it's smaller than 53 bits
    * @param {Object} [options.promoteBuffers=false] when deserializing a Binary will return it as a Node.js Buffer instance.
    * @param {Object} [options.promoteValues=false] when deserializing will promote BSON values to their Node.js closest equivalent types.
    * @param {Object} [options.fieldsAsRaw=null] allow to specify if there what fields we wish to return as unserialized raw buffer.
    * @param {Object} [options.bsonRegExp=false] return BSON regular expressions as BSONRegExp instances.
    * @return {Object} returns the deserialized JavaScript Object.
