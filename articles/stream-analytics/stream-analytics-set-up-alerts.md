---
title: "A Stream Analytics lekérdezések beállítása |} Microsoft Docs"
description: "Riasztások ismertetése a Stream Analytics"
keywords: "Riasztások beállítása"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 9952e2cf-b335-4a5c-8f45-8d3e1eda2e20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/26/2017
ms.author: samacha
ms.openlocfilehash: cdbd7da36f3eb2795fc4548915ebbf18200ca44d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Azure Stream Analytics-feladatok beállítása
## <a name="introduction-monitor-page"></a>Bemutató: Figyelő lapja
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
További támogatásért keresse fel az [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Következő lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-get-started.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

