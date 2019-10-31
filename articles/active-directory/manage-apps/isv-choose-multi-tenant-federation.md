---
title: Válassza ki a megfelelő összevonási protokollt a több-bérlős alkalmazáshoz
description: Útmutató független szoftvergyártók számára a Azure Active Directory integrálásához
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
ms.openlocfilehash: ede458e7d4c1cb1a8d7e3f2e2c9df54d5925d6d8
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175982"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>Válassza ki a megfelelő összevonási protokollt a több-bérlős alkalmazáshoz

A szolgáltatott szoftver (SaaS) alkalmazás fejlesztésekor ki kell választania azt az összevonási protokollt, amely a legjobban megfelel az Ön és az ügyfelek igényeinek. Ez a döntés a fejlesztési platformon alapul, és az ügyfelek Office 365-és Azure AD-ökoszisztémájában elérhető adatszolgáltatásokkal való integrációt kívánja elérni.

Tekintse [meg az egyszeri bejelentkezéses integrációhoz elérhető protokollok](what-is-single-sign-on.md) teljes listáját Azure Active Directory.
A következő táblázat összehasonlítja a 
* Nyílt hitelesítés 2,0 (OAuth 2,0)
* Open ID-kapcsolat (OIDC)
* Security Assertion Markup Language (SAML)
* Web Services-összevonás (WSFed)

| Szolgáltatás| OAuth/OIDC| SAML/WSFed |
| - |-|-|
| Webalapú egyszeri bejelentkezés| √| √ |
| Webalapú egyszeri kijelentkezés| √| √ |
| Mobile-alapú egyszeri bejelentkezés| √| √ |
| Mobile-alapú egyszeri kijelentkezés| √| √ |
| A mobil alkalmazások feltételes hozzáférési szabályzatai| √| X |
| Zökkenőmentes MFA-élmény a Mobile Applications szolgáltatáshoz| √| X |
| Hozzáférés Microsoft Graph| √| X |

\* Lehetséges, de a Microsoft nem ad meg mintákat vagy útmutatást.

## <a name="oauth-20-and-open-id-connect"></a>OAuth 2,0 és Open ID-kapcsolat

