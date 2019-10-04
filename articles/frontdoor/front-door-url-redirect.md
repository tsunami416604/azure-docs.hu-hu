---
title: Azure bejárati ajtó Service – átirányítási URL-cím |} A Microsoft Docs
description: Ez a cikk segít megérteni a Azure bejárati ajtajának szolgáltatás hogyan támogatja a URL-átirányítás a saját útvonalakat, ha konfigurálva.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: sharadag
ms.openlocfilehash: 3d77a16d24a1a843b39d97904a675518c43a525a
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332939"
---
# <a name="url-redirect"></a>Átirányítási URL-címe
Használhatja az Azure bejárati ajtajának szolgáltatás átirányítja a forgalmat. Irányíthatja a forgalmat több szinten (protokoll, állomásnév, elérési út, lekérdezési karakterlánc), és a összes funkcióját az egyes mikroszolgáltatások, az átirányítási elérési út alapján. Ez egyszerűbbé teszi az alkalmazás konfigurációja, optimalizálja az erőforrás-felhasználás, és többek között a globális és -alapú átirányítás új átirányítás forgatókönyveket teszi lehetővé.
</br>

![Azure bejárati ajtajának az átirányítási URL-címe][1]

## <a name="redirection-types"></a>Átirányítás típusa
Egy átirányítási típus állítja be, hogy az ügyfelek, hogy megértse az átirányítási válasz állapotkódja. Átirányítás a következő típusú támogatottak:

- **301 (véglegesen áthelyezett)** : Azt jelzi, hogy a célként megadott erőforrás van rendelve egy új állandó URI-t, és ehhez az erőforráshoz esetleges jövőbeli hivatkozásokat kell kerülnie a karakterláncon belül lévő URI-k valamelyikével. HTTP – HTTPS átirányításról állapotkód 301 használata. 
- **302 (található)** : Azt jelzi, hogy a célként megadott erőforrás ideiglenesen másik URI alatt található. Az átirányítás előfordulhat előfordulhat, hogy módosítható, mivel az ügyfél továbbra is szeretné használni a hatékony kérés URI azonosítója a jövőbeni kérések kell kerülnie.
- **307 (ideiglenes átirányítási)** : Azt jelzi, hogy a célként megadott erőforrás ideiglenesen másik URI alatt található, és nem kell a felhasználói ügynök kérelmi metódus módosíthatja, ha elvégez egy automatikus átirányítás erre az URI. Az átirányítás idővel változhat, mivel az ügyfél továbbra is az eredeti hatékony kérés URI-t használni a jövőbeli kérések kellett.
- **(Állandó átirányítás) 308**: Azt jelzi, hogy a célként megadott erőforrás van rendelve egy új állandó URI-t, és ehhez az erőforráshoz esetleges jövőbeli hivatkozásokat kell kerülnie a karakterláncon belül lévő URI-k valamelyikével. Ügyfelek képességeket, szerkesztési hivatkozást kell kerülnie tényleges automatikusan Újracsatolás hivatkozik a kérés URI-ja egy vagy több új hivatkozások a kiszolgáló által küldött, ahol csak lehetséges.

## <a name="redirection-protocol"></a>Átirányítás protokoll
Beállíthatja, hogy az átirányítási használandó protokoll. Ez lehetővé teszi, hogy állítsa be a HTTP – HTTPS átirányításról, átirányítási funkciója, a leggyakoribb alkalmazási helyzetei közül.

- **Csak HTTPS**: A protokoll beállítása HTTPS csak, ha a forgalom a HTTP – HTTPS átirányítás szeretne. Az Azure bejárati ajtajának Service használatát javasolja kell mindig az átirányítás HTTPS csak.
- **Csak HTTP**: Ez átirányítja a bejövő kérelem HTTP-re. Használja ezt az értéket csak akkor, ha meg szeretné tartani a forgalom a HTTP-, nem titkosított.
- **Egyezés kérelem**: Ez a beállítás megőrzi a bejövő kérelem által használt protokoll. Tehát egy HTTP-kérelem HTTP marad, és egy HTTPS-kérést marad HTTPS utáni átirányítási.

## <a name="destination-host"></a>Cél gazdagép
Egy átirányítási útválasztás beállítása részeként a állomásnév vagy a tartomány az átirányítási kérelem is módosíthatja. Ez a mező módosítása az állomásnév az átirányítási URL-címét, vagy más módon megőrizni a gazdanevet erről a bejövő kérelem állíthatja be. Tehát ez a mező használatával átirányíthatja kéréseket https://www.contoso.com/ * való https://www.fabrikam.com/ *.

## <a name="destination-path"></a>Cél elérési útja
Olyan esetekben, ahol szeretné cserélje le a részleges útvonalat egy URL-átirányítás részeként beállíthatja ezt a mezőt az új elérési útja értéket. Ellenkező esetben lehet váltani a elérési útjának értéke megőrzése átirányítási részeként. Tehát, használja ezt a mezőt, átirányíthatja küldött összes kérést https://www.contoso.com/ * való https://www.contoso.com/redirected-site.

## <a name="query-string-parameters"></a>Lekérdezési karakterlánc paraméterei
Továbbá cserélje le a lekérdezési karakterlánc paraméterei az átirányított URL-címben. Annak érdekében, hogy cserélje le az összes meglévő lekérdezési karakterláncot a bejövő kérelem URL-címe, adja meg a mezőben a "Replace", és majd állítsa be a megfelelő értékre. Ellenkező esetben az eredeti készletét lekérdezési karakterláncokat is megőrizheti a mező "Preserve" beállításával. Tegyük fel, ez a mező használatával irányíthatja át küldött összes forgalom https://www.contoso.com/foo/bar való https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F. 

## <a name="destination-fragment"></a>Cél töredék
A cél töredék URL-cím része, miután '#', általában használt böngészők által egy adott szakaszban lapra kerül. Ez a mező hozzáadása egy kódrészletet az átirányítási URL-címet állíthatja be.

## <a name="next-steps"></a>További lépések

- [Frontdoor létrehozására](quickstart-create-front-door.md) vonatkozó információk.
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.

<!--Image references-->
[1]: ./media/front-door-url-redirect/front-door-url-redirect.png