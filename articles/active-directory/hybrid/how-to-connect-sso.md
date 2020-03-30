---
title: 'Azure AD Connect: Zökkenőmentes egyszeri bejelentkezés | Microsoft dokumentumok'
description: Ez a témakör ismerteti az Azure Active Directory (Azure AD) zökkenőmentes egyszeri bejelentkezést, és azt, hogy hogyan teszi lehetővé, hogy valódi egyszeri bejelentkezést biztosítson a vállalati asztali felhasználók számára a vállalati hálózaton belül.
services: active-directory
keywords: mi az Azure AD Connect, telepítse az Active Directoryt, szükséges összetevőket az Azure AD, Egyszeri bejelentkezés, Egyszeri bejelentkezés
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1b7e4716e731e6b73e3ac60b64baa71043906fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77483754"
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Az Azure Active Directory zökkenőmentes egyszeri bejelentkezése

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Mi az Azure Active Directory zökkenőmentes egyszeri bejelentkezés?

Az Azure Active Directory közvetlen egyszeri bejelentkezése (Azure AD közvetlen SSO) automatikusan bejelentkezteti a felhasználókat, ha azok a vállalati hálózatra csatlakozó vállalati eszközeiket használják. Ha engedélyezve van, a felhasználóknak nem kell beírniuk a jelszavukat az Azure AD-be való bejelentkezéshez, és általában még a felhasználónevüket is be kell írniuk. A szolgáltatás használatával a felhasználók könnyen elérhetik a felhőalapú alkalmazásokat anélkül, hogy ehhez további helyszíni összetevőkre lenne szükség.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

A zökkenőmentes egyszeri bejelentkezés kombinálható a [jelszókivonat-szinkronizálással](how-to-connect-password-hash-synchronization.md) vagy az [átmenő hitelesítés bejelentkezési](how-to-connect-pta.md) módszerekkel. A zökkenőmentes egyszeri bejelentkezés _nem_ alkalmazható az Active Directory összevonási szolgáltatásokra (ADFS).

![Zökkenőmentes egyszeri bejelentkezés](./media/how-to-connect-sso/sso1.png)

>[!IMPORTANT]
>A zökkenőmentes egyszeri bejelentkezéshez a felhasználó eszközének csak tartományhoz való **csatlakoztatása** szükséges, de nem használható az [Azure AD-hez vagy](../devices/concept-azure-ad-join.md) hibrid [Azure AD-hez csatlakozott](../devices/concept-azure-ad-join-hybrid.md) eszközökön. Egyszeri szolgáltatás az Azure AD-hez, és a hibrid Azure AD csatlakozott az [elsődleges frissítési jogkivonat](../devices/concept-primary-refresh-token.md)on.

## <a name="key-benefits"></a>Főbb előnyök

- *Nagyszerű felhasználói élmény*
  - A felhasználók automatikusan bejelentkeznek mind a helyszíni, mind a felhőalapú alkalmazásokba.
  - A felhasználóknak nem kell ismételten megadniuk a jelszavukat.
