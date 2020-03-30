---
title: Feladat-átvétel és feladat-visszavétel az Azure Site Recovery szolgáltatásban
description: Ismerje meg a feladatátvételt és a sikertelen azure site recovery.
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: d9b54f3c452212e12419a5ffd67b116c8660308d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281807"
---
# <a name="about-on-premises-disaster-recovery-failoverfailback"></a>Helyszíni vész-helyreállítási feladatátvétel/feladat-visszavétel –

Ez a cikk áttekintést nyújt a feladatátvételről és a feladat-visszavételről a helyszíni gépek azure-beli vész-helyreállítási során az [Azure-ba](site-recovery-overview.md)az Azure Site Recovery szolgáltatással.

## <a name="recovery-stages"></a>Helyreállítási szakaszok

A Site Recovery feladatátvétele és feladat-visszavétele négy szakaszból áll:

- **1. szakasz: Feladatátvétel a helyszíni :** A helyszíni gépek azure-replikálásának beállítása után, amikor a helyszíni hely leáll, a gépek et át kell adnia az Azure-nak. Feladatátvétel után az Azure virtuális gépek replikált adatokból jönnek létre.
- **2. fázis: Az Azure virtuális gépek újbóli védelme:** Az Azure-ban újra védi az Azure-beli virtuális gépeket, hogy azok replikálódnak vissza a helyszíni webhelyre. A helyszíni virtuális gép (ha rendelkezésre áll) ki van kapcsolva az újravédelem során, az adatok konzisztenciájának biztosítása érdekében.
- **3. szakasz: Feladatátvétel az Azure-ból:** Ha a helyszíni hely a szokásos módon fut újra, futtasson egy másik feladatátvétel, ezúttal az Azure-beli virtuális gépek a helyszíni helyre. Visszaléphet arra az eredeti helyre, ahonnan a feladatátvételt átadott, vagy egy másik helyre.
- **4. szakasz: A helyszíni gépek újbóli védelme:** A rendszer biztonsági rendszerének lebukását követően ismét engedélyezze a helyszíni gépek replikálását az Azure-ba.

## <a name="failover"></a>Feladatátvétel

Feladatátvételt hajt végre az üzletmenet-folytonossági és vész-helyreállítási (BCDR) stratégia részeként.

- A BCDR-stratégia első lépéseként folyamatosan replikálja a helyszíni gépeket az Azure-ba. A felhasználók hozzáférhetnek a helyszíni forrásgépeken futó számítási feladatokhoz és alkalmazásokhoz.
- Ha szükség van, például ha van egy kimaradás a helyszínen, nem sikerül a replikálásgépek át az Azure-ba. Az Azure virtuális gépek a replikált adatok használatával jönnek létre.
- Az üzletmenet folytonossága érdekében a felhasználók továbbra is hozzáférhetnek az Azure virtuális gépeken lévő alkalmazásokhoz.

A feladatátvétel kétfázisú tevékenység:

- **Feladatátvétel:** A feladatátvétel, amely létrehoz egy Azure virtuális gép a kiválasztott helyreállítási pont használatával.
- **Véglegesítés:** A feladatátvétel után ellenőrzi a virtuális gép az Azure-ban:
    - Ezután véglegesítheti a feladatátvételt a kijelölt helyreállítási pontra, vagy másik pontot választhat a véglegesítéshez.
    - A feladatátvétel véglegesítése után a helyreállítási pont nem módosítható.


## <a name="connect-to-azure-after-failover"></a>Csatlakozás az Azure-hoz feladatátvétel után

Az RDP/SSH használatával feladatátvétel után létrehozott Azure virtuális gépekhez való csatlakozáshoz számos követelmény áll fenn.

