---
title: SQL Database-referenciák használata Azure Stream Analytics feladatokban
description: Ez a cikk azt ismerteti, hogyan használhatók a SQL Database a Azure Stream Analytics feladatokhoz a Azure Portal és a Visual Studióban.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 733ac7d1ff1d50e5fdcfa0dec2ad3fd3f30f6d86
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72926676"
---
# <a name="use-reference-data-from-a-sql-database-for-an-azure-stream-analytics-job"></a>Azure Stream Analytics feladatokhoz tartozó SQL Database hivatkozási adatainak használata

Azure Stream Analytics támogatja a Azure SQL Database a hivatkozási adatok forrásaként. A Azure Portal és a Visual Studióban Stream Analytics eszközökkel SQL Database is használhatja a Stream Analytics feladatokra vonatkozó hivatkozási adatként. Ez a cikk bemutatja, hogyan végezheti el mindkét módszert.

## <a name="azure-portal"></a>Azure Portal

A következő lépésekkel adhat hozzá Azure SQL Database hivatkozásként megadott bemeneti forrásként a Azure Portal használatával:

### <a name="portal-prerequisites"></a>A portál előfeltételei

1. Hozzon létre egy Stream Analytics feladatot.

2. Hozzon létre egy Storage-fiókot, amelyet a Stream Analytics feladatokhoz szeretne használni.

3. Hozza létre a Azure SQL Database egy olyan adatkészlettel, amelyet a Stream Analytics feladatainak hivatkozási adatként kell használni.

### <a name="define-sql-database-reference-data-input"></a>Adja meg SQL Database a hivatkozási adatok bemenetét

1. A Stream Analyticsi feladatban válassza a **feladatok topológia**alatt található **bemenetek** lehetőséget. Kattintson a **hivatkozás hozzáadása** elemre, és válassza a **SQL Database**lehetőséget.

   ![Stream Analytics feladatok bemenete](./media/sql-reference-data/stream-analytics-inputs.png)

