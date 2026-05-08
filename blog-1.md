# TypeScript "any" type is a "type safety hole", while 'unknown' the safer choice for handling unpredictable data

TypeScript is used for implementing type safety in Javascript programming. But it is very common that a certain type is not know at compile time. This is where the `any` and `unknown` is used. Even they practically applied to same scenarios but they have their own pros and cons. Developers should choose which one to use in the case that a certain is unknown.

## The "any" Type: A Type Safety Hole

The `any` type is a way to disable type checking. When `any` type is assined, TypeScript ignores type checking and anything can be assigned throughout the code for the respective variable.

```typescript
let value: any = "hello";
value.toUpperCase(); // ✓ OK
value.toFixed(2); // ✓ OK (but will fail at runtime)
value.someMethod(); // ✓ OK (but might not exist)
```

### Why is it a Type Safety Hole?

The problem with `any` is that it **bypasses type checking completely**. This creates several issues:

1. Invalid operations can cause runtime errors
2. No autocomplete or suggestion support in the editor
3. Type uncertainity is spread throughout the code
4. Harder to debug in case of any issue

## The "unknown" Type: The Safer Alternative

The `unknown` type defines unknown type, similar to `any`. But TypeScript is more strict about `unknown` type. Without narrowing the type of an unknown type the usage remains very limited

```typescript
let value: unknown = "hello";
value.toUpperCase(); // ✗ Error: Object is of type 'unknown'
```

### Why is it Safer?

`unknown` forces developes to narrow down about what the type actually is:

1. Must check type before using
2. Easier handling
3. Better autocomplete and suggestions in editors
4. Less likely to cause error at runtime

## Some usage examples for unknown types

### Method 1: typeof Guard

```typescript
function process(value: unknown) {
  if (typeof value === "string") {
    // value is string here
    console.log(value.toUpperCase());
  } else if (typeof value === "number") {
    // value is number here
    console.log(value.toFixed(2));
  } else {
    // value is unknown here
    console.log("Unknown type");
  }
}
```

### Method 2: instanceof Check

```typescript
class User {
  constructor(public name: string) {}
}

function greet(obj: unknown) {
  if (obj instanceof User) {
    // obj is User here
    console.log(`Hello, ${obj.name}`);
  }
}
```

### Method 3: Type Predicates

```typescript
function isString(value: unknown): value is string {
  return typeof value === "string";
}

function process(value: unknown) {
  if (isString(value)) {
    // value is string here
    console.log(value.toUpperCase());
  }
}
```

### Method 4: Truthiness Checks

```typescript
function printLength(value: unknown) {
  if (value) {
    // Eliminates null, undefined, false, 0, ''
    console.log(value); // More specific type
  }
}
```

## Conclusion

While `any` might seem easy to use, it is a bad practice. The `unknown` type with type narrowing gives a safe way to handle unpredictable data and better maintenance. Thank you for staying till the end!
