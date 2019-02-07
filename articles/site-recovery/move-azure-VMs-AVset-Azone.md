---
title: Azure IaaS virtuális gépek áthelyezése egy másik Azure-régióba az Azure Site Recovery szolgáltatással. zóna – rögzíthetők virtuális gépként |} A Microsoft Docs
description: Az Azure Site Recoveryvel Azure IaaS virtuális gépek áthelyezése egy másik Azure-régióba zónához rögzített virtuális gépként.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 6a731750da4edfb4a71c00156c5ff527dee30941
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823368"
---
# <a name="move-azure-vms-into-availability-zones"></a>Az Azure virtuális gépek áthelyezése a rendelkezésre állási zónában történő
Az Azure-beli rendelkezésre állási zónák adatközpont meghibásodása ellen védi az alkalmazásait és adatait. Egyes rendelkezésre állási zónák épül fel egy vagy több adatközpont független áramellátással, hűtéssel és hálózati található. Rugalmasság biztosítása érdekében legalább három különálló zónát, az összes engedélyezett régióban van. Egy adott régión belül a rendelkezésre állási zónák fizikai elválasztása adatközpont meghibásodása ellen védi az alkalmazásokat és adatokat. A rendelkezésre állási zónákban az Azure iparági ajánlott 99,99 %-os virtuális gép ÜZEMIDŐT biztosít. Rendelkezésre állási zónában a említett egyes régiókban támogatott [Itt](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones). 

Egy forgatókönyvben, ahol a virtuális gépek "példányban" be egy adott régióban üzembe helyezve, és ezek rendelkezésre állási zónában helyezi a rendelkezésre állási továbbfejlesztése megteheti az Azure Site Recovery használatával. Ez további osztályozhatók:

- Egypéldányos virtuális gépeket áthelyezni a célrégióban a rendelkezésre állási zónák
- Virtuális gépek áthelyezése egy rendelkezésre állási csoportot a célrégióban a rendelkezésre állási zónában történő

> [!IMPORTANT]
> Jelenleg az Azure Site Recovery támogatja a virtuális gépek áthelyezése a régió egy másik, és nem támogatja a áthelyezése egy adott régión belül. 

## <a name="verify-prerequisites"></a>Előfeltételek ellenőrzése

- Győződjön meg arról, hogy rendelkezik-e a célrégióban [támogatja a rendelkezésre állási zónában](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones)–, ha a választott [forrásrégió - cél régió kombináció támogatott](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support) és a célként megadott régióban lévő képesek tájékozott döntés.
- Ismernie kell a [forgatókönyv-architektúrát és az összetevőket](azure-to-azure-architecture.md).
- Tekintse át a [támogatási korlátokat és követelményeket](azure-to-azure-support-matrix.md) ismertető részt.
- Fiókengedélyek ellenőrzése: Ha most hozta létre az ingyenes Azure-fiókját, akkor Ön az előfizetés rendszergazdája. Ha nem Ön az előfizetés rendszergazdája, akkor a rendszergazdával együttműködve rendelje hozzá a fiókhoz a szükséges engedélyeket. Engedélyezze a virtuális gép replikációját, és végül az adatok másolása az Azure Site Recovery használatával a cél, kell rendelkeznie:

    1. Engedélyek virtuális gép az Azure-erőforrásokban történő létrehozásához. A „Virtuális gépek közreműködője” beépített szerepkör rendelkezik ezekkel az engedélyekkel, amelyek a következők:
        - Engedély virtuális gépek a kiválasztott erőforráscsoportban történő létrehozásához
        - Engedély virtuális gépek a kiválasztott virtuális hálózaton történő létrehozásához
        - Engedély a kiválasztott tárfiókra történő íráshoz

    2. Az Azure Site Recovery-műveletek kezelésére szóló engedélyre is szüksége lesz. A „Site Recovery-közreműködő” szerepkör minden olyan engedéllyel rendelkezik, amely a Site Recovery-műveletek Recovery Services-tárolóban történő kezeléséhez szükséges.

## <a name="prepare-the-source-vms"></a>A forrás virtuális gépek előkészítése

1. A virtuális gépek kell használnia a felügyelt lemezek Ha való áthelyezéséhez használja a Site Recovery rendelkezésre állási zónában szeretné. Meglévő Windows virtuális gépek (VM), amely a nem felügyelt lemezek használata alakíthatja, a virtuális gépek a következő lépéseket a felügyelt lemezek használata alakíthatja [Itt](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks). Ez, győződjön meg arról, hogy a rendelkezésre állási csoportban van konfigurálva, "felügyelt. 
2. Ellenőrizze, hogy a legújabb főtanúsítványok megtalálhatók-e az áthelyezni kívánt Azure virtuális gépeken. Ha a legújabb főtanúsítványok nem találhatók található, az adatok másolását a célrégióban, biztonsági okokból nem lehet engedélyezni.

3. A Windows rendszerű virtuális gépek esetében az összes új Windows-frissítést telepíteni kell a virtuális gépen, így a megbízható főtanúsítványok mindegyike megtalálható lesz a számítógépen. Leválasztott környezet esetén hajtsa végre a vállalat szabványos Windows Update- és tanúsítványfrissítési folyamatait.

