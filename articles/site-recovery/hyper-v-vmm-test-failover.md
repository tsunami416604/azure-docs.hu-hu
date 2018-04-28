---
title: A vész-Helyreállítási részletezési Hyper-V virtuális gépek futtatásához egy másodlagos helyre Azure Site Recovery segítségével |} Microsoft Docs
description: Ismerje meg, a vész-Helyreállítási részletezési futtatása Hyper-V virtuális gépek VMM-felhőkben egy másodlagos adatközpontba Azure Site Recovery segítségével.
services: site-recovery
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: article
ms.date: 02/12/2018
ms.author: ponatara
ms.openlocfilehash: c389776f62db5fd04f67ef22822e21fd4aee368f
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="run-a-dr-drill-for-hyper-v-vms-to-a-secondary-site"></a>A vész-Helyreállítási részletezési futtatása Hyper-V virtuális gépek egy másodlagos helyre


Ez a cikk ismerteti a vész-helyreállítási részletezési egy másodlagos helyszíni helyre, a System Center Virtual Machine Manager V(MM) felhőkben felügyelt Hyper-V virtuális gépek használatával [Azure Site Recovery](site-recovery-overview.md).

A replikációs stratégiájának érvényesítéséhez feladatátvételi teszt futtatása, és hajtsa végre a vész-Helyreállítási részletezési adatvesztés vagy leállás nélkül. Feladatátvételi teszt nem rendelkezik gyakorolt hatás, a folyamatban lévő replikáció, vagy az éles környezetben. 

## <a name="how-do-test-failovers-work"></a>Hogyan teszt feladatátadás munkahelyi?

Az elsődleges feladatátvételi teszt futtatása a másodlagos helyen. Ha egyszerűen ellenőrizni kívánja, hogy a virtuális gépek átadja a feladatokat, a feladatátvételi tesztet a másodlagos helyen semmit beállítása nélkül is futtathatja. Ha szeretné ellenőrizni a feladatátvételt az alkalmazás akkor működik, várt módon, szüksége lesz a hálózati és infrastrukturális beállítása a másodlagos helyen.
- Egy virtuális, vagy a feladatátvételi teszt futtathatja egy [helyreállítási terv](site-recovery-create-recovery-plans.md).
- Feladatátvételi teszt hálózat nélkül is futtathatja, egy meglévő hálózati, vagy egy automatikusan létrehozott hálózati. Ezekről a beállításokról további részletek az alábbi táblázatban szerepelnek.
    - Feladatátvételi teszt hálózat nélkül is futtathatja. Ez a beállítás akkor hasznos, ha egyszerűen ellenőrizni kívánja, hogy a virtuális gépek a feladatátvétel képes volt, de nem fogja tudni igazolni a hálózati konfigurációt.
    - A feladatátvétel futtatása egy meglévő hálózattal. Azt javasoljuk, hogy ne használja éles hálózati környezetben.
    - A feladatátvétel futtatása, és lehetővé teszik a Site Recovery automatikusan hozzon létre egy tesztelési hálózatot. Ebben az esetben a Site Recovery létrehoz a hálózati automatikusan, és ha a feladatátvételi teszt befejeződött tisztítása.
