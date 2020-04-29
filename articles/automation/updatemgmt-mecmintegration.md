---
title: Azure Automation-Update Management használata Configuration Manager-ügyfelekkel
description: Ebből a cikkből megtudhatja, hogyan konfigurálhatja a Microsoft Endpoint Configuration Managert ezzel a megoldással, hogy szoftverfrissítéseket telepítsen a ConfigMgr-ügyfelekre.
services: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 32a077c476d9669c3f32bd4040fdc8ff90156c19
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678734"
---
# <a name="deploy-updates-to-microsoft-endpoint-configuration-manager-clients-with-update-management"></a>A Microsoft Endpoint Configuration Manager-ügyfelek frissítéseinek központi telepítése Update Management

A Microsoft Endpoint Configuration Managerban a számítógépek, kiszolgálók és mobileszközök felügyeletére használt ügyfelek a szoftverfrissítések felügyeleti (SUM) ciklusának részeként a szoftverfrissítések felügyeletének erősségét és érettségét is kihasználják.

A felügyelt Windows-kiszolgálókat bejelentheti és frissítheti a szoftverfrissítések központi telepítésének létrehozásával és előzetes előkészítésével Configuration Managerban, és részletes állapotba állíthatja a befejezett frissítési központi telepítéseket a [Update Management](automation-update-management.md)használatával. Ha Configuration Managert használ a megfelelőségi jelentés frissítéséhez, de nem a frissítési központi telepítések Windows-kiszolgálókkal való kezeléséhez, akkor folytathatja a jelentéskészítést Configuration Manager, amíg a biztonsági frissítések kezelése a Update Management megoldással történik.

## <a name="prerequisites"></a>Előfeltételek

* Az Automation-fiókhoz hozzá kell adni az [Update Management-megoldást](automation-update-management.md) .
* A Configuration Manager-környezet által felügyelt Windows Server-kiszolgálóknak is jelentést kell készíteniük a Log Analytics munkaterületre, amelyhez a Update Management megoldás is engedélyezve van.
* Ez a funkció a Configuration Manager aktuális ág 1606-es és újabb verziójában engedélyezett. A Configuration Manager központi adminisztrációs hely vagy önálló elsődleges hely integrálásához Azure Monitor-naplók és-gyűjtemények importálásával tekintse át a [kapcsolódási Configuration Manager a Azure monitor naplókat](../azure-monitor/platform/collect-sccm.md).  
* A Windows rendszerű ügynökszámítógépeket vagy a Windows Server Update Services (WSUS) szolgáltatással való kommunikációhoz kell konfigurálni, vagy a Microsoft Update szolgáltatáshoz kell hozzáféréssel rendelkezniük, ha nem kapnak biztonsági frissítéseket a Configuration Managertől.   

Az Azure IaaS-ben futtatott ügyfelek Configuration Manager-környezettel való kezelésének módja elsődlegesen az Azure-adatközpontok és az infrastruktúra közötti kapcsolattól függ. Ez a kapcsolat befolyásolja a kialakítás módosításait, amelyeket a Configuration Manager-infrastruktúrán kell elvégeznie, valamint az ezeket a szükséges módosításokat támogató járulékos költségeket. Annak megismeréséhez, hogy milyen tervezési szempontokat kell értékelnie a továbblépés előtt, tekintse meg az [Azure-beli Configuration Managerrel kapcsolatos gyakori kérdéseket](https://docs.microsoft.com/configmgr/core/understand/configuration-manager-on-azure#networking).

## <a name="configuration"></a>Configuration

### <a name="manage-software-updates-from-configuration-manager"></a>Szoftverfrissítések kezelése a Configuration Managerből 

Hajtsa végre a következő lépéseket, ha a frissítéstelepítések kezelését továbbra is a Configuration Managerből szeretné elvégezni. Azure Automation csatlakozik a Configuration Managerhoz a Log Analytics munkaterülethez csatlakoztatott ügyfélszámítógépek frissítéseinek alkalmazásához. A frissítés tartalma ugyanúgy elérhető az ügyfélszámítógép gyorsítótárából, mintha a telepítést a Configuration Manager kezelte volna.

1. Hozzon létre egy szoftverfrissítés-telepítést a Configuration Manager-hierarchia legfelső szintű helyéről a [szoftverfrissítések központi telepítése](https://docs.microsoft.com/configmgr/sum/deploy-use/deploy-software-updates)című témakörben ismertetett eljárással. Az egyetlen beállítás, amelyet egy standard telepítéstől eltérően kell konfigurálnia, a **Ne telepítse a szoftverfrissítéseket** kiválasztása, amely a telepítőcsomag letöltésének viselkedését vezérli. Ezt a viselkedést a Update Management megoldás felügyeli, ha a következő lépésben létrehoz egy ütemezett frissítési telepítést.

1. A Azure Automation területen válassza a **Update Management**lehetőséget. Hozzon létre egy új központi telepítést a [frissítés központi telepítésének létrehozása](automation-tutorial-update-management.md#schedule-an-update-deployment) című témakörben ismertetett lépéseket követve, és válassza a **típus** legördülő menüben az **importált csoportok** lehetőséget a megfelelő Configuration Manager gyűjtemény kiválasztásához. Tartsa szem előtt a következő fontos szempontokat: a. Ha egy karbantartási időszak van meghatározva a kiválasztott Configuration Manager-eszköz gyűjteményén, a gyűjtemény tagjai az ütemezett telepítésben megadott **időtartam** beállítás helyett tiszteletben tartsák.
    b. A cél gyűjtemény tagjainak csatlakozniuk kell az internethez (vagy közvetlenül, egy proxykiszolgálón keresztül vagy az Log Analytics átjárón keresztül).

A frissítés telepítésének a Azure Automationon keresztül történő befejezése után a kiválasztott számítógépcsoport tagjai lesznek a helyi ügyfél-gyorsítótárból beütemezett időpontban települő frissítések. [A frissítéstelepítés állapotát bármikor megtekintheti](automation-tutorial-update-management.md#view-results-of-an-update-deployment) a telepítés eredményeinek monitorozásához.

### <a name="manage-software-updates-from-azure-automation"></a>Szoftverfrissítések kezelése Azure Automationról

Azon Windows Server rendszerű virtuális gépek frissítéseinek kezeléséhez, amelyek egyben Configuration Manager-ügyfelek is, a megoldás által kezelt minden ügyfél esetében egy ügyfélszabályzatot kell beállítania a szoftverfrissítés-kezelési funkció letiltásához. Alapértelmezés szerint az ügyfélbeállítások minden eszközt megcéloznak a hierarchiában. A házirend-beállítással és annak konfigurálásával kapcsolatos további információkért tekintse át az [ügyfélbeállítások konfigurálása a Configuration Manager-ben](https://docs.microsoft.com/configmgr/core/clients/deploy/configure-client-settings)című témakört.

A konfigurációs módosítás elvégzése után hozzon létre egy új központi telepítést a [frissítés központi telepítésének létrehozása](automation-tutorial-update-management.md#schedule-an-update-deployment) című témakörben ismertetett lépéseket követve, **és válassza ki** a megfelelő Configuration Manager gyűjteményt a **típus** legördülő listából.

## <a name="next-steps"></a>További lépések

