---
title: Hozzáférési csomag hozzáférésének áttekintése az Azure AD-jogosultságkezelésben
description: Megtudhatja, hogyan végezheti el a jogosultságkezelési hozzáférési csomagok hozzáférés-felülvizsgálatát az Azure Active Directory hozzáférési felülvizsgálatokban (előzetes verzió).
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
ms.openlocfilehash: 99de022b7259b33baab3aa825673a8f85e932bff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968749"
---
# <a name="review-access-of-an-access-package-in-azure-ad-entitlement-management"></a>Hozzáférési csomag hozzáférésének áttekintése az Azure AD-jogosultságkezelésben

Az Azure AD jogosultságkezelés leegyszerűsíti, hogy a vállalatok hogyan kezeljék a csoportokhoz, alkalmazásokhoz és SharePoint-webhelyekhez való hozzáférést. Ez a cikk azt ismerteti, hogyan végezheti el a hozzáférési felülvizsgálathoz kijelölt véleményezőként hozzárendelt más felhasználók hozzáférési felülvizsgálatát.

## <a name="prerequisites"></a>Előfeltételek

A felhasználók aktív hozzáférési csomag-hozzárendeléseinek áttekintéséhez meg kell felelnie a hozzáférés felülvizsgálatának előfeltételeinek:
- Prémium szintű Azure AD P2
- Globális rendszergazda
- Kijelölt felhasználói rendszergazda, katalógustulajdonos vagy Access-csomagkezelő

További információt a [Licenckövetelmények című témakörben talál.](entitlement-management-overview.md#license-requirements)


## <a name="open-the-access-review"></a>A hozzáférési felülvizsgálat megnyitása

A hozzáférési felülvizsgálat megkereséséhez és megnyitásához kövesse az alábbi lépéseket:

1. E-mailt kaphat a Microsofttól, amely a hozzáférés áttekintését kéri. Keresse meg az e-mailt a hozzáférési felülvizsgálat megnyitásához. Íme egy példa e-mail ben átnézni hozzáférést:
    
    ![Access-ellenőrző e-mail](./media/entitlement-management-access-reviews-review-access/review-access-reviewer-email.png)

1. A **hozzáférés-ellenőrzés** megnyitásához kattintson a Felhasználói hozzáférés áttekintése hivatkozásra. 

1. Ha nem rendelkezik az e-maillel, a függőben https://myaccess.microsoft.comlévő hozzáférési véleményeket úgy találhatja meg, hogy közvetlenül a rendszerre navigál.  (Az Egyesült Államok `https://myaccess.microsoft.us` kormánya, használja helyette.)

1. Kattintson az **Access-ellenőrzések** elemre a bal oldali navigációs sávon a függőben lévő hozzáférési ellenőrzések listájának megtekintéséhez.
    
    ![Hozzáférési vélemények kiválasztása a Saját hozzáférés ben](./media/entitlement-management-access-reviews-review-access/review-access-myaccess-select-access-review.png)

1. Kattintson a megkezdeni kívánt értékelésre.
    
    ![A hozzáférési felülvizsgálat kiválasztása](./media/entitlement-management-access-reviews-review-access/review-access-select-access-review.png)

## <a name="perform-the-access-review"></a>A hozzáférés-ellenőrzés végrehajtása

Miután megnyitotta a hozzáférési felülvizsgálatot, látni fogja azoknak a felhasználóknak a nevét, akiknek át kell tekintenie. Kétféleképpen hagyhatja jóvá vagy tagadhatja meg a hozzáférést:
- Manuálisan jóváhagyhatja vagy megtagadhatja a hozzáférést egy vagy több felhasználó számára
- Elfogadhatja a rendszer javaslatokat

### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>Hozzáférés manuális jóváhagyása vagy megtagadása egy vagy több felhasználó számára
1. Tekintse át a felhasználók listáját, és határozza meg, hogy mely felhasználóknak kell továbbra is hozzáférniük.

    ![Az átnézandó felhasználók listája](./media/entitlement-management-access-reviews-review-access/review-access-list-of-users.png)

1. A hozzáférés jóváhagyásához vagy megtagadásához jelölje ki a felhasználó nevétől balra található választógombot.

1. A felhasználónevek feletti sávon válassza a **Jóváhagyás** vagy a **Megtagadás** lehetőséget.

    ![A felhasználó kijelölése](./media/entitlement-management-access-reviews-review-access/review-access-select-users.png)

1. Ha nem biztos benne, kattintson a **Nem tudom** gombra.

    Ha ezt a beállítást adja meg, a felhasználó fenntartja a hozzáférést, és ez a beállítás megjelenik a naplónaplókban. A napló ban látható a többi ellenőrző, hogy még mindig befejezte az ellenőrzés.

1. Előfordulhat, hogy meg kell indokolnia döntését. Írjon be egy okot, és kattintson a **Küldés gombra.**

    ![Hozzáférés jóváhagyása vagy megtagadása](./media/entitlement-management-access-reviews-review-access/review-access-decision-approve.png)

1. A döntést bármikor módosíthatja az értékelés vége előtt. Ehhez jelölje ki a felhasználót a listából, és módosítsa a döntést. Jóváhagyhatja például egy korábban elutasított felhasználó hozzáférését.

Ha több véleményező van, a felvétel az utolsó elküldött választ rögzíti. Vegyünk egy példát, amikor a rendszergazda két véleményezőt jelöl ki – Alice és Bob. Alice megnyitja először az értékelést, és jóváhagyja a hozzáférést. Az ellenőrzés befejezése előtt Bob megnyitja az értékelést, és megtagadja a hozzáférést. Ebben az esetben az utolsó megtagadási hozzáférési döntés rögzítésre kerül.

>[!NOTE]
>Ha egy felhasználó nak meg tagadja a hozzáférést, a rendszer nem távolítja el azonnal a hozzáférési csomagból. A felhasználó a felülvizsgálat befejezésekor törlődik a hozzáférési csomagból, vagy a rendszergazda befejezi az értékelést.

### <a name="approve-or-deny-access-using-the-system-generated-recommendations"></a>Hozzáférés jóváhagyása vagy megtagadása a rendszer által létrehozott javaslatok használatával

Több felhasználó hozzáférésének gyorsabb áttekintéséhez használhatja a rendszer által létrehozott javaslatokat, és egyetlen kattintással elfogadhatja a javaslatokat. A javaslatok a felhasználó bejelentkezési tevékenysége alapján jönnek létre.

1.  A lap tetején lévő sávon kattintson a **Javaslatok elfogadása gombra.**
    
    ![Válassza a Javaslatok elfogadása lehetőséget.](./media/entitlement-management-access-reviews-review-access/review-access-use-recommendations.png)
    
    Megjelenik az ajánlott műveletek összegzése.

1.  A javaslatok elfogadásához kattintson a **Küldés** gombra.

## <a name="next-steps"></a>További lépések

- [A hozzáférési csomagok önellenőrzése](entitlement-management-access-reviews-self-review.md)