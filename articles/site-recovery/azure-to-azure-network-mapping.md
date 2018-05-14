---
title: Virtuális hálózatok közötti két Azure-régiók az Azure Site Recovery leképezése |} Microsoft Docs
description: Az Azure Site Recovery koordinálja a replikáció, feladatátvétel és helyreállítási virtuális gépek és fizikai kiszolgálók. További információk a feladatátvétel az Azure-bA vagy másodlagos adatközpontba.
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
editor: ''
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: 9294940785deb0834a419de8320286783635d68e
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="map-virtual-networks-in-different-azure-regions"></a>Virtuális hálózatok a különböző Azure-régiók leképezése


A cikkből megtudhatja, hogyan képezheti Azure Virtual Network különböző Azure-régiók egymás mellett található két példánya. A hálózatleképezés biztosítja, hogy a replikált virtuális gépek létrehozásakor a cél Azure-régió, a virtuális gép is létrejön a virtuális hálózaton, amelyek a forrás virtuális gép virtuális hálózathoz van rendelve.  

## <a name="prerequisites"></a>Előfeltételek
Hálózatok megfeleltetéséhez előtt győződjön meg arról, hogy létrehozott egy [Azure-beli virtuális hálózat](../virtual-network/virtual-networks-overview.md) a forrás-régió, mind a cél Azure-régiót.

## <a name="map-virtual-networks"></a>Virtuális hálózatok leképezése

Egy Azure virtuális hálózatot, amely egy Azure-régiót (Forráshálózat), amely az Azure virtuális gépek (célhálózat), egy másik régióban található, virtuális hálózathoz található leképezni, keresse fel **Site Recovery-infrastruktúra**  >  **Leképezési hálózati**. Hozzon létre egy hálózatra való leképezés.

![Hozzárendelések ablak hálózati – a hálózatra való leképezés létrehozása](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)


A következő példában a virtuális gép fut a Kelet-Ázsia régióban. A virtuális gép a Délkelet-Ázsia régió replikálódnak.

A Kelet-Ázsia régió Délkelet-Ázsia régió egy hálózatra való leképezés létrehozásához válassza ki a forrás hálózati helye és a cél hálózati helyét. Ezután válassza az **OK** lehetőséget.

![Adja hozzá a hálózat leképezési ablakban - forrása és célja a forrás hálózati helyek kiválasztása](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)


Ismételje meg a fenti folyamatot létrehozni a hálózati hozzárendelést Délkelet-Ázsia régióban, Kelet-Ázsia régió.

![Adja hozzá a hálózati leképezési ablaktábla - forrás- és a cél hálózati helyek kiválasztása](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-a-network-when-you-enable-replication"></a>A hálózati hozzárendelését, amikor a replikáció engedélyezése

A replikált virtuális gép egy Azure-régióban egy másik régióban először, ha nincs hálózati leképezés, amikor a replikáció beállítása a cél hálózati állíthatja be. Ez a beállítás alapján, Azure Site Recovery hoz létre hálózatok leképezését a forrás-régióban, a cél régióban, és a cél-régióban, a forrás régióban.   

![A beállítások ablaktábla konfigurálása - a célhelye](./media/site-recovery-network-mapping-azure-to-azure/network-mapping4.png)

Alapértelmezés szerint a Site Recovery egy hálózatot, amely azonos a forrás hálózati cél régióban hoz létre. A Site Recovery létrehoz egy hálózati hozzáadásával **-asr** utótagként a forrás hálózati nevét. Már létezik a hálózat kiválasztásához jelölje ki a **Testreszabás**.

![Testre szabhatja a cél beállításai ablaktábla - készlet célként megadott erőforráscsoport-név és cél virtuális hálózat neve](./media/site-recovery-network-mapping-azure-to-azure/network-mapping5.png)

Ha hálózati leképezése már megtörtént, a cél virtuális hálózati replikációs engedélyezésekor nem módosíthatja. Ebben az esetben a cél virtuális hálózat megváltoztatásához módosítsa a meglévő hálózatleképezést.  

![Testre szabhatja a cél beállításai ablaktábla - állítsa be a célként megadott erőforráscsoport-név](./media/site-recovery-network-mapping-azure-to-azure/network-mapping6.png)

![Hálózati leképezési ablaktábla módosítása – egy olyan meglévő cél virtuális hálózat nevének módosítása](./media/site-recovery-network-mapping-azure-to-azure/modify-network-mapping.png)

> [!IMPORTANT]
> Ha módosít egy hálózatleképezés terület A B régióban, győződjön meg arról, a hálózatleképezés régió B régióban A. módosítása
>
>


## <a name="subnet-selection"></a>Alhálózat kiválasztása
A cél virtuális gép alhálózata a forrás virtuális gép az alhálózat neve alapján van kiválasztva. Olyan alhálózatot, amely ugyanaz a neve, mint a forrás virtuális gép a célhálózat érhető el, ha a cél virtuális gép alhálózaton van beállítva. Ha egy ilyen nevű alhálózat nem létezik a célhálózat, a betűrendben az első alhálózat értéke a cél alhálózathoz.

Az alhálózat módosításához keresse fel a **számítás és hálózat** a virtuális gép beállításait.

![A számítási műveletek és hálózati számítási tulajdonságok ablak](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="ip-address"></a>IP-cím

Az alábbi szakaszok ismertetik az a cél virtuális gép egyes hálózati adapter IP-cím van megadva.

### <a name="dhcp"></a>DHCP
A hálózati illesztő a forrás virtuális gép DHCP használja, ha a cél virtuális gép hálózati kapcsolat is van beállítva a DHCP használatára.

### <a name="static-ip-address"></a>Statikus IP-cím
Ha a forrás virtuális gép hálózati kapcsolat egy statikus IP-címet használ, a hálózati illesztő a cél virtuális gép is használatára van beállítva a statikus IP-címet. A következő szakaszok ismertetik, hogyan egy statikus IP-cím van beállítva.

#### <a name="same-address-space"></a>Ugyanazt a címtartományt

Ha a forrás alhálózat és a cél alhálózathoz ugyanazt a címtartományt, a forrás virtuális gép hálózati illesztő IP-címe van állítja be a cél IP-címet. Ha az azonos IP-cím nem érhető el, a következő elérhető IP-cím értéke a cél IP-címet.

#### <a name="different-address-spaces"></a>Különböző címterek

Ha a forrás alhálózat és a cél alhálózathoz különböző címterek, a következő elérhető IP-címet a cél alhálózathoz értéke a cél IP-címet.

A cél IP-címet meg mindegyik hálózati interfész módosításához keresse fel a **számítás és hálózat** a virtuális gép beállításait.

## <a name="next-steps"></a>További lépések

* Felülvizsgálati [útmutató az Azure virtuális gépek replikálása a hálózat](site-recovery-azure-to-azure-networking-guidance.md).
