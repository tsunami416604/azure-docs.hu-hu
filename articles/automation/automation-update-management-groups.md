---
title: Dinamikus csoportok használata Azure Automation Update Management
description: Ez a cikk azt ismerteti, hogyan működnek a dinamikus csoportok a Azure Automation Update Managementokkal.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 29a72eb1fe7b8be18cd2160fc63160e408378585
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617437"
---
# <a name="use-dynamic-groups-with-update-management"></a>Dinamikus csoportok használata az Update Managementtel

A Update Management lehetővé teszi az Azure-beli vagy nem Azure-beli virtuális gépek dinamikus csoportjának megcélzását a frissítések központi telepítéséhez. A lekérdezések által definiált csoportok kiértékelése a központi telepítés idejére történik, így nem kell módosítania a központi telepítést a gépek hozzáadásához.

## <a name="azure-machines"></a>Azure-gépek

A dinamikus csoportok nem működnek a klasszikus virtuális gépekkel. A lekérdezés meghatározásakor a következő elemek együtt használhatók dinamikus csoportok feltöltéséhez:

* Előfizetés
* Erőforráscsoportok
* Helyek
* Címkék

![Csoportok kiválasztása](./media/automation-update-management/select-groups.png)

Egy dinamikus csoport eredményének megtekintéséhez kattintson az **előnézet**gombra. Az előnézet megjeleníti a csoporttagság aktuális időpontját. A példában olyan gépeket keresünk, amelyek címkével `Role` rendelkeznek a csoport **BackendServer**. Ha további gépek szerepelnek a címkén, akkor azokat a rendszer az adott csoportra vonatkozó jövőbeli központi telepítésekhez adja hozzá.

![előzetes verziójú csoportok](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Nem Azure-beli gépek

A nem Azure-beli gépek esetében a mentett keresések, más néven számítógépcsoportok, a dinamikus csoport létrehozásához használatosak. A mentett keresések létrehozásáról további információt a [számítógépcsoport létrehozása](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)című témakörben talál. A csoport létrehozása után kiválaszthatja a mentett keresések listájából. Az **előnézet** gombra kattintva megtekintheti a mentett keresésben lévő számítógépeket.

![Csoportok kiválasztása](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>További lépések

A dinamikus csoport létrehozása után [létrehozhat egy frissítés központi telepítését](automation-tutorial-update-management.md).
