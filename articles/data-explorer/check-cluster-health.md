---
title: Az Azure Data Explorer fürt állapotának ellenőrzése
description: Ez a cikk ismerteti a lépéseket az Azure Data Explorer fürt állapotának monitorozásához.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a280d8869a3790444a97c38f792a3d9eeb6bde1d
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59046546"
---
# <a name="check-the-health-of-an-azure-data-explorer-cluster"></a>Az Azure Data Explorer fürt állapotának ellenőrzése

Nincsenek számos tényező befolyásolja az Azure Data Explorer egy fürt, többek között a CPU, memória és a lemez alrendszerébe állapotát. Ez a cikk bemutatja a fürt állapotának felmérésére használjuk is igénybe vehet néhány alapvető lépéseit.

1. Jelentkezzen be itt: [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. A bal oldali panelen válassza ki a fürtöt, és futtassa a következő parancsot.

    ```Kusto
    .show diagnostics
    | project IsHealthy
    ```
    Egy 1 kimenete kifogástalan; egy 0 kimenete sérült.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és keresse meg a fürthöz.

1. Alatt **figyelés**, jelölje be **metrikák**, majd **életben tartsa**, ahogy az alábbi képen látható. 1-hez közeli kimenet azt jelenti, hogy a fürt kifogástalan állapotú.

    ![Fürt életben tartsa metrika](media/check-cluster-health/portal-metrics.png)

1. A diagram egyéb metrika hozzáadása lehetőség. Ezután válassza ki a diagram **metrika hozzáadása**. Válassza ki egy másik metrikát – Ez a példa bemutatja **CPU**.

    ![Metrika hozzáadása](media/check-cluster-health/add-metric.png)

1. Ha a fürt állapotának a problémák diagnosztizálása segítségre van szüksége, nyisson egy támogatási kérést a [az Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).