---
title: SSO-munkamenetek Azure Active Directory B2Cban | Microsoft Docs
description: Munkamenet-viselkedés konfigurálása Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ea8c40faad4ee709ae98f868e36fd42e46501bea
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82927037"
---
# <a name="azure-ad-b2c-session"></a>Azure AD B2C-munkamenet

Az egyszeri bejelentkezés (SSO) biztonsági és kényelmi funkciókat biztosít, amikor a felhasználók bejelentkeznek Azure Active Directory B2C (Azure AD B2C) alkalmazásokban. Ez a cikk a Azure AD B2Cben használt egyszeri bejelentkezési módszereket ismerteti, és segít kiválasztani a legmegfelelőbb egyszeri bejelentkezési módszert a szabályzat konfigurálása során.

Egyszeri bejelentkezés esetén a felhasználók egyetlen fiókkal jelentkeznek be, és több alkalmazáshoz is hozzáférhetnek. Az alkalmazás lehet web-, mobil-vagy egyoldalas alkalmazás, függetlenül a platformtól vagy a tartománynévtől.

Amikor a felhasználó először bejelentkezik egy alkalmazásba, Azure AD B2C megőrzi a cookie-alapú munkamenetet. További hitelesítési kérelmek esetén Azure AD B2C beolvassa és érvényesíti a cookie-alapú munkamenetet, és a felhasználó értesítése nélkül kiadja a hozzáférési jogkivonatot. Ha a cookie-alapú munkamenet lejár vagy érvénytelenné válik, a rendszer felszólítja a felhasználót, hogy jelentkezzen be újra.  

## <a name="sso-session-types"></a>SSO-munkamenetek típusai

A Azure AD B2C való integráció három típusú SSO-munkamenetet foglal magában:

- **Azure ad B2C** – a Azure ad B2C által felügyelt munkamenet
- **Összevont identitás-szolgáltató** – az identitás-szolgáltató által kezelt munkamenet, például Facebook, Salesforce vagy Microsoft-fiók
- Web-, mobil-vagy egyoldalas alkalmazás által felügyelt **alkalmazás** -munkamenet

![SSO-munkamenet](media/session-overview/sso-session-types.png)

### <a name="azure-ad-b2c-sso"></a>Egyszeri bejelentkezés Azure AD B2C 

Amikor egy felhasználó sikeresen hitelesít egy helyi vagy közösségi fiókkal, a Azure AD B2C egy cookie-alapú munkamenetet tárol a felhasználó böngészőjében. A cookie-t a Azure AD B2C bérlői tartomány neve alatt tárolja, `https://contoso.b2clogin.com`például:.

Ha egy felhasználó először összevont fiókkal jelentkezik be, majd a munkamenet ideje (élettartam vagy TTL) bejelentkezik egy adott alkalmazásba vagy egy másik alkalmazásba, Azure AD B2C megpróbál új hozzáférési jogkivonatot beszerezni az összevont identitás-szolgáltatótól. Ha az összevont identitás-szolgáltató munkamenete lejárt vagy érvénytelen, az összevont identitás szolgáltatója kéri a felhasználót a hitelesítő adataik megadására. Ha a munkamenet továbbra is aktív (vagy ha a felhasználó egy összevont fiók helyett helyi fiókkal jelentkezett be), a Azure AD B2C engedélyezi a felhasználó számára, és megszünteti a további kéréseket.

Konfigurálhatja a munkamenet viselkedését, beleértve a munkamenet ÉLETTARTAMát, valamint azt, hogy a Azure AD B2C hogyan osztja meg a munkamenetet a házirendek és az alkalmazások között.

### <a name="federated-identity-provider-sso"></a>Összevont identitás-szolgáltató egyszeri bejelentkezés

A közösségi vagy vállalati identitás-szolgáltató a saját munkamenetét kezeli. A cookie-t az Identity Provider tartományneve (például `https://login.salesforce.com`) tárolja. Azure AD B2C nem szabályozza az összevont identitás-szolgáltatói munkamenetet. Ehelyett a munkamenet viselkedését az összevont identitás-szolgáltató határozza meg. 

Vegyük példaként a következő esetet:

