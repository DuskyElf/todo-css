# Pure CSS Todo App

This is a fully functional Todo app built with just HTML and CSS — JavaScript OUT THE WINDOW! While I wouldn't recommend this approach for your next production app (more on the limitations below), it's a fun way to explore how creative we can get with CSS and HTML elements. Plus, it shows off a pretty cool implementation of theme selection without needing any scripts!

## How It Works?

### State Management: HTML Form Controls

The magic happens through cleverly placed input elements that manage our app's state:

```html
<input type="radio" name="tab-switcher" checked="checked">    <!-- Tab state -->
<input type="checkbox">                                       <!-- Task completion state -->
<input type="radio" name="theme">                             <!-- Theme selection -->
```

These inputs are still functional but hidden from view with some CSS tricks:

```css
input[type="radio"],
input[type="checkbox"] {
    width: 0;
    opacity: 0;
    cursor: pointer;
    position: absolute;
}
```

Each hidden input gets wrapped with a custom-styled label that acts as its visual stand-in. The cool thing about putting an input inside a label is that clicking anywhere on the label triggers the input. We can then target these labels with selectors like `label:has(>input:checked)` to apply different styles based on their state.

Here's a quick example:

```html
<label done-btn>✓<input type="checkbox"></label>
```

```css
[done-btn]:has(>input:checked) {
    color: var(--secondary);
}
```

The key insight here is that these input elements are our only way to interact with the app. While we can't directly manipulate the DOM with CSS, we can change styles based on input states. This creates the illusion of interactivity by showing or hiding elements depending on the current state of our inputs.

### "Database" Structure

Our app's "database" is really just a collection of pre-defined HTML elements:

```html
<main todo-database>
    <label add-btn>+<input type="radio"></label>
    <div class="todo-data">
        <label done-btn>✓<input type="checkbox"></label>
        <input type="text">
    </div>
    <!-- Repeated for each potential todo item -->
</main>
```

This gives us a fixed number of todo slots (pre-allocated in the HTML). We show or hide these elements using conditional CSS that acts a bit like database queries against the current state.

### CSS Selector-Based Queries

Here's where things get really interesting! We use some pretty advanced CSS selectors to mimic what would normally be JavaScript logic:

```css
body:has(label[nav-todo]>input:checked) .todo-data:has(label[done-btn]>input:checked) {
    display: none;
}
```

This is basically saying:
```
IF we're on the "todo" tab AND the task is marked as complete THEN
    hide that task
END IF
```

And for the "Done" tab:

```css
body:has(label[nav-done]>input:checked) .todo-data:not(:has(label[done-btn]>input:checked)) {
    display: none;
}
```

Which translates to:
```
IF we're on the "done" tab AND the task is NOT complete THEN
    hide that task
END IF
```

Pretty clever, right?

### Chain Reaction for Task Creation

One of my favorite tricks in this app is how we handle adding new tasks. Each todo item has an "add" button (actually a radio button) that controls whether the next todo item appears:

```css
/* Hide the todo item if its preceding add button is not checked */
[add-btn]:has(>input:not(:checked))+.todo-data {
    display: none;
}

/* Hide the add button once it's been clicked */
[add-btn]:has(>input:checked) {
    display: none;
}
```

To make it look like we're dynamically adding tasks, we only show the first unchecked add button and hide all the rest:

```css
/* Hide all subsequent add buttons */
[add-btn]:not(:has(>input:checked))~[add-btn] {
    display: none;
}
```

### Themeable UI via CSS Variables

Want to switch themes? No problem! The app uses a nifty theming system built with CSS variables:

```css
:root:has([theme-s] label:nth-child(1)>input:checked) {
    --text: #e7e4e9;
    --background: #110c13;
    --primary: #c1a4d2;
    --secondary: #5f2e7a;
    --accent: #9e4acd;
}

:root:has([theme-s] label:nth-child(2)>input:checked) {
    --text: #1a171c;
    --background: #f1ecf3;
    --primary: #4b2d5c;
    --secondary: #b685d1;
    --accent: #8431b4;
}

/* ... more themes */

```

When you pick a theme, the conditional `:has()` selector figures out which radio button is checked and defines the theme variables on the root accordingly. Throughout the app, all elements depend on these variables while defining their styles.

## Technical Limitations

While this CSS-only approach is super cool, it does come with some important limitations to keep in mind:

CSS is fundamentally designed for styling pages, not creating application logic. It cannot directly interact with or manipulate the DOM like JavaScript can. Instead, we're cleverly styling elements to be visible or hidden based on the state of input elements.

There's essentially no equivalent to JavaScript's dynamic memory allocation (like `malloc()` or `free()`) in CSS. This means all possible todo items must be predefined in the HTML, just waiting to be revealed when needed. It's like having a fixed-size array instead of a dynamic data structure!

This approach also makes it impossible to:
- Save todos between sessions (no localStorage or database integration)
- Dynamically generate new UI components beyond what's in the initial HTML
- Implement complex operations like sorting, filtering beyond simple visibility toggles
- Connect to external services or APIs

Despite these constraints, it's fascinating to see just how far we can push CSS with creative thinking. This project demonstrates that sometimes limitations can spark the most innovative solutions!

## Browser Compatibility

For this CSS wizardry to work, you'll need a browser that supports the `:has()` selector. Since December 2023, this feature works across the latest devices and browser versions. This feature might not work in older devices or browsers.
- Chrome/Edge 105+
- Firefox 121+
- Safari 15.4+
- Opera 91+

## Technical References

Want to dive deeper? Check out these resources:
- [CSS :has() Selector](https://developer.mozilla.org/en-US/docs/Web/CSS/:has)
- [CSS Custom Properties](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties)
- [CSS Combinators](https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/Selectors/Combinators)