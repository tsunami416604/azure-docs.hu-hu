---
title: Kötegelés használata az alkalmazás teljesítményének javítása érdekében
description: A témakör azt bizonyítja, hogy az adatbázis-kötegelési műveletek jelentősen javítja az Azure SQL Database-alkalmazások sebességét és méretezhetőségét. Bár ezek a kötegelési technikák bármely SQL Server-adatbázishoz működnek, a cikk középpontjában az Azure áll.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 01/25/2019
ms.openlocfilehash: cacc01151edaf31db938cf8abf3d46e75397758f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76545024"
---
# <a name="how-to-use-batching-to-improve-sql-database-application-performance"></a>Kötegelés használata az SQL Database-alkalmazás teljesítményének javításához

Az Azure SQL Database-be való kötegelési műveletek jelentősen javítják az alkalmazások teljesítményét és méretezhetőségét. Az előnyök megértéséhez a cikk első része néhány mintavizsgálati eredményeket, amelyek összehasonlítják a szekvenciális és kötegelt kérelmek egy SQL-adatbázis. A cikk további része a technikákat, forgatókönyveket és szempontokat mutatja be, amelyek segítenek a kötegelés sikeres használatában az Azure-alkalmazásokban.

## <a name="why-is-batching-important-for-sql-database"></a>Miért fontos a kötegelés az SQL Database számára?

A távoli szolgáltatás hívásainak kötegelése jól ismert stratégia a teljesítmény és a méretezhetőség növelésére. A távoli szolgáltatásokkal való bármilyen interakciónak állandó feldolgozási költségei vannak, például szerializálás, hálózati átvitel és deszerializálás. Számos külön tranzakció egyetlen kötegbe történő csomagolása minimalizálja ezeket a költségeket.

Ebben a dokumentumban szeretnénk megvizsgálni a különböző SQL Database kötegelési stratégiák és forgatókönyvek. Bár ezek a stratégiák az SQL Server t használó helyszíni alkalmazások esetében is fontosak, számos oka van annak, hogy kiemelje az SQL Database kötegelésének használatát:

* Az SQL Database elérése potenciálisan nagyobb hálózati késést érhet el, különösen akkor, ha az SQL Database-t ugyanazon a Microsoft Azure-adatközponton kívülről éri el.
* Az SQL Database több-bérlős jellemzői azt jelentik, hogy az adatelérési réteg hatékonysága összefügg az adatbázis általános méretezhetőségével. Az SQL-adatbázisnak meg kell akadályoznia, hogy egyetlen bérlő/felhasználó más bérlők kárára monopolizálja az adatbázis-erőforrásokat. Az előre definiált kvótákat meghaladó használatra adott válaszként az SQL Database csökkentheti az átviteli forgalmat, vagy szabályozási kivételekkel válaszolhat. A hatékonyság, például a kötegelés, lehetővé teszi, hogy több munkát végezjön az SQL Database-en, mielőtt elérné ezeket a korlátokat. 
* Kötegelés is hatékony architektúrák, amelyek több adatbázist (szilánkok) használó architektúrák. Az egyes adatbázisegységekkel való interakció hatékonysága továbbra is kulcsfontosságú tényező az általános méretezhetőségben. 

Az SQL Database használatának egyik előnye, hogy nem kell kezelniaz adatbázist üzemeltető kiszolgálókat. Ez a felügyelt infrastruktúra azonban azt is jelenti, hogy másképp kell gondolkodnia az adatbázis-optimalizálással kapcsolatban. A továbbiakban nem javíthatja az adatbázis hardverét vagy hálózati infrastruktúráját. A Microsoft Azure vezérli ezeket a környezeteket. A fő terület, amely szabályozhatja, hogy az alkalmazás hogyan lép kölcsönhatásba az SQL Database. A kötegelés az egyik ilyen optimalizálás. 

A papír első része az SQL Database-t használó .NET alkalmazások különböző kötegelési technikáit vizsgálja. Az utolsó két szakasz a kötegelési irányelveket és forgatókönyveket ismerteti.

## <a name="batching-strategies"></a>Kötegelési stratégiák

### <a name="note-about-timing-results-in-this-article"></a>Megjegyzés az időzítés eredményeiről ebben a cikkben

> [!NOTE]
> Az eredmények nem viszonyítási pontok , hanem **relatív teljesítmény**rejlett . Az időzítések átlagosan legalább 10 tesztfuttatáson alapulnak. A műveletek üres táblába kerülnek. Ezeket a teszteket a V12 előtti enmérőket mértük, és nem feltétlenül felelnek meg az új [DTU-szolgáltatásszintek](sql-database-service-tiers-dtu.md) vagy [virtuálismag-szolgáltatásszintek](sql-database-service-tiers-vcore.md)használatával a V12-es adatbázisban tapasztalt átviteli hangnak. A kötegelési technika relatív előnyeinek hasonlónak kell lenniük.

### <a name="transactions"></a>Tranzakciók

Úgy tűnik, furcsa, hogy kezdődik a felülvizsgálat kötegelés megvitatásával tranzakciókat. Az ügyféloldali tranzakciók használata azonban finom kiszolgálóoldali kötegelési hatással rendelkezik, amely javítja a teljesítményt. A tranzakciók pedig csak néhány sornyi kóddal adhatók hozzá, így gyors módot biztosítanak a szekvenciális műveletek teljesítményének javítására.

Vegye figyelembe a következő C# kódot, amely egy egyszerű tábla beszúrási és frissítési műveleteinek sorozatát tartalmazza.

```csharp
List<string> dbOperations = new List<string>();
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
dbOperations.Add("insert MyTable values ('new value',1)");
dbOperations.Add("insert MyTable values ('new value',2)");
dbOperations.Add("insert MyTable values ('new value',3)");
```
A következő ADO.NET kód egymás után hajtja végre ezeket a műveleteket.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    conn.Open();

    foreach(string commandString in dbOperations)
    {
        SqlCommand cmd = new SqlCommand(commandString, conn);
        cmd.ExecuteNonQuery();
    }
}
```

A kód optimalizálásának legjobb módja a hívások ügyféloldali kötegelésének valamilyen formája. De van egy egyszerű módja annak, hogy növelje a teljesítményt a kódot egyszerűen csomagolás a hívások sorrendjét egy tranzakció. Itt van ugyanaz a kód, amely egy tranzakciót használ.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    conn.Open();
    SqlTransaction transaction = conn.BeginTransaction();

    foreach (string commandString in dbOperations)
    {
        SqlCommand cmd = new SqlCommand(commandString, conn, transaction);
        cmd.ExecuteNonQuery();
    }

    transaction.Commit();
}
```

