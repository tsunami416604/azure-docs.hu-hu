---
title: SSIS használata Azure Data Lake Analytics U-SQL feladatok ütemezéséhez |} A Microsoft Docs
description: Ismerje meg, hogyan használható az SQL Server Integration Services U-SQL feladatok ütemezéséhez.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/17/2018
ms.author: yanacai
ms.openlocfilehash: 919ec4e26c2da945a0623e772dc00d378a583b69
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126745"
---
# <a name="schedule-u-sql-jobs-using-sql-server-integration-services-ssis"></a>Az SQL Server Integration Services (SSIS) használatával U-SQL-feladatok ütemezése

Ebből a dokumentumból megismerheti, hogyan összehangolására, és az SQL Server integrációs szolgáltatás (SSIS) használatával U-SQL-feladatok létrehozása. 

## <a name="prerequisites"></a>Előfeltételek

[Az Azure Feature Pack for Integration Services](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud) biztosít a [Azure Data Lake Analytics-feladat](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017) és a [Azure Data Lake Analytics Csatlakozáskezelő](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017) , segít csatlakozni az Azure Data Lake Elemzési szolgáltatás. Ez a feladat használ, győződjön meg arról, telepíti:

- [Töltse le és telepítse az SQL Server Data Tools (SSDT) a Visual Studióhoz](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017)
- [Az Azure Feature Pack for Integration Services (SSIS) telepítése](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017)

## <a name="azure-data-lake-analytics-task"></a>Az Azure Data Lake Analytics-feladat

Az Azure Data Lake Analytics-feladat segítségével a felhasználók az Azure Data Lake Analytics-fiók U-SQL feladatok elküldéséhez. 

[Ismerje meg, hogyan konfigurálhatja az Azure Data Lake Analytics-feladat](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017).

![Az Azure Data Lake Analytics-feladat az SSIS](./media/data-lake-analytics-schedule-jobs-ssis/data-lake-analytics-azure-data-lake-analytics-task-in-ssis.png)

Akkor képes a U-SQL parancsfájl különböző helyeken SSIS beépített függvények használatával és feladatokat, az alábbi forgatókönyvek bemutatják, hogyan konfigurálható a U-SQL-parancsfájlok különböző felhasználói esetek.

## <a name="scenario-1-use-inline-script-call-tvfs-and-stored-procs"></a>A forgatókönyv 1 használható beágyazott parancsprogram hívása tvfs és tárolt procs

Az Azure Data Lake Analytics feladat Editor, konfigurálja **forrás típusa** , **DiretInput**, és a U-SQL-utasításokat injektálhatnak **USQLStatemnt**.

Egyszerű maintainence és kód felügyeleti, csak helyezzen rövid U-SQL-szkript, beágyazott parancsfájlokra például hívása meglévő táblázat értékű függvények és tárolt eljárások a U-SQL-adatbázisokban. 

![Az SSIS-tevékenység beágyazott U-SQL parancsfájl szerkesztése](./media/data-lake-analytics-schedule-jobs-ssis/edit-inline-usql-script-in-ssis.png)

