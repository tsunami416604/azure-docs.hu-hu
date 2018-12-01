---
title: Állítsa be az IP-címek az Azure Site Recovery a feladatátvételt követően csatlakozni egy másodlagos helyszíni helyre |} A Microsoft Docs
description: Ismerteti, hogyan állíthatja be az IP-címkezelés vész-helyreállítási és az Azure Site Recovery a feladatátvétel utáni virtuális gépek egy másodlagos helyszíni helyre való csatlakozáshoz.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/28/2018
ms.author: raynew
ms.openlocfilehash: 8be247535384fe0fe3dddb5e3f096e457d1a96c4
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52726185"
---
# <a name="set-up-ip-addressing-to-connect-to-a-secondary-on-premises-site-after-failover"></a>Az IP-címkezelést a feladatátvételt követően csatlakozni egy másodlagos helyszíni helyre

Követően átadja a feladatokat a Hyper-V virtuális gépeket a System Center Virtual Machine Manager (VMM) felhőkben egy másodlagos helyre, akkor képeseknek kell lenniük a replika virtuális gépek csatlakozni. Ez a cikk segít teszi ezt. 

## <a name="connection-options"></a>Csatlakozási lehetőségek

A feladatátvételt követően a rendszer több módon kezelni a replika virtuális gépek IP-címzés: 

- **Az azonos IP-cím megőrzése feladatátvétel után**: Ebben a forgatókönyvben a replikált virtuális gépen az elsődleges virtuális gép megegyező IP-címre. Ez leegyszerűsíti a kapcsolódó hálózati problémák feladatátvétel után, de néhány infrastruktúra munkát igényel.
- **Egy másik IP-címet használja a feladatátvételt követően**: Ebben a forgatókönyvben a virtuális gép új IP-címet lekéri a feladatátvételt követően. 
 

## <a name="retain-the-ip-address"></a>Az IP-cím megőrzése

Ha szeretné megőrizni az IP-címek az elsődleges helyről másodlagos helyre a feladatátvételt követően, végezheti el:

- Helyezze üzembe egy többhelyes alhálózatot az elsődleges és a másodlagos helyek között.
- Végezzen el egy teljes alhálózat feladatátvételt az elsődleges kiszolgálóról másodlagos helyre. Az új helyet, az IP-címek jelzi az útvonalak frissítése kell.


### <a name="deploy-a-stretched-subnet"></a>A többhelyes alhálózat üzembe helyezése

A többhelyes konfiguráció esetén az alhálózat érhető el egyszerre az elsődleges és másodlagos helyeken. A felhőbe archivált alhálózatán lép a gép és annak (3. rétegbeli) IP-címének konfigurációja, a másodlagos helyen, a hálózat automatikusan irányítja a forgalmat az új helyre. 

- A 2. rétegbeli (adatrétege hivatkozás) szempontjából szüksége lesz, amely egy kiterjesztett VLAN-t kezelheti a hálózati berendezések.
- Nyújtsák VLAN, a potenciális tartalék tartomány mindkét helyre kiterjeszti. Ez lesz a hibaérzékeny pont. Amíg nem valószínű, ilyen esetben, nem feltétlenül egy incidenst, például egy szórási storm különítenie. 


### <a name="fail-over-a-subnet"></a>Átadja a feladatokat egy alhálózatot

Is átadja a feladatokat a felhőbe archivált alhálózat előnyeinek beszerzése nélkül ténylegesen kiterjesztését, a teljes alhálózathoz. Ez a megoldás egy alhálózat érhető el a forrás- vagy webhely, de nem mind egyszerre.

- A feladatátvétel esetén az IP-címtér fenntartása programozott módon rendezheti az útválasztó infrastruktúra alhálózatok áthelyezheti egyik helyről egy másikra.
- Feladatátvétel esetén a alhálózatok helyezze át a társított virtuális gépeiken.
- A fő hátrány, ez a megközelítés, hogy hiba esetén, hogy helyezze át a teljes alhálózathoz.

#### <a name="example"></a>Példa

Íme egy példa a teljes alhálózat feladatátvétel. 

- A feladatátvétel előtt az elsődleges hely van az alhálózat 192.168.1.0/24 futó alkalmazások.
- Feladatátvétel során a virtuális gépeket az alhálózaton belüli összes feladatátvétel a másodlagos helyre, és megőrizheti az IP-címek. 
- Helyek közötti útvonalak megfelelően a tény, hogy az alhálózat 192.168.1.0/24 összes virtuális gép most már helyezte át a másodlagos hely módosítani kell.

