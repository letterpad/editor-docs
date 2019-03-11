# Hooks

Hooks are callback functions triggered from plugins. This allows you to overwrite a plugin's default behaviour. For eg. if you want to change the bold tag from `<strong>` to `<b>`, you can do so using one of the hooks. Currently we have three hooks that you can tap into.

### onButtonClick

```
onButtonClick(e: MouseEvent, type: string, callbacks: { [key: string]: any }) => boolean
```

**Example**

```
 onButtonClick = (
    _: MouseEvent,
    type: string,
    callbacks: { [key: string]: any }
  ) => {
    if (type === "img") {
      if (callbacks.showPlaceholder) {
        callbacks.showPlaceholder(Gallery);
        return true;
      }
    }
    return false
  };
```

### onBeforeRender

With this hook, you can change the way a particular tag is rendered

```
onBeforeRender = ({ type: string; props: { editor: Editor } }) => void
```

**Example**

```js
onBeforeRender = (props: {
    type: string,
    renderType: string
    props: { editor: Editor, children: ReactNode }
}) => {
    if (props.type === "strong") {
        return <b>{children}</b>;
    }
};
```

### getCharCount

A hook to get the word count.

```
getCharCount = (count) => void
```
