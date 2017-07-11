---
title: "Azure Analysis Services oktatóanyag – 8. lecke: Perspektívák létrehozása | Microsoft Docs"
description: "Ismerteti a perspektívák létrehozását az Azure Analysis Services oktatóprojektjében."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/26/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: 491500909b0de0360afae45e172e85999d764fe0
ms.contentlocale: hu-hu
ms.lasthandoff: 06/03/2017

---
<a id="lesson-8-create-perspectives" class="xliff"></a>

# 8. lecke: Perspektívák létrehozása

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Ebben a leckében internetes értékesítési perspektívát hozhat létre. A perspektíva a modell egy olyan megtekinthető alkészletét határozza meg, amely célzott, üzletspecifikus vagy alkalmazásspecifikus szempontokat biztosít. Ha a felhasználó egy perspektíva használatával csatlakozik a modellhez, csak azokat a modellobjektumokat (táblázatokat, oszlopokat, mértékeket, hierarchiákat és KPI-ket) látja, amelyeket a mezők meghatároztak az adott perspektívában. További információkért lásd: [Perspektívák](https://docs.microsoft.com/sql/analysis-services/tabular-models/perspectives-ssas-tabular).
  
Az ebben a leckében létrehozott internetes értékesítés perspektíva nem tartalmazza a DimCustomer táblaobjektumot. Ha egy olyan perspektívát hoz létre, amely kizár bizonyos objektumokat a megtekintésből, ezek az objektumok attól még léteznek a modellben. Ez ugyanakkor nem látható a jelentéskészítő ügyfélmező listájában. A számított oszlopok és mértékek attól függetlenül végezhetnek számításokat a kizárt objektumadatokból, hogy egy perspektíva részét képezik-e.  
  
A lecke célja annak leírása, hogyan hozható létre perspektíva, illetve a táblázatos modell-létrehozási eszköz bemutatása. Ha a későbbiekben kibővíti ezt a modellt, hogy további táblázatokat tartalmazzon, létrehozhat további perspektívákat, amelyek a modell különböző szempontjait határozzák meg, például a leltárt és az értékesítést.  
  
A lecke elvégzésének várható időtartama: **5 perc**.  
  
<a id="prerequisites" class="xliff"></a>

## Előfeltételek  
Ez a témakör a táblázatos modellezéssel foglalkozó oktatóanyag részét képezi, amelyet a megfelelő sorrendben kell elvégezni. Az ebben a leckében található feladatok végrehajtása előtt be kell fejeznie az előző leckét: [7. lecke: Fő teljesítménymutatók létrehozása](../tutorials/aas-lesson-7-create-key-performance-indicators.md).  
  
<a id="create-perspectives" class="xliff"></a>

## Perspektívák létrehozása  
  
<a id="to-create-an-internet-sales-perspective" class="xliff"></a>

#### Internetes értékesítési perspektíva létrehozása  
  
1.  Kattintson a **Modell** menü > **Perspektívák** > **Létrehozás és kezelés** elemére.  
  
2.  A **Perspektívák** párbeszédpanelen kattintson az **Új perspektíva** elemre.  
  
3.  Kattintson duplán az **Új perspektíva** oszlop fejlécére, és nevezze át **Internetes értékesítésre**.  
  
4.  Válassza ki az összes táblát, *kivéve* a **DimCustomer** táblát.  
  
    ![aas-lesson8-perspectives](../tutorials/media/aas-lesson8-perspectives.png)
  
    Egy későbbi leckében használhatja az Elemzés az Excelben szolgáltatást a perspektíva tesztelésére. Az Excel Kimutatás mezők listája a DimCustomer tábla kivételével minden táblát tartalmaz.  

<a id="whats-next" class="xliff"></a>

## A következő lépések
[9. lecke: Hierarchiák létrehozása](../tutorials/aas-lesson-9-create-hierarchies.md).
  
  
  
  