A tranzakciókat mindkét példában ténylegesen használják. Az első példában minden egyes hívás implicit tranzakció. A második példában egy explicit tranzakció az összes hívást lezárja. Az [íráselőtti tranzakciónapló](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide?view=sql-server-ver15#WAL)dokumentációja szerint a naplórekordok a tranzakció véglegesítésekekén a lemezre kerülnek. Így azáltal, hogy több hívást a tranzakció, a tranzakciónaplóba írási késleltetheti, amíg a tranzakció véglegesítése. Valójában engedélyezi a kiszolgáló tranzakciós naplójába történő írások kötegelését.

Az alábbi táblázat néhány ad hoc tesztelési eredményt mutat be. A tesztek ugyanazokat a szekvenciális lapkákat végezték tranzakciókkal és tranzakciók nélkül. További perspektíva érdekében az első tesztkészlet távolról futott a laptopról a Microsoft Azure adatbázisára. A tesztek második készlete egy felhőszolgáltatásból és adatbázisból futott, amely egy microsoft Azure adatközpontban (USA nyugati része) található. Az alábbi táblázat a tranzakciókkal rendelkező és tranzakciók nélküli szekvenciális beszúrások ezredmásodpercben való időtartamát mutatja be.

**Helyszíni az Azure-ba:**

| Műveletek | Nincs tranzakció (ms) | Tranzakció (ms) |
| --- | --- | --- |
| 1 |130 |402 |
| 10 |1208 |1226 |
| 100 |12662 |10395 |
| 1000 |128852 |102917 |

**Az Azure-tól az Azure-ig (ugyanaz az adatközpont):**

| Műveletek | Nincs tranzakció (ms) | Tranzakció (ms) |
| --- | --- | --- |
| 1 |21 |26 |
| 10 |220 |56 |
| 100 |2145 |341 |
| 1000 |21479 |2756 |

> [!NOTE]
> Az eredmények nem viszonyítási pontok. Tekintse meg az [időzítési eredményekről szóló feljegyzést ebben a cikkben.](#note-about-timing-results-in-this-article)

Az előző teszteredmények alapján a tranzakció egyetlen műveletének becsomagolása ténylegesen csökkenti a teljesítményt. De ahogy egyetlen tranzakción belül növeli a műveletek számát, a teljesítmény javulása markánsabbá válik. A teljesítménykülönbség akkor is észrevehetőbb, ha a Microsoft Azure adatközponton belül minden művelet történik. Az SQL Database microsoft Azure adatközponton kívülről történő használatának megnövekedett késése háttérbe szorítja a tranzakciók használatának teljesítménynövekedését.

Bár a tranzakciók használata növelheti a teljesítményt, továbbra is [tartsa be a tranzakciókra és kapcsolatokra vonatkozó bevált gyakorlatokat.](https://msdn.microsoft.com/library/ms187484.aspx) Tartsa a tranzakciót a lehető legrövidebbre, és zárja be az adatbázis-kapcsolatot a munka befejezése után. Az előző példában található használati utasítás biztosítja, hogy a kapcsolat a következő kódblokk befejezésekor lezárul.

Az előző példa bemutatja, hogy helyi tranzakciót adhat bármely ADO.NET kódhoz két szóval. A tranzakciók gyors módot kínálnak a szekvenciális beszúrási, frissítési és törlési műveleteket eredményező kódok teljesítményének javítására. A leggyorsabb teljesítmény érdekében azonban fontolja meg a kód további módosítását az ügyféloldali kötegelés, például a tábla értékű paraméterek előnyeinek kihasználása érdekében.

A ADO.NET tranzakciókról a [Helyi tranzakciók ADO.NET című témakörben](https://docs.microsoft.com/dotnet/framework/data/adonet/local-transactions)talál további információt.

### <a name="table-valued-parameters"></a>Tábla értékű paraméterek

A táblaértékű paraméterek támogatják a felhasználó által definiált táblatípusokat a Transact-SQL utasítások, a tárolt eljárások és a függvények paramétereiként. Ez az ügyféloldali kötegelési technika lehetővé teszi, hogy több soradatot küldjön a tábla értékű paraméteren belül. A táblaértékű paraméterek használatához először definiáljon egy táblatípust. A következő Transact-SQL utasítás létrehoz egy **SajatTableType**nevű táblatípust.

```sql
    CREATE TYPE MyTableType AS TABLE 
    ( mytext TEXT,
      num INT );
```

A kódban egy **DataTable táblát** hoz létre pontosan ugyanolyan nevekkel és típusokkal, mint a táblatípus. Adja át ezt a **DataTable** paramétert egy szöveges lekérdezésben vagy tárolt eljáráshívásban. A következő példa ezt a technikát mutatja be:

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    DataTable table = new DataTable();
    // Add columns and rows. The following is a simple example.
    table.Columns.Add("mytext", typeof(string));
    table.Columns.Add("num", typeof(int));
    for (var i = 0; i < 10; i++)
    {
        table.Rows.Add(DateTime.Now.ToString(), DateTime.Now.Millisecond);
    }

    SqlCommand cmd = new SqlCommand(
        "INSERT INTO MyTable(mytext, num) SELECT mytext, num FROM @TestTvp",
        connection);

    cmd.Parameters.Add(
        new SqlParameter()
        {
            ParameterName = "@TestTvp",
            SqlDbType = SqlDbType.Structured,
            TypeName = "MyTableType",
            Value = table,
        });

    cmd.ExecuteNonQuery();
}
```

Az előző példában az **SqlCommand** objektum sorokat szúr be egy táblaértékű ** \@TestTvp**paraméterből. A korábban létrehozott **DataTable** objektum ehhez a paraméterhez van rendelve az **SqlCommand.Parameters.Add** metódussal. A lapkák kötegelése egy hívásban jelentősen növeli a teljesítményt a szekvenciális lapkákkal szemben.

Az előző példa további javításához használjon tárolt eljárást a szövegalapú parancs helyett. A következő Transact-SQL parancs olyan tárolt eljárást hoz létre, amely a **SimpleTestTableType** táblaértékű paramétert veszi igénybe.

```sql
CREATE PROCEDURE [dbo].[sp_InsertRows] 
@TestTvp as MyTableType READONLY
AS
BEGIN
INSERT INTO MyTable(mytext, num) 
SELECT mytext, num FROM @TestTvp
END
GO
```

Ezután módosítsa az **SqlCommand** objektumdeklarációját az előző kódpéldában a következőre.

```csharp
SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
cmd.CommandType = CommandType.StoredProcedure;
```

A legtöbb esetben a táblaértékű paraméterek teljesítménye egyenértékű vagy jobb, mint a többi kötegelési technika. A táblaértékű paraméterek gyakran előnyösebbek, mivel rugalmasabbak, mint más lehetőségek. Más technikák, például az SQL tömeges másolása például csak új sorok beszúrását teszik lehetővé. A táblaértékű paraméterekkel azonban a tárolt eljárás logikájával meghatározhatja, hogy mely sorok a frissítések, és melyek a beszúrások. A táblatípus úgy is módosítható, hogy tartalmazzon egy "Művelet" oszlopot, amely jelzi, hogy a megadott sort be kell-e szúrni, frissíteni vagy törölni kell.The table type can also be modified to contain a "Operation" column that indicates whether the specified row should be inserted, updated, or deleted.

Az alábbi táblázat a táblázatban értékelt paraméterek ezredmásodpercben történő használatára vonatkozó ad hoc teszteredményeket mutatja be.

| Műveletek | Helyszíni azure-ba (ms) | Azonos adatközpont (ms) Azonos Azure |
| --- | --- | --- |
| 1 |124 |32 |
| 10 |131 |25 |
| 100 |338 |51 |
| 1000 |2615 |382 |
| 10000 |23830 |3586 |

> [!NOTE]
> Az eredmények nem viszonyítási pontok. Tekintse meg az [időzítési eredményekről szóló feljegyzést ebben a cikkben.](#note-about-timing-results-in-this-article)
> 
> 

A kötegelés ből származó teljesítménynövekedés azonnal nyilvánvalóvá válik. Az előző szekvenciális tesztben 1000 művelet 129 másodpercet vett igénybe az adatközponton kívül és 21 másodpercet az adatközponton belül. A táblaértékű paraméterekkel azonban 1000 művelet mindössze 2,6 másodpercet vesz igénybe az adatközponton kívül és 0,4 másodpercet az adatközponton belül.

A tábla értékű paraméterekről további információt a [Táblaértékű paraméterek című témakörben talál.](https://msdn.microsoft.com/library/bb510489.aspx)

### <a name="sql-bulk-copy"></a>SQL tömeges másolat

Az SQL tömeges másolása egy másik módja annak, hogy nagy mennyiségű adatot szúrjon be a céladatbázisba. A .NET-alkalmazások az **SqlBulkCopy** osztály segítségével tömeges beszúrási műveleteket hajthatnak végre. **Az SqlBulkCopy** függvénye hasonló a parancssori eszközhöz **(Bcp.exe)** vagy a TRANSact-SQL utasításhoz, **a BULK INSERT utasításhoz.** A következő kód példa bemutatja, hogyan kell tömegesen másolni a sorokat a forrás **DataTable**, tábla, a cél tábla SQL Server, MyTable.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    using (SqlBulkCopy bulkCopy = new SqlBulkCopy(connection))
    {
        bulkCopy.DestinationTableName = "MyTable";
        bulkCopy.ColumnMappings.Add("mytext", "mytext");
        bulkCopy.ColumnMappings.Add("num", "num");
        bulkCopy.WriteToServer(table);
    }
}
```

Vannak olyan esetek, amikor a tömeges másolás előnyben részesítendő a tábla értékű paraméterekkel szemben. Lásd a Tábla értékű paraméterek és a BULK INSERT műveletek összehasonlító táblázatát a [Table-Valued Parameters](https://msdn.microsoft.com/library/bb510489.aspx)című cikkben.

A következő ad hoc vizsgálati eredmények az **SqlBulkCopy-nal** való kötegelés teljesítményét mutatják ezredmásodpercben.

| Műveletek | Helyszíni azure-ba (ms) | Azonos adatközpont (ms) Azonos Azure |
| --- | --- | --- |
| 1 |433 |57 |
| 10 |441 |32 |
| 100 |636 |53 |
| 1000 |2535 |341 |
| 10000 |21605 |2737 |

> [!NOTE]
> Az eredmények nem viszonyítási pontok. Tekintse meg az [időzítési eredményekről szóló feljegyzést ebben a cikkben.](#note-about-timing-results-in-this-article)
> 
> 

Kisebb kötegméretek esetén a tábla értékű paraméterek túlteljesítettek az **SqlBulkCopy** osztálynál. Az **SqlBulkCopy** azonban 12–31%-kal gyorsabban teljesített, mint az 1000 és 10 000 soros tesztek táblaértékű paraméterei. A táblaértékű paraméterekhez hasonlóan az **SqlBulkCopy** is jó választás kötegelt beszúrások esetén, különösen a nem kötegelt műveletek teljesítményéhez képest.

A ADO.NET tömeges másolásáról az [SQL Server tömeges másolása című témakörben](https://msdn.microsoft.com/library/7ek5da1a.aspx)talál további információt.

### <a name="multiple-row-parameterized-insert-statements"></a>Többsoros paraméteres INSERT utasítások

A kis kötegek egyik alternatívája egy nagy paraméteres INSERT utasítás összeállítása, amely több sort szúr be. A következő kódpélda bemutatja ezt a technikát.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    string insertCommand = "INSERT INTO [MyTable] ( mytext, num ) " +
        "VALUES (@p1, @p2), (@p3, @p4), (@p5, @p6), (@p7, @p8), (@p9, @p10)";

    SqlCommand cmd = new SqlCommand(insertCommand, connection);

    for (int i = 1; i <= 10; i += 2)
    {
        cmd.Parameters.Add(new SqlParameter("@p" + i.ToString(), "test"));
        cmd.Parameters.Add(new SqlParameter("@p" + (i+1).ToString(), i));
    }

    cmd.ExecuteNonQuery();
}
```

Ez a példa az alapkoncepció bemutatására szól. Egy reálisabb forgatókönyv a lekérdezési karakterlánc és a parancsparaméterek egyidejű létrehozásához szükséges entitásokon keresztül haladna. Összesen 2100 lekérdezési paraméterre van korlátozva, így ez korlátozza az így feldolgozható sorok számát.

A következő ad hoc teszteredmények az ilyen típusú beszúrási utasítás teljesítményét mutatják ezredmásodpercben.

| Műveletek | Táblaértékű paraméterek (ms) | Egyutasításos INSERT (ms) |
| --- | --- | --- |
| 1 |32 |20 |
| 10 |30 |25 |
| 100 |33 |51 |

> [!NOTE]
> Az eredmények nem viszonyítási pontok. Tekintse meg az [időzítési eredményekről szóló feljegyzést ebben a cikkben.](#note-about-timing-results-in-this-article)
> 
> 

Ez a megközelítés kissé gyorsabb lehet a 100 sornál kisebb kötegek esetén. Bár a fejlesztés kicsi, ez a technika egy másik lehetőség, amely jól működik az adott alkalmazás forgatókönyvében.

### <a name="dataadapter"></a>DataAdapter

A **DataAdapter** osztály lehetővé teszi egy **DataSet** objektum módosítását, majd a módosítások INSERT, UPDATE és DELETE műveletekként történő elküldését. Ha így használja a **DataAdaptert,** fontos megjegyezni, hogy minden egyes különböző művelethez külön hívás történik. A teljesítmény javítása érdekében használja az **UpdateBatchSize** tulajdonságot az egyszerre kötegelt műveletek számára. További információt a [Kötegelt műveletek végrehajtása adatadapterek használatával](https://msdn.microsoft.com/library/aadf8fk2.aspx)című témakörben talál.

### <a name="entity-framework"></a>Entitás keretrendszere

Entitás keretrendszer jelenleg nem támogatja a kötegelés. A közösség különböző fejlesztői megpróbálták bemutatni a megoldásokat, például felülbírálták a **SaveChanges** metódust. De a megoldások általában összetettek, és az alkalmazáshoz és az adatmodellhez vannak szabva. Az Entity Framework codeplex projekt jelenleg rendelkezik egy vitalapezzel a szolgáltatáskéréssel. A vitafórum megtekintéséhez olvassa el a [Tervezési értekezleti megjegyzések – 2012.](https://entityframework.codeplex.com/wikipage?title=Design%20Meeting%20Notes%20-%20August%202%2c%202012)

### <a name="xml"></a>XML

A teljesség érdekében fontosnak teszünk, hogy az XML-ről kötegelési stratégiaként beszéljünk. Az XML használatának azonban nincs előnye más módszerekkel szemben, és számos hátránya van. A megközelítés hasonló a tábla értékű paraméterekhez, de egy XML-fájlt vagy karakterláncot a rendszer a felhasználó által definiált tábla helyett tárolt eljárásnak ad át. A tárolt eljárás elemzi a parancsokat a tárolt eljárásban.

Ennek a megközelítésnek számos hátránya van:

* Az XML használata nehézkes és hibaérzékeny lehet.
* Az XML elemzése az adatbázisban processzorigényes lehet.
* A legtöbb esetben ez a módszer lassabb, mint a tábla értékű paraméterek.

Ezen okok miatt az XML használata kötegelt lekérdezésekhez nem ajánlott.

## <a name="batching-considerations"></a>A kötegelési szempontok

A következő szakaszok további útmutatást nyújtanak az SQL Database-alkalmazások kötegelésének használatához.

### <a name="tradeoffs"></a>Kompromisszumokat

Az architektúrától függően a kötegelés a teljesítmény és a rugalmasság közötti kompromisszumot is magában foglalhat. Vegyük például azt a forgatókönyvet, amelyben a szerepkör váratlanul leáll. Ha egy adatsort veszít el, a hatás kisebb, mint a be nem küldött sorok nagy kötegének elvesztése. Nagyobb a kockázata annak, ha puffereli a sorokat, mielőtt egy adott időablakban elküldenék őket az adatbázisba.

A kompromisszum miatt értékelje ki a kötegelt műveletek típusát. Agresszívebben (nagyobb kötegek és hosszabb időablakok) kevésbé kritikus adatokkal kötegel.

### <a name="batch-size"></a>Köteg mérete

A mi vizsgálatok, nem volt jellemzően nem volt előnye, hogy törés nagy tételek kisebb darabokat. Valójában ez az albontás gyakran lassabb teljesítményt eredményezett, mint egyetlen nagy tétel benyújtása. Vegyünk például egy olyan esetet, amelyben 1000 sort szeretne beszúrni. Az alábbi táblázat bemutatja, hogy mennyi ideig tart a tábla értékű paraméterek használata 1000 sor beszúrásához, ha kisebb kötegekre van osztva.

| Köteg mérete | Ismétléseket | Táblaértékű paraméterek (ms) |
| --- | --- | --- |
| 1000 |1 |347 |
| 500 |2 |355 |
| 100 |10 |465 |
| 50 |20 |630 |

> [!NOTE]
> Az eredmények nem viszonyítási pontok. Tekintse meg az [időzítési eredményekről szóló feljegyzést ebben a cikkben.](#note-about-timing-results-in-this-article)
> 
> 

Láthatja, hogy az 1000 sor legjobb teljesítménye az, ha egyszerre küldi el őket. Más tesztek (nem látható itt), volt egy kis teljesítmény nyereség megtörni egy 10000 sor tétel két tétel 5000. De a tábla sémája ezeka tesztek viszonylag egyszerű, ezért teszteket kell végeznie a konkrét adatok és kötegméretek ellenőrzéséhez ezeket az eredményeket.

Egy másik figyelembe vemandó tényező, hogy ha a teljes köteg túl nagy lesz, az SQL Database lehet, hogy szabályozza, és nem véglegesíti a köteget. A legjobb eredmény érdekében tesztelje az adott forgatókönyvet annak megállapítására, hogy van-e ideális kötegméret. A kötegméret konfigurálhatóvá tétele futásidőben a teljesítmény vagy a hibák alapján a gyors módosítások engedélyezéséhez.

Végül egyensúlyozza ki a köteg méretét a kötegeléssel kapcsolatos kockázatokkal. Ha átmeneti hibák vannak, vagy a szerepkör meghibásodik, fontolja meg a művelet újbóli megkísérlésének vagy a kötegben lévő adatok elvesztésének következményeit.

### <a name="parallel-processing"></a>Párhuzamos feldolgozás

Mi a teendő, ha a kötegméret csökkentésének megközelítését alkalmazta, de több szálat használt a munka végrehajtásához? Ismét, a vizsgálatok azt mutatták, hogy több kisebb többszálas tételek jellemzően végzett rosszabb, mint egy nagyobb tétel. A következő teszt1000 sor beillesztésére irányul egy vagy több párhuzamos kötegbe. Ez a teszt azt mutatja, hogy a több egyidejű köteg ténylegesen csökkentette a teljesítményt.

| Kötegmérete [Iterációk] | Két szál (ms) | Négy szál (ms) | Hat szál (ms) |
| --- | --- | --- | --- |
| 1000 [1] |277 |315 |266 |
| 500 [2] |548 |278 |256 |
| 250 [4] |405 |329 |265 |
| 100 [10] |488 |439 |391 |

> [!NOTE]
> Az eredmények nem viszonyítási pontok. Tekintse meg az [időzítési eredményekről szóló feljegyzést ebben a cikkben.](#note-about-timing-results-in-this-article)
> 
> 

A párhuzamosság miatt a teljesítmény romlásának számos lehetséges oka lehet:

* Egy helyett több egyidejű hálózati hívás van.
* Egy tábla több művelete versengést és blokkolást eredményezhet.
* A többszálas működéshez kapcsolódó általános költségek vannak társítva.
* A több kapcsolat megnyitásának költsége meghaladja a párhuzamos feldolgozás előnyeit.

Ha különböző táblákat vagy adatbázisokat céloz meg, ezzel a stratégiával teljesítménynövekedést láthat. Adatbázis-szilánkolás vagy összevonások lenne egy forgatókönyv erre a megközelítésre. A szilánkok több adatbázist használ, és különböző adatokat vezetnek az egyes adatbázisokhoz. Ha minden kis köteg egy másik adatbázisba kerül, akkor a műveletek párhuzamos végrehajtása hatékonyabb lehet. Azonban a teljesítménynyereség nem elég jelentős ahhoz, hogy a megoldásban az adatbázis-kicsikőzés használatának alapjaként használható.

Egyes tervekben a kisebb kötegek párhuzamos végrehajtása a terhelés alatt lévő rendszerben a kérelmek nagyobb átviteli teljesítményét eredményezheti. Ebben az esetben, annak ellenére, hogy gyorsabb egy nagyobb köteg feldolgozása, több kötegek feldolgozása párhuzamosan hatékonyabb lehet.

Ha párhuzamos végrehajtást használ, fontolja meg a munkaszálak maximális számának szabályozását. A kisebb szám kevesebb versengést és gyorsabb végrehajtási időt eredményezhet. Vegye figyelembe azt a további terhelést is, amelyet ez a céladatbázisra helyez mind a kapcsolatokban, mind a tranzakciókban.

### <a name="related-performance-factors"></a>Kapcsolódó teljesítménytényezők

Az adatbázis teljesítményére vonatkozó tipikus útmutatás szintén hatással van a kötegelésre. A beszúrási teljesítmény például csökken a nagy elsődleges kulccsal vagy sok nem fürtözött indexszel rendelkező táblák esetében.

Ha a táblaértékű paraméterek tárolt eljárást használnak, az eljárás elején használhatja a **SET NOCOUNT ON** parancsot. Ez az utasítás letiltja az érintett sorok számának visszatérését az eljárásban. Tesztjeink során azonban a **SET NOCOUNT ON** használata vagy nem volt hatással, vagy csökkent a teljesítmény. A teszttárolt eljárás egyszerű volt a táblaértékű paraméter egyetlen **INSERT** paranccsal. Lehetséges, hogy az összetettebb tárolt eljárások számára előnyös lenne ez a nyilatkozat. De ne feltételezze, hogy **a SET NOCOUNT ON** hozzáadása a tárolt eljáráshoz automatikusan javítja a teljesítményt. A hatás megértéséhez tesztelje a tárolt eljárást a **SET NOCOUNT ON** utasítással és anélkül.

## <a name="batching-scenarios"></a>Kötegelési forgatókönyvek

A következő szakaszok ismertetik, hogyan kell használni a tábla értékű paraméterek három alkalmazási forgatókönyvek. Az első forgatókönyv bemutatja, hogyan működhet együtt a pufferelés és a kötegelés. A második forgatókönyv javítja a teljesítményt azáltal, hogy egyetlen tárolt eljáráshívásban végzi a főműveleti műveleteket. A végső forgatókönyv bemutatja, hogyan kell használni a tábla értékű paramétereket egy "UPSERT" műveletben.

### <a name="buffering"></a>Pufferelés

Bár vannak olyan forgatókönyvek, amelyek nyilvánvalóan jelölt kötegelés, számos forgatókönyv, amely kihasználhatja a kötegelés késleltetett feldolgozás. A késleltetett feldolgozás azonban nagyobb kockázatot is hordoz, ha az adatok váratlan hiba esetén elvesznek. Fontos megérteni ezt a kockázatot, és figyelembe venni a következményeket.

Vegyünk például egy olyan webalkalmazást, amely nyomon követi az egyes felhasználók navigációs előzményeit. Az alkalmazás minden oldalkérésnél adatbázis-hívást kezdeményezhet a felhasználó oldalnézetének rögzítésére. De nagyobb teljesítmény és méretezhetőség érhető el pufferelés a felhasználók navigációs tevékenységek, majd elküldi ezeket az adatokat az adatbázisba kötegekben. Az adatbázis-frissítést eltelt idő és/vagy pufferméret szerint aktiválhatja. Egy szabály megadhatja például, hogy a köteget 20 másodperc után, vagy ha a puffer eléri az 1000 elemet, fel kell dolgozni.

A következő kódpélda [a Reactive Extensions - Rx](https://msdn.microsoft.com/data/gg577609) segítségével dolgozza fel a figyelési osztály által előadott pufferelt eseményeket. A puffer kitöltésekor vagy az időtúllépés elérésekor a felhasználói adatok kötege táblaértékű paraméterrel kerül az adatbázisba.

A következő NavHistoryData osztály modellezi a felhasználói navigáció részleteit. Olyan alapvető információkat tartalmaz, mint a felhasználói azonosító, az elért URL-cím és a hozzáférési idő.

```csharp
public class NavHistoryData
{
    public NavHistoryData(int userId, string url, DateTime accessTime)
    { UserId = userId; URL = url; AccessTime = accessTime; }
    public int UserId { get; set; }
    public string URL { get; set; }
    public DateTime AccessTime { get; set; }
}
```

A NavHistoryDataMonitor osztály felelős a felhasználói navigációs adatok adatbázisba való pufferelésért. Tartalmaz egy metódust, RecordUserNavigationEntry, amely válaszol emelésével **OnAdded** esemény. A következő kód azt a konstruktorlogikát mutatja be, amely az Rx segítségével az esemény alapján megfigyelhető gyűjteményt hoz létre. Ezután előfizet erre a megfigyelhető gyűjteményre a Buffer metódussal. A túlterhelés azt határozza meg, hogy a puffert 20 másodpercenként vagy 1000 bejegyzésként kell elküldeni.

```csharp
public NavHistoryDataMonitor()
{
    var observableData =
        Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

    observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
}
```

A kezelő az összes pufferelt elemet táblaértékű típussá alakítja, majd átadja ezt a típust egy tárolt eljárásnak, amely feldolgozza a köteget. A következő kód a NavHistoryDataEventArgs és a NavHistoryDataMonitor osztályok teljes definícióját mutatja.

```csharp
public class NavHistoryDataEventArgs : System.EventArgs
{
    public NavHistoryDataEventArgs(NavHistoryData data) { Data = data; }
    public NavHistoryData Data { get; set; }
}

public class NavHistoryDataMonitor
{
    public event EventHandler<NavHistoryDataEventArgs> OnAdded;

    public NavHistoryDataMonitor()
    {
        var observableData =
            Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

        observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
    }
```

A kezelő az összes pufferelt elemet táblaértékű típussá alakítja, majd átadja ezt a típust egy tárolt eljárásnak, amely feldolgozza a köteget. A következő kód a NavHistoryDataEventArgs és a NavHistoryDataMonitor osztályok teljes definícióját mutatja.

```csharp
    public class NavHistoryDataEventArgs : System.EventArgs
    {
        if (OnAdded != null)
            OnAdded(this, new NavHistoryDataEventArgs(data));
    }

    protected void Handler(IList<EventPattern<NavHistoryDataEventArgs>> items)
    {
        DataTable navHistoryBatch = new DataTable("NavigationHistoryBatch");
        navHistoryBatch.Columns.Add("UserId", typeof(int));
        navHistoryBatch.Columns.Add("URL", typeof(string));
        navHistoryBatch.Columns.Add("AccessTime", typeof(DateTime));
        foreach (EventPattern<NavHistoryDataEventArgs> item in items)
        {
            NavHistoryData data = item.EventArgs.Data;
            navHistoryBatch.Rows.Add(data.UserId, data.URL, data.AccessTime);
        }

        using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
        {
            connection.Open();

            SqlCommand cmd = new SqlCommand("sp_RecordUserNavigation", connection);
            cmd.CommandType = CommandType.StoredProcedure;

            cmd.Parameters.Add(
                new SqlParameter()
                {
                    ParameterName = "@NavHistoryBatch",
                    SqlDbType = SqlDbType.Structured,
                    TypeName = "NavigationHistoryTableType",
                    Value = navHistoryBatch,
                });

            cmd.ExecuteNonQuery();
        }
    }
}
```

A pufferelési osztály használatához az alkalmazás statikus NavHistoryDataMonitor objektumot hoz létre. Minden alkalommal, amikor egy felhasználó hozzáfér egy laphoz, az alkalmazás meghívja a NavHistoryDataMonitor.RecordUserNavigationEntry metódust. A pufferelési logika folytatja, hogy ezeket a bejegyzéseket kötegekben küldje el az adatbázisba.

### <a name="master-detail"></a>Fő részlet

A táblaértékű paraméterek egyszerű INSERT esetekben hasznosak. Azonban nagyobb kihívást jelenthet a kötegbeszúrások, amelyek több táblát foglalnak magukban. A "fő/részlet" forgatókönyv jó példa erre. A főtábla azonosítja az elsődleges entitást. Egy vagy több részlettábla további adatokat tárol az entitásról. Ebben a forgatókönyvben az idegen kulcs kapcsolatok érvényt szereznek a részletek és az egyedi főentitás kapcsolatának. Fontolja meg a PurchaseOrder tábla és a hozzá tartozó OrderDetail tábla egyszerűsített verzióját. A következő Transact-SQL négy oszlopból hozza létre a PurchaseOrder táblát: OrderID, OrderDate, CustomerID és Status.

```sql
CREATE TABLE [dbo].[PurchaseOrder](
[OrderID] [int] IDENTITY(1,1) NOT NULL,
[OrderDate] [datetime] NOT NULL,
[CustomerID] [int] NOT NULL,
[Status] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_PurchaseOrder] 
PRIMARY KEY CLUSTERED ( [OrderID] ASC ))
```

Minden rendelés egy vagy több termékvásárlást tartalmaz. Ez az információ a PurchaseOrderDetail táblában kerül rögzítésre. A következő Transact-SQL öt oszlopból hozza létre a PurchaseOrderDetail táblát: OrderID, OrderDetailID, ProductID, UnitPrice és OrderQty.

```sql
CREATE TABLE [dbo].[PurchaseOrderDetail](
[OrderID] [int] NOT NULL,
[OrderDetailID] [int] IDENTITY(1,1) NOT NULL,
[ProductID] [int] NOT NULL,
[UnitPrice] [money] NULL,
[OrderQty] [smallint] NULL,
CONSTRAINT [PrimaryKey_PurchaseOrderDetail] PRIMARY KEY CLUSTERED 
( [OrderID] ASC, [OrderDetailID] ASC ))
```

A PurchaseOrderDetail tábla OrderID oszlopának hivatkoznia kell egy rendelésre a PurchaseOrder táblából. Az idegen kulcs következő definíciója kényszeríti ezt a megkötést.

```sql
ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD 
CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
REFERENCES [dbo].[PurchaseOrder] ([OrderID])
```

A táblaértékű paraméterek használatához minden céltáblához egy felhasználó által definiált táblatípussal kell rendelkeznie.

```sql
CREATE TYPE PurchaseOrderTableType AS TABLE 
( OrderID INT,
    OrderDate DATETIME,
    CustomerID INT,
    Status NVARCHAR(50) );
GO

CREATE TYPE PurchaseOrderDetailTableType AS TABLE 
( OrderID INT,
    ProductID INT,
    UnitPrice MONEY,
    OrderQty SMALLINT );
GO
```

Ezután definiáljon egy tárolt eljárást, amely elfogadja az ilyen típusú táblákat. Ez az eljárás lehetővé teszi, hogy egy alkalmazás helyileg kötegelje a rendelések és a rendelés részleteit egyetlen hívásban. A következő Transact-SQL a teljes tárolt eljárásdeklarációt tartalmazza ehhez a beszerzési rendeléshez.

```sql
CREATE PROCEDURE sp_InsertOrdersBatch (
@orders as PurchaseOrderTableType READONLY,
@details as PurchaseOrderDetailTableType READONLY )
AS
SET NOCOUNT ON;

-- Table that connects the order identifiers in the @orders
-- table with the actual order identifiers in the PurchaseOrder table
DECLARE @IdentityLink AS TABLE ( 
SubmittedKey int, 
ActualKey int, 
RowNumber int identity(1,1)
);

-- Add new orders to the PurchaseOrder table, storing the actual
-- order identifiers in the @IdentityLink table   
INSERT INTO PurchaseOrder ([OrderDate], [CustomerID], [Status])
OUTPUT inserted.OrderID INTO @IdentityLink (ActualKey)
SELECT [OrderDate], [CustomerID], [Status] FROM @orders ORDER BY OrderID;

-- Match the passed-in order identifiers with the actual identifiers
-- and complete the @IdentityLink table for use with inserting the details
WITH OrderedRows As (
SELECT OrderID, ROW_NUMBER () OVER (ORDER BY OrderID) As RowNumber 
FROM @orders
)
UPDATE @IdentityLink SET SubmittedKey = M.OrderID
FROM @IdentityLink L JOIN OrderedRows M ON L.RowNumber = M.RowNumber;

-- Insert the order details into the PurchaseOrderDetail table, 
-- using the actual order identifiers of the master table, PurchaseOrder
INSERT INTO PurchaseOrderDetail (
[OrderID],
[ProductID],
[UnitPrice],
[OrderQty] )
SELECT L.ActualKey, D.ProductID, D.UnitPrice, D.OrderQty
FROM @details D
JOIN @IdentityLink L ON L.SubmittedKey = D.OrderID;
GO
```

Ebben a példában a @IdentityLink helyileg definiált tábla tárolja az újonnan beszúrt sorok tényleges OrderID értékeit. Ezek a rendelésazonosítók eltérnek a @orders tábla @details értékű paraméterek ideiglenes OrderID értékeitől. Ezért a @IdentityLink tábla ezután összekapcsolja az OrderID értékeket a @orders paraméterből a PurchaseOrder tábla új sorainak valódi OrderID értékeivel. Ezt a lépést @IdentityLink követően a tábla megkönnyítheti a rendelés részleteinek beszúrását a tényleges OrderID azonosítóval, amely megfelel az idegen kulcsmegkötésnek.

Ez a tárolt eljárás kódból vagy más Transact-SQL hívásokból használható. Tekintse meg a tábla értékű paraméterek szakasza a papír egy kódot példa. A következő Transact-SQL bemutatja, hogyan lehet hívni a sp_InsertOrdersBatch.

```sql
declare @orders as PurchaseOrderTableType
declare @details as PurchaseOrderDetailTableType

INSERT @orders 
([OrderID], [OrderDate], [CustomerID], [Status])
VALUES(1, '1/1/2013', 1125, 'Complete'),
(2, '1/13/2013', 348, 'Processing'),
(3, '1/12/2013', 2504, 'Shipped')

INSERT @details
([OrderID], [ProductID], [UnitPrice], [OrderQty])
VALUES(1, 10, $11.50, 1),
(1, 12, $1.58, 1),
(2, 23, $2.57, 2),
(3, 4, $10.00, 1)

exec sp_InsertOrdersBatch @orders, @details
```

Ez a megoldás lehetővé teszi, hogy minden köteg az 1-nél kezdődő OrderID értékeket használjon. Ezek az ideiglenes OrderID értékek a kötegben lévő kapcsolatokat írják le, de a tényleges OrderID értékeket a beszúrási művelet időpontjában határozzák meg. Az előző példában ugyanazokat az utasításokat többször is futtathatja, és egyedi rendeléseket hozhat létre az adatbázisban. Ezért fontolja meg további kód vagy adatbázis-logika hozzáadását, amely megakadályozza az ismétlődő rendeléseket ezzel a kötegelési technikával.

Ez a példa azt mutatja be, hogy még összetettebb adatbázis-műveletek, például a főrészletes műveletek, táblaértékű paraméterek használatával kötegelhetők.

### <a name="upsert"></a>UPSERT

Egy másik kötegelési forgatókönyv magában foglalja a meglévő sorok egyidejű frissítését és új sorok beszúrását. Ezt a műveletet néha "UPSERT" (update + insert) műveletnek is nevezik. Ahelyett, hogy külön hívásokat kezdeményezne az INSERT és update kezdeményezésre, a MERGE utasítás a legmegfelelőbb ehhez a feladathoz. A MERGE utasítás egyetlen hívással is végrehajthatja a beszúrási és a frissítési műveleteket.

A táblaértékű paraméterek a MERGE utasítással együtt használhatók a frissítések és beszúrások végrehajtására. Vegyünk például egy egyszerűsített Alkalmazott táblát, amely a következő oszlopokat tartalmazza: EmployeeID, Keresztnév, Vezetéknév, SocialSecurityNumber:

```sql
CREATE TABLE [dbo].[Employee](
[EmployeeID] [int] IDENTITY(1,1) NOT NULL,
[FirstName] [nvarchar](50) NOT NULL,
[LastName] [nvarchar](50) NOT NULL,
[SocialSecurityNumber] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED 
([EmployeeID] ASC ))
```

Ebben a példában használhatja azt a tényt, hogy a SocialSecurityNumber egyedi, hogy több alkalmazott egyesítése. Először hozza létre a felhasználó által definiált táblatípust:

```sql
CREATE TYPE EmployeeTableType AS TABLE 
( Employee_ID INT,
    FirstName NVARCHAR(50),
    LastName NVARCHAR(50),
    SocialSecurityNumber NVARCHAR(50) );
GO
```

Ezután hozzon létre egy tárolt eljárást vagy írjon olyan kódot, amely a MERGE utasítást használja a frissítés végrehajtásához és beszúrásához. A következő példa a MERGE utasítást használja @employeesegy tábla értékű paraméteren, amelyNek EmployeeTableType típusú. A @employees táblázat tartalma itt nem látható.

```sql
MERGE Employee AS target
USING (SELECT [FirstName], [LastName], [SocialSecurityNumber] FROM @employees) 
AS source ([FirstName], [LastName], [SocialSecurityNumber])
ON (target.[SocialSecurityNumber] = source.[SocialSecurityNumber])
WHEN MATCHED THEN 
UPDATE SET
target.FirstName = source.FirstName, 
target.LastName = source.LastName
WHEN NOT MATCHED THEN
    INSERT ([FirstName], [LastName], [SocialSecurityNumber])
    VALUES (source.[FirstName], source.[LastName], source.[SocialSecurityNumber]);
```

További információt a MERGE utasítás dokumentációjában és példáiban talál. Bár ugyanazt a munkát el lehet végezni egy többlépéses tárolt eljáráshívásban, külön INSERT és UPDATE műveletekkel, a MERGE utasítás hatékonyabb. Az adatbáziskód olyan Transact-SQL hívásokat is létrehozhat, amelyek közvetlenül használják a MERGE utasítást anélkül, hogy két adatbázis-hívást kellene kérniük az INSERT és az UPDATE számára.

## <a name="recommendation-summary"></a>Ajánlás összefoglalása

Az alábbi lista a cikkben tárgyalt kötegelési javaslatok összefoglalását tartalmazza:

* Az SQL Database-alkalmazások teljesítményének és méretezhetőségének növelése érdekében használjon pufferelést és kötegelést.
* Ismerje meg a kötegelés/pufferelés és a rugalmasság közötti kompromisszumot. Szerepkör-hiba esetén az üzleti legkritikusabb adatok feldolgozatlan kötegének elvesztésének kockázata meghaladja a kötegelés teljesítményelőnyét.
* A késés csökkentése érdekében próbálja meg az adatbázis összes hívását egyetlen adatközponton belül tartani.
* Ha egyetlen kötegelési technikát választ, a táblaértékű paraméterek biztosítják a legjobb teljesítményt és rugalmasságot.
* A leggyorsabb beszúrási teljesítmény érdekében kövesse az alábbi általános irányelveket, de tesztelje a forgatókönyvet:
  * 100 < esetén használjon egyetlen paraméteres INSERT parancsot.
  * 1000 < használjon táblaértékű paramétereket.
  * A >= 1000 sorhoz használja az SqlBulkCopy programot.
* A frissítésekhez és a műveletek törléséhez használjon táblaértékű paramétereket tárolt eljáráslogikával, amely meghatározza a helyes műveletet a táblaparaméter minden egyes sorában.
* A kötegméretre vonatkozó irányelvek:
  * Használja a legnagyobb kötegméreteket, amelyek az alkalmazás és az üzleti követelmények nek megfelelőek.
  * A nagy kötegek teljesítménynyereségének és az ideiglenes vagy katasztrofális hibák kockázatának egyensúlyba hozása. Mi a következménye az újrapróbálkozások nak vagy a kötegben lévő adatok elvesztésének? 
  * Tesztelje a legnagyobb kötegméretet, és ellenőrizze, hogy az SQL Database nem utasítja-e el.
  * Hozzon létre olyan konfigurációs beállításokat, amelyek szabályozzák a kötegelést, például a kötegméretet vagy a pufferelési időablakot. Ezek a beállítások rugalmasságot biztosítanak. Módosíthatja a kötegelési viselkedést éles környezetben a felhőszolgáltatás újratelepítése nélkül.
* Kerülje az egy adatbázis egyetlen tábláján működő kötegek párhuzamos végrehajtását. Ha úgy dönt, hogy egy köteget több munkaszál között oszt fel, futtasson teszteket a szálak ideális számának meghatározásához. Egy meghatározatlan küszöbérték után több szál csökkenti a teljesítményt, nem pedig növeli azt.
* Fontolja meg a pufferelés a méret és az idő, mint egy módja a kötegelés további forgatókönyvek megvalósítása.

## <a name="next-steps"></a>További lépések

Ez a cikk arra összpontosított, hogy az adatbázis-tervezési és kódolási technikák a kötegeléshez kapcsolódóan hogyan javíthatják az alkalmazás teljesítményét és méretezhetőségét. De ez csak egy tényező az átfogó stratégiádban. A teljesítmény és a méretezhetőség további módjaiért tekintse meg az [Azure SQL Database teljesítményútmutatóját az egyes adatbázisokhoz,](sql-database-performance-guidance.md) [valamint a rugalmas készlet ár- és teljesítményszempontjait.](sql-database-elastic-pool-guidance.md)

