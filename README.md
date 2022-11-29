# Build an editor using CodeMirror with linting and autocomplete

### Workshop Agenda

* Why CodeMirror and how to choose a library
* Basic setup for CodeMirror
* Adding out of the box themes and custom ones
* Adding your own linting
* Adding general autocomplete
* Adding more complex autocomplete
* Adding completely custom autocomplete


### 1. Before you begin

Build alongside us using Stackblitz `https://stackblitz.com/` and create a React instance

Or use your own environment and make sure you have `node` and `yarn` installed to get started.

### 2. Install the main react-codemirror package or add them directly on Stackblitz

```bash
yarn add @uiw/react-codemirror

yarn add @codemirror/lang-javascript
```

### 3. Your basic setup to accept JavaScript

```js
import React from 'react';
import CodeMirror from '@uiw/react-codemirror';
import { javascript } from '@codemirror/lang-javascript';
import './style.css';

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

Add some basic styling in your `style.css` file

```css
.cm-theme {
  margin: 10px 0;
}

.cm-editor {
  border: 1px #aaa solid;
}
```

### 4. Add multiple editors supporting HTML and CSS

```js
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

```js
import { sublime } from '@uiw/codemirror-theme-sublime';
import { githubDark } from '@uiw/codemirror-theme-github';
import { okaidia } from '@uiw/codemirror-theme-okaidia';
```

Your code should look like this:

```js
import React from 'react';
import './style.css';

import CodeMirror from '@uiw/react-codemirror';
import { javascript } from '@codemirror/lang-javascript';
import { html } from '@codemirror/lang-html';
import { css } from '@codemirror/lang-css';
import { sublime } from '@uiw/codemirror-theme-sublime';
import { githubDark } from '@uiw/codemirror-theme-github';
import { okaidia } from '@uiw/codemirror-theme-okaidia';

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
          theme={sublime}
        />
      </div>
      <div>
        <CodeMirror
          value="<html> </html>"
          height="200px"
          extensions={[html()]}
          onChange={onChange}
          theme={githubDark}
        />
      </div>
      <div>
        <CodeMirror
          value="h1 {}"
          height="200px"
          extensions={[css()]}
          onChange={onChange}
          theme={okaidia}
        />
      </div>
    </div>
  );
}
```

### 6. Add your own custom theme

Create a new file called `customTheme.js`

```js
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

Your code should look like this now:

```js
import React from 'react';
import './style.css';

import CodeMirror from '@uiw/react-codemirror';
import { javascript } from '@codemirror/lang-javascript';
import customTheme from './customTheme';

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
          value="console.log('hello world!');"
          height="200px"
          extensions={[javascript({})]}
          onChange={onChange}
          theme={customTheme}
        />
      </div>
    </div>
  );
}
```

### 7. Save to localStorage with undo history

By editing the `onChange` and `initialState` props and by using the `localStorage` Web API, we can make the editor state persist across refreshes.

```js
import React from 'react';
import './style.css';

import CodeMirror from '@uiw/react-codemirror';
import { javascript } from '@codemirror/lang-javascript';
import { githubDark } from '@uiw/codemirror-theme-github';

import { historyField } from '@codemirror/commands';

// See [toJSON](https://codemirror.net/docs/ref/#state.EditorState.toJSON) documentation for more details
const stateFields = { history: historyField };

export default function App() {
  const serializedState = localStorage.getItem('myEditorState');
  const value = localStorage.getItem('myValue') || '';

  return (
    <CodeMirror
      value={value}
      height={'300px'}
      theme={githubDark}
      extensions={[javascript({})]}
      initialState={
        serializedState
          ? {
              json: JSON.parse(serializedState || ''),
              fields: stateFields,
            }
          : undefined
      }
      onChange={(value, viewUpdate) => {
        localStorage.setItem('myValue', value);

        const state = viewUpdate.state.toJSON(stateFields);
        localStorage.setItem('myEditorState', JSON.stringify(state));
      }}
    />
  );
}
```

### 7. Add your own linter

Create an export a file called `jsLinter.js` and paste the following into it (then update depedencies)

```js
import { linter } from '@codemirror/lint';
import { JSHINT as jshint } from 'jshint';

