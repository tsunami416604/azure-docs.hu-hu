---
title: "Állítsa a virtuális gépek az Azure-ból a helyszíni helyhez |} Microsoft Docs"
description: "Az Azure virtuális gépek feladatátvétel után a feladat-visszavételt a virtuális gépeket kapcsolja vissza a helyszíni is kezdeményezhető. Útmutató: a feladat-visszavétel előtt lássa el újból védelemmel."
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: rajanaki
ms.openlocfilehash: cd5e53b49a850acf851e8351b5e14e2993176435
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="reprotect-machines-from-azure-to-an-on-premises-site"></a>Lássa el újból védelemmel gépek az Azure-ból a helyszíni helyhez

Miután [feladatátvételi](site-recovery-failover.md) helyszíni VMware virtuális gépek vagy fizikai kiszolgálók Azure-ba, a sikertelen első lépése a helyszíni webhelyre, hogy lássa el újból védelemmel a feladatátvétel során létrehozott Azure virtuális gépeken. Ez a cikk ismerteti, hogyan ehhez. 

Gyors áttekintését tekintse meg a következő videó bemutatja, hogyan hogy áthelyezze a feladatokat az Azure-ból a helyszíni-helyhez.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="before-you-start"></a>Előkészületek

Ha egy sablon használatával a virtuális gépek létrehozásakor, győződjön meg arról, hogy minden virtuális gép rendelkezik-e a saját a lemezek UUID azonosítója. Ha a helyszíni virtuális gép UUID ütközik a, a fő célkiszolgálón, mert mindkét ugyanazon sablon alapján létrehozott, az ismételt védelem sikertelen lesz. Telepítsen egy másik fő célkiszolgáló ugyanazon sablonból nem lett létrehozva.
- Ha vissza egy másik vCenter az sikertelen lesz, győződjön meg arról, hogy az új vCenter és a fő célkiszolgáló felderített. Egy tipikus tünete, hogy a datastores nem elérhetők és látható a **lássa el újból védelemmel** párbeszédpanel megnyitásához.
- Replikálja a helyszíni, szüksége lesz egy feladatátvételi házirendhez. Ez a házirend automatikusan létrehozása egy előre házirend létrehozásakor. Vegye figyelembe:
    - Ez a házirend létrehozása közben a konfigurációs kiszolgálóhoz tartozó automatikus lekérdezi.
    - Ez a házirend gyűjteménytulajdonság nem szerkeszthető.
    - A készlet a házirend értékei (helyreállítási Időkorlát küszöbértéke = 15 perc, a helyreállítási pontok megőrzésének ideje 24 óra múlva App konzisztencia pillanatfelvételeinek gyakoriságát = = 60 perc)
- Ismételt védelem és a feladat-visszavétel során a helyszíni konfigurációs kiszolgáló kell futnia, és csatlakoztatva.
- Ha a vCenter-kiszolgáló kezeli a virtuális gépeket vissza fogjuk sikertelenül győződjön meg arról, hogy rendelkezik a [szükséges engedélyek](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) a vCenter-kiszolgáló a virtuális gépek felderítése.
- Törölje a pillanatképeket a fő célkiszolgálón, ahhoz, hogy lássa el újból védelemmel. Ha pillanatképeket megtalálható a helyszíni fő célkiszolgáló vagy a virtuális gépet, ismételt védelem sikertelen lesz. A pillanatképek a virtuális gépen a védelem-újrabeállítási feladat során automatikusan összefésülése megtörténjen.
- Az összes virtuális gép egy replikálási csoport azonos típusú operációs rendszer (az összes Windows vagy az összes Linux) kell lennie. Vegyes operációs rendszerrel egy replikációs csoportot a védelem-újrabeállítási és a helyszíni feladat-visszavétel jelenleg nem támogatott. Ennek az oka, hogy a fő célkiszolgálón ugyanazt az operációs rendszert a virtuális gépként kell lennie, és a replikációs csoport összes virtuális gépet kell rendelkeznie a azonos fő célkiszolgálót. 
- A konfigurációs kiszolgáló szükségesek helyszíni esetén, a feladat-visszavételt. A feladat-visszavétel során a virtuális gép léteznie kell a konfigurációs kiszolgáló adatbázisában. Ellenkező esetben a feladat-visszavétel nem sikerül. Győződjön meg arról, hogy az egy rendszeresen ütemezett biztonsági mentések a konfigurációs kiszolgáló. Katasztrófa esetén állítsa vissza a kiszolgáló ugyanazon IP-címét, hogy a feladat-visszavétel működik.
- Ismételt védelem és a feladat-visszavétel szükséges a pont-pont VPN-en keresztül replikálja az adatokat. Adja meg a hálózati, így az Azure virtuális gép feladatátadása képes elérni (ping) a helyszíni konfigurációs kiszolgáló. A folyamatkiszolgáló az Azure-hálózat virtuális gép feladatátadása is telepíthet. Ez a folyamat-kiszolgáló képes kommunikálni a helyszíni konfigurációs kiszolgáló kell.
- Győződjön meg arról, hogy a feladatátvétel és a feladat-visszavétel a következő portok megnyitása.

    ![A feladatátvétel és a feladat-visszavétel portok](./media/vmware-azure-reprotect/failover-failback.png)

