---
title: 'Gyors útmutató: beszédek, szándékok és entitások felismerése, Python-Speech szolgáltatás'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/27/2019
ms.author: erhopf
ms.openlocfilehash: aee5d6e1f6ed0519df7d1059b39f215d9f0d9091
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660506"
---
A szándék-felismerési útmutató befejezéséhez létre kell hoznia egy LUIS-fiókot és egy projektet a LUIS betekintő portál használatával. Ehhez a rövid útmutatóhoz csak LUIS-előfizetés szükséges. Nincs szükség beszédfelismerési szolgáltatás előfizetésére.

Első lépésként létre kell hoznia egy LUIS-fiókot és-alkalmazást a LUIS betekintő portál használatával. A létrehozott LUIS-alkalmazás egy előre elkészített tartományt fog használni a Home Automation számára, amely a leképezéseket, az entitásokat és a példa hosszúságú kimondott szöveg biztosítja. Ha elkészült, egy, a felhőben futó LUIS-végponttal fog rendelkezni, amely a Speech SDK használatával hívható meg. 

A LUIS-alkalmazás létrehozásához kövesse az alábbi utasításokat: 

* <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-get-started-create-app" target="_blank">Gyors útmutató: előre elkészített tartományi alkalmazás készítése</a>

Ha elkészült, három dologra lesz szüksége: 

* A LUIS-kulcs
* A LUIS régió
* A LUIS-alkalmazás azonosítója

Itt találja ezt az információt a [Luis betekintő portálon](https://preview.luis.ai/):

1. A LUIS betekintő portálon válassza a **kezelés**, majd az **Azure-erőforrások**lehetőséget. Ezen az oldalon megtalálja a LUIS-kulcsot és-helyet (más néven _régiót_).  

   > [!div class="mx-imgBorder"]
   > ![LUIS-kulcs és-hely](../../../media/luis/luis-key-region.png)

2. Miután megkapta a kulcsot és a helyet, szüksége lesz az alkalmazás AZONOSÍTÓJÁRA. **Alkalmazás-beállítások** kiválasztása – az alkalmazás azonosítója elérhető ezen az oldalon.

   > [!div class="mx-imgBorder"]
   > ![LUIS-alkalmazás azonosítója](../../../media/luis/luis-app-id.png)