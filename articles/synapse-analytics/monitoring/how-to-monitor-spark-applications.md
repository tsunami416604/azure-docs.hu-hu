---
title: Apache Spark-alkalmazások figyelése
description: Az Azure Synapse Studio segítségével figyelheti az Apache Spark-alkalmazásokat.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: f231693fdcf3519e29eed38e47db52d92acc00fa
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430746"
---
# <a name="use-the-azure-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>Az Azure Synapse Studio használatával (előzetes verzió) figyelheti apache spark-alkalmazásait

Az Azure Synapse Analytics segítségével a Spark segítségével jegyzetfüzeteket, feladatokat és más típusú alkalmazásokat futtathat a spark-készleteken a munkaterületen.

Ez a cikk bemutatja, hogyan figyelheti a Spark-alkalmazások, amely lehetővé teszi, hogy tartsa szemmel a legújabb állapot, problémák és a folyamat.

## <a name="accessing-the-list-of-spark-applications"></a>A Spark-alkalmazások listájának elérése

A Spark-alkalmazások listájának megtekintéséhez a munkaterületen, először [nyissa meg az Azure Synapse Studio,](https://web.azuresynapse.net/) és válassza ki a munkaterületet.

  > [!div class="mx-imgBorder"]
  > ![Bejelentkezés a munkaterületre](./media/common/login-workspace.png)

Miután megnyitotta a munkaterületet, válassza a bal oldali **Figyelő** szakaszt.

  > [!div class="mx-imgBorder"]
  > ![A Figyelő központ kiválasztása](./media/common/left-nav.png)

Válassza a **Spark-alkalmazások** a Spark-alkalmazások listájának megtekintéséhez.

  > [!div class="mx-imgBorder"]
  > ![Spark-alkalmazások kiválasztása](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filtering-your-spark-applications"></a>A Spark-alkalmazások szűrése

Szűrheti a Spark-alkalmazások listáját az okra, amelyek érdeklik. A képernyő tetején lévő szűrők lehetővé teszik, hogy megadjon egy mezőt, amelyre szűrni szeretne.

Szűrheti például a nézetet, hogy csak az "értékesítés" nevet tartalmazó Spark-alkalmazások jelenjenek meg:

  > [!div class="mx-imgBorder"]
  > ![Szűrő gomb](./media/common/filter-button.png)

  > [!div class="mx-imgBorder"]
  > ![Mintaszűrő](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="viewing-details-about-a-specific-spark-application"></a>Adott Spark-alkalmazás részleteinek megtekintése

A Spark-alkalmazások egyikének részleteinek megtekintéséhez válassza ki a Spark-alkalmazást, és tekintse meg a részleteket. Ha a Spark-alkalmazás még mindig fut, figyelheti a folyamatot.

## <a name="next-steps"></a>További lépések

A figyelési folyamat futtatása, tekintse meg a [Figyelő folyamat fut az Azure Synapse Studio](how-to-monitor-pipeline-runs.md) cikket.  
