---
title: Microsoft Azure StorSimple virtuális tömb – áttekintés
description: A StorSimple virtuális tömb, egy integrált tárolási megoldás, amely a helyszíni virtuális tömb és a Microsoft Azure felhőalapú tárolóközött végzett tárolási feladatokat kezeli.
author: alkohli
ms.assetid: 169c639b-1124-46a5-ae69-ba9695525b77
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 32781a83aec996b23f161f5fe695f39a0de38685
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76273871"
---
# <a name="introduction-to-the-storsimple-virtual-array"></a>Bevezetés a StorSimple virtuális tömbbe

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

## <a name="overview"></a>Áttekintés

A Microsoft Azure StorSimple virtuális tömb egy integrált tárolási megoldás, amely a hipervizorban futó helyszíni virtuális tömbés a Microsoft Azure felhőalapú tárhelye közötti tárolási feladatokat kezeli. A virtuális tömb egy hatékony, költséghatékony és könnyen kezelhető fájlkiszolgáló vagy iSCSI-kiszolgáló megoldás, amely kiküszöböli a vállalati tárolással és adatvédelemmel kapcsolatos problémákat és költségeket. A virtuális tömb különösen alkalmas a ritkán használt archiválási adatok tárolására.

Ez a cikk áttekintést nyújt a virtuális tömb - itt van néhány más források:

* Az ajánlott eljárásokat a [StorSimple Virtual Array ajánlott eljárások című témakörben tésszet.](storsimple-ova-best-practices.md)
* A StorSimple 8000 sorozatú eszközök áttekintéséhez látogasson el a [StorSimple 8000 sorozat: hibrid felhőalapú megoldásra.](storsimple-overview.md)
* A StorSimple 5000/7000 sorozatú eszközökkel kapcsolatos további információkért látogasson el a [StorSimple online súgójára.](http://onlinehelp.storsimple.com/)

A virtuális tömb támogatja az iSCSI vagy a Server Message Block (SMB) protokollt. A meglévő hipervizor-infrastruktúrán fut, és rétegezést biztosít a felhőhöz, a felhőalapú biztonsági mentéshez, a gyors visszaállításhoz, az elemszintű helyreállításhoz és a vész-helyreállítási funkciókhoz.

Az alábbi táblázat a StorSimple virtuális tömb fontos jellemzőit foglalja össze.

| Szolgáltatás | StorSimple Virtual Array |
| --- | --- |
| Telepítési követelmények |Virtualizációs infrastruktúrát használ (Hyper-V vagy VMware) |
| Rendelkezésre állás |Egyetlen csomópont |
| Teljes kapacitás (felhővel együtt) |Akár 64 TB felhasználható kapacitás virtuális tömbenként |
| Helyi kapacitás |390–6,4 TB-os felhasználható kapacitás virtuális tömbenként (500–8 TB lemezterület kiépítése szükséges) |
| Natív protokollok |iSCSI vagy SMB |
| Helyreállítási időre vonatkozó célkitűzés (RTO) |iSCSI: mérettől függetlenül kevesebb, mint 2 perc |
| Helyreállítási időkorlát (RPO) |Napi biztonsági mentések és igény szerinti biztonsági mentések |
| Tárolási rétegezés |Hőleképezés segítségével határozza meg, hogy mely adatokat kell rétegezve be- és kidolgozni |
| Támogatás |A szállító által támogatott virtualizációs infrastruktúra |
| Teljesítmény |Az alapul szolgáló infrastruktúrától függően változik |
| Az adatok mobilitása |Visszaállíthatja ugyanarra az eszközre, vagy elemszintű helyreállítást (fájlkiszolgáló) |
| Tárolási rétegek |Helyi hipervizor tárolás és felhő |
| Megosztás mérete |Többszintű: legfeljebb 20 TB; helyileg rögzített: akár 2 TB |
| Kötet mérete |Rétegzett: 500 GB és 5 TB között; helyileg rögzített: 50 GB és 200 GB között <br> A rétegzett kötetek maximális helyi foglalása 200 GB. |
| Pillanatképek |Összeomlás-konzisztens |
| Elemszintű helyreállítás |Igen, ez az. felhasználók visszaállíthatják a megosztások |

## <a name="why-use-storsimple"></a>Miért válassza partnerünket, a(z) StorSimple-t?

A StorSimple percek alatt csatlakoztatja a felhasználókat és a kiszolgálókat az Azure-tárhelyhez, alkalmazásmódosítás nélkül.

Az alábbi táblázat a StorSimple virtuális tömb megoldás által nyújtott legfontosabb előnyöket ismerteti.

| Szolgáltatás | Előny |
| --- | --- |
| Átlátható integráció |A virtuális tömb támogatja az iSCSI vagy az SMB protokollt. A helyi szint és a felhőszint közötti adatmozgás zökkenőmentes és átlátható a felhasználó számára. |
| Csökkentett tárolási költségek |A StorSimple segítségével elegendő helyi tárolót biztosít a leggyakrabban használt gyorsforgalmi adatok jelenlegi igényeinek kielégítéséhez. A tárolási igények növekedésével a StorSimple költséghatékony felhőalapú tárolásba rendeli a hideg adatokat. Az adatok deduplikálása és tömörítése a felhőbe való küldés előtt a tárolási követelmények és a költségek további csökkentése érdekében. |
| Egyszerűsített tároláskezelés |A StorSimple központosított felügyeletet biztosít a felhőben a StorSimple Eszközkezelő használatával több eszköz kezeléséhez. |
| Továbbfejlesztett katasztrófa-helyreállítás és megfelelőség |A StorSimple a metaadatok azonnali visszaállításával és szükség szerint az adatok visszaállításával megkönnyíti a gyorsabb vészhelyreállítást. Ez azt jelenti, hogy a normál működés minimális megszakítással folytatódhat. |
| Az adatok mobilitása |A felhőbe rétegzett adatok helyreállítási és áttelepítési célból más helyekről is elérhetők. Ne feledje, hogy az adatokat csak az eredeti virtuális tömbbe állíthatja vissza. A vész-helyreállítási szolgáltatások segítségével azonban visszaállíthatja a teljes virtuális tömböt egy másik virtuális tömbbe. |

## <a name="storsimple-workload-summary"></a>StorSimple munkaterhelés összegzése

A támogatott StorSimple számítási feladatok összegzése az alábbiakban táblázatba.

|Forgatókönyv     |Számítási feladat     |Támogatott      |Korlátozások               | Alkalmazható verziók|
|-------------|-------------|---------------|---------------------------|--------------------|
|Távoli iroda/fiókiroda (ROBO)  |Fájlmegosztás     |Igen      |Lásd [a fájlkiszolgálóra vonatkozó maximális korlátokat.](storsimple-ova-limits.md)<br></br>Lásd a [támogatott SMB-verziók rendszerkövetelményeit.](storsimple-ova-system-requirements.md)| Az összes verzió     |
|Felhőarchiválás  |Archiválási fájlmegosztás     |Igen      |Lásd [a fájlkiszolgálóra vonatkozó maximális korlátokat.](storsimple-ova-limits.md)<br></br>Lásd a [támogatott SMB-verziók rendszerkövetelményeit.](storsimple-ova-system-requirements.md)| Az összes verzió     |

A StorSimple virtuális tömb a legalkalmasabb a ritkán használt adatok. Míg a virtuális tömb rendelkezik egy helyi gyorsítótár a teljesítmény növelése érdekében, a felhasználók nak azt kell feltételezniük, hogy az eszköz szolgáltatások fájlokat a legalacsonyabb szintű tárolási (a felhő). Minden virtuális tömb körülbelül 100 Mb/s sebességgel tud írni és olvasni az Azure storage-ba. Ez a hivatkozás meg oszlik az eszközre érkező összes kérelem között, és szűk keresztmetszetté válhat, amint az az alábbi ábrán látható.

![Felhőarchiválás](./media/storsimple-ova-overview/cloud-archiving.png)

Ha több egyidejű felhasználó éri el a virtuális tömböt, akkor az Azure-ral létesített kapcsolatuk alacsonyabb teljesítményt eredményez. Felhasználónként nincs garantált teljesítmény, és az eszköz az egyes kéréseket azok érkezésekor dolgozza fel.

A StorSimple virtuális tömb nem alkalmas magas rendelkezésre állást igénylő számítási feladatokhoz. A virtuális tömb egyetlen csomóponteszköz, amely a szoftverfrissítések telepítésekor állásidőt tapasztal. A rendszergazdáknak évente 30 perces karbantartási időszakot kell tervezniük.

## <a name="workflows"></a>Munkafolyamatok

A StorSimple virtuális tömb különösen alkalmas a következő munkafolyamatokhoz:

* [Felhőalapú tárhelykezelés](#cloud-based-storage-management)
* [Helyfüggetlen biztonsági mentés](#location-independent-backup)
* [Adatvédelem és katasztrófa utáni helyreállítás](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>Felhőalapú tárhelykezelés
Az Azure Portalon futó StorSimple Eszközkezelő szolgáltatás sal kezelheti a több eszközön és több helyen tárolt adatokat. Ez különösen hasznos elosztott elágazási forgatókönyvekben. Vegye figyelembe, hogy a virtuális tömbök és a fizikai StorSimple eszközök kezeléséhez létre kell hoznia a StorSimple Eszközkezelő szolgáltatás külön példányait. Azt is vegye figyelembe, hogy a virtuális tömb mostantól az új Azure Portalt használja a klasszikus Azure-portál helyett.

![felhőalapú tárhelykezelés](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>Helyfüggetlen biztonsági mentés
A virtuális tömb, felhőbeli pillanatképek biztosít egy helyfüggetlen, időpont-in-time másolatot egy kötet vagy megosztás. A felhőbeli pillanatképek alapértelmezés szerint engedélyezve vannak, és nem tilthatók le. Az összes kötet és megosztás biztonsági mentésegy időben egyetlen napi biztonsági mentési házirenden keresztül történik, és szükség esetén további ad hoc biztonsági mentéseket is készíthet.

### <a name="data-protection-and-disaster-recovery"></a>Adatvédelem és katasztrófa utáni helyreállítás
A virtuális tömb a következő adatvédelmi és vész-helyreállítási forgatókönyveket támogatja:

* **Kötet- vagy megosztás-visszaállítás** – A visszaállítás új munkafolyamatként való használatához helyreállíthatja a kötetet vagy a megosztást. Ezzel a módszersel helyreállíthatja a teljes kötetet vagy megosztást.
* **Elemszintű helyreállítás** – A megosztások egyszerűsített hozzáférést biztosítanak a legutóbbi biztonsági mentések eléréséhez. Könnyedén helyreállíthatja az egyes fájlokat a felhőben elérhető speciális *.backup* mappából. Ez a visszaállítási képesség felhasználó által vezérelt, és nincs szükség felügyeleti beavatkozásra.
* **Vész-helyreállítási** – a feladatátvételi képesség segítségével helyreállíthatja az összes kötetet vagy megosztást egy új virtuális tömbhöz. Hozza létre az új virtuális tömböt, és regisztrálja azt a StorSimple Eszközkezelő szolgáltatással, majd feladatátvételt az eredeti virtuális tömbfelett. Az új virtuális tömb ezután vállalja a kiépített erőforrásokat.

## <a name="storsimple-virtual-array-components"></a>StorSimple virtuális tömb összetevői

A virtuális tömb a következő összetevőket tartalmazza:

* [Virtuális tömb](#virtual-array) – a virtualizált környezetben vagy hipervizorban kiépített virtuális gépen alapuló hibrid felhőalapú tárolóeszköz.
* [StorSimple Device Manager-szolgáltatás](#storsimple-device-manager-service) – Az Azure Portal egy bővítménye, amely lehetővé teszi egy vagy több StorSimple-eszköz kezelését egyetlen webes felületről, amelyet különböző földrajzi helyekről érhet el. A StorSimple Eszközkezelő szolgáltatás sal szolgáltatásokat hozhat létre és kezelhet, eszközöket és riasztásokat tekinthet meg és kezelhet, valamint köteteket, megosztásokat és meglévő pillanatképeket kezelhet.
* [Helyi webes felhasználói felület](#local-web-user-interface) – Webalapú felhasználói felület, amely az eszköz konfigurálására szolgál, hogy az csatlakozni tudhasson a helyi hálózathoz, majd regisztrálja az eszközt a StorSimple Eszközkezelő szolgáltatással. 
* [Parancssori felület](#command-line-interface) – Olyan Windows PowerShell-felület, amelynek segítségével támogatási munkamenetet indíthat a virtuális tömbön.
  A következő szakaszok részletesebben ismertetik ezeket az összetevőket, és ismertetik, hogy a megoldás hogyan rendezi el az adatokat, hogyan osztja ki a tárolást, és hogyan könnyíti meg a tárolás kezelést és az adatvédelmet.

### <a name="virtual-array"></a>Virtuális tömb

A virtuális tömb egy egycsomópontos tárolási megoldás, amely elsődleges tárolást biztosít, kezeli a kommunikációt a felhőalapú tárhellyel, és segít az eszközön tárolt összes adat biztonságának és titkosságának biztosításában.

A virtuális tömb egy letölthető modellben érhető el. A virtuális tömb maximális kapacitása 6,4 TB az eszközön (8 TB alapvető tárolási követelménnyel) és 64 TB a felhőalapú tárolással együtt.

A virtuális tömb a következő funkciókkal rendelkezik:

* Költséghatékony. Kihasználja a meglévő virtualizációs infrastruktúrát, és telepíthető a meglévő Hyper-V vagy VMware hipervizorra.
* Az adatközpontban található, és iSCSI-kiszolgálóként vagy fájlkiszolgálóként konfigurálható.
* Integrálva van a felhővel.
* A biztonsági mentések a felhőben tárolódnak, ami megkönnyítheti a vészhelyreállítást és egyszerűsítheti az elemszintű helyreállítást (ILR).
* A virtuális tömbre ugyanúgy alkalmazhat frissítéseket, mint egy fizikai eszközre.

> [!NOTE]
> Virtuális tömb nem bontható ki. Ezért fontos, hogy megfelelő tárhelyet, amikor létrehozza a virtuális tömb.

### <a name="storsimple-device-manager-service"></a>StorSimple-eszközkezelő szolgáltatás

A Microsoft Azure StorSimple egy webalapú felhasználói felületet, a StorSimple Eszközkezelő szolgáltatást biztosít, amely lehetővé teszi a StorSimple-tárhely központi kezelését. A StorSimple Eszközkezelő szolgáltatással a következő feladatokat hajthatja végre:

* Több StorSimple virtuális tömb kezelése egyetlen szolgáltatásból.
* A StorSimple virtuális tömbök biztonsági beállításainak konfigurálása és kezelése. (A felhőben lévő titkosítás a Microsoft Azure API-któl függ.)
* A tárfiók hitelesítő adatainak és tulajdonságainak konfigurálása.
* Kötetek vagy megosztások konfigurálása és kezelése.
* Kötetek vagy megosztások adatainak biztonsági és visszaállítása.
* A teljesítmény figyelése.
* Tekintse át a rendszerbeállításokat, és azonosítsa a lehetséges problémákat.

A StorSimple Eszközkezelő szolgáltatás segítségével napi felügyelet a virtuális tömb.

További információt a [StorSimple Eszközkezelő szolgáltatás használata a StorSimple-eszköz felügyeletéhez](storsimple-virtual-array-manager-service-administration.md)című területen talál.

### <a name="local-web-user-interface"></a>Helyi webes felhasználói felület

A virtuális tömb tartalmaz egy webalapú felhasználói felületet, amely az eszköz egyszeri konfigurálásához és a StorSimple Eszközkezelő szolgáltatással való regisztrálásához használatos. Segítségével leállíthatja és újraindíthatja a virtuális tömböt, diagnosztikai teszteket futtathat, szoftvereket frissíthet, módosíthatja az eszköz rendszergazdai jelszavát, megtekintheti a rendszernaplókat, és szervizkérelem benyújtásához forduljon a Microsoft támogatási szolgálatához.

A webalapú felhasználói felület használatáról a [StorSimple virtuális tömb felügyelete a webalapú felhasználói felület használata című lapban](storsimple-ova-web-ui-admin.md)talál tájékoztatást.

### <a name="command-line-interface"></a>Parancssori felület

A mellékelt Windows PowerShell-felület lehetővé teszi, hogy támogatási munkamenetet kezdeményezzen a Microsoft támogatási szolgálatával, hogy azok segíthessenek a virtuális tömbön esetleg felmerülő problémák elhárításában és megoldásában.

## <a name="storage-management-technologies"></a>Tárolási kezelési technológiák

A virtuális tömb önarchiták és más összetevők mellett a StorSimple megoldás a következő szoftvertechnológiákat használja a fontos adatok gyors eléréséhez, a tárhelyfelhasználás csökkentéséhez és a virtuális tömbön tárolt adatok védelméhez:

* [Automatikus tárolási rétegezés](#automatic-storage-tiering) 
* [Helyileg rögzített megosztások és kötetek](#locally-pinned-shares-and-volumes)
* Deduplikáció és tömörítés rétegzett vagy felhőbe biztonsági másolatban 
* [Ütemezett és igény szerinti biztonsági mentések](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>Automatikus tárolási rétegezés
A virtuális tömb egy új rétegezési mechanizmust használ a tárolt adatok kezelésére a virtuális tömbben és a felhőben. Csak két réteg van: a helyi virtuális tömb és az Azure felhőalapú tárhely. A StorSimple virtuális tömb automatikusan rendezi az adatokat a rétegek alapján egy hőtérkép, amely nyomon követi az aktuális használat, kor és más adatokkal való kapcsolatok. A legaktívabb (legforróbb) adatokat helyileg tárolják, míg a kevésbé aktív és inaktív adatokat automatikusan áttelepíti a felhőbe. (Az összes biztonsági mentés a felhőben tárolódik.) A StorSimple a használati minták változásával módosítja és átrendezi az adat- és tárolási hozzárendeléseket. Előfordulhat például, hogy egyes információk idővel kevésbé aktívak. Mivel fokozatosan kevésbé aktív, rétegzett ki a felhőbe. Ha ugyanezek az adatok ismét aktívvá válnak, a rendszer rétegezi a tárolótömbbe.

Egy adott rétegzett megosztás vagy kötet adatai garantáltak a saját helyi rétegterület (körülbelül 10%-a teljes kiosztott terület az adott megosztás vagy kötet). Bár ez csökkenti a virtuális tömb ben az adott megosztás vagy kötet rendelkezésre álló tárhelyét, biztosítja, hogy egy megosztás vagy kötet rétegezését ne befolyásolja más megosztások vagy kötetek rétegezési igényei. Így egy nagyon elfoglalt számítási feladat egy megosztáson vagy köteten nem kényszerítheti az összes többi számítási feladatot a felhőbe.

Az iSCSI-hez létrehozott rétegzett kötetek maximális helyi foglalása a kötet méretétől függetlenül 200 GB.

![automatikus tárolási rétegezés](./media/storsimple-ova-overview/automatic-storage-tiering.png)

> [!NOTE]
> Megadhatja a kötet helyileg rögzített, ebben az esetben az adatok a virtuális tömbben maradnak, és soha nem rétegzett a felhőbe. További információért nyissa meg [a helyileg rögzített megosztások és kötetek című részeket.](#locally-pinned-shares-and-volumes)


### <a name="locally-pinned-shares-and-volumes"></a>Helyileg rögzített megosztások és kötetek

Létrehozhat ja a megfelelő megosztásokat és köteteket helyileg rögzítettként. Ez a képesség biztosítja, hogy a kritikus alkalmazások által igényelt adatok a virtuális tömbben maradnak, és soha nem lesznek rétegzve a felhőbe. A helyileg rögzített megosztások és kötetek a következő funkciókkal rendelkeznek:

* Nem vonatkoznak rájuk a felhőbeli késések vagy kapcsolódási problémák.
* Továbbra is élvezhetik a StorSimple felhőalapú biztonsági mentési és vész-helyreállítási funkcióinak előnyeit.

A helyileg rögzített megosztást vagy kötetet rétegzettként, illetve rétegzett megosztásként vagy kötetként visszaállíthatja helyileg rögzítettként. 

A helyileg rögzített kötetekről a [Kötetek kezeléséhez a StorSimple Eszközkezelő szolgáltatás használata című](storsimple-virtual-array-manage-volumes.md)rész című részben talál további információt.

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>Deduplikáció és tömörítés rétegzett vagy felhőbe biztonsági másolatban

A StorSimple deduplikációt és adattömörítést használ a felhőbeli tárolási követelmények további csökkentése érdekében. A deduplikáció csökkenti a tárolt adatok teljes mennyiségét azáltal, hogy kiküszöböli a redundanciát a tárolt adatkészletben. Az információ változásakor a StorSimple figyelmen kívül hagyja a változatlan adatokat, és csak a módosításokat rögzíti. Emellett a StorSimple csökkenti a tárolt adatok mennyiségét az ismétlődő adatok azonosításával és eltávolításával.

> [!NOTE]
> A virtuális tömbben tárolt adatok nem deduplikáltak vagy tömörítettek. Minden deduplikáció és tömörítés közvetlenül az adatok felhőbe küldése előtt történik.

### <a name="scheduled-and-on-demand-backups"></a>Ütemezett és igény szerinti biztonsági mentések

A StorSimple adatvédelmi funkciók lehetővé teszik igény szerinti biztonsági mentések létrehozását. Emellett az alapértelmezett biztonsági mentési ütemezés biztosítja, hogy az adatokról naponta biztonsági másolat készüljön. A biztonsági mentések növekményes pillanatképek formájában készülnek, amelyek a felhőben tárolódnak. A pillanatképek, amelyek csak a legutóbbi biztonsági mentés óta végrehajtott módosításokat rögzítik, gyorsan létrehozhatók és visszaállíthatók. Ezek a pillanatképek kritikus fontosságúak lehetnek a vész-helyreállítási forgatókönyvekben, mert helyettesítik a másodlagos tárolórendszereket (például a szalagos biztonsági mentést), és lehetővé teszik az adatok visszaállítását az adatközpontba vagy szükség esetén alternatív helyekre.

## <a name="managing-personal-information"></a>Személyes adatok kezelése

A virtuális sorozatsstorSimple Eszközkezelő két kulcspéldányban gyűjtszemélyes adatokat:
 - Figyelmeztesse a felhasználói beállításokat, ahol a felhasználók e-mail címei vannak konfigurálva. Ezt az információt a rendszergazda törölheti. 
 - Azok a felhasználók, akik hozzáférhetnek a megosztásokon lévő adatokhoz. Megjelenik azoknak a felhasználóknak a listája, akik hozzáférhetnek a megosztási adatokhoz, és exportálhatók. Ez a lista a megosztások törlésekor is törlődik.

További információt a [Microsoft Adatvédelmi szabályzata az Adatvédelmi központban talál.](https://www.microsoft.com/trustcenter)

## <a name="next-steps"></a>További lépések

További információ [a virtuális tömbportál előkészítéséről.](storsimple-virtual-array-deploy1-portal-prep.md)