/**
 * Sets up the javascript linter. Documentation: https://codemirror.net/examples/lint/
 */
const jsLinter = (lintOptions) => {
  return linter((view) => {
    const diagnostics = [];
    const codeText = view.state.doc.toJSON();
    jshint(codeText, lintOptions);
    const errors = jshint?.data().errors;

    if (errors && errors.length > 0) {
      errors.forEach((error) => {
        const selectedLine = view.state.doc.line(error.line);

        const diagnostic = {
          from: selectedLine.from,
          to: selectedLine.to,
          severity: 'error',
          message: error.reason,
        };

        diagnostics.push(diagnostic);
      });
    }
    return diagnostics;
  });
};

export default jsLinter;

```

Afterwards, update your main App.js code 

```js
import React from 'react';
import './style.css';

import CodeMirror from '@uiw/react-codemirror';
import { javascript } from '@codemirror/lang-javascript';
import { githubDark } from '@uiw/codemirror-theme-github';
import { historyField } from '@codemirror/commands';
import { lintGutter } from '@codemirror/lint';

import jsLinter from './jsLinter';

// See [toJSON](https://codemirror.net/docs/ref/#state.EditorState.toJSON) documentation for more details
const stateFields = { history: historyField };

export default function App() {
  const serializedState = localStorage.getItem('myEditorState');
  const value = localStorage.getItem('myValue') || '';

  const lintOptions = {
    esversion: 11,
    browser: true,
  };

  return (
    <CodeMirror
      value={value}
      height={'300px'}
      theme={githubDark}
      extensions={[javascript({}), jsLinter(lintOptions), lintGutter()]}
      initialState={
        serializedState
          ? {
              json: JSON.parse(serializedState || ''),
              fields: stateFields,
            }
          : undefined
      }
      onChange={(value, viewUpdate) => {
        localStorage.setItem('myValue', value);

        const state = viewUpdate.state.toJSON(stateFields);
        localStorage.setItem('myEditorState', JSON.stringify(state));
      }}
    />
  );
}
```

You can be less strict using the following `lintOptions`

```js
asi: true,
expr: true,
undef: false,
```

### 8. Add your own autocomplete

Create a new file called `completeJSDoc.js` and put the following code inside of it

```js
import { syntaxTree } from '@codemirror/language';

const tagOptions = [
  'constructor',
  'deprecated',
  'link',
  'param',
  'returns',
  'type',
].map((tag) => ({ label: '@' + tag, type: 'keyword' }));

export default function completeJSDoc(context) {
  let nodeBefore = syntaxTree(context.state).resolveInner(context.pos, -1);
  if (
    nodeBefore.name != 'BlockComment' ||
    context.state.sliceDoc(nodeBefore.from, nodeBefore.from + 3) != '/**'
  )
    return null;
  let textBefore = context.state.sliceDoc(nodeBefore.from, context.pos);
  let tagBefore = /@\w*$/.exec(textBefore);
  if (!tagBefore && !context.explicit) return null;
  return {
    from: tagBefore ? nodeBefore.from + tagBefore.index : context.pos,
    options: tagOptions,
    validFor: /^(@\w*)?$/,
  };
}
```

Your main code should look like this now

```js
import React from 'react';
import './style.css';

import CodeMirror from '@uiw/react-codemirror';
import { javascript, javascriptLanguage } from '@codemirror/lang-javascript';
import { githubDark } from '@uiw/codemirror-theme-github';
import { historyField } from '@codemirror/commands';
import { lintGutter } from '@codemirror/lint';

import jsLinter from './jsLinter';
import completeJSDoc from './completeJSDoc';

// See [toJSON](https://codemirror.net/docs/ref/#state.EditorState.toJSON) documentation for more details
const stateFields = { history: historyField };

