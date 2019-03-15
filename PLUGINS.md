# Plugins

The letterpad editor comes with some basic plugins which will allow to create mixed content. Currently we have the below plugins:

-   Rich Text (bold, italic, underline, link, headings)
-   List (ordered and unordered)
-   Image
-   Gallery
-   Codeblock
-   Highlight words
-   Audio
-   Blockquote
-   Embed

# Concept

In Letterpad Editor, most plugins takes care of 4 essential responsibilites.

1. Rendering a tag
2. Rendering through markdown
3. Rendering when pasting a html content
4. Converting the tag to json (to save to database)

We will get into details while writing a plugin.

A plugin may render itself as a node or as a mark or may not render anything.

#### Node

By default, a node element occupies the entire space of its parent element (container), thereby creating a `block`.

> A node element always starts on a new line and takes up the full width available (stretches out to the left and right as far as it can).

Examples: `div`, `header`, `footer`, `section`, etc. are all node type.

A node element can contain marks in it. eg.

```html
<section>
    <strong>Bold</strong> is a mark inside the node <code>section</code>. It can
    also contain <em>italics</em>
</section>
```

#### Mark

A mark is used to format or describe a string of characters in your editor. Examples include basic formatting, like bold, italics, and underline, but they can also be used to implement more complex features. The basic idea is that if it can be implemented by describing a range of the characters, but doesn’t affect the structure of the document, marks are a good choice.

You can read more about how to write a plugin in the the section [Writing a Plugin](writeplugin.md)
