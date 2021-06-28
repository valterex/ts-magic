# Misc

- [Type-predicate](#type-predicate)
- [Generic object types](#generic-object-types)
- [Utility types](#utility-types)

# Type manipulation

- [Generic type variables](#generic-type-variables)
- [Generic functions](#generic-functions)
- [Generic interfaces](#generic-interfaces)
- [Generic classes](#generic-classes)
- [Generic constraints](#generic-constraints)
- [Template literal types](#template-literal-types)
- [Indexed access types](#indexed-access-types)
- [Typeof type operator](#typeof-type-operator)

## Type predicate

```ts
interface Visitor {
  id: string;
}

interface Subscriber extends Visitor {
  annualMembership: boolean;
  trial: boolean;
}

interface Guest extends Visitor {
  firstVisit: boolean;
}

let subscriber: Subscriber = {
  id: "1",
  annualMembership: true,
  trial: false,
};

let guest: Guest = {
  id: "2",
  firstVisit: false,
};

const isSubscriber = (value: Visitor): value is Subscriber => {
  return (value as Subscriber).annualMembership !== undefined;
};

const isGuest = (value: Visitor): value is Guest => {
  return (value as Guest).id !== undefined;
};

const getUserType = () => {
  if (isSubscriber(subscriber)) {
    return (
      "Annual membership " +
      subscriber.annualMembership +
      "Trial expired: " +
      subscriber.trial
    );
  }

  if (isGuest(guest)) {
    "Guest Id: " + console.log(guest.id);
  }
};
```

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

## Utility types

### `Partial<Type>`

```ts
interface Planet {
  meanDiameter: number;
  earthVolume: number;
}

const updatePlanet = (
  planet: Planet,
  physicalCharacteristics: Partial<Planet>
) => {
  return { ...planet, ...physicalCharacteristics };
};

const mercury = {
  meanDiameter: 4880,
  earthVolume: 0.055,
};

const updatedMercury = updatePlanet(mercury, { meanDiameter: 4890 });
```

### `Required<Type>`

```ts
interface Mammal {
  kingdom?: string;
  phylum?: string;
}

const mammal: Required<Mammal> = { kingdom: "Animalia" };
// Property 'phylum' is missing in type '{ kingdom: string; }' but required in type 'Required<Mammal>'.ts(2741)
```

### `Readonly<Type>`

```ts
interface Position {
  x: number;
  y: number;
}

const immutableLocation: Readonly<Position> = {
  x: 0,
  y: 0,
};

immutableLocation.x = 1;
// Cannot assign to 'x' because it is a read-only property.ts(2540)
```

### `Record<Keys, Type>`

```ts
interface Boxer {
  active: boolean;
  stance?: "orthodox" | "southpaw";
}

type Name = "Mike Tyson" | "Micky Ward" | "Vasiliy Lomachenko";

const boxers: Record<Name, Boxer> = {
  "Mike Tyson": { active: false },
  "Vasiliy Lomachenko": { active: true, stance: "southpaw" },
  "Micky Ward": {
    active: false,
  },
};
```

### `Pick<Type, Keys>`

```ts
interface Post {
  id: string;
  createdBy: string;
  lastUpdated: string | null;
  title: string;
}

type PostSummary = Pick<Post, "id" | "title">;

const post: PostSummary = {
  id: "1",
  title: "Lorem ipsum",
};
```

### `Omit<Type, Keys>

```ts
type PostId = Omit<Post, "createdBy" | "lastUpdated" | "title">;

// *** Exclude<Type, ExcludedUnion>
type Middleweight = Exclude<
  "Mike Tyson" | "Gennady Golovkin" | "Jermall Charlo",
  "Mike Tyson"
>;
// type MiddleWeights = "Gennady Golovkin" | "Jermall Charlo"

type Heavyweight = Exclude<"Mike Tyson", "Gennady Golovkin" | "Jermall Charlo">;
// type Heavyweight = "Mike Tyson"

type FunctionOnly = Exclude<
  string | number | (() => Promise<void>),
  string | number
>;
```

### `Extract<Type, Union>`

```ts
type Falafel = Extract<"hummus" | "bread", "bread" | "cheese">;
// type Falafel = "bread"

type Kebab = NonNullable<"yummy" | null | undefined>;
// type Kebab = "yummy"

// *** Parameters<Type>
type T1 = Parameters<(s: Array<string>, c: Array<number>) => Promise<void>>;
// type T1 = [s: string[], c: number[]]
```

### `ReturnType<Type>`

```ts
const helloDave = (x: number, y: number) => {
  return x + y;
};

type helloDaveT = ReturnType<typeof helloDave>;
// type helloDaveT = number

type imSorryDave = ReturnType<() => Promise<void>>;
// type imSorryDave = Promise<void>

interface User {
  length: number;
}

type U = ReturnType<<T extends User, User extends Array<string>>() => T>;
// type U = string[]
```

## Generic type variables

```ts
const sendMessage = <T>(message: T): T => {
  return message;
};

const newMessage = sendMessage("Hello, world!");
```

```ts
const getMessages = <T>(messages: Array<T>): Array<T> => {
  console.log(messages.length);
  return messages;
};

// Another way
interface ArrLength {
  length: number;
}

const getMessages = <T extends ArrLength>(messages: T): T => {
  console.log(messages.length);
  return messages;
};
```

## Generic functions

```ts
const foo = <T>(arg: T): T => {
  return arg;
};

let bar: <T>(arg: T) => T = foo;
```

## Generic interfaces

```ts
interface GenericInterface<T> {
  (arg: T): T;
}

const whoAmI = <T>(name: T): T => {
  return name;
};

let identity: GenericInterface<number> = whoAmI;
```

## Generic classes

```ts
class GenericClass<T> {
  zeroValue: T;
  add: (x: T, y: T) => T;
}

let genericInstance = new GenericClass<number>();

genericInstance.zeroValue = 0;

genericInstance.add = function (x, y) {
  return x + y;
};
```

## Generic constraints

```ts
const getProperty = <T, K extends keyof T>(obj: T, key: K) => {
  return obj[key];
};

let x = { a: 1, b: 2, c: 3, d: 4 };

getProperty(x, "a");

getProperty(x, "m"); // Argument of type '"m"' is not assignable to parameter of type '"a" | "b" | "c" | "d"'.
```

## `keyof` type operator

```ts
type Film = { title: string; releaseYear: number };

type FilmKeys = keyof Film; // "title" | "releaseYear"
```

## Template literal types

```ts
type FirstName = "Mike";
type LastName = "Tyson";

type Greeting = `Hello, ${FirstName} ${LastName}`;
// type Greeting = "Hello, Mike Tyson"

type User = "username" | "account";
type MembershipType = "paid" | "trial";

type IDs = `${User | MembershipType}_id`;
// type IDs = "username_id" | "account_id" | "paid_id" | "trial_id"
```

```ts
type PropEventSource<Type> = {
  on(
    eventName: `${string & keyof Type}Changed`,
    callback: (newValue: any) => void
  ): void;
};

/// Create a "watched object" with an 'on' method
/// so that you can watch for changes to properties.
declare function makeWatchedObject<Type>(
  obj: Type
): Type & PropEventSource<Type>;

const person = makeWatchedObject({
  firstName: "Saoirse",
  lastName: "Ronan",
  age: 26,
});

person.on("firstNameChanged", () => {});

person.on("firstName", () => {});
// Argument of type '"firstName"' is not assignable to parameter of type '"firstNameChanged" | "lastNameChanged" | "ageChanged"'.

person.on("frstNameChanged", () => {});
// Argument of type '"frstNameChanged"' is not assignable to parameter of type '"firstNameChanged" | "lastNameChanged" | "ageChanged"'.
```

```ts
type AnotherPropEventSource<Type> = {
  on<Key extends string & keyof Type>(
    eventName: `${Key}Changed`,
    callback: (newValue: Type[Key]) => void
  ): void;
};

declare function makeWatchedObject<Type>(
  obj: Type
): Type & AnotherPropEventSource<Type>;

const anotherPerson = makeWatchedObject({
  firstName: "Saoirse",
  lastName: "Ronan",
  age: 26,
});

person.on("firstNameChanged", (newName) => {
  // (parameter) newName: string
  console.log(`new name is ${newName.toUpperCase()}`);
});

person.on("ageChanged", (newAge) => {
  // (parameter) newAge: number
  if (newAge < 0) {
    console.warn("warning! negative age");
  }
});
```

```ts
type aGreeting = "Hello, world";
type ShoutyGreeting = Uppercase<aGreeting>;
// type ShoutyGreeting = "HELLO, WORLD"

type ASCIICacheKey<Str extends string> = `ID-${Uppercase<Str>}`;
type MainID = ASCIICacheKey<"my_app">;
// type MainID = "ID-MY_APP"
```

## Indexed access types

```ts
type Division =
  | "flyweight"
  | "bantamweight"
  | "featherweight"
  | "lightweight"
  | "welterweight"
  | "middleweight"
  | "light-heavyweight"
  | "heavyweight";

type ProBoxer = {
  firstName: string;
  lastName: string;
  active: boolean;
  gender: "male" | "female";
  age: number;
  weight: number;
  height: number;
  stance: "orthodox" | "southpaw";
  wins: number;
  losses: number;
  division: Division;
};

type Stance = ProBoxer["stance"];
// type Stance = "orthodox" | "southpaw"

type GenderAge = Person["gender" | "age"];
// type GenderAge = number | "male" | "female"

type ProBoxerPropTypes = ProBoxer[keyof ProBoxer];
// type ProBoxerPropTypes = string | number | boolean

type StanceOrDivision = "stance" | "division";
type StanceDivisionProBoxerProps = ProBoxer[StanceOrDivision];
// type StanceDivisionProBoxerProps = "orthodox" | "southpaw" | Division
```

## Mapped types

```ts
interface Artist {
  name: string;
}

type ArtistSummary = {
  [key: string]: boolean | Artist;
};

const ArtistData: ArtistSummary = {
  isAnyGood: true,
  // name: "Alton Ellis" - not assignable
  data: {
    name: "Alton Ellis",
  },
};
```

```ts
type OptionsFlags<T> = {
  [P in keyof T]: () => Promise<Array<any>>;
};

type FeatureFlags = {
  getAllUsers: () => void;
  getAllAccounts: () => void;
};

type FeatureOptions = OptionsFlags<FeatureFlags>;

/*
type FeatureOptions = {
  getAllUsers: () => Promise<Array<any>>;
  getAllAccounts: () => Promise<Array<any>>;
}
*/
```

```ts
// Remove 'readonly' attributes from a type's properties

type CreateMutable<T> = {
  -readonly [P in keyof T]: T[P];
};

type LockedAccount = {
  readonly id: string;
  readonly name: string;
};

type UnlockedAccount = CreateMutable<LockedAccount>;

/*
type UnlockedAccount = {
  id: string;
  name: string;
};
*/
```

```ts
// Remove 'optional' attributes from a type's properties

type Concrete<T> = {
  [P in keyof T]-?: T[P];
};

type MaybeUser = {
  id: string;
  name?: string;
  age?: number;
};

type User = Concrete<MaybeUser>;

/*
type User = {
  id: string;
  name: string;
  age: number;
};
*/
```

```ts
type Getters<T> = {
  [P in keyof T as `get${Capitalize<string & P>}`]: () => T[P];
};

interface Person {
  name: string;
  age: number;
  location: string;
}

type LazyPerson = Getters<Person>;

/*
type LazyPerson = {
    getName: () => string;
    getAge: () => number;
    getLocation: () => string;
}
*/
```

```ts
// Remove type property

type RemoveKindField<T> = {
  [P in keyof T as Exclude<P, "kind">]: T[P];
};

interface Circle {
  kind: "circle";
  radius: number;
}

type KindlessCircle = RemoveKindField<Circle>;

/*
type KindlessCircle = {
  radius: number;
};
*/
```

## Typeof type operator

```ts
let quote = "Everybody has a plan until they get punched in the face.";
let mikeTyson: typeof quote; // let quote: string
```

```ts
type Predicate<T> = (x: T) => T;
type returnString = ReturnType<Predicate<string>>;
// type returnString = string
```

```ts
const foo = (arg: number): string => {
  return String(arg);
};

type returnBar = ReturnType<typeof foo>;
// type returnBar = string
```

## Conditional types

```ts
interface Animal {
  organs: boolean;
}

interface Human extends Animal {
  consciousness: boolean;
}

type Example1 = Human extends Animal ? "isHuman" : null;
// type Example1 = "isHuman";

type Example2 = Rock extends Animal ? boolean : number;
// type Example2 = number;
```

```ts
interface IdLabel {
  id: number;
}

interface NameLabel {
  name: string;
}

type NameOrId<T extends number | string> = T extends number
  ? IdLabel
  : NameLabel;

const createLabel = <T extends number | string>(idOrName: T): NameOrId<T> => {
  throw "unimplemented";
};

let a = createLabel("typescript");
// let a: NameLabel

let b = createLabel(2.8);
// let b: IdLabel

let c = createLabel(Math.random() ? "hello" : 42);
// let c: NameLabel | IdLabel
```
