---
title: Terabájt adat betöltése az SQL Data Warehouse-bA |} A Microsoft Docs
description: Bemutatja, hogyan 1 TB-nyi adatot tölthetők be az Azure SQL Data Warehouse-bA az Azure Data Factoryvel 15 perc alatt
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
ms.openlocfilehash: 727a503a2fd942e6fbef9009c760a39f90828d2f
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433127"
---
# <a name="load-1-tb-into-azure-sql-data-warehouse-under-15-minutes-with-data-factory"></a>1 TB adat betöltése az Azure SQL Data Warehouse-bA a Data Factory 15 perc alatt
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [másolhat az Azure SQL Data Warehouse-ból a Data Factory használatával](../connector-azure-sql-data-warehouse.md).


[Az Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) egy felhőalapú, horizontálisan felskálázható adatbázis nagy mennyiségű relációs és nem relációs adatok feldolgozására alkalmas állapotban van.  Nagymértékben párhuzamos feldolgozási (MPP) architektúrára épülő SQL Data Warehouse vállalati adatokat az adatraktár számítási feladatokhoz van optimalizálva.  Felhőalapú rugalmasság és számítási egymástól függetlenül méretezheti a storage is kínál.

Ismerkedés az Azure SQL Data Warehouse már használatával minden eddiginél egyszerűbben **Azure Data Factory**.  Az Azure Data Factory egy teljes körűen felügyelt felhőalapú adatintegrációs szolgáltatás, amely egy SQL Data Warehouse az adatokat, a meglévő rendszerről, és hogy értékes időt kiértékelése az SQL Data Warehouse és az elemzések készítése közben mentése feltöltéséhez használható megoldások. Az alábbiakban az adatok betöltése az Azure Data Factory használatával az Azure SQL Data Warehouse előnyei:

* **Könnyű beállítás**: 5. lépés – nincs parancsfájl-szükséges intuitív varázslóval.
* **Sokrétű támogatás adattároló**: a helyszíni és felhőalapú adattárak számos beépített támogatása.
* **Biztonságos és megfelelő**: HTTPS- vagy ExpressRoute keresztül továbbított adatok, és a szolgáltatás globális jelenlét biztosítja az adatok sosem hagyja el a földrajzi határ
* **A PolyBase használatával páratlan teljesítményt** – adatok áthelyezése az Azure SQL Data Warehouse-bA a Polybase használatával a leghatékonyabb módja a. Az előkészítési blob szolgáltatást használja, minden típusú adattárakban mellett az Azure Blob storage, amely alapértelmezés szerint támogatja a Polybase nagy terhelés sebességet érhet el.

Ez a cikk bemutatja, hogyan használhatja a Data Factory másolási varázslója 1 TB méretű adatok betöltése az Azure Blob Storage-ból az Azure SQL Data Warehouse 15 percen belül, több mint 1,2 GB/s adatátviteli kapacitással.

Ebben a cikkben részletes útmutatást nyújt az adatok áthelyezése az Azure SQL Data Warehouse-bA a másolás varázsló használatával.

> [!NOTE]
>  Általános információk képességekkel kapcsolatos adat-előállító az adatok áthelyezését és- tárolókról az Azure SQL Data Warehouse: [adatok importálására és az Azure Data Factory használatával az Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md) cikk.
>
> Is létrehozható Azure Portalon, a Visual Studio, PowerShell, folyamatokat stb. Lásd: [oktatóanyag: Adatok másolása Azure blobból az Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) gyors bemutató részletes utasításokat a másolási tevékenységgel az Azure Data Factoryban.  
>
>

