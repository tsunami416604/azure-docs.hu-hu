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
ms.openlocfilehash: b6107211f49978bbacd1a827a9adc37ccef60a5b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57855411"
---
# <a name="move-azure-vms-into-availability-zones"></a>Az Azure virtuális gépek áthelyezése a rendelkezésre állási zónában történő
Az Azure-beli rendelkezésre állási zónák segítségével az alkalmazások és adatok védelme az Adatközpont meghibásodása. Minden rendelkezésre állási zónában egy vagy több független áramellátással, hűtéssel és hálózattal rendelkező adatközpont található. Rugalmasság biztosítása érdekében legalább három különálló zónát, az összes engedélyezett régióban van. Egy adott régión belül a rendelkezésre állási zónák fizikai elválasztása megvédi alkalmazásait és adatait a adatközpont meghibásodása. A rendelkezésre állási zónákban az Azure kínál egy szolgáltatásiszint-szerződés (SLA 99,99 %-os üzemidő a virtuális gépek (VM) esetében). A rendelkezésre állási zónák a leírtak szerint bizonyos régiókban támogatottak [Mik az Azure-beli rendelkezésre állási zónák?](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones).

Egy forgatókönyvben, ahol a virtuális gépeken üzemelnek *egypéldányos* egy adott régióban, és szeretné javítani a rendelkezésre állási ezek a virtuális gépek rendelkezésre állási zónában történő áthelyezésével, ehhez az Azure Site Recovery használatával. Ez a művelet további osztályozhatók:

- Egypéldányos virtuális gépeket áthelyezni a célrégióban a rendelkezésre állási zónák
- Virtuális gépek áthelyezése egy rendelkezésre állási csoportot a célrégióban a rendelkezésre állási zónában történő

> [!IMPORTANT]
> Jelenleg az Azure Site Recovery támogatja a virtuális gépek áthelyezését egy régió egy másik, de nem támogatja a áthelyezése egy adott régión belül.

## <a name="check-prerequisites"></a>Előfeltételek ellenőrzése

- Ellenőrizze, hogy rendelkezik-e a célrégióban [rendelkezésre állási zónák támogatása](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones). Ellenőrizze, hogy a választott [forrás régióban vagy a céloldali régió kombináció támogatott](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support). Győződjön meg a célrégióban a tájékozott döntést.
- Ismernie kell a [forgatókönyv-architektúrát és az összetevőket](azure-to-azure-architecture.md).
- Tekintse át a [támogatási korlátokat és követelményeket](azure-to-azure-support-matrix.md) ismertető részt.
- Ellenőrizze a fiók engedélyeit. Ha az imént létrehozott ingyenes Azure-fiókját, Ön az előfizetés rendszergazdája. Ha nem az előfizetés-rendszergazda, kérjen a rendszergazdától, rendelje hozzá a szükséges engedélyekkel. Engedélyezze a virtuális gép replikációját, és végül másolja az adatokat a cél Azure Site Recovery használatával, meg kell rendelkeznie:

    1. Virtuális gép létrehozása az Azure-erőforrások számára. A *virtuális gépek Közreműködője* beépített szerepkör rendelkezik ezekkel az engedélyekkel, többek között:
        - Engedély virtuális gépek a kiválasztott erőforráscsoportban történő létrehozásához
        - Engedély virtuális gépek a kiválasztott virtuális hálózaton történő létrehozásához
        - Engedély a kiválasztott tárfiókra történő íráshoz

    2. Az Azure Site Recovery-feladatok kezelésére jogosult. A *Site Recovery-közreműködő* szerepkör rendelkezik a Recovery Services-tárolót a Site Recovery-műveletek kezeléséhez szükséges összes engedélyt.

## <a name="prepare-the-source-vms"></a>A forrás virtuális gépek előkészítése

1. A virtuális gépek felügyelt lemezeket használja, ha a Site Recovery használatával a rendelkezésre állási zónában áthelyezi őket. Meglévő Windows virtuális gépek, amelyek a felügyelt lemezek használata nem felügyelt lemezek használata alakíthatja. Kövesse a lépéseket [Windows virtuális gép átalakítása nem felügyeltről felügyelt felügyelt lemezekre](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks). Győződjön meg arról, hogy a rendelkezésre állási csoportban van konfigurálva, *felügyelt*.
2. Ellenőrizze, hogy a legújabb főtanúsítványok megtalálhatók-e az áthelyezni kívánt Azure virtuális gépeken. Ha a legújabb főtanúsítványok nem találhatók található, az adatok másolását a célrégióban a biztonsági korlátozások miatt nem sikerült engedélyezni.

3. A Windows rendszerű virtuális gépek esetében az összes új Windows-frissítést telepíteni kell a virtuális gépen, így a megbízható főtanúsítványok mindegyike megtalálható lesz a számítógépen. Leválasztott környezet esetén hajtsa végre a szabványos Windows update és a tanúsítvány folyamatait a szervezet számára.

