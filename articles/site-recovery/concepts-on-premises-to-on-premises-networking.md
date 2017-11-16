---
title: "Az IP-címzést, az Azure Site Recovery egy másodlagos helyre a feladatátvételt követően csatlakozni |} Microsoft Docs"
description: "Ismerteti, hogyan állíthatja be az IP-címzés, a virtuális gépek csatlakoztatása az Azure Site Recovery egy másodlagos helyre a feladatátvételt követően."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: prateek9us
editor: 
ms.assetid: 67d73590-185c-49b2-a097-597bf54747a9
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: pratshar
ms.openlocfilehash: 6baeda08b1c41cc024a02f51ca27be2829c46962
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="set-up-ip-addressing-to-connect-after-failover-to-a-secondary-site"></a>Az IP-címzés egy másodlagos helyre a feladatátvételt követően kapcsolódni

Miután megtekintette a telepítés előfeltételeit, olvassa el ebben a cikkben tervezze meg a hálózati bővített végez replikálást a Hyper-V virtuális gépek (VM) kezelése a System Center Virtual Machine Manager (VMM) felhők, egy másodlagos hely használatával [Azure Site Recovery](site-recovery-overview.md) az Azure portálon. 

A cikk elolvasása után felmerülő megjegyzéseit alul vagy az [Azure Recovery Services fórumban](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) teheti közzé.

## <a name="overview"></a>Áttekintés

A replikációs és feladatátvételi stratégiát tervezésekor az alábbiakhoz hasonló fontos kérdések egyike a másodpéldány csatlakoztatása a feladatátvételt követően. Több lehetősége van: 

- **Különböző IP-cím**: kiválaszthatja, hogy a replikált virtuális gép egy másik IP-cím használatára. Ebben a forgatókönyvben a virtuális Gépet egy új IP-cím beolvasása feladatátvétel után, és egy DNS-frissítés szükséges.
- **Tartsa meg ugyanazt a címet**: érdemes használja az ugyanazon IP-címet a replika virtuális Gépre. Megőrzi az azonos IP-cím címek egyszerűbbé teszi a helyreállítást csökkentésével hálózattal kapcsolatos problémákat a feladatátvételt követően. 

## <a name="retaining-ip-addresses"></a>IP-címek megőrzése

Szeretné megőrizni az IP-címeket az elsődleges helyről másodlagos helyre a feladatátvételt követően, ha a teljes alhálózat feladatátvétellel, és új helyének megadására az IP-címek az útvonalak frissítése, vagy alternatív telepítheti a felhőbe archivált alhálózat az elsődleges és a helyreállítási helyek között.

### <a name="stretched-subnet"></a>Felhőbe archivált alhálózati

Felhőbe archivált alhálózat az alhálózati érhető el egyszerre az elsődleges és másodlagos helyen. Ha a kiszolgáló és az IP-(3. rétegbeli) konfigurációja helyez át a másodlagos hely, a hálózati irányítja a forgalmat az új helyre automatikusan. 

2. rétegbeli (adatrétege hivatkozás) szempontjából van szüksége, melyekkel kezelhető a felhőbe archivált VLAN hálózati berendezések. Emellett a VLAN nyújtsák, a potenciális tartalék tartomány terjeszti ki mindkét helyhez, tulajdonképpen a hibaérzékeny pontok kialakulását váljon. Ez nem egy nem valószínű, akkor fordulhat elő, hogy egy szórási storm elindult, és nem lehet elkülönített. 


### <a name="subnet-failover"></a>Alhálózati feladatátvétel

Alhálózati feladatátvételt az a felhőbe archivált alhálózati élni ténylegesen nyújtás nélkül is futtathatja. Ebben a megoldásban alhálózat lesz elérhető a forrás vagy a célként megadott helyen, de nem mindkét helyen egyszerre. A feladatátvétel esetén az IP-címtér fenntartása programozott módon rendezheti az útválasztó-infrastruktúra az alhálózatok áthelyezése egyik helyről egy másikra. Miután a feladatátvétel esetén áthelyezné alhálózatok, a társított virtuális gépek. A fő hátránya, hogy hiba esetén, hogy a teljes alhálózat áthelyezése.

### <a name="example"></a>Példa

Íme egy példa a teljes alhálózat feladatátvételi. Az elsődleges hely rendelkezik alhálózati 192.168.1.0/24 futó alkalmazások. Feladatátvétel során az alhálózatban lévő virtuális gépek feladatátvétel történt a másodlagos helyre, és tartsa meg az IP-címét. Útvonalak érdekében, hogy a virtuális gép virtuális gépeire tartozó alhálózati 192.168.1.0/24 most áthelyezte a másodlagos hely módosítani kell.

A következő grafikus megjelenítése az alhálózatok, előtt, és a feladatátvétel után:

