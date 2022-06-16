# SWAM: the Smart Web Application Minifier

SWAM is a tool for minifying HTML, CSS, and JavaScript files, in a smarter way.

## Why SWAM?

You might ask what "smarter" minification is.

SWAM was based on a PoC I wrote up a while ago for minifying HTML classNames. That's primarily what SWAM does. It parses HTML, CSS, and JavaScript (via a library) to set your website up for the most efficient minification, including removing comments, whitespace, HTML classNames and (if enabled) IDs, and much more.

An example of what SWAM can do is below.

<details>
<summary>Before</summary>

```html
<style>
    .red {
        color: red;
    }
    .blue {
        color: blue;
    }
    .green {
        color: green;
    }
    .hidden {
        display: none;
    }
    #header {
        background-color: red;
    }
</style>

<div>
    <header id="header" class="green">
        <h1>Hello World!</h1>
    </header>
    <article>
        <p>This is some text.</p>
        <p>
            <span class="red">I'm red.</span> <span class="green">I'm green.</span> <span class="green">Me too!</span>
            <br />
            <span class="blue">And I'm blue.</span>
            <br />
            <div class="hidden" id="to-unhide">I appear after the page loads.</div>
        </p>
    </article>
</div>

<script>
    document.getElementByID('to-unhide').classList.remove('hidden');
</script>
```

</details>
<details>
<summary>After</summary>

<!-- prettier-ignore-start -->

```js
// configured similar to
{
    minifyClassNames: true,
    minifyIDs: true,
    whitelistIDs: [ 'header' ],
    jsName: '_'
}
```

```html
<!-- made less minified for readability reasons -->
<style>
    /* prioritizing more often used classes to earlier names */
    .b { color: red; }
    .c { color: blue; }
    .a { color: green; }
    .d { display: none; }
</style>

<div>
    <header id="header" class="a">
        <h1>Hello World!</h1>
    </header>
    <article>
        <p>This is some text.</p>
        <p>
            <span class="b">I'm red.</span> <span class="a">I'm green.</span> <span class="a">Me too!</span>
            <br />
            <span class="c">And I'm blue.</span>
            <br />
            <div class="d" id="a">I appear after the page loads.</div>
        </p>
    </article>
</div>

<script>
    document.getElementByID(_.id('to-unhide')).classList.remove(_.class('hidden'));
</script>
```

<!-- prettier-ignore-end -->

</details>

What makes it smart? A lot of optimizations exist in the code. For example, if a class or ID is used once, it's added to a special whitelist to use less bytes in the lookup tables.