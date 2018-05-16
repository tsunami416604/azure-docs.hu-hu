---
title: API-k hibakeresése kérelmek nyomkövetésének használatával az Azure API Management szolgáltatásban | Microsoft Docs
description: Az oktatóanyag lépéseit követve megtudhatja, hogyan vizsgálhatja meg a kérések feldolgozásának lépéseit az Azure API Management szolgáltatásban.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 01a9120af2fb240eed1463613f7a6f946a9c16ed
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="debug-your-apis-using-request-tracing"></a>API-k hibakeresése kérelmek nyomkövetésének használatával

Ez az oktatóanyag leírja, hogyan vizsgálhatja meg a kérések feldolgozását az API hibakeresésének és hibaelhárításának elősegítése érdekében. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hívás nyomon követése

![API-vizsgáló](media/api-management-howto-api-inspector/api-inspector001.PNG)

## <a name="prerequisites"></a>Előfeltételek

+ Tekintse át a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).
+ Végezze el a következő oktatóanyagot is: [Az első API importálása és közzététele](import-and-publish.md).

## <a name="trace-a-call"></a>Hívás nyomon követése

1. Válassza az **API-k** lehetőséget.
2. Kattintson a **Demo Conference API** elemre az API-k listájában.
3. Válassza a **GetSpeakers** műveletet.
4. Váltson a **Teszt** lapra.
5. Győződjön meg róla, hogy egy **Ocp-Apim-Trace** elnevezésű HTTP-fejlécet is belefoglal, amely a **true** (igaz) értékre van állítva.
6. Kattintson a **„Küldés”** gombra egy API-hívás indításához. 
7. Várjon, amíg a hívás véget ér. 
8. Lépjen az **API-konzol** **Nyomkövetés** lapjára. A következő hivatkozások bármelyikére kattintva a részletes nyomkövetési információkra ugorhat: **bejövő**, **háttér**, **kimenő**.

    A **bejövő** szakaszban láthatja az API Management által a hívótól fogadott eredeti kérést, valamint a kérésre alkalmazott összes szabályzatot, beleértve a 2. lépésben hozzáadott sebességkorlát- és fejlécbeállítás-szabályzatot.

    A **háttér** szakaszban az API Management által az API háttérmodulnak küldött kérések és a kapott válaszok láthatók.
    
    A **kimenő** szakaszban a válaszra annak a hívónak való visszaküldését megelőzően alkalmazott szabályzatok láthatók.
 
    > [!TIP]
    > Az egyes lépések a hívás az API Management által való fogadása óta eltelt időt is mutatják.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Hívás nyomon követése

Folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Változatok használata](api-management-get-started-revise-api.md)
