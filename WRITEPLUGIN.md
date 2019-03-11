# Write your first plugin

All plugins are kept in the `src/plugins` folder. Each plugin should contain an `index.js` file containing the configuration of the plugin. Each plugin can contain multiple plugins within itself.

Lets say you want to create a plugin which takes care of images.
If we follow symentec html, the markup would look like this.

```html
<figure>
    <img src="foo.jpg" />
</figure>
```

Here you will have to handle two tags, `figure` and `img`. So you may write 2 plugins in one configuration.

Lets write a simple plugin which takes care of `linebreaks`.
We will start with the basic nature of line-break. It is of block type and it is a node.

// TODO - Explain type and tag and possible options for each

```js
[
    {
        type: "block",
        tag: "node"
    }
];
```

Next we have to tell the plugin which html tag it has to take care. In this case, its the `hr` tag.

```js
export default [
    {
        type: "block",
        tag: "node",
        identifier: ["hr"]
    }
];
```

Once it encounters a `hr` tag, what kind of transformation should the plugin do. We define this in the `render` option.
We will transform `hr` tag into a `div` with some class, to write css for line break.

```js
export default [
  {
    type: "block",
    tag: "node",
    identifier: ["hr"]
    render: ({ children, attributes }: any) => (
      <div {...attributes} className="seperator">{children}</div>
    )
  }
];

// the css may look like this
.seperator {
    border-bottom: 1px solid #eee;
}
```

Lets say, when we type `---`, it should transform this into a line break. Gor this you can
use the markdown option.

```js
export default [
  {
    type: "block",
    tag: "node",
    identifier: ["hr"]
    render: ({ children, attributes }: any) => (
      <div {...attributes} className="seperator">{children}</div>
    ),
    markdown: {
        trigger: "space",
        before: /^(-{3})$/, // regex to identify ---
        change: editor => {
            return editor.setBlocks({ type: "hr" });
        }
    }
  }
];
```

Here, the `change` method gives you the instance of the editor. This will allow you to do more complex things.

And that is it. Thats exactly how a plugin works.
