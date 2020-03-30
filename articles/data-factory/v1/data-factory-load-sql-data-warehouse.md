---
title: Több terabájtnyi adat betöltése az SQL Data Warehouse-ba
description: Bemutatja, hogyan tölthető be 1 TB adat az Azure SQL Data Warehouse-ba 15 perc alatt az Azure Data Factory segítségével
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: a6c133c0-ced2-463c-86f0-a07b00c9e37f
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 079d29c241cfbbdcc991f024c07b07b378670c10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80130885"
---
# <a name="load-1-tb-into-azure-sql-data-warehouse-under-15-minutes-with-data-factory"></a>1 TB betöltése az Azure SQL Data Warehouse-ba 15 perc alatt a Data Factory segítségével
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el az [Adatok másolása az Azure SQL Data Warehouse-ba vagy onnan a Data Factory használatával című témakört.](../connector-azure-sql-data-warehouse.md)


[Az Azure SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) egy felhőalapú, kibővített adatbázis, amely képes nagy mennyiségű adat feldolgozására, relációs és nem relációs.  A masszívan párhuzamos feldolgozási (MPP) architektúrára épülő SQL Data Warehouse vállalati adattárház-számítási feladatokra van optimalizálva.  Felhőrugalmasságot kínál a tárhely és az önálló számítási kapacitás méretezésének rugalmasságával.

Az Azure SQL Data Warehouse használatának első lépései minden eddiginél egyszerűbbek az **Azure Data Factory**használatával.  Az Azure Data Factory egy teljes körűen felügyelt felhőalapú adatintegrációs szolgáltatás, amely segítségével feltöltheti az SQL Data Warehouse-t a meglévő rendszerből származó adatokkal, és értékes időt takaríthat meg az SQL Data Warehouse kiértékelése és az elemzések létrehozása során Megoldások. Az Azure Data Factory használatával az adatok Azure SQL Data Warehouse szolgáltatásba való betöltésének legfontosabb előnyei:

* **Könnyen beállítható:** 5 lépéses intuitív varázsló, amely nem szükséges parancsfájlok futtatására.
* **Gazdag adattár támogatás:** beépített támogatás a helyszíni és felhőalapú adattárak gazdag készletéhez.
* **Biztonságos és megfelelő:** az adatok ÁTVITELE HTTPS-en vagy ExpressRoute-on keresztül történik, és a globális szolgáltatásjelenlét biztosítja, hogy az adatok soha ne hagyja el a földrajzi határt
* **Páratlan teljesítmény a PolyBase használatával** – A Polybase használata a leghatékonyabb módja az adatok Azure SQL Data Warehouse-ba való áthelyezésének. Az átmeneti blob funkció használatával az Azure Blob storage mellett az összes adattárból nagy terhelési sebességet érhet el, amelyet a Polybase alapértelmezés szerint támogat.

Ez a cikk bemutatja, hogyan használhatja a Data Factory Copy Wizard 1-TB-os adatokat az Azure Blob Storage-ból az Azure SQL Data Warehouse-ba 15 percen belül, több mint 1,2 GBps átviteli sebességgel.

Ez a cikk lépésenkénti útmutatást nyújt az adatok Azure SQL Data Warehouse-ba való áthelyezéséhez a Másolás varázsló használatával.

> [!NOTE]
>  Az Azure SQL Data Warehouse-ba történő adatáthelyezési és -áthelyezési a Data Factory funkcióiról az [Adatok áthelyezése az Azure SQL Data Warehouse-ba és onnan az Azure Data Factory](data-factory-azure-sql-data-warehouse-connector.md) cikk használatával című témakörben olvashat.
>
> Folyamatokat is hozhat létre a Visual Studio, a PowerShell stb. Tekintse meg [az oktatóanyagot: Adatok másolása](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) az Azure Blobból az Azure SQL Database-be egy gyors forgatókönyvért, amely részletes útmutatást tartalmaz az Azure Data Factory másolási tevékenységének használatával kapcsolatban.  
>
>

