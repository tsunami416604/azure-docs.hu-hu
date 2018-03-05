---
title: "IP-címzés beállítása az Azure-bA az Azure Site Recovery feladatátvételt követően csatlakozhatnak |} Microsoft Docs"
description: "Ismerteti, hogyan lehet IP-címzési való csatlakozáshoz Azure virtuális gépek a feladatátvételt követően az Azure Site Recovery helyszíni beállítása"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: carmonm
editor: 
ms.assetid: f02cdbea-0940-48bf-9fa5-f38d9e584fae
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: b9aeaf1dc6d471ba993dd470403ba60ce68153fc
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="set-up-ip-addressing-to-connect-after-failover-to-azure"></a>Az IP-címzést, az Azure-bA a feladatátvételt követően kapcsolódni

Ez a cikk ismerteti a hálózati követelmények használata után Azure virtuális gépekhez való csatlakozás a [Azure Site Recovery](site-recovery-overview.md) replikációs és feladatátvételi Azure szolgáltatást.

Ebben a cikkben megtanulhatja:

> [!div class="checklist"]
> * A csatlakozási módszer használható
> * Egy másik IP-cím használata Azure replikált virtuális gépekhez
> * Hogyan szeretné megőrizni az IP-címek Azure virtuális gépek a feladatátvételt követően

## <a name="connecting-to-replica-vms"></a>A replika virtuális gépek csatlakoztatása

A replikációs és feladatátvételi stratégiát tervezésekor az alábbiakhoz hasonló fontos kérdések egyike csatlakoztatása az Azure virtuális gép a feladatátvételt követően. Számos több lehetősége a replika Azure virtuális gépek hálózati stratégia tervezésekor.

- **Különböző IP-cím**: egy másik IP-címtartományt használja a replikált Azure Virtuálisgép-hálózat állítható be. Ebben a forgatókönyvben a virtuális Gépet egy új IP-cím beolvasása feladatátvétel után, és egy DNS-frissítés szükséges.
- **Tartsa meg az IP-cím**: Előfordulhat, hogy szeretné használják az azonos IP-címtartományt, amely a helyszíni elsődleges hely, a feladatátvételt követően az Azure-hálózatot. Megőrzi az azonos IP-cím címek egyszerűbbé teszi a helyreállítást csökkentésével hálózattal kapcsolatos problémákat a feladatátvételt követően. Azonban ha az Azure-bA replikál, szüksége lesz az útvonalak frissítése az új hellyel a IP-címek a feladatátvételt követően.

## <a name="retaining-ip-addresses"></a>IP-címek megőrzése

A Site Recovery biztosít a funkció megőrzését rögzített IP oldja meg, amikor az Azure-ba, az alhálózati feladatátvevő feladatátvétele.

- Alhálózati feladatátvétellel egy bizonyos alhálózat jelen webhely 1 vagy 2. hely, de soha nem mindkét helyen egyszerre.
- Ahhoz, hogy a feladatátvétel esetén az IP-címtér kezelése, programozott módon el rendezése az útválasztó-infrastruktúra az alhálózatok áthelyezése egyik helyről egy másikra.
- A feladatátvételi az alhálózatok a társított védett virtuális gépek áthelyezése. A fő hátránya, hogy hiba esetén, hogy a teljes alhálózat áthelyezése.


### <a name="failover-example"></a>Feladatátvétel – példa

Nézzük Azure usng ficticious vállalat, a Woodgrove Bank feladatátvételi példa.

- A Woodgrove Bank, hogy az üzleti alkalmazások egy helyszíni hely üzemelteti. Azok a gazdagép a mobile apps szolgáltatásban az Azure-on.
- Nincs VPN webhelyek kapcsolat a helyszíni peremhálózati hálózat és az Azure virtuális hálózat között. A VPN-kapcsolat, mert a virtuális hálózat az Azure-ban jelenik meg a helyszíni hálózat kiterjesztése.
- Woodgrove szeretné replikálni a helyszíni munkaterhelések az Azure Site Recovery szolgáltatással.
 - Woodgrove kódolt IP-címek, így azok meg kell őriznie az alkalmazások számára az IP-címek az Azure-bA a feladatátvételt követően függő alkalmazások rendelkezik.
 - Azure-beli erőforrást használja az IP cím tartomány 172.16.1.0/24 172.16.2.0/24.

![Alhálózati feladatátvétel előtt](./media/site-recovery-network-design/network-design7.png)

**Feladatátvétel előtt infrastruktúra**


A Woodgrove tenni a virtuális gépek replikálása az Azure-ba, megtartja az IP-címek, itt meg Mi az a vállalati kell tennie:


1. Hozzon létre, amelyben az Azure virtuális gépek létrehozása után a feladatátvétel a helyszíni gépeket az Azure virtuális hálózatot. A helyszíni hálózat kiterjesztése kell, hogy az alkalmazások zökkenőmentesen feladatátvétel.
2. A Site Recovery szolgáltatásban, a feladatátvétel előtt azok hozzárendelése a azonos IP-címet a virtuálisgép-tulajdonságokat. A feladatátvétel után a Site Recovery rendel hozzá ezt a címet az Azure virtuális Géphez.
3. Miután feladatátvételi fut, és az Azure virtuális gépek jönnek létre a azonos IP-címmel, csatlakoznak-e a hálózat használja egy [kapcsolatcsoporttal Vnet](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). Ez a művelet parancsfájlalapú lehet.
4. Módosítsa az útvonalak, hogy tükrözze a 192.168.1.0/24 most áthelyezte az Azure van szükségük.


**A feladatátvételt követően infrastruktúra**

![Alhálózati feladatátvételt követően](./media/site-recovery-network-design/network-design9.png)

#### <a name="site-to-site-connection"></a>Hely-hely kapcsolat

A vnet – vnet-kapcsolatot, a feladatátvétel után mellett Woodgrove pont-pont VPN-kapcsolat adható meg:
- A pont-pont kapcsolat beállításához, az Azure-hálózatot csak irányíthatja forgalom a helyszíni helyre (helyi-ntwork) Ha az IP-címtartomány eltér a helyi IP-címtartomány. Ennek az az oka Azure felhőbe archivált alhálózatok nem támogatja. Ezért, ha alhálózati 192.168.1.0/24 helyi, nem egy helyi hálózati 192.168.1.0/24 az Azure-hálózat. Ez elvárható, hiszen az Azure nem tudja, hogy nincs aktív virtuális gépek nincsenek-e az alhálózatot, és csak a vész-helyreállítási létre az alhálózatot.
- Nem fogja tudni megfelelően irányítható a hálózati forgalom kívül az Azure-hálózatot, hogy a hálózat és a helyi hálózat alhálózatai nem ütköznek.




## <a name="assigning-new-ip-addresses"></a>Új IP-címek hozzárendelése

Ez [blogbejegyzés](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) ismerteti, hogyan állíthatja be az Azure hálózati infrastruktúra, ha már nincs szükség IP-címek megőrizni a feladatátvételt követően. Az kezdődik-e az alkalmazás leírása, hogyan állíthatja be a helyszíni hálózat és az Azure-ban megvizsgálja, és folyamat végén a feladatátvételeket a futtatásával kapcsolatos információkat.

## <a name="next-steps"></a>További lépések
[Feladatátvétel futtatása](site-recovery-failover.md)
