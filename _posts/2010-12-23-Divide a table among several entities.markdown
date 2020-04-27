---
layout: post
title:  "Entity Framework: Divide a table among several entities."
date:   2010-12-23 12:15:31 +0100
categories: Entity Framework, .NET
comments: false
---

If we have a table with several frequently used fields but others used only very rarely, we can divide the table among several entities to avoid loading those fields in each query.

For example, if we have a photography table with a field that has the image in high resolution, we can divide the photography entity that the designer has loaded in two.

For that we create a new entity such as FotografiaAltaResolucion, we change the name of the key field that has created us to match the name of the key field of the photography entity and then we cut and paste the high resolution field in the entity FotografiaAltaResolucion.

In the mapping details we click on add a table and select the photography table so we map the two fields of the new entity with those of the photography entity. We create a one-to-one partnership between the two entities. In the properties of the association we click on add a referential reference, selecting the main one to photography and making sure that the key properties are placed on both sides to the Id of the entities. With this we tell the Entity Framework that there cannot be a High Resolution Photography without a Photography. With this we get the effect "lazy loading (as LINQ uses), to load in the query heavy or underused fields only when they are really needed.