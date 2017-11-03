---
title: "Az Azure SQL Database-alkalmazás teljesítményének javítása érdekében a kötegelés használata"
description: "A témakör igazolja, hogy kötegelési adatbázis-műveletek sebessége nagy mértékben imroves és méretezhetőséget biztosít a az Azure SQL adatbázis-alkalmazások. Habár ezek a technológiák kötegelési bármely SQL Server-adatbázis is működik, a cikk célja az Azure-on."
services: sql-database
documentationcenter: na
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 563862ca-c65a-46f6-975d-10df7ff6aa9c
ms.service: sql-database
ms.custom: develop apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 07/12/2016
ms.author: sstein
ms.openlocfilehash: 8622bddc809c9d95f7acf359ff708d5ab31cf620
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="how-to-use-batching-to-improve-sql-database-application-performance"></a>SQL-adatbázis teljesítményének javítása érdekében a kötegelés használata
Az Azure SQL Database-műveletek kötegelése jelentősen javítja a teljesítményét és méretezhetőségét, az alkalmazások. Előnyeinek megismerése, hogy ez a cikk első része ismertet néhány minta vizsgálati eredmények, hasonlítsa össze az SQL-adatbázis szekvenciális és kötegelt kérelmek. A cikk fennmaradó a technikák, a forgatókönyvek és a szempontokat tartalmaz, amelyek segítséget nyújtanak az Azure-alkalmazásokban sikeresen kötegelés használandó jeleníti meg.

## <a name="why-is-batching-important-for-sql-database"></a>Miért van kötegelés fontos az SQL Database?
A távoli szolgáltatás hívásainak kötegelés növelését a teljesítmény és méretezhetőség jól ismert stratégiáját. Egy távoli szolgáltatással, például a szerializálás, a hálózati átvitel és a deszerializálás kölcsönhatások feldolgozási költségek rögzítettek. Ezek a költségek azokat a kötegek sok külön tranzakciókat minimálisra csökkenti.

A dokumentum azt szeretnénk vizsgálja meg a különböző SQL-adatbázis kötegelés azokat a stratégiákat és forgatókönyvek. Bár ezek stratégiák is fontos SQL Server használó helyszíni alkalmazások esetén, több oka konzolban kötegelés SQL-adatbázis használatát:

* Nincs potenciálisan nagyobb hálózati késés SQL-adatbázis, különösen akkor, ha az ugyanahhoz a Microsoft Azure adatközponton kívülről SQL-adatbázis elérésére.
* SQL-adatbázis több-bérlős jellemzői azt jelenti, hogy az adatok hozzáférési réteg felel meg az adatbázis átfogó méretezhetősége hatékonyságát. SQL-adatbázis az egyes bérlői felhasználók megakadályozása kell legaktívabbak más bérlők hátrányára adatbázis-erőforrások. SQL-adatbázis használati, amelyek átlépik ezt az előre definiált kvóták válaszul, átviteli csökkentheti vagy szabályozási kivételeket válaszolni. Hatékonyság, például a kötegelés, lehetővé teszik a munkájuk elvégzéséhez további SQL-adatbázis a működés felső korlátjának elérése előtt. 
* Kötegelés esetében is, amelyek több adatbázist (horizontális) architektúrára való hatékony. Az adatbázis tárolóegységekhez folytatott kommunikációt hatékonyságát még mindig a teljes méretezhetőség kulcsfontosságú tényező. 

Az SQL-adatbázis használatának előnyei egyike, hogy nem kell az adatbázist üzemeltető kiszolgáló kezeléséhez. Azonban a felügyelt infrastruktúra is azt jelenti, hogy másképp gondolniuk adatbázis optimalizálás. Már nem megtekintheti az adatbázis hardver- vagy hálózati infrastruktúra javítása érdekében. A Microsoft Azure határozza meg azokat a környezetben. A fő területet, amely befolyásolhatja az SQL-adatbázis és az alkalmazás együttműködését. Kötegelés egyike ezek az optimalizálások. 

A dokumentum első része SQL-adatbázis használata a .NET-alkalmazások különböző kötegelési technikák megvizsgálja. Az utolsó két szakaszok fedik le a kötegelési irányelvek és forgatókönyvek.

## <a name="batching-strategies"></a>Kötegelési stratégiák
### <a name="note-about-timing-results-in-this-topic"></a>Megjegyzés: Ebben a témakörben időzítési eredmény
> [!NOTE]
> Eredmények nem referenciaalapokhoz képest, de van kialakítva, hogy megjelenítése **relatív teljesítménye**. Időzítés legalább 10 teszt futtatása átlagosan alapulnak. Műveletek esetében a beszúrások, a program üres táblát. Ezek a tesztek mért előtti-12-es verzióra, és ezek nem feltétlenül felelnek meg, hogy Ön is szembesülhet egy 12-es verziójú adatbázis, az új átviteli [szolgáltatásszintek](sql-database-service-tiers.md). A relatív előnye, hogy a kötegelési technika hasonlónak kell lenniük.
> 
> 

