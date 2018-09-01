---
title: Jogkivonat, munkamenet és egyszeri bejelentkezés beállításainak az Azure Active Directory B2C |} A Microsoft Docs
description: Jogkivonat, munkamenet és egyszeri bejelentkezés beállításainak az Azure Active Directory B2C-t.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/16/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 061e2257200b6d660a421a86c540f43597112c5e
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43337885"
---
# <a name="azure-active-directory-b2c-token-session-and-single-sign-on-configuration"></a>Azure Active Directory B2C: Jogkivonat, munkamenet és egyszeri bejelentkezés konfigurálása

Ez a funkció lehetővé teszi részletesebb szabályozás érdekében az egy [házirend alapon](active-directory-b2c-reference-policies.md), a:

1. A biztonsági jogkivonatokat az Azure Active Directory (Azure AD) B2C által kibocsátott élettartamának.
2. Azure AD B2C által felügyelt webes alkalmazás munkamenetek élettartamát.
3. A biztonsági jogkivonatokat az Azure AD B2C által kibocsátott jogcímek fontos formátumát.
4. Egyszeri bejelentkezés (SSO) viselkedését több alkalmazást és a szabályzatok a B2C-bérlőben.

Beépített szabályzatokat használhatja ezt a szolgáltatást az Azure AD B2C-címtárát a következő:

1. Az alábbi lépéseket követve [lépjen a B2C Funkciók menü](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) az Azure Portalon.
2. Kattintson a **regisztrálási vagy bejelentkezési szabályzatok**. * Megjegyzés: Használhatja ezt a szolgáltatást minden olyan házirendtípus, nem csak a ** regisztrálási vagy bejelentkezési szabályzatok x.
3. Nyissa meg a szabályzat ehhez kattintson rá. Kattintson például a **B2C_1_SiUpIn**.
4. Kattintson a **szerkesztése** a menü tetején.
5. Kattintson a **jogkivonatok, munkamenetek és egyszeri bejelentkezési konfiguráció**.
6. A kívánt módosításokat. Ismerje meg az ezt követő szakaszokban elérhető tulajdonságok.
7. Kattintson az **OK** gombra.
8. Kattintson a **mentése** a menü felső részén.

## <a name="token-lifetimes-configuration"></a>Jogkivonatok élettartamának konfigurálása

Az Azure AD B2C támogatja a [OAuth 2.0 engedélyezési protokollt](active-directory-b2c-reference-protocols.md) védett erőforrásokhoz való biztonságos hozzáférés engedélyezéséhez. Ez a támogatás megvalósítása, az Azure AD B2C-t bocsát ki különböző [biztonsági jogkivonatokat](active-directory-b2c-reference-tokens.md). A tulajdonságok segítségével kezelheti az Azure AD B2C által kibocsátott biztonsági jogkivonat élettartama a következők:

* **Hozzáférési és azonosító jogkivonat élettartama (perc)**: a védett erőforrások eléréséhez használt OAuth 2.0 tulajdonosi jogkivonat élettartama.
  * Alapértelmezett = 60 perc.
  * Minimális (inkluzív) = 5 perc.
  * (A szélsőértékek megengedettek) legfeljebb 1440 perc =.
* **Frissítési jogkivonat élettartama (nap)**: az a maximális időtartam, ameddig egy új hozzáférési vagy azonosító jogkivonat beszerzésére egy frissítési jogkivonat használható (és szükség esetén egy új frissítési jogkivonat, ha az alkalmazás volt megadva a `offline_access` hatókör).
  * Alapértelmezett = 14 nap.
  * Minimális (inkluzív) = 1 nap.
  * (A szélsőértékek megengedettek) maximális = 90 nap.
* **Frissítési jogkivonat csúszóablak-élettartama (nap)**: Ez az időtartam elteltével a felhasználónak újra hitelesíteni kell, attól függetlenül, az érvényességi időtartam legutóbbi frissítése az alkalmazás által beszerzett jogkivonattal kényszeríti. Ez csak biztosítható, hogy ha a kapcsoló beállítása **kötött**. Nagyobb vagy azzal egyenlőnek kell a **frissítési jogkivonat élettartama (nap)** értéket. Ha a kapcsoló beállítása **Unbounded**, nem adhat meg egy adott érték.
  * Alapértelmezett = 90 nap.
  * Minimális (inkluzív) = 1 nap.
  * (A szélsőértékek megengedettek) legfeljebb 365 nap.

Használati esetek, hogy ezek a tulajdonságok használatával engedélyezheti néhány az alábbiak:

* Engedélyezi, hogy egy felhasználó határozatlan ideig mobilalkalmazás bejelentkezve marad mindaddig, amíg ő a folyamatosan aktív, az alkalmazás a. Ezt megteheti úgy is a **frissítési jogkivonat csúszóablak-élettartama (nap)** váltson **Unbounded** a bejelentkezési házirend.
* Az iparági biztonsági és megfelelőségi követelmények felel meg a megfelelő hozzáférési jogkivonatok élettartamának beállításával.

    > [!NOTE]
    > Ezek a beállítások nem érhetők el, a jelszó-átállítási házirendeket.
    > 
    > 

## <a name="token-compatibility-settings"></a>Biztonságijogkivonat-kompatibilitási beállítások

Formázási módosításokat végeztünk el fontos biztonsági jogkivonatokat az Azure AD B2C által kibocsátott jogcímeket. Ez azért volt szükség, a standard szintű protokoll támogatása javítása és a jobb együttműködés külső identitás-kódtárakat. Azonban ha el szeretné kerülni a meglévő alkalmazások, létrehoztunk lehetővé teszi ügyfeleink számára vehetnek részt igény szerint a következő tulajdonságokkal:

