---
title: Dinamikus csoportok használata Azure Automation Update Management
description: Ez a cikk azt ismerteti, hogyan használhatók a dinamikus csoportok Azure Automation Update Managementokkal.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: c440862f1379983d4644fe99f33207456f747d23
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830939"
---
# <a name="use-dynamic-groups-with-update-management"></a>Dinamikus csoportok használata az Update Managementtel

A Update Management lehetővé teszi az Azure-beli vagy nem Azure-beli virtuális gépek dinamikus csoportjának megcélzását a frissítések központi telepítéséhez. A dinamikus csoportok használatával megtarthatja az üzemelő példányok frissítését a számítógépekre.

> [!NOTE]
> A dinamikus csoportok nem működnek a klasszikus virtuális gépekkel.

Az Azure-beli és a nem Azure-beli gépek dinamikus csoportjait a Azure Portal **Update Management** szolgáltatásában adhatja meg. Lásd: [több Azure-beli virtuális gép frissítéseinek kezelése](manage-update-multi.md).

A dinamikus csoportokat egy olyan lekérdezés határozza meg, amely Azure Automation kiértékelése a központi telepítés idején történik. Ha a dinamikus csoportos lekérdezés nagy mennyiségű gépet kér le, Azure Automation egyszerre legfeljebb 1000 gépet dolgozhat fel. Tekintse meg a következőt: [Az Azure-előfizetések és -szolgáltatások korlátozásai, kvótái és megkötései](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#update-management). 

> [!NOTE]
> Ha várhatóan több mint 1000 gépet szeretne frissíteni, javasoljuk, hogy több frissítési ütemterv között ossza fel a frissítéseket. 

## <a name="define-dynamic-groups-for-azure-machines"></a>Dinamikus csoportok definiálása az Azure Machines szolgáltatáshoz

Az Azure-gépek dinamikus csoportos lekérdezésének meghatározásakor a következő elemek használatával töltheti fel a dinamikus csoportot:

* Előfizetés
* Erőforráscsoportok
* Helyek
* Címkék

![Csoportok kiválasztása](./media/automation-update-management/select-groups.png)

A dinamikus csoportos lekérdezés eredményének megtekintéséhez kattintson az **előnézet**gombra. Az előnézet megjeleníti a csoporttagság aktuális időpontját. A példában olyan gépeket keresünk, amelyek címkével rendelkeznek a `Role` csoport **BackendServer**. Ha további gépek szerepelnek a címkén, akkor azokat a rendszer az adott csoportra vonatkozó jövőbeli központi telepítésekhez adja hozzá.

![előzetes verziójú csoportok](./media/automation-update-management/preview-groups.png)

## <a name="define-dynamic-groups-for-non-azure-machines"></a>Dinamikus csoportok definiálása nem Azure-beli gépekhez

A nem Azure-alapú gépek dinamikus csoportja mentett kereséseket, más néven számítógépcsoportot használ. A mentett keresések létrehozásáról további információt a [számítógépcsoport létrehozása](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)című témakörben talál. A mentett keresés létrehozása után kiválaszthatja azt a mentett keresések listájában a Azure Portal **Update Management szolgáltatásban** . Az **előnézet** gombra kattintva megtekintheti a mentett keresésben lévő számítógépeket.

![Csoportok kiválasztása](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>További lépések

A Update Management használatáról az Azure-beli [virtuális gépek frissítéseinek és javításának kezelése](automation-tutorial-update-management.md)című témakörben olvashat.
