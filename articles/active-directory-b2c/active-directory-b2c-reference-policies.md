---
title: Beépített szabályzatokat az Azure Active Directory B2C |} A Microsoft Docs
description: A témakör az Azure Active Directory B2C bővíthető házirend-keretrendszer és a különféle házirend létrehozása.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 5c89f39b2f94309ea3d99230f5265d834c7093d9
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444820"
---
# <a name="azure-active-directory-b2c-built-in-policies"></a>Az Azure Active Directory B2C: Beépített szabályzatok


Az Azure Active Directory (Azure AD) B2C bővíthető házirend-keretrendszer a szolgáltatás alapvető erősségét. Például csak a teljes leírása fogyasztói identitások jellemzőit házirendek regisztráció, bejelentkezés vagy profil szerkesztéséhez. Például egy regisztrálási szabályzatot lehetővé teszi a viselkedés szabályozására, a következő beállítások konfigurálásához:

* Fióktípus esetében (például a Facebookhoz közösségi fiókok) vagy a helyi fiókok, például az e-mail-címeket, amelyek a fogyasztók iratkozzon fel az alkalmazás használatával
* Attribútumok (például utónév, postai irányítószám és cipőméreten) regisztráció során a felhasználóknak gyűjtendő
* Az Azure multi-factor Authentication használata
* Az összes bejelentkezési oldalak megjelenésének és
* Információ (amely jegyzékfájlok egy jogkivonatban jogcímekként), hogy az alkalmazás fogad mikor a Futtatás végeztével házirend

Különböző típusú több szabályzat létrehozása a bérlőben, és igény szerint használhatja őket az alkalmazásokban. Házirendek alkalmazásokhoz felhasználhatók. Ez rugalmasságot lehetővé teszi a fejlesztők és módosíthatja a fogyasztói identitások jellemzőit, minimális konfigurációval vagy a programkód módosítása nélkül.

Házirendek használata egy egyszerű fejlesztői felületen keresztül érhetők el. Az alkalmazás elindítja egy szabályzatot egy normál HTTP-hitelesítési kérelmet (a szabályzatparaméter átadja a kérésben) használatával, és választ, testre szabott jogkivonatot kap. Például az egyetlen kérelmek, amelyek aktiválják az előfizetési szabályzat és a kérelmek, amelyek aktiválják a bejelentkezési szabályzat közötti különbség a szabályzat nevét, amely a "p" lekérdezésisztring-paraméter szerepel:

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

## <a name="create-a-sign-up-or-sign-in-policy"></a>Regisztrálási vagy bejelentkezési szabályzat létrehozása

Ez a szabályzat mindkét felhasználói regisztrációs és bejelentkezési élmény egyetlen konfigurációval rendelkező kezeli. A fogyasztók le a megfelelő elérési útját (regisztrálási vagy bejelentkezési) függően is vezetett. Bemutatja a jogkivonatokat, amelyeket az alkalmazás fogad sikeres regisztrálásokkor vagy bejelentkezések tartalmát is.  A kódminta a **regisztrálási vagy bejelentkezési** házirend [elérhető itt](active-directory-b2c-devquickstarts-web-dotnet-susi.md).  Javasoljuk, hogy használja-e ez a szabályzat keresztül egy **előfizetési** csoportházirend vagy egy **bejelentkezési** házirend.  

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

## <a name="create-a-sign-up-policy"></a>Előfizetési szabályzat létrehozása

[!INCLUDE [active-directory-b2c-create-sign-up-policy](../../includes/active-directory-b2c-create-sign-up-policy.md)]

## <a name="create-a-sign-in-policy"></a>Bejelentkezési szabályzat létrehozása

[!INCLUDE [active-directory-b2c-create-sign-in-policy](../../includes/active-directory-b2c-create-sign-in-policy.md)]

## <a name="create-a-profile-editing-policy"></a>Profilszerkesztési szabályzat létrehozása

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

## <a name="create-a-password-reset-policy"></a>Új jelszó kérésére vonatkozó szabályzat létrehozása

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

## <a name="preview-policies"></a>Előzetes verzió házirendek

Új szolgáltatásokat adunk, mivel ezek közül néhányat a nem lehet elérhető a meglévő szabályzatokat.  Tervezzük cserélje le a legújabb azonos típusú régebbi verziók után ezek a szabályzatok adja meg az általánosan elérhető  A meglévő szabályzatokat nem változik, és új funkciók előnyeinek kihasználásához kell új szabályzatokat hozhat létre.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="how-do-i-link-a-sign-up-or-sign-in-policy-with-a-password-reset-policy"></a>Hogyan lehet hivatkozni a jelszó-visszaállítási házirend regisztrálási vagy bejelentkezési szabályzat?
Létrehozásakor egy **regisztrálási vagy bejelentkezési** szabályzat (helyi fiókoknál), tekintse meg a **elfelejtette jelszavát?** tapasztalatait az első lapon. Ez a hivatkozás nem automatikusan eseményindító jelszó alaphelyzetbe házirend. 

Ehelyett a hibakódot **`AADB2C90118`** küld vissza az alkalmazást. Az alkalmazás egy adott jelszó kérésére vonatkozó szabályzat meghívásával erről a hibakódról kezelnie kell. További információkért lásd: egy [minta azt mutatja be a módszert is, amely a linking házirendek](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI).

### <a name="should-i-use-a-sign-up-or-sign-in-policy-or-a-sign-up-policy-and-a-sign-in-policy"></a>Regisztrálási vagy bejelentkezési szabályzat vagy a regisztrálási szabályzat és a egy bejelentkezési szabályzatot kell használnom?
Javasoljuk, hogy használjon egy **regisztrálási vagy bejelentkezési** házirend fölé egy **előfizetési** házirend és a egy **bejelentkezési** házirend.  

A **regisztrálási vagy bejelentkezési** házirend, mint további képességekkel rendelkezik a **bejelentkezési** házirend. Emellett lehetővé teszi, hogy oldal-UI testreszabása és jobb támogatása érdekében a honosításra van. 

A **bejelentkezési** házirend ajánlott, nem kell a szabályzatok honosítása, ha csak kisebb testreszabási lehetőségeket márkajelzési szükséges, és szeretné jelszó alaphelyzetbe állítása, beépített.

## <a name="next-steps"></a>További lépések
* [Jogkivonat, munkamenet és egyszeri bejelentkezés konfigurálása](active-directory-b2c-token-session-sso.md)
* [Során felhasználói regisztrációs e-mailes ellenőrzés letiltása](active-directory-b2c-reference-disable-ev.md)

