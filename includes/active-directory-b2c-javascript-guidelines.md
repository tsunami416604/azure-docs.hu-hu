---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/11/2020
ms.author: mimart
ms.openlocfilehash: ef08f1adc15475cd5dd38548ed39dc57532c0ef5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185835"
---
## <a name="guidelines-for-using-javascript"></a>A JavaScript használatának irányelvei

Kövesse az alábbi irányelveket, amikor az alkalmazás felületét JavaScript használatával testreszabja:

- Ne kössön kattintási `<a>` eseményt a HTML-elemekhez.
- Ne vegyen igénybe függőséget az Azure AD B2C-kódtól vagy a megjegyzésektől.
- Ne módosítsa az Azure AD B2C HTML-elemek sorrendjét vagy hierarchiáját. Az Azure AD B2C-szabályzat használatával szabályozhatja a felhasználói felület elemeinek sorrendjét.
- A következő szempontokat figyelembe véve bármely RESTful szolgáltatást hívhatja:
    - Előfordulhat, hogy be kell állítania a RESTful szolgáltatás CORS-t az ügyféloldali HTTP-hívások engedélyezéséhez.
    - Győződjön meg arról, hogy a RESTful szolgáltatás biztonságos, és csak a HTTPS protokollt használja.
    - Ne használja a JavaScript közvetlenül az Azure AD B2C végpontok hívásához.
- Beágyazhatja a JavaScriptet, vagy külső JavaScript-fájlokra hivatkozhat. Külső JavaScript-fájl használata esetén ügyeljen arra, hogy az abszolút URL-t használja, ne pedig relatív URL-t.
- JavaScript-keretrendszerek:
    - Az Azure AD B2C a jQuery egy adott verzióját használja. Ne adja meg a jQuery másik verzióját. Ha ugyanazon az oldalon több verziót is használ, az problémákat okoz.
    - A RequireJS használata nem támogatott.
    - Az Azure AD B2C nem támogatja a legtöbb JavaScript-keretrendszert.
- Az Azure AD B2C-beállítások `window.SETTINGS` `window.CONTENT` at lehet olvasni a hívás , objektumok, például az aktuális felhasználói felület nyelvét. Ne változtassa meg ezeknek az objektumoknak az értékét.
- Az Azure AD B2C hibaüzenet testreszabásához használja a honosítást egy szabályzatban.
- Ha valamit el lehet érni egy házirend használatával, általában ez az ajánlott módszer.
