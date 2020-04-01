---
title: Az Azure-beli virtuális gépek áthelyezése egy másik régióba az Azure Site Recovery szolgáltatással
description: Az Azure Site Recovery használatával áthelyezi az Azure IaaS virtuális gépeket egyik Azure-régióból a másikba.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 4882206692c334d6ab6af28feb5d2cba5277eea1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78303936"
---
# <a name="move-azure-vms-to-another-region"></a>Azure-beli virtuális gépek áthelyezése egy másik régióba

Vannak különböző forgatókönyvek, amelyben szeretné áthelyezni a meglévő Azure IaaS virtuális gépek (Virtuális gépek) egyik régióból a másikba. Például szeretné javítani a meglévő virtuális gépek megbízhatóságát és rendelkezésre állását, a kezelhetőség javítása érdekében, vagy áthelyezni cégirányítási okokból. További információ: az [Azure vm move overview](azure-to-azure-move-overview.md). 

Az Azure [Site Recovery](site-recovery-overview.md) szolgáltatás használatával kezelheti és koordinálhatja a helyszíni gépek és az Azure-beli virtuális gépek vészutáni helyreállítását az üzletmenet folytonossága és a vészhelyreállítás (BCDR) számára. A Site Recovery használatával is kezelheti az Azure virtuális gépek áthelyezését egy másodlagos régióba.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> 
> * Az áthelyezés előfeltételeinek ellenőrzése
> * A forrásvirtuális gépek és a célterület előkészítése
> * Az adatok másolása és a replikáció engedélyezése
> * Tesztelje a konfigurációt, és végezze el az áthelyezést
> * A forrásrégió erőforrásainak törlése
> 
> [!NOTE]
> Ez az oktatóanyag bemutatja, hogyan helyezheti át az Azure-beli virtuális gépeket az egyik régióból a másikba. Ha javítania kell a rendelkezésre állási lehetőségeket azáltal, hogy egy rendelkezésre állási csoportban lévő virtuális gépeket egy másik régióban rögzített virtuális gépekzónába helyezi át, tekintse meg az [Azure-beli virtuális gépek áthelyezése a rendelkezésre állási zónákba című oktatóanyagot.](move-azure-vms-avset-azone.md)

## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy az Azure-beli virtuális gépek az Azure-régióban, ahonnan át szeretne helyezni.
- Ellenőrizze, hogy a [választott forrásrégió – célrégió kombináció](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)támogatott-e, és hozzon megalapozott döntést a célrégióról.
- Ismernie kell a [forgatókönyv-architektúrát és az összetevőket](azure-to-azure-architecture.md).
- Tekintse át a [támogatási korlátokat és követelményeket](azure-to-azure-support-matrix.md) ismertető részt.
- Ellenőrizze a fiókengedélyeket. Ha létrehozta az ingyenes Azure-fiókját, ön az előfizetés rendszergazdája. Ha nem Ön az előfizetés rendszergazdája, a rendszergazdával együttműködve rendelje hozzá a szükséges engedélyeket. A virtuális gép replikációjának engedélyezéséhez és az azure site recovery használatával lényegében adatok másolásához a következőkkel kell rendelkeznie:

    - Engedélyek virtuális gép az Azure-erőforrásokban történő létrehozásához. A Virtuálisgép közreműködő beépített szerepköre rendelkezik a következő engedélyekkel, amelyek a következők:
    - Engedély virtuális gépek a kiválasztott erőforráscsoportban történő létrehozásához
    - Engedély virtuális gépek a kiválasztott virtuális hálózaton történő létrehozásához
    - Engedély a kiválasztott tárfiókra történő íráshoz
    
    - Az Azure Site Recovery-műveletek kezeléséhez szükséges engedélyek. A Site Recovery Contributor szerepkör rendelkezik a Helyreállítási szolgáltatások tárolóban a Site Recovery-műveletek kezeléséhez szükséges összes engedéllyel.

- Győződjön meg arról, hogy a legújabb főtanúsítványok az áthelyezni kívánt Azure-beli virtuális gépeken vannak. Ha a legújabb főtanúsítványok nem a virtuális gép, biztonsági korlátozások megakadályozzák az adatok másolása a célrégióba.

