
## JavaScript DOM Questions

### 1. What is the difference between `getElementById`, `getElementsByClassName`, and `querySelector` / `querySelectorAll`?

`getElementById('someId')` searches the DOM for an element whose `id` attribute matches the string and returns that one element directly, or `null` if nothing matches. Since IDs are meant to be unique, this method always returns at most one element and is the fastest way to look something up.

`getElementsByClassName('someName')` returns an `HTMLCollection` of all elements that carry that class name. The key behaviour here is that the collection is **live** — if you later add or remove elements with that class, the collection automatically reflects those changes without you calling the method again.

`querySelector` accepts any valid CSS selector string and returns the **first** matching element or `null`. `querySelectorAll` returns a **static NodeList** of all matches — a snapshot taken at the moment of the call that will not update if the DOM changes later. These two methods are the most flexible.

### 2. How do you create and insert a new element into the DOM?

We can create a new element with `document.createElement('tagName')`, configure it, then use one of several methods to place it in the document.

```js:
// 1. Create
const card = document.createElement('div');
card.className = 'job-card';
card.textContent = 'Google — Software Engineer';

// 2. Insert
document.getElementById('jobs-list').appendChild(card);       // adds at end
document.getElementById('jobs-list').prepend(card);           // adds at start
existingCard.insertAdjacentElement('afterend', card);         // inserts after a sibling

### 3. What is Event Bubbling? And how does it work?

Event bubbling is the process by which a DOM event fired on a child element travels upward through every ancestor element until it reaches the document root.

If we have this structure:

```html
<div id="list">
  <button id="delete">Delete</button>
</div>
```

Clicking the button fires the click event on the button first, then it bubbles up to the `div`, then to `body`, then to `html`, then to `document`. A click listener attached to the `div` will fire even though the user only clicked the button inside it.

The DOM event model has three phases: **capture** (travels down from root to target), **target** (fires on the element that was actually interacted with), and **bubble** (travels back up). `addEventListener` listens in the bubble phase by default. we can stop the event from continuing up by calling `event.stopPropagation()` inside any handler.

---

### 4. What is Event Delegation in JavaScript? Why is it useful?

Event delegation means attaching a single event listener to a **parent** element and using it to handle events that originate on any of its children. This works because of bubbling — events fired on children travel up to the parent, where the listener catches them.

```js
document.getElementById('jobs-list').addEventListener('click', function(e) {
  if (e.target.classList.contains('act-interview')) {
    // handle Interview button click
  }
  if (e.target.classList.contains('del-btn')) {
    // handle Delete button click
  }
});
```

This pattern is useful for three reasons. First, **performance** — one listener consumes far less memory than attaching individual listeners to dozens of buttons. Second, **dynamic content** — elements added to the DOM after the listener was registered are handled automatically, because the parent is always present. Third, **cleaner code** — all related event logic lives in one place.

---

### 5. What is the difference between `preventDefault()` and `stopPropagation()`?

Both are called on the event object but they solve completely different problems.

**preventDefault() vs. stopPropagation()
These two are often confused, but they serve very different purposes.

event.preventDefault(): Stops the browser's default action.

Example: Stopping a link from opening a URL or a submit button from refreshing the page.

event.stopPropagation(): Stops the event from bubbling up the DOM tree.

Example: If you click a "Delete" button inside a clickable "Card," you use this to make sure the card's click event doesn't fire when the button is pressed.

