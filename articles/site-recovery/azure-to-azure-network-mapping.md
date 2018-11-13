---
title: Az Azure Site Recoveryben két Azure-régiók közötti virtuális hálózatok leképezése |} A Microsoft Docs
description: Az Azure Site Recovery koordinálja a replikáció, feladatátvétel és helyreállítás virtuális gépek és fizikai kiszolgálók. Ismerje meg a feladatátvételt az Azure-bA vagy egy másodlagos adatközpontba.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: mayg
ms.openlocfilehash: 683f8ef89b02679d1f3f1a66f867f0dde757ada1
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51564969"
---
# <a name="set-up-network-mapping-and-ip-addressing-for-vnets"></a>A hálózatleképezés és virtuális hálózatok IP-címzés beállítása

Ez a cikk bemutatja, hogyan képezhet le az Azure virtuális hálózatok (Vnetek) a különböző Azure-régiókban található két példányát, és a hálózatok közötti IP-címzés beállítása. A hálózatleképezés biztosítja, hogy a replikált virtuális gép létrejött-e a cél Azure-beli régióban jön létre a virtuális hálózattal, a forrás virtuális gép virtuális hálózat lesz leképezve.

## <a name="prerequisites"></a>Előfeltételek

Hálózatok leképezése, mielőtt rendelkeznie kell [Azure virtuális hálózatok](../virtual-network/virtual-networks-overview.md) a forrás és cél Azure-régióban. 

## <a name="set-up-network-mapping"></a>A hálózatleképezés beállítása

Hálózatok leképezése a következőképpen:

1. A **Site Recovery-infrastruktúra**, kattintson a **+ Hálózatleképezés**.

    ![ A hálózatleképezés létrehozása](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)

3. A **hálózatleképezés hozzáadása**válassza ki a forrás és cél helye. Ebben a példában a forrás virtuális gép fut, a Kelet-Ázsia régióban, és a Délkelet-Ázsia régióba replikálja.

    ![Válassza ki a forrás és cél ](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)
3. Most hozzon létre egy hálózatleképezés ellentétes a címtárban. Ebben a példában a forrás lesz Délkelet-Ázsia és a cél lesz Kelet-Ázsia.

    ![Adja hozzá a hálózati hozzárendelési ablaktáblán – a célhálózat a forrás- és helyek kiválasztása](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-networks-when-you-enable-replication"></a>Hálózatok leképezése, amikor a replikáció engedélyezése

Ha még nem készített hálózatleképezés vészhelyreállítás az Azure virtuális gépek konfigurálása előtt, megadhatja a cél hálózatot, amikor Ön [beállítását, és engedélyezze a replikációt](azure-to-azure-how-to-enable-replication.md). Amikor ezt megteheti az alábbiak történnek:

- Alapján, válassza ki a cél a Site Recovery automatikusan létrehozza hálózatleképezések a célrégióban a forrás és a célként megadott forrásrégióba.
- Alapértelmezés szerint a Site Recovery létrehoz egy hálózatot a célrégióban a forrás hálózati azonos. Site Recovery szolgáltatással biztonságba **-asr** utótagként a Forráshálózat nevét. Testre szabhatja a célhálózat.
- Ha már elvégezte a hálózatleképezést, ha engedélyezi a replikációt a cél virtuális hálózattal nem módosítható. Ha módosítani szeretné a cél virtuális hálózattal, meg kell a meglévő hálózatleképezés módosítása.
- B régióba régióból A hálózatleképezés módosítása esetén győződjön meg arról, hogy is a hálózatleképezés módosítása B régióból régióba A.]

## <a name="specify-a-subnet"></a>Adjon meg egy alhálózatot

A virtuális gép ki van jelölve a cél alhálózat a forrás virtuális gép az alhálózat neve alapján.