- Válasszon ki egy helyreállítási pontot a teszt feladatátvételhez kell: 
    - **Legújabb feldolgozott**: Ez a beállítás átkerül a virtuális gép a legutóbbi helyreállítási pontot, a Site Recovery által feldolgozott. Ez a lehetőség alacsony helyreállítási időre vonatkozó célkitűzést (RTO) nyújt, mert a rendszer nem tölt időt a feldolgozatlan adatok feldolgozásával.
    - **Legutóbbi alkalmazáskonzisztens**: a Site Recovery által feldolgozott ezt a beállítást átkapcsolás egy virtuális Gépet a legújabb alkalmazáskonzisztens helyreállítási pontnak. 
    - **Legújabb**: Ez a beállítás először dolgozza fel a el lett küldve a Site Recovery szolgáltatásban, a helyreállítási pont létrehozása az egyes virtuális gépek, mielőtt hibát jelentene keresztül hozzá összes adatot. Ezt a lehetőséget biztosít a legalacsonyabb helyreállítási Időkorlát (Helyreállításipont-célkitűzés), mert a virtuális gép létrehozása után feladatátvételi kell replikálni a Site Recovery szolgáltatásban a feladatátvétel kiváltásakor összes adatot.
    - **Legújabb virtuális Gépre kiterjedő feldolgozott**: egy vagy több virtuális gépek, amelyeken engedélyezve van a virtuális Gépre kiterjedő konzisztencia tartalmazó helyreállítási terveket érhető el. A beállítás engedélyezve van a virtuális gépek átadja a virtuális Gépre kiterjedő legújabb közös alkalmazáskonzisztens helyreállítási pontnak. Más virtuális gépek feladatátvételt a legutóbbi feldolgozott helyreállítási pontot.
    - **Legújabb virtuális Gépre kiterjedő alkalmazáskonzisztens**: Ez a beállítás érhető el a helyreállítási terv legalább egy virtuális gépek, amelyeken engedélyezve van a virtuális Gépre kiterjedő konzisztencia. A virtuális Gépre kiterjedő legutóbbi közös alkalmazáskonzisztens helyreállítási pontnak feladatátvételt a replikálási csoport részét képező virtuális gépek. Más virtuális gépek feladatátvételt a legutóbbi alkalmazáskonzisztens helyreállítási pontnak.
    - **Egyéni**: használja ezt a beállítást egy adott VM adott helyreállítási pont létrehozása a feladatátvételt.



## <a name="prepare-networking"></a>Készítse elő a hálózatkezelés

Feladatátvételi teszt futtatásakor azonosítóját, válassza ki a teszt replika gépek hálózati beállításait a táblázatban foglaltak szerint.

**A beállítás** | **Részletek** 
--- | --- 
**Egyik sem** | A teszt virtuális gép létrejön az állomás, amelyen a replika virtuális gép található. Nem adnak a felhőhöz, és nem csatlakozik a hálózathoz.<br/><br/> Csatlakozhat a gépet egy Virtuálisgép-hálózat létrehozása után.
**Meglévő** | A teszt virtuális gép létrejön az állomás, amelyen a replika virtuális gép található. Még nincs hozzáadva a felhőbe.<br/><br/>Az éles hálózattól elkülönített Virtuálisgép-hálózatot létrehozni.<br/><br/>A VLAN-alapú hálózati használata, azt javasoljuk, hogy hozzon létre egy külön logikai hálózat (nincs használatban az üzemi) a VMM-ben erre a célra. Ezt a logikai hálózatot a feladatátvételi tesztet a Virtuálisgép-hálózatok létrehozásához használt.<br/><br/>A logikai hálózat legalább egy virtuális gép összes Hyper-V-kiszolgáló a hálózati adapterek társítva kell lennie.<br/><br/>A VLAN logikai hálózatok a hálózati helyeket a logikai hálózat ad hozzá, el kell különíteni.<br/><br/>Windows Hálózatvirtualizálás-alapú logikai hálózat használata, az Azure Site Recovery automatikusan létrehozza elszigetelt Virtuálisgép-hálózatok. 
**Hálózat létrehozása** | Egy ideiglenes teszthálózatot használ a megadott beállítás alapján automatikusan jön létre **logikai hálózati** és a kapcsolódó hálózati helyeket.<br/><br/> Feladatátvételi ellenőrzi, hogy a virtuális gépek jönnek létre. |Ezt a beállítást kell használnia, ha a helyreállítási terv egynél több Virtuálisgép-hálózatot használ.<br/><br/> Windows Hálózatvirtualizálás hálózatok használata, ezt a beállítást automatikusan hozhat létre Virtuálisgép-hálózat (alhálózat és IP-címkészletek) ugyanazokat a beállításokat, a hálózat, a replika virtuális gép. Automatikusan kiüríti a Virtuálisgép-hálózatok a feladatátvételi teszt befejeződése után.<br/><br/> A teszt virtuális gép létrehozása a gazdagépen, amelyen a replika virtuális gép található. Még nincs hozzáadva a felhőbe.

