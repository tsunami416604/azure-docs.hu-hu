---
title: "Azure Time Series Insights-környezet létrehozása | Microsoft Docs"
description: "Ez az oktatóanyag bemutatja, hogyan hozhat létre Time Series-környezetet, hogyan csatlakoztathatja egy eseményforráshoz és kezdheti elemezni az eseményadatokat percek alatt."
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: santoshb
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/18/2017
ms.author: omravi
ms.openlocfilehash: d8a3f79630868c83cd9fde6ea0c414e334a58e22
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2017
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Új Time Series Insights-környezet létrehozása az Azure Portalon

A Time Series Insights-környezet egy Azure-erőforrás bejövő adatforgalmi és tárolási kapacitással. Az ügyfelek az Azure Portalon hozhatnak létre környezeteket a szükséges kapacitással.

## <a name="steps-to-create-the-environment"></a>A környezet létrehozásának lépései

A környezet létrehozásához kövesse az alábbi lépéseket:

1.  Jelentkezzen be az [Azure Portal](https://portal.azure.com).
2.  Kattintson a plusz jelre („+”) a bal felső sarokban.
3.  A keresőmezőben keressen a „Time Series Insights” kifejezésre.

  ![A Time Series Insights-környezet létrehozása](media/get-started/getstarted-create-environment1.png)

4.  Válassza a „Time Series Insights” elemet, és kattintson a „Létrehozás” lehetőségre.

  ![A Time Series Insights-erőforráscsoport létrehozása](media/get-started/getstarted-create-environment2.png)

5.  Adja meg a környezet nevét. Ez a név jelöli majd a környezetet a [Time Series Explorerben](https://insights.timeseries.azure.com).
6.  Válasszon egy előfizetést. Azt az előfizetést válassza, amely tartalmazza az eseményforrást. A Time Series Insights képes automatikusan észlelni az azonos előfizetésben lévő Azure IoT Hub- és Event Hub-erőforrásokat.
7.  Válasszon ki vagy hozzon létre egy erőforráscsoportot. Az erőforráscsoport olyan Azure-erőforrások gyűjteménye, amelyeket együtt használnak.
8.  Válasszon egy üzemeltetési helyet. Annak érdekében, hogy az adatokat ne kelljen egyik adatközpontból a másikba mozgatni, olyan helyet válasszon, amely tartalmazza az eseményforrást.
9.  Válasszon tarifacsomagot.
10. Válassza ki a kapacitást. A környezet kapacitása a létrehozást követően módosítható.
11. Hozza létre a környezetet. A környezetet rögzítheti az irányítópulton, így a bejelentkezés után könnyen elérheti.

  ![A Time Series Insights rögzítése az irányítópulton](media/get-started/getstarted-create-environment3.png)

## <a name="next-steps"></a>Következő lépések

* [Adathozzáférési házirendek meghatározása](time-series-insights-data-access.md) a környezet eléréséhez a [Time Series Insights portálon](https://insights.timeseries.azure.com)
* [Eseményforrás létrehozása](time-series-insights-add-event-source.md)
* [Események küldése](time-series-insights-send-events.md) az eseményforrásnak
