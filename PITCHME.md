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

#### Mistake#1: How to chain promises?

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

#### Mistake#2: The promisey callback hell

```js
getUserIdByEmail('saqib@example.com').then(function(userId) {
  getUserPostsById(userId).then(posts => // use posts here)
})
```

---

#### Better compose promises

```js
getUserIdByEmail('saqib@example.com')
.then(function(userId) {
  return getUserPostsById(userId)
})
.then(posts => // use posts here)
```

---

#### Mistake#3: Can promises switch between async/sync flow?

```js
Promise.resolve(3).then(console.log)
console.log(4)
```

Question: What will get logged first? `3` or `4`?

---

#### Mistake#4: How to iterate over promises?

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

#### Mistake#5

Are these the same codes?

```js
somePromise()
.then(successHandler)
then(null, errorHandler)
```

```js
somePromise()
.then(successHandler)
.catch(errorHandler)
```

---

#### Mistak#6: Using promises to sequentially make async calls

```js
function executeSequentially(promises) {
  // the argument passed is an array of promises
  var result = Promise.resolve();
  promises.forEach(function (promise) {
    result = result.then(promise);
  });
  return result;
}
```

---

#### Use promise factories instead

```js
function executeSequentially(promiseFactories) {
  // the argument passed is an array of promise factories
  var result = Promise.resolve();
  promiseFactories.forEach(function (promiseFactory) {
    result = result.then(promiseFactory);
  });
  return result;
}
```

---

#### Mistake#7: Promises fall through

What will get logged? `foo` or `bar`?
```js
Promise.resolve('foo').then(Promise.resolve('bar')).then(function (result) {
  console.log(result);
});
```