4. Linux rendszerű virtuális gépekhez kövesse az útmutatást, a Linux terjesztőt, hogy a legújabb megbízható főtanúsítványok és a visszavont tanúsítványok listájának lekérése a virtuális gép által biztosított.
5. Ellenőrizze, hogy ne hitelesítési proxyt használ az áthelyezni kívánt virtuális gépek hálózati kapcsolatának vezérlésére.

6. Ha az áthelyezni kívánt virtuális gép nem rendelkezik hozzáféréssel az internethez, és használ tűzfalproxyt a kimenő hozzáférés vezérléséhez, ellenőrizze a feltételeknek [ kimenő hálózati kapcsolat konfigurálása](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).

7. A forrás hálózati elrendezés és az ellenőrzéshez, többek között terheléselosztókhoz NSG-k és nyilvános IP-cím jelenleg használt erőforrások azonosítása.

## <a name="prepare-the-target-region"></a>Készítse elő a célrégió

1. Ellenőrizze, hogy az Azure-előfizetés lehetővé teszi, hogy hozhat létre virtuális gépeket a vészhelyreállításhoz használt célrégió. Ha szükséges, ügyfél támogatja a szükséges kvóta engedélyezéséhez.

2. Ellenőrizze, hogy előfizetése rendelkezik-e a megfelelő erőforrásokkal a forrásként szolgáló virtuális gépeknek megfelelő méretű virtuális gépek támogatásához. A Site Recovery használatával adatok másolása a cél az, ha azt választja ki az azonos méretű vagy a cél virtuális gép a legközelebbi lehetséges mérete.

