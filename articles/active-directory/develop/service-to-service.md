---
title: Az Azure Active Directory Service-to-service-alkalmazások
description: Protokoll flow, a regisztráció és az alkalmazástípushoz jogkivonat lejárati azt ismerteti, milyen szolgáltatások, alkalmazások és az alapokat.
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
ms.openlocfilehash: 1a10bf35618d7f543957d1f839618397f08698d4
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55095429"
---
# <a name="service-to-service-apps"></a>Szolgáltatások – alkalmazások

Szolgáltatások – alkalmazások lehet egy démon, vagy a kiszolgáló alkalmazást, amelyet erőforrások lekérése a webes API-k. Nincsenek alkalmazandó az ebben a szakaszban két alárendelt forgatókönyvek:

- Egy démon, amelyet egy webes API, épülő az OAuth 2.0 ügyfél-hitelesítő adatok engedélyezési típus meghívásához

    Ebben az esetben fontos tudni, hogy néhány dolgot. Első lépésként felhasználói interakció esetén nem lehet egy démon alkalmazással, amelyhez szükség van az alkalmazás a saját identitás rendelkezik. Egy démon alkalmazás például a batch-feladat vagy egy operációs rendszer szolgáltatás fut a háttérben. Ez az alkalmazástípus egy hozzáférési jogkivonatot kér az alkalmazás azonosítóját és az Alkalmazásazonosítót, hitelesítő adat (jelszó vagy tanúsítvány) és alkalmazás bemutatása az Azure AD-Alkalmazásazonosító URI-ja. Sikeres hitelesítést követően a démon kap egy hozzáférési jogkivonatot az Azure AD-ben szereznek, majd a webes API meghívásához.

- Kiszolgálói alkalmazás (például a webes API-k), amelyek egy webes API-t OAuth 2.0-alapú meghatalmazásos draft specifikáció épül

    Ebben a forgatókönyvben tegyük fel, hogy a felhasználó a natív alkalmazások hitelesítette, és a natív alkalmazásnak kell webes API-hívás. Az Azure AD kibocsát egy JWT jogkivonat a webes API meghívásához. Ha a webes API meghívása egy másik alsóbb rétegbeli webes API-t, a alapú meghatalmazásos folyamat használhatja a felhasználói identitás delegálásához, és a második szintű webes API hitelesítésére.

## <a name="diagram"></a>Ábra

![Démon, vagy az webes API-diagram](./media/authentication-scenarios/daemon_server_app_to_web_api.png)

## <a name="dprotocol-flow"></a>DProtocol folyamat

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Identita aplikace az OAuth 2.0 ügyfél-hitelesítő adatok megadása

1. Először a kiszolgálói alkalmazás hitelesítenie kell az Azure AD szolgáltatásba, például egy interaktív bejelentkezési párbeszédpanel emberi beavatkozás nélkül történik. Adatbeolvasási kérelmet küld az Azure AD jogkivonat végpontra, a hitelesítő adat, alkalmazás Azonosítóját és alkalmazás Alkalmazásazonosító URI-t biztosít.
1. Az Azure AD hitelesíti magát az alkalmazást, és a webes API meghívásához használt JWT hozzáférési jogkivonatot ad vissza.
1. HTTPS-en keresztüli a webes alkalmazás használja a kapott JWT jogkivonat a JWT-karakterlánc a "Tulajdonos" megnevezéssel a kérelem az engedélyezési fejléc hozzáadása a webes API-t. A webes API majd érvényesíti a JWT jogkivonatot, és ha az érvényesítés sikeres, visszaadja a kívánt erőforrást.

### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>A meghatalmazott felhasználó identitását az OAuth 2.0-alapú meghatalmazásos Draft specifikáció

A folyamat az alábbiak ismertetik feltételezi, hogy a felhasználó hitelesítése egy másik alkalmazás (például egy natív alkalmazást), és az első szintű webes API hozzáférési jogkivonat beszerzése a felhasználói identitás használják.

