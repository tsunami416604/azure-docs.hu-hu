---
title: Többtényezős hitelesítés kötelezővé tétele |} A Microsoft Docs
description: Ismerje meg, hogyan többtényezős hitelesítést (MFA) az Azure Active Directory Privileged Identity Management kiterjesztésű emelt jogosultsági szintű identitásait.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 8e1c0fa212b31c05fcc4559f9f8d42b627f0da0e
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622847"
---
# <a name="how-to-require-mfa-in-azure-ad-privileged-identity-management"></a>Az Azure AD Privileged Identity Management MFA igénylése
Azt javasoljuk, hogy a többtényezős hitelesítés (MFA) a rendszergazdák az összes szükséges. Ez csökkenti a biztonsági kockázatot jelentő jelszó miatt a támadás kockázatát.

Megkövetelheti, hogy a felhasználók bejelentkezéskor befejezéséhez az MFA-hitelesítést. A blogbejegyzést [Office 365 és a többtényezős hitelesítés az Azure MFA](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/) hasonlítja össze, mit tartalmaz az Office és az Azure-előfizetések, a Microsoft Azure multi-factor Authentication ajánlatunkban foglalt szolgáltatásait.

Azt is megkövetelheti, hogy a felhasználók aktiválnak egy szerepkörhöz az Azure AD PIM-ben amikor befejeződnek az MFA-hitelesítést. Ezzel a módszerrel a felhasználó nem fejeződött be az MFA-hitelesítést, ha azok jelentkezett be, ha kéri erre a PIM által.

## <a name="requiring-mfa-in-azure-ad-privileged-identity-management"></a>Az Azure AD Privileged Identity Management többtényezős hitelesítés megkövetelése
Ha Ön kezeli a kiemelt szerepkörű rendszergazda, miközben a PIM identitásokat, riasztásokat, amelyek a többtényezős hitelesítés ajánlott kiemelt jogosultságokkal rendelkező fiókok jelenhet meg. A biztonsági riasztást a PIM irányítópultján kattintson, és a egy új panel nyílik meg a rendszergazdai fiókok, amelyek kell többtényezős hitelesítés listájával.  Megkövetelheti az MFA több szerepkör kiválasztásával, majd kattintson a **javítása** gombra, vagy Ön is kattintson az egyes szerepkörök melletti három pontra, majd kattintson a **javítása** gombra.

> [!IMPORTANT]
> Kattintson a jobb most az Azure MFA csak akkor működik a munkahelyi vagy iskolai fiókokhoz, nem Microsoft-fiókok (általában egy személyes fiók, amellyel jelentkezzen be a Microsoft szolgáltatásaihoz, például a Skype, Xbox, Outlook.com, stb.). Ez az oka bárki Microsoft-fiókkal nem lehet jogosult rendszergazdája, mert a szerepkörök aktiválására nem használhatják a többtényezős hitelesítés. Ha ezek a felhasználók továbbra is a Microsoft-fiókkal számítási feladatok kezelése, megszereznie azokat állandó rendszergazdák most.
> 
> 

Emellett egy adott szerepkör esetében a többtényezős hitelesítés követelménye módosíthatja a PIM-irányítópult a szerepkörök szakaszban kattintson rá. Ezután kattintson a **beállítások** a szerepkör panelen, és majd onnan **engedélyezése** a multi-factor Authentication hitelesítés.

## <a name="how-azure-ad-pim-validates-mfa"></a>Hogyan ellenőrzi az Azure AD PIM-ben a többtényezős hitelesítés
A többtényezős hitelesítés érvényesítése, amikor egy felhasználó aktiválja a szerepkörét két lehetőség van.

A legegyszerűbb lehetőség, hogy a felhasználók számára, akik egy rendszerjogosultságú szerepkör aktiválása az Azure MFA támaszkodnak. Ehhez először ellenőrizze, hogy ezek a felhasználók licencét, ha szükséges, és az Azure MFA-kiszolgáló regisztrálva van. Ennek módjáról a további információk [Ismerkedés az Azure multi-factor Authentication a felhőben](../authentication/howto-mfa-getstarted.md). Azt javasoljuk, de nem szükséges, hogy konfigurálja-e az Azure AD MFA kényszerítése ezeknek a felhasználóknak, amikor bejelentkeznek a. Ennek az oka az MFA-ellenőrzések magát az Azure AD PIM szerint történik.

Azt is megteheti Ha a felhasználók végeznek helyszíni hitelesítést használhat az identitásszolgáltató feladata a multi-factor Authentication. Például, ha már konfigurálta a AD összevonási szolgáltatások az Azure AD-ben elérése előtt intelligenskártya-alapú hitelesítés kötelezővé tétele [felhőerőforrások védelme Azure multi-factor Authentication és az AD FS](../authentication/howto-mfa-adfs.md) utasításokat tartalmaz AD FS-t az Azure AD-jogcímek küldése konfigurálásához. Amikor egy felhasználó megpróbál egy szerepkör aktiválásához, Azure AD PIM fogja fogadni, hogy az MFA már ellenőrzése megtörtént a felhasználó után, megkapja a megfelelő jogcímeket.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>További lépések
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]

