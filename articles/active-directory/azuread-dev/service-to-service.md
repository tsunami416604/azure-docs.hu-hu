---
title: Szolgáltatás-szolgáltatás alkalmazások az Azure Active Directoryban
description: Ez a témakör azt ismerteti, hogy milyen szolgáltatás-szolgáltatás alkalmazások és az alapismeretek a protokoll-folyamat, a regisztráció és a jogkivonat lejárata az adott alkalmazástípushoz.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 179034533d90dbbb6ca362fc6f72996f32873729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154763"
---
# <a name="service-to-service-apps"></a>Szolgáltatás-szolgáltatás alkalmazások

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

A szolgáltatás-szolgáltatás alkalmazások lehetnek démon- vagy kiszolgálóalkalmazások, amelyeknek erőforrásokat kell beszerezniük egy webes API-ból. Erre a szakaszra két alforgatókönyv vonatkozik:

- Az OAuth 2.0 ügyfélhitelesítő adatok ra épülő webes API-t meghívnió démon

    Ebben a forgatókönyvben fontos megérteni néhány dolgot. Először is, a felhasználói beavatkozás nem lehetséges egy démon alkalmazás, amely megköveteli, hogy az alkalmazás saját identitást. Egy démonalkalmazás például egy kötegelt feladat, vagy egy háttérben futó operációsrendszer-szolgáltatás. Az ilyen típusú alkalmazás az alkalmazásidentitás használatával kér egy hozzáférési jogkivonatot, és bemutatja az alkalmazásazonosítóját, hitelesítő adatait (jelszavát vagy tanúsítványát), valamint az Azure AD alkalmazásazonosító-URI-ját. Sikeres hitelesítés után a démon kap egy hozzáférési jogkivonatot az Azure AD-től, amely ezután a webes API-hívásához használható.

- Olyan kiszolgálóalkalmazás (például webes API), amelynek meg kell hívnia egy webes API-t, amely az OAuth 2.0 piszkozatspecifikációra épül

    Ebben a forgatókönyvben tegyük fel, hogy a felhasználó hitelesített egy natív alkalmazáson, és ez a natív alkalmazás meg kell hívnia egy webes API-t. Az Azure AD kiad egy JWT-hozzáférési jogkivonatot a webes API-hívásához. Ha a webes API-t meg kell hívnia egy másik alsóbb rétegbeli webes API-t, a folyamat nevében delegálhatja a felhasználó identitását, és hitelesítheti magát a második szintű webes API-t.

## <a name="diagram"></a>Ábra

![Démon- vagy kiszolgálóalkalmazás webAPI-diagramra](./media/authentication-scenarios/daemon-server-app-to-web-api.png)

## <a name="protocol-flow"></a>Protokoll-folyamat

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Alkalmazásidentitás OAuth 2.0 ügyfélhitelesítő hitelesítő adatok megadásával

1. Először is a kiszolgálóalkalmazás nak hitelesítenie kell magát az Azure AD-vel, emberi beavatkozás, például interaktív bejelentkezési párbeszéd nélkül. Kérést küld az Azure AD jogkivonat-végpontjára, amely megadja a hitelesítő adatokat, az alkalmazásazonosítót és az alkalmazásazonosító URI-t.
1. Az Azure AD hitelesíti az alkalmazást, és egy JWT-hozzáférési jogkivonatot ad vissza, amely a webes API-hívásához szolgál.
1. HTTPS-kapcsolaton keresztül a webalkalmazás a visszaadott JWT-hozzáférési jogkivonatot használja a JWT-karakterlánc hozzáadása a "tulajdonos" megjelöléssel a kérelem engedélyezési fejlécében a webes API-hoz. A webes API ezután érvényesíti a JWT-jogkivonatot, és ha az érvényesítés sikeres, visszaadja a kívánt erőforrást.

### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Delegált felhasználói identitás az OAuth 2.0-val a vázlatspecifikáció nevében

Az alábbiakban tárgyalt folyamat feltételezi, hogy a felhasználó egy másik alkalmazáson (például egy natív alkalmazásban) lett hitelesítve, és a felhasználói identitásuk az első rétegbeli webes API hozzáférési jogkivonatának megszerzésére lett használva.

