---
title: API válaszok utánzása az Azure Portallal | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan lehet az API Management (APIM) használatával úgy beállítani a szabályzatokat az API-kon, hogy azok egy utánzott választ adjanak vissza. Ezzel a metódussal a fejlesztők akkor is folytathatják az API Management-példány megvalósítását és tesztelését, ha a háttérrendszer nem elérhető valós válaszok visszaadása céljából.
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
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: 9b9a691cb2bce2357d184420912ab340aee534e8
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50412740"
---
# <a name="mock-api-responses"></a>API-válaszok utánzása

A háttérbeli API-k APIM API-kba importálhatók, de manuálisan is létrehozhatók és kezelhetők. Az oktatóanyag lépései bemutatják, hogyan hozhat létre és kezelhet üres API-kat az APIM használatával. Az oktatóanyag bemutatja, hogyan állíthat be szabályzatokat az API-kon úgy, hogy azok utánzott válaszokat adjanak vissza. Ezzel a metódussal a fejlesztők akkor is folytathatják az APIM-példány megvalósítását és tesztelését, ha a háttérrendszer nem elérhető valós válaszok visszaadása céljából. A válaszok utánzása számos forgatókönyv esetében lehet hasznos:

+ Ha előbb az API előtérrendszere készül el, és a háttérrendszer csak később lesz megvalósítva, vagy ha a háttérrendszer fejlesztése párhuzamosan zajlik.
+ Ha a háttérrendszer ideiglenesen üzemen kívül van vagy nem méretezhető.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Tesztelési API létrehozása 
> * Művelet hozzáadása a tesztelési API-hoz
> * Válaszutánzás engedélyezése
> * Az utánzott API tesztelése

![Utánzott műveleti válasz](./media/mock-api-responses/mock-api-responses01.png)

## <a name="prerequisites"></a>Előfeltételek

+ Az [Azure API Management terminológiájának](api-management-terminology.md) ismerete.
+ Az [Azure API Management-szabályzatok alapelveinek](api-management-howto-policies.md) ismerete.
+ Tekintse át a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).

## <a name="create-a-test-api"></a>Tesztelési API létrehozása 

A jelen szakaszban ismertetett lépések bemutatják, hogyan hozható létre egy háttérrendszer nélküli üres API. Azt is bemutatja, hogyan lehet műveletet hozzáadni az API-hoz. A szakasz lépéseinek végrehajtása után a művelet meghívása hibát ad vissza. A hibák a „Válaszutánzás engedélyezése” szakasz lépéseinek végrehajtásával megszűnnek.

![Üres API létrehozása](./media/mock-api-responses/03-MockAPIResponses-01-CreateTestAPI.png)

1. Az **API Management** szolgáltatásban válassza az **API** lehetőséget.
2. A bal oldali menüben válassza az **+ API hozzáadása** elemet.
3. Válassza az **Üres API** elemet a listából.
4. A **Megjelenítendő név** mezőben adja meg a „*Tesztelési API*” nevet.
5. A **Termékek** mezőben adja meg a „*Korlátlan*” értéket.
6. Kattintson a **Létrehozás** gombra.

## <a name="add-an-operation-to-the-test-api"></a>Művelet hozzáadása a tesztelési API-hoz

![Művelet hozzáadása az API-hoz](./media/mock-api-responses/03-MockAPIResponses-02-AddOperation.png)

