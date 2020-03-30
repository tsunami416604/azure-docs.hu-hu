---
title: Hozzáférési csomag hozzáférési felülvizsgálatának létrehozása az Azure AD-jogosultságkezelésben
description: Megtudhatja, hogyan hozhat létre hozzáférési felülvizsgálati szabályzatot a jogosultságkezelési hozzáférési csomagokhoz az Azure Active Directory-hozzáférés-felülvizsgálatokban (előzetes verzió).
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
ms.openlocfilehash: a862bbb1f574e4adab2f7d8e59a1abe8e5a5fa2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73608838"
---
# <a name="create-an-access-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Hozzáférési csomag hozzáférési felülvizsgálatának létrehozása az Azure AD-jogosultságkezelésben

Az elavult hozzáférés kockázatának csökkentése érdekében engedélyeznie kell az Azure AD-jogosultságkezelésben egy hozzáférési csomaghoz aktív hozzárendelésekkel rendelkező felhasználók időszakos felülvizsgálatát. Új hozzáférési csomag létrehozásakor vagy meglévő hozzáférési csomag szerkesztésekor engedélyezheti az ellenőrzéseket. Ez a cikk bemutatja, hogyan engedélyezheti a hozzáférési csomagok hozzáférési felülvizsgálatát.

## <a name="prerequisites"></a>Előfeltételek

A hozzáférési csomagok felülvizsgálatának engedélyezéséhez meg kell felelnie a hozzáférési csomagok létrehozásának előfeltételeinek:
- Prémium szintű Azure AD P2
- Globális rendszergazda, Felhasználói rendszergazda, Katalógustulajdonosa vagy Access-csomagkezelő

További információt a [Licenckövetelmények című témakörben talál.](entitlement-management-overview.md#license-requirements)


## <a name="create-an-access-review-of-an-access-package"></a>Hozzáférési csomag hozzáférési felülvizsgálatának létrehozása

Új hozzáférési csomag [létrehozásakor](entitlement-management-access-package-create.md) vagy [meglévő hozzáférési csomagszabályzat szerkesztésekor](entitlement-management-access-package-lifecycle-policy.md) engedélyezheti a hozzáférés-felülvizsgálatokat. A hozzáférési csomagok hozzáférési felülvizsgálatának engedélyezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az **Életciklus** lapot egy hozzáférési csomaghoz, és görgessen le az **Access-vélemények**elemre.

1. Helyezze a **Hozzáférés-felülvizsgálatok megkövetelése** váltást **Az Igen**gombra.

    ![A hozzáférési felülvizsgálat hozzáadása](./media/entitlement-management-access-reviews/access-reviews-pane.png)

1. Adja meg azt a dátumot, amelynek kezdete mellett az ellenőrzések **kezdete következik.**

1. Ezután állítsa a **felülvizsgálati gyakoriságot** **évente**, **kétévente,** **negyedéves** vagy **havi**.
Ez a beállítás határozza meg, hogy milyen gyakran kerülsore access-felülvizsgálatra.

1. Az Időtartam beállításával **megadhatja,** hogy az ismétlődő adatsorok egyes felülvizsgálatai hány napig legyenek nyitva az ellenőrzők tőlük érkező adatok számára. Például ütemezhet egy éves felülvizsgálatot, amely január 1-jén kezdődik, és 30 napig tart, így az ellenőrzőknek a hónap végéig kell válaszolniuk.

1. A **Véleményezők**csoportban válassza az **Önellenőrzés** lehetőséget, ha azt szeretné, hogy a felhasználók saját hozzáférés-felülvizsgálatot végezzenek, vagy válassza a **Specifikus véleményező(ke)t,** ha ki szeretne jelölni egy véleményezőt.

    ![Véleményezők hozzáadása kiválasztása](./media/entitlement-management-access-reviews/access-reviews-add-reviewer.png)

1. Ha **a Konkrét véleményező(k)** lehetőséget választotta, adja meg, hogy mely felhasználók végezzék el a hozzáférési felülvizsgálatot:
    1. Válassza **az Ellenőrzők hozzáadása**lehetőséget.
    1. Az **Ellenőrzők kiválasztása** ablaktáblában keresse meg és jelölje ki azokat a felhasználókat, akiket véleményezőként szeretne keresni.
    1. Miután kiválasztotta az véleményezőt, kattintson a **Kijelölés** gombra.

    ![Az ellenőrzők megadása](./media/entitlement-management-access-reviews/access-reviews-select-reviewer.png)

1. Kattintson **a Véleményezés + Létrehozás** gombra, ha új hozzáférési csomagot hoz létre, vagy a **Frissítés gombra,** ha hozzáférési csomagot szerkeszt, a lap alján.

## <a name="view-the-status-of-the-access-review"></a>A hozzáférés-felülvizsgálat állapotának megtekintése

A kezdési dátum után a hozzáférés-felülvizsgálat megjelenik az **Access-ellenőrzések** szakaszban. A hozzáférési felülvizsgálat állapotának megtekintéséhez kövesse az alábbi lépéseket:

1. Az **Identitáscégcégcsoport**ban kattintson az **Access-csomagok** elemre, majd válassza ki az ellenőrizni kívánt hozzáférési ellenőrzési állapotú hozzáférési csomagot.   

1. Miután eljutott a hozzáférési csomag áttekintéséhez, kattintson a bal oldali menü **Hozzáférés-ellenőrzések** parancsára.
    
    ![Hozzáférési vélemények kiválasztása](./media/entitlement-management-access-reviews/access-review-status-access-package-overview.png)

1. Megjelenik egy lista, amely tartalmazza az összes olyan szabályzatot, amelyhez hozzáférési felülvizsgálat tartozik. Kattintson az értékelésre a jelentés megtekintéséhez.

    ![A hozzáférési felülvizsgálatok listája](./media/entitlement-management-access-reviews/access-review-status-select-access-reviews.png)
   
1. A jelentés megtekintésekor megjelenik az áttekintett felhasználók száma és a véleményező által rajtuk végrehajtott műveletek száma.

    ![Ellenőrzés állapotának megtekintése](./media/entitlement-management-access-reviews/access-review-status.png)
 

## <a name="access-reviews-email-notifications"></a>Az Access ellenőrzi az e-mailértesítéseket
Kijelölhet ellenőrzőket, vagy a felhasználók saját maguk is áttekinthetik a hozzáférésüket. Alapértelmezés szerint az Azure AD küld egy e-mailt a véleményezők vagy önellenőrzők röviddel az ellenőrzés megkezdése után.

Az e-mail utasításokat tartalmaz a hozzáférési csomagokhoz való hozzáférés áttekintéséhez. Ha az ellenőrzés a felhasználók számára, hogy vizsgálja felül a hozzáférést, mutasd meg nekik az utasításokat, hogyan kell elvégezni egy önellenőrzés a hozzáférési csomagok.
  
Ha vendégfelhasználókat rendelt látogatóként, és nem fogadták el az Azure AD-s vendégmeghívásukat, akkor nem kapnak e-maileket az Azure AD hozzáférési felülvizsgálataiból. Először el kell fogadniuk a meghívást, és létre kell hozniuk egy fiókot az Azure AD-vel, mielőtt megkapnák az e-maileket. 

## <a name="next-steps"></a>További lépések

- [Hozzáférési csomagok hozzáférésének áttekintése](entitlement-management-access-reviews-review-access.md)
- [A hozzáférési csomagok önellenőrzése](entitlement-management-access-reviews-self-review.md)
