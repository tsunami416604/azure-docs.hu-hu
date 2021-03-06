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
ms.openlocfilehash: 8818d4db489cef8203ae515c18c61e215d577033
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387615"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Adatintegráció az Azure szinapszis Analyticsben és Azure Data Factory

Az Azure szinapszis Analyticsben az adatintegrációs képességek, például a szinapszis-folyamatok és az Adatáramlások a Azure Data Factoryon alapulnak. További információ: [What is Azure Data Factory](../../data-factory/introduction.md).


## <a name="available-features-in-adf--azure-synapse-analytics"></a>Az ADF elérhető funkciói & Azure szinapszis Analytics

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