## <a name="prerequisites"></a>Előfeltételek
* Az Azure Blob Storage: Ezzel a kísérlettel Azure Blob Storage (GRS) használ a TPC-H tesztelési adatkészlet tárolásához.  Ha nem rendelkezik Azure storage-fiók, ismerje meg, [storage-fiók létrehozása](../../storage/common/storage-quickstart-create-account.md).
* [TPC-H](http://www.tpc.org/tpch/) adatok: tesztelési adatkészletként TPC-H használni fogjuk.  Ehhez szüksége `dbgen` a TPC-H eszközkészletéből, amely segítséget nyújt az adatkészlet létrehozásához.  Letöltheti a forráskódot `dbgen` a [TPC eszközök](http://www.tpc.org/tpc_documents_current_versions/current_specifications.asp) összeállítása saját magának, és töltse le a lefordított bináris [GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TPCHTools).  Az alábbi parancsokat a-1 TB-os egybesimított fájl létrehozásához futtassa a dbgen.exe `lineitem` megállítását tábla 10 fájlok:

  * `Dbgen -s 1000 -S **1** -C 10 -T L -v`
  * `Dbgen -s 1000 -S **2** -C 10 -T L -v`
  * …
  * `Dbgen -s 1000 -S **10** -C 10 -T L -v`

    Most másolja a létrehozott fájlokat az Azure-Blobba.  Tekintse meg [adatok importálására és a helyi fájlrendszerből egy Azure Data Factory használatával](data-factory-onprem-file-system-connector.md) hogyan valósítható meg az ADF-példány használatával.    
* Az Azure SQL Data Warehouse: Ezzel a kísérlettel adatokat tölt be az Azure SQL Data Warehouse 6000 dwu-kkal létrehozott

    Tekintse meg [hozzon létre egy Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) részletes útmutató az SQL Data Warehouse-adatbázis létrehozása.  Az SQL Data Warehouse a Polybase a legjobb lehetséges betöltési teljesítmény lekéréséhez Adattárházegység (Dwu) a teljesítmény beállítás, amely 6000 dwu-k engedélyezett maximális száma lehetőséget választjuk.

  > [!NOTE]
  > Az Azure-Blobból betöltésekor az Adatbetöltési teljesítménye arányos kell konfigurálni az SQL Data Warehouse dwu-k számának:
  >
  > 1 TB-os 1000 való betöltésének DWU az SQL Data Warehouse 87 perc (közel 200 MB/s átviteli sebesség) betöltése 1 TB-os figyelembe veszi 2000 DWU az SQL Data warehouse-ba kerül 46 perc (~ 380 MB/s átviteli sebesség) betöltése 1 TB-os 6000 DWU az SQL Data Warehouse-bA 14 perc (~1.2 GB/s átviteli sebesség) vesz igénybe.
  >
  >

    Szeretne létrehozni egy SQL Data Warehouse 6000 dwu-k, a teljesítmény csúszkát egészen jobbra való:

    ![Teljesítmény csúszka](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    A meglévő adatbázis, amely nincs beállítva 6000 dwu-k méretezheti az Azure portal használatával.  Keresse meg az adatbázist az Azure Portalon, és nincs olyan **méretezési** gombra a **áttekintése** panelen a következő képen látható:

    ![Méretezés gomb](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    Kattintson a **méretezési** gombra kattintva nyissa meg a következő panelen, a csúszka maximális értéke, és kattintson **mentése** gombra.

    ![Méretezési csoport párbeszédpanel](media/data-factory-load-sql-data-warehouse/scale-dialog.png)

    Ez a kísérlet adatokat tölt be az Azure SQL Data Warehouse használatával `xlargerc` erőforrásosztályhoz.

    A legjobb teljesítmény elérése érdekében másolási felhasználó nyithatja meg az SQL Data Warehouse segítségével kell `xlargerc` erőforrásosztályhoz.  Ismerje meg, hogyan valósítható meg a következő [módosítása egy felhasználói erőforrás osztály példa](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).  
* A következő DDL-utasítás futtatásával hozzon létre az Azure SQL Data Warehouse-adatbázis, céloldali tábla sémáját:

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
Az előfeltételként felsorolt lépéseket befejeződött, az azt most már készen áll a másolási tevékenység, a másolás varázsló használatával konfigurálhatja.

## <a name="launch-copy-wizard"></a>A Másolás varázsló indítása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a **erőforrás létrehozása** a bal felső sarokban, kattintson a **intelligencia és elemzés**, és kattintson a **adat-előállító**.
3. Az a **új adat-előállító** panelen:

   1. Adja meg **LoadIntoSQLDWDataFactory** számára a **neve**.
       Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a hibaüzenetet kapja: **Nem érhető el a Data factory name "LoadIntoSQLDWDataFactory"**, módosítsa a nevet az adat-előállító (például yournameLoadIntoSQLDWDataFactory), és próbálkozzon újra a létrehozással. A Data Factory-összetevők elnevezési szabályait a [Data Factory - Naming Rules](data-factory-naming-rules.md) (Data Factory – Elnevezési szabályok) című témakörben találhatja.  
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
Az első lépés, hogy az adatok betöltése az ütemezés konfigurálása.  

A **Properties** (Tulajdonságok) oldalon:

1. Adja meg **CopyFromBlobToAzureSqlDataWarehouse** a **feladat neve**
2. Válassza ki **futtatása után azonnal** lehetőséget.   
3. Kattintson a **tovább**.  

    ![Másolás varázsló – Tulajdonságok lap](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>2. lépés: Forrás konfigurálása
Ez a szakasz bemutatja, a forrás konfigurálásának lépéseit: Az 1 TB-os TPC tartalmazó Azure Blob-H sortételt fájlokat.

1. Válassza ki a **Azure Blob Storage** , az adatok tárolására, és kattintson a **tovább**.

    ![Másolás varázsló - forrás kiválasztása lap](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

2. Adja meg a kapcsolati adatokat az Azure Blob storage-fiók, és kattintson a **tovább**.

    ![Másolás varázsló - adatforrás kapcsolati adatait](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

3. Válassza ki a **mappa** elem fájlokat. sor, majd kattintson a TPC-H tartalmazó **tovább**.

    ![Másolás varázsló – a bemeneti mappa kiválasztása](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

4. Gomb megnyomásakor **tovább**, a rendszer automatikusan észleli a fájlformátum beállításai.  Győződjön meg arról, hogy oszlophatároló ' |} "helyett az alapértelmezett vesszővel','.  Kattintson a **tovább** , kinyomtatnak rendelkezik az adatok után.

    ![Másolás varázsló - fájlformátum beállításai](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>3. lépés: Cél konfigurálása
Ez a szakasz bemutatja, hogyan konfigurálhatja a cél: `lineitem` az Azure SQL Data Warehouse-adatbázis táblája.

1. Válasszon **Azure SQL Data Warehouse** céljaként tárolja, és kattintson a **tovább**.

    ![Másolás varázsló – válassza ki a célként megadott adattárba](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

2. Adja meg az Azure SQL Data Warehouse kapcsolódási információt.  Ügyeljen arra, hogy a szerepkör tagja a felhasználó megadja `xlargerc` (lásd: a **Előfeltételek** részletes utasításokat a következő szakaszban), és kattintson a **tovább**.

    ![Másolás varázsló – cél kapcsolati adatok](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

3. Válassza ki a céloldali tábla, és kattintson a **tovább**.

    ![Másolás varázsló - tábla hozzárendelése oldal](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

4. A séma hozzárendelése oldal, hagyja "Oszlop-hozzárendelés alkalmazás" beállítás nincs bejelölve, és kattintson a **tovább**.

## <a name="step-4-performance-settings"></a>4. lépés: Teljesítményadat-beállításai

**A polybase lehetővé** alapértelmezés szerint be van jelölve.  Kattintson a **tovább**.

![Másolás varázsló - séma hozzárendelése oldal](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>5. lépés: Telepítheti és figyelheti a betöltési eredmények
1. Kattintson a **Befejezés** telepítése gombra.

    ![Másolás varázsló – Összegzés lapján](media/data-factory-load-sql-data-warehouse/summary-page.png)

2. Az üzembe helyezés befejezése után kattintson a `Click here to monitor copy pipeline` figyelése a másolási folyamat futtatása. Válassza ki a létrehozott másolási folyamat a **tevékenység Windows** listája.

    ![Másolás varázsló – Összegzés lapján](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

    Megtekintheti a Futtatás részletei példányt a **Activity Window Explorer** a jobb oldali panelen, beleértve a forrás olvas és ír be a cél, időtartama és az átlagos átviteli sebességet a Futtatás adatmennyiség.

    Az alábbi képernyőképen az látható, mint 1 TB-os másolása az Azure Blob Storage-ból az SQL Data Warehouse-bA tartott 14 perc, hatékonyan a 1.22 GB/s átviteli sebesség eléréséhez!

    ![Másolás varázsló – sikeres párbeszédpanel](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>Ajánlott eljárások
Az alábbiakban néhány gyakorlati tanácsok az Azure SQL Data Warehouse-adatbázis futtatásához:

* Egy nagyobb erőforrásosztály használata a FÜRTÖZÖTT OSZLOPCENTRIKUS INDEX való betöltésekor.
* Hatékonyabb illesztés fontolja meg kivonatoló terjesztést használ alapértelmezett round robin terjesztési helyett válassza oszlop szerint.
* A gyorsabb betöltési sebességre képes érdemes lehet halommemória átmeneti adatokhoz.
* Statisztika létrehozása, betöltése az Azure SQL Data Warehouse befejezése után.

Lásd: [ajánlott eljárások az Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-best-practices.md) részleteiről.

## <a name="next-steps"></a>További lépések
* [A Data Factory másolási varázslója](data-factory-copy-wizard.md) – Ez a cikk részletesen a Másolás varázslót.
* [Másolási tevékenységek teljesítményéhez és finomhangolási útmutató](data-factory-copy-activity-performance.md) – Ez a cikk tartalmazza a referencia-TELJESÍTMÉNYMÉRÉSEK és finomhangolási útmutató.