### <a name="transactions"></a>Tranzakciók
Furcsa megvitatása tranzakciók által kötegelés áttekintése megkezdéséhez tűnik. De a tranzakciók ügyféloldali használata finom kiszolgálóoldali kötegelési hatással van, amely javítja a teljesítményt. És a tranzakciók hozzáadása is lehetséges csak néhány sornyi kódot, így gyorsan egymást követő műveletek teljesítményének javításával biztosítanak.

Vegye figyelembe a következő C#-kódban insert sorozatát tartalmazó és a frissítési műveletek egyszerű táblán.

    List<string> dbOperations = new List<string>();
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
    dbOperations.Add("insert MyTable values ('new value',1)");
    dbOperations.Add("insert MyTable values ('new value',2)");
    dbOperations.Add("insert MyTable values ('new value',3)");

A következő ADO.NET kód egymás után végrehajtja ezeket a műveleteket.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        conn.Open();

        foreach(string commandString in dbOperations)
        {
            SqlCommand cmd = new SqlCommand(commandString, conn);
            cmd.ExecuteNonQuery();                   
        }
    }

A legjobb módja, ez a kód optimalizálása érdekében, hogy az adott hívások kötegelés ügyféloldali valamilyen alkalmazza. Azonban ez a kód a teljesítmény növelése érdekében használatával egyszerűen a hívások sorrendjét a tranzakcióban egyszerű módszert. Itt található, amely egy tranzakció használja ugyanazt a kódot.

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

