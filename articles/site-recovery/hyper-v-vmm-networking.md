---
title: IP-címzés beállítása feladatátvétel után egy másodlagos helyre az Azure Site Recovery szolgáltatással
description: Bemutatja, hogyan állíthatja be az IP-címzést a virtuális gépekhez való csatlakozáshoz egy másodlagos helyszíni helyen a vész-helyreállítás és az Azure Site Recovery feladatátvétel után.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: a61f7ff69e648262eb721eb61a98b09dbbee924c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73961428"
---
# <a name="set-up-ip-addressing-to-connect-to-a-secondary-on-premises-site-after-failover"></a>IP-címzés beállítása egy másodlagos helyszíni helyhez való csatlakozáshoz a feladatátvétel után

Miután feladatátvételi rendszer a System Center virtuálisgép-kezelő (VMM) felhők egy másodlagos helyre, akkor képesnek kell lennie arra, hogy csatlakozzon a replika virtuális gépek. Ez a cikk segít ebben. 

## <a name="connection-options"></a>Csatlakozási beállítások

A feladatátvétel után többféleképpen is kezelhetők a replika virtuális gépek IP-címzése: 

- **Ugyanazt az IP-címet a feladatátvétel után:** Ebben a forgatókönyvben a replikált virtuális gép rendelkezik az elsődleges virtuális gép IP-címével. Ez leegyszerűsíti a hálózattal kapcsolatos problémákat a feladatátvétel után, de némi infrastrukturális munkát igényel.
- **Használjon egy másik IP-címet feladatátvétel után:** Ebben az esetben a virtuális gép kap egy új IP-címet a feladatátvétel után. 
 

## <a name="retain-the-ip-address"></a>Az IP-cím megtartása

Ha meg szeretné őrizni az IP-címeket az elsődleges helyről, a feladatátvétel után a másodlagos helyre a következőket teheti:

- Az elsődleges és a másodlagos helyek között nyújtott alhálózat üzembe helyezése.
- Hajtson végre teljes alhálózati feladatátvételt az elsődleges és a másodlagos hely között. Az IP-címek új helyének jelzéséhez frissítenie kell az útvonalakat.


### <a name="deploy-a-stretched-subnet"></a>Nyújtott alhálózat telepítése

Feszített konfigurációban az alhálózat egyszerre érhető el az elsődleges és a másodlagos helyeken is. Egy feszített alhálózatban, amikor áthelyez egy gépet és annak IP-címét (3. réteg) a másodlagos helyre, a hálózat automatikusan az új helyre irányítja a forgalmat. 

- A Layer 2 (adatkapcsolati réteg) szempontjából olyan hálózati berendezésekre van szükség, amelyek képesek kezelni a kifeszített VLAN-t.
- A VLAN nyújtásával a potenciális tartalék tartomány mindkét helyszínre kiterjed. Ez lesz az egyetlen pont a hiba. Bár nem valószínű, hogy egy ilyen esetben előfordulhat, hogy nem tudja elkülöníteni az eseményeket, például egy közvetítési vihart. 


### <a name="fail-over-a-subnet"></a>Feladatátvétel alhálózaton

A teljes alhálózat feladatátvételével a nyújtott alhálózat előnyeinek eléréséhez anélkül, hogy ténylegesen megnyújtaná azt. Ebben a megoldásban egy alhálózat érhető el a forrás- vagy célhelyen, de nem egyszerre.

- Feladatátvétel esetén az IP-címterület fenntartásához programozott módon gondoskodhat arról, hogy az útválasztó-infrastruktúra alhálózatokat helyezzeát át egyik helyről a másikra.
- Feladatátvétel esetén az alhálózatok a társított virtuális gépekkel együtt mozognak.
- Ennek a megközelítésnek a fő hátránya, hogy meghibásodás esetén át kell helyeznie a teljes alhálózatot.

#### <a name="example"></a>Példa

Íme egy példa a teljes alhálózati feladatátvételre. 

- A feladatátvétel előtt az elsődleges hely 192.168.1.0/24 alhálózatban futó alkalmazásokkal rendelkezik.
- A feladatátvétel során az alhálózat összes virtuális gépe átlesz a másodlagos hely, és megőrzi az IP-címüket. 
- Az összes hely közötti útvonalakat módosítani kell, hogy tükrözzék azt a tényt, hogy a 192.168.1.0/24 alhálózat összes virtuális gépe átkerült a másodlagos helyre.

Az alábbi ábrák a feladatátvétel előtti és utáni alhálózatokat mutatják be.


**Feladatátvétel előtt**

