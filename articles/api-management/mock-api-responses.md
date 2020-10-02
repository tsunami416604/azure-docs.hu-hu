---
title: Oktatóanyag – az API-válaszok modellezése API Managementban – Azure Portal | Microsoft Docs
description: Ebben az oktatóanyagban az API Management használatával állít be egy szabályzatot egy API-ra, így egy kigúnyolt választ ad vissza, ha a háttér nem érhető el valódi válaszok küldéséhez.
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: apimpm
ms.openlocfilehash: 78743c5f045f2544cafe88414ed996d08bacd2a0
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631130"
---
# <a name="tutorial-mock-api-responses"></a>Oktatóanyag: API-válaszok modellezése

A háttérbeli API-k importálhatók egy API Management (APIM) API-ba, vagy manuálisan is létrehozható és felügyelhető. Az ebben az oktatóanyagban szereplő lépések bemutatják, hogyan hozhat létre üres API-t az APIM használatával, és hogyan kezelheti manuálisan, majd beállíthat egy szabályzatot egy API-ra, hogy egy kigúnyolt választ ad vissza. Ezzel a metódussal a fejlesztők akkor is folytathatják az APIM-példány megvalósítását és tesztelését, ha a háttérrendszer nem elérhető valós válaszok visszaadása céljából. 

A válaszok kimodellezésének képessége számos esetben hasznos lehet:

+ Ha előbb az API előtérrendszere készül el, és a háttérrendszer csak később lesz megvalósítva, vagy ha a háttérrendszer fejlesztése párhuzamosan zajlik.
+ Ha a háttérrendszer ideiglenesen üzemen kívül van vagy nem méretezhető.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Tesztelési API létrehozása 
> * Művelet hozzáadása a tesztelési API-hoz
> * Válaszutánzás engedélyezése
> * Az utánzott API tesztelése


:::image type="content" source="media/mock-api-responses/mock-api-responses01.png" alt-text="Kigúnyolt API-válasz":::

## <a name="prerequisites"></a>Előfeltételek

+ Az [Azure API Management terminológiájának](api-management-terminology.md) ismerete.
+ Az [Azure API Management-szabályzatok alapelveinek](api-management-howto-policies.md) ismerete.
+ Tekintse át a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).

## <a name="create-a-test-api"></a>Tesztelési API létrehozása 

A jelen szakaszban ismertetett lépések bemutatják, hogyan hozható létre egy háttérrendszer nélküli üres API. 


1. Jelentkezzen be a Azure Portalba, és navigáljon a API Management-példányhoz.
1. Válassza **az API-** k  >  **+ API-k hozzáadása**  >  **üres API**elemet.
1. Az **üres API létrehozása** ablakban válassza a **teljes**lehetőséget.
1. Adja meg a **megjelenítendő névhez**tartozó *teszt API* -t.
1. Válassza a **korlátlan** lehetőséget a **termékek**számára.
1. Győződjön meg arról, hogy a **felügyelt** be van jelölve az **átjárók**között
1. Kattintson a **Létrehozás** gombra.

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-01-create-test-api.png" alt-text="Kigúnyolt API-válasz":::

## <a name="add-an-operation-to-the-test-api"></a>Művelet hozzáadása a tesztelési API-hoz

