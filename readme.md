#Jerga de la programacion Funcional.

La programación funcional (FP) ofrece muchas ventajas, y como resultado su popularidad ha ido en aumento. Sin embargo, cada paradigma de programación viene con su propia jerga y la FP no es la excepción. Al proporcionar un glosario de términos, esperamos hacer el aprendizaje de la programación funcional mas simple.

Los ejemplos se presentan usando la sintaxis de JavaScript 2015 (ES2015) [Why JavaScript?](https://github.com/hemanth/functional-programming-jargon/wiki/Why-JavaScript%3F)

*Este es un [WIP](https://github.com/hemanth/functional-programming-jargon/issues/20); por fabor sientase libre de enviarnos un PR ;)*

Cuando sea necesario, este documento usara términos definidos en la especificación de [Fantasy Land spec](https://github.com/fantasyland/fantasy-land)

__Traducciones__
* [Portuguese](https://github.com/alexmoreno/jargoes-programacao-funcional)
* [Spanish](https://github.com/idcmardelplata/functional-programming-jargon)

__Tabla de Contenido__
<!-- RM(noparent,notop) -->

* [Aridad](#arity)
* [Funciones de orden superior (HOF)](#higher-order-functions-hof)
* [Aplicación parcial](#partial-application)
* [Currying](#currying)
* [Auto Currying](#auto-currying)
* [Composición funcional](#function-composition)
* [Continuación](#function-composition)
* [Pureza](#purity)
* [Efectos secundarios](#side-effects)
* [Idempotente](#idempotent)
* [Estilo de punto libre o programación tacita](#point-free-style)
* [Predicado](#predicate)
* [Contratos](#contracts)
* [Funciones guardas](#guarded-functions)
* [Categorías](#categories)
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
* [Co-mónada](#comonad)
* [Funtor Aplicativo](#applicative-functor)
* [Morfismo](#morphism)
  * [Endomorfismo](#endomorphism)
  * [Isomorfismo](#isomorphism)
* [Setoide](#setoid)
* [Semigrupo](#semigroup)
* [Plegable](#foldable)
* [Traversable](#traversable)
* [Signatura de tipos](#type-signatures)
* [Tipos de unión](#union-type)
* [Tipos de producto](#product-type)
* [Opcional](#option)
* [Librerías para la programación funcional en JavaScript](#functional-programming-libraries-in-javascript)


<!-- /RM -->

## Aridad

El numero de argumentos que una función toma. Utiliza términos como unario, binario, ternario etc. Esta palabra tiene la distinción de estar compuesta de dos sufijos, "Ary" y "ity" Adicionalmente por ejemplo, toma dos argumentos, y así se define como una función binaria o una función de aridad dos. Tal función a veces puede ser llamada "diadica" por personas que prefieren las raíces griegas al latín. Del mismo modo, una función que toma un numero variable de argumentos se denomina "variadic", mientras que una función binaria solamente toma dos y nada mas que dos argumentos. Vea mas adelante curring y aplicación parcial.


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

## Continuación
En cualquier punto dado de un programa, la parte del codigo que aun esta por ejecutarse se conoce como continuación.

```js
const printAsString = (num) => console.log(`Dado ${num}`)

const addOneAndContinue = (num, cc) => {
  const result = num + 1
  cc(result)
}

addOneAndContinue(2, printAsString) // 'Dado 3'
```

Las continuaciones se ven a menudo en la programacion asyncrona, cuando el programa necesita esperar a recibir datos antes de que pueda continuar. La respuesta a menudo se pasa al resto del programa, que es la continuacion, una vez que se ha recibido.

```js
const continueProgramWith = (data) => {
  // Continues program with data
}

readFileAsync('path/to/file', (err, response) => {
  if (err) {
    // handle error
    return
  }
  continueProgramWith(response)
})
```

## Pureza

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

Una funcion anonima que puede ser tratada igual que un valor.

```js
;(function (a) {
  return a + 1
})

;(a) => a + 1
```
Las funciones lambda se suelen pasar a menudo como argumentos a las funciones de orden superior.

```js
[1, 2].map((a) => a + 1) // [2, 3]
```

Tambien puede asignarse una funcion lambda a una variable.

```js
const add1 = (a) => a + 1
```

## Lambda Calculus

Una rama de las matematicas que utiliza funciones para crear un [modelo universal de computacion](https://en.wikipedia.org/wiki/Lambda_calculus).

## Evaluacion Perezosa

La evaluación perezosa es un mecanismo de evaluacion llamado por necesidad, este retrasa la evaluacion de una expresion hasta que su valor sea necesario.
En lenguajes funcionales, esto permite estructuras como listas infinitas, que normalmente no estarian disponible en un lenguaje imperativo donde la secuencia de comandos es significativa.

```js
const rand = function*() {
  while (1 < 2) {
    yield Math.random()
  }
}
```

```js
const randIter = rand()
randIter.next() // Cada ejecucion obtiene un valor aleatorio, la expresion es evaluada por necesidad.
```

## Monoide

Objeto con una funcion que "combina" ese objeto con otro del mismo tipo.

Un simple monoide es la suma de numeros.

```js
1 + 1 // 2
```

En este caso los numeros son los objetos y `+` es la funcion.

Tambien debe existir un valor "identidad" que cuando se combina con un valor no lo cambia.

El valor identidad para la suma es el  `0`.
```js
1 + 0 // 1
```

Tambien es necesario que el agrupamiento de operaciones no altere el resultado (asociatividad).

```js
1 + (2 + 3) === (1 + 2) + 3 // true
```

La concatenacion de los Arrays tambien forma un monoide.

```js
;[1, 2].concat([3, 4]) // [1, 2, 3, 4]
```

 El valor identidad es un array vacio `[]`.

```js
;[1, 2].concat([]) // [1, 2]
```

Si se proporcionan funciones de identidad y composicion, las funciones mismas forman un monoide:

```js
const identity = (a) => a
const compose = (f, g) => (x) => f(g(x))
```
`foo` es cualquier funcion que toma un argumento.
```
compose(foo, identity) ≍ compose(identity, foo) ≍ foo
```

## Monada

Una monada es un objeto con dos funciones [`of`](#pointed-functor) y `chain`, `chain`  es similar a map excepto que anula el objeto anidado resultante.

```js
// Implementación
Array.prototype.chain = function (f) {
  return this.reduce((acc, it) => acc.concat(f(it)), [])
}

// Forma de uso.
;Array.of('cat,dog', 'fish,bird').chain((a) => a.split(',')) // ['cat', 'dog', 'fish', 'bird']

// En cambio con map.
;Array.of('cat,dog', 'fish,bird').map((a) => a.split(',')) // [['cat', 'dog'], ['fish', 'bird']]
```

`of` tambien es conocido como `return` en otros lenguajes funcionales.
`chain` tambien se conoce como `flatmap` o `bind` en otros lenguajes.

## Co-mónada

Una comonada es una monada que implementa las funciones `extract` y `extend`.

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

Extract toma un valor fuera de un functor.

```js
CoIdentity(1).extract() // 1
```

Extend ejecuta una función en el co-monad. La funcion deve devolver el mismo tipo que el co-monad.

```js
CoIdentity(1).extend((co) => co.extract() + 1) // CoIdentity(2)
```

## Funtor Aplicativo.

Un functor aplicativo es un functor con una funcion `ap`. Esa funcion `ap` lo que hace es aplicar una funcion en el functor a un valor en otro functor del mismo tipo.

Un ejemplo básico puede ser este:
```js
//Constructor
var Functor = function(value) {
  this.__value = value;
}

//Pointed Functor.
Functor.of = function(value) {
  return new Functor(value);
}
Functor.prototype.map = function(fn) {
  return Functor.of(fn(this.__value));
}

//Aplicative Functor.
Functor.prototype.ap = function(another_functor) {
  return another_functor.map(this.__value);
}

//Dummy function
multiply = (x) => (y) => x * y

let result = Functor.of(multiply)
                    .ap(Functor.of(10))
                    .ap(Functor.of(30))

console.log(result) // Functor { __value: 300 }

```
Con otro ejemplo, vemos claramente como podemos utilizar este tipo de functores con `Array`s.

```js
// Implementación
Array.prototype.ap = function (xs) {
  return this.reduce((acc, f) => acc.concat(xs.map(f)), [])
}

// Ejemplo de uso:
;[(a) => a + 1].ap([1]) // [2]
```

Esto es especialmente util, si se tiene 2 objetos y se desea aplicar una funcion binaria a su contenido.

```js
// Arrays que buscamos combinar.
const arg1 = [1, 3]
const arg2 = [4, 5]

// Funcion de combinacion- debe ser curry para que esto funcione.
const add = (x) => (y) => x + y

const partiallyAppliedAdds = [add].ap(arg1) // [(y) => 1 + y, (y) => 3 + y]
```

Esto nos da un array de funciones que podemos llamar a trabes del metodo `ap` para obtener el resultado:

```js
partiallyAppliedAdds.ap(arg2) // [5, 6, 7, 8]
```

## Morfismo

Una funcion de transformación.

### Endomorfismo

Una funcion donde el tipo de entrada es el mismo que el de salida.

```js
// uppercase :: String -> String
const uppercase = (str) => str.toUpperCase()

// decrement :: Number -> Number
const decrement = (x) => x - 1
```

### Isomorfismo

Un par de transformaciones entre 2 tipos de objetos de naturaleza estructural y donde no se pierden datos.

Por ejemplo, las coordenadas 2D podrian ser almacenadas como un array `[2,3]` o como un objeto `{x: 2, y: 3}`,

```js
// Proporcionar funciones para convertirlas en ambas direcciones hace que sea isomorfas.
const pairToCoords = (pair) => ({x: pair[0], y: pair[1]})

const coordsToPair = (coords) => [coords.x, coords.y]

coordsToPair(pairToCoords([1, 2])) // [1, 2]

pairToCoords(coordsToPair({x: 1, y: 2})) // {x: 1, y: 2}
```

## Setoide

Un objeto que tiene una funcion `equals` que puede utilizarse para comparar otros objetos del mismo tipo.

Transformemos el Array en un setoide:

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

## Semigrupo

Objeto que tiene una funcion `concat` que la combina con otro objeto del mismo tipo.

```js
;[1].concat([2]) // [1, 2]
```

## Plegable

Objeto que tiene una funcion `reduce` que puede transformar ese objeto en otro tipo.

```js
const sum = (list) => list.reduce((acc, val) => acc + val, 0)
sum([1, 2, 3]) // 6
```

## Traversable

TODO

## Signatura de tipos.
A menudo, las funciones en JavaScript incluiran comentarios que indican los tipos de sus argumentos y valores de retorno.

Hay un poco de variación en la comunidad, pero a menudo se suelen seguir los siguientes patrones:

```js
// functionName :: firstArgType -> secondArgType -> returnType

// add :: Number -> Number -> Number
const add = (x) => (y) => x + y

// increment :: Number -> Number
const increment = (x) => x + 1
```

Si una funcion acepta otra funcion como argumento, esta se envuelve entre parentesis.

```js
// call :: (a -> b) -> a -> b
const call = (f) => (x) => f(x)
```

Las letras `a`, `b`, `c`, `d` son usadas para indicar que el argumento puede ser de cualquier tipo. La siguiente version de `map` toma una funcion que transforma un valor de algun tipo `a` en otro valor de tipo `b`, un array de valores de tipo `a`, y retorna una matriz de valores de tipo `b`.

```js
// map :: (a -> b) -> [a] -> [b]
const map = (f) => (list) => list.map(f)
```

__Otras lecturas__
* [Ramda's type signatures](https://github.com/ramda/ramda/wiki/Type-Signatures)
* [Mostly Adequate Guide](https://drboolean.gitbooks.io/mostly-adequate-guide/content/ch7.html#whats-your-type)
* [What is Hindley-Milner?](http://stackoverflow.com/a/399392/22425) en Stack Overflow

## Tipos de Union

Un tipo union es la combinación de dos tipos juntos en otro.

JS no tiene tipos estaticos, pero supongamos que inventamos un tipo `NumOrString` que es la suma de `String` y `Number`.

El operador `+` en JS trabaja en strings y numeros, entonces para poder usar este nuevo tipo, necesitamos describir sus entradas y salidas.

```js
// add :: (NumOrString, NumOrString) -> NumOrString
const add = (a, b) => a + b

add(1, 2) // Returns number 3
add('Foo', 2) // Returns string "Foo2"
add('Foo', 'Bar') // Returns string "FooBar"
```

Los tipos de union tambien se conocen como tipos algebraicos, uniones marcadas o tipos de suma.

[Aqui](https://github.com/paldepind/union-type) hay un par de [librerias](https://github.com/puffnfresh/daggy) en JavaScript que ayudan a definir y a usar tipos de union.

## Tipos de producto.

Un tipo **producto** combina tipos de una manera que probablemente este mas familiarizado con:

```js
// point :: (Number, Number) -> {x: Number, y: Number}
const point = (x, y) => ({x: x, y: y})
```
Se llama un producto porque el total de valores posibles de la estructura de datos es el producto de los diferentes valores.

Vease tambien [Teoria de conjuntos](https://en.wikipedia.org/wiki/Set_theory).

## Opcional

Option es un [tipo de union](#union-type) con dos casos, a menudo llamados `Some` y `None`.

Option es util para componer funciones que no retornen un valor.


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
Utilice `chain` para secuenciar funciones que retornan `Option`s

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

`Option` a menudo tambien es conocido como `Maybe`. `Some` es algunas veces llamado `Just`. y `None` es algunas veces llamado  `Nothing`.

## Librerias para la Programacion Funcional en JavaScriṕt.

* [mori](https://github.com/swannodette/mori)
* [Ramda](https://github.com/ramda/ramda)
* [Folktale](http://folktalejs.org)
* [monet.js](https://cwmyers.github.io/monet.js/)
* [lodash](https://github.com/lodash/lodash)
* [Underscore.js](https://github.com/jashkenas/underscore)
* [Lazy.js](https://github.com/dtao/lazy.js)
* [maryamyriameliamurphies.js](https://github.com/sjsyrek/maryamyriameliamurphies.js)
* [Haskell in ES6](https://github.com/casualjavascript/haskell-in-es6)

---

__P.S:__ Este repositorio es exitoso debido a las maravillosas [contribuciones](https://github.com/hemanth/functional-programming-jargon/graphs/contributors)!