- *Könnyen telepíthető & felügyelet*
  - A helyszíni műveletekhez nincs szükség további összetevőkre.
  - A felhőalapú hitelesítés bármely módszerével működik - [Jelszókivonat-szinkronizálás](how-to-connect-password-hash-synchronization.md) vagy [átmenő hitelesítés](how-to-connect-pta.md).
  - A csoportházirend segítségével néhány vagy az összes felhasználó számára elérhető.
  - Regisztráljon nem Windows 10-es eszközöket az Azure AD-vel anélkül, hogy bármilyen AD FS-infrastruktúrára lenne szükség. Ehhez a funkcióhoz a munkahelyi csatlakozásügyfél 2.1-es vagy újabb verzióját kell [használnia.](https://www.microsoft.com/download/details.aspx?id=53554)

## <a name="feature-highlights"></a>Kiemelt funkciók

- A bejelentkezési felhasználónév lehet a helyszíni alapértelmezett felhasználónév (`userPrincipalName`) vagy az Azure`Alternate ID`AD Connect ( ) szolgáltatásban konfigurált másik attribútum. Mindkét használati eset működik, mert `securityIdentifier` a zökkenőmentes egyszeri bejelentkezés a Kerberos-jegyben lévő jogcímet használja a megfelelő felhasználói objektum azure AD-ben való kikereséséhez.
- A zökkenőmentes egyszeri bejelentkezés egy opportunista funkció. Ha bármilyen okból meghibásodik, a felhasználói bejelentkezési élmény visszatér a szokásos viselkedéséhez – azaz a felhasználónak meg kell adnia a jelszavát a bejelentkezési oldalon.
- Ha egy alkalmazás `https://myapps.microsoft.com/contoso.com`(például ) `domain_hint` továbbít egy (OpenID Connect) vagy `whr` (SAML) `login_hint` paramétert – a bérlő vagy a paraméter azonosítását – a felhasználó azonosítására az Azure AD bejelentkezési kérelemben, a felhasználók automatikusan bejelentkeznek anélkül, hogy felhasználóneveket vagy jelszavakat adnának be.
- A `https://contoso.sharepoint.com`felhasználók akkor is kapnak egy csendes bejelentkezési élményt, `https://login.microsoftonline.com/contoso.com/<..>` ha egy alkalmazás (például ) bejelentkezési kérelmeket küld `https://login.microsoftonline.com/<tenant_ID>/<..>` az Azure AD bérlőként beállított végpontjainak – azaz - az Azure AD közös végpontja helyett –, `https://login.microsoftonline.com/common/<...>`azaz.
- A kijelentkezés támogatott. Ez lehetővé teszi a felhasználók számára, hogy válasszanak egy másik Azure AD-fiókot, hogy jelentkezzen be, ahelyett, hogy automatikusan bejelentkezik a zökkenőmentes egyszeri bejelentkezés automatikusan.
- A 16.0.8730.xxxx és újabb verziókkal rendelkező Office 365 Win32-ügyfelek (Outlook, Word, Excel és mások) nem interaktív folyamattal támogatottak. A OneDrive-hoz aktiválnia kell a [OneDrive csendes konfigurációs funkcióját](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) a csendes bejelentkezési élmény érdekében.
- Az Azure AD Connecten keresztül engedélyezhető.
- Ez egy ingyenes funkció, és nem kell semmilyen fizetett kiadása az Azure AD-hez, hogy használja.
- A böngészőalapú ügyfelek és az Office-ügyfelek támogatják a [modern hitelesítést](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) a Kerberos-hitelesítésre képes platformokon és böngészőkben:

| OS\Böngésző |Internet Explorer|Microsoft Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|igen\*|Igen|Igen|Igen\*\*\*|N/A
|Windows 8.1|igen\*|N/A|Igen|Igen\*\*\*|N/A
|Windows 8|igen\*|N/A|Igen|Igen\*\*\*|N/A
|Windows 7|igen\*|N/A|Igen|Igen\*\*\*|N/A
|Windows Server 2012 R2 vagy újabb|igen\*\*|N/A|Igen|Igen\*\*\*|N/A
|Mac OS X|N/A|N/A|Igen\*\*\*|Igen\*\*\*|Igen\*\*\*


\*Az Internet Explorer 10-es vagy újabb verziójának megigénylő

\*\*Az Internet Explorer 10-es vagy újabb verzióját igényli. Fokozottan védett mód letiltása

\*\*\*[További konfigurációt](how-to-connect-sso-quick-start.md#browser-considerations) igényel

>[!NOTE]
>A Windows 10 esetében az [azure AD Join](../devices/concept-azure-ad-join.md) használata az Azure AD optimális egyszeri bejelentkezési élményéhez.

## <a name="next-steps"></a>További lépések

- [**Gyors indítás**](how-to-connect-sso-quick-start.md) – Az Azure AD seamless sso üzembe helyezett szolgáltatása.
- [**Telepítési terv**](https://aka.ms/deploymentplans/sso) – Lépésről lépésre történő telepítési terv.
- [**Műszaki Deep Dive**](how-to-connect-sso-how-it-works.md) - Ismerje meg, hogyan működik ez a funkció.
- [**Gyakori kérdések**](how-to-connect-sso-faq.md) – Válaszok a gyakran ismételt kérdésekre.
- [**Hibaelhárítás**](tshoot-connect-sso.md) – Ismerje meg, hogyan oldhatja meg a szolgáltatással kapcsolatos gyakori problémákat.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - A bejelentés új funkció kérelmeket.

