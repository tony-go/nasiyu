<h1 align="center">nasiyu</h1>
<h3 align="center">Opinionated framework for writing reliable and testable scripts.</h3>

## ⭐️ Features
- Developer friendly
- Testable (dependency injection)
- Typescript ready
- Iterator ready
- Sequential/parallel execution 

## 📦 Install

```bash
$ npm install @tonygo/nasiyu
```

## 🧰 Usage

### Sequential script

```js
import { Builder } from '@tonygo/nasiyu';
import fs from 'fs/promises'

const myScript = new Builder({
  name: 'sequential script',
  inject: {
    fs,
    env: 'dev'
  }
});

myScript.addStep(
  'step number 1',
  async ({ injected, logger }) => {
    logger.info('Env', injected.end);
    const result = await injected.fs.readFile('temp', { encoding: 'utf-8' });
    return result;
  }
);

myScript.addStep(
  'step number 2',
  async({ prev, exit, logger }) => {
    if (prev === 'stop') {
      logger.error('end');
      return exit(34);
    }
    logger.info('continue ...');
  }
);

export myScript;
```

```js
// run your script elsewhere
import myScript from 'wher-your-export';

await  myScript.run();
// use an iterator if you want to control execution
const it =  myScript.it();
```

```js
// in a test file
import myScript from 'wher-your-export';

test('should read a file', () => {
  const fakeReadFile = spy();
   myScript.overwriteInject({
      fs: {
        readFile: fakeReadFile
      }
  });
  
  await myScript.run();
  
  expect(fakeReadFile.calls.length).toEqual(1);
});
```

In the console it should output (should be polished):

```bash
==========<sequential script>===========
|-----> 🏎- step number1 starts
|-----> Info - Running ...
|-----> Info - "Env, dev"
|-----> Success - Finished
|-----> 🏎- step number2 starts
|-----> Info - Running ...
|-----> Error - end 
|-----> Error - Exited with code 34 
==========<sequential script>===========
```

### Script with concurrency 

> todo(tonygo)

## API

> todo(tonygo)
