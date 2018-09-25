---
title: Az Azure Data Explorer fürt állapotának ellenőrzése
description: Ez a cikk ismerteti a lépéseket annak megállapításához, hogy az Azure Data Explorer fürt kifogástalan állapotú.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 0746247d2c912ba66e81b95f45b168e32b522130
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46988427"
---
# <a name="check-the-health-of-an-azure-data-explorer-cluster"></a>Az Azure Data Explorer fürt állapotának ellenőrzése

Nincsenek számos tényező befolyásolja az Azure Data Explorer egy fürt, többek között a CPU, memória és a lemez alrendszerébe állapotát. Ez a cikk bemutatja a fürt állapotának felmérésére használjuk is igénybe vehet néhány alapvető lépéseit.

1. Jelentkezzen be a [ https://dataexplorer.azure.com ](https://dataexplorer.azure.com).

1. A bal oldali panelen válassza ki a fürtöt, és futtassa a következő parancsot.

    ```Kusto
    .show diagnostics
    | project IsHealthy
    ```
    Egy 1 kimenete kifogástalan; egy 0 kimenete sérült.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és keresse meg a fürthöz.

1. Alatt **figyelés**, jelölje be **metrikák**, majd **életben tartsa**, ahogy az alábbi képen látható. 1-hez közeli kimenet azt jelenti, hogy a fürt kifogástalan állapotú.

    ![Fürt életben tartsa metrika](media/check-cluster-health/portal-metrics.png)

1. Adja hozzá például a Processzor- és memória-gyorsítótárazás a mérőműszer-Erőforrás-kihasználtsága a fürt más metrikákkal.

1. Ha a fürt állapotának a problémák diagnosztizálása segítségre van szüksége, nyisson egy támogatási kérést a [az Azure portal](https://portal.azure.com).