**Feladatátvétel** | **Helyen** | **Műveletek**
--- | --- | ---
**Azure VM (Windows(** | A helyszíni gépen feladatátvétel előtt | **Hozzáférés az interneten keresztül**: Rdp engedélyezése. Győződjön meg arról, hogy a TCP- és UDP-szabályok nyilvánosak, és hogy az RDP a **Windows tűzfal** > **engedélyezett alkalmazásai**összes profiljához engedélyezett. **Public**<br/><br/> Hozzáférés a helyek közötti **VPN-en**keresztül: Rdp engedélyezése a számítógépen. Ellenőrizze, hogy az RDP engedélyezett-e a **Windows tűzfal** -> **engedélyezett alkalmazásaiban és szolgáltatásaiban** **a tartományi és magánhálózatok** esetében.<br/><br/>  Győződjön meg arról, hogy az operációs rendszer SAN-házirendje **OnlineAll**. [További információ](https://support.microsoft.com/kb/3031135).<br/><br/> Győződjön meg arról, hogy nincsenek függőben lévő Windows-frissítések a virtuális gép, amikor elindítja a feladatátvételt. Előfordulhat, hogy a Windows Update akkor indul el, amikor feladatátvételt végez, és a frissítések elvégzéséig nem tud bejelentkezni a virtuális gépre.
**Windows t futtató Azure virtuális gép** | Az Azure virtuális gép feladatátvétel után |  [Nyilvános IP-cím hozzáadása](https://aka.ms/addpublicip) a virtuális gép számára.<br/><br/> A hálózati biztonsági csoport szabályok a feladatátvételi virtuális gép (és az Azure-alhálózat, amelyhez csatlakozik) engedélyeznie kell a bejövő kapcsolatokat az RDP-port.<br/><br/> Ellenőrizze **a rendszerindítási diagnosztika** segítségével ellenőrizze a virtuális gép képernyőképének ellenőrzéséhez. Ha nem tud csatlakozni, ellenőrizze, hogy a virtuális gép fut-e, és tekintse át a [hibaelhárítási tippeket.](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)
**Linuxot futtató Azure virtuális gép** | A helyszíni gépen feladatátvétel előtt | Győződjön meg arról, hogy a biztonságos rendszer szolgáltatás a virtuális gép van beállítva, hogy automatikusan elindul a rendszer indításakor.<br/><br/> Ellenőrizze, hogy a tűzfalszabályok engedélyezik-e az SSH-kapcsolatot.
**Linuxot futtató Azure virtuális gép** | Az Azure virtuális gép feladatátvétel után | A hálózati biztonsági csoport szabályok a feladatátvételi virtuális gép (és az Azure-alhálózat, amelyhez csatlakozik) engedélyeznie kell a bejövő kapcsolatokat az SSH-port.<br/><br/> [Nyilvános IP-cím hozzáadása](https://aka.ms/addpublicip) a virtuális gép számára.<br/><br/> Ellenőrizze **a rendszerindítási diagnosztika** egy screenshot a virtuális gép.<br/><br/>

## <a name="types-of-failover"></a>A feladatátvétel típusai

A Site Recovery különböző feladatátvételi beállításokat biztosít.

**Feladatátvétel** | **Részletek** | **Helyreállítás** | **Munkafolyamat**
--- | --- | --- | ---
**Feladatátvétel tesztelése** | A BCDR-stratégiát érvényesítő fúró futtatásához használható adatvesztés vagy állásidő nélkül.| Létrehoz egy másolatot a virtuális gép az Azure-ban, nincs hatással a folyamatos replikáció, vagy az éles környezetben. | 1. Futtasson egy teszt feladatátvételt egyetlen virtuális számítógépen, vagy több virtuális gépen egy helyreállítási tervben.<br/><br/> 2. Válassza ki a teszt feladatátvételhez használandó helyreállítási pontot.<br/><br/> 3. Válasszon ki egy Azure-hálózatot, amelyben az Azure virtuális gép lesz található, amikor jön létre a feladatátvétel után. A hálózat csak a teszt feladatátvételhez használatos.<br/><br/> 4. Ellenőrizze, hogy a fúró a várt módon működött-e. A Site Recovery automatikusan törli az Azure-ban a fúró során létrehozott virtuális gépeket.
**Tervezett feladatátvétel-Hyper-V**  | Általában a tervezett állásidő.<br/><br/> A forrásvirtuális gépek le vannak állítva. A feladatátvétel megkezdése előtt a rendszer szinkronizálja a legújabb adatokat. | Nulla adatvesztés a tervezett munkafolyamathoz. | 1. Tervezzen meg egy állásidő-karbantartási időszakot, és értesítse a felhasználókat.<br/><br/> 2. A felhasználók felé néző alkalmazások offline állapotba helyezése.<br/><br/> 3. Tervezett feladatátvétel kezdeményezése a legújabb helyreállítási ponttal. A feladatátvétel nem fut, ha a számítógép nem áll le, vagy ha hibák at tapasztal.<br/><br/> 4. A feladatátvétel után ellenőrizze, hogy az Azure virtuális gép replikája aktív-e az Azure-ban.<br/><br/> 5. Véglegesítse a feladatátvételt a befejezéshez. A véglegesítési művelet törli az összes helyreállítási pontot.
**Feladatátvétel-Hyper-V** | Általában akkor fut, ha nem tervezett kimaradás van, vagy az elsődleges hely nem érhető el.<br/><br/> Szükség esetén állítsa le a virtuális gép, és szinkronizálja a végső módosításokat a feladatátvétel megkezdése előtt.  | Minimális adatvesztés az alkalmazások számára. | 1. Indítsa el a BCDR tervet. <br/><br/> 2. Feladatátvétel kezdeményezése. Adja meg, hogy a Site Recovery állítsa le a virtuális gép, és szinkronizálja/replikálja a legújabb módosításokat, mielőtt a feladatátvétel.<br/><br/> 3. Az alábbi táblázatban összefoglalva számos helyreállításipont-beállításra átadhat át ennyire.<br/><br/> Ha nem engedélyezi a virtuális gép leállításának lehetőségét, vagy ha a Site Recovery nem tudja leállítani, a legújabb helyreállítási pontot használja a lesz használva.<br/>A feladatátvétel akkor is fut, ha a gép nem lehet leállítani.<br/><br/> 4. Feladatátvétel után ellenőrizze, hogy az Azure virtuális gép replikája aktív-e az Azure-ban.<br/> Ha szükséges, válasszon egy másik helyreállítási pontot a megőrzési ablak24 óra.<br/><br/> 5. Véglegesítse a feladatátvételt a befejezéshez. A véglegesítési művelet törli az összes rendelkezésre álló helyreállítási pontot.
**Feladatátvétel-VMware** | Általában akkor fut, ha nem tervezett kimaradás van, vagy az elsődleges hely nem érhető el.<br/><br/> Tetszés szerint adja meg, hogy a Site Recovery megpróbálja elindítani a virtuális gép leállítását, és szinkronizálja és replikálja a végső módosításokat a feladatátvétel megkezdése előtt.  | Minimális adatvesztés az alkalmazások számára. | 1. Indítsa el a BCDR tervet. <br/><br/> 2. Feladatátvétel kezdeményezése a Site Recovery-ből. Adja meg, hogy a Site Recovery megpróbálja-e elindítani a virtuális gép leállítását és a szinkronizálást a feladatátvétel futtatása előtt.<br/> A feladatátvétel akkor is fut, ha a gépek nem lehet leállítani.<br/><br/> 3. A feladatátvétel után ellenőrizze, hogy az Azure virtuális gép replikája aktív-e az Azure-ban. <br/>Ha szükséges, válasszon egy másik helyreállítási pontot a 72 órás megőrzési ablakból.<br/><br/> 5. Véglegesítse a feladatátvételt a befejezéshez. A véglegesítési művelet törli az összes helyreállítási pontot.<br/> Windows virtuális gépek esetén a Site Recovery letiltja a VMware-eszközöket feladatátvétel közben.

## <a name="failover-processing"></a>Feladatátvétel feldolgozása

Bizonyos esetekben a feladatátvétel további feldolgozást igényel, amely körülbelül 8–10 percet vesz igénybe. Előfordulhat, hogy hosszabb tesztelési feladatátvételi időket észlel:

* A 9.8-nál régebbi Mobility szolgáltatásverziót futtató VMware virtuális gépek.
* Fizikai szerverek.
* VMware Linux virtuális gépek.
* A fizikai kiszolgálókként védett hyper-V virtuális gépek.
* VMware virtuális gépek, amelyek nem rendelkeznek a DHCP-szolgáltatás engedélyezve van.
* VMware virtuális gépek, amelyek nem rendelkeznek a következő rendszerindító illesztőprogramokkal: storvsc, vmbus, storflt, intelide, atapi.

## <a name="recovery-point-options"></a>Helyreállítási pont beállításai

A feladatátvétel során számos helyreállításipont-beállítást választhat.

**Beállítás** | **Részletek**
--- | ---
**Legfrissebb (legalacsonyabb RPO)** | Ez a beállítás biztosítja a legalacsonyabb helyreállítási pont célkitűzést (RPO). Először feldolgozza az összes adatot, amely et a Site Recovery szolgáltatásnak küldött, hogy hozzon létre egy helyreállítási pontot az egyes virtuális gépek, mielőtt átad neki. Ez a helyreállítási pont az összes adatot replikált site recovery, amikor a feladatátvétel t.
**Legutóbbi feldolgozott**  | Ez a beállítás átadja a virtuális gépeknek a Site Recovery által feldolgozott legújabb helyreállítási pontot. Egy adott virtuális gép legújabb helyreállítási pontjának megtekintéséhez tekintse meg **a legújabb helyreállítási pontokat** a virtuális gép beállításaiban. Ez a lehetőség alacsony helyreállítási időre vonatkozó célkitűzést (RTO) nyújt, mert a rendszer nem tölt időt a feldolgozatlan adatok feldolgozásával.
**Legújabb alkalmazás-konzisztens** |  Ez a beállítás átadja a virtuális gépeket a Site Recovery által feldolgozott legújabb alkalmazáskonzisztens helyreállítási pontnak, ha az alkalmazáskonzisztens helyreállítási pontok engedélyezve vannak. Ellenőrizze a legújabb helyreállítási pontot a virtuális gép beállításaiban.
**Legújabb több virtuális gép feldolgozása** | Ez a beállítás egy vagy több virtuális géptöbb virtuális gép konzisztenciájával rendelkező helyreállítási tervek esetén érhető el. Virtuális gépek a beállítás engedélyezve van feladatátvétel a legújabb közös multi-vm konzisztens helyreállítási pont. A tervben lévő bármely más virtuális gép feladatátvételt a legújabb feldolgozott helyreállítási pontra.
**Legújabb többvm-es alkalmazás-konzisztens** |  Ez a beállítás egy vagy több virtuális géptöbb virtuális gép konzisztenciájával rendelkező helyreállítási tervek esetén érhető el. Virtuális gépek, amelyek egy replikációs csoport részét képezik, feladatátvétela a legújabb közös többvirtuális gép alkalmazás-konzisztens helyreállítási pont. Más virtuális gépek feladatátvétela a legújabb alkalmazáskonzisztens helyreállítási pont.
**Egyéni** | Ezzel a beállítással egy adott virtuális gép egy adott helyreállítási ponton időben feladatátvételt. Ez a beállítás nem érhető el a helyreállítási tervek.

> [!NOTE]
> A helyreállítási pontok nem telepíthetők át egy másik Helyreállítási szolgáltatások tárolóba.

## <a name="reprotectionfailback"></a>Újravédelem/feladat-visszavétel

Az Azure-ba való feladatátvétel után a replikált Azure-beli virtuális gépek nem védett állapotban vannak.

- Első lépésként a helyszíni hely vissza, el kell indítania az Azure virtuális gépek replikálása a helyszíni. Az újravédelmi folyamat a feladatátvétel től függ.
- Miután a gépek replikálják az Azure-ból a helyszíni, futtathatja a feladatátvételt az Azure-ból a helyszíni helyre.
- Miután a gépek ismét a helyszínen futnak, engedélyezheti a replikációt, hogy azok replikálódjanak az Azure-ba a vész-helyreállításhoz.

A feladat-visszavétel a következőképpen működik:

- A feladat-visszavételhez a virtuális gépnek legalább egy helyreállítási pontra van szüksége a visszavételhez. A helyreállítási tervben a tervben lévő összes virtuális gépnek legalább egy helyreállítási pontra van szüksége.
- Azt javasoljuk, hogy használja a **legújabb** helyreállítási pontot a feladat-visszavétel (ez egy összeomlás-konzisztens pont).
    - Van egy alkalmazás-konzisztens helyreállítási pont opció. Ebben az esetben egyetlen virtuális gép helyreállítja a legújabb elérhető alkalmazás-konzisztens helyreállítási pontot. A replikációs csoporttal rendelkező helyreállítási terv esetén minden replikációs csoport helyreáll a rendelkezésre álló közös helyreállítási pontra.
    - Az alkalmazáskonzisztens helyreállítási pontok időben lemaradhatnak, és adatvesztés következhet be.
- Az Azure-ból a helyszíni helyre történő feladatátvétel során a Site Recovery leállítja az Azure virtuális gépeket. A feladatátvétel véglegesítésekén a Site Recovery eltávolítja a sikertelen Azure-beli virtuális gépeket az Azure-ban.


## <a name="vmwarephysical-reprotectionfailback"></a>VMware/fizikai újravédelem/feladat-visszavétel

A VMware-gépek és a fizikai kiszolgálók újbóli védelméhez és visszavételéhez az Azure-ból a helyszíni adatokra, szükség van egy feladat-visszavételi infrastruktúrára, és számos követelményre van szükség.

- **Ideiglenes folyamatkiszolgáló az Azure-ban:** Az Azure-ból való visszavételhez be kell állítania egy Azure-beli virtuális gép-kiszolgálót, amely az Azure-ból történő replikáció kezelésére szolgál. Ez a virtuális gép a feladatok visszaadását követően törölhető.
- **VPN-kapcsolat**: A feladat-visszavételhez szükség van egy VPN-kapcsolat (vagy ExpressRoute) az Azure-hálózatról a helyszíni helyre.
- **Külön fő célkiszolgáló**: Alapértelmezés szerint a fő célkiszolgáló, amely telepítve volt a konfigurációs kiszolgálóa a helyszíni VMware VM kezeli a feladat-visszavétel. Ha nagy mennyiségű forgalmat kell visszaadnia, ehhez hozzon létre egy külön helyszíni fő célkiszolgálót.
- **Feladat-visszavételi szabályzat**: A helyszíni helyre történő újbóli replikáláshoz feladat-visszavételi szabályzatra van szükség. Ez a szabályzat automatikusan létrejön, amikor létrehoz egy replikációs szabályzatot a helyszíni Azure-ba.
    - Ez a házirend automatikusan társítva lesz a konfigurációs kiszolgálóval.
    - Ez a házirend nem szerkeszthető.
    - Házirend-értékek: RPO-küszöbérték – 15 perc; Helyreállítási pont megtartása - 24 óra; Alkalmazáskonzisztens pillanatfelvétel-gyakoriság - 60 perc.

További információ a VMware/fizikai újravédelemről és feladat-visszavételről:
- [Tekintse át](vmware-azure-reprotect.md#before-you-begin) az újravédelemre és a feladat-visszavételre vonatkozó további követelményeket.
- [Folyamatkiszolgáló üzembe helyezése](vmware-azure-prepare-failback.md#deploy-a-process-server-in-azure) az Azure-ban.
- Külön fő célkiszolgáló [telepítése.](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server)

Amikor újra védi az Azure-beli virtuális gépeket a helyszíni, megadhatja, hogy szeretné visszaadni az eredeti helyre, vagy egy másik helyre.

- **Eredeti hely-helyreállítás:** Ez leáll az Azure-ból ugyanarra a forrásra a helyszíni gépen, ha létezik. Ebben a forgatókönyvben csak a módosítások replikálódik vissza a helyszíni.
- **Alternatív hely helyreállítása:** Ha a helyszíni gép nem létezik, visszaléphet az Azure-ból egy másik helyre. Amikor újra védi az Azure virtuális gép a helyszíni, a helyszíni gép jön létre. A teljes adatreplikáció az Azure-ból a helyszíni adatokba történik. - - [Tekintse át](concepts-types-of-failback.md) a hely-feladat-visszavétel követelményeit és korlátait.



## <a name="hyper-v-reprotectionfailback"></a>Hyper-V újravédelem/feladat-visszavétel

A Hyper-V virtuális gépek újbóli védelme és visszavétele az Azure-ból a helyszíni létesítményekbe:

- Csak a tárfiók használatával replikáló Hyper-V virtuális gépek et lehet visszavonni. A felügyelt lemezekkel replikáló Hyper-V virtuális gépek feladat-visszavétele nem támogatott.
- A helyszíni Hyper-V-állomásokat (vagy ha használják a Rendszerközpont VMM-t) az Azure-hoz kell csatlakoztatni.
- Egy tervezett feladat-visszavételt futtat az Azure-ból a helyszíni.
- Nem kell konkrét összetevőket beállítani a Hyper-V VM feladat-visszavételhez.
- A tervezett feladatátvétel során megadhatja az adatok feladat-visszavétel előtti szinkronizálásának beállításait:
    - **Adatok szinkronizálása feladatátvétel előtt:** Ez a beállítás minimálisra csökkenti a virtuális gépek állásidejét, mivel a gépeket a leállításuk nélkül szinkronizálja.
        - 1. fázis: Pillanatképet készít az Azure virtuális gépről, és átmásolja azt a helyszíni Hyper-V gazdagépre. A gép továbbra is fut az Azure-ban.
        - 2. fázis: Leállítja az Azure virtuális gép, hogy nincs új változás történik ott. A különbözeti módosítások végleges készlete átkerül a helyszíni kiszolgálóra, és elindul a helyszíni virtuális gép.
    - **Adatok szinkronizálása csak feladatátvétel közben**: Ez a beállítás gyorsabb, mert arra számítunk, hogy a lemez nagy része megváltozott, és így nem végez ellenőrzőösszeg-számításokat. Ez végre egy letöltés a lemez. Azt javasoljuk, hogy használja ezt a beállítást, ha a virtuális gép már fut az Azure-ban egy ideig (egy hónap vagy több), vagy ha a helyszíni virtuális gép törölve lett.

[További információ](hyper-v-azure-failback.md) a Hyper-V újbóli védelméről és a feladat-visszavételről.

Amikor újra védi az Azure-beli virtuális gépeket a helyszíni, megadhatja, hogy szeretné visszaadni az eredeti helyre, vagy egy másik helyre.

- **Eredeti hely-helyreállítás:** Ez leáll az Azure-ból ugyanarra a forrásra a helyszíni gépen, ha létezik. Ebben az esetben az előző eljárásban ismertetett szinkronizálási beállítások közül választhat.
- **Alternatív hely helyreállítása:** Ha a helyszíni gép nem létezik, visszaléphet az Azure-ból egy másik helyre. Amikor újra védi az Azure virtuális gép a helyszíni, a helyszíni gép jön létre. Ezzel a beállítással azt javasoljuk, hogy válassza ki az adatok feladatátvétel előtti szinkronizálásának lehetőségét.
- [Tekintse át](hyper-v-azure-failback.md) a hely-feladat-visszavétel követelményeit és korlátait.


Miután nem sikerült visszaad a helyszíni hely, engedélyezi **a Reverse Replicate** a virtuális gép replikálása az Azure-ba, a ciklus befejezése.




## <a name="next-steps"></a>További lépések
- Adott [VMware virtuális gépek](vmware-azure-tutorial-failover-failback.md) feladatátvétele
- Adott [Hyper-V virtuális gépek feladatátvétele.](hyper-v-azure-failover-failback-tutorial.md)
- [Hozzon létre](site-recovery-create-recovery-plans.md) egy helyreállítási tervet.
- A [helyreállítási tervben lévő virtuális gépek feladatátvétele.](site-recovery-failover.md)
- [Felkészülés](vmware-azure-failback.md) VMware-újravédelem és feladat-visszavétel.
- [Hyper-V virtuális gépek visszavétele.](hyper-v-azure-failback.md)