### <a name="best-practices"></a>Ajánlott eljárások

- A termelési számítási feladatokhoz állásidő tesztelését egy üzemi hálózat okoz. Kérje meg a felhasználókat, hogy nem használja a kapcsolódó alkalmazások, ha a vész-helyreállítási részletezési folyamatban van.

- A tesztelési célú hálózat felel meg a teszt feladatátvételhez használt VMM logikai hálózat típusa nem szükséges. Azonban néhány kombináció nem működnek:

     - Ha a replika használ a DHCP- és a VLAN-alapú elkülönítés, a Virtuálisgép-hálózatot a replika egy statikus IP-címkészlet nem szükséges. A teszt feladatátvételhez Windows Hálózatvirtualizálás használatával nem fognak működni, mert nincs címkészletek érhetők el. 
        
     - A feladatátvételi teszt nem fog működni, ha a replika nincs elkülönítés használ, és a vizsgálat a Windows-hálózat Hálózatvirtualizálást használ. Ennek az az oka az elkülönítés nélküli hálózat nem rendelkezik az alhálózatok, a Windows Hálózatvirtualizálás hálózat létrehozásához szükséges.
        
- Azt javasoljuk, hogy nem használja a kiválasztott hálózati hálózatra való leképezés, a feladatátvételi teszthez.

- A Virtuálisgép-hálózatok leképezése után feladatátvételi, attól függ, hogyan lett konfigurálva a Virtuálisgép-hálózatot a VMM-konzol hogyan kapcsolódnak a replika virtuális gépeket.


### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>Nincs elkülönítés vagy VLAN-elkülönítéssel Virtuálisgép-hálózatot

Ha nincs elkülönítés vagy VLAN-elkülönítéssel Virtuálisgép-hálózatot a VMM-ben van konfigurálva, vegye figyelembe a következőket:

- DHCP meg van adva a Virtuálisgép-hálózathoz, ha a replika virtuális gép csatlakozik-e át a beállításokat, a társított logikai hálózatot a hálózati hely meg van adva a VLAN-Azonosítót. A virtuális gép megkapja az IP-címét a rendelkezésre álló DHCP-kiszolgálótól.
- Adja meg a célként megadott Virtuálisgép-hálózathoz statikus IP-címkészlet nem kell. Egy statikus IP-címkészletet a Virtuálisgép-hálózat használata esetén a replika virtuális gép csatlakozik-e át a beállításokat, a társított logikai hálózatot a hálózati hely meg van adva a VLAN-Azonosítót.
- A virtuális gép kap az IP-címét, amely a Virtuálisgép-hálózathoz van definiálva. Ha egy statikus IP-címkészletet a célként megadott Virtuálisgép-hálózat nincs megadva, az IP-címek hozzárendelését sikertelen lesz. Az IP-címkészlet létrehozása a védelmi és helyreállítási használni mind a forrás- és a VMM-kiszolgálókon.

### <a name="vm-network-with-windows-network-virtualization"></a>Virtuálisgép-hálózat Windows Hálózatvirtualizálás

Ha egy Virtuálisgép-hálózatot a VMM-ben Windows Hálózatvirtualizálás van konfigurálva, vegye figyelembe a következőket:

- Meg kell határozni a célként megadott Virtuálisgép-hálózatot, függetlenül attól, hogy a forrás Virtuálisgép-hálózat használatára van konfigurálva DHCP vagy statikus IP-címkészletet statikus címkészletet. 
- DHCP határozza meg, ha a célként megadott VMM-kiszolgáló DHCP-kiszolgálóként működik, és a célként megadott Virtuálisgép-hálózat IP-címet a készlet definiált biztosít.
- A forráskiszolgáló meg van adva egy statikus IP-címkészlet használatát, ha a célként megadott VMM-kiszolgáló IP-címet a készlet foglal le. Mindkét esetben IP-címek hozzárendelését sikertelen lesz, ha nincs megadva egy statikus IP-címkészletet.



## <a name="prepare-the-infrastructure"></a>Az infrastruktúra előkészítése

