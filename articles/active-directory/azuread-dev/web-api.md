---
title: Web API-alkalmazások az Azure Active Directoryban
description: Leírja, hogy milyen webes API-alkalmazások, és az alapismeretek a protokoll-folyamat, a regisztráció és a jogkivonat lejárati ehhez az alkalmazástípushoz.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 9cf5a9c81ca1d7a42a5a8e342dee55f335656c3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154423"
---
# <a name="web-api"></a>Webes API

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

A web API-alkalmazások olyan webalkalmazások, amelyeknek erőforrásokat kell beszereznie egy webes API-ból. Ebben a forgatókönyvben két identitástípus van, amelyeket a webalkalmazás a webes API hitelesítéséhez és hívásához használhat:

- **Alkalmazásidentitás** – Ez a forgatókönyv az OAuth 2.0 ügyfél hitelesítő adatok megadása az alkalmazásként való hitelesítéshez és a webes API eléréséhez használja. Alkalmazásidentitás használata esetén a webes API csak azt észleli, hogy a webalkalmazás hívja meg, mivel a webes API nem kap semmilyen információt a felhasználóról. Ha az alkalmazás információt kap a felhasználóról, akkor az alkalmazásprotokollon keresztül lesz elküldve, és az Azure AD nem írja alá. A webes API megbízik abban, hogy a webalkalmazás hitelesítette a felhasználót. Emiatt ezt a mintát megbízható alrendszernek nevezzük.
- **Delegált felhasználói identitás** – Ez a forgatókönyv kétféleképpen valósítható meg: OpenID Connect és OAuth 2.0 engedélyezési kód megadása egy bizalmas ügyféllel. A webalkalmazás beszerez egy hozzáférési jogkivonatot a felhasználó számára, amely bizonyítja a webes API-nak, hogy a felhasználó sikeresen hitelesítette a webalkalmazást, és hogy a webalkalmazás képes volt egy delegált felhasználói identitást beszerezni a webes API hívásához. Ez a hozzáférési jogkivonat a kérelemben a webes API-t, amely engedélyezi a felhasználót, és visszaadja a kívánt erőforrást.

Az alkalmazásidentitás és a delegált felhasználói identitástípusok az alábbi folyamat ban is megvitatásra kerülnek. A legfontosabb különbség közöttük az, hogy a delegált felhasználói identitás először be kell szereznie egy engedélyezési kódot, mielőtt a felhasználó bejelentkezhet, és hozzáférhet a webes API-hoz.

## <a name="diagram"></a>Ábra

![Webalkalmazás -hoz Pókháló API-diagram](./media/authentication-scenarios/web-app-to-web-api.png)

## <a name="protocol-flow"></a>Protokoll-folyamat

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Alkalmazásidentitás OAuth 2.0 ügyfélhitelesítő hitelesítő adatok megadásával

1. Egy felhasználó be van jelentkezve az Azure AD-be a webalkalmazásban (további információért lásd a **webalkalmazások** szakaszt).
1. A webalkalmazásnak be kell szereznie egy hozzáférési jogkivonatot, hogy hitelesíthesse magát a webes API-ban, és lekérdezhesse a kívánt erőforrást. Kérést küld az Azure AD jogkivonat-végpont, a hitelesítő adatok, az alkalmazásazonosító és a webes API-alkalmazásazonosító URI-t.
1. Az Azure AD hitelesíti az alkalmazást, és egy JWT-hozzáférési jogkivonatot ad vissza, amely a webes API-hívásához szolgál.
1. HTTPS-kapcsolaton keresztül a webalkalmazás a visszaadott JWT-hozzáférési jogkivonatot használja a JWT-karakterlánc hozzáadása a "tulajdonos" megjelöléssel a kérelem engedélyezési fejlécében a webes API-hoz. A webes API ezután érvényesíti a JWT-jogkivonatot, és ha az érvényesítés sikeres, visszaadja a kívánt erőforrást.

