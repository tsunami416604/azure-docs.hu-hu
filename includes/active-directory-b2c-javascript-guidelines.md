---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/11/2020
ms.author: mimart
ms.openlocfilehash: ef08f1adc15475cd5dd38548ed39dc57532c0ef5
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78185835"
---
## <a name="guidelines-for-using-javascript"></a>Útmutató a JavaScript használatával

Ha testre szabja a felületén az alkalmazás JavaScript használatával, kövesse az alábbi irányelveket:

- `<a>` HTML-elemeken nem köthető kattintási esemény.
- Nem kell egy függőség az Azure AD B2C-kód vagy a megjegyzéseket.
- Ne módosítsa, sorrend, illetve az Azure AD B2C-vel HTML-elemek hierarchiájának. Egy Azure AD B2C-szabályzat segítségével szabályozhatja a felhasználói felületi elemek sorrendjét.
- E szempontok bármely RESTful szolgáltatás hívása:
    - Szükség lehet a CORS-t lehetővé teszi a HTTP-hívások ügyféloldali RESTful szolgáltatás beállítása.
    - Ellenőrizze, hogy a RESTful szolgáltatás biztonságos, és csak a HTTPS protokollt használja.
    - Ne használja a JavaScript közvetlenül az Azure AD B2C-végpontokat.
- Ágyazható be a JavaScript, vagy JavaScript-fájlok külső kapcsolat. Egy külső JavaScript-fájl használata esetén ügyeljen arra, hogy az abszolút URL-címet, és nem egy relatív URL-címet használja.
- JavaScript-keretrendszer:
    - Az Azure AD B2C jQuery egy adott verzióját használja. Nem tartalmazza a jQuery egy másik verziója. Egynél több verziója ugyanazon az oldalon rendelkezik, problémákat.
    - RequireJS használata nem támogatott.
    - A legtöbb JavaScript-keretrendszerek nem támogatottak az Azure AD B2C-t.
- Azure AD B2C beállítások a `window.SETTINGS`, `window.CONTENT` objektumok, például az aktuális felhasználói felület nyelvének meghívásával olvashatók. Ezek az objektumok értékét ne módosítsa.
- Az Azure AD B2C-vel hibaüzenet egyéni beállításához használja a honosítás egy házirendben.
- Ha bármi házirend segítségével biztosíthatja, általában, az ajánlott módszer a.
