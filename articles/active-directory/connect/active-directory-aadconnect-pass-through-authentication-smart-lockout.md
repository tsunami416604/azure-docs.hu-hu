---
title: "Az Azure AD Connect: Áteresztő hitelesítés – intelligens zárolás |} Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan Azure Active Directory (Azure AD) áteresztő hitelesítés védje a találgatásos jelszó támadások ellen, a felhőben a helyszíni fiókok."
services: active-directory
keywords: "Az Azure AD Connect áteresztő hitelesítés, telepítés Active Directory szükséges összetevőket az Azure AD, SSO, egyszeri bejelentkezést."
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: billmath
ms.openlocfilehash: 771741fd7da8c9b6932851851aaca148f9596643
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2017
---
# <a name="azure-active-directory-pass-through-authentication-smart-lockout"></a>Az Azure Active Directory áteresztő hitelesítés: Az intelligens zárolás

## <a name="overview"></a>Áttekintés

Az Azure AD találgatásos jelszó támadások ellen, és megakadályozza, hogy a valódi felhasználók az Office 365 és az SaaS-alkalmazások kívül zárás alatt. Ez a funkció hívása **intelligens zárolás**, akkor támogatott, ha átmenő hitelesítést, a bejelentkezési módszer használatát. Intelligens zárolás alapértelmezés szerint az összes bérlőre vonatkozó engedélyezve van, és védeni, a felhasználói fiókok folyamatosan; Kapcsolja be, szükség van.

Intelligens zárolás működik, hogy nyomon követi a sikertelen bejelentkezési kísérletek, és egy bizonyos után **bejelentkezési próbálkozásra van lehetőségük**kezdődően egy **kizárás időtartama**. A támadó a kizárás időtartama alatt a bejelentkezési kísérletek utasítja el. Ha nem szűnik meg a támadás, az ezt követő sikertelen bejelentkezési kísérletek a kizárás időtartama leteltével fiókzárolási hosszabb időtartamokat eredményez.

>[!NOTE]
>Az alapértelmezett bejelentkezési próbálkozásra van lehetőségük 10 sikertelen bejelentkezési kísérletek, és a kizárás időtartama alapértelmezett érték 60 másodperc.

Intelligens zárolás is különböztet bejelentkezéseket és a támadók valódi felhasználók és a támadók a legtöbb esetben csak zárolja. Ez a funkció megakadályozza, hogy a támadók ártó zárolják a valódi felhasználók. A valódi felhasználók és a támadók megkülönböztetéséhez bejelentkezési viselkedés, a felhasználói eszközökön & böngészők és egyéb jelekkel részhez használjuk. Azt a rendszer folyamatosan javítása az algoritmusok.

