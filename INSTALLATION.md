# Installation

The editor is available in the npm repository and can be installed using the below command.

```sh
npm install letterpad-editor
# if you are using yarn
yarn add letterpad-editor
```

You can use the editor in your project by creating a React component and importing the editor.

```js
import React from "react";
import { render } from "react-dom";
import LetterpadEditor from "letterpad-editor";

render(<LetterpadEditor />, document.getElementById("app"));
```
