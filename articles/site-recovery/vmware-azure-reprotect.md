---
title: A VMware virtuális gépek és fizikai kiszolgálók vész-helyreállítása során az Azure-ból a helyszíni helyre irányuló virtuális gépek újravédése | Microsoft Docs
description: A VMware virtuális gépek és fizikai kiszolgálók vész-helyreállítását követően az Azure-ba történő feladatátvétel után megtudhatja, hogyan térhet vissza az Azure-ból a helyszíni helyre.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: mayg
ms.openlocfilehash: cf1ccdf953781ca9b9bd17152f2cf32677997d12
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791805"
---
# <a name="reprotect-and-fail-back-machines-to-an-on-premises-site-after-failover-to-azure"></a>Gépek ismételt védetté és visszavétele a helyszíni helyre az Azure-ba történő feladatátvétel után

A helyszíni VMware virtuális gépek vagy fizikai kiszolgálók Azure-ba történő [feladatátvétele](site-recovery-failover.md) után az első lépés a helyszíni helyre történő visszaállításának első lépése a feladatátvétel során létrehozott Azure-beli virtuális gépek ismételt védelemmel való ellátása. Ez a cikk azt ismerteti, hogyan teheti ezt meg. 

A gyors áttekintésért tekintse meg az alábbi videót, amely bemutatja, hogyan végezheti el a feladatátvételt az Azure-ból egy helyszíni helyre.<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="before-you-begin"></a>Előzetes teendők

Ha sablont használt a virtuális gépek létrehozásához, győződjön meg arról, hogy minden virtuális gép saját UUID-t használ a lemezekhez. Ha a helyszíni virtuális gép UUID-je a fő célként megadott UUID-val ütközne, mert mindkettő ugyanabból a sablonból lett létrehozva, az ismételt védelem sikertelen lesz. Helyezzen üzembe egy másik fő célt, amely nem ugyanabból a sablonból lett létrehozva. Tekintse meg az alábbi információkat:
- Ha egy másik vCenter kísérli meg a feladatátvételt, győződjön meg arról, hogy az új vCenter és a fő célkiszolgáló fel van derítve. A tipikus tünet az, hogy az adattárolók nem érhetők el, vagy nem láthatók az ismételt **védelem** párbeszédpanelen.
- A helyszíni környezetbe való replikáláshoz feladat-visszavételi szabályzatra van szükség. Ez a szabályzat automatikusan létrejön a továbbítási irány házirendjének létrehozásakor. Tekintse meg az alábbi információkat:
    - Ez a szabályzat automatikusan társítva van a konfigurációs kiszolgálóval a létrehozás során.
    - Ez a szabályzat nem szerkeszthető.
    - A szabályzat beállított értékei (RPO küszöbérték = 15 perc, helyreállítási pont megőrzése = 24 óra, alkalmazás konzisztencia-pillanatképének gyakorisága = 60 perc).
