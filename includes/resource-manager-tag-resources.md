---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 01/03/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 6d8f29e9272f3e05bb585c032240cce67c37d2bf
ms.sourcegitcommit: 2c59a05cb3975bede8134bc23e27db5e1f4eaa45
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2020
ms.locfileid: "75665143"
---
1. Egy adott erőforrás vagy erőforráscsoport címkéi megtekintéséhez keresse meg a meglévő címkéket az áttekintésben. Ha eddig még nem vett fel címkéket, a lista üres.
   
     ![Erőforrás vagy erőforráscsoport címkéinak megtekintése](./media/resource-manager-tag-resources/view-tags.png)
1. Címke hozzáadásához válassza a **címkék hozzáadásához kattintson ide**.

1. Adjon meg egy nevet és egy értéket. A címke hozzáadásához válassza a **+** lehetőséget.
   
     ![Címke hozzáadása](./media/resource-manager-tag-resources/add-tag.png)
1. Szükség szerint folytassa a címkék hozzáadását. Ha elkészült, kattintson a **Mentés** elemre.
   
     ![Címkék mentése](./media/resource-manager-tag-resources/save-tags.png)
1. A címkék most már megjelennek az áttekintésben.
   
     ![Címkék megjelenítése](./media/resource-manager-tag-resources/view-new-tags.png)
1. Címke hozzáadásához vagy törléséhez válassza a **módosítás**lehetőséget.
   
1. Címke törléséhez válassza a Kuka ikont. Ezt követően válassza a **Mentés** lehetőséget.

     ![Címke törlése](./media/resource-manager-tag-resources/delete-tag.png)


Címkék tömeges kiosztása több erőforráshoz:

1. Az erőforrások listájából válassza ki a címkét hozzárendelni kívánt erőforrások jelölőnégyzetét.

     ![Több erőforrás kiválasztása](./media/resource-manager-tag-resources/select-multiple-resources.png)

1. **Címkék hozzárendelésének** kiválasztása

     ![Címkék hozzárendelése](./media/resource-manager-tag-resources/assign-tags.png)

1. A név és az érték után válassza a **+** lehetőséget. Ha elkészült, válassza a **hozzárendelés**lehetőséget.

     ![Hozzárendelés kiválasztása](./media/resource-manager-tag-resources/select-assign.png)

A címkével rendelkező összes erőforrás megtekintése:

1. A Azure Portal menüben válassza a **minden szolgáltatás**lehetőséget. Válassza az **általános**, majd a **címkék**lehetőséget.

     ![Keresés címke szerint](./media/resource-manager-tag-resources/find-tags-general.png)

1. Válassza ki a címkét az erőforrások megtekintéséhez.

     ![Címke kiválasztása](./media/resource-manager-tag-resources/select-tag.png)

1. Megjelenik az adott címkével rendelkező összes erőforrás.

     ![Erőforrások megtekintése címke szerint](./media/resource-manager-tag-resources/view-resources-by-tag.png)
