---
title: "A Hyper-V (a System Center VMM nélkül) az Azure Site Recovery Azure replikáció hálózati terv |} Microsoft Docs"
description: "Ez a cikk ismerteti az alaphálózati topológia tervezése szükséges, ha (VMM nélkül) a Hyper-V virtuális gépek replikálása az Azure-bA"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5ca12254-975d-48e8-a84d-422825dac9b2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.openlocfilehash: 100b9d8a55c2c163e7a04680f0f7d7963315ee73
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="step-4-plan-networking-for-hyper-v-to-azure-replication"></a>4. lépés: Hyper-v hálózatkezelési Azure replikáció tervezése

Ez a cikk hálózati tervezési szempontok, ha replikálása a helyszíni Hyper-V virtuális gépek (a System Center VMM nélkül) az Azure használatát foglalja össze a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás.

Megjegyzéseket a cikk alján tehet, kérdéseket pedig az [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) teheti fel.


## <a name="connecting-to-replica-vms-after-failover"></a>Csatlakozás a replika virtuális gépek a feladatátvételt követően

A replikációs és feladatátvételi stratégiát tervezésekor az alábbiakhoz hasonló fontos kérdések egyike csatlakoztatása az Azure virtuális gép a feladatátvételt követően. Számos több lehetősége a replika Azure virtuális gépek hálózati stratégia tervezésekor.

