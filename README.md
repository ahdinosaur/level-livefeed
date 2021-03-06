# level-livefeed

Live query a range in leveldb

## Example

You query a range in the database. It will load the range from
    disk and then also add on anything else you put or del from
    it.

It's basically a never ending feed like `tail -f`

```js
var livefeed = require("..")
    , level = require("levelidb")
    , WriteStream = require("write-stream")

    , db = level("/tmp/db-livefeed-example", {
        createIfMissing: true
    })

var stream = livefeed(db, { start: "foo:", end: "foo;" })

stream.pipe(WriteStream(function (chunk) {
    console.log("chunk", chunk.type, chunk.key.toString()
        , chunk.value && chunk.value.toString())
}))

stream.on("loaded", function () {
    console.log("finished loading from disk")
})

setTimeout(function () {
    db.put("foo:some key", "some value")

    db.del("foo:die")

    db.batch([
        { type: "put", key: "foo:one", value: "two" }
        , { type: "del", key: "foo:two" }
    ])
}, 2000)
```

prints

```
chunk put foo:one two
chunk put foo:some key some value
finished loading from disk
chunk put foo:some key some value
chunk del foo:die undefined
chunk put foo:one two
chunk del foo:two undefined
```

## Installation

`npm install level-livefeed`

## Contributors

 - Raynos

## MIT Licenced
