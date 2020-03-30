---
title: Felhasználói folyamatok az Azure Active Directory B2C könyvtárban | Microsoft dokumentumok
description: További információ az Azure Active Directory B2C bővíthető házirend-keretrendszeréről és a különböző felhasználói folyamatok létrehozásáról.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c11bc48742c398d2048a236c7d00af044971f845
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185608"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Felhasználói folyamatok az Azure Active Directory B2C-ben

Az Azure Active Directory B2C (Azure AD B2C) bővíthető házirendkeretrendszere a szolgáltatás fő erőssége. A házirendek teljes mértékben ismertetik az identitáskezelési élményeket, például a regisztrációt, a bejelentkezést vagy a profilszerkesztést. A leggyakoribb identitáskezelési feladatok beállítása érdekében az Azure AD B2C portál előre definiált, konfigurálható szabályzatokat, úgynevezett **felhasználói folyamatokat**tartalmaz.

## <a name="what-are-user-flows"></a>Mik azok a felhasználói folyamatok?

A felhasználói folyamat lehetővé teszi az alkalmazások viselkedésének szabályozását a következő beállítások konfigurálásával:

- A bejelentkezéshez használt fióktípusok, például közösségi fiókok, például Facebook- vagy helyi fiókok
- A fogyasztótól begyűjtendő attribútumok, például a keresztnév, az irányítószám és a cipőméret
- Azure Multi-Factor Authentication
- A felhasználói felület testreszabása
- Az alkalmazás jogcímként kapott információ tokenben

Számos különböző típusú felhasználói folyamatot hozhat létre a bérlőben, és szükség szerint használhatja őket az alkalmazásokban. A felhasználói folyamatok újra felhasználhatók az alkalmazások között. Ez a rugalmasság lehetővé teszi az identitásélmények definiálását és módosítását a kód minimális vagy semmilyen módosításával. Az alkalmazás elindítja a felhasználói folyamat egy szabványos HTTP-hitelesítési kérelem, amely tartalmazza a felhasználói folyamat paraméter. A testreszabott [jogkivonat](tokens-overview.md) válaszként érkezik.

A következő példák a "p" lekérdezési karakterlánc paramétert mutatják be, amely meghatározza a használandó felhasználói folyamatot:

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

## <a name="user-flow-versions"></a>Felhasználói folyamatok verziói

Az Azure Portalon a felhasználói folyamatok új [verziói](user-flow-versions.md) folyamatosan kerülnek hozzáadásra. Az Azure AD B2C használatának megkezdésekor a tesztelt felhasználói folyamatok használata ajánlott. Amikor új felhasználói folyamatot hoz létre, az **Ajánlott** lapon választhatja ki a szükséges felhasználói folyamatot.

Jelenleg a következő felhasználói folyamatok ajánlottak:

- **Regisztráció és bejelentkezés** – egyetlen konfigurációval kezeli a regisztrációs és a bejelentkezési élményeket is. A felhasználók a környezettől függően a helyes úton haladnak. Javasoljuk, hogy használja ezt a felhasználói folyamatot egy **regisztrációs** felhasználói folyamat vagy egy **bejelentkezési** felhasználói folyamat.
- **Profilszerkesztés** – Lehetővé teszi a felhasználók számára a profiladataik szerkesztését.
- **Jelszó-visszaállítás** – Lehetővé teszi annak beállítását, hogy a felhasználók visszaállíthatják-e a jelszavukat, és ha annak módját.

## <a name="linking-user-flows"></a>Felhasználói folyamatok összekapcsolása

A helyi fiókkal rendelkező **regisztrációs vagy bejelentkezési** felhasználói folyamat tartalmaz egy **Elfelejtett jelszót?** hivatkozást az élmény első oldalán. Erre a hivatkozásra kattintva nem indítja el automatikusan a jelszó-visszaállítási felhasználói folyamatot.

Ehelyett a hibakódot `AADB2C90118` adja vissza az alkalmazásnak. Az alkalmazásnak kezelnie kell ezt a hibakódot egy adott felhasználói folyamat futtatásával, amely alaphelyzetbe állítja a jelszót. Egy példa megtekintéséhez vessen egy pillantást egy [egyszerű ASP.NET minta,](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) amely bemutatja a felhasználói folyamatok összekapcsolása.

## <a name="email-address-storage"></a>E-mail cím tárolása

A felhasználói folyamat részeként e-mail-cím re kérhető. Ha a felhasználó hitelesíti magát egy közösségi identitásszolgáltatónál, az e-mail cím a **többiMails** tulajdonságban tárolódik. Ha egy helyi fiók felhasználónévn alapul, akkor az e-mail cím egy erős hitelesítési részletes tulajdonságban tárolódik. Ha egy helyi fiók e-mail-címen alapul, akkor az e-mail cím a **signInNames** tulajdonságban tárolódik.

Az e-mail cím nem garantált, hogy ellenőrizni kell az ilyen esetek bármelyike. A bérlői rendszergazda letilthatja az e-mailek ellenőrzését a helyi fiókok alapvető szabályzataiban. Még ha az e-mail-cím ellenőrzése engedélyezve is van, a címek nincsenek ellenőrizve, ha közösségi identitásszolgáltatótól származnak, és nem változtak.

Csak a **többiMailés** **signInNames** tulajdonság a Microsoft Graph API-n keresztül lesz elérhető. Az erős hitelesítési részletes tulajdonságban lévő e-mail cím nem érhető el.

## <a name="next-steps"></a>További lépések

Az ajánlott felhasználói folyamatok létrehozásához kövesse az [oktatóanyag utasításait: Felhasználói folyamat létrehozása](tutorial-create-user-flows.md).
