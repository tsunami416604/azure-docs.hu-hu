---
title: "A Microsoft Azure StorSimple virtuális tömb áttekintése |} Microsoft Docs"
description: "A StorSimple virtuális tömb, egy integrált tárolási megoldás, amely kezeli a Microsoft Azure felhőalapú tárolást és a helyszíni virtuális tömb közötti tárolási feladatok elvégzéséről ismerteti."
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 169c639b-1124-46a5-ae69-ba9695525b77
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/16/2017
ms.author: alkohli
ms.openlocfilehash: b9a5797751fa970c569c93e5efe300d4d74319ce
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="introduction-to-the-storsimple-virtual-array"></a>A StorSimple virtuális tömb bemutatása

## <a name="overview"></a>Áttekintés

A Microsoft Azure StorSimple virtuális tömb egy integrált tárolási megoldás, amely kezeli a tárolási feladatok elvégzéséről a hipervizor és a Microsoft Azure felhőalapú tárolást futtató helyszíni virtuális tömb között. A virtuális tömbjének értéke egy hatékony, költséghatékony és egyszerűen kezelhető fájl- vagy iSCSI-kiszolgálói megoldás, amely kiküszöböli a problémák és a vállalati tárolás és az adatvédelem társított is számos. A virtuális tömbjének értéke különösen jól alkalmazható, a ritkábban használt archív adatokhoz tárolásához.

Ez a cikk áttekintést a virtuális tömb – az alábbiakban néhány egyéb erőforrások:

