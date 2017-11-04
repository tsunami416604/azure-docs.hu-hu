---
title: "Licencelés: Azure AD SSPR |} Microsoft Docs"
description: "Az Azure AD az önkiszolgáló jelszó-átállítási licencelési követelmények"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
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
ms.openlocfilehash: db849cd1e9da634064f79fbc041098542580ad02
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Licencelési követelmények az Azure AD az önkiszolgáló jelszó alaphelyzetbe állítása

Ahhoz, hogy Azure AD-Jelszóvisszaállítási függvény akkor **rendelkeznie kell legalább egy licenc nélküli a szervezet**. Nem határoztunk felhasználónkénti a jelszó alaphelyzetbe állítása során szerzett licencelési. A Microsoft licencszerződésben a megfelelőség biztosítása érdekében, licenceket rendelhet azokat a felhasználókat, a prémium szolgáltatások használatáért kell.

* **Csak felhőalapú felhasználók** -Office 365 (Office 365) bármely fizetett SKU vagy Azure AD alapvető
* **Felhő** és/vagy **helyszíni felhasználók** -Azure AD Premium P1 vagy P2, Enterprise Mobility + Security (EMS) vagy biztonságos hatékony vállalati (Másodper)

## <a name="licenses-required-for-password-writeback"></a>A jelszóvisszaírás szükséges engedélyek

A jelszóvisszaírás használatához rendelkeznie kell egyet a következő licenccel az Ön bérelt szolgáltatásának.

* Prémium szintű Azure AD P1
* Prémium szintű Azure AD P2
* Enterprise Mobility + Security E3
* Enterprise Mobility + Security E5
* Microsoft 365 E3 csomag
* Microsoft 365 E5

> [!WARNING]
> Önálló Office 365 tervek licencelési **nem támogatják a jelszóvisszaírás** és jelszó használata kötelezővé tehető az előző csomagok esetében ez a funkció működéséhez.

A következő oldalakon található további licencelési adatait, beleértve a költségek

* [Az Azure Active Directory árképzési hely](https://azure.microsoft.com/pricing/details/active-directory/)
* [Az Azure Active Directory-funkciók és képességek](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365](https://www.microsoft.com/microsoft-365/enterprise)

## <a name="enable-group-or-user-based-licensing"></a>Csoport vagy felhasználó alapú licencelés engedélyezése

Mostantól az Azure AD támogatja a biztonságicsoport-alapú licencelés engedélyezése rendszergazdák számára, hogy egyszerre több licenceket rendelhet egy felhasználói csoporton, mint az őket egyenként. [Rendelje hozzá, győződjön meg arról, és a licencek kapcsolatos problémák megoldásához](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses)

Egyes Microsoft-szolgáltatások nem érhetők el az összes helyen. Licenc rendelhet egy felhasználót, mielőtt a rendszergazda a felhasználó a "Használati hely" tulajdonság adjon meg. Licenc hozzárendelése felhasználói végezhető > Profil > Beállítások szakaszban az Azure portálon. **Licenc-hozzárendelést használ, anélkül, hogy a megadott felhasználási hely összes felhasználója örökli a könyvtár helye.**

## <a name="next-steps"></a>Következő lépések

* [Hogyan végezze el a sikeres bevezetéshez az önkiszolgáló jelszó-Változtatási?](active-directory-passwords-best-practices.md)
* [A jelszó megváltoztatására](active-directory-passwords-update-your-own-password.md).
* [Az önkiszolgáló jelszó-változtatási regisztrációs](active-directory-passwords-reset-register.md).
* [Milyen adatok SSPR használja, és milyen adatokat kell tölteni a felhasználók számára?](active-directory-passwords-data.md)
* [Hitelesítési módszerek állnak rendelkezésre a felhasználók számára?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Mik a házirend-beállításokban az önkiszolgáló jelszó-Változtatási?](active-directory-passwords-policy.md)
* [A jelszóvisszaírás és miért fontos információk?](active-directory-passwords-writeback.md)
* [Hogyan jelentést az önkiszolgáló jelszó-Változtatási tevékenység?](active-directory-passwords-reporting.md)
* [Mik az önkiszolgáló jelszó-Változtatási közül az összes, és mit azokat a következőket:?](active-directory-passwords-how-it-works.md)
* [Szerintem valami nem működik. Hogyan hibáinak elhárítása az önkiszolgáló jelszó-Változtatási?](active-directory-passwords-troubleshoot.md)
* [A rendszer nem jelzett valahol máshol kérdést kell](active-directory-passwords-faq.md)
