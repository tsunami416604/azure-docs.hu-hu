---
title: "Az Azure Site Recovery két Azure-régiók közötti hálózatleképezés |} Microsoft Docs"
description: "Az Azure Site Recovery koordinálja a replikálása, feladatátvétele és helyreállítási virtuális gépek és fizikai kiszolgálók. További információk a feladatátvétel az Azure vagy egy másodlagos adatközpontba."
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/11/2017
ms.author: pratshar
ms.openlocfilehash: 9d6a806ec533259797080fbfee2c38f918ebd8a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="network-mapping-between-two-azure-regions"></a>Két Azure-régiók közötti hálózatleképezés


A cikkből megtudhatja, hogyan képezheti két Azure-régiók egymás mellett az Azure virtuális hálózatot. Hálózatleképezés biztosítja, hogy a replikált virtuális gép létrehozásakor a cél Azure-régiót jön létre, a virtuális hálózat, amely a forrás virtuális gép virtuális hálózathoz van rendelve.  

## <a name="prerequisites"></a>Előfeltételek
Mielőtt leképez hálózatok mindenképpen, létrehozott [Azure virtuális hálózataihoz](../virtual-network/virtual-networks-overview.md) mindkét forrás és cél Azure-régiók.

## <a name="map-networks"></a>Hálózatok leképezése

Az Azure-régió, egy Azure virtuális hálózat hozzárendelése egy másik virtuális hálózatnak egy másik régióban, lépjen a Site Recovery-infrastruktúra -> Hálózatleképezés (az Azure virtuális gépek) és a hálózatra való leképezés létrehozásához.

![Hálózatleképezés](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)


Az alábbi példában a virtuális gép Kelet-Ázsia régióban fut, és Délkelet-Ázsia replikálódnak.

Válassza ki a forrás és cél hálózati, és kattintson az OK gombra a hálózatra való leképezés készíteni Kelet-Ázsia Délkelet-Ázsia.

![Hálózatleképezés](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)


Hajtsa végre ugyanezt a hálózatra való leképezés létrehozása Délkelet-Ázsia Kelet-Ázsia.  
![Hálózatleképezés](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="mapping-network-when-enabling-replication"></a>Ha engedélyezve van a replikáció hálózati

Ha hálózatra való leképezés nem történik meg, amikor első alkalommal űrlap egy Azure-régiót virtuális gép replikál egy másikra, akkor választhatja célhálózat ugyanazt az folyamat részeként. A Site Recovery forrás régió cél régióhoz és a cél régió forrás régiónak a kijelölés alapján hoz létre a hálózatok leképezését.   

![Hálózatleképezés](./media/site-recovery-network-mapping-azure-to-azure/network-mapping4.png)

Alapértelmezés szerint a Site Recovery egy hálózatot hoz létre, amely azonos a forrás-hálózathoz, és adja hozzá a cél régióban "-asr" utótagként a forrás hálózati nevét. Testreszabás kattintva választhat egy már létrehozott hálózati.

![Hálózatleképezés](./media/site-recovery-network-mapping-azure-to-azure/network-mapping5.png)


Ha a hálózatra való leképezés már megtörtént, a cél virtuális hálózati replikáció során nem módosíthatja. Az megváltoztatásához módosítsa a meglévő hálózatleképezést.  

![Hálózatleképezés](./media/site-recovery-network-mapping-azure-to-azure/network-mapping6.png)

![Hálózatleképezés](./media/site-recovery-network-mapping-azure-to-azure/modify-network-mapping.png)

> [!IMPORTANT]
> Ha módosít egy hálózatra való leképezést a terület-1 régió-2, ellenőrizze, hogy a hálózat leképezését régió-2 régió-1, valamint módosítja.
>
>


## <a name="subnet-selection"></a>Alhálózat kiválasztása
A cél virtuális gép alhálózata a forrás virtuális gép az alhálózat neve alapján van kiválasztva. Ha nincs a célhálózat elérhető legyen, mint a forrás virtuális gép azonos nevű alhálózatot, majd, amely választja a cél virtuális gép. Ha nem található a cél hálózati azonos nevű alhálózat majd betűrendben első alhálózat választja a cél alhálózathoz. Ez az alhálózat a számítás és hálózat a virtuális gép beállításai között módosíthatja.

![Alhálózat módosítása](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="ip-address"></a>IP-cím

Az egyes a cél virtuális gép hálózati illesztő IP-cím van kiválasztva az alábbiak szerint:

### <a name="dhcp"></a>DHCP
Ha a forrás virtuális gép hálózati illesztő nem használja a DHCP, majd a cél virtuális gép hálózati adapter is értéke DHCP.

### <a name="static-ip"></a>Statikus IP-cím
Ha a forrás virtuális gép hálózati illesztő statikus IP-címet használ, akkor a cél virtuális gép hálózati adapter is értéke statikus IP-címet használ. Statikus IP-cím van kiválasztva az alábbiak szerint:

#### <a name="same-address-space"></a>Ugyanazt a címtartományt

Ha a forrás alhálózat és a cél alhálózathoz ugyanazt a címtartományt, akkor cél IP-címet értéke ugyanaz, mint az IP-címe a forrás virtuális gép hálózati illesztőt. Ha ugyanazon IP-cím nem érhető el, majd néhány más elérhető IP értéke a cél IP-címet.

#### <a name="different-address-space"></a>Különböző címterület

Ha a forrás alhálózat és a cél alhálózathoz eltérő, cél IP-címet állítja van bármilyen elérhető IP-cél alhálózaton.

A virtuális gép számítási és hálózati beállításainak megnyitásával módosíthatja a mindegyik hálózati interfész cél IP-címet.

## <a name="next-steps"></a>Következő lépések

- További tudnivalók [hálózati útmutatót az Azure virtuális gépek replikálása](site-recovery-azure-to-azure-networking-guidance.md).
