---
title: Futtassa a Hyper-V virtuális gépek vészhelyreállítási próbát egy másodlagos helyre az Azure Site Recoveryvel |} A Microsoft Docs
description: Ismerje meg, hogyan vész-helyreállítási futtassa a VMM-felhőkben futó Hyper-V virtuális gépek egy másodlagos helyszíni adatközpontba, az Azure Site Recovery használatával.
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: ponatara
ms.openlocfilehash: 3f7e534e9c698e31e1061c35aec713d20c7e570f
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50211349"
---
# <a name="run-a-dr-drill-for-hyper-v-vms-to-a-secondary-site"></a>Vész-helyreállítási futtatása Hyper-V virtuális gépek másodlagos helyre


Ez a cikk bemutatja, hogyan teheti a vészhelyreállítási (DR) próbát egy másodlagos helyszíni helyre, a System Center Virtual Machine Manager V(MM) felhőkben felügyelt Hyper-V virtuális gépek használatával [Azure Site Recovery](site-recovery-overview.md).

Futtasson egy feladatátvételi tesztet, ellenőrizze a replikációs stratégiáját, és hajtsa végre a Vészhelyreállítás a próba adatveszteség és állásidő nélkül. Feladatátvételi teszt nincs hatással a folyamatban lévő replikáció vagy az éles környezetben. 

## <a name="how-do-test-failovers-work"></a>Hogyan teszt feladatátvétel munkahelyi?

Az elsődleges kiszolgálóról a másodlagos hely futtatni egy feladatátvételi tesztet. Egyszerűen szeretne ellenőrizni, hogy a virtuális gép feladatátadásakor, ha a másodlagos helyen bármit beállítása nélkül futtathatja egy feladatátvételi tesztet. Ha szeretné ellenőrizni a feladatátvételt az alkalmazás akkor működik, a várt módon, szüksége lesz a másodlagos helyen lévő hálózati és infrastruktúra beállítása.
- Az egyetlen virtuális gép vagy a feladatátvételi teszt futtathatja egy [helyreállítási terv](site-recovery-create-recovery-plans.md).
- Feladatátvételi teszt hálózat nélkül futtathatja a meglévő hálózaton, vagy egy automatikusan létrehozott hálózati. Ezek a lehetőségek további információt az alábbi táblázatban szerepelnek.
    - Feladatátvételi teszt hálózat nélkül futtathatja. Ez a beállítás akkor hasznos, ha egyszerűen ellenőrizni szeretné, hogy a virtuális gép a feladatátvételt képes volt, de nem tudja ellenőrizni a hálózati konfigurációt.
    - A feladatátvétel futtatása a meglévő hálózaton. Azt javasoljuk, hogy ne használja éles hálózati környezetben.
    - A feladatátvétel futtatása, és bízza a Site Recovery automatikusan létrehoz egy tesztelési hálózatot. Ebben az esetben a Site Recovery fog hozzon létre automatikusan a hálózaton, és amikor elkészült a feladatátvételi teszt karbantartása.
- Ki kell választania egy helyreállítási pontot a feladatátvételi tesztet: 
    - **Legutóbb feldolgozott**: Ez a beállítás a virtuális gép feladatait a Site Recovery által feldolgozott legutóbbi helyreállítási pontot. Ez a lehetőség alacsony helyreállítási időre vonatkozó célkitűzést (RTO) nyújt, mert a rendszer nem tölt időt a feldolgozatlan adatok feldolgozásával.
    - **Legutóbbi alkalmazáskonzisztens**: Ez a beállítás sikertelen, egy virtuális Gépet, a legutóbbi alkalmazáskonzisztens helyreállítási pontnak adja át a Site Recovery által feldolgozott. 
    - **Legújabb**: Ez a lehetőség először feldolgozza a helyreállítási pont létrehozása előtt feladatátadás azt a virtuális gépek Site Recovery szolgáltatásba küldött összes adat. Ezt a lehetőséget biztosít a a legkisebb helyreállítási Időkorlát (helyreállítási időkorlát), mert a virtuális gép létrehozása után a feladatátvételi lesz a feladatátvétel elindításakor a Site Recoverybe replikált összes adatot.
    - **Legújabb több virtuális gépre kiterjedő feldolgozott**: egy vagy több virtuális gépek, amelyeken engedélyezve van a virtuális gépre kiterjedő konzisztencia tartalmazó helyreállítási tervek érhető el. A beállítás engedélyezve van a virtuális gépek feladatátvételt a legutóbbi közös virtuális gépre kiterjedő konzisztens helyreállítási pont. Más virtuális gépek feladatátvételt a legutóbbi feldolgozott helyreállítási pontot.
    - **Legújabb több virtuális gépre kiterjedően alkalmazáskonzisztens**: Ez a beállítás érhető el helyreállítási terveket biztosít egy vagy több virtuális gépek, amelyeken engedélyezve van a virtuális gépre kiterjedő konzisztencia. A replikációs csoport részét képező virtuális gépek feladatátvételt a legutóbbi közös virtuális gépre kiterjedően alkalmazáskonzisztens helyreállítási pontot. Más virtuális gépek feladatátvételt a legutóbbi alkalmazáskonzisztens helyreállítási pontot.
    - **Egyéni**: használja ezt a beállítást egy adott virtuális gép egy adott helyreállítási pontra való átadásához.



