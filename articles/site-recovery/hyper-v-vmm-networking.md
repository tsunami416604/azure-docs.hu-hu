---
title: "Az IP-címzést, az Azure Site Recovery a feladatátvételt követően csatlakozni egy másodlagos helyszíni helyre |} Microsoft Docs"
description: "Ismerteti, hogyan állíthatja be az IP-címeket egy másodlagos helyszíni helyre a virtuális gépek csatlakoztatása az Azure Site Recovery a feladatátvételt követően."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 02/12/2018
ms.author: rayne
ms.openlocfilehash: 531705bc704b3366c1c670ecf07c809ade67bc55
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="set-up-ip-addressing-to-connect-to-a-secondary-on-premises-site-after-failover"></a>Az IP-címzés csatlakozni egy másodlagos helyszíni helyre a feladatátvételt követően

Miután a rendszer átadja a Hyper-V virtuális gépek a másodlagos helyek a System Center Virtual Machine Manager (VMM) felhőkben, képeseknek kell lenniük a replika virtuális gépek csatlakozni. Ez a cikk segítséget nyújt ennek. 

## <a name="connection-options"></a>Kapcsolati lehetőségek

A feladatátvétel után többféle módon kezelje a replika virtuális gép IP-címeket: 

- **Tartsa meg ugyanazt a címet a feladatátvételt követően**: Ebben a forgatókönyvben a replikált virtuális gép rendelkezik az elsődleges virtuális gép ugyanazon IP-címre. Ez leegyszerűsíti kapcsolódó hálózati problémák feladatátvétel után, de néhány infrastruktúra munkát igényel.
- **Egy másik IP-címet használja a feladatátvételt követően**: Ebben a forgatókönyvben a virtuális Gépet egy új IP-cím lekérdezi a feladatátvételt követően. 
 

## <a name="retain-the-ip-address"></a>Tartsa meg az IP-cím

Ha szeretné megőrizni az IP-címeket az elsődleges helyről másodlagos helyre a feladatátvételt követően, hogy a következőket teheti:

- Telepítse a felhőbe archivált alhálózat az elsődleges és a másodlagos helyek között.
- Végezzen el egy teljes alhálózati feladatátvételt az elsődleges másodlagos helyen. Frissítenie kell az IP-címek új helyét jelöljék az útvonalakat.


### <a name="deploy-a-stretched-subnet"></a>A felhőbe archivált alhálózati telepítése

Felhőbe archivált konfiguráció esetén az alhálózat nem áll rendelkezésre egyidejűleg az elsődleges és másodlagos helyen. Felhőbe archivált alhálózat a másodlagos helyre, és az IP-(3. rétegbeli) beállítása egy gép áthelyezésekor a hálózati automatikusan irányítja a forgalmat az új helyre. 

- 2. rétegbeli (adatrétege hivatkozás) szempontjából van szüksége, melyekkel kezelhető a felhőbe archivált VLAN hálózati berendezések.
- A VLAN nyújtsák, a potenciális tartalék tartomány kiterjeszti a két hely. Ez lesz a hibaérzékeny pontok kialakulását. Amíg nem valószínű, ilyen esetben nem valószínűleg egy szórási storm például incidens különítenie. 


### <a name="fail-over-a-subnet"></a>Egy alhálózattal sikertelen

A teljes alhálózat élni a felhőbe archivált alhálózati ténylegesen nyújtás nélkül átveheti. Ebben a megoldásban alhálózat érhető el a forrás- vagy a célként megadott helyen, de nem mindkettőt egyszerre.

- A feladatátvétel esetén az IP-címtér fenntartása programozott módon rendezheti az útválasztó infrastruktúrára helyezhetik át alhálózataikat egyik helyről egy másikra.
- A feladatátvétel esetén a társított virtuális gépek alhálózatok helyezze át.
- A fő a hátránya hátránya, hogy hiba esetén, akkor helyezze át a teljes alhálózathoz.

#### <a name="example"></a>Példa

Íme egy példa a teljes alhálózat feladatátvételi. 

- A feladatátvétel előtt az elsődleges hely rendelkezik alhálózati 192.168.1.0/24 futó alkalmazások.
- A feladatátvétel során a virtuális gépeket az alhálózaton feladatátvétel történt a másodlagos helyre, és tartsa meg az IP-címét. 
- Helyek közötti útvonalak érdekében, hogy az összes virtuális alhálózati 192.168.1.0/24 most áthelyezte a másodlagos hely módosítani kell.