1. A felhasználók bejelentkeznek a Facebookon, hogy ellenőrizzék a hírcsatornát.
2. Később a felhasználó megnyitja az alkalmazást, és elindítja a bejelentkezési folyamatot. Az alkalmazás átirányítja a felhasználót, hogy Azure AD B2C a bejelentkezési folyamat befejezéséhez.
3. A Azure AD B2C regisztrációs vagy bejelentkezési oldalán a felhasználó úgy dönt, hogy bejelentkezik Facebook-fiókjával. A rendszer átirányítja a felhasználót a Facebook-ra. Ha a Facebookban aktív munkamenet van, a rendszer nem kéri a felhasználót, hogy adja meg hitelesítő adatait, és a rendszer azonnal átirányítja a Facebook-tokenbe Azure AD B2C.

### <a name="application-sso"></a>Alkalmazás egyszeri bejelentkezés

A web-, mobil-vagy egyoldalas alkalmazások védelmét OAuth-hozzáférés, azonosító jogkivonatok vagy SAML-tokenek védik. Amikor egy felhasználó egy védett erőforráshoz próbál hozzáférni az alkalmazásban, az alkalmazás ellenőrzi, hogy van-e aktív munkamenet az alkalmazás oldalán. Ha nincs alkalmazás-munkamenet, vagy a munkamenet lejárt, akkor az alkalmazás beAzure AD B2C a bejelentkezési oldalra.

Az alkalmazás-munkamenet lehet az alkalmazás tartományának neve alatt tárolt cookie-alapú munkamenet, például: `https://contoso.com`. A mobil alkalmazások eltérő módon tárolhatják a munkamenetet, de hasonló megközelítést alkalmaznak.

## <a name="azure-ad-b2c-session-configuration"></a>Azure AD B2C munkamenet-konfiguráció

### <a name="session-scope"></a>Munkamenet hatóköre

A Azure AD B2C munkamenet a következő hatókörökkel konfigurálható:

- **Bérlő** – ez a beállítás az alapértelmezett érték. Ezzel a beállítással a B2C-bérlőben több alkalmazás és felhasználói folyamat is megoszthatja ugyanazt a felhasználói munkamenetet. Ha például egy felhasználó bejelentkezik egy alkalmazásba, a felhasználó zökkenőmentesen is bejelentkezhet egy másikba, amikor hozzáfér.
- **Alkalmazás** – ez a beállítás lehetővé teszi, hogy a felhasználói munkamenetet kizárólag egy alkalmazáshoz, más alkalmazásoktól függetlenül kezelje. Használhatja például ezt a beállítást, ha azt szeretné, hogy a felhasználó bejelentkezzen a contoso gyógyszertárba, függetlenül attól, hogy a felhasználó már be van-e jelentkezve a contoso-élelmiszerbe.
- **Házirend** – ez a beállítás lehetővé teszi a felhasználói munkamenetek kizárólag felhasználói folyamatokhoz való fenntartását, az azt használó alkalmazásoktól függetlenül. Ha például a felhasználó már bejelentkezett, és elvégezte a többtényezős hitelesítés (MFA) lépését, a felhasználó több alkalmazás nagyobb biztonságú részeihez is hozzáférhet, ha a munkamenet a felhasználói folyamathoz kötődik, nem jár le.
- **Letiltva** – ez a beállítás arra kényszeríti a felhasználót, hogy a házirend minden végrehajtásakor a teljes felhasználói folyamaton keresztül fusson.

### <a name="session-life-time"></a>Munkamenet élettartamának ideje

A **munkamenet élettartama** azt az időtartamot használja, ameddig a Azure ad B2C munkamenet-cookie-t a sikeres hitelesítés után a felhasználó böngészőjében tárolja a rendszer. A munkamenet élettartama 15 és 720 perc közötti értékre állítható be.

### <a name="keep-me-signed-in"></a>Bejelentkezve szeretnék maradni

A [bejelentkezett szolgáltatás megtartása](custom-policy-keep-me-signed-in.md) funkció kiterjeszti a munkamenet élettartamát egy állandó cookie használatával. A munkamenet aktív marad, miután a felhasználó bezárja és újra megnyitja a böngészőt. A munkamenetet csak akkor vonja vissza a rendszer, ha a felhasználó kijelentkezik. A bejelentkezett eszköz bejelentkezve funkció csak helyi fiókkal történő bejelentkezésre vonatkozik.

