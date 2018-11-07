---
title: Az IP-címkezelést kapcsolódás után a vész-helyreállítási és az Azure-bA az Azure Site Recovery feladatátvételi |} A Microsoft Docs
description: Ismerteti, hogyan állíthatja be az IP-címek az Azure virtuális géphez való kapcsolódásra vész-helyreállítási és a feladatátvétel után a helyszínről az Azure Site Recoveryvel
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: mayg
ms.openlocfilehash: c05a97dc5a5fe9a0e8a6f1a8598ff82fcf488ab2
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51253278"
---
# <a name="set-up-ip-addressing-to-connect-to-azure-vms-after-failover"></a>Az IP-címkezelés Azure virtuális géphez való kapcsolódásra a feladatátvételt követően

Ez a cikk ismerteti a hálózati követelmények használata után az Azure virtuális gépekhez való csatlakozáshoz a [Azure Site Recovery](site-recovery-overview.md) replikáció és feladatátvétel az Azure szolgáltatásba.

Ez a cikk azt ismerteti:

> [!div class="checklist"]
> * A kapcsolódási módszert használhat
> * A másik IP-cím használata Azure virtuális gépek replikálása
> * IP-cím megőrzése Azure-beli virtuális gépek a feladatátvételt követően

## <a name="connecting-to-replica-vms"></a>Replika virtuális gépek csatlakoztatása

A replikációs és feladatátvételi stratégiát tervezésekor a legfontosabb kérdések egyik, hogyan csatlakozhat az Azure virtuális gép a feladatátvételt követően. A replika Azure virtuális gépek hálózati stratégia tervezésekor, van több lehetősége:

- **Használjon eltérő IP-címet**: választhat egy másik IP-címtartományt használjon replikált Azure Virtuálisgép-hálózathoz. Ebben a forgatókönyvben a virtuális gép új IP-címet lekéri a feladatátvételt követően, és a egy DNS-frissítés szükség.
- **Azonos IP-cím megőrzése**: Előfordulhat, hogy használni kívánt IP-címtartományból, mint az elsődleges helyszíni hely, a feladatátvétel után az Azure-hálózatot. A azonos IP-cím megőrzése címek egyszerűbbé teszi a helyreállítás csökkentésével hálózattal kapcsolatos problémákat a feladatátvételt követően. Azonban ha az Azure-bA replikál, szüksége lesz az útvonalak frissítése az IP-címet az új helyre a feladatátvételt követően.

## <a name="retaining-ip-addresses"></a>IP-cím megőrzése

A Site recoveryben teszi, hogy a fix IP-cím megőrzése oldja meg, amikor az Azure-bA egy alhálózat-feladatátvétellel feladatátvétele.

- Alhálózat feladatátvétel esetén a megadott alhálózat jelen legyen hely 1 vagy 2. hely, de soha nem mindkét helyen egyszerre.
- Annak érdekében, hogy feladatátvétel esetén az IP-címtér kezelése, programozott módon elrendezte az útválasztó-infrastruktúra az alhálózatok áthelyezheti egyik helyről egy másikra.
- A feladatátvétel során az alhálózatok és a kapcsolódó védett virtuális gépek áthelyezése. A fő hátránya, hogy hiba esetén, hogy a teljes alhálózat áthelyezése.


### <a name="failover-example"></a>Feladatátvételi példa

Lássunk erre egy példát feladatátvételi az Azure-ban egy fiktív vállalat, a Woodgrove Bank.

- A Woodgrove Bank futtat egy helyszíni hely üzleti alkalmazások. Az Azure-ban a mobilalkalmazások üzemeltetheti azokat.
- Nincs VPN hely – hely kapcsolat a helyszíni peremhálózati hálózat és az Azure virtuális hálózat között. A VPN-kapcsolat miatt a virtuális hálózat az Azure-ban a helyszíni hálózat kiterjesztése jelenik meg.
- A Woodgrove szeretné az Azure-bA a Site Recovery a helyszíni számítási feladatok replikálására.
 - A Woodgrove rendelkezik változtatható IP-címek, így azok kell megőrizni az alkalmazásokat, IP-címek az Azure-bA a feladatátvételt követően függő alkalmazásokat.
 - Az Azure-ban futó erőforrásokat használja az IP cím tartományt 172.16.1.0/24 172.16.2.0/24.

![Alhálózat a feladatátvétel előtt](./media/site-recovery-network-design/network-design7.png)

**A feladatátvétel előtt infrastruktúra**


A Woodgrove lehessen a virtuális gépek replikálásához az Azure-ban milyen IP-címeket, itt a megőrzése a vállalat kell tennie:


1. Hozzon létre, amelyben az Azure virtuális gépek létrehozása után feladatátvétele a helyszíni gépek az Azure virtual network. A helyszíni hálózat kiterjesztése, hogy az alkalmazások átveheti zökkenőmentesen kell.
2. A Site Recovery a feladatátvétel előtt hozzá az azonos IP-cím a gép tulajdonságai. A feladatátvételt követően a Site Recovery hozzárendeli ezt a címet az Azure virtuális Gépen.
3. Miután feladatátvételi fut, és az Azure virtuális gépek jönnek létre ugyanazon az IP-címmel, csatlakoznak-e a hálózaton történő egy [virtuális hálózatok közötti kapcsolat](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). Ez a művelet parancsfájlalapú lehet.
4. Akkor kell módosítania az útvonalakat, hogy tükrözze a 192.168.1.0/24 most már át lett helyezve az Azure-bA.


**A feladatátvételt követően infrastruktúra**

![Alhálózat feladatátvétel után](./media/site-recovery-network-design/network-design9.png)

#### <a name="site-to-site-connection"></a>Helyek közötti kapcsolat

Site-to-site VPN-kapcsolat mellett a vnet – vnet kapcsolat, a feladatátvételt követően a Woodgrove adható meg:
- Egy helyek közötti kapcsolat beállításakor az Azure-hálózatot csak irányíthatja a forgalmat a helyszíni helyre (helyi-hálózatot) Ha az IP-címtartomány eltér a helyi IP-címtartományt. Ennek az oka az Azure nem támogatja a többhelyes alhálózatokat. Így ha alhálózati 192.168.1.0/24 helyi, nem egy helyi hálózati 192.168.1.0/24 adhat hozzá a az Azure-hálózatot. Ez elvárható, hiszen az Azure nem ismert, hogy nincsenek-e nincs aktív virtuális gépeket az alhálózaton, és csak a vész-helyreállítási létre az alhálózatot.
- Az, hogy megfelelően irányítani az Azure-hálózat hálózati forgalom, a hálózat és a helyi hálózat alhálózatai nem ütköznek.




## <a name="assigning-new-ip-addresses"></a>Új IP-címek hozzárendelése

Ez [blogbejegyzés](https://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) azt ismerteti, hogyan állítható be az Azure hálózati infrastruktúra, ha már nincs szükség IP-cím megőrzése feladatátvétel után. Azt az alkalmazás leírásának kezdődik, módját beállítása a helyszíni hálózat és az Azure-ban, és azt állapítja meg, a feladatátvétel futtatásával kapcsolatos információkat.

## <a name="next-steps"></a>További lépések
[Feladatátvétel futtatása](site-recovery-failover.md)
