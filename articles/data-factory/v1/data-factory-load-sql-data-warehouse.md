---
title: Terabájtos adatterhelés SQL Data Warehouseba
description: Azt mutatja be, hogyan tölthető 1 TB adat a Azure SQL Data Warehouse 15 perc alatt Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: a6c133c0-ced2-463c-86f0-a07b00c9e37f
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 05dcff2276a799b1debc76e4f85fbbac6606eb59
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682553"
---
# <a name="load-1-tb-into-azure-sql-data-warehouse-under-15-minutes-with-data-factory"></a>Töltse be az 1 TB-ot Azure SQL Data Warehouse 15 perc alatt Data Factory
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el a következő témakört: [adatok másolása Azure SQL Data Warehouseba vagy a Data Factory használatával](../connector-azure-sql-data-warehouse.md).


A [Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) egy felhőalapú, kibővíthető adatbázis, amely képes nagy mennyiségű, a kapcsolatok és a nem kapcsolatok kezelésére.  A nagymértékben párhuzamos feldolgozási (MPP) architektúrára épülő SQL Data Warehouse a vállalati adattárház számítási feladataihoz van optimalizálva.  A felhő rugalmasságának köszönhetően rugalmasan méretezheti a tárolást és a számításokat egymástól függetlenül.

A Azure SQL Data Warehouse használatának első lépései mostantól minden eddiginél könnyebben **Azure Data Factory**.  A Azure Data Factory egy teljes körűen felügyelt felhőalapú adatintegrációs szolgáltatás, amely felhasználható egy SQL Data Warehouse a meglévő rendszerből származó adatokkal való feltöltésére, és értékes időt takaríthat meg a SQL Data Warehouse kiértékelése és az elemzések kiépítése során. megoldások. Az alábbi fő előnyökkel jár az adatAzure SQL Data Warehouse Azure Data Factory használatával történő betöltésének főbb előnyei:

* **Könnyen beállítható**: 5 lépésből álló intuitív varázsló, amely nem igényel parancsfájlt.
* **Gazdag adattár-támogatás**: beépített támogatás a helyszíni és felhőalapú adattárak gazdag készletéhez.
* **Biztonságos és megfelelő**: az adatátvitel HTTPS-vagy ExpressRoute keresztül történik, és a globális szolgáltatás jelenléte biztosítja, hogy az adatai soha nem hagyják el a földrajzi határt
* **Páratlan teljesítmény a Base használatával** – a Base használata az adatok Azure SQL Data Warehouseba való áthelyezésének leghatékonyabb módja. Az átmeneti blob funkcióval nagy terhelési sebességet érhet el az Azure Blob Storage melletti összes adattárolóból, amelyet alapértelmezés szerint a Base támogat.

Ebből a cikkből megtudhatja, hogyan használhatja a Data Factory másolási varázslót az 1 TB-os adatok Azure-Azure SQL Data Warehouse Blob Storageból való betöltésére 15 percen belül, 1,2 GB/s sebességnél.

Ez a cikk részletes útmutatást nyújt az adatAzure SQL Data Warehouseba való áthelyezéséhez a másolás varázsló segítségével.

> [!NOTE]
>  Az adatok Azure SQL Data Warehouseba való áthelyezésével Data Factory képességeivel kapcsolatos általános információkért lásd: [adatok áthelyezése a Azure SQL Data Warehousebe és a Azure Data Factory cikk használatával](data-factory-azure-sql-data-warehouse-connector.md) .
>
> A Visual Studióval, a PowerShell-lel és más szolgáltatásokkal is készíthet folyamatokat. Tekintse meg az [oktatóanyag: adatok másolása az Azure blobból Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) egy gyors útmutatóhoz, amely részletes útmutatást nyújt a másolási tevékenység Azure Data Factory-ban való használatához.  
>
>

