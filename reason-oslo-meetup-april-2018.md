- title : vRRoom + bs-json
- theme : beige
- transition : slide
- controls : false

***

## vRRoom + bs-json

***

## vRRoom

* Utility library for ReasonReact development
* https://github.com/glennsl/vrroom
* `npm install --save glennsl/vrroom`

***
- transition: fade

### Text

```
<div> {"Hello World" |> ReasonReact.stringToElement} </div>
```

--- 

- transition: fade

### Text


```
let str = ReasonReact.stringToElement
<div> {"Hello World" |> str} </div>
```

--- 

- transition: fade

### Text

```
let ste = ReasonReact.stringToElement
<div> {"Hello World" |> ste} </div>
```
```
let s = ReasonReact.stringToElement
<div> {"Hello World" |> s} </div>
```
```
let text = ReasonReact.stringToElement
<div> {"Hello World" |> text} </div>
```

---

- transition: fade

### Text

```
open Vrroom;

<div> {"Hello World" |> text} </div>
```

---

- transition: fade

### Text

```
open Vrroom;

<div> {42 |> Text.int} </div>

<div> {4.2 |> Text.float} </div>

<div> {"Hello World" |> Text.string} </div>

<div> {Js.true_ |> Text.any} </div>
```

***

### Nothing
aka `ReasonReact.nullElement`

<br/>

```
(alignIcon === `Left ? icon : nothing)
```

---

### &nbsp;

```
 let nbsp = [%raw {|'\u00a0'|}];
 ```

```
switch package##description {
| ""          => nbsp
| description => description |> text
}
```

***

### Fragment

ReactJS equivalent:

```
<> ... </>
```

or

```
<React.Fragment> ... </React.Fragment>
```

---

### Fragment

```
<ul>
  ...
  <li class="label">Answer to everything:</li>
  <li class="value">42</li>
  ...
