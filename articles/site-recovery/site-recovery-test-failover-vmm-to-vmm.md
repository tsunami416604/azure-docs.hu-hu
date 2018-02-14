---
title: "(A VMM VMM) feladatátvétel ellenőrzése az Azure Site Recovery |} Microsoft Docs"
description: "Az Azure Site Recovery koordinálja a replikáció, feladatátvétel és helyreállítási virtuális gépek és fizikai kiszolgálók. További információk a feladatátvétel az Azure vagy egy másodlagos adatközpontba."
services: site-recovery
documentationcenter: 
author: ponatara
manager: abhemraj
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/22/2017
ms.author: ponatara
ms.openlocfilehash: 2730cebc1cdc47db283ae851560d93fdbf50ee48
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="test-failover-vmm-to-vmm-in-site-recovery"></a>(A VMM VMM) feladatátvételi teszt a Site Recovery szolgáltatásban


Ez a cikk bemutatja, és utasításokat a feladatátvételi teszt vagy a vész-helyreállítási részletezési virtuális gépek (VM) és fizikai kiszolgálók, amelyek az Azure Site Recovery védett. A System Center Virtual Machine Manager VMM által felügyelt helyszíni hely fogja használni a helyreállítás helyeként.

Ellenőrizze a replikációs stratégiát, vagy egy vész-Helyreállítási részletezési adatvesztés vagy leállás nélkül végezzen feladatátvételi tesztet futtatja. Feladatátvételi teszt nem rendelkezik gyakorolt hatás, a folyamatban lévő replikáció, illetve az éles környezetben. Futtatható virtuális gép vagy egy [helyreállítási terv](site-recovery-create-recovery-plans.md). Ha a feladatátvételi teszt folyamatban váltanak, meg kell adnia a hálózaton, amelyhez a teszt virtuális gépek csatlakozni fognak. A feladatátvételi teszt előrehaladását követheti a **feladatok** lap.  