- **Másik IP-cím**: egy másik IP-címtartományt használja a replikált Azure Virtuálisgép-hálózat állítható be. Ebben a forgatókönyvben a virtuális Gépet egy új IP-cím beolvasása feladatátvétel után, és egy DNS-frissítés szükséges. [További információ](site-recovery-test-failover-vmm-to-vmm.md#prepare-the-infrastructure-for-test-failover)
- **IP-cím**: Előfordulhat, hogy szeretné használják az azonos IP-címtartományt, amely a helyszíni elsődleges hálózat, a feladatátvételt követően az Azure-hálózatot.  Megőrzi az azonos IP-cím címek egyszerűbbé teszi a helyreállítást csökkentésével hálózattal kapcsolatos problémákat a feladatátvételt követően. Azonban ha az Azure-bA replikál, szüksége lesz az útvonalak frissítése az új hellyel a IP-címek a feladatátvételt követően.


## <a name="retain-ip-addresses"></a>Tartsa meg az IP-címek

A Site Recovery biztosít a funkció megőrzését rögzített IP oldja meg, amikor az Azure-ba, az alhálózati feladatátvevő feladatátvétele.

Alhálózati feladatátvétellel egy bizonyos alhálózat jelen webhely 1 vagy 2. hely, de soha nem mindkét helyen egyszerre. Ahhoz, hogy a feladatátvétel esetén az IP-címtér kezelése, programozott módon el rendezése az útválasztó-infrastruktúra az alhálózatok áthelyezése egyik helyről egy másikra. A feladatátvételi az alhálózatok a társított védett virtuális gépek áthelyezése. A fő hátránya, hogy hiba esetén, hogy a teljes alhálózat áthelyezése.


### <a name="failover-example"></a>Feladatátvétel – példa

Nézzük például feladatátvétel az Azure-bA.

- Egy ficticious vállalati, a Woodgrove Bank üzemeltető üzleti alkalmazások helyszíni infrastruktúra van. A mobilalkalmazás Azure üzemelteti.
- A helyszíni peremhálózati hálózat és az Azure virtuális hálózat közötti pont-pont (VPN) kapcsolatot biztosít a Woodgrove Bank virtuális gépeket az Azure és a helyszíni kiszolgálók közötti kapcsolatot.
- A VPN-t, az azt jelenti, hogy a vállalat virtuális hálózat az Azure-ban jelenik meg a helyszíni hálózat kiterjesztése.
- Woodgrove szeretné replikálni a helyszíni munkaterhelések az Azure Site Recovery segítségével.
 - Woodgrove rendelkezik az alkalmazások és konfigurációk, amely IP-címek kódolt függ, és így meg kell őriznie az IP-címet az alkalmazások az Azure-bA a feladatátvételt követően kezelésére.
 - Woodgrove tartomány 172.16.1.0/24, Azure-beli erőforrásaihoz 172.16.2.0/24 rendelkezik hozzárendelt IP-címeket.


A Woodgrove tenni a virtuális gépek replikálása az Azure-ba, megtartja az IP-címek, itt meg Mi az a vállalati kell tennie:

1. Hozzon létre egy Azure virtuális hálózatra. A helyszíni hálózat kiterjesztése kell, hogy az alkalmazások zökkenőmentesen feladatátvétel.
2. Azure-helyek VPN-kapcsolat, az Azure-ban létrehozott virtuális hálózatok pont – hely kapcsolat mellett hozzáadását teszi lehetővé.
3. A pont-pont kapcsolat az Azure-hálózat beállítása során Ön irányíthatja a forgalmat a helyszíni helyre (helyi hálózati) csak akkor, ha az IP-címtartomány eltér a helyi IP-címtartományt.
    - Ennek az az oka Azure felhőbe archivált alhálózatok nem támogatja. Ezért ha alhálózati 192.168.1.0/24 helyi, nem adhat hozzá egy helyi hálózati 192.168.1.0/24 az Azure-hálózat.
    - Ez elvárható, hiszen az Azure nem tudja, hogy nincs aktív virtuális gépek nincsenek-e az alhálózatot, és csak a vész-helyreállítási létre az alhálózatot.
    - A fogja tudni megfelelően irányítható a hálózati forgalom kívül az Azure-hálózatot a hálózati és a helyi hálózat alhálózatai nem ütköznek-e.

![Alhálózati feladatátvétel előtt](./media/hyper-v-site-walkthrough-network/network-design7.png)

### <a name="before-failover"></a>Feladatátvétel előtt

1. Hozzon létre egy további hálózati (például a helyreállítási hálózati). Ez az a hálózaton található, amely átadja a virtuális gépek jönnek létre.
2. Győződjön meg arról, hogy az IP-címet a virtuális gépek a feladatátvétel után a virtuális gép tulajdonságai őrzi meg a > **konfigurálása**, adja meg, hogy a virtuális gép rendelkezik-e a helyszíni ugyanazt a címet, és kattintson a **mentése**.
3. A virtuális gép feladatátvételt, ha az Azure Site Recovery rendeli hozzá a megadott IP-cím.

    ![Hálózat tulajdonságai](./media/hyper-v-site-walkthrough-network/network-design8.png)

4. Miután a feladatátvétel eseményindító aktiválódik, és a virtuális gépeket az Azure-hoz létre a szükséges IP-címmel, csatlakozhat a hálózati használatával egy [Vnet hálózatok vonnection](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). Ez a művelet parancsfájlalapú lehet.
5. Útvonalak megfelelően módosítani kell, hogy tükrözze a 192.168.1.0/24 most áthelyezte az Azure kell.

    ![Alhálózati feladatátvételt követően](./media/hyper-v-site-walkthrough-network/network-design9.png)

### <a name="after-failover"></a>A feladatátvételt követően

Ha a fenti módon még nem rendelkezik Azure-hálózat, létrehozhat egy-helyek közti VPN-kapcsolat az elsődleges hely és az Azure, failvoer után.

## <a name="change-ip-addresses"></a>IP-címek módosítása

Ez [blogbejegyzés](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) ismerteti, hogyan állíthatja be az Azure hálózati infrastruktúra, ha már nincs szükség IP-címek megőrizni a feladatátvételt követően. Az kezdődik-e az alkalmazás leírása, hogyan állíthatja be a helyszíni hálózat és az Azure-ban megvizsgálja, és folyamat végén a feladatátvételeket a futtatásával kapcsolatos információkat.  

## <a name="next-steps"></a>Következő lépések

Ugrás a [5. lépés: Azure előkészítése](hyper-v-site-walkthrough-prepare-azure.md)
