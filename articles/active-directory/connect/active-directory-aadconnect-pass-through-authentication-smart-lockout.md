---
title: "Az Azure AD Connect: Áteresztő hitelesítés – intelligens zárolás |} Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan Azure Active Directory (Azure AD) áteresztő hitelesítés védje a találgatásos jelszó támadások ellen, a felhőben a helyszíni fiókok"
services: active-directory
keywords: "Az Azure AD Connect áteresztő hitelesítés, telepítés Active Directory szükséges összetevőket az Azure AD, SSO, egyszeri bejelentkezést."
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: billmath
ms.openlocfilehash: 9477d47824213d7ea15bcf6c6b615a220bae2e48
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="azure-active-directory-pass-through-authentication-smart-lockout"></a>Az Azure Active Directory áteresztő hitelesítés: Az intelligens zárolás

## <a name="overview"></a>Áttekintés

Azure Active Directory (Azure AD) jelszó találgatásos támadások ellen, és megakadályozza, hogy a valódi felhasználók az Office 365 és az SaaS-alkalmazások kívül zárás alatt. Ez a funkció hívása *intelligens zárolás*, akkor támogatott, ha átmenő hitelesítést, a bejelentkezési módszer használatát. Intelligens zárolás alapértelmezés szerint engedélyezve van az összes bérlőre, nem pedig csak bérlők áteresztő hitelesítés használatát, és folyamatosan védelmet nyújt a felhasználói fiókokat.

Intelligens zárolás nyomon követi a sikertelen bejelentkezési kísérletek. Egy bizonyos után *bejelentkezési próbálkozásra van lehetőségük*, elindul a *kizárás időtartama*. Intelligens zárolás elutasítja tett bármilyen kísérlet a támadó jelentkezzen be a kizárás időtartama alatt. Ha a támadás továbbra is fennáll, további sikertelen bejelentkezési kísérletek leteltével a kizárás időtartama eredménye a fiókzárolás hosszabb időtartamok.

>[!NOTE]
>Az alapértelmezett bejelentkezési próbálkozásra van lehetőségük 10 sikertelen bejelentkezési kísérletek. Az alapértelmezett zárolási beállítás 60 másodperc.

Intelligens zárolás is az eredeti felhasználó bejelentkezéseket és a támadók bejelentkezések különböztet. A legtöbb esetben ez megakadályozza, hogy csak a támadók. Ez a funkció megakadályozza, hogy a támadók ártó zárolják a valódi felhasználók. Intelligens zárolás túli bejelentkezési viselkedés, a felhasználói eszközök és böngészők használ, és egyéb jelzi a valódi felhasználók és a támadók megkülönböztetéséhez. Az algoritmusok folyamatosan növelése.

