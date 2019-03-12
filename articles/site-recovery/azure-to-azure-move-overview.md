---
title: Azure IaaS virtuális gépek áthelyezése egy másik Azure-régióba az Azure Site Recovery szolgáltatással |} A Microsoft Docs
description: Az Azure Site Recovery használatával az Azure IaaS virtuális gépek áthelyezése egy Azure-régióból a másikba.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 07fee8e5d051f0d2c04d3b5e34b66299734ca29c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57549143"
---
# <a name="move-azure-vms-to-another-region"></a>Azure-beli virtuális gépek áthelyezése egy másik régióba

Az Azure alap az ügyfél együtt növekszik, és támogatást nyújt a növekvő igényeknek lépést tartani az új régiók. Új funkciók is bekerülnek havi szolgáltatások között. Előfordulhat, hogy szeretné áthelyezni a virtuális gépek (VM) egy másik régióba vagy rendelkezésre állási zónák rendelkezésre állás növelése érdekében.

Ez az oktatóanyag leírja, amelyben szeretné a virtuális gépek áthelyezése különböző helyzetekben. Emellett bemutatja, hogyan lehet konfigurálni az architektúra a célrégióban, magas rendelkezésre állás eléréséhez. 

Ebben az oktatóanyagban ismertetik:

> [!div class="checklist"]

> * Virtuális gépek áthelyezése okok
> * Tipikus architektúrák
> * Helyezze át virtuális gépeket, hogy a célrégióban
> * Helyezze át virtuális gépeket a rendelkezésre állás növelése érdekében

## <a name="reasons-to-move-azure-vms"></a>Azure virtuális gépek áthelyezése okok

A következő okok miatt előfordulhat, hogy helyezze át virtuális gépeket:

- Már üzembe helyezte, egy adott régióban, és a egy új régió támogatása hozzáadva, amely a végfelhasználók számára az alkalmazás vagy szolgáltatás közelebb. Ebben a forgatókönyvben szeretne áthelyezni a virtuális gépek, hogy az új régióban, a késés csökkentése érdekében. Használja ugyanazt a megközelítést, ha szeretne az előfizetések egyesíteni, vagy ha a igénylő áthelyezheti az irányítás és a szervezeti szabályok vannak.
- A virtuális gép telepítve lett egy egypéldányos virtuális gépként, vagy egy rendelkezésre állási csoport részeként. Ha meg szeretné növelni a rendelkezésre állási SLA-k, továbbléphet a virtuális gépek rendelkezésre állási zónában történő.

## <a name="steps-to-move-azure-vms"></a>Lépések az Azure virtuális gépek áthelyezése

Virtuális gépek áthelyezése a következő lépésekből áll:

1. Ellenőrizze az előfeltételeket.
2. Készítse elő a forrás virtuális gépeket.
3. Készítse elő a célrégióban.
4. Adatok másolása a célrégióban. Azure Site Recovery replikációs technológia segítségével a célrégióban a forrás virtuális gép adatainak másolása.
5. A konfiguráció tesztelése. A replikáció befejezése után tesztelje a konfigurációt nem éles hálózathoz feladatátvételi teszt elvégzésével.
6. Az áthelyezés végrehajtásához.
7. Vesse el az erőforrásokat a forrásrégióban.

> [!NOTE]
> Ezeket a lépéseket részleteit a következő szakaszok szerepelnek.
> [!IMPORTANT]
> Jelenleg az Azure Site Recovery támogatja a virtuális gépek áthelyezését egy régió egy másik, de nem támogatja a áthelyezése egy adott régión belül.

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>Többrétegű telepítések jellemző architektúrák

Ez a szakasz ismerteti a leggyakoribb üzembe helyezési architektúra a többrétegű alkalmazások az Azure-ban. A példában egy alkalmazás három egy nyilvános IP-címmel. A szintek (webes, alkalmazás és adatbázis) mindegyike rendelkezik minden két virtuális gépet, és kapcsolódik az Azure load balancer az egyéb csomagokra. Az adatbázis csomag esetében az SQL Server Always On replikációt a magas rendelkezésre állású virtuális gépek között.