2. Töltse ki a Stream Analytics bemeneti konfigurációját. Válassza ki az adatbázis nevét, a kiszolgáló nevét, a felhasználónevet és a jelszót. Ha azt szeretné, hogy a rendszer rendszeres időközönként frissítse a hivatkozási adatokat, válassza a "bekapcsolva" lehetőséget a frissítési sebesség megadásához a DD: HH: PP lapon. Ha a nagyméretű adatkészletek rövid frissítési gyakorisággal rendelkeznek, használhat [különbözeti lekérdezést](sql-reference-data.md#delta-query)is.

   ![SQL Database hivatkozás konfigurálása](./media/sql-reference-data/sql-input-config.png)

3. Tesztelje a pillanatkép-lekérdezést az SQL-lekérdezés szerkesztőjében. További információ: [a Azure Portal SQL-lekérdezési szerkesztőjének használata a kapcsolódáshoz és az adatok lekérdezéséhez](../sql-database/sql-database-connect-query-portal.md)

### <a name="specify-storage-account-in-job-config"></a>Storage-fiók meghatározása a feladatok konfigurációjában

Navigáljon a **Storage-fiók beállításaihoz** a **Konfigurálás** területen, majd válassza a **Storage-fiók hozzáadása**lehetőséget.

   ![Stream Analytics Storage-fiók beállításai](./media/sql-reference-data/storage-account-settings.png)

### <a name="start-the-job"></a>A feladat indítása

Ha más bemeneteket, kimeneteket és lekérdezést konfigurált, elindíthatja a Stream Analytics feladatot.

## <a name="tools-for-visual-studio"></a>Eszközök a Visual Studióhoz

A következő lépésekkel adhatja hozzá a Azure SQL Databaset a Visual Studio használatával, hivatkozásként szolgáló bemeneti forrásként:

### <a name="visual-studio-prerequisites"></a>A Visual Studio előfeltételei

1. [Telepítse a Visual studióhoz készült stream Analytics-eszközöket](stream-analytics-tools-for-visual-studio-install.md). A Visual Studio következő verziói támogatottak:

   * Visual Studio 2015
   * Visual Studio 2019

2. Ismerkedjen meg a [Visual Studio stream Analytics eszközeivel](stream-analytics-quick-create-vs.md) .

3. Tárfiók létrehozása.

### <a name="create-a-sql-database-table"></a>SQL Database tábla létrehozása

A SQL Server Management Studio használatával hozzon létre egy táblázatot a hivatkozási adatai tárolásához. A részletekért lásd: [az első Azure SQL Database-adatbázis megtervezése a SSMS használatával](../sql-database/sql-database-design-first-database.md) .

Az alábbi példában a következő utasításban használt példában szereplő táblázat jött létre:

```SQL
create table chemicals(Id Bigint,Name Nvarchar(max),FullName Nvarchar(max));
```

### <a name="choose-your-subscription"></a>Válassza ki az előfizetését

1. A Visual Studio **View** (Nézet) menüjében válassza a **Server Explorer** (Kiszolgálókezelő) lehetőséget.

2. Kattintson a jobb gombbal az **Azure**-ra, válassza a **Kapcsolódás Microsoft Azure előfizetéshez**lehetőséget, majd jelentkezzen be az Azure-fiókjával.

### <a name="create-a-stream-analytics-project"></a>Stream Analytics-projekt létrehozása

1. Válassza a **File > New Project** (Fájl > Új projekt) lehetőséget. 

2. A bal oldali sablonlistában válassza ki a **Stream Analytics**, majd az **Azure Stream Analytics Application** (Azure Stream Analytics-alkalmazás) elemet. 

3. Adja meg a projekt **nevét**, **helyét**és a **megoldás nevét**, majd kattintson **az OK gombra**.

   ![Új Stream Analytics projekt a Visual Studióban](./media/sql-reference-data/stream-analytics-vs-new-project.png)

### <a name="define-sql-database-reference-data-input"></a>Adja meg SQL Database a hivatkozási adatok bemenetét

1. Hozzon létre egy új bemenetet.

   ![Új Stream Analytics bevitel a Visual Studióban](./media/sql-reference-data/stream-analytics-vs-input.png)

2. Kattintson duplán a **megoldáskezelő**a **input. JSON** fájlra.

3. Töltse ki a **stream Analytics bemeneti konfigurációját**. Válassza ki az adatbázis nevét, a kiszolgáló nevét, a frissítés típusát és a frissítési gyakoriságot. A frissítési gyakoriságot a `DD:HH:MM`formátumban kell megadni.

   ![Stream Analytics bemeneti konfiguráció a Visual Studióban](./media/sql-reference-data/stream-analytics-vs-input-config.png)

   Ha a "végrehajtás csak egyszer" vagy a "végrehajtás rendszeresen" lehetőséget választja, a rendszer egy **[bemeneti alias]** nevű SQL Codebehind fájlt hoz létre. a Snapshot. SQL a **bemeneti. JSON** fájl csomópont alatt jön létre a projektben.

   ![A Visual Studio mögötti bemeneti kód](./media/sql-reference-data/once-or-periodically-codebehind.png)

   Ha a "rendszeres időközönként frissül a Delta szolgáltatással" lehetőséget választja, két SQL-CodeBehind-fájl jön létre: **[bemeneti alias]. Snapshot. SQL** és **[input alias]. Delta. SQL**.

   ![Kód a megoldás Explorerben](./media/sql-reference-data/periodically-delta-codebehind.png)

4. Nyissa meg az SQL-fájlt a szerkesztőben, és írja be az SQL-lekérdezést.

5. Ha a Visual Studio 2019-at használja, és telepítette SQL Server adateszközöket, a **végrehajtás**gombra kattintva tesztelheti a lekérdezést. A varázsló ablaka az SQL-adatbázishoz való kapcsolódáshoz nyújt segítséget, a lekérdezési eredmény pedig az alul található ablakban fog megjelenni.

### <a name="specify-storage-account"></a>Storage-fiók meghatározása

Nyissa meg az **JobConfig. JSON** fájlt, és határozza meg az SQL-referenciák pillanatképeit tároló Storage-fiókot.

   ![Stream Analytics feladatok konfigurálása a Visual Studióban](./media/sql-reference-data/stream-analytics-job-config.png)

### <a name="test-locally-and-deploy-to-azure"></a>Helyi tesztelés és üzembe helyezés az Azure-ban

Mielőtt üzembe helyezné a feladatot az Azure-ban, a lekérdezési logikát helyileg is tesztelheti az élő bemeneti adatokkal. A szolgáltatással kapcsolatos további információkért tekintse meg az [élő adatok helyi tesztelése a Visual studióhoz készült Azure stream Analytics Tools (előzetes verzió)](stream-analytics-live-data-local-testing.md)című témakört. Ha végzett a teszteléssel, kattintson **a Küldés az Azure**-ba lehetőségre. A feladatok elindításával kapcsolatos további információkért tekintse meg az [stream Analytics létrehozása a Visual studiohoz készült Azure stream Analytics Tools használatával](stream-analytics-quick-create-vs.md) című útmutatót.

## <a name="delta-query"></a>Különbözeti lekérdezés

A különbözeti lekérdezés használatakor a rendszer [Azure SQL Database ideiglenes táblákat](../sql-database/sql-database-temporal-tables.md) ajánlott használni.

1. Hozzon létre egy ideiglenes táblázatot a Azure SQL Databaseban.
   
   ```SQL 
      CREATE TABLE DeviceTemporal 
      (  
         [DeviceId] int NOT NULL PRIMARY KEY CLUSTERED 
         , [GroupDeviceId] nvarchar(100) NOT NULL
         , [Description] nvarchar(100) NOT NULL 
         , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
         , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
         , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
      )  
      WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.DeviceHistory));  -- DeviceHistory table will be used in Delta query
   ```
2. A pillanatkép-lekérdezés szerzője. 

   A **\@snapshotTime** paraméter használatával utasítsa a stream Analytics futtatókörnyezetet az SQL Database ideiglenes táblából származó, a rendszeridőben érvényes hivatkozási adatkészlet beszerzésére. Ha nem adja meg ezt a paramétert, akkor egy pontatlan alapszintű hivatkozási adat beszerzésére van szükség az órajel-eltérések miatt. Alább látható egy példa a teljes pillanatkép-lekérdezésre:
   ```SQL
      SELECT DeviceId, GroupDeviceId, [Description]
      FROM dbo.DeviceTemporal
      FOR SYSTEM_TIME AS OF @snapshotTime
   ```
 
2. A különbözeti lekérdezés szerzője. 
   
   Ez a lekérdezés lekérdezi az SQL-adatbázis összes olyan sorát, amelyet kezdési idő, **\@deltaStartTime**és befejezési idő **\@deltaEndTime**. A különbözeti lekérdezésnek a pillanatkép-lekérdezéssel megegyező oszlopokat, valamint az oszlop **_műveletét_** kell visszaadnia. Ez az oszlop határozza meg, hogy a sor **\@deltaStartTime** és **\@deltaEndTime**között van-e beszúrva vagy törölve. Az eredményül kapott sorok **1** -ként vannak megjelölve, ha a rekordok be lettek helyezve, vagy **2** Ha törölve lettek. 

   A frissített rekordok esetében az időbeli táblázat a beszúrási és törlési művelet rögzítésével végzi a könyvelést. A Stream Analytics futtatókörnyezet Ezután alkalmazza a különbözeti lekérdezés eredményét az előző pillanatképre, hogy a hivatkozási adatok naprakészek maradjanak. A különbözeti lekérdezés példája a következő:

   ```SQL
      SELECT DeviceId, GroupDeviceId, Description, 1 as _operation_
      FROM dbo.DeviceTemporal
      WHERE ValidFrom BETWEEN @deltaStartTime AND @deltaEndTime   -- records inserted
      UNION
      SELECT DeviceId, GroupDeviceId, Description, 2 as _operation_
      FROM dbo.DeviceHistory   -- table we created in step 1
      WHERE ValidTo BETWEEN @deltaStartTime AND @deltaEndTime     -- record deleted
   ```
 
   Vegye figyelembe, hogy Stream Analytics futtatókörnyezet időszakosan futtathatja a pillanatkép-lekérdezést az ellenőrzőpontok tárolására szolgáló különbözeti lekérdezésen kívül is.

## <a name="test-your-query"></a>A lekérdezés tesztelése
   Fontos ellenőrizni, hogy a lekérdezés visszaadja-e a várt adatkészletet, amelyet a Stream Analytics-feladatok hivatkozási adatként fognak használni. A lekérdezés teszteléséhez nyissa meg a bekapcsolást a portálon, a feladatok szakaszban. Ezután kiválaszthatja a mintaadatok a SQL Database hivatkozás bemenetén. A minta elérhetővé válása után letöltheti a fájlt, és megtekintheti, hogy a visszaadott információk a várt módon működnek-e. Ha optimalizálni szeretné a fejlesztési és tesztelési iterációkat, javasoljuk, hogy használja a [Visual Studio stream Analytics eszközeit](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install). Azt is megteheti, hogy az előnyben részesített bármely más eszközt, hogy a lekérdezés a megfelelő eredményeket adja vissza Azure SQL Database, majd használja azt a Stream Analytics feladatokban. 

## <a name="faqs"></a>Gyakori kérdések

**Felmerülhetek-e további költségek az SQL-alapú adatbevitel Azure Stream Analyticsban való használatával?**

A Stream Analytics-feladatokban nem használhatók további [díjszabási egységenként](https://azure.microsoft.com/pricing/details/stream-analytics/) . A Stream Analytics feladatokhoz azonban hozzá kell rendelni egy társított Azure Storage-fiókot. A Stream Analytics feladata lekérdezi az SQL DB-t (a feladatok indítási és frissítési időköze alatt) a hivatkozási adathalmaz lekérésére és a pillanatképnek a Storage-fiókban való tárolására. Ezeknek a pillanatképeknek a tárolása az Azure Storage-fiók [díjszabási oldalának](https://azure.microsoft.com/pricing/details/storage/) részletes díját is felmerül.

**Hogyan tudja, hogy a rendszer az SQL DB-ből kérdezi le, és felhasználja a Azure Stream Analytics feladatokban?**

A logikai név alapján két mérőszám van szűrve (a metrikák Azure Portalon), amelyet az SQL Database-alapú adatbevitel állapotának figyelésére használhat.

   * InputEvents: Ez a metrika az SQL Database-alapú hivatkozási adatkészletből betöltött rekordok számát méri.
   * InputEventBytes: Ez a metrika a Stream Analytics feladathoz tartozó memóriában betöltött hivatkozási adatok pillanatképének méretét méri. 

Mindkét metrika kombinációja felhasználható annak a következtetésére, hogy a művelet lekérdezi az SQL Database-t, és beolvassa a hivatkozási adatkészletet, majd betölti azt a memóriába.

**Szükség van-e a Azure SQL Database speciális típusára?**

Azure Stream Analytics bármilyen típusú Azure SQL Databasevel működni fog. Fontos azonban megérteni, hogy a hivatkozási adatok bemenetére beállított frissítési sebesség hatással lehet a lekérdezés terhelésére. A különbözeti lekérdezési beállítás használatához ajánlott az időbeli táblák használata Azure SQL Databaseban.

**Miért Azure Stream Analytics a pillanatképek tárolása az Azure Storage-fiókban?**

A Stream Analytics garantáltan pontosan egyszer dolgozza fel az eseményeket, és legalább egyszer kézbesíti az eseményeket. Azokban az esetekben, amikor az átmeneti problémák hatással vannak a feladatra, az állapot visszaállításához kis mennyiségű visszajátszás szükséges. A visszajátszás engedélyezéséhez az szükséges, hogy a pillanatképek egy Azure Storage-fiókban legyenek tárolva. Az ellenőrzőpontok újrajátszása szolgáltatással kapcsolatos további információkért lásd: [ellenőrzőpontok és Replay fogalmak Azure stream Analytics feladatokban](stream-analytics-concepts-checkpoint-replay.md).

## <a name="next-steps"></a>Következő lépések

* [A Stream Analytics-keresések hivatkozási adatainak használata](stream-analytics-use-reference-data.md)
* [Gyors útmutató: Stream Analytics-feladatok létrehozása a Visual studióhoz készült Azure Stream Analytics Tools használatával](stream-analytics-quick-create-vs.md)
* [Élő adatellenőrzés helyi tesztelése a Visual Studio Azure Stream Analytics eszközeivel (előzetes verzió)](stream-analytics-live-data-local-testing.md)
