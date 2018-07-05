---
title: OpenID Connect Identitásszolgáltatók hozzáadása a beépített szabályzatokat az Azure Active Directory B2C |} A Microsoft Docs
description: Az OpenID Connect-szolgáltató hozzáadása az Azure AD B2C-beépített szabályzatait útmutató áttekintése.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/27/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e5baf95cd2426c9753620cba7c5a67b4c1672788
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444225"
---
# <a name="azure-active-directory-b2c-add-a-custom-openid-connect-identity-provider-in-built-in-policies"></a>Az Azure Active Directory B2C: Egyéni OpenID Connect-identitásszolgáltató hozzáadása a beépített szabályzatok

>[!NOTE]
> Ez a funkció jelenleg nyilvános előzetes verzióban. Az éles környezetben ne használja a szolgáltatást.

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) , amelyek segítségével biztonságosan bejelentkezhetnek a felhasználók OAuth 2.0-ra épülő, hitelesítési protokoll. A legtöbb Identitásszolgáltatók, például ezt a protokollt használó [Azure ad-ben](active-directory-b2c-setup-oidc-azure-active-directory.md), az Azure AD B2C-ben támogatottak. Ez a cikk bemutatja, hogyan adhat hozzá egyéni OpenID Connect Identitásszolgáltatók, a beépített szabályzatokat.

## <a name="configuring-a-custom-openid-connect-identity-provider"></a>Egyéni OpenID Connect-identitásszolgáltató konfigurálása

Egyéni OpenID Connect-identitásszolgáltató hozzáadása:

1. Az alábbi lépéseket követve [keresse meg az Azure AD B2C-beállítások](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) az Azure Portalon.
1. Kattintson a **Identitásszolgáltatók**.
1. Kattintson a **Hozzáadás** gombra.
1. Az a **identitásszolgáltató típusa**válassza **OpenID Connect**.

### <a name="setting-up-the-openid-connect-identity-provider"></a>Az OpenID Connect identitásszolgáltató beállítása

#### <a name="metadata-url"></a>Metaadatok URL-címe

-Specifikáció alapján minden OpenID Connect Identitásszolgáltatók ismerteti egy metaadat-dokumentum, amely tartalmazza a legtöbb bejelentkezési végrehajtásához szükséges adatokat. Ez magában foglalja az adatokat, például a használandó URL-címek és a szolgáltatás nyilvános aláírási kulcsok helyét. Az OpenID Connect metaadat-dokumentumokról mindig egy végpontot, amely a következő helyen található `.well-known\openid-configuration`.

Az OpenID Connect identitásszolgáltató szeretne hozzáadni, adja meg a metaadatok URL-címe.

#### <a name="client-id-and-secret"></a>Ügyfél-azonosítója és kulcsa

Az identitásszolgáltató szükséges ahhoz, hogy a felhasználók jelentkezhetnek be, fejlesztők számára, hogy egy alkalmazás regisztrálása a szolgáltatásban. Az alkalmazás Azonosítóját kell (a továbbiakban a **ügyfél-azonosító**) és a egy **titkos Ügyfélkód**. Másolja ki ezeket az értékeket az identitásszolgáltató, és adja meg azokat a megfelelő mezőkbe.

> [!NOTE]
> A titkos ügyfélkulcsot nem kötelező. Azonban meg kell adnia egy ügyfélkulcsot Ha használni szeretné a [hitelesítési kódfolyamat](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), amely a titkos kulcs használatával a kód a jogkivonat exchange.

#### <a name="scope"></a>Hatókör

Hatókörök meghatározása az adatokat és engedélyeket szeretne gyűjteni, az egyéni identitásszolgáltató. OpenID Connect kérelmek tartalmaznia kell a `openid` hatókör értéke az identitásszolgáltatótól az azonosító jogkivonat fogadásához. Az azonosító jogkivonat nélküli felhasználók nem fognak tudni bejelentkezni az Azure AD B2C-t az egyéni identitásszolgáltató használatával.

Más hatókörök (szóközökkel elválasztott) lehet csatolni. Az egyéni identitásszolgáltató dokumentációban megtekintheti, hogy más hatókörök lehet érhető el.

#### <a name="response-type"></a>Válasz típusa

Válasz típusa ismerteti, milyen típusú információkat küld vissza a kezdeti hívása a `authorization_endpoint` az egyéni identitásszolgáltató. 

* `code`: Forgalomért a [hitelesítési kódfolyamat](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), vissza az Azure AD B2C-vel visszaad egy kódot. Az Azure AD B2C majd folytatódik hívja a `token_endpoint` a kódot a jogkivonat segítségével.
* `token`: Egy hozzáférési jogkivonatot az egyéni identitásszolgáltató vissza az Azure AD B2C-vel visszaad.
* `id_token`: Egy azonosító tokent az egyéni identitásszolgáltató vissza az Azure AD B2C-t ad vissza.


#### <a name="response-mode"></a>Válasz módja

A válasz módja határozza meg, hogy elküldi az adatokat az egyéni identitásszolgáltató vissza az Azure AD B2C-vel használandó módszert.

* `form_post`: Válasz Ez az üzemmód ajánlott legjobb biztonsági. A válasz a HTTP-n keresztül továbbított `POST` metódus a kódot vagy a jogkivonatot a törzsében található kódolása a `application/x-www-form-urlencoded` formátumban.
* `query`: A kód vagy a tokenen küldi vissza a rendszer egy lekérdezési paraméter.


#### <a name="domain-hint"></a>Tartományemlékeztető

Közvetlenül a bejelentkezési oldalon a megadott identitásszolgáltató nem a felhasználó válasszon között elérhető identitás-szolgáltatóktól, ugorjon a tartomány mutató használható. Ahhoz, hogy az ilyen viselkedést, adja meg a tartomány-mutató értéket.

Az egyéni identitásszolgáltató ugorhat, fűzze hozzá a paraméter `domain_hint=<domain hint value>` a kérelmet, jelentkezzen be az Azure AD B2C-vel hívásakor végéhez.


### <a name="mapping-the-claims-from-the-openid-connect-identity-provider"></a>A jogcímek, az OpenID Connect identitásszolgáltató leképezése

Után az egyéni identitás a szolgáltató egy azonosító jogkivonat küld vissza az Azure AD B2C-ben, hogy képes legyen leképezni a jogcímeket a kapott jogkivonat a jogcímek, az Azure AD B2C-vel felismeri és használja az Azure AD B2C-vel igényeinek. 

Az alábbi leképezések mindegyike esetében tekintse meg az egyéni identitásszolgáltató tudni, hogy a jogcímek, az identitásszolgáltató jogkivonatok vissza a visszaadott a dokumentációt.

* `User ID`: Adja meg a jogcímet, amely biztosítja a bejelentkezett felhasználó egyedi azonosítója.
* `Display Name`: A jogcímet, amely biztosítja a megjelenítendő név vagy a felhasználó teljes nevét adja meg.
* `Given Name`: A jogcímet, amely biztosítja a felhasználó utónevét adja meg.
* `Surname`: A jogcímet, amely biztosítja a felhasználó vezetéknevét adja meg.
* `Email`: A jogcímet, amely biztosítja a felhasználó e-mail-címét adja meg.

## <a name="next-steps"></a>További lépések

Adja hozzá az egyéni OpenID Connect identitásszolgáltató a [beépített szabályzat](active-directory-b2c-reference-policies.md).
