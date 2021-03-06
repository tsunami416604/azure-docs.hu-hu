---
title: 'Oktatóanyag: Ismerkedés az Azure szinapszis Analytics szolgáltatással – a szinapszis-munkaterület monitorozása'
description: Ebből az oktatóanyagból megtudhatja, hogyan figyelheti a tevékenységeket a szinapszis-munkaterületen.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: monitoring
ms.topic: tutorial
ms.date: 10/15/2020
ms.openlocfilehash: c2260cc0d2c6b889b44c738576e33abceebe23f1
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96444410"
---
# <a name="monitor-your-synapse-workspace"></a>A szinapszis-munkaterület figyelése

Ebből az oktatóanyagból megtudhatja, hogyan figyelheti a tevékenységeket a szinapszis-munkaterületen. Az SQL, a Apache Spark és a folyamatok aktuális és korábbi tevékenységeit is figyelheti. 

## <a name="introduction-to-the-monitor-hub"></a>A figyelő központ bemutatása

Nyissa meg a szinapszis Studio alkalmazást, és navigáljon a **figyelő** központhoz. Itt láthatja az összes olyan tevékenység előzményeit, amely a munkaterületen zajlik, és melyek aktívak. 

* Az **integráció** alatt nyomon követheti a folyamatokat, az eseményindítókat és az integrációs modulokat.
* A **tevékenységek** területen nyomon követheti a Spark és az SQL tevékenységeket. 

## <a name="integration"></a>Integráció

1. Navigáljon az **integrációs > folyamathoz**. Ebben a nézetben láthatja, hogy a folyamat minden alkalommal fut a munkaterületen. 
1. Keresse meg az előző lépésben futtatott folyamatot, és kattintson a **folyamatának nevére** a részletek megtekintéséhez.
1. A szinapszis Studio felső részén található **navigációs sáv** lehetőségre kattintva az előző nézethez való visszatéréshez kattintson a **minden folyamat futtatása** elemre.

## <a name="apache-spark-activities"></a>Apache Spark tevékenységek

1. Navigáljon az **integrációs > tevékenységekhez > Apache Spark alkalmazásokhoz**. Most már megtekintheti az összes olyan Spark-alkalmazást, amely fut vagy fut a munkaterületen.
1. Keressen olyan alkalmazást, amely már nem fut, és kattintson az **alkalmazás nevére**. Most megtekintheti a Spark-alkalmazás részleteit.
1. Ha már ismeri a Apache Sparkt, megkeresheti a standard Apache Spark History Server felhasználói felületet a **Spark History Serverre** kattintva.

## <a name="sql-activities"></a>SQL-tevékenységek

1. Navigáljon az **integrációs > tevékenységekhez > SQL-kérelmekre**.
1. Ebben a nézetben az SQL-kérelmeket láthatja.
1. Válassza ki a figyelni kívánt **készletet** . Most láthatja, hogy az összes futó SQL-kérelem vagy a munkaterületen fut a készletben.
1. Keresse meg az adott SQL-kérelmet, és vigye az egérmutatót az adott elemre. Ha a hover, megjelenik egy SQL-parancsfájl ikon.
1. Kattintson az SQL-parancsfájl ikonra az SQL-kérelem teljes szövegének megtekintéséhez.

    > [!NOTE] 
    > A szinapszis studión keresztül elküldött SQL-kérelmek egy olyan munkaterületen, amelyen engedélyezve van a dedikált SQL-készlet (korábbi nevén SQL DW), megtekinthetők a figyelő központban. Az összes többi figyelési tevékenységhez lépjen Azure Portal dedikált SQL-készlet (korábban SQL DW) figyelése lehetőségre.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerkedés a Knowledge centerrel](get-started-knowledge-center.md)