![Feladatátvétel előtt](./media/hyper-v-vmm-networking/network-design2.png)

**Feladatátvétel után**

![Feladatátvétel után](./media/hyper-v-vmm-networking/network-design3.png)

A feladatátvétel után a Site Recovery lefoglal egy IP-címet a virtuális gép minden egyes hálózati adapteréhez. A cím a megfelelő hálózat statikus IP-címkészletéből van lefoglalva, minden virtuálisgép-példányhoz.

- Ha a másodlagos hely IP-címkészlete megegyezik a forráshelyen lévővel, a Site Recovery ugyanazt az IP-címet (a forrás virtuális gép) osztja le a replika virtuális gépszámára. Az IP-cím a VMM-ben van fenntartva, de nincs beállítva a Hyper-V állomás feladatátvételi IP-címeként. A Hyper-V gazdagépen lévő feladatátvételi IP-cím közvetlenül a feladatátvétel előtt van beállítva.
- Ha ugyanaz az IP-cím nem érhető el, a Site Recovery egy másik elérhető IP-címet foglal le a készletből.
- Ha a virtuális gépek DHCP-t használnak, a Site Recovery nem kezeli az IP-címeket. Ellenőriznie kell, hogy a másodlagos helyen lévő DHCP-kiszolgáló a forráshellyel azonos tartományból foglalhat-e le címeket.

### <a name="validate-the-ip-address"></a>Az IP-cím ellenőrzése

Miután engedélyezte a virtuális gép védelmét, a következő mintaparancsfájlsegítségével ellenőrizheti a virtuális géphez rendelt címet. Ez az IP-cím van beállítva, mint a feladatátvételi IP-cím, és a virtuális géphez van rendelve a feladatátvétel időpontjában:

    ```
    $vm = Get-SCVirtualMachine -Name <VM_NAME>
    $na = $vm[0].VirtualNetworkAdapters>
    $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
    $ip.address 
    ```

## <a name="use-a-different-ip-address"></a>Másik IP-cím használata

Ebben a forgatókönyvben a feladatátvételt megvevő virtuális gépek IP-címei módosulnak. Ennek a megoldásnak a hátránya a szükséges karbantartás.  Előfordulhat, hogy frissíteni kell a DNS- és gyorsítótár-bejegyzéseket. Ez leállást eredményezhet, amely a következőképpen mérsékelhető:

- Használjon alacsony TTL értékeket az intranetes alkalmazásokhoz.
- A DNS-kiszolgáló időben történő frissítéséhez használja a következő parancsfájlt a Site Recovery helyreállítási tervben. Dinamikus DNS-regisztráció használata esetén nincs szüksége a parancsfájlra.

    ```
    param(
    string]$Zone,
    [string]$name,
    [string]$IP
    )
    $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
    $newrecord = $record.clone()
    $newrecord.RecordData[0].IPv4Address  =  $IP
    Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord
    ```
    
### <a name="example"></a>Példa 

Ebben a példában az elsődleges és másodlagos helyek különböző IP-címekkel rendelkezünk, és van egy harmadik hely, ahonnan az elsődleges vagy helyreállítási helyen tárolt alkalmazások elérhetők.

- A feladatátvétel előtt az alkalmazások a 192.168.1.0/24 alhálózatot üzemeltetik az elsődleges helyen.
- Feladatátvétel után az alkalmazások a 172.16.1.0/24 alhálózatban vannak konfigurálva a másodlagos helyen.
- Mindhárom webhely hozzáférhet egymáshoz.
- Feladatátvétel után az alkalmazások visszaállnak a helyreállítási alhálózatban.
- Ebben az esetben nincs szükség a teljes alhálózat feladatátvételre, és nincs szükség módosításokra a VPN- vagy hálózati útvonalak újrakonfigurálásához. A feladatátvétel és néhány DNS-frissítés biztosítja, hogy az alkalmazások elérhetők maradjanak.
- Ha a DNS úgy van beállítva, hogy engedélyezze a dinamikus frissítéseket, akkor a virtuális gépek regisztrálják magukat az új IP-cím használatával, amikor a feladatátvétel után elindulnak.

**Feladatátvétel előtt**

![Eltérő IP-cím – feladatátvétel előtt](./media/hyper-v-vmm-networking/network-design10.png)

**Feladatátvétel után**

![Eltérő IP-cím - feladatátvétel után](./media/hyper-v-vmm-networking/network-design11.png)


## <a name="next-steps"></a>További lépések

[Feladatátvétel futtatása](hyper-v-vmm-failover-failback.md)

