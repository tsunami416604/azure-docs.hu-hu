---
title: Azure VMware-megoldás létrehozása a CloudSimple Private Cloud használatával
description: Ismerteti, hogyan hozhat létre CloudSimple-alapú privát felhőt a VMware számítási feladatok felhőbe való kiterjesztéséhez, működési rugalmassággal és folytonossággal
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 02a2bd311ea1e89a49eb12ef57a167a08eea5f98
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812250"
---
# <a name="create-a-cloudsimple-private-cloud"></a>CloudSimple saját felhő létrehozása

A privát felhő létrehozása segít a hálózati infrastruktúra számos gyakori igényének kielégítésében:

* **Növekedés**. Ha elérte a meglévő infrastruktúra hardveres frissítési pontját, a privát felhő lehetővé teszi, hogy az új hardveres befektetés nélkül bővítse a-t.

* **Gyors bővítés**. Ha bármilyen ideiglenes vagy nem tervezett kapacitás merül fel, a privát felhő lehetővé teszi a további kapacitás késleltetés nélküli létrehozását.

* **Fokozott védelem**. A három vagy több csomópontból álló privát felhővel automatikus redundanciát és magas rendelkezésre állású védelmet érhet el.

* **Hosszú távú infrastrukturális igények**. Ha az adatközpontok kapacitása vagy a költségek csökkentése érdekében szeretné átstrukturálni a költségeket, a privát felhő lehetővé teszi az adatközpontok kivonását és a felhőalapú megoldásba való átállást, miközben továbbra is kompatibilis a vállalati műveletekkel.

Privát felhő létrehozásakor egyetlen vSphere-fürtöt és az abban a fürtben létrehozott összes felügyeleti virtuális gépet kap.

## <a name="before-you-begin"></a>Előkészületek

A csomópontokat a privát felhő létrehozása előtt kell kiépíteni.  A csomópontok kiépítésével kapcsolatos további információkért lásd: [csomópontok kiépítése a VMware-megoldáshoz a CloudSimple – Azure-](create-nodes.md) cikk.

CIDR-tartomány kiosztása a vSphere/vSAN alhálózatokhoz a privát felhőben. A privát felhőt egy vCenter-kiszolgáló által kezelt elkülönített VMware stack (ESXi-gazdagépek, vCenter, vSAN és NSX) környezete hozza létre. A felügyeleti összetevők a vSphere/vSAN alhálózatok CIDR kiválasztott hálózatban vannak telepítve. A hálózati CIDR tartománya különböző alhálózatokra van osztva az üzembe helyezés során.  A vSphere/vSAN alhálózati címtartomány egyedinek kell lennie. Nem lehet átfedésben a CloudSimple-környezettel kommunikáló hálózattal.  A CloudSimple kommunikáló hálózatok a helyszíni hálózatokat és az Azure-beli virtuális hálózatokat is tartalmazzák.  A vSphere/vSAN alhálózatokkal kapcsolatos további információkért lásd: a [VLAN-ok és](cloudsimple-vlans-subnets.md)az alhálózatok áttekintése.

* Minimális vSphere/vSAN alhálózatok CIDR tartományának előtagja:/24 
* VSphere/vSAN alhálózatok maximális CIDR-tartományának előtagja:/21

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="access-the-cloudsimple-portal"></a>Hozzáférés a CloudSimple portáljához

Nyissa meg a [CloudSimple portált](access-cloudsimple-portal.md).

## <a name="create-a-new-private-cloud"></a>Új privát felhő létrehozása

1. Az **erőforrások** lapon kattintson az **új privát felhő**elemre.

    ![Hozzon létre egy privát felhőt – a kezdéshez](media/create-pc-button.png)

2. Válassza ki a saját felhő erőforrásainak tárolására szolgáló helyet.

3. Válassza ki a CS28 vagy a CS36 you'ev kiépített csomópont-típusát a privát felhő számára. Az utóbbi lehetőség a maximális számítási és memória-kapacitást is tartalmazza.

4. Válassza ki a privát felhő csomópontjainak számát. Kiválaszthatja a you'ev által kiépített csomópontok számát [](create-nodes.md).

    ![Egyéni felhő – alapszintű beállítások létrehozása](media/create-private-cloud-basic-info.png)

5. Kattintson **a Tovább gombra: Speciális beállítások**.

6. Adja meg a vSphere/vSAN alhálózatok CIDR tartományát. Győződjön meg arról, hogy a CIDR-tartomány nem fedi átfedésben a helyszíni vagy más Azure-alhálózatokkal (virtuális hálózatokkal) vagy az átjáró-alhálózattal.  Ne használjon az Azure-beli virtuális hálózatokon definiált CIDR-tartományt.
    
    **CIDR-tartomány beállításai:** /24,/23,/22, vagy/21. Az a/24 CIDR-tartomány legfeljebb kilenc csomópontot támogat, a/23 CIDR-tartomány akár 41 csomópontot is támogat, a/22 és/21 CIDR tartomány pedig legfeljebb 64 csomópontot támogat (a csomópontok maximális száma a privát felhőben).

    > [!CAUTION]
    > A vSphere/vSAN CIDR-tartomány IP-címei a saját felhőalapú infrastruktúra használatára vannak fenntartva.  Ne használja az IP-címet ezen a tartományon bármely virtuális gépen.

7. Kattintson **a Tovább gombra: Tekintse át**és hozza létre.

8. Tekintse át a beállításokat. Ha módosítania kell a beállításokat, kattintson az **előző**gombra.

9. Kattintson a **Create** (Létrehozás) gombra.

A saját felhőalapú kiépítés a Létrehozás gombra kattintva indul el.  A CloudSimple-portál [feladatok](https://docs.azure.cloudsimple.com/activity/#tasks) lapján figyelheti a folyamat előrehaladását.  A kiépítés akár 30 percet is igénybe vehet.  A kiépítés befejeződése után e-mailt fog kapni.

## <a name="next-steps"></a>További lépések

* [Privát felhő kibontása](expand-private-cloud.md)