</ul>
```

---

### Fragment

```
<ul>
  {items 
    |> Array.map(
        item => ReasonReact.arrayToElement([|
          <li className="label"> {item.label |> text} </li>
          <li className="value"> {item.value |> text} </li>
        |])
    |> ReasonReact.arrayToElement}
</ul>
```

`Warning: Each child in an array or iterator should have a unique "key" prop.`

---

### Fragment

"Official" solution:

```
ReasonReact.cloneElement(
    parent,
    ~props=Js.Obj.empty(),
    [| ... |]
);
```

```
ReasonReact.createDomElement(
    "div",
    ~props=Js.Obj.empty(),
    [| ... |]
);
```

---

### Fragment

Better solution:

```
let listItem = item =>
  <Fragment>
    <li className="label"> {item.label |> text} </li>
    <li className="value"> {item.value |> text} </li>
  </Fragment>
```

No key warning. No wrapping element

***

### Control.Map

```
<ul>
  {
    switch items {
    | [||] => <li> {"no items" |> text} </li>
    | items => 
      items |> Array.map(name => <li> {name |> text} </li>)
            |> ReasonReact.arrayToElement
    }  
  }
<ul>
```

---
### Control.Map

```
<ul>
  <Control.Map items empty=<li> {"no items" |> text} </li> >
    ...(name => <li> {name |> text} </li>)
  </Control.Map>
<ul>
```

---

### Control.MapList

```
<ul>
  {
    switch items {
    | [] => <li> {"no items" |> text} </li>
    | items => 
      items |> List.map(name => <li> {name |> text} </li>)
            |> Array.of_list
            |> ReasonReact.arrayToElement
    }  
  }
<ul>
```

---

### Control.MapList

```
<ul>
  <Control.MapList items
                   empty=(<li> {"no items" |> text} </li>)>
    ...(name => <li> {name |> text} </li>)
  </Control.MapList>
<ul>
```

---

### Control.IfSome

```
{
  switch maybeError {
  | Some(error) => error |> text
  | None        => nothing
  }
}
```

```
<Control.IfSome option=maybeError>
  ...(error => error |> text)
</Control.IfSome>
```

---

### Control.If

```
{showHello ? "Hello" |> text : nothing}
```

```
<Control.If cond=showHello>
    ...(() => "Hello" |> text)
</Control.If>
```

***

### pure

```
module Item = {
  let instance = ReasonReact.statelessComponent("Item");
  let make = (~label, _children) => {
    ...instance,
    render: _self =>
      <li> (label |> text) </li>
  }
};
```

---

### pure

```
module Item = {
  let make = pure((render, ~label) => render(
    <li> (label |> text) </li>
  ));
};
```

***

### childless

```
module MyComponent = {
  let make = (_children) => {
    ...
  }
}
```

```
<MyComponent>
  <div> {"some child" |> text} </div>
</MyComponent>
```

---

### childless

```
type nothing;
type childless = array(nothing);
```

```
module MyComponent = {
  let make = (_:childless) => {
    ...
  }
}
```

---

### childless

```
This has type:
  ReasonReact.reactElement array -> ReasonReact.reactElement
But somewhere wanted:
  childless -> 'a
```

***

### ClassName

```
let className = 
  ClassName.(join([
    "tooltip",
    "s-hovered" |> if_(isHovered),
    maybeErrorClass |> fromOption
  ]));
```

```
<div className>
  ...
</div>
```

***

## bs-json

* Decoding (and encoding) of JSON data structures
* Combinators: functional, compositional
* Takes more than a little bit from Elm's `Json.Decode`
* https://github.com/glennsl/bs-json
* `npm install --save @glennsl/bs-json`

***

### Encoder - Interface

```
type encoder('a) = 'a => Js.Json.t
```

---

### Encoder - Interface

```
let int : encoder(int)
```

---

### Encoder - Interface

```
let int : int => Js.Json.t
```

---

### Encoder - Interface

```
encoder(float)  -> float => Js.Json.t
encoder(string) -> string => Js.Json.t
```

***

### Encoder - Usage

```
42 |> Json.Encode.int |> Json.stringify
```

***

### Encoder - Implementation

```
external int : int => Js.Json.t = "%identity"
```

***

### Encoder "factories" - Interface

```
let list : encoder('a) => encoder(list('a))
```

---

### Encoder "factories" - Interface

```
let list : ('a => Js.Json.t) => list('a) => Js.Json.t
```

***

### Encoder "factories" - Usage

```
[1, 2, 3] |> Json.Encode.(list(int)) |> Json.stringify
```

---

### Encoder "factories" - Usage

```
open Json.Encode;
[1, 2, 3] |> list(int) |> Json.stringify
```

***

### Encoder "factories" - Implementation

```
let list = (encode, l) =>
  l |> List.map encode
    |> Array.of_list
    |> jsonArray
```

***

### Encoder "factories" - Optimized helpers

```
[|1, 2, 3|] |> Json.Encode.stringArray |> Json.stringify
```

***

### Example: encode object

```
open Json.Encode;

let json = Js.Dict.fromList([
    ("name", "bs-json" |> string),
    ("stars", 84 |> int),
    ("repository", "https://github.com/glennsl/bs-json" |> string)
    ("releases", ["1.2.0", "1.1.0", "1.0.1", ...] |> list(string))
]);
```

***

### Decoder - Interface

```
type decoder('a) = Js.Json.t => 'a
```

---

### Decoder - Interface

```
let int : decoder(int)
```

---

### Decoder - Interface

```
let int : Js.Json.t => int
```

***

### Decoder - Usage

```
"42" |> Json.parseOrRaise |> Json.Decode.int
```

---

### Decoder - Proper Usage

```
let n: int =
  match (Json.parse(json)) {
  | Some(json) =>
    try (json |> Json.Decode.int) {
    | Json.Decode.DecodeError(_) => 0
    }
  | None => 0
  };
```

***

### Decoder - Implementation

```
let int = json => {
  let f = float(json);
  if _isInteger(f) {
    (Obj.magic(f: float): int)
  } else {
    raise(DecodeError("Expected integer, got " ++ _stringify(json)))
  }
};
```

***

### Decoder - Implementation

```
let float = json => 
  if Js.typeof(json) == "number" {
    (Obj.magic(json: Js.Json.t): float)
  } else {
    raise(DecodeError("Expected number, got " ++ _stringify(json)))
  };
```

***

### Decoder - Implementation

```
let _isInteger = value =>
  Js.Float.isFinite(value) && Js.Math.floor_float(value) === value
```

***

### Decoder "factories" - Interface

```
let list : decoder('a) => decoder(list('a))
```

---

### Decoder "factories" - Interface

```
let list : (Js.Json.t => 'a) => Js.Json.t => list('a)
```

***

### Decoder "factories" - Usage

```
"[1, 2, 3]" |> Json.parseOrRaise |> Json.Decode.(list(int))
```

---

### Decoder "factories" - Usage

```
open Json.Decoder;
[1, 2, 3] |> Json.parseOrRaise |> list(int)
```

***

### Decoder "factories" - Implementation

```
let list = (decode, json) =>
  json |> array(decode) |> Array.to_list
```

---

### Decoder "factories" - Implementation

```
let array = (decode, json) =>
  if (Js.Array.isArray(json)) {
    let source: array(Js.Json.t) = Obj.magic(json: Js.Json.t);
    let length = Js.Array.length(source);
    let target = _unsafeCreateUninitializedArray(length);

    for (i in 0 to length - 1) {
      let value =
        try (decode(Array.unsafe_get(source, i))) {
        | DecodeError(msg) =>
          raise(DecodeError(msg ++ "\n\tin array at index " ++ string_of_int(i)))
        };

      Array.unsafe_set(target, i, value);
    };

    target;
  } else {
    raise(DecodeError("Expected array, got " ++ _stringify(json)));
  };
```

***

### Decoding objects

```
let field : (string, decoder('a)) => decoder('a)
let at : (list(string), decoder('a)) => decoder('a)
```

---

### Decoding objects - Example

```
{
  "name": "@glennsl/bs-json",
  ...
  "repository": {
    "type": "git",
    "url": "git+https://github.com/glennsl/bs-json.git"
  }
}
```

```
{
  ...
  "repository": "git+https://github.com/glennsl/bs-json.git"
}
```

```
let decodePackageJson = json => Json.Decode.{
  name          : json |> field("name", string),
  ...
  repositoryUrl : json |> optional(either(
                            at(["repository", "url"], string),
                            field("repository", string))),
};
```

***

### Error handling

```
let n: int =
  match (Json.parse(json)) {
  | Some(json) =>
    try (json |> Json.Decode.int) {
    | Json.Decode.DecodeError(_) => 0
    }
  | None => 0
  };
```

---

### Error handling

```
let n: option(int) =
  json |> Json.parse
       |> Option.map(
            json =>
              try (json |> Json.Decode.int) {
              | Json.Decode.DecodeError(_) => 0
              }
          );
```

---

### Error handling

```
open Json.Decode;

let n: option(int) =
  json |> Json.parse
       |> Option.flatMap(optional(int));
```

---

### Error handling

```
open Json.Decode;

let n: option(int) =
  json |> Json.parse
       |> Option.map(int |> withDefault(0));
```

---

### Error handling

```
open Json.Decode;

let n: int =
  json |> Json.parse
       |> Option.flatMap(optional(int))
       |> Option.or_(0);
```

***

### either/oneOf

```
let either: decoder('a) => decoder('a) => decoder(a) 
let oneOf: list(decoder('a)) => decoder(a) 
```

---

### map

```
let map: ('a => 'b, decoder('a)) => decoder('b)
```

---

### either + map example

```
type intOrString = Int(int) | String(string);

let intOrStringDecoder =
  Json.Decode.(
    either(
      int    |> map(n => Int(n)),
      string |> map(s => String(s))
    )
  );
```

***

### Future - field

Now: 

```
let field : (string, decoder('a)) => decoder('a)
```

2.0: 

```
let field : string => decoder(Js.Json.t)
```

---

### Future - field

Now:

```
json |> field("foo", int)
```

2.0:

```
json |> field("foo") |> int
```

---

### Future - field

Now:

```
json |> optional(field("foo", int))
```

2.0:

```
json |> optional(field("foo")) |> Option.map(int)
```

---

### Future - errors

Now:

```
DecodeError("Error: expected int, got `null`")
DecodeError("Error: expected field 'foo'")
```

2.0

```
DecodeError("Error: expected int, got `null`", `IncompatibleType("int"))
DecodeError("Error: expected field 'foo'", `MissingField("foo"))
```

***

Fin
