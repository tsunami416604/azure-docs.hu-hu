---
title: Azure Data Lake Analytics U-SQL-feladatok ütemezése SSIS használatával
description: Megtudhatja, hogy az SQL Server Integration Services használatával hogyan ütemezheti az U-SQL-feladatokat szövegközi parancsfájllal vagy U-SQL lekérdezési fájlokkal.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/17/2018
ms.openlocfilehash: 0650fcc5023ac57b193fa23b0dedf65113fd64e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71672891"
---
# <a name="schedule-u-sql-jobs-using-sql-server-integration-services-ssis"></a>U-SQL feladatok ütemezése az SQL Server Integration Services (SSIS) használatával

Ebben a dokumentumban megtudhatja, hogyan vezényelheti és hozhat létre U-SQL feladatokat az SQL Server Integration Service (SSIS) használatával. 

## <a name="prerequisites"></a>Előfeltételek

[Az Azure Feature Pack for Integration Services](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud) az Azure Data Lake Analytics [feladatot](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017) és az Azure Data Lake [Analytics kapcsolatkezelőt](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017) biztosítja, amely segít csatlakozni az Azure Data Lake Analytics szolgáltatáshoz. A feladat használatához telepítse a következőket:

- [Sql Server Data Tools (SSDT) for Visual Studio letöltése és telepítése](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017)
- [Az Azure Feature Pack integrációs szolgáltatásokhoz (SSIS) telepítése](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017)

## <a name="azure-data-lake-analytics-task"></a>Az Azure Data Lake Analytics feladata

Az Azure Data Lake Analytics feladat lehetővé teszi a felhasználók számára, hogy U-SQL-feladatokat küldjenek az Azure Data Lake Analytics-fiókba. 

[Ismerje meg, hogyan konfigurálható az Azure Data Lake Analytics-feladat.](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017)

![Azure Data Lake Analytics-feladat az SSIS-ben](./media/data-lake-analytics-schedule-jobs-ssis/data-lake-analytics-azure-data-lake-analytics-task-in-ssis.png)

Az U-SQL parancsfájlt különböző helyekről is beszerezheti az SSIS beépített függvényei és feladatai használatával, az alábbi forgatókönyvek pedig bemutatják, hogyan konfigurálhatja az U-SQL-parancsfájlokat a különböző felhasználói esetekhez.

## <a name="scenario-1-use-inline-script-call-tvfs-and-stored-procs"></a>1. forgatókönyv-Inline script hívás tvfs és tárolt procs

Az Azure Data Lake Analytics Feladatszerkesztőben konfigurálja a **SourceType programot** **DirectInput ként,** és helyezze az U-SQL utasításokat az **USQLStatement alkalmazásba.**

Az egyszerű karbantartás és a kódkezelés érdekében csak rövid U-SQL parancsfájlokat helyezhet el inline parancsfájlként, például meghívhat meglévő táblaértékű függvényeket és tárolt eljárásokat az U-SQL-adatbázisokban. 

![Szövegközi U-SQL parancsfájl szerkesztése az SSIS-feladatban](./media/data-lake-analytics-schedule-jobs-ssis/edit-inline-usql-script-in-ssis.png)

