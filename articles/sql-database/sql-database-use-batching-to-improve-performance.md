---
title: Kötegelés használata Azure SQL Database-alkalmazások teljesítményének javítása érdekében
description: A témakör igazolja, hogy adatbázis-műveletek kötegelése jelentősen növeli a sebességet és méretezhetőséget biztosít, az Azure SQL Database-alkalmazások. Bár a kötegelés technikák ugyanúgy alkalmazhatók bármely SQL Server-adatbázis, a cikk célja az Azure-ban.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: e76b5ecd3d6401c317f6500ec376fc25d3fa55b8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57997688"
---
# <a name="how-to-use-batching-to-improve-sql-database-application-performance"></a>Kötegelés használata SQL Database-alkalmazások teljesítményének javítása érdekében

Műveletek az Azure SQL Database kötegelés jelentősen növeli a teljesítményét és méretezhetőségét, az alkalmazások. Annak érdekében, hogy tájékozódjon, ez a cikk első részében vonatkozik néhány minta vizsgálati eredmények, amelyek egymást követő és kötegelt kérések egy SQL Database összehasonlítása. A cikk többi része a technikák, forgatókönyvek és annak érdekében, hogy az Azure-alkalmazások sikeresen kötegelés használata szempontok jeleníti meg.

## <a name="why-is-batching-important-for-sql-database"></a>Miért van az SQL Database fontos kötegelés

Kötegelés hívások egy távoli szolgáltatásnak egy jól ismert stratégiát teljesítmény és méretezhetőség növelése. Egy távoli szolgáltatással, például a szerializálási, a hálózati átvitel és a deszerializálás kölcsönhatások feldolgozási költségek vannak rögzítve. Ezeket a díjakat az egy kötegben több különálló tranzakciókat csomagolására minimálisra csökkenthető.

Ebből a cikkből szeretnénk vizsgálni különböző stratégiákat és forgatókönyvek kötegelés SQL-adatbázis. Bár ezek a stratégiák az SQL Server használó helyszíni alkalmazások számára is fontosak, több oka a kiemelés kötegelés az SQL Database használatát:

* Nincs esetleg nagyobb késést éri el az SQL Database, különösen akkor, ha az SQL-adatbázis elérésére a Microsoft Azure-adatközpontokon kívülre.
* Az SQL Database több-bérlős jellemzőit azt jelenti, hogy az adatok hozzáférési réteg utal. az adatbázis teljes méretezhetőségét hatékonyságát. Az SQL Database akadályozza meg, hogy az egyes bérlői felhasználók kisajátíthassa az adatbázis-erőforrások más bérlők rovására. Előre meghatározott kvóták felhasználást válaszul SQL Database csökkentheti az átviteli sebesség vagy szabályozási kivételeket elhárítását. Így a hatékonyságot, például a kötegelés, lehetővé teszi ezeket a korlátokat érhesse el az SQL Database további munkát. 
* Kötegelés akkor is, amelyeket több adatbázis (sharding) használata esetén. Az adatbázis-egységenként-szal hatékonyságát még egy kulcsfontosságú tényező az általános méretezhetőség. 

Az SQL Database használatával járó előnyöket egyik célja, hogy nem kell az adatbázist üzemeltető kiszolgálók felügyeletére. Azonban ez a felügyelt infrastruktúra is azt jelenti, hogy másképp állításoknak adatbázis-optimalizálást. Már nem megtekintheti az adatbázis hardverének vagy a hálózati infrastruktúra javítása érdekében. A Microsoft Azure szkriptjét szabályozza. Szabályozhatja a fő területen, hogy az alkalmazás hogyan használja az SQL Database. Kötegelés az ezek az optimalizációk egyik. 

A tanulmány első része a SQL Database használata .NET-alkalmazásokban különböző kötegelés technikák megvizsgálja. Az utolsó két szakasz tárgyalja a kötegelés irányelvek és forgatókönyvek.

## <a name="batching-strategies"></a>Kötegelés stratégiák

### <a name="note-about-timing-results-in-this-article"></a>Ebben a cikkben időzítési eredmények kapcsolatos megjegyzés:

> [!NOTE]
> Eredmények nem referenciaalapokhoz képest történő, de van kialakítva, hogy megjelenítése **relatív teljesítmény**. Időzítés legalább 10 tesztelések átlagosan alapulnak. Üres táblába beilleszti a műveletekre. Ezek a tesztek mért előtti-12-es, és nem feltétlenül felelnek az átviteli sebességet, amelyet Ön is szembesülhet az új V12-es adatbázisban [DTU szolgáltatásszintek](sql-database-service-tiers-dtu.md) vagy [virtuális mag szolgáltatásszintek](sql-database-service-tiers-vcore.md). A relatív előnye, hogy a kötegelés technika hasonlónak kell lenniük.

### <a name="transactions"></a>Tranzakciók

Úgy tűnik, rendellenes megvizsgálja a tranzakciók által kötegelés felülvizsgálatát a kezdéshez. De a tranzakciók ügyféloldali használata hatása a változás is kiszolgálóoldali kötegelés, amely javítja a teljesítményt. És a tranzakciók is hozzáadhatók csak néhány sornyi kóddal, így gyorsan egymást követő műveletek teljesítményének javítása érdekében biztosítanak.

