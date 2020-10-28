---
title: 'Oktatóanyag: a folyamatok integrálásának első lépései'
description: Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a folyamatokat és tevékenységeket a szinapszis Studio használatával.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 10/27/2020
ms.openlocfilehash: af01d5b5e424dd5ea229115f7aa3570d0b7cd511
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92744932"
---
# <a name="integrate-with-pipelines"></a>Integráció a folyamatokkal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a folyamatokat és tevékenységeket a szinapszis Studio használatával. 

## <a name="overview"></a>Áttekintés

Az Azure Szinapszisban számos feladatot integrálhat.

1. A szinapszis Studióban nyissa meg az **integrációs** hubot.
1. Válassza **+**  >  a **folyamat** lehetőséget egy új folyamat létrehozásához.
1. Lépjen a **fejlesztés** központba, és válassza ki a korábban létrehozott jegyzetfüzetek egyikét.
1. Húzza a jegyzetfüzetet a folyamatba ( **Megjegyzés** : az importálási modulok hozzáadása lépés a jegyzetfüzetben a [dokumentumban](https://docs.microsoft.com/azure/synapse-analytics/spark/synapse-spark-sql-pool-import-export#transfer-data-to-or-from-a-sql-pool-attached-with-the-workspace)megadott módon, amely a folyamatból való futtatáskor szükséges)
1. A folyamatban válassza a **Hozzáadás trigger**  >  **új/szerkesztés** lehetőséget.
1. Az **eseményindító kiválasztása** területen válassza az **új** lehetőséget, majd állítsa az **ismétlődést** "minden 1 órában" értékre.
1. Kattintson az **OK** gombra. 
1. Kattintson **Az összes közzététele** gombra.
1. Ahhoz, hogy a folyamat azonnal fusson, a következő órára való várakozás nélkül válassza az **trigger** -  >  **trigger** hozzáadása lehetőséget.



## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Adatok megjelenítése Power BI használatával](get-started-visualize-power-bi.md)
                                 
