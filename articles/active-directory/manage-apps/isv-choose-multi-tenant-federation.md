---
title: Válassza ki a megfelelő összevonási protokollt a több-bérlős alkalmazáshoz
description: Útmutató független szoftverszállítóknak az Azure Active Directoryval való integrációhoz
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
ms.openlocfilehash: 3b3edbbe037c3874d639476e516b3732b7573d9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443375"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>Válassza ki a megfelelő összevonási protokollt a több-bérlős alkalmazáshoz

Amikor a szoftvert szolgáltatásként (SaaS) fejleszti, ki kell választania azt az összevonási protokollt, amely a legjobban megfelel az Ön és ügyfelei igényeinek. Ez a döntés a fejlesztési platformon alapul, és az ügyfelek Office 365- és Azure AD-ökoszisztémájában elérhető adatokkal való integrációra.

Tekintse meg az Azure Active Directoryval [való sso-integrációkhoz elérhető protokollok](what-is-single-sign-on.md) teljes listáját.
Az alábbi táblázat 
* Nyílt hitelesítés 2.0 (OAuth 2.0)
* Nyitott id connect (OIDC)
* Biztonsági helyességifeltétel-jelölőnyelv (SAML)
* Webszolgáltatások összevonása (WSFed)

| Képesség| OAuth / OIDC| SAML / WSFed |
| - |-|-|
| Webalapú egyszeri bejelentkezés| √| √ |
| Webalapú egyszeri kijelentkezés| √| √ |
| Mobil alapú egyszeri bejelentkezés| √| √* |
| Mobilalapú egyszeri kijelentkezés| √| √* |
| Feltételes hozzáférési házirendek mobilalkalmazásokhoz| √| X |
| Zökkenőmentes MFA-élmény mobil alkalmazásokhoz| √| X |
| A Microsoft Graph elérése| √| X |

*Lehetséges, de a Microsoft nem ad mintákat vagy útmutatást.

## <a name="oauth-20-and-open-id-connect"></a>OAuth 2.0 és Open ID Connect

