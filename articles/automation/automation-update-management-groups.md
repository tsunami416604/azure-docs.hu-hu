---
title: Dinamikus csoportok használata az Azure Update Management szolgáltatással
description: Ez a cikk ismerteti, hogyan működik a dinamikus csoportok az Azure Automation Update Management.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 678b3f361e4456a2c482896f7d7dc20d530b917b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75420403"
---
# <a name="use-dynamic-groups-with-update-management"></a>Dinamikus csoportok használata az Update Managementtel

Update Management lehetővé teszi, hogy célzott dinamikus Azure-csoport vagy nem Azure-beli virtuális gépek a frissítési központi telepítések. Ezek a csoportok kiértékelése üzembe helyezéskor történik, így nem kell a központi telepítést a gépek hozzáadásához.

## <a name="azure-machines"></a>Azure-gépek

Ezeket a csoportokat egy lekérdezés határozza meg, amikor egy frissítés központi telepítése megkezdődik, a csoport tagjait kiértékeli a rendszer. A dinamikus csoportok nem működnek a klasszikus virtuális gépek. A lekérdezés definiálásakor a következő elemek használhatók együtt a dinamikus csoport feltöltéséhez:

* Előfizetés
* Erőforráscsoportok
* Helyek
* Címkék

![Csoportok kijelölése](./media/automation-update-management/select-groups.png)

A dinamikus csoport eredményeinek megtekintéséhez kattintson az **Előnézet** gombra. Ez az előnézet mutatja a csoporttagság abban az időben, ebben a példában keresünk gépek a címke **Szerepkör** egyenlő **BackendServer**. Ha több gép hez hozzáadódik ez a címke, akkor azok hozzá adódnak az adott csoport on-t jövőbeli központi telepítésekhez.

![előnézeti csoportok](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Nem Azure-alapú gépek

A nem Azure-alapú gépek, mentett keresések is nevezik számítógépcsoportok, a dinamikus csoport létrehozásához használt. A mentett keresés létrehozásáról a [Számítógépcsoport létrehozása](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)című témakörben olvashat. A csoport létrehozása után kiválaszthatja azt a mentett keresések listájából. Az **Előnézet gombra** kattintva megtekintheti az adott időpontban mentett keresésben lévő számítógépek előnézetét.

![Csoportok kijelölése](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>További lépések

Dinamikus csoport létrehozása után [létrehozhat egy központi telepítést](automation-tutorial-update-management.md)
