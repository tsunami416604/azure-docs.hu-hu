---
title: Egy SQL Database-ből a referenciaadatok használata az Azure Stream Analytics-feladat (előzetes verzió)
description: Ez a cikk ismerteti a referenciaadat-bemenetek SQL-adatbázis használata az Azure Stream Analytics-feladat az Azure Portalon, és a Visual Studióban.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 79f0e58ea11d8bdb8c30ca1e50fae2635f719681
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118020"
---
# <a name="use-reference-data-from-a-sql-database-for-an-azure-stream-analytics-job-preview"></a>Egy SQL Database-ből a referenciaadatok használata az Azure Stream Analytics-feladat (előzetes verzió)

Az Azure Stream Analytics támogatja az Azure SQL Database bemenete referenciaadatokat forrásaként. Használhatja az SQL Database referenciaadatok a Stream Analytics-feladatot az Azure Portalon, és a Visual Studióban a Stream Analytics tools for. Ez a cikk bemutatja, hogyan hajtsa végre a két módszert.

## <a name="azure-portal"></a>Azure Portal

A következő lépések segítségével adja hozzá az Azure SQL Database mint egy bemeneti forrás referencia, az Azure portal használatával:

### <a name="portal-prerequisites"></a>Portál Előfeltételek

1. Hozzon létre egy Stream Analytics-feladatot.

2. Hozzon létre egy tárfiókot, a Stream Analytics-feladat által használható.

3. Hozzon létre az Azure SQL Database referenciaadatok, a Stream Analytics-feladat által használt adatkészlet.

### <a name="define-sql-database-reference-data-input"></a>Az SQL Database referenciaadat-bemenetek megadása

1. Válassza ki a Stream Analytics-feladat **bemenetek** alatt **feladattopológia**. Kattintson a **bemeneti hivatkozás hozzáadása** válassza **SQL Database**.

   ![Stream Analytics-feladat bemenete](./media/sql-reference-data/stream-analytics-inputs.png)