- A Windows rendszerű virtuális gépek esetében az összes új Windows-frissítést telepíteni kell a virtuális gépen, így a megbízható főtanúsítványok mindegyike megtalálható lesz a számítógépen. Leválasztott környezet esetén hajtsa végre a vállalat szabványos Windows Update- és tanúsítványfrissítési folyamatait.
    
- Linuxos virtuális gépek esetén kövesse a Linux-forgalmazó által adott útmutatást a legújabb megbízható főtanúsítványok és visszavont tanúsítványok listájának bekéréséhez a virtuális gépen.
- Győződjön meg arról, hogy nem használ hitelesítési proxyt az áthelyezni kívánt virtuális gépek hálózati kapcsolatának szabályozására.

- Ha az áthelyezni kívánt virtuális gép nem fér hozzá az internethez, vagy tűzfalproxyt használ a kimenő hozzáférés szabályozására, [ellenőrizze a követelményeket.](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms)

- Azonosítsa a forráshálózati elrendezést és az összes jelenleg használt erőforrást. Ez magában foglalja, de nem kizárólagosan a terheléselosztók, hálózati biztonsági csoportok (NSG-k) és a nyilvános IP-k.

- Ellenőrizze, hogy az Azure-előfizetés lehetővé teszi-e, hogy virtuális gépeket hozzon létre a vész-helyreállítási célrégióban. A szükséges kvóta engedélyezéséhez vegye fel a kapcsolatot az ügyfélszolgálattal.

- Győződjön meg arról, hogy az előfizetés elegendő erőforrással rendelkezik a forrás virtuális gépeknek megfelelő méretű virtuális gépek támogatásához. Ha a Site Recovery segítségével másolja az adatokat a cél, Site Recovery kiválasztja az azonos méretű vagy a legközelebbi méretet a cél virtuális gép.

