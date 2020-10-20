---
title: A virtuális gépek frissítéseinek és javításának kezelése Azure Automation
description: Ez a cikk azt ismerteti, hogyan használhatók a Update Management az Azure-beli és nem Azure-beli virtuális gépek frissítéseinek és javításának kezeléséhez.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 07/28/2020
ms.custom: mvc
ms.openlocfilehash: 24dcb501872aabf9fac3da0cccc2a1af9c9b06ff
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92222841"
---
# <a name="manage-updates-and-patches-for-your-vms"></a>A virtuális gépek frissítéseinek és javításának kezelése

A Azure Automation Update Management található szoftverfrissítések olyan eszközöket és erőforrásokat biztosítanak, amelyek segítségével kezelhető az Azure-ban és a hibrid felhőben lévő gépekre vonatkozó szoftverfrissítések nyomon követésének és alkalmazásának összetett feladata. A hatékony szoftverfrissítés-kezelési folyamat szükséges a működési hatékonyság fenntartásához, a biztonsági problémák megoldásához, valamint a megnövekedett számítógépes biztonsági fenyegetések kockázatának csökkentéséhez. A technológia változó jellege és a folyamatosan felmerülő új biztonsági fenyegetések miatt azonban a szoftverfrissítések hatékony felügyelete következetes és folytonos figyelmet igényel.

> [!NOTE]
> A Update Management támogatja az első féltől származó frissítések telepítését és azok letöltését. Ehhez a támogatáshoz módosításokat kell végezni a frissítendő rendszereken. A beállítások a rendszereken való konfigurálásával kapcsolatban lásd: [Windows Update beállításainak konfigurálása Azure Automation Update Management](configure-wuagent.md) .

A virtuális gépek frissítéseinek kezelése előtt győződjön meg arról, hogy a következő módszerek egyikével engedélyezte Update Managementét:

* [Az Update Management engedélyezése Automation-fiókból](enable-from-automation-account.md)
* [Update Management engedélyezése a Azure Portal tallózásával](enable-from-portal.md)
* [Az Update Management engedélyezése runbookból](enable-from-runbook.md)
* [Az Update Management engedélyezése Azure-beli virtuális gépről](enable-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>A központi telepítés hatókörének korlátozása

Update Management a munkaterületen belüli hatókör-konfigurációval célozza meg a számítógépeket a frissítések fogadásához. További információ: a [Update Management központi telepítési hatókörének korlátozása](scope-configuration.md).

## <a name="compliance-assessment"></a>Megfelelőségi értékelés

Mielőtt telepítené a számítógépekre a szoftverfrissítéseket, tekintse át az engedélyezett gépek frissítési megfelelőségi felmérésének eredményeit. Minden szoftverfrissítés esetében a megfelelőségi állapot rögzítése történik, majd a kiértékelés után a rendszer a begyűjtést és a naplókban Azure Monitor való továbbítását is elvégzi.

Windows rendszerű gépen a megfelelőségi vizsgálat alapértelmezés szerint 12 óránként fut. Az ütemezett vizsgálat mellett a frissítés megfelelőségének vizsgálata a Windows-ügynök újraindítása és a frissítés telepítése előtt 15 Log Analytics percen belül megkezdődik. Érdemes áttekinteni a Windows Update-ügyfél Update Management használatával történő [konfigurálásának](configure-wuagent.md) javaslatait is, hogy elkerülje azokat a problémákat, amelyek megakadályozzák a megfelelő felügyeletet.

Linux rendszerű gépek esetén a megfelelőségi vizsgálat alapértelmezés szerint óránként történik. Ha a Linux rendszerhez készült Log Analytics-ügynök újra lett indítva, a megfelelőségi vizsgálat 15 percen belül megkezdődik.

A megfelelőségi eredményeket a rendszer minden egyes kiértékelni kívánt gép Update Management ismerteti. A felügyelethez engedélyezett új gépek esetében akár 30 percet is igénybe vehet, hogy az irányítópulton megjelenjenek a frissített adatok.

Tekintse át a [szoftverfrissítések figyelése](view-update-assessments.md) című témakört, és ismerkedjen meg a megfelelőségi eredmények megtekintésével.

## <a name="deploy-updates"></a>Frissítések telepítése

A megfelelőségi eredmények áttekintése után a szoftverfrissítés központi telepítési fázisa a szoftverfrissítések központi telepítésének folyamata. A frissítések telepítéséhez ütemezzen egy központi telepítést, amely igazodik a kiadási ütemtervhez és a szolgáltatás ablakához. Kiválaszthatja, hogy a telepítés milyen típusú frissítéseket tartalmazzon. Például hozzáadhatja a kritikus vagy a biztonsági frissítéseket, és kizárhatja a kumulatív frissítéseket.

Tekintse át a [szoftverfrissítések központi telepítése](deploy-updates.md) című témakört, amelyből megtudhatja, hogyan ütemezhet egy frissítés telepítését.

## <a name="review-update-deployments"></a>Frissítések központi telepítésének áttekintése

Az üzembe helyezés befejezése után tekintse át a telepítés sikerességét a számítógép vagy a célcsoport alapján. A telepítés állapotának figyeléséhez tekintse meg a [központi telepítés állapotának áttekintése](deploy-updates.md#check-deployment-status) című témakört.

## <a name="next-steps"></a>Következő lépések

* Ha meg szeretné tudni, hogyan hozhat létre riasztásokat a telepítési eredmények frissítésével kapcsolatban, tekintse meg [a riasztások létrehozása a Update Managementhoz](configure-alerts.md)című témakört.

* [Azure monitor naplók lekérdezésével](query-logs.md) elemezheti a frissítési felméréseket, a központi telepítéseket és az egyéb kapcsolódó felügyeleti feladatokat. Előre definiált lekérdezéseket tartalmaz, amelyek segítenek az első lépések megtételében.