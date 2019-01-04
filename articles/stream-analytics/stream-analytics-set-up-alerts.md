---
title: Állítsa be a riasztásokat az Azure Stream Analytics-feladatok figyelése
description: Ez a cikk ismerteti, hogyan állítsa be a figyelést és riasztásokat az Azure Stream Analytics-feladatok az Azure portal használatával.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 727747d84d0db32c73fc1a200bcea7e5c149d24b
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53554911"
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Állítsa be a riasztásokat az Azure Stream Analytics-feladatok
Figyelmeztetést jelenít meg, ha egy metrika eléri a megadott feltétel állíthat be riasztásokat. Például előfordulhat, hogy állíthatja be egy adott feltételt, például a következő riasztást:

`If there are zero input events in the last 5 minutes, send email notification to sa-admin@example.com`

Szabályok a metrikák a portálon keresztül is beállítható, vagy konfigurálhatók [programozott módon](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) műveletnaplók adatokon.

## <a name="set-up-alerts-in-the-azure-portal"></a>Az Azure Portal értesítések beállítása
1. Az Azure Portalon nyissa meg a Stream Analytics-feladat szeretne riasztást létrehozni. 

2. Az a **feladat** panelen kattintson a **figyelés** szakaszban.  

3. Az a **metrika** panelen kattintson a **riasztás hozzáadása** parancsot.

      ![Az Azure portal Stream Analytics riasztások beállítása](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)  

4. Adjon meg egy nevet és leírást.

5. A választók használatával definiálhatja a feltételt, amely alatt a riasztást küld.

6. Adja meg a riasztás hová kell adatait.

      ![Az Azure Stream Analytics-feladat riasztás beállítása](./media/stream-analytics-set-up-alerts/stream-analytics-add-alert.png)  

Riasztások konfigurálása az Azure Portalon a további részletekért lásd: [riasztási értesítések fogadása](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  


## <a name="get-help"></a>Segítségkérés
További támogatásért keresse fel az [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-get-started.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

