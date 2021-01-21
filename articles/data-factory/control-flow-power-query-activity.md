---
title: Power Query tevékenység a Azure Data Factory
description: Megtudhatja, hogyan használhatja a Power Query tevékenységet a Data Factory-folyamatokban lévő huzavona-funkciókhoz
services: data-factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: 3314053e5b81c597d6d29015a5ebda6e171731d1
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/21/2021
ms.locfileid: "98634231"
---
# <a name="power-query-activity-in-data-factory"></a>Power Query-tevékenység a refactoryban

A Power Query tevékenység lehetővé teszi, hogy Power Query Mash-UPS-t hozzon létre és hajtson végre, hogy az adatmennyiséget huzavona egy Data Factory folyamaton belül. Létrehozhat egy új Power Query mash-up az új erőforrások menüpontból, vagy egy energiaellátási tevékenységet is hozzáadhat a folyamathoz.

![A gyári erőforrások ablaktáblán látható Power Query bemutató képernyőkép.](media/data-flow/power-query-wrangling.png)

Korábban a Azure Data Factory adathuzavona az adatfolyam menüpontból lett létrehozva. Ez új Power Query tevékenységből való létrehozásra módosult. A Power Query mash-up szerkesztőben közvetlenül dolgozhat interaktív adatfeltárási művelettel, majd mentheti a munkáját. Ha elkészült, elvégezheti Power Query tevékenységét, és hozzáadhatja azt egy folyamathoz. A Azure Data Factory automatikusan kibővíti és működővé tenni az adatait a Azure Data Factory adatáramlási Spark-környezetének használatával.

## <a name="translation-to-data-flow-script"></a>Fordítás az adatfolyam-parancsfájlba

Ahhoz, hogy a méretezést a Power Query tevékenységgel lehessen elérni, Azure Data Factory lefordítja a ```M``` szkriptet egy adatfolyam-parancsfájlba, hogy a Power Query a Azure Data Factory adatfolyam Spark-környezettel lehessen végrehajtani. A huzavona-adatfolyamok létrehozása kód nélküli adatelőkészítéssel. Az elérhető függvények listáját lásd: [transzformációs függvények](wrangling-functions.md).

## <a name="next-steps"></a>Következő lépések

További információ az huzavona-fogalmakról [a Power Query használatával Azure Data Factory](wrangling-tutorial.md)
