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
remotedb.getAllDocs().then(function (docs) {
  docs.forEach(function(document) {
    localdb.put(document).then(function(response) {
      alert("Document stored successfully with ID" + document._id);
    }).catch(function (err) {
      if (err.name == 'conflict') {
        localdb.get(document._id).then(function (resp) {
          localdb.remove(resp._id, resp._rev).then(function (resp) {
// et cetera...
```

---

#### Better compose promises

```js
remotedb.getAllDocs().then(function (resultOfAllDocs) {
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
  return Promise.resolve(3)
}

ourPromise().then(console.log)
console.log(4)
```

Question: What will get logged first? `3` or `4`?

---

#### Mistake#3: How to iterate over promises?

```js
// I want to remove() all docs
db.getAllDocs().then(function (docs) {
  docs.forEach(function (document) {
    db.remove(document);
  });
}).then(function () {
  // Are all documents removed here???
});
```

---

Traditional looping constructs will not work, we will need `Promise.all`

```js
db.getAllDocs().then(function (docs) {
  return Promise.all(docs.map(function (document) {
    return db.remove(document);
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

Causing side effects will not chain, use `return` statement instead

```js
somePromise().then(function () {
  return someOtherPromise();
}).then(function () {
  // is someOtherPromise resolved here?
  // Yes!!!
});
```

---