4. Linux rendszerű virtuális gépek esetében kövesse a Linux-terjesztőtől származó útmutatást a legújabb megbízható főtanúsítványok és a visszavont tanúsítványok listájának lekéréséhez a virtuális gépen.
5. Győződjön meg arról, hogy nem használ hitelesítési proxyt az áthelyezni kívánt virtuális gépek hálózati kapcsolatának vezérlésére.

6. Ha az áthelyezni kívánt virtuális gép nem rendelkezik internetkapcsolattal, és használ tűzfalproxyt a kimenő hozzáférés vezérléséhez, ellenőrizze a követelmények [Itt](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).

7. Azonosítsa a forrás hálózati elrendezés és az összes erőforrást, amely a jelenleg használt – beleértve a terheléselosztók, az NSG-k, az ellenőrzés a nyilvános IP-stb.

## <a name="prepare-the-target-region"></a>Készítse elő a célrégió

1. Győződjön meg arról, hogy Azure-előfizetése lehetővé teszi-e virtuális gépek létrehozását a vészhelyreállításhoz használni kívánt célrégióban. Forduljon az ügyfélszolgálathoz a szükséges kvóta engedélyezéséhez, ha szükséges.

2. Ellenőrizze, hogy előfizetése rendelkezik-e a megfelelő erőforrásokkal a forrásként szolgáló virtuális gépeknek megfelelő méretű virtuális gépek támogatásához. a Site Recovery használatakor az adatok másolásához a cél választja, az azonos méretű vagy a cél virtuális gép a legközelebbi lehetséges méretét.

