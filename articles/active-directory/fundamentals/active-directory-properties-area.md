---
title: A szervezet adatvédelmi adatainak hozzáadása – Azure Active Directory | Microsoft dokumentumok
description: Útmutató a szervezet adatvédelmi adatainak az Azure Active Directory tulajdonságai területhez való hozzáadásáról.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: ajburnle
ms.reviewer: bpham
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 343671c1d9ee82950a9822648f9831588da7e9f8
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876184"
---
# <a name="add-your-organizations-privacy-info-using-azure-active-directory"></a>A szervezet adatvédelmi adatainak hozzáadása az Azure Active Directory használatával
Ez a cikk bemutatja, hogyan adhat hozzá egy bérlői rendszergazda adatvédelmi adatokat a szervezet Azure Active Directory (Azure AD) bérlőjéhez az Azure Portalon keresztül.

Javasoljuk, hogy adja meg a globális adatvédelmi kapcsolattartót és a szervezet adatvédelmi nyilatkozatát is, hogy a belső alkalmazottak és a külső vendégek áttekinthessék az irányelveket. Mivel az adatvédelmi nyilatkozatok egyedileg jönnek létre, és minden vállalkozáshoz testre szabottak, javasoljuk, hogy segítségért forduljon ügyvédhez.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="add-your-privacy-info-on-azure-ad"></a>Az azure-beli ad-n tárolt adatvédelmi adatok hozzáadása
A szervezet adatvédelmi adatait az Azure AD **Tulajdonságok** területén adja hozzá.

### <a name="to-access-the-properties-area-and-add-your-privacy-information"></a>A Tulajdonságok terület elérése és az adatvédelmi adatok hozzáadása

1.    Jelentkezzen be az Azure Portalon bérlői rendszergazdaként.

2.    A bal oldali navigációs sávon válassza az **Azure Active Directory**lehetőséget, majd a **Tulajdonságok**lehetőséget.

    Megjelenik **a Tulajdonságok** terület.

    ![Az Azure AD tulajdonságai terület, kiemelve az adatvédelmi információs területet](media/active-directory-properties-area/properties-area.png)

3.    Adja meg az alkalmazottak adatvédelmi adatait:

    - **Technikai kapcsolat.** Írja be annak az e-mail címét, akivel a szervezeten belül technikai támogatásért kapcsolatba szeretne lépni.
    
    - **Globális adatvédelmi kapcsolattartó.** Írja be annak az e-mail címét, akivel kapcsolatba szeretne lépni a személyes adatok védelmével kapcsolatos kérdéseiért. Ez a személy is, aki a Microsoft kapcsolatot, ha van egy adatszivárgás. Ha itt nem szerepel személy, a Microsoft felveszi a kapcsolatot a globális rendszergazdákkal.

    - **Adatvédelmi nyilatkozat URL-címe.** Írja be a szervezet dokumentumára mutató hivatkozást, amely leírja, hogy a szervezet hogyan kezeli a belső és a külső vendég adatvédelmét.

        >[!Important]
        >Ha nem adja meg sem a saját adatvédelmi nyilatkozatát, sem az adatvédelmi kapcsolattartóját, akkor a külső vendégek az **Engedélyek áttekintése** mezőben olyan szöveget fognak látni, ** <amely szerint _a szervezet neve_> nem adott meg hivatkozásokat a kifejezéseikre, hogy áttekintsék.** Például egy vendég felhasználó fogja látni ezt az üzenetet, amikor meghívást kap nak egy szervezet elérésére a B2B együttműködésen keresztül.

        ![B2B Együttműködési ellenőrzési engedélyek mező üzenettel](media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4.    Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>További lépések
- [Az Azure Active Directory B2B együttműködési meghívók beváltása](../b2b/redemption-experience.md)
- [Felhasználó profiladatainak hozzáadása vagy módosítása az Azure Active Directoryban](active-directory-users-profile-azure-portal.md)