- Az ismételt védelem és a feladat-visszavétel során a helyszíni konfigurációs kiszolgálónak futnia és csatlakoztatva kell lennie.
- Ha a vCenter-kiszolgáló felügyeli azokat a virtuális gépeket, amelyeken a feladat-visszavételt végzi, győződjön meg arról, hogy rendelkezik a [szükséges engedélyekkel](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) a vCenter-kiszolgálókon futó virtuális gépek felderítéséhez.
- Az ismételt védelem előtt törölje a pillanatképeket a fő célkiszolgálón. Ha Pillanatképek találhatók a helyszíni fő tárolón vagy a virtuális gépen, az ismételt védelem sikertelen lesz. A virtuális gépen lévő Pillanatképek automatikusan egyesülnek egy újravédelemi feladatokban.
- A replikációs csoport összes virtuális gépnek azonos operációsrendszer-típusnak kell lennie (vagy az összes Windows vagy az összes Linux rendszernek). A vegyes operációs rendszerekkel rendelkező replikációs csoportok jelenleg nem támogatottak a helyszíni védelemhez és a feladat-visszavételhez. Ennek az az oka, hogy a fő célhelynek a virtuális géppel megegyező operációs rendszernek kell lennie. A replikációs csoport összes virtuális gépnek ugyanazzal a fő célként kell tartoznia. 
- A fő célhelynek azonos vagy magasabb operációsrendszer-verzióval kell rendelkeznie, mint a replikált elemek operációs rendszerének verziószáma.
- A feladat-visszavételt követően konfigurációs kiszolgáló szükséges a helyszínen. A feladat-visszavétel során a virtuális gépnek léteznie kell a konfigurációs kiszolgáló adatbázisában. Ellenkező esetben a feladat-visszavétel sikertelen. Győződjön meg arról, hogy rendszeresen ütemezett biztonsági mentést készít a konfigurációs kiszolgálóról. Ha vészhelyzet van, állítsa vissza a kiszolgálót ugyanazzal az IP-címmel, hogy a feladat-visszavétel működik. 
- Az ismételt védelem és a feladat-visszavétel egy helyek közötti (S2S) VPN-vagy ExpressRoute-alapú magánhálózati kapcsolatot igényel az adatreplikáláshoz. Adja meg a hálózatot úgy, hogy az Azure-beli feladatátvételt használó virtuális gépek elérjék (pingelni) a helyszíni konfigurációs kiszolgálót. A feladatátvételi virtuális gép (ek) Azure-hálózatán kell telepítenie egy folyamat-kiszolgálót. A folyamat-kiszolgálónak képesnek kell lennie kommunikálni a helyszíni konfigurációs kiszolgálóval és a fő célkiszolgálón is.
- Abban az esetben, ha a replikált elemek IP-címeit megtartották a feladatátvétel során, S2S vagy ExpressRoute kapcsolatot kell létesíteni az Azure-beli virtuális gépek és a konfigurációs kiszolgáló feladat-visszavételi NIC-je között. Vegye figyelembe, hogy az IP-címek megőrzése megköveteli, hogy a konfigurációs kiszolgáló két hálózati adapterrel rendelkezzen – egyet a forrásoldali gépekhez, és egyet az Azure feladat-visszavétel kapcsolatához. Ennek célja, hogy elkerülje a forrás alhálózati címtartományok átfedését és a feladatátvételt a virtuális gépeken.
- Győződjön meg arról, hogy a következő portokat nyitja meg a feladatátvételhez és a feladat-visszavételhez:

    ![Feladatátvételi és feladat-visszavételi portok](./media/vmware-azure-reprotect/failover-failback.png)