- Győződjön meg arról, hogy a forráshálózati elrendezésben azonosított összes összetevőhöz létrehoz egy célerőforrást. Ez a lépés fontos annak biztosításához, hogy a virtuális gépek minden funkcióval és funkcióval rendelkeznek a célrégióban, amely rendelkezik a forrásrégióban.

     > [!NOTE] 
     > Az Azure Site Recovery automatikusan felderíti és létrehozza a virtuális hálózatot, amikor engedélyezi a replikációt a forrás virtuális gép. A replikáció engedélyezéséhez előre létrehozhat egy hálózatot is, és hozzárendelheti azt a virtuális géphez a felhasználói folyamatban. Ahogy azt később említettük, manuálisan kell létrehoznia bármely más erőforrást a célrégióban.

    A forrásvirtuális gép konfigurációja alapján az Ön számára releváns leggyakrabban használt hálózati erőforrások létrehozásához tekintse meg az alábbi dokumentációt:
    - [Network security groups (Hálózati biztonsági csoportok)](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Terheléselosztók](https://docs.microsoft.com/azure/load-balancer)
    -  [Nyilvános IP-cím](../virtual-network/virtual-network-public-ip-address.md)
    - A többi hálózati összetevőt lásd a [hálózati dokumentációban.](https://docs.microsoft.com/azure/?pivot=products&panel=network)



## <a name="prepare"></a>Előkészítés
A következő lépések bemutatják, hogyan készülhet elő a virtuális gép az azure site recovery megoldásként való áthelyezéshez. 

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>A tároló létrehozása bármely régióban, kivéve a forrásrégiót

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) > **helyreállítási szolgáltatásokba.**
1. Válassza **az Erőforráskezelő** > **eszközök** > **biztonsági másolatkészítése és a Webhely-helyreállítás lehetőséget.**
1. A **Név** mezőben adja meg a **ContosoVMVault** rövid nevet. Ha egynél több előfizetéssel rendelkezik, válassza ki ezek közül a megfelelőt.
1. Hozza létre a **ContosoRG**erőforráscsoportot.
1. Válassza ki a kívánt Azure-régiót. A támogatott régiók ellenőrzéséhez tekintse meg a földrajzi elérhetőséget az [Azure Site Recovery díjszabási részleteiben.](https://azure.microsoft.com/pricing/details/site-recovery/)
1. A **Recovery Services-tárolókban**válassza **az Áttekintés** > **ContosoVMVault** > **+Replicate**lehetőséget.
1. A **Forrás** beállításnál válassza az **Azure** értéket.
1. A **Forrás helye** értékeként válassza ki azt az Azure-régiót, ahol az adott virtuális gépek éppen futnak.
1. Válassza ki a Resource Manager-alapú üzemi modellt. Ezután válassza a **Forrás-** és **Forrás erőforráscsoportot.**
1. A beállítások mentéséhez válassza az **OK gombot.**

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Engedélyezze az Azure virtuális gépeit replikációs célokra, és kezdje meg az adatok másolását

A Site Recovery lekéri az előfizetéshez és az erőforráscsoporthoz társított virtuális gépek listáját.

1. A következő lépésben jelölje ki az áthelyezni kívánt virtuális gép, majd kattintson az **OK gombra.**
1. A **Beállítások csoportban**válassza a **Vészhelyreállítás**lehetőséget.
1. A **Vész-helyreállítási** > **célrégió**konfigurálása területen válassza ki azt a célterületet, amelyre replikálni szeretne.
1. A jelen oktatóanyag esetében fogadja el a többi alapértelmezett értéket.
1. Válassza **a Replikáció engedélyezése**lehetőséget. Ez a lépés elindítja a virtuális gép replikációjának engedélyezéséhez egy feladatot.

    ![A replikáció engedélyezése](media/tutorial-migrate-azure-to-azure/settings.png)

## <a name="move"></a>Áthelyezés

A következő lépések bemutatják, hogyan hajthatja végre az áthelyezést a célterületre.

1. Menj a páncélterembe. A**Replikált elemek** **beállításai** > ban jelölje ki a virtuális gép, majd a **Feladatátvétel**lehetőséget.
2. A **Feladatátvétel** területen válassza a **Legújabb** elemet.
3. Válassza a **Gép leállítása a feladatátvétel megkezdése előtt** lehetőséget. A Site Recovery megkísérli leállítani a forrás virtuális gépeket a feladatátvétel indítása előtt. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A feladatátvételi folyamatot a Feladatok lapon **követheti.**
4. A feladat befejezése után ellenőrizze, hogy a virtuális gép a várt módon jelenik-e meg a cél Azure-régióban.


## <a name="discard"></a>Elvetés 

Abban az esetben, ha ellenőrizte az áthelyezett virtuális gép, és meg kell változtatni a feladatátvételi pont, vagy szeretne visszatérni egy korábbi pontra, a **Replikált elemek**, kattintson a jobb oldali válassza ki a virtuális gép > **változáshelyreállítási pont**. Ez a lépés lehetővé teszi, hogy adjon meg egy másik helyreállítási pontot, és feladatátvételt, hogy az egyik. 


## <a name="commit"></a>Véglegesítés 

Miután ellenőrizte az áthelyezett virtuális gép, és készen áll a módosítás véglegesítésére, a **Replikált elemek**, válassza ki a jobb oldali válassza ki a virtuális gép > **commit.** Ez a lépés befejezi az áthelyezési folyamatot a célterületre. Várjon, amíg a véglegesítési feladat befejeződik.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A következő lépések végigvezetik a forrásrégió és az áthelyezéshez használt kapcsolódó erőforrások karbantartásán.

Az áthelyezéshez használt összes erőforrás esetében:

- Nyissa meg a virtuális gép. Válassza **a Replikáció letiltása**lehetőséget. Ez a lépés leállítja a folyamat a virtuális gép adatainak másolása.

   > [!IMPORTANT]
   > Fontos, hogy ezt a lépést, hogy elkerülje az Azure Site Recovery replikációja.

Ha nem tervezi a forráserőforrások újbóli felhasználását, hajtsa végre az alábbi további lépéseket:

1. Törölje az előfeltételekben azonosított forrásrégió összes megfelelő hálózati [erőforrását.](#prerequisites)
1. Törölje a megfelelő tárfiókot a forrásrégióban.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure-virtuális gép egy másik Azure-régióba. Most konfigurálhatja a vész-helyreállítási a virtuális gép, amely áthelyezett.

> [!div class="nextstepaction"]
> [Vészhelyreállítás beállítása migrálás után](azure-to-azure-quickstart.md)

