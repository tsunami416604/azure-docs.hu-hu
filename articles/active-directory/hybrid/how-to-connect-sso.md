---
title: 'Azure AD Connect: zökkenőmentes egyszeri bejelentkezés | Microsoft Docs'
description: Ez a témakör a Azure Active Directory (Azure AD) zökkenőmentes egyszeri bejelentkezését ismerteti, valamint azt, hogy a vállalati hálózaton belül hogyan biztosíthat valódi egyszeri bejelentkezést a vállalati asztali felhasználók számára.
services: active-directory
keywords: Mi az Azure AD Connect, az Azure AD, az egyszeri bejelentkezéshez szükséges összetevők telepítése Active Directory
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
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77483754"
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Zökkenőmentes egyszeri bejelentkezés Azure Active Directory

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Mi az Azure Active Directory zökkenőmentes egyszeri bejelentkezés?

Az Azure Active Directory közvetlen egyszeri bejelentkezése (Azure AD közvetlen SSO) automatikusan bejelentkezteti a felhasználókat, ha azok a vállalati hálózatra csatlakozó vállalati eszközeiket használják. Ha ez a beállítás engedélyezve van, a felhasználóknak nem kell beírniuk a jelszavukat, hogy bejelentkezzenek az Azure AD-be, és általában még begépelik a felhasználóneveket. A szolgáltatás használatával a felhasználók könnyen elérhetik a felhőalapú alkalmazásokat anélkül, hogy ehhez további helyszíni összetevőkre lenne szükség.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

A zökkenőmentes egyszeri bejelentkezést a jelszó- [kivonatoló szinkronizálással](how-to-connect-password-hash-synchronization.md) vagy az [átmenő hitelesítési](how-to-connect-pta.md) bejelentkezési módszerekkel kombinálhatja. A zökkenőmentes SSO _nem_ alkalmazható a Active Directory ÖSSZEVONÁSI szolgáltatások (AD FS) (ADFS) szolgáltatásra.

![Zökkenőmentes egyszeri bejelentkezés](./media/how-to-connect-sso/sso1.png)

>[!IMPORTANT]
>A zökkenőmentes egyszeri bejelentkezéshez a felhasználó eszközének csak **tartományhoz csatlakoztatottnak** kell lennie, de az [Azure ad-hez csatlakoztatott](../devices/concept-azure-ad-join.md) vagy [hibrid Azure ad-hez csatlakoztatott](../devices/concept-azure-ad-join-hybrid.md) eszközökön nem használható. Az Azure AD-hez csatlakoztatott és hibrid Azure AD-hez csatlakoztatott egyszeri bejelentkezés az [elsődleges frissítési jogkivonat](../devices/concept-primary-refresh-token.md)alapján működik.

## <a name="key-benefits"></a>Főbb előnyök

- *Nagyszerű felhasználói élmény*
  - A felhasználók automatikusan bejelentkeznek a helyszíni és a felhőalapú alkalmazásokba is.
  - A felhasználóknak nem kell ismételten megadniuk a jelszavukat.
