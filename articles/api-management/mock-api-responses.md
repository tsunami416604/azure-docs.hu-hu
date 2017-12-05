---
title: "Mock API válaszok az Azure portállal |} Microsoft Docs"
description: "Az oktatóanyag bemutatja, hogyan használható az API Management (APIM) házirend beállítása az API-k, így mocked választ ad vissza. A metódus endables fejlesztők végrehajtására, és abban az esetben, ha a háttérkiszolgáló nem érhető el valós válaszok küldéséhez az API Management-példány tesztelése gombra."
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
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: e485071b026c52eb23532639546ad475fc92cde3
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2017
---
# <a name="mock-api-responses"></a>Utánzatait API válaszok

Háttér API-k is importálja az APIM API-k vagy létrehozott és kezelt manuálisan. A jelen oktatóanyagban szereplő lépések bemutatják a APIM segítségével hozzon létre egy üres API-t, és manuális kezelése. Az oktatóanyag bemutatja, hogyan házirend beállítása az API-k, így mocked választ ad vissza. Ez a módszer lehetővé teszi a fejlesztők végrehajtására, és akkor is, ha a háttérkiszolgáló nem valódi válaszok küldéséhez APIM példány tesztelése gombra. Válaszok kiadványszerkesztést is hasznos lehet a számos forgatókönyv részeként:

+ Ha az API homlokzati úgy van kialakítva, először tesztelhet, és a háttér-megvalósítási később. Vagy a háttér még fejlesztés alatt párhuzamosan.
+ Ha a háttérkiszolgáló nem ideiglenesen nem működési vagy méretezése nem sikerült.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Egy teszt API létrehozása 
> * A tesztelési API művelet hozzáadása
> * Válasz mocking engedélyezése
> * A mocked API tesztelése

![Mocked művelet válasz](./media/mock-api-responses/mock-api-responses01.png)

## <a name="prerequisites"></a>Előfeltételek

Fejezze be a következő gyorsindítási: [hozzon létre egy Azure API Management példányt](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-a-test-api"></a>Egy teszt API létrehozása 

A jelen szakaszban szereplő lépéseket hozzon létre egy üres API nem háttér szemléltetik. Azt is bemutatja, hogyan művelet hozzáadása az API-t. Hiba történt a jelen szakaszban szereplő lépéseket befejezése után a művelet hoz létre. Nincs hiba kap, miután elvégezte a "Válasz mocking engedélyezése" című szakaszában ismertetett lépéseket.

1. Válassza ki **API-k** a alatt **API MANAGEMENT**.
2. A bal oldali menüben válassza ki a **+ Hozzáadás API**.
3. Válassza ki **üres API** a listából.
4. Adja meg "*API tesztelése*" a **megjelenített név**.
5. Adja meg "*korlátlan*" a **termékek**.
6. Kattintson a **Létrehozás** gombra.

## <a name="add-an-operation-to-the-test-api"></a>A tesztelési API művelet hozzáadása

1. Válassza ki az előző lépésben létrehozott API-t.
2. Kattintson a **+ Hozzáadás művelet**.

    ![Mocked művelet válasz](./media/mock-api-responses/mock-api-responses02.png)

    |Beállítás|Érték|Leírás|
    |---|---|---|
    |**URL-cím** (HTTP-műveletet)|GET|Az előre meghatározott HTTP-műveletek közül választhat.|
    |**URL-CÍME** |*/test*|Egy URL-címet a API-hoz. |
    |**Megjelenített név**|*Hívás tesztelése*|A megjelenített neve a **fejlesztői portálján**.|
    |**Leírás**||Adjon meg egy leírást, amely a dokumentáció a a fejlesztők számára, ez az API használatával biztosítható a művelet a **fejlesztői portálján**.|
    |**Lekérdezés** lap||Lekérdezési paramétereket adhat hozzá. Mellett ad meg nevet és leírást, megadhatja az értékeket, amelyeket ez a paraméter lehet hozzárendelni. Az értékek egyike lehet állítani alapértelmezettként (nem kötelező).|
    |**Kérelem** lap||Megadhatja a kérelem tartalomtípus, példákat és sémák. |
    |**Válasz** lap|Ezt a táblázatot követő lépések.|A válaszhoz tartozó állapotkódok, példák és sémák megadása.|

3. Válassza ki a **válasz** lapon, az URL-cím alatt megjelenített név és Leírás mezőket.
4. Kattintson a **+ Hozzáadás válasz**.
5. Válassza ki **200 OK** a listából.
6. A a **felelősséget** válassza ki a jobb oldali fejléc **+ Hozzáadás ábrázolását**.
7. Adja meg "*az application/json*" azokat a keresési mezőbe, majd válassza ki a **az application/json** tartalomtípus.
8. Az a **minta** szöveget adja meg a "*{"sampleField":"test"}*".
9. Kattintson a **Mentés** gombra.

## <a name="enable-response-mocking"></a>Válasz mocking engedélyezése

1. Válassza ki a "Teszt API létrehozása" lépésben létrehozott API-t.
2. Válassza ki a tesztelési műveletet hozzáadott.
2. Kattintson a jobb oldalon látható a **tervezési** fülre.
3. Az a **bejövő feldolgozási** ablak, kattintson a ceruza ikonra.
4. Az a **Mocking** lapon jelölje be **statikus válaszok** a **viselkedés Mocking**.
5. Az a **API-kezelés a következő választ ad vissza:** szövegmezőben **200 OK, az application/json**. Ez a beállítás azt jelzi, hogy az API-t a válasz minta az előző szakaszban meghatározott kell visszaadnia.
6. Kattintson a **Mentés** gombra.

## <a name="test-the-mocked-api"></a>A mocked API tesztelése

1. Válassza ki a "Teszt API létrehozása" lépésben létrehozott API-t.
2. Nyissa meg a **teszt** fülre.
3. Győződjön meg arról a **hívás tesztelése** API van kiválasztva.

    > [!TIP]
    > A szöveg egy sárga sáv **Mocking engedélyezett** azt jelzi, hogy válaszok API Management által visszaadott mocking házirend, és nem egy tényleges háttér választ küld-e.

3. Válassza ki **küldése** hívás teszt végrehajtásához.
4. A **HTTP-válasz** jeleníti meg az első szakaszban az oktatóanyag mintaként megadott JSON.

## <a name="video"></a>Videó

> [!VIDEO https://www.youtube.com/embed/i9PjUAvw7DQ]
> 
> 

## <a name="next-steps"></a>Következő lépések
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Egy teszt API létrehozása
> * A tesztelési API művelet hozzáadása
> * Válasz mocking engedélyezése
> * A mocked API tesztelése

Előzetes következő oktatóanyagot:

> [!div class="nextstepaction"]
> [Az átalakítási és egy közzétett API védelme](transform-api.md)
