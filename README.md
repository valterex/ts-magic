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