- *Egyszerűen üzembe helyezhető & felügyelet*
  - Ehhez a működéshez nincs szükség a helyszínen további összetevőkre.
  - Bármilyen módszerrel használható a felhőalapú hitelesítéshez – [jelszó-kivonatolási szinkronizálás](how-to-connect-password-hash-synchronization.md) vagy [átmenő hitelesítés](how-to-connect-pta.md).
  - A Csoportházirend használatával egy vagy több felhasználóhoz is beállíthatók.
  - A nem Windows 10-es eszközök regisztrálása az Azure AD-vel anélkül, hogy AD FS infrastruktúrára lenne szükség. Ehhez a funkcióhoz a [munkahelyi csatlakoztatási ügyfél](https://www.microsoft.com/download/details.aspx?id=53554)2,1-es vagy újabb verzióját kell használnia.

## <a name="feature-highlights"></a>A funkciók Kiemelt jellemzői

- A bejelentkezési Felhasználónév lehet a helyszíni alapértelmezett Felhasználónév (`userPrincipalName`) vagy a Azure AD Connect (`Alternate ID`) szolgáltatásban konfigurált másik attribútum. Mindkét használati eset működik, mivel a zökkenőmentes egyszeri bejelentkezés a Kerberos-jegyben lévő `securityIdentifier` jogcímet használja a megfelelő felhasználói objektum kereséséhez az Azure AD-ben.
- A zökkenőmentes SSO egy opportunista funkció. Ha bármilyen okból nem sikerül, a felhasználói bejelentkezési élmény visszatér a szokásos viselkedésére – azaz a felhasználónak meg kell adnia a jelszavát a bejelentkezési oldalon.
- Ha egy alkalmazás (például `https://myapps.microsoft.com/contoso.com`) továbbítja a `domain_hint` (OpenID Connect) vagy a `whr` (SAML) paramétert a bérlő azonosítására, vagy `login_hint` paraméter – a felhasználó azonosítására az Azure AD bejelentkezési kérelmében, a felhasználók automatikusan bejelentkeznek anélkül, hogy felhasználóneveket vagy jelszavakat kellene beírniuk.
- A felhasználók akkor is csendes bejelentkezési élményt kapnak, ha egy alkalmazás (például `https://contoso.sharepoint.com`) olyan bejelentkezési kéréseket küld az Azure AD-végpontoknak, amelyek bérlőként vannak beállítva – azaz `https://login.microsoftonline.com/contoso.com/<..>` vagy `https://login.microsoftonline.com/<tenant_ID>/<..>` – az Azure AD közös végpontja helyett – azaz `https://login.microsoftonline.com/common/<...>`.
- A kijelentkezés támogatott. Így a felhasználók kiválaszthatnak egy másik Azure AD-fiókot a szolgáltatásba való bejelentkezéshez ahelyett, hogy automatikusan be kellene jelentkezniük a zökkenőmentes egyszeri bejelentkezés automatikus használatával.
- Az Office 365 Win32-ügyfelek (Outlook, Word, Excel és egyebek) nem interaktív folyamattal támogatottak a 16.0.8730. xxxx és újabb verziók használatával. A OneDrive a csendes bejelentkezési élmény érdekében aktiválni kell a [OneDrive csendes konfigurációs szolgáltatást](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) .
- Azure AD Connect használatával engedélyezhető.
- Ez egy ingyenes funkció, és nincs szüksége az Azure AD fizetős kiadásaira a használatához.
- A szolgáltatás olyan webböngésző-alapú ügyfelek és Office-ügyfelek számára támogatott, amelyek támogatják a [modern hitelesítést](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) a Kerberos-hitelesítésre képes platformokon és böngészőkön:

| OS\Browser |Internet Explorer|Microsoft Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Igen\*|Igen|Igen|Igen\*\*\*|N/A
|Windows 8.1|Igen\*|N/A|Igen|Igen\*\*\*|N/A
|Windows 8|Igen\*|N/A|Igen|Igen\*\*\*|N/A
|Windows 7|Igen\*|N/A|Igen|Igen\*\*\*|N/A
|Windows Server 2012 R2 vagy újabb|Igen\*\*|N/A|Igen|Igen\*\*\*|N/A
|Mac OS X|N/A|N/A|Igen\*\*\*|Igen\*\*\*|Igen\*\*\*


\*az Internet Explorer 10-es vagy újabb verzióját igényli

\*\*az Internet Explorer 10-es vagy újabb verzióját igényli. Fokozottan védett üzemmód letiltása

\*\*\*[további konfigurálást](how-to-connect-sso-quick-start.md#browser-considerations) igényel

>[!NOTE]
>A Windows 10 esetében ajánlott az Azure [ad JOIN](../devices/concept-azure-ad-join.md) használata az Azure ad-vel való optimális egyszeri bejelentkezéshez.

## <a name="next-steps"></a>Következő lépések

- [**Gyorskonfigurálás**](how-to-connect-sso-quick-start.md) – az Azure ad zökkenőmentes egyszeri bejelentkezésének megkezdése és futtatása.
- [**Üzembe helyezési terv**](https://aka.ms/deploymentplans/sso) – lépésenkénti üzembe helyezési terv.
- [**Technikai**](how-to-connect-sso-how-it-works.md) részletes útmutató – a funkció működésének megismerése.
- [**Gyakori kérdések**](how-to-connect-sso-faq.md) – válaszok a gyakori kérdésekre.
- [**Hibaelhárítás**](tshoot-connect-sso.md) – megismerheti a szolgáltatással kapcsolatos gyakori problémák megoldását.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – új szolgáltatásokra vonatkozó kérelmek bejelentése.

