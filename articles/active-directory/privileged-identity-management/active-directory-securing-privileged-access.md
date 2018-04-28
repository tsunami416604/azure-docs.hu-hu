---
title: Emelt szintű hozzáférés biztosítása az Azure AD |} Microsoft Docs
description: Ez a témakör ismerteti a privileged access Azure, az Azure Active Directory és a Microsoft Online Services védelmének.
services: active-directory
documentationcenter: ''
author: barclayn
manager: mtillman
editor: mwahl
ms.assetid: 235a0ce9-1daf-4433-8f65-9c6afcd64d08
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: kgremban
ms.custom: pim
ms.openlocfilehash: f49d5a629b9643df81253c57ee8e3458b43b0c59
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="securing-privileged-access-in-azure-ad"></a>Az Azure Active Directory biztonságossá tétele a privilegizált hozzáférési jogosultsága.
Emelt szintű hozzáférés biztonságossá tétele Ez kritikus első lépés egy modern szervezet üzleti eszközök védelme érdekében. Kiemelt jogosultságú fiókok azok a fiókok, felügyelheti és kezelheti az informatikai rendszerek. A támadók számítógépes ezeket a fiókokat ahhoz, hogy hozzáférjenek a szervezetek adatokhoz és rendszerek célként. Emelt szintű hozzáférés biztonságossá tételéhez, a fiókok és a kockázat, hogy egy rosszindulatú felhasználó rendszert kell különíteni.

További felhasználók emelt szintű hozzáférés keresztül felhőszolgáltatások eléréséhez fizet. Ilyen lehet például az Office365 globális rendszergazdák, Azure-előfizetés rendszergazdák és felhasználók rendszergazdai hozzáféréssel rendelkező virtuális gépek vagy SaaS-alkalmazásokhoz.

A Microsoft azt javasolja, hogy kövesse a terv [emelt szintű hozzáférés biztonságossá tétele](https://technet.microsoft.com/library/mt631194.aspx).

A felhasználó Azure Active Directory, Office 365, vagy más Microsoft-szolgáltatások és alkalmazások esetén az alapelvek alkalmazni, hogy a felhasználói fiókok által kezelt és hitelesítése az Active Directory vagy az Azure Active Directoryban. Az alábbi szakaszokban további információk az Azure AD-funkciókat támogató emelt szintű hozzáférés biztonságossá tétele.

## <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication
A rendszergazdai hitelesítési biztonság növelése érdekében jogosultságok megadása előtt a kétlépéses ellenőrzés elvégzéséhez szükséges. Kétlépéses ellenőrzés egy metódus ellenőrzésére, akik meg, amelyek nem csak a felhasználónév és jelszó használatát igényli. A második biztonsági szintként, a felhasználói bejelentkezéseket és tranzakciókat tartalmaz.

Az Azure multi-factor Authentication (MFA) a Microsoft kétlépéses ellenőrzés megoldás, mely segít hozzáférés biztonságossá tételét adatokhoz és alkalmazásokhoz a felhasználói betartása mellett egy egyszerű bejelentkezési folyamat keresletének. Erős hitelesítés, többek között könnyen ellenőrzési lehetőségek széles keresztül biztosítja:

- telefonhívásokat
- Szöveges üzenetek
- mobilalkalmazás-értesítések
- mobilalkalmazás ellenőrző kódok kezelésére
- Harmadik féltől származó OATH jogkivonatokkal

Tekintse át az alábbi videó az Azure multi-factor Authentication működésének áttekintése:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Windows-Azure-Multi-Factor-Authentication/player]

További információkért lásd: [többtényezős hitelesítés az Office 365 és az Azure MFA](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/).

## <a name="time-bound-privileges"></a>Időhöz kötött jogosultságokkal
Egyes szervezetek tapasztalhatja, hogy túl sok felhasználó rendelkeznek magas szintű jogosultsággal rendelkező szerepkört. A felhasználó előfordulhat, hogy érhetőek el a szerepkör egy adott tevékenységet, például a Feliratkozás a szolgáltatás azonban nem használja ezeket az engedélyeket gyakran ezt követően.

Jogosultságokat a kitettség idő csökkentése, és növelje a használatukat lássák, korlátozza a felhasználók számára csak véve a jogosultságait az "igény szerint" (JIT), vagy rendeljen hozzá ezeket a szerepköröket egy rövidebb időtartam a folyamatot a jogosultságok automatikusan visszavonja. Az Azure Active Directory, az Azure-erőforrások (előzetes verzió) és a Microsoft Online Services, használhatja [az Azure AD Privileged Identity Management (PIM)](https://aka.ms/AzurePIM).

![A PIM irányítópult][2]

## <a name="attack-detection"></a>Észlelt támadás
[Az Azure Active Directory Identity Protection](../active-directory-identityprotection.md) a kockázati eseményekről és a szervezet identitásait érintő lehetséges biztonsági rések egyesített nézetét biztosítja. Identity Protection kockázati események alapján számítja ki minden felhasználó, amely lehetővé teszi, kockázati-alapú házirendek automatikusan védő a szervezete egy felhasználó kockázati szintjét. Ezek a házirendek más feltételes hozzáférés-szabályozási EMS, és az Azure Active Directory által biztosított automatikusan a felhasználó letiltásához vagy ad javaslatokat, amelyek tartalmazzák a jelszó alaphelyzetbe állítását és a többtényezős hitelesítés kényszerítése.

![Azure AD Identity Protection][3]

## <a name="conditional-access"></a>Feltételes hozzáférés
Feltételes hozzáférés-vezérlést Azure Active Directory ellenőrzi a megadott feltételek úgy dönt, hogy a felhasználó hitelesítéséhez az alkalmazáshoz való hozzáférés engedélyezése előtt. Ha ezek a feltételek teljesülnek, a felhasználó hitelesítése és hozzáférni az alkalmazáshoz engedélyezett.

## <a name="related-articles"></a>Kapcsolódó cikkek
* Engedélyezése [Azure többtényezős hitelesítés](../authentication/howto-mfa-getstarted.md)
* Engedélyezése [az Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-configure.md)
* Engedélyezése [Azure AD Identity Protection](../active-directory-identityprotection.md)
* Engedélyezése [feltételes hozzáférés-vezérlést](../active-directory-conditional-access-azure-portal.md)

Egy teljes biztonsági terv felépítésével további információkért lásd: az "ügyfél feladatkörei és terv" részében a [vállalati fejlesztők a Microsoft Cloud biztonsági](https://aka.ms/securecustomer) dokumentum. Bővebben módon vegye fel a Microsoft-szolgáltatások az alábbi témakörök egyik segít, forduljon a Microsoft képviselőjével, vagy keresse fel a [számítógépes megoldások lap](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx).

<!--Image references-->
[1]: ../media/active-directory-privileged-identity-management-configure/Search_PIM.png
[2]: ../media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ../media/active-directory-identityprotection/29.png
