# Objects

- [Generic object types](#generic-object-types)

## Generic object types

```ts
interface User<T> {
  title: T;
}

let user: User<string>;
```

```ts
type Account<T> = {
  id: T;
};

interface Personal {}

type PersonalAccount = Account<Personal>;
```

```ts
// *** Generic helper types

type OrNull<Type> = Type | null;
type OneOrMany<Type> = Type | Type[];

type OneOrManyOrNull<Type> = OrNull<OneOrMany<Type>>;
// type OneOrManyOrNull<Type> = OneOrMany<Type> | null;

type OneOrManyOrNullStrings = OneOrManyOrNull<string>;
// type OneOrManyOrNullStrings = OneOrMany<string> | null;
```
