---
title: NHyper-V vész-helyreállítási gyakorlat futtatása egy másodlagos helyre az Azure Site Recovery szolgáltatással
description: Megtudhatja, hogyan futtathat VÉSZ-fúrót a VMM-felhőkben lévő Hyper-V virtuális gépekre az Azure Site Recovery használatával egy másodlagos helyszíni adatközpontba.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 0363911574a076b13cb72591fb2564364e096c76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257965"
---
# <a name="run-a-dr-drill-for-hyper-v-vms-to-a-secondary-site"></a>Dr-fúró futtatása hyper-V virtuális gépekhez egy másodlagos helyre


Ez a cikk ismerteti, hogyan kell csinálni egy vész-helyreállítási (DR) fúró a System Center Virtual Machine Manager V(MM) felhők, egy másodlagos helyszíni helyen, az [Azure Site Recovery](site-recovery-overview.md)használatával.

Futtategy tesztfeladat-átvételt a replikációs stratégia érvényesítéséhez, és a VÉSZ-gyakorlat ot adatvesztés vagy állásidő nélkül hajthatja végre. A tesztfeladat-átvétel nincs hatással a folyamatban lévő replikációra vagy az éles környezetre. 

## <a name="how-do-test-failovers-work"></a>Hogyan működnek a tesztfeladat-átvételek?

Futtat egy teszt feladatátvételt az elsődleges ről a másodlagos helyre. Ha egyszerűen csak azt szeretné ellenőrizni, hogy a virtuális gép feladatátvétel, futtathat egy teszt feladatátvételt anélkül, hogy bármit a másodlagos helyen állítana be. Ha ellenőrizni szeretné, hogy az alkalmazás feladatátvétele a várt módon működik-e, be kell állítania a hálózatot és az infrastruktúrát a másodlagos helyen.
- Futtathat egy tesztfeladat-átvételt egyetlen virtuális számítógépen vagy egy [helyreállítási terven.](site-recovery-create-recovery-plans.md)
- A tesztfeladat-átvétel hálózaton kívül, meglévő hálózattal vagy automatikusan létrehozott hálózattal is futtatható. Ezekről a lehetőségekről az alábbi táblázatban olvashat bővebben.
    - A tesztfeladat-átvétel hálózaton kívül is futtatható. Ez a beállítás akkor hasznos, ha egyszerűen csak ellenőrizni szeretné, hogy a virtuális gép képes volt-e feladatátvételre, de nem fogja tudni ellenőrizni a hálózati konfigurációt.
    - Futtassa a feladatátvételt egy meglévő hálózattal. Azt javasoljuk, hogy ne használjon éles hálózatot.
    - Futtassa a feladatátvételt, és hagyja, hogy a Site Recovery automatikusan hozzon létre egy teszthálózatot. Ebben az esetben a Site Recovery automatikusan létrehozza a hálózatot, és a tesztfeladat-átvétel befejezésekor törli azt.
- Ki kell választania egy helyreállítási pontot a teszt feladatátvételhez: 
    - **Legutóbbi feldolgozott:** Ez a beállítás nem felel meg a virtuális gép nek a Site Recovery által feldolgozott legújabb helyreállítási pont. Ez a lehetőség alacsony helyreállítási időre vonatkozó célkitűzést (RTO) nyújt, mert a rendszer nem tölt időt a feldolgozatlan adatok feldolgozásával.
    - **Legújabb alkalmazás-konzisztens:** Ez a beállítás feladatátvételegy virtuális gép a site recovery által feldolgozott legújabb alkalmazáskonzisztens helyreállítási pont. 
    - **Legújabb**: Ez a beállítás először feldolgozza az összes adatot, amely et elküldött site recovery szolgáltatás, hozzon létre egy helyreállítási pontot minden virtuális gép, mielőtt átadott neki. Ez a beállítás biztosítja a legalacsonyabb RPO (helyreállításipont), mert a feladatátvétel után létrehozott virtuális gép az összes adatot replikálja a Site Recovery, amikor a feladatátvétel t.
    - **Legújabb több virtuális gép feldolgozása:** Elérhető helyreállítási tervek, amelyek egy vagy több virtuális gép, amely több virtuális gép konzisztenciája engedélyezve van. Virtuális gépek a beállítás engedélyezve van feladatátvétel a legújabb közös multi-vm konzisztens helyreállítási pont. Más virtuális gépek feladatátvétela a legújabb feldolgozott helyreállítási pont.
    - **Legújabb többvm-es alkalmazáskonzisztens:** Ez a beállítás egy vagy több virtuális gép, amelyek több virtuális gép konzisztenciája engedélyezve van. Virtuális gépek, amelyek egy replikációs csoport részét képezik, feladatátvétela a legújabb közös többvirtuális gép alkalmazás-konzisztens helyreállítási pont. Más virtuális gépek feladatátvétela a legújabb alkalmazáskonzisztens helyreállítási pont.
    - **Egyéni:** Ezzel a beállítással egy adott virtuális gép egy adott helyreállítási pont.