Kapcsolódó cikk: [hogyan adhatók át a paramétert a tárolt eljárások](#scenario-6-pass-parameters-to-u-sql-script)

## <a name="scenario-2-use-u-sql-files-in-azure-data-lake-store"></a>A forgatókönyv 2 használható U-SQL-fájlokat az Azure Data Lake Store

Használhatja U-SQL-fájlokat a az Azure Data Lake Store használatával **Azure Data Lake Store rendszer feladat** az Azure Feature Pack. Ez a megközelítés lehetővé teszi, hogy a felhőben tárolt parancsfájlok.

Kövesse az alábbi lépéseket az Azure Data Lake Store rendszer feladat és az Azure Data Lake Analytics tevékenység közötti kapcsolat beállításához.

### <a name="set-task-control-flow"></a>Készlet feladat átvitelvezérlés

SSIS-csomag Tervező nézetben, adjon hozzá egy **Azure Data Lake Store rendszer feladat**, amely egy **Foreach ciklus tároló** és a egy **Azure Data Lake Analytics feladat** a Foreach ciklus a A tároló. A Azure Data Lake Store fájl feladat segítségével töltse le az ADLS-fiók U-SQL-fájlokat egy ideiglenes mappába. A Foreach ciklus tároló és az Azure Data Lake Analytics feladat segítségével minden U-SQL-fájl az ideiglenes mappában, az Azure Data Lake Analytics-fiók, mint egy U-SQL-feladat elküldéséhez.

![Használjon U-SQL-fájlokat az Azure Data Lake Store](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-data-lake-store.png)

### <a name="configure-azure-data-lake-store-file-system-task"></a>Az Azure Data Lake Store-fájl rendszerfeladatot konfigurálása

1. Állítsa be **művelet** való **CopyFromADLS**.
2. Állítsa be a **AzureDataLakeConnection**, tudjon meg többet [Azure Data Lake Store Csatlakozáskezelő](https://docs.microsoft.com/en-us/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017).
3. Állítsa be **AzureDataLakeDirectory**. A U-SQL-parancsprogramok tárolására mappára mutasson. Használjon relatív elérési útként a Azure Data Lake Store-fiók gyökérmappájában.
4. Állítsa be **cél** egy mappába, amely a letöltött U-SQL-parancsfájlok gyorsítótárazásával. A mappa elérési útját a U-SQL feladatok elküldéséhez a Foreach ciklus tároló lesz használható. 

![Az Azure Data Lake Store-fájl rendszerfeladatot konfigurálása](./media/data-lake-analytics-schedule-jobs-ssis/configure-azure-data-lake-store-file-system-task.png)

[További információ az Azure Data Lake Store fájl rendszerfeladatot](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-store-file-system-task?view=sql-server-2017).

### <a name="configure-foreach-loop-container"></a>Foreach ciklus tároló konfigurálása

1. A **gyűjtemény** lap **enumerátor** való **Foreach fájl enumerátor**.

2. Állítsa be **mappa** alatt **enumerátor konfigurációs** az ideiglenes mappába a letöltött U-SQL-parancsfájlok tartalmazó csoport.

3. Állítsa be **fájlok** alatt **enumerátor konfigurációs** való `*.usql` a hurok tároló csak a fájlok végződő kivételekkel, `.usql`.

    ![Foreach ciklus tároló konfigurálása](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-collection.png)

4. A **változó-hozzárendelések** lapon, a felhasználó által definiált U-SQL-fájl esetében a fájl nevének lekérése érdekében változó hozzáadása. Állítsa be a **Index** 0-ra, a fájl nevének lekérése érdekében. Ebben a példában adjon meg egy változót, nevű `User::FileName`. Ez a változó dinamikusan U-SQL parancsfájl fájl kapcsolata, és állítsa be a U-SQL-feladat neve az Azure Data Lake Analytics tevékenység használható.

    ![Konfigurálja a Foreach ciklus tároló fájl nevének lekérése](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-variable-mapping.png)

### <a name="configure-azure-data-lake-analytics-task"></a>Az Azure Data Lake Analytics-feladat konfigurálása 

1. Állítsa be **forrás típusa** való **FileConnection**.

2. Állítsa be **FileConnection** a fájl kapcsolat, amely a fájl objektumra mutat a Foreach ciklus tárolóból adja vissza.
    
    Ez a kapcsolat létrehozása:

    1. Válasszon **<New Connection...>** FileConnection beállításban.
    2. Állítsa be **használati típust** való **létező fájl**, és állítsa be a **fájl** , bármely meglévő fájlok fájl elérési útját.

        ![Foreach ciklus tároló konfigurálása](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

    3. A **kezelők** megtekintéséhez, kattintson a jobb gombbal a fájl kapcsolat csak most hozta létre, és válassza a **tulajdonságok**.

    4. Az a **tulajdonságok** ablakában bontsa ki a **kifejezések**, és állítsa be **ConnectionString** a Foreach ciklus tárolóban, például definiált változó `@[User::FileName]`.

        ![Foreach ciklus tároló konfigurálása](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-property-for-foreach-loop-container.png)

3. Állítsa be **AzureDataLakeAnalyticsConnection** küldhetők be feladatok a kívánt Azure Data Lake Analytics-fiókhoz. Tudjon meg többet [Azure Data Lake Analytics Csatlakozáskezelő](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017).

4. Egyéb feladat konfigurációt állítson be. [További](https://docs.microsoft.com/en-us/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017).

5. Használat **kifejezések** dinamikus beállításához az U-SQL-feladat neve:

    1. A **kifejezések** oldalon adja hozzá a kifejezés kulcs-érték párokat a **JobName**.
    2. A JobName állítsa az értékét a Foreach ciklus tárolóban, például definiált változó `@[User::FileName]`.
    
        ![SSIS kifejezés konfigurálása a U-SQL-feladat neve](./media/data-lake-analytics-schedule-jobs-ssis/configure-expression-for-u-sql-job-name.png)

## <a name="scenario-3-use-u-sql-files-in-azure-blob-storage"></a>A forgatókönyv-3 használható U-SQL-fájlok Azure Blob Storage-ban

Használhatja az Azure Blob Storage-U-SQL-fájlok használatával **Azure Blob letöltése feladat** az Azure Feature Pack. Ez a megközelítés lehetővé teszi a parancsfájlok használata a felhőben.

A lépések hasonlóak az [Scnario 2: használjon U-SQL-fájlokat az Azure Data Lake Store](#scenario-2-use-u-sql-files-in-azure-data-lake-store). Módosítsa a feladat az Azure Data Lake Store fájlt az Azure Blob letöltése feladat. [További információ az Azure Blob letöltése feladat](https://docs.microsoft.com/en-us/sql/integration-services/control-flow/azure-blob-download-task?view=sql-server-2017).

Átvitelvezérlés például nem éri el.

![Használjon U-SQL-fájlokat az Azure Data Lake Store](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-blob-storage.png)

## <a name="scenario-4-use-u-sql-files-on-the-local-machine"></a>A forgatókönyv 4 használható U-SQL-fájlok a helyi gépen

Mellett a felhőben tárolt U-SQL-fájlok használata esetén is használhatja a helyi gépén vagy -fájlokat az SSIS-csomagok üzembe helyezéséhez.

1. Kattintson a jobb gombbal **kezelők** SSIS a projektre, és válassza a **új Csatlakozáskezelő**.

2. Válassza ki **fájl** írja be, és kattintson a **hozzáadása...** .

3. Állítsa be **használati típust** való **létező fájl**, és állítsa be a **fájl** a fájl a helyi gépen.

    ![A helyi fájl fájlkapcsolat hozzáadása](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

4. Adjon hozzá **az Azure Data Lake Analytics** feladat és:
    1. Állítsa be **forrás típusa** való **FileConnection**.
    2. Állítsa be **FileConnection** a fájl kapcsolatra csak most hozta létre.

5. Egyéb konfigurációk, az Azure Data Lake Analytics a feladat befejezéséhez.

## <a name="scenario-5-use-u-sql-statement-in-ssis-variable"></a>A forgatókönyv 5 használható U-SQL-utasítás SSIS változóban

Bizonyos esetekben szükség lehet dinamikusan generálni a U-SQL-utasításokkal. Használhat **SSIS változó** a **SSIS kifejezés** és egyéb SSIS-feladatok, például parancsfájl feladat dinamikusan létrehozni a U-SQL-utasítás segítségével.

1. Nyissa meg változókat eszköz ablak keresztül **SSIS > változók** legfelső szintű menü.

2. Adjon hozzá egy SSIS változó és állítsa az értékét közvetlenül vagy **kifejezés** az érték létrehozásához.

3. Adjon hozzá **az Azure Data Lake Analytics-feladat** és:
    1. Állítsa be **forrás típusa** való **változó**.
    2. Állítsa be **SourceVariable** , az SSIS változó csak most hozta létre.

4. Egyéb konfigurációk, az Azure Data Lake Analytics a feladat befejezéséhez.

## <a name="scenario-6-pass-parameters-to-u-sql-script"></a>U-SQL parancsfájl forgatókönyv 6-adja át a paramétereket

Bizonyos esetekben érdemes lehet, hogy dinamikusan állítsa be a U-SQL-változó értékét a U-SQL parancsfájl. **Paraméterleképezés** ebben a forgatókönyvben az Azure Data Lake Analytics feladat súgó funkciót. Nincsenek általában két jellemző felhasználói esetek:

- A bemeneti és kimeneti beállítása dinamikusan aktuális dátum és idő alapján a fájl elérésiút-változókat.
- Állítsa a paramétert, a tárolt eljárásokat.

[További információ a U-SQL parancsfájl paramétereinek beállítása](https://docs.microsoft.com/en-us/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017#parameter-mapping-page-configuration).

## <a name="next-steps"></a>További lépések

- [SSIS-csomagok futtatása az Azure-ban](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
- [Az Azure Feature Pack for Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud)
- [Azure Data Factory használatával az U-SQL-feladatok ütemezésére](https://docs.microsoft.com/en-us/azure/data-factory/transform-data-using-data-lake-analytics)

