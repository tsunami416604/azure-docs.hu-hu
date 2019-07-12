---
title: Válassza ki a megfelelő összevonási protokollt a több-bérlős alkalmazás
description: Független szoftverszállítók, Azure Active Directoryval való integrálását ismertető útmutató
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53b315b87200b37cda215a29a65be9babaf54f43
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795199"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>Válassza ki a megfelelő összevonási protokollt a több-bérlős alkalmazás

Amikor a szoftver (saas biztosított) alkalmazás fejlesztése, ki kell választania az összevonási protokoll, amely a leginkább megfelel az Ön és ügyfelei igényeinek. Ezt a döntést a fejlesztési platform, és a el kívánja integrálható, így az ügyfelek az Office 365 és az Azure AD-ökoszisztémán belül rendelkezésre álló adatok alapján.

A teljes listáját lásd: [protokollok integrációja egyszeri Bejelentkezéssel elérhető](what-is-single-sign-on.md) az Azure Active Directoryval.
A következő táblázat összehasonlítja 
* Nyílt hitelesítés 2.0 (OAuth 2.0-s)
* Open ID Connect (OIDC)
* Security Assertion Markup Language (SAML)
* Web Services Federation (WSFed)

| Képesség| OAuth / OIDC| SAML / WSFed |
| - |-|-|
| Webes egyszeri bejelentkezés| √| √ |
| Webes egyszeri kijelentkezéshez| √| √ |
| Mobileszköz-alapú egyszeri bejelentkezés| √| √* |
| Mobileszköz-alapú egyszeri kijelentkezéshez| √| √* |
| Feltételes hozzáférési szabályzatok a mobilalkalmazásokhoz| √| X |
| Mobilalkalmazások zökkenőmentes többtényezős hitelesítés| √| X |
| Access Microsoft Graph| √| X |

\* Lehetséges, de a Microsoft nem biztosít, minták és útmutatást.

## <a name="oauth-20-and-open-id-connect"></a>Az OAuth 2.0 és az Open ID Connect

