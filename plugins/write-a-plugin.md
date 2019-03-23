# Write a plugin

## Write your first plugin

Plugins are the only way to add features to the editor. A plugin can be added in the below way:

```javascript
import React from "react";
import render from "react-dom";
import LetterpadEditor from "letterpad-editor";

// yout custom plugin
import customPlugin from "./customPlugin";
// create an array of your custom plugins
const myPlugins = [customPlugin];

render(
    <LetterpadEditor plugins={myPlugins} />,
    document.getElementById("#app")
);
```

**Requirements of a plugin**

1. Each plugin should contain an `index.js` file containing the configuration of the plugin **in an array**.
2. As the plugin outputs an array, you can have multiple plugins defined in one plugin
3. A plugin is responsible to take care of its own markdown feature if it has any.
4. There may exist only one plugin which takes care of a tag \(like img, blockquote, etc\).
5. When html content is pasted, each tag is rendered by one and only one plugin. This means if there are multiple plugins responsible to render an `img` tag, only one plugin will be allowed to render. This depends on the order of plugins. The plugin positioned at the last will always have the highest priority.
6. Plugins developed by you will always have higher priority than the default plugins.

Lets say you want to create a plugin which takes care of images. The markup would look like this.

```markup
<img src="foo.jpg" />
```

Here you will have to handle the tag `img`. Lets see how we can write a plugin to render this.

We will start with the property of `img`. It is of `node` type.

```javascript
[
    {
        renderType: "node"
    }
];
```

Next we have to tell the plugin which html tags it has to take care.

```javascript
export default [
    {
        renderType: "node",
        identifier: ["img"]
    }
];
```

> It is very **important** to note that this plugin should not only allow us to insert an image but should also be able to render an image when we paste html content.

To insert an image, we need a button in the toolbar.

```javascript
export default [
  {
    type: "block",
    tag: "node",
    identifier: ["img"]
    toolbarButtons: [
      {
        button: ImageButton
      }
    ],
  }
];
```

On clicking the `ImageButton` it should allow us to enter an URL in an `input` box. We expose the methods `onButtonClick` and `showPlaceholder`. If you would like the plugin to be extensible, then you should call onButtonClick, before anything else to check if the user wants to handle this action. And then on hitting enter, the image should get inserted in the editor.

```javascript
import React from "react";
import Button from "../../components/Button";
import ImageInput from "./ImageInput";

const ImageButton = ({ editor, callbacks }) => {
    if (!editor) return <span />;
    return (
        <Button
            icon="image"
            onMouseDown={e => {
                // check if the user wants to handle this action
                const hookCalled = callbacks.onButtonClick(e, "img", callbacks);
                if (hookCalled) {
                    return;
                }
                e.preventDefault();
                callbacks.showPlaceholder(ImageInput);
            }}
        />
    );
};

export default ImageButton;
```

`showPlaceholder` will internally pass the props `onComplete`, `editor` and `ref`. The `ref` is only responsible to focus the input box automatically. So it is optional. The `onComplete` hook tells the editor that the input box work is done and can be removed.

The ImageInput component may look like this:

```javascript
const ImageInput = React.forwardRef(({ onComplete, editor, node }, ref) => {
    const [url, setUrl] = useState("");
    return (
        <input
            ref={ref}
            value={url}
            onChange={e => setUrl(e.target.value)}
            onKeyUp={e => {
                if (e.keyCode == 13) {
                    // remove the input box and insert the image
                    onComplete();
                    if (url) {
                        editor
                            .insertInline({
                                type: "img",
                                data: { src, align, title }
                            })
                            .wrapBlock({ type: "figure", data: { src } });
                    }
                    editor.focus();
                }
            }}
            type="text"
            placeholder="Paste an Image link and press Enter"
        />
    );
});
```

At this point, we have already inserted the image in the slate document. However, slate does not know how to render this image. For that, we will add the render method in the configuration.

```javascript
export default [
  {
    type: "block",
    tag: "node",
    identifier: ["img"]
    toolbarButtons: [
      {
        button: ImageButton
      }
    ],
    render: ImageNode,
  }
];
```

And the `ImageNode` component will render the image tag.

```javascript
export default ({ node, attributes }) => {
    return <img width="100%" src={node.data.get("src")} {...attributes} />;
};
```

**Pasting HTML containing image**

It is also the responsible of the plugin to render the content when html buffer is pasted. For that we define a set of rules that are understood by slatejs.

First we will write a deserializer which will read the buffer and create an object in the slate document.

```javascript
export default [
  {
    type: "block",
    tag: "node",
    identifier: ["img"]
    toolbarButtons: [
      {
        button: ImageButton
      }
    ],
    render: ImageNode,
    rules: {
      deserialize: (el, next) => {
        if (el.tagName !== "IMG") return;
        return {
          object: "inline",
          type: "img",
          nodes: next(el.childNodes),
          data: {
            align: el.getAttribute("align"),
            title: el.getAttribute("title"),
            src: el.getAttribute("src")
          }
        };
      }
    }
  }
];
```

You can read more about this in [slatejs documentation](https://docs.slatejs.org/walkthroughs/saving-and-loading-html-content).

