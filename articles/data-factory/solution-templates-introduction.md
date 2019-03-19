---
title: A sablonok az Azure Data Factory – áttekintés |} A Microsoft Docs
description: Ismerje meg, hogyan gyors használatbavétele az Azure Data Factory egy előre definiált sablon használatával.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/04/2019
author: gauravmalhot
ms.author: gamal
manager: craigg
ms.openlocfilehash: 4bd38991b2452bdda65a7647f844dcc17fdfb125
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58003547"
---
# <a name="templates"></a>Sablonok

A sablonok olyan előre meghatározott Azure Data Factory-folyamatok, amelyek lehetővé teszik, hogy a Data Factory használatának gyors megkezdése. Sablonok akkor hasznos, ha használja a Data Factory, és szeretne gyorsan használatba vehető. Ezek a sablonok csökkentheti a fejlesztésre szánt időt, amellyel adatintegrációs projektek ezáltal visszajelzéseivel fejlesztői hatékonyságot érhet el.

## <a name="create-data-factory-pipelines-from-templates"></a>Data Factory-folyamatok létrehozásához sablonokból

Elkezdheti egy Data Factory-folyamatot hoz létre egy sablont a következő két módon:

1.  Válassza ki **folyamat létrehozása sablonból** a sablonkatalógusban megnyitása az Áttekintés oldalon.

    ![Nyissa meg a sablonkatalógusban az áttekintő lap](media/solution-templates-introduction/templates-intro-image1.png)

1.  A szerző lapon az erőforrás-kezelőben válassza ki a **+**, majd **folyamatot sablonból** a sablonkatalógusban megnyitásához.

    ![Nyissa meg a sablonkatalógusban a szerző lapról](media/solution-templates-introduction/templates-intro-image2.png)

## <a name="template-gallery"></a>Sablontár

![A sablontár](media/solution-templates-introduction/templates-intro-image3.png)

### <a name="out-of-the-box-data-factory-templates"></a>A beépített adat-előállító sablon kívül

A Data Factory az Azure Resource Manager-sablonok mentése a data factory folyamat sablonokat használ. Láthatja, hogy minden a Resource Manager-sablonok, a jegyzékfájl belül a beépített adat-előállító sablon nem használható együtt a [hivatalos Azure Data Factory GitHub-adattárat](https://github.com/Azure/Azure-DataFactory/tree/master/templates). Az előre definiált sablonok, a Microsoft által biztosított tartalmazza, de nem korlátozódnak az alábbi cikkeket:

-   Másolhatja a sablonokat:

    -   [A tömeges másolási adatbázisból](solution-template-bulk-copy-with-control-table.md)
    
    -   [Új fájlok másolása LastModifiedDate szerint](solution-template-copy-new-files-lastmodifieddate.md)

    -   [Másolja ki több fájlt tároló fájlalapú tárolók között](solution-template-copy-files-multiple-containers.md)

    -   [Adatbázisból származó változásadatok másolásához](solution-template-delta-copy-with-control-table.md)

    -   Másolja át \<forrás\> való \<cél\>

        -   Az Azure Data Lake Store az Amazon S3-ból velikost Haldy 2

        -   Az Azure Data Lake Store Google big Data típusú lekérdezésből velikost Haldy 2

        -   Az Azure Data Lake Store 2. generációs gyűjtések HDF a

        -   Az Azure Data Lake Store a Netezza velikost Haldy 1

        -   Az Azure SQL Database helyszíni SQL Serverről

        -   Az Azure SQL Data Warehouse helyszíni SQL Serverről

        -   Az Azure SQL Data Warehouse helyszíni Oracle

-   SSIS-sablonok

    -   Az SSIS-csomagok Azure-SSIS integrációs modul ütemezése

-   Sablonok átalakítása

    -   [Az Azure Databricks ETL](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>Saját sablonok

Mentheti is egy folyamatot egy sablon kiválasztásával **Mentés sablonként** folyamat lapján.

![Folyamat mentése sablonként](media/solution-templates-introduction/templates-intro-image4.png)

Megtekintheti a folyamatok a sablonként menteni a **saját sablonok** a Sablonkatalógusban szakaszában. Emellett megtekintheti őket a **sablonok** szakaszban az erőforrás-kezelőben.

![Saját sablonok](media/solution-templates-introduction/templates-intro-image5.png)

> [!NOTE]
> Saját sablonok szolgáltatását használja, rendelkezik GIT-integráció engedélyezéséhez. Az Azure DevOps GIT és a GitHub egyaránt támogatottak.
