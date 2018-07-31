---
title: Az Azure-ból a helyszíni helyre virtuális gépek ismételt védelme |} A Microsoft Docs
description: Az Azure virtuális gépek a feladatátvételt követően térjen vissza a helyszíni virtuális gépek csatlakozva a feladat-visszavételhez is kezdeményezhető. Megtudhatja, hogyan védi meg ismét a feladat-visszavétel előtt.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: rajanaki
ms.openlocfilehash: 1b410b2832d856f80d640aab2096fef270156c81
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346679"
---
# <a name="reprotect-machines-from-azure-to-an-on-premises-site"></a>Egy helyszíni helyhez az Azure-ból gépek ismételt védelme

Miután [feladatátvételi](site-recovery-failover.md) a helyszíni VMware virtuális gépek vagy fizikai kiszolgálók Azure-ba, a sikertelen első lépése az a helyszíni hely, hogy a feladatátvétel során létrehozott Azure virtuális gépek ismételt védelme. Ez a cikk azt ismerteti, hogyan teheti ezt. 

Röviden tekintse meg a következő videó bemutatja, hogyan a feladatátvételt az Azure-ból a helyszíni helyre.<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="before-you-begin"></a>Előkészületek

Ha egy sablont a virtuális gépek létrehozásakor, győződjön meg arról, hogy minden virtuális gép rendelkezik-e a saját UUID azonosító, a lemezek. Ha a helyszíni virtuális gép UUID ütközik a fő célkiszolgáló UUID, mert mindkettő egy sablon alapján létrehozott, az ismételt védelem sikertelen lesz. Helyezze üzembe, amely nem az azonos sablon alapján létrehozott egy másik fő célkiszolgálót. Tekintse meg az alábbi információkat:
- Ha a feladat-visszavételhez egy másik vCenter, ügyeljen arra, hogy az új vCenter és a fő célkiszolgáló felderítése történik meg. Egy tipikus tünete, hogy állomásához nem érhetők el, vagy nem láthatók a a **ismételt védelme** párbeszédpanel bezárásához.
- Replikálja a helyszíni, a feladat-visszavételi szabályzatra van szükség. Ez a szabályzat automatikusan létrejön, amikor létrehoz egy előre szabályzatot. Tekintse meg az alábbi információkat:
    - Ez a szabályzat automatikus létrehozása során a konfigurációs kiszolgáló társítva.
    - Ez a szabályzat nem szerkeszthető.
    - A készlet a szabályzat értékei a következők (helyreállítási Időkorlát küszöbértéke = 15 perc, a helyreállítási pont megőrzése = 24 óra, az alkalmazás konzisztenciájának pillanatkép gyakorisága = 60 perc).
- Ismételt védelem és a feladat-visszavétel során a helyszíni konfigurációs kiszolgálón fut és csatlakoztatva kell lennie.
- Ha a vCenter-kiszolgáló kezeli a virtuális gépek, amelyhez Ön fogja feladat-visszavételt, győződjön meg arról, hogy rendelkezik-e a [szükséges engedélyek](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) virtuális gépek a vCenter-kiszolgálók felderítéséhez.
- Az ismételt védelem előtt törölje a pillanatképek a fő célkiszolgálón. Ha pillanatképeket megadva, a helyszíni fő célkiszolgáló és a virtuális gépen, az ismételt védelem sikertelen lesz. A pillanatképek a virtuális gép ismételt védelmi feladat során automatikusan összefésülése megtörténjen.
- A replikációs csoport összes virtuális gép azonos típusú operációs rendszer (az összes Windows vagy az összes Linux) kell lennie. Vegyes operációs rendszert jelenleg egy replikációs csoport védelem-újrabeállítást és a helyszíni feladat-visszavétel nem támogatott. Ennek oka az, a fő célkiszolgáló kell lennie, mint a virtuális gép ugyanazt az operációs rendszert. Replikációs csoport összes virtuális gépet az azonos fő célkiszolgálót kell rendelkeznie. 
- Konfigurációs kiszolgáló szükséges helyszíni esetén, feladat-visszavételt. A feladat-visszavétel során a virtuális gép léteznie kell a konfigurációs kiszolgáló adatbázisát. Ellenkező esetben a feladat-visszavétel nem sikerül. Győződjön meg arról, hogy a konfigurációs kiszolgáló rendszeresen ütemezett biztonsági mentéseket. Katasztrófa esetén állítsa vissza a kiszolgáló ugyanazt az IP-címmel, hogy a feladat-visszavétel működik.
- Ismételt védelem és a feladat-visszavétel szükséges az adatok replikálásához site-to-site (S2S) VPN. Adja meg a hálózati, így a feladatátvételi virtuális gépek az Azure-ban érhető el (ping), a helyszíni konfigurációs kiszolgáló. Emellett érdemes az Azure-hálózatot a feladatátvételi virtuális gép a folyamatkiszolgáló üzembe helyezése. A folyamatkiszolgálóhoz is tud kommunikálni a helyszíni konfigurációs kiszolgálón kell lennie.
- Győződjön meg arról, hogy nyissa meg a feladatátvétel és feladat-visszavételt a következő portokat:

    ![A feladatátvétel és feladat-visszavétel portok](./media/vmware-azure-reprotect/failover-failback.png)