Áteresztő hitelesítés továbbítja a jelszó érvényesítése kérések a helyszíni Active Directory, ezért meg kell akadályozni, hogy a támadók a felhasználók Active Directory-fiókok zárolásának. Az Active Directory rendelkezik saját fiókzárolási házirendek, pontosabban [fiókzárolás küszöbértékénél](https://technet.microsoft.com/library/hh994574(v=ws.11).aspx) és [fiókzárolás számlálójának visszaállítása után](https://technet.microsoft.com/library/hh994568(v=ws.11).aspx) házirendek. Az Azure AD fiókzárolás küszöbértéke és fiókzárolási Duration típusú értékek megfelelő konfigurálása a helyszíni Active Directory elérése előtti támadások a felhőben kiszűrésére.

>[!NOTE]
>>Az intelligens zárolás funkciót szabad és _a_ alapértelmezés szerint az összes ügyfél számára. Azonban az Azure AD bejelentkezési próbálkozásra van lehetőségük és a Graph API-jával fiókzárolási Duration típusú értékek módosítása szükséges a bérlő aktiválódik az Azure AD Premium P2. 

Győződjön meg arról, hogy a felhasználók a helyszíni Active Directory-fiókokat is védett, ellenőrizze, hogy kell:

   * Az Azure AD bejelentkezési próbálkozásra van lehetőségük van _kevesebb_ mint az Active Directory számítógépfiókok zárolási küszöbértéke. Állítsa be az értékeket, úgy, hogy az Active Directory számítógépfiókok zárolási küszöbértéke legalább két vagy három alkalommal hosszabb, mint az Azure AD bejelentkezési próbálkozásra van lehetőségük.
   * Az Azure AD Fiókzárolási időtartam (másodpercben jelölt) _hosszabb_ , mint az Active Directory fiókzárolás számlálójának visszaállítása után időtartam (percben jelölt).

>[!IMPORTANT]
>Jelenleg a rendszergazda nem fiókok zárolásának feloldása a felhasználók felhőalapú ha azok zárolva van az intelligens zárolás funkció. A rendszergazdának meg kell várnia a kizárás időtartama lejár.

## <a name="verify-your-active-directory-account-lockout-policies"></a>Ellenőrizze az Active Directory fiókzárolási házirendek

Az Active Directory fiókzárolási házirendek ellenőrzéséhez használja az alábbi utasításokat:

1.  Nyissa meg a Csoportházirend kezelése eszközt.
2.  Szerkessze a csoportházirendet, amelyet vonatkozik az összes olyan felhasználót, például a **alapértelmezett tartományházirend**.
3.  Keresse meg a **számítógép konfigurációja** > **házirendek** > **Windows-beállítások** > **biztonsági beállítások**   >  **Fiókházirendek** > **fiókzárolási házirend**.
4.  Ellenőrizze a **fiókzárolás küszöbértékénél** és **fiókzárolás számlálójának visszaállítása után** értékeket.

![Active Directory fiókzárolási házirendek](./media/active-directory-aadconnect-pass-through-authentication/pta5.png)

## <a name="use-the-graph-api-to-manage-your-tenants-smart-lockout-values-requires-a-premium-license"></a>A Graph API-val kezelheti a bérlő intelligens zárolás értékek (Premium licenc szükséges)

>[!IMPORTANT]
>Az Azure AD fiókzárolás küszöbértéke és fiókzárolási Duration típusú értékek módosítása Graph API-jával az Azure AD Premium P2 szolgáltatása. Azt is, hogy a bérlő globális rendszergazdának lennie kell.

Használhat [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) olvassa, állítsa be, és az Azure AD intelligens zárolás értékeinek frissítéséhez. Ezek a műveletek programozott módon is végezhet.

### <a name="view-smart-lockout-values"></a>Intelligens zárolás értékeinek megtekintése

Kövesse az alábbi lépéseket a bérlői intelligens zárolás értékek megjelenítése:

1. Jelentkezzen be a bérlő globális rendszergazdaként Graph Explorer. Ha a számítógép, engedélyezheti a hozzáférést a kért engedélyeket.
2. Válassza ki **módosítsa az engedélyeket**, majd válassza ki a **Directory.ReadWrite.All** engedéllyel.
3. Az alábbiak szerint konfigurálhatja a Graph API-kérelem: a verzió **BETA**, a kérelem típus **beolvasása**, és az URL-címe `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`.
4. Válassza ki **lekérdezés futtatása** a bérlő intelligens zárolás értékeit. Ha a bérlő értékek előtt még nem állított, lásd: üres.

### <a name="set-smart-lockout-values"></a>Intelligens zárolás értékeinek megadása

A következőképpen állíthatja be a bérlő intelligens zárolás értékeket (csak az első alkalommal szükséges):

1. Jelentkezzen be a bérlő globális rendszergazdaként Graph Explorer. Ha a számítógép, engedélyezheti a hozzáférést a kért engedélyeket.
2. Válassza ki **módosítsa az engedélyeket**, majd válassza ki a **Directory.ReadWrite.All** engedéllyel.
3. Az alábbiak szerint konfigurálhatja a Graph API-kérelem: a verzió **BETA**, a kérelem típus **POST**, és az URL-címe `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`.
4. Másolja és illessze be a következő JSON-kérelmi azokat a **kérelemtörzset** mező.
5. Válassza ki **lekérdezés futtatása** a bérlő intelligens zárolás értékeinek beállításához.

```
{
  "templateId": "5cf42378-d67d-4f36-ba46-e8b86229381d",
  "values": [
    {
      "name": "LockoutDurationInSeconds",
      "value": "300"
    },
    {
      "name": "LockoutThreshold",
      "value": "5"
    },
    {
      "name" : "BannedPasswordList",
      "value": ""
    },
    {
      "name" : "EnableBannedPasswordCheck",
      "value": "false"
    }
  ]
}
```

>[!NOTE]
>Ha nem adja meg őket, hagyja a **BannedPasswordList** és **EnableBannedPasswordCheck** , üres értékek (**""**) és **hamis** kulcsattribútumokkal.

Győződjön meg arról, hogy helyesen van beállítva a bérlő intelligens zárolás értékek a lépések segítségével [nézet intelligens zárolás értékek](#view-smart-lockout-values).

### <a name="update-smart-lockout-values"></a>Intelligens zárolás értékek frissítése

Kövesse az alábbi lépéseket a bérlői intelligens zárolás értékek frissítése (Ha meg őket, mielőtt):

1. Jelentkezzen be a bérlő globális rendszergazdaként Graph Explorer. Ha a számítógép, engedélyezheti a hozzáférést a kért engedélyeket.
2. Válassza ki **módosítsa az engedélyeket**, majd válassza ki a **Directory.ReadWrite.All** engedéllyel.
3. [Kövesse az alábbi lépéseket a bérlői intelligens zárolás értékek megjelenítése](#view-smart-lockout-values). Másolás a `id` értékét (GUID) rendelkező elemet **displayName** , **PasswordRuleSettings**.
4. Az alábbiak szerint konfigurálhatja a Graph API-kérelem: a verzió **BETA**, a kérelem típus **javítás**, és az URL-címe `https://graph.microsoft.com/beta/<your-tenant-domain>/settings/<id>`. A 3. lépésben a GUID `<id>`.
5. Másolja és illessze be a következő JSON-kérelmi azokat a **kérelemtörzset** mező. Az intelligens zárolás értékeket szükség szerint módosítható.
6. Válassza ki **lekérdezés futtatása** a bérlő intelligens zárolás értékeinek frissítéséhez.

```
{
  "values": [
    {
      "name": "LockoutDurationInSeconds",
      "value": "30"
    },
    {
      "name": "LockoutThreshold",
      "value": "4"
    },
    {
      "name" : "BannedPasswordList",
      "value": ""
    },
    {
      "name" : "EnableBannedPasswordCheck",
      "value": "false"
    }
  ]
}
```

Győződjön meg arról, hogy frissítette a bérlő intelligens zárolás értékek megfelelően a lépések segítségével [nézet intelligens zárolás értékek](#view-smart-lockout-values).

## <a name="next-steps"></a>További lépések
[UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): az Azure Active Directory fórumán használja a következő fájl új frissítéseiről.
