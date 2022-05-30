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

Create your script:

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

Execute your script:

```js
// run your script elsewhere
import myScript from 'wher-your-export';

await  myScript.run();
// use an iterator if you want to control execution
const it =  myScript.it();
```

Test your script easily:

```js
// in a test file
import myScript from 'wher-your-export';

test('should read a file', () => {
  const fakeReadFile = spy();
  myScript.overwriteInjected({
      fs: {
        readFile: fakeReadFile
      }
  });
  myScript.overwriteOptions({
    logger: false
  });
  
  await myScript.run();
  
  expect(fakeReadFile.calls.length).toEqual(1);
});
```

In the console it should output (should be polished):

```bash
==========<sequential script>===========
| step number 1 | 🏎 Start
| step number 1 | INFO - Running ...
| step number 1 | INFO - Env, dev
| step number 1 | 🏁 Finished
| ------------------
| step number 2 | 🏎 Start
| step number 2 | INFO - Running ...
| step number 2 | ERROR - end
| step number 2 | ERROR - Exited with code 34 
| step number 2 | 🏁 Finished
| ------------------
==========<sequential script>===========
```

### Script with concurrency 

Create your script:

```js
import { Builder } from '@tonygo/nasiyu';
import fs from 'fs/promises'

const myScript = new Builder({
  name: 'script with concurrency',
  concurrency: 'default',
});

myScript.addStep(
  'step number 1',
  async ({ logger }) => {
    setTimeout(() => {
      logger.info('bar')
    }, 2000)
  }
);

myScript.addStep(
  'step number 2',
  async ({ logger }) => {
    logger.info('foo')
  }
);

export myScript;
```

In the console it should output unordered logs (should be polished):

```bash
==========<sequential script>===========
| step number 1 | 🏎 Start
| step number 1 | INFO - Running ...
| step number 2 | 🏎 Start
| step number 2 | INFO - Running ...
| step number 2 | INFO - foo
| step number 2 | 🏁 Finished
| step number 1 | INFO - foo
| step number 1 | 🏁 Finished
==========<sequential script>===========
```


## API

## const script = new Builder(options: BuilderOptions): Script;

```typescript
interface BuilderOptions<Injected = Record<string, any>> {
  name: string;
  description?: string;
  inject?: Injected,
  concurrency?: 'default' | true | number, // default false
  logger?: boolean // default true
}

interface Script {
  /**
  * Invoke this method to run the script
  */
  run: () => Promise<boolean>;
  
  /**
  * Return an iterator to execute step in a lazy way
  * Note: only available in sequential mode
  */
  it: () => Iterable<boolean>;
  
  /**
  * Overwrite injected object
  */
  overwriteInjected: (dependencies: Injected) => void;
  
  /**
  * Overwrite options
  */
  overwriteOptions: (options: BuilderOptions) => void;
}
```