## <a name="prepare-networking"></a>Hálózatok előkészítése

Feladatátvételi teszt futtatásakor kéri, hogy hálózati beállítások megadása a teszt replika gépek, a táblázat foglalja össze.

**Beállítás** | **Részletek** 
--- | --- 
**Egyik sem** | A teszt virtuális gép jön létre a gazdagépen, amelyen a replika virtuális gép is található. Nincs hozzáadva a felhőbe, és nem csatlakozik a hálózathoz.<br/><br/> Kapcsolódás a gép Virtuálisgép-hálózat létrehozása után.
**Meglévő használata** | A teszt virtuális gép jön létre a gazdagépen, amelyen a replika virtuális gép is található. A felhőben nem adódik.<br/><br/>Az elkülönített Virtuálisgép-hálózat létrehozása az éles hálózattól.<br/><br/>Ha egy VLAN-alapú hálózat használata esetén ajánlott (nem éles környezetben használt) külön logikai hálózatot hoz létre a VMM-ben erre a célra. A logikai hálózaton hozzon létre Virtuálisgép-hálózatok feladatátvételi teszteket szolgál.<br/><br/>A logikai hálózat legalább egy virtuális gépeket üzemeltető összes Hyper-V-kiszolgáló a hálózati adapterek társítva kell lennie.<br/><br/>A VLAN logikai hálózatok akkor adja hozzá a logikai hálózathoz hálózati helyek elkülönített kell lennie.<br/><br/>Windows Hálózatvirtualizálás-alapú logikai hálózatot használja, az Azure Site Recovery automatikusan hoz létre a elkülönített Virtuálisgép-hálózatokat. 
**Hálózat létrehozása** | A megadott beállítás alapján automatikusan jön létre egy ideiglenes tesztelési célú hálózat **logikai hálózat** és a kapcsolódó hálózati telephelyeknek.<br/><br/> Feladatátvételi ellenőrzi, hogy a virtuális gépek jönnek létre. |Ezt a beállítást kell használnia, ha a helyreállítási terv egynél több Virtuálisgép-hálózatot használ.<br/><br/> Windows Hálózatvirtualizálás hálózatokat használ, ha ez a beállítás automatikusan hozhat létre Virtuálisgép-hálózat (alhálózatokat és IP-címkészletek) ugyanazokat a beállításokat, a replika virtuális gép a hálózatban. Ezek a Virtuálisgép-hálózatok automatikusan törlődnek a feladatátvételi teszt befejeződése után.<br/><br/> A teszt virtuális gép létrehozása a gazdagépen, amelyen a replika virtuális gép található. A felhőben nem adódik.

### <a name="best-practices"></a>Ajánlott eljárások

- Tesztelés éles hálózati környezetben hatására leállás a termelési számítási feladatokhoz. Kérje meg a felhasználók nem kapcsolódó alkalmazások használata, amikor a vészhelyreállítási próba végrehajtása van folyamatban.

- A tesztelési célú hálózat nem kell egyeznie a feladatátvételi teszthez használni a VMM logikai hálózat típusa. De néhány kombináció nem működnek:

     - Ha a replika használja a DHCP- és a VLAN-alapú elkülönítés, a Virtuálisgép-hálózat számára a replika egy statikus IP-címkészlet nem szükséges. Így a Windows Hálózatvirtualizálás segítségével a teszt feladatátvételhez nem fog működni, mert nem-címkészlet, érhető el. 
        
     - Feladatátvételi teszt nem fog működni, ha a replika hálózati elkülönítés nélkül használja, és a tesztelési célú hálózat Windows Hálózatvirtualizálást használ. Ennek az oka az elkülönítés nélküli hálózat nem rendelkezik az alhálózatok Windows Hálózatvirtualizálás hálózat létrehozásához szükséges.
        