1. A natív alkalmazás elküldi a hozzáférési jogkivonatot az első rétegbeli webes API-ba.
1. Az első rétegbeli webes API egy kérést küld az Azure AD token végpont, amely az alkalmazásazonosító és a hitelesítő adatok, valamint a felhasználó hozzáférési jogkivonatát. Emellett a kérelem küldése egy on_behalf_of paraméter, amely azt jelzi, hogy a webes API új jogkivonatokat kér egy alsóbb rétegbeli webes API-t az eredeti felhasználó nevében.
1. Az Azure AD ellenőrzi, hogy az első rétegbeli webes API-nak van-e engedélye a második szintű webes API eléréséhez, és érvényesíti a kérést, egy JWT-hozzáférési jogkivonatot és egy JWT-frissítési jogkivonatot ad vissza az első szintű webes API-nak.
1. HTTPS-en keresztül az első rétegbeli webes API-t, majd meghívja a második szintű webes API-t a tokenkarakterlánc hozzáfűzésével az engedélyezési fejlécben a kérelemben. Az első rétegbeli webes API továbbra is meghívhatja a második rétegbeli webes API-t mindaddig, amíg a hozzáférési jogkivonat és a frissítési jogkivonatok érvényesek.

## <a name="code-samples"></a>Kódminták

Tekintse meg a daemon vagy a kiszolgálóalkalmazás webAPI-forgatókönyveikód-mintáit: [Kiszolgáló vagy démonalkalmazás webAPI-ra](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)

## <a name="app-registration"></a>Alkalmazásregisztráció

* Egyetlen bérlő – Az alkalmazás identitását és a delegált felhasználói identitás esetekben a démon vagy a kiszolgáló alkalmazás regisztrálva kell lennie ugyanabban a könyvtárban az Azure AD-ben. A webes API beállítható úgy, hogy engedélyeket tűzött ki, amelyek a démon vagy a kiszolgáló erőforrásaihoz való hozzáférésének korlátozására szolgálnak. Delegált felhasználói identitástípus alkalmazása esetén a kiszolgálóalkalmazásnak ki kell választania a kívánt engedélyeket. Az **alkalmazás regisztrációjának API-engedélylapján,** miután kiválasztotta az **Engedély hozzáadása** és az API-család kiválasztását, válassza a **Delegált engedélyek**lehetőséget, majd válassza ki az engedélyeket. Ez a lépés nem szükséges, ha az alkalmazás identitástípusát használja.
* Több-bérlős – Először a démon vagy a kiszolgálóalkalmazás úgy van beállítva, hogy jelezze a működési engedélyeket. A szükséges engedélyek listája egy párbeszédablakban jelenik meg, amikor a célkönyvtárban lévő felhasználó vagy rendszergazda hozzájárul az alkalmazáshoz, amely elérhetővé teszi azt a szervezet számára. Egyes alkalmazások csak felhasználói szintű engedélyeket igényelnek, amelyekhez a szervezet bármely felhasználója beleegyezhet. Más alkalmazások rendszergazdai szintű engedélyeket igényelnek, amelyekhez a szervezet felhasználója nem járulhat hozzá. Csak a címtár-rendszergazda adhat beleegyezést az ilyen szintű engedélyeket igénylő alkalmazásokhoz. Amikor a felhasználó vagy a rendszergazda hozzájárul, mindkét webes API-k regisztrálva vannak a címtárban.

## <a name="token-expiration"></a>Token lejárata

Amikor az első alkalmazás az engedélyezési kódot használja a JWT-hozzáférési jogkivonat lekéréséhez, jwt-frissítési jogkivonatot is kap. Amikor a hozzáférési jogkivonat lejár, a frissítési jogkivonat segítségével újra hitelesíteni a felhasználót hitelesítő adatok kérése nélkül. Ezt a frissítési jogkivonatot ezután a felhasználó hitelesítésére használják, ami egy új hozzáférési jogkivonatot és frissítési jogkivonatot eredményez.

## <a name="next-steps"></a>További lépések

- További információ az [egyéb alkalmazástípusokról és -forgatókönyvekről](app-types.md)
- Ismerje meg az Azure [AD-hitelesítés alapjait](v1-authentication-scenarios.md)
