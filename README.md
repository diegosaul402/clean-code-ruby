# clean-code-ruby
*Esta es una traducción libre al español de [clean-code-ruby](https://github.com/uohzxela/clean-code-ruby) y puede estar sujeta a errores. Los códigos de ejemplo no serán modificados para evitar incosistencias con el material original.*

Conceptos de Clean Code (Código Limpio) adaptados para Ruby.

Inspirado por [clean-code-javascript](https://github.com/ryanmcdermott/clean-code-javascript).

*Nota: Los ejemplos han sido traídos en su mayoría de JavaScript y pueden no ser idiomáticos. Siéntase libre de señalar cualquier código no idiomático de Ruby creando un issue o enviando un pull request. Todas las contribuciones son bienvenidas!*

## Tabla de contenido
  1. [Introducción](#Introducción)
  2. [Variables](#variables)
  3. [Methods](#methods)
  4. [Objects and Data Structures](#objects-and-data-structures)
  5. [Classes](#classes)
  6. [SOLID](#solid)
  7. [Testing](#testing)
  9. [Error Handling](#error-handling)
  10. [Formatting](#formatting)
  11. [Comments](#comments)
  12. [Translations](#translations)

## Introducción
![Humorous image of software quality estimation as a count of how many expletives
you shout when reading code](http://www.osnews.com/images/comics/wtfm.jpg)

Los Principios de la Ingeniería de Software, del libro de Robert C. Martin
[*Clean Code*](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882),
adaptados para Ruby. Esta no es una guía de estilo. Es una guía para producir software 
[legible, reusable, y refactorizable](https://github.com/ryanmcdermott/3rs-of-software-architecture) en Ruby.

Los principios aquí presentados no deben ser seguidos de manera estricta, y aún menos
ejemplos son universalmente aceptados. Son simplemente pautas, pero que han sido creadas durante 
los muchos años de experiencia colectiva de los autores de *Código Limpio*.

Nuestro oficio en la Ingeniería de Software tiene sólo un poco más de 50 años y
todavía seguimos aprendiendo mucho. Cuando la arquitectura de software es tan
vieja como la rquitectura misma, tendremos reglas muy difíciles de seguir. Por el momento,
dejemos que estas guías sirvan como pauta para que usted y su equipo evalúen la
calidad del código que producen.

One more thing: knowing these won't immediately make you a better software
developer, and working with them for many years doesn't mean you won't make
mistakes. Every piece of code starts as a first draft, like wet clay getting
shaped into its final form. Finally, we chisel away the imperfections when
we review it with our peers. Don't beat yourself up for first drafts that need
improvement. Beat up the code instead!

Una cosa más: saber estas pautas no lo hará inmediatamente un mejor desarrollador
de software, y trabajar con ellas durante muchos años no significa que no cometerá errores.
Cada pieza de código empieza como un borrador, así como la arcilla siendo amazada hasta 
su forma final. Finalmente pulimos cualquier imperfección cuando lo revisamos con
nuestros pares. No se castigue por sus primeros borradores que necesitan mejoras.
Castigue al código en su lugar!

## **Variables**
### Usa nombres de variables pronunciables y con significado 

**Mal:**
```ruby
yyyymmdstr = Time.now.strftime('%Y/%m/%d')
```

**Bien:**
```ruby
current_date = Time.now.strftime('%Y/%m/%d')
```
**[⬆ volver arriba](#tabla-de-contenido)**

### Usa el mismo vocabulario para el mismo tipo de variable

Elige un concepto y mantente con él.

**Mal:**
```ruby
user_info
user_data
user_record

starts_at
start_at
start_time
```

**Bien:**
```ruby
user

starts_at
```
**[⬆ volver arriba](#tabla-de-contenido)**

### Usa constantes y nombres que puedas buscar

Leeremos más código que el que alguna vez escribiremos. Es importante que el código
que escribamos sea legible y se pueda buscar.
Herimos a nuestors lectores al **no** nombrar variables que sean significativas para
entender nuestro programa. Crea variables que puedas buscar.

Tambien, crea constantes en lugar de *hardcodear* valores y crear "números mágicos".

**Mal:**
```ruby
# Qué demonios es 86_400?
status = Timeout::timeout(86_400) do
  # ...
end
```

**Bien:**
```ruby
# Declárala como una constante.
SECONDS_IN_A_DAY = 86_400

status = Timeout::timeout(SECONDS_IN_A_DAY) do
  # ...
end
```
**[⬆ volver arriba](#tabla-de-contenido)**

### Usa variables explicativas
**Mal:**
```ruby
address = 'One Infinite Loop, Cupertino 95014'
city_zip_code_regex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/
save_city_zip_code(city_zip_code_regex.match(address)[1], city_zip_code_regex.match(address)[2])
```

**Bien:**
```ruby
address = 'One Infinite Loop, Cupertino 95014'
city_zip_code_regex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/
_, city, zip_code = city_zip_code_regex.match(address).to_a
save_city_zip_code(city, zip_code)
```
**[⬆ volver arriba](#tabla-de-contenido)**

### Evita el mapeo mental
Explícito es mejor que implícito.

**Mal:**
```ruby
locations = ['Austin', 'New York', 'San Francisco']
locations.each do |l|
  do_stuff
  do_some_other_stuff
  # ...
  # ...
  # ...
  # Espera, para qué es 'l'?
  dispatch(l)
end
```

**Bien:**
```ruby
locations = ['Austin', 'New York', 'San Francisco']
locations.each do |location|
  do_stuff
  do_some_other_stuff
  # ...
  # ...
  # ...
  dispatch(location)
end
```
**[⬆ volver arriba](#tabla-de-contenido)**

### No agregues contexto innecesario
Si el nombre de tu clase/objeto te dice algo, no repitas el nombre en tu variable.

**Mal:**
```ruby
car = {
  car_make: 'Honda',
  car_model: 'Accord',
  car_color: 'Blue'
}

def paint_car(car)
  car[:car_color] = 'Red'
end
```

**Bien:**
```ruby
car = {
  make: 'Honda',
  model: 'Accord',
  color: 'Blue'
}

def paint_car(car)
  car[:color] = 'Red'
end
```
**[⬆ volver arriba](#tabla-de-contenido)**

### Usa argumentos por defecto en lugar de evaluaciones de cortocircuito
Los argumentos por defecto son por lo general más limpios que las [evaluaciones de cortocircuito](https://es.wikipedia.org/wiki/Evaluación_de_cortocircuito).
Sé consciente de que tu método sólo proveerá valores por defecto para argumentos indefinidos si las usas.
Otros valores "falsy" como `false` y `nil` no serán reempalzados por un valor por defecto.


**Mal:**
```ruby
def create_micro_brewery(name)
  brewery_name = name || 'Hipster Brew Co.'
  # ...
end
```

**Bien:**
```ruby
def create_micro_brewery(brewery_name = 'Hipster Brew Co.')
  # ...
end
```
**[⬆ volver arriba](#tabla-de-contenido)**

## **Methods**
### Method arguments (2 or fewer ideally)
Limiting the amount of method parameters is incredibly important because it
makes testing your method easier. Having more than three leads to a
combinatorial explosion where you have to test tons of different cases with
each separate argument.

One or two arguments is the ideal case, and three should be avoided if possible.
Anything more than that should be consolidated. Usually, if you have
more than two arguments then your method is trying to do too much. In cases
where it's not, most of the time a higher-level object will suffice as an
argument. Or you can pass data to the method by instance variables.

Since Ruby allows you to make objects on the fly, without a lot of class
boilerplate, you can use an object if you are finding yourself needing a
lot of arguments. The prevailing pattern in Ruby is to use a hash of arguments.

To make it obvious what properties the method expects, you can use the keyword arguments syntax (introduced in Ruby 2.1). This has a few advantages:

1. When someone looks at the method signature, it's immediately clear what
properties are being used.
2. If a required keyword argument is missing, Ruby will raise a useful `ArgumentError` that tells us which required argument we must include.

**Mal:**
```ruby
def create_menu(title, body)
  # ...
end
```

**Bien:**
```ruby
def create_menu(title:, body:)
  # ...
end

create_menu(title: 'Foo', body: 'Bar')
```
**[⬆ volver arriba](#tabla-de-contenido)**


### Methods should do one thing
This is by far the most important rule in software engineering. When methods
do more than one thing, they are harder to compose, test, and reason about.
When you can isolate a method to just one action, they can be refactored
easily and your code will read much cleaner. If you take nothing else away from
this guide other than this, you'll be ahead of many developers.

**Mal:**
```ruby
def email_clients(clients)
  clients.each do |client|
    client_record = database.lookup(client)
    email(client) if client_record.active?
  end
end

email_clients(clients)
```

**Bien:**
```ruby
def email_clients(clients)
  clients.each { |client| email(client) }
end

def active(objects)
  objects.select { |object| active?(object) }
end

def active?(object)
  record = database.lookup(object)
  record.active?
end

email_clients(active(clients))
```
**[⬆ volver arriba](#tabla-de-contenido)**

### Method names should say what they do
Poorly named methods add to the code reviewer's cognitive load at best, and mislead the
code reviewer at worst. Strive to capture the precise intent when naming methods.

**Mal:**
```ruby
def add_to_date(date, month)
  # ...
end

date = DateTime.now

# It's hard to tell from the method name what is added
add_to_date(date, 1)
```

**Bien:**
```ruby
def add_month_to_date(date, month)
  # ...
end

date = DateTime.now
add_month_to_date(date, 1)
```
**[⬆ volver arriba](#tabla-de-contenido)**

### Methods should only be one level of abstraction
When you have more than one level of abstraction your method is usually
doing too much. Splitting up methods leads to reusability and easier
testing. Furthermore, methods should descend by the level of abstraction: one very abstract method should call methods that are less abstract and so on.

**Mal:**
```ruby
def interpret(code)
  regexes = [
    # ...
  ]
  statements = code.split(' ')

  tokens = regexes.each_with_object([]) do |regex, memo|
    statements.each do |statement|
      # memo.push(...)
    end
  end

  ast = tokens.map do |token|
    # ...
  end

  ast.map do |node|
    # ...
  end
end
```

**Bien:**
```ruby
def interpret(code)
  tokens = tokenize(code)
  ast = lex(tokens)
  parse(ast)
end

def tokenize(code)
  regexes = [
    # ...
  ]
  statements = code.split(' ')

  regexes.each_with_object([]) do |regex, tokens|
    statements.each do |statement|
      # tokens.push(...)
    end
  end
end

def lex(tokens)
  tokens.map do |token|
    # ...
  end
end

def parse(ast)
  ast.map do |node|
    # ...
  end
end
```
**[⬆ volver arriba](#tabla-de-contenido)**

### Remove duplicate code
Do your absolute best to avoid duplicate code. Duplicate code is bad because it
means that there's more than one place to alter something if you need to change
some logic.

Imagine if you run a restaurant and you keep track of your inventory: all your
tomatoes, onions, garlic, spices, etc. If you have multiple lists that
you keep this on, then all have to be updated when you serve a dish with
tomatoes in them. If you only have one list, there's only one place to update!

Oftentimes you have duplicate code because you have two or more slightly
different things, that share a lot in common, but their differences force you
to have two or more separate methods that do much of the same things. Removing
duplicate code means creating an abstraction that can handle this set of
different things with just one method/module/class.

Getting the abstraction right is critical, that's why you should follow the
SOLID principles laid out in the *Classes* section. Bad abstractions can be
worse than duplicate code, so be careful! Having said this, if you can make
a good abstraction, do it! Don't repeat yourself, otherwise you'll find yourself
updating multiple places anytime you want to change one thing.

**Mal:**
```ruby
def show_developer_list(developers)
  developers.each do |developer|
    data = {
      expected_salary: developer.expected_salary,
      experience: developer.experience,
      github_link: developer.github_link
    }

    render(data)
  end
end

def show_manager_list(managers)
  managers.each do |manager|
    data = {
      expected_salary: manager.expected_salary,
      experience: manager.experience,
      portfolio: manager.mba_projects
    }

    render(data)
  end
end
```

**Bien:**
```ruby
def show_employee_list(employees)
  employees.each do |employee|
    data = build_data(employee)
    render(data)
  end
end

def build_data(employee)
  general_data = {
    expected_salary: employee.expected_salary,
    experience: employee.experience
  }

  general_data.merge(position_specific_data(employee))
end

def position_specific_data(employee)
  case employee.type
  when 'manager'
    { portfolio: employee.mba_projects }
  when 'developer'
    { github_link: employee.github_link }
  end
end
```
**[⬆ volver arriba](#tabla-de-contenido)**

### Don't use flags as method parameters
Flags tell your user that this method does more than one thing. Methods should do one thing. Split out your methods if they are following different code paths based on a boolean.

**Mal:**
```ruby
def create_file(name, temp)
  if temp
    fs.create("./temp/#{name}")
  else
    fs.create(name)
  end
end
```

**Bien:**
```ruby
def create_file(name)
  fs.create(name)
end

def create_temp_file(name)
  create_file("./temp/#{name}")
end
```
**[⬆ volver arriba](#tabla-de-contenido)**

### Avoid Side Effects (part 1)
A method produces side effects if it does anything more than take values and/or
return values. A side effect could be writing to a file,
modifying some global variable, or accidentally wiring all your money to a
stranger.

Now, you do need to have side effects in a program on occasion. Like the previous
example, you might need to write to a file. What you want to do is to
centralize where you are doing this. Don't have several methods and classes
that write to a particular file. Have one service that does it. One and only one.

The main point is to avoid common pitfalls like sharing state between objects
without any structure, using mutable data types that can be written to by anything,
and not centralizing where your side effects occur. If you can do this, you will
be happier than the vast majority of other programmers.

**Mal:**
```ruby
# Global variable referenced by following method.
# If we had another method that used this name, now it'd be an array and it could break it.
$name = 'Ryan McDermott'

def split_into_first_and_last_name
  $name = $name.split(' ')
end

split_into_first_and_last_name()

puts $name # ['Ryan', 'McDermott']
```

**Bien:**
```ruby
def split_into_first_and_last_name(name)
  name.split(' ')
end

name = 'Ryan McDermott'
first_and_last_name = split_into_first_and_last_name(name)

puts name # 'Ryan McDermott'
puts first_and_last_name # ['Ryan', 'McDermott']
```
**[⬆ volver arriba](#tabla-de-contenido)**

### Avoid Side Effects (part 2)
In Ruby, everything is an object and everything is passed by value, but these values are references to objects. In the case of objects and arrays, if your method makes a change
in a shopping cart array, for example, by adding an item to purchase,
then any other method that uses that `cart` array will be affected by this
addition. That may be great, however it can be bad too. Let's imagine a bad
situation:

The user clicks the "Purchase", button which calls a `purchase` method that
spawns a network request and sends the `cart` array to the server. Because
of a bad network connection, the `purchase` method has to keep retrying the
request. Now, what if in the meantime the user accidentally clicks "Add to Cart"
button on an item they don't actually want before the network request begins?
If that happens and the network request begins, then that purchase method
will send the accidentally added item because it has a reference to a shopping
cart array that the `add_item_to_cart` method modified by adding an unwanted
item.

A great solution would be for the `add_item_to_cart` to always clone the `cart`,
edit it, and return the clone. This ensures that no other methods that are
holding onto a reference of the shopping cart will be affected by any changes.

Two caveats to mention to this approach:
  1. There might be cases where you actually want to modify the input object,
but when you adopt this programming practice you will find that those cases
are pretty rare. Most things can be refactored to have no side effects!

  2. Cloning big objects can be very expensive in terms of performance. Luckily,
this isn't a big issue in practice because there are
[great gems](https://github.com/hamstergem/hamster) that allow
this kind of programming approach to be fast and not as memory intensive as
it would be for you to manually clone objects and arrays.

**Mal:**
```ruby
def add_item_to_cart(cart, item)
  cart.push(item: item, time: Time.now)
end
```

**Bien:**
```ruby
def add_item_to_cart(cart, item)
  cart + [{ item: item, time: Time.now }]
end
```
**[⬆ volver arriba](#tabla-de-contenido)**

### Favor functional programming over imperative programming
Ruby isn't a functional language in the way that Haskell is, but it has
a functional flavor to it. Functional languages are cleaner and easier to test.
Favor this style of programming when you can.

**Mal:**
```ruby
programmer_output = [
  {
    name: 'Uncle Bobby',
    lines_of_code: 500
  }, {
    name: 'Suzie Q',
    lines_of_code: 1500
  }, {
    name: 'Jimmy Gosling',
    lines_of_code: 150
  }, {
    name: 'Grace Hopper',
    lines_of_code: 1000
  }
]

total_output = 0

programmer_output.each do |output|
  total_output += output[:lines_of_code]
end
```

**Bien:**
```ruby
programmer_output = [
  {
    name: 'Uncle Bobby',
    lines_of_code: 500
  }, {
    name: 'Suzie Q',
    lines_of_code: 1500
  }, {
    name: 'Jimmy Gosling',
    lines_of_code: 150
  }, {
    name: 'Grace Hopper',
    lines_of_code: 1000
  }
]

INITIAL_VALUE = 0

total_output = programmer_output.sum(INITIAL_VALUE) { |output| output[:lines_of_code] }
```
**[⬆ volver arriba](#tabla-de-contenido)**

### Encapsulate conditionals

**Mal:**
```ruby
if params[:message].present? && params[:recipient].present?
  # ...
end
```

**Bien:**
```ruby
def send_message?(params)
  params[:message].present? && params[:recipient].present?
end

if send_message?(params)
  # ...
end
```
**[⬆ volver arriba](#tabla-de-contenido)**

### Avoid negative conditionals

**Mal:**
```ruby
if !genres.blank?
  # ...
end
```

**Bien:**
```ruby
unless genres.blank?
  # ...
end

# or

if genres.present?
  # ...
end
```
**[⬆ volver arriba](#tabla-de-contenido)**

### Avoid conditionals
This seems like an impossible task. Upon first hearing this, most people say,
"how am I supposed to do anything without an `if` statement?" The answer is that
you can use polymorphism to achieve the same task in many cases. The second
question is usually, "well that's great but why would I want to do that?" The
answer is a previous clean code concept we learned: a method should only do
one thing. When you have classes and methods that have `if` statements, you
are telling your user that your method does more than one thing. Remember,
just do one thing.

**Mal:**
```ruby
class Airplane
  # ...
  def cruising_altitude
    case @type
    when '777'
      max_altitude - passenger_count
    when 'Air Force One'
      max_altitude
    when 'Cessna'
      max_altitude - fuel_expenditure
    end
  end
end
```

**Bien:**
```ruby
class Airplane
  # ...
end

class Boeing777 < Airplane
  # ...
  def cruising_altitude
    max_altitude - passenger_count
  end
end

class AirForceOne < Airplane
  # ...
  def cruising_altitude
    max_altitude
  end
end

class Cessna < Airplane
  # ...
  def cruising_altitude
    max_altitude - fuel_expenditure
  end
end
```
**[⬆ volver arriba](#tabla-de-contenido)**

### Avoid type-checking (part 1)
Ruby is dynamically typed, which means your methods can take any type of argument.
Sometimes you are bitten by this freedom and it becomes tempting to do
type-checking in your methods. There are many ways to avoid having to do this.
The first thing to consider is consistent APIs.

**Mal:**
```ruby
def travel_to_texas(vehicle)
  if vehicle.is_a?(Bicycle)
    vehicle.pedal(@current_location, Location.new('texas'))
  elsif vehicle.is_a?(Car)
    vehicle.drive(@current_location, Location.new('texas'))
  end
end
```

**Bien:**
```ruby
def travel_to_texas(vehicle)
  vehicle.move(@current_location, Location.new('texas'))
end
```
**[⬆ volver arriba](#tabla-de-contenido)**

### Avoid type-checking (part 2)
If you are working with basic values like strings and integers,
and you can't use polymorphism but you still feel the need to type-check,
you should consider using [contracts.ruby](https://github.com/egonSchiele/contracts.ruby). The problem with manually type-checking Ruby is that
doing it well requires so much extra verbiage that the faux "type-safety" you get
doesn't make up for the lost readability. Keep your Ruby clean, write
good tests, and have good code reviews.

**Mal:**
```ruby
def combine(val1, val2)
  if (val1.is_a?(Numeric) && val2.is_a?(Numeric)) ||
     (val1.is_a?(String) && val2.is_a?(String))
    return val1 + val2
  end

  raise 'Must be of type String or Numeric'
end
```

**Bien:**
```ruby
def combine(val1, val2)
  val1 + val2
end
```
**[⬆ volver arriba](#tabla-de-contenido)**

### Remove dead code
Dead code is just as bad as duplicate code. There's no reason to keep it in
your codebase. If it's not being called, get rid of it! It will still be safe
in your version history if you still need it.

**Mal:**
```ruby
def old_request_module(url)
  # ...
end

def new_request_module(url)
  # ...
end

req = new_request_module(request_url)
inventory_tracker('apples', req, 'www.inventory-awesome.io')
```

**Bien:**
```ruby
def new_request_module(url)
  # ...
end

req = new_request_module(request_url)
inventory_tracker('apples', req, 'www.inventory-awesome.io')
```
**[⬆ volver arriba](#tabla-de-contenido)**

## **Objects and Data Structures**
### Use getters and setters
Using getters and setters to access data on objects could be better than simply
looking for a property on an object. "Why?" you might ask. Well, here's an
unorganized list of reasons why:

* When you want to do more beyond getting an object property, you don't have
to look up and change every accessor in your codebase.
* Makes adding validation simple when doing a `set`.
* Encapsulates the internal representation.
* Easy to add logging and error handling when getting and setting.
* You can lazy load your object's properties, let's say getting it from a
server.


**Mal:**
```ruby
def make_bank_account
  # ...

  {
    balance: 0
    # ...
  }
end

account = make_bank_account
account[:balance] = 100
account[:balance] # => 100
```

**Bien:**
```ruby
class BankAccount
  def initialize
    # this one is private
    @balance = 0
  end

  # a "getter" via a public instance method
  def balance
    # do some logging
    @balance
  end

  # a "setter" via a public instance method
  def balance=(amount)
    # do some logging
    # do some validation
    @balance = amount
  end
end

account = BankAccount.new
account.balance = 100
account.balance # => 100
```

Alternatively, if your getters and setters are absolutely trivial, you should use `attr_accessor` to define them. This is especially convenient for implementing data-like objects which expose data to other parts of the system (e.g., ActiveRecord objects, response wrappers for remote APIs).

**Bien:**
```ruby
class Toy
  attr_accessor :price
end

toy = Toy.new
toy.price = 50
toy.price # => 50
```

However, you have to be aware that in some situations, using `attr_accessor` is a code smell, read more [here](https://solnic.codes/2012/04/04/get-rid-of-that-code-smell-attributes).

**[⬆ volver arriba](#tabla-de-contenido)**


## **Classes**

### Avoid fluent interfaces
A [Fluent interface](https://en.wikipedia.org/wiki/Fluent_interface) is an object
oriented API that aims to improve the readability of the source code by using
[method chaining](https://en.wikipedia.org/wiki/Method_chaining).

While there can be some contexts, frequently builder objects, where this
pattern reduces the verbosity of the code (e.g., ActiveRecord queries),
more often it comes at some costs:

1. Breaks [Encapsulation](https://en.wikipedia.org/wiki/Encapsulation_%28object-oriented_programming%29)
2. Breaks [Decorators](https://en.wikipedia.org/wiki/Decorator_pattern)
3. Is harder to [mock](https://en.wikipedia.org/wiki/Mock_object) in a test suite
4. Makes diffs of commits harder to read

For more information you can read the full [blog post](https://ocramius.github.io/blog/fluent-interfaces-are-evil/)
on this topic written by [Marco Pivetta](https://github.com/Ocramius).

**Mal:**
```ruby
class Car
  def initialize(make, model, color)
    @make = make
    @model = model
    @color = color
    # NOTE: Returning self for chaining
    self
  end

  def set_make(make)
    @make = make
    # NOTE: Returning self for chaining
    self
  end

  def set_model(model)
    @model = model
    # NOTE: Returning self for chaining
    self
  end

  def set_color(color)
    @color = color
    # NOTE: Returning self for chaining
    self
  end

  def save
    # save object...
    # NOTE: Returning self for chaining
    self
  end
end

car = Car.new('Ford','F-150','red')
  .set_color('pink')
  .save
```

**Bien:**
```ruby
class Car
  attr_accessor :make, :model, :color

  def initialize(make, model, color)
    @make = make
    @model = model
    @color = color
  end

  def save
    # Save object...
  end
end

car = Car.new('Ford', 'F-150', 'red')
car.color = 'pink'
car.save
```
**[⬆ volver arriba](#tabla-de-contenido)**

### Prefer composition over inheritance
As stated famously in [*Design Patterns*](https://en.wikipedia.org/wiki/Design_Patterns) by the Gang of Four,
you should prefer composition over inheritance where you can. There are lots of
good reasons to use inheritance and lots of good reasons to use composition.
The main point for this maxim is that if your mind instinctively goes for
inheritance, try to think if composition could model your problem better. In some
cases, it can.

You might be wondering then, "when should I use inheritance?" It
depends on your problem at hand, but this is a decent list of when inheritance
makes more sense than composition:

1. Your inheritance represents an "is-a" relationship and not a "has-a"
relationship (Human->Animal vs. User->UserDetails).
2. You can reuse code from the base classes (Humans can move like all animals).
3. You want to make global changes to derived classes by changing a base class.
(Change the caloric expenditure of all animals when they move).

**Mal:**
```ruby
class Employee
  def initialize(name, email)
    @name = name
    @email = email
  end

  # ...
end

# Bad because Employees "have" tax data. EmployeeTaxData is not a type of Employee
class EmployeeTaxData < Employee
  def initialize(ssn, salary)
    @ssn = ssn
    @salary = salary
  end

  # ...
end
```

**Bien:**
```ruby
class EmployeeTaxData
  def initialize(ssn, salary)
    @ssn = ssn
    @salary = salary
  end

  # ...
end

class Employee
  def initialize(name, email)
    @name = name
    @email = email
  end

  def set_tax_data(ssn, salary)
    @tax_data = EmployeeTaxData.new(ssn, salary)
  end
  # ...
end
```
**[⬆ volver arriba](#tabla-de-contenido)**

## **SOLID**
### Single Responsibility Principle (SRP)
As stated in Clean Code, "There should never be more than one reason for a class
to change". It's tempting to jam-pack a class with a lot of functionality, like
when you can only take one suitcase on your flight. The issue with this is
that your class won't be conceptually cohesive and it will give it many reasons
to change. Minimizing the number of times you need to change a class is important.
It's important because if too much functionality is in one class and you modify
a piece of it, it can be difficult to understand how that will affect other
dependent modules in your codebase.

**Mal:**
```ruby
class UserSettings
  def initialize(user)
    @user = user
  end

  def change_settings(settings)
    return unless valid_credentials?
    # ...
  end

  def valid_credentials?
    # ...
  end
end
```

**Bien:**
```ruby
class UserAuth
  def initialize(user)
    @user = user
  end

  def valid_credentials?
    # ...
  end
end

class UserSettings
  def initialize(user)
    @user = user
    @auth = UserAuth.new(user)
  end

  def change_settings(settings)
    return unless @auth.valid_credentials?
    # ...
  end
end
```
**[⬆ volver arriba](#tabla-de-contenido)**

### Open/Closed Principle (OCP)
As stated by [Bertrand Meyer](https://en.wikipedia.org/wiki/Bertrand_Meyer), "software entities (classes, modules, functions,
etc.) should be open for extension, but closed for modification." What does that
mean though? This principle basically states that you should allow users to
add new functionalities without changing existing code.

In the "bad" example below adding another adapter would require changing `HttpRequester` class. This violates OCP.

**Mal:**
```ruby
class AjaxAdapter
  attr_reader :name

  def initialize
    @name = 'ajaxAdapter'
  end
end

class NodeAdapter
  attr_reader :name

  def initialize
    @name = 'nodeAdapter'
  end
end

class HttpRequester
  def initialize(adapter)
    @adapter = adapter
  end

  def fetch(url)
    case @adapter.name
    when 'ajaxAdapter'
      make_ajax_call(url)
    when 'nodeAdapter'
      make_http_call(url)
    end
  end

  def make_ajax_call(url)
    # ...
  end

  def make_http_call(url)
    # ...
  end
end
```

**Bien:**
```ruby
class AjaxAdapter
  def request(url)
    # ...
  end
end

class NodeAdapter
  def request(url)
    # ...
  end
end

class HttpRequester
  def initialize(adapter)
    @adapter = adapter
  end

  def fetch(url)
    @adapter.request(url)
  end
end
```
**[⬆ volver arriba](#tabla-de-contenido)**

### Liskov Substitution Principle (LSP)
This is a scary term for a very simple concept. It's formally defined as "If S
is a subtype of T, then objects of type T may be replaced with objects of type S
(i.e., objects of type S may substitute objects of type T) without altering any
of the desirable properties of that program (correctness, task performed,
etc.)." That's an even scarier definition.

The best explanation for this is if you have a parent class and a child class,
then the base class can always be replaced by the child class without getting
incorrect results. This might still be confusing, so let's take a look at the
classic Square-Rectangle example. Mathematically, a square is a rectangle, but
if you model it using the "is-a" relationship via inheritance, you quickly
get into trouble.

**Mal:**
```ruby
class Rectangle
  def initialize
    @width = 0
    @height = 0
  end

  def color=(color)
    # ...
  end

  def render(area)
    # ...
  end

  def width=(width)
    @width = width
  end

  def height=(height)
    @height = height
  end

  def area
    @width * @height
  end
end

class Square < Rectangle
  def width=(width)
    @width = width
    @height = width
  end

  def height=(height)
    @width = height
    @height = height
  end
end

def render_large_rectangles(rectangles)
  rectangles.each do |rectangle|
    rectangle.width = 4
    rectangle.height = 5
    area = rectangle.area # BAD: Returns 25 for Square. Should be 20.
    rectangle.render(area)
  end
end

rectangles = [Rectangle.new, Rectangle.new, Square.new]
render_large_rectangles(rectangles)
```

**Bien:**
```ruby
class Shape
  def color=(color)
    # ...
  end

  def render(area)
    # ...
  end
end

class Rectangle < Shape
  def initialize(width, height)
    @width = width
    @height = height
  end

  def area
    @width * @height
  end
end

class Square < Shape
  def initialize(length)
    @length = length
  end

  def area
    @length * @length
  end
end

def render_large_shapes(shapes)
  shapes.each do |shape|
    area = shape.area
    shape.render(area)
  end
end

shapes = [Rectangle.new(4, 5), Rectangle.new(4, 5), Square.new(5)]
render_large_shapes(shapes)
```
**[⬆ volver arriba](#tabla-de-contenido)**

### Interface Segregation Principle (ISP)
Ruby doesn't have interfaces so this principle doesn't apply as strictly
as others. However, it's important and relevant even with Ruby's lack of
type system.

ISP states that "Clients should not be forced to depend upon interfaces that
they do not use." Interfaces are implicit contracts in Ruby because of
duck typing.

When a client depends upon a class that contains interfaces that the client does not use, but that other clients do use, then that client will be affected by the changes that those other clients force upon the class.

The following example is taken from [here](http://geekhmer.github.io/blog/2015/03/18/interface-segregation-principle-in-ruby/).

**Mal:**
```ruby
class Car
  # used by Driver
  def open
    # ...
  end

  # used by Driver
  def start_engine
    # ...
  end

  # used by Mechanic
  def change_engine
    # ...
  end
end

class Driver
  def drive
    @car.open
    @car.start_engine
  end
end

class Mechanic
  def do_stuff
    @car.change_engine
  end
end

```

**Bien:**
```ruby
# used by Driver only
class Car
  def open
    # ...
  end

  def start_engine
    # ...
  end
end

# used by Mechanic only
class CarInternals
  def change_engine
    # ...
  end
end

class Driver
  def drive
    @car.open
    @car.start_engine
  end
end

class Mechanic
  def do_stuff
    @car_internals.change_engine
  end
end
```

**[⬆ volver arriba](#tabla-de-contenido)**

### Dependency Inversion Principle (DIP)
This principle states two essential things:
1. High-level modules should not depend on low-level modules. Both should
depend on abstractions.
2. Abstractions should not depend upon details. Details should depend on
abstractions.

Simply put, DIP keeps high-level
modules from knowing the details of its low-level modules and setting them up.
It can accomplish this through dependency injection. A huge benefit of this is that
it reduces the coupling between modules. Coupling is a very bad development pattern
because it makes your code hard to refactor.

As stated previously, Ruby doesn't have interfaces so the abstractions
that are depended upon are implicit contracts. That is to say, the methods
and properties that an object/class exposes to another object/class. In the
example below, the implicit contract is that any Request module for an
`InventoryTracker` will have a `request_items` method.

**Mal:**
```ruby
class InventoryRequester
  def initialize
    @req_methods = ['HTTP']
  end

  def request_item(item)
    # ...
  end
end

class InventoryTracker
  def initialize(items)
    @items = items

    # BAD: We have created a dependency on a specific request implementation.
    @requester = InventoryRequester.new
  end

  def request_items
    @items.each do |item|
      @requester.request_item(item)
    end
  end
end

inventory_tracker = InventoryTracker.new(['apples', 'bananas'])
inventory_tracker.request_items
```

**Bien:**
```ruby
class InventoryTracker
  def initialize(items, requester)
    @items = items
    @requester = requester
  end

  def request_items
    @items.each do |item|
      @requester.request_item(item)
    end
  end
end

class InventoryRequesterV1
  def initialize
    @req_methods = ['HTTP']
  end

  def request_item(item)
    # ...
  end
end

class InventoryRequesterV2
  def initialize
    @req_methods = ['WS']
  end

  def request_item(item)
    # ...
  end
end

# By constructing our dependencies externally and injecting them, we can easily
# substitute our request module for a fancy new one that uses WebSockets.
inventory_tracker = InventoryTracker.new(['apples', 'bananas'], InventoryRequesterV2.new)
inventory_tracker.request_items
```
**[⬆ volver arriba](#tabla-de-contenido)**

## **Testing**
Testing is more important than shipping. If you have no tests or an
inadequate amount, then every time you ship code you won't be sure that you
didn't break anything. Deciding on what constitutes an adequate amount is up
to your team, but having 100% coverage (all statements and branches) is how
you achieve very high confidence and developer peace of mind. This means that
in addition to having a [great testing framework](http://rspec.info/), you also need to use a
[good coverage tool](https://coveralls.io/).

There's no excuse to not write tests. Aim to always write tests
for every new feature/module you introduce. If your preferred method is
Test Driven Development (TDD), that is great, but the main point is to just
make sure you are reaching your coverage goals before launching any feature,
or refactoring an existing one.

### Single expectation per test

**Mal:**
```ruby
require 'rspec'

describe 'Calculator' do
  let(:calculator) { Calculator.new }

  it 'performs addition, subtraction, multiplication and division' do
    expect(calculator.calculate('1 + 2')).to eq(3)
    expect(calculator.calculate('4 - 2')).to eq(2)
    expect(calculator.calculate('2 * 3')).to eq(6)
    expect(calculator.calculate('6 / 2')).to eq(3)
  end
end
```

**Bien:**
```ruby
require 'rspec'

describe 'Calculator' do
  let(:calculator) { Calculator.new }

  it 'performs addition' do
    expect(calculator.calculate('1 + 2')).to eq(3)
  end

  it 'performs subtraction' do
    expect(calculator.calculate('4 - 2')).to eq(2)
  end

  it 'performs multiplication' do
    expect(calculator.calculate('2 * 3')).to eq(6)
  end

  it 'performs division' do
    expect(calculator.calculate('6 / 2')).to eq(3)
  end
end
```
**[⬆ volver arriba](#tabla-de-contenido)**

## **Error Handling**
Thrown errors are a good thing! They mean the runtime has successfully
identified when something in your program has gone wrong and it's letting
you know by stopping method execution on the current stack, killing the
process, and notifying you in the logs with a stack trace.

### Don't ignore caught errors
Doing nothing with a caught error doesn't give you the ability to ever fix
or react to said error. Logging the error
isn't much better as often times it can get lost in a sea of other logs. If you wrap any bit of code in a `begin/rescue` it means you
think an error may occur there and therefore you should have a plan,
or create a code path, for when it occurs.

**Mal:**
```ruby
require 'logger'

logger = Logger.new(STDOUT)

begin
  method_that_might_throw()
rescue StandardError => err
  logger.info(err)
end
```

**Bien:**
```ruby
require 'logger'

logger = Logger.new(STDOUT)
# Change the logger level to ERROR to output only logs with ERROR level and above
logger.level = Logger::ERROR

begin
  method_that_might_throw()
rescue StandardError => err
  # Option 1: Only log errors
  logger.error(err)
  # Option 2: Notify end-user via an interface
  notify_user_of_error(err)
  # Option 3: Report error to a third-party service like Honeybadger
  report_error_to_service(err)
  # OR do all three!
end
```

### Provide context with exceptions
Use a descriptive error class name and a message when you raise an error. That way you know why the error occurred and you can rescue the specific type of error.

***Mal:***
```ruby
def initialize(user)
  fail unless user
  ...
end
```

***Bien:***
```ruby
def initialize(user)
  fail ArgumentError, 'Missing user' unless user
  ...
end
```

**[⬆ volver arriba](#tabla-de-contenido)**


## **Formatting**
Formatting is subjective. Like many rules herein, there is no hard and fast
rule that you must follow. The main point is DO NOT ARGUE over formatting.
There are tons of tools like [RuboCop](https://github.com/bbatsov/rubocop) to automate this.
Use one! It's a waste of time and money for engineers to argue over formatting.

For things that don't fall under the purview of automatic formatting
(indentation, tabs vs. spaces, double vs. single quotes, etc.) look here
for some guidance.

### Use consistent capitalization
Ruby is dynamically typed, so capitalization tells you a lot about your variables,
methods, etc. These rules are subjective, so your team can choose whatever
they want. The point is, no matter what you all choose, just be consistent.

**Mal:**
```ruby
DAYS_IN_WEEK = 7
daysInMonth = 30

songs = ['Back In Black', 'Stairway to Heaven', 'Hey Jude']
Artists = ['ACDC', 'Led Zeppelin', 'The Beatles']

def eraseDatabase; end

def restore_database; end

class ANIMAL; end
class Alpaca; end
```

**Bien:**
```ruby
DAYS_IN_WEEK = 7
DAYS_IN_MONTH = 30

SONGS = ['Back In Black', 'Stairway to Heaven', 'Hey Jude'].freeze
ARTISTS = ['ACDC', 'Led Zeppelin', 'The Beatles'].freeze

def erase_database; end

def restore_database; end

class Animal; end
class Alpaca; end
```
**[⬆ volver arriba](#tabla-de-contenido)**


### Method callers and callees should be close
If a method calls another, keep those methods vertically close in the source
file. Ideally, keep the caller right above the callee. We tend to read code from
top-to-bottom, like a newspaper. Because of this, make your code read that way.

**Mal:**
```ruby
class PerformanceReview
  def initialize(employee)
    @employee = employee
  end

  def lookup_peers
    db.lookup(@employee, :peers)
  end

  def lookup_manager
    db.lookup(@employee, :manager)
  end

  def peer_reviews
    peers = lookup_peers
    # ...
  end

  def perf_review
    peer_reviews
    manager_review
    self_review
  end

  def manager_review
    manager = lookup_manager
    # ...
  end

  def self_review
    # ...
  end
end

review = PerformanceReview.new(employee)
review.perf_review
```

**Bien:**
```ruby
class PerformanceReview
  def initialize(employee)
    @employee = employee
  end

  def perf_review
    peer_reviews
    manager_review
    self_review
  end

  def peer_reviews
    peers = lookup_peers
    # ...
  end

  def lookup_peers
    db.lookup(@employee, :peers)
  end

  def manager_review
    manager = lookup_manager
    # ...
  end

  def lookup_manager
    db.lookup(@employee, :manager)
  end

  def self_review
    # ...
  end
end

review = PerformanceReview.new(employee)
review.perf_review
```

**[⬆ volver arriba](#tabla-de-contenido)**

## **Comments**


### Don't leave commented out code in your codebase
Version control exists for a reason. Leave old code in your history.

**Mal:**
```ruby
do_stuff
# do_other_stuff
# do_some_more_stuff
# do_so_much_stuff
```

**Bien:**
```ruby
do_stuff
```
**[⬆ volver arriba](#tabla-de-contenido)**

### Don't have journal comments
Remember, use version control! There's no need for dead code, commented code,
and especially journal comments. Use `git log` to get history!

**Mal:**
```ruby
# 2016-12-20: Removed monads, didn't understand them (RM)
# 2016-10-01: Improved using special monads (JP)
# 2016-02-03: Removed type-checking (LI)
# 2015-03-14: Added combine with type-checking (JR)
def combine(a, b)
  a + b
end
```

**Bien:**
```ruby
def combine(a, b)
  a + b
end
```
**[⬆ volver arriba](#tabla-de-contenido)**

## Translations

This is also available in other languages:

  - [Brazilian Portuguese](https://github.com/uohzxela/clean-code-ruby/blob/master/translations/pt-BR.md)
  - [Simplified Chinese](https://github.com/uohzxela/clean-code-ruby/blob/master/translations/zh-CN.md)

**[⬆ volver arriba](#tabla-de-contenido)**
