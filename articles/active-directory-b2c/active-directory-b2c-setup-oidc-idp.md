---
title: Regisztráció és bejelentkezés beállítása OpenID Connect-Azure Active Directory B2C
description: Az OpenID Connecttel való regisztráció és bejelentkezés beállítása Azure Active Directory B2C használatával.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 720deb28ce124af23035337ac88cfb1d37fc7c53
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69509689"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>Az OpenID Connecttel való regisztráció és bejelentkezés beállítása Azure Active Directory B2C használatával

Az [OpenID Connect](active-directory-b2c-reference-oidc.md) egy OAuth 2,0-re épülő hitelesítési protokoll, amely biztonságos felhasználói bejelentkezéshez használható. A protokollt használó legtöbb identitás-szolgáltató Azure AD B2C támogatott. Ez a cikk azt ismerteti, hogyan adhat hozzá egyéni OpenID Connect-szolgáltatókat a felhasználói folyamatokhoz.

## <a name="add-the-identity-provider"></a>Az identitás-szolgáltató hozzáadása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
4. Válassza az **identitás-szolgáltatók**lehetőséget, majd kattintson a **Hozzáadás**gombra.
5. Az **identitás-szolgáltató típusa**beállításnál válassza az **OpenID Connect**elemet.

## <a name="configure-the-identity-provider"></a>Az identitás-szolgáltató konfigurálása

Az OpenID Connect-identitások minden szolgáltatója olyan metaadat-dokumentumot ír le, amely a bejelentkezéshez szükséges információk többségét tartalmazza. Ide tartozik például a használni kívánt URL-címek és a szolgáltatás nyilvános aláíró kulcsainak helye. Az OpenID Connect metaadat-dokumentum mindig olyan végponton található, amely a `.well-known\openid-configuration`-ben végződik. A hozzáadni kívánt OpenID Connect-szolgáltatóhoz adja meg a metaadatok URL-címét.

Annak engedélyezéséhez, hogy a felhasználók bejelentkezzenek, az identitás-szolgáltató megköveteli a fejlesztőknek, hogy regisztráljanak egy alkalmazást a szolgáltatásban. Az alkalmazáshoz tartozik egy **ügyfél-azonosító** és egy **ügyfél-titok**néven ismert azonosító. Másolja ezeket az értékeket az identitás-szolgáltatóból, és írja be őket a megfelelő mezőkbe.

> [!NOTE]
> Az ügyfél titkos kulcsa nem kötelező. Ha azonban használni szeretné az [engedélyezési kód folyamatát](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), meg kell adnia egy ügyfél titkos kulcsát, amely a titkos kódot használja a jogkivonat kódjának cseréjéhez.

A hatókör határozza meg az egyéni identitás-szolgáltatótól összegyűjtött információkat és engedélyeket. Az OpenID Connect-kérelmeknek `openid` tartalmaznia kell a hatókör értékét, hogy az azonosító jogkivonatot megkaphassa az identitás-szolgáltatótól. Az azonosító jogkivonat nélkül a felhasználók nem tudnak bejelentkezni a Azure AD B2Cra az egyéni identitás-szolgáltató használatával. Más hatóköröket szóközzel lehet elválasztani egymástól. Tekintse át az egyéni identitás szolgáltatójának dokumentációját, amelyből megtudhatja, hogy milyen egyéb hatókörök is elérhetők.

A válasz típusa azt írja le, hogy `authorization_endpoint` milyen típusú információkat küld vissza a rendszer az egyéni identitás-szolgáltató kezdeti hívását. A következő típusú válaszokat lehet használni:

- `code`: Az [engedélyezési kód folyamatábrája](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)alapján a rendszer visszaadja a kódot Azure ad B2C. Azure ad B2C folytatja a meghívását `token_endpoint` a tokenhez tartozó kód cseréjére.
- `token`: A rendszer visszaadja a hozzáférési jogkivonatot az egyéni identitás-szolgáltató Azure AD B2C.
- `id_token`: A rendszer visszaadja az azonosító jogkivonatot az egyéni identitás-szolgáltató Azure AD B2C.

A válasz mód határozza meg azt a módszert, amelyet az adatoknak az egyéni identitás szolgáltatótól a Azure AD B2Cba való visszaküldéséhez kell használni. A következő módokat használhatja:

- `form_post`: Ez a válasz mód ajánlott a legjobb biztonság érdekében. A válasz továbbítása a http `POST` -metódussal történik, és a kódot vagy a tokent a törzsbe kódolja a `application/x-www-form-urlencoded` formátum használatával.
- `query`: A kód vagy token lekérdezési paraméterként lesz visszaadva.

A tartomány-emlékeztetővel közvetlenül a megadott Identity Provider bejelentkezési lapjára ugorhat, így a felhasználó nem választhat a rendelkezésre álló identitás-szolgáltatók listáján. Az ilyen viselkedés engedélyezéséhez adjon meg egy értéket a tartományhoz. Az egyéni identitás-szolgáltatóra való ugráshoz fűzze hozzá `domain_hint=<domain hint value>` a (z) paramétert a kérés végéhez, amikor meghívja Azure ad B2C a bejelentkezéshez.

Miután az egyéni identitás-szolgáltató visszaküldi az azonosító jogkivonatot a Azure AD B2Cnak, Azure AD B2Cnak képesnek kell lennie a kapott token jogcímeinek hozzárendelésére a Azure AD B2C által felismert és használt jogcímekre. A következő leképezések mindegyike esetében tekintse át az egyéni identitás szolgáltatójának dokumentációját, és Ismerje meg, hogy milyen jogcímeket ad vissza az identitás-szolgáltató jogkivonatában:

- `User ID`: Adja meg a bejelentkezett felhasználó egyedi azonosítóját biztosító jogcímet.
- `Display Name`: Adja meg azt a jogcímet, amely megadja a felhasználó megjelenítendő nevét vagy teljes nevét.
- `Given Name`: Adja meg a felhasználó vezetéknevét biztosító jogcímet.
- `Surname`: Adja meg a felhasználó vezetéknevét biztosító jogcímet.
- `Email`: Adja meg a felhasználó e-mail-címét biztosító jogcímet.