## <a name="prepare-networking"></a>Hálózatkezelés előkészítése

Teszt feladatátvétel futtatásakor a rendszer kéri, hogy válassza ki a teszt replikagépek hálózati beállításait, a mint a táblázatban összefoglalva.

| **Beállítás** | **Részletek** | |
| --- | --- | --- |
| **Nincs** | A teszt virtuális gép jön létre az állomáson, amelyen a replika virtuális gép található. Nem kerül a felhőbe, és nem csatlakozik semmilyen hálózathoz.<br/><br/> A számítógép létrehozása után csatlakozhat a virtuális gép hálózatához.| |
| **Meglévő kontissza** | A teszt virtuális gép jön létre az állomáson, amelyen a replika virtuális gép található. Nem kerül a felhőbe.<br/><br/>Hozzon létre egy virtuális gép hálózat, amely el van különítve az éles hálózattól.<br/><br/>Ha VLAN-alapú hálózatot használ, azt javasoljuk, hogy hozzon létre egy külön logikai hálózatot (nem használt éles környezetben) a VMM erre a célra. Ez a logikai hálózat virtuálisgép-hálózatok létrehozására szolgál a sikertelen feladatátvételhez.<br/><br/>A logikai hálózatot a virtuális gépeket üzemeltető összes Hyper-V kiszolgáló legalább egyik hálózati adapteréhez kell társítani.<br/><br/>VlAN logikai hálózatok esetén a logikai hálózathoz hozzáadott hálózati helyeket el kell különíteni.<br/><br/>Ha Windows hálózati virtualizáción alapuló logikai hálózatot használ, az Azure Site Recovery automatikusan létrehozza az elkülönített virtuálisgép-hálózatokat. | |
| **Hálózat létrehozása** | Az ideiglenes teszthálózat automatikusan létrejön a **Logikai hálózat** ban és a kapcsolódó hálózati helyeken megadott beállítás alapján.<br/><br/> Feladatátvételi ellenőrzi, hogy a virtuális gépek jönnek létre.<br/><br/> Ezt a beállítást akkor használja, ha egy helyreállítási terv egynél több virtuálisgép-hálózatot használ.<br/><br/> Ha Windows hálózati virtualizációs hálózatokat használ, ez a beállítás automatikusan létrehozhat virtuálisgép-hálózatokat ugyanazzal a beállítással (alhálózatok és IP-címkészletek) a replika virtuális gép hálózatában. Ezek a virtuálisgép-hálózatok automatikusan törlődnek a teszt feladatátvétel befejezése után.<br/><br/> A teszt virtuális gép jön létre az állomáson, amelyen a replika virtuális gép létezik. Nem kerül a felhőbe.|

### <a name="best-practices"></a>Ajánlott eljárások

- Az éles hálózat tesztelése állásidőt okoz az éles számítási feladatokhoz. Kérje meg a felhasználókat, hogy ne használják a kapcsolódó alkalmazásokat, amikor a vész-helyreállítási gyakorlat folyamatban van.

- A teszthálózatnak nem kell megfelelnie a tesztfeladat-átvételhez használt VMM logikai hálózattípusnak. De, néhány kombináció nem működik:

     - Ha a replika DHCP- és VLAN-alapú elkülönítést használ, a replika virtuális géphálózatának nincs szüksége statikus IP-címkészletre. Így a Windows Hálózati virtualizáció használata a teszt feladatátvételhez nem fog működni, mert nincs elérhető címkészlet. 
        
     - A tesztfeladat-átvétel nem fog működni, ha a replikahálózat nem használ elkülönítést, és a teszthálózat a Windows hálózati virtualizációt használja. Ennek az az oka, hogy az elkülönítés nélküli hálózat nem rendelkezik a Windows hálózati virtualizációs hálózat létrehozásához szükséges alhálózatokkal.
        
