---
title: Azure Automation Update Management integrálása Windows-végponttal Configuration Manager
description: Ez a cikk azt ismerteti, hogyan konfigurálható a Microsoft Endpoint Configuration Manager és a Update Management a szoftverfrissítések központi telepítése a Manager-ügyfelekre.
services: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 06/16/2020
ms.topic: conceptual
ms.openlocfilehash: 2bcf2518ab7f4e5a3648b508e42868fd5bb1a863
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84817210"
---
# <a name="integrate-update-management-with-windows-endpoint-configuration-manager"></a>Update Management integrálása Windows-végponttal Configuration Manager

A Microsoft Endpoint Configuration Managerban a számítógépek, kiszolgálók és mobileszközök felügyeletére használt ügyfelek a szoftverfrissítések felügyeleti (SUM) ciklusának részeként a szoftverfrissítések felügyeletének erősségét és érettségét is kihasználják.

A felügyelt Windows-kiszolgálókat jelentéssel és frissítéssel állíthatja be és frissítheti a Windows Endpoint Configuration Managerban, valamint a befejezett frissítések telepítésének részletes állapotát [Update Management](automation-update-management.md)használatával. Ha a Windows-végpontot Configuration Manager a frissítési megfelelőségi jelentéskészítéshez, de nem a frissítési központi telepítések Windows-kiszolgálókkal való kezeléséhez, akkor folytathatja a jelentéskészítést a Configuration managernek, miközben a biztonsági frissítéseket Azure Automation Update Management kezeli.

>[!NOTE]
>Míg Update Management támogatja a Windows Server 2008 R2 frissítési értékelését és javítását, a nem támogatja az ezen operációs rendszert futtató Configuration Manager Endpoint által felügyelt ügyfeleket.

## <a name="prerequisites"></a>Előfeltételek

* Az Automation-fiókhoz [Azure Automation Update Management](automation-update-management.md) kell hozzáadnia.
* A Windows-végpont által felügyelt Windows-kiszolgálókon Configuration Manager környezetnek is jelentést kell készítenie a Log Analytics munkaterületre, amely szintén engedélyezve van Update Management.
* Ez a funkció engedélyezve van a Windows-végponton Configuration Manager az aktuális ág 1606-es és újabb verziója. A Windows-végpont Configuration Manager központi adminisztrációs hely vagy egy önálló elsődleges hely integrálásához Azure Monitor naplókkal és gyűjtemények importálásával tekintse át a [kapcsolódási Configuration Manager a Azure monitor naplókat](../azure-monitor/platform/collect-sccm.md).  
* A Windows-ügynököket úgy kell konfigurálni, hogy Windows Server Update Services-(WSUS-) kiszolgálóval kommunikáljanak, Microsoft Update vagy ha nem kapnak biztonsági frissítéseket a Windows-végpontról Configuration Manager.

