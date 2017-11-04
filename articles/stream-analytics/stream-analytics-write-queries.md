---
title: "A Stream Analytics lekérdezések írásával |} Microsoft Docs"
description: "A Stream Analytics és adatait kérdezi le a lekérdezéseket írhat |} tanulási elérésiút-szegmens."
keywords: "lekérdezi írásával, adatait kérdezi le, a lekérdezések írásáról lekérdezés írása"
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 0e9cdadd-0ee0-4bee-b65b-4a06fb863c95
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 215b774c20d80a67b1cefa2634131bd44860c692
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-write-queries-in-stream-analytics"></a>A Stream Analytics lekérdezések írásával
A streamfeldolgozó logikákat, az Azure Stream Analytics lekérdezések írásáról valósul meg "állandó lekérdezés" van meghatározva, mielőtt egy feladat elindul, és végre az adatok, mivel a feladat eléri. A adatátalakítást lekérdezési SQL-szerű nyelven, amely része a nagy mértékben T-SQL egy hozzáadott nyelvi fájlkiterjesztéseket – például [Ablakozó](https://msdn.microsoft.com/library/azure/dn835019.aspx) express historikus szemantika használatával.

## <a name="writing-queries"></a>Lekérdezések írásáról:
1. A Stream Analytics-feladat az Azure portálon, kattintson **lekérdezés**.
   
    ![Válassza ki a lekérdezés](./media/stream-analytics-write-queries/1-stream-analytics-write-queries.png)  
   
    Az Azure portálon kattintson **lekérdezés**.
   
    ![Válassza ki a lekérdezés előnézeti](./media/stream-analytics-write-queries/query-preview-portal.png)  
2. Új lekérdezés sablon első lépések megtételéhez rendelkezik. A lekérdezés sablon lekérdezést hajt végre "csatlakoztatott", amely minden projektek mezők a bemeneti események a kimeneti be.  
   
   * Ha a feladat legalább egy bemeneti és kimeneti meghatározta, lecserélheti a helyőrző "[YourOutputAlias]", és a bemeneti és kimeneti megadott kívánt aliasok "[YourInputAlias]" mezőket először használja. Ezenkívül is hozhatnak létre és a lekérdezés tesztelése a klasszikus Azure-portálon a feladathoz bemenetekhez és kimenetekhez definiálása nélkül.
   * Ha szeretne végrehajtani, mint egy egyszerű áteresztő további feldolgozás, módosíthatja a lekérdezés definíciója. Lekérdezés szerzői megkezdéséhez, tekintse meg néhány gyakori lekérdezési minták a rendszer rögzíti [Itt](stream-analytics-stream-analytics-query-patterns.md).  
   
   ![Ablak adatait](./media/stream-analytics-write-queries/2-stream-analytics-write-queries.png)  

## <a name="to-validate-query-data-is-working"></a>Lekérdezési adatok érvényesítéséhez működik:
Tesztelheti, hogy a lekérdezés futtatása a böngészőben egy vagy több helyi JSON a fájlokat tartalmazó Tesztadatok által várt viselkedik-e. Ez nem fog elindulni a feladat vagy számlázási hatással van.

> [!NOTE]
> Jelenleg a böngészőben a lekérdezéstesztelés nem támogatott az Azure portálon.  
> 
> 

1. Győződjön meg arról, hogy nincsenek-e hibák a lekérdezés (ellenkező esetben a Teszt gombra letiltja), majd kattintson a vizsgálat gombra.  
   
   ![Teszt adatait](./media/stream-analytics-write-queries/3-stream-analytics-write-queries.png)  
2. Adja meg azokat a fájlokat az egyes az adatokat a lekérdezésben hivatkozott kéri. Ebben a példában a sablon lekérdezés állapotban maradt-van, így a "yourinputalias" nevű bemeneti arra kéri a párbeszédpanelen.
   
   ![Adatok lekérdezés tesztelése](./media/stream-analytics-write-queries/4-stream-analytics-write-queries.png)  
3. Jelöljön ki egy teszt fájlt. Több mintafájlt érhető el a [github](https://github.com/Azure/azure-stream-analytics/tree/master/Sample Data) és a saját adatok adatfolyam bemenetei a mintaadatok függvény a bemeneti adatok lapon keresztül is lekérhetik mintaadatok.
   
   ![A bemeneti lekérdezés](./media/stream-analytics-write-queries/5-stream-analytics-write-queries.png)  
4. A párbeszédpanel bezárása, után a Tesztadatok keresztül futtatni szeretné a lekérdezést, és látni fogja az eredményeket a lekérdezés lap alján.
   
   ![Lekérdezés összegzése](./media/stream-analytics-write-queries/6-stream-analytics-write-queries.png)  

## <a name="get-help"></a>Segítségkérés
További támogatásért keresse fel az [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Következő lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

