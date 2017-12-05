---
title: "A kérelmek nyomon követése az Azure API Management használata API-k hibakeresése |} Microsoft Docs"
description: "Ez az oktatóanyag megtudhatja, hogyan vizsgálhatja meg a lépéseket az Azure API Management feldolgozási kövesse."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 7b9bec7927169b9d820c095a7d11705264e7dcfe
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2017
---
# <a name="debug-your-apis-using-request-tracing"></a>A kérelem-nyomkövetés használata API-k hibakeresése

Ez az oktatóanyag segítséget nyújt a Hibakeresés és hibaelhárítás az API-kérés feldolgozási vizsgálhatja ismerteti. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Nyomkövetés-hívás

![API-inspector](media/api-management-howto-api-inspector/api-inspector001.PNG)

## <a name="prerequisites"></a>Előfeltételek

+ Fejezze be a következő gyorsindítási: [hozzon létre egy Azure API Management példányt](get-started-create-service-instance.md).
+ Is, végezze el a következő oktatóanyagot: [importálása és az első API-t közzétenni](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="trace-a-call"></a>Nyomkövetés-hívás

1. Válassza ki **API-k**.
2. Kattintson a **bemutató konferencia API** az API listájában.
3. Válassza ki **GetSpeakers** műveletet.
4. Váltás a **teszt** fülre.
5. Mindenképpen nevű HTTP-fejléc **Ocp-Apim-nyomkövetési** a érték **igaz**.
6. Kattintson a **"Send"** egy API-híváshoz. 
7. Várja meg a hívás befejezéséhez. 
8. Lépjen a **nyomkövetési** lapra a **API konzol**. A részletes nyomkövetési adatok mennyiségét, a következő hivatkozások bármelyikére kattintva: **bejövő**, **háttér**, **kimenő**.

    Az a **bejövő** szakaszban láthatja az eredeti kérés API Management érkezett a hívó és alkalmazza a kérelmet, beleértve a sebességhatár és a set-fejléc házirendek a 2. lépésben hozzáadott összes házirendet.

    Az a **háttér** szakaszban láthatja az API Management az API háttéralkalmazás és a kapott választ küldött kérelmeket.
    
    Az a **kimenő** területen látható minden, a válasz vissza a hívó történő elküldése előtt alkalmazott házirendek.
 
    > [!TIP]
    > Egyes lépéseit, megjelenítheti az eltelt idő óta API Management fogadta a kérelmet.

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Nyomkövetés-hívás

Előzetes következő oktatóanyagot:

> [!div class="nextstepaction"]
> [Változatok használata](api-management-get-started-revise-api.md)
