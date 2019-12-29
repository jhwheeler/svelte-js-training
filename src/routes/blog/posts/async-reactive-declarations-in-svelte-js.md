---
title: Async Reactive Declarations in Svelte JS
date: "2020-01-01T00:00:00.000Z"
---

One of the tricker aspects of writing an app is **handling asynchronous actions**. In Svelte, there are a number of ways to handle async code; in this tutorial, we'll compare and contrast a few of them.

## Challenge

Svelte's reactive declaration syntax is in general quite intuitive. However, when it comes to asynchronous code, it can be a bit confusing to figure out exactly how to take advantage of Svelte's reactivity.

## Setup

In this tutorial, we'll be using the [Numbers API](http://numbersapi.com) to generate random facts about any given number. While there are a quite a lot of options available, we'll stick to facts about years to keep it simple. And we'll just request plain text; no JSON needed today.

To get started, let's create some simple markup with an `<input />` where the user can choose a year.

```html
<script>
  let year
</script>

<h1>Get a random fact for any year since 1 AD</h1>

<input bind:value={year} type="number" />
```

Let's add some basic styling as well:

```html
<style>
  input {
    text-align: center;
    height: 2rem;
    width: 4rem;
    border: 1px solid gray;
    border-radius: 5px;
    background-color: #f2f2f2;
    font-size: 1rem;
  }
</style>
```

Now we're ready to start fetching data! Let's look at each method in turn for handling async code in Svelte. 

## Method 1: Callbacks

This is probably the simplest way to write async reactive declarations in Svelte. Three easy steps:

1. Declare a variable to store the result of the async function
2. Place the async function in a reactive statement with `$:`
3. In the callback, assign the result of the promise to the storage variable

```js
let result

$: getData(query)
  .then(response => result = response)
```

There are a few more aspects of this that we'll go over, but this is the basic flow.

Whenever `query` is updated, `getData()` will be called and `result` will be assigned to the value of the resolved promise.

Let's take a look at this in the context of our number fact app. 

```html
<script>
  const base = 'http://numbersapi.com'

  let result
  let year

  $: if (year) {
    fetch(`${base}/${year}/year`)
      .then(response => response.text())
      .then(data => result = data)
  }
</script>
```

Every time the user changes `year` via the input binding, the fetch function will be called. Note: if `base` weren't a constant, you could change it and also trigger the reactive block. If you add another variable for the block to be dependent on, that will trigger updates as well.

Finally, display the result below the `<input />`:

```js
{#if result}
  <h3>{result}</h3>
{/if}
```

This method is quite concise and simple to use. However, in its current state, there is a serious flaw here. Can you spot it?

 **Race conditions**. As the user changes the chosen year, new data is fetched each time. If a previous fetch call returns after a more recent one, the result displayed will be incorrect, showing the result of the earlier request.
