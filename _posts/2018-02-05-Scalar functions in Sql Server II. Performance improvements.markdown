---
title:  "Scalar functions in Sql Server II. Performance improvements."
date:   2018-02-05 12:15:31 +0100
categories:
  - SQL
tags:
  - T-SQL 
---

Today I am writing to you as I promised to see the solutions to the performance problems involved in using the scalar functions in more complex queries.

The first is to create the scalar function as a table function, that is, instead of returning a single value, a set of values ​​as a normal query.

We create the function:

{% highlight sql %}
CREATE FUNCTION fn_ NombreUsuarioTableFunction (@IdUsuario int)
RETURNS TABLE 
AS RETURN 
(SELECT  U.LastName + ', ' +  U.FirstName [NombreCompleto], D.Departamento  
FROM Usuarios 
 JOIN DEPARTAMENTOS D ON U.IdDepartamento = D.IDDepartamento
WHERE IdUsuario = @IdUsuario)
GO
{% endhighlight %}

The scalar function scalar function was used as follows:

{% highlight sql %}
SELECT NombreUsuario(U.IDUsuario) AS NombreCompleto, U.Departamento 
 FROM USUARIOS 
JOIN Ventas V ON U.Idusuario = V.IdUsuario 
WHERE NombreUsuario(U.IDUsuario) = 'Javier Pérez Pérez'
{% endhighlight %}

To use the table function we have to use a CROSS JOIN as follows:

{% highlight sql %}
SELECT U.NombreCompleto, U.Departamento 
FROM Ventas V
 CROSS APPLY NombreUsuarioTableFunction (V.IDUsuario) U 
WHERE U.NombreCompleto = 'Javier Pérez Pérez'
{% endhighlight %}

In this way the number of I/O is much more efficient and CPU usage is much lower.

The other alternative is to create a view. For example:

{% highlight sql %}
CREATE VIEW VistaNombreUsuario 
AS 
SELECT U.IdUsuario, U.LastName + ', ' +  U.FirstName [NombreCompleto], D.Departamento 
FROM Usuarios 
 JOIN DEPARTAMENTOS D ON U.IdDepartamento = D.IDDepartamento
GO
{% endhighlight %}

Now we make a JOIN with the view:

{% highlight sql %}
SELECT U.NombreCompleto, U.Departamento 
FROM Ventas V 
  JOIN VistaNombreUsuario U ON U.IdUsuario = V.IdUsuario 
U.NombreCompleto = 'Javier Pérez Pérez'
{% endhighlight %}

With the view we gain that the CPU usage is a bit lower although the I / O is the same as with the use of CROSS JOIN.

If we put together that it is a little better to use the CPU using a view, this is my preferred way, because it is more understandable for me than using the CROSS JOIN. In addition, the view is always more generic to be able to filter what we want or without any filter, what has the functions that we have to pass the filter beforehand and what allows us to create other functions so that it does not filter anything or to pass more filters or other different.

I have not put any performance test because [here I leave an article][articulo-rendimiento] which explains very well the yields attaching tests with the profiler. It has no waste and is essential if you are interested in the performance tests of everything I have told you.

[articulo-rendimiento]: https://www.databasejournal.com/features/mssql/article.php/3845381/T-SQL-Best-Practices-150-Don146t-Use-Scalar-Value-Functions-in-Column-List-or-WHERE-Clauses.htm

