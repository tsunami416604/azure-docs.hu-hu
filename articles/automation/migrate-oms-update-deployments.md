---
title: Az OMS-frissítési telepítések áttelepítése az Azure-ba
description: Ez a cikk bemutatja, hogyan telepítheti át a meglévő OMS-frissítési központi telepítéseket az Azure-ba
services: automation
ms.subservice: update-management
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: 2660e4a348d2ffd71f912ff80c36a5a9a3c9fe88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75417787"
---
# <a name="migrate-your-oms-update-deployments-to-azure"></a>Az OMS-frissítési telepítések áttelepítése az Azure-ba

Az Operations Management Suite (OMS) portál [elavult.](../azure-monitor/platform/oms-portal-transition.md) Az OMS-portálon az Update Management szolgáltatáshoz elérhető összes funkció elérhető az Azure Portalon. Ez a cikk az Azure Portalra való áttelepítéshez szükséges információkat tartalmazza.

## <a name="key-information"></a>Legfontosabb információk

* A meglévő telepítések továbbra is működni fognak. Miután újra létrehozta a központi telepítést az Azure-ban, törölheti a régi üzembe helyezést az OMS-ből.
* Az OMS-ben már meglévő összes funkció elérhető az Azure-ban, ha többet szeretne megtudni az Update Management szolgáltatásról, olvassa [el az Update Management áttekintéscímű témakört.](automation-update-management.md)

## <a name="access-the-azure-portal"></a>Az Azure Portal elérése

Az OMS-munkaterületen kattintson **a Megnyitás az Azure-ban**gombra. Ez az OMS által használt Log Analytics-munkaterületre navigál.

![Megnyitás az Azure-ban - OMS portál](media/migrate-oms-update-deployments/link-to-azure-portal.png)

Az Azure Portalon kattintson az **Automation-fiók elemre.**

![Azure Monitor-naplók](media/migrate-oms-update-deployments/log-analytics.png)

Az Automation-fiókban kattintson **a Frissítéskezelés** elemre az Update Management megnyitásához.

![Frissítéskezelés](media/migrate-oms-update-deployments/azure-automation.png)

A jövőben közvetlenül az Azure Portalra léphet, a **Minden szolgáltatás**csoportban válassza az **Automation-fiókok lehetőséget** a Felügyeleti **eszközök**csoportban, válassza ki a megfelelő Automation-fiókot, majd kattintson a **Frissítéskezelés gombra.**

## <a name="recreate-existing-deployments"></a>Meglévő telepítések újbóli létrehozása

Az OMS-portálon létrehozott összes frissítési központi telepítés nek van egy [mentett keresése,](../azure-monitor/platform/computer-groups.md) amelyet számítógépcsoportnak is neveznek, és amelynek neve megegyezik a létező frissítésközponti telepítés nevével. A mentett keresés a frissítés központi telepítésében ütemezett gépek listáját tartalmazza.

![Frissítéskezelés](media/migrate-oms-update-deployments/oms-deployment.png)

A meglévő mentett keresés használatához kövesse az alábbi lépéseket:

Új központi telepítés létrehozásához nyissa meg az Azure Portalt, válassza ki a használt Automation-fiókot, és kattintson a **Frissítéskezelés gombra.** Kattintson **a Frissítés telepítésének ütemezése gombra.**

![Frissítés telepítésének ütemezése](media/migrate-oms-update-deployments/schedule-update-deployment.png)

Megnyílik **az Új frissítés központi telepítése** ablaktábla. Adja meg az alábbi táblázatban leírt tulajdonságok értékeit, majd kattintson a **Létrehozás gombra:**

A gépek frissítéséhez válassza ki a meglévő OMS-telepítés által használt mentett keresést.

| Tulajdonság | Leírás |
| --- | --- |
|Név |A frissítéstelepítést beazonosító egyedi név. |
|Operációs rendszer| Válassza a **Linux** vagy **a Windows**lehetőséget.|
|Frissíthető gépek |Jelöljön ki egy Mentett keresést, importált csoportot, vagy válassza a Gép elemet a legördülő menüből, és válassza ki az egyes gépeket. Ha a **Gépek** lehetőséget választotta, a gép állapota az **ÜGYNÖK KÉSZÜLTSÉGÉNEK FRISSÍTÉSE** oszlopban látható.</br> A számítógépcsoportok létrehozásának különböző módszereiről az Azure Monitor naplóiban a [Számítógépcsoportok az Azure Monitor naplóiban című témakörben olvashat.](../azure-monitor/platform/computer-groups.md) |
|Frissítési besorolások|Válassza ki az összes szükséges frissítési besorolást. A CentOS ezt nem támogatja a dobozból.|
|Kizárandó frissítések|Adja meg a kizárandó frissítéseket. Windows esetén írja be a tudásbáziscikket a **tudásbáziselőtag** nélkül. Linux esetén írja be a csomag nevét, vagy használjon helyettesítő karaktert.  |
|Ütemezési beállítások|Válassza ki az indítási időt, majd válassza az **Egyszer** vagy az **Ismétlődés lehetőséget** az ismétlődéshez. | 
| Karbantartási ablak |A frissítésekhez beállított percek száma. Az érték nem lehet kevesebb, mint 30 perc vagy 6 óránál. |
| Újraindítás vezérlése| Meghatározza az újraindítások kezelésének módját.</br>Az elérhető lehetőségek:</br>Újraindítás szükség esetén (alapértelmezett beállítás)</br>Mindig induljon újra</br>Soha ne induljon újra</br>Csak újraindítás – frissítések nem lesznek telepítve|

Kattintson **az Ütemezett frissítés központi telepítései** elemre az újonnan létrehozott frissítési központi telepítés állapotának megtekintéséhez.

![új frissítés központi telepítése](media/migrate-oms-update-deployments/new-update-deployment.png)

Mint korábban említettük, miután az új üzemelő példányok konfigurálása az Azure Portalon keresztül, a meglévő központi telepítések eltávolíthatók az OMS-portálról.

## <a name="next-steps"></a>További lépések

Ha többet szeretne tudni az Azure-beli frissítéskezelésről, olvassa el [az Update Management (Frissítéskezelés) témakört.](automation-update-management.md)
