---
title: Adja hozzá a szervezet adatvédelmi információ az Azure AD |} Microsoft Docs
description: Ismerteti, hogyan adja hozzá a szervezet adatvédelmi információ az Azure Active Directory (Azure AD) tulajdonságai területen.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: lizross
ms.reviewer: bpham
ms.custom: it-pro
ms.openlocfilehash: 8cdf30ed09601a31529073eaedd4ab53780157d5
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2018
---
# <a name="how-to-add-your-organizations-privacy-info-in-azure-active-directory"></a>Útmutató: Azure Active Directoryban adja hozzá a szervezet adatvédelmi információ
Ez a cikk azt ismerteti, hogyan egy Bérlői rendszergazda adhat hozzá adatvédelemmel kapcsolatos információ egy szervezet Azure Active Directory (Azure AD) bérlői, az Azure portálon keresztül.

Erősen ajánlott hozzáadása a globális adatvédelmi ügyfél, mind a szervezete adatvédelmi nyilatkozatát, így belső alkalmazottai és külső vendégek is tekintse át a házirendeket. Adatvédelmi nyilatkozat egyedileg hozott létre, és az egyes üzleti igazított, mert erősen ajánlott egy ügyvéddel kérjen segítséget.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="access-the-properties-area-to-add-your-privacy-info"></a>Hozzáférés a tulajdonságai területen hozzáadása a adatvédelmi információ

1.  Jelentkezzen be az Azure-portálon a bérlői rendszergazda felhasználóként.

2.  Válassza a bal oldali navigációs sávja **Azure Active Directory**, majd válassza ki **tulajdonságok**.

    A **tulajdonságok** jelenik meg.

    ![Az adatvédelmi információs terület kiemelés az Azure AD tulajdonságai területen](./media/active-directory-properties-area/properties-area.png)

3.  Adja hozzá a adatvédelmi információ az alkalmazottak számára:

    - **Műszaki jelezze a problémát.** Írja be a személy, forduljon a technikai támogatási szolgálathoz a szervezeten belüli e-mail címet.
    
    - **Globális adatvédelmi jelezze a problémát.** Írja be a személyes adatok védelmét kapcsolódó lekérdezések a kapcsolattartó személy e-mail címet. Ez a személy már is, akik a Microsoft felveszi a kapcsolatot a adatok sérülése esetén. Ha az itt felsorolt személy nem, a Microsoft felveszi a kapcsolatot a globális rendszergazdák.

    - **Adatvédelmi nyilatkozat URL-címe.** Írja be a hivatkozást, amely leírja, miként a szervezet kezeli a mindkét dokumentum a szervezet belső és külső Vendég adatok védelmét.

        >[!Important]
        >Ha nem adja meg a saját adatvédelmi nyilatkozat vagy a adatvédelmi partnere, a külső vendégek jelenik meg a szöveg a **felülvizsgálati engedélyek** mezőben, amely szerint,  **< _a szervezeti neve_> nem rendelkezik a feltételeket, hogy átnézhesse őket mutató hivatkozások**. A Vendég felhasználó például megjelenik ez az üzenet, amikor megkapják a meghívó B2B együttműködés keresztül férnek hozzá a szervezet.

        ![B2B együttműködés felülvizsgálati engedélyek üzenettel](./media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4.  Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>További lépések
- [Az Azure Active Directory B2B együttműködés meghívó érvényesítési](https://aka.ms/b2bredemption)
- [Adja hozzá, vagy egy Azure Active Directoryban lévő felhasználói profil adatainak módosítása](/active-directory-users-profile-azure-portal.md)