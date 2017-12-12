---
title: "Licencelés: Azure AD SSPR |} Microsoft Docs"
description: "Az Azure AD az önkiszolgáló jelszó-átállítási licencelési követelmények"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 45cecbf1334d4d708f1578d1512627bc0ead3a04
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Licencelési követelmények az Azure AD az önkiszolgáló jelszó alaphelyzetbe állítása

Ahhoz, hogy az Azure Active Directory (Azure AD) jelszó-változtatási függvény akkor *rendelkeznie kell legalább egy licenc nélküli a szervezet*. Jelenleg nem kényszerítenek felhasználónkénti a jelszó alaphelyzetbe állítása során szerzett licencelési. A Microsoft licencszerződésben a megfelelőség biztosítása érdekében, licenceket rendelhet azokat a felhasználókat, a prémium szolgáltatások használatáért kell.

* **Csak felhőalapú felhasználók**: Office 365 bármely fizetett SKU vagy Azure AD alapvető
* **Felhő** vagy **helyszíni felhasználók**: Azure AD Premium P1 vagy P2, Enterprise Mobility + Security (EMS) vagy biztonságos hatékony vállalati (Másodper)

## <a name="licenses-required-for-password-writeback"></a>A jelszóvisszaírás szükséges engedélyek

Jelszóvisszaírás használatához rendelkeznie kell a következő licenccel a tenant egyikét:

* Prémium szintű Azure AD P1
* Prémium szintű Azure AD P2
* Enterprise Mobility + Security E3
* Enterprise Mobility + Security E5
* Microsoft 365 (terv E3)
* Microsoft 365 (terv E5)

> [!WARNING]
> Önálló Office 365 tervek licencelési *nem támogatják a jelszóvisszaírás* , és van szüksége, hogy ez a funkció működéséhez az előző csomagok valamelyikének.

További licencelési információt, beleértve a költségek, a következő oldalakon található:

* [Az Azure Active Directory-hely díjszabása](https://azure.microsoft.com/pricing/details/active-directory/)
* [Az Azure Active Directory-funkciók és képességek](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [A Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)

## <a name="enable-group-or-user-based-licensing"></a>Csoport vagy felhasználó alapú licencelés engedélyezése

Mostantól az Azure AD licencelésre biztonságicsoport-alapú. A rendszergazdák is hozzárendelhet licenceket tömeges, felhasználók, mint az őket egyenként csoportja számára. További információkért lásd: [rendelhet, győződjön meg arról, és a licencek kapcsolatos problémák megoldásához](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses).

Egyes Microsoft-szolgáltatások nem érhetők el az összes helyen. Mielőtt licenc rendelhet egy felhasználói, a rendszergazdának meg kell adnia a **felhasználási hely** tulajdonságát a felhasználó. A licencek hozzárendelése végezhető a **felhasználói** > **profil** > **beállítások** szakaszban az Azure portálon. *Licenc-hozzárendelést használ, anélkül, hogy a megadott felhasználási hely összes felhasználója örökli a könyvtár helye.*

## <a name="next-steps"></a>Következő lépések

* [Hogyan végezhető el az SSPR sikeres bevezetése?](active-directory-passwords-best-practices.md)
* [Jelszó visszaállítása vagy módosítása](active-directory-passwords-update-your-own-password.md)
* [Regisztráció önkiszolgáló jelszó-visszaállításra](active-directory-passwords-reset-register.md)
* [Milyen adatokat használ az SSPR, és milyen adatokat kell kitöltenie a felhasználók számára?](active-directory-passwords-data.md)
* [Milyen hitelesítési módszerek érhetők el a felhasználók számára?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Mik az SSPR szabályzatbeállításai?](active-directory-passwords-policy.md)
* [Mi a jelszóvisszaíró, és miért fontos?](active-directory-passwords-writeback.md)
* [Hogyan készíthető jelentés az SSPR-ben végzett tevékenységekről?](active-directory-passwords-reporting.md)
* [Mik az SSPR beállításai, és mit jelentenek?](active-directory-passwords-how-it-works.md)
* [Azt hiszem, hogy valami nem működik. Hogyan háríthatom el az SSPR hibáit?](active-directory-passwords-troubleshoot.md)
* [Olyan kérdésem van, amely máshol nem szerepelt](active-directory-passwords-faq.md)

