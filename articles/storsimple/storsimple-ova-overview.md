---
title: A Microsoft Azure StorSimple Virtual Array – áttekintés |} A Microsoft Docs
description: Ismerteti a StorSimple Virtual Array, egy integrált tárolási megoldás, amely egy helyszíni virtuális tömb és a Microsoft Azure felhőalapú tárolást közötti tárolási feladatokat kezeli.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 169c639b-1124-46a5-ae69-ba9695525b77
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/02/2018
ms.author: alkohli
ms.openlocfilehash: 78ed53e5e2f5d04943e6c32ddfedf037cb9e1f73
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39480954"
---
# <a name="introduction-to-the-storsimple-virtual-array"></a>A StorSimple Virtual Array – bevezetés

## <a name="overview"></a>Áttekintés

A Microsoft Azure StorSimple Virtual Array egy integrált tárolási megoldás, amely a hipervizor és a Microsoft Azure cloud storage-ban futó helyszíni virtuális tömb közötti tárolási feladatokat kezeli. A virtuális tömb egy hatékony, költségkímélő és könnyen felügyelt fájlkiszolgáló vagy a problémák és a költségeket, a nagyvállalati adattárolás és -védelem számos költsége kiküszöbölhető az iSCSI-és kiszolgálóoldali megoldása. A virtuális tömb különösen alkalmas a ritkábban használt archív adatokhoz, tárolásához.

Ez a cikk áttekintést a virtuális tömb – Íme néhány további forrás:

