---
layout: post
title:  "Assignment of variables in T-SQL."
date:   2018-02-12 12:15:31 +0100
categories: T-SQL
comments: true
---

Last week I was asked by a partner how to initialize a variable with the result of a query in T-SQL.

I told him how I did the assignment with SET, but I remembered that there was another way to do it that I did not use and this led me to investigate how to do it in this other way that is directly with a SELECT.

The two ways would be like this:

{% highlight sql %}
DECLARE @variableSET Int
DECLARE @variableSelect1 Int
DECLARE @variableSelect2 varchar(50)

--SET Initialization
SET @variableSET = (SELECT IdUser FROM Users WHERE IdUser = 127)

--SELECT Initialization
SELECT @variableSelect1 = IdUser, @variableSelect2 = UserName 
FROM Users 
WHERE IdUser = 127

--We print the results of the variables to see what they return to us.
PRINT @variableSET
PRINT @variableSelect1
PRINT @variableSelect2
{% endhighlight %}

Then I put the differences that exist between the two initialization methods

1. SET is standard ANSI while SELECT is not. This means that SET can be used in any database engine while SELECT only in SQL SERVER.

2. SET can only assign one variable at a time while SELECT can do it with several. We see it in the example as with SET we only declare @variableSET while with SELECT we assign the variables @ variableSelect1 and @ variableSelect2.

3. If we assign from a query, as in the case of the example, we have to be sure that it will return a scalar value if it returns a result set the SET will fail. The SELECT does not fail which is even more dangerous since it will assign a value of the result ignoring the others, so it will be making the assignment of a result that we do not expect and not failing this can then be very difficult to debug.

4. If the query does not return any value with SET, the NULL value will be assigned to the variable but with SELECT no assignment will be made so the variable will maintain its previous value, which can also lead to errors quite difficult to debug.

For all the above we can see that it is better to use SET as a method of assigning variables in T-SQL.