- Azt javasoljuk, hogy ne használja a hálózati leképezéshez, a tesztfeladat-átvételhez kiválasztott hálózatot.

- A replika virtuális gépei leképezett virtuálisgép-hálózatokhoz való csatlakozása a feladatátvétel után attól függ, hogy a VMM-hálózat hogyan van konfigurálva a VMM-konzolon.


### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>Elkülönítés és VLAN-elkülönítés nélkül konfigurált virtuálisgép-hálózat

Ha a vm-hálózat vmm-ben van konfigurálva elkülönítés vagy VLAN-elkülönítés nélkül, vegye figyelembe a következőket:

- Ha a virtuális gép hálózatához DHCP van megadva, a replika virtuális gép a társított logikai hálózat hálózati helyéhez megadott beállításokon keresztül csatlakozik a VLAN-azonosítóhoz. A virtuális gép az elérhető DHCP-kiszolgálótól kapja az IP-címét.
- Nem kell statikus IP-címkészletet definiálnia a célvirtuális gép hálózatához. Ha a virtuális gép hálózatához statikus IP-címkészletet használ, a replika virtuális gép a társított logikai hálózat hálózati helyéhez megadott beállításokon keresztül csatlakozik a VLAN-azonosítóhoz.
- A virtuális gép megkapja az IP-címét a virtuális gép hálózatához definiált készletből. Ha egy statikus IP-címkészlet nincs definiálva a cél virtuálisgép-hálózaton, az IP-címfoglalás sikertelen lesz. Hozza létre az IP-címkészletet a védelemhez és helyreállításhoz használt forrás- és célVMM-kiszolgálókon is.

### <a name="vm-network-with-windows-network-virtualization"></a>Virtuálisgép-hálózat Windows hálózati virtualizációval

Ha a VM-hálózat a VMM-ben van konfigurálva a Windows hálózati virtualizációval, vegye figyelembe a következőket:

- Meg kell határoznia egy statikus készleta a cél virtuálisgép-hálózat, függetlenül attól, hogy a forrás virtuálisgép-hálózat dhcp vagy statikus IP-címkészlet használatára van konfigurálva. 
- Ha dhcp-t határoz meg, a cél VMM-kiszolgáló DHCP-kiszolgálóként működik, és a célvirtuális gép hálózatához definiált készletből biztosít IP-címet.
- Ha a forráskiszolgálóhoz statikus IP-címkészlet et használ, a cél VMM-kiszolgáló ip-címet foglal le a készletből. Az IP-cím kiosztása mindkét esetben sikertelen lesz, ha nincs definiálva statikus IP-címkészlet.



## <a name="prepare-the-infrastructure"></a>Az infrastruktúra előkészítése

Ha egyszerűen csak azt szeretné ellenőrizni, hogy egy virtuális gép feladatátvételt, futtathat egy teszt feladatátvétel infrastruktúra nélkül. Ha teljes VÉSZ-gyakorlatot szeretne végezni az alkalmazás feladatátvételének teszteléséhez, elő kell készítenie az infrastruktúrát a másodlagos helyen:

- Ha egy tesztfeladat-átvételt egy meglévő hálózaton keresztül futtat, készítse elő az Active Directoryt, a DHCP-t és a DNS-t a hálózatban.
- Ha egy teszt feladatátvételt futtat azzal a lehetőséggel, hogy automatikusan hozzon létre egy virtuálisgép-hálózatot, a teszt feladatátvétel futtatása előtt hozzá kell adnia az automatikusan létrehozott hálózathoz infrastruktúra-erőforrásokat. A helyreállítási tervben ezt megkönnyítheti, ha manuális lépést ad hozzá az 1- es csoport előtti manuális lépéshez a tesztfeladat-átvételhez használni kívánt helyreállítási tervben. Ezután adja hozzá az infrastruktúra-erőforrásokat az automatikusan létrehozott hálózathoz a teszt feladatátvétel futtatása előtt.


### <a name="prepare-dhcp"></a>DHCP előkészítése
Ha a tesztfeladat-átvételben részt vevő virtuális gépek DHCP-t használnak, hozzon létre egy teszt DHCP-kiszolgálót az elkülönített hálózaton belül a tesztfeladat-átvétel céljából.


