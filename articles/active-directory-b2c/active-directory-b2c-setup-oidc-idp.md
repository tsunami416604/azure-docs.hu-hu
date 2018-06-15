---
title: Identitás-szolgáltatóktól OpenID Connect hozzáadása a beépített házirendek az Azure Active Directory B2C |} Microsoft Docs
description: Áttekintés az útmutató az OpenID Connect szolgáltatók hozzáadása a beépített házirendek az Azure AD B2C belül.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/27/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e4325a7c87ac9975e819b22536838ec438fa1bcd
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34709563"
---
# <a name="azure-active-directory-b2c-add-a-custom-openid-connect-identity-provider-in-built-in-policies"></a>Az Azure Active Directory B2C: OpenID Connect identitás egyéni szolgáltatók beépített házirendek is hozzáadhatók.

>[!NOTE]
> A funkció jelenleg nyilvános előzetes verziójához. Üzemi környezetben ne használja a szolgáltatást.

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) egy olyan hitelesítési protokoll az OAuth 2.0, felhasználók biztonságos aláírásához használt platformra épül. A legtöbb Identitásszolgáltatók, mint például a protokollt használó [az Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md), az Azure AD B2C támogatottak. Ez a cikk azt ismerteti, hogyan adhat hozzá egyéni OpenID Connect Identitásszolgáltatók a beépített házirendbe.

## <a name="configuring-a-custom-openid-connect-identity-provider"></a>Egyéni OpenID Connect identitásszolgáltató konfigurálása

Egyéni OpenID Connect identitásszolgáltató hozzáadása:

1. Az alábbi lépéseket követve [navigáljon az Azure AD B2C beállítások](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) az Azure portálon.
1. Kattintson a **identitás-szolgáltatóktól**.
1. Kattintson a **Hozzáadás** gombra.
1. Az a **identitás szolgáltatótípus**, jelölje be **OpenID Connect**.

### <a name="setting-up-the-openid-connect-identity-provider"></a>Az OpenID Connect identitásszolgáltató beállítása

#### <a name="metadata-url"></a>Metaadatok URL-címe

Specifikáció szerint minden OpenID Connect Identitásszolgáltatók, amely tartalmazza a bejelentkezés végrehajtásához szükséges információk többsége metaadat-dokumentum ismerteti. Ide tartoznak például az URL-címek használatára és a szolgáltatás nyilvános aláírási kulcsok helyét. Az OpenID Connect metaadat-dokumentum mindig itt található: egy végpontot, amely `.well-known\openid-configuration`.

Az OpenID Connect identitásszolgáltató számára szeretne hozzáadni, adja meg a metaadatainak URL-CÍMÉT.

#### <a name="client-id-and-secret"></a>Ügyfél-azonosító és a titkos kulcs

Ahhoz, hogy a felhasználók jelentkezhetnek be, az identitásszolgáltató fejlesztők számára, hogy egy alkalmazás regisztrálása a szolgáltatás szükséges. Ez az alkalmazás azonosítója lesz szerepelhet (néven a **ügyfél-azonosító**) és egy **ügyfélkulcs**. Másolja az identitásszolgáltató ezeket az értékeket, és adja meg azokat a megfelelő mezőkbe.

> [!NOTE]
> A titkos ügyfélkulcs nem kötelező megadni. Azonban meg kell adnia egy ügyfélkulcsot Ha használni szeretné a [hitelesítésikód-folyamata](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), melyik a titkos kulcsot használ az exchange-vagy a jogkivonatot a kódot.

#### <a name="scope"></a>Hatókör

Hatókörök meghatározása információkat és engedélyeket szeretne gyűjtse össze az egyéni identitás-szolgáltatótól. Kérelmek OpenID Connect tartalmaznia kell a `openid` érték hatókör az identitásszolgáltató által a Azonosítót jogkivonatban fogadásához. A azonosító token nélkül felhasználók nem fognak tudni bejelentkezni az Azure AD B2C az egyéni identitás-szolgáltató használatával.

Más hatókörök is bővül (vesszővel elválasztva terület). Tekintse meg, milyen más hatókörök előfordulhat, hogy az egyéni identitásszolgáltató dokumentációja elérhető.

#### <a name="response-type"></a>Válasz típusa

A típusú választ ismerteti, hogy milyen típusú adatokat küld vissza a kezdeti hívásában a `authorization_endpoint` az egyéni identitás-szolgáltató. 

* `code`: Mint / a [hitelesítésikód-folyamata](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), vissza az Azure AD B2C visszatér a kódot. Az Azure AD B2C majd folytatódik a hívni a `token_endpoint` az exchange-vagy a jogkivonatot a kódot.
* `token`: Egy hozzáférési jogkivonatot visszatér vissza az Azure AD B2C az egyéni identitásszolgáltató által.
* `id_token`: Az azonosító tokent visszatér vissza az Azure AD B2C az egyéni identitásszolgáltató által.


#### <a name="response-mode"></a>Válasz módja

A válasz módja határozza meg, amellyel küldheti az adatokat újból az egyéni identitásszolgáltató az Azure AD B2C metódus.

* `form_post`: Ez a válasz mód ajánlott legjobb biztonsági. A válasz a HTTP Protokollon keresztül továbbított `POST` metódust használ, a kód vagy a szervezet használatával kódolás jogkivonat a `application/x-www-form-urlencoded` formátumban.
* `query`: A kód vagy a token változatlanul adódik vissza egy lekérdezési paraméter.


#### <a name="domain-hint"></a>Tartományemlékeztető

A tartomány mutató ugorjon közvetlenül a bejelentkezési oldal, a megadott identitásszolgáltató, ahelyett, hogy a felhasználó válasszon között rendelkezésre álló Identitásszolgáltatók használható. Lehetővé teszi az ilyen viselkedést, adjon meg egy értéket a tartomány mutató.

Az egyéni identitásszolgáltató ugráshoz hozzáfűzése a paraméter `domain_hint=<domain hint value>` a kérést, jelentkezzen be az Azure AD B2C meghívásakor végéig.


### <a name="mapping-the-claims-from-the-openid-connect-identity-provider"></a>A jogcímek az OpenID Connect identitásszolgáltató leképezése

Után az egyéni identitás a szolgáltató egy azonosító tokent küld vissza az Azure AD B2C-ben az Azure AD B2C kell lennie az Azure AD B2C felismeri és használja a jogcímeket a fogadott jogkivonatból a jogcímalapú rendelhetnek. 

Az egyes alábbi megfeleltetéseket tekintse meg az egyéni identitás szolgáltató tudni, hogy a jogcímek, az identitásszolgáltató jogkivonatokat a visszaadott dokumentációját.

* `User ID`: Adja meg a jogcímet, amely biztosítja az egyedi azonosító a bejelentkezett felhasználó nevében.
* `Display Name`: Adja meg a jogcímet, amely a megjelenített név vagy a felhasználó teljes nevét.
* `Given Name`: Adja meg a jogcímet, amely a felhasználó utóneve.
* `Surname`: Adja meg a jogcímet, amely a felhasználó vezetékneve.
* `Email`: Adja meg a jogcímet, amely a felhasználó e-mail címe.

## <a name="next-steps"></a>További lépések

Adja hozzá az egyéni OpenID Connect identitásszolgáltató a [beépített házirend](active-directory-b2c-reference-policies.md).
