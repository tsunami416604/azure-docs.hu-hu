---
title: Natív alkalmazások az Azure Active Directoryban
description: Bemutatja, hogy mik a natív alkalmazások, és az alapismeretek a protokollfolyamat, a regisztráció és a jogkivonat lejárata ehhez az alkalmazástípushoz.
services: active-directory
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
ms.openlocfilehash: 9ecf711f5442b6f21de53d2735ad1c94d7cb6223
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154797"
---
# <a name="native-apps"></a>Natív alkalmazások

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

A natív alkalmazások olyan alkalmazások, amelyek egy felhasználó nevében webes API-t hívnak meg. Ez a forgatókönyv az OAuth 2.0 engedélyezési kód engedélyezési típusára épül egy nyilvános ügyféllel, az [OAuth 2.0 specifikáció](https://tools.ietf.org/html/rfc6749)4.1 szakaszában leírtak szerint. A natív alkalmazás az OAuth 2.0 protokoll használatával szerez be egy hozzáférési jogkivonatot a felhasználó számára. Ezt a hozzáférési jogkivonatot ezután elküldi a kérelemben a webes API-nak, amely engedélyezi a felhasználót, és visszaadja a kívánt erőforrást.

## <a name="diagram"></a>Ábra

![Natív alkalmazás a webes API-diagramhoz](./media/authentication-scenarios/native-app-to-web-api.png)

## <a name="protocol-flow"></a>Protokoll-folyamat

Ha az AD hitelesítési kódtárak, a legtöbb protokoll részleteket alább leírt kezeli az Ön számára, például a böngésző pop-up, token cache, és kezelése frissítési jogkivonatok.

1. Egy böngésző előugró ablak használatával a natív alkalmazás kérelmet nyújt be az Azure AD engedélyezési végpontjára. Ez a kérelem tartalmazza az alkalmazásazonosítót és a natív alkalmazás átirányítási URI-ját az Azure Portalon látható módon, valamint a webes API alkalmazásazonosító-URI-ját. Ha a felhasználó még nem jelentkezett be, a rendszer kéri, hogy jelentkezzen be újra
1. Az Azure AD hitelesíti a felhasználót. Ha ez egy több-bérlős alkalmazás, és hozzájárulásszükséges az alkalmazás használatához, a felhasználó nak beleegyezését kell adnia, ha még nem tette meg. A jóváhagyás megadása után és a sikeres hitelesítés után az Azure AD kiad egy engedélyezési kód választ az ügyfélalkalmazás átirányítási URI-jára.
1. Amikor az Azure AD kiad egy engedélyezési kód választ az átirányítási URI-ra, az ügyfélalkalmazás leállítja a böngésző interakcióját, és kinyeri az engedélyezési kódot a válaszból. Ezzel az engedélyezési kóddal az ügyfélalkalmazás kérést küld az Azure AD tokenvégpontjára, amely tartalmazza az engedélyezési kódot, az ügyfélalkalmazás részleteit (alkalmazásazonosító és átirányítási URI), valamint a kívánt erőforrást (alkalmazásazonosító URI-t a webes API).
1. Az engedélyezési kódot, valamint az ügyfélalkalmazásra és a webes API-ra vonatkozó információkat az Azure AD érvényesíti. Sikeres érvényesítés után az Azure AD két jogkivonatot ad vissza: egy JWT-hozzáférési jogkivonatot és egy JWT-frissítési jogkivonatot. Emellett az Azure AD a felhasználóalapvető adatait adja vissza, például a megjelenítendő nevét és a bérlői azonosítót.
1. HTTPS-kapcsolaton keresztül az ügyfélalkalmazás a visszaadott JWT-hozzáférési jogkivonatot használja a JWT-karakterlánc hozzáadása a "tulajdonos" megjelöléssel a kérelem engedélyezési fejlécében a webes API-hoz. A webes API ezután érvényesíti a JWT-jogkivonatot, és ha az érvényesítés sikeres, visszaadja a kívánt erőforrást.
1. Amikor a hozzáférési jogkivonat lejár, az ügyfélalkalmazás hibaüzenetet kap, amely azt jelzi, hogy a felhasználónak újra hitelesítenie kell magát. Ha az alkalmazás rendelkezik egy érvényes frissítési jogkivonattal, egy új hozzáférési jogkivonat beszerzésére használható anélkül, hogy a felhasználót újra be kellene jelentkeznie. Ha a frissítési jogkivonat lejár, az alkalmazásnak újra újra interaktívan hitelesítenie kell a felhasználót.

> [!NOTE]
> Az Azure AD által kiadott frissítési jogkivonat több erőforrás eléréséhez használható. Például ha rendelkezik egy ügyfélalkalmazás, amely rendelkezik engedéllyel, hogy hívja a két webes API-k, a frissítési jogkivonat ot lehet használni, hogy egy hozzáférési jogkivonatot a másik webes API-t is.

## <a name="code-samples"></a>Kódminták

Tekintse meg a natív alkalmazás-web API-forgatókönyvek kódmintáit. És, nézz vissza gyakran - adunk új mintákat gyakran. [Natív alkalmazás a webes API-hoz](sample-v1-code.md#desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api).

## <a name="app-registration"></a>Alkalmazásregisztráció

Ha regisztrál egy alkalmazást az Azure AD v1.0-s végpontjával, olvassa [el az Alkalmazás regisztrálása című témakört.](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)

* Egyetlen bérlő – mind a natív alkalmazás, mind a webes API-t ugyanabban a címtárban kell regisztrálni az Azure AD-ben. A webes API beállítható úgy, hogy engedélyeket hozzon létre, amelyek a natív alkalmazás erőforrásaihoz való hozzáférésének korlátozására szolgálnak. Az ügyfélalkalmazás ezután kiválasztja a kívánt engedélyeket az Azure Portal "Engedélyek más alkalmazásokhoz" legördülő menüből.
* Több-bérlős – Először is, a natív alkalmazás csak valaha regisztrált a fejlesztő vagy a közzétevő könyvtárában. Másodszor, a natív alkalmazás úgy van beállítva, hogy jelezze a működési engedélyeket. A szükséges engedélyek listája egy párbeszédablakban jelenik meg, amikor a célkönyvtárban lévő felhasználó vagy rendszergazda hozzájárul az alkalmazáshoz, amely elérhetővé teszi azt a szervezet számára. Egyes alkalmazások csak felhasználói szintű engedélyeket igényelnek, amelyekhez a szervezet bármely felhasználója beleegyezhet. Más alkalmazások rendszergazdai szintű engedélyeket igényelnek, amelyekhez a szervezet felhasználója nem járulhat hozzá. Csak a címtár-rendszergazda adhat beleegyezést az ilyen szintű engedélyeket igénylő alkalmazásokhoz. Ha a felhasználó vagy a rendszergazda hozzájárul, csak a webes API van regisztrálva a címtárban. 

## <a name="token-expiration"></a>Token lejárata

Ha a natív alkalmazás az engedélyezési kódot használja egy JWT-hozzáférési jogkivonat lekéréséhez, jwt-frissítési jogkivonatot is kap. Amikor a hozzáférési jogkivonat lejár, a frissítési jogkivonat segítségével újra hitelesíteni a felhasználót anélkül, hogy újra be kellene jelentkeznie. Ezt a frissítési jogkivonatot ezután a felhasználó hitelesítésére használják, ami egy új hozzáférési jogkivonatot és frissítési jogkivonatot eredményez.

## <a name="next-steps"></a>További lépések

- További információ az [egyéb alkalmazástípusokról és -forgatókönyvekről](app-types.md)
- Ismerje meg az Azure [AD-hitelesítés alapjait](v1-authentication-scenarios.md)
