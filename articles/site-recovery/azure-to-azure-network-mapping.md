---
title: Az Azure Site Recoveryben két Azure-régiók közötti virtuális hálózatok leképezése |} A Microsoft Docs
description: Az Azure Site Recovery koordinálja a replikáció, feladatátvétel és helyreállítás virtuális gépek és fizikai kiszolgálók. Ismerje meg a feladatátvételt az Azure-bA vagy egy másodlagos adatközpontba.
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
ms.date: 07/06/2018
ms.author: manayar
ms.openlocfilehash: 0e73abf0fc170079d4b2bc093df45c998e68d184
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49320526"
---
# <a name="map-virtual-networks-in-different-azure-regions"></a>Különböző Azure-régiókban található virtuális hálózatok leképezése


Ez a cikk ismerteti, hogyan képezhet le egymással különböző Azure-régióban található Azure Virtual Network két példánya. A hálózatleképezés biztosítja, hogy egy replikált virtuális gép létrehozásakor a cél Azure-régió, a virtuális gép is jön létre a virtuális hálózaton, amely a virtuális hálózat, a forrás virtuális gép le van képezve.  

## <a name="prerequisites"></a>Előfeltételek
Hálózatok leképezése, előtt győződjön meg arról, hogy létrehozott-e egy [az Azure virtual network](../virtual-network/virtual-networks-overview.md) a forrásrégióban és a cél Azure-régióban is.

## <a name="map-virtual-networks"></a>Virtuális hálózatok leképezése

Képezze le az Azure virtuális hálózat, amely egy Azure-régiót (forrás-hálózat), az Azure virtual machines (célhálózat), egy másik régióban található virtuális hálózat található, lépjen a **Site Recovery-infrastruktúra**  >  **Leképezés hálózati**. A hálózatleképezés létrehozása.

![Hálózati leképezések ablak – hálózatleképezés létrehozása](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)


A következő példában a virtuális gép fut, a kelet-ázsiai régióban. A virtuális gép replikálása folyamatban van a Délkelet-ázsiai régióban.

A hálózatleképezés létrehozása a Kelet-Ázsia régióból a Délkelet-ázsiai régióban, válassza ki a forrás hálózati helyét, és a cél hálózati helyét. Ezután válassza az **OK** lehetőséget.

![Hálózat leképezése ablakban - hozzáadása a Forráshálózat a forrás- és helyek kiválasztása](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)


Ismételje meg a fenti folyamatot a hálózatleképezés létrehozására a Délkelet-ázsiai régióban és a kelet-ázsiai régióban.

