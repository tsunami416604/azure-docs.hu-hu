---
title: "Hibakeresési művelet használatával és a szolgáltatás naplói a Stream Analytics |} Microsoft Docs"
description: "Használati útmutató Stream Analytics műveleti naplói"
keywords: "Service naplóit"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: a2ed9676-f0bd-4398-87c8-a592779ac728
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 2ee0871752dc2a3da345339fb826340d44ae48d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="debug-stream-analytics-jobs-using-service-and-operation-logs"></a>Hibakeresés és üzemeltetése naplók segítségével Stream Analytics-feladatok
Az összes Azure-szolgáltatások adja meg a felhasználóknak, hogy a felügyeleti műveleteivel kapcsolatos rekord részletei üzemeltetési naplózási üzeneteket. Az Azure Stream Analytics ezt az információt hibakeresési célra például feladat állapotát, a feladatok előrehaladásának megtekintéséhez használható, és nyomon lehet követni a feladat előrehaladását idővel hibaüzenetek indítsa el a kimeneti feldolgozásra.

## <a name="find-operation-logs-in-the-azure-portal"></a>Az Azure-portálon műveletnaplókat keresése
A műveletnaplók kétféleképpen érhető el:  

* A Stream Analytics-feladat irányítópult  
* A klasszikus Azure portál felügyeleti szolgáltatások  

## <a name="dashboard-of-the-stream-analytics-job"></a>A Stream Analytics-feladat irányítópult
A Stream Analytics-feladatok megfelelő naplók mutató hivatkozást a projekt irányítópultján lapon jelenik meg. Ha a hivatkozásra kattint, oly módon, hogy azt mutatja, hogy adott feladat a legújabb naplók beállítja a szűrőket.

  ![Válassza ki a felügyeleti szolgáltatások naplók](./media/stream-analytics-operation-logs/01-stream-analytics-operation-logs.png)  

## <a name="management-services"></a>Felügyeleti szolgáltatások
Lehetőségre, és manuálisan navigáljon a műveletnaplók Stream Analytics és egyéb szolgáltatások, a klasszikus Azure portálon:

1. Kattintson a **szolgáltatások** a a [a klasszikus Azure portálon](https://manage.windowsazure.com).
2. Válassza ki **Stream Analytics** a **típus** és a feladat nevét **szolgáltatásnév**.  
   
   ![Válassza ki a Stream Analytics](./media/stream-analytics-operation-logs/02-stream-analytics-operation-logs.png)  

## <a name="find-audit-logs-in-the-azure-portal"></a>Naplók az Azure portálon található
A Stream Analytics-feladat az Azure portálon található műveleti naplókat, kattintson a **Tallózás** , és válassza **naplók**.

  ![Válassza ki a Stream Analytics Azure-portálon](./media/stream-analytics-operation-logs/06-stream-analytics-operation-logs.png)  

Ekkor megnyílik a megjelenített események az elmúlt hét napban, az összes erőforrás jeleníti meg az előfizetését.  Adjon meg típusú vagy időkereten belül események megtekintéséhez kattintva szűrheti a **szűrő** parancsot.

  ![Válassza ki a Stream Analytics Azure-portálon](./media/stream-analytics-operation-logs/07-stream-analytics-operation-logs.png)  

## <a name="get-log-details"></a>Részletek a naplóban
Időtartomány és a feladat a naplók megtekintéséhez állapot szerint szűrheti.

Az Azure-portálon kattintson a a **részletek** gombra a kijelölt esemény részletes adatainak megtekintése az ablak alján. 

  ![Válassza ki a részletei](./media/stream-analytics-operation-logs/03-stream-analytics-operation-logs.png)  

Az Azure-portálon kattintson a benne lévő részletes eseményeket naplóbejegyzést.

  ![Azure-portálon válassza részletei](./media/stream-analytics-operation-logs/08-stream-analytics-operation-logs.png)  

Ott, megnyithatja a **részletes** megtalálható az esemény kattintva jelennek meg.

  ![Azure-portálon válassza részletei](./media/stream-analytics-operation-logs/09-stream-analytics-operation-logs.png)  

## <a name="debug-a-failed-job"></a>Sikertelen feladat hibakeresése
Az Azure portálon kattintson a keresés ikonra, majd a "sikertelen" típusú. Ez az összes naplók hibákkal eredményt ad. 

  ![Sikertelen feladat-hibakeresés](./media/stream-analytics-operation-logs/04-stream-analytics-operation-logs.png)  

Az Azure portálon, az üzenet megtekintéséhez szintje szerint szűrheti **kritikus** események.

  ![Az Azure portál hibakeresési](./media/stream-analytics-operation-logs/10-stream-analytics-operation-logs.png)  

Válassza ki a hibákat egyikét sem, és törölje a **részletek** további információt a hiba.  Bizonyos hibaüzenetek is ismertetik, hogyan csökkentheti a problémát. 

  ![Művelet részletei](./media/stream-analytics-operation-logs/05-stream-analytics-operation-logs.png)  

Abban az esetben, ha kapcsolatba kell lépnie [támogatási](https://azure.microsoft.com/support/options/) vagy a csapat keresztül nyújtanak a [MSDN fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics), vegye figyelembe a működési részleteket, kifejezetten a **korrelációs azonosító**. 

## <a name="get-help"></a>Segítségkérés
További támogatásért keresse fel az [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Következő lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

