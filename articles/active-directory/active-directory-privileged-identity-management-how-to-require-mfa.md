---
title: "Többtényezős hitelesítés megkövetelése |} Microsoft Docs"
description: "A kiemelt identitásokat az Azure Active Directory Privileged Identity Management bővítmény megtudhatja, hogyan többtényezős hitelesítést (MFA)."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 1e3dc4ad-3a6a-4a52-8417-3ca4f84ae05c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/06/2017
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: b778774fa23be8219db3f716d79bac324a7de9d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-require-mfa-in-azure-ad-privileged-identity-management"></a>Az Azure AD Privileged Identity Management többtényezős hitelesítés megkövetelése
Azt javasoljuk, hogy a rendszergazdák az összes szükséges többtényezős hitelesítés (MFA). Ez csökkenti a sérült biztonságú jelszó miatt a támadás kockázatát.

Megkövetelheti, hogy a felhasználók bejelentkezéskor befejeződnek az MFA-kérdést. A blogbejegyzést [többtényezős hitelesítés az Office 365 és az Azure MFA](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/) összehasonlítja az Office és az Azure-előfizetések, tartalmazza a Microsoft Azure multi-factor Authentication ajánlatban foglalt szolgáltatásait.

Is megkövetelheti, hogy a felhasználók, amikor azok az Azure AD PIM szerepkört aktiváló befejeződnek az MFA-kérdést. Ezzel a módszerrel a felhasználó nem fejeződött be az MFA-kérdést, ha azok jelentkezett be, ha kéri erre PIM által.

## <a name="requiring-mfa-in-azure-ad-privileged-identity-management"></a>Az Azure AD Privileged Identity Management a többtényezős hitelesítés megkövetelése
Ha Ön kezeli a PIM egy kiemelt szerepkörű rendszergazda identitások, riasztásokat, amelyek MFA javasoljuk a kiemelt jogosultságokkal rendelkező fiókok jelenhet meg. A biztonsági riasztást a PIM irányítópulton kattintson, és egy új panel nyílik meg a rendszergazdai fiókokat kell a többtényezős Hitelesítést igénylő listáját.  Megkövetelheti az MFA jelölje ki a több szerepkört, majd kattintson a **javítsa** gombra, vagy is kattintson folytatást jelző pontokra mellett adott szerepkörök, majd kattintson a **javítsa** gombra.

> [!IMPORTANT]
> Jobb gombbal a most Azure MFA csak akkor működik a munkahelyi vagy iskolai fiókjait, nem Microsoft-fiókok (általában egy személyes fiók, amellyel jelentkezzen be Microsoft-szolgáltatásokat, mint a Skype, Xbox, Outlook.com-os, stb.). Ebből kifolyólag bárki, aki a Microsoft-fiókkal nem lehet jogosult rendszergazdája, mert a szerepkörök aktiválásához nem használhatják a többtényezős Hitelesítést. Ha ezek a felhasználók kezelése a Microsoft-fiókkal munkaterhelések továbbra kell, jogosultságszint-emelés azokat állandó rendszergazdák most.
> 
> 

Ezenkívül egy adott szerepkör esetében a többtényezős hitelesítés követelménye módosíthatja a PIM irányítópult szerepkörök szakaszában kattintással. Kattintson a **beállítások** a szerepkörválasztás paneljét, majd ezután válassza a **engedélyezése** a többtényezős hitelesítés.

## <a name="how-azure-ad-pim-validates-mfa"></a>Hogyan ellenőrzi az Azure AD PIM a többtényezős hitelesítés
Érvényesítése többtényezős Hitelesítést, amikor egy felhasználó aktiválja a szerepkör esetén két lehetőség áll rendelkezésre.

A legegyszerűbb lehetőség az Azure MFA használják a felhasználók számára egy kiemelt szerepkörhöz aktiválása. Ehhez először ellenőrizze, hogy ezek a felhasználók licencét, ha szükséges, az Azure MFA szolgáltatásra regisztrált. További információt ennek a [Ismerkedés az Azure multi-factor Authentication a felhőben](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md). Az ajánlott, de nem szükséges, az Azure AD-t a többtényezős Hitelesítést ezen felhasználók bejelentkeznek az konfigurált. Ennek az az oka az MFA-ellenőrzések Azure AD PIM maga szerint történik.

Azt is megteheti Ha a felhasználók hitelesítéséhez a helyszíni lehet Identitásszolgáltatóként az MFA felelős. Például, ha konfigurálta az Azure AD elérése előtt intelligenskártya-alapú hitelesítést igényel AD összevonási szolgáltatások [Securing felhőalapú erőforrásokat az Azure többtényezős hitelesítés és az AD FS](../multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud.md) útmutatást tartalmaz az AD FS konfigurálása az Azure AD-jogcímek küldésére. Ha a felhasználó megpróbál egy szerepkör aktiválásához, Azure AD PIM fogad el, hogy többtényezős hitelesítés már érvényesítve lett a felhasználó a megfelelő jogcímeket kap után.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Következő lépések
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

