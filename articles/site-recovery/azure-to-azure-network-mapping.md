---
title: Virtuális hálózatok leképezése két régió között az Azure Site Recovery szolgáltatásban
description: Ismerje meg a virtuális hálózatok leképezése két Azure-régiók között az Azure VM vész-helyreállítási Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: mayg
ms.openlocfilehash: b8f0512f978f25ca196ad6e9a7a03243c47f0662
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258082"
---
# <a name="set-up-network-mapping-and-ip-addressing-for-vnets"></a>Hálózati hozzárendelés és IP-címzés beállítása a virtuális hálózatokhoz

Ez a cikk bemutatja, hogyan képezheti le a különböző Azure-régiókban található Virtuális Azure-hálózatok (VNets) két példányát, és hogyan állíthatja be a hálózatok közötti IP-címzést. A hálózati leképezés a célhálózat nak a replikáció engedélyezésekor a forráshálózaton alapuló alapértelmezett viselkedését biztosítja.

## <a name="prerequisites"></a>Előfeltételek

A hálózatok leképezése előtt rendelkeznie kell [az Azure-virtuális hálózatok](../virtual-network/virtual-networks-overview.md) a forrás-és a cél-Azure-régiókban. 

## <a name="set-up-network-mapping-manually-optional"></a>Hálózati hozzárendelés manuális beállítása (nem kötelező)

Térképhálózatok az alábbiak szerint:

1. A **Hely-helyreállítási infrastruktúra területén**kattintson a **+Hálózati leképezés gombra.**

    ![ Hálózati hozzárendelés létrehozása](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)

3. A **Hálózatleképezés hozzáadása**területen válassza ki a forrás- és célhelyeket. A mi példánkban a forrás virtuális gép fut a kelet-ázsiai régióban, és replikálja a délkelet-ázsiai régióban.

    ![Forrás és cél kiválasztása](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)
