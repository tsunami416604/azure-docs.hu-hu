---
title: "Frissítések céljainak beállítása az SCCM-gyűjtemények használatával az OMS-frissítéskezelésben | Microsoft Docs"
description: "Ebből a cikkből megtudhatja, hogyan konfigurálhatja a System Center Configuration Managert ezzel a megoldással az SCCM által kezelt számítógépek frissítéseinek kezelésére."
services: operations-management-suite
documentationcenter: 
author: georgewallace
manager: carmonm
editor: 
ms.assetid: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: gwallace
ms.openlocfilehash: 40e343ab75a2c2508d64ec0aeb293f5154813135
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="integrate-system-center-configuration-manager-with-oms-update-management"></a>System Center Configuration Manager integrálása az OMS-frissítéskezeléssel

Azok a felhasználók, akik befektettek a System Center Configuration Managerbe a számítógépek, kiszolgálók és mobileszközök kezelése érdekében, a teljesítményét és fejlettségét a szoftverfrissítések kezelése során, a szoftverfrissítés-kezelési (SUM) ciklus részeként is kihasználják.  

A ma már meglévő, az OMS és a Configuration Manager közötti integrációra építve jelentheti és frissítheti a felügyelt Windows-kiszolgálókat a szoftverfrissítés-telepítések létrehozásával és előkészítésével, illetve részletes állapotinformációkat kaphat a befejezett frissítések telepítéséről a [frissítéskezelési megoldás](../operations-management-suite/oms-solution-update-management.md) használatával. Ha a Configuration Managert a frissítések megfelelőségének jelentésére és nem a frissítések telepítésének Windows-kiszolgálókkal való kezelésére használja, továbbra is jelenthet a Configuration Managernek, miközben a biztonsági frissítéseket az OMS frissítéskezelési megoldással kezeli.

## <a name="prerequisites"></a>Előfeltételek

* A [frissítéskezelési megoldást](../operations-management-suite/oms-solution-update-management.md) hozzá kell adni a Log Analytics-munkaterülethez, és össze kell kapcsolni az Automation-fiókkal ugyanabban az erőforráscsoportban és régióban.   
* A System Center Configuration Manager környezete által jelenleg kezelt Windows-kiszolgálóknak a Log Analytics-munkaterületnek kell jelenteniük, amelyhez szintén engedélyezve van a frissítéskezelési megoldás.  
* Ez a funkció a System Center Configuration Manager aktuális ágának 1606-os vagy újabb verzióját támogatja.  A Configuration Manager központi adminisztrációs helyének vagy egy különálló elsődleges hely Log Analyticsszel és importált gyűjteményekkel való integrálásához tekintse meg a [Configuration Manager a Log Analyticshez történő csatlakoztatását](../log-analytics/log-analytics-sccm.md) ismertető részt.  
* A Windows rendszerű ügynökszámítógépeket vagy a Windows Server Update Services (WSUS) szolgáltatással való kommunikációhoz kell konfigurálni, vagy a Microsoft Update szolgáltatáshoz kell hozzáféréssel rendelkezniük, ha nem kapnak biztonsági frissítéseket a Configuration Managertől.   