Az API-k egy vagy több műveletet tesznek elérhetővé. Ebben a szakaszban adjon hozzá egy műveletet a létrehozott üres API-hoz. A szakasz lépéseinek végrehajtása után a művelet meghívása hibát ad vissza. A válaszok kiírásának [engedélyezése](#enable-response-mocking) szakasz későbbi lépéseinek elvégzése után nem fog hibaüzeneteket kapni.

1. Válassza ki az előző lépésben létrehozott API-t.
1. Válassza a **+ Művelet hozzáadása** lehetőséget.
1. A **frontend** ablakban adja meg a következő értékeket.

     | Beállítás             | Érték                             | Leírás                                                                                                                                                                                   |
    |---------------------|-----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Megjelenített név**    | *Teszthívás*                       | A [fejlesztői portálon](api-management-howto-developer-portal.md)megjelenő név.                                                                                                                                       |
    | **URL** (HTTP-művelet) | GET                               | Válassza ki az előre definiált HTTP-műveletek egyikét.                                                                                                                                         |
    | **URL-cím**             | */test*                           | Az API URL-címe.                                                                                                                                                                       |
    | **Leírás**     |                                   |  A művelet opcionális leírása, amely az API-t használó fejlesztők számára biztosít dokumentációt a fejlesztői portálon.                                                    |
    
1. Válassza a **válaszok** lapot az URL-cím, a megjelenítendő név és a Leírás mezők alatt. Adja meg az ezen a lapon megjelenő beállításokat a válasz állapotkódok, a tartalomtípusok, a példák és a sémák meghatározásához.
1. Válassza a **+ válasz hozzáadása**lehetőséget, majd a listából válassza a **200 OK** elemet.
1. A jobb oldali **Ábrázolások** fejléc alatt válassza a **+ Ábrázolás hozzáadása** lehetőséget.
1. Adja meg az *Application/JSON* kifejezést a keresőmezőbe, és válassza ki az **alkalmazás/JSON** tartalomtípust.
1. A **Minta** szövegmezőbe írja be a következőt: `{ "sampleField" : "test" }`.
1. Válassza a **Mentés** lehetőséget.

:::image type="content" source="media/mock-api-responses/03-mock-api-responses-02-add-operation.png" alt-text="Kigúnyolt API-válasz" border="false":::

Bár ehhez a példához nem szükséges, az API-műveletek további beállításait más lapokon is konfigurálhatja, többek között a következőket:


|Tab      |Leírás  |
|---------|---------|
|**Lekérdezés**     |  Lekérdezési paraméterek hozzáadása A név és a Leírás megadása mellett a lekérdezési paraméterekhez rendelt értékeket is megadhat. Az értékek egyike megjelölhető alapértelmezettként (választható).        |
|**Kérés**     |  A kérelem tartalmának típusai, példák és sémák definiálása.       |

## <a name="enable-response-mocking"></a>Válaszutánzás engedélyezése

1. Válassza ki a [tesztelési API létrehozása](#create-a-test-api)területen létrehozott API-t.
1. Válassza ki a hozzáadott tesztműveletet.
1. A jobb oldali ablakban ellenőrizze, hogy a **tervezés** lap van-e kiválasztva.
1. A **bejövő feldolgozás** ablakban válassza a **+ házirend hozzáadása**lehetőséget.

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-03-enable-mocking.png" alt-text="Kigúnyolt API-válasz" border="false":::

1. Válassza ki a katalógusbeli **válaszokat**  a gyűjteményből.

    :::image type="content" source="media/mock-api-responses/mock-responses-policy-tile.png" alt-text="Kigúnyolt API-válasz" border="false":::

1. **Az API Management válaszának** szövegmezőjébe írja be a következőt: **200 OK, alkalmazás/json**. Ez a beállítás azt jelzi, hogy az API az előző szakaszban definiált mintaválaszt adja vissza.

    :::image type="content" source="media/mock-api-responses/mock-api-responses-set-mocking.png" alt-text="Kigúnyolt API-válasz":::

1. Válassza a **Mentés** lehetőséget.

    > [!TIP]
    > Az API-val rendelkező sárga sáv **lehetővé** teszi, hogy a API Management küldött válaszok elküldjenek egy kigúnyoló szabályzatot, nem pedig a háttérrendszer tényleges válaszát.

## <a name="test-the-mocked-api"></a>Az utánzott API tesztelése

1. Válassza ki a [tesztelési API létrehozása](#create-a-test-api)területen létrehozott API-t.
1. Kattintson a **Teszt** fülre.
1. Győződjön meg róla, hogy a **Teszthívás** API van kiválasztva. Teszthívás indításához válassza a **Küldés** lehetőséget.

   :::image type="content" source="media/mock-api-responses/03-mock-api-responses-04-test-mocking.png" alt-text="Kigúnyolt API-válasz":::

1. A **HTTP-válasz** mező az oktatóanyag első szakaszában mintaként megadott JSON-t jeleníti meg.

    :::image type="content" source="media/mock-api-responses/mock-api-responses-test-response.png" alt-text="Kigúnyolt API-válasz":::

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Tesztelési API létrehozása
> * Művelet hozzáadása a tesztelési API-hoz
> * Válaszutánzás engedélyezése
> * Az utánzott API tesztelése

Folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Közzétett API átalakítása és védelme](transform-api.md)
