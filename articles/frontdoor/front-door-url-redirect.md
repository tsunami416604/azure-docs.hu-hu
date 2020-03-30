---
title: Azure bejárati ajtó - URL átirányítás | Microsoft dokumentumok
description: Ez a cikk segít megérteni, hogy az Azure Bejárati ajtó hogyan támogatja az URL-átirányítást az útvonalaikhoz, ha konfigurálva van.
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
ms.openlocfilehash: 5e3e44c4aee84fe9e2e21174a1d65fdf26b765a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295476"
---
# <a name="url-redirect"></a>URL-átirányítás
Az Azure Bejárati ajtaját használhatja a forgalom átirányításához. A forgalmat több szinten (protokoll, állomásnév, elérési út, lekérdezési karakterlánc) irányíthatja át, és az összes funkció konfigurálható az egyes mikroszolgáltatásokhoz, mivel az átirányítás útvonalalapú. Ez leegyszerűsíti az alkalmazáskonfigurációt, optimalizálja az erőforrás-használatot, és támogatja az új átirányítási forgatókönyveket, beleértve a globális és az útvonal-alapú átirányítást.
</br>

![Az Azure bejárati ajtajának URL-címe átirányítás][1]

## <a name="redirection-types"></a>Átirányítási típusok
Az átirányítási típus beállítja az ügyfelek válaszállapotkódját az átirányítás céljának megértéséhez. A következő átirányítási típusok támogatottak:

- **301 (Állandóan áthelyezve):** Azt jelzi, hogy a célerőforrás új állandó URI-hoz lett rendelve, és az erőforrásra vonatkozó jövőbeli hivatkozásoknak a mellékelt URI-k egyikét kell használniuk. Használja a 301-es állapotkódot a HTTP-HTTPS átirányításhoz. 
- **302 (Talált)**: Azt jelzi, hogy a célerőforrás ideiglenesen egy másik URI alatt található. Mivel az átirányítás időnként módosulhat, az ügyfélnek továbbra is a hatékony kérelem URI-ját kell használnia a jövőbeli kérelmekhez.
- **307 (Ideiglenes átirányítás)**: Azt jelzi, hogy a célerőforrás ideiglenesen egy másik URI alatt található, és a felhasználói ügynök nem módosíthatja a kérésmetódust, ha automatikus átirányítást hajt végre az adott URI-ra. Mivel az átirányítás idővel változhat, az ügyfélnek továbbra is a jövőbeli kérelmekhez az eredeti hatékony kérelem URI-ját kell használnia.
- **308 (Állandó átirányítás):** Azt jelzi, hogy a célerőforrás új állandó URI-hoz lett rendelve, és az erőforrásra vonatkozó jövőbeni hivatkozásoknak a mellékelt URI-k egyikét kell használniuk. A kapcsolatszerkesztési képességekkel rendelkező ügyfeleknek lehetőség szerint automatikusan újra kell kapcsolniuk a hatékony kérelem URI-jára mutató hivatkozásokat a kiszolgáló által küldött egy vagy több új hivatkozáshoz.

## <a name="redirection-protocol"></a>Átirányítási protokoll
Beállíthatja az átirányításhoz használt protokollt. Ez lehetővé teszi az átirányítási szolgáltatás egyik leggyakoribb használati esetét, azaz a HTTP HTTPS átirányítást.

- **HTTPS :** Állítsa a protokollt csak HTTPS-re, ha át szeretné irányítani a forgalmat a HTTP-ről HTTPS-re. Az Azure Bejárati ajtó azt javasolja, hogy mindig állítsa be az átirányítást csak HTTPS-re.
- **CSAK HTTP**: Ez átirányítja a bejövő kérelmet HTTP-re. Ezt az értéket csak akkor használja, ha meg szeretné tartani a http-forgalmat, amely nem titkosított.
- **Egyeztetési kérelem**: Ez a beállítás megtartja a bejövő kérelem által használt protokollt. Így egy HTTP-kérelem http marad, és egy HTTPS-kérelem marad HTTPS utáni átirányítás.

## <a name="destination-host"></a>Célállomás
Az átirányítási útválasztás konfigurálása részeként módosíthatja az átirányítási kérelem állomásnevét vagy tartományát is. Ez a mező beállíthatja az átirányítás URL-címében szereplő állomásnév módosítását, vagy más módon megőrizheti az állomásnevet a bejövő kérelemből. Így ezzel a mezővel átirányíthatja `https://www.contoso.com/*` az `https://www.fabrikam.com/*`összes küldött kérelmet a rendszerbe.

## <a name="destination-path"></a>Cél elérési útja
Azokban az esetekben, amikor az átirányítás részeként le szeretné cserélni egy URL elérési útszegmensét, ezt a mezőt az új elérési útértékkel állíthatja be. Ellenkező esetben megőrizheti az elérési út értékét az átirányítás részeként. Így ezzel a mezővel átirányíthatja az `https://www.contoso.com/\*` `https://www.contoso.com/redirected-site`összes kérést, amelyet a rendszernek küldött.

## <a name="query-string-parameters"></a>Lekérdezési karakterlánc paraméterei
Az átirányított URL-címben is lecserélheti a lekérdezési karakterlánc paramétereit. A bejövő kérelem URL-címének meglévő lekérdezési karakterláncának cseréjéhez állítsa ezt a mezőt "Csere" értékre, majd állítsa be a megfelelő értéket. Ellenkező esetben megőrizheti az eredeti lekérdezési karakterláncokat a mező "Megőrzés" értékre állításával. Ebben a mezőben például átirányíthatja az `https://www.contoso.com/foo/bar` összes `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F`küldött forgalmat a rendszerbe. 

## <a name="destination-fragment"></a>Céltöredék
A céltöredék az URL-nek a "#" utáni része, amelyet a böngészők általában arra használnak, hogy egy adott szakaszra találjanak egy oldalon. Ezt a mezőt beállíthatja úgy, hogy töredéket adjon az átirányítási URL-címhez.

## <a name="next-steps"></a>További lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.

<!--Image references-->
[1]: ./media/front-door-url-redirect/front-door-url-redirect.png