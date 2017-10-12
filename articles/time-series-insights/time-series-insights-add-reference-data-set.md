---
title: "Referencia-adatkészlet hozzáadása Azure Time Series Insight-környezethez | Microsoft Docs"
description: "Ebben az oktatóanyagban referencia-adatkészletet fog hozzáadni az Azure Time Series Insights-környezetéhez"
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/29/2017
ms.author: venkatja
ms.openlocfilehash: 574f3c5b3a889733f47d729c795ec39009f2ef4a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-ibiza-portal"></a>Referencia-adatkészlet létrehozása Azure Time Series Insights-környezethez az Ibiza Portal használatával

Egy referencia-adatkészlet az eseményforrásbeli eseményekkel kibővített elemek gyűjteménye. A Time Series Insights bejövő forgalmat kezelő motorja a referencia-adatkészlet egy elemét csatolja egy eseményforrásbeli eseményhez. Ez a kibővített esemény ezután lekérdezhető. A csatolás a referencia-adatkészletben definiált kulcsokon alapul.

## <a name="steps-to-add-a-reference-data-set-to-your-environment"></a>Referencia-adatkészlet környezethez adása lépésenként

1. Jelentkezzen be az [Ibiza Portalra](https://portal.azure.com).
2. Az Ibiza Portal bal oldali menüjében kattintson a „Minden erőforrás” lehetőségre.
3. Válassza ki az Azure Time Series Insights-környezetet.

    ![A Time Series Insights referencia-adatkészlet létrehozása](media/add-reference-data-set/getstarted-create-reference-data-set-1.png)

4. Válassza a „Referencia-adatkészletek” lehetőséget, majd kattintson a „+ Hozzáadás” gombra.

    ![A Time Series Insights referencia-adatkészlet létrehozása – részletesen](media/add-reference-data-set/getstarted-create-reference-data-set-2.png)

5. Adja meg a referencia-adatkészlet nevét.
6. Adja meg a kulcs nevét és típusát. E név és típus alapján lesz kiválasztva a megfelelő tulajdonság az eseményforrásbeli eseményből. Ha kulcsnévként például „EszkozAzon”-t ad meg, típusként pedig karakterláncot („String”), akkor a Time Series Insights bejövő forgalmat kezelő motorja egy „EszkozAzon” nevű, karakterlánc típusú tulajdonságot fog keresni a beérkező eseményben. Az eseményhez csatoláshoz egynél több kulcs is megadható. A tulajdonságnév egyeztetésénél számítanak a kis- és nagybetűk.

     ![A Time Series Insights referencia-adatkészlet létrehozása – részletesen](media/add-reference-data-set/getstarted-create-reference-data-set-3.png)

7. Kattintson a „Létrehozás” elemre.

## <a name="next-steps"></a>Következő lépések

* [Referencia-adatok kezelése](time-series-insights-manage-reference-data-csharp.md) programozott módon.
* Az alkalmazásprogramozási felület (API) teljes leírását a [Referencia-adatok API](/rest/api/time-series-insights/time-series-insights-reference-reference-data-api) dokumentum tartalmazza.