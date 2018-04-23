---
title: Szerepköralapú hozzáférés-vezérlés az Azure Portalon | Microsoft Docs
description: Megismerheti a hozzáférés kezelését az Azure Portal szerepköralapú hozzáférés-vezérlése segítségével. Szerepkör-hozzárendelésekkel rendelhet engedélyeket az erőforrásokhoz.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2017
ms.author: rolyon
ms.reviewer: rqureshi
ms.openlocfilehash: 35cb4be5c1ee24bba8026cbe66d2316753f9ed32
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="use-role-based-access-control-to-manage-access-to-your-azure-subscription-resources"></a>Az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése szerepköralapú hozzáférés-vezérléssel
> [!div class="op_single_selector"]
> * [Hozzáférés kezelése felhasználó vagy csoport alapján](role-assignments-users.md)
> * [Hozzáférés kezelése erőforrás alapján](role-assignments-portal.md)

Az Azure Szerepköralapú hozzáférés-vezérlés (RBAC) részletes hozzáférés-vezérlést biztosít az Azure-hoz. Az RBAC használata lehetővé teszi, hogy csak olyan mértékű hozzáférést biztosítson, ami a felhasználóknak a feladataik elvégzéséhez szükséges. Ez a cikk segít az RBAC beállításában és használatában az Azure Portalon. Ha további részleteket szeretne arról, hogy hogyan segít az RBAC a hozzáférések kezelésében, tekintse meg [a szerepköralapú hozzáférés-vezérlést](overview.md) ismertető szakaszt.

Előfizetésenként 2000 szerepkör-hozzárendelés osztható ki. 

## <a name="view-access"></a>Hozzáférés megtekintése
Az [Azure Portal](https://portal.azure.com) fő paneljén láthatja, hogy kinek van hozzáférése egy adott erőforráshoz, erőforráscsoporthoz vagy előfizetéshez. Tegyük fel, hogy szeretnénk megnézni, hogy kinek van hozzáférése egy erőforráscsoporthoz:

1. Válassza az **Erőforráscsoportok** elemet a bal oldali navigációs sávban.  
    ![Erőforráscsoportok – ikon](./media/role-assignments-portal/resourcegroups_icon.png)
2. Válassza ki az erőforráscsoport nevét az **Erőforráscsoportok** panelen.
3. A bal oldali menüben válassza az **Access control (IAM)** lehetőséget.  
4. Az Access control panel az összes olyan felhasználót, csoportot és alkalmazást felsorolja, amely hozzáféréssel rendelkezik az erőforráscsoporthoz.  
   
    ![Képernyőfelvétel a felhasználók panelről – örökölt vagy hozzárendelt hozzáférés](./media/role-assignments-portal/view-access.png)

Figyelje meg, hogy egyes szerepkör hatóköre **erre az erőforrásra** érvényes, míg mások más hatókörökből **öröklődnek**. A hozzáférés lehet kifejezetten az erőforráscsoporthoz rendelt, vagy a szülő előfizetés egyik hozzárendeléséből örökölt.

> [!NOTE]
> Az új RBAC-modellben a hagyományos előfizetés-adminisztrátorok és társadminisztrátorok minősülnek tulajdonosoknak.

## <a name="add-access"></a>Hozzáférés felvétele
A hozzáférés a szerepkör-hozzárendelés hatókörébe tartozó erőforrásból, erőforráscsoportból vagy előfizetésből biztosítható.

1. Kattintson a **Hozzáadás** gombra az Access control panelen.  
2. A **Szerepkör kiválasztása** panelen válassza ki a felvenni kívánt szerepkört.
3. Válassza ki azt a felhasználót, csoportot vagy alkalmazást a címtárában, amely számára hozzáférést kíván biztosítani. A címtárban rákereshet megjelenítendő nevekre, e-mail-címekre és objektumazonosítókra.  
   
    ![Felhasználók hozzáadása panel – keresés – képernyőfelvétel](./media/role-assignments-portal/grant-access2.png)
4. A hozzárendelés létrehozásához kattintson az **OK** gombra. A **Felhasználó felvétele** előugró ablak nyomon követi a folyamatot.  
    ![Felhasználó felvétele folyamatjelző sáv – képernyőfelvétel](./media/role-assignments-portal/addinguser_popup.png)

A szerepkör-hozzárendelés a sikeres felvétel után megjelenik a **Felhasználók** panelen.

## <a name="remove-access"></a>Hozzáférés megszüntetése
1. Vigye az egérmutatót az eltávolítani kívánt hozzárendelés fölé. Ekkor megjelenik egy jelölőnégyzet a név mellett.
2. A jelölőnégyzetek segítségével válasszon ki egy vagy több szerepkör-hozzárendelést.
2. Válassza az **Eltávolítás** lehetőséget.  
3. Válassza az **Igen** lehetőséget az eltávolítás megerősítéséhez.

Az örökölt hozzárendeléseket nem lehet eltávolítani. Ha egy örökölt hozzárendelést kell eltávolítania, azt abban a hatókörben kell megtennie, ahol a szerepkör-hozzárendelés létrejött. A **Hatókör** oszlopban az **Örökölt** elem melletti hivatkozás azokra az erőforrásokra mutat, ahol az adott szerepkör hozzárendelése megtörtént. Lépjen az ott szereplő erőforrásra a szerepkör-hozzárendelés eltávolításához.

![Felhasználók panel – az örökölt hozzáférés letiltja az eltávolítás gombot – képernyőfelvétel](./media/role-assignments-portal/remove-access2.png)

## <a name="other-tools-to-manage-access"></a>Más eszközök a hozzáférés kezelésére
A szerepkörök hozzárendelését és a hozzáférések kezelését más eszközökön is elvégezheti az Azure RBAC-parancsokkal, nem csak az Azure Portalon.  Az alábbi hivatkozásokat követve további információkat szerezhet az előfeltételekről, és megismerkedhet az Azure RBAC-parancsok használatával.

* [Azure PowerShell](role-assignments-powershell.md)
* [Azure parancssori felület](role-assignments-cli.md)
* [REST API](role-assignments-rest.md)

## <a name="next-steps"></a>További lépések
* [Jelentés létrehozása a hozzáférés-módosítások előzményeiről](change-history-report.md)
* Lásd: [Beépített RBAC-szerepkörök](built-in-roles.md)
* Saját [egyéni szerepkörök az Azure RBAC-ben](custom-roles.md)

