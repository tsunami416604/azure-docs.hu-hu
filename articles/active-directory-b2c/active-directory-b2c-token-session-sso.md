---
title: Jogkivonat, munkamenet és egyszeri bejelentkezés beállításainak az Azure Active Directory B2C |} A Microsoft Docs
description: Jogkivonat, munkamenet és egyszeri bejelentkezés beállításainak az Azure Active Directory B2C-t.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 456e32e2f5194417f004f80feef1852dd3d0befd
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52723278"
---
# <a name="token-session-and-single-sign-on-configuration-in-azure-active-directory-b2c"></a>Jogkivonat, munkamenet és egyszeri bejelentkezés beállításainak az Azure Active Directory B2C-vel

Ez a funkció lehetővé teszi részletesebb szabályozás érdekében az egy [folyamat felhasználónkénti](active-directory-b2c-reference-policies.md), a:

- A biztonsági jogkivonatokat az Azure Active Directory (Azure AD) B2C által kibocsátott élettartamának.
- Azure AD B2C által felügyelt webes alkalmazás munkamenetek élettartamát.
- A biztonsági jogkivonatokat az Azure AD B2C által kibocsátott jogcímek fontos formátumát.
- Egyszeri bejelentkezés (SSO) viselkedését több alkalmazást és az Azure AD B2C-bérlőben a felhasználói folyamatok.

Használhatja ezt a szolgáltatást minden olyan házirendtípus, de ebben a példában megjelenítése a szolgáltatás használata egy regisztrálási vagy bejelentkezési felhasználói folyamata. Felhasználói folyamatok használhatja ezt a szolgáltatást az Azure AD B2C-címtárát a következő:

1. Kattintson a **felhasználókövetési adatai**.
2. Nyissa meg a felhasználói folyamat ehhez kattintson rá. Kattintson például a **B2C_1_SiUpIn**.
3. Kattintson a **Tulajdonságok** elemre.
4. A **jogkivonat-kompatibilitási beállítások**, végezze el a szükséges módosításokat. Ismerje meg az ezt követő szakaszokban elérhető tulajdonságok.
5. Kattintson a **mentése** a menü felső részén.

## <a name="token-lifetimes-configuration"></a>Jogkivonatok élettartamának konfigurálása

Az Azure AD B2C támogatja a [OAuth 2.0 engedélyezési protokollt](active-directory-b2c-reference-protocols.md) védett erőforrásokhoz való biztonságos hozzáférés engedélyezéséhez. Ez a támogatás megvalósítása, az Azure AD B2C-t bocsát ki különböző [biztonsági jogkivonatokat](active-directory-b2c-reference-tokens.md). 

A következő tulajdonságok segítségével felügyelhető az Azure AD B2C által kibocsátott biztonsági jogkivonat élettartama:

- **Hozzáférési és azonosító jogkivonat élettartama (perc)** – a védett erőforrások eléréséhez az OAuth 2.0 tulajdonosi jogkivonat élettartama.
    - Alapértelmezett = 60 perc.
    - Minimális (inkluzív) = 5 perc.
    - (A szélsőértékek megengedettek) legfeljebb 1440 perc =.
- **Frissítési jogkivonat élettartama (nap)** – az a maximális időtartam, ameddig egy új hozzáférési vagy azonosító jogkivonat beszerzésére egy frissítési jogkivonat használható (és szükség esetén egy új frissítési jogkivonat, ha az alkalmazás volt megadva a `offline_access` hatókör).
    - Alapértelmezett = 14 nap.
    - Minimális (inkluzív) = 1 nap.
    - (A szélsőértékek megengedettek) maximális = 90 nap.
- **Frissítési jogkivonat csúszóablak-élettartama (nap)** - e a felhasználónak újra hitelesíteni kell, attól függetlenül, az érvényességi időtartam legutóbbi frissítése az alkalmazás által beszerzett jogkivonattal kötelező idő időszak eltelte után. Ez csak biztosítható, hogy ha a kapcsoló beállítása **kötött**. Nagyobb vagy azzal egyenlőnek kell a **frissítési jogkivonat élettartama (nap)** értéket. Ha a kapcsoló beállítása **Unbounded**, nem adhat meg egy adott érték.
    - Alapértelmezett = 90 nap.
    - Minimális (inkluzív) = 1 nap.
    - (A szélsőértékek megengedettek) legfeljebb 365 nap.

A következő használati esetek engedélyezve vannak ezek a tulajdonságok használatával:

- Maradjon bejelentkezve a egy mobilalkalmazásba határozatlan ideig felhasználó engedélyezése mindaddig, amíg a felhasználó az alkalmazás folyamatosan aktív. Beállíthat **frissítési jogkivonat csúszóablak-élettartama (nap)** való **Unbounded** a bejelentkezési felhasználói folyamat.
- Az iparági biztonsági és megfelelőségi követelmények felel meg a megfelelő hozzáférési jogkivonatok élettartamának beállításával.

Ezek a beállítások a jelszó-átállítási felhasználókövetési adatai nem érhetők el. 

## <a name="token-compatibility-settings"></a>Biztonságijogkivonat-kompatibilitási beállítások

A következő tulajdonságok engedélyezése az ügyfelek számára, hogy engedélyezve van a hibajelentések igény szerint:

