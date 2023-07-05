# programming_ecto  
  
PART 1  
  
Chapter 1  
  
++ All communication to and from the database goes through Repo.  
++ mix ecto.reset will drop the database, re-create it, and repopulate it with the original sample data.  
++ If you want to talk to your databae, you to the the Repository (Repo module)  
++ set option (Repo.update_all("artists", set: [updated_at: DateTime.utc_now()])) tells Ecto what fields to change for all entries  
++ the standard return value for all ??\_all functions is a tuple where the first item is the number of records affected by the operation. The second contains the values we asked to be returned. - Repo.insert_all("artists", [%{name: "Max Roach"}, %{name: "Art Blakey"}], returning: [:id, :name]) <- will return the id + name of the two records changed as the second item.  
++ Can use raw SQL with Repo.query - ex: Repo.query("select \* from artists where id=1")

Chapter 2

++ Ecto provides two different synta: macro (pipe operator) and keyword  
++ Macro (like the Ecto.Query.from function) is code that writes code  
++ When using prefixes in your database, you can specify them by using the prefix keyword  
++ Because Ecto's query syntax is implemented using macros, we need to use the pin operator (^) when using variables  
++ Ecto's Type function will convert a type for us (integer to string for example)  
++ Create a Query binding by using the 'in' keyword (from a in "artists", where: a.name == "Bill Evans", select...)  
++ Use the fragment keyword to inject raw SQL into a query (can also create macro if keyword is used often)  
++ The Union operator combines the results of different queries  
++ order_by and group_by keywords are available in Ecto  
++ Give thought to columns that might contain null by using :asc_nulls_last, :asc_nulls_first  
++ the having clause will take results of query and further refine them  
++ when using the select keyword, you can provide a map for a more expressive return statement  
++ When composing queries, binding order is preserverd  
++ To create named bindings, you use the keyword 'as: ' and it must follow after from and/or join. You also must use an atom  
++ Named bindings can appear in any order after the from keyword  
++ use has_named_binding? function to determine whether query has a named binding  
++ use or macro in where keyword  
++ or_where macro works exactly like the where keyword but uses OR instead of the default AND operator

Chapter 3

++ Schema macro takes two args: name of table to map to and a block containing definitions for the fields to use  
++ The schema does not have to match 1 to 1 w/ the columns in database table, only those fields we plan to use in our code  
++ timestamp macro adds two fields to schema - inserted_at, updated_at <- these fields must be found within the table for this to work properly  
++ By default, Ecto will create id field for you  
++ Integrating a schema into the query performs type conversions for us, allows us to remove the select option and returns a schema struct  
++ In Ecto, relationships between DB tables are referred to as associations  
++ For nested associations, use the through: option  
++ For many-to-many associations, use the join_through: option. If you only need foreign keys in the relationship table, then you do not need to create a schema. However, if you need other keys in that table besides the foreign keys (timestamps, etc), then you'll need to create a schema  
++ Due to Ecto not having "lazy loading," you can use the preload function if you want to load any associations within a given query (grabbing all albums then preloading tracks for albums for example)  
++ on_delete option provides a way to delete child records when a parent record has been deleted (:nothing, :nilify_all, :delete_all are different options to pass)

Chapter 4

++ First step in creating a changeset is taking the raw input data and generating an Ecto.Changeset struct.  
++ Casting and filtering is when we perform any needed type casting operations (changing a string to an integer) and then filter our any values we don't need.  
++ If the data is internal (the app is generating it), then we can create a changeset using the change function. (ex: changeset = change(%Artist{name: "Charlie Parker"}))  
++ To make changes to an existing record, we use a record fetched from Repo (Repo.get_by(data) -> change(data))  
++ To see the changes that are going to applied, check the changes field (ex: changeset.changes)  
++ We can update a changeset instead of creating a new one (ex: changeset = change(changeset, birth_date: ~D[1941-01-27])) or you can add multiple arguments to the change function (ex: changeset = change(artist, name: "Robert Hutcherson",
birth_date: ~D[1941-01-27]))  
++ the cast function is similar to change as it returns a changeset  
++ the cast function takes three arguments  
first arg: new schema struct, a fetched record, or another changeset  
second arg: raw data to be applied  
third arg: list of params allowed in the changeset, anything not listed will be discarded  
++ you can pass an empty_values option to the changeset to change the string NULL for example to an empty value  
++ Validations and constraints check the integrity of our data  
++ the validate_change function is a way to create custom validations by passing an anonymous function to perform whatever check you need.  
++ it's smart to move your custom validations to separate function, makes it easier to read  
++ Constraints are enforced by the database, will not see an error until the databasse is interacted with  
++ the unsafe_validate_unique function is a hybrid between a vliadation and a constraint  
++ To set up a changeset to work without a schema, you must first define a map where the keys are the keys of the changeset and the values are the data types

