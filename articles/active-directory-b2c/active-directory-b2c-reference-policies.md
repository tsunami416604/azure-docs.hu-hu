---
title: "Az Azure Active Directory B2C: Beépített házirendek |} Microsoft Docs"
description: "A témakör az Azure Active Directory B2C bővíthető szabályzat-keretrendszert és különféle házirend létrehozása"
services: active-directory-b2c
documentationcenter: 
author: sama
manager: mtillman
editor: PatAltimore
ms.assetid: 0d453e72-7f70-4aa2-953d-938d2814d5a9
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: sama
ms.openlocfilehash: f0aa3d19e15837b75888293f0cd19683b7621a6a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-built-in-policies"></a>Az Azure Active Directory B2C: Beépített házirendek


Az Azure Active Directory (Azure AD) B2C bővíthető szabályzat-keretrendszernek a szolgáltatás alapvető erősségével. Például csak a teljes leírása fogyasztói identitással kapcsolatos műveletet házirendek regisztráció, bejelentkezés és profil szerkesztése. Például a regisztrációs szabályzatban teszi viselkedés szabályozására, a következő beállítások konfigurálása:

* Fióktípus (közösségi, például a Facebookhoz) és helyi fiókok például az e-mail címeket, amelyek fogyasztókat iratkozzon fel az alkalmazáshoz
* Attribútumok (például utónév, irányítószámát és cipőméreten) kell lennie az ügyféltől összegyűjtött regisztráció során
* Az Azure többtényezős hitelesítés
* Az összes regisztrációs lap megjelenését és működését
* Információ (amely akkor jelentkezik, mint a jogcím egy jogkivonatba), hogy az alkalmazás esetén a házirendet kap futtatása befejeződött

Hozzon létre több, különböző típusú házirendet az Ön bérlőjében, és felhasználja az alkalmazásokban, igény szerint. Házirendek a különböző alkalmazások felhasználhatók. Erre a rugalmasságra segítségével a fejlesztők határozza meg, és módosítsa a fogyasztói identitással kapcsolatos műveletet, a minimális vagy a kód nem módosultak.

Házirendek állnak rendelkezésre egy egyszerű fejlesztői felületen keresztül. Az alkalmazás elindítja egy házirendet a szabványos HTTP-hitelesítési kérelmek (házirend paraméter átadja a kérésben) használatával, és megkapja a testreszabott jogkivonatot válaszként. Például az egyetlen különbség, hogy a regisztrációs szabályzatban meghívása és invoke egy bejelentkezési házirend kérelmeket között a házirend nevére, amely a "p" lekérdezési karakterlánc-paraméter szerepel:

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

A szabályzat-keretrendszernek kapcsolatos további információkért lásd: [ebben a blogbejegyzésben kapcsolatban az Azure AD B2C meg a nagyvállalati mobilitással és biztonsággal foglalkozó blogban](http://blogs.technet.com/b/ad/archive/2015/11/02/a-look-inside-azuread-b2c-with-kim-cameron.aspx).

## <a name="create-a-sign-up-or-sign-in-policy"></a>Regisztráció vagy bejelentkezés házirend létrehozása

Ez a házirend mindkét fogyasztói előfizetési & bejelentkezési élmény egyetlen konfigurációval kezeli. Fogyasztók le a megfelelő elérési utat (regisztráció vagy bejelentkezés) attól függően, hogy a helyi rendszer vezetett. Azt is bemutatja a jogkivonatokat, amelyek az alkalmazás sikeres regisztrációra vagy bejelentkezések kap tartalmát.  A kód a minta a regisztráció vagy bejelentkezés házirend [érhető el itt](active-directory-b2c-devquickstarts-web-dotnet-susi.md).  Ajánlott maszkolandó, hogy a regisztrációs szabályzatban, és a bejelentkezési házirend keresztül használja ezt a házirendet is.  

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

## <a name="create-a-sign-up-policy"></a>Előfizetési házirend létrehozása

[!INCLUDE [active-directory-b2c-create-sign-up-policy](../../includes/active-directory-b2c-create-sign-up-policy.md)]

## <a name="create-a-sign-in-policy"></a>Bejelentkezési házirend létrehozása

[!INCLUDE [active-directory-b2c-create-sign-in-policy](../../includes/active-directory-b2c-create-sign-in-policy.md)]

## <a name="create-a-profile-editing-policy"></a>A Profilszerkesztési házirend létrehozása

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

## <a name="create-a-password-reset-policy"></a>A jelszó-visszaállítási házirend létrehozása

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="how-do-i-link-a-sign-up-or-sign-in-policy-with-a-password-reset-policy"></a>Hogyan hivatkozásra a jelszó-visszaállítási házirend regisztráció vagy bejelentkezés házirenddel?
A regisztráció vagy bejelentkezés házirendje (helyi fiókoknál) hoz létre, ha megjelenik egy **elfelejtette jelszó?** tapasztalat első oldalán lévő hivatkozásra. Ez a hivatkozás nem automatikusan eseményindító jelszó alaphelyzetbe állítása házirend. 

Ehelyett a hiba kódja  **`AADB2C90118`**  küld vissza az alkalmazást. Ez a hibakód kezelni a megadott jelszó-visszaállítási házirend figyelőn kell az alkalmazást. További információkért lásd: a [minta azt mutatja be a módszert is, amely a linking házirendek](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI).

### <a name="should-i-use-a-sign-up-or-sign-in-policy-or-a-sign-up-policy-and-a-sign-in-policy"></a>A regisztráció vagy bejelentkezés házirend vagy a regisztrációs szabályzatban, és a bejelentkezési házirend érdemes használni?
Azt javasoljuk, hogy a regisztrációs szabályzatban, és a bejelentkezési házirend keresztül használja a regisztráció vagy bejelentkezés házirend.  

A regisztráció vagy bejelentkezés házirend a bejelentkezési házirend-nál több képességekkel rendelkezik. Lehetővé teszi, hogy lap felhasználói felületének testreszabása és a honosításhoz jobb támogatást. 

A bejelentkezési házirend ajánlott, nem kell szerepelnie a házirendeket, ha csak kisebb testreszabási lehetőségek szükséges branding, és szeretné a jelszó alaphelyzetbe állítása azt beépítve.

## <a name="next-steps"></a>Következő lépések
* [Token, munkamenet és egyszeri bejelentkezés konfigurálása](active-directory-b2c-token-session-sso.md)
* [Tiltsa le a felhasználói regisztráció során e-mail ellenőrzése](active-directory-b2c-reference-disable-ev.md)

