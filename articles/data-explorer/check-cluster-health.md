---
title: Egy Azure Data Explorer-fürt állapotának ellenőrzése
description: Ez a cikk az Azure Data Explorer-fürt állapotának figyelése lépéseket ismerteti.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a280d8869a3790444a97c38f792a3d9eeb6bde1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60861318"
---
# <a name="check-the-health-of-an-azure-data-explorer-cluster"></a>Egy Azure Data Explorer-fürt állapotának ellenőrzése

Számos tényező befolyásolja az Azure Data Explorer-fürt állapotát, beleértve a processzort, a memóriát és a lemezalrendszert. Ez a cikk néhány alapvető lépést mutat be a fürt állapotának felméréséhez.

1. Jelentkezzen be [https://dataexplorer.azure.com](https://dataexplorer.azure.com)a ikonra.

1. A bal oldali ablaktáblában jelölje ki a fürtöt, és futtassa a következő parancsot.

    ```Kusto
    .show diagnostics
    | project IsHealthy
    ```
    Az 1 kimenet kifogástalan; a 0 kimenet nem kifogástalan.

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)és keresse meg a fürtöt.

1. A **Figyelés**csoportban válassza **a Metrikák**lehetőséget, majd válassza az **Életben tartás**lehetőséget az alábbi képen látható módon. Az 1-hez közeli kimenet kifogástalan állapotú fürtöt jelent.

    ![Fürt életben tartása metrika](media/check-cluster-health/portal-metrics.png)

1. Lehetőség van más mutatók hozzáadására is a diagramhoz. Jelölje ki a diagramot, majd **adja hozzá a mutatót**. Válasszon ki egy másik mérőszámot - ebben a példában a **CPU**látható.

    ![Metrika hozzáadása](media/check-cluster-health/add-metric.png)

1. Ha segítségre van szüksége a fürt állapotával kapcsolatos problémák diagnosztizálásában, nyisson meg egy támogatási kérelmet az [Azure Portalon.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)