![Adja hozzá a hálózati hozzárendelési ablaktáblán – a célhálózat a forrás- és helyek kiválasztása](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-a-network-when-you-enable-replication"></a>Egy hálózat leképezése, amikor a replikáció engedélyezése

Replikálásakor egy virtuális gépet egy Azure-régióból egy másik régióba először, ha nem hálózati létezik leképezés, beállíthatja a célhálózat, amikor a replikáció beállítása. Ez a beállítás alapján, az Azure Site Recovery létrehoz hálózatleképezések a célrégióban a forrás-régióban, valamint a célrégióban a forrás régiójára.   

![Beállítások panel konfigurálása – válassza ki a célhelyen](./media/site-recovery-network-mapping-azure-to-azure/network-mapping4.png)

Alapértelmezés szerint a Site Recovery létrehoz egy hálózatot a célrégióban a forrás hálózati azonos. A Site Recovery létrehoz egy hálózati hozzáadásával **-asr** utótagként a Forráshálózat nevét. Már létre van hozva a hálózat kiválasztásához jelölje ki a **Testreszabás**.

![Testre szabhatja a célként megadott beállítások panel - készlet célként megadott erőforráscsoport-nevet és a cél virtuális hálózat neve](./media/site-recovery-network-mapping-azure-to-azure/network-mapping5.png)

Ha már elvégezte a hálózatleképezést, ha engedélyezi a replikációt a cél virtuális hálózattal nem módosítható. Ebben az esetben ha módosítani szeretné a cél virtuális hálózattal, a meglévő hálózatleképezés módosítása.  

![Testre szabhatja a célként megadott beállítások panel – állítsa be a célként megadott erőforráscsoport-név](./media/site-recovery-network-mapping-azure-to-azure/network-mapping6.png)

![Hálózati hozzárendelési ablaktáblán módosítása – módosíthatja egy meglévő céloldali virtuális hálózat neve](./media/site-recovery-network-mapping-azure-to-azure/modify-network-mapping.png)

> [!IMPORTANT]
> B régióba régióból A hálózatleképezés módosítása esetén győződjön meg arról, a hálózatleképezés régióba. a B-régióból módosítása
>
>


## <a name="subnet-selection"></a>Alhálózat kiválasztása
A cél virtuális gép az alhálózat van kiválasztva, a forrás virtuális gép az alhálózat neve alapján. Egy alhálózat, amelynek a neve megegyezik a forrás virtuális gép a célhálózat érhető el, ha a cél virtuális gépen az alhálózatban van beállítva. Ha egy azonos nevű alhálózat nem létezik a célhálózat, a betűrend szerint az első alhálózat a célként megadott alhálózat van beállítva.

Az alhálózat módosításához nyissa meg a **számítás és hálózat** a virtuális gép beállításait.

![Számítás és hálózat számítási tulajdonságok ablak](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="ip-address"></a>IP-cím

A cél virtuális gép minden hálózati adapter IP-cím van beállítva, a következő szakaszokban leírtak szerint.

### <a name="dhcp"></a>DHCP
Ha a forrás virtuális gép hálózati adapterét használ a DHCP, a a cél virtuális gép hálózati adapterét is DHCP használatára van beállítva.

### <a name="static-ip-address"></a>Statikus IP-cím
Ha a forrás virtuális gép hálózati adapterét statikus IP-címet használ, a cél virtuális gép hálózati adapterét is használatára van beállítva a statikus IP-címet. A következő szakaszok ismertetik, hogyan statikus IP-cím van beállítva.

### <a name="ip-assignment-behavior-during-failover"></a>IP-hozzárendelésének viselkedését feladatátvétel során
#### <a name="1-same-address-space"></a>1. Azonos címtartomány

Ha a forrásoldali alhálózat és a célként megadott alhálózat ugyanazt a címtartományt, a forrás virtuális gép hálózati adapterének IP-címét a cél IP-cím van beállítva. Az azonos IP-cím nem érhető el, ha a cél IP-cím a következő elérhető IP-címe van beállítva.

#### <a name="2-different-address-spaces"></a>2. Különböző címterek

Ha a forrásoldali alhálózat és a célként megadott alhálózat címe különböző tárolóhelyek, a célként megadott alhálózat következő elérhető IP-címek a cél IP-cím van beállítva.


### <a name="ip-assignment-behavior-during-test-failover"></a>IP-hozzárendelésének viselkedését a feladatátvételi teszt során
#### <a name="1-if-the-target-network-chosen-is-the-production-vnet"></a>1. Ha a kiválasztott célhálózat az éles virtuális hálózat
- A helyreállítási IP-cím (cél-IP) lesz, de statikus IP-cím **nem lesz az azonos IP-cím** szerint foglalva feladatátvételhez.
- A hozzárendelt IP-cím lesz végéről az alhálózat címtartományának következő elérhető IP.
- A például, ha a forrás virtuális gép statikus IP-cím van beállítva: 10.0.0.19 és a feladatátvételi teszt kíséreltek meg a beállított éles hálózati környezetben: ***dr-ÉLES-északnyugati része***, a 10.0.0.0/24 alhálózati tartományba. </br>
A feladatátvételi virtuális Gépen az alhálózat címtartományának, amely végéről – a következő elérhető IP-Címmel rendelkező hozzájuk: 10.0.0.254 </br>

**Megjegyzés:** terminológia **éles vNet** a neve a "célhálózat" leképezve a vész-helyreállítási konfiguráció során.
#### <a name="2-if-the-target-network-chosen-is-not-the-production-vnet-but-has-the-same-subnet-range-as-production-network"></a>2. Ha a kiválasztott célhálózat nem az éles virtuális hálózathoz, de az egyező alhálózati tartományba éles hálózati környezetben van 

- A helyreállítási IP-cím (cél IP-címet) a statikus IP-cím lesz a **azonos IP-címet** (azaz a konfigurált statikus IP-cím), foglalva feladatátvételhez. A megadott azonos IP-cím áll rendelkezésre.
- Ha a konfigurált statikus IP-cím már hozzá van rendelve egy másik virtuális gép/eszköz, a helyreállítási IP-cím végéről az alhálózat címtartományának következő elérhető IP lesz.
- A például, ha a forrás virtuális gép statikus IP-cím van beállítva: 10.0.0.19 és a feladatátvételi teszt kísérlet történt egy tesztelési célú hálózat az: ***dr-nem – ÉLES-északnyugati része***, az éles hálózati környezetben – egyező alhálózati tartományba 10.0.0.0/24. </br>
  A feladatátvételi virtuális Gépen hozzájuk a következő statikus IP-cím </br>
    - statikus IP-cím beállítva: 10.0.0.19, ha az IP-cím.
    - Következő elérhető IP: 10.0.0.254, ha az IP-cím 10.0.0.19 elem már szerepel használja.


A cél IP-címet minden egyes hálózati adapter módosításához nyissa meg a **számítás és hálózat** a virtuális gép beállításait.</br>
Ajánlott eljárásként, mindig ajánlott feladatátvételi teszt végrehajtásához tesztelési hálózatot választ.
## <a name="next-steps"></a>További lépések

* Felülvizsgálat [hálózatkezelési útmutató Azure-beli virtuális gépek replikálásához](site-recovery-azure-to-azure-networking-guidance.md).