### <a name="prepare-active-directory"></a>Az Active Directory előkészítése
Az alkalmazásteszteléshez tesztfeladat-átvétel futtatásához szüksége van az éles Active Directory-környezet egy példányára a tesztkörnyezetben. További információt az [Active Directory tesztfeladat-átvételi szempontjaiból](site-recovery-active-directory.md#test-failover-considerations)talál.

### <a name="prepare-dns"></a>A DNS előkészítése
Dns-kiszolgáló előkészítése a tesztfeladat-átvételre az alábbiak szerint:

* **DHCP**: Ha a virtuális gépek DHCP-t használnak, a teszt DNS IP-címét frissíteni kell a teszt DHCP-kiszolgálón. Ha a Windows hálózati virtualizáció hálózati típusát használja, a VMM-kiszolgáló DHCP-kiszolgálóként működik. Ezért a DNS IP-címét frissíteni kell a teszt feladatátvételi hálózatban. Ebben az esetben a virtuális gépek regisztrálják magukat a megfelelő DNS-kiszolgálóra.
* **Statikus cím**: Ha a virtuális gépek statikus IP-címet használnak, a teszt DNS-kiszolgáló IP-címét frissíteni kell a teszt feladatátvételi hálózatban. Előfordulhat, hogy frissítenie kell a DNS-t a teszt virtuális gépek IP-címével. Erre a célra a következő mintaparancsfájlt használhatja:

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

Ez az eljárás azt ismerteti, hogyan futtathatok tesztfeladat-átvételt a helyreállítási tervhez. Azt is megteheti, hogy egyetlen virtuális gép feladatátvételt hajt stathat a **Virtuális gépek** lapon.

1. Válassza a **helyreállítási tervek** > *recoveryplan_name*lehetőséget. Kattintson **a Feladatátvételi** > **teszt feladatátvételi lehetőségre.**
2. A **teszt feladatátvételi** panelen adja meg, hogyan replika virtuális gépek kell csatlakozni a hálózatokhoz a teszt feladatátvétel után.
3. A **feladatátvétel** folyamatának nyomon követése a Feladatok lapon.
4. A feladatátvétel befejezése után ellenőrizze, hogy a virtuális gépek sikeresen elindulnak-e.
5. Ha elkészült, kattintson **a karbantartás teszt feladatátvétel** a helyreállítási terv. A **Megjegyzések alkalmazásban**rögzítse és mentse a tesztfeladat-átvételhez kapcsolódó megfigyeléseket. Ez a lépés törli a Site Recovery által a teszt feladatátvétel során létrehozott virtuális gépeket és hálózatokat. 

![Feladatátvétel tesztelése](./media/hyper-v-vmm-test-failover/TestFailover.png)
 


> [!TIP]
> A virtuális gépnek a teszt feladatátvétel során megadott IP-cím ugyanaz az IP-cím, amelyet a virtuális gép kapna egy tervezett vagy nem tervezett feladatátvételhez (feltételezve, hogy az IP-cím elérhető a teszt feladatátvételi hálózatban). Ha ugyanaz az IP-cím nem érhető el a teszt feladatátvételi hálózatban, a virtuális gép kap egy másik IP-címet, amely elérhető a teszt feladatátvételi hálózatban.



### <a name="run-a-test-failover-to-a-production-network"></a>Tesztfeladat-átvétel futtatása éles hálózaton

Azt javasoljuk, hogy ne futtasson tesztfeladat-átvételt az éles helyreállítási hely hálózatán, amelyet a hálózati leképezés során megadott. Ha azonban egy feladatátvevő virtuális gépen végpontok közötti hálózati kapcsolatot kell érvényesítenie, vegye figyelembe a következő pontokat:

* Győződjön meg arról, hogy az elsődleges virtuális gép le van állítva, amikor a teszt feladatátvételt végzi. Ellenkező esetben két azonos identitással rendelkező virtuális gép ugyanabban a hálózatban fog futni. Ez a helyzet nem kívánt következményekhez vezethet.
* A tesztfeladat-átvételi virtuális gépeken végrehajtott módosítások elvesznek a tesztfeladat-átvételi virtuális gépek karbantartásakor. Ezek a változások nem replikálódik vissza az elsődleges virtuális gépekre.
* Az ilyen tesztelés az éles alkalmazás leállásához vezet. Kérje meg az alkalmazás felhasználóit, hogy ne használják az alkalmazást, amikor a VÉSZ-fúró folyamatban van.  


## <a name="next-steps"></a>További lépések
Miután sikeresen futtatott egy VÉSZ-fúrót, [futtathat egy teljes feladatátvételt.](site-recovery-failover.md)



