---
title: 'Az Azure AD Connect: Zökkenőmentes egyszeri bejelentkezést |} Microsoft Docs'
description: Ez a témakör ismerteti az Azure Active Directory (Azure AD) zökkenőmentes egyszeri bejelentkezést és hogyan lehetővé teszi, hogy valódi egyszeri bejelentkezést a vállalati asztali felhasználók a vállalati hálózaton belüli adja meg.
services: active-directory
keywords: Mi az Azure AD Connect telepítés Active Directory szükséges összetevőket az Azure AD, SSO, egyszeri bejelentkezést.
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: billmath
ms.openlocfilehash: 6a106ba59f09ffc20f8461c5267ae06695df4cd0
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Az Azure Active Directory zökkenőmentes egyszeri bejelentkezést.

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Mi az az Azure Active Directory zökkenőmentes egyszeri bejelentkezést?

Azure Active Directory zökkenőmentes egyszeri bejelentkezést (az Azure AD zökkenőmentes SSO) amikor kapcsolódnak a vállalati eszközök csatlakoznak a vállalati hálózathoz a felhasználók bejelentkezésekor automatikusan. Ha engedélyezve van, a felhasználók nem kell írja be a jelszavát, jelentkezzen be az Azure AD, és általában, akkor írja be a felhasználónevek. Ez a funkció a felhőalapú alkalmazások egyszerű hozzáférést biztosít a felhasználók anélkül, hogy semmilyen további helyszíni összetevőt.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Zökkenőmentes SSO kombinálva, vagy a [Jelszókivonat-szinkronizálást](active-directory-aadconnectsync-implement-password-hash-synchronization.md) vagy [áteresztő hitelesítés](active-directory-aadconnect-pass-through-authentication.md) bejelentkezési módszerek.

![Zökkenőmentes egyszeri bejelentkezést.](./media/active-directory-aadconnect-sso/sso1.png)

>[!IMPORTANT]
>Zökkenőmentes SSO _nem_ alkalmazható az Active Directory összevonási szolgáltatások (ADFS).

## <a name="key-benefits"></a>Főbb előnyök

- *Kiváló felhasználói élmény*
  - Felhasználók automatikusan bejelentkezett mind a helyszíni és felhőalapú alkalmazásokat.
  - Adja meg ismételten a jelszavukat a felhasználóknak nem kell.
- *Könnyen üzembe helyezése és felügyelete*
  - További összetevők szükségesek a helyszíni működnek.
  - A felhőalapú hitelesítés - bármely metódusát együttműködve [Jelszókivonat-szinkronizálást](active-directory-aadconnectsync-implement-password-hash-synchronization.md) vagy [áteresztő hitelesítés](active-directory-aadconnect-pass-through-authentication.md).
  - Egyes kell eldöntése vagy csoportházirend segítségével minden felhasználó.
  - Windows 10-eszközök regisztrálása nélkül bármely AD FS infrastruktúra Azure AD-val. Ez a funkció arra kéri, hogy 2.1-es vagy újabb verzióját használja a [munkahelyhez ügyfél](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="feature-highlights"></a>A szolgáltatás kiemelések

- Bejelentkezési felhasználónév vagy a helyszíni alapértelmezett felhasználónév lehet (`userPrincipalName`) vagy az Azure AD Connect konfigurált egy másik attribútum (`Alternate ID`). Mindkét esetben a munkahelyi használható, mert a zökkenőmentes egyszeri Bejelentkezést használ a `securityIdentifier` ellenőrizzék a megfelelő felhasználói objektum, az Azure ad-ben a Kerberos jegyet a jogcímek.
- Zökkenőmentes SSO az alkalmi szolgáltatása. Ha bármilyen okból nem sikerül, a felhasználói bejelentkezési élmény visszatér a rendszeres viselkedését - Egytényezős, a felhasználó adja meg a jelszót a bejelentkezési oldalon kell.
- Ha egy alkalmazás (például https://myapps.microsoft.com/contoso.com) továbbítja a `domain_hint` (OpenID Connect) vagy `whr` (SAML) paraméter - azonosító a bérlő vagy `login_hint` paraméter - a felhasználói azonosító az Azure AD bejelentkezési kérelem, a felhasználók vannak automatikusan megtörténik a őket belépés felhasználónév vagy jelszó nélkül.
- A felhasználók is kapnak a beavatkozás nélküli bejelentkezést, ha egy alkalmazás (például https://contoso.sharepoint.com) bejelentkezési kérelmek küldése az Azure AD-végpontok központjaként – Ez azt jelenti, hogy https://login.microsoftonline.com/contoso.com/<..> vagy https://login.microsoftonline.com/<tenant_ID>/<..> - helyett az Azure AD közös végpont - Ez azt jelenti, hogy https://login.microsoftonline.com/common/<...>.
- Kijelentkezési esetén támogatott. Ez lehetővé teszi a felhasználók kiválaszthatják a bejelentkezéshez, zökkenőmentes SSO használatával automatikusan automatikusan aláírás alatt álló helyett egy másik Azure AD-fiókot.
- Office 365-ügyfelek (16.0.8730.xxxx és újabb) használata támogatott a nem interaktív folyamat használatával.
- Az Azure AD Connect használatával engedélyezhető.
- Egy szabad szolgáltatást, és nem kell használni az Azure AD bármely fizetős verziója.
- A webes webböngésző-alapú ügyfelek és a támogató Office-ügyfelek támogatott [modern hitelesítést](https://aka.ms/modernauthga) platformok és böngészők képes a Kerberos-hitelesítés:

| OS\Browser |Internet Explorer|Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Igen|Nem|Igen|igen\*|–
|Windows 8.1|Igen|–|Igen|igen\*|–
|Windows 8|Igen|–|Igen|igen\*|–
|Windows 7|Igen|–|Igen|igen\*|–
|Mac OS X|–|–|igen\*|igen\*|igen\*

\*Szükséges [további konfigurációs](active-directory-aadconnect-sso-quick-start.md#browser-considerations)

>[!NOTE]
>Windows 10, a ajánljuk, hogy használjon [az Azure AD Join](../active-directory-azureadjoin-overview.md) a optimális egyszeri bejelentkezéses felhasználói élmény biztosítása az Azure AD számára.

## <a name="next-steps"></a>További lépések

- [**Gyors üzembe helyezési** ](active-directory-aadconnect-sso-quick-start.md) - létrehozásához, és az Azure AD zökkenőmentes SSO futtatása.
- [**Műszaki mélyreható** ](active-directory-aadconnect-sso-how-it-works.md) – Ez a funkció működésének megismerése.
- [**Gyakran ismételt kérdések** ](active-directory-aadconnect-sso-faq.md) -gyakran feltett kérdésekre adott válaszokat.
- [**Hibaelhárítás** ](active-directory-aadconnect-troubleshoot-sso.md) -Útmutató: a szolgáltatással kapcsolatos gyakori problémák megoldása.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – új funkciókérések tárolásához.
