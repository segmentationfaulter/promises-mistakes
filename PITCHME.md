# Common Mistakes With Promises

---

#### Goal: Learn how do these work?

```js

// Please note that both doSomething and doSomethingElse
// are functions which return promises

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
  // is the result of someOtherPromise available here?
});
```

---

Causing side effects will not chain, use `return` statement instead

```js
somePromise().then(function () {
  return someOtherPromise();
}).then(function () {
  // is the result of someOtherPromise available here?
  // Yes!!!
});
```

---

#### Mistake#2: The promisey callback hell

```js
getUserIdByEmail('saqib@example.com').then(function(userId) {
  getUserPostsById(userId).then(posts => /* use posts here */)
})
```

---

#### Better chain promises

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
Promise.resolve(1).then(console.log)
console.log(2)
```

What will get logged first? `1` or `2`?

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

Are these the same?

```js
somePromise()
.then(successHandler)
.then(null, errorHandler)
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
Promise.resolve('foo')
.then(Promise.resolve('bar'))
.then(console.log);
```

---

#### Puzzle#1 Answer

```js
doSomething().then(function () {
  return doSomethingElse();
}).then(finalHandler);
```

```
doSomething
|-----------------|
                  doSomethingElse(resultOfDoSomething)
                  |------------------|
                                     finalHandler(resultOfDoSomethingElse)
                                     |------------------|
```

---

#### Puzzle#2 Answer

```js
doSomething().then(function () {
  doSomethingElse();
}).then(finalHandler);
```

```
doSomething
|-----------------|
                  doSomethingElse(undefined)
                  |------------------|
                  finalHandler(undefined)
                  |------------------|
```

---

#### Puzzle#3 Answer

```js
doSomething().then(doSomethingElse())
  .then(finalHandler);
```

```
doSomething
|-----------------|
nothing goes here, simply falls through
|---------------------------------|
                  finalHandler(resultOfDoSomething)
                  |------------------|
```

---

#### Puzzle#4 Answer

```js
doSomething().then(doSomethingElse)
  .then(finalHandler);
```

```
doSomething
|-----------------|
                  doSomethingElse(resultOfDoSomething)
                  |------------------|
                                     finalHandler(resultOfDoSomethingElse)
                                     |------------------|
```
