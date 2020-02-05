---
title: Azure VMware-megoldások (AVS) – saját AVS-felhő létrehozása
description: Leírja, hogyan hozhat létre egy AVS Private Cloud-t a VMware-alapú számítási feladatok felhőbe való kiterjesztéséhez, működési rugalmasságot és folytonosságot biztosítva
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 128a0a1eec03878d0deba93048c54324aab7d888
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024789"
---
# <a name="create-an-avs-private-cloud"></a>AVS Private Cloud létrehozása

Az AVS Private Cloud egy elkülönített VMware-verem, amely támogatja az ESXi-gazdagépeket, a vCenter, a vSAN és a NSX. Az AVS privát felhők kezelése az AVS-portálon keresztül történik. Saját vCenter-kiszolgálóval rendelkeznek a saját felügyeleti tartományában. A verem dedikált csomópontokon és elszigetelt operációs rendszer nélküli hardveres csomópontokon fut.

Az AVS Private Cloud létrehozása segít a hálózati infrastruktúra számos gyakori igényének kielégítésében:

* **Növekedés**. Ha elérte a meglévő infrastruktúra hardveres frissítési pontját, az AVS Private Cloud lehetővé teszi, hogy az új hardveres befektetés nélkül bővítse az eszközt.

* **Gyors bővítés**. Ha bármilyen ideiglenes vagy nem tervezett kapacitás merül fel, az AVS Private Cloud lehetővé teszi a további kapacitás késleltetés nélküli létrehozását.

* **Fokozott védelem**. A három vagy több csomópontos AVS Private-felhővel automatikus redundanciát és magas rendelkezésre állású védelmet érhet el.

* **Hosszú távú infrastrukturális igények**. Ha az adatközpontok kapacitása vagy a költségek csökkentése érdekében szeretné átstrukturálni a költségeket, az AVS Private Cloud lehetővé teszi az adatközpontok kivonását és a felhőalapú megoldásba való átállást, miközben továbbra is kompatibilis a vállalati műveletekkel.

Az AVS Private Cloud létrehozásakor egyetlen vSphere-fürtöt és a fürtben létrehozott összes felügyeleti virtuális gépet kap.

## <a name="before-you-begin"></a>Előzetes teendők

A csomópontokat az AVS Private Cloud létrehozása előtt kell kiépíteni. A csomópontok kiépítésével kapcsolatos további információkért lásd: [csomópontok kiépítése az Azure VMware Solutions (AVS) szolgáltatáshoz](create-nodes.md).

CIDR-tartomány kiosztása a vSphere/vSAN alhálózatokhoz az AVS Private Cloud számára. Az AVS Private Cloud egy vCenter-kiszolgáló által kezelt elkülönített VMware stack-környezetként (ESXi-gazdagépekkel, vCenter, vSAN és NSX) jön létre. A felügyeleti összetevők a vSphere/vSAN alhálózatok CIDR kiválasztott hálózatban vannak telepítve. A hálózati CIDR tartománya különböző alhálózatokra van osztva az üzembe helyezés során. A vSphere/vSAN alhálózati címtartomány egyedinek kell lennie. Nem lehet átfedésben az AVS-környezettel kommunikáló hálózattal. Az AVS-vel kommunikáló hálózatok a helyszíni hálózatokat és az Azure-beli virtuális hálózatokat is tartalmazzák. A vSphere/vSAN alhálózatokkal kapcsolatos további információkért lásd: a VLAN-ok és az alhálózatok áttekintése.

* Minimális vSphere/vSAN alhálózatok CIDR tartományának előtagja:/24
* VSphere/vSAN alhálózatok maximális CIDR-tartományának előtagja:/21


## <a name="access-the-avs-portal"></a>Hozzáférés az AVS-portálhoz

Hozzáférés az [AVS-portálhoz](access-cloudsimple-portal.md).

## <a name="create-a-new-avs-private-cloud"></a>Új AVS Private Cloud létrehozása

1. Válassza az **Összes szolgáltatás** elemet.
2. Keressen rá az **AVS-szolgáltatások**kifejezésre.
3. Válassza ki azt az AVS-szolgáltatást, amelyen létre szeretné hozni a saját AVS-Felhőjét.
4. Az **Áttekintés**lapon kattintson az **AVS Private Cloud létrehozása** lehetőségre az AVS Portal új böngésző lapjának megnyitásához. Ha a rendszer kéri, jelentkezzen be az Azure bejelentkezési hitelesítő adataival.

    ![AVS Private Cloud létrehozása az Azure-ból](media/create-private-cloud-from-azure.png)

5. Az AVS-portálon adja meg az AVS Private-felhő nevét.
6. Válassza ki a **helyet** az AVS Private Cloud-hoz.
7. Válassza ki a **csomópont típusát**, amely megfelel az Azure-beli vásárlásoknak.
8. **Csomópontok számának**meghatározása Az AVS privát felhő létrehozásához legalább három csomópont szükséges.
5. A CloudSimple-portálon adja meg a saját felhő nevét.
6. Válassza ki a saját felhő **helyét** .
7. Válassza ki a **csomópont típusát**, amely megfelel az Azure-ban kiépített lehetőségeknek.
8. **Csomópontok számának**meghatározása  A privát felhő létrehozásához legalább három csomópont szükséges.

    ![Az AVS Private Cloud létrehozása – alapszintű információ](media/create-private-cloud-basic-info.png)

9. Kattintson a **Tovább: speciális beállítások**elemre.
10. Adja meg a vSphere/vSAN alhálózatok CIDR tartományát. Győződjön meg arról, hogy a CIDR-tartomány nem fedi átfedésben a helyszíni vagy más Azure-alhálózatokkal (virtuális hálózatokkal) vagy az átjáró-alhálózattal.

    **CIDR-tartomány beállításai:** /24,/23,/22, vagy/21. Az a/24 CIDR-tartomány legfeljebb kilenc csomópontot támogat, a/23 CIDR-tartomány akár 41 csomópontot is támogat, a/22 és/21 CIDR tartomány pedig legfeljebb 64 csomópontot támogat (a csomópontok maximális száma egy AVS Private Cloud-ban).

    > [!IMPORTANT]
    > Az vSphere/vSAN CIDR-tartomány IP-címei az AVS Private Cloud Infrastructure használatára vannak fenntartva. Ne használja az IP-címet ebben a tartományban bármely virtuális gépen.

11. Kattintson **a Tovább gombra: Áttekintés és létrehozás**.
12. Tekintse át a beállításokat. Ha módosítania kell a beállításokat, kattintson az **előző**gombra.
13. Kattintson a **Create** (Létrehozás) gombra.

Elindul az AVS Private Cloud kiépítési folyamata. Az AVS Private Cloud üzembe helyezése akár két óráig is eltarthat.

A meglévő AVS privát felhő kibővítésével kapcsolatos útmutatásért lásd: [az AVS Private Cloud kibontása](expand-private-cloud.md).
