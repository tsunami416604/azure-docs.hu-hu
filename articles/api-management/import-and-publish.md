---
title: Az első API importálása és közzététele az Azure API Management ben
description: Ismerje meg, hogyan importálhat egy OpenAPI Specification API-t az Azure API Managementbe, és hogyan tesztelheti az API-t az Azure Portalon.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: apimpm
ms.openlocfilehash: 886063dcf886d79ac960814f20b3789e8e3b6839
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78163492"
---
# <a name="import-and-publish-your-first-api"></a>Az első API importálása és közzététele 

Ez az oktatóanyag bemutatja, hogyan importálhat egy OpenAPI specifikációs háttérAPI-t JSON formátumban az Azure API Management be. A Microsoft biztosítja a háttér-API-t, és az Azure-ban üzemelteti [https://conferenceapi.azurewebsites.net?format=json](https://conferenceapi.azurewebsites.net?format=json)azt.

Miután importálta a háttérbeli API-t az API Management be, az API Management API a háttér-API homlokzatává válik. Testreszabhatja a homlokzatot az API Management ben az igényeinek megfelelően anélkül, hogy megérintené a háttér-API-t. További információ: [Az API átalakítása és védelme](transform-api.md). 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * API importálása az API Management szolgáltatásba
> * Az API tesztelése az Azure Portalon

![Új API](./media/api-management-import-and-publish/created-api.png)

## <a name="prerequisites"></a>Előfeltételek

- Ismerje meg [az Azure API Management terminológiáját.](api-management-terminology.md)
- [Hozzon létre egy Azure API Management példányt.](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-backend-api"></a><a name="create-api"> </a>Háttérrendszeri API importálása és közzététele

Ez a szakasz bemutatja, hogyan importálhat és tehet közzé egy OpenAPI specifikációs háttérAPI-t.
 
1. Az API Management-példány bal oldali navigációs sávján válassza az API Management szakasz **API-k** **at.**
1. Jelölje ki az **OpenAPI** csempét, majd az előugró képernyőn válassza a **Teljes** lehetőséget.
1. A **Létrehozás OpenAPI-ból specifikáció** képernyőn az alábbi táblázatban szereplő értékek segítségével hozza létre az API-t.
   
   Az űrlap egyik mezője melletti piros csillag azt jelzi, hogy a mező szükséges. Az API-értékeket a létrehozás során vagy később a **Beállítások** lapon állíthatja be. 
   
   ![API létrehozása](./media/api-management-import-and-publish/create-api.png)
   
   |Beállítás|Érték|Leírás|
   |-------|-----|-----------|
   |**OpenAPI specifikáció**|*https:\//conferenceapi.azurewebsites.net?format=json*|Az API-t megvalósító szolgáltatás. Az API Management erre a címre továbbítja a kérelmeket.|
   |**Megjelenített név**|Miután megadta az előző szolgáltatás URL-címét, az API Management kitölti ezt a mezőt a JSON alapján.|A fejlesztői portálon megjelenő név.|
   |**Név**|Miután megadta az előző szolgáltatás URL-címét, az API Management kitölti ezt a mezőt a JSON alapján.|Az API egyedi neve.|
   |**Leírás**|Miután megadta az előző szolgáltatás URL-címét, az API Management kitölti ezt a mezőt a JSON alapján.|Az API nem kötelező leírása.|
   |**URL-séma**|**HTTPS**|Mely protokollok használhatók az API eléréséhez.|
   |**API URL-címének utótagja**|*conference*|Az utótag hozzáfűzve az API Management szolgáltatás alap URL-címéhez. Az API Management az utótagjuk szerint különbözteti meg az API-kat, így az utótagnak egyedinek kell lennie egy adott közzétevő minden API-ja esetében.|
   |**Termékek**|**Korlátlan**|Egy vagy több API társítása. Minden API Management-példány hoz két minta termékek: **Starter** és **korlátlan.** Az API-t úgy teheti közzé, hogy az API-t egy termékkel társítja, ebben a példában **korlátlan.**<br/>Több API-t is felvehet egy termékbe, és a fejlesztői portálon keresztül kínálhatja őket a fejlesztőknek. Ha hozzá szeretné adni ezt az API-t egy másik termékhez, írja be vagy válassza ki a termék nevét. Ismételje meg ezt a lépést, ha az API-t több termékhez szeretné hozzáadni. Később a **Beállítások** lapon is hozzáadhat API-kat a termékekhez.<br/>Ahhoz, hogy a fejlesztők hozzáférhessenek az API-hoz, elő kell fizetniük a termékre. Amikor előfizetnek, olyan előfizetési kulcsot kapnak, amely az adott termék bármely API-jának megfelelő. <br/>Ha létrehozta az API Management-példányt, már rendszergazda, így a példány minden termékére előfizetett.|
   |**Címkék**| |Címkék a kereséshez, csoportosításhoz vagy szűréshez szükséges API-k rendszerezéséhez.|
   |**Új verziót készít az API-ról?**|Kijelölés vagy kijelölés megszüntetése|A verziószámozásról az [API több verziójának közzététele](api-management-get-started-publish-versions.md)című témakörben talál további információt.|
   
   > [!NOTE]
   > Az API közzétételéhez társítania kell azt egy termékkel. Ezt a **Beállítások** oldalon teheti meg.
   
1. Kattintson a **Létrehozás** gombra.

Ha problémái vannak egy API-definíció importálásával, tekintse meg [az ismert problémák és korlátozások listáját.](api-management-api-import-restrictions.md)

## <a name="test-the-new-api-in-the-azure-portal"></a>Az új API tesztelése az Azure Portalon

Az API-műveleteket közvetlenül az Azure Portalról hívhatja meg, amely kényelmes módot biztosít a műveletek megtekintésére és tesztelésére.

1. Az API Management-példány bal oldali navigációs sávján válassza az **API-k lehetőséget** az **API-kezelés** szakaszban, majd a **Demo Conference API lehetőséget.**
1. Válassza a **Teszt** lapot, majd a **GetSpeakers**lehetőséget. A lapon **a Lekérdezési paraméterek** és az Fejlécek ( ha vannak ) **láthatók.** Az **Ocp-Apim-Subscription-Key** automatikusan kitöltődik az API-hoz társított előfizetési kulcshoz.
1. Válassza a **Küldés**lehetőséget.
   
   ![API-leképezés tesztelése](./media/api-management-import-and-publish/01-import-first-api-01.png)
   
   A háttérrendszer a **200 OK** üzenetet és néhány adatot küld válaszként.

## <a name="next-steps"></a><a name="next-steps"> </a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az első API importálása
> * Az API tesztelése az Azure Portalon

A következő oktatóanyagra lépésként megtudhatja, hogyan hozhat létre és tehet közzé terméket:

> [!div class="nextstepaction"]
> [Termékek létrehozása és közzététele](api-management-howto-add-products.md)
