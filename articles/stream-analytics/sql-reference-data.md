---
title: SQL-adatbázis referenciaadatainak használata Egy Azure Stream Analytics-feladatban
description: Ez a cikk ismerteti, hogyan használhatja az SQL-adatbázis referencia adatbevitel egy Azure Stream Analytics-feladat az Azure Portalon és a Visual Studióban.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: aebb590d93b3fb26151f15c176a2941845cdd50c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426503"
---
# <a name="use-reference-data-from-a-sql-database-for-an-azure-stream-analytics-job"></a>Sql-adatbázisreferencia-adatok használata Azure Stream Analytics-feladathoz

Az Azure Stream Analytics támogatja az Azure SQL Database-t, mint a referenciaadatok bemeneti forrását. Az SQL Database referenciaadatként használhatja a Stream Analytics-feladathoz az Azure Portalon és a Visual Studióban a Stream Analytics-eszközökkel. Ez a cikk bemutatja, hogyan kell mindkét módszer.

## <a name="azure-portal"></a>Azure portál

Az alábbi lépésekkel adja hozzá az Azure SQL Database-t referencia-bemeneti forrásként az Azure Portal használatával:

### <a name="portal-prerequisites"></a>A portál előfeltételei

1. Stream Analytics-feladat létrehozása.

2. Hozzon létre egy tárfiókot a Stream Analytics feladat által használandó.

3. Hozza létre az Azure SQL-adatbázist egy adathalmazt, amelyet a Stream Analytics-feladat referenciaadatként használ.

### <a name="define-sql-database-reference-data-input"></a>SQL-adatbázis hivatkozási adatbevitelének definiálása

1. A Stream Analytics-feladatban válassza **a Bemenetek** lehetőséget a **Feladattopológia**területen. Kattintson **a Hivatkozásbevitel hozzáadása** gombra, és válassza az **SQL Database parancsot.**

   ![Stream Analytics-feladat bevitele](./media/sql-reference-data/stream-analytics-inputs.png)

