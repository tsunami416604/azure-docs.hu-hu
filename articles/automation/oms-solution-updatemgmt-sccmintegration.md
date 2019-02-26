---
title: SCCM-gyűjtemények használatával az Azure Automation – Update Management frissítések
description: Ebből a cikkből megtudhatja, hogyan konfigurálhatja a System Center Configuration Managert ezzel a megoldással az SCCM által kezelt számítógépek frissítéseinek kezelésére.
services: automation
ms.service: automation
ms.subservice: update-management
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ddc055be45eae923be31d7d11621c9427660bf74
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56818306"
---
# <a name="integrate-system-center-configuration-manager-with-update-management"></a>A System Center Configuration Manager integrálása az Update Management

Azok a felhasználók, akik befektettek a System Center Configuration Managerbe a számítógépek, kiszolgálók és mobileszközök kezelése érdekében, a teljesítményét és fejlettségét a szoftverfrissítések kezelése során, a szoftverfrissítés-kezelési (SUM) ciklus részeként is kihasználják.

Jelentés és létrehozásával és előkészítésével szoftverfrissítés-telepítést a Configuration Manager által felügyelt Windows-kiszolgálók frissítése, és részletes állapotinformációkat kaphat a befejezett frissítések telepítéséről használatával a [frissítéskezelési megoldás](automation-update-management.md). Ha a frissítések megfelelőségének jelentésére, de nem a központi Windows-kiszolgálókkal való kezelése a Configuration Manager együttes használata, továbbra is jelenthet a Configuration Managernek, amíg a frissítéskezelési megoldással felügyelt biztonsági frissítések.

## <a name="prerequisites"></a>Előfeltételek

* Rendelkeznie kell a [Update Management megoldás](automation-update-management.md) az Automation-fiókhoz hozzáadott.
* A System Center Configuration Manager környezete által jelenleg kezelt Windows-kiszolgálóknak a Log Analytics-munkaterületnek kell jelenteniük, amelyhez szintén engedélyezve van a frissítéskezelési megoldás.
* Ez a funkció engedélyezve van a System Center Configuration Managerben aktuális ág 1606-os vagy újabb verziója. A Configuration Manager központi adminisztrációs hely vagy önálló elsődleges hely integrálása az Azure Monitor naplóira, és a gyűjtemények importálásához, tekintse át a [naplózza a Configuration Manager csatlakoztatása az Azure monitornak](../azure-monitor/platform/collect-sccm.md).  
* A Windows rendszerű ügynökszámítógépeket vagy a Windows Server Update Services (WSUS) szolgáltatással való kommunikációhoz kell konfigurálni, vagy a Microsoft Update szolgáltatáshoz kell hozzáféréssel rendelkezniük, ha nem kapnak biztonsági frissítéseket a Configuration Managertől.   

Az Azure IaaS-ben futtatott ügyfelek Configuration Manager-környezettel való kezelésének módja elsődlegesen az Azure-adatközpontok és az infrastruktúra közötti kapcsolattól függ. Ez a kapcsolat befolyásolja a kialakítás módosításait, amelyeket a Configuration Manager-infrastruktúrán kell elvégeznie, valamint az ezeket a szükséges módosításokat támogató járulékos költségeket. Annak megismeréséhez, hogy milyen tervezési szempontokat kell értékelnie a továbblépés előtt, tekintse meg az [Azure-beli Configuration Managerrel kapcsolatos gyakori kérdéseket](/sccm/core/understand/configuration-manager-on-azure#networking).

## <a name="configuration"></a>Konfiguráció

### <a name="manage-software-updates-from-configuration-manager"></a>Szoftverfrissítések kezelése a Configuration Managerből 

Hajtsa végre a következő lépéseket, ha a frissítéstelepítések kezelését továbbra is a Configuration Managerből szeretné elvégezni. Az Azure Automation csatlakozik a Configuration Manager frissítéseinek az a Log Analytics-munkaterülethez csatlakoztatott ügyfélszámítógépeken. A frissítés tartalma ugyanúgy elérhető az ügyfélszámítógép gyorsítótárából, mintha a telepítést a Configuration Manager kezelte volna.

1. Hozzon létre egy szoftverfrissítés-telepítést a legfelső szintű helyről a Configuration Manager-hierarchiában a [szoftverfrissítési eljárás üzembe helyezését](/sccm/sum/deploy-use/deploy-software-updates) ismertető cikkben leírt folyamatot követve. Az egyetlen beállítás, amelyet egy standard telepítéstől eltérően kell konfigurálnia, a **Ne telepítse a szoftverfrissítéseket** kiválasztása, amely a telepítőcsomag letöltésének viselkedését vezérli. Ez a viselkedés a frissítéskezelési megoldás kezeli egy ütemezett frissítési telepítés létrehozásával a következő lépésben.

1. Válassza ki az Azure Automation szolgáltatás **az Update Management**. Hozzon létre egy új telepítést a következő témakörben ismertetett [frissítéstelepítés létrehozását](automation-tutorial-update-management.md#schedule-an-update-deployment) válassza **importált csoportok** a a **típus** , válassza ki a megfelelő legördülő Configuration Manager-gyűjteményt. Az alábbi fontos szempontokat tartsa szem előtt: egy. Ha karbantartási időszak van adva a kiválasztott Configuration Manager-eszközök gyűjteményére, a gyűjtemény tagjai tartsa tiszteletben, hanem a **időtartama** az ütemezett telepítési művelet a megadott beállításokat.
    b. A célgyűjtemény tagjai az internethez (közvetlenül, proxykiszolgálón keresztül vagy a Log Analytics-átjárón) kapcsolattal kell rendelkeznie.

Miután befejezte az Azure Automation segítségével a frissítések telepítését, a célszámítógépek, amelyek a kiválasztott számítógépcsoport tagjai a helyi telepíti a frissítéseket az ütemezett időpontban. [A frissítéstelepítés állapotát bármikor megtekintheti](automation-tutorial-update-management.md#view-results-of-an-update-deployment) a telepítés eredményeinek monitorozásához.

### <a name="manage-software-updates-from-azure-automation"></a>Szoftverfrissítések kezelése az Azure Automationben

Azon Windows Server rendszerű virtuális gépek frissítéseinek kezeléséhez, amelyek egyben Configuration Manager-ügyfelek is, a megoldás által kezelt minden ügyfél esetében egy ügyfélszabályzatot kell beállítania a szoftverfrissítés-kezelési funkció letiltásához. Alapértelmezés szerint az ügyfélbeállítások minden eszközt megcéloznak a hierarchiában. A szabályzattal és annak beállításával kapcsolatos információkért lásd [az ügyfélbeállítások a System Center Configuration Managerben történő konfigurálását](/sccm/core/clients/deploy/configure-client-settings) ismertető részt.

A konfigurációs módosítás elvégzése után létrehozhat egy új központi telepítést a következő témakörben ismertetett [frissítéstelepítés létrehozását](automation-tutorial-update-management.md#schedule-an-update-deployment) válassza **importált csoportok** a a **típusa** listából válassza ki a megfelelő Configuration Manager-gyűjteményt.

## <a name="next-steps"></a>További lépések

