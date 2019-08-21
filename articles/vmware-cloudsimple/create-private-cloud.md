---
title: Azure VMware-megoldás a CloudSimple által – CloudSimple saját felhő létrehozása
description: Ismerteti, hogyan hozhat létre CloudSimple-alapú privát felhőt a VMware számítási feladatok felhőbe való kiterjesztéséhez, működési rugalmassággal és folytonossággal
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: aacdb57c312946a9ec2b17a8d41aa9150efc277d
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640974"
---
# <a name="create-a-cloudsimple-private-cloud"></a>CloudSimple saját felhő létrehozása

A privát felhő egy elkülönített VMware-verem, amely támogatja az ESXi-gazdagépeket, a vCenter, a vSAN és a NSX. A privát felhők kezelése a CloudSimple-portálon keresztül történik. Saját vCenter-kiszolgálóval rendelkeznek a saját felügyeleti tartományában. A verem dedikált csomópontokon és elszigetelt operációs rendszer nélküli hardveres csomópontokon fut.

A privát felhő létrehozása segít a hálózati infrastruktúra számos gyakori igényének kielégítésében:

* **Növekedés**. Ha elérte a meglévő infrastruktúra hardveres frissítési pontját, a privát felhő lehetővé teszi, hogy az új hardveres befektetés nélkül bővítse a-t.

* **Gyors bővítés**. Ha bármilyen ideiglenes vagy nem tervezett kapacitás merül fel, a privát felhő lehetővé teszi a további kapacitás késleltetés nélküli létrehozását.

* **Fokozott védelem**. A három vagy több csomópontból álló privát felhővel automatikus redundanciát és magas rendelkezésre állású védelmet érhet el.

* **Hosszú távú infrastrukturális igények**. Ha az adatközpontok kapacitása vagy a költségek csökkentése érdekében szeretné átstrukturálni a költségeket, a privát felhő lehetővé teszi az adatközpontok kivonását és a felhőalapú megoldásba való átállást, miközben továbbra is kompatibilis a vállalati műveletekkel.

Privát felhő létrehozásakor egyetlen vSphere-fürtöt és az abban a fürtben létrehozott összes felügyeleti virtuális gépet kap.

## <a name="access-the-cloudsimple-portal"></a>Hozzáférés a CloudSimple portáljához

Nyissa meg a [CloudSimple portált](access-cloudsimple-portal.md).

## <a name="create-a-new-private-cloud"></a>Új privát felhő létrehozása

1. Válassza az **Összes szolgáltatás** elemet.
2. Keressen rá a **CloudSimple Services**kifejezésre.
3. Válassza ki azt a CloudSimple-szolgáltatást, amelyen létre szeretné hozni a saját Felhőjét.
4. Az **Áttekintés**lapon kattintson a **privát felhő létrehozása** elemre a CloudSimple-portál új böngésző lapjának megnyitásához. Ha a rendszer kéri, jelentkezzen be az Azure bejelentkezési hitelesítő adataival.

    ![Privát felhő létrehozása az Azure-ból](media/create-private-cloud-from-azure.png)

5. A CloudSimple-portálon adja meg a saját felhő nevét.
6. Válassza ki a saját felhő **helyét** .
7. Válassza ki a **csomópont típusát**, amely megfelel az Azure-beli vásárlásoknak.  Kiválaszthatja a [CS28 vagy a CS36 lehetőséget](cloudsimple-node.md#vmware-solution-by-cloudsimple-nodes-sku). Az utóbbi lehetőség a maximális számítási és memória-kapacitást is tartalmazza.
8. **Csomópontok számának**meghatározása  A privát felhő létrehozásához legalább három csomópont szükséges.

    ![Privát felhő létrehozása – alapszintű információ](media/create-private-cloud-basic-info.png)

9. Kattintson **a Tovább gombra: Speciális beállítások**.
10. Adja meg a vSphere/vSAN alhálózatok CIDR tartományát. Győződjön meg arról, hogy a CIDR-tartomány nem fedi átfedésben a helyszíni vagy más Azure-alhálózatokkal (virtuális hálózatokkal) vagy az átjáró-alhálózattal.

    **CIDR-tartomány beállításai:** /24,/23,/22, vagy/21. Az a/24 CIDR-tartomány legfeljebb kilenc csomópontot támogat, a/23 CIDR-tartomány akár 41 csomópontot is támogat, a/22 és/21 CIDR tartomány pedig legfeljebb 64 csomópontot támogat (a csomópontok maximális száma a privát felhőben).

    > [!IMPORTANT]
    > A vSphere/vSAN CIDR-tartomány IP-címei a saját felhőalapú infrastruktúra számára vannak fenntartva.  Ne használja az IP-címet ebben a tartományban bármely virtuális gépen.

11. Kattintson **a Tovább gombra: Tekintse át**és hozza létre.
12. Tekintse át a beállításokat. Ha módosítania kell a beállításokat, kattintson az **előző**gombra.
13. Kattintson a **Create** (Létrehozás) gombra.

Elindul a felhőalapú kiépítési folyamat. A privát felhő üzembe helyezése akár két óráig is eltarthat.

A meglévő privát felhő kibővítésével kapcsolatos útmutatásért lásd: [privát felhő](expand-private-cloud.md)kibontása.
