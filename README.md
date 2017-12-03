# aurelia-store
Aurelia single state store based on RxJS

THIS IS WORK IN PROGRESS, DO NOT USE YET FOR PRODUCTION

## Install
Install the npm dependency via

```bash
npm install aurelia-store rxjs
```

## Aurelia CLI Support
If your Aurelia CLI build is based on RequireJS or SystemJS you can setup the plugin using the following dependency declaration:

```json
...
"dependencies": [
  {
    "name": "aurelia-store",
    "path": "../node_modules/aurelia-store/dist/amd",
    "main": "aurelia-store"
  },
  {
    "name": "rxjs",
    "path": "../node_modules/rxjs",
    "main": false
  }
]
```

## Configuration
In your `main.ts` you'll have to register the Store using a custom entity as your State type:

```typescript
import {Aurelia} from 'aurelia-framework'
import environment from './environment';

export interface State {
  frameworks: string[];
}

export function configure(aurelia: Aurelia) {
  aurelia.use
    .standardConfiguration()
    .feature('resources');

  ...

  const initialState: State = {
    frameworks: ["Aurelia", "React", "Angular"]
  };

  aurelia.use.plugin("aurelia-store", initialState);  // <----- REGISTER THE PLUGIN

  aurelia.start().then(() => aurelia.setRoot());
}
```


## Usage
Once the plugin is installed and configured you can use the Store by injecting it via constructor injection.

You register actions (reducers) by with methods, which get the current state and have to return the modified next state.

An example VM and View can be seen below:

```typescript
import { autoinject } from 'aurelia-dependency-injection';
import { State } from './state';
import { Store } from "aurelia-store";

const demoAction = (state: State) => {
  const newState = Object.assign({}, state);
  newState.frameworks.push("PustekuchenJS");

  return newState;
}

@autoinject()
export class App {

  public state: State;

  constructor(private store: Store<State>) {
    this.store.registerAction("DemoAction", demoAction);

    setTimeout(() => this.store.dispatch(demoAction), 2000);
  }

  attached() {
    // this is the single point of data subscription, the state inside the component will be automatically updated
    // no need to take care of manually handling that. This will also update all subcomponents
    this.store.state.subscribe(
      state => this.state = state
    );
  }
}
```

```html
<template>
  <h1>Frameworks</h1>

  <ul>
    <li repeat.for="framework of state.frameworks">${framework}</li>
  </ul>
</template>

```

## Passing parameters to actions
You can provide parameters to your actions by adding them after the initial state parameter. When dispatching provide your values which will be spread to the actual reducer.

```typescript
// additional parameter
const greetingAction = (state: State, greetingTarget: string) => {
  const newState = Object.assign({}, state);
  newState.target = greetingTarget;

  return newState;
}

...

// dispatching with the value for greetingTarget
this.store.dispatch(greetingAction, "zewa666");
```


## Async actions
You may also register actions which resolve the newly created state with a promise.


## Acknowledgement
Thanks goes to Dwayne Charrington for his Aurelia-TypeScript starter package https://github.com/Vheissu/aurelia-typescript-plugin

## Further info
If you want to learn more about state containers in Aurelia take a look at this article from [Pragmatic Coder](http://pragmatic-coder.net/using-a-state-container-with-aurelia/)