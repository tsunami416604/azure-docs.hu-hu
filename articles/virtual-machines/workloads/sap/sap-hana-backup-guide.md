---
title: Biztonsági mentési útmutató az SAP Hana az Azure Virtual Machinesben |} A Microsoft Docs
description: Biztonsági mentési útmutató az SAP HANA biztosít két fő biztonsági mentési lehetőségeket SAP HANA Azure-beli virtuális gépeken
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: 89896fab7b1c359007ed23d4f9d9771e366ca68a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58013344"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Útmutató az Azure-beli virtuális gépeken futó SAP HANA biztonsági mentéséhez

## <a name="getting-started"></a>Első lépések

Az Azure-beli virtuális gépeken futó SAP Hana biztonsági mentési útmutató csak azt ismerteti, Azure-ra vonatkozó témakörök. Általános SAP HANA biztonsági mentés a kapcsolódó elemeket, a SAP HANA dokumentációban (lásd: _SAP HANA biztonsági mentési dokumentáció_ a cikk későbbi részében).

Ez a cikk célja a két fő biztonsági mentési lehetőségeket SAP Hana Azure-beli virtuális gépeken:

- A fájlrendszer az Azure Linux rendszerű virtuális gépként HANA biztonsági mentés (lásd: [SAP HANA az Azure Backup a fájlok szintjére](sap-hana-backup-file-level.md))
- Az Azure storage blob snapshot funkció használatával manuálisan tárolási pillanatképekkel vagy az Azure Backup szolgáltatás alapján HANA biztonsági mentés (lásd: [SAP HANA biztonsági mentés tárolási pillanatképeken alapuló](sap-hana-backup-storage-snapshots.md))

SAP HANA API-t, amely lehetővé teszi, hogy a külső biztonsági mentési eszközök való közvetlen integráció az SAP HANA biztonsági mentést biztosít. (Ez nem ez az útmutató hatókörén belül.) Nincs közvetlen integráció az SAP HANA az Azure Backup szolgáltatással most már elérhető jobb alapján ez az API van.

