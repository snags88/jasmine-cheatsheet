# Jasmine Cheatsheet
Cheatsheet for syntax used in writing JS specs with Jasmine. **Note that this uses ES6 syntax**

#### Basic Structure
```js
describe('A sample test', () => {
  //Declare scoped variables similar to `let`
  const a = true;
  
  beforeEach( () => {
    // Test setup for enclosed examples in this scope
  });
  
  afterEach( () => {
    // Test teardown for enclosed examples in this scope
  });
  
  it('passes a simple spec', () => {
    expect(a).toBe(true);
  });
});
```

#### Basic Matchers
- `expect(1).toBe(1)`
- `expect(1).not.toBe(2)`
- `expect({a: 1, b: 2}).toEqual({a: 1, b: 2})`
- `expect('foo bar baz').toMatch(/bar/)`
- `expect(a).toBeDefined()`
- `expect(a).toBeUndefined()`
- `expect(null).toBeNull()`
- `expect(1).toBeTruthy()`
- `expect(0).toBeFalsy()`
- `expect(['foo', 'bar', 'baz']).toContain('bar')`
- `expect('foo bar baz').toContain('bar')`
- `expect(1).toBeLessThan(2)`
- `expect(2).toBeGreaterThan(1)`
- Any matcher: `expect(2).toEqual(jasmine.any(Number))`
- Partial matcher: `expect({a: 1, b: 2}).toEqual(jasmine.objectContaining({a: 1}))`

#### Error Matchers
Important usage here is that the function definition is passed into the `expect()` function. The function is not invoked. Otherwise, it would throw an exception in your test.

```js
const foo = () => {
  throw new TypeError("foo bar baz");
};

// Any error
expect(foo).toThrow(); 

// Specific errors
expect(foo).toThrowError("foo bar baz");
expect(foo).toThrowError(/bar/);
expect(foo).toThrowError(TypeError);
expect(foo).toThrowError(TypeError, "foo bar baz");
```

#### Spies
Spies are useful for seeing if a particular function was called, how many times it was called, what arguments were passed in, and to mock/stub out the function return value. _Spies in Jasmine will not automatically call the function it is spying on._

```js
const mathStuff = {
  add(x, y) {
    return x + y;
  }
}

spyOn(mathStuff, 'add'); // Spy on the object behavior

mathStuff.add(1, 2); // Call object behavior

// Assertion check
expect(mathStuff.add).toHaveBeenCalled();
expect(mathStuff.add).toHaveBeenCalledWith(1, 2);
```

- `spyOn(mathStuff, 'add').and.callThrough();` will spy and run the actual function.
- `spyOn(mathStuff, 'add').and.returnValue(100);` will spy and stub out the return value.
- `spyOn(mathStuff, 'add').and.callFake((a, b) => {return 1});` will spy and delegate calls to a different function.
- `spyOn(mathStuff, 'add').and.throwError('someError');` will spy and throw error
- `spyOn(mathstuff, 'add').and.stub();` will negate any `callThrough`s

You can also get additional information on the spy by using `calls` on the spied function.

- `mathStuff.add.calls.any()`: `false` if it hasn't been called, `true` otherwise.
- `mathStuff.add.calls.count()`: returns number of times the spy was called.
- `mathStuff.add.calls.argsFor(index)`: returns arguments for call number `index`
- `mathStuff.add.calls.allArgs()`: returns arguments for all calls.
- `mathStuff.add.calls.reset()`: clears spying.

If you don't have a specific function to spy on, but you need a spy (maybe a callback function), you can create bare spy that will respond to all the above interface.

```js
const spy = jasmine.createSpy('mySpy');

spy('hello', 'world')
expect(spy).toHaveBeenCalled();
expect(spy).toHaveBeenCalledWith('hello', 'world');
```

#### Asynchronus Specs
You can pass in a `done` variable that can be called to signal that an asynchronous test is complete. This can be done for the `beforeEach`, `it`, `afterEach` functions:

```js
it('runs async tests', (done) => {
  // Do async stuff
  done();
});
