---
title: Azure VMware-megoldás által CloudSimple Magánfelhő létrehozása
description: Ismerteti, hogyan lehet VMware számítási feladatok felhőbe kiterjeszteni a működési rugalmasság és folytonossági CloudSimple Magánfelhő létrehozása
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e5c03c1d8a865b792ce79e3e2b576a629b71e02c
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332906"
---
# <a name="create-a-cloudsimple-private-cloud"></a>Create a CloudSimple Private Cloud

Magánfelhő létrehozása segítségével közös kielégítése, a hálózati infrastruktúra-cím:

* **Növekedési**. A hardverek frissítési pont elérte a meglévő infrastruktúra, ha a Magánfelhő lehetővé teszi a nem szükséges új hardverberuházás.

* **Bővítés gyors**. Ha bármely ideiglenes vagy nem tervezett kapacitást kell merülnek fel, Magánfelhő teszi lehetővé a megnövelt kapacitás létrehozása késleltetés nélkül.

* **Nagyobb védelem**. A Magánfelhő három vagy több csomópont kap automatikus redundancia és magas rendelkezésre állású védelmét.

* **Hosszú távú infrastruktúra kell**. Az adatközpontok kapacitását, vagy azt szeretné átalakításának csökkentheti a költségeket, ha a Magánfelhő lehetővé teszi kivonása adatközpontok és a egy felhőalapú megoldás közben a vállalati műveletek kompatibilis át.

Magánfelhő létrehozásakor kap egy egyetlen vSphere-fürt és a felügyeleti fürt létrehozott virtuális gépeket.

## <a name="before-you-begin"></a>Előkészületek

A Magánfelhő létrehozása előtt ki kell építeni a csomópontokat.  A csomópont kiépítése további információkért lásd: [csomópontok kiépítésére CloudSimple – Azure által a VMware megoldás](create-nodes.md) cikk.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="access-the-cloudsimple-portal"></a>Hozzáférés a CloudSimple portáljához

Hozzáférés a [CloudSimple portál](access-cloudsimple-portal.md).

## <a name="create-a-new-private-cloud"></a>Hozzon létre egy új privát felhő

1. Az a **erőforrások** kattintson **új Magánfelhő**.

    ![Hozzon létre egy Magánfelhő - elindítása](media/create-pc-button.png)

2. Válassza ki a helyet, a Magánfelhő-erőforrások üzemeltetéséhez.

3. Válassza ki a CS28 vagy CS36 csomópont típusa you'ev megjelenjen a Magánfelhő. Az utóbbi lehetőséget tartalmaz a maximális számítási és memória-kapacitás.

4. Válassza ki a csomópontok számát a Magánfelhő számára. Választhat, legfeljebb a rendelkezésre álló csomópontok számát, hogy you'ev [kiépített](create-nodes.md).

    ![Hozzon létre egy Magánfelhő - alapbeállítások](media/create-private-cloud-basic-info.png)

5. Kattintson a **tovább: Speciális beállítások**.

6. VSphere/vSAN alhálózatokat a CIDR-tartományt adja meg. Győződjön meg arról, hogy a CIDR-tartományt nem lehetnek átfedésben a helyszíni vagy más Azure alhálózatok (virtuális hálózatok) vagy az átjáró-alhálózat.  Ne használja az Azure virtuális hálózataiban működő meghatározott minden CIDR-tartományt.
    
    **Beállítások CIDR-tartományt:** /24, /23, /22 vagy /21. Egy/24 CIDR-tartományt támogatja a legfeljebb kilenc csomópontokat, a /23 CIDR-tartományt legfeljebb a 41 csomópontokat, és a egy /22 és /21 CIDR-tartományt támogatja a legfeljebb 64 csomópontot (a Magánfelhő a csomópontok maximális száma).

    > [!CAUTION]
    > IP-címeket a CIDR-tartományt vSphere/vsan-hoz az Magánfelhő-infrastruktúrájában használatra van fenntartva.  Ne használja az IP-cím a tartományban lévő bármelyik virtuális gépet.

7. Kattintson a **tovább: Tekintse át, és hozzon létre**.

8. Tekintse át a beállításokat. Ha módosítania kell a beállításokat, kattintson a **előző**.

9. Kattintson a **Create** (Létrehozás) gombra.

Kiépítés Magánfelhő indul kattintva hozzon létre.  Nyomon követheti a folyamat a [feladatok](https://docs.azure.cloudsimple.com/activity/#tasks) CloudSimple portál oldalán.  Kiépítés percig is eltarthat 30, két óra.  Az üzembe helyezés befejeződése után kapni fog egy e-mailt.

## <a name="next-steps"></a>További lépések

* [Bontsa ki a magánfelhőben](expand-private-cloud.md)