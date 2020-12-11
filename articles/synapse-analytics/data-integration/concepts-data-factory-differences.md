---
title: Eltérések az Azure Data Factoryhoz képest
description: Ismerje meg, hogyan különbözik az Azure szinapszis Analytics adatintegrációs képességei a Azure Data Factory
services: synapse-analytics
author: kromerm
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: makromer
ms.reviewer: jrasnick
ms.openlocfilehash: a8fd0ef006b246e30c02cfb321c72b4e070f54de
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109147"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Adatintegráció az Azure szinapszis Analyticsben és Azure Data Factory

Az Azure szinapszis Analyticsben az adatintegrációs képességek, például a szinapszis-folyamatok és az Adatáramlások a Azure Data Factoryon alapulnak. További információ: [What is Azure Data Factory](../../data-factory/introduction.md).


## <a name="available-features-in-azure-data-factory-and-azure-synapse-analytics"></a>Az Azure Data Factory és az Azure szinapszis Analytics elérhető funkciói

Tekintse meg az alábbi táblázatot a funkciók rendelkezésre állásához:

| Kategória                 | Funkció    |  Azure Data Factory  | Azure Synapse Analytics |
| ------------------------ | ---------- | :------------------: | :---------------------: |
| **Integration Runtime**  | A SSIS és a SSIS Integration Runtime használata | ✓ | ✗ |
|                          | Régiók közötti Integration Runtime támogatása (adatfolyamatok) | ✓ | ✗ |
|                          | Integration Runtime megosztás | ✓<br><small>*Megosztható különböző adatüzemek között* | ✗ |
|                          | Élettartam | ✓ | ✗ |
| **Folyamatok tevékenységei** | SSIS-csomag tevékenység | ✓ | ✗ |
|                          | Power Query tevékenység támogatása | ✓ | ✓ |
| **A Template Gallery és a Knowledge Center** | Megoldássablonok | ✓<br><small>*Azure Data Factory sablon-gyűjtemény* | ✓<br><small>*A szinapszis munkaterület tudásalapú központja* |
| **GIT-tárház integrációja** | GIT-integráció | ✓ | ✓ |
| **Figyelés**           | Spark-feladatok figyelése adatfolyamként | ✗ | ✓<br><small>*A szinapszis Spark-készletek kihasználása* |
|                          | Integráció a Azure Monitor | ✓ | ✗ |

> [!Note]
> Az **élő élettartam** egy olyan Azure Integration Runtime-beállítás, amely lehetővé teszi, hogy a Spark-fürt az adatfolyamok végrehajtása után egy ideig *melegen maradjon* .
>


## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg az adatintegrációval a szinapszis munkaterületen azáltal, hogy megtanulja, hogyan kell beolvasni az [adatAzure Data Lake Storage Gen2-fiókba](data-integration-data-lake.md).
