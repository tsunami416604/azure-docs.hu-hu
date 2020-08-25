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
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: fc5e8c7a7aa0d4693d96c3405ec0e180a6d13f8e
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "75768527"
---
# <a name="debug-your-apis-using-request-tracing"></a>API-k hibakeresése kérelmek nyomkövetésének használatával

Ez az oktatóanyag leírja, hogyan vizsgálhatja meg a kérések feldolgozását az API hibakeresésének és hibaelhárításának elősegítése érdekében. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hívás nyomon követése

![API-vizsgáló](media/api-management-howto-api-inspector/api-inspector001.PNG)

## <a name="prerequisites"></a>Előfeltételek

+ Az [Azure API Management terminológiájának](api-management-terminology.md) ismerete.
+ Tekintse át a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).
+ Végezze el a következő oktatóanyagot is: [Az első API importálása és közzététele](import-and-publish.md).

## <a name="trace-a-call"></a>Hívás nyomon követése

![API-nyomkövetés](media/api-management-howto-api-inspector/06-DebugYourAPIs-01-TraceCall.png)

1. Válassza az **API-k** lehetőséget.
2. Kattintson a **Demo Conference API** elemre az API-k listájában.
3. Váltson a **Teszt** lapra.
4. Válassza a **GetSpeakers** műveletet.
5. Győződjön meg róla, hogy egy **Ocp-Apim-Trace** elnevezésű HTTP-fejlécet is belefoglal, amely a **true** (igaz) értékre van állítva.

   > [!NOTE]
   > * Ha az Ocp-Apim-Subscription-Key nincs automatikusan kitöltve, akkor a fejlesztői portálon, a profiloldalon a kulcsok felfedésével szerezheti be.
   > * Ahhoz, hogy nyomkövetést kapjon a OCP-APIM-Trace HTTP-fejléc használatakor, engedélyezni kell az előfizetési kulcs **nyomkövetési** beállítását. A **nyomkövetés engedélyezése** beállítás konfigurálásához a bal oldali menü **API Management** területén válassza az **előfizetések**lehetőséget.
   >   ![Nyomkövetés engedélyezése a API Management-előfizetések ablaktáblán](media/api-management-howto-api-inspector/allowtracing.png)

6. Az API-hívás létrehozásához kattintson a **Küldés** gombra. 
7. Várjon, amíg a hívás véget ér. 
8. Lépjen az **API-konzol****Nyomkövetés** lapjára. A következő hivatkozások bármelyikére kattintva a részletes nyomkövetési információkra ugorhat: **bejövő**, **háttér**, **kimenő**.

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
