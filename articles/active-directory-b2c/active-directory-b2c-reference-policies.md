---
title: Beépített szabályzatokat az Azure Active Directory B2C |} A Microsoft Docs
description: A témakör az Azure Active Directory B2C bővíthető házirend-keretrendszer és a különféle házirend létrehozása.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 4a0dfcbba1867d4792a0e4a383ee097df0ea410b
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52835811"
---
# <a name="azure-active-directory-b2c-user-flows"></a>Az Azure Active Directory B2C: Felhasználókövetési adatai


Az Azure Active Directory (Azure AD) B2C bővíthető házirend-keretrendszer a szolgáltatás alapvető erősségét. Például csak a teljes leírása fogyasztói identitások jellemzőit házirendek regisztráció, bejelentkezés vagy profil szerkesztéséhez. Segítséget a leggyakoribb feladatok identitás beállítása, az Azure AD B2C-portálon előre meghatározott, a konfigurálható szabályzatok nevű tartalmaz **felhasználókövetési adatai**. Például egy regisztrációs felhasználói folyamata lehetővé teszi a viselkedés szabályozására, a következő beállítások konfigurálásához:

* Fióktípus esetében (például a Facebookhoz közösségi fiókok) vagy a helyi fiókok, például az e-mail-címeket, amelyek a fogyasztók iratkozzon fel az alkalmazás használatával
* Attribútumok (például utónév, postai irányítószám és cipőméreten) regisztráció során a felhasználóknak gyűjtendő
* Az Azure multi-factor Authentication használata
* Az összes bejelentkezési oldalak megjelenésének és
* Információ (amely jegyzékfájlok egy jogkivonatban jogcímekként), hogy az alkalmazás fogad mikor a felhasználói folyamat befejeződik

Különböző típusú több felhasználói folyamatot létrehozni a bérlőben, és igény szerint használhatja őket az alkalmazásokban. Felhasználói folyamatok alkalmazásokhoz felhasználhatók. Ez rugalmasságot lehetővé teszi a fejlesztők és módosíthatja a fogyasztói identitások jellemzőit, minimális konfigurációval vagy a programkód módosítása nélkül.

Felhasználói folyamatok használata egy egyszerű fejlesztői felületen keresztül érhetők el. Az alkalmazás felhasználói folyamat aktiválása egy szabványos HTTP-hitelesítési kérelmet (a felhasználói folyamat paraméter átadja a kérésben) használatával, és a egy testre szabott token válaszként megkapja. Például az egyetlen kérelmek, amelyek aktiválják az előfizetés-kezelő felhasználói folyamat és a kérelmek, amelyek aktiválják az felhasználói bejelentkezési folyamat közötti különbség a felhasználói interakciósorozat neve, amely a "p" lekérdezésisztring-paraméter szerepel:

```

https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up user flow

```

```

https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in user flow

```

## <a name="create-a-sign-up-or-sign-in-user-flow"></a>Regisztrálási vagy bejelentkezési felhasználói folyamat létrehozása

Ez a felhasználói folyamat mindkét felhasználói regisztrációs és bejelentkezési élmény egyetlen konfigurációval rendelkező kezeli. A fogyasztók le a megfelelő elérési útját (regisztrálási vagy bejelentkezési) függően is vezetett. Bemutatja a jogkivonatokat, amelyeket az alkalmazás fogad sikeres regisztrálásokkor vagy bejelentkezések tartalmát is.  A kódminta a **regisztrálási vagy bejelentkezési** a felhasználói folyamat [elérhető itt](active-directory-b2c-devquickstarts-web-dotnet-susi.md).  Javasoljuk, hogy használja-e a felhasználói folyamat keresztül egy **előfizetési** felhasználói folyamat vagy egy **bejelentkezési** felhasználói folyamatot.  

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

## <a name="create-a-sign-up-user-flow"></a>Regisztráció felhasználói folyamat létrehozása

[!INCLUDE [active-directory-b2c-create-sign-up-policy](../../includes/active-directory-b2c-create-sign-up-policy.md)]

## <a name="create-a-sign-in-user-flow"></a>Jelentkezzen be a felhasználói folyamat létrehozása

[!INCLUDE [active-directory-b2c-create-sign-in-policy](../../includes/active-directory-b2c-create-sign-in-policy.md)]

## <a name="create-a-profile-editing-user-flow"></a>Profilszerkesztési felhasználói folyamata létrehozása

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

## <a name="create-a-password-reset-user-flow"></a>Jelszó alaphelyzetbe állítása felhasználói folyamat létrehozása

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

## <a name="preview-user-flows"></a>Előzetes verzió felhasználókövetési adatai

Új szolgáltatásokat adunk, mivel ezek közül néhányat nem lehet áll rendelkezésre a meglévő szabályzatok, vagy a felhasználói folyamatok.  Tervezzük cserélje le a legújabb azonos típusú régebbi verziók után ezek a felhasználói folyamatok adja meg az általánosan elérhető  A meglévő szabályzatokat vagy a felhasználói folyamatok nem változik, és új funkciók előnyeinek kihasználásához lesz új felhasználói folyamatokat hozhat létre.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="how-do-i-link-a-sign-up-or-sign-in-user-flow-with-a-password-reset-user-flow"></a>Hogyan lehet hivatkozni a jelszó alaphelyzetbe állítása felhasználói folyamat regisztrálási vagy bejelentkezési felhasználói folyamat?
Amikor hoz létre egy **regisztrálási vagy bejelentkezési** felhasználói folyamat (a helyi fiókok esetében), megjelenik egy **elfelejtette jelszavát?** tapasztalatait az első lapon. Ez a hivatkozás nem automatikusan eseményindító jelszó alaphelyzetbe felhasználói folyamatot. 

Ehelyett a hibakódot **`AADB2C90118`** küld vissza az alkalmazást. Az alkalmazás egy adott jelszóvisszaállítási felhasználói folyamatot meghívásával erről a hibakódról kezelnie kell. További információkért lásd: egy [minta azt mutatja be a felhasználói folyamatok hivatkozást megközelítés](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI).

### <a name="should-i-use-a-sign-up-or-sign-in-user-flow-or-a-sign-up-user-flow-and-a-sign-in-user-flow"></a>Regisztrálási vagy bejelentkezési felhasználói folyamat vagy a felhasználói regisztrációs folyamat és a egy bejelentkezési felhasználói folyamatot kell használnom?
Javasoljuk, hogy használjon egy **regisztrálási vagy bejelentkezési** felhasználói folyamat során egy **előfizetési** felhasználói folyamat és a egy **bejelentkezési** felhasználói folyamat.  

A **regisztrálási vagy bejelentkezési** felhasználói folyamat, mint további képességekkel rendelkezik a **bejelentkezési** felhasználói folyamatot. Emellett lehetővé teszi, hogy oldal-UI testreszabása és jobb támogatása érdekében a honosításra van. 

A **bejelentkezési** felhasználói folyamat ajánlott, nem kell a felhasználói folyamatok honosítása, ha csak kisebb testreszabási lehetőségeket szükséges márkajelzési, és szeretné a jelszó alaphelyzetbe állítása, beépített.

## <a name="next-steps"></a>További lépések
* [Jogkivonat, munkamenet és egyszeri bejelentkezés konfigurálása](active-directory-b2c-token-session-sso.md)
* [Során felhasználói regisztrációs e-mailes ellenőrzés letiltása](active-directory-b2c-reference-disable-ev.md)