Az OAuth 2.0 az [engedélyezés szabványos](https://oauth.net/2/) protokollja. Az OIDC (OpenID Connect) egy [iparági szabványnak megfelelő](https://openid.net/connect/) identitáshitelesítési réteg, amely az OAuth 2.0 protokollra épül.

### <a name="benefits"></a>Előnyök

A Microsoft az OIDC/OAuth 2.0 használatát javasolja, mivel a protokollokba beépített hitelesítéssel és engedélyezéssel rendelkeznek. Az SAML-lel emellett engedélyezési eszközöket is végre kell hajtania.

A protokollokban rejlő engedélyezés lehetővé teszi, hogy az alkalmazás a Microsoft Graph API-n keresztül hozzáférjen a gazdag felhasználói és szervezeti adatokhoz, és integrálódjon azokkal.

Az OAuth 2.0 és az OIDC használata leegyszerűsíti az ügyfelek végfelhasználói élményét az alkalmazás egyszeri sso-jának elfogadásakor. Könnyedén meghatározhatja a szükséges engedélykészleteket, amelyek et automatikusan kitünteti a rendszergazda vagy a végfelhasználó hozzájárulása.

Ezenkívül ezek a protokollok használata lehetővé teszi az ügyfelek számára, hogy feltételes hozzáférés és MFA-házirendek használatával szabályozzák az alkalmazásokhoz való hozzáférést. A Microsoft [könyvtárakat és kódmintákat biztosít több technológiai platformon,](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) hogy segítse a fejlesztést.  

### <a name="implementation"></a>Megvalósítás

Az alkalmazást regisztrálja a Microsoft Identity szolgáltatásban, amely egy OAuth 2.0-s szolgáltató. Ezután regisztrálhatja az OAuth 2.0-alapú alkalmazást bármely más identitásszolgáltatóval, amelyhez integrálni szeretne. 

Az alkalmazás regisztrálásáról és az SSO webalkalmazásokhoz való protokolljainak megvalósításáról az [OpenID Connect és az Azure Active Directory használatával a webalkalmazásokhoz való hozzáférés engedélyezése című](../develop/sample-v2-code.md)témakörben talál további információt.  Az SSO protokolljainak mobilalkalmazásokban történő megvalósításáról az alábbi témakörökben talál további információt: 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [Univerzális Windows-platform](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>SAML 2.0 és WSFed

A biztonsági helyességifeltétel-jelölőnyelvet (SAML) általában webes alkalmazásokhoz használják. Tekintse [meg, hogy az Azure hogyan használja az SAML protokollt](../develop/active-directory-saml-protocol-reference.md) egy áttekintésért. 

A Web Services Federation (WSFed) egy általánosan használt [iparági szabvány](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) a .Net platform használatával kifejlesztett webes alkalmazásokhoz.

### <a name="benefits"></a>Előnyök

Az SAML 2.0 egy kiforrott szabvány, és a legtöbb technológiai platform támogatja az SAML 2.0 nyílt forráskódú kódtárait. Az SAML SSO konfigurálásához felügyeleti felületet biztosíthat ügyfeleinek. Konfigurálhatják az SAML SSO-t a Microsoft Azure AD-hez és bármely más, az SAML 2-t támogató identitásszolgáltatóhoz

### <a name="trade-offs"></a>Kompromisszumok

Ha SAML 2.0 vagy WSFed protokollokat használ mobilalkalmazásokhoz, bizonyos feltételes hozzáférési szabályzatok, beleértve a többtényezős hitelesítést (MFA) is, csökkent szintű élményt élveznek. Továbbá, ha szeretné elérni a Microsoft Graph, a szükséges jogkivonatok létrehozásához végre kell hajtania az OAuth 2.0-s hitelesítésen keresztül. 

### <a name="implementation"></a>Megvalósítás

A Microsoft nem biztosít tárakat az SAML implementációhoz, és nem javasol bizonyos könyvtárakat. Számos nyílt forráskódú könyvtár érhető el.

## <a name="sso-and-using-microsoft-graph-rest-api"></a>SSO és a Microsoft Graph Rest API használata 

A Microsoft Graph az összes Microsoft 365 adathálója, beleértve az Office 365-öt, a Windows 10-et és az Enterprise Mobility and Security-t, valamint további termékeket, például a Dynamics 365-öt. Ez magában foglalja az alapvető sémák az entitások, mint például a felhasználók, csoportok, naptár, mail, fájlok, és több, amelyek a felhasználók termelékenységét. A Microsoft Graph három felületet kínál a fejlesztőknek egy REST-alapú API-t, a Microsoft Graph-adatok összekapcsolását és az összekötőket, amelyek lehetővé teszik a fejlesztők számára, hogy saját adataikat adják hozzá a Microsoft Graph-hoz.  

A fenti protokollok használata az sso lehetővé teszi az alkalmazás hozzáférését a Microsoft Graph REST API-n keresztül elérhető gazdag adatokhoz. Ez lehetővé teszi az ügyfelek számára, hogy nagyobb értéket kapjanak a Microsoft 365-be történő befektetéseikből. Az alkalmazás például meghívhatja a Microsoft Graph API-t, hogy integrálja az ügyfelek Office 365-példányát, valamint a felületfelhasználók Microsoft Office- és SharePoint-elemeit az alkalmazáson belül. 

Ha az Open ID Connect hitelesítéshez használja, akkor a fejlesztési élmény zökkenőmentes, mert az Open ID Connect alapja, az OAuth2 segítségével jogkivonatokat lehet használni a Microsoft Graph API-k meghívásához. Ha az alkalmazás SAML vagy WSFed használatával, további kódot kell hozzáadnia az alkalmazáson belül, hogy ezeket az OAuth2-t beszerezze a Microsoft Graph API-k meghívásához szükséges jogkivonatok beszerzéséhez. 

## <a name="next-steps"></a>Következő lépések

[Az SSO engedélyezése több-bérlős alkalmazások esetében](isv-sso-content.md)

[Dokumentáció létrehozása a több-bérlős alkalmazáshoz](isv-create-sso-documentation.md)