SAP HANA az Azure virtuális gép különböző típusú, például az Azure M-sorozat hivatalosan támogatott. A teljes listája az SAP HANA megkapta a minősítést az Azure virtuális gépek, tekintse meg [Certified IaaS platformok](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Ez a cikk frissítjük, új ajánlatokat az SAP Hana az Azure-ban válnak elérhetővé.

Nincs olyan SAP HANA hibrid megoldás érhető el az Azure-ban, ahol az SAP HANA futtatása nem virtualizált fizikai kiszolgálókon. Azonban az SAP HANA az Azure biztonsági mentési útmutató vonatkozik, ahol az SAP HANA fut egy Azure-beli virtuális gépen, nem futó SAP HANA tiszta Azure környezetben &quot;nagyméretű példányok.&quot; Lásd: [SAP HANA (nagyméretű példányok) áttekintése és architektúrája az Azure-ban](hana-overview-architecture.md) bővebben a biztonsági mentési megoldás az &quot;nagyméretű példányok&quot; tárolási pillanatképeken alapuló.

Általános információk az Azure-on támogatott SAP-termékekhez található [SAP Megjegyzés 1928533](https://launchpad.support.sap.com/#/notes/1928533).

A következő három adat áttekintést a natív Azure-képességek használata jelenleg az SAP HANA biztonsági mentési lehetőségeket, és három lehetséges jövőbeli biztonsági mentési forgatókönyveket is megjelenítheti. A kapcsolódó cikkek [SAP HANA az Azure Backup a fájlok szintjére](sap-hana-backup-file-level.md) és [SAP HANA biztonsági mentés tárolási pillanatképeken alapuló](sap-hana-backup-storage-snapshots.md) ezeket a lehetőségeket, többek között az SAP mérete és a teljesítménnyel kapcsolatos részletesebben ismerteti HANA biztonsági mentéseket, amelyek multi-terabájt méretű.

![Ezen az ábrán két lehetőség közül választhat a mentésre a virtuális gép aktuális állapota](media/sap-hana-backup-guide/image001.png)

Ezen az ábrán az aktuális virtuális gép állapotát, vagy az Azure Backup szolgáltatás vagy Virtuálisgép-lemezek manuális pillanatképét mentése lehetőségét. Ezzel a módszerrel egy adatforgalmi&#39;nincs az SAP HANA biztonsági másolatainak kezelése. A lemez pillanatkép forgatókönyv kihívás fájlrendszer-konzisztencia és a egy alkalmazáskonzisztens lemez állapota. A konzisztencia a témakör az szakaszban tárgyalt _SAP HANA adatkonzisztencia, amikor a pillanatképeket tároló_ a cikk későbbi részében. Lehetőségeket és az Azure Backup szolgáltatás az SAP HANA biztonsági mentések kapcsolatos korlátozásokat a cikk későbbi részében is ismertetése.

![Ezen az ábrán lehetőségei véve egy SAP HANA fájlszintű biztonsági mentése a virtuális gép](media/sap-hana-backup-guide/image002.png)

Ezen az ábrán az véve egy SAP HANA biztonsági mentés a virtuális gép és a majd tárolásukról HANA biztonsági másolatok valahol ellenkező esetben a különböző eszközök használatával. HANA biztonsági másolat, mint a pillanatkép-alapú biztonsági mentési megoldás több időt igényel, de integritás és konzisztencia tekintetében előnnyel. További részleteket a cikk későbbi részében találhatók.

![Ez az ábra bemutatja a lehetséges jövőbeli SAP HANA biztonsági mentés](media/sap-hana-backup-guide/image003.png)

Ez az ábra egy lehetséges jövőbeli SAP HANA biztonsági mentési forgatókönyv látható. SAP HANA engedélyezve van a másodlagos replikációt a biztonsági másolatok készítése, ha azt szeretné adja hozzá a biztonsági mentési stratégia további lehetőségeket. Jelenleg nem lehet megfelelően egy bejegyzés az SAP HANA wiki:

_&quot;Az lehessen tenni a biztonsági másolatok a másodlagos oldalon?_

_Nem, jelenleg csak adatokat és az elsődleges oldalán található biztonsági másolataihoz. Napló automatikus biztonsági mentés engedélyezése esetén a másodlagos oldalának átvétele után a naplóalapú biztonsági mentések lesz automatikusan írva van.&quot;_

## <a name="sap-resources-for-hana-backup"></a>Az SAP HANA biztonsági mentés erőforrások

### <a name="sap-hana-backup-documentation"></a>SAP HANA backup – dokumentáció

- [Az SAP HANA felügyeleti bemutatása](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [A biztonsági mentési és helyreállítási stratégia tervezése](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [HANA biztonsági mentés ütemezése a ABAP DBACOCKPIT](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [Ütemezés biztonsági mentések (SAP HANA Vezérlőpult)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- SAP HANA – gyakori kérdések a biztonsági mentési [SAP Megjegyzés 1642148](https://launchpad.support.sap.com/#/notes/1642148)
- Az adatbázis- és pillanatképek SAP HANA – gyakori kérdések [SAP Megjegyzés 2039883](https://launchpad.support.sap.com/#/notes/2039883)
- Nem megfelelő hálózati fájl rendszerek biztonsági mentését és helyreállítását [SAP Megjegyzés 1820529](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="why-sap-hana-backup"></a>Miért SAP HANA biztonsági mentés?

Az Azure storage kínál a rendelkezésre állás és megbízhatóság beépített (lásd: [a Microsoft Azure Storage bemutatása](../../../storage/common/storage-introduction.md) további információ az Azure storage).

A minimális &quot;biztonsági mentési&quot; , hogy az Azure SLA-k, az SAP HANA adathoz és naplófájlhoz az SAP HANA-kiszolgáló virtuális Géphez csatolt Azure virtuális merevlemezeken tartja támaszkodnak. Ez a megközelítés magában foglalja a virtuális gép hibáinak, de az SAP HANA-adatok és a naplófájlokat vagy a logikai hibák, például az adatok vagy fájlok törlése véletlenül iratkozott le nem potenciális károknak. Biztonsági másolatok is megfelelőségi vagy jogi okokból szükség. Röviden mindig szükség van az SAP HANA biztonsági mentésekhez.

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>SAP HANA biztonsági mentés helyességét ellenőrzése
Storage snapshots használatával egy teszt visszaállítást egy másik rendszeren futó esetén ajánlott. Ez a megközelítés lehetővé teszi győződjön meg arról, hogy-e a biztonsági mentéshez helyes, és a belső folyamatok egy biztonsági mentés és visszaállítás munkahelyi várt módon. Ez egy jelentős pedig a küszöbértéket a helyszínen, sokkal egyszerűbb elvégezni a felhő azáltal, hogy szükséges erőforrások átmenetileg erre a célra.

Tartsa szem előtt, amely ennek során egy egyszerű visszaállítást és ellenőrzi, hogy a HANA működik és fut nem elegendő. Ideális esetben egy győződjön meg arról, hogy a visszaállított adatbázis nem okoz gondot tábla konzisztencia-ellenőrzést kell futtatni. SAP HANA kínál a konzisztencia-ellenőrzések leírt különféle [SAP Megjegyzés 1977584](https://launchpad.support.sap.com/#/notes/1977584).

A tábla konzisztencia-ellenőrzés kapcsolatos információk is találhatók SAP webhellyel [tábla és a katalógus konzisztencia-ellenőrzéseket](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b).

A szokásos fájlszintű biztonsági a teszt visszaállítás már nem szükséges. Két SAP HANA-eszközzel, amelyek segítségével ellenőrizheti, hogy melyik biztonsági mentés visszaállítási használható: hdbbackupdiag és hdbbackupcheck. Lásd: [manuális ellenőrzése-e a helyreállítás az lehetséges](https://help.sap.com/saphelp_hanaplatform/helpdata/en/77/522ef1e3cb4d799bab33e0aeb9c93b/content.htm) ezekkel az eszközökkel kapcsolatos további részletekért.

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>És a hátrányai HANA biztonsági mentés és a storage-pillanatkép

SAP adatforgalmi&#39;t részesítik előnyben vagy HANA biztonsági mentés és a storage-pillanatkép. Felsorolja azok előnyeiről és hátrányairól, így az egyik, hogy melyik függően a helyzetről és a rendelkezésre álló tár technológia (lásd: [tervezése a biztonsági mentési és helyreállítási stratégiát](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)).

Az Azure-ban, vegye figyelembe, hogy az Azure blob pillanatkép-e a szolgáltatás nem található (tény)&#39;t garancia fájlrendszer-konzisztencia (lásd: [tárolóblob-pillanatképek használata a PowerShell-lel](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)). A következő szakaszban _SAP HANA adatkonzisztencia, amikor a pillanatképeket tároló_, ez a funkció kapcsolatos szempontokat ismerteti.

Emellett egy rendelkezik megszegéseinek számlázási használatakor gyakran blob-pillanatképekkel ebben a cikkben leírtak szerint: [Understanding hogyan pillanatképek lépheti túl a költségek](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)–, nem&#39;t, nyilvánvaló, mint az Azure virtuális lemezek használatával.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>SAP HANA adatkonzisztencia, amikor a pillanatképeket tároló

Fájl rendszer- és konzisztencia egy bonyolult problémát akkor, ha a tárolási pillanatképeket. A legegyszerűbb módja a problémák elkerülése érdekében állítsa le az SAP HANA vagy az egész virtuális gép esetleg még lenne. Előfordulhat, hogy a leállás doable egy bemutatót vagy prototípus vagy akár egy fejlesztői rendszernek, de nem egy éles rendszerre vonatkozó beállítást.

Az Azure-ban, vegye figyelembe, hogy az Azure blob pillanatkép-e a szolgáltatás nem adatáthelyezések&#39;t garancia fájlrendszer-konzisztencia. Azt jól működik, azonban használatával az SAP HANA pillanatkép-szolgáltatás, mindaddig, amíg nincs érintett csak egyetlen virtuális lemez. De akár egyetlen lemezről, a további elemek azokat fel kell venni. [Megjegyzés: 2039883 SAP](https://launchpad.support.sap.com/#/notes/2039883) SAP HANA biztonsági másolatokat tároló-pillanatképeinek használatával kapcsolatos fontos információkat tartalmaz. Például, hogy megjelenik-e, a XFS fájlrendszerrel, azaz futtatásához szükséges **xfs\_rögzítése** konzisztencia biztosításához tárolási pillanatkép megkezdése előtt (lásd: [xfs\_freeze(8) – Linux man lap ](https://linux.die.net/man/8/xfs_freeze) részleteiért **xfs\_rögzítése**).

A témakör a konzisztencia válik, még több ezek komoly kihívásokat jelenthetnek egy esetet, ahol az egyetlen operációs rendszer kiterjedő több lemezeket és köteteket. Például úgy, hogy mdadm vagy LVM használatával, és a rendszerben, használjon csíkozást. Az SAP-Jegyzetnek állapotok a fent említett:

_&quot;Ne feledje, hogy a tárolási rendszer rendelkezik i/o-konzisztencia biztosításához az SAP HANA-adatok kötetenként tárolási pillanatkép létrehozása során, azaz az SAP HANA szolgáltatásspecifikus adatai kötet pillanatfelvételeinek atomi műveletnek kell lennie.&quot;_

Feltéve, hogy nincs átfedés négy Azure virtuális lemezek egy XFS fájlrendszer, a következő lépéseket egy egységes pillanatképet, a HANA adatterület képviselő nyújtanak:

- HANA pillanatképe előkészítése
- Befagyasztani a fájlrendszer (például **xfs\_rögzítése**)
- Az összes szükséges blob pillanatképeinek létrehozása az Azure-ban
- A fájlrendszer rögzítésének feloldása
- Erősítse meg a HANA pillanatképe

Javaslat, hogy a fent leírt lépéseket minden olyan esetben kell a biztonságos oldalon, függetlenül attól, hogy melyik fájlrendszer. Vagy ha ez egy egyetlen lemez vagy, mdadm vagy LVM több lemezre kiterjedő csíkozást.

Fontos győződjön meg róla a HANA pillanatképe. Oka az, hogy a &quot;másolási Módosításkori,&quot; SAP HANA nem feltétlenül igényelnek további lemezterületet verzióban ez mód pillanatkép előkészítése. Ez&#39;s is nem lehet új biztonsági másolatok elindulni, amíg az SAP HANA pillanatképe ellenőrzése.

Az Azure Backup szolgáltatás az Azure Virtuálisgép-bővítmények használatával gondoskodik a fájlrendszer-konzisztencia. Ezek a Virtuálisgép-bővítmények önálló használatra nem érhetők el. Egy SAP HANA konzisztencia kezelése továbbra is rendelkezik. A kapcsolódó cikkben [SAP HANA az Azure Backup a fájlok szintjére](sap-hana-backup-file-level.md) további információt.

### <a name="sap-hana-backup-scheduling-strategy"></a>SAP HANA biztonsági mentési ütemezés stratégia

Az SAP HANA-cikk [tervezése a biztonsági mentési és helyreállítási stratégiát](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) el a biztonsági mentéseket a basic csomag állapota:

- (Naponta) pillanatkép-tárolás
- Fájl- vagy bacint formátumban (hetente egyszer) mindazok az adatok biztonsági mentése
- Automatikus naplóalapú biztonsági mentések

Igény szerint egy sikerült go teljesen; tároló-pillanatképek nélkül sikerült helyettesíteni HANA különbözeti biztonsági mentések, például a különbözeti vagy a növekményes biztonsági mentések által (lásd: [különbözeti biztonsági mentések](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm)).

A HANA-felügyeleti útmutató egy példa felsorolását tartalmazza. Azt sugallja, hogy egy helyreállítás SAP HANA egy adott időpontra, amikor a biztonsági mentések a következő lépéseket:

1. Adatok teljes biztonsági mentés
2. Különbségi biztonsági mentés
3. A növekményes biztonsági mentés 1
4. A növekményes biztonsági mentés 2
5. Naplóalapú biztonsági mentések

Vonatkozó pontos ütemezés feltárhatja, hogy mikor és milyen gyakran történjen, egy adott biztonsági mentési típusú, már nem általános útmutatásként biztosíthat – túl az ügyfél-specifikus, és attól függ, hány adatváltozásokat a rendszer fordulnak elő. Márka egy teljes HANA biztonsági mentés hetente egyszer az SAP oldaláról, amely általános útmutatásként látható, egy alapvető javaslat.
Kapcsolódó elemek naplóalapú biztonsági mentések, az SAP HANA dokumentációjában [Naplóalapú biztonsági mentések](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm).

SAP is javasol néhány, a végtelenségig biztosítható, hogy a biztonságimásolat-katalógus housekeeping módon (lásd: [biztonságimásolat-katalógus és a biztonsági mentési tár Housekeeping](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ca/c903c28b0e4301b39814ef41dbf568/content.htm)).

### <a name="sap-hana-configuration-files"></a>SAP HANA-konfigurációs fájlok

Ahogy az a – gyakori kérdések a [SAP Megjegyzés 1642148](https://launchpad.support.sap.com/#/notes/1642148), az SAP HANA-konfigurációs fájlok nem részei egy standard HANA biztonsági mentés. Ezek nem lényegesek visszaállítani a rendszer. Manuálisan a HANA-konfigurációt sikerült módosítani a visszaállítás után. Abban az esetben egy szeretne kapni a visszaállítási folyamat során egyéni ugyanazt a konfigurációt, hogy külön-külön biztonsági mentése a HANA-konfigurációs fájlok szükség.

Ha dedikált HANA biztonsági mentés fájlrendszerbe standard HANA biztonsági mentések, egy sikerült is másolja a konfigurációs fájlokat az ugyanolyan biztonsági mentési fájlrendszer, és másolja minden együtt a végső tárolási célra például a ritkán használt adatok blob storage-ban.

### <a name="sap-hana-cockpit"></a>Az SAP HANA vezérlőpultja

Az SAP HANA vezérlőpultja monitorozást és a egy böngészőből az SAP HANA kezelése lehetőséget biztosít. Azt is lehetővé teszi, hogy az SAP HANA biztonsági másolatok kezelése, és ezért csak az SAP HANA Studio és ABAP DBACOCKPIT alternatívájaként használható (lásd: [SAP HANA vezérlőpultja](https://help.sap.com/saphelp_hanaplatform/helpdata/en/73/c37822444344f3973e0e976b77958e/content.htm) további információt).

![Ez az ábra bemutatja az SAP HANA vezérlőpultja adatbázis felügyeleti képernyő](media/sap-hana-backup-guide/image004.png)

Ezen az ábrán az SAP HANA vezérlőpultja adatbázis felügyeleti képernyő, és a biztonsági mentési csempét a bal oldalon. A biztonsági mentési csempe jelenik meg a megfelelő felhasználói engedélyekkel kell rendelkeznie bejelentkezési fiókot.

![Biztonsági mentések figyelhető az SAP HANA Vezérlőpulton, amíg azok folyamatban](media/sap-hana-backup-guide/image005.png)

Biztonsági mentések figyelhető az SAP HANA vezérlőpultja, míg azok folyamatos, és amikor elkészült, a biztonsági mentés részletei érhetők el.

![Azure virtuális gépen egy SLES 12 Gnome desktoppal Firefox használatával](media/sap-hana-backup-guide/image006.png)

Az előző képernyőfelvételeken az Azure Windows virtuális történtek. A Firefox Azure virtuális gépen egy SLES 12 Gnome desktoppal példa látható. SAP HANA biztonsági mentés ütemezésének megadása az SAP HANA vezérlőpultja lehetősége jeleníti meg. Egy is megtekintheti, ahogy azt sugallja, dátum és idő előtagjaként a biztonsági mentési fájlokat. Az SAP HANA Studio, az alapértelmezett előtag a &quot;COMPLETE\_adatok\_biztonsági MENTÉSI&quot; teljes biztonsági mentés során. Egy egyedi előtagot használata javasolt.

### <a name="sap-hana-backup-encryption"></a>SAP HANA biztonsági mentés titkosítása

SAP HANA közben biztosít titkosítást mind az adat- és naplófájlok. Ha az SAP HANA-adat- és naplófájlok nem titkosítottak, majd a biztonsági másolatok is nem titkosítottak. Az ügyfél az SAP HANA biztonsági mentések titkosításához valamilyen harmadik féltől származó megoldás használata esetén. Lásd: [adatok és a naplózási kötet titkosítása](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/01f36fbb5710148b668201a6e95cf2/content.htm) további információ az SAP HANA-titkosítás.

A Microsoft Azure-ügyfél használatával az IaaS virtuális gép titkosítási szolgáltatás titkosítása. Használhat például egy dedikált csatlakoztatott adatlemezek a virtuális Géphez, amelyek segítségével a SAP HANA biztonsági másolatok tárolására, majd készítsen másolatot a következő lemezek.

Az Azure Backup szolgáltatás képes kezelni a titkosított virtuális gépek vagy-tárolólemezek (lásd: [biztonsági mentése és visszaállítása titkosított virtuális gépek az Azure Backup szolgáltatással](../../../backup/backup-azure-vms-encryption.md)).

Egy másik lehetőség lenne, az SAP HANA virtuális gép és annak lemezeire titkosítás nélkül, és az SAP HANA biztonsági másolatok tárolása a storage-fiók, amelyre engedélyezték a titkosítást (lásd: [Azure Storage Service Encryption az inaktív adatok](../../../storage/common/storage-service-encryption.md)).

## <a name="test-setup"></a>Teszt beállítása

### <a name="test-virtual-machine-on-azure"></a>Teszt virtuális gép az Azure-ban

A tesztek elvégzéséhez egy SAP HANA telepítése egy Azure GS5 virtuális gépen a következő biztonsági mentési és visszaállítási ellenőrzés használták. Az ismertetett alapelvek megegyeznek a M-sorozat virtuális gépei.

![Ezen az ábrán része a HANA tesztelési virtuális gép az Azure portal áttekintése](media/sap-hana-backup-guide/image007.png)

Ezen az ábrán a HANA tesztelési virtuális gép az Azure portal áttekintése része.

### <a name="test-backup-size"></a>Tesztelje a biztonsági másolat mérete

![Ez az ábra a biztonsági mentési konzol, HANA Studio származik, és megjeleníti a biztonságimásolat-fájl mérete a HANA-index kiszolgáló 229 GB](media/sap-hana-backup-guide/image008.png)

Egy helyőrző tábla töltötte az adatokkal, egy tárolt adatok biztonsági másolatának mérete több mint 200 GB származtassa valósághű teljesítményadatok lekérése. Az ábra a biztonsági mentési konzol, HANA Studio származik, és jeleníti meg a biztonságimásolat-fájl mérete a HANA-index kiszolgáló 229 GB. A vizsgálatok során az alapértelmezett biztonsági mentési előtag "COMPLETE_DATA_BACKUP" az SAP HANA Studio lett megadva. A valódi éles rendszerek egy több hasznos előtagot kell definiálni. Az SAP HANA vezérlőpultja dátum/idő javasol.

### <a name="test-tool-to-copy-files-directly-to-azure-storage"></a>Vizsgálati eszköz közvetlenül az Azure storage-fájlok másolása

SAP HANA biztonsági mentési fájlok átvitele közvetlenül az Azure blob storage-bA vagy az Azure-fájlmegosztások, a blobxfer eszköz lett megadva, mert mindkét célok támogatja, és egyszerűen integrálható az automatizálási szkriptek a parancssori felület miatt. A blobxfer eszköz [GitHub](https://github.com/Azure/blobxfer).

### <a name="test-backup-size-estimation"></a>Biztonsági másolat mérete-becslésére tesztelése

Fontos, ha meg szeretné becsülni az SAP HANA biztonsági mentés méretét. Ez a becslés segít a teljesítmény javítása a biztonsági mentés fájljaiból fájlmásolás közben párhuzamosság miatt számos biztonságimásolat-fájl maximális mérete definiálásával. (Ezeket az adatokat részletesen a cikk későbbi részében.) Az egyik is el kell döntenie, hogy egy teljes biztonsági mentés vagy a különbözeti biztonsági mentést (növekményes vagy különbözeti).

Szerencsére van egy egyszerű SQL-utasítást, amely a biztonsági mentési fájlok méretének becslése: **kiválasztása \* m\_biztonsági MENTÉSI\_mérete\_BECSLÉSEKET** (lásd: [becslés a fájlrendszer biztonsági mentést szükséges hely](https://help.sap.com/saphelp_hanaplatform/helpdata/en/7d/46337b7a9c4c708d965b65bc0f343c/content.htm)).

![Az SQL-utasítás kimenete szinte teljesen az adatok teljes biztonsági mentés a lemezen valós mérete megegyezik.](media/sap-hana-backup-guide/image009.png)

A teszt rendszer esetében az SQL-utasítás kimenete szinte teljesen az adatok teljes biztonsági mentés a lemezen valós mérete megegyezik.

### <a name="test-hana-backup-file-size"></a>Teszt HANA biztonságimásolat-fájl mérete

![A HANA Studio biztonsági mentési konzol lehetővé teszi egy HANA biztonsági másolatok maximális méretének korlátozása](media/sap-hana-backup-guide/image010.png)

A HANA Studio biztonsági mentési konzol lehetővé teszi egy HANA biztonsági másolatok maximális méretének korlátozására. Minta a környezetben, a szolgáltatás lehetővé teszi egy 230 GB-os biztonsági mentési fájl helyett több kisebb biztonsági mentési fájlokat beolvasni. Kisebb fájlméretet jelentős hatással van a teljesítményre (a kapcsolódó cikkben [SAP HANA az Azure Backup a fájlok szintjére](sap-hana-backup-file-level.md)).

## <a name="summary"></a>Összegzés

A vizsgálati eredmények az alábbi táblázatokban és hátrányai megoldások biztonsági mentése az Azure-beli virtuális gépeken futó SAP HANA-adatbázis alapján.

**SAP HANA biztonsági mentésére a fájlrendszer, és másolja a biztonságimásolat-fájlt utána a végső biztonsági mentési cél**

|Megoldás                                           |Szakemberek számára                                 |Hátrányok                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|A Virtuálisgép-lemezek HANA biztonsági mentések megtartása                      |Nincsenek további felügyeleti erőfeszítések     |Helyi virtuális gép lemezterületet igényel eats           |
|Másolja a biztonságimásolat-fájlt a blobtárolóba való Blobxfer eszköz |Több fájlok másolása a párhuzamosságot használhatják a ritka elérésű blobtároló | További eszköz karbantartási és az egyéni parancsfájlok | 
|BLOB másolása a Powershell vagy parancssori felület használatával                    |Nincs szükség esetén további eszköz valósítható meg az Azure Powershell vagy parancssori felület |manuális folyamat, az ügyfél használatával hitelesítik a parancsfájlkezelést, és a visszaállításhoz másolt blobok felügyeleti rendelkezik.|
|NFS-megosztásra másolásához                                  |A biztonsági mentési fájlokat más virtuális gép a HANA-kiszolgáló gyakorolt hatás nélkül utófeldolgozása|Lassú másolási folyamat|
|Az Azure File Service Blobxfer másolása                |Nem keleti-afrikai fel területet a helyi Virtuálisgép-lemezek|Nincs közvetlen támogatást írja HANA jelenleg 5 TB-os, a fájlmegosztás biztonsági mentési és méretkorlátozások|
|Az Azure Backup szolgáltatás ügynöke                                 | Előnyben részesített megoldás lehet         | Jelenleg nem érhető el Linux rendszeren    |



**Tárolási pillanatképeken alapuló biztonsági mentés SAP HANA**

|Megoldás                                           |Szakemberek számára                                 |Hátrányok                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Azure Backup Service                               | Lehetővé teszi a virtuális gép biztonsági mentésének alapján a blobpillanatképeket | Amikor nem használja a fájl visszaállítás, a visszaállítási folyamat, amely majd azt egy új SAP HANA-licenc kulcs szükséges az igényel a új virtuális gép létrehozása|
|Manuális tárolóblob-pillanatképek                              | Rugalmasan hozhat létre, és állítsa vissza az adott Virtuálisgép-lemezek a virtuális gép egyedi azonosítója módosítása nélkül|Az összes manuális tevékenység, amely kell végrehajtani, az ügyfél által|

## <a name="next-steps"></a>További lépések
* [Az SAP HANA az Azure Backup a fájlok szintjére](sap-hana-backup-file-level.md) ismerteti a fájl alapú biztonsági mentési beállítás.
* [SAP HANA biztonsági mentés tárolási pillanatképeken alapuló](sap-hana-backup-storage-snapshots.md) ismerteti a pillanatkép-alapú biztonsági mentési beállítást.
* Magas rendelkezésre állást és az Azure-ban (nagyméretű példányok) SAP Hana vész-helyreállítási terv létrehozásához, lásd: [SAP HANA (nagyméretű példányok) magas rendelkezésre állás és vészhelyreállítás recovery az Azure-ban](hana-overview-high-availability-disaster-recovery.md).
