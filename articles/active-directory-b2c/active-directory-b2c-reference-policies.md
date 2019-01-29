---
title: Az Azure Active Directory B2C felhasználói folyamatok |} A Microsoft Docs
description: További információ az Azure Active Directory B2C-vel és a különböző felhasználói folyamatok létrehozása a bővíthető házirend-keretrendszer.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 88ac97c36a9b09b795a3ea792df6711fc1275422
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55163007"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Az Azure Active Directory B2C felhasználói folyamatok

Az Azure Active Directory (Azure AD) B2C bővíthető házirend-keretrendszer a szolgáltatás alapvető erősségét. Például csak a teljes leírása identitásélményt házirendek regisztráció, bejelentkezés vagy profil szerkesztéséhez. Segítséget a leggyakoribb feladatok identitás beállítása, az Azure AD B2C-portálon előre meghatározott, a konfigurálható szabályzatok nevű tartalmaz **felhasználókövetési adatai**. 

## <a name="what-are-user-flows"></a>Mik azok a felhasználói folyamatok?

Felhasználói folyamat segítségével szabályozható a viselkedést az alkalmazások a úgy konfigurálja a következő beállításokat:

- Típusok használt a bejelentkezéshez, mint a közösségi fiókok fiók, mint a Facebook vagy a helyi fiókok
- A felhasználóknak, például az első név, postai irányítószám, gyűjteni és cipő mérete attribútumok
- Azure Multi-Factor Authentication
- A felhasználói felület testreszabása
- Az alkalmazás fogad a jogkivonatban jogcímekként információk 

Különböző típusú számos felhasználói folyamatok létrehozása a bérlőben, és igény szerint használhatja őket az alkalmazásokban. Felhasználói folyamatok alkalmazásokhoz felhasználhatók. Ezt a rugalmasságot és identitással kapcsolatos műveletet, a minimális vagy a programkód módosítása nélkül teszi lehetővé. Az alkalmazás felhasználói folyamat aktiválása egy normál HTTP-hitelesítési kérelmet, amely tartalmazza a felhasználói folyamat paraméter használatával. A testre szabott [token](active-directory-b2c-reference-tokens.md) nem érkezett válasz. 

Az alábbi példák bemutatják a "p" lekérdezésisztring-paraméter, amely meghatározza a használandó felhasználói folyamatot:

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

Az új Azure Portalon [felhasználói folyamatok verziói](user-flow-versions.md) folyamatosan bővül. Használatának megkezdése az Azure AD B2C-vel, ha vizsgálni a felhasználói folyamatok használata javasolt, hogy használja. Egy új felhasználói folyamatot hoz létre, ha úgy dönt, a felhasználói folyamatot, amely szükséges a **ajánlott** fülre.

Jelenleg ajánlott a következő felhasználói folyamatok:

- **Regisztráljon, és jelentkezzen be a** – mind a regisztrációs és bejelentkezési élményt egyetlen konfigurációval rendelkező kezeli. Felhasználók vannak vezetett le a megfelelő elérési útját a környezettől függően. Javasoljuk, hogy használja-e a felhasználói folyamat keresztül egy **előfizetési** felhasználói folyamat vagy egy **bejelentkezési** felhasználói folyamatot.
- **Profil szerkesztése** – lehetővé teszi a felhasználók szerkesztése azok profiladatait.
- **Új jelszó kérésére vonatkozó** -e, és hogyan felhasználókat jelszavuk is konfigurálhatja.

## <a name="linking-user-flows"></a>Hivatkozási felhasználókövetési adatai

A **regisztrálási vagy bejelentkezési** helyi fiókkal rendelkező felhasználói folyamat magában foglalja egy **elfelejtette jelszavát?** tapasztalatait az első lapon. Ez a hivatkozás nem automatikusan eseményindító jelszó alaphelyzetbe felhasználói folyamatot. 

Ehelyett a hibakódot `AADB2C90118` küld vissza az alkalmazást. Az alkalmazás által egy adott felhasználói folyamatot, amely alaphelyzetbe állítja a jelszót erről a hibakódról kezelnie kell. Lásd például, vessen egy pillantást egy [egyszerű ASP.NET-minta](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) , amely bemutatja a felhasználói folyamatok csatolását.

## <a name="email-address-storage"></a>E-mail cím storage

Lehet, hogy egy e-mail-cím részeként szükség van egy felhasználói folyamat. Ha a felhasználó hitelesíti magát egy közösségi identitásszolgáltatóval, az e-mail-cím tárolódik a **otherMails** tulajdonság. Ha egy helyi fiók felhasználói nevét alapul, az e-mail-cím tárolva van egy erős hitelesítés részletei tulajdonság. Ha egy e-mail-cím alapján egy helyi fiókot, akkor az e-mail-cím tárolja a **signInNames** tulajdonság.
 
Az e-mail-cím nem garantált, hogy mindkét esetben ellenőrizhető. Bérlői rendszergazda letilthatja az e-mailes ellenőrzés azokban a házirendekben, amelyek alapvető helyi fiókok esetében. Akkor is, ha az e-mail cím ellenőrzése engedélyezve van, a címek nem ellenőrzött Ha egy közösségi identitásszolgáltatót származnak, és azok még nem lett módosítva.
 
Csak a **otherMails** és **signInNames** tulajdonságait az Active Directory Graph API keresztül érhetők el. Az e-mail-címét az erős hitelesítés részletei tulajdonság nem érhető el.

## <a name="next-steps"></a>További lépések

A javasolt felhasználói folyamatok létrehozásához kövesse a [oktatóanyag: Felhasználói folyamat létrehozása](tutorial-create-tenant.md).