- **Kibocsátói (iss) jogcím** – Ez a tulajdonság azonosítja az Azure AD B2C-bérlő, amely kiállította a jogkivonatot.
    - `https://<domain>/{B2C tenant GUID}/v2.0/` – Ez az az alapértelmezett érték.
    - `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` – Ez az érték a B2C-bérlő és a felhasználói folyamatot a token kérésben használt azonosítókat tartalmazza. Ha az alkalmazás vagy könyvtár van szüksége az Azure AD B2C-vel meg kell felelnie az a [OpenID Connect-felderítési 1.0 specifikáció](http://openid.net/specs/openid-connect-discovery-1_0.html), használja ezt az értéket.
- **Tárgy (sub) jogcím** – Ez a tulajdonság azonosítja az entitást, amelyhez a token használjon esetleg imperatív állításokat információkat.
    - **ObjectID** – Ez a tulajdonság alapértelmezett értéke. A címtárban, a felhasználó Objektumazonosítóját feltölti a `sub` a jogkivonatban található jogcímek.
    - **Nem támogatott** – Ez a tulajdonság csak a visszamenőleges kompatibilitás érdekében biztosított, és azt javasoljuk, hogy a Váltás **ObjectID** , amint tudunk.
- **Házirend Azonosítóját jelölő jogcím** – Ez a tulajdonság azonosítja a jogcímtípus, amelybe a házirend-azonosító jogkivonat a kérelemben használt fel van töltve.
    - **tfp** – Ez a tulajdonság alapértelmezett értéke.
    - **ACR** – Ez a tulajdonság csak a visszamenőleges kompatibilitás érdekében biztosított.

## <a name="session-behavior"></a>Munkamenet viselkedése

Az Azure AD B2C támogatja a [OpenID Connect hitelesítési protokoll](active-directory-b2c-reference-oidc.md) biztonságos bejelentkezési webes alkalmazásokhoz való engedélyezéséhez szükséges. A következő tulajdonságok használatával webes alkalmazás munkameneteket kezelhessen:

- **Webalkalmazás munkamenet élettartama (perc)** – a felhasználó böngészőjében a sikeres hitelesítést követően tárolt Azure AD B2C munkameneti cookie élettartama.
    - Alapértelmezett = 1440 perc.
    - Minimális (inkluzív) = 15 perc.
    - (A szélsőértékek megengedettek) legfeljebb 1440 perc =.
- **Webalkalmazás munkamenet-időkorlátja** – Ha ez a kapcsoló beállítása **abszolút**, a felhasználónak kötelező a megadott időszak után újra hitelesíteni **webalkalmazás munkamenet élettartama (perc)** telik. Ha ez a kapcsoló beállítása **működés közbeni** (az alapértelmezett beállítás), a felhasználó bejelentkezve marad mindaddig, amíg a felhasználó a webalkalmazásban folyamatosan aktív.

A következő használati esetek engedélyezve vannak ezek a tulajdonságok használatával:

- Megfelel az iparági biztonsági és megfelelőségi követelményeknek a megfelelő webes alkalmazás munkamenet beállításával élettartama.
- Egy felhasználó és a magas biztonsági szintű részét a webes alkalmazás közötti interakció során beállított idő elteltével hitelesítés kényszerítése. 

Ezek a beállítások a jelszó-átállítási felhasználókövetési adatai nem érhetők el.

## <a name="single-sign-on-sso-configuration"></a>Egyszeri bejelentkezés (SSO) konfigurációja

Ha több alkalmazás és a felhasználói folyamatok a B2C-bérlőben, felhasználói interakció érdekében kezelheti navigációt használ a **egyszeri bejelentkezési konfigurációjának** tulajdonság. A tulajdonsága a következő beállítások egyikére:

- **Bérlő** – Ez a beállítás az alapértelmezett érték. Ezzel a beállítással lehetővé teszi, hogy több alkalmazás és a felhasználói folyamatok megosztani az ugyanazon felhasználói munkamenetet a B2C-bérlőben. Például ha egy felhasználó bejelentkezik, egy alkalmazásba, a felhasználó is zökkenőmentesen be tud jelentkezni egy másik egy, Contoso Gyógyszertári, elérésekor.
- **Alkalmazás** – Ez a beállítás lehetővé teszi, hogy kizárólag az alkalmazáshoz, független más alkalmazásokat a felhasználói munkamenetek fenntartását. Például ha szeretné a felhasználót, hogy jelentkezzen be a Contoso Gyógyszertári (ugyanazokat a hitelesítő adatokat), akkor is, ha a felhasználó már bejelentkezett Contoso vásárlás, egy másik alkalmazás ugyanazon B2C bérlői. 
- **A házirend** – Ez a beállítás lehetővé teszi, hogy kizárólag a felhasználói folyamat, az azt használó alkalmazások független a felhasználói munkamenetek fenntartását. Például ha a felhasználó már bejelentkezett, és egy többszörös többtényezős hitelesítés (MFA) lépés befejeződött, a felhasználónak is hozzáférést kell biztosítani több alkalmazás részei magasabb biztonsági mindaddig, amíg a felhasználói folyamat kapcsolódik a munkamenet le nem jár.
- **Letiltott** – Ez a beállítás kényszeríti a felhasználót, hogy a szabályzat minden végrehajtás révén a teljes felhasználói folyamatot futtat. Például ez lehetővé teszi több felhasználó számára, hogy jelentkezzen be az alkalmazás (a megosztott asztali forgatókönyvek esetében), még akkor is, amikor egyetlen felhasználó marad az aláírt teljes ideje alatt.

Ezek a beállítások a jelszó-átállítási felhasználókövetési adatai nem érhetők el. 

