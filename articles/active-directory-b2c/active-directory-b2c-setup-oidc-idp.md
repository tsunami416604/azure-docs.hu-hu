---
title: Állítsa be regisztráció és bejelentkezés OpenID-kapcsolattal az Azure Active Directory B2C használatával |} A Microsoft Docs
description: Állítsa be regisztráció és bejelentkezés OpenID-kapcsolattal az Azure Active Directory B2C használatával.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e6fc9ded2b3509f9505d88f0ae7ccc790e47b0f2
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52842764"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>Regisztráció és bejelentkezés a OpenID-kapcsolattal az Azure Active Directory B2C beállítása

>[!NOTE]
> Ez a funkció jelenleg nyilvános előzetes verzióban. Az éles környezetben ne használja a szolgáltatást.


[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) , amelyek segítségével biztonságosan bejelentkezhetnek a felhasználók OAuth 2.0-ra épülő, hitelesítési protokoll. A legtöbb Identitásszolgáltatók, például ezt a protokollt használó [Azure ad-ben](active-directory-b2c-setup-oidc-azure-active-directory.md), az Azure AD B2C-ben támogatottak. Ez a cikk bemutatja, hogyan adhat hozzá egyéni OpenID Connect Identitásszolgáltatók be a felhasználói folyamatok.


## <a name="add-the-identity-provider"></a>Identitásszolgáltató hozzáadása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
4. Válassza ki **Identitásszolgáltatók**, és kattintson a **Hozzáadás**.
5. Az a **identitásszolgáltató típusa**válassza **OpenID Connect (előzetes verzió)**.

## <a name="configure-the-identity-provider"></a>Az identitásszolgáltató konfigurálása

Minden OpenID Connect Identitásszolgáltatók ismerteti egy metaadat-dokumentum, amely tartalmazza a legtöbb bejelentkezési végrehajtásához szükséges adatokat. Ez magában foglalja az adatokat, például a használandó URL-címek és a szolgáltatás nyilvános aláírási kulcsok helyét. Az OpenID Connect metaadat-dokumentumokról mindig egy végpontot, amely a következő helyen található `.well-known\openid-configuration`. Az OpenID Connect identitásszolgáltató szeretne hozzáadni, adja meg a metaadatok URL-címe.

Ahhoz, hogy a felhasználók jelentkezhetnek be, az identitásszolgáltató igényel a fejlesztők számára, hogy egy alkalmazás regisztrálása a szolgáltatásban. Ez az alkalmazás-azonosító néven van a **ügyfél-azonosító** és a egy **titkos Ügyfélkód**. Másolja ki ezeket az értékeket az identitásszolgáltató, és adja meg azokat a megfelelő mezőkbe.

> [!NOTE]
> A titkos ügyfélkulcsot nem kötelező. Azonban meg kell adnia egy ügyfélkulcsot Ha használni szeretné a [hitelesítési kódfolyamat](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), amely a titkos kulcs használatával a kód a jogkivonat exchange.

Hatókör definiálja azokat az adatokat és engedélyeket szeretne gyűjteni, az egyéni identitásszolgáltató. OpenID Connect kérelmek tartalmaznia kell a `openid` hatókör értéke az identitásszolgáltatótól az azonosító jogkivonat fogadásához. Az azonosító jogkivonat nélkül munkavégzésük nem tud bejelentkezni az Azure AD B2C-t az egyéni identitásszolgáltató használatával. Más hatókörök szóközökkel elválasztott is bővül. Az egyéni identitásszolgáltató dokumentációban megtekintheti, hogy más hatókörök lehet érhető el.

Válasz típusa ismerteti, milyen típusú információkat küld a kezdeti hívást a `authorization_endpoint` az egyéni identitásszolgáltató. A következő választ típusok használhatók:

- `code`: Forgalomért a [hitelesítési kódfolyamat](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), vissza az Azure AD B2C-vel visszaad egy kódot. Az Azure AD B2C abból hívja a `token_endpoint` Exchange a kódot a jogkivonat.
- `token`: Egy hozzáférési jogkivonatot az egyéni identitásszolgáltató küldte vissza az Azure AD B2C-t.
- `id_token`: Egy azonosító jogkivonat vissza az Azure AD B2C-t küld vissza, az egyéni identitásszolgáltató.

A válasz módja határozza meg, hogy elküldi az adatokat az egyéni identitásszolgáltató vissza az Azure AD B2C-vel használandó módszert. A következő választ mód használható:

- `form_post`: Válasz Ez az üzemmód ajánlott legjobb biztonsági. A válasz a HTTP-n keresztül továbbított `POST` metódus a kódot vagy a jogkivonatot a törzsében található kódolása a `application/x-www-form-urlencoded` formátumban.
- `query`: A kód vagy a tokenen lekérdezési paraméterként adja vissza.

Közvetlenül a bejelentkezési oldalon a megadott identitásszolgáltató nem a felhasználó válasszon között elérhető identitás-szolgáltatóktól, ugorjon a tartomány mutató használható. Ahhoz, hogy az ilyen viselkedést, adja meg a tartomány-mutató értéket. Az egyéni identitásszolgáltató ugorhat, fűzze hozzá a paraméter `domain_hint=<domain hint value>` a kérelmet, jelentkezzen be az Azure AD B2C-vel hívásakor végéhez.

Után az egyéni identitás a szolgáltató egy azonosító jogkivonat küld vissza az Azure AD B2C-ben, hogy képes legyen leképezni a jogcímeket a kapott jogkivonat a jogcímek, az Azure AD B2C-vel felismeri és használja az Azure AD B2C-vel igényeinek. A következő hozzárendeléseket mindegyike esetében tekintse meg az egyéni identitásszolgáltató tudni, hogy a jogcímek, az identitásszolgáltató jogkivonatok vissza a visszaadott dokumentációt:

- `User ID`: Adja meg a jogcímet, amely biztosítja a bejelentkezett felhasználó egyedi azonosítója.
- `Display Name`: A jogcímet, amely biztosítja a megjelenítendő név vagy a felhasználó teljes nevét adja meg.
- `Given Name`: A jogcímet, amely biztosítja a felhasználó utónevét adja meg.
- `Surname`: A jogcímet, amely biztosítja a felhasználó vezetéknevét adja meg.
- `Email`: A jogcímet, amely biztosítja a felhasználó e-mail-címét adja meg.

