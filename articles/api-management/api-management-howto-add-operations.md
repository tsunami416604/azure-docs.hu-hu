---
title: "Műveletek felvétele az Azure API Management API |} Microsoft Docs"
description: "Útmutató tevékenységek hozzáadása az Azure API Management API."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 1158a023-1913-4e9c-93de-9164b672f9b3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 105fc51c2d1152a40a5757985da47330e0b7b8cf
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-add-operations-to-an-api-in-azure-api-management"></a>Műveletek hozzáadása egy API-t az Azure API Management
Egy API-t az API Management használata előtt hozzá kell adni műveletek. Ez az útmutató bemutatja, hogyan hozzáadása és konfigurálása a különböző típusú műveletek az API-k az API Management.

## <a name="add-operation"></a>Művelet hozzáadása
Műveletek felvétele, illetve egy API-t, a közzétevő portálon konfigurálva. A közzétevő portál eléréséhez kattintson **Publisher portal** az Azure portálon az API Management szolgáltatás.

![Közzétevő portál][api-management-management-console]

> Ha még nem hozott létre API Management szolgáltatáspéldányt, tekintse meg az [Ismerkedés az Azure API Management szolgáltatással][Get started with Azure API Management] oktatóanyag [API Management szolgáltatáspéldány létrehozása][Create an API Management service instance] című szakaszát.
> 
> 

Válassza ki a kívánt API a közzétevő portálon, és válassza ki a **műveletek** fülre. 

![Műveletek][api-management-operations]

Kattintson a **művelet hozzáadása** hozzáadása egy új művelet. A **új művelet** jelenik meg, és a **aláírás** alapértelmezett kiválasztása lapon.

![A művelet hozzáadása][api-management-add-operation]

Adja meg a **HTTP-műveletet** válassza ki a legördülő listából.

![HTTP-metódus][api-management-http-method]

<a name="url-template"></a>

Az URL-cím sablon URL-cím elérési út egy vagy több szegmensek és nulla vagy több lekérdezési karakterláncot paraméterek töredékkel URL-CÍMÉT, írja be. Az URL-cím sablon, az alap URL-címet a API fűzött azonosítja egy egyetlen HTTP-műveletből. Tartalmazhat egy vagy több nevű változó részeit, amelyek nincsenek azonosítva kapcsos zárójelek. Változó részei Sablonparaméterek hívják, és dinamikusan kiosztott értékeket kinyert a kérelem URL-címe, ha a kérés feldolgozása folyamatban van az API Management platformon.

> Az URL-cím sablon helyettesítő mintákat tartalmazhat. Például megadó `/*` előre minden belépési kérelmet, hogy a biztonsági HTTP-metódus véget ér szolgáltatás.

![URL-cím sablon][api-management-url-template]

<a name="rewrite-url-template"></a>

Ha szükséges, adja meg a **URL-cím újraírása sablon**. Ez lehetővé teszi, hogy a szabványos URL-cím sablon használata az előtér-a bejövő kérelmeket feldolgozó a háttér-keresztül konvertált URL-címet a átdolgozás sablon alapján hívása közben. Az URL-cím sablonból sablon paramétereket a átdolgozás sablonban kell használni. A következő példa bemutatja, hogyan tartalomtípus elérésiút-szegmens az webszolgáltatás az előző példából az API Management platform, az URL-sablonokkal keresztül közzétett API egy lekérdezési paraméterben meghatározott kódolású.

![Sablon átdolgozás URL-címe][api-management-url-template-rewrite]

A művelet hívók fogják használni a `/customers?customerid=ALFKI` és ez lesz rendelve `/Customers('ALFKI')` amikor meghívták a háttér-szolgáltatás.

**Megjelenítési** nevét és **leírás** adja meg a művelet leírását, és biztosítja a dokumentáció a a fejlesztők számára az API használatával a fejlesztői portálra.

![Leírás][api-management-description]

A művelet leírást adhat meg egyszerű szöveges vagy a HTML a **leírás** szövegmezőben.

## <a name="operation-caching"></a>Művelet gyorsítótárazása
Válasz gyorsítótárazását csökkenti a késést API fogyasztók, csökkenti a sávszélesség-használat és csökken a terhelés HTTP webes szolgáltatás végrehajtási az API által érzékelt. 

Egyszerűen és gyorsan gyorsítótárazása a művelethez, válassza ki a **gyorsítótárazását** fülre és ellenőrizze a **engedélyezése** jelölőnégyzetet.

![Gyorsítótárazás][api-management-caching-tab]

**Időtartam** megadja az időtartamot, amelynek során a művelet választ a gyorsítótárban marad. Az alapértelmezett érték 3600 másodperc, vagyis 1 óra.

Gyorsítótár-kulcsok segítségével válaszok különböztetheti meg, hogy a választ, minden más gyorsítótárkulcshoz megfelelő saját külön gyorsítótárazott értéket kap. Szükség esetén adja meg a megadott lekérdezési karakterlánc paraméterek és/vagy HTTP-fejlécek gyorsítótár értékeit számítástechnikai használandó a **lekérdezési karakterlánc paraméterei Vary** és **Vary fejléc által** szövegdobozok kulcsattribútumokkal. Ha nincs megadott, teljes kérelem URL-CÍMÉT, és a következő HTTP-fejléc értékei használt a gyorsítótár Kulcslétrehozási: **elfogadás** és **Accept-Charset**.