3. Győződjön meg arról, hogy minden összetevő, a forrás hálózati elrendezés azonosított célként megadott erőforrás létrehozása. Ez azért fontos, ügyeljen arra, hogy, szélesítheti közzététele a célrégióban, a virtuális gépek rendelkeznek az a funkciók és szolgáltatások, amelyek a forrás volt.

    > [!NOTE]
    > Az Azure Site Recovery automatikusan felderíti és hoz létre egy virtuális hálózat és a storage-fiókot, ha engedélyezi a replikációt a forrás virtuális gép, vagy is előre létrehozni ezeket az erőforrásokat, és rendelje hozzá a virtuális géphez a replikáció engedélyezése lépés részeként. Azonban más erőforrások, az alábbiakban említett kell manuálisan hozza létre a célrégióban.

     Tekintse meg a következő dokumentumokban a leggyakrabban használt hálózati erőforrások létrehozása a megadott, a forrás virtuális gép konfigurációja alapján.

    - [Hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Terheléselosztók] (https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials
        
     - [Nyilvános IP-cím ](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
   Bármely más hálózati összetevők, tekintse meg a hálózati [dokumentációját.](https://docs.microsoft.com/azure/#pivot=products&panel=network) 

> [!IMPORTANT]
> Győződjön meg arról, a célzott zóna zónaredundáns load balancer használata. Tudjon meg többet [Itt](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones).

4. Manuálisan [hozzon létre egy nem éles hálózati](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) a célrégióban, ha a konfiguráció tesztelése, a végső kivágási keresztül a célrégióban a végrehajtása előtt. Ez a minimális zavaró tényező az éles környezetet hoz létre, és javasolt.


> [!NOTE]
> Az alábbi lépések a következők egyetlen virtuális gép, bővítheti ugyanaz a több virtuális gép lépjen a Recovery Services-tárolót és kattintson a + replikálás, és válassza a megfelelő virtuális gépek együtt.

## <a name="enable-replication"></a>A replikáció engedélyezése
Az alábbi lépéseket végigvezeti Önt az Azure Site Recovery használata az adatok a célrégióban replikálását engedélyezése előtt, végül helyezhetik át őket a rendelkezésre állási zónák.

1. Az Azure Portalon kattintson a **virtuális gépek**, és válassza ki a virtuális gép rendelkezésre állási zónában történő áthelyezésének.
2. A **Műveletek** területen kattintson a **Vészhelyreállítás** elemre.
3. A **Vészhelyreállítás konfigurálása** > **Célrégió** részben válassza ki a célrégiót, amelybe a replikálást végezni kívánja. Győződjön meg, hogy ebben a régióban [támogatja](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones) rendelkezésre állási zónák.
![Enable-rep-1. PNG](media/azure-vms-to-zones/enable-rep-1.PNG)

1. Válassza ki **tovább: Speciális beállítások**
2. Válassza ki a cél subscriptiom, a cél virtuális gép erőforráscsoportja és a virtuális hálózati helyénvalónak értékeit.
3. Az a **rendelkezésre állási** területén válassza a globális zóna, amelybe át szeretné helyezni a virtuális gép. 
> [!NOTE]
> Ha nem látja a rendelkezésre állási csoport vagy a globális zóna lehetőséget, adjon snore, amely a [Előfeltételek](#prepare-the-source-vms) teljesülnek-e, és a [előkészítési](#prepare-the-source-vms) forrás virtuális gépek teljes.

   ![enable-rep-2.PNG](media/azure-vms-to-zones/enable-rep-2.PNG)

7. Kattintson az engedélyezés beállításjegyzékkel. Ekkor elindul a virtuális gép replikálásának engedélyezési feladata.

## <a name="verify-settings"></a>A beállítások ellenőrzése

A replikálási feladat befejeződése után ellenőrizheti a replikálás állapotát, módosíthatja a replikációs beállításokat, és tesztelheti az üzemelő példányt.

1. A Virtuális gép menüben kattintson a **Vészhelyreállítás** elemre.
2. Ellenőrizheti a replikálás állapotát, a létrehozott helyreállítási pontokat, valamint a forrás- és célrégiókat a térképen.

   ![A replikálás állapota](media/azure-to-azure-quickstart/replication-status.png)

## <a name="test-the-configuration"></a>A konfiguráció tesztelése

1. A virtuális gép menüben kattintson a **vész-helyreállítási**.
2. Kattintson a **feladatátvételi teszt** ikonra.
3. A **Feladatátvételi teszt** területen válasszon ki egy helyreállítási pontot a feladatátvétel végrehajtásához:

   - **Legutóbb feldolgozott**: A virtuális gép feladatait a Site Recovery szolgáltatás által feldolgozott legutóbbi helyreállítási pontot. Megjelenik az időbélyeg. Ezzel a beállítással a rendszer nem tölt időt az adatok feldolgozásával, így a helyreállítási időre vonatkozó célkitűzés (RTO) alacsony.
   - **Legutóbbi alkalmazáskonzisztens**: Ez a beállítás minden virtuális gép a legutóbbi alkalmazáskonzisztens helyreállítási pontnak feladatait. Megjelenik az időbélyeg.
   - **Egyéni**: Válassza ki bármelyik helyreállítási pontot.

3. Válassza ki a teszt a céloznia, amelyre át szeretné helyezni a az Azure virtuális gépek a konfiguráció tesztelése az Azure virtual network. 

> [!IMPORTANT]
> Azt javasoljuk, hogy a teszt sikertelen, és nem az éles hálózati környezetben a célrégióban, amelybe a virtuális gépek áthelyezése végül szeretne egy különálló Azure-beli Virtuálisgép-hálózatot használja.

4. Az áthelyezés tesztelése indításához kattintson **OK**. A folyamat előrehaladásának megtekintéséhez a virtuális gépre kattintva megnyithatja a tulajdonságait. Vagy kattintson a **Feladatátvételi teszt** feladatra a tároló neve > **Beállítások** > **Feladatok** > **Site Recovery-feladatok** menüpontban.
5. A feladatátvétel befejezését követően az Azure-beli virtuális gép replikája megjelenik az Azure Portal > **Virtuális gépek** területen. Győződjön meg arról, hogy a virtuális gép fut, megfelelő a méretezése és a megfelelő hálózathoz csatlakozik.
6. Ha törölni szeretné az áthelyezés tesztelése során létrejön a virtuális gép, kattintson a **feladatátvételi teszt utáni karbantartás** a replikált elemen. A **megjegyzések**, jegyezze fel, és mentse a teszt megfigyeléseket.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Hajtsa végre az áttérés a célrégióban, és erősítse meg.

1.  A virtuális gép menüben kattintson a **vész-helyreállítási**.
2. Kattintson a **feladatátvételi** ikonra.
3. A **Feladatátvétel** területen válassza a **Legújabb** elemet. 
4. Válassza a **Gép leállítása a feladatátvétel megkezdése előtt** lehetőséget. A Site Recovery megkísérli leállítani a forrás virtuális gépeket a feladatátvétel indítása előtt. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon. 
5. Ha a feladat befejeződött, ellenőrizze, hogy a virtuális gép megjelenik a cél Azure-régió, a várt módon.
6. A **Replikált elemek** listában kattintson a jobb gombbal a virtuális gépre, majd kattintson a **Véglegesítés** parancsra. Ez befejezi az áttelepítési folyamat a célként megadott régióban. Várjon, amíg a véglegesítés feladat befejeződik.

## <a name="discard-the-resource-in-the-source-region"></a>Az erőforrás a forrásrégióban elvetése 

1. Keresse meg a virtuális Gépet.  Kattintson a **tiltsa le a replikációt**.  Ezzel leállítja a virtuális gép számára az adatok másolásának folyamatát.  

> [!IMPORTANT]
> Fontos a fenti lépés elkerülése érdekében a Site Recovery replikációs post számolják fel az áthelyezés végrehajtásához. A forrás replikálási beállításai automatikusan törlődnek. Vegye figyelembe, hogy a Site Recovery-bővítmény, amely telepítve van a replikáció során nem törlődnek, és manuálisan el kell távolítani. 

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure virtuális gépek rendelkezésre állásának egy rendelkezésre állási csoport vagy a rendelkezésre állási zónában helyezi nőtt. Vész-helyreállítási mostantól konfigurálhatja az áthelyezett virtuális gép számára.

> [!div class="nextstepaction"]
> [Vészhelyreállítás beállítása migrálás után](azure-to-azure-quickstart.md)


