---
title: Virtuális gépek áthelyezése egy Azure-régióba rendelkezésre állási zónákkal az Azure Site Recovery használatával
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 5728ce7125695b191de4f91d5bd9003384f428a7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78298308"
---
# <a name="move-azure-vms-into-availability-zones"></a>Azure-beli virtuális gépek áthelyezése rendelkezésre állási zónákba
Az Azure rendelkezésre állási zónái segítenek megvédeni az alkalmazásokat és az adatokat az adatközponti hibáktól. Az egyes rendelkezésre állási zónák egy vagy több, önálló áramellátással, hűtéssel, és hálózattal rendelkező adatközpontból állnak. A rugalmasság biztosítása érdekében legalább három különálló zóna van az összes engedélyezett régióban. A rendelkezésre állási zónák fizikai elkülönítése egy régión belül segít megvédeni az alkalmazásokat és az adatokat az adatközponti hibáktól. A rendelkezésre állási zónák, az Azure kínál szolgáltatásiszint-szerződés (SLA) 99,99% a virtuális gépek (Virtuális gépek) rendelkezésre állása. A rendelkezésre állási zónák at bizonyos régiók támogatják, ahogy az [Azure-ban rendelkezésre állási zónák?](https://docs.microsoft.com/azure/availability-zones/az-overview#services-support-by-region)

Egy olyan forgatókönyvben, ahol a virtuális gépek *egyetlen példányként* egy adott régióban üzembe helyezése, és szeretné javítani a rendelkezésre állást azáltal, hogy ezeket a virtuális gépeket egy rendelkezésre állási zónába helyezi, ezt megteheti az Azure Site Recovery használatával. Ez a művelet tovább kategorizálható:

- Egypéldányos virtuális gépek áthelyezése a célrégió rendelkezésre állási zónáiba
- Virtuális gépek áthelyezése egy rendelkezésre állási csoportban a célterület rendelkezésre állási zónáiba

> [!IMPORTANT]
> Jelenleg az Azure Site Recovery támogatja a virtuális gépek áthelyezése az egyik régióból a másikba, de nem támogatja a régión belüli áthelyezést.

## <a name="check-prerequisites"></a>Előfeltételek ellenőrzése

- Ellenőrizze, hogy a célrégió [támogatja-e a rendelkezésre állási zónákat.](https://docs.microsoft.com/azure/availability-zones/az-overview#services-support-by-region) Ellenőrizze, hogy a [választott forrásrégió/célrégió kombináció támogatott-e.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support) Megalapozott döntést kell hoznia a célrégióról.
- Ismernie kell a [forgatókönyv-architektúrát és az összetevőket](azure-to-azure-architecture.md).
- Tekintse át a [támogatási korlátokat és követelményeket](azure-to-azure-support-matrix.md) ismertető részt.
- Ellenőrizze a fiókengedélyeket. Ha most hozta létre az ingyenes Azure-fiókját, ön az előfizetés rendszergazdája. Ha nem Ön az előfizetés rendszergazdája, a rendszergazdával együttműködve rendelje hozzá a szükséges engedélyeket. A virtuális gép replikációjának engedélyezéséhez és az Azure Site Recovery használatával az adatok at a célba való másolásához a következőkkel kell rendelkeznie:

    1. Engedélyt az Azure-erőforrásokban virtuális gép létrehozásához. A *Virtuálisgép közreműködő* beépített szerepköre rendelkezik a következő engedélyekkel, amelyek a következők:
        - Engedély virtuális gépek a kiválasztott erőforráscsoportban történő létrehozásához
        - Engedély virtuális gépek a kiválasztott virtuális hálózaton történő létrehozásához
        - Engedély a kiválasztott tárfiókra történő íráshoz

    2. Az Azure Site Recovery feladatok kezeléséhez szükséges engedély. A *Webhely-helyreállítási közreműködő* szerepkör rendelkezik a Helyreállítási szolgáltatások tárolójában a Hely-helyreállítási műveletek kezeléséhez szükséges összes engedéllyel.

## <a name="prepare-the-source-vms"></a>A forrásvirtuális gépek előkészítése

1. A virtuális gépeknek felügyelt lemezeket kell használniuk, ha a Site Recovery használatával egy rendelkezésre állási zónába szeretné áthelyezni őket. A nem felügyelt lemezeket használó meglévő Windows-virtuális gépeket menedzselt lemezekké alakíthatja. Kövesse a [Windows virtuális gépek konvertálása nem felügyelt lemezekről felügyelt lemezekké című lépéseit.](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks) Győződjön meg arról, hogy a rendelkezésre állási készlet *felügyeltként*van konfigurálva.
2. Ellenőrizze, hogy a legújabb főtanúsítványok vannak-e az áthelyezni kívánt Azure-beli virtuális gépeken. Ha a legújabb főtanúsítványok nincsenek jelen, az adatok másolása a célrégióba biztonsági korlátozások miatt nem engedélyezhető.

3. A Windows rendszerű virtuális gépek esetében az összes új Windows-frissítést telepíteni kell a virtuális gépen, így a megbízható főtanúsítványok mindegyike megtalálható lesz a számítógépen. Leválasztott környezetben kövesse a windows-frissítési és tanúsítványfrissítési folyamatokat a szervezet számára.

4. Linuxos virtuális gépek esetén kövesse a Linux-forgalmazó által adott útmutatást a legújabb megbízható főtanúsítványok és visszavont tanúsítványok listájának bekéréséhez a virtuális gépen.
5. Győződjön meg arról, hogy nem használ hitelesítési proxyt az áthelyezni kívánt virtuális gépek hálózati kapcsolatának szabályozására.

6. Ha az áthelyezni kívánt virtuális gép nem fér hozzá az internethez, és tűzfalproxyt használ a kimenő hozzáférés szabályozásához, ellenőrizze a [kimenő hálózati kapcsolat konfigurálása](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms)című szakaszkövetelményeit.

7. Azonosítsa a forráshálózati elrendezést és az ellenőrzéshez jelenleg használt erőforrásokat, beleértve a terheléselosztókat, az NSG-ket és a nyilvános IP-címeket.

## <a name="prepare-the-target-region"></a>A célterület előkészítése

1. Ellenőrizze, hogy az Azure-előfizetés lehetővé teszi-e a virtuális gépek létrehozását a vész-helyreállítási célrégióban. Szükség esetén lépjen kapcsolatba az ügyfélszolgálattal a szükséges kvóta engedélyezéséhez.

2. Ellenőrizze, hogy előfizetése rendelkezik-e a megfelelő erőforrásokkal a forrásként szolgáló virtuális gépeknek megfelelő méretű virtuális gépek támogatásához. Ha a Site Recovery használatával másolja az adatokat a cél, akkor kiválasztja az azonos méretű vagy a legközelebbi méretet a cél virtuális gép.

3. Hozzon létre célerőforrást a forráshálózati elrendezésben azonosított összes összetevőhöz. Ez a művelet biztosítja, hogy miután átvágta a célrégióba, a virtuális gépek rendelkeznek a forrásban lévő összes funkcióval és szolgáltatással.

    > [!NOTE]
    > Az Azure Site Recovery automatikusan felderíti és létrehozza a virtuális hálózati és tárfiókot, amikor engedélyezi a replikációt a forrás virtuális gép. Ezeket az erőforrásokat előre is létrehozhatja, és a replikációs lépés részeként hozzárendelheti a virtuális géphez. De minden más erőforrás, ahogy azt később említettük, manuálisan kell létrehozni őket a célrégióban.

     A következő dokumentumok a forrás virtuális gép konfigurációja alapján ismertetik, hogyan hozhat létre a leggyakrabban használt hálózati erőforrásokat, amelyek relevánsak az Ön számára.

    - [Network security groups (Hálózati biztonsági csoportok)](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Terheléselosztók](https://docs.microsoft.com/azure/load-balancer)
    - [Nyilvános IP-cím](../virtual-network/virtual-network-public-ip-address.md)
    
   A többi hálózati összetevőről a hálózati [dokumentációban](https://docs.microsoft.com/azure/?pivot=products&panel=network)tájékozódhat.

    > [!IMPORTANT]
    > Győződjön meg arról, hogy zónaredundáns terheléselosztót használ a célban. További információ a [Standard Load Balancer and Availability Zones oldalon.](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)

4. Manuálisan [hozzon létre egy nem éles hálózatot](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) a célrégióban, ha tesztelni szeretné a konfigurációt, mielőtt átvágna a célrégióra. Ezt a módszert javasoljuk, mert minimális interferenciát okoz az éles környezetben.

## <a name="enable-replication"></a>A replikáció engedélyezése
A következő lépések az Azure Site Recovery használatával ismertetik az adatok célrégióba történő replikációját, mielőtt végül áthelyezné őket a rendelkezésre állási zónákba.

> [!NOTE]
> Ezek a lépések egyetlen virtuális géphez tartoznak. Ugyanezt több virtuális gépre is kiterjesztheti. Nyissa meg a Recovery Services-tárolót, válassza a **+ Replikálás**lehetőséget, és együtt válassza ki a megfelelő virtuális gépeket.

1. Az Azure Portalon válassza **a Virtuális gépek**lehetőséget, és válassza ki a rendelkezésre állási zónákba áthelyezni kívánt virtuális gépet.
2. A **Műveletek** részen válassza a **Vészhelyreállítás** elemet.
3. A **Vész-helyreállítási** > **célrégió**konfigurálása területen válassza ki azt a célterületet, amelyre replikálni szeretne. Győződjön meg arról, hogy ez a régió [támogatja a](https://docs.microsoft.com/azure/availability-zones/az-overview#services-support-by-region) rendelkezésre állási zónák.

    ![A célrégió kiválasztása](media/azure-vms-to-zones/enable-rep-1.PNG)

4. Válassza a **Tovább: Speciális beállítások lehetőséget.**
5. Válassza ki a megfelelő értékeket a cél-előfizetés, a cél virtuálisgép-erőforráscsoport és a virtuális hálózat.
6. A **rendelkezésre állás i** szakaszban válassza ki azt a rendelkezésre állási zónát, amelybe át szeretné helyezni a virtuális gép. 
   > [!NOTE]
   > Ha nem látja a rendelkezésre állási készlet vagy a rendelkezésre állási zóna lehetőséget, győződjön meg arról, hogy az [előfeltételek](#prepare-the-source-vms) teljesülnek, és a forrás virtuális gépek [előkészítése](#prepare-the-source-vms) befejeződött.
  
    ![A rendelkezésre állási zóna kiválasztására szolgáló beállítások](media/azure-vms-to-zones/enable-rep-2.PNG)

7. Válassza ki a **Replikáció engedélyezése** elemet. Ez a művelet elindítja a virtuális gép replikációjának engedélyezéséhez egy feladatot.

## <a name="check-settings"></a>Beállítások ellenőrzése

A replikálási feladat befejeződése után ellenőrizheti a replikálás állapotát, módosíthatja a replikációs beállításokat, és tesztelheti az üzemelő példányt.

1. A Virtuális gép menüben válassza a **Vészhelyreállítás** elemet.
2. Ellenőrizheti a replikáció állapotát, a létrehozott helyreállítási pontokat, valamint a forrás- és célrégiókat a térképen.

   ![A replikálás állapota](media/azure-to-azure-quickstart/replication-status.png)

## <a name="test-the-configuration"></a>A konfiguráció tesztelése

1. A virtuális gép menüjében válassza a **Vészhelyreállítás**lehetőséget.
2. Válassza a **Feladatátvétel** tesztikont.
3. A **Feladatátvétel tesztben**válassza ki a feladatátvételhez használandó helyreállítási pontot:

   - **Legutóbb feldolgozott**: A virtuális gép feladatait a Site Recovery szolgáltatás által feldolgozott legutóbbi helyreállítási pontnak adja át. Megjelenik az időbélyeg. Ezzel a beállítással a rendszer nem tölt időt az adatok feldolgozásával, így a helyreállítási időre vonatkozó célkitűzés (RTO) alacsony.
   - **Legutóbbi alkalmazáskonzisztens**: Ez a beállítás az összes virtuális gép feladatait a legutóbbi alkalmazáskonzisztens helyreállítási pontnak adja át. Megjelenik az időbélyeg.
   - **Egyéni**: Bármelyik helyreállítási pontot kiválaszthatja.

3. Válassza ki a tesztcél Azure virtuális hálózat, amelyhez át szeretné helyezni az Azure virtuális gépek a konfiguráció teszteléséhez. 

    > [!IMPORTANT]
    > Azt javasoljuk, hogy a teszthiba egy külön Azure virtuálisgép-hálózatot használjon, és ne az éles hálózatot abban a célrégióban, amelybe a virtuális gépeket szeretné áthelyezni.

4. Az áthelyezés tesztelésének megkezdéséhez kattintson az **OK gombra.** A folyamat nyomon követéséhez válassza a virtuális gép a tulajdonságai megnyitásához. Vagy kiválaszthatja a **Feladatátvételi** feladat tesztfeladatát a tároló > **Beállítások** > **feladatok** > **hely-helyreállítási feladatok ban.**
5. A feladatátvétel befejezését követően az Azure-beli virtuális gép replikája megjelenik az Azure Portal > **Virtuális gépek** területen. Győződjön meg arról, hogy a virtuális gép fut, megfelelő a méretezése és a megfelelő hálózathoz csatlakozik.
6. Ha törölni szeretné az áthelyezés tesztelése során létrehozott virtuális gép, válassza **a Karbantartás teszt feladatátvétel** a replikált elem. A **Megjegyzések alkalmazásban**rögzítse és mentse a teszthez kapcsolódó megfigyeléseket.

## <a name="move-to-the-target-region-and-confirm"></a>Ugrás a célterületre, és megerősítés

1.  A virtuális gép menüjében válassza a **Vészhelyreállítás**lehetőséget.
2. Válassza a **Feladatátvétel** ikont.
3. A **Feladatátvétel** területen válassza a **Legújabb** elemet. 
4. Válassza a **Gép leállítása a feladatátvétel megkezdése előtt** lehetőséget. A Site Recovery megkísérli leállítani a forrás virtuális gépeket a feladatátvétel indítása előtt. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A feladatátvételi folyamatot a Feladatok lapon **követheti.** 
5. A feladat befejezése után ellenőrizze, hogy a virtuális gép a várt módon jelenik-e meg a cél Azure-régióban.
6. A **Replikált elemek** listában kattintson a jobb gombbal a virtuális gépre, majd kattintson a **Véglegesítés** parancsra. Ezzel befejezi az áthelyezési folyamatot a célterületre. Várjon, amíg a véglegesítési feladat befejeződik.

## <a name="discard-the-resource-in-the-source-region"></a>Az erőforrás elvetése a forrásrégióban

Nyissa meg a virtuális gép. Válassza **a Replikáció letiltása**lehetőséget. Ez a művelet leállítja a virtuális gép adatainak másolását.  

> [!IMPORTANT]
> Az előző lépéssel elkerülheti, hogy az áthelyezés után a Site Recovery replikációja díjat számítson fel. A forrás replikálási beállításai automatikusan törlődnek. Vegye figyelembe, hogy a replikáció részeként telepített Site Recovery bővítmény nem törlődik, és manuálisan kell eltávolítani.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure-virtuális gép rendelkezésre állását egy rendelkezésre állási csoportba vagy rendelkezésre állási zónába való áthelyezéssel növelte. Most már beállíthatja a vész-helyreállítási az áthelyezett virtuális gép.

> [!div class="nextstepaction"]
> [Vészhelyreállítás beállítása migrálás után](azure-to-azure-quickstart.md)