2. Töltse ki a Stream Analytics bemeneti konfigurációk. Válassza ki az adatbázis nevét, kiszolgálónevét, felhasználónevét és jelszavát. Ha azt szeretné, hogy a referenciaadatok bevitele rendszeres időközönként frissüljön, a "Be" gombra választva adja meg a frissítési gyakoriságot a DD:Óó:PP-ben. Ha nagy adatkészletek rövid frissítési gyakorisággal rendelkeznek, [használhatdelta-lekérdezést.](sql-reference-data.md#delta-query)

   ![SQL-adatbázis hivatkozási konfigurációja](./media/sql-reference-data/sql-input-config.png)

3. Tesztelje a pillanatkép-lekérdezést az SQL-lekérdezésszerkesztőben. További információ: [Az Azure Portal SQL-lekérdezésszerkesztőjének használata adatok csatlakoztatásához és lekérdezéséhez című témakörben](../sql-database/sql-database-connect-query-portal.md)

### <a name="specify-storage-account-in-job-config"></a>Tárfiók megadása a Feladat konfigurációjában

Keresse meg a **Tárfiók beállításai t,** majd a **Tárfiók** **hozzáadása**csoportban.

   ![A Stream Analytics tárfiókbeállításai](./media/sql-reference-data/storage-account-settings.png)

### <a name="start-the-job"></a>A feladat indítása

Miután konfigurálta az egyéb bemeneteket, kimeneteket és lekérdezéseket, elindíthatja a Stream Analytics-feladatot.

## <a name="tools-for-visual-studio"></a>Visual Studio-eszközök

Az alábbi lépésekkel hozzáadhatja az Azure SQL Database-t referencia-bemeneti forrásként a Visual Studio használatával:

### <a name="visual-studio-prerequisites"></a>A Visual Studio előfeltételei

1. [Telepítse a Stream Analytics eszközöket a Visual Studio számára](stream-analytics-tools-for-visual-studio-install.md). A Visual Studio következő verziói támogatottak:

   * Visual Studio 2015
   * Visual Studio 2019

2. Ismerkedjen meg a [Visual Studio Stream Analytics eszközeivel.](stream-analytics-quick-create-vs.md)

3. Tárfiók létrehozása.

### <a name="create-a-sql-database-table"></a>SQL Database tábla létrehozása

Az SQL Server Management Studio segítségével hozzon létre egy táblázatot a referenciaadatok tárolására. A részletekért tekintse meg [az első Azure SQL-adatbázis tervezése SSMS használatával](../sql-database/sql-database-design-first-database.md) című témakört.

A következő példában használt példatábla a következő utasításból jött létre:

```SQL
create table chemicals(Id Bigint,Name Nvarchar(max),FullName Nvarchar(max));
```

### <a name="choose-your-subscription"></a>Válassza ki az előfizetését

1. A Visual Studio **View** (Nézet) menüjében válassza a **Server Explorer** (Kiszolgálókezelő) lehetőséget.

2. Kattintson a jobb gombbal az **Azure-ra,** válassza **a Csatlakozás a Microsoft Azure-előfizetéshez**lehetőséget, és jelentkezzen be Azure-fiókjával.

### <a name="create-a-stream-analytics-project"></a>Stream Analytics-projekt létrehozása

1. Válassza a **File > New Project** (Fájl > Új projekt) lehetőséget. 

2. A bal oldali sablonlistában válassza ki a **Stream Analytics**, majd az **Azure Stream Analytics Application** (Azure Stream Analytics-alkalmazás) elemet. 

3. Írja be a projekt **nevét**, **helyét**és **megoldásnevét,** majd kattintson **az OK gombra.**

   ![Új Stream Analytics projekt a Visual Studióban](./media/sql-reference-data/stream-analytics-vs-new-project.png)

### <a name="define-sql-database-reference-data-input"></a>SQL-adatbázis hivatkozási adatbevitelének definiálása

1. Hozzon létre egy új bemenetet.

   ![Új Stream Analytics-bevitel a Visual Studióban](./media/sql-reference-data/stream-analytics-vs-input.png)

2. Kattintson duplán **az Input.json ikonra** a **Megoldáskezelőben.**

3. Töltse ki a **Stream Analytics bemeneti konfigurációját.** Válassza ki az adatbázis nevét, a kiszolgáló nevét, a frissítés típusát és a frissítési gyakoriságot. Adja meg a frissítési gyakoriságot a formátumban. `DD:HH:MM`

   ![Stream Analytics beviteli konfigurációja a Visual Studióban](./media/sql-reference-data/stream-analytics-vs-input-config.png)

   Ha a "Csak egyszer végrehajtva" vagy a "Rendszeres időközönként végrehajtás" lehetőséget választja, egy **[Input Alias]. snapshot.sql** nevű SQL CodeBehind fájl jön létre a projektben az **Input.json** fájlcsomópont alatt.

   ![A Visual Studio mögötti beviteli kód](./media/sql-reference-data/once-or-periodically-codebehind.png)

   Ha a "Rendszeres frissítés a Deltával" lehetőséget választja, a rendszer két SQL CodeBehind fájlt hoz létre: **[Input Alias].snapshot.sql** és **[Input Alias].delta.sql**.

   ![Kód mögött a megoldáskezelőben](./media/sql-reference-data/periodically-delta-codebehind.png)

4. Nyissa meg az SQL-fájlt a szerkesztőben, és írja be az SQL-lekérdezést.

5. Ha a Visual Studio 2019-et használja, és telepítette az SQL Server Data eszközöket, a **lekérdezést**a Végrehajtás gombra kattintva tesztelheti. Megjelenik egy varázslóablak, amely segít az SQL-adatbázishoz való csatlakozásban, és a lekérdezés eredménye megjelenik az ablak alján.

### <a name="specify-storage-account"></a>Tárfiók megadása

Nyissa meg **a JobConfig.json fájlmegnyitását** az SQL referencia-pillanatképek tárolásához.

   ![Stream Analytics-feladat konfigurációja a Visual Studióban](./media/sql-reference-data/stream-analytics-job-config.png)

### <a name="test-locally-and-deploy-to-azure"></a>Tesztelje helyileg és telepítse az Azure-ba

Mielőtt üzembe helyezne a feladatot az Azure-ba, helyileg tesztelheti a lekérdezési logikát az élő bemeneti adatokkal. Erről a funkcióról további információt az [Élő adatok helyi tesztelése az Azure Stream Analytics-eszközök visual studio-hoz (előzetes verzió) használatával című témakörben talál.](stream-analytics-live-data-local-testing.md) Ha végzett a teszteléssel, kattintson **a Küldés az Azure-ba**gombra. Hivatkozzon a [Stream Analytics létrehozása az Azure Stream Analytics eszközök a Visual Studio](stream-analytics-quick-create-vs.md) rövid útmutató, hogyan indíthatja el a feladatot.

## <a name="delta-query"></a>Különbözeti lekérdezés

A különbözeti lekérdezés használataesetén az [Azure SQL Database-ben ajánlott a temporális táblák](../sql-database/sql-database-temporal-tables.md) használata.

1. Hozzon létre egy historikus táblát az Azure SQL Database-ben.
   
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
2. A pillanatkép-lekérdezés megkészítése. 

   A ** \@snapshotTime** paraméter rel a Stream Analytics futásidejű, hogy szerezze be a referencia-adatkészletet az SQL-adatbázis időbeli tábla érvényes a rendszer idő. Ha nem adja meg ezt a paramétert, azt kockáztatja, hogy az óraeltérés miatt pontatlan alaphivatkozási adatkészletet kap. A teljes pillanatkép-lekérdezésre az alábbiakban talál példát:
   ```SQL
      SELECT DeviceId, GroupDeviceId, [Description]
      FROM dbo.DeviceTemporal
      FOR SYSTEM_TIME AS OF @snapshotTime
   ```
 
2. A különbözeti lekérdezés megkészítése. 
   
   Ez a lekérdezés az SQL-adatbázis összes olyan sorát olvassa be, amelyet a kezdési időpontban, ** \@a deltaStartTime**és a ** \@deltaEndTime**befejezési idő alatt szúrta ki vagy törölte. A különbözeti lekérdezésnek ugyanazokat az oszlopokat kell visszaadnia, mint a pillanatkép-lekérdezésnek, valamint az **_oszlopműveletnek._** Ez az oszlop határozza meg, hogy a sor beszúrva vagy törölve van-e a ** \@deltaStartTime** és a ** \@deltaEndTime**között. Az eredményül kapott sorok **1-ként** vannak megjelölve, ha a rekordokat beszúrták, vagy **2-ként,** ha törölték őket. 

   A frissített rekordok esetében az időbeli tábla könyvelést végez egy beszúrási és törlési művelet rögzítésével. A Stream Analytics futásidejű majd alkalmazza a különbözeti lekérdezés eredményeit az előző pillanatkép a referenciaadatok naprakészen tartása érdekében. A különbözeti lekérdezésre az alábbi példa látható:

   ```SQL
      SELECT DeviceId, GroupDeviceId, Description, 1 as _operation_
      FROM dbo.DeviceTemporal
      WHERE ValidFrom BETWEEN @deltaStartTime AND @deltaEndTime   -- records inserted
      UNION
      SELECT DeviceId, GroupDeviceId, Description, 2 as _operation_
      FROM dbo.DeviceHistory   -- table we created in step 1
      WHERE ValidTo BETWEEN @deltaStartTime AND @deltaEndTime     -- record deleted
   ```
 
   Vegye figyelembe, hogy a Stream Analytics futásidejű rendszeres időközönként futtathatja a pillanatkép-lekérdezés mellett a különbözeti lekérdezés ellenőrzőpontok tárolására.

## <a name="test-your-query"></a>A lekérdezés tesztelése
   Fontos ellenőrizni, hogy a lekérdezés visszaadja-e a várt adatkészletet, amelyet a Stream Analytics-feladat referenciaadatként fog használni. A lekérdezés teszteléséhez nyissa meg a Bevitel a feladattopológia szakaszban a portálon. Ezután kiválaszthatja a mintaadatokat az SQL Database Reference bemeneten. Miután a minta elérhetővé válik, letöltheti a fájlt, és ellenőrizheti, hogy a visszaadott adatok a várt módon jelennek-e meg. Ha optimalizálni szeretné a fejlesztést és az ismétléseket, ajánlott a Stream Analytics eszközök használata [a Visual Studio számára.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install) A kívánt bármely más eszközt is beállíthatja, hogy a lekérdezés a megfelelő eredményeket adja vissza az Azure SQL Database-től, majd ezt használja a Stream Analytics-feladatban. 

## <a name="faqs"></a>Gyakori kérdések

**Az Azure Stream Analytics SQL-referenciaadat-bevitelének használatával további költségek merülnek fel?**

A Stream Analytics-feladatban nincs további [költség streamelési egységenként.](https://azure.microsoft.com/pricing/details/stream-analytics/) A Stream Analytics-feladatnak azonban rendelkeznie kell egy társított Azure storage-fiókkal. A Stream Analytics-feladat lekérdezi az SQL DB-t (a feladat indítási és frissítési időközében) a referencia-adatkészlet lekéréséhez, és tárolja a pillanatképet a tárfiókban. Ezek a pillanatképek tárolása további díjakat az Azure storage-fiók [díjszabási lapján](https://azure.microsoft.com/pricing/details/storage/) részletezve.

**Honnan tudhatom, hogy a referenciaadatok pillanatképét lekérdezik az SQL DB-től, és az Azure Stream Analytics-feladatban használják?**

Két metrikák logikai név (a Metrikák Azure Portal), amely segítségével figyelheti az SQL-adatbázis referencia-adatok bemeneti állapotát.

   * InputEvents: Ez a metrika méri az SQL-adatbázis referencia-adatkészletéből betöltött rekordok számát.
   * InputEventbytes: Ez a metrika méri a stream analytics-feladat memóriájába betöltött referenciaadatok pillanatképének méretét. 

A két metrika kombinációja lehet következtetni, ha a feladat lekérdezése az SQL-adatbázis beolvasása a referencia-adatkészletet, majd betöltése a memóriába.

**Szükségem lesz egy speciális típusú Azure SQL-adatbázisra?**

Az Azure Stream Analytics bármilyen Azure SQL-adatbázissal működik. Fontos azonban megérteni, hogy a referenciaadatok bemenetéhez beállított frissítési gyakoriság hatással lehet a lekérdezés terhelésére. A különbözeti lekérdezési beállítás használatához ajánlott az Azure SQL Database-ben időbeli táblák használata.

**Miért tárolja az Azure Stream Analytics a pillanatképeket az Azure Storage-fiókban?**

A Stream Analytics garantáltan pontosan egyszer dolgozza fel az eseményeket, és legalább egyszer kézbesíti az eseményeket. Azokban az esetekben, ahol átmeneti problémák befolyásolják a munkát, egy kis mennyiségű visszajátszás szükséges az állapot visszaállításához. A visszajátszás engedélyezéséhez szükség van, hogy ezeket a pillanatképeket egy Azure Storage-fiókban tárolja. Az ellenőrzőpontok visszajátszásáról az [Ellenőrzőpont és a visszajátszási fogalmak az Azure Stream Analytics-feladatokban](stream-analytics-concepts-checkpoint-replay.md)című témakörben talál további információt.

## <a name="next-steps"></a>További lépések

* [Referenciaadatok használata a Stream Analytics-ben lévő keresgélő műveletekhez](stream-analytics-use-reference-data.md)
* [Gyors útmutató: Stream Analytics-feladat létrehozása a Visual Studio Azure Stream Analytics eszközeinek használatával](stream-analytics-quick-create-vs.md)
* [Élő adatok helyi tesztelése az Azure Stream Analytics-eszközök kel a Visual Studio számára (előzetes verzió)](stream-analytics-live-data-local-testing.md)