Tranzakciók mindkét ezekben a példákban ténylegesen használatban van. Az első példában minden egyes tekintendő, amely az implicit tranzakciókban. A második példában az explicit tranzakciók becsomagolja a hívások mindegyikét. / Dokumentációját a [írási előre tranzakciónapló](https://msdn.microsoft.com/library/ms186259.aspx), naplórekordokat kiürített a lemezre, ha a tranzakció véglegesítése. Így több hívást együtt egy tranzakcióban, az írás a tranzakciós napló tudja elhalasztani, amíg a tranzakció. Érvényben engedélyezi az írási műveleteket ad ki a kiszolgáló tranzakciónapló a kötegelés.

Az alábbi táblázat néhány alkalmi vizsgálati eredményeket jeleníti meg. A következő tesztek kerülnek végrehajtásra, és anélkül tranzakciók azonos szekvenciális beszúrása. Több szempont az első készletét tesztek futott távolról a hordozható az adatbázis a Microsoft Azure-ban. A második készlet tesztet hajt végre egy felhőalapú szolgáltatás, hogy mindkét tartózkodott belül az azonos Microsoft Azure datacenter (USA nyugati régiója) adatbázis futott. A következő táblázat az időtartam a szekvenciális Beszúrások rendelkező és anélküli tranzakciók ezredmásodpercben.

**Az Azure-bA helyszíni**:

| Műveletek | Nincs tranzakció (ms) | Tranzakció (ms) |
| --- | --- | --- |
| 1 |130 |402 |
| 10 |1208 |1226 |
| 100 |12662 |10395 |
| 1000 |128852 |102917 |

**Azure-az Azure-ba (ugyanabban az adatközpontban)**:

| Műveletek | Nincs tranzakció (ms) | Tranzakció (ms) |
| --- | --- | --- |
| 1 |21 |26 |
| 10 |220 |56 |
| 100 |2145 |341 |
| 1000 |21479 |2756 |

> [!NOTE]
> A eredményei nem referenciaalapokhoz képest. Tekintse meg a [időzítési eredményezi, hogy ez a témakör Megjegyzés](#note-about-timing-results-in-this-topic).
> 
> 

Az előző teszt eredményei alapján teljesítmény ténylegesen csökkenti alkalmazásburkoló egyetlen műveletben szerepel egy tranzakcióban. De növelésével egy tranzakción belül műveletek számát, a teljesítmény fokozása több lesz megjelölve. A teljesítménybeli különbség az akkor is jobban észlelhető, ha minden műveletnél fordulhat elő, a Microsoft Azure adatközponton belül. Az SQL-adatbázisát használja a Microsoft Azure adatközponton kívülről nagyobb késéseket overshadows tranzakciók használatával jobb a teljesítménye.

Bár a tranzakciók használata növelheti a teljesítményt, továbbra is [tekintse át az ajánlott eljárások az tranzakciók és kapcsolatok](https://msdn.microsoft.com/library/ms187484.aspx). Tartsa a lehető legrövidebb tranzakció, és a munka végeztével, zárja be az adatbázis-kapcsolatot. Az utasítás használatával az előző példában szereplő biztosítja, hogy a kapcsolat megszakad, a következő kódblokk befejezéséről.

A korábbi példa bemutatja, hogy adhat hozzá egy helyi tranzakció ADO.NET kódok esetén is tenné két sort. Tranzakciók ajánlatot gyorsan kódot, amely lehetővé teszi a szekvenciális beszúrási, frissítési és törlési műveletek teljesítményének növelésében. Azonban a leggyorsabb teljesítmény érdekében fontolja meg a kódot használja ki az ügyféloldali kötegelés, például a táblázat értékű paramétereket tovább.

Az ADO.NET tranzakciókkal kapcsolatos további információkért lásd: [ADO.NET helyi tranzakciók](https://docs.microsoft.com/dotnet/framework/data/adonet/local-transactions).

### <a name="table-valued-parameters"></a>tábla értékű paraméter
Tábla értékű paraméter paraméterekkel a Transact-SQL-utasítások, tárolt eljárások és függvények, felhasználó által definiált táblatípusokban támogatja. Ez az ügyféloldali kötegelési módszer lehetővé teszi, hogy több sornyi adatot belül a tábla értékű paraméter küldhet. Tábla értékű paraméter használatához először meg kell határoznia egy táblatípus. A következő Transact-SQL-utasítást hoz létre nevű tábla típus **MyTableType**.

    CREATE TYPE MyTableType AS TABLE 
    ( mytext TEXT,
      num INT );


A kódban, hozzon létre egy **DataTable** pontos azonos nevét és a táblatípus típusú. Ez átadni **DataTable** egy paraméterben, szöveges lekérdezés vagy tárolt eljárás hívása. A következő példa bemutatja, ezzel a módszerrel:

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

Az előző példában a **SqlCommand** objektum egy tábla értékű paraméter a sor beszúrása  **@TestTvp** . A korábban létrehozott **DataTable** objektum ezt a paramétert hozzá van rendelve a **SqlCommand.Parameters.Add** metódust. A teljesítmény egy hívásban Beszúrások kötegelés jelentősen növeli a szekvenciális Beszúrások keresztül.

Az előző példa folytatásaként javításához használja a tárolt eljárás egy szöveges parancs helyett. A következő Transact-SQL-parancs létrehoz egy tárolt eljárás, amely a **SimpleTestTableType** tábla értékű paraméter.

    CREATE PROCEDURE [dbo].[sp_InsertRows] 
    @TestTvp as MyTableType READONLY
    AS
    BEGIN
    INSERT INTO MyTable(mytext, num) 
    SELECT mytext, num FROM @TestTvp
    END
    GO

Módosítsa a **SqlCommand** objektum az előző példakódban csatlakoztatása a következő nyilatkozatot.

    SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
    cmd.CommandType = CommandType.StoredProcedure;

A legtöbb esetben a tábla értékű paraméter rendelkezik egyenértékű vagy jobb teljesítményt biztosít, mint más kötegelési módszerek. Tábla értékű paraméterek gyakran érdemes, mivel rugalmasabb, mint más beállítások. Egyéb módszerek, például SQL tömeges másolás, például csak új sorok beszúrását lehetővé teszik. De tábla értékű paraméter segítségével programot a tárolt eljárás annak meghatározására, hogy mely sorai frissítések, és amelyek beszúrása. A táblatípus is módosíthatja a tartalmaz egy "Művelet" oszlopot, amely jelzi, hogy a megadott sor kell beszúrni, frissíteni, vagy törölve.

A következő táblázat a tábla értékű paraméterek használatával vizsgálati eredményeket ad hoc ezredmásodpercben.

| Műveletek | A helyszíni Azure-ba (ms) | Az Azure ugyanabban az adatközpontban (ms) |
| --- | --- | --- |
| 1 |124 |32 |
| 10 |131 |25 |
| 100 |338 |51 |
| 1000 |2615 |382 |
| 10000 |23830 |3586 |

> [!NOTE]
> A eredményei nem referenciaalapokhoz képest. Tekintse meg a [időzítési eredményezi, hogy ez a témakör Megjegyzés](#note-about-timing-results-in-this-topic).
> 
> 

A kötegelés jobb a teljesítménye azonnal kétségtelenül. Az előző szekvenciális teszt 1000 műveletek 129 másodperc az adatközponton kívülről és az adatközponton belül a 21 másodpercet vett igénybe. De tábla értékű paraméter 1000 műveletek másodpercre csak 2.6-os és az adatközponton belül idõtartamtól az adatközponton kívülről.

További információ a tábla értékű paraméter: [Table-Valued paraméterek](https://msdn.microsoft.com/library/bb510489.aspx).

### <a name="sql-bulk-copy"></a>SQL tömeges másolási
SQL tömeges másolási egy másik módja a nagy mennyiségű adat elhelyezni a céladatbázis. .NET-alkalmazások használhatják a **SqlBulkCopy** osztály végrehajtására tömeges beszúrási műveletek. **SqlBulkCopy** a parancssori eszköz, a függvény hasonló **Bcp.exe**, vagy a Transact-SQL-utasítás **TÖMEGES Beszúrás**. Az alábbi példakód bemutatja, hogyan tömegesen másolni a sorokat a forráshelyen **DataTable**, táblázatra, az SQL Server, a céltábla táblanév.

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

Néhány esetben, ha tömeges másolás előnyben részesített tábla értékű paraméter felett van. Tekintse meg a tábla értékű paraméter és a következő témakör TÖMEGES beszúrási műveletek összehasonlító táblázatot [Table-Valued paraméterek](https://msdn.microsoft.com/library/bb510489.aspx).

A következő alkalmi vizsgálati eredmények megjelenítése a kötegelés teljesítményének **SqlBulkCopy** ezredmásodpercben.

| Műveletek | A helyszíni Azure-ba (ms) | Az Azure ugyanabban az adatközpontban (ms) |
| --- | --- | --- |
| 1 |433 |57 |
| 10 |441 |32 |
| 100 |636 |53 |
| 1000 |2535 |341 |
| 10000 |21605 |2737 |

> [!NOTE]
> A eredményei nem referenciaalapokhoz képest. Tekintse meg a [időzítési eredményezi, hogy ez a témakör Megjegyzés](#note-about-timing-results-in-this-topic).
> 
> 

A Köteg mérete kisebb, használja a tábla értékű paraméterek outperformed a **SqlBulkCopy** osztály. Azonban **SqlBulkCopy** gyorsabb, mint a tábla értékű paraméterek a 12-31 % elvégzi a tesztek 1000 és 10 000 sorok. Tábla értékű paraméterek, például **SqlBulkCopy** van a kötegelt Beszúrás jó választás, különösen akkor, ha nem kötegelni műveletek teljesítményének képest.

A tömeges másolás az ADO.NET további információkért lásd: [az SQL Server tömeges másolási műveletek](https://msdn.microsoft.com/library/7ek5da1a.aspx).

### <a name="multiple-row-parameterized-insert-statements"></a>Több soron kívüli Beszúrás paraméteres utasításokat
Egy kis kötegek esetben nagy paraméteres utasítást, amely több sor beszúrása összeállításához. Az alábbi példakód mutatja be, ezzel a módszerrel.

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


Ez a példa arra szolgál, hogy az alapvető fogalma megjelenítése. A modell forgatókönyv volna ismétlése a lekérdezési karakterláncot és a parancs paraméterei egyidejűleg összeállításához szükséges entitásokat. Azonban legfeljebb összesen 2100 lekérdezési paraméterek, ez korlátozza az ilyen módon feldolgozható sorok száma.

A következő alkalmi teszteredmények utasítást ilyen típusú teljesítményének megjelenítése ezredmásodpercben.

| Műveletek | Tábla értékű paraméter (ms) | Utasításból INSERT (ms) |
| --- | --- | --- |
| 1 |32 |20 |
| 10 |30 |25 |
| 100 |33 |51 |

> [!NOTE]
> A eredményei nem referenciaalapokhoz képest. Tekintse meg a [időzítési eredményezi, hogy ez a témakör Megjegyzés](#note-about-timing-results-in-this-topic).
> 
> 

Ezt a módszert használja, amelyek 100-nál kevesebb sort kötegek némileg gyorsabb lehet. Bár a javítása kis, ez a módszer akkor egy másik lehetőség, amely előfordulhat, hogy kiválóan működjenek az adott alkalmazás helyzetnek.

### <a name="dataadapter"></a>DataAdapter
A **DataAdapter** osztály lehetővé teszi, hogy módosítsa egy **DataSet** objektumot, és küldje el az INSERT, UPDATE és DELETE műveletek változik. Ha használja a **DataAdapter** ezen a módon kikapcsolja, fontos megjegyezni, hogy külön hívások legyenek-e készülve az egyes különálló műveletet. A teljesítmény javítása érdekében használja a **UpdateBatchSize** tulajdonságot, amely egyszerre kell lehet kötegelni műveletek száma. További információkért lásd: [végrehajtása kötegelt műveletek használatával DataAdapters](https://msdn.microsoft.com/library/aadf8fk2.aspx).

### <a name="entity-framework"></a>Entitás-keretrendszer
Entitás-keretrendszer jelenleg nem támogatja kötegelés. A közösségi különböző fejlesztők próbált meg lehetséges megoldások, például a felülbírálás bemutatása a **a SaveChanges metódus** metódust. De a megoldások általában összetett és testreszabott, az alkalmazás és az adatmodell. Az Entity Framework codeplex-projekt jelenleg is rendelkezik az ismertető a szolgáltatás kérésre. Az ismertető megtekintése: [tervezési értekezlet megjegyzések - 2012 augusztus 2](http://entityframework.codeplex.com/wikipage?title=Design%20Meeting%20Notes%20-%20August%202%2c%202012).

### <a name="xml"></a>XML
A teljesség kedvéért azt látja, hogy fontos, mint egy kötegelési stratégia XML kapcsolatban. Az XML-kód használatát azonban más módszerekkel nem előnyöket és számos hátránya rendelkezik. A megoldás, tábla értékű paraméter hasonló, de egy XML-fájl vagy karakterlánc objektumnak átadott helyett a felhasználó által definiált tábla tárolt eljárást. A tárolt eljárás elemzi a parancsok a tárolt eljárást.

Ezt a megközelítést több hátrányai van:

* Az XML működő nehézkes lehet, és hibalehetőségeket rejt magában hiba.
* Az adatbázis az XML-elemzés processzorigényes is lehet.
* A legtöbb esetben ez a módszer lassabb, mint a tábla értékű paraméter.

Ezen okok miatt a XML kötegelt lekérdezések használata nem ajánlott.

## <a name="batching-considerations"></a>Kötegelési kapcsolatos szempontok
Az alábbi szakaszokban további útmutatás nyújtása a kötegelés SQL-adatbázis alkalmazások használatát.

### <a name="tradeoffs"></a>Mellékhatásokkal
Attól függően, hogy az architektúrák kötegelés magába foglaló a teljesítményt és rugalmasságot közötti kompromisszumot. Vegyük példaként a forgatókönyvet, ahol a szerepkör váratlanul leáll. Ha elveszíti egy adatsornak, szempontjából kisebb, mint egy nagy sorköteg el nem küldött elvesztése hatását. Nagyobb veszélynek van Ha sorok elegendő pufferrel, mielőtt elküldi őket az adatbázishoz megadott időkeretnél.

Miatt ez kompromisszumot kiértékelheti, hogy Ön kötegelt működés. A Batch-agresszívabb (nagyobb kötegek és hosszabb idő windows) kevésbé fontos adatokkal.

### <a name="batch-size"></a>Köteg mérete
A tesztelés során történt általában nagy kötegek ossza kisebb csoportjai való nem szolgál előnyökkel. Gyakran ez felosztása, mint egy egyetlen nagy kötegelt lassabban eredményezett. Vegyük példaként egy olyan forgatókönyvet, ahol szeretné 1000 sor beszúrása. Az alábbi táblázatban látható, mennyi ideig tart a tábla értékű paraméter használatával 1000 sor, amikor kisebb kötegekben osztva.

| Köteg mérete | Az ismétlés | Tábla értékű paraméter (ms) |
| --- | --- | --- |
| 1000 |1 |347 |
| 500 |2 |355 |
| 100 |10 |465 |
| 50 |20 |630 |

> [!NOTE]
> A eredményei nem referenciaalapokhoz képest. Tekintse meg a [időzítési eredményezi, hogy ez a témakör Megjegyzés](#note-about-timing-results-in-this-topic).
> 
> 

Láthatja, hogy-e a legjobb teljesítményt 1000 sor elküldeni őket egyszerre. Más tesztekben (itt nem látható) történt egy 10000 sor kötegelt felosztása két kötegek 5000 jobb a teljesítménye kis. Azonban ezekben a tesztekben a következő tábla sémáját viszonylag egyszerű, végre kell hajtania az adatokat és a Köteg mérete ezen eredmények ellenőrzése tesztek.

Egy másik szempont az, hogy, hogy a teljes kötegelt túl nagyra nő, ha SQL-adatbázis előfordulhat, hogy sávszélesség-szabályozási és elutasítja a kötegelt véglegesítéséhez. A legjobb eredmény elérése érdekében tesztelje az adott forgatókönyv annak meghatározásához, hogy van-e az épp ezért tökéletes választás a köteg méretének. Ellenőrizze a Köteg mérete konfigurálható teljesítmény vagy hibák alapján gyors módosításának engedélyezése a futási időben.

Végezetül egyenleg a Köteg mérete a kötegelés kapcsolódó kockázatokat. Ha átmeneti hiba merül fel, vagy a szerepkör nem sikerül, fontolja meg, majd próbálja megismételni a műveletet, vagy az adatvesztés a kötegben következményeit.

### <a name="parallel-processing"></a>Párhuzamos feldolgozás
Mi történik, ha a köteg méretének csökkentését megközelítés tartott, de több szál hajthatók végre a munkahelyi? Ebben az esetben a tesztek bemutatta, hogy több kisebb többszálas kötegek általában végre a nagyobb kötegek rosszabb. A következő teszt megkísérli 1000 sor beszúrása egy vagy több párhuzamos kötegekben. Ez a vizsgálat bemutatja, hogyan több egyidejű kötegek ténylegesen csökkent teljesítményt.

| A köteg méretének [ismétlési] | Két szállal (ms) | Négy szálak (ms) | Hat szálak (ms) |
| --- | --- | --- | --- |
| 1000 [1] |277 |315 |266 |
| 500 [2] |548 |278 |256 |
| 250 [4] |405 |329 |265 |
| 100 [10] |488 |439 |391 |

> [!NOTE]
> A eredményei nem referenciaalapokhoz képest. Tekintse meg a [időzítési eredményezi, hogy ez a témakör Megjegyzés](#note-about-timing-results-in-this-topic).
> 
> 

Van több lehetséges oka a akár teljesítménycsökkenés párhuzamosság miatt:

* Nincsenek egy helyett több egyidejű hálózati hívást.
* Egyetlen tábla több műveleteket versengés és blokkolja a eredményezhet.
* Nincsenek társított terhek többszálas.
* Több kapcsolat megnyitásának járó költségek ez fontosabb, mint az az előnye, hogy a párhuzamos feldolgozást.

Különböző táblákhoz vagy adatbázisok célozhat meg, akkor megállapíthatja, hogy ezt a stratégiát, hogy néhány teljesítmény. Adatbázis horizontális vagy összevonási lenne a forgatókönyv ezt a módszert használja. Horizontális több adatbázist használ, és továbbítja a különböző adatokat az egyes adatbázisok. Ha egy kis köteg egy másik adatbázishoz, hatékonyabb lehet majd a műveletet hajt végre párhuzamosan. Jobb a teljesítménye azonban nem elég jelentős döntést alapjául használandó adatbázis horizontális használja a megoldásban.

Néhány terveibe kisebb kötegekben párhuzamos végrehajtása eredményezhet továbbfejlesztett kapacitásának terhelés alatt a rendszer. Ebben az esetben akkor is, ha gyorsabb, nagyobb a kötegek feldolgozni, párhuzamosan több kötegenként lehet hatékonyabb.

Ha párhuzamos végrehajtás, érdemes a munkaszálak maximális számának vezérlése. Kevesebb gyorsabb végrehajtási idő, és kevesebb a versengés eredményezheti. Is fontolja meg az egyéb terheléseket, amelyeket a ez helyez el a céladatbázist, kapcsolatok és a tranzakciók egyaránt.

### <a name="related-performance-factors"></a>A teljesítmény kapcsolódó tényezők
Adatbázis teljesítménye jellemző útmutatást is kötegelés hatással van. Például be nagy elsődleges kulcs, vagy sok fürtözetlen indexeire rendelkező táblák csökken a teljesítmény.

Ha a tábla értékű paraméter tárolt eljárás használatához a paranccsal **SET NOCOUNT ON** az eljárás elején. A jelen nyilatkozat mellőzi a visszatérési az eljárás az érintett sorok száma. Azonban a tesztelés során használatát **SET NOCOUNT ON** kellett nincs hatása, vagy teljesítménye csökkent. A vizsgálati tárolt eljárás egyetlen egyszerű volt **BESZÚRÁSA** parancsot a tábla értékű paraméter. Akkor lehet, hogy összetettebb tárolt eljárások a jelen nyilatkozat előnyös. Nem érdemes feltételezni, hogy hozzáadása, de **SET NOCOUNT ON** a tárolt eljárás automatikusan javítja a teljesítményt. Szeretné megtudni, a hatás, tesztelje a tárolt eljárás használatával és anélkül a **SET NOCOUNT ON** utasítást.

## <a name="batching-scenarios"></a>Kötegelése forgatókönyvek
Az alábbi szakaszok ismertetik a tábla értékű paraméter három alkalmazás helyzetekben használhatja. Az első forgatókönyv bemutatja, hogyan pufferelés és kötegelés hogyan tudnak együttműködni. A második forgatókönyv javítja a teljesítményt, egyetlen tárolt eljárás hívása a fő-részletek műveletet hajt végre. A végső forgatókönyv bemutatja, hogyan tábla értékű paraméterek használatához a "UPSERT" művelet.

### <a name="buffering"></a>Pufferelés
Habár van néhány olyan forgatókönyvet, nyilvánvaló jelölt kötegelés, ott sikerült előnyeit által késleltetett feldolgozási kötegelés több forgatókönyv áll. Késleltetett feldolgozási is, hogy az adatok nem vesztek el meghibásodása nagyobb veszélynek végzi. Fontos megérteni a kockázat, és vegye figyelembe a következményekkel.

Vegye figyelembe például egy webes alkalmazás, amely nyomon követi a minden felhasználó előzménylistáján. A lap lekérése az alkalmazás sikerült hívható meg a felhasználó lapmegtekintés rögzítésére adatbázis. De a nagyobb teljesítmény és méretezhetőség legyen elérhető a felhasználók navigációs tevékenységek pufferelés, és elküldi ezeket az adatokat az adatbázisba kötegekben. Az adatbázis frissítést a futása közben eltelt idő és/vagy puffer mérete indíthat el. Egy szabály például megadhatja, hogy a kötegelt 20 másodperc, vagy amikor a puffer eléri-e 1000 elemek után fel kell dolgozni.

Az alábbi példakód [reaktív bővítmények – a Rx](https://msdn.microsoft.com/data/gg577609) figyelési osztály által kiváltott pufferelt események feldolgozásához. Amikor beírja a puffer, vagy időkorlátot, a felhasználói adatok kötegelt zajlik egy tábla értékű paraméter az adatbázisba.

A következő NavHistoryData osztály modellek a felhasználó navigációs adatait. Például a felhasználói azonosító az elért URL-cím vagy a hozzáférés idejének alapszintű információkat tartalmaz.

    public class NavHistoryData
    {
        public NavHistoryData(int userId, string url, DateTime accessTime)
        { UserId = userId; URL = url; AccessTime = accessTime; }
        public int UserId { get; set; }
        public string URL { get; set; }
        public DateTime AccessTime { get; set; }
    }

A NavHistoryDataMonitor osztály a felhasználói navigációs adatokat az adatbázisba pufferelés felelős. Tartalmaz egy metódust, RecordUserNavigationEntry, amely válaszol-e megjelenítve jelzi egy **OnAdded** esemény. A következő kód bemutatja a konstruktor logika, amely használja a Rx hozzon létre egy megfigyelhető gyűjteményt az események alapján. Majd feliratkozva a megfigyelhető gyűjteményhez a következő puffer metódussal. A túlterhelés határozza meg, hogy a memóriapuffer 20 másodpercenként vagy 1000 bejegyzések küldjön.

    public NavHistoryDataMonitor()
    {
        var observableData =
            Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

        observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);           
    }

A kezelő összes pufferelt elem alakítja át a tábla értékű típus, és majd átadja a ehhez a típushoz, amely feldolgozza a kötegelt tárolt eljárást. A következő kód bemutatja a NavHistoryDataEventArgs, mind a NavHistoryDataMonitor osztályok teljes definíciója.

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

        public void RecordUserNavigationEntry(NavHistoryData data)
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

Ez az osztály pufferelési használatához az alkalmazás egy statikus NavHistoryDataMonitor objektumot hoz létre. Minden alkalommal, amikor egy felhasználó egy lap fér hozzá az alkalmazás meghívja a NavHistoryDataMonitor.RecordUserNavigationEntry metódust. Ezek a bejegyzések küldése az adatbázis kötegekben irányuló pufferelési logika eltérő lehet.

### <a name="master-detail"></a>Fő részletei
Tábla értékű paraméter egyszerű INSERT forgatókönyvek hasznosak. Azonban lehet, például az egynél több tábla kötegelt Beszúrás további kihívást. A "kapcsolatú" például az is jó példa. A fő táblázat azonosítja az elsődleges entitás. Egy vagy több részletek tábla entitás több adatot tároljon. Ebben a forgatókönyvben a külső kulcsok kapcsolatai kényszerítése a kapcsolat egy egyedi fő entitásra részletességi. Vegye figyelembe a PurchaseOrder és a kapcsolódó OrderDetail tábla egyszerűsített verziója. A következő Transact-SQL négy oszlopot hoz létre a PurchaseOrder tábla: OrderID, orderdate oszlopra, CustomerID és állapotát.

    CREATE TABLE [dbo].[PurchaseOrder](
    [OrderID] [int] IDENTITY(1,1) NOT NULL,
    [OrderDate] [datetime] NOT NULL,
    [CustomerID] [int] NOT NULL,
    [Status] [nvarchar](50) NOT NULL,
     CONSTRAINT [PrimaryKey_PurchaseOrder] 
    PRIMARY KEY CLUSTERED ( [OrderID] ASC ))

Minden egyes rendelés egy vagy több termék vásárlás tartalmazza. Ezt az információt a PurchaseOrderDetail tábla rögzített. A következő Transact-SQL hoz létre a PurchaseOrderDetail tábla öt oszlopok: OrderID, OrderDetailID, ProductID, Egységár és OrderQty.

    CREATE TABLE [dbo].[PurchaseOrderDetail](
    [OrderID] [int] NOT NULL,
    [OrderDetailID] [int] IDENTITY(1,1) NOT NULL,
    [ProductID] [int] NOT NULL,
    [UnitPrice] [money] NULL,
    [OrderQty] [smallint] NULL,
     CONSTRAINT [PrimaryKey_PurchaseOrderDetail] PRIMARY KEY CLUSTERED 
    ( [OrderID] ASC, [OrderDetailID] ASC ))

Az OrderID oszlop a PurchaseOrderDetail tábla sorrendben kell hivatkoznia, a PurchaseOrder táblából. A következő idegen kulcs definícióját a korlátozás érvényesítése.

    ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD 
    CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
    REFERENCES [dbo].[PurchaseOrder] ([OrderID])

Tábla értékű paraméter használatához rendelkeznie kell egy felhasználó által definiált táblatípus minden céloldali táblához.

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

Majd adja meg, amely támogatja a következő típusú táblák tárolt eljárást. Ez az eljárás lehetővé teszi az helyileg a batch-rendeléseket és egy hívás a rendelés részleteit. A következő Transact-SQL beszerzési sorrendje a példa a teljes tárolt eljárás nyilatkozat biztosít.

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

Ebben a példában a helyileg definiált @IdentityLink tábla tárolja az újonnan behelyezett sorainak tényleges OrderID értéke. Ilyen rendelés azonosítókat az ideiglenes OrderID értékek eltérnek a @orders és @details tábla értékű paraméter. Emiatt a @IdentityLink tábla csatlakoztatja az OrderID értékeit a @orders paramétert az új sort a PurchaseOrder tábla valós OrderID értékeit. Ez a lépés után a @IdentityLink tábla megkönnyítheti a rendelés részleteit és a tényleges OrderID, amely eleget tesz a Külsőkulcs-korlátozást beszúrni.

Ez a tárolt eljárás használható kód vagy más Transact-SQL-hívások. A dokumentum a kód például a táblázat értékű paramétereket című szakaszában talál. A következő Transact-SQL bemutatja, hogyan hívhatja meg a sp_InsertOrdersBatch.

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

Ez a megoldás lehetővé teszi, hogy az egyes kötegekben OrderID értékek: 1 kezdődő használandó. Ideiglenes OrderID értékeiről leírják a kötegben lévő kapcsolatok, de a tényleges OrderID értékek az insert művelet időpontjában határozza meg. Futtassa az előző példában szereplő ismételten a azonos utasításokat, és egyedi rendeléseket lehet létrehozni az adatbázist. Emiatt érdemes további kóddal vagy az adatbázis logika, amely megakadályozza a duplikált rendelések használatakor ezzel a technikával kötegelés.

Ez a példa mutatja be, hogy még inkább összetett adatbázis-műveletek, például a részletezés master operations, is lehet kötegelni tábla értékű paraméterek használatával.

### <a name="upsert"></a>UPSERT
Egy másik kötegelési forgatókönyv magában foglalja a egyidejűleg frissíteni a létező sorok és új sort beszúrni. Ez a művelet van más néven "UPSERT" (frissítés + insert) műveletet. Ahelyett, hogy külön hívások BESZÚRÁSA, és FRISSÍTI a MERGE utasítás bizonyul a legalkalmasabbnak a tevékenységhez. A MERGE utasítás végrehajtása mindkét insert és frissítési művelet egyetlen hívással.

Tábla értékű paraméter frissítések és a Beszúrás elvégzéséhez használható a MERGE utasítással. Vegyük példaként a következő oszlopokat tartalmazó egyszerűsített alkalmazott táblázat: EmployeeID, az Utónév, a Vezetéknév, a SocialSecurityNumber:

    CREATE TABLE [dbo].[Employee](
    [EmployeeID] [int] IDENTITY(1,1) NOT NULL,
    [FirstName] [nvarchar](50) NOT NULL,
    [LastName] [nvarchar](50) NOT NULL,
    [SocialSecurityNumber] [nvarchar](50) NOT NULL,
     CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED 
    ([EmployeeID] ASC ))

Ebben a példában használhatja arra, hogy a SocialSecurityNumber egyedi több alkalmazott EGYESÍTÉSÉVEL végrehajtásához. Először hozza létre a felhasználó által definiált táblatípus:

    CREATE TYPE EmployeeTableType AS TABLE 
    ( Employee_ID INT,
      FirstName NVARCHAR(50),
      LastName NVARCHAR(50),
      SocialSecurityNumber NVARCHAR(50) );
    GO

A következő tárolt eljárás létrehozása, vagy kiírhatja a MERGE utasítás segítségével hajtsa végre a frissítést, majd szúrja be a kódját. Az alábbi példában a MERGE utasítás egy tábla értékű paraméter @employees, EmployeeTableType típusú. A tartalmát a @employees tábla itt nem látható.

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

További információkért lásd: a dokumentáció és példák a MERGE utasításban. Bár a többlépéses tárolt ugyanaz a munkahelyi sikerült végezhető el a eljáráshívási külön INSERT, és frissítési műveleteket, a MERGE utasítás hatékonyabban. Adatbázis kód hogyan hozhat létre a MERGE utasítás közvetlenül nélkül két adatbázis hívások az INSERT vagy UPDATE használó Transact-SQL-hívások is.

## <a name="recommendation-summary"></a>A javaslat összefoglaló
Az alábbi lista a jelen témakörben bemutatott kötegelési ajánlások összegzését tartalmazza:

* Pufferelés és kötegelés segítségével növelheti a teljesítményét és méretezhetőségét SQL adatbázis-alkalmazások.
* Ismerje meg a mellékhatásokkal kötegelés/pufferelés és a rugalmasság között. Adott szerepkör meghibásodás során a egy feldolgozatlan kötegelt az üzleti szempontból kritikus fontosságú adatok elvesztését kockáztatja a teljesítmény előnye, hogy kötegelés előfordulhat, hogy járó.
* Tartsa a késés csökkentése érdekében az adatbázis egy adatközponton belül minden hívások történt kísérlet.
* Ha úgy dönt, hogy egyetlen kötegelési technika, tábla értékű paraméter nyújtanak a legjobb teljesítményt és rugalmasságot biztosít.
* A leggyorsabb insert teljesítmény érdekében kövesse az alábbi általános irányelveket, de a forgatókönyv teszteléséhez:
  * < 100 sorai egy paraméteres INSERT parancs használata.
  * A < 1000 sor használja a táblázat értékű paramétereket.
  * A > = 1000 sorok, SqlBulkCopy használja.
* A és törlési műveletek frissítéséhez használja tábla értékű paraméter tárolt eljárás logikával határozza meg, hogy a megfelelő műveletet az egyes sorokkal. a tábla paraméterben.
* Köteg mérete irányelveket:
  * A legnagyobb kötegelt méretek számára az alkalmazás- és üzleti követelmények célszerű használni.
  * A jobb teljesítménye nagy köteg a kockázatot jelentő ideiglenes vagy végzetes hibák elosztása. Mi az a következménye az újrapróbálkozások és az adatvesztés a kötegben? 
  * A legnagyobb kötegméret ellenőrzése, hogy SQL-adatbázis nem elutasítania tesztelése.
  * A vezérlő kötegelés, például a köteg méretének vagy a pufferelési időszak-konfigurációs beállítások létrehozása. Ezek a beállítások rugalmasságot biztosít. Éles környezetben kötegelési viselkedést a felhőalapú szolgáltatás ismételt üzembe helyezésével módosíthatja.
* Kerülje a párhuzamos végrehajtás kötegek, amely több adatbázis egyetlen táblájára működik. Ha a kötegek osztja szét több munkavégző szál, tesztek futtatása annak szálak ideális számának meghatározásához. Után egy nem meghatározott küszöbértéket több szál fog miatta a teljesítmény, nem pedig növeli azt.
* Vegye figyelembe a pufferelés méret és így további forgatókönyvek kötegelés végrehajtási idő.

## <a name="next-steps"></a>Következő lépések
Ez a cikk összpontosított hogyan adatbázis tervezési és a kapcsolódó kötegelés technikák kódolási javíthatja az alkalmazás teljesítményét és méretezhetőségét. Ez azonban csak egy tényező az általános stratégiában. A jobb teljesítmény és méretezhetőség további részleteket lásd: [Azure SQL Database teljesítményét útmutatást az önálló adatbázisok](sql-database-performance-guidance.md) és [rugalmas készletek ára és teljesítménye szempontok](sql-database-elastic-pool-guidance.md).