* Ajánlott eljárásokat lásd: [ajánlott eljárások a StorSimple Virtual Array](storsimple-ova-best-practices.md).
* A StorSimple 8000 sorozatú eszközök áttekintéséhez keresse [a StorSimple 8000 sorozat: egy hibrid felhőmegoldás](storsimple-overview.md).
* A StorSimple 5000/7000 sorozat eszközökkel kapcsolatos információkért keresse [StorSimple Online súgó](http://onlinehelp.storsimple.com/).

A virtuális tömb támogatja az iSCSI- vagy Server Message Block (SMB) protokollt. A meglévő hipervizor-infrastruktúra fut, és a felhő, felhőalapú biztonsági mentés, a gyors visszaállítás, elemszintű helyreállítás és vész-helyreállítási funkciók rétegezést biztosít.

A következő táblázat összefoglalja a StorSimple Virtual Array fontos jellemzőit.

| Szolgáltatás | StorSimple Virtual Array |
| --- | --- |
| Telepítési követelmények |Használja a virtualizálási infrastruktúrában (Hyper-V vagy VMware) |
| Rendelkezésre állás |Egyetlen csomópont |
| Teljes kapacitás (beleértve a felhő) |Akár 64 TB felhasználható kapacitás virtuális tömbönként |
| Helyi kapacitás |6.4-es TB felhasználható kapacitás a virtuális tömb (kell kiépíteni a merevlemez-terület 500 GB – 8 TB) / 390 GB |
| Natív protokollok |iSCSI- vagy SMB |
| Helyreállítási időre vonatkozó célkitűzés (RTO) |iSCSI: kevesebb mint 2 percet méretétől függetlenül |
| Helyreállítási időkorlát (RPO) |Napi biztonsági mentésekhez, és igény szerinti biztonsági mentéseket |
| Tárolási rétegek |Használja a leképezés csak határozza meg, milyen adatokat kell lennie rétegzett, vagy melegítsük |
| Támogatás |A szolgáltató által támogatott virtualizálási infrastruktúrában |
| Teljesítmény |Alapul szolgáló infrastruktúra függően változik |
| Rugalmasan áthelyezhető adatok |Állíthatja vissza ugyanarra az eszközre vagy elemszintű helyreállítást (fájlkiszolgáló) |
| Tárolási rétegek |Helyi hipervizoron tárolása és felhőben |
| Megosztás méretének |A rétegzett: akár 20 TB-ig; a helyileg rögzített: 2 TB-ig |
| Kötet mérete |A többszintű: 500 GB-os 5 TB-ig; a helyileg rögzített: 50 GB és 200 GB <br> A rétegzett kötetek maximális helyi foglalás 200 GB-os. |
| Pillanatképek |Összeomlás-konzisztens |
| Elemszintű helyreállítás |Igen; felhasználók visszaállíthatják a megosztások |

## <a name="why-use-storsimple"></a>A StorSimple miért érdemes használni?

A StorSimple összekapcsolja a felhasználókat és kiszolgálók az Azure storage-alkalmazás bármilyen módosítás nélkül, percek alatt.

A következő táblázat néhány fontos előnye, hogy a StorSimple Virtual Array megoldás biztosítja.

| Szolgáltatás | Előny |
| --- | --- |
| Transzparens integráció |A virtuális tömb támogatja az iSCSI- vagy az SMB protokoll. Az adatok áthelyezése a helyi szinten és a felhő szintjén között, zökkenőmentes és a felhasználó számára. |
| Alacsonyabb tárolási költségek |A StorSimple használatakor az aktuális igények figyelembevételével a leggyakrabban használt gyakori elérésű adatok elegendő helyi tároló üzembe helyezése. Tárolási igények, növelhető, a StorSimple rétegek ritkán használt adatok költséghatékony felhőbeli tárhelyén. Az adatok deduplikált, és a tömörített további a tárhellyel kapcsolatos követelmények és költségek csökkentése érdekében a felhőbe való elküldése előtt. |
| Egyszerűbb |A StorSimple több eszköz kezelése a StorSimple-Eszközkezelő segítségével a felhőben központosított felügyeletet biztosít. |
| Továbbfejlesztett vész-helyreállítási és megfelelőség |StorSimple megkönnyíti a gyorsabb vészhelyreállítás által azonnal visszaállítása a metaadatokat és az adatok helyreállítása, igény szerint. Ez azt jelenti, hogy minimális továbbra is a normál működést. |
| Rugalmasan áthelyezhető adatok |A felhő rétegzett adatok használatát a többi hely helyreállítási és migrálási célokból érhető el. Vegye figyelembe, hogy visszaállíthatja az eredeti virtuális tömb csak az adatokat. Használhatja azonban vész-helyreállítási funkciók a teljes virtuális tömb visszaállítása egy másik virtuális tömbbe. |

## <a name="storsimple-workload-summary"></a>A StorSimple a számítási feladatok összefoglalása

A StorSimple elvégezhető feladatok összefoglalása a lenti táblázatban láthatóak.

|Forgatókönyv     |Számítási feladat     |Támogatott      |Korlátozások               |
|-------------|-------------|---------------|---------------------------|
|Távoli Office/fiókiroda (ROBO)  |Fájlmegosztás     |Igen      |Lásd: [korlátozásainak fájlkiszolgáló](storsimple-ova-limits.md).<br></br>Lásd: [támogatott SMB-verzió rendszerkövetelményei](storsimple-ova-system-requirements.md).| Az összes verzió     |
|A felhő archiválása  |Archív fájl megosztása     |Igen      |Lásd: [korlátozásainak fájlkiszolgáló](storsimple-ova-limits.md).<br></br>Lásd: [támogatott SMB-verzió rendszerkövetelményei](storsimple-ova-system-requirements.md).| Az összes verzió     |

A StorSimple Virtual Array olyan a ritkán használt adatokhoz. Míg a virtuális tömb helyi gyorsítótárral a nagyobb teljesítmény, a felhasználók kell feltételezik, hogy az eszköz services, storage (felhő), a legalacsonyabb szinttel fájlokat. Minden egyes virtual array írása és olvasása az Azure storage-körülbelül 100 MB/s. Hivatkozás az eszközre érkező összes kérelemegységének van osztva, és szűk keresztmetszetet jelenthet az alábbi ábrán látható módon.

![A felhő archiválása](./media/storsimple-ova-overview/cloud-archiving.png)

Több egyidejű felhasználók férhetnek hozzá a virtuális tömböt, ha az összes osztoznak egy kisebb teljesítményt és a kapcsolatot. Nem garantált teljesítményt nyújtó felhasználónként van, és az eszköz feldolgozza az egyes kérések azonnali vizsgálatát.

A StorSimple Virtual Array nem alkalmas a magas rendelkezésre állást igénylő számítási feladatokhoz. A virtuális tömb egy egyetlen csomóponttal rendelkező eszköz, amely során állásidő lép, amikor a szoftverfrissítések telepítése. A rendszergazdák a karbantartási időszak 30 perces évenként 3 - 4 alkalommal kell terveznie.

## <a name="workflows"></a>Munkafolyamatok

A StorSimple Virtual Array a következő munkafolyamatok különösen alkalmas kiterjedt:

* [Felhőalapú tárolók kezelése](#cloud-based-storage-management)
* [Helyszínfüggetlen biztonsági mentést](#location-independent-backup)
* [Adat-helyreállítás védelem és vészhelyreállítás](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>Felhőalapú tárolók kezelése
Az Azure Portalon fut, a StorSimple-Eszközkezelő szolgáltatás segítségével több eszközön, és több helyen tárolt adatok kezeléséhez. Ez különösen hasznos az elosztott fiókirodai forgatókönyvek. Vegye figyelembe, hogy a virtuális tömbök és a fizikai StorSimple-eszközök kezelése a StorSimple-Eszközkezelő szolgáltatás külön példányát kell létrehoznia. Azt is vegye figyelembe, hogy a virtuális tömb most már használja az új Azure Portalon a klasszikus Azure portál helyett.

![felhőalapú tárolók kezelése](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>Helyszínfüggetlen biztonsági mentést
A virtuális tömb felhőbeli pillanatképek biztosíthat egy kötetet vagy megosztást helyfüggetlen, időponthoz másolatát. Felhőbeli pillanatképek alapértelmezés szerint engedélyezve vannak, és nem tiltható le. Minden kötetek és megosztások biztonsági mentése napi biztonsági mentési szabályzat használatával egy időben, és elvégezhető a további ad hoc biztonsági mentések, amikor szükséges.

### <a name="data-protection-and-disaster-recovery"></a>Adat-helyreállítás védelem és vészhelyreállítás
A virtuális tömb az alábbi az adatvédelem és a vészhelyreállítási forgatókönyveket támogatja:

* **Kötetet vagy megosztást visszaállítási** – új munkafolyamat használja a restore helyreállítani egy kötetet vagy megosztást. Ez a módszer használatával a teljes kötetet vagy megosztást helyreállítani.
* **Elemszintű helyreállítás** – megosztások egyszerűen hozzáférhet a legújabb biztonsági mentések engedélyezése. Egyetlen fájl könnyen helyreállítható egy speciális *.backup* mappa elérhető a felhőben. A visszaállítási funkció felhasználói és rendszergazdai beavatkozás nélküli nem szükséges.
* **Vész-helyreállítási** – feladatátvételi képességének használata minden kötetek vagy megosztások egy új virtuális tömb való helyreállításához. Az új virtuális tömb létrehozása és a StorSimple-Eszközkezelő szolgáltatással regisztrálja, majd átadja a feladatokat az eredeti virtuális tömb. Az új virtuális tömb majd feltételezi a kiépített erőforrások.

## <a name="storsimple-virtual-array-components"></a>A StorSimple Virtual Array összetevők

A virtuális tömb az alábbi összetevőket tartalmazza:

* [Virtuális tömb](#virtual-array) – a virtualizált környezet vagy hipervizoron üzembe helyezett virtuális gépen alapuló hibrid felhőalapú tárolóeszköz.
* [StorSimple-Eszközkezelő szolgáltatás](#storsimple-device-manager-service) kiterjesztése az Azure Portal, amely lehetővé teszi egy vagy több StorSimple eszközt, amely a földrajzilag különböző helyeken keresztül elérhető közös webes felületről kezelheti. Hozzon létre és a szolgáltatások kezeléséhez, megtekintheti és kezelheti az eszközöket és a riasztások és kötetek, megosztások és meglévő pillanatképek kezelése a StorSimple-Eszközkezelő szolgáltatás segítségével.
* [Helyi webes felhasználói felület](#local-web-user-interface) – egy webalapú felhasználói felület, amellyel az eszköz konfigurálása, hogy azt a helyi hálózathoz csatlakozzon, és ezután regisztrálja az eszközt a StorSimple-Eszközkezelő szolgáltatással. 
* [Parancssori felület](#command-line-interface) – A Windows PowerShell felület használatával indítson el egy támogatási munkamenetet a virtuális tömb.
  Az alábbi szakaszok ismertetik ezeket az összetevőket nagyobb részletességgel, és a megoldás hogyan rendezi az adatokat, foglalja le a storage, és megkönnyíti a tárolók kezelése és az adatvédelem ismertetik.

### <a name="virtual-array"></a>Virtuális tömb

A virtuális tömb egy egycsomópontos tárolómegoldás, amely elsődleges tárolót biztosít, a felhőalapú tárolással kommunikációt kezeli, és segít biztosítani, a biztonság és az eszközön tárolt adatok titkosságát.

A virtuális tömb, amely letölthető egy modell érhető el. A virtuális tömb kapacitása maximum 6.4-es TB az eszközt (az egy alapul szolgáló tárolási követelmény legfeljebb 8 TB) és 64 TB-os, beleértve a felhőalapú tárolás.

A virtuális tömb a következő funkciókkal rendelkezik:

* Költséghatékony. Ez megkönnyíti a meglévő virtualizálási infrastruktúrával használja, és is üzembe helyezhetők a meglévő Hyper-V vagy VMware hipervizoron.
* Az adatközpontban található és a egy iSCSI-kiszolgáló vagy egy fájlkiszolgáló beállítható.
* Integrálva van a felhőben.
* Biztonsági másolatok a felhőben, amelyek megkönnyítik a vész-helyreállítási és egyszerűsítheti az elemszintű helyreállítást (ILR) vannak tárolva.
* A virtuális tömb frissítéseket alkalmazhat, ugyanúgy, mint a lenne egy fizikai eszköz vonatkozik.

> [!NOTE]
> A virtuális tömb nem bonthatók ki. Ezért fontos a megfelelő tároló létesítése, ha a virtuális tömb létrehozása.

### <a name="storsimple-device-manager-service"></a>StorSimple-eszközkezelő szolgáltatás

A Microsoft Azure StorSimple egy webalapú felhasználói felület, a StorSimple-Eszközkezelő szolgáltatás, amely lehetővé teszi, hogy központilag kezelheti a StorSimple-tároló biztosít. A StorSimple-Eszközkezelő szolgáltatás segítségével a következő feladatokat:

* Egyetlen szolgáltatás több StorSimple Virtual Arrayt kezelése.
* Konfigurálása és kezelése a StorSimple Virtual Arrayt biztonsági beállításait. (A titkosítás a felhőben a Microsoft Azure API-k függ.)
* Tárfiók hitelesítő adatainak és tulajdonságok konfigurálása.
* Konfigurálása és kezelése a kötetek vagy megosztások.
* Biztonsági mentése és visszaállítása a kötetek és megosztások.
* Teljesítmény figyelése.
* Tekintse át a rendszer beállításait, és azonosíthatja a lehetséges problémákat.

A StorSimple-Eszközkezelő szolgáltatás segítségével a virtuális tömb napi felügyelet.

További információért ugorjon [a StorSimple-eszköz felügyelete a StorSimple-Eszközkezelő szolgáltatás segítségével](storsimple-virtual-array-manager-service-administration.md).

### <a name="local-web-user-interface"></a>Helyi webes felhasználói felület

A virtuális tömböt tartalmaz egy webalapú felhasználói felület, amely egyszeri konfigurálásának és regisztrációjának az eszközt a StorSimple-Eszközkezelő szolgáltatással szolgál. Állítsa le és indítsa újra a virtuális tömb, futtasson diagnosztikai teszteket, frissítheti a szoftvereket, módosítsa az eszköz rendszergazdai jelszava, megtekinthetők a naplófájlok a system és Microsoft Support küldjön szolgáltatási kérelmet használhatja azt.

A webes felhasználói felület használatával kapcsolatos információkért keresse [a webes felhasználói felület segítségével felügyelheti a StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

### <a name="command-line-interface"></a>Parancssori felület

A csomagban foglalt Windows PowerShell felület lehetővé teszi egy támogatási munkamenetet a Microsoft Support kezdeményezni, így meghatározhatja, hogy a virtuális tömb az előforduló hibák elhárítása és hibáinak elhárítása.

## <a name="storage-management-technologies"></a>Tárolási felügyeleti technológiák

A StorSimple megoldás mellett a virtuális tömb és más olyan összetevők, a következő szoftver technológiákat használ a gyors hozzáférést biztosít a fontos adatok tárhelyfelhasználás csökkentésében és a virtuális tömb a tárolt adatok védelme érdekében:

* [Automatikus tárolórétegzés](#automatic-storage-tiering) 
* [A gyors helyi-megosztásokat és -kötetek](#locally-pinned-shares-and-volumes)
* [A deduplikáció és az adatok tömörítése rétegzett, vagy a felhő biztonsági mentése](#deduplication-and-compression-for-data-tiered/backed-up-to-the-cloud) 
* [Ütemezett és igény szerinti biztonsági mentéseket](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>Automatikus tárolórétegzés
A virtuális tömb új rétegezési mechanizmust alkalmaz a virtuális tömb és a felhőben tárolt adatok kezelésére. Nincsenek csak két szinten: a helyi virtuális tömb és az Azure felhőbeli tárhelyén. A StorSimple Virtual Array adatokat automatikusan elrendezése a rétegekben hőtérkép, amely nyomon követi aktuális használati életkor és kapcsolatok más adatok alapján. Adatok, amelyek a legtöbb aktív (legkeresettebb) helyileg van tárolva, míg a kevésbé aktív és inaktív adatok automatikus áttelepítése a felhőbe. (Minden biztonsági mentés a felhőben vannak tárolva.) StorSimple állítja be, és újrarendezi az adatokat, és a használati mintákat, tároló-hozzárendelések módosítása. Például bizonyos adatok válhat, kevésbé aktív idővel. Váló fokozatosan kevésbé aktív, akkor többszintű ki a felhőbe. Ha ugyanazokat az adatokat, hogy újra aktívvá válik, azt többszintű a tárolótömbhöz.

Egy adott rétegzett megosztás vagy a kötet adatainak garantáltan a saját helyi rétegen terület (körülbelül 10 %-a teljes kiosztott terület az adott fájlmegosztás vagy kötet). Bár ez csökkenti a rendelkezésre álló tár az adott fájlmegosztás vagy kötet a virtuális tömb, biztosítja, hogy rétegek egy fájlmegosztás vagy kötet nem érinti a további kötetek vagy megosztások rétegezési igényeinek. Így nagyon elfoglalt számítási feladatok egy fájlmegosztás vagy kötet nem kényszerítheti ki az összes egyéb számítási feladatok felhőbe.

Az iSCSI létrehozott rétegzett kötetek rendelkezik maximális helyi foglalást a 200 GB-os függetlenül a kötet méretét.

![automatikus tárolórétegzés](./media/storsimple-ova-overview/automatic-storage-tiering.png)

> [!NOTE]
> A helyileg rögzített kötet is megadhat, ebben az esetben az adatok a virtuális tömb nem marad, és soha nem rétegzett a felhőbe. További információért ugorjon [-megosztásokat és -köteteket a helyileg rögzített](#locally-pinned-shares-and-volumes).


### <a name="locally-pinned-shares-and-volumes"></a>A gyors helyi-megosztásokat és -kötetek

Megfelelő megosztások és a helyileg rögzített kötetek is létrehozhat. Ez a képesség biztosítja, hogy a kritikus fontosságú alkalmazásai által igényelt adatok továbbra is a virtuális tömb, és soha nem többszintű a felhőbe. A gyors helyi-megosztásokat és -köteteket a következő jellemzőkkel rendelkeznek:

* Ezekre nem vonatkozik felhőalapú késések vagy kapcsolódási problémák.
* Azok a StorSimple felhőalapú biztonsági mentési és vész helyreállítási funkciók továbbra is élvezhetik.

Visszaállíthatja egy helyileg rögzített megosztást vagy, a rétegzett kötet vagy rétegzett megosztás vagy a kötet a helyileg rögzített. 

A gyors helyi kötetek kapcsolatos további információkért látogasson el [kötetek kezelése a StorSimple-Eszközkezelő szolgáltatás segítségével](storsimple-virtual-array-manage-volumes.md).

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>A deduplikáció és az adatok tömörítése rétegzett, vagy a felhő biztonsági mentése

A StorSimple a deduplikáció és az adatok tömörítés használja tovább csökkentheti a felhőbeli tárhellyel kapcsolatos követelmények. A deduplikáció csökkenti a tárolt adatkészlet redundanciájának kiküszöbölése révén tárolt adatok teljes mennyisége. Adat változik, ahogy a StorSimple figyelmen kívül hagyja a változatlan adatok, és csak a változtatásokat rögzíti. A StorSimple emellett azonosításával és a duplikált adatok eltávolítását a csökkenti a tárolt adatok mennyisége.

> [!NOTE]
> A virtuális tömb tárolt adatok nem deduplikált vagy tömörítve. Az összes adatdeduplikáció és a tömörítés előtt az adatokat küld a felhőben történik.

### <a name="scheduled-and-on-demand-backups"></a>Ütemezett és igény szerinti biztonsági mentéseket

Adatvédelmi funkciókat a StorSimple lehetővé teszi igény szerinti biztonsági mentések létrehozását. Emellett egy alapértelmezett biztonsági mentési ütemezést biztosítja, hogy adatokat készüljön biztonsági mentés naponta. Biztonsági mentés növekményes pillanatkép, amely a felhőben vannak tárolva formájában kerül. Jegyezze fel a módosítások csak az utolsó biztonsági mentés óta, pillanatképeket hozhat létre és gyorsan visszaállítva. Ezeket a pillanatképeket is kell koncentrálniuk, a vész-helyreállítási helyzetekben, mert cserélje le a másodlagos tárhelyen rendszereket (ilyen például a szalagos biztonsági mentés), és lehetővé teszi az adatok helyreállítását az adatközpontban vagy a másodlagos helyek szükség esetén.

## <a name="managing-personal-information"></a>Személyes adatok kezelése

Virtual Series StorSimple-Eszközkezelő gyűjti össze a két fő példányt a személyes adatokat:
 - Riasztás, ahol a felhasználók e-mail címét konfigurálva vannak a felhasználói beállításokat. Ez az információ a számítógépek a rendszergazda által. 
 - Felhasználók, akik férhet hozzá az adatokhoz, a megosztások elhelyezkedhet. Felhasználók, akik férhetnek hozzá a megosztás adataihoz listája jelenik meg, és exportálhatók. Ez a lista is törlődik. a megosztások törlésekor.

További információkért tekintse át a [biztonsági és adatkezelési központ a Microsoft Privacy szabályzatokban](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [készítse elő a virtuális tömb portál](storsimple-virtual-array-deploy1-portal-prep.md).
