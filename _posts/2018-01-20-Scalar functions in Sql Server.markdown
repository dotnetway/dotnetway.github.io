---
title:  "Scalar functions in Sql Server."
date:   2018-01-20 12:15:31 +0100
categories:
  - SQL
tags:
  - T-SQL
---

Today I wanted to talk about scalar functions in Sql Server and a very interesting utility that I discovered recently.

A scalar function has to return a single value if it would not give an error, so we have to make sure that the query with which we define it only returns one value. As parameters we can pass the ones we want.

They are used differently to the functions of Table, they are removed * FROM between the SELECT and the name of the function.

An example of use would be:

{% highlight sql %}
SELECT NombreUsuario(10);
{% endhighlight %}

In this case we would return the full name of a user that we may have concatenated in the function with all the fields that we have type First Name, Last Name1, Last Name 2. In the function parameter I have given an example of a user ID that is given would happen, so it would be to give me back the full name of the user with Id 10.

Now comes the interesting thing. If we have quite complex queries with many joins and to obtain another data we have to cross many tables again and we can make mistakes we can use the scalar functions within the select of our query so that it returns us a unique value per processed line, as a function parameter We would pass the field of the current query that we need to pass and Sql Server will process the function for each line by passing the value of the field passed by parameter and will return a field with the data we need to which we must put an Alias ​​to distinguish it in the query because by default a name is not assigned to that field.

For example, we could put the previous function in the select to return the full username.

{% highlight sql %}
SELECT NombreUsuario(U.IDUsuario) AS NombreCompleto, D.Departamento 
FROM USUARIOS U
 JOIN DEPARTAMENTOS D ON U.IdDepartamento = D.IDDepartamento
{% endhighlight %}

Best of all, we can also use the function in the where.

{% highlight sql %}
SELECT NombreUsuario(U.IDUsuario) AS NombreCompleto, D.Departamento 
FROM USUARIOS 
 JOIN Ventas V ON U.Id = V.IdUsuario 
WHERE NombreUsuario(U.IDUsuario) = 'Javier Pérez Pérez'
{% endhighlight %}

In a principle like this for simple consultations it may not make much sense other than the utility of not having to concatenate the name, which is also not a small thing. But in many more complex ones with many Joins saves your life.

Now come the problems. Putting the scalar functions in the Select and in the Where the bbdd overloads a lot because the function is executed for each line that returns the query so that in queries that return many results it is not advisable to use them because they take a long time to execute and have Very bad performance.

We can observe this by opening the profiler to plot the query by enabling the SP: Completed and SQL: BatchCompleted events. There we can with SP: Completed that the function is executed as many times as lines return the query, so in the end the use of CPU triggers and worse the use of readings, with respect to the query directly concatenating the username already In this case, it is only executed once and the SQL event: BatchCompleted events, which is the one that summarizes the cost of the query, we see that the CPU is used much less and especially the readings are drastically reduced.

Now the good news. We can avoid this with a table function instead of scaling and a CROSS APPLY or with a normal JOIN against a view that has the query that returns the full name. But that will be the subject of another post.