- Azt javasoljuk, hogy nem használja a hálózatot a hálózatleképezést, a feladatátvételi teszthez.

- Hogyan replika virtuális gépek csatlakoznak leképezett Virtuálisgép-hálózatok, miután feladatátvételi attól függ, hogyan a Virtuálisgép-hálózat van konfigurálva, a VMM-konzolon.


### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>Nincs elkülönítés vagy VLAN-elkülönítést Virtuálisgép-hálózatot

Ha egy Virtuálisgép-hálózat elkülönítési és VLAN-elkülönítést, a VMM-ben van konfigurálva, vegye figyelembe a következőket:

- Ha DHCP meg van adva a Virtuálisgép-hálózat, a replika virtuális gép a VLAN-Azonosítót a logikai hálózat társítva a hálózati hely megadott beállításokat keresztül csatlakozik. A virtuális gép IP-címének rendelkezésre álló DHCP-kiszolgálótól kap.
- Nem kell egy statikus IP-címkészletet a céloldali Virtuálisgép-hálózat megadása. Egy statikus IP-címkészletet a Virtuálisgép-hálózat használata esetén a replika virtuális gép csatlakozik a VLAN-Azonosítót a megadott logikai hálózat társítva a hálózati hely beállításain keresztül.
- A virtuális gép IP-címének fogad a készlethez, amely a Virtuálisgép-hálózat van meghatározva. Ha egy statikus IP-címkészlet nem határoz meg a céloldali Virtuálisgép-hálózatot, IP-cím lefoglalását sikertelen lesz. Az IP-címkészlet létrehozása, amely használhatja a védelmi és helyreállítási mind a forrás- és a VMM-kiszolgálókon.

### <a name="vm-network-with-windows-network-virtualization"></a>Virtuálisgép-hálózat, a Windows Hálózatvirtualizálás

Ha egy Virtuálisgép-hálózat a VMM-ben a Windows Hálózatvirtualizálással van konfigurálva, vegye figyelembe a következőket:

- Meg kell határozni a céloldali Virtuálisgép-hálózat, függetlenül attól, hogy a forrás Virtuálisgép-hálózat használatára van konfigurálva a DHCP vagy statikus IP-címkészlet egy statikus címkészletet. 
- DHCP határozza meg, ha a célként megadott VMM-kiszolgáló DHCP-kiszolgálóként működik, és a céloldali VM-hálózat a készlethez meghatározott IP-címet biztosít.
- Ha a forráskiszolgáló statikus IP-címkészlet használatát meg van adva, a célként megadott VMM-kiszolgáló IP-címet a készletből foglal le. Mindkét esetben IP-cím lefoglalását sikertelen lesz, ha nincs megadva egy statikus IP-címkészletet.



## <a name="prepare-the-infrastructure"></a>Az infrastruktúra előkészítése

Egyszerűen ellenőrizni szeretné, hogy egy virtuális gép feladatátvételt is, ha a feladatátvételi teszt infrastruktúra nélkül is futtathatja. Ha szeretne tenni az alkalmazást feladatátvételi teszt teljes Vészhelyreállítási próba, meg kell a másodlagos helyen az infrastruktúra előkészítése:

- Ha egy meglévő hálózati használatával feladatátvételi teszt futtatása, készítse elő az Active Directory, a DHCP és DNS a hálózathoz tartozó.
- Ha egy feladatátvételi tesztet futtatja automatikusan létrehozhat egy Virtuálisgép-hálózatot, hozzá kell infrastruktúra-erőforrások az automatikusan létrehozott hálózathoz, a feladatátvételi teszt futtatása előtt. A helyreállítási tervben szereplő, megkönnyítheti Ez egy manuális lépés előtt 1 csoport hozzáadásával a helyreállítási tervet, amely a feladatátvételi teszthez használni fog. Adja hozzá az infrastruktúra-erőforrások, az automatikusan létrehozott hálózati feladatátvételi teszt futtatása előtt.


### <a name="prepare-dhcp"></a>DHCP előkészítése
Ha részt vesz a virtuális gépek feladatátvételi teszt DHCP használatára, hozzon létre egy teszt DHCP-kiszolgáló a feladatátvételi teszt céljából elszigetelt hálózaton belül.


