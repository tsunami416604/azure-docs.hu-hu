## Mi az a Table szolgáltatás?

Az Azure Table Storage szolgáltatás nagy mennyiségű strukturált adatok tárolására alkalmas. A szolgáltatás egy NoSQL-adattár, amely az Azure-felhőből és azon kívülről érkező hitelesített hívásokat fogadja. Az Azure-táblák strukturált, nem relációs adatok tárolására alkalmasak. A Table szolgáltatás leggyakoribb felhasználási módjai:

-   Több TB-nyi, webes méretű alkalmazások kiszolgálására alkalmas strukturált adat tárolása
-   Olyan adatkészletek tárolása, amelyekhez nincs szükség bonyolult illesztésekre, külső kulcsokra vagy tárolt eljárásokra, és a gyors hozzáférés érdekében denormalizálhatók
-   Adatok gyors lekérdezése fürtözött indexszel
-   Adathozzáférés az OData protokoll és a LINQ-lekérdezések WCF Data Service .NET-kódtárakkal való használatával

A Table szolgáltatás hatalmas strukturált, nem relációs adatkészletek tárolására és lekérdezésére alkalmas, a táblák pedig az igény növekedésével együtt nőnek.

## A Table szolgáltatással kapcsolatos fogalmak

A Table szolgáltatás az alábbi összetevőkből áll:

![Table1][Table1]

-   **URL-címformátum:** A kód egy fiók tábláira hivatkozik az alábbi címformátummal:   
    http://`<storage account>`.table.core.windows.net/`<table>`  
      
    Az Azure-táblákat közvetlenül is elérheti, ha a fenti címet használja az OData protokollal. További információk az [OData.org][] webhelyen találhatók

-   **Tárfiók:** Minden Azure Storage-hozzáférés tárfiókon keresztül valósítható meg. A tárfiókok kapacitásával kapcsolatos további információkért lásd: [Azure Storage Scalability and Performance Targets](storage-scalability-targets.md) (Az Azure Storage méretezhetőségi és teljesítménycéljai).

-   **Tábla:** A tábla az entitások gyűjteményét tartalmazza. A táblák nem kényszerítenek sémát az entitásokra, ami azt jelenti, hogy egyetlen tábla különböző tulajdonságkészletekkel rendelkező entitásokat is tartalmazhat. A tárfiókok által tárolható táblák számát csak a tárfiókok kapacitási határa korlátozza.

-   **Entitás:** Az entitás egy adatbázissorhoz hasonló tulajdonságkészlet. Egy entitás legfeljebb 1 MB méretű lehet.

-   **Tulajdonságok:** A tulajdonság egy név-érték pár. Minden entitás legfeljebb 252 tulajdonságot tartalmazhat adattárolás céljából. Minden entitás 3 rendszertulajdonsággal rendelkezik, amelyek egy partíciókulcsot, egy sorkulcsot és egy időbélyegzőt adnak meg. Az azonos partíciókulcsú entitások gyorsabban lekérdezhetők, illetve szűrhatók be/frissíthetők atomi műveletek során. Egy entitás sorkulcsa a partíción belüli azonosítója.

A táblák és tulajdonságok elnevezésével kapcsolatos további információkért lásd: [Understanding the Table Service Data Model](https://msdn.microsoft.com/library/azure/dd179338.aspx) (A Table szolgáltatás adatmodelljének ismertetése).
  
  [Table1]: ./media/storage-table-concepts-include/table1.png
  [OData.org]: http://www.odata.org/



<!--HONumber=Jun16_HO2-->


