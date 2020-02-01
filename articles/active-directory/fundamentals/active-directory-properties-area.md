---
title: Adja hozzá a szervezete adatvédelmi adatait – Azure Active Directory | Microsoft Docs
description: Útmutató a szervezet adatvédelmi adatainak a Azure Active Directory tulajdonságok részhez való hozzáadásához.
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
ms.openlocfilehash: 95f1877ae630919ba7da5a851b5f8291ade2d165
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76897585"
---
# <a name="add-your-organizations-privacy-info-using-azure-active-directory"></a>A szervezet adatvédelmi adatainak hozzáadása a Azure Active Directory használatával
Ez a cikk azt ismerteti, hogyan adhat hozzá egy bérlői rendszergazda az adatvédelemmel kapcsolatos adatokat egy szervezet Azure Active Directory (Azure AD) bérlőhöz a Azure Portal keresztül.

Javasoljuk, hogy vegye fel mind a globális adatvédelmi kapcsolattartót, mind a szervezete adatvédelmi nyilatkozatát, így a belső alkalmazottak és a külső vendégek is áttekinthetik a szabályzatokat. Mivel az adatvédelmi nyilatkozatok egyedi módon hozhatók létre és testreszabhatók az egyes vállalkozások számára, javasoljuk, hogy kérjen segítséget az ügyvédi segítségért.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="add-your-privacy-info-on-azure-ad"></a>Adatvédelmi információk hozzáadása az Azure AD-ben
A szervezet adatvédelmi információit az Azure AD **Tulajdonságok** területén adhatja hozzá.

### <a name="to-access-the-properties-area-and-add-your-privacy-information"></a>A tulajdonságok részhez való hozzáféréshez és az adatvédelmi adatok hozzáadásához

1.  Jelentkezzen be a Azure Portal bérlői rendszergazdaként.

2.  A bal oldali navigációs sávon válassza a **Azure Active Directory**lehetőséget, majd válassza a **Tulajdonságok**lehetőséget.

    Megjelenik a **Tulajdonságok** rész.

    ![Az Azure AD-tulajdonságok az adatvédelmi információs területeket kiemelve](media/active-directory-properties-area/properties-area.png)

3.  Adja meg az alkalmazottak adatvédelmi adatait:

    - **Technikai kapcsolattartó.** Írja be annak a személynek az e-mail-címét, akit a szervezeten belüli technikai támogatáshoz szeretne felvenni.
    
    - **Globális adatvédelmi kapcsolattartó.** Írja be annak a személynek az e-mail-címét, aki a személyes adatok védelmével kapcsolatos lekérdezésekhez szeretne csatlakozni. Ez a személy akkor is, ha a Microsoft kapcsolattartási adatvesztéssel jár. Ha itt nem szerepel személy, a Microsoft kapcsolatba lép a globális rendszergazdával.

    - **Adatvédelmi nyilatkozat URL-címe.** Írja be a szervezet dokumentumára mutató hivatkozást, amely leírja, hogyan kezeli a szervezet a belső és a külső vendég adatvédelmét.

        >[!Important]
        >Ha nem tartalmazza a saját adatvédelmi nyilatkozatát vagy az Ön adatvédelmi kapcsolattartóját, a külső vendégek a **felülvizsgálati engedélyek** mezőben megjelenő szöveget is láthatják, **<_a szervezete neve_> nem adott meg hivatkozásokat a saját feltételeinek áttekintéséhez**. A vendég felhasználó például akkor látja ezt az üzenetet, amikor meghívót kap egy szervezetnek a B2B-együttműködésen keresztüli elérésére.

        ![B2B Collaboration felülvizsgálati engedélyek Box üzenettel](media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4.  Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>Következő lépések
- [Azure Active Directory B2B együttműködés meghívásának beváltása](https://aka.ms/b2bredemption)
- [Felhasználói profil adatainak hozzáadása vagy módosítása Azure Active Directory](active-directory-users-profile-azure-portal.md)
