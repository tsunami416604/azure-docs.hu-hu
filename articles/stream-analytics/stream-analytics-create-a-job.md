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
ms.openlocfilehash: 05fdf1e20efd129cdfc27e1d37bc9e124edf5dcd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-a-data-analytics-processing-job-for-stream-analytics"></a>A Stream Analytics egy analytics feldolgozási feladatot létrehozása
A legfelső szintű erőforrás Azure Stream Analytics egy Stream Analytics-feladat.  Egy vagy több bemeneti adatforrások, a lekérdezés megadása a data transformation, és legalább egy kimeneti tárolókat eredmények írt áll. Együtt ezek lehetővé teszik a felhasználónak az adatfolyamként történő adatáttelepítések esetében feldolgozása adatelemzés végrehajtása.

A Stream Analytics használatának megkezdéséhez először hozzon létre egy új Stream Analytics-feladat.  Vegye figyelembe, hogy ez a művelet nem számlázási hatással van, amíg a feladat elindult.

1. Jelentkezzen be az online [a klasszikus Azure portálon](http://manage.windowsazure.com) vagy a [Azure-portálon](https://portal.azure.com/).
2. A portál: **kattintson az új**, majd kattintson a **Data Services** vagy **adatelemzés** attól függően, hogy a portálon, és kattintson **Azure Stream Analytics** vagy **Stream Analytics** , majd **Gyorslétrehozás**.
   
   ![Az elemzés adatfeldolgozás feladat varázsló](./media/stream-analytics-create-a-job/1-stream-analytics-create-a-job.png)  
   
   ![Hozzon létre a feldolgozás adatelemzés](./media/stream-analytics-create-a-job/4-stream-analytics-create-a-job.png)  
3. Adja meg a Stream Analytics-feladat kívánt beállításait.
   
   * Az a **feladatnév** mezőbe írja be a Stream Analytics-feladat azonosító nevet. Ha a **feladatnév** van hitelesítve, egy zöld pipa jelenik meg a feladat neve mezőbe. A **feladatnév** csak alfanumerikus karaktereket tartalmazhat, és a "-" karakter, és 3 és 63 karakter közé kell esnie.
   * Használjon **régió** az Azure portálon vagy **hely** megadhatja a földrajzi helyet, ahol szeretné futtatni a feladatot az Azure portálon található.
   * Ha az Azure portál használatával, válassza ki vagy hozzon létre egy tárfiókot kívánja használni, mint a **Monitoring Storage-fiók regionális**. Ez a tárfiók összes Stream Analytics-feladatok ebben a régióban fut a figyelési adatok tárolására szolgál.
   * Ha az Azure portál használatával adja meg egy új vagy meglévő **erőforráscsoport** az alkalmazáshoz kapcsolódó erőforrások tárolásához.
4. Az új Stream Analytics-feladat beállításainak konfigurálása után kattintson **Stream Analytics-feladat létrehozása**. A Stream Analytics-feladat, létre kell néhány percet is igénybe vehet. A állapotának ellenőrzéséhez a folyamatot az értesítési központ a figyelheti.
   
   ![Adatok analytics feldolgozási feladat értesítési központ](./media/stream-analytics-create-a-job/2-stream-analytics-create-a-job.png)  
   
   ![Feldolgozása az Azure portál adatelemzés feladat-feladat létrehozása](./media/stream-analytics-create-a-job/5-stream-analytics-create-a-job.png)  
5. Az új feladat állapota megjelenik **Created**. Figyelje meg, hogy a **Start** gomb le van tiltva. Konfigurálja a feladat bemeneti, a lekérdezés és a kimeneti a feladat indítása előtt.
   
   ![Az elemzés adatfeldolgozás feladat állapota](./media/stream-analytics-create-a-job/3-stream-analytics-create-a-job.png)  
   
   ![Feladatállapot feldolgozása az Azure portál adatelemzés](./media/stream-analytics-create-a-job/6-stream-analytics-create-a-job.png)  

## <a name="get-help"></a>Segítségkérés
További támogatásért keresse fel az [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Következő lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

