---
title: "Hogyan kell elindítani a folyamatos átviteli feladat Stream Analytics |} Microsoft Docs"
description: "Az Azure Stream Analytics futtatásának módját a folyamatos átviteli feladatnak |} tanulási elérésiút-szegmens."
keywords: "a folyamatos átviteli feladatok"
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 9d46950f-2b69-49ce-a567-df558c5dd820
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: fb084f1c6b53e2582849e71271e8114a22dcf05c
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="how-to-run-a-streaming-job-in-azure-stream-analytics"></a>A folyamatos átviteli feladatok futtatása az Azure Stream Analytics
Ha a bemeneti feladat, a lekérdezés és a kimeneti összes megadva megkezdheti a Stream Analytics-feladat.

A feladat indítása:

1. A klasszikus Azure portálon, a projekt irányítópultján kattintson **Start** az oldal alján.
   
   ![Indítsa el a feladat gomb](./media/stream-analytics-run-a-job/1-stream-analytics-run-a-job.png)  
   
   Az Azure portálon kattintson **Start** a feladat lap tetején.
   
   ![Az Azure portál indítási feladat gomb](./media/stream-analytics-run-a-job/4-stream-analytics-run-a-job.png)  
2. Adjon meg egy **Start kimeneti** érték határozza meg, ha a feladat indul állít elő. Az alapértelmezett beállítás, amely korábban nem lettek elindítva feladatok **feladat kezdete**, ami azt jelenti, hogy a feladat adatainak feldolgozása azonnal megkezdődik. Azt is megadhatja a **egyéni** idő (a korábbi adatok felhasználásához) múltbeli vagy a jövőben (a jövőbeli időpontig feldolgozási késleltetés). Olyan esetekben, amikor egy feladat korábban elindul vagy leáll, a beállítás **feladat utolsó befejezési időpontja** érhető el, folytassa a feladatot, kimeneti utoljára, és az adatveszteség elkerülése érdekében.  

> [!NOTE]
> Partíciók használata esetén a feladat utolsó befejezési időpontja összes partíciójára legutóbbi kimeneti minimumát jelöli.
   
   ![Indítsa el a folyamatos átviteli feladat idő](./media/stream-analytics-run-a-job/2-stream-analytics-run-a-job.png)  
   
   ![Az Azure portál indítási folyamatos átviteli feladat idő](./media/stream-analytics-run-a-job/5-stream-analytics-run-a-job.png)  
3. Erősítse meg választását. A feladat állapota változik *indítása* és rövidesen áthelyezi *futtató* a feldolgozás megkezdése után. Figyelheti a feladat előrehaladását a a **Start** műveletet a **értesítési központ**:
   
   ![folyamatos átviteli feladatok előrehaladásának](./media/stream-analytics-run-a-job/3-stream-analytics-run-a-job.png)  
   
   ![Folyamatos átviteli feladatok előrehaladásának Azure-portálon](./media/stream-analytics-run-a-job/6-stream-analytics-run-a-job.png)  

## <a name="get-help"></a>Segítségkérés
További támogatásért keresse fel az [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Következő lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

