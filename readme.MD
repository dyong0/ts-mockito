# ts-mockito

Mocking library for TypeScript inspired by http://mockito.org/

## Main features


* Strongly typed
* IDE autocomplete
* Mock creation (`mock`)
* Changeing mock behavior (`when`) via:
	* `thenReturn` - return value
	* `thenThrowsError` - throw an error
* Checking if methods were called with given arguments (`verify`)
	* `anything`, `notNull`, `anyString`, `arrayContainig` etc. - for more flexible comparision
	* `once`, `twice`, `times`, `atLeast` etc. - allows call count verification
* Recording multiple behaviors

## Usage

### Basics
``` typescript
// Creating mock
let mockedFoo:Foo = mock(Foo);

// Getting instance from mock
let foo:Foo = instance(mockedFoo);

// Using instance in source code
foo.getBar(3);
foo.getBar(5);

// Explicit, readable verification
verify(fooMock.getBar(3)).called();
verify(fooMock.getBar(5)).called();
```

### Stubbing method calls

``` typescript
// Creating mock
let mockedFoo:Foo = mock(Foo);

// stub method before execution
when(mockedFoo.getBar(3)).thenReturn('three');

// Getting instance
let foo:Foo = instance(mockedFoo);

// prints three
console.log(foo.getBar(3));

// prints null, because "getBar(999)" was not stubbed
console.log(foo.getBar(999));
```

### Call count verification

``` typescript
// Creating mock
let mockedFoo:Foo = mock(Foo);

// Getting instance
let foo:Foo = instance(mockedFoo);

// Some calls
foo.getBar(1);
foo.getBar(2);
foo.getBar(2);
foo.getBar(3);

// Call count verification
verify(mockedFoo.getBar(1)).once();               // was called with arg === 1 only once
verify(mockedFoo.getBar(2)).twice();              // was called with arg === 2 exactly two times
verify(mockedFoo.getBar(between(2, 3))).thrice(); // was called with arg beween 2-3 exactly three times
verify(mockedFoo.getBar(anyNumber()).time(4);     // was called with any number arg exactly four times
verify(mockedFoo.getBar(2)).atLeast(2);           // was called with arg === 2 min two times
verify(mockedFoo.getBar(1)).atMoast(1);           // was called with arg === 1 max one time
verify(mockedFoo.getBar(4)).never();              // was never called with arg === 4
```

### Recording multiple behaviors

If more than one behavior is set, first matching is executed and removed

``` typescript
let mockedFoo:Foo = mock(Foo);

when(mockedFoo.getBar(anyNumber())).thenReturn('one');
when(mockedFoo.getBar(anyNumber()).thenReturn('two');
when(mockedFoo.getBar(anyNumber())).thenReturn('three');

let foo:Foo = instance(mockedFoo);

console.log(foo.getBar(1));	// one
console.log(foo.getBar(1));	// two
console.log(foo.getBar(1));	// three
console.log(foo.getBar(1));	// null - no more behaviors defined
```

Another example with specific values


``` typescript
let mockedFoo:Foo = mock(Foo);

when(mockedFoo.getBar(1)).thenReturn('one');
when(mockedFoo.getBar(1)).thenReturn('second time one');
when(mockedFoo.getBar(2)).thenReturn('two');

let foo:Foo = instance(mockedFoo);

console.log(foo.getBar(1));	// one
console.log(foo.getBar(1));	// second time one
console.log(foo.getBar(1));	// null - no more behaviors for arg === 1 defined
console.log(foo.getBar(2));	// two
console.log(foo.getBar(2));	// null - no more behaviors for arg === 2 defined
```

### Thanks

* Szczepan Faber (https://www.linkedin.com/in/szczepiq) 