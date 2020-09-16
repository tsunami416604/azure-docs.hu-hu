---
title: 'Oktatóanyag: Ismerkedés az Azure szinapszis Analytics szolgáltatással – a szinapszis-munkaterület monitorozása'
description: Ebből az oktatóanyagból megtudhatja, hogyan figyelheti a tevékenységeket a szinapszis-munkaterületen.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/27/2020
ms.openlocfilehash: f8be96fb008471d040839141a230c13b8f1657a5
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708172"
---
# <a name="monitor-your-synapse-workspace"></a>A szinapszis-munkaterület figyelése

Ebből az oktatóanyagból megtudhatja, hogyan figyelheti a tevékenységeket a szinapszis-munkaterületen. Az SQL, a Apache Spark aktuális és korábbi tevékenységeit is figyelheti. és folyamatok. 

## <a name="introduction-to-the-monitor-hub"></a>A figyelő központ bemutatása

Nyissa meg a szinapszis Studio alkalmazást, és navigáljon a **figyelő** központhoz. Itt láthatja az összes olyan tevékenység előzményeit, amely a munkaterületen zajlik, és melyek aktívak. 

* A **előkészítés alatt nyomon követheti a**folyamatokat, az eseményindítókat és az integrációs modulokat.
* A **tevékenységek**területen nyomon követheti a Spark és az SQL tevékenységeket. 

## <a name="orchestration"></a>Vezénylés

1. Navigáljon a **>i folyamathoz**. Ebben a nézetben láthatja, hogy a folyamat minden alkalommal fut a munkaterületen. 
1. Keresse meg az előző lépésben futtatott folyamatot, és kattintson a **folyamatának nevére**.
1. Most láthatja, hogyan futnak az egyes tevékenységek a folyamaton belül.
1. A szinapszis Studio felső részén található **navigációs sáv** lehetőségre kattintva az előző nézethez való visszatéréshez kattintson a **minden folyamat futtatása** elemre.

## <a name="apache-spark-activities"></a>Apache Spark tevékenységek

1. Navigáljon a **> tevékenységek > Apache Spark alkalmazásokhoz**. Most már megtekintheti az összes olyan Spark-alkalmazást, amely fut vagy fut a munkaterületen.
1. Keressen olyan alkalmazást, amely már nem fut, és kattintson az **alkalmazás nevére**. Most megtekintheti a Spark-alkalmazás részleteit.
1. Ha már ismeri a Apache Sparkt, megkeresheti a standard Apache Spark History Server felhasználói felületet a **Spark History Serverre**kattintva.

## <a name="sql-activities"></a>SQL-tevékenységek

1. Navigáljon a **> tevékenységek > SQL-kérelmekhez**.
1. Ebben a nézetben az SQL-kérelmeket láthatja.
1. Válassza ki a figyelni kívánt **készletet** . Most láthatja, hogy az összes futó SQL-kérelem vagy a munkaterületen fut a készletben.
1. Keresse meg az adott SQL-kérelmet, és vigye az egérmutatót az adott elemre. Ha a hover, megjelenik egy SQL-parancsfájl ikon.
1. Kattintson az SQL-parancsfájl ikonra az SQL-kérelem teljes szövegének megtekintéséhez.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerkedés a Knowledge centerrel](get-started-knowledge-center.md)