* **Kibocsátói (iss) jogcím**: Ez azonosítja az Azure AD B2C-bérlő, amely kiállította a jogkivonatot.
  * `https://<domain>/{B2C tenant GUID}/v2.0/`: Ez az az alapértelmezett érték.
  * `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/`: Ez az érték a B2C-bérlő és a házirend jogkivonat a kérelemben használt azonosítókat tartalmazza. Ha az alkalmazás vagy könyvtár van szüksége az Azure AD B2C-vel meg kell felelnie az a [OpenID Connect-felderítési 1.0 specifikáció](http://openid.net/specs/openid-connect-discovery-1_0.html), használja ezt az értéket.
* **Tárgy (sub) jogcím**: Ez azonosítja az entitást, azaz a felhasználó, amelynek a token használjon esetleg imperatív állításokat információkat.
  * **ObjectID**: Ez az az alapértelmezett érték. A címtárban, a felhasználó Objektumazonosítóját feltölti a `sub` a jogkivonatban található jogcímek.
  * **Nem támogatott**: Ez csak a visszamenőleges kompatibilitás érdekében biztosított. emellett javasoljuk, hogy váltson át a **ObjectID** , amint tudunk.
* **Házirend Azonosítóját jelölő jogcím**: Ez azonosítja a jogcímtípus, amelybe a házirend-azonosító jogkivonat a kérelemben használt fel van töltve.
  * **tfp**: Ez az az alapértelmezett érték.
  * **ACR**: Ez csak a visszamenőleges kompatibilitás érdekében biztosított. emellett javasoljuk, hogy váltson át a `tfp` , amint tudunk.

## <a name="session-behavior"></a>Munkamenet-viselkedés

Az Azure AD B2C támogatja a [OpenID Connect hitelesítési protokoll](active-directory-b2c-reference-oidc.md) biztonságos bejelentkezési webes alkalmazásokhoz való engedélyezéséhez szükséges. A webes alkalmazás munkamenetek kezelésére használható tulajdonságok a következők:

* **Webalkalmazás munkamenet élettartama (perc)**: a felhasználó böngészőjében a sikeres hitelesítést követően tárolt Azure AD B2C munkameneti cookie élettartama.
  * Alapértelmezett = 1440 perc.
  * Minimális (inkluzív) = 15 perc.
  * (A szélsőértékek megengedettek) legfeljebb 1440 perc =.
* **Webalkalmazás munkamenet-időkorlátja**: Ha ez a kapcsoló beállítása **abszolút**, a felhasználónak újra hitelesíteni kell a megadott időszak után kötelező **webalkalmazás munkamenet élettartama (perc)** telik. Ha ez a kapcsoló beállítása **működés közbeni** (az alapértelmezett beállítás), a felhasználó bejelentkezve marad mindaddig, amíg a felhasználó a webalkalmazásban folyamatosan aktív.

Használati esetek, hogy ezek a tulajdonságok használatával engedélyezheti néhány az alábbiak:

* Megfelel az iparági biztonsági és megfelelőségi követelményeknek a megfelelő webes alkalmazás munkamenet beállításával élettartama.
* Egy felhasználó és a magas biztonsági szintű részét a webes alkalmazás közötti interakció során beállított idő elteltével ismételt hitelesítés kényszerítése. 

    > [!NOTE]
    > Ezek a beállítások nem érhetők el, a jelszó-átállítási házirendeket.
    > 
    > 

## <a name="single-sign-on-sso-configuration"></a>Egyszeri bejelentkezés (SSO) konfigurációja
Ha több alkalmazás és a szabályzatok a B2C-bérlőben, felhasználói interakció érdekében kezelheti navigációt használ a **egyszeri bejelentkezési konfigurációjának** tulajdonság. A tulajdonsága a következő beállítások egyikére:

* **Bérlő**: Ez az alapértelmezett beállítása. Ezzel a beállítással lehetővé teszi több alkalmazásokat és házirendeket az ugyanazon felhasználói munkamenet megosztani a B2C-bérlőben. Például ha egy felhasználó bejelentkezik, egy alkalmazásba, Contoso vásárlás, ő is zökkenőmentesen be tud jelentkezni egy másik egy, Contoso Gyógyszertári, elérésekor.
* **Alkalmazás**: Ez lehetővé teszi, hogy kizárólag az alkalmazáshoz, független más alkalmazásokat a felhasználói munkamenet fenntartásához. Például a (azonos adatokkal), a Contoso Gyógyszertári bejelentkezni a felhasználó azt szeretné, akkor is, ha ő már aláírt Contoso vásárlás be, ha egy másik alkalmazás ugyanazon B2C-bérlőben. 
* **A házirend**: Ez lehetővé teszi, hogy kizárólag a házirendet, az azt használó alkalmazások független a felhasználói munkamenet fenntartásához. Például ha a felhasználó már bejelentkezett, és egy többszörös többtényezős hitelesítés (MFA) lépés befejeződött, ő is hozzáférést kell biztosítani több alkalmazások magasabb biztonsági részeinek mindaddig, amíg a szabályzat kapcsolódik a munkamenet le nem jár.
* **Letiltott**: Ez kényszeríti a teljes felhasználói interakciósorozat keresztül futtathatók a szabályzat minden végrehajtás a felhasználó. Például ez lehetővé teszi több felhasználó számára, hogy jelentkezzen be az alkalmazás (a megosztott asztali forgatókönyvek esetében), még akkor is, amikor egyetlen felhasználó marad az aláírt teljes ideje alatt.

    > [!NOTE]
    > Ezek a beállítások nem érhetők el, a jelszó-átállítási házirendeket.
    > 
    > 