> A és a házirendek gyorsítótárazást további információkért lásd: [gyorsítótárazásának művelet eredménye az Azure API Management][How to cache operation results in Azure API Management].
> 
> 

## <a name="request-parameters"></a>Kérelem paraméterei
A Paraméterek lapon felügyelt művelet paramétereit. A megadott paraméterek a **URL-cím sablon** a a **aláírás** lapon a rendszer automatikusan hozzáadja, és csak az URL-cím sablon szerkesztésével módosíthatja. További paraméterek manuálisan is megadhatók.

Egy új lekérdezési paraméter hozzáadásához kattintson **lekérdezési paraméter hozzáadása** , és írja be a következő információkat:

* **Név** -paraméter neve.
* **Leírás** -rövid leírását tartalmazza (választható) paraméter.
* **Típus** -paraméter típusa, az vetett kiválasztott le.
* **Értékek** -értékeket, amelyeket ez a paraméter lehet hozzárendelni. Az értékek egyike lehet állítani alapértelmezettként (nem kötelező).
* **Szükséges** -paraméter kötelezővé teheti a jelölőnégyzet bejelölésével. 

![A kérelemben szereplő paraméterek][api-management-request-parameters]

## <a name="request-body"></a>Kérelem törzse
Ha a művelet lehetővé teszi, hogy (pl. PUT, POST) és egy szervezet például az összes támogatott ábrázolását formátumot (pl. json, XML) által biztosított igényel. 

> A kérelem törzsében csak dokumentáció célokat szolgál, és nincs érvényesítve.
> 
> 

Adjon meg egy kérelemtörzset, térjen át a **törzs** fülre.

Kattintson a **ábrázolását adja hozzá**, elkezdi beírni kívánt tartalom típusa nevét (például application/json), a legördülő listán válassza ki és illessze be a kívánt kérelem törzse példa a kijelölt formátumú szöveget. 

![Kérés törzsében][api-management-request-body]

A további képviseletein, azt is megadhatja a egy nem kötelező leírás a **leírás** szövegmezőben.

## <a name="responses"></a>Válaszok
Ajánlott a művelet készíthet összes állapotkódokat válaszok példákat. Előfordulhat, hogy az egyes állapotkód egynél több válasz törzsében példa, minden támogatott tartalomtípusokat. 

A válasz hozzáadásához kattintson **Hozzáadás** és kezdje el begépelni az kívánt állapotkódot. Ebben a példában az állapotkód akkor **200 OK**. Követően a kód jelenik meg a legördülő listán, válassza ki azt, és ez a válaszkód létrehozza és hozzáadja a művelethez.

![Válaszkód][api-management-response-code]

Kattintson a **ábrázolását adja hozzá**elkezdi beírni a kívánt tartalom típusa nevét (például application/json), majd válassza ki az vetett le.

![Törzs tartalom típusa][api-management-response-body-content-type]

Illessze be a válasz törzsében példa a kijelölt formátumú szöveg. 

![Választörzs][api-management-response-body]

Tetszés szerint megadhat egy leírást, azokat a **leírás** szövegmezőben.

Ha a művelet már konfigurálva van, kattintson a **mentése**.

## <a name="next-steps"> </a>Következő lépések
Miután a műveletek bekerülnek az API-k, a következő lépés, hogy rendelje hozzá az API-t a termék és közzéteszi az, hogy a fejlesztők meghívhatja a műveleteket.

* [Hogyan hozhat létre, és a termék közzététele][How to create and publish a product]

[api-management-management-console]: ./media/api-management-howto-add-operations/api-management-management-console.png
[api-management-operations]: ./media/api-management-howto-add-operations/api-management-operations.png
[api-management-add-operation]: ./media/api-management-howto-add-operations/api-management-add-operation.png
[api-management-http-method]: ./media/api-management-howto-add-operations/api-management-http-method.png
[api-management-url-template]: ./media/api-management-howto-add-operations/api-management-url-template.png
[api-management-url-template-rewrite]: ./media/api-management-howto-add-operations/api-management-url-template-rewrite.png
[api-management-description]: ./media/api-management-howto-add-operations/api-management-description.png
[api-management-caching-tab]: ./media/api-management-howto-add-operations/api-management-caching-tab.png
[api-management-request-parameters]: ./media/api-management-howto-add-operations/api-management-request-parameters.png
[api-management-request-body]: ./media/api-management-howto-add-operations/api-management-request-body.png
[api-management-response-code]: ./media/api-management-howto-add-operations/api-management-response-code.png
[api-management-response-body-content-type]: ./media/api-management-howto-add-operations/api-management-response-body-content-type.png
[api-management-response-body]: ./media/api-management-howto-add-operations/api-management-response-body.png


[api-management-contoso-api]: ./media/api-management-howto-add-operations/api-management-contoso-api.png

[api-management-add-new-api]: ./media/api-management-howto-add-operations/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-add-operations/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-add-operations/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-add-operations/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-add-operations/api-management-echo-operations.png

[Add an operation]: #add-operation
[Operation caching]: #operation-caching
[Request parameters]: #request-parameters
[Request body]: #request-body
[Responses]: #responses
[Next steps]: #next-steps

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to cache operation results in Azure API Management]: api-management-howto-cache.md
