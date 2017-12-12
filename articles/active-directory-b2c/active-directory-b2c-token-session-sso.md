---
title: "Token, munkamenet és egyszeri bejelentkezés konfigurálása - az Azure AD B2C |} Microsoft Docs"
description: "Token, munkamenet és egyszeri bejelentkezés konfigurálása az Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: mtillman
editor: parakhj
ms.assetid: e78e6344-0089-49bf-8c7b-5f634326f58c
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: parakhj
ms.openlocfilehash: 76feb172ce9b08d4feef9c86e74a592553a3c7f4
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-token-session-and-single-sign-on-configuration"></a>Az Azure Active Directory B2C: Token, munkamenet és egyszeri bejelentkezés konfigurálása

Ez a funkció lehetővé teszi részletes vezérlés, a egy [házirend alapon](active-directory-b2c-reference-policies.md), a:

1. Azure Active Directory (Azure AD) B2C által kibocsátott biztonsági jogkivonatok élettartama.
2. A webes alkalmazás munkamenetek kezeli az Azure AD B2C élettartamának.
3. Fontos jogcím szerepel a biztonsági jogkivonatokat az Azure AD B2C által kibocsátott formátumban.
4. Egyszeri bejelentkezés (SSO) viselkedés több alkalmazások és házirendek a B2C-bérlőben.

Beépített házirendek használhatja ezt a szolgáltatást a Azure AD B2C-címtárban az alábbiak szerint:

1. Az alábbi lépéseket követve [lépjen a B2C Funkciók menü](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) az Azure portálon.
2. Kattintson a **regisztráció vagy bejelentkezés házirendek**. *Megjegyzés: Használható ez a szolgáltatás bármely házirend típus nem csupán a **regisztráció vagy bejelentkezés házirendek***.
3. Egy házirend megnyitásához kattintson rá. Kattintson például a **B2C_1_SiUpIn**.
4. Kattintson a **szerkesztése** a menü tetején.
5. Kattintson a **Token, a munkamenet és az egyszeri bejelentkezés config**.
6. A kívánt módosításokat. További információk a rendelkezésre álló tulajdonságok az ezt követő szakaszok.
7. Kattintson az **OK** gombra.
8. Kattintson a **mentése** a menü felső részén található.

## <a name="token-lifetimes-configuration"></a>Token élettartama konfiguráció

Az Azure AD B2C támogatja a [OAuth 2.0 protokoll](active-directory-b2c-reference-protocols.md) védett erőforrások biztonságos hozzáférést tesz lehetővé. Ez a támogatás végrehajtásához az Azure AD B2C bocsát ki különböző [biztonsági jogkivonatokat](active-directory-b2c-reference-tokens.md). A biztonsági jogkivonatokat az Azure AD B2C által kibocsátott élettartamának kezeléséhez használható tulajdonságok a következők:

* **& Azonosító elérése token élettartama (perc)**: az OAuth 2.0 tulajdonosi jogkivonat élettartamát használt védett erőforrásokhoz való hozzáférést.
  * Alapértelmezett = 60 perc.
  * (A határokat is beleértve) minimális = 5 perc.
  * (A határokat is beleértve) legfeljebb 1440 perc =.
* **Frissítse a jogkivonatok élettartama (nap)**: az maximális időtartamot, ameddig egy frissítési jogkivonat segítségével szerezzen be egy új hozzáférés vagy az azonosító token (és szükség esetén egy új frissítési jogkivonat, ha az alkalmazás megadták a `offline_access` hatókör).
  * Alapértelmezett = 14 nap.
  * (A határokat is beleértve) minimális = 1 nap.
  * (A határokat is beleértve) maximális = 90 nap.
* **Frissítse mozgó ablakban az élettartam (nap)**: Ebben az időszakban elteltével a felhasználónak újra hitelesíteni, függetlenül a legutóbbi érvényességi frissítse az alkalmazás által igényelt jogkivonat kényszeríti. Azt is csak adni, ha a kapcsoló értéke **Bounded**. Nagyobb vagy azzal egyenlőnek kell a **frissítési jogkivonat élettartamát (nap)** érték. Ha a kapcsoló értéke **Unbounded**, nem adhat meg egy adott értéket.
  * Alapértelmezett = 90 nap.
  * (A határokat is beleértve) minimális = 1 nap.
  * (A határokat is beleértve) legfeljebb 365 nap =.

Az alábbiak néhány engedélyezheti az ezekkel a tulajdonságokkal használati esethez:

* Amikor engedélyezi határozatlan ideig mobilalkalmazás bejelentkezve marad mindaddig, amíg nem folyamatosan aktív, az alkalmazás. Ehhez úgy, hogy a **frissítési jogkivonat mozgó ablak élettartamát (nap)** váltani **Unbounded** a bejelentkezési házirend.
* Az iparági biztonsági és megfelelőségi követelményeknek megfelelő úgy, hogy a megfelelő hozzáférési jogkivonat élettartamát.

    > [!NOTE]
    > Ezek a beállítások nem érhetők el, a jelszó-átállítási házirendek.
    > 
    > 

## <a name="token-compatibility-settings"></a>Token kompatibilitási beállítások

A Microsoft módosítja formázási az Azure AD B2C által kibocsátott biztonsági jogkivonatokba fontos jogcímeket. Ez végezhető el a szabványos protokoll támogatása javítása és a külső azonosító könyvtárak jobb együttműködés érdekében. Azonban ha el szeretné kerülni meglévő alkalmazások, létrehozott segítségével a felhasználók részt igény szerint a következő tulajdonságokat:

