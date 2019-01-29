---
title: Egyoldalas alkalmazások az Azure Active Directoryban
description: Ismerteti, milyen egyoldalas alkalmazások (gyógyfürdők) vannak, és a protokoll flow, a regisztráció és a jogkivonat lejárati az alkalmazástípushoz az alapokat.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.openlocfilehash: f9f5f7dc4752e3cb568acd3ad907d115e4d24e85
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55102328"
---
# <a name="single-page-applications"></a>Egylapos alkalmazások

Egyoldalas alkalmazások (gyógyfürdők) általában egy JavaScript-megjelenítési réteg (előtérbeli), amely a böngészőben, és a egy webes API-háttéralkalmazás számára az olyan kiszolgálón fut, és valósítja meg az alkalmazás üzleti logikája szerint vannak felépítve. Tudjon meg többet az implicit engedélyezés megadásáról, és segít eldönteni, hogy ez az alkalmazás forgatókönyvhöz jobb: [OAuth2 implicit ismertetése adja meg az Azure Active Directoryban folyamat](v1-oauth2-implicit-grant-flow.md).

Ebben a forgatókönyvben, ha a felhasználó bejelentkezik, a JavaScript előtér-célból használ [Active Directory Authentication Library for JavaScript (adal-t. JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js) és a egy azonosító jogkivonat (id_token) beszerzése az Azure ad-ben az implicit engedélyezés megadásáról. A jogkivonatot a rendszer gyorsítótárazza, és az ügyfél csatlakoztatja a kérést, a tulajdonosi jogkivonatot, amikor a webes API háttéralkalmazás, amely az OWIN közbenső szoftver használatával lett biztonságossá téve.

## <a name="diagram"></a>Ábra

![Egyoldalas alkalmazás diagram](./media/authentication-scenarios/single_page_app.png)

## <a name="protocol-flow"></a>Protokoll folyamat

1. A felhasználó ellép a webalkalmazáshoz.
1. Az alkalmazás a böngészőben a JavaScript-kezelőfelület (megjelenítési réteg) adja vissza.
1. A felhasználó bejelentkezési, például a bejelentkezési hivatkozásra kattintva indítja el. A böngészőben a GET-azonosító jogkivonat kérése az Azure AD engedélyezési végpont küld. Ezt a kérelmet az alkalmazás Azonosítóját és a válasz URL-címe tartalmazza a lekérdezési paraméterek.
1. Az Azure AD ellenőrzi a válasz URL-cím, szemben a regisztrált válasz URL-cím, amelyet az Azure Portalon.
1. A felhasználó bejelentkezik a bejelentkezési oldalon.
1. Sikeres hitelesítés esetén az Azure AD-azonosító jogkivonat hoz létre, és visszaadja egy URL-cím töredék (#), az alkalmazás válasz URL-cím. Éles alkalmazás esetén a válasz URL-cím HTTPS kell lennie. A visszaadott jogkivonat tartalmazza a jogkivonatot érvényesíteni az alkalmazás által igényelt jogcímeket a felhasználó és az Azure AD.
1. A JavaScript-ügyfél a böngészőben futó kód kigyűjti a választ az alkalmazás webes API back end hívások biztonságossá tételéhez használja a jogkivonatot.
1. A böngésző meghívja az alkalmazás webes API vissza az engedélyezési fejléc végződhet az azonosító jogkivonat. Az Azure AD-hitelesítési szolgáltatás kiad egy azonosító jogkivonat használható tulajdonosi jogkivonattal, ha az erőforrás ugyanaz, mint az ügyfél-azonosító (ebben az esetben ez igaz, a webes API-t az alkalmazás saját háttér).

## <a name="code-samples"></a>Kódminták

Tekintse meg a [egyoldalas alkalmazás-forgatókönyvek Kódminták](sample-v1-code.md#single-page-applications). Ügyeljen arra, hogy térjen vissza később gyakran új mintát gyakran kerülnek.

## <a name="app-registration"></a>Appok regisztrálása

* Egyetlen bérlő – Ha egy alkalmazás csak a szervezet számára hoz létre, azt regisztrálni kell a vállalati címtárban az Azure portal használatával.
* Több-bérlős – Ha olyan alkalmazás, amely a szervezeten kívüli felhasználók által használható épít, a vállalati címtárban szerepelnie kell, de is regisztrálni kell az alkalmazást használó minden egyes szervezet címtárában. Elérhetővé teszi az alkalmazás a címtárban, megadhatja a regisztrációs folyamat, amely lehetővé teszi, hogy engedélyt adjanak az alkalmazásnak az ügyfelek számára. Amikor regisztrál az alkalmazáshoz, akkor megjelenik egy párbeszédpanel, amely tartalmazza az alkalmazáshoz szükséges engedélyeket, majd a beállítást, hogy engedélyt adjanak. Attól függően, a szükséges engedélyekkel a másik szervezet rendszergazda is szükség beleegyezés. A felhasználó vagy rendszergazda beleegyezik, az alkalmazás regisztrálva lesz a címtárban.

Miután felvette az alkalmazást, azt kell konfigurálni az OAuth 2.0 típusú implicit engedélyezés protokoll használatára. Ez a protokoll alkalmazások alapértelmezés szerint le van tiltva. Ahhoz, hogy az alkalmazás az OAuth2 típusú implicit engedélyezés protokoll, az Azure Portalról az alkalmazásjegyzék szerkesztése és az "oauth2AllowImplicitFlow" értéket Igaz értékre van állítva. További információ: [alkalmazásjegyzék](reference-app-manifest.md).

## <a name="token-expiration"></a>Jogkivonat lejáratáról

ADAL.js használatával segít:

* egy lejárt jogkivonat frissítését
* a webes API-erőforrás meghívásához hozzáférési jogkivonatot kér

A sikeres hitelesítés után az Azure AD hozzon létre egy munkamenetet a felhasználó böngészőben a cookie-k ír. Vegye figyelembe, hogy létezik-e a munkamenet a felhasználó és az Azure ad-ben (nem a felhasználó és a webes alkalmazás közötti) között. Amikor a jogkivonat lejár, ADAL.js ehhez a munkamenethez beavatkozás nélkül juthat hozzá egy másik jogkivonatot használ. ADAL.js küldeni és fogadni a kérést, az OAuth típusú implicit engedélyezés protokollal egy rejtett iFrame használatával. ADAL.js használatával is ugyanezzel a módszerrel a más webes API-erőforrásoknak az alkalmazás meghívja a mindaddig, amíg ezek az erőforrások támogatja az eltérő eredetű erőforrások megosztása (CORS), regisztrálva van a felhasználó könyvtárában, és minden szükséges jóváhagyás volt csendes hozzáférési tokenek beszerzése a felhasználó által megadott bejelentkezés során.

## <a name="next-steps"></a>További lépések

* További információk egyéb [alkalmazástípusok és forgatókönyvek](app-types.md)
* További tudnivalók az Azure AD [hitelesítés alapjai](authentication-scenarios.md)
