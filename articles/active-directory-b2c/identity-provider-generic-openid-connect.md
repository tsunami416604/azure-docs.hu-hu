---
title: Regisztráció és bejelentkezés beállítása az OpenID Connect segítségével
titleSuffix: Azure AD B2C
description: Az Azure Active Directory B2C bármely OpenID Connect identitásszolgáltatójával (IdP) beállíthat regisztrációt és bejelentkezést.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c72abf79f0a420309ebe229673be9439fd99b74c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188256"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>Regisztráció és bejelentkezés beállítása az OpenID Connect segítségével az Azure Active Directory B2C használatával

[Az OpenID Connect](openid-connect.md) az OAuth 2.0-s rendszerre épülő hitelesítési protokoll, amely biztonságos felhasználói bejelentkezéshez használható. A protokollt használó legtöbb identitásszolgáltatót támogatja az Azure AD B2C. Ebből a cikkből megtudhatja, hogyan vehet fel egyéni OpenID Connect identitásszolgáltatókat a felhasználói folyamatokba.

## <a name="add-the-identity-provider"></a>Az identitásszolgáltató hozzáadása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja, kattintson a felső menü **Directory + előfizetésszűrőre,** és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza **az Identitásszolgáltatók**lehetőséget, majd az **Új OpenID Connect szolgáltató**lehetőséget.

## <a name="configure-the-identity-provider"></a>Az identitásszolgáltató konfigurálása

Minden OpenID Connect identitásszolgáltató olyan metaadat-dokumentumot ír le, amely a bejelentkezéshez szükséges legtöbb információt tartalmazza. Ez olyan információkat is tartalmaz, mint például a használandó URL-címek és a szolgáltatás nyilvános aláíró kulcsainak helye. Az OpenID Connect metaadat-dokumentum mindig a végződésű végponton `.well-known\openid-configuration`található. A hozzáadni kívánt OpenID Connect identitásszolgáltató nak adja meg a metaadat-URL-címét.

## <a name="client-id-and-secret"></a>Ügyfélazonosító és titkos titok

Ahhoz, hogy a felhasználók bejelentkezhessenek, az identitásszolgáltató megköveteli a fejlesztőktől, hogy regisztráljanak egy alkalmazást a szolgáltatásukban. Az alkalmazás azonosítója az **ügyfélazonosító** és az **ügyféltitok.** Másolja ezeket az értékeket az identitásszolgáltatóból, és adja meg azokat a megfelelő mezőkbe.

> [!NOTE]
> Az ügyféltitkos kulcsot nem lehet kibékülve. Azonban meg kell adnia egy ügyféltitkos kulcsot, ha az [engedélyezési kód folyamatát](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)szeretné használni, amely a titkos kulcsot használja a kód tokenre való cseréjéhez.

## <a name="scope"></a>Hatókör

A hatókör határozza meg az egyéni identitásszolgáltatótól begyűjteni kívánt információkat és engedélyeket. Az OpenID Connect `openid` kérelmeknek tartalmazniuk kell a hatókör értékét ahhoz, hogy az azonosító tokent az identitásszolgáltatótól kaphassák. Az azonosító jogkivonat nélkül a felhasználók nem tudnak bejelentkezni az Azure AD B2C-be az egyéni identitásszolgáltató használatával. Más hatókörök is hozzáfűzhetők térben elválasztva. Tekintse meg az egyéni identitásszolgáltató dokumentációját, hogy milyen más hatókörök érhetők el.

## <a name="response-type"></a>Válasz típusa

A választípus leírja, hogy milyen típusú információkat küld `authorization_endpoint` vissza az egyéni identitásszolgáltató nak szóló első hívásban. A következő választípusok használhatók:

* `code`: Az [engedélyezési kód folyamata](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)szerint egy kódot vissza ad vissza az Azure AD B2C.: Per per the authorization code flow, a code will be returned returned to Azure AD B2C. Az Azure AD B2C `token_endpoint` folytatja a jogkivonat cseréjéhez a kód hívása.
* `id_token`: Egy azonosító jogkivonat ot visszaaz Azure AD B2C az egyéni identitásszolgáltató.

## <a name="response-mode"></a>Válasz mód

A válaszmód határozza meg azt a módszert, amelyet az adatok nak az egyéni identitásszolgáltatótól az Azure AD B2C-nek való visszaküldéséhez kell használni. A következő válaszmódok használhatók:

* `form_post`: Ez a válaszmód a legjobb biztonság érdekében ajánlott. A válasz a HTTP-módszerrel `POST` történik, a kód vagy a token `application/x-www-form-urlencoded` kódolása a törzsben a formátum használatával történik.
* `query`: A kód vagy jogkivonat lekérdezési paraméterként kerül visszaadásra.

## <a name="domain-hint"></a>Tartomány tipp

A tartománytipp segítségével közvetlenül a megadott identitásszolgáltató bejelentkezési oldalára ugorhat, ahelyett, hogy a felhasználó nak ki kellene választania az elérhető identitásszolgáltatók listájából. Az ilyen viselkedés engedélyezéséhez adjon meg egy értéket a tartománytipphez. Az egyéni identitásszolgáltatóra való ugráshoz `domain_hint=<domain hint value>` fűzze hozzá a paramétert a kérés végéhez, amikor az Azure AD B2C-t hívja a bejelentkezéshez.

## <a name="claims-mapping"></a>Jogcímek leképezése

Miután az egyéni identitásszolgáltató egy azonosító jogkivonatot küld vissza az Azure AD B2C-nek, az Azure AD B2C-nek képesnek kell lennie a kapott jogkivonat jogcímének hozzárendelésére az Azure AD B2C által felismert és használt jogcímekhez. Az alábbi leképezések mindegyikéhez tekintse meg az egyéni identitásszolgáltató dokumentációját az identitásszolgáltató jogkivonataiban visszaadott jogcímek megértéséhez:

* **Felhasználói azonosító**: Adja meg azt a jogcímet, amely a bejelentkezett felhasználó *egyedi azonosítóját* adja meg.
* **Megjelenítendő név**: Adja meg azt a jogcímet, amely megadja a felhasználó *megjelenítendő vagy* *teljes nevét.*
* **Megadott név**: Adja meg a felhasználó *utónevét* megadó jogcímet.
* **Vezetéknév**: Adja meg a felhasználó *vezetéknevét* megadó jogcímet.
* **E-mail**: Adja meg a felhasználó *e-mail címét* megadó jogcímet.
