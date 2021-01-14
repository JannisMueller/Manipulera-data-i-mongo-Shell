# Manipulera-data-i-mongo-Shell-
Denna repository innehåller min lösning för Labb 3 (kurs "Utveckling mot databas och databasadministration", ITHS 202021).


1. Använd en databas med namn myDB.
   ```
   use myDB
   
   switched to db myDB
   ```

2. Skapa ett dokument med innehåll FirstName: ”Selma”, LastName: Lagerlöf och sätt in det i en kollektion med namn ”authors”.

   ```
   db.authors.insertOne({FirstName: "Selma", LastName: "Lagerlöf"});
   {
   	"acknowledged" : true,
   	"insertedId" : ObjectId("5ffeb4054a48c2bcdf8104ae")
   }
   ```

3. Lägg till ytterligare ett dokument i ”authors” med FirstName: ”August”, LastName: ”Bondeson”, Birth: 1854

   ```
   db.authors.insertOne({FirstName: "August", LastName: "Bondeson", Birth: "1854"});
   {
   	"acknowledged" : true,
   	"insertedId" : ObjectId("5ffeb4374a48c2bcdf8104af")
   }
   ```

4. Uppdatera dokumentet för August Bondeson och lägg till Death: 1906

   ```
   db.authors.updateOne({FirstName : "August", LastName : "Bondeson" } ,{ $set: {Birth: 1906} });
   { "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }
   ```

5. Lägg till ytterligare författare i ”authors” genom att köra kommandot load(”addAuthors.js”) från mongo Shell. (Ladda ner filen från ITHS Distans).

   ```
   load ("/Users/jannismuller/Downloads/addAuthors.js")
   true
   ```

6.  Räkna hur många dokument det finns totalt i ”authors”.

   ```
   db.authors.count()
   6
   ```

7.  Räkna hur många författare som heter August i förnamn.

   ```
   db.authors.count( { FirstName : "August" } )
   2
   ```

8.  Lägg till Birth: 1858 och Death: 1940 för Selma Lagerlöf

   ```
   db.authors.updateOne({FirstName : "Selma", LastName: "Lagerlöf"} ,{ $set: {Birth: 1858, Death: 1940}})
   { "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }
   ```

9.  Lägg till en array ”Books” för Selma Lagerlöf med följande böcker: ”Gösta Berlings saga”, ”En herrgårdsägen”, ”Nils Holgerssons underbara resa genom Sverige” 

    ```
    db.authors.updateOne({FirstName : "Selma", LastName: "Lagerlöf"} ,{ $set: {Books: ["Gösta Berlings saga","En herrgårdsägen","Nils Holgerssons undabara resor       genom Sverige"]}})
    { "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }
    ```

10. Lägg till boken ”Vi på Saltkråkan” bland Astrid Lindgrens böcker. 

    ```
    db.authors.updateOne({FirstName : "Astrid", LastName: "Lindgren"} ,{ $addToSet: {Books: "Vi på Saltkråkan"}})
    { "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }
    ```

11. Ta bort boken ”Bröderna Lejonhjärta” från Astrid Lindgrens böcker.

    ```
    db.authors.updateOne({FirstName : "Astrid", LastName: "Lindgren"} ,{ $pop: {Books: -1 }})
    { "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }
    ```

12. Visa dokument för de författare som dog år 2000 eller senare.

    ```
    db.authors.find({Death: {$gte: 2000}})
    { "_id" : ObjectId("5ffeb4744a48c2bcdf8104b0"), "FirstName" : "Astrid", "LastName" : "Lindgren", "Birth" : 1907, "Death" : 2002, "Books" : [ "Här kommer Pippi Långstrump", "Mio min Mio", "Vi på Saltkråkan" ] }
    ```

13.  Räkna hur många författare som dog på 1940-talet. 

    ```
    db.authors.find({$and: [{Death: {$gte: 1940}},{Death: {$lt: 1950}}]} ).pretty()
    {
    	"_id" : ObjectId("5ffeb4054a48c2bcdf8104ae"),
    	"FirstName" : "Selma",
    	"LastName" : "Lagerlöf",
    	"Birth" : 1858,
    	"Death" : 1940,
    	"Books" : [
    		"Gösta Berlings saga",
    		"En herrgårdsägen",
    		"Nils Holgerssons undabara resor genom Sverige"
    	]
    }
    {
    	"_id" : ObjectId("5ffeb4744a48c2bcdf8104b1"),
    	"FirstName" : "Hjalmar",
    	"LastName" : "Söderberg",
    	"Birth" : 1869,
    	"Death" : 1941,
    	"Books" : [
    		"Den allvarsamma leken",
    		"Martin Bircks ungdom",
    		"Doktor Glas"
    	]
    }
    {
    	"_id" : ObjectId("5ffeb4744a48c2bcdf8104b2"),
    	"FirstName" : "Karin",
    	"LastName" : "Boye",
    	"Birth" : 1900,
    	"Death" : 1941,
    	"Books" : [
    		"Samlade dikter",
    		"Kallocain"
    	]
    }
    
    ```

14. Visa endast attributen FirstName, LastName och Death för de författare som dog på 1940-talet. 

    ```
    db.authors.find({$and: [{Death: {$gte: 1940}},{Death: {$lt: 1950}}]}, {FirstName: 1, LastName: 1, Death: 1, _id: 0} )
    { "FirstName" : "Selma", "LastName" : "Lagerlöf", "Death" : 1940 }
    { "FirstName" : "Hjalmar", "LastName" : "Söderberg", "Death" : 1941 }
    { "FirstName" : "Karin", "LastName" : "Boye", "Death" : 1941 }
    ```

15. Lägg till Gender: ”Male” i dokument med FirstName = ”Hjalmar”. 

    ```
    db.authors.update({FirstName : "Hjalmar"} ,{ $set: {Gender: "Male"} })
    
    WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
    ```

16. Lägg till Gender: ”Female” i dokument där FirstName är Astrid, Karin eller Selma.

    ```
    db.authors.updateMany({FirstName: {$in: ["Astrid", "Karin", "Selma"] } } ,{ $set: {Gender: "Female"} })
    { "acknowledged" : true, "matchedCount" : 3, "modifiedCount" : 3 }
    ```

17. Ta bort arrayen Books från dokumentet med August Strindberg.

    ```
    db.authors.update({FirstName : "August", LastName: "Strindberg"} ,{ $unset: {Books: ""} })
    WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
    ```

18. Ta bort dokumenten där FirstName = ”August”

    ```
    db.authors.remove({FirstName: "August"})

    WriteResult({ "nRemoved" : 2 })
    ```