- A [portok és az URL-címek engedélyezési követelményeinek](vmware-azure-deploy-configuration-server.md#prerequisites)beolvasása.

## <a name="deploy-a-process-server-in-azure"></a>Folyamat-kiszolgáló üzembe helyezése az Azure-ban

A helyszíni helyhez való visszatérés előtt szüksége van egy Azure-beli Process Serverre:

- A Process Server adatokat fogad a védett virtuális gépekről az Azure-ban, majd adatokat küld a helyszíni helyre.
- A folyamat-kiszolgáló és a védett virtuális gép között kis késleltetésű hálózatra van szükség. Ezért javasoljuk, hogy telepítsen egy Process Servert az Azure-ban. A replikálási teljesítmény magasabb, ha a folyamat-kiszolgáló közelebb van a replikálási virtuális géphez (az Azure-beli feladatátvételt végző géphez). 
- A koncepció igazolásához használhatja a helyszíni folyamat-kiszolgálót és a ExpressRoute a privát társak használatával.

Folyamat-kiszolgáló üzembe helyezése az Azure-ban:

1. Ha egy folyamat-kiszolgálót kell üzembe helyeznie az Azure-ban, tekintse [meg a folyamat-kiszolgáló beállítása az Azure-ban feladat-visszavételhez](vmware-azure-set-up-process-server-azure.md)című részt
2. Az Azure-beli virtuális gépek replikációs adatküldést küldenek a folyamat-kiszolgálónak. Konfigurálja a hálózatokat úgy, hogy az Azure-beli virtuális gépek el tudják érni a folyamat-kiszolgálót.
3. Ne feledje, hogy az Azure-ból a helyszíni rendszerbe történő replikáció csak a S2S VPN-en vagy a ExpressRoute-hálózat privát kapcsolatán keresztül történhet. Gondoskodjon arról, hogy elegendő sávszélesség álljon rendelkezésre a hálózati csatornán keresztül.

## <a name="deploy-a-separate-master-target-server"></a>Különálló fő célkiszolgáló üzembe helyezése

A fő célkiszolgáló fogadja a feladat-visszavételi adatokat. Alapértelmezés szerint a fő célkiszolgáló a helyszíni konfigurációs kiszolgálón fut. A sikertelen forgalom mennyiségétől függően azonban előfordulhat, hogy a feladat-visszavételhez létre kell hoznia egy külön fő célkiszolgáló-kiszolgálót. A következőképpen hozhat létre egyet:

* Linuxos [fő célkiszolgáló létrehozása](vmware-azure-install-linux-master-target.md) a Linux rendszerű virtuális gépek feladat-visszavételéhez. Erre szükség van. Vegye figyelembe, hogy az LVM fő célkiszolgáló nem támogatott.
* Ha szeretné, hozzon létre egy különálló fő célkiszolgáló a Windows virtuális gép feladat-visszavételéhez. Ehhez futtassa újra az egyesített telepítőt, és válassza a fő célkiszolgáló létrehozásához. [További információk](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers). 

A fő célkiszolgáló létrehozása után végezze el a következő feladatokat:

- Ha a virtuális gép a vCenter-kiszolgálón található a helyszínen, a fő célkiszolgáló számára elérhetőnek kell lennie a helyszíni virtuális gép virtuálisgép-lemezének (VMDK) fájljának. A replikált információnak a virtuális gép lemezére való írásához hozzáférés szükséges. Győződjön meg arról, hogy a helyszíni virtuális gép adattára a fő cél gazdagépére van csatlakoztatva írási/olvasási hozzáféréssel.
- Ha a virtuális gép nem található a helyszínen a vCenter-kiszolgálón, a Site Recovery szolgáltatásnak létre kell hoznia egy új virtuális gépet az ismételt védelem során. Ez a virtuális gép az ESX-gazdagépen jön létre, amelyen létrehozta a fő célt. Gondosan válassza ki az ESX-gazdagépet, hogy a feladat-visszavételi virtuális gép a kívánt gazdagépen legyen létrehozva.
- A fő célkiszolgáló tárolási vMotion nem használható. Ha a fő célkiszolgáló tárolási vMotion használ, a feladat-visszavétel sikertelen lesz. A virtuális gép nem indítható el, mert a lemezek nem érhetők el. Ennek megelőzése érdekében zárja ki a fő célkiszolgáló a vMotion listájáról.
- Ha egy fő cél az ismételt védelem után egy tárolási vMotion feladatot is végrehajt, a fő célhelyhez csatolt védett virtuálisgép-lemezek áttelepülnek a vMotion feladat céljára. Ha ezt követően próbálkozik vissza, a lemez leválasztása meghiúsul, mert a lemezek nem találhatók. Ezután nehéz lesz megkeresni a tároló fiókjaiban található lemezeket. Manuálisan kell megkeresnie őket, és csatolni kell őket a virtuális géphez. Ezt követően a helyszíni virtuális gép indítható el.
- Adjon hozzá egy adatmegőrzési meghajtót a meglévő Windows fő célkiszolgáló számára. Adjon hozzá egy új lemezt, és formázza a meghajtót. Az adatmegőrzési meghajtó segítségével leállíthatja azokat az időpontokat, amikor a virtuális gép a helyszíni helyre replikálódik. A következő egy adatmegőrzési meghajtó néhány feltétele. Ha ezek a feltételek nem teljesülnek, a meghajtó nem jelenik meg a fő célkiszolgáló számára:
    - A kötet nem használatos más célra, például a replikálás céljára.
    - A kötet nincs zárolási módban.
    - A kötet nem gyorsítótár-kötet. A fő célhely telepítése nem létezik ezen a köteten. A Process Server és a fő cél egyéni telepítési kötete nem jogosult az adatmegőrzési kötetre. Ha a Process Server és a fő cél egy kötetre van telepítve, a kötet a fő cél gyorsítótár-kötete.
    - A kötet fájlrendszerének típusa nem FAT vagy FAT32.
    - A kötet kapacitása nem nulla.
    - A Windows alapértelmezett megőrzési kötete az R-kötet.
    - A Linux alapértelmezett megőrzési kötete a/mnt/Retention.
- Új meghajtót kell hozzáadnia, ha meglévő Process Server-vagy konfigurációs kiszolgáló számítógépet vagy egy méretezési vagy feldolgozási kiszolgálót vagy egy fő célkiszolgáló számítógépét használja. Az új meghajtónak meg kell felelnie az előző követelményeknek. Ha az adatmegőrzési meghajtó nincs jelen, a portálon nem jelenik meg a kiválasztás legördülő listában. Miután hozzáadta a meghajtót a helyszíni fő célhoz, akár 15 percet is igénybe vehet, hogy a meghajtó megjelenjen a portálon a kijelölésben. A konfigurációs kiszolgálót akkor is frissítheti, ha a meghajtó 15 perc elteltével nem jelenik meg.
- Telepítse a VMware-eszközöket, vagy nyissa meg a VM-Tools eszközt a fő célkiszolgálón. Az eszközök nélkül a fő cél ESXi-gazdagépén lévő adattárolók nem észlelhetők.
- Állítsa be a `disk.EnableUUID=true` beállítást a fő célként megadott virtuális gép konfigurációs paraméterei között a VMware-ben. Ha ez a sor nem létezik, adja hozzá. Erre a beállításra akkor van szükség, ha konzisztens UUID-t biztosít a VMDK, hogy az megfelelően legyen csatolva.
- Az ESX-gazdagépen, amelyen a fő cél létrejön, rendelkeznie kell legalább egy, a hozzá csatolt virtuálisgép-fájlrendszer (VMFS) adattárral. Ha nincsenek csatolva VMFS adattárolók, **akkor az adattároló bemenete** az ismételt védelem lapon üres, és nem folytathatja a folytatást.
- A fő célkiszolgáló nem rendelkezhet pillanatképekkel a lemezeken. Ha vannak Pillanatképek, az ismételt védelem és a feladat-visszavétel sikertelen.
- A fő cél nem rendelkezhet Paravirtual SCSI-vezérlővel. A vezérlő csak LSI logikai vezérlő lehet. LSI logikai vezérlő nélkül az ismételt védelem sikertelen lesz.
- A fő cél bármelyik példányhoz legfeljebb 60 lemez csatlakoztatható. Ha a helyszíni fő célhelyre újra védelemmel ellátott virtuális gépek száma meghaladja a 60-nél több lemezt, akkor a fő célhelyre való ismételt védelem sikertelen lesz. Győződjön meg arról, hogy elegendő fő céllemez-tárolóhelytel rendelkezik, vagy telepítsen további fő célkiszolgáló-kiszolgálókat.
    

## <a name="enable-reprotection"></a>Ismételt védelem engedélyezése

Miután egy virtuális gép elindul az Azure-ban, eltarthat egy ideig, amíg az ügynök vissza nem regisztrálja a konfigurációs kiszolgálót (akár 15 percet is igénybe vehet). Ebben az időszakban nem fogja tudni újból védelemmel ellátni, és hibaüzenet jelzi, hogy az ügynök nincs telepítve. Ha ez történik, várjon néhány percet, majd próbálkozzon újra az ismételt védelemsel:


1. Válassza **a**tár  > **replikált elemek**lehetőséget. Kattintson a jobb gombbal a feladatátvétel alatt álló virtuális gépre, majd válassza az **ismételt védelem**lehetőséget. Vagy a parancsgombok közül válassza ki a gépet, majd válassza az **ismételt védelem**lehetőséget.
2. Ellenőrizze, hogy be van-e jelölve az **Azure és a** helyszíni védelem iránya.
3. A **fő célkiszolgáló** és a **folyamat-kiszolgáló**területen válassza ki a helyszíni fő célkiszolgáló és a Process Server kiszolgálót.  
4. Az **adattár**mezőben válassza ki azt az adattárolót, amelyre a helyi lemezeket helyre kívánja állítani. Ez a beállítás akkor használható, ha a helyszíni virtuális gép törlődik, és új lemezeket kell létrehoznia. Ezt a beállítást a rendszer figyelmen kívül hagyja, ha a lemezek már léteznek. Továbbra is meg kell adnia egy értéket.
5. Válassza ki az adatmegőrzési meghajtót.
6. A feladat-visszavételi szabályzat automatikusan ki van jelölve.
7. A védelem megkezdéséhez kattintson **az OK gombra** . Egy feladat elkezdi replikálni a virtuális gépet az Azure-ból a helyszíni helyre. Nyomon követheti a folyamat előrehaladását a **feladatok** lapon. Ha az ismételt védelem sikeres, a virtuális gép védett állapotba kerül.

Tekintse meg az alábbi információkat:
- Ha egy másik helyre kíván helyreállítani (a helyszíni virtuális gép törlésekor), válassza ki a fő célkiszolgáló számára konfigurált adatmegőrzési meghajtót és adattárat. Ha a helyszíni helyre végzi a feladatátvételt, a feladat-visszavételi védelmi tervben található VMware virtuális gépek ugyanazt az adattárat használják, mint a fő célkiszolgáló. Ezután létrejön egy új virtuális gép a vCenter-ben.
- Ha az Azure-beli virtuális gépet egy meglévő helyszíni virtuális gépre szeretné helyreállítani, csatlakoztassa a helyszíni virtuális gép adattárolóit olvasási/írási hozzáféréssel a fő célkiszolgáló ESXi-gazdagépén.

    ![Ismételt védelem párbeszédpanel](./media/vmware-azure-reprotect/reprotectinputs.png)

- A helyreállítási terv szintjén is újravédhető. Egy replikációs csoport csak helyreállítási terven keresztül védhető újra. Helyreállítási terv használatával történő ismételt védelem esetén meg kell adnia minden védett gép értékét.
- Ugyanazt a fő célkiszolgálót használja a replikációs csoportok ismételt védelméhez. Ha egy másik fő célkiszolgáló használatával szeretné újból védetté tenni a replikációs csoportokat, a kiszolgáló nem tud közös időpontot biztosítani.
- A helyszíni virtuális gép ki van kapcsolva az ismételt védelem során. Ez elősegíti az adatok konzisztenciáját a replikáció során. Az ismételt védelem befejeződése után ne kapcsolja be a virtuális gépet.



## <a name="common-issues"></a>Gyakori problémák

- Ha csak olvasási jogosultsággal rendelkező felhasználói vCenter-felderítést végez, és a virtuális gépek védelme, a védelem sikeres és a feladatátvétel működik. Az ismételt védelem során a feladatátvétel meghiúsul, mert nem lehet felderíteni az adattárolókat. A tünet az, hogy az adattárolók nem jelennek meg az ismételt védelem során. A probléma megoldásához frissítse a vCenter hitelesítő adatait egy olyan fiókkal, amely rendelkezik engedélyekkel, majd próbálja megismételni a feladatot. 
- Ha egy linuxos virtuális gépet hajt végre, és a helyszínen futtatja azt, láthatja, hogy a Network Manager-csomag el lett távolítva a gépről. Ez az Eltávolítás azért fordul elő, mert a Network Manager-csomag el lesz távolítva a virtuális gép Azure-ban való helyreállításakor.
- Ha egy Linux rendszerű virtuális gép statikus IP-címmel van konfigurálva, és a feladatátvétel az Azure-ba történik, az IP-cím beszerzése a DHCP-ből történik. Ha a feladatátvételt a helyszíni rendszerre végzi, a virtuális gép továbbra is DHCP-t használ az IP-cím megvásárlásához. Manuálisan jelentkezzen be a gépre, és szükség esetén állítsa vissza az IP-címet statikusra. A Windows rendszerű virtuális gépek újra megvásárolhatják statikus IP-címét.
- Ha az ESXi 5,5 Free Edition vagy a vSphere 6 hypervisor Free Edition verziót használja, a feladatátvétel sikeres lesz, de a feladat-visszavétel nem sikerül. A feladat-visszavétel engedélyezéséhez frissítsen a program próbaverziós licencére.
- Ha nem tudja elérni a konfigurációs kiszolgálót a folyamat-kiszolgálóról, a Telnet használatával ellenőrizze a 443-es porton futó konfigurációs kiszolgálóval létesített kapcsolatot. A konfigurációs kiszolgálót a Process Serverről is megpróbálhatja pingelni. A folyamat kiszolgálójának szívverése is lehet, ha a konfigurációs kiszolgálóhoz csatlakozik.
- A fizikai helyszíni kiszolgálóként védett Windows Server 2008 R2 SP1-kiszolgálót nem lehet visszaadni az Azure-ból a helyszíni helyre.
- A következő esetekben nem végezhető el a feladat-visszavétel:
    - A gépeket áttelepítette az Azure-ba. [További információk](migrate-overview.md#what-do-we-mean-by-migration).
    - Áthelyezett egy virtuális gépet egy másik erőforráscsoporthoz.
    - Törölte az Azure-beli virtuális gépet.
    - Letiltotta a virtuális gép védelmét.
    - A virtuális gépet manuálisan hozta létre az Azure-ban. Az ismételt védelem előtt a gépet először a helyszínen, a feladatátvételt pedig az Azure-ba kellett volna védeni.
    - Csak egy ESXi-gazdagépre lehet sikertelen. A VMware virtuális gépeket és fizikai kiszolgálókat nem lehet feladat-visszavétel Hyper-V-gazdagépekre, fizikai gépekre vagy VMware-munkaállomásokra.


## <a name="next-steps"></a>Következő lépések

Miután a virtuális gép védett állapotba lépett, [elindíthatja a feladat-visszavételt](vmware-azure-failback.md). A feladat-visszavétel leállítja az Azure-beli virtuális gépet, és elindítja a helyszíni virtuális gépet. Némi állásidőt vár az alkalmazáshoz. Válassza ki a feladat-visszavétel időpontját, amikor az alkalmazás képes elviselni az állásidőt.


