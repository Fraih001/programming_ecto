# programming_ecto

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

++