Az Azure IaaS-ben üzemeltetett ügyfelek kezelése meglévő Windows-végponti Configuration Manager környezettel elsődlegesen az Azure-adatközpontok és az infrastruktúra közötti kapcsolattól függ. Ez a kapcsolat hatással van minden olyan tervezési módosításra, amelyet a szükséges módosítások támogatásához a Windows-végponton Configuration Manager infrastruktúrán és a kapcsolódó díjakon kell végrehajtania. Annak megismeréséhez, hogy milyen tervezési szempontokat kell értékelnie a továbblépés előtt, tekintse meg az [Azure-beli Configuration Managerrel kapcsolatos gyakori kérdéseket](https://docs.microsoft.com/configmgr/core/understand/configuration-manager-on-azure#networking).

## <a name="manage-software-updates-from-windows-endpoint-configuration-manager"></a>Szoftverfrissítések kezelése Windows-végpontról Configuration Manager

Hajtsa végre a következő lépéseket, ha továbbra is szeretné kezelni a frissítések központi telepítését a Windows Endpoint Configuration Managerról. Azure Automation csatlakozik a Windows-végponthoz Configuration Manager a Log Analytics munkaterülethez csatlakoztatott ügyfélszámítógépek frissítéseinek alkalmazásához. A tartalom frissítése az ügyfélszámítógép gyorsítótárából úgy érhető el, mintha a központi telepítést a Windows Endpoint Configuration Manager felügyelte.

1. Hozzon létre egy szoftverfrissítés központi telepítését a Windows-végpont Configuration Manager hierarchiájának legfelső szintű helyéről a [szoftverfrissítések központi telepítése](https://docs.microsoft.com/configmgr/sum/deploy-use/deploy-software-updates)című témakörben ismertetett eljárással. Az egyetlen beállítás, amelyet egy standard telepítéstől eltérően kell konfigurálnia, a **Ne telepítse a szoftverfrissítéseket** kiválasztása, amely a telepítőcsomag letöltésének viselkedését vezérli. Ezt a viselkedést Update Management felügyelheti egy ütemezett frissítési telepítés létrehozásával a következő lépésben.

1. A Azure Automation területen válassza a **Update Management**lehetőséget. Hozzon létre egy új központi telepítést a [frissítés központi telepítésének létrehozása](automation-tutorial-update-management.md#schedule-an-update-deployment) című témakörben ismertetett lépéseket követve, és válassza a **típus** legördülő menüben az **importált csoportok** lehetőséget a megfelelő Windows-végpont Configuration Manager gyűjtemény kiválasztásához Tartsa szem előtt a következő fontos szempontokat: a. Ha egy karbantartási időszak van meghatározva a kiválasztott Windows-végponton Configuration Manager-eszköz gyűjteményben, a gyűjtemény tagjai megtisztelik az ütemezett telepítésben definiált **időtartam** beállítás helyett.
    b. A cél gyűjtemény tagjainak csatlakozniuk kell az internethez (vagy közvetlenül, egy proxykiszolgálón keresztül vagy az Log Analytics átjárón keresztül).

A frissítés telepítésének a Azure Automationon keresztül történő befejezése után a kiválasztott számítógépcsoport tagjai lesznek a helyi ügyfél-gyorsítótárból beütemezett időpontban települő frissítések. [A frissítéstelepítés állapotát bármikor megtekintheti](automation-tutorial-update-management.md#check-deployment-status) a telepítés eredményeinek monitorozásához.

## <a name="manage-software-updates-from-azure-automation"></a>Szoftverfrissítések kezelése Azure Automationról

Ha Windows Server rendszerű virtuális gépek frissítéseit szeretné kezelni, amelyek Windows Endpoint Configuration Manager-ügyfelek, konfigurálnia kell az ügyfél-házirendet, hogy letiltsa a Update Management által felügyelt összes ügyfél Szoftverfrissítések felügyelete szolgáltatását. Alapértelmezés szerint az ügyfélbeállítások minden eszközt megcéloznak a hierarchiában. A házirend-beállítással és annak konfigurálásával kapcsolatos további információkért tekintse át az [ügyfélbeállítások konfigurálása a Configuration Manager-ben](https://docs.microsoft.com/configmgr/core/clients/deploy/configure-client-settings)című témakört.

A konfigurációs módosítás elvégzése után hozzon létre egy új központi telepítést a [frissítés központi telepítésének létrehozása](automation-tutorial-update-management.md#schedule-an-update-deployment) című témakörben ismertetett lépéseket követve, és válassza a **típus** legördülő menüből az **importált csoportok** lehetőséget, hogy kiválassza a megfelelő Windows-végpontot Configuration Manager gyűjteményt.

## <a name="next-steps"></a>További lépések

Az integrációs ütemterv beállításával kapcsolatban lásd: [frissítések központi telepítésének ütemezett](automation-tutorial-update-management.md#schedule-an-update-deployment)beállítása.