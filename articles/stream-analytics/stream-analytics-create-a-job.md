---
title: "A Stream Analytics egy analytics feldolgozási feladatot létrehozása |} Microsoft Docs"
description: "Hozzon létre egy analytics feldolgozási feladatot a Stream Analytics |} tanulási elérésiút-szegmens."
keywords: "az elemzés adatfeldolgozás"
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: e825fbcf-69e9-443f-b402-3b7a4568f415
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 98784783beccc19df916920fc41364a23e6bae11
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-create-a-data-analytics-processing-job-for-stream-analytics"></a>A Stream Analytics egy analytics feldolgozási feladatot létrehozása
A legfelső szintű erőforrás Azure Stream Analytics egy Stream Analytics-feladat.  Egy vagy több bemeneti adatforrások, a lekérdezés megadása a data transformation, és legalább egy kimeneti tárolókat eredmények írt áll. Együtt ezek lehetővé teszik a felhasználónak az adatfolyamként történő adatáttelepítések esetében feldolgozása adatelemzés végrehajtása.

A Stream Analytics használatának megkezdéséhez először hozzon létre egy új Stream Analytics-feladat.  Vegye figyelembe, hogy ez a művelet nem számlázási hatással van, amíg a feladat elindult.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza ki **hozzon létre egy erőforrást** > **adatok + analitika** > **Stream Analytics-feladat**.
3. Kattintson a **Létrehozás** gombra.
   
3. Adja meg a Stream Analytics-feladat kívánt beállításait.
   
   * Az a **feladatnév** mezőbe írja be a Stream Analytics-feladat azonosító nevet. Ha a **feladatnév** van hitelesítve, egy zöld pipa jelenik meg a feladat neve mezőbe. A **feladatnév** csak alfanumerikus karaktereket tartalmazhat, és a "-" karakter, és 3 és 63 karakter közé kell esnie.
   * Használjon **hely** adhatja meg a földrajzi hely, ahol szeretné futtatni a feladatot.
   * Adjon meg egy új vagy meglévő **erőforráscsoport** az alkalmazáshoz kapcsolódó erőforrások tárolásához.
4. Kattintson a **Létrehozás** gombra.
A Stream Analytics-feladat, létre kell néhány percet is igénybe vehet. A állapotának ellenőrzéséhez a folyamatot az értesítési központ a figyelheti.
    
   ![Feldolgozása az Azure portál adatelemzés feladat-feladat létrehozása](./media/stream-analytics-create-a-job/5-stream-analytics-create-a-job.png)  
5. Az új feladat állapota megjelenik **Created**. Figyelje meg, hogy a **Start** gomb le van tiltva. Konfigurálja a feladat bemeneti, a lekérdezés és a kimeneti a feladat indítása előtt.

   
   ![Feladatállapot feldolgozása az Azure portál adatelemzés](./media/stream-analytics-create-a-job/6-stream-analytics-create-a-job.png)  

## <a name="get-help"></a>Segítségkérés
További támogatásért keresse fel az [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