3. Most hozzon létre egy hálózati leképezést az ellenkező irányba. A mi példánkban a forrás most Délkelet-Ázsia lesz, a célpont pedig Kelet-Ázsia.

    ![Hálózati hozzárendelés hozzáadása ablaktábla – Forrás- és célhelyek kiválasztása a célhálózathoz](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-networks-when-you-enable-replication"></a>Hálózatok leképezése a replikáció engedélyezésekor

Ha még nem készített hálózati leképezést, mielőtt konfigurálná a vészhelyreállítást az Azure virtuális gépekhez, megadhatja a célhálózatot [a beállításkor, és engedélyezheti a replikációt.](azure-to-azure-how-to-enable-replication.md) Ha ezt teszi, a következő történik:

- A kiválasztott cél alapján a Site Recovery automatikusan létrehozza a hálózati hozzárendeléseket a forrástól a célrégióig, valamint a célterületről a forrásrégióra.
- Alapértelmezés szerint a Site Recovery olyan hálózatot hoz létre a célrégióban, amely megegyezik a forráshálózattal. A Site Recovery **-asr** utótagként hozzáadja a forráshálózat nevét. A célhálózat ot testre szabhatja.
- Ha a hálózati leképezés már megtörtént egy forráshálózaton, a leképezett célhálózat mindig az alapértelmezett lesz a további virtuális gépek replikációjának engedélyezésekor. A legördülő menüben a többi elérhető lehetőség kiválasztásával módosíthatja a célvirtuális hálózatot. 
- Az új replikációk alapértelmezett célvirtuális hálózatának módosításához módosítania kell a meglévő hálózati hozzárendelést.
- Ha módosítani szeretné a hálózati hozzárendelést A régióból A régióba, győződjön meg arról, hogy először törli a hálózati hozzárendelést a B régióból az A régióba. A fordított leképezés törlése után módosítsa a hálózati leképezést A régióból B régióba, majd hozza létre a megfelelő sztornírozást.

>[!NOTE]
>* A hálózati hozzárendelés módosítása csak az új virtuálisgép-replikációk alapértelmezéseit módosítja. Nincs hatással a meglévő replikációk virtuális hálózatának célhálózati választásaira. 
>* Ha módosítani szeretné egy meglévő replikáció célhálózatát, lépjen a replikált elem számítási és hálózati beállításai lapra.

## <a name="specify-a-subnet"></a>Alhálózat megadása

A cél virtuális gép alhálózata a forrásvirtuális gép alhálózatának neve alapján kerül kiválasztásra.

- Ha a célhálózatban elérhető a forrásvirtuálisgép alhálózatával azonos nevű alhálózat, akkor az alhálózat be van állítva a célvirtuális géphez.
- Ha a célhálózatban nem létezik azonos nevű alhálózat, az alfabetikus sorrendben az első alhálózat lesz beállítva célalhálózatként.
- Módosíthatja a cél alhálózata a **számítási és hálózati** beállításokat a virtuális gép.

    ![Számítási és hálózati számítási tulajdonságok ablak](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="set-up-ip-addressing-for-target-vms"></a>IP-címzés beállítása a célvirtuális gépekhez

A célvirtuális gép minden hálózati adapterének IP-címe a következőképpen van konfigurálva:

- **DHCP**: Ha a forrásvirtuális gép hálózati adaptere DHCP-t használ, a célvirtuális gép hálózati adaptere is DHCP-t használ.
- **Statikus IP-cím:** Ha a virtuális gép forráshálózati adaptere statikus IP-címzést használ, a cél virtuálisgép-hálózati adapter statikus IP-címet is használ.


## <a name="ip-address-assignment-during-failover"></a>IP-címkiosztás feladatátvétel közben

**Forrás- és célalhálózatok** | **Részletek**
--- | ---
Ugyanaz a címterület | A forrás virtuálisgép-hálózati adapter IP-címe a cél virtuális hálózati adapter IP-címe.<br/><br/> Ha a cím nem érhető el, a következő elérhető IP-cím lesz a cél.
Különböző címtér | A célalhálózatban a következő elérhető IP-cím a cél virtuális hálózati adapter címként van beállítva.



## <a name="ip-address-assignment-during-test-failover"></a>IP-címhozzárendelés a teszt feladatátvétele során

**Célhálózat** | **Részletek**
--- | ---
A célhálózat a feladatátvevő virtuális hálózat | - A cél IP-cím statikus lesz ugyanazzal az IP-címmel. <br/><br/>  - Ha ugyanaz az IP-cím már hozzá van rendelve, akkor az IP-cím a következő, amely az alhálózati tartomány végén érhető el. Például: Ha a forrás IP-címe 10.0.0.19, és a feladatátvevő hálózat a 10.0.0.0/24 tartományt használja, akkor a célvirtuális géphez rendelt következő IP-cím 10.0.0.254.
A célhálózat nem a feladatátvevő virtuális hálózat | - A cél IP-cím statikus lesz ugyanazzal az IP-címmel.<br/><br/>  - Ha ugyanaz az IP-cím már hozzá van rendelve, akkor az IP-cím a következő, amely az alhálózati tartomány végén érhető el.<br/><br/> Például: Ha a forrás statikus IP-cím 10.0.0.19 és feladatátvétel i hálózaton, amely nem a feladatátvevő hálózat, a tartomány 10.0.0.0/24, majd a cél statikus IP-cím lesz 10.0.0.0.19, ha rendelkezésre áll, és egyébként lesz 10.0.0.254.

- A feladatátvevő virtuális hálózat az a célhálózat, amelyet a vész-helyreállítás beállításakor választ ki.
- Azt javasoljuk, hogy mindig egy nem éles hálózat ot a teszt feladatátvételhez.
- A cél IP-címet a **virtuális gép számítási és hálózati** beállításaiban módosíthatja.


## <a name="next-steps"></a>További lépések

- Tekintse át az Azure virtuális gép vész-helyreállítási [hálózati útmutatóját.](site-recovery-azure-to-azure-networking-guidance.md)
- [További információ](site-recovery-retain-ip-azure-vm-failover.md) az IP-címek feladatátvétel utáni megőrzéséről.
