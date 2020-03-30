---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 03/19/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: a00291182059506aeab9cde965fa4cbd5177ecf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132168"
---
Ha egy felhasználó nem rendelkezik a címkék alkalmazásához szükséges hozzáféréssel, hozzárendelheti a **Címka közreműködői** szerepkört a felhasználóhoz. További információ: [Oktatóanyag: Felhasználói hozzáférés biztosítása az Azure-erőforrásokhoz az RBAC és az Azure Portal használatával.](../articles/role-based-access-control/quickstart-assign-role-user-portal.md)

1. Egy erőforrás vagy erőforráscsoport címkéinek megtekintéséhez keresse meg a meglévő címkéket az áttekintésben. Ha eddig még nem vett fel címkéket, a lista üres.

   ![Erőforrás vagy erőforráscsoport címkéinek megtekintése](./media/resource-manager-tag-resources/view-tags.png)

1. Címke hozzáadásához jelölje be **a Kattintson ide jelölőnégyzetet a címkék hozzáadásához.**

1. Adjon meg egy nevet és egy értéket.

   ![Címke hozzáadása](./media/resource-manager-tag-resources/add-tag.png)

1. Szükség szerint folytassa a címkék hozzáadását. Ha elkészült, kattintson a **Mentés** elemre.

   ![Címkék mentése](./media/resource-manager-tag-resources/save-tags.png)

1. A címkék most antól megjelennek az áttekintésben.

   ![Címkék megjelenítése](./media/resource-manager-tag-resources/view-new-tags.png)

1. Címke hozzáadásához vagy törléséhez válassza a **Módosítás**lehetőséget.

1. Címke törléséhez jelölje ki a kuka ikont. Ezután válassza a **Mentés gombot.**

   ![Címke törlése](./media/resource-manager-tag-resources/delete-tag.png)

Címkék tömeges hozzárendelése több erőforráshoz:

1. Az erőforrások bármelyik listájából jelölje be a címkét hozzárendelni kívánt erőforrások jelölőnégyzetét. Ezután válassza **a Címkék hozzárendelése**lehetőséget.

   ![Több erőforrás kijelölése](./media/resource-manager-tag-resources/select-multiple-resources.png)

1. Nevek és értékek hozzáadása. Ha elkészült, kattintson a **Mentés** elemre.

   ![Hozzárendelés kiválasztása](./media/resource-manager-tag-resources/select-assign.png)

Az összes erőforrás megtekintése címkével:

1. Az Azure Portal menüben keressen **címkéket.** Válassza ki a rendelkezésre álló lehetőségek közül.

   ![Keresés címke szerint](./media/resource-manager-tag-resources/find-tags-general.png)

1. Válassza ki az erőforrások megtekintéséhez szükséges címkét.

   ![Címke kijelölése](./media/resource-manager-tag-resources/select-tag.png)

1. Az ezzel a címkével rendelkező összes erőforrás megjelenik.

   ![Erőforrások megtekintése címke szerint](./media/resource-manager-tag-resources/view-resources-by-tag.png)
