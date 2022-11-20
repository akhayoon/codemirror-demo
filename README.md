# Build an editor using CodeMirror with linting and autocomplete

### 1. Before you begin

Build alongside us using Stackblitz `https://stackblitz.com/` and create a React instance

Or use your own environment and make sure you have `node` and `yarn` installed to get started.

### 2. Install the main react-codemirror package

```
yarn add @uiw/react-codemirror

yarn addd @codemirror/lang-javascript
```

### 3. Your basic setup to accept JavaScript

```
import React from 'react';
import CodeMirror from '@uiw/react-codemirror';
import { javascript } from '@codemirror/lang-javascript';

export default function App() {
  const onChange = React.useCallback((value, viewUpdate) => {
    console.log('value:', value);
  }, []);
  return (
    <CodeMirror
      value="console.log('hello world!');"
      height="200px"
      extensions={[javascript()]}
      onChange={onChange}
    />
  );
}
```

### 4. Add multiple editors supporting HTML and CSS

```
import React from 'react';
import './style.css';

import CodeMirror from '@uiw/react-codemirror';
import { javascript } from '@codemirror/lang-javascript';
import { html } from '@codemirror/lang-html';
import { css } from '@codemirror/lang-css';

export default function App() {
  const onChange = React.useCallback((value, viewUpdate) => {
    console.log('value:', value);
  }, []);
  return (
    <div>
      <div>
        <CodeMirror
          value="console.log('hello world!');"
          height="200px"
          extensions={[javascript({})]}
          onChange={onChange}
        />
      </div>
      <div>
        <CodeMirror
          value="<html> </html>"
          height="200px"
          extensions={[html()]}
          onChange={onChange}
        />
      </div>
      <div>
        <CodeMirror
          value="h1 {}"
          height="200px"
          extensions={[css()]}
          onChange={onChange}
        />
      </div>
    </div>
  );
}

```

### 5. Add pre-installed themes and customize others

Install some prebuild themes to play around with 

```
import { sublime } from '@uiw/codemirror-theme-sublime';
import { githubDark } from '@uiw/codemirror-theme-github';
import { okaidia } from '@uiw/codemirror-theme-okaidia';
```

### 6. Add your own custom theme

Create a new file called `customTheme.js`

```
import { createTheme } from '@uiw/codemirror-themes';
import { tags as t } from '@lezer/highlight';

const myTheme = createTheme({
  theme: 'light',
  settings: {
    background: '#ffffff',
    foreground: '#75baff',
    caret: '#5d00ff',
    selection: '#036dd626',
    selectionMatch: '#036dd626',
    lineHighlight: '#8a91991a',
    gutterBackground: '#fff',
    gutterForeground: '#8a919966',
  },
  styles: [
    { tag: t.comment, color: '#787b8099' },
    { tag: t.variableName, color: '#0080ff' },
    { tag: [t.string, t.special(t.brace)], color: '#5c6166' },
    { tag: t.number, color: '#5c6166' },
    { tag: t.bool, color: '#5c6166' },
    { tag: t.null, color: '#5c6166' },
    { tag: t.keyword, color: '#5c6166' },
    { tag: t.operator, color: '#5c6166' },
    { tag: t.className, color: '#5c6166' },
    { tag: t.definition(t.typeName), color: '#5c6166' },
    { tag: t.typeName, color: '#5c6166' },
    { tag: t.angleBracket, color: '#5c6166' },
    { tag: t.tagName, color: '#5c6166' },
    { tag: t.attributeName, color: '#5c6166' },
  ],
});

export default myTheme;

```
