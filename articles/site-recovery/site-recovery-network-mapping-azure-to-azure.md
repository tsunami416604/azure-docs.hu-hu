---
title: "Az Azure Site Recovery két Azure-régiók közötti hálózatleképezés |} Microsoft Docs"
description: "Az Azure Site Recovery koordinálja a replikálása, feladatátvétele és helyreállítási virtuális gépek és fizikai kiszolgálók. További információk a feladatátvétel az Azure vagy egy másodlagos adatközpontba."
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/15/2017
ms.author: manayar
ms.openlocfilehash: bf3d557c77e3cb6ade6f1bb3773c807f9c8b43f6
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2017
---
# <a name="network-mapping-between-two-azure-regions"></a>Két Azure-régiók közötti hálózatleképezés


A cikkből megtudhatja, hogyan képezheti két Azure-régiók egymás mellett az Azure virtuális hálózatot. Hálózatleképezés biztosítja, hogy a replikált virtuális gépek létrehozásakor a cél Azure-régiót jön létre, a virtuális hálózat, amely a forrás virtuális gép virtuális hálózathoz van rendelve.  

## <a name="prerequisites"></a>Előfeltételek
Hálózatok megfeleltetéséhez előtt győződjön meg arról, hogy létrehozta [Azure virtuális hálózataihoz](../virtual-network/virtual-networks-overview.md) mindkét forrás és cél Azure-régiók.

## <a name="map-networks"></a>Hálózatok leképezése

Az Azure-régió, egy Azure virtuális hálózat hozzárendelése egy másik virtuális hálózatnak egy másik régióban, lépjen a Site Recovery-infrastruktúra -> Hálózatleképezés (az Azure virtuális gépek) és a hálózatra való leképezés létrehozásához.

![Hálózatleképezés](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)


Az alábbi példában a virtuális gép Kelet-Ázsia régióban fut, és Délkelet-Ázsia replikálódnak.

Válassza ki a forrás és cél hálózati, és kattintson az OK gombra a hálózatra való leképezés készíteni Kelet-Ázsia Délkelet-Ázsia.

![Hálózatleképezés](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)


Ismételje meg a fenti folyamatot, Délkelet-Ázsia a hálózatra való leképezés létrehozása Kelet-Ázsia.

![Hálózatleképezés](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="mapping-network-when-enabling-replication"></a>Ha engedélyezve van a replikáció hálózati

Amikor replikál egy virtuális gép először az Azure-régió, egy másik hálózatra való leképezés nem lett végrehajtva, ha ugyanazt az folyamat részeként a cél hálózati választhatja meg. A Site Recovery forrás régió cél régióhoz és a cél régió forrás régiónak a kijelölés alapján hoz létre a hálózatok leképezését.   

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
A cél virtuális gép alhálózata a forrás virtuális gép az alhálózat neve alapján van kiválasztva. Ha a cél hálózati legyen, mint a forrás virtuális gép azonos nevű alhálózat érhető el, majd alhálózaton a cél virtuális gép van kiválasztva. Ha nem található a cél hálózati azonos nevű alhálózat, majd betűrendben az első alhálózat választja a cél alhálózathoz. Ez az alhálózat a számítás és hálózat a virtuális gép beállításai között módosíthatja.

![Alhálózat módosítása](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="ip-address"></a>IP-cím

A cél virtuális gép egyes hálózati adapter IP-cím van kiválasztva az alábbiak szerint:

### <a name="dhcp"></a>DHCP
Ha a forrás virtuális gép hálózati illesztő nem használja a DHCP, majd a cél virtuális gép hálózati kapcsolat is értéke DHCP.

### <a name="static-ip"></a>Statikus IP-cím
Ha a forrás virtuális gép hálózati illesztő statikus IP-címet használ, akkor a cél virtuális gép hálózati kapcsolat is értéke statikus IP-címet használ. Statikus IP-cím van kiválasztva az alábbiak szerint:

#### <a name="same-address-space"></a>Ugyanazt a címtartományt

Ha a forrás alhálózat és a cél alhálózathoz ugyanazt a címtartományt, majd a forrás virtuális gép hálózati illesztő IP-címe van állítja be a cél IP-címet. Ha az azonos IP-cím nem érhető el, majd a következő elérhető IP-cím értéke a cél IP-címet.

#### <a name="different-address-space"></a>Különböző címterület

Ha a forrás alhálózat és a cél alhálózathoz különböző címterek, majd a következő elérhető IP-címet a cél alhálózathoz értéke a cél IP-címet.

A virtuális gép számítási és hálózati beállításainak megnyitásával módosíthatja a mindegyik hálózati interfész cél IP-címet.

## <a name="next-steps"></a>Következő lépések

További tudnivalók [hálózati útmutatót az Azure virtuális gépek replikálása](site-recovery-azure-to-azure-networking-guidance.md).