* **Egypéldányos virtuális gépek különböző rétegek telepített**: A szinten minden virtuális Géphez van konfigurálva, egy egypéldányos virtuális Gépet, és kapcsolódik, terheléselosztók, a többi szint. Ez a konfiguráció a legegyszerűbb elfogadására.

     ![Egypéldányos virtuális gépek üzembe helyezése szolgáltatásszinten](media/move-vm-overview/regular-deployment.png)

* **Virtuális gépek rendelkezésre állási csoportokban lévő összes csomag**: A szinten minden virtuális gép egy rendelkezésre állási csoportban van konfigurálva. [A rendelkezésre állási csoportok](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) győződjön meg arról, hogy az Azure-ban üzembe helyezett virtuális gépek egy fürtben több elkülönített hardvercsomópont között legyenek elosztva. Ez biztosítja, hogy Azure-ban egy hardveres vagy szoftveres hiba történik, ha a virtuális gépeknek csak egy részhalmazát érinti, és a teljes megoldás elérhető és működőképes maradjon.

     ![Rendelkezésre állási csoportokban lévő virtuális gépek üzembe helyezése](media/move-vm-overview/avset.png)

* **Az egyes szintek több rendelkezésre állási zónában üzembe helyezett virtuális gépek**: A szinten minden virtuális gép között van konfigurálva [rendelkezésre állási zónák](https://docs.microsoft.com/azure/availability-zones/az-overview). Egy rendelkezésre állási zónában az Azure-régióban a tartalék tartomány és a egy frissítési tartományt. Például, ha több Azure-régióban három zónában legalább három virtuális gépet hoz létre, a virtuális gépek hatékony és között elosztott három tartalék tartományt három frissítési tartományt. Az Azure platform felismeri a terjesztéshez győződjön meg arról, hogy a különböző zónában lévő virtuális gépek nem frissülnek egy időben, a frissítési tartományok között.

     ![Rendelkezésre állási zónában üzembe helyezés](media/move-vm-overview/zone.png)

## <a name="move-vms-as-is-to-a-target-region"></a>Helyezze át a virtuális gépek módon, hogy a célként megadott régióban

Alapján a [architektúrák](#typical-architectures-for-a-multi-tier-deployment) korábban említettük, itt van a központi telepítések fog kinézni a célrégióban, mert az áthelyezés után.

* **Egypéldányos virtuális gépek különböző rétegek telepített**

     ![Egypéldányos virtuális gépek üzembe helyezése szolgáltatásszinten](media/move-vm-overview/single-zone.png)

* **Virtuális gépek rendelkezésre állási csoportokban lévő összes csomag**

     ![Tartományok közötti terület a rendelkezésre állási csoportok](media/move-vm-overview/crossregionaset.png)

* **Az egyes szintek több rendelkezésre állási zónában üzembe helyezett virtuális gépek**

     ![Rendelkezésre állási zónák között a virtuális gép üzembe helyezése](media/move-vm-overview/azonecross.png)

## <a name="move-vms-to-increase-availability"></a>Helyezze át a virtuális gépek rendelkezésre állás növelése érdekében

* **Egypéldányos virtuális gépek különböző rétegek telepített**

     ![Egypéldányos virtuális gépek üzembe helyezése szolgáltatásszinten](media/move-vm-overview/single-zone.png)

* **Virtuális gépek rendelkezésre állási csoportokban lévő összes csomag**: Konfigurálhatja a virtuális gépek rendelkezésre állási csoportba külön rendelkezésre állási zónában történő, ha engedélyezi a virtuális gép replikációja az Azure Site Recovery használatával. A rendelkezésre állási SLA 99,9 %-os az áthelyezési művelet befejezése után.

     ![Virtuális gép üzembe helyezése a rendelkezésre állási csoportok és a rendelkezésre állási zónák között](media/move-vm-overview/aset-azone.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]

> * [Azure virtuális gépek áthelyezése egy másik régióba](azure-to-azure-tutorial-migrate.md)

> * [Az Azure virtuális gépek áthelyezése a rendelkezésre állási zónában történő](move-azure-vms-avset-azone.md)

