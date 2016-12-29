# Functional Programming Jargon
#Programacion Funcional Jerga

La programacion funcional (FP) ofrece muchas ventajas, y como resultado su popularidad ha ido en aumento. Sin embargo, cada paradigma de programación viene con su propia jerga y la FP no es la excepción. Al proporcionar un glosario de terminos, esperamos hacer el aprendizaje de la programación funcional mas simple.

Los ejemplos se presentan usando la syntaxis de JavaScript 2015 (ES2015) [Why JavaScript?](https://github.com/hemanth/functional-programming-jargon/wiki/Why-JavaScript%3F)

*Este es un [WIP](https://github.com/hemanth/functional-programming-jargon/issues/20); por fabor sientase libre de enviarnos un PR ;)*

Cuando sea necesario, este documento usara terminos definidos en la especificacion de [Fantasy Land spec](https://github.com/fantasyland/fantasy-land)

__Traducciones__
* [Portuguese](https://github.com/alexmoreno/jargoes-programacao-funcional)
* [Spanish](https://github.com/idcmardelplata/functional-programming-jargon)

__Tabla de Contenido__
<!-- RM(noparent,notop) -->

* [Aridad](#arity)
* [Funciones de orden superior (HOF)](#higher-order-functions-hof)
* [Aplicacion parcial](#partial-application)
* [Currying](#currying)
* [Auto Currying](#auto-currying)
* [Composicion funcional](#function-composition)
* [Pureza](#purity)
* [Efectos secundarios](#side-effects)
* [Idempotente](#idempotent)
* [Estilo de punto libre o programacion tacita](#point-free-style)
* [Predicado](#predicate)
* [Contratos](#contracts)
* [Funciones guardas](#guarded-functions)
* [Categorias](#categories)
* [Valor](#value)
* [Constante](#constant)
* [Funtor](#functor)
  * [Preservar la identidad](#preserves-identity)
  * [Componible](#composable)
* [Functor apuntado](#pointed-functor)
* [Elevar (lift)](#lift)
* [Transparencia referencial](#referential-transparency)
* [Razonamiento equacional](#equational-reasoning)
* [Lambda](#lambda)
* [Calculo Lambda](#lambda-calculus)
* [Evaluación Perezosa](#lazy-evaluation)
* [Monoide](#monoid)
* [Monada](#monad)
* [Comonada](#comonad)
* [Funtor Aplicativo](#applicative-functor)
* [Morfismo](#morphism)
  * [Endomorfismo](#endomorphism)
  * [Isomorfismo](#isomorphism)
* [Setoide](#setoid)
* [Semigrupo](#semigroup)
* [Plegable](#foldable)
* [Traversable](#traversable)
* [Signatura de tipos](#type-signatures)
* [Tipos de union](#union-type)
* [Tipos de producto](#product-type)
* [Opcional](#option)
* [Librerias para la programación funcional en JavaScript](#functional-programming-libraries-in-javascript)


<!-- /RM -->

## Arity

El numero de argumentos que una funcion toma. utiliza terminos como unario, binario, ternario etc. Esta pablabra tiene la distincion de estar compuesta de dos sufijos, "Ary" y "ity" Adicionalmente por ejemplo, toma dos argumentos, y asi se define como una funcion binaria o una funcion de aridad dos. Tal funcion a veces puede ser llamada "diadica" por personas que prefieren las raices griegas al latín. Del mismo modo, una funcion que toma un numero variable de argumentos se denomina "variadic", mientras que una funcion binaria solamente toma dos y nada mas que dos argumentos. Vea mas adelante curring y aplicacion parcial.


```js
const sum = (a, b) => a + b

const arity = sum.length
console.log(arity) // 2

// The arity of sum is 2
```

## Funciones de Orden Superior (HOF)

Funcion que toma una funcion como argumento y puede o no retornar una funcion.

```js
const filter = (predicate, xs) => {
  const result = []
  for (let idx = 0; idx < xs.length; idx++) {
    if (predicate(xs[idx])) {
      result.push(xs[idx])
    }
  }
  return result
}
```

```js
const is = (type) => (x) => Object(x) instanceof type
```

```js
filter(is(Number), [0, '1', 2, null]) // [0, 2]
```

## Aplicacion parcial.

Aplicar parcialmente una funcion, significa crear una nueva funcion rellenando previamente alguno de los argumentos de la funcion original.


```js
// Ayudante para creaqr funciones parcialmente aplicadas.
// Toma una funcion y algunos argumentos.
const partial = (f, ...args) =>
  // retorna una funcion que toma el resto de los argumentos.
  (...moreArgs) =>
    // y llama a la funcion original con todos ellos.
    f(...args, ...moreArgs)

// Algo para aplicar.
const add3 = (a, b, c) => a + b + c

// Parcialmente aplica  `2` y `3` a `add3` obteniendo una funcion de un solo argumento ( funcion unaria o de aridad 1 )
const fivePlus = partial(add3, 2, 3) // (c) => 2 + 3 + c

fivePlus(4) // 9
```
Tambien puede utilizar `Function.prototype.bind` para aplicar parcialmente una funcion en JavaScript.

```js
const add1More = add3.bind(null, 2, 3) // (c) => 2 + 3 + c
```
La aplicacion parcial ayuda a crear funciones mas simples a partir de funciones mas complejas mediante la adicion de sus datos cuando los tenga. Las funciones [curri]((#currying)) se aplican parcialmente de forma automatica.

## Currying

El proceso de convertir una funcion que toma multiples argumentos, en una funcion que los toma uno a la vez.

Cada vez que la funcion es llamada, esta solamente acepta un argumento y retorna una funcion que toma el siguiente argumento y asi continua hasta que se pasen todos los argumentos.

```js
const sum = (a, b) => a + b

const curriedSum = (a) => (b) => a + b

curriedSum(40)(2) // 42.

const add2 = curriedSum(2) // (b) => 2 + b

add2(10) // 12

```

## Auto Currying
Transforma una funcion que toma multiples argumentos en una funcion que, si se le da un numero menor de argumentos de los que espera, devuelve una funcion que toma el resto. Cuando la funcion obtiene el numero completo de argumentos se evalua.

Underscore, lodash, y ramda tienen una funcion `curry` que trabaja de esta manera.

```js
const add = (x, y) => x + y

const curriedAdd = _.curry(add)
curriedAdd(1, 2) // 3
curriedAdd(1) // (y) => 1 + y
curriedAdd(1)(2) // 3
```

__Otras lecturas__
* [Favoring Curry](http://fr.umio.us/favoring-curry/)
* [Hey Underscore, You're Doing It Wrong!](https://www.youtube.com/watch?v=m3svKOdZijA)

## Composición Funcional

Componer funciones es el acto de poner 2 funciones juntas para formar una tercera funcion donde la salida de una es la entrada de la otra.

```js
const compose = (f,g) => (argumentos) => f(g(argumentos)) // Definicion
const toString = (val) => val.toString()
const floorAndToString = compose(toString, Math.floor);
floorAndToString(121.212121) // '121'
```

## Purity

Una funcion es pura si el valor de retorno es determinado solamente por sus valores de entrada, y no produce efectos secundarios.

```js
const greet = (name) => `Hi, ${name}`

greet('Brianne') // 'Hi, Brianne'
```

A diferencia de la siguiente funcion.

```js
window.name = 'Brianne'

const greet = () => `Hi, ${window.name}`

greet() // "Hi, Brianne"
```

La salida del ejemplo anterior, esta basada en datos almacenados fuera de la funcion...

```js
let greeting

const greet = (name) => {
  greeting = `Hi, ${name}`
}

greet('Brianne')
greeting // "Hi, Brianne"
```

... y este modifica el estado fuera de la funcion.

## Efectos Secundarios.

Se dice que una funcion o expresion tiene un efecto secundario si aparte de retornar un valor, interactua con el estado mutable externo (lee o escribe).

```js
const differentEveryTime = new Date()
```

```js
console.log('IO es un efecto secundario!')
```

## Idempotente

una funcion es idempotente si al volver a aplicarla sobre su resultado, no produce resultados diferentes.

```
f(f(x)) ≍ f(x)
```

```js
Math.abs(Math.abs(10))
```

```js
sort(sort(sort([2, 1])))
```

## Estilo de punto libre o programacion tacita.

Es un estilo de escribir funciones donde la definicion de la funcion no identifica de forma explicita los argumentos utilizados. Este estilo usualmente requiere [currying](#currying) o otra [Funcion de Orden Superior](#higher-order-functions-hof). Este estilo de programacion tambien es conocido como Programacion Tacita.

```js
// Dado
const map = (fn) => (list) => list.map(fn)
const add = (a) => (b) => a + b

// Then

// Sin utilizar el estilo points-free - `numbers` es un argumento explicito.
const incrementAll = (numbers) => map(add(1))(numbers)

// Utilizando Points-free - La lista es un argumento implicito.
const incrementAll2 = map(add(1))
```
`incrementAll` identifica y usa el parametro `numbers`, por lo que no es points-free. `incrementAll2` esta escrita simplemente combinando funciones y valores, pero sin mencionar sus argumentos. __Es points-free__.

Las definiciones de las funciones points-free son muy similares a las asignaciones normales sin `function`  o  `=>`.

## Predicado
Un predicado es una funcion que retorna true o false segun un valor dado. Un uso comun de un predicado es como callback para la funcion filter de los Arrays.

```js
const predicate = (a) => a > 2

;[1, 2, 3, 4].filter(predicate) // [3, 4]
```

## Contratos

Un contrato especifica las obligaciones y garantias de comportamiento de una funcion o expresion en tiempo de ejecucion. Esto actua como un conjunto de reglas que se esperan de la entrada y salida de una funcion o expresion, y los errores son generalmente reportados cuando un contrato es violado.

```js
// Define un contrato: int -> int
const contract = (input) => {
  if (typeof input === 'number') return true
  throw new Error('Contract violated: expected int -> int')
}

const addOne = (num) => contract(num) && num + 1

addOne(2) // 3
addOne('some string') // Contract violated: expected int -> int
```

## Guarded Functions

TODO

## Categoria.

Las categorias son objetos con funciones asociadas que se adieren a ciertas reglas, por ejemplo los [Monoides](#monoid)

## Valores

Cualquier cosa que pueda ser asignado a una variable.

```js
Object.freeze({name: 'John', age: 30}) // La funcion `freeze` refuerza la inmutabilidad.
;(a) => a
;[1]
undefined
```

## Constante

Una constante es una variable que no puede ser reasignada una vez definida.

```js
const five = 5
const john = Object.freeze({name: 'John', age: 30})
```

Las constantes son [referencialmente transparentes](#referential-transparency). Es decir que pueden ser reemplazadas por los valores que representan sin afectar al resultado. 

Con las dos constantes anteriores, la siguiente expresion siempre devolvera  `true`.

```js
john.age + five === ({name: 'John', age: 30}).age + (5)
```

## Funtor

Un objeto que implementa una funcion llamada `map` que, mientras se ejecuta sobre cada valor en el objeto para producir un nuevo objeto, a adhiere a 2 reglas.


### Preserva la identidad
```
object.map(x => x) ≍ object
```

### Es componible

```
object.map(compose(f, g)) ≍ object.map(g).map(f)
```

(`f`, `g` son funciones arbitrarias)

Un functor comun en JavaScript es `Array` ya que cumple con las dos reglas de los funtores:

```js
[1, 2, 3].map(x => x) // = [1, 2, 3]
```

and

```js
const f = x => x + 1
const g = x => x * 2

;[1, 2, 3].map(x => f(g(x))) // = [3, 5, 7]
;[1, 2, 3].map(g).map(f)     // = [3, 5, 7]
```

## Funtor apuntado
Un objeto con una funcion `of` que pone un solo valor dentro del functor.

ES2015 agrega `Array.of` para hacer al tipo Array un funtor apuntado.

```js
Array.of(1) // [1]
```

## Elevar (lift)

Lifting (elevar) es cuando usted toma un valor y lo pone en un objeto como un [functor](#pointed-functor). Si usted eleva una funcion en un [funtor aplicativo](#applicative-functor), entonces puede hacer que funcione en valores que tambien estan en ese funtor.

Algunas implementaciones tienen una funcion llamada `lift` o `liftA2` para facilitar la ejecucion de funciones en los funtores

```js
const liftA2 = (f) => (a, b) => a.map(f).ap(b) // note it's `ap` and not `map`.

const mult = a => b => a * b

const liftedMult = liftA2(mult) // this function now works on functors like array

liftedMult([1, 2], [3]) // [3, 6]
liftA2((a, b) => a + b)([1, 2], [3, 4]) // [4, 5, 5, 6]
```
Elevar una funcion de un argumento y aplicarla, hace lo mismo que `map`.

```js
const increment = (x) => x + 1

lift(increment)([2]) // [3]
;[2].map(increment) // [3]
```


## Transparencia Referencial

Se dice que una expresion que puede ser cambiada por su valor sin alterar el comportamiento del programa es referencialmente transparente.

Supongamos que tenemos una funcion greet.

```js
const greet = () => 'Hello World!'
```

Cualquier invocacion de `greet()` puede ser reemplazada por `Hello World!`, por lo tanto greet es referencialmente transparente.

##  Razonamiento Equacional.

Cuando una aplicacion esta compuesta de expresiones y carece de efectos secundarios, las verdades sobre el sistema se pueden derivar de las partes.

## Lambda

An anonymous function that can be treated like a value.

```js
;(function (a) {
  return a + 1
})

;(a) => a + 1
```
Lambdas are often passed as arguments to Higher-Order functions.

```js
[1, 2].map((a) => a + 1) // [2, 3]
```

You can assign a lambda to a variable.

```js
const add1 = (a) => a + 1
```

## Lambda Calculus
A branch of mathematics that uses functions to create a [universal model of computation](https://en.wikipedia.org/wiki/Lambda_calculus).

## Lazy evaluation

Lazy evaluation is a call-by-need evaluation mechanism that delays the evaluation of an expression until its value is needed. In functional languages, this allows for structures like infinite lists, which would not normally be available in an imperative language where the sequencing of commands is significant.

```js
const rand = function*() {
  while (1 < 2) {
    yield Math.random()
  }
}
```

```js
const randIter = rand()
randIter.next() // Each execution gives a random value, expression is evaluated on need.
```

## Monoid

An object with a function that "combines" that object with another of the same type.

One simple monoid is the addition of numbers:

```js
1 + 1 // 2
```
In this case number is the object and `+` is the function.

An "identity" value must also exist that when combined with a value doesn't change it.

The identity value for addition is `0`.
```js
1 + 0 // 1
```

It's also required that the grouping of operations will not affect the result (associativity):

```js
1 + (2 + 3) === (1 + 2) + 3 // true
```

Array concatenation also forms a monoid:

```js
;[1, 2].concat([3, 4]) // [1, 2, 3, 4]
```

The identity value is empty array `[]`

```js
;[1, 2].concat([]) // [1, 2]
```

If identity and compose functions are provided, functions themselves form a monoid:

```js
const identity = (a) => a
const compose = (f, g) => (x) => f(g(x))
```
`foo` is any function that takes one argument.
```
compose(foo, identity) ≍ compose(identity, foo) ≍ foo
```

## Monad

A monad is an object with [`of`](#pointed-functor) and `chain` functions. `chain` is like [`map`](#functor) except it un-nests the resulting nested object.

```js
// Implementation
Array.prototype.chain = function (f) {
  return this.reduce((acc, it) => acc.concat(f(it)), [])
}

// Usage
;Array.of('cat,dog', 'fish,bird').chain((a) => a.split(',')) // ['cat', 'dog', 'fish', 'bird']

// Contrast to map
;Array.of('cat,dog', 'fish,bird').map((a) => a.split(',')) // [['cat', 'dog'], ['fish', 'bird']]
```

`of` is also known as `return` in other functional languages.
`chain` is also known as `flatmap` and `bind` in other languages.

## Comonad

An object that has `extract` and `extend` functions.

```js
const CoIdentity = (v) => ({
  val: v,
  extract () {
    return this.val
  },
  extend (f) {
    return CoIdentity(f(this))
  }
})
```

Extract takes a value out of a functor.

```js
CoIdentity(1).extract() // 1
```

Extend runs a function on the comonad. The function should return the same type as the comonad.

```js
CoIdentity(1).extend((co) => co.extract() + 1) // CoIdentity(2)
```

## Applicative Functor

An applicative functor is an object with an `ap` function. `ap` applies a function in the object to a value in another object of the same type.

```js
// Implementation
Array.prototype.ap = function (xs) {
  return this.reduce((acc, f) => acc.concat(xs.map(f)), [])
}

// Example usage
;[(a) => a + 1].ap([1]) // [2]
```

This is useful if you have two objects and you want to apply a binary function to their contents.

```js
// Arrays that you want to combine
const arg1 = [1, 3]
const arg2 = [4, 5]

// combining function - must be curried for this to work
const add = (x) => (y) => x + y

const partiallyAppliedAdds = [add].ap(arg1) // [(y) => 1 + y, (y) => 3 + y]
```

This gives you an array of functions that you can call `ap` on to get the result:

```js
partiallyAppliedAdds.ap(arg2) // [5, 6, 7, 8]
```

## Morphism

A transformation function.

### Endomorphism

A function where the input type is the same as the output.

```js
// uppercase :: String -> String
const uppercase = (str) => str.toUpperCase()

// decrement :: Number -> Number
const decrement = (x) => x - 1
```

### Isomorphism

A pair of transformations between 2 types of objects that is structural in nature and no data is lost.

For example, 2D coordinates could be stored as an array `[2,3]` or object `{x: 2, y: 3}`.

```js
// Providing functions to convert in both directions makes them isomorphic.
const pairToCoords = (pair) => ({x: pair[0], y: pair[1]})

const coordsToPair = (coords) => [coords.x, coords.y]

coordsToPair(pairToCoords([1, 2])) // [1, 2]

pairToCoords(coordsToPair({x: 1, y: 2})) // {x: 1, y: 2}
```



## Setoid

An object that has an `equals` function which can be used to compare other objects of the same type.

Make array a setoid:

```js
Array.prototype.equals = (arr) => {
  const len = this.length
  if (len !== arr.length) {
    return false
  }
  for (let i = 0; i < len; i++) {
    if (this[i] !== arr[i]) {
      return false
    }
  }
  return true
}

;[1, 2].equals([1, 2]) // true
;[1, 2].equals([0]) // false
```

## Semigroup

An object that has a `concat` function that combines it with another object of the same type.

```js
;[1].concat([2]) // [1, 2]
```

## Foldable

An object that has a `reduce` function that can transform that object into some other type.

```js
const sum = (list) => list.reduce((acc, val) => acc + val, 0)
sum([1, 2, 3]) // 6
```

## Traversable

TODO

## Type Signatures

Often functions in JavaScript will include comments that indicate the types of their arguments and return values.

There's quite a bit of variance across the community but they often follow the following patterns:

```js
// functionName :: firstArgType -> secondArgType -> returnType

// add :: Number -> Number -> Number
const add = (x) => (y) => x + y

// increment :: Number -> Number
const increment = (x) => x + 1
```

If a function accepts another function as an argument it is wrapped in parentheses.

```js
// call :: (a -> b) -> a -> b
const call = (f) => (x) => f(x)
```

The letters `a`, `b`, `c`, `d` are used to signify that the argument can be of any type. The following version of `map` takes a function that transforms a value of some type `a` into another type `b`, an array of values of type `a`, and returns an array of values of type `b`.

```js
// map :: (a -> b) -> [a] -> [b]
const map = (f) => (list) => list.map(f)
```

__Further reading__
* [Ramda's type signatures](https://github.com/ramda/ramda/wiki/Type-Signatures)
* [Mostly Adequate Guide](https://drboolean.gitbooks.io/mostly-adequate-guide/content/ch7.html#whats-your-type)
* [What is Hindley-Milner?](http://stackoverflow.com/a/399392/22425) on Stack Overflow

## Union type
A union type is the combination of two types together into another one.

JS doesn't have static types but let's say we invent a type `NumOrString` which is a sum of `String` and `Number`.

The `+` operator in JS works on strings and numbers so we can use this new type to describe its inputs and outputs:

```js
// add :: (NumOrString, NumOrString) -> NumOrString
const add = (a, b) => a + b

add(1, 2) // Returns number 3
add('Foo', 2) // Returns string "Foo2"
add('Foo', 'Bar') // Returns string "FooBar"
```

Union types are also known as algebraic types, tagged unions, or sum types.

There's a [couple](https://github.com/paldepind/union-type) [libraries](https://github.com/puffnfresh/daggy) in JS which help with defining and using union types.

## Product type

A **product** type combines types together in a way you're probably more familiar with:

```js
// point :: (Number, Number) -> {x: Number, y: Number}
const point = (x, y) => ({x: x, y: y})
```
It's called a product because the total possible values of the data structure is the product of the different values.

See also [Set theory](https://en.wikipedia.org/wiki/Set_theory).

## Option
Option is a [union type](#union-type) with two cases often called `Some` and `None`.

Option is useful for composing functions that might not return a value.

```js
// Naive definition

const Some = (v) => ({
  val: v,
  map (f) {
    return Some(f(this.val))
  },
  chain (f) {
    return f(this.val)
  }
})

const None = () => ({
  map (f) {
    return this
  },
  chain (f) {
    return this
  }
})

// maybeProp :: (String, {a}) -> Option a
const maybeProp = (key, obj) => typeof obj[key] === 'undefined' ? None() : Some(obj[key])
```
Use `chain` to sequence functions that return `Option`s
```js

// getItem :: Cart -> Option CartItem
const getItem = (cart) => maybeProp('item', cart)

// getPrice :: Item -> Option Number
const getPrice = (item) => maybeProp('price', item)

// getNestedPrice :: cart -> Option a
const getNestedPrice = (cart) => getItem(obj).chain(getPrice)

getNestedPrice({}) // None()
getNestedPrice({item: {foo: 1}}) // None()
getNestedPrice({item: {price: 9.99}}) // Some(9.99)
```

`Option` is also known as `Maybe`. `Some` is sometimes called `Just`. `None` is sometimes called `Nothing`.

## Functional Programming Libraries in JavaScript

* [Ramda](https://github.com/ramda/ramda)
* [Folktale](http://folktalejs.org)
* [monet.js](https://cwmyers.github.io/monet.js/)
* [lodash](https://github.com/lodash/lodash)
* [Underscore.js](https://github.com/jashkenas/underscore)
* [Lazy.js](https://github.com/dtao/lazy.js)
* [maryamyriameliamurphies.js](https://github.com/sjsyrek/maryamyriameliamurphies.js)
* [Haskell in ES6](https://github.com/casualjavascript/haskell-in-es6)

---

__P.S:__ This repo is successful due to the wonderful [contributions](https://github.com/hemanth/functional-programming-jargon/graphs/contributors)!