- Olvassa el az összes a [portok és URL-engedélyezési előfeltételei](vmware-azure-deploy-configuration-server.md#prerequisites).

## <a name="deploy-a-process-server-in-azure"></a>Az Azure-ban folyamatkiszolgáló üzembe helyezése

Az Azure-ban folyamatkiszolgáló lehet szükség, mielőtt visszaadja a feladatokat a helyszíni helyre:
- A folyamatkiszolgáló adatokat fogad a védett virtuális gépen az Azure-ban, és ezután elküldi az adatokat a helyszíni helyre.
- Egy kis késleltetésű hálózatra szükség a folyamatkiszolgáló és a védett virtuális gép között. Általánosságban elmondható figyelembe kell vennie a késés, amikor eldönti, hogy szükséges-e a folyamatkiszolgáló az Azure-ban:
    - Ha az Azure ExpressRoute-kapcsolatok beállítása, használhatja a helyszíni folyamatkiszolgálót küldhet adatokat, mert a virtuális gép és a folyamatkiszolgáló között késés alacsony.
    - Javasoljuk azonban, ha csak egy S2S VPN, az Azure-ban a folyamatkiszolgáló üzembe helyezése.
    - Feladat-visszavétel során egy Azure-alapú folyamatkiszolgáló használatát javasoljuk. A replikációs teljesítmény értéke magasabb, ha a folyamatkiszolgáló közelebb a replikáló virtuális gépre (a átvevő gépen az Azure-ban). Egy megvalósíthatósági használata a helyi folyamatkiszolgáló és az ExpressRoute privát társviszony-létesítés.

Az Azure-ban folyamatkiszolgáló üzembe helyezése:

1. Ha az Azure-ban folyamatkiszolgáló üzembe helyezése van szüksége, tekintse meg [állítsa be a folyamatkiszolgáló, az Azure-ban feladat-visszavételhez](vmware-azure-set-up-process-server-azure.md).
2. Az Azure virtuális gépek küldhetnek replikációs adatokat a folyamatkiszolgálónak. Konfigurálja a hálózatokat, így az Azure virtuális gépek elérheti a folyamatkiszolgáló.
3. Ne feledje, hogy a replikáció az Azure-ból a helyszíni akkor fordulhat elő, csak az S2S VPN-kapcsolattal, vagy a privát társviszony-létesítésen keresztül, az ExpressRoute-hálózaton. Győződjön meg arról, hogy elegendő sávszélesség, hálózati csatornán keresztül érhető el.

## <a name="deploy-a-separate-master-target-server"></a>Egy különálló fő célkiszolgálót üzembe helyezése

A fő célkiszolgáló fogadja a feladat-visszavételi adatokat. Alapértelmezés szerint a fő célkiszolgáló a helyszíni konfigurációs kiszolgálón fut. Azonban nem sikerült biztonsági forgalom mennyiségétől függően előfordulhat, hogy szeretne hozzon létre egy különálló fő célkiszolgálót a feladat-visszavételhez. Hogyan hozhat létre egyet a következő:

* [Hozzon létre egy Linux rendszerű fő célkiszolgálót](vmware-azure-install-linux-master-target.md) a Linux rendszerű virtuális gépek feladat-visszavételhez. Ez a szükséges.
* Szükség esetén hozzon létre egy különálló fő célkiszolgálót a Windows virtuális gép feladat-visszavételhez. Ehhez futtassa újra az egyesített telepítő, és válassza ki a fő célkiszolgáló létrehozása. [További információk](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers).

Miután létrehozott egy fő célkiszolgálót, a következő feladatokat végezheti el:

- Ha a virtuális gép nem található a helyszíni vCenter-kiszolgálón, a fő célkiszolgáló kell tudnia érnie a helyszíni virtuális gép, virtuálisgép-lemezek (VMDK) fájlba. A replikált adatokat a virtuálisgép-lemezek írási hozzáférés szükséges. Győződjön meg arról, hogy a helyszíni virtuális gép adattárolója olvasási/írási hozzáférést a fő célkiszolgáló gazdagép csatlakoztatva van.
- Ha a virtuális gép nem található a helyszíni vCenter-kiszolgálón, a Site Recovery szolgáltatás kell hozzon létre egy új virtuális gép ismételt védelem során. Az ESX-gazdagépen, amelyen a fő célkiszolgáló létrehozása a virtuális gép jön létre. Válassza ki az ESX-gazdagép figyelmesen, hogy a feladat-visszavételi virtuális gép jön létre, amelyeket szeretne a gazdagépen.
- A fő célkiszolgáló nem használhatja a Storage vMotion. A fő célkiszolgáló a Storage vMotion használatával, előfordulhat, hogy a feladat-visszavételi sikertelen lesz. A virtuális gép nem indítható el, mivel a lemezek nem érhetők el azt. A probléma elkerüléséhez, zárja ki a fő célkiszolgálókat a vMotion-listából.
- A fő célkiszolgáló a Storage vMotion feladat ismételt védelem után megy keresztül, ha a védett virtuálisgép-lemezeket a fő célkiszolgálóhoz csatolt át a cél a vMotion feladat. Ha megpróbálja ezt követően ismét sikertelen, a lemez terméknek sikertelen lesz, mivel a lemezek nem találhatók. Nehéz majd lesz található lemezeket a storage-fiókokban. Keresse meg őket manuálisan, és csatolja őket a virtuális gép kell. Ezt követően a helyszíni virtuális gép rendszerindításra alkalmas.
- Adja hozzá a meglévő Windows fő célkiszolgáló adatmegőrzési meghajtó. Adjon hozzá egy új lemezt, és formázza a meghajtót. Állítsa le a pontok időpontban, amikor a virtuális gépet replikálja a helyszíni hely az adatmegőrzési meghajtó szolgál. Az alábbiakban egy adatmegőrzési meghajtó néhány feltétele. Ha ezek a feltételek nem teljesülnek, a meghajtó nem szerepel a listán a fő célkiszolgáló:
    - A kötet nem használható az semmilyen más célra, például a replikációs cél.
    - A kötet nem található zárolási üzemmódban.
    - A kötet nem a gyorsítótár kötetében. A fő célkiszolgáló telepítése a köteten lévő nem létezik. A folyamat-kiszolgáló és a fő cél az egyéni telepítési kötete nem megfelelő egy adatmegőrzési kötetnek. A folyamatkiszolgáló és fő célkiszolgáló egy köteten vannak telepítve, a kötet esetén a fő célkiszolgáló gyorsítótárkötet.
    - A kötet fájlrendszerének típusa nem FAT vagy FAT32.
    - A kötet kapacitása nem nulla.
    - Az a Windows alapértelmezett adatmegőrzési kötete az R kötet.
    - A Linux alapértelmezett adatmegőrzési kötete/mnt/Retention.
- Egy új meghajtót kell adni, ha server/konfigurációs kiszolgáló meglévő folyamat gép vagy egy méretezési csoport vagy a folyamat vagy a fő cél server gépet használ. Az új meghajtó az előző követelményeknek kell megfelelnie. Ha az adatmegőrzési meghajtó nincs jelen, nem jelenik meg a portálon lévő legördülő listában. Ha egy meghajtót ad hozzá a helyszíni fő célkiszolgáló, megjelennek a portálon a kijelölést az a meghajtó akár 15 percet vesz igénybe. Ha a meghajtó nem jelenik meg, 15 perc után is frissítheti a konfigurációs kiszolgálón.
- Telepítse a VMware-eszközök vagy a nyílt-vm-eszközök a fő célkiszolgálón. Az eszközök nélkül az adattárolók a fő célkiszolgáló ESXi-gazdagépen nem észlelhető.
- Állítsa be a `disk.EnableUUID=true` beállítást a konfigurációs paraméterek, a fő virtuális gép VMware-ben. Ha a sor nem létezik, adja hozzá. Ez a beállítás szükséges, hogy megfelelően csatlakoztatja, adjon meg egy egységes UUID a VMDK.
- Az ESX-gazdagép, amelyen jön létre a fő célkiszolgáló rendelkeznie kell legalább egy virtuális gép fájl system (VMFS) csatlakoztatott adattároló. Ha nincs VMFS adattárolók, a **adattárolója** a védelem-újrabeállítás oldalon bemenet üres, és nem folytatható.
- A fő célkiszolgáló nem lehet pillanatképeket a lemezeket. Ha a pillanatképek vannak, sikertelen ismételt védelem és a feladat-visszavétel.
- A fő célkiszolgáló nem rendelkezik Paravirtual SCSI-vezérlőhöz. A vezérlő csak egy LSI Logic-vezérlő lehet. LSI Logic-vezérlő, nélkül ismételt védelem sikertelen lesz.
- Minden példány esetében a fő célkiszolgáló lehet a hozzá csatolt lemezeket legfeljebb 60. Ha több mint 60 lemezek teljes száma, a helyszíni fő célkiszolgáló védelme alatt álló rendelkezik a virtuális gépek száma a fő célkiszolgálóhoz reprotects megkezdéséhez sikertelen lesz. Győződjön meg arról, hogy rendelkezik-e elegendő fő cél a lemez a bővítőhelyek, vagy üzembe helyezhet további fő célkiszolgálókat.
    

## <a name="enable-reprotection"></a>Ismételt védelem engedélyezése

Ha egy virtuális gép elindul az Azure-ban, az ügynököt, hogy a konfigurációs kiszolgálóra (akár 15 perc) regisztrálása egy kis ideig vesz igénybe. Ebben az időszakban nem lehet virtuális gép ismételt védelmére, és a egy hibaüzenet tájékoztatja, hogy az ügynök nincs telepítve. Ha ez történik, várjon néhány percet, és próbálkozzon az ismételt védelem újra:


1. Válassza ki **tároló** > **replikált elemek**. Kattintson a jobb gombbal a virtuális gépet, hogy átadta a feladatait, és válassza **ismételt védelem**. Vagy, a parancs a gombot, válassza ki a gépet, és válassza **ismételt védelem**.
2. Ellenőrizze, hogy a **a helyszíni Azure** iránya védelem van kiválasztva.
3. A **fő célkiszolgáló** és **Folyamatkiszolgáló**, válassza ki a helyszíni fő célkiszolgáló és a folyamatkiszolgáló.  
4. A **adattárolója**, válassza ki az adattárhoz, amelyre a lemezeket a helyszínen helyre szeretné. Ha a helyszíni virtuális gép törlődik, és hozzon létre új lemezeket kell ezt a beállítást használják. A rendszer figyelmen kívül hagyja ezt a beállítást, ha a lemez már létezik. Továbbra is szeretné adjon meg egy értéket.
5. Válassza ki az adatmegőrzési meghajtó.
6. A feladat-visszavételi szabályzat automatikusan ki van jelölve.
7. Válassza ki **OK** az ismételt védelem megkezdéséhez. Egy feladat elkezdi replikálni a virtuális gépet az Azure-ból a helyszíni helyre. A **Feladatok** lapon követheti nyomon a folyamat állapotát. Ha az ismételt védelem sikeres, a virtuális gépek védett állapotba kerül.

Tekintse meg az alábbi információkat:
- Ha azt szeretné, (ha törölték a helyszíni virtuális gép) egy másodlagos helyre történő helyreállítást, válassza ki az adatmegőrzési meghajtó és a fő célkiszolgáló konfigurált adattároló. Visszaadja a feladatokat a helyszíni hely, a VMware virtuális gépek, a feladat-visszavétel védelmi terv a fő célkiszolgáló ugyanezt az adattárat használjuk. A vCenter majd jön létre egy új virtuális gépet.
- Ha meg szeretné helyreállítani a virtuális gépet a meglévő helyszíni virtuális gépként az Azure-ban, csatlakoztassa az olvasási/írási hozzáférést a helyszíni virtuális gép adattárolók a fő célkiszolgáló ESXi-gazdagépen.

    ![Ismételt védelem párbeszédpanel](./media/vmware-azure-reprotect/reprotectinputs.png)

- A helyreállítási terv szintjén is megvédhetné. Replikációs csoport csak a helyreállítási terv keretében is újra. A helyreállítási terv használatával ismételt védelme, amikor meg kell adnia az értékeket minden védett gép.
- Ugyanazt a fő célkiszolgálót használja a replikációs csoportok ismételt védelméhez. Replikációs csoportok ismételt védelméhez használhatja egy másik fő célkiszolgálót, ha a kiszolgáló nem tudja megállapítani közös időben.
- Ismételt védelem során a helyszíni virtuális gép ki van kapcsolva. Ez elősegíti az adatok konzisztenciáját a replikáció során. Nem kapcsolja be a virtuális gép ismételt védelem befejeződése után.



## <a name="common-issues"></a>Gyakori problémák

- A Site Recovery jelenleg csak, egy vsan-hoz vagy a VMFS adattárolója feladat-visszavétel támogatja. Az NFS-adattároló nem támogatott. Ez a korlátozás miatt a védelem-újrabeállítás képernyőn adattároló kiválasztása bemeneti üres az NFS-adattárainak, vagy azt jeleníti meg a vSAN-adattár, de a feldolgozás során sikertelen. Ha szeretne feladat-visszavételt, egy VMFS adattároló létrehozása a helyszínen, és a feladat-visszavételhez azt. Ez a feladat-visszavétel hatására a VMDK teljes letöltésére.
- Ha egy olvasási jogosultsággal rendelkező felhasználó vCenter észlelését, és virtuális gépek védelme, védelmi sikeres lesz, és feladatátvételi működik. Ismételt védelem, során feladatátvétel sikertelen lesz, mivel a állomásához nem lesz felderítve. A probléma tünete, hogy a adattárainak ismételt védelem során nem jelennek meg. A probléma megoldásához, a vCenter hitelesítő adatainak frissítése egy megfelelő engedélyekkel rendelkező fiókkal, és próbálkozzon újra a feladatot. 
- Ha a feladat-visszavételt Linux rendszerű virtuális gép, és futtassa azt a helyszíni, láthatja, hogy a hálózatkezelő csomagot el lett távolítva a gépről. Ez az Eltávolítás az oka, hogy a hálózatkezelő csomagot törlődik, ha a virtuális gép van helyreállította az Azure-ban.
- Ha Linux rendszerű virtuális gép statikus IP-címmel van konfigurálva, és a feladatátvétel alatt áll az Azure-ba, az IP-cím igényelve, a DHCP-Kiszolgálótól. Ha átadja a feladatokat a helyszíni, a virtuális gép továbbra is az IP-cím beszerzése a DHCP használatával. Manuálisan jelentkezzen be a gépre, és majd állítsa vissza a statikus cím szükség esetén az IP-címet. Windows virtuális gép újra is szerez a statikus IP-címére.
- Vagy az ESXi 5.5-ös free Edition kiadása, vagy a vSphere 6 hipervizor ingyenes kiadás használatakor a feladatátvétel sikeres, de feladat-visszavétel nem jár sikerrel. Ahhoz, hogy a feladat-visszavételt, frissíteni vagy program próbalicencre.
- A folyamatkiszolgáló a konfigurációs kiszolgáló nem érhető el, ha a Telnet használatával ellenőrizze a kapcsolatot és a konfigurációs kiszolgáló 443-as porton. Akkor is is próbálja meg megpingelni a konfigurációs kiszolgáló a folyamatkiszolgálóról. Folyamatkiszolgáló is kell rendelkeznie a szívverést, ha és a konfigurációs kiszolgáló csatlakoztatva van.
- A Windows Server 2008 R2 SP1 kiszolgáló által védett sikertelenként egy a helyszíni fizikai kiszolgáló nem lehet az Azure-ból a helyszíni helyhez.
- Nem utasíthat el az alábbi esetekben:
    - Migrált gépek az Azure-bA. [További információk](migrate-overview.md#what-do-we-mean-by-migration).
    - Egy virtuális Gépet egy másik erőforrás-csoportba helyezte át.
    - Törli az Azure virtuális Gépen.
    - A virtuális gép védelmének letiltotta.
    - Létrehozta a virtuális Gépet manuálisan az Azure-ban. A gép kell eredetileg védett a helyszíni rendszer és ismételt védelem előtt az Azure-bA feladatátvétele.
    - Csak az ESXi-gazdagép sikertelen lehet. Feladat-visszavétel VMware virtuális gépek vagy fizikai kiszolgálók Hyper-V-gazdagépek, a fizikai gépek vagy a VMware-munkaállomások nem.


## <a name="next-steps"></a>További lépések

Miután a virtuális gépet egy védett állapotba került, [feladat-visszavétel kezdeményezése](vmware-azure-failback.md). A feladat-visszavételi leállítja a virtuális gép az Azure-ban, és a helyszíni virtuális gép elindul. Némi állásidővel, az alkalmazás várható. Válassza ki a feladat-visszavételhez egy időpontot, amikor az alkalmazás működését állásidő.