### <a name="prepare-active-directory"></a>Az Active Directory előkészítése
Az alkalmazás tesztelése a feladatátvételi teszt futtatásához, a termelési Active Directory-környezetet egy példányát a tesztelési környezetben kell. További információkért tekintse át a [Active Directoryra vonatkozó feladatátvételi szempontokat részletező cikkben](site-recovery-active-directory.md#test-failover-considerations).

### <a name="prepare-dns"></a>DNS előkészítése
Készítse elő egy DNS-kiszolgáló a teszt feladatátvételhez a következőképpen:

* **DHCP**: virtuális gépek DHCP használatára, ha a teszt DNS IP-címét frissíteni kell a teszt DHCP-kiszolgálón. Ha egy hálózati Windows Hálózatvirtualizálás típusú használ, a VMM-kiszolgáló a DHCP-kiszolgáló funkcionál. Ezért DNS IP-címét frissíteni kell a feladatátvételi teszt hálózatában. Ebben az esetben a virtuális gépek regisztrálhatják magukat a megfelelő DNS-kiszolgálóra.
* **Statikus cím**: virtuális gépek statikus IP-címet használja, ha a teszt DNS-kiszolgáló IP-címét frissíteni kell a feladatátvételi teszt hálózatában. Szüksége lehet a DNS frissítése az a teszt virtuális gépek IP-címét. Az alábbi parancsprogram erre a célra használhatja:

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

Ez az eljárás ismerteti egy helyreállítási terv feladatátvételi teszt futtatása. Másik megoldásként futtathatja az egyetlen virtuális gép feladatátvétele a **virtuális gépek** fülre.

1. Válassza ki **helyreállítási tervek** > *recoveryplan_name*. Kattintson a **feladatátvételi** > **feladatátvételi teszt**.
2. Az a **feladatátvételi teszt** panelen adja meg, hogy replika virtuális gépek csatlakozniuk kell hálózatok a feladatátvételi teszt után.
3. A feladatátvételi folyamat előrehaladásának nyomon a **feladatok** fülre.
4. Feladatátvétel befejezése után ellenőrizze, hogy a virtuális gépek sikeresen elindulnak-e.
5. Ha elkészült, kattintson a **feladatátvételi teszt utáni karbantartás** elemre a helyreállítási terven. A **Jegyzetek** területen jegyezheti fel és mentheti a feladatátvételi teszttel kapcsolatos megfigyeléseket. Ez a lépés törli a virtuális gépek és a Site Recovery által a feladatátvételi teszt során létrehozott hálózatok. 

![Feladatátvételi teszt](./media/hyper-v-vmm-test-failover/TestFailover.png)
 


> [!TIP]
> Az IP-címet kap egy virtuális gép feladatátvételi teszt során az az azonos IP-cím, amely a virtuális gépet szeretne kapni a tervezett vagy nem tervezett feladatátvétel (feltéve, hogy az IP-cím érhető el a feladatátvételi teszt hálózatában). Ha az azonos IP-cím nem érhető el a feladatátvételi teszt hálózatában lévő, a virtuális gép kap egy másik IP-cím elérhető a feladatátvételi teszt hálózatában.



### <a name="run-a-test-failover-to-a-production-network"></a>Éles hálózati környezetben a feladatátvételi teszt futtatása

Azt javasoljuk, hogy nincs-e a feladatátvételi teszt futtatásához a termelési helyreállítási hely hálózathoz, a hálózatleképezés során megadott. De ha szeretne érvényesíteni a végpontok közötti hálózati kapcsolat átvevő virtuális gépen, vegye figyelembe a következőket:

* Győződjön meg arról, hogy az elsődleges virtuális gép állítsa le, amikor a feladatátvételi tesztet. Ha nem, akkor ugyanazzal az identitással két virtuális gépet egyszerre ugyanazon a hálózaton fog futnia. Ilyen esetben nem kívánt következményekkel vezethet.
* A feladatátvételi tesztet hajt végre módosításokat virtuális gépek elvesznek a feladatátvételi virtuális tesztgépeket törlésekor. Ezeket a módosításokat a rendszer nem replikálja vissza az elsődleges virtuális gép.
* Ehhez hasonló tesztelés vezet az éles alkalmazás leállása nélkül. Kérje meg a felhasználókat az alkalmazás nem használják az alkalmazást, ha a Dr működési van folyamatban.  


## <a name="next-steps"></a>További lépések
Miután sikeresen futtatta a vész-helyreállítási, [teljes feladatátvételt végez](site-recovery-failover.md).