## <a name="deploy-a-process-server-in-azure"></a>Az Azure-ban folyamat-kiszolgáló központi telepítése

Ahhoz, hogy visszaadják feladataikat a helyszíni hely szükség lehet a folyamatkiszolgáló az Azure-ban:
- A folyamatkiszolgáló] adatokat fogad a védett virtuális gép az Azure-ban, és adatokat küld a helyszíni hely.
- A kis késleltetésű hálózatra azért szükség a folyamatkiszolgáló és a védett virtuális gép között. Általánosságban elmondható figyelembe kell vennie a késés, amikor arról dönt, hogy szükséges-e a folyamatkiszolgáló az Azure-ban:
    - Ha egy ExpressRoute-kapcsolat beállítása, használhatja egy helyszíni folyamatkiszolgáló szeretnék adatokat küldeni, mert a virtuális gép és a folyamatkiszolgáló között késleltetés alacsony.
    - De ha csak egy S2S VPN-je, ajánlott az Azure-ban a folyamatkiszolgáló telepítése.
    - Feladat-visszavétel során az Azure-alapú folyamatkiszolgáló használatát javasoljuk. A replikációs teljesítményt nagyobb, ha a folyamatkiszolgáló a replikáló virtuális gépre (a átvevő gép az Azure-ban) közelebb. Egy-a-koncepció igazolása használhatja a helyszíni folyamatkiszolgáló ExpressRoute együtt a magánhálózati társviszony-létesítés.

Telepítse az alábbiak szerint:

1. Ha kell telepítenie a folyamatkiszolgáló az Azure-ban, hajtsa végre az [ezeket az utasításokat](vmware-azure-set-up-process-server-azure.md)
2. Az Azure virtuális gépeken futó replikációs adatokat küld a folyamatkiszolgáló. Konfigurálja a hálózatokat, hogy az Azure virtuális gépek érhető el a folyamatkiszolgáló.
3. Ne feledje, hogy a replikáció az Azure-ból a helyszíni akkor fordulhat elő, csak az S2S VPN-kapcsolaton keresztül, vagy a magánhálózati társviszony-létesítés az ExpressRoute-hálózaton keresztül. Győződjön meg arról, hogy elegendő sávszélesség érhető el, hogy hálózati csatornán keresztül.

## <a name="deploy-a-separate-master-target-server"></a>Egy külön fő célkiszolgáló telepítése

