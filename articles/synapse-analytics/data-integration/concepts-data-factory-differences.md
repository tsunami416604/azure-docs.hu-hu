---
title: Különbségek a Azure Data Factory
description: Ismerje meg, hogyan különbözik az Azure szinapszis Analytics adatintegrációs képességei a Azure Data Factory
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: be098977ba51e529aaecfb5dc3b7a03444f55a7e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91343051"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Adatintegráció az Azure szinapszis Analyticsben és Azure Data Factory

Az Azure szinapszis Analyticsben az adatintegrációs képességek, például a szinapszis-folyamatok és az Adatáramlások a Azure Data Factoryon alapulnak. További információ: [What is Azure Data Factory](../../data-factory/introduction.md). Szinte az összes funkció azonos vagy hasonló, és a dokumentáció a két szolgáltatás között van megosztva. Ez a cikk kiemeli és azonosítja a Azure Data Factory és az Azure szinapszis közötti jelenlegi különbségeket.

Ha szeretné megtekinteni, hogy egy Azure Data Factory funkció vagy cikk az Azure Szinapszisra vonatkozik-e, tekintse meg a cikk tetején található monikert.

![A monikerre vonatkozik](../media/concepts-data-factory-differences/applies-to-moniker.png "A monikerre vonatkozik")

## <a name="features-in-azure-data-factory-not-planned-for-azure-synapse"></a>Az Azure Szinapszishoz nem tervezett szolgáltatások Azure Data Factory

A következő szolgáltatások érhetők el Azure Data Factoryban, de az Azure Szinapszishoz nem tervezték őket.

* SSIS-csomagok emelésének és átváltásának lehetősége.
* A hópehely a másolási tevékenység és az adatforgalom leképezése fogadóként.
* Az Azure Integration Runtime élő beállításának leképezési folyamatának ideje.

## <a name="azure-synapse-features-not-supported-in-azure-data-factory"></a>Az Azure szinapszis szolgáltatásai nem támogatottak Azure Data Factory

A következő szolgáltatások érhetők el az Azure Szinapszisban, de nem tervezik Azure Data Factory számára.

* A leképezési adatfolyamatok Spark-feladatainak figyelése csak a Szinapszisban érhető el. A Szinapszisban a Spark motor a felhasználó előfizetésében található, így a felhasználók megtekinthetik a részletes Spark-naplókat. Azure Data Factory a feladatok végrehajtása Azure Data Factory által felügyelt Spark-fürtön történik. 

## <a name="azure-data-factory-features-that-behave-differently-in-synapse"></a>A Szinapszisban eltérően viselkedő szolgáltatások Azure Data Factory

A következő funkciók vagy eltérően viselkednek, vagy jelenleg nem léteznek az Azure Szinapszisban. 

* Huzavona-adatfolyamok
* A megoldás sablonjának gyűjteménye
* Git-integráció és egy natív CI/CD-megoldás
* Integráció az Azure monitorral
* Erőforrások átnevezése közzététel után
* Hibrid integrációs modul konfigurációja a szinapszis munkaterületen belül. A felhasználók nem rendelkezhetnek felügyelt VNet IR-vel és Azure IR.
* Integrációs futtatókörnyezet megosztása a szinapszis-munkaterületek között

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg az adatintegrációval a szinapszis munkaterületen azáltal, hogy megtanulja, hogyan kell beolvasni az [adatAzure Data Lake Storage Gen2-fiókba](data-integration-data-lake.md).