Chapter 5

++ Transactions allow you to group operations together so you can be certain that they will all succeed, or all fail.  
++ Ecto supports transactions through the Repo.transaction function.  
You can execute this function in two ways. The first way is to provide another function that contains the operations you’d like to execute. The second way is to use Ecto.Multi, a data structure consisting of a queue of operations to be run within a transaction.  
++ Must use bang (!) after database call to ensure error is raised by transaction function and rollback occurs  
If you didn't want to use a bang, then you can use the rollback function explicitly  
++ When using the transaction button, you want to execute database operations first before any non-database functions  
++ Drawbacks to anon transation functions:  
+ One small error like forgetting a bang can have a big negative impact on db   
+ Anon functions are not composable, limiting their reusability  
+ Requires a lot of code to see errors  
++ Instead of using an anon function to use transactions, you can use Ecto.Multi  
++ Use the new keyword to create a new multi struct (Multi.new) instead of %Multi{}  
  
Chapter 6  
  
++ Ecto uses migrations to create and alter tables in the DB  
++ A migration is a set of commands, created in Elixir, that contains the instructions for the changes you want to make  
++ The easiest way to create a new migration is to use the mix task that Ecto provides: mix ecto.gen.migration.  
++ Ecto will always create a primary key column called id unless you instruct it not to  
++ mix.ecto.rollback allows you to rollback your last migration  
++ general rule: it's OK to edit an existing migration provided that you haven't already committed your migration to source control.  
++ Use index function to create indices for columns within a table  
++ When migrating data from one table to another, consider using the flush function as it tells Ecto to execute the currently queued operations (any code before the flush call)  
++ When making big changes to the db, write up and down functions so that Ecto can rollback changes effectively, usually the up function just being the change  function, then a separate down function to assist with rollback  
  
PART 2  
  
Chapter 7  
  
++ --sup flag when creating a new project adds Ecto to the supervision tree of the project  
  
Chapter 8  
  
++ Come back to this chapter if working with forms  
  
Chapter 9  
  
++ To use the testing sandbox, add "pool: Ecto.Adapters.SQL.Sandbox" to the Repo config file  
++ Then, need to set the correct ownership mode  
++ Ownership mode affects the way the sandbox interacts with different processes. The three modes are: :auto, :manual, :shared  
++ To work around limitations of shared mode (able to share processes but unable to run tests concurrently), Ecto provides allowances.  
  
Chapter 10  
  
++ In order to create a new type, based on an existing one, first need to create a module that implements the Ecto.Type behavior  
++ Within module, must create type, dump, load and cast functions  
++ To work with a data type not recognized in Ecto, need to write custom driver extension  
  
Chapter 11  
  
++ Upsert is a mash-up of update & insert  
++ Use on_conflict to make changes when using insert (:replace, :nothing, :raise - default)  
++ When using replace, it is a tuple {:replace, [:columns to replace]}  
++ Add conflict_target / returning keys to specificy exactly which column should be changed  
  
Chapter 12  
  
++ When testing to see whether a changeset is created correctly, use apply_changes() instead of calling the db. It is a much less expensive call.  
  
Chapter 13  
  
++ Embedded schema are an alternative to using regular associations.  
++ Embedded schema are captured in the same table as the parent schema, instead of a separate table  
  
Chapter 14  
  
++ Polymorphism isn't built out of the box in Ecto. Need to use one of three approaches outlined in the book.  
  
Chapter 15  
  
++ 
