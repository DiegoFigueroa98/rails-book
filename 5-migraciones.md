# ActiveRecord - Migraciones

Las migraciones nos permiten hacer cambios sobre el esquema de la base de datos de forma iterativa y consistente.

Una migración es un archivo que se crea dentro de la carpeta `db/migrate` y que contiene instrucciones para modificar el esquema de la base de datos (crear tablas, agregar columnas, eliminar columnas, eliminar tablas, etc.).

Cuando creas un **modelo** desde la línea de comandos con el generador de Rails, automáticamente se crea una **migración** con las instrucciones para crear la tabla.

Sin embargo, también puedes crear migraciones para agregar, remover o cambiar columnas de una tabla generalmente.

## Creando una migración

La forma más fácil de crear una migración es desde la línea de comandos:

```
$ rails generate migration <nombre_de_la_migración>
```

Por ejemplo, para crear una migración vacía y agregar un campo `publisher` a la tabla `books` podemos ejecutar el siguiente comando:

```
$ rails generate migration AddPublisherToBooks
```

El archivo que se generaría en `db/migrate` tendría lo siguiente:

```ruby
class AddPublisherToBooks < ActiveRecord::Migration[5.0]
  def change
  end
end
```

Para agregar el campo debemos agregar la siguiente línea al método `change`:

```ruby
add_column :books, :publisher, :string
```

Si la migración es de la forma "AddXXXToYYY" seguido de una lista de columnas y su tipo, la migración va a tener las instrucciones para agregar automáticamente ese o esos campos.

Por ejemplo, la migración anterior la hubiesemos podido crear más fácil de la siguiente forma:

```
$ rails generate migration AddPublisherToBooks publisher
```

Y eso va a generar la siguiente migración:

```ruby
class AddPublisherToBooks < ActiveRecord::Migration[5.0]
  def change
    add_column :books, :publisher, :string
  end
end
```

De forma similar podemos generar una migración para remover una columna si la migración es de la forma "RemoveXXXFromYYY":

```
$ rails generate migration RemovePublisherFromBooks publisher
```

Al ejecutar el comando generaría la siguiente migración:

```ruby
class RemovePublisherFromBooks < ActiveRecord::Migration[5.0]
  def change
    remove_column :books, :publisher, :string
  end
end
```

## Ejecutando y reversando migraciones

Para ejecutar las migraciones pendientes ejecuta el siguiente comando en la consola:

```
$ rails db:migrate
```

Para reversar la última migración ejecuta el siguiente comando:

```
$ rails db:rollback
```

Para conocer el estado de las migraciones ejecuta:

```
$ rails db:migrate:status
```

## Instrucciones

Una **migración** no es más que una serie de **instrucciones** escritas en Ruby para modificar el esquema de la base de datos.

Las **instrucciones** se escriben en el método `change` de la migración.

Las **instrucciones** más comunes son: agregar una columna, remover una columna, renombrar una columna y cambiar el tipo de una columna.

### Agregando una columna

Utiliza el método `add_column` para agregar una columna. Como mínimo debes pasarle 3 argumentos: la **tabla** a la que quieres agregarle la columna, el **nombre de la columna** y el **tipo de datos**. Por ejemplo:

```ruby
add_column :books, :publisher, :string
```

`add_column` recibe un cuarto argumento, un hash de opciones, allí puedes pasarle otras opciones, por ejemplo:

```ruby
add_column :books, :publisher, :string, limit: 100, null: false
```

### Eliminando una columna

Utiliza el método `remove_column` pasándole los mismos argumentos que `add_column`. Por ejemplo:

```ruby
remove_column :books, :publisher, :string
```

### Cambiando el nombre de una columna

Utiliza el método `rename_column` para cambiar el nombre de una columna. Por ejemplo, para cambiar el nombre de la columna `published_at` por `published_date`:

```ruby
rename_columna :books, :publisher, :publisher_name
```

### Cambiando el tipo de datos de una columna

Para cambiar el tipo de datos y otras opciones utiliza el método `change_column`. Como mínimo debes pasarle el nombre de la tabla, el nombre de la columna y el nuevo tipo. Por ejemplo:

```ruby
change_column :books, :published_date, :boolean
change_column :books, :publisher, :string, limit: 80
```

Puedes conocer todos los métodos que puedes utilizar como instrucciones en [este enlace](http://api.rubyonrails.org/v5.1.1/classes/ActiveRecord/ConnectionAdapters/SchemaStatements.html).

## Usando `reversible`

Todos los métodos que hemos visto hasta ahora (`add_column`, `remove_column`, etc.) son **reversibles**.

Los métodos reversibles saben cómo volver a su estado anterior en caso de que se reverse la migración con `rails db:rollback`.

Algunas migraciones pueden requerir un procesamiento complejo que Active Record no sabe cómo reversar.

Puedes utilizar el método `reversible` para escribir código que se va a ejecutar al migrar y reversar independientemente:

```ruby
class ExampleMigration < ActiveRecord::Migration[5.0]
  def change
    reversible do |dir|
      dir.up do
        puts "Esto se imprime cuando hacen la migración únicamente"
      end

      dir.down do
        puts "Esto se imprime cuando reversan la migración únicamente"
      end
    end
  end
end
```

El método `reversible` es útil para hacer migraciones de datos (p.e. cuando creas una nueva columna y debes actualizar todos los registros) o para ejecutar código SQL especial.

Es posible que ahora no necesites esta funcionalidad pero es bueno que sepas que existe!
