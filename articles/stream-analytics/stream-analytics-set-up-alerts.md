---
title: Figyelési értesítések az Azure Stream Analytics-feladatok beállítása
description: A cikkből megtudhatja, hogyan használható az Azure-portálon figyelése és Azure Stream Analytics-feladatok riasztásainak beállítása.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/26/2017
ms.openlocfilehash: fb69f1b52e5b17dd6794c896969c29003a929c9b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-set-up-alerts-for-azure-stream-analytics-jobs"></a>Oktatóanyag: Azure Stream Analytics-feladatok riasztások beállítása
Figyelmeztetést jelenít meg, ha egy metrika eléri a megadott feltétel riasztásokat állíthat be. Például beállíthat egy feltételt a következőhöz hasonló értesítést:

`If there are zero input events in the last 5 minutes, send email notification to sa-admin@example.com`

Szabályok mérőszámokat a portálon keresztül is beállítható, vagy konfigurálhatók [programozott módon](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) műveletnaplók adatok.

## <a name="set-up-alerts-in-the-azure-portal"></a>Az Azure portálon riasztások beállítása
1. Nyissa meg a Stream Analytics-feladat szeretne létrehozni egy riasztást az Azure-portálon. 

2. Az a **feladat** panelen kattintson a **figyelés** szakasz.  

3. Az a **metrika** panelen kattintson a **riasztás hozzáadása** parancsot.

      ![Az Azure portál beállítása](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)  

4. Adjon meg egy nevet és leírást.

5. A választók használatával adja meg a feltétel, amely alatt a riasztást küld.

6. A riasztás hová kell ismertetik.

      ![Egy Azure Streaming Analytics-feladat riasztás beállítása](./media/stream-analytics-set-up-alerts/stream-analytics-add-alert.png)  

Riasztások konfigurálása az Azure-portálon a további részletekért lásd: [riasztási értesítéseket](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  


## <a name="get-help"></a>Segítségkérés
További támogatásért keresse fel az [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-get-started.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