Az alábbi ábrán az alhálózatok előtt, és a feladatátvétel után.


**Feladatátvétel előtt**

![Feladatátvétel előtt](./media/hyper-v-vmm-networking/network-design2.png)

**A feladatátvételt követően**

![A feladatátvételt követően](./media/hyper-v-vmm-networking/network-design3.png)

Feladatátvétel után a Site Recovery IP-címet a virtuális Gépre mindegyik hálózati interfész osztja ki. A cím le van foglalva a releváns hálózati minden egyes Virtuálisgép-példány a statikus IP-címkészletből.

- Ha az IP-címkészlet a másodlagos hely ugyanaz, mint a forrás, a Site Recovery foglal le, azonos IP-címe (a forrás virtuális gép), a replika virtuális Gépre. Az IP-cím van fenntartva a VMM-ben, de azt nem állítja be a feladatátvételi IP-címet a Hyper-V gazdagépen. A feladatátvételi IP-címet egy Hyper-v gazdagépen a feladatátvétel előtt van beállítva.
- Az azonos IP-cím nem érhető el, ha a Site Recovery lefoglalja egy másik elérhető IP-cím a készlet.
- Ha a virtuális gépek DHCP használatára, a Site Recovery nem kezelése az IP-címek. Ellenőrizze, hogy a DHCP-kiszolgáló a másodlagos helyen is lefoglalhat címeket ugyanazzal a tartománnyal, mint amit a forráshely van szüksége.

### <a name="validate-the-ip-address"></a>Az IP-cím ellenőrzése

A virtuális gépek védelmének engedélyezése után segítségével mintaparancsfájl követően ellenőrizze a virtuális Géphez rendelt címét. Az IP-cím állítja be a feladatátvételi IP-címet, és a feladatátvétel idején a virtuális Géphez rendelt:

    ```
    $vm = Get-SCVirtualMachine -Name <VM_NAME>
    $na = $vm[0].VirtualNetworkAdapters>
    $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
    $ip.address 
    ```

## <a name="use-a-different-ip-address"></a>Különböző IP-cím

Ebben a forgatókönyvben az IP-címek a virtuális gépeket, a feladatátvétel módosult. Ez a megoldás hátránya a karbantartási feladat.  DNS- és gyorsítótár-bejegyzéseket előfordulhat, hogy frissítenie kell. Ennek eredményeként állásidő, amely mérsékelhető a következőképpen:

- Intranetes alkalmazások alacsony TTL értéket használjon.
- A következő parancsfájl használata a Site Recovery helyreállítási tervet, egy időben a DNS-kiszolgáló frissítése. A parancsfájl nincs szükség, ha dinamikus DNS-regisztrációt.

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

Ebben a példában az elsődleges és másodlagos helyek között tudunk különböző IP-címeket, és egy harmadik hely, mely alkalmazások, az elsődleges vagy tartalék üzemeltetett webhely is elérhetők.

- A feladatátvétel előtt az alkalmazások legyenek az elsődleges helyen futtatott alhálózati 192.168.1.0/24.
- A feladatátvétel után alkalmazások alhálózati 172.16.1.0/24 a másodlagos helyen vannak konfigurálva.
- Összes három helyen képesek elérni egymást.
- Feladatátvétel után az alkalmazások a helyreállítási alhálózat állítható vissza.
- Ebben az esetben nincs szükség a teljes alhálózat feladatátvételt, és nem végez módosítást szükségesek, konfigurálja újra a VPN- vagy hálózati útvonalak. A feladatátvétel, és bizonyos DNS-frissítések, győződjön meg arról, hogy alkalmazások továbbra is elérhető.
- Ha a DNS dinamikus frissítések engedélyezésére van konfigurálva, a virtuális gépek fognak regisztrálni saját maguk az új IP-címet, amikor elindítja a feladatátvételt követően.

**Feladatátvétel előtt**

![Másik IP-cím - feladatátvétel előtt](./media/hyper-v-vmm-networking/network-design10.png)

**A feladatátvételt követően**

![Másik IP-cím - feladatátvételt követően](./media/hyper-v-vmm-networking/network-design11.png)


## <a name="next-steps"></a>További lépések

[Feladatátvétel futtatása](hyper-v-vmm-failover-failback.md)

