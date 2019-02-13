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
ms.openlocfilehash: 0d0009c833c313b5416998502601285e5b710a8d
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56112546"
---
# <a name="move-azure-vms-to-another-region"></a>Azure-beli virtuális gépek áthelyezése egy másik régióba

Az Azure egyre nagymértékben együtt az ügyfél kiindulási, és a bővíti a releváns új régiók a növekvő igények támogatása. Is találhatók újabb olyan képességeket, amelyek a szolgáltatás hozzáadja havi rendszerességgel történik. Ezért nincsenek alkalommal, amikor szeretne a virtuális gépek áthelyezése egy másik régióba vagy a rendelkezésre állás növelése érdekében a rendelkezésre állási zónában történő.

Ez a dokumentum végigvezeti a különböző forgatókönyvekben hol szeretne áthelyezni a virtuális gépek és a egy útmutató hogyan az architektúra kell konfigurálni a célkiszolgálón, magas rendelkezésre állás eléréséhez. 
> [!div class="checklist"]
> * [Miért át, az Azure virtuális gépek](#why-would-you-move-azure-vms)
> * [Azure virtuális gépek áthelyezése](#how-to-move-azure-vms)
> * [Tipikus architektúrák](#typical-architectures-for-a-multi-tier-deployment)
> * [Helyezze át a virtuális gépek módon, hogy a célként megadott régióban](#move-azure-vms-to-another-region)
> * [Helyezze át a virtuális gépek rendelkezésre állás növelése érdekében](#move-vms-to-increase-availability)


## <a name="why-would-you-move-azure-vms"></a>Miért át, az Azure virtuális gépek

Ügyfeleink a virtuális gépek áthelyezése a következő okok miatt:-

- Ha korábban már üzembe helyezte egy adott régióban és a egy új régióban váltak, amely a végfelhasználók számára az alkalmazás vagy szolgáltatás közelebb, akkor érdemes **helyezze át a virtuális gépek, az, az új régióban** a késés csökkentése érdekében. Ugyanezzel a módszerrel történik, ha szeretne az előfizetések egyesíteni, vagy nincsenek a cégirányítási / szervezeti szabályok, amelyek kell áthelyezni. 
- Ha a virtuális gép lett üzembe helyezve az Egypéldányos virtuális gép vagy részeként rendelkezésre állási készlet, és szeretné növelni a rendelkezésre állási SLA-k is **helyezhetik át a virtuális gépek rendelkezésre állási zónában**. 

## <a name="how-to-move-azure-vms"></a>Azure virtuális gépek áthelyezése
Virtuális gépek áthelyezése a következő lépésekből áll:

1. Előfeltételek ellenőrzése 
2. A forrás virtuális gépek előkészítése 
3. Készítse elő a célrégió 
4. Másolja az adatokat a célrégióban – használja az Azure Site Recovery replikációs technológiával az adatok a célrégióban a forrás virtuális gép másolása
5. A konfiguráció teszteléséhez: Egy a replikálás befejeződik, a konfiguráció teszteléséhez feladatátvételi teszt végrehajtásához keresztül nem éles hálózathoz.
6. Az áthelyezés végrehajtásához 
7. Elveti a forrásrégióban található erőforrások 


> [!IMPORTANT]
> Jelenleg az Azure Site Recovery támogatja a virtuális gépek áthelyezése a régió egy másik, és nem támogatja a áthelyezése egy adott régión belül. 

> [!NOTE]
> Ezek a lépések részletes útmutatást szerepelnek a forgatókönyv dokumentációját itt említett

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>Többrétegű telepítések jellemző architektúrák
Szakasz alatti bemutatja a leggyakoribb üzembe helyezési architektúrák ügyfelek fogad el, a többrétegű alkalmazások az Azure-ban. A példa megragadjuk itt nem egy nyilvános IP-Címmel rendelkező három a többrétegű alkalmazások. Minden, a rétegek – webes, alkalmazás és az adatbázis egyes 2 virtuális gépen van, és köti össze a többi szint egy terheléselosztót. Az adatbázis csomag esetében az SQL Always ON replikációt a magas rendelkezésre állású (HA) virtuális gépek között.

1.  **Egyetlen példánnyal rendelkező virtuális gépet üzembe helyezett különböző rétegekben**-köti össze az egyéb csomagokra terheléselosztók egypéldányos virtuális gép, egy szinten lévő mindegyik virtuális gép van konfigurálva. Ez az a legegyszerűbb konfiguráció, amely az ügyfelek fogad el.

       ![egyetlen virtuális gépek](media/move-vm-overview/regular-deployment.PNG)

2. **Az egyes szintek különböző rendelkezésre állási csoportban üzembe helyezett virtuális gépek** -csomagban minden virtuális gép úgy van konfigurálva, egy rendelkezésre állási beállítása. [A rendelkezésre állási csoportok](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) győződjön meg arról, hogy az Azure-ban üzembe helyezett virtuális gépek egy fürtben több elkülönített hardvercsomópont között legyenek elosztva. Ezáltal biztosítható, hogy ha hardveres vagy szoftveres hiba fordul elő az Azure-ban, az a virtuális gépeknek csak egy részhalmazát érintse, és a teljes megoldás továbbra is elérhető és működőképes maradjon. 
   
      ![avset](media/move-vm-overview/AVset.PNG)

3. **Az egyes szintek különböző rendelkezésre állási csoportban üzembe helyezett virtuális gépek** -csomagban minden virtuális gép között van konfigurálva [rendelkezésre állási zónák](https://docs.microsoft.com/azure/availability-zones/az-overview). Egy rendelkezésre állási zónában az Azure-régióban a tartalék tartomány és a egy frissítési tartományt. Például, ha több Azure-régióban három zónában legalább három virtuális gépet hoz létre, a virtuális gépek hatékony és között elosztott három tartalék tartományt három frissítési tartományt. Az Azure platform felismeri a terjesztéshez győződjön meg arról, hogy a különböző zónában lévő virtuális gépek nem frissülnek egy időben, a frissítési tartományok között.

      ![zóna-deploymnt](media/move-vm-overview/zone.PNG)



 ## <a name="move-vms-as-is-to-a-target-region"></a>Helyezze át a virtuális gépek módon, hogy a célként megadott régióban

A fentiek alapján említett [architektúrák](#typical-architectures-for-a-multi-tier-deployment), hogyan jelennek meg a központi telepítéseket, egyszer hajtsa végre az áttérés a célrégióban-jébe heres.


1. **Egypéldányos virtuális gépek különböző rétegek telepített** 

     ![single-zone.PNG](media/move-vm-overview/single-zone.PNG)

2. **Az egyes szintek különböző rendelkezésre állási csoportban üzembe helyezett virtuális gépek**

     ![crossregionAset.PNG](media/move-vm-overview/crossregionAset.PNG)


3. **Az egyes szintek különböző rendelkezésre állási zónában üzembe helyezett virtuális gépek**
      

     ![AzoneCross.PNG](media/move-vm-overview/AzoneCross.PNG)

## <a name="move-vms-to-increase-availability"></a>Helyezze át a virtuális gépek rendelkezésre állás növelése érdekében

1. **Egypéldányos virtuális gépek különböző rétegek telepített** 

     ![single-zone.PNG](media/move-vm-overview/single-zone.PNG)

2. **Az egyes szintek különböző rendelkezésre állási csoportban üzembe helyezett virtuális gépek** -lehet váltani, helyezze a virtuális gépeket egy rendelkezésre állási csoport külön rendelkezésre állási zónában, történő, ha úgy dönt, hogy engedélyezze a replikációt a virtuális gép Azure Site Recovery segítségével a konfigurálása. A rendelkezésre állási SLA 99,9 %-os az áthelyezési művelet befejezése után.

     ![aset-Azone.PNG](media/move-vm-overview/aset-Azone.PNG)


## <a name="next-steps"></a>További lépések

Ebben a dokumentumban tekintse át a virtuális gépek áthelyezésére az általános útmutatást. Ez a részletes végrehajtása tudni további információk:


> [!div class="nextstepaction"]
> * [Azure virtuális gépek áthelyezése egy másik régióba](azure-to-azure-tutorial-migrate.md)

> * [Az Azure virtuális gépek áthelyezése a rendelkezésre állási zónában történő](move-azure-VMs-AVset-Azone.md)

