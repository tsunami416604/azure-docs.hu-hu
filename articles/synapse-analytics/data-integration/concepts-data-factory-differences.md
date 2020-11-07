---
title: Eltérések az Azure Data Factoryhoz képest
description: Ismerje meg, hogyan különbözik az Azure szinapszis Analytics adatintegrációs képességei a Azure Data Factory
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: 10f5336dd4c8a02acf623b1b14226ca676006953
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357649"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Adatintegráció az Azure szinapszis Analyticsben és Azure Data Factory

Az Azure szinapszis Analyticsben az adatintegrációs képességek, például a szinapszis-folyamatok és az Adatáramlások a Azure Data Factoryon alapulnak. További információ: [What is Azure Data Factory](../../data-factory/introduction.md). Szinte az összes funkció azonos vagy hasonló, és a dokumentáció a két szolgáltatás között van megosztva. Ez a cikk kiemeli és azonosítja a Azure Data Factory és az Azure szinapszis közötti jelenlegi különbségeket.

Ha szeretné megtekinteni, hogy egy Azure Data Factory funkció vagy cikk az Azure Szinapszisra vonatkozik-e, tekintse meg a cikk tetején található monikert.

![A monikerre vonatkozik](../media/concepts-data-factory-differences/applies-to-moniker.png "A monikerre vonatkozik")

## <a name="features-in-azure-data-factory-not-planned-for-azure-synapse"></a>Az Azure Szinapszishoz nem tervezett szolgáltatások Azure Data Factory

A következő szolgáltatások érhetők el Azure Data Factoryban, de az Azure Szinapszishoz nem tervezték őket.

* **SSIS-csomagok élettartama és eltolása:** Azure Data Factory a SSIS Integration Runtime használatával megemelheti és áthelyezheti a SSIS-csomagokat. A SSIS Integration Runtime és a végrehajtás SSIS csomag tevékenység nem érhető el a szinapszis-munkaterületeken. 
* **Élettartam:** Az élettartam értéke az Azure Integration Runtime egyik beállítása, amely lehetővé teszi, hogy a Spark-fürt az adatfolyamatok elvégzése után egy ideig *melegen* maradjon. Ez a funkció nem érhető el a szinapszis-munkaterületeken.

## <a name="azure-synapse-features-not-supported-in-azure-data-factory"></a>Az Azure szinapszis szolgáltatásai nem támogatottak Azure Data Factory

A következő szolgáltatások érhetők el az Azure Szinapszisban, de nem tervezik Azure Data Factory számára.

* **A Spark-feladatok leképezési folyamatának figyelése:** A Szinapszisban a Spark motor a felhasználó előfizetésében található, így a felhasználók megtekinthetik a részletes Spark-naplókat. Azure Data Factory a feladatok végrehajtása Azure Data Factory által felügyelt Spark-fürtön történik, és ez az információ nem érhető el. 

## <a name="azure-data-factory-features-that-behave-differently-in-synapse"></a>A Szinapszisban eltérően viselkedő szolgáltatások Azure Data Factory

A következő funkciók vagy eltérően viselkednek, vagy jelenleg nem léteznek az Azure Szinapszisban. 

* **Huzavona-adatfolyamatok:** A huzavona adatfolyam tevékenység jelenleg csak Azure Data Factoryban érhető el.
* **A megoldás sablonjának gyűjteménye:** Azure Data Factory a felhasználók a megoldás sablonjának gyűjteményében megtalálják a folyamat sablonjait. A szinapszis-munkaterületeken a Tudásközpont különböző sablonokat tartalmaz, valamint további adatkészleteket és SQL-parancsfájlokat. 
* **Git-integráció és egy NATÍV CI/CD-megoldás:** Jelenleg egy szinapszis-munkaterület nem tud csatlakozni a git-tárházhoz, és nem ugyanazt a folyamatos integrációt és kézbesítési folyamatot követi, mint Azure Data Factory.
* **Integráció az Azure monitorral:** A szinapszis-munkaterületek nem integrálható a Azure Monitor, mert Azure Data Factory.
* **Hibrid Integration Runtime-konfiguráció:** A szinapszis munkaterületen belül a felhasználók nem rendelkezhetnek felügyelt VNet IR-vel és Azure IRval. Ez a funkció Azure Data Factory támogatott.
* **Integration Runtime megosztása:** A saját üzemeltetésű integrációs modulok nem oszthatók meg a szinapszis-munkaterületek között. Ez a funkció Azure Data Factory támogatott.
* **Régiók közötti integrációs futtatókörnyezetek az adatforgalomhoz:** Az adatfolyamatok nem futhatnak az integrációs modulokon különböző régiókban, mint a szinapszis-munkaterületek. Ez a funkció Azure Data Factory támogatott.

## <a name="next-steps"></a>További lépések

Ismerkedjen meg az adatintegrációval a szinapszis munkaterületen azáltal, hogy megtanulja, hogyan kell beolvasni az [adatAzure Data Lake Storage Gen2-fiókba](data-integration-data-lake.md).