- Alhálózat neve megegyezik a forrás Virtuálisgép-alhálózatot a célhálózat érhető el, ha a cél virtuális Gépen az alhálózatban van beállítva.
- Ha egy azonos nevű alhálózat nem létezik a célhálózat, a betűrendben az első alhálózat a célként megadott alhálózat van beállítva.
- Módosíthatja a a a **számítás és hálózat** a virtuális gép beállításait.

    ![Számítás és hálózat számítási tulajdonságok ablak](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="set-up-ip-addressing-for-target-vms"></a>A cél virtuális gépek IP-címzés beállítása

A cél virtuális gép minden hálózati adapter IP-cím konfigurációja a következő:

- **DHCP**: Ha a forrás virtuális gép a hálózati Adaptert használ a DHCP, a hálózati Adaptert a cél virtuális gép is van-e állítva DHCP használatára.
- **Statikus IP-cím**: Ha a forrás virtuális gép hálózati adapter statikus IP-címkezelés, a céloldali virtuális gép hálózati is statikus IP-címet fogja használni.


## <a name="ip-address-assignment-during-failover"></a>Feladatátvétel során IP-cím hozzárendelése

**Forrás és cél alhálózat** | **Részletek**
--- | ---
Azonos címtartomány | A forrás virtuális gép hálózati adapter IP-címét és a cél virtuális gép hálózati adapter IP-cím van beállítva.<br/><br/> Ha a cím nem érhető el, a következő elérhető IP-címe és a cél van beállítva.
Különböző címtér<br/><br/> A célként megadott alhálózat következő elérhető IP-címe úgy van beállítva, és a cél virtuális gép hálózati adapter címe.



## <a name="ip-address-assignment-during-test-failover"></a>Feladatátvételi teszt során IP-cím hozzárendelése

**Célhálózat** | **Részletek**
--- | ---
Célhálózat a feladatátvételi virtuális hálózat | -Cél IP-címet a statikus, de nem megegyező IP-címre a feladatátvételhez fenntartott.<br/><br/>  -A hozzárendelt cím az alhálózati címtartomány vége a következő elérhető címe.<br/><br/> Példa: Ha a forrás IP-cím 10.0.0.19 és feladatátvételi hálózat a 10.0.0.0/24 címtartományt használja, akkor a következő IP-címe, a cél virtuális gép rendelt 10.0.0.254.
Célhálózat nincs a virtuális hálózatok közötti feladatátvétel | -Cél IP-cím statikus foglalva feladatátvételhez azonos IP-címére lesz.<br/><br/>  – Ha az azonos IP-cím már hozzá van rendelve, az IP-cím a következő parancsot a eeach érhető el, az alhálózati címtartomány.<br/><br/> Példa: Ha a statikus IP-forráscím 10.0.0.19 és feladatátvétel nem hálózatban található, amely nem a feladatátvételi hálózat, a tartomány 10.0.0.0/24, majd a célként megadott statikus IP-cím lesz 10.0.0.0.19, ha elérhető, és egyéb 10.0.0.254 lesz.

- A feladatátvételi virtuális hálózaton a célhálózat vész-helyreállítási beállításakor kiválasztott.
- Azt javasoljuk, hogy Ön mindig használja a nem éles hálózati környezetben a feladatátvételi teszthez.
- A cél IP-címet módosíthatja a **számítás és hálózat** a virtuális gép beállításait.


## <a name="next-steps"></a>További lépések

- Felülvizsgálat [hálózatkezelési útmutató](site-recovery-azure-to-azure-networking-guidance.md) vészhelyreállítás Azure virtuális Gépen.
- [További](site-recovery-retain-ip-azure-vm-failover.md) IP-cím megőrzése feladatátvétel után.

Ha a kiválasztott célhálózat a feladatátvételi virtuális hálózat"és a 2. Tegyük fel, hogy a"Ha a kiválasztott célhálózat eltér a feladatátvételi virtuális hálózathoz, de a feladatátvételi virtuális hálózatok közötti egyező alhálózati tartományba"pontot