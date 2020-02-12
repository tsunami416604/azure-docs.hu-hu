---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/11/2020
ms.author: marsma
ms.openlocfilehash: 687853720e8f963d5a58093b824bb36e8063bcf7
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149072"
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
