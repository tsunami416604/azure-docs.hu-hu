---
title: Virtuális gép áthelyezése másik régióba (Azure Site Recovery)
description: Ismerje meg, hogyan telepítheti át az SQL Server virtuális gépét egyik régióból a másikba az Azure-on belül.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 3b84119cdcc1bb7e8603de64e3d23c69dac70cc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022293"
---
# <a name="move-sql-server-vm-to-another-region-within-azure-with-azure-site-recovery-services"></a>Sql Server virtuális gép áthelyezése az Azure-beli Webhely-helyreállítási szolgáltatásokkal az Azure-on belül egy másik régióba

Ez a cikk bemutatja, hogyan használhatja az Azure Site Recovery segítségével az SQL Server virtuális gép (VM) áttelepítése az egyik régióból a másikba az Azure-on belül. 

Az SQL Server virtuális gép áthelyezése egy másik régióba a következőket igényli:
1. [**Felkészülés:**](#prepare-to-move)Győződjön meg arról, hogy mind a forrás SQL Server virtuális gép, mind a célrégió megfelelően felkészült az áthelyezésre. 
1. [**Konfigurálás:**](#configure-azure-site-recovery-vault)Az SQL Server virtuális gép áthelyezése megköveteli, hogy az az Azure Site Recovery tárolón belül replikált objektum. Hozzá kell adnia az SQL Server virtuális gép az Azure Site Recovery tároló. 
1. [**Tesztelés:**](#test-move-process)Az SQL Server virtuális gép áttelepítése megköveteli, hogy a forrásrégióból a replikált célrégióba átvegye azt. Annak érdekében, hogy az áthelyezési folyamat sikeres legyen, először meg kell vizsgálnia, hogy az SQL Server virtuális gép sikeresen feladatátvételt a célrégióba. Ez segít ki minden problémát, és elkerülni őket, amikor a tényleges lépés végrehajtása. 
1. [**Áthelyezés:**](#move-the-sql-server-vm)Miután a teszt feladatátvétel lejárta után, és tudja, hogy biztonságosan áttelepítheti az SQL Server virtuális gép, elvégezheti a virtuális gép áthelyezése a célrégióba. 
1. [**Tisztítás:**](#clean-up-source-resources)A számlázási díjak elkerülése érdekében távolítsa el az SQL Server virtuális gép a tárolóból, és az erőforráscsoportban maradt felesleges erőforrásokat. 

## <a name="verify-prerequisites"></a>Előfeltételek ellenőrzése 

- Ellenőrizze, hogy a forrásrégióból a célrégióba való áttérés [támogatott-e.](../../../site-recovery/azure-to-azure-support-matrix.md#region-support)  
- Tekintse át a [forgatókönyv-architektúrát és -összetevőket,](../../../site-recovery/azure-to-azure-architecture.md) valamint a [támogatási korlátozásokat és követelményeket.](../../../site-recovery/azure-to-azure-support-matrix.md) 
- Ellenőrizze a fiókengedélyeket. Ha létrehozta az ingyenes Azure-fiókját, ön az előfizetés rendszergazdája. Ha nem Ön az előfizetés rendszergazdája, a rendszergazdával együttműködve rendelje hozzá a szükséges engedélyeket. A virtuális gép replikációjának engedélyezéséhez és az Azure Site Recovery használatával történő adatok másolásához a következőkre van szüksége: 
    - Virtuális gép létrehozásához szükséges engedélyek. A *Virtuálisgép közreműködő* beépített szerepköre rendelkezik a következő engedélyekkel, amelyek a következők: 
        - A kijelölt erőforráscsoportban virtuális gép létrehozására vonatkozó engedélyek. 
        - A kijelölt virtuális hálózatban virtuális gép létrehozására vonatkozó engedélyek. 
        - A kijelölt tárfiókba történő írásra vonatkozó engedélyek. 
      - Az Azure Site Recovery-műveletek kezeléséhez szükséges engedélyek. A *Site Recovery Contributor* szerepkör rendelkezik a Helyreállítási szolgáltatások tárolóban a Site Recovery-műveletek kezeléséhez szükséges összes engedéllyel.  

## <a name="prepare-to-move"></a>Felkészülés az áthelyezésre
Készítse elő a forrás SQL Server virtuális gép és a célrégió az áthelyezéshez. 

### <a name="prepare-the-source-sql-server-vm"></a>A forrás SQL Server virtuális gépének előkészítése

- Győződjön meg arról, hogy a legújabb főtanúsítványok az áthelyezni kívánt SQL Server virtuális gépen találhatók. Ha a legújabb főtanúsítványok nincsenek ott, a biztonsági korlátozások megakadályozzák az adatok másolását a célterületre. 
- Windows virtuális gépek esetén telepítse a legújabb Windows-frissítéseket a virtuális gépre, hogy az összes megbízható főtanúsítvány a számítógépen található. Leválasztott környezetben kövesse a windows felhasználói felület és a tanúsítvány frissítésének szabványos folyamatát a szervezet számára. 
- Linuxos virtuális gépek esetén kövesse a Linux-forgalmazó által adott útmutatást a legújabb megbízható főtanúsítványok és visszavont tanúsítványok listájának bekéréséhez a virtuális gépen. 
- Győződjön meg arról, hogy nem használ hitelesítési proxyt az áthelyezni kívánt virtuális gépek hálózati kapcsolatának szabályozásához. 
- Ha az áthelyezni kívánt virtuális gép nem fér hozzá az internethez, vagy tűzfalproxyt használ a kimenő hozzáférés szabályozására, ellenőrizze a követelményeket. 
- Azonosítsa a forráshálózati elrendezést és az összes jelenleg használt erőforrást. Ez magában foglalja, de nem kizárólagosan a terheléselosztók, hálózati biztonsági csoportok (NSG-k) és a nyilvános IP-k. 

### <a name="prepare-the-target-region"></a>A célterület előkészítése

- Ellenőrizze, hogy az Azure-előfizetés lehetővé teszi-e, hogy virtuális gépeket hozzon létre a vész-helyreállítási célrégióban. A szükséges kvóta engedélyezéséhez vegye fel a kapcsolatot az ügyfélszolgálattal. 
- Győződjön meg arról, hogy az előfizetés elegendő erőforrással rendelkezik a forrás virtuális gépeknek megfelelő méretű virtuális gépek támogatásához. Ha a Site Recovery segítségével másolja az adatokat a cél, Site Recovery kiválasztja az azonos méretű, vagy a legközelebbi lehetséges méretet a cél virtuális gép. 
- Győződjön meg arról, hogy a forráshálózati elrendezésben azonosított összes összetevőhöz létrehoz egy célerőforrást. Ez a lépés fontos annak biztosításához, hogy a virtuális gépek minden funkcióval és funkcióval rendelkeznek a célrégióban, amely rendelkezik a forrásrégióban. 
    - Az Azure Site Recovery automatikusan felderíti és létrehozza a virtuális hálózatot, amikor engedélyezi a replikációt a forrás virtuális gép. A replikáció engedélyezéséhez előre létrehozhat egy hálózatot is, és hozzárendelheti azt a virtuális géphez a felhasználói folyamatban. A célrégióban manuálisan kell létrehoznia bármely más erőforrást.
- A forrásvirtuális gép konfigurációja alapján az Ön számára releváns leggyakrabban használt hálózati erőforrások létrehozásához tekintse meg az alábbi dokumentációt: 
    - [Network security groups (Hálózati biztonsági csoportok)](../../../virtual-network/tutorial-filter-network-traffic.md) 
    - [Load Balancer](../../../load-balancer/tutorial-load-balancer-basic-internal-portal.md)
    - [Nyilvános IP-cím](../../../virtual-network/virtual-network-public-ip-address.md)
    - További hálózati összetevőket a [hálózati dokumentációban](../../../virtual-network/virtual-networks-overview.md)talál.
- Manuálisan hozzon létre egy nem éles hálózatot a célrégióban, ha a végső áthelyezés végrehajtása előtt tesztelni szeretné a konfigurációt. Ezt a lépést azért javasoljuk, mert minimális interferenciát biztosít az éles hálózattal. 

## <a name="configure-azure-site-recovery-vault"></a>Az Azure site recovery-tárolókonfigurálása

A következő lépések bemutatják, hogyan használhatja az Azure Site Recovery adatok másolása a célrégióba. Hozza létre a Helyreállítási szolgáltatások tárolóját a forrásrégión kívül bármely más régióban. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
1. Válassza az **erőforrás létrehozását** a navigációs ablak bal felső sarkából. 
1. Válassza **az Informatikai & felügyeleti eszközeit,** majd a Biztonsági mentés és a Webhely **helyreállítása**lehetőséget. 
1. Az **Alapok lapon** a **Projekt részletei**csoportban hozzon létre egy új erőforráscsoportot a célrégióban, vagy válasszon ki egy meglévő erőforráscsoportot a célrégióban. 
1. A **Példány részletei csoportban**adja meg a tároló nevét, majd válassza ki a **célterületet** a legördülő menüből. 
1. Válassza **a Véleményezés + Létrehozás** lehetőséget a Recovery Services-tároló létrehozásához. 
1. Válassza a **Minden szolgáltatás** lehetőséget a navigációs ablak bal felső `recovery services`sarkában és a keresőmező típusában. 
1. (Opcionálisan) Válassza ki a **Recovery Services-tárolók** melletti csillagot a gyors navigációs sávhoz való hozzáadásához. 
1. Válassza **a Helyreállítási szolgáltatások tárolóit,** majd válassza ki a létrehozott Helyreállítási szolgáltatások tárolóját. 
1. Az **Áttekintő** ablaktáblán válassza a **Replikálás**lehetőséget. 

   ![Replikáció konfigurálása](media/virtual-machines-windows-sql-move-to-new-region/configure-replication.png)

1. Válassza **a Forrás** lehetőséget, majd válassza az **Azure** forrásként lehetőséget. Válassza ki a megfelelő értékeket a többi legördülő mezőkhöz, például a forrásvirtuális gépek helyét. Csak a Forrás **hely** régióban található erőforráscsoportok lesznek láthatók a **Forrás erőforráscsoport** mezőben. 
1. Válassza **a Virtuális gépek** lehetőséget, majd válassza ki az áttelepíteni kívánt virtuális gépeket. Válassza **az OK gombot** a virtuális gép kiválasztásának mentéséhez. 
1. Válassza a **Beállítások**lehetőséget, majd a legördülő menüből válassza ki a **célhelyet.** Ennek a korábban készített erőforráscsoportnak kell lennie. 
1. Miután testreszabta a replikációt, válassza a **Célerőforrások létrehozása lehetőséget** az új helyen lévő erőforrások létrehozásához. 
1. Az erőforrás-létrehozás befejezése után válassza **a Replikáció engedélyezése** az SQL Server virtuális gép replikációjának indításához a forrásból a célrégióba lehetőséget.
1. A replikáció állapotát úgy ellenőrizheti, hogy a helyreállítási tárolóba navigál, kiválasztja a **Replikált elemeket,** és megtekinti az SQL Server virtuális gép **állapotát.** A **Védett** állapot azt jelzi, hogy a replikáció befejeződött. 

   ![Replikáció állapotának ellenőrzése](media/virtual-machines-windows-sql-move-to-new-region/check-replication-status.png)

## <a name="test-move-process"></a>Vizsgálati áthelyezési folyamat
A következő lépések bemutatják, hogyan használhatja az Azure Site Recovery az áthelyezési folyamat teszteléséhez. 

1. Nyissa meg a **Recovery Services-tárolót** az [Azure Portalon,](https://portal.azure.com) és válassza **a Replikált elemek lehetőséget.** 
1. Jelölje ki az áthelyezni kívánt SQL Server virtuális gépét, ellenőrizze, hogy a replikáció állapota kifogástalan **állapotúként** **jelenik-e meg,** majd válassza a **Feladatátvétel tesztelése**lehetőséget. 

   ![A virtuális gép feladatátvétele tesztelése](media/virtual-machines-windows-sql-move-to-new-region/test-failover-of-replicated-vm.png)

1. A **Feladatátvétel tesztlapján** válassza ki a feladatátvételhez a **legújabb alkalmazáskonzisztens** helyreállítási pontot, mivel ez az egyetlen olyan pillanatképtípus, amely képes garantálni az SQL Server adatkonzisztenciáját. 
1. Válassza ki a virtuális hálózat ot az **Azure virtuális hálózat** alatt, majd válassza az OK **gombot** a feladatátvétel teszteléséhez. 
   
   >[!IMPORTANT]
   > Azt javasoljuk, hogy egy külön Azure virtuális gép hálózat a feladatátvételi teszt. Ne használja azt az éles hálózatot, amely akkor lett beállítva, amikor engedélyezte a replikációt, és hogy a virtuális gépeket végül át szeretné helyezni. 

1. A folyamat figyeléséhez keresse meg a tárolót, válassza a **Site Recovery feladatok** a **figyelés**csoportban, majd válassza ki a folyamatban lévő **feladatátvételi feladat tesztelése** lehetőséget.

   ![Feladatátvételi teszt állapotának figyelése](media/virtual-machines-windows-sql-move-to-new-region/monitor-failover-test-job.png)

1. Miután a teszt befejeződött, keresse meg a **virtuális gépek** a portálon, és tekintse át az újonnan létrehozott virtuális gép. Győződjön meg arról, hogy az SQL Server virtuális gép fut, megfelelően van méretezve, és csatlakozik a megfelelő hálózathoz. 
1. Törölje a teszt részeként létrehozott virtuális gép, mivel a **feladatátvételi** beállítás szürkén jelenik meg, amíg a feladatátvételi teszt erőforrások törlődnek. Lépjen vissza a tárolóba, válassza **a Replikált elemek**lehetőséget, jelölje ki az SQL Server virtuális gépét, és válassza a **Karbantartásteszt feladatátvétele parancsot.** Rögzítse és mentse a teszthez kapcsolódó megfigyeléseket a **Megjegyzések** szakaszban, és jelölje be a Tesztelés befejeződött jelölőnégyzet **ét. Törölje a tesztfeladat-átvételi virtuális gépeket**. Válassza **az OK gombot** az erőforrások karbantartásához a teszt után. 

   ![elemek karbantartása feladatátvételi teszt után](media/virtual-machines-windows-sql-move-to-new-region/cleanup-test-items.png)

## <a name="move-the-sql-server-vm"></a>Az SQL Server virtuális gép áthelyezése 
A következő lépések bemutatják, hogyan helyezheti át az SQL Server virtuális gép a forrásrégióból a célrégióba. 

1. Nyissa meg a Recovery Services-tárolót, válassza **a Replikált elemek**lehetőséget, jelölje ki a virtuális gép, majd a **Feladatátvétel**lehetőséget. **Recovery Services** 

   ![Feladatátvétel kezdeményezése](media/virtual-machines-windows-sql-move-to-new-region/initiate-failover.png)

1. Válassza ki a **legújabb alkalmazáskonzisztens** helyreállítási pontot a **Helyreállítási pont**csoportban. 
1. Jelölje be a **Számítógép leállítása a feladatátvétel megkezdése előtt**jelölőnégyzetet. A Site Recovery megpróbálja leállítani a forrás virtuális gép, mielőtt a feladatátvétel. A feladatátvétel akkor is folytatódik, ha a leállítás sikertelen. 
1. A feladatátvétel elindításához válassza az **OK gombot.**
1. Figyelheti a feladatátvételi folyamatot ugyanabból a **Site Recovery feladatok** lapmegtekintett, amikor a feladatátvételi teszt az előző szakaszban. 
1. A feladat befejezése után ellenőrizze, hogy az SQL Server virtuális gép a várt módon jelenik-e meg a célrégióban. 
1. Lépjen vissza a tárolóba, válassza a **Replikált elemek**lehetőséget, jelölje ki az SQL Server virtuális gépét, és válassza a **Véglegesítés** lehetőséget az áthelyezési folyamat célterületre való befejezéséhez. Várjon, amíg a véglegesítési feladat befejeződik. 
1. Regisztrálja az SQL Server virtuális gép az SQL virtuálisgép-erőforrás-szolgáltató, hogy az **SQL virtuális gép** kezelhetőségét az Azure Portalon és az erőforrás-szolgáltatóhoz társított funkciók. További információ: [Sql Server VM regisztrálása sql vm erőforrás-szolgáltatóval.](virtual-machines-windows-sql-register-with-rp.md) 

  > [!WARNING]
  > Az SQL Server-adatok konzisztenciája csak alkalmazáskonzisztens pillanatképek esetén garantált. A **legújabb feldolgozott** pillanatkép nem használható az SQL Server feladatátvételhez, mivel az összeomlás-helyreállítási pillanatkép nem tudja garantálni az SQL Server adatkonzisztenciáját. 

## <a name="clean-up-source-resources"></a>Forrásforrások karbantartása
A számlázási díjak elkerülése érdekében távolítsa el az SQL Server virtuális gép a tárolóból, és törölje a felesleges kapcsolódó erőforrásokat. 

1. Lépjen vissza a **Site Recovery** tárolóba, válassza **a Replikált elemek**lehetőséget, és válassza ki az SQL Server virtuális gépét. 
1. Válassza **a Replikáció letiltása**lehetőséget. Válassza ki a védelem letiltásának okát, majd a replikáció letiltásához kattintson **az OK gombra.** 

   >[!IMPORTANT]
   > Fontos, hogy hajtsa végre ezt a lépést, hogy elkerülje az Azure Site Recovery replikációjának díját. 

1. Ha nem tervezi a forrásrégió egyetlen erőforrásának újrafelhasználását, törölje az összes vonatkozó hálózati erőforrást és a megfelelő tárfiókokat. 


## <a name="next-steps"></a>További lépések

További információkért tekintse át a következő cikkeket: 

* [Az SQL Server áttekintése Windows virtuális gépen](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server windows vm rendszeren – gyakori kérdések](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server windowsos virtuális gépek díjszabási útmutatójában](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server windowsos virtuális gép kiadási megjegyzésekén](virtual-machines-windows-sql-server-iaas-release-notes.md)