### <a name="delegated-user-identity-with-openid-connect"></a>Delegált felhasználói identitás az OpenID Connect segítségével

1. A felhasználó be van jelentkezve egy webalkalmazás az Azure AD használatával (lásd a webböngésző a webalkalmazás szakasz felett). Ha a felhasználó a webalkalmazás még nem járult hozzá, hogy a webes alkalmazás hívja meg a webes API nevében, a felhasználónak meg kell egyed. Az alkalmazás megjeleníti a szükséges engedélyeket, és ha ezek bármelyike rendszergazdai szintű engedélyek, a címtárban lévő normál felhasználó nem lesz képes beleegyezését adni. Ez a jóváhagyási folyamat csak a több-bérlős alkalmazások, nem egy-bérlős alkalmazások, mivel az alkalmazás már rendelkezik a szükséges engedélyekkel. Amikor a felhasználó bejelentkezett, a webalkalmazás kapott egy azonosító jogkivonatot a felhasználó adatait, valamint egy engedélyezési kódot.
1. Az Azure AD által kiadott engedélyezési kód használatával a webalkalmazás kérést küld az Azure AD tokenvégpontjának, amely tartalmazza az engedélyezési kódot, az ügyfélalkalmazás részleteit (alkalmazásazonosító és átirányítási URI), valamint a kívánt erőforrást (alkalmazásazonosítót URI a webes API-hoz).
1. Az engedélyezési kódot és a webalkalmazásra és a webes API-ra vonatkozó információkat az Azure AD érvényesíti. Sikeres érvényesítés után az Azure AD két jogkivonatot ad vissza: egy JWT-hozzáférési jogkivonatot és egy JWT-frissítési jogkivonatot.
1. HTTPS-kapcsolaton keresztül a webalkalmazás a visszaadott JWT-hozzáférési jogkivonatot használja a JWT-karakterlánc hozzáadása a "tulajdonos" megjelöléssel a kérelem engedélyezési fejlécében a webes API-hoz. A webes API ezután érvényesíti a JWT-jogkivonatot, és ha az érvényesítés sikeres, visszaadja a kívánt erőforrást.

### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Delegált felhasználói identitás az OAuth 2.0 engedélyezési kód megadásával

