---
title: Dinamikus csoportok használata az Azure Update Management
description: leírja, hogyan működnek együtt a dinamikus csoportok Update Management
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 10/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: edc4384be0f1dc73f2e7e098114080d304d92ce8
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377745"
---
# <a name="use-dynamic-groups-with-update-management"></a>Dinamikus csoportok használata Update Management

A Update Management lehetővé teszi az Azure-beli vagy nem Azure-beli virtuális gépek dinamikus csoportjának megcélzását a frissítések központi telepítéséhez. A rendszer kiértékeli ezeket a csoportokat a központi telepítési időszakban, így nem kell a központi telepítést a gépek hozzáadásához módosítania.

## <a name="azure-machines"></a>Azure-gépek

Ezeket a csoportokat egy lekérdezés határozza meg, amikor a frissítés központi telepítése megkezdődik, a rendszer kiértékeli a csoport tagjait. A dinamikus csoportok nem működnek a klasszikus virtuális gépekkel. A lekérdezés meghatározásakor a következő elemek használhatók együtt a dinamikus csoport feltöltéséhez:

* Előfizetés
* Erőforráscsoportok
* Földrajzi egységek
* Címkék

![Csoportok kiválasztása](./media/automation-update-management/select-groups.png)

Egy dinamikus csoport eredményének megtekintéséhez kattintson a **Preview (előnézet** ) gombra. Ez az előzetes verzió megjeleníti a csoporttagság időpontját, ebben a példában a címke **szerepkörrel** rendelkező gépeket keresünk **BackendServer**. Ha további gépek is szerepelnek a címkében, a rendszer hozzáadja azokat az adott csoportra vonatkozó jövőbeli központi telepítésekhez.

![előzetes verziójú csoportok](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Nem Azure-beli gépek

A nem Azure-beli gépek esetében a mentett keresések, amelyeket számítógépcsoportokként is emlegetik, a dinamikus csoport létrehozásához használatosak. A mentett keresések létrehozásáról további információt a [számítógépcsoport létrehozása](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)című témakörben talál. A csoport létrehozása után kiválaszthatja a mentett keresések listájából. Az **előnézet** gombra kattintva megtekintheti a mentett keresésben lévő számítógépeket.

![Csoportok kiválasztása](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Következő lépések

A dinamikus csoport létrehozása után [létrehozhat egy frissítés központi telepítését](automation-tutorial-update-management.md)
