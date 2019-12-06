---
title: OMS-frissítés telepítése az Azure-ba
description: Ez a cikk azt ismerteti, hogyan telepítheti át meglévő OMS-frissítésének üzembe helyezését az Azure-ba
services: automation
ms.service: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 07/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 08b3f11f3e44c6580df9942aab2a890115c79ba3
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849496"
---
# <a name="migrate-your-oms-update-deployments-to-azure"></a>OMS-frissítés telepítése az Azure-ba

Az Operations Management Suite-(OMS-) portál [elavult](../azure-monitor/platform/oms-portal-transition.md). A OMS-portálon Update Management összes funkciója elérhető a Azure Portal. Ez a cikk a Azure Portalre való Migrálás érdekében szükséges információkat tartalmazza.

## <a name="key-information"></a>Fontos információ

* A meglévő telepítések továbbra is működni fognak. Miután újra létrehozta az üzembe helyezést az Azure-ban, törölheti a régi üzembe helyezést a OMS-ből.
* A OMS összes meglévő funkciója elérhető az Azure-ban, hogy többet tudjon meg a Update Managementről: [Update Management áttekintése](automation-update-management.md).

## <a name="access-the-azure-portal"></a>Az Azure Portal elérése

A OMS munkaterületen kattintson a **Megnyitás az Azure-ban**lehetőségre. Ez a OMS használt Log Analytics munkaterületre navigál.

![Megnyitás az Azure-OMS portálon](media/migrate-oms-update-deployments/link-to-azure-portal.png)

A Azure Portal kattintson az **Automation-fiók** elemre.

![Azure Monitor-naplók](media/migrate-oms-update-deployments/log-analytics.png)

Az Automation-fiókjában kattintson a **Update Management** elemre Update Management megnyitásához.

![Frissítéskezelés](media/migrate-oms-update-deployments/azure-automation.png)

A jövőben közvetlenül a Azure Portalra léphet, a **minden szolgáltatás**területen válassza az Automation- **fiókok** lehetőséget a **felügyeleti eszközök**alatt, válassza ki a megfelelő Automation-fiókot, és kattintson a **Update Management**elemre.

## <a name="recreate-existing-deployments"></a>Meglévő központi telepítések újbóli létrehozása

A OMS-portálon létrehozott összes frissítési központi telepítésnek más néven számítógépcsoport [néven is](../azure-monitor/platform/computer-groups.md) ismertnek kell lennie, amelynek a neve megegyezik a létező frissítési központi telepítéssel. A mentett keresés a frissítés központi telepítésében ütemezett számítógépek listáját tartalmazza.

![Frissítéskezelés](media/migrate-oms-update-deployments/oms-deployment.png)

A meglévő mentett keresés használatához kövesse az alábbi lépéseket:

Új frissítés központi telepítésének létrehozásához nyissa meg a Azure Portal, válassza ki a használt Automation-fiókot, majd kattintson az **Update Management**elemre. Kattintson a **frissítés központi telepítésének ütemezett frissítése**elemre.

![Frissítéstelepítések ütemezése](media/migrate-oms-update-deployments/schedule-update-deployment.png)

Megnyílik az **új frissítés központi telepítése** panel. Adja meg az alábbi táblázatban leírt tulajdonságok értékeit, majd kattintson a **Létrehozás**gombra:

A frissíteni kívánt gépek esetében válassza ki a meglévő OMS-telepítés által használt mentett keresést.

| Tulajdonság | Leírás |
| --- | --- |
|Név |A frissítéstelepítést beazonosító egyedi név. |
|Operációs rendszer| Válassza a **Linux** vagy a **Windows**lehetőséget.|
|Frissítendő gépek |Válasszon ki egy mentett keresést, importált csoportot, vagy válasszon gépet a legördülő listából, és válassza az egyes gépek lehetőséget. Ha a **Gépek** lehetőséget választotta, a gép állapota az **ÜGYNÖK KÉSZÜLTSÉGÉNEK FRISSÍTÉSE** oszlopban látható.</br> A számítógépcsoportok Azure Monitor-naplókban való létrehozásának különböző módszereiről a következő témakörben talál további információt: [számítógépcsoportok Azure monitor-naplókban](../azure-monitor/platform/computer-groups.md) |
|Frissítési besorolások|Válassza ki az összes szükséges frissítési besorolást. A CentOS nem támogatja ezt a jelölőnégyzetet.|
|Kizárási frissítések|Adja meg a kizárni kívánt frissítéseket. Windows esetén **a KB-os előtag nélkül** adja meg a tudásbázist. Linux esetén adja meg a csomag nevét, vagy használjon helyettesítő karaktert.  |
|Ütemezési beállítások|Válassza ki a kezdéshez szükséges időt, majd válassza ki az ismétlődést **egyszer** vagy **ismétlődően** . | 
| Karbantartási időszak |A frissítések számára beállított percek száma. Az érték nem lehet kevesebb, mint 30 perc vagy több mint 6 óra. |
| Vezérlő újraindítása| Meghatározza az újraindítások kezelését.</br>Az elérhető lehetőségek:</br>Újraindítás szükség esetén (alapértelmezett beállítás)</br>Mindig induljon újra</br>Soha ne induljon újra</br>Csak újraindítás – frissítések nem lesznek telepítve|

Az újonnan létrehozott frissítés központi telepítésének állapotának megtekintéséhez kattintson az **ütemezett frissítések központi telepítések** elemre.

![új frissítés telepítése](media/migrate-oms-update-deployments/new-update-deployment.png)

Amint azt korábban említettük, az új központi telepítések a Azure Portal használatával konfigurálhatók, a meglévő központi telepítések eltávolíthatók a OMS-Portálról.

## <a name="next-steps"></a>Következő lépések

További információ az Azure-beli Update Managementről: [Update Management](automation-update-management.md)