## <a name="prerequisites"></a>Előfeltételek
* Azure Blob Storage: ez a kísérlet az Azure Blob Storage (GRS) a TPC-H tesztelési adatkészlet tárolására.  Ha nem rendelkezik Azure-tárfiókkal, ismerje meg, [hogyan hozhat létre tárfiókot.](../../storage/common/storage-account-create.md)
* [TPC-H](http://www.tpc.org/tpch/) adatok: a TPC-H-t fogjuk használni tesztelési adatkészletként.  Ehhez a TPC-H `dbgen` eszközkészletből kell használnia, amely segít az adatkészlet létrehozásában.  Letöltheti a forráskódot `dbgen` a [TPC](http://www.tpc.org/tpc_documents_current_versions/current_specifications.asp) Tools-ból, és lefordíthatja saját maga, vagy letöltheti a lefordított binárist a [GitHubról](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TPCHTools).  Futtassa a dbgen.exe fájlt a következő `lineitem` parancsokkal, hogy 1 TB-os síkfájlt hozzon létre a 10 fájlra elosztva lévő táblázathoz:

  * `Dbgen -s 1000 -S **1** -C 10 -T L -v`
  * `Dbgen -s 1000 -S **2** -C 10 -T L -v`
  * …
  * `Dbgen -s 1000 -S **10** -C 10 -T L -v`

    Most másolja a létrehozott fájlokat az Azure Blobba.  Tekintse meg az [adatok áthelyezése egy helyszíni fájlrendszer az Azure Data Factory segítségével,](data-factory-onprem-file-system-connector.md) hogy hogyan kell csinálni az ADF-másolás használatával.    
* Azure SQL Data Warehouse: ez a kísérlet betölti az adatokat a 6000 DWUs-szal létrehozott Azure SQL Data Warehouse-ba

    Az [SQL Data](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) Warehouse-adatbázis létrehozásáról az Azure SQL Data Warehouse létrehozása című dokumentumban talál részletes útmutatást.  Annak érdekében, hogy a lehető legjobb terhelési teljesítményt nyújtsuk az SQL Data Warehouse-ba a Polybase használatával, a Teljesítmény beállításban engedélyezett adattárházegységek maximális számát választjuk ki, amely 6000 DWUs.

  > [!NOTE]
  > Az Azure Blobból történő betöltéskor az adatok betöltési teljesítménye közvetlenül arányos az SQL Data Warehouse-ban konfigurált DUS-ok számával:
  >
  > 1 TB betöltése 1000 DWU SQL Data Warehouse-ba 87 percet vesz igénybe (~200 Mb/s átviteli sebesség) 1 TB betöltése 2000 DWU SQL Adattárházba 46 percet vesz igénybe (~380 Mb/s átviteli sebesség) 1 TB betöltése 6000 DWU SQL Adattárházba 14 percet vesz igénybe (~1,2 GBps átviteli sebesség)
  >
  >

    6000 DWUs-szal rendelkező SQL Data Warehouse létrehozásához mozgassa a Teljesítmény csúszkát jobbra:

    ![Teljesítmény csúszka](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    Egy meglévő adatbázis, amely nincs konfigurálva 6000 DWUs, az Azure Portal használatával skálázhatja azt.  Keresse meg az adatbázist az Azure Portalon, és van egy **Méretezés** gomb az **Áttekintés** panelen az alábbi képen látható:

    ![Méretezés gomb](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    Kattintson a **Méretezés** gombra a következő panel megnyitásához, húzza a csúszkát a maximális értékre, és kattintson a **Mentés** gombra.

    ![Méretezés párbeszédpanel](media/data-factory-load-sql-data-warehouse/scale-dialog.png)

    Ez a kísérlet betölti az `xlargerc` adatokat az Azure SQL Data Warehouse erőforrásosztály használatával.

    A lehető legjobb átviteli kapacitás elérése érdekében a másolást `xlargerc` az erőforrásosztályhoz tartozó SQL Data Warehouse-felhasználó használatával kell elvégezni.  Ebből megtudhatja, hogyan teheti ezt meg [a Felhasználói erőforrásosztály módosítása példában.](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md)  
* Hozzon létre céltábla-sémát az Azure SQL Data Warehouse adatbázisában a következő DDL-utasítás futtatásával:

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
  Az előfeltételi lépések végrehajtásával készen állunk a másolási tevékenység konfigurálására a Másolás varázslóval.

## <a name="launch-copy-wizard"></a>A Másolás varázsló indítása
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Kattintson az **Erőforrás létrehozása** a bal felső sarokban, majd az **Intelligencia + elemzés**elemre, majd a Data **Factory**parancsra.
3. Az **Új adatgyár** ablaktáblában:

   1. Írja be **a LoadIntoSQLDWDataFactory nevet** a **nevéhez.**
       Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenet jelenik meg: **A "LoadIntoSQLDWDataFactory" adatgyári név nem érhető el,** módosítsa az adatgyár nevét (például yournameLoadIntoSQLDWDataFactory), majd próbálkozzon újra a létrehozással. A Data Factory-összetevők elnevezési szabályait a [Data Factory - Naming Rules](data-factory-naming-rules.md) (Data Factory – Elnevezési szabályok) című témakörben találhatja.  
   2. Válassza ki **Azure-előfizetését.**
   3. Az erőforráscsoportban hajtsa végre a következő lépések egyikét:
      1. Meglévő erőforráscsoport kiválasztásához kattintson a **Use existing** (Meglévő használata) elemre.
      2. Az erőforráscsoport nevének megadásához válassza ki a **Create new** (Új létrehozása) lehetőséget.
   4. Válassza ki a Data Factory **helyét**.
   5. A panel alján jelölje be a **Pin to dashboard** (Rögzítés az irányítópulton) jelölőnégyzetet.  
   6. Kattintson **a Létrehozás gombra.**
4. A létrehozás befejezése után megjelenik a **Data Factory** panel az alábbi képen látható módon:

   ![Data factory kezdőlap](media/data-factory-load-sql-data-warehouse/data-factory-home-page-copy-data.png)
5. A Data Factory kezdőlapján kattintson a **Copy data** (Adatok másolása) csempére a **Copy Wizard** (Másolás varázsló) elindításához.

   > [!NOTE]
   > Ha a böngésző azt jeleníti meg, hogy „Authorizing...” (Hitelesítés), és nem lép tovább, tiltsa le a **Block third party cookies and site data** (Harmadik féltől származó cookie-k és webhelyadatok tiltása) jelölőnégyzetet, illetve törölje a jelölését, vagy hagyja engedélyezve, és hozzon létre kivételt a **login.microsoftonline.com** webhelyhez, majd próbálkozzon ismét a varázsló elindításával.
   >
   >

## <a name="step-1-configure-data-loading-schedule"></a>1. lépés: Az adatok betöltési ütemezésének konfigurálása
Az első lépés az adatbetöltési ütemezés konfigurálása.  

A **Properties** (Tulajdonságok) oldalon:

1. Írja be **a CopyFromBlobToAzureSqlDataWarehouse értéket** a **feladat nevéhez**
2. Válassza **a Futtatás egyszer most** lehetőséget.   
3. Kattintson a **Tovább** gombra.  

    ![Másolás varázsló – Tulajdonságok lap](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>2. lépés: Forrás konfigurálása
Ez a szakasz a forrás konfigurálásának lépéseit mutatja be: az 1 TB-H-s tpc-H sorfájlokat tartalmazó Azure Blob.

1. Válassza ki az **Azure Blob Storage-t** adattárként, és kattintson a **Tovább**gombra.

    ![Másolás varázsló – Forráslap kiválasztása](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

2. Töltse ki az Azure Blob tárfiók csatlakozási adatait, majd kattintson a **Tovább**gombra.

    ![Másolás varázsló – Forráskapcsolat adatai](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

3. Válassza ki a TPC-H sorelemek fájljait tartalmazó **mappát,** majd kattintson a **Tovább**gombra.

    ![Másolás varázsló – beviteli mappa kijelölése](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

4. A **Tovább**gombra kattintva a fájlformátum-beállításokat a rendszer automatikusan észleli.  Ellenőrizze, hogy az oszlophatároló "|" az alapértelmezett ""-mező helyett "|"-os.  Az adatok megtekintése után kattintson a **Tovább** gombra.

    ![Másolás varázsló – fájlformátum-beállítások](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>3. lépés: Cél konfigurálása
Ez a szakasz bemutatja, `lineitem` hogyan konfigurálhatja a cél: tábla az Azure SQL Data Warehouse adatbázisban.

1. Válassza az **Azure SQL Data Warehouse** lehetőséget céltárolóként, majd kattintson a **Tovább**gombra.

    ![Másolás varázsló – céladattár kiválasztása](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

2. Töltse ki az Azure SQL Data Warehouse kapcsolatadatait.  Győződjön meg arról, hogy megadja `xlargerc` a szerepkörben tagsággal rendelkező felhasználót (a részletes utasításokat lásd az **előfeltételek** című szakaszban), majd kattintson a **Tovább**gombra.

    ![Másolás varázsló – célkapcsolat adatai](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

3. Válassza ki a céltáblát, és kattintson a **Tovább gombra.**

    ![Másolás varázsló – táblahozzárendelési lap](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

4. A Sémaleképezés lapon hagyja bejelölve az "Oszlopleképezés alkalmazása" beállítást, majd kattintson a **Tovább**gombra.

## <a name="step-4-performance-settings"></a>4. lépés: Teljesítménybeállítások

**A polibázis engedélyezése** alapértelmezés szerint be van jelölve.  Kattintson a **Tovább** gombra.

![Másolás varázsló – sémaleképezési lap](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>5. lépés: A terhelési eredmények üzembe helyezése és figyelése
1. Az üzembe helyezéshez kattintson a **Befejezés** gombra.

    ![Másolás varázsló – összefoglaló lap](media/data-factory-load-sql-data-warehouse/summary-page.png)

2. A központi telepítés befejezése `Click here to monitor copy pipeline` után kattintson ide a másolási futtatás folyamatának figyeléséhez. Válassza ki a létrehozott másolási folyamatot a **Tevékenység Windows** listában.

    ![Másolás varázsló – összefoglaló lap](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

    A másolási futtatás részleteit a jobb oldali **panelen, a Tevékenységablak-kezelőben** tekintheti meg, beleértve a forrásból beolvasott és a célba írt adatmennyiséget, az időtartamot és a futtatás átlagos átviteli mertéjét.

    Amint az a következő képernyőképen látható, az 1 TB másolása az Azure Blob Storage-ból az SQL Data Warehouse-ba 14 percet vett igénybe, hatékonyan elérve az 1,22 GBps átviteli sebességet!

    ![Másolás varázsló – sikeres párbeszédpanel](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>Ajánlott eljárások
Íme néhány gyakorlati tanács az Azure SQL Data Warehouse-adatbázis futtatásához:

* Nagyobb erőforrásosztály használata fürtözött COLUMNSTORE INDEX betöltésekor.
* A hatékonyabb illesztések érdekében fontolja meg a kivonatterjesztés használatát egy kiválasztott oszlop által az alapértelmezett ciklikus multiplexelés helyett.
* A gyorsabb betöltési sebesség érdekében fontolja meg a halom memóriakupaco használatát az átmeneti adatokhoz.
* Az Azure SQL Data Warehouse betöltésének befejezése után hozzon létre statisztikákat.

A részleteket [az Azure SQL Data Warehouse ajánlott eljárások](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-best-practices.md) című témakörben találja.

## <a name="next-steps"></a>További lépések
* [Adatfeldolgozó másolat varázsló](data-factory-copy-wizard.md) – Ez a cikk a Másolás varázslóval kapcsolatos részleteket tartalmazza.
* [Tevékenység teljesítményének és finomhangolási útmutatójának másolása](data-factory-copy-activity-performance.md) – Ez a cikk a referenciateljesítmény-méréseket és a finomhangolási útmutatót tartalmazza.
