---
title: Az Azure Update Management használata a Configuration Manager-ügyfelekkel
description: Ez a cikk a Microsoft Endpoint Configuration Manager ezzel a megoldással történő konfigurálásának segítése a szoftverfrissítések ConfigMgr-ügyfelekre történő telepítéséhez.
services: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: f0ca836e3b53c3cce755d45b50fe168073f0bbaa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513133"
---
# <a name="deploy-updates-to-microsoft-endpoint-configuration-manager-clients-with-update-management"></a>Frissítések telepítése a Microsoft Endpoint Configuration Manager ügyfeleihez frissítéskezeléssel

Azok az ügyfelek, akik a Microsoft Endpoint Configuration Manager programba fektettek be a számítógépek, kiszolgálók és mobileszközök kezelésére, a szoftverfrissítések kezelésének (SZUM) részeként is támaszkodnak az erejükre és az érettségükre.

A felügyelt Windows-kiszolgálókat úgy jelentheti és frissítheti, hogy szoftverfrissítési központi telepítéseket hoz létre és előrendez a Configuration Manager ben, és részletesen megkaphatja a befejezett frissítési központi telepítések állapotát az [Update Management megoldás](automation-update-management.md)segítségével. Ha a Configuration Manager t használja a frissítésmegfelelőségi jelentésekhez, de a frissítések központi telepítésének kezeléséhez nem a Windows-kiszolgálókkal, folytathatja a jelentést a Configuration Manager nek, amíg a biztonsági frissítéseket az Update Management megoldással kezelik.

## <a name="prerequisites"></a>Előfeltételek

* Az Automation-fiókhoz hozzá kell adnia az [Update Management megoldást.](automation-update-management.md)
* A Configuration Manager-környezet által jelenleg felügyelt Windows-kiszolgálóknak is jelenteniük kell a Log Analytics-munkaterületnek, amelyen az Update Management megoldás is engedélyezve van.
* Ez a funkció engedélyezve van a Configuration Manager jelenlegi ága 1606-os vagy újabb verzióban. Ha integrálni szeretné a Configuration Manager központi felügyeleti helyét vagy egy önálló elsődleges helyet az Azure Monitor naplóival és importálási gyűjteményekkel, tekintse át a [Connect Configuration Manager az Azure Monitor-naplókat.](../azure-monitor/platform/collect-sccm.md)  
* A Windows rendszerű ügynökszámítógépeket vagy a Windows Server Update Services (WSUS) szolgáltatással való kommunikációhoz kell konfigurálni, vagy a Microsoft Update szolgáltatáshoz kell hozzáféréssel rendelkezniük, ha nem kapnak biztonsági frissítéseket a Configuration Managertől.   

Az Azure IaaS-ben futtatott ügyfelek Configuration Manager-környezettel való kezelésének módja elsődlegesen az Azure-adatközpontok és az infrastruktúra közötti kapcsolattól függ. Ez a kapcsolat befolyásolja a kialakítás módosításait, amelyeket a Configuration Manager-infrastruktúrán kell elvégeznie, valamint az ezeket a szükséges módosításokat támogató járulékos költségeket. Annak megismeréséhez, hogy milyen tervezési szempontokat kell értékelnie a továbblépés előtt, tekintse meg az [Azure-beli Configuration Managerrel kapcsolatos gyakori kérdéseket](https://docs.microsoft.com/configmgr/core/understand/configuration-manager-on-azure#networking).

## <a name="configuration"></a>Konfiguráció

### <a name="manage-software-updates-from-configuration-manager"></a>Szoftverfrissítések kezelése a Configuration Managerből 

Hajtsa végre a következő lépéseket, ha a frissítéstelepítések kezelését továbbra is a Configuration Managerből szeretné elvégezni. Az Azure Automation csatlakozik a Configuration Manager hez, hogy frissítéseket alkalmazzon a Log Analytics-munkaterülethez csatlakoztatott ügyfélszámítógépekre. A frissítés tartalma ugyanúgy elérhető az ügyfélszámítógép gyorsítótárából, mintha a telepítést a Configuration Manager kezelte volna.

1. Hozzon létre egy szoftverfrissítési központi telepítést a Configuration Manager-hierarchia legfelső szintű helyéről a [szoftverfrissítések telepítése](https://docs.microsoft.com/configmgr/sum/deploy-use/deploy-software-updates)című segédprogramban ismertetett eljárás sal. Az egyetlen beállítás, amelyet egy standard telepítéstől eltérően kell konfigurálnia, a **Ne telepítse a szoftverfrissítéseket** kiválasztása, amely a telepítőcsomag letöltésének viselkedését vezérli. Ezt a viselkedést az Update Management megoldás kezeli egy ütemezett frissítéstelepítésének létrehozásával a következő lépésben.

1. Az Azure Automationben válassza **a Frissítéskezelés lehetőséget.** Hozzon létre egy új központi telepítést a [Frissítési központi telepítés létrehozása](automation-tutorial-update-management.md#schedule-an-update-deployment) című részben leírt lépések végrehajtásával, és válassza **az Importált csoportok** lehetőséget a **Típus** legördülő menüben a megfelelő Configuration Manager-gyűjtemény kiválasztásához. Ne feledje, a következő fontos pontokat: a. Ha a kijelölt Configuration Manager-eszközgyűjteményben karbantartási időszak van megadva, a gyűjtemény tagjai az ütemezett telepítésben megadott **Időtartam** beállítás helyett tisztelik azt.
    b. A célgyűjtemény tagjainak internetkapcsolattal kell rendelkezniük (közvetlenül, proxykiszolgálón vagy a Log Analytics-átjárón keresztül).

Miután befejezte a frissítés üzembe helyezését az Azure Automationen keresztül, a kijelölt számítógépcsoport hoz tartozó célszámítógépek a helyi ügyfélgyorsítótárból az ütemezett időpontban telepítik a frissítéseket. [A frissítéstelepítés állapotát bármikor megtekintheti](automation-tutorial-update-management.md#view-results-of-an-update-deployment) a telepítés eredményeinek monitorozásához.

### <a name="manage-software-updates-from-azure-automation"></a>Szoftverfrissítések kezelése az Azure Automationből

Azon Windows Server rendszerű virtuális gépek frissítéseinek kezeléséhez, amelyek egyben Configuration Manager-ügyfelek is, a megoldás által kezelt minden ügyfél esetében egy ügyfélszabályzatot kell beállítania a szoftverfrissítés-kezelési funkció letiltásához. Alapértelmezés szerint az ügyfélbeállítások minden eszközt megcéloznak a hierarchiában. A házirend-beállítással és konfigurálásával kapcsolatos további információkért tekintse át [az Ügyfélbeállítások konfigurálása a Configuration Manager alkalmazásban](https://docs.microsoft.com/configmgr/core/clients/deploy/configure-client-settings)című részt.

A konfigurációs módosítás végrehajtása után hozzon létre egy új központi telepítést a [Frissítés i központi telepítésének létrehozása](automation-tutorial-update-management.md#schedule-an-update-deployment) című részben leírt lépések végrehajtásával, és válassza az Importált **csoportok** lehetőséget a **Típus** legördülő menüben a megfelelő Configuration Manager-gyűjtemény kiválasztásához.

## <a name="next-steps"></a>További lépések

