---
title: Az Azure-beli virtuális gépek áthelyezése egy másik régióba az Azure Site Recovery szolgáltatással
description: Az Azure Site Recovery használatával áthelyezi az Azure-beli virtuális gépeket az egyik Azure-régióból a másikba.
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 3f715af835df6783ae5d59dd073a042a553fba4d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75498048"
---
# <a name="moving-azure-vms-to-another-azure-region"></a>Az Azure-beli virtuális gépek áthelyezése egy másik Azure-régióba

Ez a cikk áttekintést nyújt az Azure-beli virtuális gépek másik Azure-régióba való áthelyezésének okairól és lépéseiről az [Azure Site Recovery](site-recovery-overview.md)használatával. 


## <a name="reasons-to-move-azure-vms"></a>Az Azure-beli virtuális gépek áthelyezésének okai

Előfordulhat, hogy a virtuális gépeket a következő okok miatt helyezheti át:

- Már üzembe helyezett egy régióban, és egy új régió támogatása került hozzáadásra, amely közelebb áll az alkalmazás vagy szolgáltatás végfelhasználóihoz. Ebben a forgatókönyvben szeretné áthelyezni a virtuális gépek, ahogy az új régióban a késés csökkentése érdekében. Használja ugyanezt a megközelítést, ha szeretné konszolidálni az előfizetéseket, vagy ha vannak olyan cégirányítási vagy szervezeti szabályok, amelyek megkövetelik az áthelyezést.
- A virtuális gép egypéldányos virtuális gépként vagy egy rendelkezésre állási csoport részeként lett telepítve. Ha növelni szeretné a rendelkezésre álló SLOS-okat, áthelyezheti a virtuális gépeket egy rendelkezésre állási zónába.

## <a name="steps-to-move-azure-vms"></a>Az Azure-beli virtuális gépek áthelyezésének lépései

A virtuális gépek áthelyezése a következő lépéseket foglalja magában:

1. Ellenőrizze az előfeltételeket.
2. Készítse elő a forrás virtuális gépeket.
3. Készítsék elő a célterületet.
4. Adatok másolása a célterületre. Az Azure Site Recovery replikációs technológia segítségével másolja az adatokat a forrás virtuális gép ről a célrégióba.
5. Tesztelje a konfigurációt. A replikáció befejezése után tesztelje a konfigurációt egy nem éles hálózatra irányuló tesztfeladat-feladatátvétel végrehajtásával.
6. Hajtsd végre az lépést.
7. A forrásrégió erőforrásainak elvetése.

> [!NOTE]
> Ezekről a lépésekről a következő szakaszokban talál részleteket.
> [!IMPORTANT]
> Jelenleg az Azure Site Recovery támogatja a virtuális gépek áthelyezése az egyik régióból a másikba, de nem támogatja a régión belüli áthelyezést.

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>Többrétegű telepítés tipikus architektúrái

Ez a szakasz az Azure-ban egy többrétegű alkalmazás leggyakoribb központi telepítési architektúráit ismerteti. A példa egy háromrétegű alkalmazás nyilvános IP-cím. A rétegek (web, alkalmazás és adatbázis) mindegyike két virtuális géptel rendelkezik, és egy Azure-terheléselosztó csatlakozik a többi réteghez. Az adatbázisréteg sql server mindig a virtuális gépek között a magas rendelkezésre állás érdekében replikációs.

* **A különböző szintek között üzembe helyezett egypéldányos virtuális gépek:** A réteg minden egyes virtuális gép egypéldányos virtuális gépként van konfigurálva, és a terheléselosztók a többi réteghez csatlakozik. Ez a konfiguráció a legegyszerűbb, hogy elfogadja.

     ![Egypéldányos virtuális gép telepítése a rétegek között](media/move-vm-overview/regular-deployment.png)

* **Virtuális gépek minden egyes rétegben üzembe helyezett rendelkezésre állási csoportok között:** Minden egyes virtuális gép egy rétegben van konfigurálva egy rendelkezésre állási csoportban. [A rendelkezésre állási készletek](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) biztosítják, hogy az Azure-ban üzembe helyezett virtuális gépek több elkülönített hardvercsomópont között legyenek elosztva egy fürtben. Ez biztosítja, hogy ha az Azure-on belül hardver- vagy szoftverhiba történik, az csak a virtuális gépek egy részét érinti, és a teljes megoldás elérhető és működőképes marad.

     ![Virtuális gép üzembe helyezése az összes rendelkezésre állási csoportban](media/move-vm-overview/avset.png)

* **A rendelkezésre állási zónák között üzembe helyezett minden rétegben**lévő virtuális gépek: A réteg minden virtuális gépe [a rendelkezésre állási zónák](https://docs.microsoft.com/azure/availability-zones/az-overview)között van konfigurálva. Egy Azure-régióban rendelkezésre állási zóna egy tartalék tartomány és egy frissítési tartomány kombinációja. Ha például három vagy több virtuális gépet hoz létre egy Azure-régió három zónája között, a virtuális gépek hatékonyan oszlanak el három tartalék tartomány és három frissítési tartomány között. Az Azure platform felismeri ezt a terjesztési frissítési tartományok között, hogy győződjön meg arról, hogy a különböző zónákban lévő virtuális gépek nem frissülnek egyszerre.

     ![Rendelkezésre állási zóna telepítése](media/move-vm-overview/zone.png)

## <a name="move-vms-as-is-to-a-target-region"></a>Virtuális gépek áthelyezése a célterületre

A korábban említett [architektúrák](#typical-architectures-for-a-multi-tier-deployment) alapján a következőképpen fog kinézni a központi telepítések, miután végrehajtotta az áthelyezést, ahogy a célrégióra is.

* **A különböző szintekközött üzembe helyezett egypéldányos virtuális gépek**

     ![Egypéldányos virtuális gép telepítése a rétegek között](media/move-vm-overview/single-zone.png)

* **Virtuális gépek az egyes rétegekben az elérhetőségi csoportok között üzembe helyezve**

     ![Régiók közötti rendelkezésre állási csoportok](media/move-vm-overview/crossregionaset.png)

* **Virtuális gépek az egyes rétegekben a rendelkezésre állási zónák között üzembe helyezve**

     ![Virtuális gép telepítése a rendelkezésre állási zónák között](media/move-vm-overview/azonecross.png)

## <a name="move-vms-to-increase-availability"></a>Virtuális gépek áthelyezése a rendelkezésre állás növelése érdekében

* **A különböző szintekközött üzembe helyezett egypéldányos virtuális gépek**

     ![Egypéldányos virtuális gép telepítése a rétegek között](media/move-vm-overview/single-zone.png)

* **Virtuális gépek minden egyes rétegben üzembe helyezett különböző rendelkezésre állási csoportok:** Konfigurálhatja a virtuális gépek egy rendelkezésre állási csoport ban külön rendelkezésre állási zónák, ha engedélyezi a replikációt a virtuális gép az Azure Site Recovery használatával. Az sla a rendelkezésre állási lesz 99,99%, miután befejezte az áthelyezési műveletet.

     ![Virtuális gép üzembe helyezése a rendelkezésre állási csoportok és a rendelkezésre állási zónák között](media/move-vm-overview/aset-azone.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> 
> * [Azure-beli virtuális gépek áthelyezése egy másik régióba](azure-to-azure-tutorial-migrate.md)
> 
> * [Azure-beli virtuális gépek áthelyezése rendelkezésre állási zónákba](move-azure-vms-avset-azone.md)