Az alábbi ábrán az alhálózatok előtt, és a feladatátvételt követően.


**A feladatátvétel előtt**

![A feladatátvétel előtt](./media/hyper-v-vmm-networking/network-design2.png)

**Feladatátvétel után**

![Feladatátvétel után](./media/hyper-v-vmm-networking/network-design3.png)

A feladatátvételt követően a Site Recovery a virtuális gép minden hálózati adapter IP-címet foglal le. A címet a statikus IP-címkészletből a megfelelő hálózatban, az egyes Virtuálisgép-példányhoz van lefoglalva.

- Ha az IP-címkészlet a másodlagos hely ugyanaz, mint amellyel a forráshelyen, a Site Recovery azonos IP-címét (a forrás virtuális gép), a replika virtuális gép foglal le. Az IP-cím foglalt a VMM-ben, de a Hyper-V gazdagépen történő beállítása nem feladatátvételi IP-címet. Egy Hyper-v gazdagép feladatátvételi IP-cím van beállítva, a feladatátvétel előtt.
- Az azonos IP-cím nem érhető el, ha a Site Recovery lefoglalja egy másik elérhető IP-cím a készletből.
- Ha a virtuális gépek DHCP használatára, a Site Recovery nem felügyeli az IP-címek. Ellenőrizze, hogy a DHCP-kiszolgáló a másodlagos helyen is lefoglalhat címeket a forráshely egyező tartományba kell.

### <a name="validate-the-ip-address"></a>Az IP-cím ellenőrzése

Miután engedélyezte egy virtuális gép védelmét, használhatja az alábbi minta parancsfájl ellenőrizze a címet a virtuális géphez rendelt. Az IP-címet állítja be a feladatátvételi IP-címet, és a feladatátvétel idején a virtuális géphez hozzárendelt:

    ```
    $vm = Get-SCVirtualMachine -Name <VM_NAME>
    $na = $vm[0].VirtualNetworkAdapters>
    $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
    $ip.address 
    ```

## <a name="use-a-different-ip-address"></a>Egy másik IP-cím használata

Ebben a forgatókönyvben feladatátvételt végrehajtó virtuális gépek IP-címei megváltoztak. Ez a megoldás hátránya a szükséges karbantartás.  DNS- és a gyorsítótár-bejegyzéseket kell frissíteni. Ennek eredményeként leállást mérsékelhető a következőképpen:

- Intranet-alkalmazásokat használni alacsony élettartam-értékek.
- Használja a következő szkriptet a Site Recovery helyreállítási terv egy időben a DNS-kiszolgáló frissítése. A parancsfájl nem szükséges, ha a dinamikus DNS-regisztráció.

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

Ebben a példában az elsődleges és másodlagos helyek között rendelkezünk különböző IP-címeket, és a egy harmadik hely az elsődleges vagy tartalék üzemeltetett alkalmazások a hely érhető el.

- A feladatátvétel előtt az alkalmazások olyan elsődleges helyen üzemeltetett alhálózati 192.168.1.0/24.
- A feladatátvételt követően az alkalmazások az alhálózat 172.16.1.0/24 a másodlagos helyen vannak konfigurálva.
- Összes három helyen képesek elérni egymást.
- A feladatátvételt követően az alkalmazások helyreállítási alhálózatban lehet visszaállítani.
- Ebben a forgatókönyvben nem átadja a feladatokat a teljes alhálózat nem szükséges, és nincs szükség változtatásokra segítségével konfigurálja újra a VPN- vagy hálózati útvonalak. A feladatátvétel és az egyes DNS-frissítésekkel, győződjön meg arról, hogy az alkalmazások is elérhető.
- Ha a DNS dinamikus frissítések engedélyezésére van konfigurálva, a virtuális gépek fog regisztrálhatják magukat az új IP-címet használja, amikor elindítja a feladatátvételt követően.

**A feladatátvétel előtt**

![Különböző IP-cím – a feladatátvétel előtt](./media/hyper-v-vmm-networking/network-design10.png)

**Feladatátvétel után**

![Másik IP-cím - feladatátvétel után](./media/hyper-v-vmm-networking/network-design11.png)


## <a name="next-steps"></a>További lépések

[Feladatátvétel futtatása](hyper-v-vmm-failover-failback.md)

