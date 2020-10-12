---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/11/2020
ms.author: mimart
ms.openlocfilehash: ef08f1adc15475cd5dd38548ed39dc57532c0ef5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "78185835"
---
## <a name="guidelines-for-using-javascript"></a>Útmutató a JavaScript használatához

Kövesse az alábbi irányelveket, amikor testreszabja az alkalmazás felületét a JavaScript használatával:

- Ne hozzon létre egy kattintásos eseményt `<a>` HTML-elemeken.
- Ne vegyen fel függőséget Azure AD B2C kód vagy Megjegyzés alapján.
- Ne módosítsa Azure AD B2C HTML-elemek sorrendjét vagy hierarchiáját. A felhasználói felületi elemek sorrendjének szabályozásához használjon Azure AD B2C szabályzatot.
- A következő megfontolásokkal hívhat meg bármilyen REST-szolgáltatást:
    - Előfordulhat, hogy az ügyféloldali HTTP-hívások engedélyezéséhez be kell állítania a REST szolgáltatás CORS.
    - Győződjön meg arról, hogy a REST szolgáltatás biztonságos, és csak a HTTPS protokollt használja.
    - Ne használja közvetlenül a JavaScriptet Azure AD B2C végpontok meghívásához.
- Beágyazhatja a JavaScriptet, vagy külső JavaScript-fájlokra mutató hivatkozásokat is használhat. Külső JavaScript-fájl használata esetén ügyeljen arra, hogy az abszolút URL-címet használja, nem pedig relatív URL-címet.
- JavaScript-keretrendszerek:
    - Azure AD B2C a jQuery egy adott verzióját használja. Ne adja meg a jQuery egy másik verzióját. Ha egynél több verziót használ ugyanazon a lapon, problémákat okoz.
    - A RequireJS használata nem támogatott.
    - Azure AD B2C nem támogatja a legtöbb JavaScript-keretrendszert.
- Azure AD B2C beállítások a következő módon olvashatók: hívás `window.SETTINGS` , `window.CONTENT` objektumok, például az aktuális felhasználói felület nyelve. Ne módosítsa az objektumok értékét.
- A Azure AD B2C-hibaüzenet testreszabásához használjon honosítást egy házirendben.
- Ha bármilyen adat egy szabályzat használatával érhető el, általában az ajánlott módszer.
