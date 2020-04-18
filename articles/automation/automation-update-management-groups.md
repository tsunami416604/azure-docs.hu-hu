---
title: Dinamikus csoportok használata az Azure Automation Update Management szolgáltatással
description: Ez a cikk ismerteti, hogyan működik a dinamikus csoportok az Azure Automation Update Management.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 29a72eb1fe7b8be18cd2160fc63160e408378585
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617437"
---
# <a name="use-dynamic-groups-with-update-management"></a>Dinamikus csoportok használata az Update Managementtel

Update Management lehetővé teszi, hogy az Azure vagy a nem Azure virtuális gépek dinamikus csoportját célozza meg a frissítési központi telepítésekhez. Ezek a lekérdezések által meghatározott csoportok kiértékelése üzembe helyezéskor történik, így nem kell a központi telepítést a gépek hozzáadásához.

## <a name="azure-machines"></a>Azure-gépek

A dinamikus csoportok nem működnek a klasszikus virtuális gépek. A lekérdezés definiálásakor a következő elemek használhatók együtt egy dinamikus csoport feltöltéséhez:

* Előfizetés
* Erőforráscsoportok
* Helyek
* Címkék

![Csoportok kijelölése](./media/automation-update-management/select-groups.png)

A dinamikus csoport eredményeinek megtekintéséhez kattintson az **Előnézet gombra.** Az előnézet az aktuális időpontban mutatja a csoporttagságot. A példában olyan gépeket keresünk, `Role` amelyek a **BackendServer**csoport címkéjével rendelkeznek. Ha több gép hez hozzáadódik ez a címke, azok hozzáadódnak az adott csoport on-k on-t a jövőbeli központi telepítésekhez.

![előnézeti csoportok](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Nem Azure-alapú gépek

A nem Azure-alapú gépek, mentett keresések, más néven számítógépcsoportok, a dinamikus csoport létrehozásához használt. A mentett keresés létrehozásáról a [Számítógépcsoport létrehozása](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)című témakörben olvashat. A csoport létrehozása után kiválaszthatja azt a mentett keresések listájából. Az **Előnézet gombra** kattintva megtekintheti az adott időpontban mentett keresésben lévő számítógépek előnézetét.

![Csoportok kijelölése](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>További lépések

Dinamikus csoport létrehozása után [létrehozhat egy központi telepítést.](automation-tutorial-update-management.md)