1. A natív alkalmazás elküldi a hozzáférési jogkivonatot az első szintű webes API-t.
1. Az első szintű webes API-t egy kérést küld az Azure AD jogkivonat-végpont, így az alkalmazás azonosítója és a hitelesítő adatokat, valamint a felhasználói jogkivonat. Emellett a kérést küld-e egy on_behalf_of paraméter, amely azt jelzi, hogy a webes API-t az eredeti felhasználó nevében alsóbb rétegbeli webes API-hívás új jogkivonat kér.
1. Az Azure AD ellenőrzi, hogy az első szintű webes API-t a második szintű webes API-k elérésére jogosult, és érvényesíti a kérést, a JWT-jogkivonatokkal visszaadó jwt-t az első szintű webes API-jogkivonat frissítésére.
1. HTTPS-en keresztüli az első szintű webes API ekkor meghívja a második szintű webes API a jogkivonat-karakterláncot a kérelem az engedélyezési fejléc hozzáfűzésével. Az első szintű webes API-t továbbra is a második szintű webes API hívása, mindaddig, amíg a hozzáférési jogkivonatot és frissítési jogkivonatok érvényesek.

## <a name="code-samples"></a>Kódminták

Tekintse meg a Kódminták démon vagy kiszolgálói alkalmazás webes API-forgatókönyvekhez. És a új mintát gyakran hozzáadása során gyakran készítsen biztonsági ellenőrzést. [Kiszolgáló vagy alkalmazás démon a webes API-hoz](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)

## <a name="app-registration"></a>Appok regisztrálása

* Egyetlen bérlő – az alkalmazás azonosítóját és a delegált felhasználó identitás esetben a démon, vagy server alkalmazást regisztrálni kell ugyanabban a címtárban az Azure ad-ben. A webes API elérhetővé engedélykészletet, amelyek segítségével korlátozhatja a démon, vagy az erőforrásokhoz való hozzáférése konfigurálható. Ha egy meghatalmazott felhasználói identitástípus használatban van, a kiszolgálói alkalmazás kell válassza ki a kívánt engedélyekkel a "Engedélyeket az egyéb alkalmazások" legördülő menüből az Azure Portalon. Ebben a lépésben nincs szükség, ha az identitás alkalmazástípus használatban van.
* Több-bérlős-First, a démon vagy kiszolgálói alkalmazás jelzi a megfelelő működéséhez szükséges engedélyekkel van konfigurálva. Szükséges engedélyek listája egy párbeszédpanel jelenik meg, amikor egy felhasználó vagy rendszergazda a célkönyvtárban duplikátum beleegyezésével az alkalmazáshoz, ami lehetővé teszi a szervezet számára elérhető. Egyes alkalmazások csak a felhasználói szintű engedélyeket, amelyeket a szervezet bármely felhasználója jóváhagyhat van szükségük. Más alkalmazások szükséges rendszergazdai engedélyekkel, amelyek a szervezet egy felhasználója nem járulhatnak hozzá. Csak egy könyvtár rendszergazda engedélyezheti, hogy ez a jogosultsági szint szükséges alkalmazásokat. Amikor a felhasználó vagy rendszergazda hozzájárul, mind a webes API-k a címtárban van regisztrálva.

## <a name="token-expiration"></a>Jogkivonat lejáratáról

Az első alkalmazás eléréséhez a JWT jogkivonat a hozzáférési kód használja, amikor a JWT-frissítési jogkivonatok is fogad. Ha a hozzáférési jogkivonat lejár, a frissítési jogkivonat használható újra hitelesíteni kell a felhasználói hitelesítő adatok kérése nélkül. A frissítési jogkivonat majd segítségével hitelesíti a felhasználót, amely egy új hozzáférési jogkivonatot és a frissítési jogkivonatot eredményez.

## <a name="next-steps"></a>További lépések

- További információk egyéb [alkalmazástípusok és forgatókönyvek](app-types.md)
- További tudnivalók az Azure AD [hitelesítés alapjai](authentication-scenarios.md)