OAuth 2.0-s- [szabványos](https://oauth.net/2/) protokollt használ a hitelesítéshez. OIDC (OpenID Connect) van egy [iparági szabványnak számító](https://openid.net/connect/) az Oath-2.0 protokoll-ra épülő hitelesítést identitásrétegének.

### <a name="benefits"></a>Előnyök

A Microsoft azt javasolja, hitelesítési és engedélyezési protokollok beépített rendelkeznek OIDC/OAuth 2.0-val. Az SAML továbbá meg kell valósítania engedélyezési.

Ezeket a protokollokat rejlő engedélyezési lehetővé teszi, hogy az alkalmazás eléréséhez, és integrálható gazdag felhasználói és szervezeti adatait a Microsoft Graph API-n keresztül.

OAuth 2.0 és OIDC használatával egyszerűbbé teszi az ügyfelek végfelhasználói élmény, az alkalmazás egyszeri bejelentkezési elfogadásakor. Egyszerűen megadhatja a engedély beállítása szükséges, amely ezután automatikusan szerepelnek a rendszergazda vagy a végfelhasználó jóváhagyják.

Ezek a protokollok használatával ezenkívül lehetővé teszi a ügyfelei számára, hogy a feltételes hozzáférés és az MFA-szabályzatok segítségével szabályozhatja az alkalmazásokhoz való hozzáférés. A Microsoft biztosít kódtárak és [Kódminták technológia több platformon](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) , ezzel elősegítve a fejlesztést.  

### <a name="implementation"></a>Megvalósítás

A Microsoft Identity, amely az OAuth 2.0-s szolgáltató regisztrálhatja alkalmazását. Sikerült majd regisztrálását is az OAuth 2.0-alapú alkalmazás bármilyen más identitásszolgáltatóval, akinél szeretné integrálni. 

Az alkalmazás regisztrálásához és megvalósítani a ezeket a protokollokat az SSO web Apps információkért lásd: [OpenID Connect és az Azure Active Directory használatával webes alkalmazásokhoz való hozzáférés engedélyezése](../develop/sample-v2-code.md).  Hogyan lehet megvalósítani a ezeket a protokollokat az SSO-mobilalkalmazásokban, a következő információt: 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [Univerzális Windows-platform](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>SAML 2.0 és WSFed

Webes alkalmazások általában szolgál a Security Assertion Markup Language (SAML). Lásd: [Azure használja a SAML-protokoll](../develop/active-directory-saml-protocol-reference.md) áttekintését. 

Web Services Federation (WSFed) van egy [iparági szabványnak számító](http://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) általánosan használt webes alkalmazásokhoz, amelyek a .net-platformról lettek kifejlesztve.

### <a name="benefits"></a>Előnyök

SAML 2.0 egy érett standard és a legtöbb technológia platformok támogatja nyílt forráskódú könyvtáraink SAML 2.0-hoz készült. Az ügyfelek felügyeleti felületet SAML egyszeri bejelentkezést biztosíthat. SAML SSO konfigurálhatják a Microsoft Azure ad-ben, és bármely más identitásszolgáltató, amely támogatja az SAML-2

### <a name="trade-offs"></a>Kompromisszummal

Mobil alkalmazások SAML 2.0 vagy WSFed protokollt használja, ha egyes feltételes hozzáférési szabályzatokat, beleértve a multi-factor Authentication (MFA) lesz csökkentett teljesítményű élményt. Emellett ha szeretne hozzáférni a Microsoft Graph, szüksége lesz megvalósításához szükséges jogkivonatokat hoz létre keresztül az OAuth 2.0 engedélyezési. 

### <a name="implementation"></a>Megvalósítás

A Microsoft nem kódtárakat biztosít SAML végrehajtásához, és adott könyvtárakat javasoljuk. Nincsenek elérhető számos nyílt forráskódú kódtár.

## <a name="sso-and-using-microsoft-graph-rest-api"></a>Egyszeri bejelentkezés és a Microsoft Graph használatával Rest API 

A Microsoft Graph felhőadat-háló összes Microsoft 365, beleértve az Office 365, Windows 10-es és a nagyvállalati mobilitási és biztonsági és további termékek, például a Dynamics 365. Ez magában foglalja a core sémákat az entitások, például a felhasználók, csoportok, naptár, levelek, fájlok és több, a meghajtó felhasználói termelékenység csökkenését. A Microsoft Graph három felületek kínál a fejlesztőknek egy REST-alapú API, Microsoft Graph adatváltozásainak kapcsolódási, és az összekötők, amelyek engedélyezik a fejlesztők a Microsoft Graph adhat hozzá a saját adatait.  

Az SSO használatával a fent említett protokollok lehetővé teszi a Microsoft Graph REST API-n keresztül elérhető részletes adatok az alkalmazás elérését. Ez lehetővé teszi az ügyfeleknek a Microsoft 365-ben a befektetések kihasználása. Például az alkalmazás meghívhatja a Microsoft Graph API integrálható az Office 365-példány az ügyfelek és a felszínre hozni azokat a felhasználókat a Microsoft Office és SharePoint-elemek az alkalmazáson belül. 

Használ Open ID Connect hitelesítést, akkor a fejlesztési környezetet biztosít a lesz zökkenőmentes, mert OAuth2 fogja használni, ha alapját a Open ID Connect, jogkivonatok beszerzésére használható a Microsoft Graph API-k meghívására. Ha az alkalmazás SAML vagy WSFed használja, hozzá kell adnia további programkódokat kellene megtervezni lekérni a jogkivonatokat, a Microsoft Graph API-k meghívása kell beszerezni ezeket OAuth2 az alkalmazáson belül. 

## <a name="next-steps"></a>További lépések

[A több-bérlős alkalmazás egyszeri bejelentkezés engedélyezése](isv-sso-content.md)

[Dokumentáció a több-bérlős alkalmazás létrehozása](isv-create-sso-documentation.md)