1. Válassza ki az előző lépésben létrehozott API-t.
2. Kattintson a **+ Művelet hozzáadása** elemre.

    | Beállítás             | Érték                             | Leírás                                                                                                                                                                                   |
    |---------------------|-----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Megjelenített név**    | *Teszthívás*                       | A **fejlesztői portálon** megjelenített név.                                                                                                                                       |
    | **URL** (HTTP-művelet) | GET                               | Az előre meghatározott HTTP-műveletek közül választhat.                                                                                                                                         |
    | **URL-cím**             | */test*                           | Az API URL-címe.                                                                                                                                                                       |
    | **Leírás**     |                                   | Adja meg a művelet leírását, amely dokumentációként szolgál majd a **fejlesztői portálon** az API-t használó fejlesztők számára.                                                    |
    | **Lekérdezés** lap       |                                   | Hozzáadhat lekérdezési paramétereket. A név és a leírás megadása mellett megadhatja az ehhez a paraméterhez rendelhető értékeket is. Az értékek egyike megjelölhető alapértelmezettként (választható). |
    | **Kérés** lap     |                                   | Definiálhatja a kéréshez tartozó tartalomtípusokat, példákat és sémákat.                                                                                                                                  |
    | **Válasz** lap    | Lásd a táblázat alatti lépéseket. | Definiálhatja a válaszhoz tartozó állapotkódokat, tartalomtípusokat, példákat és sémákat.                                                                                                                           |

3. Válassza a **Válasz** lapot az URL-cím, a Megjelenítendő név és a Leírás mezők alatt.
4. Kattintson a **+ Válasz hozzáadása** gombra.
5. Válassza a **200 OK** elemet a listából.
6. A jobb oldali **Ábrázolások** fejléc alatt válassza a **+ Ábrázolás hozzáadása** lehetőséget.
7. Írja be az „*alkalmazás/json*” kifejezést a keresőmezőbe, és válassza ki az **alkalmazás/json** tartalomtípust.
8. A **Minta** szövegmezőbe írja be a következőt: `{ 'sampleField' : 'test' }`.
9. Kattintson a **Létrehozás** gombra.

## <a name="enable-response-mocking"></a>Válaszutánzás engedélyezése

![Válaszutánzás engedélyezése](./media/mock-api-responses/03-MockAPIResponses-03-EnableMocking.png)

1. Válassza ki a „Tesztelési API létrehozása” lépésben létrehozott API-t.
2. Válassza ki a hozzáadott tesztműveletet.
3. A jobb oldali ablakban kattintson a **Tervezés** lapra.
4. A **Bejövő feldolgozás** ablakban kattintson a **+ Szabályzat hozzáadása** gombra.
5. Válassza ki a **válaszok utánzását** lehetővé tévő csempét a katalógusban.

    ![Válaszok utánzása szabályzat csempéje](./media/mock-api-responses/mock-responses-policy-tile.png)

6. **Az API Management válaszának** szövegmezőjébe írja be a következőt: **200 OK, alkalmazás/json**. Ez a beállítás azt jelzi, hogy az API az előző szakaszban definiált mintaválaszt adja vissza.

    ![Válaszutánzás engedélyezése](./media/mock-api-responses/mock-api-responses-set-mocking.png)

7. Kattintson a **Save** (Mentés) gombra.

## <a name="test-the-mocked-api"></a>Az utánzott API tesztelése

![Utánzott API tesztelése](./media/mock-api-responses/03-MockAPIResponses-04-TestMocking.png)

1. Válassza ki a „Tesztelési API létrehozása” lépésben létrehozott API-t.
2. Nyissa meg a **Teszt** lapot.
3. Győződjön meg róla, hogy a **Teszthívás** API van kiválasztva.

    > [!TIP]
    > Egy sárga, **Utánzás engedélyezve** szöveggel ellátott sáv jelzi, hogy az API Management által visszaadott válaszokat egy utánzási szabályzat küldi, és nem a háttérrendszer valós válaszairól van szó.

4. Teszthívás indításához válassza a **Küldés** lehetőséget.
5. A **HTTP-válasz** mező az oktatóanyag első szakaszában mintaként megadott JSON-t jeleníti meg.

    ![Válaszutánzás engedélyezése](./media/mock-api-responses/mock-api-responses-test-response.png)

## <a name="video"></a>Videó

> [!VIDEO https://www.youtube.com/embed/i9PjUAvw7DQ]

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