- A feladatátvétel előtt alhálózati 192.168.0.1/24 aktív a forráshelyen, aktívvá válik a másodlagos helyre a feladatátvételt követően.
- Az elsődleges hely és a helyreállítási hely, harmadik és elsődleges hely és harmadik hely és helyreállítási hely között útvonalakat megfelelően módosítani kell.

**Feladatátvétel előtt**

![Feladatátvétel előtt](./media/vmm-to-vmm-walkthrough-network/network-design2.png)

**A feladatátvételt követően**

![A feladatátvételt követően](./media/vmm-to-vmm-walkthrough-network/network-design3.png)

A feladatátvétel után ez történik:

- A Site Recovery foglal le, a megfelelő hálózatot, minden VMM-példányt a statikus IP-címkészletből IP-címet a virtuális Gépre, mindegyik hálózati interfész.
- Ha az IP-címkészlet a másodlagos hely ugyanaz, mint a forrás, a Site Recovery azonos IP-címe (a forrás virtuális gép) a replika virtuális Gépre foglal le. Az IP-cím van fenntartva a VMM-ben, de azt nem állítja be a feladatátvételi IP-címet a Hyper-V gazdagépen. A feladatátvételi IP-címet egy Hyper-v gazdagépen a feladatátvétel előtt van beállítva.
- Az azonos IP-cím nem érhető el, ha a Site Recovery lefoglalja egy másik elérhető IP-cím a készlet.
- Ha a virtuális gépek DHCP használatára, a Site Recovery nem kezelése az IP-címek. Ellenőrizze, hogy a DHCP-kiszolgáló a másodlagos helyen is címet lefoglalni a ugyanazzal a tartománnyal, mint amit a forráshely van szüksége.

### <a name="validate-the-ip-address"></a>Az IP-cím ellenőrzése

A virtuális gépek védelmének engedélyezése után segítségével mintaparancsfájl követően ellenőrizze a virtuális Géphez rendelt címét. Az azonos IP-cím állítja be a feladatátvételi IP-címet, és a feladatátvétel idején a virtuális Géphez rendelt:

    ```
    $vm = Get-SCVirtualMachine -Name <VM_NAME>
    $na = $vm[0].VirtualNetworkAdapters>
    $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
    $ip.address 
    ```

## <a name="changing-ip-addresses"></a>IP-címek módosítása

Ebben a forgatókönyvben az IP-címek a virtuális gépeket, a feladatátvétel módosult. Ez a megoldás hátránya a karbantartási feladat. Általában a DNS fog frissülni után a replika virtuális gép elindításához. DNS-bejegyzések módosítania kell módosítani vagy fluster tartalmazza, és a gyorsítótárazott bejegyzés frissítése. Ez állásidőt eredményezhet. Állásidő mérsékelhető a következőképpen:

- Intranetes alkalmazások alacsony TTL értéket használjon.
- A Site Recovery helyreállítási tervet, a következő parancsfájl segítségével frissítse a DNS-kiszolgáló egy időben történő frissítés biztosításához. A parancsfájl nincs szükség, ha dinamikus DNS-regisztrációt.

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

Vizsgáljuk meg egy olyan forgatókönyvet, amelyben arra készül, különböző IP-címek használatát az elsődleges és a helyreállítási helyen. Ebben a példában az elsődleges és másodlagos helyek között tudunk különböző IP-címeket, és; s harmadik webhely, az elsődleges vagy tartalék hely futó alkalmazásokat is elérhetők.

- A feladatátvétel előtt alkalmazások üzemeltetett alhálózati 192.168.1.0/24 az elsődleges helyen, és alhálózati 172.16.1.0/24 a másodlagos helyen találhatók egy feladatátvétel után van konfigurálva.
- VPN-kapcsolatok és a hálózati útvonalak konfigurálva megfelelően, hogy az összes három helyen képesek elérni egymást.
- Feladatátvétel után az alkalmazások a helyreállítási alhálózat állítható vissza. Ebben az esetben nincs szükség a teljes alhálózat feladatátvételt, és nem végez módosítást szükségesek, konfigurálja újra a VPN- vagy hálózati útvonalak. A feladatátvétel, és bizonyos DNS-frissítések, győződjön meg arról, hogy alkalmazások továbbra is elérhető.
- Ha a DNS dinamikus frissítések engedélyezésére van konfigurálva, a virtuális gépek fognak regisztrálni saját maguk az új IP-címet, amikor elindítja a feladatátvételt követően.

**Feladatátvétel előtt**

![Különböző IP - feladatátvétel előtt](./media/vmm-to-vmm-walkthrough-network/network-design10.png)

**A feladatátvételt követően**

![Különböző IP - feladatátvételt követően](./media/vmm-to-vmm-walkthrough-network/network-design11.png)