A fő célkiszolgáló fogadja a feladat-visszavételi adatokat. Alapértelmezés szerint a fő célkiszolgáló a helyszíni konfigurációs kiszolgálón fut. Azonban nem sikerült visszaírt forgalom mennyiségét, attól függően szükség lehet egy külön fő célkiszolgáló feladat-visszavételi létrehozásához. Megtudhatja, hogyan szeretne létrehozni egyet:

    * [Hozzon létre egy Linux fő célkiszolgáló](vmware-azure-install-linux-master-target.md) Linux virtuális gépek feladat-visszavételre. Erre azért szükség.
    * Másik lehetőségként létrehozhatja egy külön fő célkiszolgáló Windows virtuális gép feladat-visszavételre. Ehhez futtassa újra a telepítőt az egységes, és létrehozhat egy fő célkiszolgálóra. [További információk](physical-azure-set-up-source.md#run-azure-site-recovery-unified-setup).

Miután létrehozott egy fő célkiszolgálóra, tegye a következőket:

- Ha a virtuális gép jelen helyszíni a vCenter Server, a fő célkiszolgáló szüksége a helyszíni virtuális gép vmdk-fájl elérésére. Hozzáférés szükséges a virtuális gépek lemezeit a replikált adatokat írni. Győződjön meg arról, hogy a helyszíni virtuális gép adattároló csatlakoztatva van-e a fő célkiszolgáló gazdagépre olvasási/írási hozzáférést.
- Ha a virtuális gép nem található a helyi a vCenter Server, a Site Recovery szolgáltatásban hozzon létre egy új virtuális gép ismételt védelem alatt kell. Ezt a virtuális gépet az ESX-gazdagépen, amelyen hoz létre a fő célkiszolgáló jön létre. Válassza ki, az ESX-gazdagépet, hogy a feladat-visszavétel a virtuális gép jön létre, amelyet a gazdagépen.
- Nem használható Storage vmotion szolgáltatások használata a fő célkiszolgáló server *. A sikertelen feladat-visszavétel okozhatnak. A virtuális gép nem indítható el, mert a lemez nem érhetők el azt. A probléma elkerülése érdekében zárja ki a fő célkiszolgálóra a vMotion listájáról.
- A fő célkiszolgálón a Storage vMotion feladatok ismételt védelem után megy keresztül, ha a védett virtuális gépek lemezeket a fő célkiszolgálóhoz csatlakoztatott telepítse át a vMotion feladat célja. Ezt követően ismét próbálja meg, ha a lemez terméknek sikertelen lesz, mivel a lemezek nem találhatók. A javítást nehéz megtalálni a lemezeket a storage-fiókok. Keresse meg őket manuálisan, és csatolja azokat a virtuális gép kell. Ezt követően a helyszíni virtuális gép rendszerindításra alkalmas.
- Adjon hozzá a meglévő Windows fő célkiszolgáló adatmegőrzési meghajtót. Adjon hozzá egy új lemezt, és formázza a meghajtót. Az adatmegőrzési meghajtó állítsa le a pontok időpont, amikor a virtuális gép replikálja vissza a helyszíni hely szolgál. Az alábbiakban néhány adatmegőrzési meghajtó feltétel. Ezek a feltételek nélkül a meghajtó nem jelenik a fő célkiszolgálón.
    - A kötet nem szolgál más célra, például a replikációs cél.
    - A kötet nem zár módban van.
    - A kötet nincs a gyorsítótár kötetén. A fő célkiszolgáló telepítése nem létezik a köteten található. A folyamat kiszolgáló és a fő célkiszolgáló egyéni telepítési lemez nem jogosult az adatmegőrzési kötet. A folyamatkiszolgáló és fő célkiszolgáló egy olyan köteten vannak telepítve, ha a lemez a fő célkiszolgáló a gyorsítótár kötetén.
    - A kötet fájlrendszerének nincs FAT vagy FAT32.
    - A kötet kapacitása nem nulla.
    - Az alapértelmezett adatmegőrzési kötet a Windows, az R-kötet.
    - Az alapértelmezett adatmegőrzési kötet Linux /mnt/retention.
- Egy meglévő folyamat kiszolgálóhoz vagy konfigurációs kiszolgáló vagy terjedő skálán vagy folyamat server/fő cél kiszolgáló használata, adjon hozzá egy új meghajtót kell. Az új meghajtó meg kell felelnie a fenti követelményeknek. Ha az adatmegőrzési meghajtó nincs jelen, nem jelenik meg a legördülő listában a portálon. Után hozzáadhat egy meghajtót a helyszíni fő célkiszolgáló, megjelennek a portálon a kijelölt meghajtó akár 15 percet vesz igénybe. Ha a meghajtó nem jelenik meg 15 perc után is frissítheti a konfigurációs kiszolgáló.
- A VMware-eszközök telepítése a fő célkiszolgálón. A VMware-eszközök nélkül a fő célkiszolgáló ESXi-állomáson datastores nem észlelhető.
- Állítsa be a `disk.EnableUUID=true` beállítása a konfigurációs paraméterek, a fő célkiszolgáló virtuális gép VMware-ben. Ha a sor nem létezik, adja hozzá. Ez a beállítás szükséges, hogy azt a megfelelő csatlakoztatja, adja meg a virtuális gép lemezét (VMDK) való egységes UUID.
- A fő célkiszolgáló rendelkeznie kell legalább egy virtuálisgép-Fájlrendszereinek datastore csatolva. Ha nincs, a **Datastore** a védelem-újrabeállítási oldalon bemenet üres lesz, és nem folytathatja a műveletet.
- A fő célkiszolgáló nem lehet pillanatképeket a lemezeken. Ha pillanatképek vannak, ismételt védelem és a feladat-visszavétel nem.
- A fő célkiszolgáló Paravirtual SCSI-vezérlőhöz nem tartozhat. A vezérlő csak egy LSI Logic vezérlő lehet. Egy LSI Logic vezérlőt ismételt védelem sikertelen lesz.
- Adott esetben atmst 60 lemez nem csatlakoztatható a fő célkiszolgáló is biztosítani. Ha alatt látható el újra védelemmel történő a helyszíni fő célkiszolgáló virtuális gépek száma több, mint 60 lemezek sum teljes száma, majd a fő célkiszolgálóhoz reprotects indul sikertelenek lesznek. Győződjön meg arról, hogy rendelkezik-e elegendő fő cél a lemez üzembe helyezési ponti, vagy további fő célkiszolgálóra telepíteni.
    

## <a name="enable-reprotection"></a>Ismételt védelem engedélyezése

Miután a virtuális gép elindul az Azure-ban, csak bizonyos idő az ügynök regisztrálása a konfigurációs kiszolgálóra (legfeljebb 15 perc). Ez idő alatt nem lehet majd lássa el újból védelemmel, és a hibaüzenet azt jelzi, hogy az ügynök nincs telepítve. Ha ez történik, várjon néhány percet, és próbálkozzon az ismételt védelem újra.


1. A **tároló** > **replikált elemek**, kattintson a jobb gombbal a virtuális gépet, a feladatátvétel megtörtént, és válassza **védelmének újbóli beállításához**. Is kattintson a gépre, és válassza ki **védelmének újbóli beállításához** a parancs gombok közül.
2. Ellenőrizze a védelem irányát **Azure a helyszíni**, már be van jelölve.
3. A **fő célkiszolgáló** és **Folyamatkiszolgáló**, válassza ki a helyszíni fő célkiszolgáló és a folyamatkiszolgáló.
4. A **Datastore**, válassza ki, amelyhez végre kívánja hajtani a lemezek helyi az adattárral. Ez a beállítás használható, ha a helyszíni virtuális gép törlődik, és új lemezek létrehozásához szükség. A rendszer figyelmen kívül hagyja ezt a beállítást, ha a lemez már létezik, de továbbra is meg kell adnia egy értéket.
5. Válassza ki az adatmegőrzési meghajtó.
6. A feladat-visszavételi szabályzat automatikusan ki van jelölve.
7. Kattintson az **OK** gombra az ismételt védelem megkezdéséhez. Egy feladat elkezdi replikálni a virtuális gépet az Azure-ból a helyszíni helyre. A **Feladatok** lapon követheti nyomon a folyamat állapotát. Miután az ismételt védelem sikeres, a virtuális gép módba lép, egy védett állapotban.

Vegye figyelembe:
- Ha azt szeretné, helyreállítása másik helyre (a helyszíni virtuális gép törlésekor), válassza az adatmegőrzési meghajtó és a fő célkiszolgáló konfigurált adattároló. Nem vissza a helyszíni hely, a VMware virtuális gépeket a feladat-visszavétel védelmi tervet a fő célkiszolgáló ugyanazon adattár használják. Új virtuális gép vCenter létrejön.
- Ha szeretné helyreállítani a virtuális gépet az Azure-on egy meglévő helyszíni virtuális géphez, olvasási/írási hozzáférést a helyszíni virtuális gép datastores csatlakoztatása a fő célkiszolgáló ESXi-állomáson.
    ![Ismételt védelemmel párbeszédpanel](./media/vmware-azure-reprotect/reprotectinputs.png)

- A helyreállítási terv szintjén is állítsa. Replikációs csoport is látható el újra védelemmel csak a helyreállítási tervet. Amikor Ön lássa el újból védelemmel a helyreállítási terv használatával, meg kell adnia az értékek minden védett gép.
- Ugyanazt a fő célkiszolgálót használja a replikációs csoportok ismételt védelméhez. Ha másik fő célkiszolgálóval védi meg ismét a replikációs csoportokat, a kiszolgáló nem tud megállapítani közös időpontot.
- A helyszíni virtuális gép ismételt védelem ki van kapcsolva. Ez elősegíti az adatok konzisztenciáját a replikáció során. Ne kapcsolja be a virtuális gép ismételt védelem befejeződése után.



## <a name="common-issues"></a>Gyakori problémák


- Jelenleg a Site Recovery támogatja egy virtuális gép fájlrendszer (virtuálisgép-Fájlrendszereinek) vagy a vSAN-adattároló visszavétele. NFS-adattároló nem támogatott. Ez a korlátozás miatt az adattároló kijelölés adjon meg a védelem-újrabeállítási a képernyő NFS datastores esetén üres, vagy jeleníti meg a vSAN-adattárolóban, de a feldolgozás során nem sikerül. Ha azt tervezi, hogy a feladat-visszavételt, hozzon létre egy virtuálisgép-Fájlrendszereinek datastore a helyszínen, és visszaadják feladataikat azt. A feladat-visszavétel, akkor a vmdk-fájl teljes letöltésére.
- Ha egy csak olvasható felhasználói vCenter-kiszolgálók automatikus észlelését, és a virtuális gépet véd, védelmi sikeres, és feladatátvételi működik. Ismételt védelem, során feladatátvétel sikertelen lesz, mivel a datastores nem lesz felderítve. A probléma tünete, hogy a datastores ismételt védelem alatt nem jelennek meg. A probléma megoldásához frissítse a vCenter hitelesítőadat egy megfelelő jogosultsággal rendelkező fiók, és próbálja újból elvégezni a feladatot. 
- Ha a feladat-visszavételt a Linux virtuális gép, és futtassa a helyszíni, láthatja, hogy a hálózatkezelő csomag el lett távolítva a gép. Az Eltávolítás azért van, mert a hálózatkezelő csomagot el kell távolítani, ha a virtuális gépet helyre lett állítva, az Azure-ban.
- Amikor egy Linux virtuális gép statikus IP-címmel van konfigurálva, és át nem adja a Azure, az IP-cím keletkezik, a DHCP-Kiszolgálótól. Amikor a rendszer átadja a helyszínen, a virtuális gép továbbra is az IP-cím beszerzése a DHCP Protokollt használják. Manuálisan jelentkezzen be a gépet, és állítsa vissza az IP-címet a statikus cím, ha szükséges. A Windows rendszerű virtuális gép újra lekérheti a statikus IP-címet.
- Vagy ESXi 5.5 ingyenes kiadásban, vagy vSphere 6 hipervizor ingyenes kiadás használatakor a feladatátvétel sikerességét, de a feladat-visszavétel nem sikerül. Ahhoz, hogy a feladat-visszavétel, frissíteni vagy program próbalicencre.
- A folyamatkiszolgáló a konfigurációs kiszolgáló nem érhető el, ha a Telnet segítségével ellenőrizze a kapcsolatot a konfigurációs kiszolgálón a 443-as porton. A folyamatkiszolgáló a konfigurációs kiszolgáló pingelése is próbálkozhat. A folyamatkiszolgáló lehet szívverést a konfigurációs kiszolgálóhoz való csatlakozáskor.
- Windows Server 2008 R2 SP1 kiszolgáló védett, mint a helyszíni fizikai kiszolgáló nem sikertelen az Azure-ból a helyszíni-helyhez.
- Nem utasíthat el az alábbi esetekben:
    - Az Azure-bA gépeket telepített át. [További információk](migrate-overview.md#what-do-we-mean-by-migration).
    - A virtuális gép átkerül egy másik erőforráscsoportban.
    - Törli az Azure virtuális Géphez.
    - Letiltott a virtuális gép védelmét.
    - A virtuális Gépet manuálisan létrehozott Azure. A gép kell rendelkezik helyszíni eredetileg védett, és átadja a Azure védelem-újrabeállítási előtt.
    - A feladatvisszavétel csak az ESXi-állomáson. Feladat-visszavétel VMware virtuális gépek vagy fizikai kiszolgálók Hyper-V-gazdagépek, a fizikai gépek vagy a VMware munkaállomások nem lehet.


## <a name="next-steps"></a>További lépések

Miután a virtuális gép védett állapotba került, [kezdeményezni a feladat-visszavétel](vmware-azure-failback.md). A feladat-visszavételt a virtuális gép az Azure-ban leáll és a helyszíni virtuális gép. Némi állásidőt jelent a alkalmazás várt. Válassza az időpontot a feladat-visszavétel, ha az alkalmazás működését állásidő.