* **Jogcím kiállítója (iss)**: Ez az Azure AD B2C-bérlő a jogkivonatot kibocsátó azonosítja.
  * `https://login.microsoftonline.com/{B2C tenant GUID}/v2.0/`: Ez az az alapértelmezett érték.
  * `https://login.microsoftonline.com/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/`: Ez az érték azonosítók egyaránt tartalmazza a B2C-bérlő és a házirendet, amelyet a token kérés. Ha az alkalmazás vagy a könyvtárban kell-e az Azure AD B2C meg kell felelnie a [OpenID Connect felderítési 1.0 spec](http://openid.net/specs/openid-connect-discovery-1_0.html), használja ezt az értéket.
* **Tulajdonos (rész-) jogcím**: Ez azonosítja az entitást, azaz, a felhasználó, amelynek a token állításokat információkat.
  * **ObjectID**: Ez az az alapértelmezett érték. Azt a felhasználót a címtárban történő Objektumazonosítója tölti fel a `sub` jogcím a tokenben.
  * **Nem támogatott**: Ez csak a visszamenőleges kompatibilitás érdekében biztosítja, és azt javasoljuk, hogy vált **ObjectID** , amint lehet.
* **Jogcím-házirend-azonosító képviselő**: Ez azonosítja a jogcímtípus, amelybe a rendszer a házirend-azonosító, amelyet a token kérés tölti fel.
  * **tfp**: Ez az az alapértelmezett érték.
  * **ACR**: Ez csak a visszamenőleges kompatibilitás érdekében biztosítja, és azt javasoljuk, hogy vált `tfp` , amint lehet.

## <a name="session-behavior"></a>Munkamenet viselkedése

Az Azure AD B2C támogatja a [OpenID Connect hitelesítési protokoll](active-directory-b2c-reference-oidc.md) engedélyezéséhez a biztonságos bejelentkezés a webes alkalmazásokhoz. A webes alkalmazás munkameneteket kezelhessen segítségével tulajdonságai a következők:

* **A webalkalmazás munkamenet élettartama (perc)**: Azure AD B2C munkamenetcookie-t a sikeres hitelesítést követően a felhasználó böngészője tárolt élettartamát.
  * Alapértelmezett = 1440 perc.
  * (A határokat is beleértve) minimális = 15 perc.
  * (A határokat is beleértve) legfeljebb 1440 perc =.
* **Webes alkalmazás munkamenet időkorlátja**: Ha a kapcsoló értéke **abszolút**, a felhasználó által megadott idő elteltével újra hitelesíteni kényszeríti **webalkalmazás munkamenet élettartama (perc)** eltelt. Ha a kapcsoló értéke **működés közbeni** (az alapértelmezett beállítás), a felhasználó bejelentkezett marad mindaddig, amíg a felhasználó a webalkalmazásban folyamatosan aktív.

Az alábbiak néhány engedélyezheti az ezekkel a tulajdonságokkal használati esethez:

* Az iparági biztonsági és megfelelőségi követelményeknek megfelelő úgy, hogy a megfelelő webes alkalmazás munkamenet élettartama.
* Egy felhasználó és a magas biztonsági részét a webes alkalmazás közötti interakció során beállított idő elteltével ismételt hitelesítés kényszerítése. 

    > [!NOTE]
    > Ezek a beállítások nem érhetők el, a jelszó-átállítási házirendek.
    > 
    > 

## <a name="single-sign-on-sso-configuration"></a>Egyszeri bejelentkezés (SSO) konfigurálása
Ha a B2C-bérlő több alkalmazásokat és házirendeket is van, felhasználói interakció mindegyik kezelheti használatával a **egyszeri bejelentkezés konfigurációs** tulajdonság. Beállíthatja a tulajdonság a következő beállítások egyikét:

* **Bérlői**: Ez az alapértelmezett beállítása. Ezzel a beállítással lehetővé teszi több alkalmazásokat és házirendeket a B2C bérlőre, az azonos felhasználói munkamenet megosztásához. Például után a felhasználó bejelentkezik egy alkalmazásba, Contoso vásárlás, általa is is zökkenőmentesen jelentkezzen be egy másik egy, a Contoso gyógyszerészet, esetén használja.
* **Alkalmazás**: Ez lehetővé teszi, hogy a felhasználói munkamenet kizárólag az alkalmazáshoz, független a többi alkalmazás karbantartása. Például ha a felhasználót, hogy jelentkezzen be Contoso Gyógyszertári (ugyanazokat a hitelesítő adatokat), azt szeretné, akkor is, ha az őt van már be van jelentkezve Contoso vásárlás, egy másik alkalmazás ugyanazon B2C bérlői. 
* **Házirend**: Ez lehetővé teszi, hogy kizárólag a házirend, az azt használó alkalmazások független a felhasználói munkamenet fenntartásához. Például ha a felhasználó már bejelentkezett, és a többszörös többtényezős hitelesítés (MFA) lépés befejezése, általa is kell való hozzáférése több alkalmazás részei magasabb biztonsági mindaddig, amíg a házirend társítva a munkamenethez nem jár le.
* **Letiltott**: Ez arra kényszeríti a felhasználó minden végrehajtási házirend a teljes felhasználói út keresztül futtatásához. Például ez lehetővé teszi több felhasználó regisztrálnia kell az alkalmazást (egy megosztott asztali esetén), még akkor is, amikor egy felhasználó marad bejelentkezett a teljes időszak.

    > [!NOTE]
    > Ezek a beállítások nem érhetők el, a jelszó-átállítási házirendek.
    > 
    > 

