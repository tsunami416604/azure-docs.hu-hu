---
title: A sablonok áttekintése
description: Ismerje meg, hogyan használhat előre definiált sablont az Azure Data Factory gyors használatához.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 01/04/2019
ms.openlocfilehash: 14f1d3b58996d438279bfa2a234c754a6d840c79
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414732"
---
# <a name="templates"></a>Sablonok
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A sablonok előre definiált Azure Data Factory-folyamatok, amelyek lehetővé teszik a Data Factory gyors megkezdését. A sablonok akkor hasznosak, ha most ismerkedik a Data Factoryval, és gyorsan szeretné elkezdeni. Ezek a sablonok csökkentik az adatintegrációs projektek fejlesztési idejét, ezáltal javítva a fejlesztők termelékenységét.

## <a name="create-data-factory-pipelines-from-templates"></a>Adatfeldolgozó folyamatok létrehozása sablonokból

A Data Factory-folyamat létrehozásának megkezdése egy sablonból az alábbi két módon:

1.  Válassza a Folyamat létrehozása lehetőséget a **sablonból** az Áttekintés lapon a sablongyűjtemény megnyitásához.

    ![A sablongyűjtemény megnyitása az Áttekintés lapon](media/solution-templates-introduction/templates-intro-image1.png)

1.  Az Erőforrás-kezelő Szerző lapján válassza a Lehetőséget, **+** majd a Folyamat **sablonból a** sablongyűjtemény megnyitásához.

    ![A sablongyűjtemény megnyitása a Szerző lapon](media/solution-templates-introduction/templates-intro-image2.png)

## <a name="template-gallery"></a>Sablongyűjtemény

![A sablongyűjtemény](media/solution-templates-introduction/templates-intro-image3.png)

### <a name="out-of-the-box-data-factory-templates"></a>A data factory sablonok

A Data Factory az Azure Resource Manager-sablonokat használja az adatgyári folyamatsablonok mentéséhez. A [hivatalos Azure Data Factory GitHub-tárházban](https://github.com/Azure/Azure-DataFactory/tree/master/templates)megtekintheti az összes Erőforrás-kezelő sablont, valamint a Data Factory-sablonokhoz használt jegyzékfájlt. A Microsoft által biztosított előre definiált sablonok közé tartoznak többek között a következő elemek:

-   Sablonok másolása:

    -   [Tömeges másolás az adatbázisból](solution-template-bulk-copy-with-control-table.md)
    
    -   [Új fájlok másolása az utolsó módosítás dátuma szerint](solution-template-copy-new-files-lastmodifieddate.md)

    -   [Több fájltároló másolása fájlalapú tárolók között](solution-template-copy-files-multiple-containers.md)

    -   [Fájlok áthelyezése](solution-template-move-files.md)

    -   [Különbözeti másolás az adatbázisból](solution-template-delta-copy-with-control-table.md)

    -   Másolás \<\> forrásról \<célra\>

        -   [Az Amazon S3-tól az Azure Data Lake Store Gen 2-ig](solution-template-migration-s3-azure.md)

        -   A Google Big Querytől az Azure Data Lake Store Gen 2-ig

        -   A HDF-től az Azure Data Lake Store Gen 2-ig

        -   Netezza-tól az Azure Data Lake Store Gen 1-ig

        -   A helyszíni SQL Serverről az Azure SQL Database-re

        -   A helyszíni SQL Serverről az Azure SQL Data Warehouse-ra

        -   A helyszíni Oracle-től az Azure SQL Data Warehouse-ig

-   SSIS-sablonok

    -   Az Azure-SSIS-integrációs futásidő ütemezése SSIS-csomagok végrehajtásához

-   Sablonok átalakítása

    -   [ETL az Azure Databricks](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>Saját sablonok

A folyamatsablonként is menthető, ha a Folyamat lapon a **Mentés sablonként** lehetőséget választja.

![Folyamat mentése sablonként](media/solution-templates-introduction/templates-intro-image4.png)

A sablonként mentett folyamatokat a Sablontár **Saját sablonok** szakaszában tekintheti meg. Ezeket az **Erőforrás-kezelő Sablonok** szakaszában is megtekintheti.

![Saját sablonok](media/solution-templates-introduction/templates-intro-image5.png)

> [!NOTE]
> A Saját sablonok szolgáltatás használatához engedélyeznie kell a GIT-integrációt. Az Azure DevOps GIT és a GitHub egyaránt támogatottak.