A OAuth 2,0 egy [iparági szabványnak](https://oauth.net/2/) megfelelő protokoll az engedélyezéshez. A OIDC (OpenID Connect) egy [iparági szabványnak](https://openid.net/connect/) megfelelő identitás-hitelesítési réteg, amely az 2,0 protokollra épül.

### <a name="benefits"></a>Előnyök

A Microsoft azt javasolja, hogy a OIDC/OAuth 2,0-et használja, mivel azok hitelesítéssel és engedélyezéssel rendelkeznek a protokollok számára. Az SAML használatával emellett az engedélyezést is végre kell hajtania.

Az ezekben a protokollokban rejlő engedély lehetővé teszi, hogy az alkalmazás a Microsoft Graph API-n keresztül hozzáférjen és integrálható legyen a gazdag felhasználói és szervezeti adataival.

A OAuth 2,0 és a OIDC használata leegyszerűsíti az ügyfelek végfelhasználói élményét az alkalmazáshoz tartozó egyszeri bejelentkezés bevezetésekor. Egyszerűen meghatározhatja a szükséges engedélyeket, amelyeket a rendszer automatikusan a rendszergazda vagy a végfelhasználó beleegyezésével jelöl.

Ezen protokollok használata lehetővé teszi, hogy az ügyfelek feltételes hozzáférési és MFA-szabályzatokat használjanak az alkalmazásokhoz való hozzáférés szabályozásához. A Microsoft [több technológiai platformon](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) biztosít könyvtárakat és kódrészleteket a fejlesztés támogatásához.  

### <a name="implementation"></a>Megvalósítás

Az alkalmazás regisztrálása a Microsoft Identity szolgáltatásban történik, amely egy OAuth 2,0-szolgáltató. Ezután regisztrálhat a OAuth 2,0-alapú alkalmazást bármely más, a-nal integrálni kívánt identitás-szolgáltatóval. 

További információ az alkalmazás regisztrálásáról és a protokollok az egyszeri bejelentkezésről a Web Apps szolgáltatásban történő megvalósításáról: [hozzáférés engedélyezése webalkalmazásokhoz OpenID Connect és Azure Active Directory használatával](../develop/sample-v2-code.md).  A protokollok a Mobile apps szolgáltatásban való bevezetésével kapcsolatos további információkért tekintse meg a következőket: 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [Univerzális Windows-platform](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>SAML 2,0 és WSFed

A Security Assertion Markup Language (SAML) általában webes alkalmazásokhoz használatos. Tekintse meg, [hogyan használja az Azure az SAML protokollt](../develop/active-directory-saml-protocol-reference.md) az áttekintéshez. 

A webszolgáltatások összevonása (WSFed) a .NET platformmal kifejlesztett webalkalmazásokhoz általánosan használt [iparági szabvány](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) .

### <a name="benefits"></a>Előnyök

Az SAML 2,0 egy érett standard és a legtöbb technológiai platform, amely az SAML 2,0 nyílt forráskódú kódtárait támogatja. Az SAML SSO konfigurálásához adminisztrációs felületet biztosíthat ügyfeleinek. Konfigurálhatja az SAML SSO-t a Microsoft Azure ADhoz és bármely más, az SAML 2-et támogató identitás-szolgáltatóhoz.

### <a name="trade-offs"></a>Kompromisszumok

Ha SAML 2,0-vagy WSFed-protokollt használ a mobileszközök számára, bizonyos feltételes hozzáférési szabályzatok, többek között a többtényezős hitelesítés (MFA), csökkentett teljesítményű élményt nyújtanak. Emellett, ha el szeretné érni a Microsoft Graph, a szükséges tokenek létrehozásához a OAuth 2,0-es kiadást kell végrehajtania. 

### <a name="implementation"></a>Megvalósítás

A Microsoft nem biztosít könyvtárakat az SAML megvalósításához, vagy konkrét könyvtárakat javasol. Számos nyílt forráskódú függvénytár érhető el.

## <a name="sso-and-using-microsoft-graph-rest-api"></a>Egyszeri bejelentkezés és Microsoft Graph REST API használata 

Microsoft Graph az adatháló az összes Microsoft 365, beleértve az Office 365, a Windows 10 és a nagyvállalati mobilitást és biztonságot, valamint további termékek, például a Dynamics 365. Ez magában foglalja az entitások alapvető sémáit, például a felhasználókat, a csoportokat, a naptárat, a levelezést, a fájlokat és egyebeket, amelyek a felhasználók termelékenységét segítik. A Microsoft Graph három felületet biztosít a fejlesztők számára REST-alapú API-k, Microsoft Graph adatkapcsolatok és összekötők számára, amelyek lehetővé teszik a fejlesztők számára a saját adatok hozzáadását a Microsoft Graph.  

Az egyszeri bejelentkezéshez a fenti protokollok bármelyikének használata lehetővé teszi, hogy az alkalmazás hozzáférjen a Microsoft Graph REST APIon keresztül elérhető gazdag adatmennyiségekhez. Ez lehetővé teszi, hogy az ügyfelek nagyobb értéket kapjanak a befektetésük Microsoft 365. Az alkalmazás például meghívja a Microsoft Graph API-t, hogy integrálható legyen az ügyfelek Office 365-példányával és a felszíni felhasználók Microsoft Office és a SharePoint-elemekkel az alkalmazáson belül. 

Ha Open ID-csatlakozást használ a hitelesítéshez, akkor a fejlesztési élmény zökkenőmentes, mivel a OAuth2-t, a nyitott AZONOSÍTÓk összekapcsolásának alapját fogja használni a tokenek beszerzéséhez Microsoft Graph API-k meghívásához. Ha az alkalmazás SAML-vagy WSFed-t használ, az alkalmazásban további kódokat kell hozzáadnia ahhoz, hogy a OAuth2 megszerezze a Microsoft Graph API-k meghívásához szükséges jogkivonatokat. 

## <a name="next-steps"></a>Következő lépések

[Egyszeri bejelentkezés engedélyezése a több-bérlős alkalmazáshoz](isv-sso-content.md)

[Dokumentáció létrehozása a több-bérlős alkalmazáshoz](isv-create-sso-documentation.md)