Vegye figyelembe az alábbi C#-kódot, amely tartalmazza a Beszúrás sorozatát, és frissítési műveleteket végez egy egyszerű táblázat.

```csharp
List<string> dbOperations = new List<string>();
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
dbOperations.Add("insert MyTable values ('new value',1)");
dbOperations.Add("insert MyTable values ('new value',2)");
dbOperations.Add("insert MyTable values ('new value',3)");
```
A következő ADO.NET kód egymás után végrehajtja ezeket a műveleteket.

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

Ez a kód optimalizálása érdekében a legjobb módja, hogy valamilyen ügyféloldali kötegelés hívások. Azonban egy egyszerű módja, ez a kód a teljesítmény növeléséhez a egyszerűen alkalmazásburkoló hívások sorrendjét a tranzakcióban. Íme egy tranzakció használja ugyanazt a kódot.

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

Tranzakciók ténylegesen használnak a mindkét példa. Az első példában az egyes hívások egy implicit tranzakciók. A második példában az explicit tranzakciók burkolja az összes, a hívások. A dokumentációban száma a [írási előre tranzakciónapló](https://msdn.microsoft.com/library/ms186259.aspx), naplórekordok kiürített a lemezre, ha a tranzakció-véglegesítések. További hívások együtt egy tranzakcióban, így az a tranzakciós naplóba írás késleltetheti mindaddig, amíg a tranzakció véglegesítve. Érvényben engedélyezi az írási műveletek a kiszolgáló tranzakciónapló-kötegelésében.

Az alábbi táblázat néhány alkalmi vizsgálati eredményeket jeleníti meg. A tesztek kerülnek végrehajtásra azonos szekvenciális Beszúrások rendelkező és anélküli tranzakciók. Több szempontból a tesztek első készlete futtatott esetében távolról a laptopjáról a Microsoft Azure-adatbázishoz. Tesztek a második csoporton futtatott felhőszolgáltatást, illetve, hogy mindkét tartózkodott a ugyanazt a Microsoft Azure-adatközpont (USA nyugati RÉGIÓJA) adatbázis. Az alábbi táblázat az időtartam ezredmásodpercben, egymást követő Beszúrások rendelkező és anélküli tranzakciók.

**Az Azure-bA helyszíni**:

| Műveletek | Nem tranzakciós (ms) | Tranzakció (ms) |
| --- | --- | --- |
| 1 |130 |402 |
| 10 |1208 |1226 |
| 100 |12662 |10395 |
| 1000 |128852 |102917 |

**Azure-ba (ugyanabban az adatközpontban)**:

| Műveletek | Nem tranzakciós (ms) | Tranzakció (ms) |
| --- | --- | --- |
| 1 |21 |26 |
| 10 |220 |56 |
| 100 |2145 |341 |
| 1000 |21479 |2756 |

> [!NOTE]
> Eredmények nem referenciaalapokhoz képest történő állnak. Tekintse meg a [időzítési eredmények ebben a cikkben kapcsolatos megjegyzés](#note-about-timing-results-in-this-article).

Az előző ellenőrzés eredménye alapján a teljesítmény ténylegesen csökkenti alkalmazásburkoló egyetlen művelettel a tranzakcióban. De növelésével egy tranzakción belül műveletek számát, a teljesítmény fokozása több lesz megjelölve. A teljesítménybeli különbség akkor is jobban észlelhető, ha minden művelet fordul elő a Microsoft Azure-adatközpontban. Az SQL-adatbázisát használja a Microsoft Azure adatközponton kívülről a késések overshadows tranzakciók használatával, a teljesítmény miatt.

A tranzakciók használata növelheti a teljesítményt, bár továbbra is [tekintse át az ajánlott eljárások tranzakciók és a kapcsolatok](https://msdn.microsoft.com/library/ms187484.aspx). Tartsa a lehető legrövidebb tranzakciót, és a munka végeztével, zárja be az adatbázis-kapcsolat. Az utasítás használatával az előző példában biztosítja, hogy a kapcsolat megszakad, a következő kódblokk befejezéséről.

Az előző példa bemutatja, hogy adhat hozzá egy helyi tranzakció két sort az ADO.NET kód. Tranzakciók ajánlat gyorsan kódot, amely lehetővé teszi a szekvenciális beszúrási, frissítési és törlési műveletek teljesítményének javítása érdekében. Azonban a leggyorsabb teljesítmény érdekében érdemes igénybe veszi az ügyféloldali kötegelés, ilyenek például a tábla értékű paraméterek tovább a kód megváltoztatása.

ADO.NET-tranzakciók kapcsolatos további információkért lásd: [helyi ADO.NET-tranzakciók](https://docs.microsoft.com/dotnet/framework/data/adonet/local-transactions).

### <a name="table-valued-parameters"></a>tábla értékű paraméterek

Tábla értékű paraméter támogatja a felhasználó által definiált táblatípusokban a Transact-SQL-utasítások, a tárolt eljárások és függvények paraméterekként. Ez az ügyféloldali kötegelés módszer lehetővé teszi, hogy a tábla értékű paraméter belül több adatsor küldhet. Tábla értékű paraméterek használatához először meg kell határoznia egy táblatípus. A következő Transact-SQL-utasítást hoz létre, egy táblatípus, nevű **MyTableType**.

```sql
    CREATE TYPE MyTableType AS TABLE 
    ( mytext TEXT,
      num INT );
```

A kódban, hozzon létre egy **DataTable** pontosan ugyanazokat a neveket és a táblatípus típusú. Továbbítja **DataTable** szöveges lekérdezés vagy tárolt eljárás paraméter hívja. A következő példa ezt a módszert mutatja:

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

Az előző példában a **SqlCommand** objektum egy tábla értékű paraméter a sor beszúrása  **\@TestTvp**. A korábban létrehozott **DataTable** objektumot hozzá van rendelve ezt a paramétert a **SqlCommand.Parameters.Add** metódust. A teljesítmény hívásonként Beszúrások kötegelés jelentősen növeli a szekvenciális Beszúrások keresztül.

Az előző példában további javításához használja a tárolt eljárás egy szöveges alapú parancs helyett. A következő Transact-SQL parancs létrehoz egy tárolt eljárást, amely a **SimpleTestTableType** tábla értékű paraméter.

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

Módosítsa a **SqlCommand** objektumot az előző példakódban a következő nyilatkozatot.

```csharp
SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
cmd.CommandType = CommandType.StoredProcedure;
```

A legtöbb esetben tábla értékű paraméterek rendelkezik egyenértékű vagy annál nagyobb teljesítmény, mint a többi kötegelés technikákat. Tábla értékű paraméterek gyakran előnyös, mivel olyan rugalmasabb, mint a többi példány. Egyéb technikák, például az SQL tömeges másolási, például csak az új sorok beszúrását teszi lehetővé. De a tábla értékű paraméter használható logikai a tárolt eljárás annak meghatározására, hogy mely sorokat frissítések és amelyek szúr be. A tábla típusa is módosíthatja, amely azt jelzi, hogy egy megadott sorának kell lennie beszúrt, frissített vagy törölt egy "Művelet" oszlop tartalmazhat.

Az alábbi táblázat bemutatja a tábla értékű paraméterek használatával ad-hoc terhelésiteszt-eredményei ezredmásodpercben.

| Műveletek | A helyszíni az Azure-ba (ms) | Az Azure ugyanabban az adatközpontban (ms) |
| --- | --- | --- |
| 1 |124 |32 |
| 10 |131 |25 |
| 100 |338 |51 |
| 1000 |2615 |382 |
| 10000 |23830 |3586 |

> [!NOTE]
> Eredmények nem referenciaalapokhoz képest történő állnak. Tekintse meg a [időzítési eredmények ebben a cikkben kapcsolatos megjegyzés](#note-about-timing-results-in-this-article).
> 
> 

A teljesítmény nyereség a kötegelés azonnal látható. Az előző szekvenciális teszt 1000 műveletek tartott 129 másodperc az adatközponton kívülről és az adatközponton belül a 21-én másodperc. De tábla értékű paraméterek, 1000 operations csak az adatközponton kívülről és az adatközponton belül 0,4 másodperc 2.6-os másodpercig is eltarthat.

A tábla értékű paraméterek további információkért lásd: [Table-Valued paraméterek](https://msdn.microsoft.com/library/bb510489.aspx).

### <a name="sql-bulk-copy"></a>A tömeges másolási SQL

A tömeges másolási SQL egy másik módja a nagy mennyiségű adat beillesztése a céladatbázis. .NET-alkalmazások használhatják a **kapcsolatot az SqlBulkCopy** osztály végrehajtásához tömeges beszúrási műveletek. **Kapcsolatot az SqlBulkCopy** a parancssori eszköz, a függvény hasonló **Bcp.exe**, vagy a Transact-SQL utasítás **TÖMEGES Beszúrás**. Az alábbi példakód bemutatja, hogyan tömegesen másolni a sorokat a forrás **DataTable**, tábla-, az SQL Server, a céltábla MyTable.

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

Vannak bizonyos esetekben, amikor a tömeges másolási előnyben részesített tábla értékű paraméterek keresztül. Lásd a táblázat értékű paraméterek a cikk a TÖMEGES Beszúrás műveletek és-összehasonlító táblázatot [Table-Valued paraméterek](https://msdn.microsoft.com/library/bb510489.aspx).

A következő alkalmi vizsgálati eredmények megjelenítése a kötegelés teljesítményét **kapcsolatot az SqlBulkCopy** ezredmásodpercben.

| Műveletek | A helyszíni az Azure-ba (ms) | Az Azure ugyanabban az adatközpontban (ms) |
| --- | --- | --- |
| 1 |433 |57 |
| 10 |441 |32 |
| 100 |636 |53 |
| 1000 |2535 |341 |
| 10000 |21605 |2737 |

> [!NOTE]
> Eredmények nem referenciaalapokhoz képest történő állnak. Tekintse meg a [időzítési eredmények ebben a cikkben kapcsolatos megjegyzés](#note-about-timing-results-in-this-article).
> 
> 

A batch kisebb méretű, tábla értékű paraméterek használatát outperformed a **kapcsolatot az SqlBulkCopy** osztály. Azonban **kapcsolatot az SqlBulkCopy** hajtottak végre 12-31 %-kal gyorsabb, mint a tábla értékű paraméterek a tesztek 1000 és 10 000 sort. Tábla értékű paraméterek, például **kapcsolatot az SqlBulkCopy** beállítás hasznos a kötegelt Beszúrás, különösen akkor, ha nem kötegelt műveletek teljesítményének képest.

A tömeges másolás az ADO.NET további információkért lásd: [az SQL Server tömeges másolási műveletek](https://msdn.microsoft.com/library/7ek5da1a.aspx).

### <a name="multiple-row-parameterized-insert-statements"></a>Több-sor BESZÚRÁSA paraméteres utasításokat

Kis kötegei esetében egy alternatív, hogy hozhat létre egy nagy paraméteres INSERT utasítás, amely több sort szúr be. Az alábbi példakód bemutatja ezt a módszert.

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

Ebben a példában az adott megjelenítése alapvető fogalma. Ha valószerűbb forgatókönyvet szeretne egyszerre létrehozni a lekérdezési karakterláncot, és a parancs paraméterei a szükséges entitások lenne hurkot. Ön 2100 lekérdezési paramétereket, összesen legfeljebb ez korlátozza az ilyen módon feldolgozható sorok száma.

A következő alkalmi vizsgálati eredmények megjelenítése a teljesítmény, az ilyen típusú insert utasítás ezredmásodpercben.

| Műveletek | Tábla értékű paraméter (ms) | Utasításból INSERT (ms) |
| --- | --- | --- |
| 1 |32 |20 |
| 10 |30 |25 |
| 100 |33 |51 |

> [!NOTE]
> Eredmények nem referenciaalapokhoz képest történő állnak. Tekintse meg a [időzítési eredmények ebben a cikkben kapcsolatos megjegyzés](#note-about-timing-results-in-this-article).
> 
> 

Ez a megközelítés, amelyek 100-nál kevesebb sort kötegek némileg gyorsabb lehet. Bár a javítása kis méretű, ez a módszer egy másik lehetőség, amely előfordulhat, hogy kiválóan működjenek az adott forgatókönyvnek.

### <a name="dataadapter"></a>DataAdapter

A **DataAdapter** osztály lehetővé teszi, hogy módosítsa egy **adatkészlet** objektumra, és ezután küldje el a módosításokat, mint a beszúrási, frissítési és törlési műveleteket. Ha használja a **DataAdapter** ezen a módon kikapcsolja, a fontos megjegyezni, hogy külön hívások végrehajtott egyes különböző műveletek. A teljesítmény javítása érdekében használja a **UpdateBatchSize** tulajdonságot, amely egyszerre kell kötegelni műveletek száma. További információkért lásd: [végrehajtása kötegelt műveletek használatával DataAdapters](https://msdn.microsoft.com/library/aadf8fk2.aspx).

### <a name="entity-framework"></a>Entitás-keretrendszer

Entity Framework jelenleg nem támogatja kötegelés. Különböző, a közösségi fejlesztők próbált megkerülő megoldások, például felülbírálás bemutatásához a **létrehozva** metódust. De a megoldások jellemzően összetett és testre szabható, az alkalmazás és az adatmodellben. Az Entity Framework codeplex-projekt jelenleg rendelkezik egy hozzászólás oldalt a szolgáltatással kapcsolatos kéréseit. A hozzászólás megtekintése: [tervezési értekezleti feljegyzések - 2012. augusztus 2](https://entityframework.codeplex.com/wikipage?title=Design%20Meeting%20Notes%20-%20August%202%2c%202012).

### <a name="xml"></a>XML

A teljesség kedvéért gondoljuk, hogy fontos kötegelés stratégiánk XML beszélni. Az XML használatának viszont nincs keresztül más módszerekkel és több hátrányait rendelkezik. A megközelítés tábla értékű paraméterek hasonló, de egy XML-fájl vagy karakterlánc átadott egy felhasználó által definiált táblázat helyett egy tárolt eljárást. A tárolt eljárás elemzi a parancsok a tárolt eljárásban.

Van ennek a megoldásnak számos hátrányait:

* Az XML működő nehézkes lehet, és hibalehetőségeket rejt magában.
* Az adatbázis az XML-elemzés, CPU-igényes lehet.
* A legtöbb esetben ez a módszer lassabb, mint a tábla értékű paraméterek.

Ebből kifolyólag a XML kötegelt lekérdezések használata nem ajánlott.

## <a name="batching-considerations"></a>Kötegelés szempontok

A következő szakaszok további útmutatást az SQL Database szolgáltatást használó alkalmazások kötegelés használatát.

### <a name="tradeoffs"></a>Kompromisszumot kínál a

Attól függően, az architektúra kötegelés is magában foglalhat egy teljesítmény és rugalmasság közötti egyensúlyt. Vegyük példaként a forgatókönyvet, ahol a szerepkör váratlanul leáll. Ha elveszíti egy sornyi adatot, a hatás kisebb, mint egy nagy méretű batch el nem küldött sorok elvesztése hatása. Nagyobb veszélynek van, amikor az adatbázist egy adott időszak küldés előtt a sorok puffer.

Miatt a kompromisszummal jár értékelje ki operations típusát, akkor a batch. Batch-agresszívabb (nagyobb kötegek és hosszabb idő-windows) kevésbé fontos adatokkal.

### <a name="batch-size"></a>Köteg mérete

A tesztek a van nem általában nincs előnye az, hogy használhatatlanná tévő nagy kötegeket szeletekre. Sőt gyakran ez felosztása lassabban, mint egy egyetlen nagy kötegelt eredményezett. Például vegyünk egy forgatókönyvet, ahol szeretné 1000 sor beszúrásához. Az alábbi táblázat bemutatja, hogy mennyi ideig tart amikor kisebb kötegekben osztható 1000 sor beszúrása a tábla értékű paraméter segítségével.

| Köteg mérete | Az ismétlések | Tábla értékű paraméter (ms) |
| --- | --- | --- |
| 1000 |1 |347 |
| 500 |2 |355 |
| 100 |10 |465 |
| 50 |20 |630 |

> [!NOTE]
> Eredmények nem referenciaalapokhoz képest történő állnak. Tekintse meg a [időzítési eredmények ebben a cikkben kapcsolatos megjegyzés](#note-about-timing-results-in-this-article).
> 
> 

Láthatja, hogy-e a legjobb teljesítmény elérése érdekében 1000 sor elküldeni őket egyszerre. Más teszteket (itt nem látható), a kis jobb a teljesítménye 10000 sor kötegelt felosztása két kötegek 5000-es történt. Azonban ezekben a tesztekben a következő tábla sémáját viszonylag egyszerű, így végre kell hajtania az adott adatok és a köteg méretek ezen eredmények ellenőrzése a teszteket.

Egy másik szempont az, hogy, hogy az összes köteg túl nagy lesz, ha az SQL Database előfordulhat, hogy sávszélesség-szabályozási és elutasítja a batch véglegesítéséhez. A legjobb eredmények meghatározni, hogy van-e egy ideális köteg mérete az adott forgatókönyv teszteléséhez. Győződjön meg arról, a kötegméret konfigurálható teljesítmény vagy a hibák gyors beállításainak engedélyezése futásidőben.

Végül egyenleg a Köteg mérete a kötegelés kockázatokat. Ha átmeneti hibák, vagy a szerepkör nem sikerül, fontolja meg a következmények megismételni a műveletet, illetve a batch szolgáltatásban az adatok elvesztése.

### <a name="parallel-processing"></a>Párhuzamos feldolgozás

Mi történik, ha tartott a megközelítés a köteg méretének csökkentését, de a munka végrehajtásához több szálon használt? Újra a tesztek kimutatta, hogy több kisebb többszálú kötegek tárgyalja rosszabb, mint a nagyobb kötegek. A következő vizsgálat próbál 1000 sor beszúrása egy vagy több párhuzamos kötegekben. Ez a teszt bemutatja, hogyan több egyidejű kötegek ténylegesen csökkent teljesítményt.

| [Az ismétlések] kötegmérete | Két szállal (ms) | Négy szál (ms) | Hat szálak (ms) |
| --- | --- | --- | --- |
| 1000 [1] |277 |315 |266 |
| 500 [2] |548 |278 |256 |
| 250 [4] |405 |329 |265 |
| 100 [10] |488 |439 |391 |

> [!NOTE]
> Eredmények nem referenciaalapokhoz képest történő állnak. Tekintse meg a [időzítési eredmények ebben a cikkben kapcsolatos megjegyzés](#note-about-timing-results-in-this-article).
> 
> 

A párhuzamosság miatt a teljesítmény romlása több lehetséges oka is van:

* Nincsenek több egyidejű hálózati hívások helyett.
* Egyetlen tábla több műveleteket a versengés és blokkolja a eredményezhet.
* Nincsenek társított terhek többszálas folyamatokhoz.
* Több kapcsolat megnyitása járó költségek megvalósításának párhuzamos feldolgozási előnyeit.

Ha különböző tábla vagy adatbázisok céljaként, megállapíthatja, hogy bizonyos teljesítményt, ezzel a stratégiával szerezhet. Adatbázis horizontális skálázást vagy az összevonási lenne egy forgatókönyv esetében ez a megközelítés. A horizontális skálázás több adatbázist használ, és továbbítja a különböző adatokat minden egyes adatbázishoz. Ha egy másik adatbázishoz egyes kisebb kötegekben, hatékonyabb lehet majd hajt végre a műveleteket párhuzamosan. Teljesítmény viszont nem elég jelentős ahhoz a döntés alapjául használandó adatbázis horizontális skálázási a megoldásban használható.

Az egyes műveletekhez kisebb kötegekben párhuzamos végrehajtása kérések nagyobb átviteli sebességet eredményez a terhelés alatt a rendszer. Ebben az esetben akkor is, ha egyetlen nagyobb kötegelt feldolgozásához gyorsabb, párhuzamosan több köteg feldolgozása valószínűleg hatékonyabb.

Ha párhuzamos futtatáshoz, érdemes szabályozása a munkaszálak maximális számát. Kevesebb kevesebb a versengés és a egy gyorsabb végrehajtási időt eredményezhet. Emellett érdemes lehet a terheléseket, amelyeket ez helyez el a céladatbázist, kapcsolatok és a tranzakciók is.

### <a name="related-performance-factors"></a>Kapcsolódó teljesítmény tényezők

Adatbázis teljesítményének jellemző útmutatást is érinti a kötegelés. Például be nagy elsődleges kulcs, vagy számos nem fürtözött indexekkel rendelkező táblák esetén csökken a teljesítmény.

Ha a tábla értékű paraméterek egy tárolt eljárást használja, a paranccsal **SET NOCOUNT ON** az eljárás elején. A jelen nyilatkozat elrejti az eljárásban az érintett sorok száma, a visszatérési. Azonban a tesztek használatát a **SET NOCOUNT ON** csökkent teljesítményt vagy nem érintette. A vizsgálati tárolt eljárás egyetlen egyszerű volt **BESZÚRÁSA** parancsot, és a tábla értékű paraméter. Akkor lehet, hogy a jelen nyilatkozatban összetettebb tárolt eljárások előnyeit. De nem érdemes feltételezni, hogy hozzáadása **SET NOCOUNT ON** a tárolt eljárás automatikusan javítja a teljesítményt. Hatásának megértéséhez, a tárolt eljárást, és anélkül tesztelje a **SET NOCOUNT ON** utasítást.

## <a name="batching-scenarios"></a>Kötegelés forgatókönyvek

A következő szakaszok ismertetik a tábla értékű paraméterek használata az három alkalmazási esetekben. Az első forgatókönyv bemutatja, hogyan pufferelés és kötegelés hogyan tudnak együttműködni. A második forgatókönyvben javítja a teljesítményt, egy tárolt eljáráshívási a fő-részletek műveleteket végez. Végső forgatókönyv bemutatja, hogyan használható a tábla értékű paraméter "UPSERT" művelet.

### <a name="buffering"></a>Pufferelés

Bár vannak bizonyos forgatókönyvek, amelyek kézenfekvő jelöltek a kötegelés, nincsenek sikerült előnyeit úgy késleltetett kötegelés számos forgatókönyv. Késleltetett feldolgozási is, hogy az adatok váratlan meghibásodás elvész nagyobb veszélynek végzi. Fontos a kockázat és megfontolnia a következményekkel.

Vegyük példaként egy webalkalmazást, amely nyomon követi az egyes felhasználók navigációs előzményeit. Az egyes az alkalmazás sikerült hívja fel a felhasználói lap megtekintése egy adatbázis. De a nagyobb teljesítmény és méretezhetőség biztosíthatja a felhasználók navigációs tevékenységek pufferelés, és elküldi ezeket az adatokat az adatbázisba, és kötegekben. A database-frissítés során eltelt idő és/vagy puffer mérete alapján is indíthat. Egy szabály például megadhatja, hogy a batch 20 másodperc, vagy amikor a puffer eléri-e 1000 elemek után kell feldolgozni.

Az alábbi példakód [Reactive Extensions - Rx](https://msdn.microsoft.com/data/gg577609) figyelési osztály által kiváltott pufferelt események feldolgozásához. Amikor beírja a puffer, vagy időtúllépését, a felhasználói adatok kötegelt érkezik egy tábla értékű paraméter az adatbázisba.

A következő NavHistoryData osztály modellek a felhasználói navigációs adatai. Például a felhasználói azonosító, az elért URL-cím és a hozzáférés ideje alapvető információkat tartalmaz.

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

A NavHistoryDataMonitor osztály felelős az adatbázis felhasználói navigációs adatainak pufferelése. Egy módszer, RecordUserNavigationEntry, amely különböző tudásbázisokból megjelenítve jelzi a problémákat tartalmaz egy **OnAdded** esemény. A következő kód bemutatja a konstruktor logika, amely Rx használ egy esemény alapján megfigyelhető gyűjtemény létrehozásához. Majd feliratkozik a megfigyelhető gyűjtemény a puffer módszerrel oszthatók ki. A túlterhelés Megadja, hogy a puffer kell küldeni, 20 másodpercenként vagy 1000 bejegyzések.

```csharp
public NavHistoryDataMonitor()
{
    var observableData =
        Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

    observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
}
```

A kezelő a pufferelt elemek mindegyikét alakítja át a tábla értékű típusa, és továbbítja majd ilyen egy tárolt eljárást, amely feldolgozza a batch. A következő kód bemutatja a NavHistoryDataEventArgs, mind a NavHistoryDataMonitor osztályok teljes definíciója.

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

A kezelő a pufferelt elemek mindegyikét alakítja át a tábla értékű típusa, és továbbítja majd ilyen egy tárolt eljárást, amely feldolgozza a batch. A következő kód bemutatja a NavHistoryDataEventArgs, mind a NavHistoryDataMonitor osztályok teljes definíciója.

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

Ez az osztály pufferelési használatához az alkalmazás egy statikus NavHistoryDataMonitor-objektumot hoz létre. Minden alkalommal, amikor egy felhasználó hozzáfér egy oldal, az alkalmazás meghívja a NavHistoryDataMonitor.RecordUserNavigationEntry metódust. Ezek a bejegyzések küldését az adatbázist, és kötegekben gondoskodik abból pufferelési logikát.

### <a name="master-detail"></a>Fő részletek

Tábla értékű paraméterek egyszerű INSERT forgatókönyvek hasznosak. Azonban lehet kötegelt Beszúrás egynél több tábla érintő nehéz lehet. A "kapcsolatú" forgatókönyv egy jó példa. A fő táblázat azonosítja az elsődleges entitásnál. Egy vagy több részlet táblát entitás több adatot tároljon. Ebben a forgatókönyvben a külső kulcsok kapcsolatai kényszerítése a kapcsolat adatainak egy egyedi fölérendelt entitásba. Fontolja meg egy PurchaseOrder és a kapcsolódó OrderDetail tábla egyszerűsített változata. A következő Transact-SQL négy oszlopot hoz létre a PurchaseOrder tábla: OrderID, OrderDate, CustomerID és állapotát.

```sql
CREATE TABLE [dbo].[PurchaseOrder](
[OrderID] [int] IDENTITY(1,1) NOT NULL,
[OrderDate] [datetime] NOT NULL,
[CustomerID] [int] NOT NULL,
[Status] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_PurchaseOrder] 
PRIMARY KEY CLUSTERED ( [OrderID] ASC ))
```

Minden egyes ahhoz egy vagy több termék vásárlások tartalmazza. Ez az információ a PurchaseOrderDetail tábla van rögzítve. A következő Transact-SQL a PurchaseOrderDetail táblát hoz létre öt oszlopok: OrderID, OrderDetailID, ProductID, UnitPrice és OrderQty.

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

Az OrderID oszlop a PurchaseOrderDetail tábla egy megrendelés kell hivatkoznia a PurchaseOrder táblából. A következő idegen kulcs-definíciót kikényszeríti ezt a korlátozást.

```sql
ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD 
CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
REFERENCES [dbo].[PurchaseOrder] ([OrderID])
```

Tábla értékű paraméterek használatához rendelkeznie kell egy felhasználó által definiált táblatípus egyes céloldali táblához.

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

Megadhatja, egy tárolt eljárást, amely az ilyen jellegű táblákat fogad el. Ez az eljárás lehetővé teszi, hogy az alkalmazás helyileg batch-rendeléseket és a egy hívással rendelés részleteit. A következő Transact-SQL beszerzési rendelés Példánk esetében a teljes tárolt eljárás nyilatkozat biztosít.

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

Ebben a példában a helyileg definiált @IdentityLink tábla tárolja az újonnan behelyezett sorok tényleges OrderID értékeit. Ezeket az azonosítókat sorrend nem ideiglenes OrderID értékei azonosak a @orders és @details táblázat értékű paramétereket. Ebből kifolyólag a @IdentityLink tábla csatlakoztatja az OrderID értékeket a @orders paraméter az a PurchaseOrder táblázatban új sorok valós OrderID értékek. Elvégezte a lépést a @IdentityLink tábla megkönnyítheti a Beszúrás, a tényleges OrderID, amely eleget tesz a Külsőkulcs-korlátozást a rendelés részleteit.

Ez a tárolt eljárás használható a kódot, vagy más Transact-SQL-hívások. Ez a tanulmány a kód például a tábla értékű paraméterek című szakaszában talál. A következő Transact-SQL bemutatja, hogyan hívhat meg a sp_InsertOrdersBatch.

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

Ez a megoldás lehetővé teszi, hogy az egyes kötegek OrderID értékek 1-gyel kezdődő használatára. Ezek az értékek ideiglenes OrderID ismertetik a kapcsolatokat, a batch szolgáltatásban, de a tényleges OrderID értékek határozzák meg a beillesztési művelet idején. Futtassa az előző példában ismételten a azonos utasításokat, és egyedi rendelések létrehozása az adatbázisban. Emiatt fontolja meg, további kódot vagy az adatbázis logika, amely megakadályozza a duplikált rendelések használatakor ez technika kötegelés hozzáadását.

Ez a példa bemutatja, hogy még inkább összetett adatbázis-műveletek, például a részletezés master operations, is lehet kötegelni tábla értékű paraméterek használatával.

### <a name="upsert"></a>UPSERT

Egy másik kötegelés forgatókönyv magában foglalja a egyidejűleg frissíti a létező sorok és új sor beszúrása. Ez a művelet van más néven "UPSERT" (frissítés + Beszúrás) művelet. Ahelyett, hogy külön hívások BESZÚRÁSA és frissítése, a MERGE utasítás ez a feladat a legmegfelelőbb. A MERGE utasítás végrehajtása mindkét insert és a frissítési műveletek egyetlen hívással.

Tábla értékű paraméterek a frissítések és beilleszti a MERGE utasítás használható. Vegyük példaként egy egyszerűsített alkalmazott tábla, amely a következő oszlopokat tartalmazza: EmployeeID, az Utónév, Vezetéknév, SocialSecurityNumber:

```sql
CREATE TABLE [dbo].[Employee](
[EmployeeID] [int] IDENTITY(1,1) NOT NULL,
[FirstName] [nvarchar](50) NOT NULL,
[LastName] [nvarchar](50) NOT NULL,
[SocialSecurityNumber] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED 
([EmployeeID] ASC ))
```

Ebben a példában használhatja arra, hogy a SocialSecurityNumber egyedi több alkalmazottak az egyesítési művelet végrehajtásával. Először hozza létre a felhasználó által definiált táblatípus:

```sql
CREATE TYPE EmployeeTableType AS TABLE 
( Employee_ID INT,
    FirstName NVARCHAR(50),
    LastName NVARCHAR(50),
    SocialSecurityNumber NVARCHAR(50) );
GO
```

Ezután hozzon létre egy tárolt eljárást, vagy hajtsa végre a frissítést, majd szúrja be a MERGE utasítás használatával kód írására. Az alábbi példában a MERGE utasítás egy tábla értékű paraméter @employees, EmployeeTableType típusú. A tartalmát a @employees tábla nem jelennek meg itt.

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

További információkért lásd: a dokumentáció és a példák a MERGE utasítás. Bár ugyanazzal a munkahelyi sikerült végezhető el egy több lépésből álló tárolt eljáráshívási az INSERT külön, és frissítési műveleteket, a MERGE utasítás hatékonyabb. Adatbázis-kódot is hozhatnak létre, amelyek a MERGE utasítás közvetlenül anélkül, hogy a két adatbázis-hívások a INSERT és UPDATE Transact-SQL-hívásokat.

## <a name="recommendation-summary"></a>Az ajánlás összegzése

Az alábbi lista a kötegelés javaslatok ebben a cikkben tárgyalt összegzését tartalmazza:

* Pufferelés és kötegelés használatával növelheti a teljesítmény- és SQL Database az alkalmazások méretezhetősége.
* Ismerje meg, a kötegelés/pufferelés és rugalmasság közötti kompromisszumot. Szerepkör hiba esetén az üzleti szempontból kritikus fontosságú adatok olyan feldolgozatlan batch fennállna a kockázat, ellensúlyozhatja a kötegelés teljesítmény előnyeit.
* Megkísérlik kívül tartani a késés csökkentése érdekében egyetlen adatközponton belül az adatbázis összes híváshoz.
* Ha úgy dönt, hogy egyetlen kötegelés technika, a tábla értékű paraméterek a legjobb teljesítményt és rugalmasságot kínálnak.
* A leggyorsabb insert teljesítmény érdekében kövesse az alábbi általános irányelveket, de a forgatókönyv teszteléséhez:
  * < 100 sor egyetlen paraméteres INSERT parancs használható.
  * < 1000 sor táblázat értékű paramétereket használja.
  * A > = 1000 sort, használja a kapcsolatot az SqlBulkCopy.
* Az update és törlési műveletek, tábla értékű paraméterek használata tárolt eljárás logika, amely meghatározza, hogy a megfelelő műveletet az egyes sorokkal. a tábla paraméterben.
* Köteg mérete irányelveket:
  * Használja a legnagyobb köteg méretek, amelyek az alkalmazás és az üzleti követelmények jelentéssel bírnak.
  * Az ideiglenes vagy a katasztrofális hibák kockázatát nagy kötegeket teljesítmény miatt kiegyenlítése. Mi az az újrapróbálkozások következményei és az adatvesztés a batch szolgáltatásban? 
  * Tesztelje a legnagyobb kötegméret, győződjön meg arról, hogy az SQL Database nem utasít el azt.
  * A vezérlő kötegelés, például a Köteg mérete vagy a pufferelési időablak létrehozása konfigurációs beállítások. Ezek a beállítások rugalmasságot biztosítanak. Módosíthatja a kötegelés viselkedés éles környezetben a felhőalapú szolgáltatás újbóli telepítése nélkül.
* Ne használjon, amely egy adatbázisban lévő egyetlen táblából a kötegek állapotkezelését párhuzamos végrehajtás. Ha az egy kötegben osztja szét több munkavégző szál, tesztek futtatása annak ideális szálak számának meghatározásához. Után egy nem meghatározott küszöbértéket több szál fog rontja a teljesítményt, nem pedig növeli.
* Vegye figyelembe, hogy a mérete és az egyik módja a kötegelés további forgatókönyvek végrehajtási idő pufferelés.

## <a name="next-steps"></a>További lépések

Ez a cikk összpontosított hogyan adatbázis-tervezésben és a kódolási kötegelés kapcsolatos technikák javíthatja az alkalmazás teljesítményét és méretezhetőségét. Ez azonban csak egy tényező az általános stratégia. További részleteket a jobb teljesítmény és méretezhetőség, lásd: [útmutató az önálló adatbázisok teljesítményének növeléséhez Azure SQL Database](sql-database-performance-guidance.md) és [rugalmas készletek ára és teljesítménye szempontok](sql-database-elastic-pool-guidance.md).

