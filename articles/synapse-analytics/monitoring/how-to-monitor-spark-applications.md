---
title: Apache Spark alkalmazások figyelése
description: Az Azure szinapszis Studio segítségével figyelheti Apache Spark alkalmazásait.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: f231693fdcf3519e29eed38e47db52d92acc00fa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430746"
---
# <a name="use-the-azure-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>A Apache Spark-alkalmazások figyelése az Azure szinapszis Studio (előzetes verzió) használatával

Az Azure szinapszis Analytics segítségével a Spark használatával jegyzetfüzeteket, feladatokat és más típusú alkalmazásokat futtathat a Spark-készletekben a munkaterületen.

Ez a cikk ismerteti, hogyan figyelheti a Spark-alkalmazásokat, így nyomon követheti a legújabb állapotot, problémákat és előrehaladást.

## <a name="accessing-the-list-of-spark-applications"></a>A Spark-alkalmazások listájának elérése

A munkaterületen található Spark-alkalmazások listájának megtekintéséhez először [nyissa meg az Azure szinapszis Studio](https://web.azuresynapse.net/) alkalmazást, és válassza ki a munkaterületet.

  > [!div class="mx-imgBorder"]
  > ![Bejelentkezés a munkaterületre](./media/common/login-workspace.png)

Miután megnyitotta a munkaterületet, kattintson a bal oldalon található **figyelő** szakaszra.

  > [!div class="mx-imgBorder"]
  > ![Figyelő központ kiválasztása](./media/common/left-nav.png)

Válassza a **Spark-alkalmazások** lehetőséget a Spark-alkalmazások listájának megtekintéséhez.

  > [!div class="mx-imgBorder"]
  > ![Spark-alkalmazások kiválasztása](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filtering-your-spark-applications"></a>A Spark-alkalmazások szűrése

A Spark-alkalmazások listáját kiszűrheti azokra, amelyekre kíváncsi. A képernyő felső részén található szűrők segítségével megadhat egy mezőt, amely alapján szűrni szeretne.

Például szűrheti a nézetet úgy, hogy csak azokat a Spark-alkalmazásokat jelenítse meg, amelyek tartalmazzák a "Sales" nevet:

  > [!div class="mx-imgBorder"]
  > ![Szűrő gomb](./media/common/filter-button.png)

  > [!div class="mx-imgBorder"]
  > ![Minta szűrő](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="viewing-details-about-a-specific-spark-application"></a>Adott Spark-alkalmazás részleteinek megtekintése

Ha szeretné megtekinteni az egyik Spark-alkalmazás részleteit, válassza ki a Spark-alkalmazást, és tekintse meg a részleteket. Ha a Spark-alkalmazás még fut, nyomon követheti a folyamatot.

## <a name="next-steps"></a>További lépések

A folyamatok figyelésével kapcsolatos további információkért tekintse meg a [figyelő folyamat az Azure szinapszis Studio futtatása](how-to-monitor-pipeline-runs.md) című cikket.  
