Saul
====
Dynamic unit test generation for pure javascript functions. 

![Demo](https://s3.amazonaws.com/nadeesha-misc/saul+demo+gif.gif)

# What is it?

Saul like to generate tests for your pure functions when you don't feel like writing them yourself.

Suppose that you have a simple function like this.

```js
function shouldCallSaul(threatLevel) {
    if (threatLevel === 'imminent') {
        return true;
    }

    return false;
}
```

But now, you got a problem. This NEEDS to be tested. Writing a test with `describe` and `it` is fine - but your test file would be longer than the function itself. Not a problem? I'm happy for you. You're better than most of us. But the rest of us - we better call Saul.

Imagine that you can just leave a comment out in the file with...

```js
// @t "should call saul when the threat is imminent"        shouldCallSaul('imminent') equals true
// @t "should not call saul when threat is not imminent"    shouldCallSaul('nodanger') equals false
```

...And it'll dynamically generate the tests for you when you run your test framework? (ex: `mocha`). Well, in a shell, that's what it does. Your fully tested pure function would now look like:

```js
// @t "should call saul when the threat is imminent"        shouldCallSaul('imminent') equals true
// @t "should not call saul when threat is not imminent"    shouldCallSaul('nodanger') equals false
function shouldCallSaul(threatLevel) {
    if (threatLevel === 'imminent') {
        return true;
    }

    return false;
}
```

# What more can it do?

## Checking a `throw`

```js
// @t "throws on null engine" executeTest({engine: null}) throws Error
export const executeTest = (executableParams: ExecutableParams) =>
  executableParams.engine(1);
```

## Checking whether a test spy is called

```js
// @t "reads file" getFileContent('fakeFilePath', 'spyFoo') calls-spy-with fakeFilePath
export const getFileContent = (
  filepath: string,
  readFile: typeof fs.readFileSync = fs.readFileSync
): string => readFile(filepath, 'utf-8');
```

## Comparing the DOM for React-based tests (using emmet expressions)

```js
// @t "has new pill" Thumbnail({isNew: true}) contains-dom div#foo{New}
const Pill = ({isNew}) => (
    <div>
        {isNew && <div className={styles.pill} id={'foo'}>New</div>}
    </div>
)
```

And more! See: [extending saul](#extending).

## Extending Saul <a name="extending"></a>

Then engines are the "comparator" in the tests.

```js
// @t "has new pill" Thumbnail({isNew: true}) contains-dom div#foo{New}                   ===> contains-dom
// @t "reads file" getFileContent('fakeFilePath', 'spyFoo') calls-spy-with fakeFilePath   ===> calls-spy-with
// @t "throws on null engine" executeTest({engine: null}) throws Error                    ===> throws
```

They are handled by the file of that name in `src/engines`. (Example: `src/engines/contains-dom.js`)

The "engines", are responsible for generating the tests. So, as long as you build a custom engine - it can pretty much test anything. 

The default engines can do a few cool things out of the box. (check the `src/engines/` directory). You can always write your own engines and put them in your `customEnginesDir` defined in `.saulrc`.

## Installation

1. Install Saul as a dev dependency: `yarn add saul -d`
2. Create a `.saulrc` in the root.

example:
```js
{
    "fileGlob": "src/**/*.js",                      // files that contain the saul comments
    "customEnginesDir": "./src/custom-saul-engines" // optional: dir where you will put custom engine .js files
}
```

3. Invoke saul from your test. 

Example: If you have some mocha tests already, your `npm test` would look like: `mocha src/**/.js`. Simple add Saul's bin (`node_modules/.bin/saul`) right at the end:

```sh
mocha lib/*.test.js" node_modules/.bin/saul
```

## Usage with Babel

Any transformation that you apply to your tests will be inherited by saul when you run your tests. If you're running babel, this will include anything that you define in your local `.babelrc`.

For an instance, if you want to feed babel-transformed files to mocha, you will invoke mocha with `mocha --compilers js:babel-register`. You can simply add saul to the end of the command. (`mocha --compilers js:babel-register node_modules/.bin/saul`) - and things will Just Work™.

# Examples

Just give a read through this repo. Saul is tested with Saul! :rocket:

# Contributions

Please! Here are som TODOs that needs being done.

- [ ] More engines! (If you would like to contribute an engine, please take a look at the engine files at `src/engines`)
- [ ] Documentation on writing engines.
- [ ] Extending the parsers for fixtures
- [ ] Better error handling for engines
- [ ] Tests for existing engines