Kapcsolódó cikk: [Hogyan lehet átadni a paramétert a tárolt eljárásoknak](#scenario-6-pass-parameters-to-u-sql-script)

## <a name="scenario-2-use-u-sql-files-in-azure-data-lake-store"></a>2. forgatókönyv U-SQL-fájlok használata az Azure Data Lake Store-ban

Az Azure Data Lake Store-ban is használhatja az U-SQL-fájlokat az **Azure Data Lake Store fájlrendszerfeladat** használatával az Azure Feature Pack szolgáltatáscsomagban. Ez a megközelítés lehetővé teszi a felhőben tárolt parancsfájlok használatát.

Az alábbi lépéseket követve állítsa be a kapcsolatot az Azure Data Lake Store fájlrendszer-feladat és az Azure Data Lake Analytics-feladat között.

### <a name="set-task-control-flow"></a>Feladatvezérlési folyamat beállítása

Az SSIS-csomag tervezési nézetében adjon hozzá egy **Azure Data Lake Store fájlrendszer-feladatot,** egy **Foreach Loop Container-t** és egy **Azure Data Lake Analytics-feladatot** a Foreach Loop Container-ben. Az Azure Data Lake Store fájlrendszer-feladat segít az U-SQL-fájlok letöltésében az ADLS-fiókban egy ideiglenes mappába. A Foreach Loop Container és az Azure Data Lake Analytics feladat segítségével az ideiglenes mappában lévő összes U-SQL-fájlt U-SQL-feladatként elküldheti az Azure Data Lake Analytics-fiókba.

![U-SQL-fájlok használata az Azure Data Lake Store-ban](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-data-lake-store.png)

### <a name="configure-azure-data-lake-store-file-system-task"></a>Az Azure Data Lake Store fájlrendszer-feladatának konfigurálása

1. Állítsa **a Művelet et** **CopyFromADLS -re.**
2. Az **AzureDataLakeConnection**beállítása , további információ az [Azure Data Lake Store Connection Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017)ről.
3. Az **AzureDataLakeDirectory beállítása.** Mutasson az U-SQL parancsfájlokat tároló mappára. Az Azure Data Lake Store-fiók gyökérmappájához viszonyított relatív elérési utat használja.
4. Állítsa **a Cél** parancsot egy olyan mappába, amely gyorsítótárazza a letöltött U-SQL parancsfájlokat. Ez a mappaelérési út lesz használva foreach loop tároló U-SQL feladat beküldése. 

![Az Azure Data Lake Store fájlrendszer-feladatának konfigurálása](./media/data-lake-analytics-schedule-jobs-ssis/configure-azure-data-lake-store-file-system-task.png)

[További információ az Azure Data Lake Store fájlrendszer-feladatról.](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-store-file-system-task?view=sql-server-2017)

### <a name="configure-foreach-loop-container"></a>Foreach loop tároló konfigurálása

1. A **Gyűjtemény** lapon állítsa **az Enumerátort** **Foreach File Enumerátor (Foreach File Ennumerator ) beállításra.**

2. Állítsa **a Mappa** az **Enumtor konfigurációs** csoportban a letöltött U-SQL parancsfájlokat tartalmazó ideiglenes mappára.

3. Állítsa be a **Fájlok** at `*.usql` **Ennumerator konfiguráció** úgy, `.usql`hogy a hurok tároló csak a fogások a fájlok végződő .

    ![Foreach loop tároló konfigurálása](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-collection.png)

4. A **Változó leképezések** lapon adjon hozzá egy felhasználó által definiált változót az egyes U-SQL-fájlok fájlnevének lekérése érdekében. A fájlnév leéséhez állítsa az **Indexet** 0-ra. Ebben a példában definiáljon egy változót, amelynek neve `User::FileName`. Ez a változó az U-SQL parancsfájl-kapcsolat dinamikus lehívására és az U-SQL feladat nevének beállítására szolgál az Azure Data Lake Analytics-feladatban.

    ![A Foreach Loop Container konfigurálása a fájlnév leéséhez](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-variable-mapping.png)

### <a name="configure-azure-data-lake-analytics-task"></a>Az Azure Data Lake Analytics feladat konfigurálása 

1. Állítsa **a SourceType programot** **FileConnection**beállításra.

2. Állítsa be a **Fájlkapcsolat** beállítását a Foreach Loop Container fájlobjektumból visszaadott fájlobjektumokra.
    
    A fájlkapcsolat létrehozása:

   1. Válassza az ** \<Új kapcsolat...>** lehetőséget a Fájlkapcsolat beállításban.
   2. Állítsa **a Használati típust** **meglévő fájlra**, és állítsa a **fájlt** bármely meglévő fájl elérési útjára.

       ![Foreach loop tároló konfigurálása](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

   3. A **Kapcsolatkezelők** nézetben kattintson a jobb gombbal az imént létrehozott fájlkapcsolatra, és válassza a **Tulajdonságok parancsot.**

   4. A **Tulajdonságok** ablakban bontsa ki a **Kifejezések csomópontot,** és állítsa a `@[User::FileName]` **ConnectionString** karakterláncot a Foreach Loop Container-ben definiált változóra, például .

       ![Foreach loop tároló konfigurálása](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-property-for-foreach-loop-container.png)

3. Állítsa be az **AzureDataLakeAnalyticsConnection-t** az Azure Data Lake Analytics-fiókhoz, amelybe feladatokat szeretne küldeni. További információ az [Azure Data Lake Analytics csatlakozáskezelőjéről.](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017)

4. Más feladatkonfigurációk beállítása. [További információ.](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017)

5. **Kifejezések** használatával dinamikusan állíthatja be az U-SQL feladat nevét:

    1. A **Kifejezések** lapon adjon hozzá egy új kifejezéskulcs-érték párt a **Feladatnév hez.**
    2. Állítsa be a Feladatnév értékét a Foreach Loop Container-ben definiált változóra, `@[User::FileName]`például.
    
        ![SSIS-kifejezés konfigurálása az U-SQL feladat nevéhez](./media/data-lake-analytics-schedule-jobs-ssis/configure-expression-for-u-sql-job-name.png)

## <a name="scenario-3-use-u-sql-files-in-azure-blob-storage"></a>3. forgatókönyv: U-SQL-fájlok használata az Azure Blob Storage-ban

Az Azure Blob Storage-ban az **Azure Blob Download Task** az Azure Feature Pack szolgáltatásban található U-SQL-fájlokat használhatja. Ez a megközelítés lehetővé teszi a parancsfájlok felhőben való használatát.

A lépések hasonlóak [a 2.](#scenario-2-use-u-sql-files-in-azure-data-lake-store) Módosítsa az Azure Data Lake Store fájlrendszer-feladatot az Azure Blob letöltési feladatra. [További információ az Azure Blob letöltési feladatáról.](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task?view=sql-server-2017)

A vezérlési folyamat olyan, mint alatta.

![U-SQL-fájlok használata az Azure Data Lake Store-ban](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-blob-storage.png)

## <a name="scenario-4-use-u-sql-files-on-the-local-machine"></a>4. forgatókönyv U-SQL-fájlok használata a helyi számítógépen

A felhőben tárolt U-SQL fájlok használata mellett a helyi számítógépen lévő fájlokat vagy az SSIS-csomagokkal telepített fájlokat is használhatja.

1. Kattintson a jobb gombbal **a Kapcsolatkezelők** elemre az SSIS-projektben, és válassza az **Új kapcsolatkezelő parancsot.**

2. Válassza a **Fájltípus** lehetőséget, és kattintson **a Hozzáadás...** gombra.

3. Állítsa **a Használati típust** **a Létező fájlra**, és állítsa be a **fájlt** a helyi számítógépen lévő fájlra.

    ![Fájlkapcsolat hozzáadása a helyi fájlhoz](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

4. Azure **Data Lake Analytics-feladat** hozzáadása és:
    1. Állítsa **a SourceType programot** **FileConnection**beállításra.
    2. Állítsa be a **Fájlkapcsolat** beállítása az imént létrehozott fájlkapcsolatra.

5. Fejezze be az Azure Data Lake Analytics-feladat egyéb konfigurációit.

## <a name="scenario-5-use-u-sql-statement-in-ssis-variable"></a>5. forgatókönyv U-SQL utasítás használata az SSIS változóban

Bizonyos esetekben előfordulhat, hogy dinamikusan kell létrehoznia az U-SQL utasításokat. Az **SSIS-változó** **ssis-kifejezéssel** és más SSIS-feladatokkal, például a Script Task-tel is használható az U-SQL utasítás dinamikus létrehozásához.

1. Nyissa meg a Változók eszközablakot **az SSIS > változók** felső szintű menüjén keresztül.

2. Adjon hozzá egy SSIS-változót, és állítsa be közvetlenül az értéket, vagy használja a **kifejezést** az érték létrehozásához.

3. Azure **Data Lake Analytics-feladat** hozzáadása és:
    1. Állítsa **a SourceType-ot** **változóra.**
    2. Állítsa **a SourceVariable** változót az imént létrehozott SSIS változóhoz.

4. Fejezze be az Azure Data Lake Analytics-feladat egyéb konfigurációit.

## <a name="scenario-6-pass-parameters-to-u-sql-script"></a>6. forgatókönyv-Pass paraméterek u-SQL parancsfájl

Bizonyos esetekben előfordulhat, hogy dinamikusan szeretné beállítani az U-SQL változó értékét az U-SQL parancsfájlban. **Paraméter-leképezési** funkció az Azure Data Lake Analytics-feladat segít ebben a forgatókönyvben. Általában két tipikus felhasználói eset van:

- A bemeneti és kimeneti fájl elérési útváltozóinak dinamikus beállítása az aktuális dátum és idő alapján.
- Állítsa be a tárolt eljárások paraméterét.

[További információ az U-SQL parancsfájl paramétereinek beállításáról.](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017#parameter-mapping-page-configuration)

## <a name="next-steps"></a>További lépések

- [SSIS-csomagok futtatása az Azure-ban](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
- [Azure feature pack integrációs szolgáltatásokhoz (SSIS)](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud)
- [U-SQL-feladatok ütemezése az Azure Data Factory használatával](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics)
