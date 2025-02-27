# Quick starts

Demo
https://quickstarts.netlify.app/

## Install

```bash
yarn add @patternfly/quickstarts
```

### Peer dependencies

If your project doesn't already have these installed, you'll need:
```bash
yarn add @patternfly/react-core showdown
```

### Stylesheets

In your web-apps entry point, add these stylesheets (these should be imported before modules from @patternfly/quickstarts are imported):

```js
// base styles/variables for PatternFly-react
import '@patternfly/react-core/dist/styles/base.css';
// quick starts styles
import '@patternfly/quickstarts/dist/quickstarts.min.css';
```

## Usage example

Note: You can also view this example on [codesandbox](https://codesandbox.io/s/patternflyquickstarts-1386f?file=/src/App.js)!

In your main app file wrap your application with the QuickStartContextProvider and the QuickStartDrawer.

```js
import "./styles.css";

// base styles/variables for PatternFly-react
import "@patternfly/react-core/dist/styles/base.css";
// quick starts styles
import "@patternfly/quickstarts/dist/quickstarts.min.css";

import * as React from "react";
import {
  QuickStartDrawer,
  QuickStartContext,
  QuickStartContextProvider,
  QuickStartCatalogPage,
  useLocalStorage
} from "@patternfly/quickstarts";
import jsYaml from "js-yaml";
// quick start files could be yaml files or js files, or really anything,
// as long as they get parsed out to the expexted JSON format
import quickstartOne from "./quick-starts/quickstart-one.yaml";
import quickstartTwo from "./quick-starts/quickstart-two.yaml";
import quickstartThree from "./quick-starts/quickstart-three";

const App = () => {
  const allQuickStarts = [
    jsYaml.load(quickstartOne),
    jsYaml.load(quickstartTwo),
    quickstartThree
  ];
  // You can use the useLocalStorage hook if you want to store user progress in local storage
  // Otherwise you can use React.useState here or another means (backend) to store the active quick start ID and state
  const [activeQuickStartID, setActiveQuickStartID] = useLocalStorage(
    "quickstartId",
    ""
  );
  const [allQuickStartStates, setAllQuickStartStates] = useLocalStorage(
    "quickstarts",
    {}
  );
  const valuesForQuickstartContext = {
    // array of quick starts
    allQuickStarts,
    // the next 4 items tie the quick start state to the main app
    activeQuickStartID,
    setActiveQuickStartID,
    allQuickStartStates,
    setAllQuickStartStates,
    footer: {
      // don't show card footers
      show: false
    }
  };

  return (
    <QuickStartContextProvider value={valuesForQuickstartContext}>
      <QuickStartDrawer>
        <SomeNestedComponent />
        <QuickStartCatalogPage
          showFilter
          title="Quick starts"
          hint="Learn how to create, import, and run applications with step-by-step instructions and tasks."
        />
      </QuickStartDrawer>
    </QuickStartContextProvider>
  );
};

const SomeNestedComponent = () => {
  const qsContext = React.useContext(QuickStartContext);
  // the quick start ID is defined in the quick start object's metadata.name field
  return (
    <button onClick={() => qsContext.setActiveQuickStart("node-with-s2i")}>
      Toggle a quick start from another component
    </button>
  );
};

export default App;
```

## Quick starts format

Quick starts are parsed as markdown. To write your own quick start, if you use Typescript you can [check out the type definition here](https://github.com/patternfly/patternfly-quickstarts/blob/d52b194119f1ff16e69bf589d49a14931a19ac4b/packages/module/src/utils/quick-start-types.ts#L6).

A basic quick start has this structure:
```yaml
metadata:
  name: id-of-this-quick-start
spec:
  displayName: Get started with Node
  durationMinutes: 10
  description: 'Import a Node Application from git, build, and deploy it onto OpenShift.'
  introduction: >-
    **Node.js** is based on the V8 JavaScript engine and allows you to write
    server-side JavaScript applications. It provides an I/O model based on
    events and non-blocking operations that enables you to write efficient
    applications.
  tasks:
    - title: Create a Node application
      description: First task description
      review:
        failedTaskHelp: This task isn’t verified yet. Try the task again.
        instructions: >-
          The application is represented by the light grey area with the white border. The deployment is a white circle. Verify that the application was successfully created.
    - title: View the build status
      description: Second task description
      review:
        failedTaskHelp: This task isn’t verified yet. Try the task again.
        instructions: >-
          This build may take a few minutes. When it's finished, a **Complete** badge will surface on the page header beside build name **nodejsrest-http-redhat-1**. Did this badge appear?
  conclusion: Your Node application is deployed and ready.
```

For more examples of quick starts, [you can go here](https://github.com/patternfly/patternfly-quickstarts/tree/main/packages/dev/src/quickstarts-data/mocks/yamls).

### Highlighting elements

You can highlight an element on the page from within a quick start. The element that should be highlightable needs a data-quickstart-id attribute. Example:
```
<button data-quickstart-id="special-btn">Click me</button>
```

In the quick start task description, you can add this type of markdown to target this element:
```
Highlight [special button]{{highlight special-btn}}
```

### Copyable text

You can have inline or block copyable text.

#### Inline copyable text example
```
`echo "Donec id est ante"`{{copy}}
```

#### Multiline copyable text example
```
    ```
      First line of text.
      Second line of text.
    ```{{copy}}
```

#### 