3. Minden összetevő, a forrás hálózati elrendezés azonosítja a cél erőforrás létrehozása. Ez a művelet biztosítja, hogy a célrégióban, keresztül kivágási, a virtuális gépek után az összes kívánt funkciókat és szolgáltatásokat, a forrás volt.

    > [!NOTE]
    > Az Azure Site Recovery automatikusan felderíti és egy virtuális hálózati és storage-fiókot hoz létre, amikor engedélyezi a replikációt a forrás virtuális gép. Előre létrehozni ezeket az erőforrásokat is, és rendelje hozzá a virtuális géphez a replikáció engedélyezése lépés részeként. Azonban más erőforrások, amint már említettük, később kell manuálisan hozza létre a célrégióban.

     A következő dokumentumok mondja el, hogyan hozhat létre a leggyakrabban használt hálózati erőforrások, amelyek megfelelnek a megadott, a forrás virtuális gép konfigurációja alapján.

    - [Hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Terheléselosztók](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    - [Nyilvános IP-cím](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
   Bármely más hálózati összetevők, tekintse meg a hálózati [dokumentáció](https://docs.microsoft.com/azure/#pivot=products&panel=network).

    > [!IMPORTANT]
    > Győződjön meg arról, hogy zónaredundáns load balancer a célkiszolgálón. Tudjon meg többet a [Standard Load Balancer és rendelkezésre állási zónák](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones).

4. Manuálisan [hozzon létre egy nem éles hálózati](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) a célrégióban, ha azt szeretné, a konfiguráció teszteléséhez mielőtt Kivágás keresztül a célrégióban. Ez a módszer azt javasoljuk, mert az üzemi környezetben való minimális beavatkozás.

## <a name="enable-replication"></a>A replikáció engedélyezése
Az alábbi lépéseket végigvezeti Önt, ha az Azure Site Recovery használatával az adatok a célrégióban replikálását engedélyezése előtt, végül helyezhetik át őket a rendelkezésre állási zónák.

> [!NOTE]
> Ezen lépések végrehajtása egyetlen virtuális gép. Több virtuális gép azonos kiterjesztheti. Lépjen a Recovery Services-tárolót, válassza ki a **+ replikálás**, és válassza ki a megfelelő virtuális gépek együtt.

1. Az Azure Portalon válassza ki a **virtuális gépek**, és válassza ki a virtuális gép rendelkezésre állási zónában történő áthelyezésének.
2. A **műveletek**válassza **vész-helyreállítási**.
3. A **vészhelyreállítás konfigurálása** > **célrégió**, válassza ki a célrégióban, amelyhez a replikálást végezni kívánja. Győződjön meg, hogy ebben a régióban [támogatja](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones) rendelkezésre állási zónák.

    ![Választott célrégió](media/azure-vms-to-zones/enable-rep-1.PNG)

4. Válassza ki **tovább: Speciális beállítások**.
5. Válassza ki a megfelelő értékeket a célként megadott előfizetés, a céloldali virtuális gép erőforrás-csoport és a virtuális hálózat számára.
6. Az a **rendelkezésre állási** , amelybe át szeretné helyezni a virtuális gép rendelkezésre állási zóna válassza. 
   > [!NOTE]
   > Ha nem látja a rendelkezésre állási csoport vagy a globális zóna lehetőséget, győződjön meg arról, hogy a [Előfeltételek](#prepare-the-source-vms) teljesülnek-e, és a [előkészítési](#prepare-the-source-vms) forrás virtuális gépek befejeződött.
  
    ![A rendelkezésre állási zónában kiválasztására vonatkozó beállításokat](media/azure-vms-to-zones/enable-rep-2.PNG)

7. Válassza ki a **Replikáció engedélyezése** elemet. Ez a művelet elindít egy feladatot a virtuális gép replikálásának engedélyezéséhez.

## <a name="check-settings"></a>Beállítások ellenőrzése

A replikálási feladat befejeződése után ellenőrizheti a replikálás állapotát, módosíthatja a replikációs beállításokat, és tesztelheti az üzemelő példányt.

1. Válassza ki a virtuális gép menüben **vész-helyreállítási**.
2. Ellenőrizheti a replikációs állapotot, a létrehozott helyreállítási pontokat és a forrás és cél régiókat a térképen.

   ![A replikálás állapota](media/azure-to-azure-quickstart/replication-status.png)

## <a name="test-the-configuration"></a>A konfiguráció tesztelése

1. Válassza ki a virtuális gép menüben **vész-helyreállítási**.
2. Válassza ki a **feladatátvételi teszt** ikonra.
3. A **feladatátvételi teszt**, válasszon egy helyreállítási pontot a feladatátvétel végrehajtásához:

   - **Legutóbb feldolgozott**: A virtuális gép feladatait a Site Recovery szolgáltatás által feldolgozott legutóbbi helyreállítási pontot. Megjelenik az időbélyeg. Ezzel a beállítással a rendszer nem tölt időt az adatok feldolgozásával, így a helyreállítási időre vonatkozó célkitűzés (RTO) alacsony.
   - **Legutóbbi alkalmazáskonzisztens**: Ez a beállítás minden virtuális gép a legutóbbi alkalmazáskonzisztens helyreállítási pontnak feladatait. Megjelenik az időbélyeg.
   - **Egyéni**: Válassza ki bármelyik helyreállítási pontot.

3. Válassza ki a teszt a céloznia, amelyre át szeretné helyezni a az Azure virtuális gépek a konfiguráció tesztelése az Azure virtual network. 

    > [!IMPORTANT]
    > Azt javasoljuk, hogy a teszt sikertelen, és nem az éles hálózati környezetben a célrégióban, amelybe át szeretné helyezni a virtuális gépek egy különálló Azure-beli Virtuálisgép-hálózatot használja.

4. Az áthelyezés tesztelése indításához válassza **OK**. Nyomon követheti, jelölje be a virtuális Gépet a tulajdonságainak megnyitásához. Vagy választhatja a **feladatátvételi teszt** feladatot a tároló neve > **beállítások** > **feladatok** > **SiteRecovery-feladatok**.
5. A feladatátvétel befejezését követően az Azure-beli virtuális gép replikája megjelenik az Azure Portal > **Virtuális gépek** területen. Győződjön meg arról, hogy a virtuális gép fut, megfelelő a méretezése és a megfelelő hálózathoz csatlakozik.
6. Ha törli az áthelyezés tesztelése során létrejön a virtuális gép, jelölje be **feladatátvételi teszt utáni karbantartás** a replikált elemen. A **megjegyzések**, jegyezze fel, és mentse a teszt megfigyeléseket.

## <a name="move-to-the-target-region-and-confirm"></a>Helyezze át a célrégióban, és győződjön meg róla

1.  Válassza ki a virtuális gép menüben **vész-helyreállítási**.
2. Válassza ki a **feladatátvételi** ikonra.
3. A **Feladatátvétel** területen válassza a **Legújabb** elemet. 
4. Válassza a **Gép leállítása a feladatátvétel megkezdése előtt** lehetőséget. A Site Recovery megkísérli leállítani a forrás virtuális gépeket a feladatátvétel indítása előtt. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon. 
5. A feladat befejezése után ellenőrizze, hogy a virtuális gép megjelenik a cél Azure-régió, a várt módon.
6. A **Replikált elemek** listában kattintson a jobb gombbal a virtuális gépre, majd kattintson a **Véglegesítés** parancsra. Ez befejezi az áttelepítési folyamat a célként megadott régióban. Várjon, amíg a véglegesítés feladat befejeződött.

## <a name="discard-the-resource-in-the-source-region"></a>Az erőforrás a forrásrégióban elvetése

Nyissa meg a virtuális Gépet. Válassza ki **tiltsa le a replikációt**. Ez a művelet leállítja a virtuális gép számára az adatok másolásának folyamatát.  

> [!IMPORTANT]
> Hajtsa végre az előző lépés számolják fel a Site Recovery replikációjára az áthelyezés után elkerülése érdekében. A forrás replikálási beállításai automatikusan törlődnek. Vegye figyelembe, hogy a Site Recovery-bővítmény, amely telepítve van a replikáció során nem törlődnek, és manuálisan el kell távolítani.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure virtuális gépek rendelkezésre állásának egy rendelkezésre állási csoport vagy a rendelkezésre állási zónában helyezi nőtt. Most már beállíthatja vész-helyreállítási az áthelyezett virtuális gép számára.

> [!div class="nextstepaction"]
> [Vészhelyreállítás beállítása migrálás után](azure-to-azure-quickstart.md)