2. Adja meg a Stream Analytics beviteli konfigurációkat. Válassza ki az adatbázis neve, a kiszolgáló neve, a felhasználónév és jelszó. Ha azt szeretné, hogy a referenciaadatok frissítése rendszeresen adjon meg, válassza "A" Ha meg szeretné adni a frissítési gyakoriság DD:HH:MM. Ha egy rövid frissítési gyakoriság a nagy méretű adatkészleteket, használhat egy [változáslekérdezések](sql-reference-data.md#delta-query).

   ![Az SQL Database referencia-konfiguráció](./media/sql-reference-data/sql-input-config.png)

3. Tesztelje a pillanatkép-lekérdezést az SQL-Lekérdezésszerkesztő. További információkért lásd: [csatlakozás és adatok lekérdezése az Azure portal SQL-Lekérdezésszerkesztő használatával](../sql-database/sql-database-connect-query-portal.md)

### <a name="specify-storage-account-in-job-config"></a>Adja meg a tárfiók feladat config

Navigáljon a **Tárfiók-beállítások** alatt **konfigurálása** válassza **tárfiók hozzáadása**.

   ![Stream Analytics tárfiók-beállítások](./media/sql-reference-data/storage-account-settings.png)

### <a name="start-the-job"></a>A feladat indítása

Miután konfigurálta a más bemenetek, kimenetek és lekérdezése, elindíthatja a Stream Analytics-feladat.

## <a name="tools-for-visual-studio"></a>Visual Studio-eszközök

A következő lépések segítségével adja hozzá az Azure SQL Database mint egy hivatkozási bemeneti forrás a Visual Studio használatával:

### <a name="visual-studio-prerequisites"></a>A Visual Studio-Előfeltételek

1. Ha a Visual Studio 2017-et használ, frissítse a 15.8.2 vagy újabb. Vegye figyelembe, hogy 16,0 és újabb használata nem támogatott ezen a ponton.

2. [A Stream Analytics tools for Visual Studio telepítése](stream-analytics-tools-for-visual-studio-install.md). A Visual Studio következő verziói támogatottak:

   * Visual Studio 2015
   * Visual Studio 2017

3. A megismerése a [Stream Analytics tools for Visual Studio](stream-analytics-quick-create-vs.md) rövid.

4. Tárfiók létrehozása.

### <a name="create-a-sql-database-table"></a>Hozzon létre egy SQL-adatbázistáblába

SQL Server Management Studio használatával hozzon létre egy táblát a referencia-adatok tárolására. Lásd: [az első Azure SQL adatbázis megtervezése SSMS használatával](../sql-database/sql-database-design-first-database.md) részleteiről.

Az alábbi példában használt példa táblát jött létre a következő utasítást:

```SQL
create table chemicals(Id Bigint,Name Nvarchar(max),FullName Nvarchar(max));
```

### <a name="choose-your-subscription"></a>Válassza ki az előfizetését

1. A Visual Studio **View** (Nézet) menüjében válassza a **Server Explorer** (Kiszolgálókezelő) lehetőséget.

2. Kattintson a jobb gombbal **Azure**válassza **kapcsolódás a Microsoft Azure-előfizetéshez**, és jelentkezzen be az Azure-fiókjával.

### <a name="create-a-stream-analytics-project"></a>Stream Analytics-projekt létrehozása

1. Válassza a **File > New Project** (Fájl > Új projekt) lehetőséget. 

2. A bal oldali sablonlistában válassza ki a **Stream Analytics**, majd az **Azure Stream Analytics Application** (Azure Stream Analytics-alkalmazás) elemet. 

3. Adja meg a projekt **neve**, **hely**, és **megoldásnevet**, és válassza ki **OK**.

   ![Új Stream Analytics-projektet a Visual Studióban](./media/sql-reference-data/stream-analytics-vs-new-project.png)

### <a name="define-sql-database-reference-data-input"></a>Az SQL Database referenciaadat-bemenetek megadása

1. Hozzon létre egy új bemeneti.

   ![Új Stream Analytics-bemenet a Visual Studióban](./media/sql-reference-data/stream-analytics-vs-input.png)

2. Kattintson duplán a **Input.json** a a **Megoldáskezelőben**.

3. Töltse ki a **Stream Analytics beviteli konfigurációs**. Az adatbázis nevét, a kiszolgáló nevét, válassza a frissítés típusa és frissítési gyakorisága. A következő formátumban adja meg a frissítési gyakoriság `DD:HH:MM`.

   ![Stream Analytics-beli bemeneti konfiguráció a Visual Studióban](./media/sql-reference-data/stream-analytics-vs-input-config.png)

   Ha "Csak egyszer hajtható végre" vagy "Hajtsa végre a rendszeres időközönként" lehetőséget választja, egy SQL háttérkódban nevű **[bemeneti Alias].snapshot.sql** jön létre a projekt alatt a **Input.json** fájl csomópont.

   ![A Visual Studióban háttérkód bemeneti](./media/sql-reference-data/once-or-periodically-codebehind.png)

   Ha úgy dönt, hogy a "Frissítés rendszeres időközönként a különbözeti", SQL háttérkódban két fájlt hoz létre: **[bemeneti Alias].snapshot.sql** és **[bemeneti Alias].delta.sql**.

   ![A megoldáskezelőben mögött Code](./media/sql-reference-data/periodically-delta-codebehind.png)

4. Nyissa meg az SQL-fájlt a szerkesztőben, és az SQL-lekérdezés írása.

5. Ha a Visual Studio 2017-et használ, és az SQL Server Data tools telepítése, tesztelheti kattintva a lekérdezés **Execute**. Egy varázsló ablak jelenik meg, segítséget nyújtanak az SQL-adatbázishoz csatlakozik, és a lekérdezés eredménye megjelenik az ablak alján.

### <a name="specify-storage-account"></a>Storage-fiók megadása

Nyissa meg **JobConfig.json** , a hivatkozás az SQL-pillanatképek tárolására szolgáló tárfiókot adjon meg.

   ![Stream Analytics-feladat konfigurálása a Visual Studióban](./media/sql-reference-data/stream-analytics-job-config.png)

### <a name="test-locally-and-deploy-to-azure"></a>Helyi teszteléshez és üzembe helyezése az Azure-bA

A feladat üzembe helyezése az Azure-bA előtt tesztelheti a lekérdezés logikája helyileg élő bemeneti adatok alapján. Ez a szolgáltatás további információkért lásd: [tesztelése helyileg az Azure Stream Analytics tools for Visual Studio (előzetes verzió) használatával élő adatok](stream-analytics-live-data-local-testing.md). Ha végzett tesztelést, kattintson a **elküldése az Azure-bA**. Referencia a [létrehoz egy Stream Analytics az Azure Stream Analytics tools for Visual Studio használatával](stream-analytics-quick-create-vs.md) gyors útmutató: ismerje meg, hogyan indítsa el a feladatot.

## <a name="delta-query"></a>Változáslekérdezés

A különbözeti lekérdezés használatakor [az Azure SQL Database időbeli verziózású táblák](../sql-database/sql-database-temporal-tables.md) használata ajánlott.

1. A pillanatkép-lekérdezés létrehozásához. 

   Használja a **@snapshotTime** arra utasítani a Stream Analytics-futtatókörnyezet a referencia-adatkészlet beszerzése az SQL database historikus táblán, a rendszer pontos ideje szerinti érvényes paramétert. Ha nem ad meg ezt a paramétert, kockázati, egy pontatlan alap referencia-adatkészlet eltérései miatti beszerzése. Egy teljes pillanatképet lekérdezési példát alább látható:

   ![Stream Analytics pillanatkép-lekérdezés](./media/sql-reference-data/snapshot-query.png)
 
2. A különbözeti lekérdezés létrehozásához. 
   
   Ez a lekérdezés lekéri az összes sort az SQL-adatbázisban beszúrt vagy törölt belül a kezdési időt **@deltaStartTime**, és a záró időpontot **@deltaEndTime**. A különbözeti lekérdezés kell visszaadnia, mint a pillanatkép-lekérdezés ugyanazokat az oszlopokat, valamint az oszlop  **_művelet_**. Ez az oszlop határozza meg, ha a sor beszúrt vagy törölt közötti **@deltaStartTime** és **@deltaEndTime**. Az eredményül kapott sorokat a rendszer megjelölt **1** a rekordok beszúrt, ha vagy **2** Ha törölve. 

   A frissített rekordok a historikus tábla hajtja végre könyvelés beszúrási és törlési művelet rögzítésével. A Stream Analytics modul ezután alkalmazza a különbözeti lekérdezés eredményeit az előző pillanatképet a referenciaadatok naprakészen tartani. Különbözeti lekérdezés például a lenti megjelenítése:

   ![Stream Analytics különbözeti lekérdezés](./media/sql-reference-data/delta-query.png)
 
  Vegye figyelembe, hogy a Stream Analytics modul rendszeres időközönként előfordulhat, hogy futtassa a pillanatkép lekérdezést mellett a különbözeti lekérdezés ellenőrzőpontok tárolásához.

## <a name="faqs"></a>Gyakori kérdések

**Díjköteles az Azure Stream Analytics SQL referenciaadat-bemenetek használatával további költségek?**

Nincsenek további nem [folyamatos átviteli egység költség](https://azure.microsoft.com/pricing/details/stream-analytics/) a Stream Analytics-feladatban. Azonban a Stream Analytics-feladat társított Azure storage-fiókkal kell rendelkeznie. A Stream Analytics-feladat lekérdezi az SQL DB (feladat indítása és a frissítési időköz) beolvasni a referencia-adatkészlet és a pillanatkép készítése a tárfiókban lévő tárolókat. Ezeket a pillanatképeket tároló többletköltségekkel jár a részletes a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/storage/) az Azure storage-fiók.

**Hogyan tudhatom meg, hogy folyamatban van a referencia-adatok pillanatkép, az SQL DB lekérdezhetők, és használja az Azure Stream Analytics-feladatot?**

Nincsenek logikai név (a metrikák az Azure Portal) használatával az SQL database referenciaadatok bemeneti állapotának figyelésére, amelyek alapján szűrt tagnak metrikákat.

   * InputEvents: Ez a metrika az SQL database referencia-adatkészlet a betöltött rekordok számát méri.
   * InputEventBytes: Ez a mérőszám a hivatkozás adatok pillanatkép betölti a Stream Analytics-feladat memória mérete méri. 

Ezek a metrikák kombinációja használható következtetnek ki, ha a feladat beolvasni a referencia-adatkészlet az SQL-adatbázis lekérdezése és betöltéssel memória.

**Kell egy speciális típusa az Azure SQL Database?**

Az Azure Stream Analytics az Azure SQL Database bármilyen típusú fog működni. Fontos azonban megérteni, hogy a referenciaadat-bemenetek megadása frissítési gyakorisága hatással lehet a lekérdezés terhelését. A különbözeti lekérdezési beállítás használatához az Azure SQL Database időbeli verziózású táblák használata ajánlott.

**Példa a bemenetet adjon meg az SQL Database referenciaadatok is?**

Ez a funkció nem érhető el.

**Miért nem tárolja az Azure Stream Analytics pillanatképek Azure Storage-fiókban?**

A Stream Analytics garantáltan pontosan egyszer dolgozza fel az eseményeket, és legalább egyszer kézbesíti az eseményeket. Azokban az esetekben, ahol átmeneti problémák hatással a feladat ismétlési kisebb mennyiségű állapot visszaállítása szükség. Ahhoz, hogy a visszajátszás, fontos, hogy rendelkezik egy Azure Storage-fiókban tárolt ezeket a pillanatképeket. Ellenőrzőpont visszajátszását további információkért lásd: [az Azure Stream Analytics-feladatok ellenőrzőpont és visszajátszás fogalmait](stream-analytics-concepts-checkpoint-replay.md).

## <a name="next-steps"></a>További lépések

* [A Stream Analytics keresések referenciaadatok használata](stream-analytics-use-reference-data.md)
* [Rövid útmutató: Stream Analytics-feladat létrehozása az Azure Stream Analytics tools for Visual Studio használatával](stream-analytics-quick-create-vs.md)
* [Teszt élő adatok használatával helyben az Azure Stream Analytics tools for Visual Studio (előzetes verzió)](stream-analytics-live-data-local-testing.md)
