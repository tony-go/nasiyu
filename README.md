# nasiyu
Optionated framework for writing reliable and testable scripts.

## API ideas

```js
import { ScriptManager } from '@tonygo/nasiyu';
import fs from 'fs/promises'

const manager = new ScriptManager({
  name: 'vim-install'
  decription: 'Will install common tools for vim',
  inject: {
    fs,
    env: 'dev'
  }
});

manager.addStep(
  'install brew',
  async ({ injected }) => {
    console.log(injected.env, injected.fs)
  }
);

export manager;

// far away
// await manager.run();
// or
// const it = manager.it();

// in a test file
const fakeReadFile = spy();
// manager.overwriteInject({
    fs: {
      readFile: fakeReadFile
    }
// })

```

