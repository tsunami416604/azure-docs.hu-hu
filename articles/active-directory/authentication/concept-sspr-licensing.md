---
title: Licenc önkiszolgáló jelszó-átállítási - Azure Active Directoryban
description: Az Azure AD az önkiszolgáló jelszó-átállítási licencelési követelmények
services: active-directory
keywords: ''
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro;seohack1
ms.openlocfilehash: 699d621856e9febf7a5d4093d7fdc86c39d5f242
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Licencelési követelmények az Azure AD az önkiszolgáló jelszó alaphelyzetbe állítása

Ahhoz, hogy az Azure Active Directory (Azure AD) jelszó-változtatási függvény akkor *rendelkeznie kell legalább egy licenc nélküli a szervezet*. Jelenleg nem kényszerítenek felhasználónkénti a jelszó alaphelyzetbe állítása során szerzett licencelési. A megfelelő licencre van szükség, ha a felhasználó akár közvetlen, akár közvetett módon egy adott licenchez tartozó funkció előnyeihez jut.

* **Csak felhőalapú felhasználók**: Office 365 bármely fizetett SKU vagy Azure AD alapvető
* **Felhő** vagy **helyszíni felhasználók**: Azure AD Premium P1 vagy P2, Enterprise Mobility + Security (EMS) vagy Microsoft 365

## <a name="licenses-required-for-password-writeback"></a>A jelszóvisszaírás szükséges engedélyek

Jelszóvisszaírás használatához rendelkeznie kell a következő licenccel a tenant egyikét:

* Prémium szintű Azure AD P1
* Prémium szintű Azure AD P2
* Enterprise Mobility + Security E3
* Enterprise Mobility + Security E5
* Microsoft 365 E3 csomag
* Microsoft 365 E5
* Microsoft 365 F1

> [!WARNING]
> Önálló Office 365 tervek licencelési *nem támogatják a jelszóvisszaírás* , és van szüksége, hogy ez a funkció működéséhez az előző csomagok valamelyikének.
>

További licencelési információt, beleértve a költségek, a következő oldalakon található:

* [Az Azure Active Directory-hely díjszabása](https://azure.microsoft.com/pricing/details/active-directory/)
* [Az Azure Active Directory-funkciók és képességek](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [A Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)

## <a name="enable-group-or-user-based-licensing"></a>Csoport vagy felhasználó alapú licencelés engedélyezése

Mostantól az Azure AD licencelésre biztonságicsoport-alapú. A rendszergazdák is hozzárendelhet licenceket tömeges, felhasználók, mint az őket egyenként csoportja számára. További információkért lásd: [rendelhet, győződjön meg arról, és a licencek kapcsolatos problémák megoldásához](../active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses).

Egyes Microsoft-szolgáltatások nem érhetők el az összes helyen. Mielőtt licenc rendelhet egy felhasználói, a rendszergazdának meg kell adnia a **felhasználási hely** tulajdonságát a felhasználó. A licencek hozzárendelése végezhető a **felhasználói** > **profil** > **beállítások** szakaszban az Azure portálon. *Licenc-hozzárendelést használ, anélkül, hogy a megadott felhasználási hely összes felhasználója örökli a könyvtár helye.*

## <a name="next-steps"></a>További lépések

* [Hogyan végezhető el az SSPR sikeres bevezetése?](howto-sspr-deployment.md)
* [Jelszó visszaállítása vagy módosítása](../active-directory-passwords-update-your-own-password.md)
* [Regisztráció önkiszolgáló jelszó-visszaállításra](../active-directory-passwords-reset-register.md)
* [Milyen adatokat használ az SSPR, és milyen adatokat kell kitöltenie a felhasználók számára?](howto-sspr-authenticationdata.md)
* [Milyen hitelesítési módszerek érhetők el a felhasználók számára?](concept-sspr-howitworks.md#authentication-methods)
* [Mik az SSPR szabályzatbeállításai?](concept-sspr-policy.md)
* [Mi a jelszóvisszaíró, és miért fontos?](howto-sspr-writeback.md)
* [Hogyan készíthető jelentés az SSPR-ben végzett tevékenységekről?](howto-sspr-reporting.md)
* [Mik az SSPR beállításai, és mit jelentenek?](concept-sspr-howitworks.md)
* [Azt hiszem, hogy valami nem működik. Hogyan háríthatom el az SSPR hibáit?](active-directory-passwords-troubleshoot.md)
* [Olyan kérdésem van, amely máshol nem szerepelt](active-directory-passwords-faq.md)