Áteresztő hitelesítés továbbítja a jelszó érvényesítése kérelmek alakzatot a helyszíni Active Directory (AD), mert egy támadó a a felhasználók AD-fiókok zárolásának szüksége. Mivel a saját AD fiókzárolási házirendek (pontosabban [ **fiókzárolás küszöbértékénél** ](https://technet.microsoft.com/library/hh994574(v=ws.11).aspx) és [ **alaphelyzetbe fiók zárolása számláló után házirendek**](https://technet.microsoft.com/library/hh994568(v=ws.11).aspx)), kell konfigurálnia az Azure AD bejelentkezési próbálkozásra van lehetőségük és fiókzárolási Duration típusú értékek megfelelően kiszűrhetők a felhőben támadások, a helyszíni elérése előtti AD.

>[!NOTE]
>Az intelligens zárolás funkciót szabad és _a_ alapértelmezés szerint az összes ügyfél számára. Azonban az Azure AD bejelentkezési próbálkozásra van lehetőségük és fiókzárolási Duration típusú értékek Graph API-jával módosítása kell legalább egy Azure AD Premium P2 licence a bérlő. Nem kell az Azure AD Premium P2 licencek _felhasználónként_ átmenő hitelesítéssel intelligens zárolási funkció eléréséhez.

Annak érdekében, hogy a felhasználók a helyszíni AD-fiókok védettek, győződjön meg arról, hogy:

1.  Az Azure AD bejelentkezési próbálkozásra van lehetőségük van _kevesebb_ mint AD a fiókzárolás küszöbértékénél. Azt javasoljuk, hogy az értékeket úgy, hogy a fiókzárolás küszöbértékénél AD meg legalább két vagy három alkalommal fordult elő az Azure AD bejelentkezési próbálkozásra van lehetőségük.
2.  Az Azure AD kizárás időtartama (másodpercben jelölt) _hosszabb_ mint AD meg alaphelyzetbe fiók zárolása számláló után (összességében percben megadva).

>[!IMPORTANT]
>Jelenleg a rendszergazda nem fiókok zárolásának feloldása a felhasználók felhőalapú ha azok zárolva van az intelligens zárolás funkció. Várjon, amíg a kizárás időtartama lejár kell.

## <a name="verify-your-ad-account-lockout-policies"></a>Ellenőrizze a fiók zárolása AD házirendek

Az AD-fiókot fiókzárolási házirendek ellenőrzéséhez használja az alábbi utasításokat:

1.  Nyissa meg a Csoportházirend kezelése eszközt.
2.  Szerkessze a csoportházirendet, amelyet az összes felhasználóra, például az alapértelmezett tartományi házirend vonatkozik.
3.  Nyissa meg a számítógép konfigurációja\Házirendek\A beállításai\Biztonsági beállítások\Fiókházirend\Fiókzárolási házirend.
4.  Ellenőrizze a fiókzárolás küszöbértékénél és alaphelyzetbe fiók zárolása számláló után értékeket.

![AD fiókzárolási házirendek](./media/active-directory-aadconnect-pass-through-authentication/pta5.png)

## <a name="use-the-graph-api-to-manage-your-tenants-smart-lockout-values-needs-premium-license"></a>A Graph API-val kezelheti a bérlő intelligens zárolás értékek (licencre van szüksége prémium)

>[!IMPORTANT]
>Az Azure AD bejelentkezési próbálkozásra van lehetőségük és fiókzárolási Duration típusú értékek Graph API-jával módosítása az Azure AD Premium P2 szolgáltatása. Azt is, hogy a bérlő globális rendszergazdának lennie kell.

Használhat [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) olvassa, állítsa be, és az Azure AD intelligens zárolás értékeinek frissítéséhez. Azonban programozott módon ezeket a műveleteket is van.

### <a name="read-smart-lockout-values"></a>Olvassa el az intelligens zárolás értékek

Kövesse az alábbi lépéseket kiolvasni a bérlő intelligens zárolás értékeket:

1. A bérlő globális rendszergazdaként jelentkezzen be arra Graph Explorer. Ha a rendszer kéri, engedélyezheti a hozzáférést a kért engedélyeket.
2. "Engedélyek módosítása" gombra, és válassza a "Directory.ReadWrite.All" engedéllyel.
3. Az alábbiak szerint konfigurálhatja a Graph API-kérelem: "Béta" kérelemtípus verzió beállítása a "GET" és az URL-címe `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`.
4. Kattintson a "Lekérdezés futtatása" a bérlő intelligens zárolás értékeit. Ha a bérlő értékek előtt még nem állított, lásd: üres.

### <a name="set-smart-lockout-values"></a>Intelligens zárolás értékeinek megadása

Kövesse az alábbi lépéseket az beállítását a bérlő intelligens zárolás értékek (csak az első alkalommal):

1. A bérlő globális rendszergazdaként jelentkezzen be arra Graph Explorer. Ha a rendszer kéri, engedélyezheti a hozzáférést a kért engedélyeket.
2. "Engedélyek módosítása" gombra, és válassza a "Directory.ReadWrite.All" engedéllyel.
3. Az alábbiak szerint konfigurálhatja a Graph API-kérelem: a "Béta" verzió, kéréstípus "POST" és az URL-címe `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`.
4. Másolja és illessze be a következő JSON-kérelmi "Kérelemtörzset" mező.
5. Kattintson a "Lekérdezés futtatása" a bérlő intelligens zárolás értékeinek beállításához.

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
>Ha nem használ őket, hagyja a **BannedPasswordList** és **EnableBannedPasswordCheck** szerinti üres érték ("") és "false" osztályban.

Győződjön meg arról, hogy beállította-e a bérlő intelligens zárolás értékek megfelelően [ezeket a lépéseket](#read-smart-lockout-values).

### <a name="update-smart-lockout-values"></a>Intelligens zárolás értékek frissítése

Kövesse az alábbi lépéseket a bérlői intelligens zárolás értékek frissítése (Ha be őket előtt):

1. A bérlő globális rendszergazdaként jelentkezzen be arra Graph Explorer. Ha a rendszer kéri, engedélyezheti a hozzáférést a kért engedélyeket.
2. "Engedélyek módosítása" gombra, és válassza a "Directory.ReadWrite.All" engedéllyel.
3. [Kövesse az alábbi lépéseket a bérlő intelligens zárolás értékek olvasni](#read-smart-lockout-values). Másolás a `id` szolgáltatáskéréshez"", "PasswordRuleSettings" elem (GUID) értékét.
4. Az alábbiak szerint konfigurálhatja a Graph API-kérelem: "Béta" kérelemtípus verzió beállítása a "Javítás" és az URL-címe `https://graph.microsoft.com/beta/<your-tenant-domain>/settings/<id>` -3. lépés a GUID-Azonosítójának használata `<id>`.
5. Másolja és illessze be a következő JSON-kérelmi "Kérelemtörzset" mező. Az intelligens zárolás értékeket szükség szerint módosítható.
6. Kattintson a "Lekérdezés futtatása" a bérlő intelligens zárolás értékeinek frissítéséhez.

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

Győződjön meg arról, hogy a bérlő intelligens zárolás értékek megfelelően frissítette [ezeket a lépéseket](#read-smart-lockout-values).

## <a name="next-steps"></a>Következő lépések
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – új funkciókérések tárolásához.
