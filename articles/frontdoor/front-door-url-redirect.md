---
title: Azure bejárati ajtó – URL-átirányítás | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan támogatja az Azure-előtérben az útvonalak URL-átirányítását, ha vannak ilyenek.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: duau
ms.openlocfilehash: 41cb2343cb86d2ec756bb0a2fb690b7df886024f
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399038"
---
# <a name="url-redirect"></a>URL-átirányítás
A forgalom átirányításához használhatja az Azure bejáratát. A forgalmat több szinten is átirányíthatja (protokoll, állomásnév, elérési út, lekérdezési karakterlánc), és az összes funkció konfigurálható az egyes szolgáltatásokhoz, mivel az átirányítás elérésiút-alapú. Ez egyszerűsíti az alkalmazások konfigurációját, optimalizálja az erőforrás-használatot, és támogatja az új átirányítási forgatókönyveket, beleértve a globális és az elérésiút-alapú átirányítást.
</br>

![Azure-beli bejárati URL-cím átirányítása][1]

## <a name="redirection-types"></a>Átirányítási típusok
Az átirányítási típus beállítja az ügyfelekre vonatkozó válasz állapotkódot, hogy megértse az átirányítás célját. A következő típusú átirányítási típusok támogatottak:

- **301 (véglegesen áthelyezve)**: azt jelzi, hogy a célként megadott erőforráshoz új állandó URI van rendelve, és az erőforrásra vonatkozó jövőbeli hivatkozásoknak az egyik bezárt URI-t kell használniuk. A HTTP-n a HTTPS-átirányítás 301-as állapotkódot használja. 
- **302 (found)**: azt jelzi, hogy a célként megadott erőforrás átmenetileg egy másik URI-n belül található. Mivel az átirányítás esetenként módosítható, az ügyfélnek továbbra is a hatályos kérelmek URI-JÁT kell használnia a jövőbeli kérésekhez.
- **307 (ideiglenes átirányítás)**: azt jelzi, hogy a célként megadott erőforrás átmenetileg egy másik URI-n belül található, és a felhasználói ügynök nem változtathatja meg a kérési módszert, ha automatikus átirányítást hajt végre az adott URI-n. Mivel az átirányítás idővel változhat, az ügyfélnek továbbra is az eredeti érvényes kérelem URI-JÁT kell használnia a jövőbeli kérésekhez.
- **308 (állandó átirányítás)**: azt jelzi, hogy a célként megadott erőforráshoz új állandó URI-t rendeltek, és az erőforrásra vonatkozó jövőbeli hivatkozásoknak az egyik bezárt URI-t kell használniuk. Azok az ügyfelek, amelyeken hivatkozás-szerkesztési képességek vannak, automatikusan újra kell csatolni a hatályos kérelem URI-JÁT a kiszolgáló által elküldett új referenciák közül egy vagy többre, ahol lehetséges.

## <a name="redirection-protocol"></a>Átirányítási protokoll
Beállíthatja azt a protokollt, amelyet az átirányítás használni fog. Ez lehetővé teszi az átirányítási funkció egyik leggyakoribb felhasználási esetét, vagyis a HTTP-t HTTPS-átirányításra beállítani.

- **Csak HTTPS**esetén: csak HTTPS értékre állítsa a protokollt, ha http-ről HTTPS-re szeretné átirányítani a forgalmat. Az Azure bejárati ajtó azt javasolja, hogy mindig csak HTTPS-re állítsa be az átirányítást.
- **Csak http esetén**: a bejövő kérelmet a http-re irányítja át. Ezt az értéket csak akkor használja, ha a forgalmat nem titkosított HTTP-n szeretné megőrizni.
- **Kérelem egyeztetése**: Ez a beállítás megőrzi a bejövő kérelem által használt protokollt. Így a HTTP-kérések HTTP-n maradnak, és a HTTPS-kérések HTTPS post átirányítás maradnak.

## <a name="destination-host"></a>Cél gazdagép
Az átirányítási útválasztás konfigurálásának részeként módosíthatja az átirányítási kérelem állomásnevét vagy tartományát is. Ezt a mezőt beállíthatja úgy, hogy módosítsa az állomásnév URL-címét az átirányítás számára, vagy egyéb módon őrizze meg a gazdagépet a bejövő kérelemből. Így a mező használatával átirányíthatja a szolgáltatásba küldött összes `https://www.contoso.com/*` kérelmet `https://www.fabrikam.com/*` .

## <a name="destination-path"></a>Cél elérési útja
Olyan esetekben, amikor az URL-cím elérésiút-szegmensét az átirányítás részeként szeretné lecserélni, ezt a mezőt az új elérésiút-értékkel állíthatja be. Ellenkező esetben dönthet úgy is, hogy az elérési út értékét az átirányítás részeként megőrzi. Így a mező használatával átirányíthatja a szolgáltatásba küldött összes kérelmet `https://www.contoso.com/\*`  `https://www.contoso.com/redirected-site` .

## <a name="query-string-parameters"></a>Lekérdezési karakterlánc paraméterei
A lekérdezési karakterlánc paramétereit is lecserélheti az átirányított URL-címekre. A bejövő kérelem URL-címében szereplő összes meglévő lekérdezési karakterlánc lecseréléséhez állítsa a mezőt "Replace" értékre, majd állítsa be a megfelelő értéket. Ellenkező esetben megtarthatja a lekérdezési karakterláncok eredeti készletét úgy, hogy a mezőt "megőrzés" értékre állítja. Ennek a mezőnek a használatával például átirányíthatja a szolgáltatásba érkező összes `https://www.contoso.com/foo/bar` forgalmat `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F` . 

## <a name="destination-fragment"></a>Cél töredéke
A cél töredék az URL-cím "#" utáni része, amelyet általában a böngészők egy adott szakasz egy adott szakasza számára használnak. Ezt a mezőt beállíthatja úgy, hogy felvegyen egy töredéket az átirányítási URL-címhez.

## <a name="next-steps"></a>Következő lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.

<!--Image references-->
[1]: ./media/front-door-url-redirect/front-door-url-redirect.png