# Common Mistakes With Promises

---

#### Quiz: How do these variations work?

```js
doSomething().then(function () {
  return doSomethingElse();
});

doSomething().then(function () {
  doSomethingElse();
});

doSomething().then(doSomethingElse());

doSomething().then(doSomethingElse);
```

---

#### Mistake#1: The promisey callback hell

```js
remotedb.allDocs({
  include_docs: true,
  attachments: true
}).then(function (result) {
  var docs = result.rows;
  docs.forEach(function(element) {
    localdb.put(element.doc).then(function(response) {
      alert("Pulled doc with id " + element.doc._id + " and added to local db.");
    }).catch(function (err) {
      if (err.name == 'conflict') {
        localdb.get(element.doc._id).then(function (resp) {
          localdb.remove(resp._id, resp._rev).then(function (resp) {
// et cetera...
```

---

#### Better compose promises

```js
remotedb.allDocs(...).then(function (resultOfAllDocs) {
  return localdb.put(...);
}).then(function (resultOfPut) {
  return localdb.get(...);
}).then(function (resultOfGet) {
  return localdb.put(...);
}).catch(function (err) {
  console.log(err);
});
```

---

#### Mistake#2: Can promises switch between async/sync flow?

```js
const ourPromise = function() {
  return new Promise(resolve => resolve(3))
}

ourPromise().then(console.log)
console.log(4)
```

Question: What will get logged first? `3` or `4`?

---

#### Mistake#3: How to iterate?

```js
// I want to remove() all docs
db.allDocs({include_docs: true}).then(function (result) {
  result.rows.forEach(function (row) {
    db.remove(row.doc);
  });
}).then(function () {
  // Are all documents removed here???
});
```

---

#### `Promise.all` to rescue

```js
db.allDocs({include_docs: true}).then(function (result) {
  return Promise.all(result.rows.map(function (row) {
    return db.remove(row.doc);
  }));
}).then(function (arrayOfResults) {
  // All docs have really been removed() now!
});
```

---

#### Mistake#4: How to chain promises?

```js
somePromise().then(function () {
  someOtherPromise();
}).then(function () {
  // is someOtherPromise resolved here?
});
```

---

#### Causing side effects will not chain, use `return` statement instead

```js
somePromise().then(function () {
  someOtherPromise();
}).then(function () {
  // is someOtherPromise resolved here?
});
```

---