export default function App() {
  const serializedState = localStorage.getItem('myEditorState');
  const value = localStorage.getItem('myValue') || '';

  const lintOptions = {
    esversion: 11,
    browser: true,
    asi: true,
    expr: true,
    undef: false,
  };

  const jsDocCompletions = javascriptLanguage.data.of({
    autocomplete: completeJSDoc,
  });

  return (
    <CodeMirror
      value={value}
      height={'300px'}
      theme={githubDark}
      extensions={[
        javascript({}),
        jsLinter(lintOptions),
        lintGutter(),
        jsDocCompletions,
      ]}
      initialState={
        serializedState
          ? {
              json: JSON.parse(serializedState || ''),
              fields: stateFields,
            }
          : undefined
      }
      onChange={(value, viewUpdate) => {
        localStorage.setItem('myValue', value);

        const state = viewUpdate.state.toJSON(stateFields);
        localStorage.setItem('myEditorState', JSON.stringify(state));
      }}
    />
  );
}

```


### 9. Let's add a completion for the entire global scope

Createa a file called `completeFromGlobalScope.js`

```js
import { syntaxTree } from '@codemirror/language';
import { CompletionContext } from '@codemirror/autocomplete';

const completePropertyAfter = ['PropertyName', '.', '?.'];
const dontCompleteIn = [
  'TemplateString',
  'LineComment',
  'BlockComment',
  'VariableDefinition',
  'PropertyDefinition',
];

export default function completeFromGlobalScope(context: CompletionContext) {
  let nodeBefore = syntaxTree(context.state).resolveInner(context.pos, -1);

  if (
    completePropertyAfter.includes(nodeBefore.name) &&
    nodeBefore.parent?.name == 'MemberExpression'
  ) {
    let object = nodeBefore.parent.getChild('Expression');
    if (object?.name == 'VariableName') {
      let from = /\./.test(nodeBefore.name) ? nodeBefore.to : nodeBefore.from;
      let variableName = context.state.sliceDoc(object.from, object.to);
      if (typeof window[variableName] == 'object')
        return completeProperties(from, window[variableName]);
    }
  } else if (nodeBefore.name == 'VariableName') {
    return completeProperties(nodeBefore.from, window);
  } else if (context.explicit && !dontCompleteIn.includes(nodeBefore.name)) {
    return completeProperties(context.pos, window);
  }
  return null;
}

function completeProperties(from: number, object: Object) {
  let options = [];
  for (let name in object) {
    options.push({
      label: name,
      type: typeof object[name] == 'function' ? 'function' : 'variable',
    });
  }
  return {
    from,
    options,
    validFor: /^[\w$]*$/,
  };
}

````

Your code should look like this now

```js
import React from 'react';
import './style.css';

import CodeMirror from '@uiw/react-codemirror';
import { javascript, javascriptLanguage } from '@codemirror/lang-javascript';
import { githubDark } from '@uiw/codemirror-theme-github';
import { historyField } from '@codemirror/commands';
import { lintGutter } from '@codemirror/lint';

import jsLinter from './jsLinter';
import completeJSDoc from './completeJSDoc';
import completeFromGlobalScope from './completeFromGlobalScope';

// See [toJSON](https://codemirror.net/docs/ref/#state.EditorState.toJSON) documentation for more details
const stateFields = { history: historyField };

export default function App() {
  const serializedState = localStorage.getItem('myEditorState');
  const value = localStorage.getItem('myValue') || '';

  const lintOptions = {
    esversion: 11,
    browser: true,
    asi: true,
    expr: true,
    undef: false,
  };

  const jsDocCompletions = javascriptLanguage.data.of({
    autocomplete: completeJSDoc,
  });

  const globalScopeCompletions = javascriptLanguage.data.of({
    autocomplete: completeFromGlobalScope,
  });

  return (
    <CodeMirror
      value={value}
      height={'300px'}
      theme={githubDark}
      extensions={[
        javascript({}),
        jsLinter(lintOptions),
        lintGutter(),
        jsDocCompletions,
        globalScopeCompletions,
      ]}
      initialState={
        serializedState
          ? {
              json: JSON.parse(serializedState || ''),
              fields: stateFields,
            }
          : undefined
      }
      onChange={(value, viewUpdate) => {
        localStorage.setItem('myValue', value);

        const state = viewUpdate.state.toJSON(stateFields);
        localStorage.setItem('myEditorState', JSON.stringify(state));
      }}
    />
  );
}
```
