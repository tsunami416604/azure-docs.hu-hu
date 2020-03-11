---
title: Hozzáférési csomag önálló felülvizsgálata az Azure AD-jogosultságok kezelésében
description: Megtudhatja, hogyan tekintheti át a jogosultsági felügyeleti hozzáférési csomagok felhasználói hozzáférését Azure Active Directory hozzáférési felülvizsgálatokban (előzetes verzió).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 11/01/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45da1170705bab1206a98c59e02c7616c25ce502
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2020
ms.locfileid: "78967776"
---
# <a name="self-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Hozzáférési csomag önálló felülvizsgálata az Azure AD-jogosultságok kezelésében

Az Azure AD jogosultság-kezelési szolgáltatás leegyszerűsíti, hogy a vállalatok hogyan kezelhetik a csoportokat, az alkalmazásokat és a SharePoint-webhelyeket. Ez a cikk azt ismerteti, hogyan végzi el a felhasználók a hozzárendelt hozzáférési csomag (ok) önálló áttekintését.

## <a name="open-the-access-review"></a>A hozzáférési felülvizsgálat megnyitása

A hozzáférési felülvizsgálat elvégzéséhez először meg kell nyitnia a hozzáférési felülvizsgálatot. A hozzáférési felülvizsgálat megkereséséhez és megnyitásához kövesse az alábbi eljárást:

1. Előfordulhat, hogy a Microsoft e-mailt kap, amely arra kéri, hogy tekintse át a hozzáférést. Keresse meg az e-mailt a hozzáférési felülvizsgálat megnyitásához. Íme egy példa a hozzáférés felülvizsgálatát kérő e-mailre: 
    
    ![Hozzáférés felülvizsgálata – önellenőrző e-mail](./media/entitlement-management-access-reviews-review-access/self-review-reviewer-email.png)

1. Kattintson a **hozzáférés áttekintése** hivatkozásra.

1. Ha nem kap e-mailt, a függőben lévő hozzáférési felülvizsgálatok megkereséséhez közvetlenül https://myaccess.microsoft.com is megkeresheti.  (Az USA kormánya számára Ehelyett használja a `https://myaccess.microsoft.us`.)

1. A bal oldali navigációs sávon kattintson a **hozzáférési felülvizsgálatok** lehetőségre az Önhöz rendelt hozzáférési felülvizsgálatok listájának megtekintéséhez.


1.  Kattintson a megkezdeni kívánt áttekintésre.

## <a name="perform-the-access-review"></a>A hozzáférési felülvizsgálat végrehajtása

A hozzáférési felülvizsgálat megnyitása után láthatja a hozzáférést. A hozzáférési felülvizsgálat végrehajtásához kövesse az alábbi eljárást:

1.  Döntse el, hogy továbbra is szüksége van-e a hozzáférési csomag elérésére. Például, ha a projekt, amelyen dolgozik, nem fejeződött be, így továbbra is hozzá kell férnie a projekt működésének folytatásához.

1.  Az **Igen** gombra kattintva megtarthatja a hozzáférését, vagy a **nem** gombra kattintva eltávolíthatja a hozzáférését.
    >[!NOTE]
    >Ha kijelentette, hogy már nincs szüksége a hozzáférésre, nem távolítja el azonnal a hozzáférési csomagból. A rendszer eltávolítja a hozzáférési csomagból a felülvizsgálat végén, vagy ha a rendszergazda leállítja a felülvizsgálatot.

1.  Ha az **Igen**gombra kattintott, előfordulhat, hogy az **OK** mezőben szerepelnie kell egy indoklási utasításnak.

1.  Kattintson a **Küldés** gombra.

Visszatérhet a felülvizsgálathoz, ha meggondolja magát, és úgy dönt, hogy megváltoztatja a választ a felülvizsgálat vége előtt.

## <a name="next-steps"></a>Következő lépések

- [Hozzáférési csomagok hozzáférésének áttekintése](entitlement-management-access-reviews-review-access.md) 
