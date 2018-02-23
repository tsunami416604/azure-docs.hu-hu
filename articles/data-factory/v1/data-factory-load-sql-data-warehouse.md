---
title: "Terabájtos adatkészleteket betöltése az SQL Data Warehouse |} Microsoft Docs"
description: "Bemutatja, hogyan 1 TB adatot tölthetők be az Azure SQL Data Warehouse Azure Data Factory a 15 perc"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: a6c133c0-ced2-463c-86f0-a07b00c9e37f
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 1b931de564417ab98207321d7798613b187e411f
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="load-1-tb-into-azure-sql-data-warehouse-under-15-minutes-with-data-factory"></a>1 TB-os betöltése az Azure SQL Data Warehouse Data Factory a 15 perc
> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [adatok másolása vagy Azure SQL Data Warehouse a Data Factory 2-es verzió használatával](../connector-azure-sql-data-warehouse.md).


[Az SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) egy olyan felhőalapú, horizontálisan felskálázható adatbázis képes a nagy mennyiségű relációs és nem relációs adatok.  Nagymértékben párhuzamos feldolgozási (MPP) architektúrára épülő SQL Data Warehouse optimalizált vállalati adatok adatraktár munkaterhelések.  Felhő rugalmasság méretezni a tárolást, és egymástól függetlenül számítási rugalmasságot biztosít.

Ismerkedés az Azure SQL Data Warehouse mostantól egyszerűbb, mint minden eddiginél használatával **Azure Data Factory**.  Az Azure Data Factory egy teljes körűen felügyelt felhőalapú integrációs szolgáltatás, amely feltölti az SQL Data Warehouse az adatokat a rendszer, és értékes amivel időt takarít meg az SQL Data Warehouse értékelése és felépítése az elemzés közben használható megoldások. Az alábbiakban az adatok betöltése az Azure SQL Data Warehouse Azure Data Factory használatával legfontosabb előnyei:

* **Egyszerűen állíthat be**: 5. lépés intuitív varázsló nem parancsfájl-szükséges.
* **Gazdag adattároló támogatási**: a helyszíni és felhőalapú adattároló széles skáláját beépített támogatása.
* **Biztonságos és megfelelő**: HTTPS- vagy ExpressRoute keresztül továbbított adatok, és a globális szolgáltatás meglétének biztosítja az adatok sosem hagyja el a földrajzi határ
* **A PolyBase használatával unparalleled teljesítmény** – adatok áthelyezése az Azure SQL Data Warehouse Polybase használatával a leghatékonyabb módja a. Az átmeneti tárolási blob szolgáltatást használja, minden típusú adattároló Azure Blob Storage tárolóban, amely alapértelmezés szerint támogatja a Polybase mellett a nagy terhelés sebességű érhet el.

Ez a cikk bemutatja, hogyan 1 TB méretű adatok betöltése az Azure Blob Storage az Azure SQL Data Warehouse a 15 percig, több mint 1,2 GB/s átviteli sebesség a Data Factory másolása varázsló segítségével.

Ez a cikk részletesen adatok áthelyezése az Azure SQL Data Warehouse másolása varázsló használatával.

> [!NOTE]
>  Általános képességeivel kapcsolatos információk a Data Factory az adatok áthelyezése az Azure SQL Data Warehouse, lásd: [helyezze át az adatokat, és az Azure SQL Data Warehouse Azure Data Factory használatával](data-factory-azure-sql-data-warehouse-connector.md) cikk.
>
> Is hozhat létre Azure-portált használja, a Visual Studio PowerShell, adatcsatornákat stb. Lásd: [oktatóanyag: adatok másolása az Azure Blob az Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) a részletes utasításokat a másolási tevékenység során az Azure Data Factory gyors útmutatást.  
>
>