Az Azure IaaS-ben futtatott ügyfelek Configuration Manager-környezettel való kezelésének módja elsődlegesen az Azure-adatközpontok és az infrastruktúra közötti kapcsolattól függ. Ez a kapcsolat befolyásolja a kialakítás módosításait, amelyeket a Configuration Manager-infrastruktúrán kell elvégeznie, valamint az ezeket a szükséges módosításokat támogató járulékos költségeket.  Annak megismeréséhez, hogy milyen tervezési szempontokat kell értékelnie a továbblépés előtt, tekintse meg az [Azure-beli Configuration Managerrel kapcsolatos gyakori kérdéseket](https://docs.microsoft.com/sccm/core/understand/configuration-manager-on-azure#networking).    

## <a name="configuration"></a>Konfiguráció

### <a name="manage-software-updates-from-configuration-manager"></a>Szoftverfrissítések kezelése a Configuration Managerből 

Hajtsa végre a következő lépéseket, ha a frissítéstelepítések kezelését továbbra is a Configuration Managerből szeretné elvégezni.  Az OMS a Configuration Managerhez csatlakozik, hogy alkalmazza a frissítéseket a Log Analytics-munkaterülethez csatlakoztatott ügyfélszámítógépeken. A frissítés tartalma ugyanúgy elérhető az ügyfélszámítógép gyorsítótárából, mintha a telepítést a Configuration Manager kezelte volna.  

1. Hozzon létre egy szoftverfrissítés-telepítést a legfelső szintű helyről a Configuration Manager-hierarchiában a [szoftverfrissítési eljárás üzembe helyezését](https://docs.microsoft.com/sccm/sum/deploy-use/deploy-software-updates) ismertető cikkben leírt folyamatot követve.  Az egyetlen beállítás, amelyet egy standard telepítéstől eltérően kell konfigurálnia, a **Ne telepítse a szoftverfrissítéseket** kiválasztása, amely a telepítőcsomag letöltésének viselkedését vezérli. Ezt a viselkedést az OMS frissítéskezelési megoldása kezeli egy ütemezett frissítési telepítés létrehozásával a következő lépésben.  

1. Az OMS-portálon nyissa meg a Frissítéskezelés irányítópultot.  Hozzon létre egy új telepítést a [frissítéstelepítés létrehozását](../operations-management-suite/oms-solution-update-management.md#creating-an-update-deployment) ismertető részben leírtakat követve, majd a legördülő listából válassza ki a megfelelő, OMS-számítógépcsoportként megjelenő Configuration Manager-gyűjteményt.  Tartsa szem előtt az alábbi fontos szempontokat:
    1. Ha a karbantartási időszak a kiválasztott Configuration Manager-eszközgyűjteményben van megadva, a gyűjtemény tagjai ezt veszik figyelembe az OMS-ben beállított ütemezett telepítés **Időtartam** beállítása helyett.
    1. A célgyűjtemény tagjainak csatlakozniuk kell az internethez (közvetlenül, proxykiszolgálón keresztül vagy az OMS Gatewayen keresztül).  

Az OMS-megoldással végzett frissítéstelepítés után azon célszámítógépek, amelyek a kiválasztott számítógépcsoport tagjai, a helyi ügyfélgyorsítótárukból telepítik a frissítéseket az ütemezett időpontban.  [A frissítéstelepítés állapotát bármikor megtekintheti](../operations-management-suite/oms-solution-update-management.md#viewing-update-deployments) a telepítés eredményeinek monitorozásához.  


### <a name="manage-software-updates-from-oms"></a>Szoftverfrissítések kezelése OMS-ből

Azon Windows Server rendszerű virtuális gépek frissítéseinek kezeléséhez, amelyek egyben Configuration Manager-ügyfelek is, a megoldás által kezelt minden ügyfél esetében egy ügyfélszabályzatot kell beállítania a szoftverfrissítés-kezelési funkció letiltásához.  Alapértelmezés szerint az ügyfélbeállítások minden eszközt megcéloznak a hierarchiában.  A szabályzattal és annak beállításával kapcsolatos információkért lásd [az ügyfélbeállítások a System Center Configuration Managerben történő konfigurálását](https://docs.microsoft.com/sccm/core/clients/deploy/configure-client-settings) ismertető részt.  

A konfigurációs módosítás elvégzése után hozzon létre egy új telepítést a [frissítéstelepítés létrehozását](../operations-management-suite/oms-solution-update-management.md#creating-an-update-deployment) ismertető részben leírtakat követve, majd a legördülő listából válassza a megfelelő, OMS-számítógépcsoportként megjelenő Configuration Manager-gyűjteményt. 