* Gyakorlati tanácsok, lásd: [StorSimple virtuális tömb gyakorlati tanácsok](storsimple-ova-best-practices.md).
* A StorSimple 8000 sorozat eszközeire áttekintését Ugrás [StorSimple 8000 series: hibrid felhő megoldás](storsimple-overview.md).
* A StorSimple 5000/7000-es adatsorozat eszközökkel kapcsolatos információkért látogasson el [StorSimple Online súgó](http://onlinehelp.storsimple.com/).

A virtuális tömb támogatja az iSCSI- vagy Server Message Block (SMB) protokoll. A meglévő hipervizor infrastruktúra fut, és biztosítja, hogy a felhő, a felhőbeli biztonsági mentését, a gyors helyreállítás, a elemszintű helyreállítás és a vész-helyreállítási szolgáltatás rétegezéséhez.

A következő táblázat összefoglalja a StorSimple virtuális tömb fontos szolgáltatásokat.

| Szolgáltatás | StorSimple Virtual Array |
| --- | --- |
| Telepítési követelmények |Használja a virtualizálási infrastruktúrában (Hyper-V vagy VMware) |
| Rendelkezésre állás |Egyetlen csomópont |
| Teljes kapacitás (például felhő) |Akár 64 TB használható kapacitás virtuális rendszerenként |
| Helyi kapacitás |6.4-es TB használható kapacitás virtuális tömb (kell kiépíteni és 8 TB 500 GB szabad lemezterület) / 390 GB |
| Natív protokollok |iSCSI- vagy SMB |
| Helyreállítási időre vonatkozó célkitűzés (RTO) |iSCSI: függetlenül mérete kisebb, mint 2 perc |
| Helyreállítási időkorlát (RPO) |Napi és igény szerinti biztonsági mentés |
| Tároló rétegezésével |Használja a leképezés csak akkor határozza meg, milyen adatokat kell helyezhető el a bejövő vagy kimenő melegítsük |
| Támogatás |A szolgáltató által támogatott virtualizációs infrastruktúra |
| Teljesítmény |Függ az alapul szolgáló infrastruktúra |
| Adatok mobilitási |Visszaállíthatja a ugyanarra az eszközre vagy elemszintű helyreállítás (fájlkiszolgáló) |
| A tárolási rétegek |Helyi hipervizor tárolás és a felhő |
| A fájlmegosztás méretének |Szintezett: legfeljebb 20 TB; helyileg rögzített: legfeljebb 2 TB |
| Kötet mérete |Rétegzett: 500 GB-tól 5 TB; helyileg rögzített: 50 GB-os 200 GB <br> A rétegzett kötetek maximális helyi lefoglalás 200 GB-os. |
| A pillanatképek |Összeomlás-konzisztens |
| Elemszintű helyreállítás |Igen; felhasználók visszaállíthatják a megosztások |

## <a name="why-use-storsimple"></a>StorSimple miért érdemes használni?

StorSimple csatlakozik a felhasználók és a kiszolgálók az Azure storage perc alkalmazás módosítás nélkül.

A következő táblázat ismerteti a StorSimple virtuális tömb megoldás biztosító kulcs előnyöket nyújtja.

| Szolgáltatás | Előny |
| --- | --- |
| Transzparens integráció |A virtuális tömbhöz az iSCSI- vagy SMB protokollt támogatja. Az adatátvitelt jelölik a helyi szint és a felhő szintjén között, zökkenőmentes és a felhasználó számára. |
| Alacsonyabb tárolási költségek |A StorSimple kiépítése a leggyakrabban használt gyakran használt adatokkal az aktuális követelményeinek megfelelő helyi tárterület. Tárolási igényei nő, mivel a StorSimple rétegek ritkán használt adatok költséghatékony a felhőbeli tárhelyén. Az adatok deduplikációja és tömörített további a tárhellyel kapcsolatos követelmények és költségek csökkentése érdekében a felhőbe történő elküldése előtt. |
| Egyszerűsített tárolók kezelése |StorSimple több eszköz kezelése a StorSimple Device Manager segítségével a felhőben központosított felügyeletet biztosít. |
| Továbbfejlesztett vész-helyreállítási és megfelelőség |StorSimple gyorsabb vész-helyreállítási könnyíti meg a metaadatok azonnali visszaállítása és az adatok helyreállításához, igény szerint. Ez azt jelenti, hogy a normál működés minimális megszakítás folytatása. |
| Adatok mobilitási |A felhőbe rétegzett adatok érhetők el a helyreállítási és áttelepítési célokból más helyekre. Vegye figyelembe, hogy visszaállíthatja az eredeti virtuális tömb csak az adatokat. Vész-helyreállítási szolgáltatás azonban egy másik virtuális tömb állítsa vissza a teljes virtuális tömb használhatja. |

## <a name="storsimple-workload-summary"></a>StorSimple a számítási feladatok összefoglalása

Támogatott StorSimple munkaterhelések összefoglalását az alábbi táblázatban.

|Forgatókönyv     |Számítási feladat     |Támogatott      |Korlátozások               |
|-------------|-------------|---------------|---------------------------|
|ROBO  |Fájlmegosztás     |Igen      |Lásd: [fájlkiszolgáló határok](storsimple-ova-limits.md).<br></br>Lásd: [támogatott SMB verzióját rendszerkövetelményei](storsimple-ova-system-requirements.md).| Az összes verzió     |
|A felhő archiválása  |Archív fájlok megosztása     |Igen      |Lásd: [fájlkiszolgáló határok](storsimple-ova-limits.md).<br></br>Lásd: [támogatott SMB verzióját rendszerkövetelményei](storsimple-ova-system-requirements.md).| Az összes verzió     |

A StorSimple virtuális tömb bizonyul a legalkalmasabbnak, a ritkán használt adatokhoz. Míg a virtuális tömb teljesítményének növelése érdekében a helyi gyorsítótárba, a felhasználók kell feltételezik, hogy az eszköz szolgálja-e a tároló (a felhőben) legalacsonyabb szint fájlokat. Minden egyes virtuális tömb írhat és olvassa el az Azure storage körülbelül 100 Mbit/s. Adott hivatkozáson által megosztott az eszköz beérkező összes kérelmet, és a szűk keresztmetszetet jelenthet az alábbi ábrán látható módon.

![A felhő archiválása](./media/storsimple-ova-overview/cloud-archiving.png)

Ha több egyidejű felhasználók hozzáférnek a virtuális tömb, mindannyian egy alacsonyabb teljesítményt Azure vezető csatlakozni osztják meg. Felhasználónként nem garantált teljesítményre, és az eszköz egyes kérelmeket dolgozza fel, azok az ügyfélszámítógépekre érkeznek.

A StorSimple virtuális tömb a magas rendelkezésre állást igénylő munkaterhelések nem alkalmas. A virtuális tömb a szoftverfrissítések telepítésekor állásidő észlelő egyetlen csomópont eszközről szó. A rendszergazdák tervezze meg a karbantartási időszak, 3 – 4 alkalommal évente 30 perc.

## <a name="workflows"></a>Munkafolyamatok

A StorSimple virtuális tömbjének értéke a következő munkafolyamatok különösen alkalmas:

* [Felhőalapú tárolók kezelése](#cloud-based-storage-management)
* [Független hely biztonsági mentése](#location-independent-backup)
* [Adatok védelme és katasztrófa-helyreállítás](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>felhőalapú tárolók kezelése
A StorSimple Device Manager szolgáltatás fut az Azure portál segítségével több eszközön, és több helyen tárolt adatok kezelését. Ez különösen fontos elosztott fiókirodai forgatókönyvben. Vegye figyelembe, hogy virtuális tömbök és a fizikai StorSimple eszközök kezelésére a StorSimple Device Manager szolgáltatás külön példányát kell létrehozni. Ne feledje, hogy a virtuális tömb most használja az új Azure-portálon a helyett a klasszikus Azure portálon.

![felhőalapú tárolók kezelése](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>Független hely biztonsági mentése
A virtuális tömbbel rendelkező felhőalapú pillanatfelvételek adjon meg egy kötetet vagy megosztást helyfüggetlen, pont időponthoz kötött másolata. Felhőalapú pillanatfelvételek alapértelmezés szerint engedélyezve vannak, és nem tiltható le. Minden kötetek és megosztások biztonsági mentése keresztül napi biztonsági mentési házirend egy időben, és további alkalmi biztonsági mentések minden szükséges vehet igénybe.

### <a name="data-protection-and-disaster-recovery"></a>Adatok védelme és katasztrófa-helyreállítás
A virtuális tömb a következő adatok védelmére és vész-helyreállítási eljárással támogatja:

* **Kötetet vagy megosztást visszaállítási** – segítségével a visszaállítás új munkafolyamatként helyreállíthat egy kötetet vagy megosztást. Ezt a módszert használja a teljes kötetet vagy megosztást helyreállításához.
* **Elemszintű helyreállítási** – megosztások egyszerűsített hozzáférés legutóbbi biztonsági mentés engedélyezése. Könnyen kártérítés fájlhoz egy speciális *.backup* mappa elérhetővé a felhőben. A visszaállítási funkció felhasználói és rendszergazdai beavatkozás nélküli szükség.
* **Vész-helyreállítási** – a feladatátvételi képességének használata helyreállítása a minden kötetek vagy megosztások egy új virtuális tömbhöz. Az új virtuális tömb létrehozása és a StorSimple eszköz Manager szolgáltatásban való regisztrálása, majd az eredeti virtuális tömb feladatátvételt. Az új virtuális tömb majd fogja feltételezni, hogy a kiosztott erőforrásokat.

## <a name="storsimple-virtual-array-components"></a>A StorSimple virtuális tömb összetevők

A virtuális tömb összetevői a következők:

* [Virtuális tömb](#virtual-array) – egy hibrid felhő tárolóeszköz létre a virtuális környezet vagy a hipervizor a virtuális gép alapján.
* [StorSimple Device Manager szolgáltatás](#storsimple-device-manager-service) – egy bővítményt, az Azure portál, amely lehetővé teszi egy vagy több StorSimple eszközt, amely földrajzilag különböző helyeken érhető el egyetlen webes felhasználói felületen keresztüli kezelését. A StorSimple Device Manager szolgáltatás segítségével hozzon létre és szolgáltatások, megtekintheti és kezelheti az eszközöket és a riasztások, valamint kezelheti és kötetek, megosztások és a meglévő pillanatképeket.
* [Helyi webes felhasználói felület](#local-web-user-interface) – egy webes felhasználói felület, amellyel konfigurálja úgy az eszközt, hogy képes csatlakozni a helyi hálózathoz, és regisztrálja az eszközt a StorSimple eszköz Manager szolgáltatásban. 
* [Parancssori felület](#command-line-interface) – a virtuális tömbben támogatási munkamenet indításához használhatja A Windows PowerShell felületet.
  Az alábbi szakaszok ismertetik részletesebben ezeket az összetevőket, és azt ismertetik, hogyan a megoldás adatok elrendezése, foglal helyet, és elősegíti a tárolók kezelése és az adatok védelmére.

### <a name="virtual-array"></a>Virtuális tömb

A virtuális tömbjének értéke egy egy csomópontos tárolási megoldás, amely elsődleges tárolására szolgál, kezeli a felhő tárolóeszközökkel való kommunikációhoz, és biztosítja a biztonsági és az eszközön tárolt összes adat bizalmasságát.

A virtuális tömb, amely letölthető egy modell érhető el. A virtuális tömb maximális kapacitása nem 6.4-es TB az eszközt (az egy alapul szolgáló tárolási követelmény 8 TB-os) és 64 TB-ot, így a felhőbeli tárhelyén.

A virtuális tömb a következő funkciókkal rendelkezik:

* Költséghatékony. Azt elérhetővé válnak a meglévő virtualizációs infrastruktúra használatára és a meglévő Hyper-V vagy VMware hipervizor telepíthetők.
* Az adatközpontban található, és az iSCSI-kiszolgáló vagy egy fájlkiszolgáló konfigurálhatók.
* Integrálva van a felhővel.
* Biztonsági másolatai a felhőben, amely katasztrófa utáni helyreállítás elősegítheti és egyszerűbbé válik a elemszintű helyreállítás (ILR).
* A virtuális tömb frissítések alkalmazhatja, ugyanúgy, mint a lenne egy fizikai eszköz vonatkozik.

> [!NOTE]
> Egy virtuális tömb nem bonthatók ki. Ezért fontos a megfelelő tárolót létesíteni, a virtuális tömb létrehozásakor.

### <a name="storsimple-device-manager-service"></a>A StorSimple eszköz kezelő szolgáltatás

Microsoft Azure StorSimple webes felhasználói felületet, a StorSimple eszköz kezelő szolgáltatás, amelynek segítségével központilag kezelheti a StorSimple tárolási biztosít. A StorSimple Device Manager szolgáltatás segítségével a következő feladatokat:

* Egyetlen szolgáltatás több StorSimple virtuális tömbök kezelése.
* Konfigurálása és kezelése a StorSimple virtuális tömbök biztonsági beállításait. (A felhő titkosításra a Microsoft Azure API-k függ.)
* Tárfiók hitelesítő adatainak és a tulajdonságok konfigurálása.
* Konfigurálása és kezelése a kötetek vagy megosztások.
* Készítsen biztonsági másolatot, és a kötetek vagy megosztások adatairól.
* Figyelemmel kísérni a teljesítményét.
* Tekintse át a rendszer beállításait, és lehetséges problémák azonosításához.

A StorSimple Device Manager szolgáltatás a virtuális tömb napi felügyeleti használható.

További információkért látogasson el [felügyelete a StorSimple eszközt a StorSimple Device Manager szolgáltatással](storsimple-virtual-array-manager-service-administration.md).

### <a name="local-web-user-interface"></a>Helyi webes felhasználói felülete

A virtuális tömb egy webes felhasználói felület egyszeri konfigurálásának és regisztrációjának az eszköz StorSimple Device Manager szolgáltatásban használt tartalmazza. Állítsa le és indítsa újra a virtuális tömb, diagnosztikai tesztek futtatása, szoftverek frissítése, az eszköz rendszergazdai jelszava, megtekintheti a rendszer naplóit, és kérje a Microsoft Support fájlt egy szolgáltatási kérelem használhatja.

A webes felhasználói felület használatával kapcsolatos információkért látogasson el [a webes felhasználói felület segítségével felügyelheti a StorSimple virtuális tömb](storsimple-ova-web-ui-admin.md).

### <a name="command-line-interface"></a>Parancssori felület

A belefoglalt Windows PowerShell-felületet lehetővé teszi a Microsoft Support támogatási munkamenetet kezdeményezni, így meghatározhatja, hogy és a virtuális tömb előforduló megoldását.

## <a name="storage-management-technologies"></a>Felügyeleti tárolótechnológiák

A StorSimple megoldásban mellett a virtuális tömb és más olyan összetevők, a következő szoftver technológiákat használ a fontos adatok gyors hozzáférést biztosítanak, csökkenthető a tároló fogyasztása és a virtuális tömb-on tárolt adatok védelme:

* [Automatikus tárolórétegzés](#automatic-storage-tiering) 
* [Helyileg rögzített megosztásokat és -kötetek](#locally-pinned-shares-and-volumes)
* [A deduplikáció és az adatok tömörítése rétegzett, vagy a felhő biztonsági mentése](#deduplication-and-compression-for-data-tiered/backed-up-to-the-cloud) 
* [Ütemezett és igény szerinti biztonsági mentések tiltása](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>Automatikus tárolórétegzés
A virtuális tömb új rétegezési mechanizmust alkalmaz a virtuális tömb és a felhőben tárolt adatok kezelését. Nincsenek csak két: a helyi virtuális tömb és az Azure felhőbeli tárhelyén. A StorSimple virtuális tömb adatok automatikusan elrendezi a rétegek hőtérkép, amely nyomon követi az aktuális használatát, kor, és kapcsolatok más adatok alapján történő. Adatok, amelyeket a legtöbb aktív (hottest) helyileg van tárolva, miközben kevesebb az aktív és inaktív adatok automatikusan át a felhőbe. (Az összes biztonsági másolatot a felhőben vannak tárolva.) StorSimple állítja be, és újrarendezi az adatokat, és a használati minták tárolási hozzárendelését módosítsa. Például bizonyos adatokat válhatnak kevésbé aktív adott idő alatt. Fokozatosan kevésbé aktív válik, mivel azt többszintű ki a felhőbe. Ha ugyanazokat az adatokat ismét aktívvá válik, akkor többszintű a tárolótömbhöz.

Egy adott rétegzett fájlmegosztás vagy kötet adatait garantáltan a saját helyi rétegen terület (körülbelül 10 %-a a teljes kiosztott lemezterület az adott fájlmegosztás vagy kötet). Ez csökkenti a rendelkezésre álló tár az adott fájlmegosztás vagy kötet virtuális tömbben, miközben biztosítja, hogy egy fájlmegosztás vagy kötet tárolótömbökhöz nem érinti a további kötetek vagy megosztások rétegezési igényeinek. Így egy fájlmegosztás vagy kötet foglalt terhelése nem kényszerítheti ki a felhőbe az összes többi munkaterhelését.

Az iSCSI-létrehozott rétegzett kötetek maximális helyi foglalás 200 GB-os függetlenül attól, hogy a kötet rendelkezik.

![Automatikus tárolórétegzés](./media/storsimple-ova-overview/automatic-storage-tiering.png)

> [!NOTE]
> A helyileg rögzített kötet is megadhat, ebben az esetben az adatokat a virtuális tömbben marad, és soha ne rétegzett a felhőbe. További információkért látogasson el [helyileg rögzített megosztásokat és -kötetek](#locally-pinned-shares-and-volumes).


### <a name="locally-pinned-shares-and-volumes"></a>Helyileg rögzített megosztásokat és -kötetek

Megfelelő megosztások és a helyileg rögzített kötetek is létrehozhat. Ez a funkció biztosítja, hogy a kritikus alkalmazások által megkövetelt adatok marad a virtuális tömbben, és soha nem többszintű a felhőbe. Helyileg rögzített megosztásokat és -kötetek az alábbi funkciókkal rendelkeznek:

* Nincsenek felhő késések vagy kapcsolódási problémák.
* Továbbra is részesülnek StorSimple felhőalapú biztonsági mentés és katasztrófa helyreállítási szolgáltatást.

Visszaállíthatja egy helyileg rögzített vagy, rétegzett kötet vagy egy rétegzett fájlmegosztás vagy kötet, helyileg rögzített. 

További információ a helyileg rögzített kötetekhez Ugrás [kötetek kezelése a StorSimple Device Manager szolgáltatással](storsimple-virtual-array-manage-volumes.md).

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>A deduplikáció és az adatok tömörítése rétegzett, vagy a felhő biztonsági mentése

StorSimple deduplikációs és az adatok tömörítésének használatával további csökkentése érdekében a felhőben. A deduplikáció összességében jelentősen csökkenti a tárolt adatkészlet redundanciájának kiküszöbölése révén tárolt adatok. Adat változik, StorSimple figyelmen kívül hagyja a nem módosított adatainak és csak a változtatásokat rögzíti. Továbbá a StorSimple csökkenti a tárolt adatok mennyisége azonosításával és az ismétlődő adatok eltávolítása.

> [!NOTE]
> A virtuális tömb-on tárolt adatok nem deduplikált vagy tömörített. Minden deduplikációs, és tömörítés akkor fordul elő, előtt a adatküldést a felhőbe.

### <a name="scheduled-and-on-demand-backups"></a>Ütemezett és igény szerinti biztonsági mentések tiltása

StorSimple adatbiztonsági funkciók lehetővé teszik a igény szerinti biztonsági mentések létrehozását. Emellett egy alapértelmezett biztonsági mentés ütemezése biztosítja, hogy az adatokról napi fel. Biztonsági mentés készül növekményes pillanatképet, amely a felhőben vannak tárolva formájában. Jegyezze fel a módosítások csak az utolsó biztonsági mentés óta, pillanatképeket hozható létre és gyorsan visszaállítva. Ezeket a pillanatképeket különösen fontos a vész-helyreállítási eljárással lehet, mert cserélje le a másodlagos tárterületre rendszerek (például a szalagos biztonsági mentés), és lehetővé teszik az adatok helyreállítását a datacenter vagy a másodlagos helyek szükség esetén.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [készítse elő a virtuális tömb portal](storsimple-virtual-array-deploy1-portal-prep.md).