## <a name="prerequisites"></a>Előfeltételek
* Azure Blob Storage: Ez a kísérlet az Azure Blob Storaget (GRS) használja a TPC-H tesztelési adatkészlet tárolásához.  Ha nem rendelkezik Azure Storage-fiókkal, Ismerje meg, [hogyan hozhat létre egy Storage](../../storage/common/storage-quickstart-create-account.md)-fiókot.
* [TPC-h-](http://www.tpc.org/tpch/) adat: a TPC-h-t a tesztelési adatkészletként fogjuk használni.  Ehhez `dbgen`t kell használnia a TPC-H eszközkészletből, amely segítséget nyújt az adatkészlet létrehozásához.  Letöltheti `dbgen` a [TPC-eszközökről](http://www.tpc.org/tpc_documents_current_versions/current_specifications.asp) , és lefordíthatja saját maga is, vagy letöltheti a lefordított bináris fájlt a [githubról](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TPCHTools).  Futtassa a dbgen. exe fájlt a következő parancsokkal, hogy 1 TB-os, `lineitem`-táblázathoz tartozó, 10 fájlból kiosztott adatfájlt létrehozzon:

  * `Dbgen -s 1000 -S **1** -C 10 -T L -v`
  * `Dbgen -s 1000 -S **2** -C 10 -T L -v`
  * ...
  * `Dbgen -s 1000 -S **10** -C 10 -T L -v`

    Most másolja a generált fájlokat az Azure-Blobba.  Tekintse át az adatok átvitele [a helyszíni fájlrendszerbe és a rendszerből a Azure Data Factory használatával](data-factory-onprem-file-system-connector.md) című témakört, amely az ADF-másolás használatával végezhető el.    
* Azure SQL Data Warehouse: Ez a kísérlet az 6 000 DWU-mel létrehozott Azure SQL Data Warehouseba tölti be az adatgyűjtést

    SQL Data Warehouse-adatbázis létrehozásával kapcsolatos részletes utasításokért tekintse meg a [Azure SQL Data Warehouse létrehozása](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) című témakört.  Annak érdekében, hogy a lehető leghatékonyabban töltse be a terhelést SQL Data Warehouse a Base használatával, a teljesítmény beállításban engedélyezett maximális számú adatraktár-egység (DWU) közül választhat, amely 6 000 DWU.

  > [!NOTE]
  > Az Azure Blobból való betöltéskor az adatok betöltésének teljesítménye közvetlenül a SQL Data Warehouse konfigurált DWU számával arányos:
  >
  > Az 1 TB betöltése a 1 000 DWU SQL Data Warehouse a 87 percet (~ 200 MBps átviteli sebességre) helyezi át az 1 TB-ot a 2 000 DWU-be, SQL Data Warehouse az 1 TB-ot az 46 SQL Data Warehouse DWU-be (~ 380 MBps átviteli sebesség) tölti be.
  >
  >

    A 6 000 DWU rendelkező SQL Data Warehouse létrehozásához mozgassa a teljesítmény csúszkát jobbra a jobb oldalon:

    ![Teljesítmény csúszka](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    Egy olyan meglévő adatbázis esetében, amely nem 6 000 DWU van konfigurálva, Azure Portal használatával méretezheti fel.  Navigáljon a Azure Portal található adatbázishoz, és van egy **méretezési** gomb az **Áttekintés** panelen az alábbi képen látható módon:

    ![Méretezés gomb](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    Kattintson a **skála** gombra a következő panel megnyitásához, mozgassa a csúszkát a maximális értékre, majd kattintson a **Save (Mentés** ) gombra.

    ![Méretezési párbeszédpanel](media/data-factory-load-sql-data-warehouse/scale-dialog.png)

    Ez a kísérlet `xlargerc` Resource osztály használatával tölti be az Azure SQL Data Warehouse.

    A lehető legjobb átviteli sebesség eléréséhez a másolást `xlargerc` Resource osztályhoz tartozó SQL Data Warehouse felhasználó használatával kell elvégezni.  Ebből a témakörből megtudhatja, hogyan teheti ezt meg a [felhasználói erőforrás osztályra vonatkozó példa módosítása](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md)után.  
* Hozzon létre egy céltáblabeli sémát Azure SQL Data Warehouse adatbázisban a következő DDL-utasítás futtatásával:

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
  Az előfeltételként szükséges lépések elvégzésével most már készen áll a másolási tevékenység konfigurálására a másolás varázslóval.

## <a name="launch-copy-wizard"></a>A Másolás varázsló indítása
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Kattintson az **erőforrás létrehozása** elemre a bal felső sarokban, kattintson az **intelligencia és Analitika**elemre, majd a **Data Factory**elemre.
3. Az **új adatgyár** panelen:

   1. Adja meg a LoadIntoSQLDWDataFactory **nevet**.
       Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenetet kapja: a **"LoadIntoSQLDWDataFactory" nem érhető el**az adatfeldolgozó neve, módosítsa az adatgyár nevét (például yournameLoadIntoSQLDWDataFactory), és próbálkozzon újra a létrehozással. A Data Factory-összetevők elnevezési szabályait a [Data Factory - Naming Rules](data-factory-naming-rules.md) (Data Factory – Elnevezési szabályok) című témakörben találhatja.  
   2. Jelölje ki az Azure-**előfizetést**.
   3. Az erőforráscsoportban hajtsa végre a következő lépések egyikét:
      1. Meglévő erőforráscsoport kiválasztásához kattintson a **Use existing** (Meglévő használata) elemre.
      2. Az erőforráscsoport nevének megadásához válassza ki a **Create new** (Új létrehozása) lehetőséget.
   4. Válassza ki a Data Factory **helyét**.
   5. A panel alján jelölje be a **Pin to dashboard** (Rögzítés az irányítópulton) jelölőnégyzetet.  
   6. Kattintson a **Létrehozás** elemre.
4. A létrehozás befejezése után a **Data Factory** panel a következő képen látható módon jelenik meg:

   ![Data factory kezdőlap](media/data-factory-load-sql-data-warehouse/data-factory-home-page-copy-data.png)
5. A Data Factory kezdőlapján kattintson a **Copy data** (Adatok másolása) csempére a **Copy Wizard** (Másolás varázsló) elindításához.

   > [!NOTE]
   > Ha a böngésző azt jeleníti meg, hogy „Authorizing...” (Hitelesítés), és nem lép tovább, tiltsa le a **Block third party cookies and site data** (Harmadik féltől származó cookie-k és webhelyadatok tiltása) jelölőnégyzetet, illetve törölje a jelölését, vagy hagyja engedélyezve, és hozzon létre kivételt a **login.microsoftonline.com** webhelyhez, majd próbálkozzon ismét a varázsló elindításával.
   >
   >

## <a name="step-1-configure-data-loading-schedule"></a>1\. lépés: az betöltési ütemterv konfigurálása
Az első lépés az betöltési ütemterv konfigurálása.  

A **Properties** (Tulajdonságok) oldalon:

1. Adja meg a **CopyFromBlobToAzureSqlDataWarehouse** a **feladat neveként**
2. Válassza a **Futtatás egyszer már** lehetőséget.   
3. Kattintson a **Tovább** gombra.  

    ![Másolás varázsló – Tulajdonságok lap](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>2\. lépés: a forrás konfigurálása
Ez a szakasz a forrás konfigurálásának lépéseit mutatja be: az Azure-Blob az 1 TB-os TPC-H soros elemek fájljait tartalmazza.

1. Válassza ki az **Azure Blob Storage** adattáraként, majd kattintson a **tovább**gombra.

    ![Másolási varázsló – forrás kiválasztása lap](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

2. Adja meg az Azure Blob Storage-fiókhoz tartozó kapcsolatok adatait, és kattintson a **tovább**gombra.

    ![Másolás varázsló – forrásként szolgáló kapcsolatok adatai](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

3. Válassza ki a TPC-H sor elem fájljait tartalmazó **mappát** , és kattintson a **tovább**gombra.

    ![Másolás varázsló – bemeneti mappa kiválasztása](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

4. A **tovább**gombra kattintás után a rendszer automatikusan észleli a fájlformátum beállításait.  Győződjön meg arról, hogy az oszlop elválasztója "|" az alapértelmezett vessző "," helyett.  Az adatmegjelenítés után kattintson a **tovább** gombra.

    ![Másolás varázsló – fájlformátum-beállítások](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>3\. lépés: a cél konfigurálása
Ez a szakasz bemutatja, hogyan konfigurálhatja a cél: `lineitem` táblát a Azure SQL Data Warehouse adatbázisban.

1. Válassza a **Azure SQL Data Warehouse** lehetőséget a cél tárolóként, majd kattintson a **tovább**gombra.

    ![Másolási varázsló – cél adattár kiválasztása](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

2. Adja meg a Azure SQL Data Warehousehoz tartozó kapcsolatok adatait.  Győződjön meg arról, hogy a szerepkör `xlargerc` tagja (lásd az **Előfeltételek** című szakaszt a részletes utasításokhoz), és kattintson a **tovább**gombra.

    ![Másolási varázsló – cél kapcsolatok adatai](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

3. Válassza ki a cél táblát, és kattintson a **tovább**gombra.

    ![Másolás varázsló – tábla-hozzárendelés lap](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

4. A séma-hozzárendelés lapon hagyja bejelölés nélkül az "oszlop-hozzárendelés alkalmazása" lehetőséget, és kattintson a **tovább**gombra.

## <a name="step-4-performance-settings"></a>4\. lépés: a teljesítmény beállításai

Alapértelmezés szerint be van jelölve a " **Base** " jelölőnégyzet.  Kattintson a **Tovább** gombra.

![Másolás varázsló – séma leképezése lap](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>5\. lépés: a betöltési eredmények üzembe helyezése és figyelése
1. A telepítéshez kattintson a **Befejezés** gombra.

    ![Másolás varázsló – összefoglalás lap](media/data-factory-load-sql-data-warehouse/summary-page.png)

2. Az üzembe helyezés befejezése után kattintson a `Click here to monitor copy pipeline` elemre a másolás futtatási folyamatának figyeléséhez. Válassza ki a létrehozott másolási folyamatot a **tevékenység Windows** listájában.

    ![Másolás varázsló – összefoglalás lap](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

    A másolási művelet részleteit a jobb oldali panelen, a **tevékenység ablak-kezelőben** tekintheti meg, beleértve a forrásból beolvasott adatmennyiséget, valamint a célhelyre, az időtartamra és a Futtatás átlagos átviteli sebességére vonatkozó adatokat.

    Ahogy az alábbi képernyőképen is látható, az 1 TB-ot az Azure Blob Storageba másolja SQL Data Warehouse 14 percet vett igénybe, ami gyakorlatilag 1,22 GB/s sebesség elérését teszi elérhetővé.

    ![Másolás varázsló – sikeres párbeszédpanel](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>Ajánlott eljárások
Íme néhány ajánlott eljárás a Azure SQL Data Warehouse-adatbázis futtatásához:

* FÜRTÖZÖTT OSZLOPCENTRIKUS-INDEXbe való betöltéskor használjon nagyobb erőforrás-osztályt.
* A hatékonyabb összekapcsoláshoz érdemes lehet a kivonatoló eloszlást egy Select oszlop használatával használni az alapértelmezett ciklikus multiplexelés-eloszlás helyett.
* A gyorsabb betöltési sebesség érdekében érdemes lehet a kupacot használni az átmeneti adatértékekhez.
* Statisztikák létrehozása a Azure SQL Data Warehouse betöltésének befejezése után.

Részletekért tekintse [meg Azure SQL Data Warehouse ajánlott eljárásait](../../sql-data-warehouse/sql-data-warehouse-best-practices.md) .

## <a name="next-steps"></a>További lépések
* [Data Factory másolási varázsló](data-factory-copy-wizard.md) – ez a cikk a másolás varázsló részleteit tartalmazza.
* [Másolási tevékenység teljesítményének és finomhangolásának útmutatója](data-factory-copy-activity-performance.md) – ez a cikk a referenciák teljesítményének méréseit és hangolási útmutatóját tartalmazza.