1. Egy felhasználó már be van jelentkezve egy webalkalmazásba, amelynek hitelesítési mechanizmusa független az Azure AD-től.
1. A webalkalmazás nak szüksége van egy engedélyezési kódra egy hozzáférési jogkivonat beszerzéséhez, ezért a böngészőn keresztül kérelmet ad ki az Azure AD engedélyezési végpontjára, biztosítva az alkalmazásazonosítót, és a sikeres hitelesítés után átirányítva az URI-t a webalkalmazáshoz. A felhasználó bejelentkezik az Azure AD-be.
1. Ha a felhasználó a webalkalmazás még nem járult hozzá, hogy a webes alkalmazás hívja meg a webes API nevében, a felhasználónak meg kell egyed. Az alkalmazás megjeleníti a szükséges engedélyeket, és ha ezek bármelyike rendszergazdai szintű engedélyek, a címtárban lévő normál felhasználó nem lesz képes beleegyezését adni. Ez a hozzájárulás egy- és több-bérlős alkalmazásokra egyaránt vonatkozik. Az egybérlős esetben a rendszergazda rendszergazdai hozzájárulás a felhasználók nevében. Ez az Azure `Grant Permissions` [Portal](https://portal.azure.com)on található gombbal végezhető el. 
1. Miután a felhasználó hozzájárult, a webalkalmazás megkapja a hozzáférési jogkivonat megszerzéséhez szükséges engedélyezési kódot.
1. Az Azure AD által kiadott engedélyezési kód használatával a webalkalmazás kérést küld az Azure AD tokenvégpontjának, amely tartalmazza az engedélyezési kódot, az ügyfélalkalmazás részleteit (alkalmazásazonosító és átirányítási URI), valamint a kívánt erőforrást (alkalmazásazonosítót URI a webes API-hoz).
1. Az engedélyezési kódot és a webalkalmazásra és a webes API-ra vonatkozó információkat az Azure AD érvényesíti. Sikeres érvényesítés után az Azure AD két jogkivonatot ad vissza: egy JWT-hozzáférési jogkivonatot és egy JWT-frissítési jogkivonatot.
1. HTTPS-kapcsolaton keresztül a webalkalmazás a visszaadott JWT-hozzáférési jogkivonatot használja a JWT-karakterlánc hozzáadása a "tulajdonos" megjelöléssel a kérelem engedélyezési fejlécében a webes API-hoz. A webes API ezután érvényesíti a JWT-jogkivonatot, és ha az érvényesítés sikeres, visszaadja a kívánt erőforrást.

## <a name="code-samples"></a>Kódminták

Tekintse meg a webalkalmazás-web API-forgatókönyvek kódmintáit. És, nézz vissza gyakran - új mintákat adnak gyakran. Webalkalmazás [a webes API-ba](sample-v1-code.md#web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity).

## <a name="app-registration"></a>Alkalmazásregisztráció

Ha regisztrál egy alkalmazást az Azure AD v1.0-s végpontjával, olvassa [el az Alkalmazás regisztrálása című témakört.](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)

* Egyetlen bérlő – Az alkalmazás identitását és a delegált felhasználói identitás esetekben a webalkalmazás és a webes API-t regisztrálni kell ugyanabban a címtárban az Azure AD-ben. A webes API beállítható úgy, hogy engedélyeket tűzött ki, amelyek a webalkalmazás erőforrásaihoz való hozzáférésének korlátozására szolgálnak. Delegált felhasználói identitástípus használata esetén a webalkalmazásnak ki kell választania a kívánt engedélyeket az Azure Portal **on the Permissions to other applications** legördülő menüből. Ez a lépés nem szükséges, ha az alkalmazás identitástípusát használja.
* Több-bérlős – Először a webalkalmazás van beállítva, hogy jelezze az engedélyeket, hogy működéséhez szükséges. A szükséges engedélyek listája egy párbeszédablakban jelenik meg, amikor a célkönyvtárban lévő felhasználó vagy rendszergazda hozzájárul az alkalmazáshoz, amely elérhetővé teszi azt a szervezet számára. Egyes alkalmazások csak felhasználói szintű engedélyeket igényelnek, amelyekhez a szervezet bármely felhasználója beleegyezhet. Más alkalmazások rendszergazdai szintű engedélyeket igényelnek, amelyekhez a szervezet felhasználója nem járulhat hozzá. Csak a címtár-rendszergazda adhat beleegyezést az ilyen szintű engedélyeket igénylő alkalmazásokhoz. Amikor a felhasználó vagy a rendszergazda hozzájárul, a webalkalmazás és a webes API egyaránt regisztrálva van a címtárban.

## <a name="token-expiration"></a>Token lejárata

Amikor a webalkalmazás az engedélyezési kódot használja egy JWT-hozzáférési jogkivonat lekéréséhez, jwt-frissítési jogkivonatot is kap. Amikor a hozzáférési jogkivonat lejár, a frissítési jogkivonat a felhasználó újbóli hitelesítésére használható anélkül, hogy újra be kellene jelentkeznie. Ezt a frissítési jogkivonatot ezután a felhasználó hitelesítésére használják, ami egy új hozzáférési jogkivonatot és frissítési jogkivonatot eredményez.

## <a name="next-steps"></a>További lépések

- További információ az [egyéb alkalmazástípusokról és -forgatókönyvekről](app-types.md)
- Ismerje meg az Azure [AD-hitelesítés alapjait](v1-authentication-scenarios.md)
