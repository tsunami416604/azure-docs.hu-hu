---
title: Apache Spark alkalmazások figyelése
description: Az Azure szinapszis Studio segítségével figyelheti Apache Spark alkalmazásait.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: e646f1bc1a25f58dd54437cbd77750479ac9ae99
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2020
ms.locfileid: "85970899"
---
# <a name="use-the-azure-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>A Apache Spark-alkalmazások figyelése az Azure szinapszis Studio (előzetes verzió) használatával

Az Azure szinapszis Analytics segítségével a Spark használatával jegyzetfüzeteket, feladatokat és más típusú alkalmazásokat futtathat a Spark-készletekben a munkaterületen.

Ez a cikk ismerteti, hogyan figyelheti meg Apache Spark-alkalmazásait, így nyomon követheti a legújabb állapotot, problémákat és előrehaladást.

## <a name="accessing-the-list-of-apache-spark-applications"></a>Apache Spark alkalmazások listájának elérése

A munkaterületen lévő Apache Spark alkalmazások listájának megtekintéséhez először [nyissa meg az Azure szinapszis Studio](https://web.azuresynapse.net/) alkalmazást, és válassza ki a munkaterületet.

![Bejelentkezés a munkaterületre](./media/common/login-workspace.png)

Miután megnyitotta a munkaterületet, kattintson a bal oldalon található **figyelő** szakaszra.

![Figyelő központ kiválasztása](./media/common/left-nav.png)

Válassza **Apache Spark alkalmazások** lehetőséget Apache Spark alkalmazások listájának megtekintéséhez.

 ![Spark-alkalmazások kiválasztása](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filtering-your-apache-spark-applications"></a>Apache Spark alkalmazások szűrése

A Apache Spark-alkalmazások listáját szűrheti azokra, akikre kíváncsi. A képernyő felső részén található szűrők segítségével megadhat egy mezőt, amely alapján szűrni szeretne.

Például szűrheti a nézetet úgy, hogy csak a "Sales" nevet tartalmazó Apache Spark alkalmazásokat jelenítse meg:

![Szűrő gomb](./media/common/filter-button.png)

![Minta szűrő](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="viewing-details-about-a-specific-apache-spark-application"></a>Adott Apache Spark alkalmazás részleteinek megtekintése

Ha meg szeretné tekinteni az egyik Apache Spark alkalmazás részleteit, válassza ki a Apache Spark alkalmazást, és tekintse meg a részleteket. Ha a Apache Spark alkalmazás még fut, nyomon követheti a folyamatot. [További információk](apache-spark-applications.md).

## <a name="next-steps"></a>További lépések

A folyamatok figyelésével kapcsolatos további információkért tekintse meg a [figyelő folyamat az Azure szinapszis Studio futtatása](how-to-monitor-pipeline-runs.md) című cikket. 

A Apache Spark alkalmazás hibakeresésével kapcsolatos további információkért tekintse meg a [Apache Spark alkalmazások monitorozása az Azure szinapszis Studio](apache-spark-applications.md) -ban című cikket.