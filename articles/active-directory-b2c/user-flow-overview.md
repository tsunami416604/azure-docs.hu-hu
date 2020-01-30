---
title: Felhasználói folyamatok Azure Active Directory B2Cban | Microsoft Docs
description: További információ a Azure Active Directory B2C bővíthető házirend-keretrendszeréről és a különböző felhasználói folyamatok létrehozásáról.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 846819c3a05baf4ef1174d4e086f09893e834b2a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847796"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Felhasználói folyamatok Azure Active Directory B2C

A Azure Active Directory B2C (Azure AD B2C) bővíthető házirend-keretrendszere a szolgáltatás alapvető erőssége. A szabályzatok teljes mértékben leírják a személyazonossággal kapcsolatos tapasztalatokat, például a regisztrálást, a bejelentkezést vagy a profil szerkesztését. A leggyakoribb identitási feladatok beállításához a Azure AD B2C-portál a **felhasználói folyamatok**nevű előre definiált, konfigurálható szabályzatokat tartalmazza.

## <a name="what-are-user-flows"></a>Mik azok a felhasználói folyamatok?

A felhasználói folyamat lehetővé teszi az alkalmazások viselkedésének vezérlését a következő beállítások konfigurálásával:

- Bejelentkezéshez használt fióktípus, például Facebook-vagy helyi fiókokhoz hasonló közösségi fiókok
- A fogyasztótól begyűjtött attribútumok, például Utónév, irányítószám és a cipő mérete
- Azure Multi-Factor Authentication
- A felhasználói felület testreszabása
- Információ arról, hogy az alkalmazás jogcímeket fogad jogkivonatként

A bérlőben számos különböző típusú felhasználói folyamatot hozhat létre, és igény szerint használhatja azokat az alkalmazásokban. A felhasználói folyamatok az alkalmazások között újra felhasználhatók. Ez a rugalmasság lehetővé teszi az identitások megadását és módosítását a kód minimális vagy semmilyen módosításával. Az alkalmazás egy szabványos HTTP-hitelesítési kérelem használatával indítja el a felhasználói folyamatot, amely tartalmazza a felhasználói folyamat paraméterét. Egy testreszabott [jogkivonat](tokens-overview.md) érkezik válaszként.

Az alábbi példák a "p" lekérdezési karakterlánc paramétert mutatják be, amely meghatározza a használni kívánt felhasználói folyamatot:

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

A Azure Portalban a [felhasználói folyamatok új verziói](user-flow-versions.md) folyamatosan bővülnek. A Azure AD B2C használatának megkezdése után a tesztelt felhasználói folyamatok használata javasolt. Új felhasználói folyamat létrehozásakor ki kell választania a kívánt felhasználói folyamatot az **ajánlott** lapon.

A következő felhasználói folyamatok jelenleg ajánlottak:

- **Regisztráció és bejelentkezés** – kezeli a regisztrációs és a bejelentkezési élményeket egyetlen konfigurációval. A felhasználók a környezettől függően a megfelelő útvonalat vezetik le. Javasoljuk, hogy ezt a felhasználói folyamatot egy **regisztrációs** felhasználói folyamaton vagy egy **bejelentkezési** felhasználói folyamaton keresztül használja.
- **Profil szerkesztése** – lehetővé teszi a felhasználók számára a profil adatainak szerkesztését.
- **Jelszó alaphelyzetbe állítása** – beállíthatja, hogy a felhasználók hogyan állíthatják alaphelyzetbe a jelszavukat.

## <a name="linking-user-flows"></a>Felhasználói folyamatok összekapcsolása

A helyi fiókkal rendelkező **regisztrációs vagy bejelentkezési** felhasználói folyamat tartalmaz egy **elfelejtett jelszót?** hivatkozást a felület első oldalán. Ha erre a hivatkozásra kattint, nem indítja el automatikusan a jelszó-visszaállítás felhasználói folyamatát.

Ehelyett a `AADB2C90118` hibakódot adja vissza az alkalmazásnak. Az alkalmazásnak ezt a hibakódot egy adott felhasználói folyamat futtatásával kell kezelnie, amely alaphelyzetbe állítja a jelszót. Ha példát szeretne látni, tekintse meg az [egyszerű ASP.net mintát](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) , amely bemutatja a felhasználói folyamatok összekapcsolását.

## <a name="email-address-storage"></a>E-mail-cím tárterülete

Egy e-mail-cím a felhasználói folyamat részeként is megkövetelhető. Ha a felhasználó egy közösségi identitás-szolgáltatóval végzi a hitelesítést, az e-mail-cím a **otherMails** tulajdonságban tárolódik. Ha egy helyi fiók egy Felhasználónév alapján van kiválasztva, akkor az e-mail-cím egy erős hitelesítési részlet tulajdonságban tárolódik. Ha egy helyi fiók egy e-mail-címen alapul, akkor az e-mail-cím a **signInNames** tulajdonságban tárolódik.

Az e-mail-cím nem garantálható az ilyen esetekben. A bérlői rendszergazda letilthatja az e-mailek ellenőrzését a helyi fiókok alapszintű házirendjeiben. Még ha az e-mail cím ellenőrzése is engedélyezve van, a rendszer nem ellenőrzi a címeket, ha azok egy közösségi identitás-szolgáltatótól származnak, és nem változtak.

A Active Directory Graph API csak a **otherMails** és a **signInNames** tulajdonságot teszi elérhetővé. Az erős hitelesítés részletei tulajdonságban szereplő e-mail-cím nem érhető el.

## <a name="next-steps"></a>Következő lépések

Az ajánlott felhasználói folyamatok létrehozásához kövesse az [oktatóanyag: felhasználói folyamat létrehozása](tutorial-create-user-flows.md)című témakör utasításait.


