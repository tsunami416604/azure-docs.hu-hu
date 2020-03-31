---
title: Hozzáférési csomag önellenőrzése az Azure AD-jogosultságkezelésben
description: Megtudhatja, hogyan tekintheti át a jogosultságkezelési hozzáférési csomagok felhasználói hozzáférés-hozzáféréssel kapcsolatos felhasználói hozzáférés-áttekintéseit az Azure Active Directory hozzáférési felülvizsgálatokban (előzetes verzió).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78967776"
---
# <a name="self-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Hozzáférési csomag önellenőrzése az Azure AD-jogosultságkezelésben

Az Azure AD jogosultságkezelés leegyszerűsíti, hogy a vállalatok hogyan kezeljék a csoportokhoz, alkalmazásokhoz és SharePoint-webhelyekhez való hozzáférést. Ez a cikk azt ismerteti, hogy a felhasználó hogyan végzi el a hozzárendelt hozzáférési csomag(ok) önfelülvizsgálatát.

## <a name="open-the-access-review"></a>A hozzáférési felülvizsgálat megnyitása

A hozzáférés-felülvizsgálathoz először meg kell nyitnia a hozzáférési felülvizsgálatot. A hozzáférési felülvizsgálat megkereséséhez és megnyitásához kövesse az alábbi eljárást:

1. E-mailt kaphat a Microsofttól, amely a hozzáférés áttekintését kéri. Keresse meg az e-mailt a hozzáférési felülvizsgálat megnyitásához. Íme egy példa egy e-mailre, amely a hozzáférés felülvizsgálatát kéri: 
    
    ![Az Önellenőrző ek véleményezési e-mailcíme](./media/entitlement-management-access-reviews-review-access/self-review-reviewer-email.png)

1. Kattintson a **Hozzáférés áttekintése** hivatkozásra.

1. Közvetlenül https://myaccess.microsoft.com is megkeresheti a függőben lévő hozzáférési véleményeket, ha nem kap e-mailt.  (Az Egyesült Államok `https://myaccess.microsoft.us` kormánya, használja helyette.)

1. Kattintson az **Access-ellenőrzések** elemre a bal oldali navigációs sávon a függőben lévő hozzáférési ellenőrzések listájának megtekintéséhez.


1.  Kattintson a megkezdeni kívánt értékelésre.

## <a name="perform-the-access-review"></a>A hozzáférés-ellenőrzés végrehajtása

Miután megnyitotta a hozzáférési felülvizsgálatot, láthatja a hozzáférést. A hozzáférés-felülvizsgálathoz kövesse az alábbi eljárást:

1.  Döntse el, hogy továbbra is szüksége van-e a hozzáférési csomaghoz való hozzáférésre. Például a projekt, amelyen dolgozik, nem fejeződött be, így továbbra is hozzáférésre van szüksége a projekten való munka folytatásához.

1.  Kattintson az **Igen** gombra a hozzáférés megtartásához, vagy kattintson a **Nem** gombra a hozzáférés eltávolításához.
    >[!NOTE]
    >Ha kijelentette, hogy már nincs szüksége a hozzáférésre, nem lesz azonnal eltávolítva a hozzáférési csomagból. A rendszer eltávolítja a hozzáférési csomagból, amikor az ellenőrzés befejeződik, vagy ha a rendszergazda leállítja az értékelést.

1.  Ha az **Igen**gombra kattintott, előfordulhat, hogy az Ok mezőben meg kell **adnia** egy indoklási utasítást.

1.  Kattintson a **Küldés gombra.**

Visszatérhet az értékeléshez, ha meggondolja magát, és úgy dönt, hogy az értékelés vége előtt módosítja a választ.

## <a name="next-steps"></a>További lépések

- [Hozzáférés áttekintése a hozzáférési csomagokhoz](entitlement-management-access-reviews-review-access.md) 