A bejelentkezett eszköz megtartása beállítás elsőbbséget élvez a munkamenet élettartama során. Ha a Keep Me bejelentkezett funkció engedélyezve van, és a felhasználó kiválasztja azt, akkor ez a szolgáltatás a munkamenet lejárati idejét határozza meg. 

### <a name="session-expiry-type"></a>Munkamenet lejárati típusa

A **munkamenet lejárati típusa** azt jelzi, hogy a munkamenetet hogyan hosszabbítják meg a munkamenet élettartama beállítás vagy a bejelentkezett üzenet megtartása beállítás.

- **Gördülő** – azt jelzi, hogy a munkamenet minden alkalommal ki van-e terjesztve, amikor a felhasználó elvégez egy cookie-alapú hitelesítést (alapértelmezett).
- **Absolute** – azt jelzi, hogy a felhasználónak a megadott időszak után újra hitelesítenie kell magát.

## <a name="sign-out"></a>Kijelentkezés

Ha ki szeretné írni a felhasználót az alkalmazásból, nem elég az alkalmazás cookie-jait törölni, vagy más módon leállítani a munkamenetet a felhasználóval. A kijelentkezéshez át kell irányítani a felhasználót a Azure AD B2C. Ellenkező esetben előfordulhat, hogy a felhasználó újra hitelesíteni tudja az alkalmazásait anélkül, hogy újra be kellene írnia a hitelesítő adatait.

Kijelentkezési kérelem esetén Azure AD B2C:

1. A Azure AD B2C cookie-alapú munkamenet érvénytelenítése.
1. Kísérlet az összevont identitás-szolgáltatókból való kijelentkezésre:
   - OpenId Connect – ha az identitás-szolgáltató jól ismert konfigurációs végpontja megad egy `end_session_endpoint` helyet.
   - SAML – ha az Identity Provider metaadatai tartalmazzák a `SingleLogoutService` helyet.
1. Opcionálisan kijelentkezhet más alkalmazásokból is. További információ: az [egyszeri kijelentkezési](#single-sign-out) szakasz.

A kijelentkezési szolgáltatás törli a felhasználó egyszeri bejelentkezési állapotát Azure AD B2C, de előfordulhat, hogy nem írja alá a felhasználót a közösségi identitás-szolgáltatói munkamenetből. Ha a felhasználó ugyanazt az identitás-szolgáltatót választja egy későbbi bejelentkezés során, akkor a hitelesítő adatok megadása nélkül is újrahitelesíthetők. Ha a felhasználó ki szeretne jelentkezni az alkalmazásból, nem feltétlenül jelenti azt, hogy ki szeretné jelentkezni a Facebook-fiókjából. Ha azonban helyi fiókokat használ, a felhasználó munkamenete megfelelően végződik.

### <a name="single-sign-out"></a>Egyszeri kijelentkezés 


> [!NOTE]
> Ez a szolgáltatás [Egyéni szabályzatokra](custom-policy-overview.md)korlátozódik.

Amikor átirányítja a felhasználót a Azure AD B2C kijelentkezési végpontra (a OAuth2 és az SAML protokollok esetében egyaránt), Azure AD B2C törli a felhasználó munkamenetét a böngészőből. Előfordulhat azonban, hogy a felhasználó továbbra is be van jelentkezve más alkalmazásokba, amelyek az Azure AD B2C-t használják a hitelesítéshez. Ha engedélyezni szeretné, hogy az alkalmazások egyidejűleg írják alá a felhasználót, Azure AD B2C egy HTTP GET kérelmet küld az `LogoutUrl` összes olyan alkalmazás regisztrálásához, amelyhez a felhasználó jelenleg be van jelentkezve.


Az alkalmazásoknak válaszolnia kell erre a kérelemre a felhasználót azonosító munkamenetek törlésével `200` és a válasz visszaadásával. Ha az alkalmazásban szeretné támogatni az egyszeri kijelentkezést, az alkalmazás kódjában végre `LogoutUrl` kell hajtania egy alkalmazást. 

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan [konfigurálhatja a munkamenet viselkedését a felhasználói folyamatokban](session-behavior.md).
- Megtudhatja, hogyan [konfigurálhatja a munkamenet viselkedését az egyéni házirendekben](session-behavior-custom-policy.md).