Ha egyszerűen ellenőrizni kívánja, hogy a virtuális gépek feladatátvétel, a teszt feladatátvétel infrastruktúra nélkül is futtathatja. Ha szeretné a teljes vész-Helyreállítási részletezési app feladatátvétel tesztelése, készítse elő az infrastruktúrát a másodlagos helyen kell:

- Ha meglévő hálózat használatával feladatátvételi teszt futtatása, készítse elő az Active Directory, a DHCP és DNS erre a hálózatra.
- Ha automatikusan létrehozhat egy Virtuálisgép-hálózatot a feladatátvételi tesztet futtatja, akkor infrastruktúra erőforrások hozzáadása az automatikusan létrehozott hálózati, a feladatátvételi teszt futtatása előtt. A helyreállítási tervet akkor megkönnyítheti Ez egy manuális lépés előtt 1 csoport hozzáadásával a helyreállítási tervet, amely a feladatátvételi teszthez használni fog. Adja hozzá az infrastruktúrához kapcsolódó erőforrások az automatikusan létrehozott hálózathoz, a feladatátvételi teszt futtatása előtt.


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

1. Válassza ki **helyreállítási tervek** > *recoveryplan_name*. Kattintson a **feladatátvételi** > **feladatátvételi teszt**.
2. Az a **feladatátvételi teszt** panelen adja meg, hogyan replika virtuális gépek kell csatlakoztatni a hálózatokhoz a feladatátvételi teszt után.
3. A feladatátvételi folyamat előrehaladásának nyomon a **feladatok** fülre.
4. Miután a feladatátvétel befejeződött, győződjön meg arról, hogy a virtuális gép sikeresen elindul-e.
5. Amikor elkészült, kattintson a **karbantartása a feladatátvételi teszt** a a helyreállítási terv. A **Jegyzetek** területen jegyezheti fel és mentheti a feladatátvételi teszttel kapcsolatos megfigyeléseket. Ezt a lépést minden olyan virtuális gépek és a Site Recovery által a feladatátvételi teszt során létrehozott hálózatok törli. 

![A feladatátvételi teszt](./media/hyper-v-vmm-test-failover/TestFailover.png)
 


> [!TIP]
> A virtuális gép teszt feladatátvétele során megadott IP-cím a virtuális gép kapja azonos IP-cím (feltéve, hogy az IP-cím érhető el a feladatátvételi teszt hálózatában) a tervezett vagy nem tervezett feladatátvételre. Ha az azonos IP-cím nem érhető el a feladatátvételi teszt hálózatában lévő, a virtuális gép megkapja egy másik IP-címet, amely elérhető a feladatátvételi teszt hálózatában.



### <a name="run-a-test-failover-to-a-production-network"></a>Feladatátvételi teszt futtatása az üzemi hálózathoz

Azt javasoljuk, hogy nem feladatátvételi teszt termelési helyreállítási hely hálózatához, hálózatra való leképezés során adott meg. De ha a végpontok közötti hálózati kapcsolat a virtuális gép feladatátadása ellenőrizni kell, vegye figyelembe a következő szempontokat:

* Győződjön meg arról, hogy az elsődleges virtuális gép le van állítva akkor, ha a feladatátvételi tesztet. Ha ezt elmulasztja, két virtuális gépek ugyanazzal az identitással fog futni az ugyanazon a hálózaton egy időben. Ilyen esetben nem kívánt következmények vezethet.
* A teszt feladatátvételt végez módosításokat virtuális gépek esetén elveszett távolítja el a feladatátvételi teszt virtuális gépek. Ezek a változások nem replikálódnak vissza az elsődleges virtuális gépeken.
* Ehhez hasonló tesztelése az éles alkalmazás leállása nélkül vezet. Kérje meg a felhasználókat az alkalmazás nem használja az alkalmazást, ha a vész-Helyreállítási részletezési van folyamatban.  


## <a name="next-steps"></a>További lépések
Miután sikeresen futtatta a vész-Helyreállítási részletezési, [teljes feladatátvételt](site-recovery-failover.md).



