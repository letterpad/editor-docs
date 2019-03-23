# Options

## Options

Below are all the options that you can pass to the editor.

#### theme

```text
string
```

Options are `light` and `dark`. \(default - `dark`\)

#### spellCheck

```text
boolean
```

Whether or not spellcheck is turned on for the editor. This is an option provided by slatejs.

#### onButtonClick

```text
onButtonClick(e: MouseEvent, type: string, callbacks: { [key: string]: any }) => boolean
```

You can overwrite a plugin's default behaviour by tapping into this hook. Below is an example

**Example**

```text
 onButtonClick = (
    e: MouseEvent,
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

#### onBeforeRender

With this hook, you can change the way a particular tag is rendered

```text
onBeforeRender = ({ type: string; props: { editor: Editor } }) => void
```

**Example**

```javascript
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

#### getCharCount

A hook to get the word count.

```text
getCharCount = (count) => void
```