## <a name="prerequisites"></a>Előfeltételek
* Az Azure Blob Storage: ehhez a kísérlethez Azure Blob Storage (GRS) használ a TPC-H tesztelési adatkészlet tárolásához.  Ha nem rendelkezik Azure-tárfiók, további [a storage-fiók létrehozása](../../storage/common/storage-create-storage-account.md#create-a-storage-account).
* [TPC-H](http://www.tpc.org/tpch/) adatok: a tesztelési dataset TPC-H használandó fogjuk.  Ehhez szüksége `dbgen` a TPC-H eszközkészlet segítségével létrehozni az adatkészletet.  Vagy letöltheti a forráskódot `dbgen` a [TPC eszközök](http://www.tpc.org/tpc_documents_current_versions/current_specifications.asp) saját kezűleg fordítása, és töltse le a lefordított bináris fájljának [GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TPCHTools).  A következő parancsokat a strukturálatlan 1 TB-fájl létrehozásához futtassa a dbgen.exe `lineitem` terjedésének tábla 10 a fájlok között:

  * `Dbgen -s 1000 -S **1** -C 10 -T L -v`
  * `Dbgen -s 1000 -S **2** -C 10 -T L -v`
  * …
  * `Dbgen -s 1000 -S **10** -C 10 -T L -v`

    Most másolja a létrehozott fájlokat Azure Blob.  Tekintse meg [adatok áthelyezése egy helyszíni fájlrendszer érkező vagy oda irányuló Azure Data Factory használatával](data-factory-onprem-file-system-connector.md) módjáról az ADF-másolással.    
* Az SQL Data Warehouse: ehhez a kísérlethez adatokat tölt az Azure SQL Data Warehouse 6000 dwu-k számát létrehozva

    Tekintse meg [hozzon létre egy Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) részletes útmutatást az SQL Data Warehouse-adatbázis létrehozásához.  Ahhoz, hogy a lehető legjobb teljesítményt lehet betöltése az SQL Data Warehouse Polybase használatával, választjuk Adattárházegységek (dwu-k) a beállítását, amely 6000 dwu-k engedélyezett maximális számát.

  > [!NOTE]
  > Ha betöltése az Azure Blob, a teljesítmény betöltése adatai közvetlenül azzal arányos, hogy az SQL Data Warehouse konfigurált dwu-k száma:
  >
  > 1 TB-os betöltése 1000 a DWU az SQL Data Warehouse veszi a 87 perc (~ 200 MB/s átviteli) betöltése 1 TB 2000 DWU az SQL Data Warehouse vesz 46 perc (~ 380 MB/s átviteli) betöltése 1 TB az 6000 DWU SQL Data Warehouse perc 14 (~1.2 GB/s átviteli)
  >
  >

    SQL Data Warehouse létrehozásához 6000 dwu-k, a csúszka teljesítmény egészen a jobb oldalon:

    ![Teljesítmény csúszka](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    A meglévő adatbázis, amely nincs beállítva 6000 dwu-k legfeljebb az Azure-portál használatával.  Keresse meg az adatbázist, az Azure portálon, és nincs olyan **méretezési** gombra a **áttekintése** panel az alábbi ábrán látható:

    ![Skála gomb](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    Kattintson a **méretezési** gombra kattintva nyissa meg a következő panelen, a csúszka a maximális értéknél, majd kattintson **mentése** gombra.

    ![Párbeszédpanel](media/data-factory-load-sql-data-warehouse/scale-dialog.png)

    Ehhez a kísérlethez adatokat tölt be az Azure SQL Data Warehouse használatával `xlargerc` erőforrásosztály.

    Ajánlott lehetséges átviteli sebesség eléréséhez másolási kell tartozó SQL Data Warehouse felhasználó hajtható végre `xlargerc` erőforrásosztály.  Útmutató a következő ehhez [módosíthatja a felhasználói erőforrás osztály példa](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).  
* Cél táblaséma létrehozása az Azure SQL Data Warehouse-adatbázis, a következő DDL-utasítás futtatásával:

    ```SQL  
    CREATE TABLE [dbo].[lineitem]
    (
        [L_ORDERKEY] [bigint] NOT NULL,
        [L_PARTKEY] [bigint] NOT NULL,
        [L_SUPPKEY] [bigint] NOT NULL,
        [L_LINENUMBER] [int] NOT NULL,
        [L_QUANTITY] [decimal](15, 2) NULL,
        [L_EXTENDEDPRICE] [decimal](15, 2) NULL,
        [L_DISCOUNT] [decimal](15, 2) NULL,
        [L_TAX] [decimal](15, 2) NULL,
        [L_RETURNFLAG] [char](1) NULL,
        [L_LINESTATUS] [char](1) NULL,
        [L_SHIPDATE] [date] NULL,
        [L_COMMITDATE] [date] NULL,
        [L_RECEIPTDATE] [date] NULL,
        [L_SHIPINSTRUCT] [char](25) NULL,
        [L_SHIPMODE] [char](10) NULL,
        [L_COMMENT] [varchar](44) NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    ```
Az előfeltételként szükséges lépéseket, befejeződött azt most már készen áll arra a másolási tevékenység során a másolása varázsló segítségével konfigurálja.

## <a name="launch-copy-wizard"></a>A Másolás varázsló indítása
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Kattintson **hozzon létre egy erőforrást** kattintson a bal felső sarkának **Eszközintelligencia + analitika**, és kattintson **Data Factory**.
3. Az a **új adat-előállító** panelen:

   1. Adja meg **LoadIntoSQLDWDataFactory** a a **neve**.
       Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a hibaüzenetet kapja: **nem érhető el adat-előállító "LoadIntoSQLDWDataFactory"**, változtassa meg az adat-előállítóban (például yournameLoadIntoSQLDWDataFactory) nevét, és próbálja meg újra létrehozni. A Data Factory-összetevők elnevezési szabályait a [Data Factory - Naming Rules](data-factory-naming-rules.md) (Data Factory – Elnevezési szabályok) című témakörben találhatja.  
   2. Jelölje ki az Azure-**előfizetést**.
   3. Az erőforráscsoportban hajtsa végre a következő lépések egyikét:
      1. Meglévő erőforráscsoport kiválasztásához kattintson a **Use existing** (Meglévő használata) elemre.
      2. Az erőforráscsoport nevének megadásához válassza ki a **Create new** (Új létrehozása) lehetőséget.
   4. Válassza ki a Data Factory **helyét**.
   5. A panel alján jelölje be a **Pin to dashboard** (Rögzítés az irányítópulton) jelölőnégyzetet.  
   6. Kattintson a **Create** (Létrehozás) gombra.
4. A létrehozás befejezése után a **Data Factory** panel a következő képen látható módon jelenik meg:

   ![Data factory kezdőlap](media/data-factory-load-sql-data-warehouse/data-factory-home-page-copy-data.png)
5. A Data Factory kezdőlapján kattintson a **Copy data** (Adatok másolása) csempére a **Copy Wizard** (Másolás varázsló) elindításához.

   > [!NOTE]
   > Ha a böngésző azt jeleníti meg, hogy „Authorizing...” (Hitelesítés), és nem lép tovább, tiltsa le a **Block third party cookies and site data** (Harmadik féltől származó cookie-k és webhelyadatok tiltása) jelölőnégyzetet, illetve törölje a jelölését, vagy hagyja engedélyezve, és hozzon létre kivételt a **login.microsoftonline.com** webhelyhez, majd próbálkozzon ismét a varázsló elindításával.
   >
   >

## <a name="step-1-configure-data-loading-schedule"></a>1. lépés: Adatok betöltése az ütemezés konfigurálása
Az első lépés az adatok betöltése az ütemezés konfigurálása.  

A **Properties** (Tulajdonságok) oldalon:

1. Adja meg **CopyFromBlobToAzureSqlDataWarehouse** a **feladat neve**
2. Válassza ki **futtassa egyszer most** lehetőséget.   
3. Kattintson a **Tovább** gombra.  

    ![Másolása varázsló – Tulajdonságok lap](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>2. lépés: Állítson be forrás
Ez a szakasz bemutatja, hogy a forrás konfigurálásának lépéseit: az 1 TB méretű TPC tartalmazó Azure Blob-H sorelemet fájlokat.

1. Válassza ki a **Azure Blob Storage** módon tárolja az adatokat, majd kattintson a **következő**.

    ![Másolása varázsló – forrás kiválasztása lap](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

2. Az Azure Blob storage-fiók kapcsolati adatait, majd kattintson **következő**.

    ![Másolása varázsló – adatforrás kapcsolati adatait](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

3. Válassza ki a **mappa** elem fájl. sor, majd kattintson a TPC-H tartalmazó **következő**.

    ![Másolása varázsló – a bemeneti mappa kiválasztása](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

4. Gomb megnyomásakor **következő**, a rendszer automatikusan észleli a fájl formátuma beállításokat.  Győződjön meg arról, hogy oszlop elválasztó ' |} "helyett az alapértelmezett vesszővel", ".  Kattintson a **következő** után meg kell megtekintésekor az adatokat.

    ![Másolása varázsló – fájl formázási beállítások](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>3. lépés: A célkiszolgáló konfigurálása
Ez a szakasz azt ismerteti, hogyan konfigurálja a célt: `lineitem` az Azure SQL Data Warehouse-adatbázis táblája.

1. Válasszon **Azure SQL Data Warehouse** célhelyként tárolja, és kattintson a **következő**.

    ![Másolása varázsló – a célkiszolgáló kijelölése adattár](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

2. Töltse ki az Azure SQL Data Warehouse-kapcsolódási információt.  Ellenőrizze, hogy a felhasználó, aki tagja a szerepkörnek meg `xlargerc` (lásd: a **Előfeltételek** részben, részletes), és kattintson a **következő**.

    ![Másolása varázsló – cél Kapcsolatinformáció](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

3. Válassza ki a célként megadott táblája, és kattintson a **következő**.

    ![Másolása varázsló – leképezés lapja](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

4. A séma leképezési lapon hagyja "Alkalmazza oszlopleképezés" beállítás nincs bejelölve, és kattintson a **következő**.

## <a name="step-4-performance-settings"></a>4. lépés: Teljesítményadat-beállításai

**A polybase lehetővé** alapértelmezés szerint be van jelölve.  Kattintson a **Tovább** gombra.

![Másolása varázsló – séma leképezési lap](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>5. lépés: Telepítheti, és figyelheti a terhelést eredmények
1. Kattintson a **Befejezés** telepítése gombra.

    ![Másolása varázsló – összegző lapja](media/data-factory-load-sql-data-warehouse/summary-page.png)

2. A telepítés befejezése után kattintson `Click here to monitor copy pipeline` figyelése a másolási folyamat futtatásához. Válassza ki a létrehozott másolási folyamat a **tevékenység Windows** listája.

    ![Másolása varázsló – összegző lapja](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

    Megtekintheti a másolat részletek futtassa a **tevékenység ablak Explorer** a jobb oldali panelen, beleértve az adatmennyiség forrásból olvashatók és írhatók a cél, időtartam és a futtatáshoz átlagos átviteli sebessége.

    Amint az alábbi képernyőfelvételen látható, 14 perc, hatékonyan a 1.22 GB/s átviteli sebesség eléréséhez 1 TB-os másolása az Azure Blob Storage-ból az SQL Data Warehouse tartott!

    ![Másolása varázsló – sikeres párbeszédpanel](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>Ajánlott eljárások
Az alábbiakban néhány gyakorlati tanácsok az Azure SQL Data Warehouse-adatbázis futtatásához:

* Használjon nagyobb erőforrásosztály FÜRTÖZÖTT OSZLOPCENTRIKUS INDEX vezérlőbe való betöltés.
* Hatékonyabb táblákra érdemes lehet kivonatoló terjesztési round robin terjesztési alapértelmezett helyett válassza oszlop szerint.
* Betöltési sebesség gyorsabb érdemes halommemória az átmeneti adatok.
* Statisztikák létrehozása az Azure SQL Data Warehouse betöltése után.

Lásd: [ajánlott eljárások az Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-best-practices.md) részleteiről.

## <a name="next-steps"></a>További lépések
* [Data Factory másolása varázsló](data-factory-copy-wizard.md) – Ez a cikk ismerteti a varázsló.
* [Másolja a tevékenység teljesítmény- és hangolási útmutató](data-factory-copy-activity-performance.md) -Ez a cikk hivatkozás TELJESÍTMÉNYMÉRÉSEK és hangolási útmutató tartalmazza.
