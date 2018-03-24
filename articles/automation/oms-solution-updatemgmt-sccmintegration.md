---
title: Frissítések SCCM gyűjtemények használatával az Azure Automation - frissítéskezelés
description: Ebből a cikkből megtudhatja, hogyan konfigurálhatja a System Center Configuration Managert ezzel a megoldással az SCCM által kezelt számítógépek frissítéseinek kezelésére.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 481195538a8c0ece572b4589ea2c2303e559fc44
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="integrate-system-center-configuration-manager-with-update-management"></a>Frissítéskezelés a System Center Configuration Manager integrálása

Azok a felhasználók, akik befektettek a System Center Configuration Managerbe a számítógépek, kiszolgálók és mobileszközök kezelése érdekében, a teljesítményét és fejlettségét a szoftverfrissítések kezelése során, a szoftverfrissítés-kezelési (SUM) ciklus részeként is kihasználják.

Jelentést és felügyelt Windows Server update hoz létre, és előre átmeneti szoftverfrissítési központi telepítést a Configuration Manager, és részletes állapota a befejezett frissítés üzembe helyezése a [frissítése felügyeleti megoldás](automation-update-management.md). A frissítés megfelelőségi jelentéseket, de nem a Windows-kiszolgálók központi kezelésére szolgáló Configuration Manager használatakor, folytathatja a Configuration Manager jelentéskészítési, amíg a frissítés felügyeleti megoldással kezelt biztonsági frissítések.

## <a name="prerequisites"></a>Előfeltételek

* Rendelkeznie kell a [frissítés felügyeleti megoldás](automation-update-management.md) hozzáadni az Automation-fiók.
* A System Center Configuration Manager környezete által jelenleg kezelt Windows-kiszolgálóknak a Log Analytics-munkaterületnek kell jelenteniük, amelyhez szintén engedélyezve van a frissítéskezelési megoldás.
* Ez a funkció engedélyezve van a System Center Configuration Manager aktuális ág verzió 1606 és magasabb. A Configuration Manager központi adminisztrációs helyének vagy egy különálló elsődleges hely Log Analyticsszel és importált gyűjteményekkel való integrálásához tekintse meg a [Configuration Manager a Log Analyticshez történő csatlakoztatását](../log-analytics/log-analytics-sccm.md) ismertető részt.  
* A Windows rendszerű ügynökszámítógépeket vagy a Windows Server Update Services (WSUS) szolgáltatással való kommunikációhoz kell konfigurálni, vagy a Microsoft Update szolgáltatáshoz kell hozzáféréssel rendelkezniük, ha nem kapnak biztonsági frissítéseket a Configuration Managertől.   

Az Azure IaaS-ben futtatott ügyfelek Configuration Manager-környezettel való kezelésének módja elsődlegesen az Azure-adatközpontok és az infrastruktúra közötti kapcsolattól függ. Ez a kapcsolat befolyásolja a kialakítás módosításait, amelyeket a Configuration Manager-infrastruktúrán kell elvégeznie, valamint az ezeket a szükséges módosításokat támogató járulékos költségeket. Annak megismeréséhez, hogy milyen tervezési szempontokat kell értékelnie a továbblépés előtt, tekintse meg az [Azure-beli Configuration Managerrel kapcsolatos gyakori kérdéseket](/sccm/core/understand/configuration-manager-on-azure#networking).

## <a name="configuration"></a>Konfiguráció

### <a name="manage-software-updates-from-configuration-manager"></a>Szoftverfrissítések kezelése a Configuration Managerből 

Hajtsa végre a következő lépéseket, ha a frissítéstelepítések kezelését továbbra is a Configuration Managerből szeretné elvégezni. Azure Automation szolgáltatásbeli csatlakozik a Configuration Manager frissítéseinek az a Naplóelemzési munkaterület csatlakozó ügyfélszámítógépek számára. A frissítés tartalma ugyanúgy elérhető az ügyfélszámítógép gyorsítótárából, mintha a telepítést a Configuration Manager kezelte volna.

1. Hozzon létre egy szoftverfrissítés-telepítést a legfelső szintű helyről a Configuration Manager-hierarchiában a [szoftverfrissítési eljárás üzembe helyezését](/sccm/sum/deploy-use/deploy-software-updates) ismertető cikkben leírt folyamatot követve. Az egyetlen beállítás, amelyet egy standard telepítéstől eltérően kell konfigurálnia, a **Ne telepítse a szoftverfrissítéseket** kiválasztása, amely a telepítőcsomag letöltésének viselkedését vezérli. Ez a viselkedés frissítése MDM-megoldás által kezeli a következő lépésben egy ütemezett frissítés központi telepítés létrehozása.

1. Válassza ki az Azure Automationben **frissítéskezelés**. Hozzon létre egy új központi telepítést a következő helyen ismertetett lépések [egy központi telepítés létrehozása](automation-tutorial-update-management.md#schedule-an-update-deployment) válassza **csoportok importálása** a a **típus** válassza ki a megfelelő legördülő lista Configuration Manager-gyűjteményhez. Vegye figyelembe a következő fontos szempontokat: egy. Ha karbantartási időszak van definiálva a kijelölt Configuration Manager-eszközök gyűjteményére, a gyűjtemény tagjai tiszteletben ahelyett, hogy azt a **időtartam** az ütemezett telepítési mappájában megadott beállításokat.
    b. A célgyűjtemény tagjainak csatlakozniuk kell az internethez (közvetlenül, proxykiszolgálón keresztül vagy az OMS Gatewayen keresztül).

Az Azure Automation keresztül központi telepítésének befejezése után a célszámítógépeken, amelyek tagjai a kiválasztott számítógép (csoport) telepíti a frissítéseket a megadott időpontban az ügyfél helyi gyorsítótárából. [A frissítéstelepítés állapotát bármikor megtekintheti](automation-tutorial-update-management.md#view-results-of-an-update-deployment) a telepítés eredményeinek monitorozásához.

### <a name="manage-software-updates-from-azure-automation"></a>Azure Automation-szoftverfrissítések kezelése

Azon Windows Server rendszerű virtuális gépek frissítéseinek kezeléséhez, amelyek egyben Configuration Manager-ügyfelek is, a megoldás által kezelt minden ügyfél esetében egy ügyfélszabályzatot kell beállítania a szoftverfrissítés-kezelési funkció letiltásához. Alapértelmezés szerint az ügyfélbeállítások minden eszközt megcéloznak a hierarchiában. A szabályzattal és annak beállításával kapcsolatos információkért lásd [az ügyfélbeállítások a System Center Configuration Managerben történő konfigurálását](/sccm/core/clients/deploy/configure-client-settings) ismertető részt.

A konfigurációs módosítás elvégzése után egy új központi telepítés létrehozásához engedélyezi a következő témakörben ismertetett [egy központi telepítés létrehozása](automation-tutorial-update-management.md#schedule-an-update-deployment) válassza **csoportok importálása** a a **típusa** legördülő listán válassza ki a megfelelő Configuration Manager-gyűjteményhez.

## <a name="next-steps"></a>További lépések