Ha megjegyzéseit vagy kérdéseit, post őket a cikk vagy a alján a [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prepare-the-infrastructure-for-test-failover"></a>A feladatátvételi teszthez az infrastruktúra előkészítése
Ha egy meglévő hálózati használatával feladatátvételi tesztet futtatni kívánt, készítse elő az Active Directory, a DHCP és DNS erre a hálózatra.

Ha azt szeretné, a Virtuálisgép-hálózatok automatikus létrehozása a beállítás használatával feladatátvételi teszt futtatásához, adja meg egy manuális lépés előtt csoport-1 a feladatátvételi teszthez használni fog a helyreállítási tervben szereplő. Adja hozzá az infrastruktúrához kapcsolódó erőforrások az automatikusan létrehozott hálózathoz, a feladatátvételi teszt futtatása előtt.

### <a name="things-to-note"></a>Ügyeljen a következőkre
Ha egy másodlagos helyre replikál, a replika gépet használó hálózatnak a típusát, nem kell a megfelelő logikai hálózatot a feladatátvételi teszthez használt típusú, de néhány kombináció, hogy nem működik. Ha a replika használ a DHCP- és a VLAN-alapú elkülönítés, a Virtuálisgép-hálózatot a replika egy statikus IP-címkészlet nem szükséges. A teszt feladatátvételhez Windows Hálózatvirtualizálás használatával nem fognak működni, mert nincs címkészletek érhetők el. 

Emellett a feladatátvételi teszt nem fog működni, ha a replika nincs elkülönítés használ, és a vizsgálat a Windows-hálózat Hálózatvirtualizálást használ. Ennek az az oka az elkülönítés nélküli hálózat nem rendelkezik az alhálózatok, a Windows Hálózatvirtualizálás hálózat létrehozásához szükséges.

A Virtuálisgép-hálózatok leképezése után feladatátvételi, attól függ, hogyan lett konfigurálva a Virtuálisgép-hálózatot a VMM-konzol hogyan kapcsolódnak a replika virtuális gépeket.

#### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>Nincs elkülönítés vagy VLAN-elkülönítéssel Virtuálisgép-hálózatot
DHCP meg van adva a Virtuálisgép-hálózathoz, ha a replika virtuális gép csatlakozik-e át a beállításokat, a társított logikai hálózatot a hálózati hely meg van adva a VLAN-Azonosítót. A virtuális gép megkapja az IP-címét a rendelkezésre álló DHCP-kiszolgálótól. 

Adja meg a célként megadott Virtuálisgép-hálózathoz statikus IP-címkészlet nem kell. Egy statikus IP-címkészletet a Virtuálisgép-hálózat használata esetén a replika virtuális gép csatlakozik-e át a beállításokat, a társított logikai hálózatot a hálózati hely meg van adva a VLAN-Azonosítót.

A virtuális gép kap az IP-címét, amely a Virtuálisgép-hálózathoz van definiálva. Ha egy statikus IP-címkészletet a célként megadott Virtuálisgép-hálózat nincs megadva, az IP-címek hozzárendelését sikertelen lesz. Az IP-címkészlet létrehozása a védelmi és helyreállítási használni mind a forrás- és a VMM-kiszolgálókon.

#### <a name="vm-network-with-windows-network-virtualization"></a>Virtuálisgép-hálózat Windows Hálózatvirtualizálás
Ha egy Virtuálisgép-hálózatot a Windows Hálózatvirtualizálással van konfigurálva, meg kell határozni a célként megadott Virtuálisgép-hálózatot, függetlenül attól, hogy a forrás Virtuálisgép-hálózat használatára van konfigurálva DHCP vagy statikus IP-címkészletet statikus címkészletet. 

DHCP határozza meg, ha a célként megadott VMM-kiszolgáló DHCP-kiszolgálóként működik, és a célként megadott Virtuálisgép-hálózat IP-címet a készlet definiált biztosít. A forráskiszolgáló meg van adva egy statikus IP-címkészlet használatát, ha a célként megadott VMM-kiszolgáló IP-címet a készlet foglal le. Mindkét esetben IP-címek hozzárendelését sikertelen lesz, ha nincs megadva egy statikus IP-címkészletet.


### <a name="prepare-dhcp"></a>DHCP előkészítése
Ha részt vesz a virtuális gépek feladatátvételi teszt DHCP használatára, és egy teszt DHCP-kiszolgáló céljából a feladatátvételi teszt elszigetelt hálózaton belül.

### <a name="prepare-active-directory"></a>Az Active Directory előkészítése
Alkalmazás tesztelése a feladatátvételi teszt futtatása szüksége az Active Directory munkakörnyezetben másolatát a tesztkörnyezetben. További információkért tekintse át a [Active Directoryra vonatkozó feladatátvételi szempontokat részletező cikkben](site-recovery-active-directory.md#test-failover-considerations).

### <a name="prepare-dns"></a>DNS előkészítése
Készítse elő egy DNS-kiszolgáló a teszt feladatátvételhez a következőképpen:

* **DHCP**: virtuális gépek DHCP használatára, ha a vizsgálat DNS IP-címét frissíteni kell a teszt DHCP-kiszolgálón. Ha egy hálózati Windows Hálózatvirtualizálás típusú használ, a VMM-kiszolgáló a DHCP-kiszolgáló funkcionál. Ezért DNS IP-címét frissíteni kell a feladatátvételi teszt hálózatában. Ebben az esetben a virtuális gépek regisztrálják magukat a megfelelő DNS-kiszolgálót.
* **Statikus cím**: Ha a virtuális gépek egy statikus IP-címet használja, a teszt DNS-kiszolgáló IP-címét frissíteni kell a feladatátvételi teszt hálózatában. Szükség lehet a DNS frissíteni a teszt virtuális gépek IP-címét. Az alábbi mintaparancsfájl erre a célra használhatja:

        Param(
        [string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása
Ez az eljárás ismerteti a helyreállítási terv feladatátvételi teszt futtatása. Alternatív megoldásként futtathatja egyetlen virtuális gép a feladatátvételt a **virtuális gépek** fülre.

![Teszt feladatátvételi panel](./media/site-recovery-test-failover-vmm-to-vmm/TestFailover.png)

1. Válassza ki **helyreállítási tervek** > *recoveryplan_name*. Kattintson a **feladatátvételi** > **feladatátvételi teszt**.
1. Az a **feladatátvételi teszt** panelen adja meg, hogyan virtuális gépek kell csatlakoztatni a hálózatokhoz a feladatátvételi teszt után. További információkért lásd: a [hálózati beállítások](#network-options-in-site-recovery).
1. A feladatátvételi folyamat előrehaladásának nyomon a **feladatok** fülre.
1. Miután a feladatátvétel befejeződött, győződjön meg arról, hogy a virtuális gép sikeresen elindul-e.
1. Amikor elkészült, kattintson a **karbantartása a feladatátvételi teszt** a a helyreállítási terv. A **megjegyzések**, és a feladatátvételi teszttel kapcsolatos megfigyelések feljegyzéséhez mentéséhez. Ez a lépés törli a virtuális gépek és a hálózatokban, amelyek a feladatátvételi teszt során lettek létrehozva.


## <a name="network-options-in-site-recovery"></a>A Site Recovery szolgáltatásban a hálózati beállítások

Feladatátvételi teszt futtatásakor azonosítóját, válassza ki a teszt replika gépek hálózati beállításait. Több lehetősége van.  

| **Teszt feladatátvételi beállítás** | **Leírás** | **Feladatátvétel ellenőrzése** | **Részletek** |
| --- | --- | --- | --- |
| **Feladatok átadása a másodlagos VMM-hely – hálózati nélkül** |Ne válassza ki a Virtuálisgép-hálózatot. |Feladatátvételi ellenőrzi, hogy a teszt gépek jönnek létre.<br/><br/>A teszt virtuális gép a gazdagépen, amelyen a replika virtuális gép létezik jön létre. Azt nem adnak a felhőhöz, ha a replika virtuális gép is található. |<p>A átvevő gép nem csatlakozik a hálózathoz.<br/><br/>A gép lehet csatlakoztatni a Virtuálisgép-hálózatok létrehozása után. |
| **Feladatok átadása a másodlagos VMM-hely--hálózattal** |Válasszon egy meglévő Virtuálisgép-hálózatot. |Feladatátvételi ellenőrzi, hogy a virtuális gépek jönnek létre. |A teszt virtuális gép a gazdagépen, amelyen a replika virtuális gép létezik jön létre. Azt nem adnak a felhőhöz, ha a replika virtuális gép is található.<br/><br/>Az éles hálózattól elkülönített Virtuálisgép-hálózatot létrehozni.<br/><br/>A VLAN-alapú hálózati használata, azt javasoljuk, hogy hozzon létre egy külön logikai hálózat (nincs használatban az üzemi) a VMM-ben erre a célra. Ezt a logikai hálózatot a feladatátvételi tesztet a Virtuálisgép-hálózatok létrehozásához használt.<br/><br/>A logikai hálózat legalább egy virtuális gép összes Hyper-V-kiszolgáló a hálózati adapterek társítva kell lennie.<br/><br/>A VLAN logikai hálózatok a hálózati helyeket a logikai hálózat ad hozzá, el kell különíteni.<br/><br/>Windows Hálózatvirtualizálás-alapú logikai hálózat használata, az Azure Site Recovery automatikusan létrehozza elszigetelt Virtuálisgép-hálózatok. |
| **Feladatok átadása a VMM másodlagos hely – hozzon létre egy hálózati** |Egy ideiglenes teszthálózatot használ a megadott beállítás alapján automatikusan jön létre **logikai hálózati** és a kapcsolódó hálózati helyeket. |Feladatátvételi ellenőrzi, hogy a virtuális gépek jönnek létre. |Használja ezt a beállítást, ha a helyreállítási terv egynél több Virtuálisgép-hálózatot használ. Windows Hálózatvirtualizálás hálózatok használata, ezt a beállítást automatikusan hozhat létre Virtuálisgép-hálózat (alhálózat és IP-címkészletek) ugyanazokat a beállításokat, a hálózat, a replika virtuális gép. Automatikusan kiüríti a Virtuálisgép-hálózatok a feladatátvételi teszt befejeződése után.</p><p>A teszt virtuális gép a gazdagépen, amelyen a replika virtuális gép létezik jön létre. Azt nem adnak a felhőhöz, ha a replika virtuális gép is található. |

> [!TIP]
> A virtuális gép teszt feladatátvétele során megadott IP-cím a virtuális gép kapja azonos IP-cím (feltéve, hogy az IP-cím érhető el a feladatátvételi teszt hálózatában) a tervezett vagy nem tervezett feladatátvételre. Ha az azonos IP-cím nem érhető el a feladatátvételi teszt hálózatában lévő, a virtuális gép megkapja egy másik IP-címet, amely elérhető a feladatátvételi teszt hálózatában.
>
>


## <a name="test-failover-to-a-production-network-on-a-recovery-site"></a>Feladatátvételi teszt végrehajtásához éles hálózati környezetben a helyreállítási helyen
Azt javasoljuk, hogy akkor, ha feladatátvételi tesztet, akkor válasszon egy hálózatot, amely eltér a termelési helyreállítási helyen lévő hálózat a megadott [hálózatleképezés](https://docs.microsoft.com/azure/site-recovery/site-recovery-network-mapping). Azonban ha valóban szeretné érvényesíteni a végpontok közötti hálózati kapcsolat átvevő virtuális gépen, vegye figyelembe a következő szempontokat:

* Győződjön meg arról, hogy az elsődleges virtuális gép állítsa le, akkor, ha a feladatátvételi tesztet. Ha ezt elmulasztja, két virtuális gépek ugyanazzal az identitással fog futni az ugyanazon a hálózaton egy időben. Ilyen esetben nem kívánt következmények vezethet.
* Feladatátvételi teszt virtuális gépek elvégzett módosítások elvesznek, ha feladatátvételi teszt virtuális gépek törölheti. Ezeket a módosításokat a rendszer nem replikálja az elsődleges virtuális gépre.
* A módszerre tesztelés eredménye az éles alkalmazás leállása nélkül. Kérje meg a felhasználókat az alkalmazás nem használja az alkalmazást, ha a vész-Helyreállítási részletezési van folyamatban.  


## <a name="next-steps"></a>Következő lépések
Próbálkozás után sikeresen futtatta a feladatátvételi tesztet, egy [feladatátvételi](site-recovery-failover.md).
