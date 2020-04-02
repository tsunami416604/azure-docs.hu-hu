---
title: A StorSimple virtual array ajánlott eljárása | Microsoft dokumentumok
description: A StorSimple virtuális tömb telepítésével és kezelésével kapcsolatos gyakorlati tanácsok ismertetése.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 57ac6eeb-c47c-442d-a5f4-b360d81a76a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 82608c98fc8ea15167b690547906c2238b1b3c04
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80544343"
---
# <a name="storsimple-virtual-array-best-practices"></a>A StorSimple Virtual Array ajánlott eljárásai

## <a name="overview"></a>Áttekintés

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

A Microsoft Azure StorSimple virtual array egy integrált tárolási megoldás, amely a hipervizorban futó helyszíni virtuális eszközök és a Microsoft Azure felhőalapú tárhelye közötti tárolási feladatokat kezeli. A StorSimple virtual array a 8000-es sorozatú fizikai tömb hatékony és költséghatékony alternatívája. A virtuális tömb futhat a meglévő hipervizor infrastruktúrán, támogatja mind az iSCSI és az SMB protokollok, és kiválóan alkalmas a távoli irodai/ fiókiroda forgatókönyvek. A StorSimple megoldásokkal kapcsolatos további információkért látogasson el a [Microsoft Azure StorSimple áttekintése című témakörbe.](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx)

Ez a cikk a StorSimple virtuális tömb kezdeti beállítása, üzembe helyezése és felügyelete során alkalmazott ajánlott eljárásokat ismerteti. Ezek az ajánlott eljárások a virtuális tömb beállítására és felügyeletére vonatkozóérvényesítési irányelveket biztosítják. Ez a cikk az okat az adatközpontokban üzembe helyező és kezelő rendszergazdákat célozza meg.

Javasoljuk, hogy rendszeresen tekintse át az ajánlott eljárásokat annak érdekében, hogy az eszköz továbbra is megfeleljen a beállítási vagy üzemeltetési folyamat módosításakor. Ha a virtuális tömbön megvalósítása során bármilyen probléma merül fel, segítségért forduljon a [Microsoft támogatási szolgálatához.](storsimple-virtual-array-log-support-ticket.md)

## <a name="configuration-best-practices"></a>A konfigurációval kapcsolatos gyakorlati tanácsok
Ezek az ajánlott eljárások azokat az irányelveket fedik le, amelyeket a virtuális tömbök kezdeti beállítása és üzembe helyezése során be kell tartani. Ezek az ajánlott eljárások közé tartoznak a virtuális gép kiépítésével, a csoportházirend-beállításokkal, a méretezéssel, a hálózatbeállítással, a tárfiókok konfigurálásával, valamint a virtuális tömb megosztásainak és köteteinek létrehozásával kapcsolatos eljárások. 

### <a name="provisioning"></a>Kiépítés
A StorSimple virtuális tömb egy virtuális gép (VM) a gazdakiszolgáló hipervizorán (Hyper-V vagy VMware) kiépítve. A virtuális gép kiépítésekor győződjön meg arról, hogy a gazdagép képes elegendő erőforrást fordítani. További információkért lépjen a [tömb minimális erőforrás-követelményeire.](storsimple-virtual-array-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements)

A virtuális tömb kiépítésekor valósítsa meg a következő ajánlott eljárásokat:

|  | Hyper-V | VMware |
| --- | --- | --- |
| **Virtuális gép típusa** |**2. generáció** Virtuális gép a Windows Server 2012-es vagy újabb rendszerhez, valamint *.vhdx-lemezképhez.* <br></br> **1. generáció** Virtuális gép Windows Server 2008-as vagy újabb rendszerhez, valamint *.vhd* lemezképhez. |A *.vmdk* lemezkép használata esetén használja a 8-as virtuális gép 8-as verzióját. |
| **Memória típusa** |Konfigurálás **statikus memóriaként**. <br></br> Ne használja a **dinamikus memória** beállítást. | |
| **Adatlemez típusa** |Provision **dinamikusan bővülő**.<br></br> **A rögzített méret** hosszú időt vesz igénybe. <br></br> Ne használja a **differencing** beállítást. |Használja a **vékony üzembe építési** lehetőséget. |
| **Adatlemez módosítása** |A tágulás vagy a zsugorítás nem engedélyezett. Ennek kísérlete az eszközön lévő összes helyi adat elvesztését eredményezi. |A tágulás vagy a zsugorítás nem engedélyezett. Ennek kísérlete az eszközön lévő összes helyi adat elvesztését eredményezi. |

### <a name="sizing"></a>Méretezés
A StorSimple virtuális tömb méretezésekor vegye figyelembe a következő tényezőket:

* Kötetek vagy megosztások helyi foglalása. A terület körülbelül 12%-a van fenntartva a helyi rétegen minden egyes kiépített rétegzett kötet vagy megosztás számára. A terület nagyjából 10%-a a fájlrendszer helyileg rögzített kötete számára is van fenntartva.
* Pillanatkép többletterhelés. A helyi szinten nagyjából 15%-os terület van fenntartva a pillanatképek számára.
* Helyre kell állítani. Ha a visszaállítást új műveletként végzi, a méretezésnek figyelembe kell vennie a visszaállításhoz szükséges helyet. A visszaállítás azonos méretű megosztásra vagy kötetre történik.
* A váratlan növekedéshez bizonyos puffereket kell lefoglalni.

Az előző tényezők alapján a méretezési követelményeket a következő egyenlet telítheti:

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`

Az alábbi példák bemutatják, hogyan méretezhet egy virtuális tömböt a követelmények alapján.

#### <a name="example-1"></a>1. példa:
A virtuális tömbön meg szeretné tudni, hogy

* 2 TB-os többszintű kötetet vagy megosztást.
* 1 TB-os többszintű kötetet vagy megosztást.
* 300 GB-os helyileg rögzített kötetet vagy megosztást.

Az előző kötetek vagy részvények esetében számoljuk ki a helyi szinten a helyigényeket.

Először is, minden rétegzett kötet/megosztás esetében a helyi foglalás a kötet/megosztás méretének 12%-a lenne. A helyileg rögzített kötet/megosztás esetében a helyi foglalás a helyileg rögzített kötet/megosztás méretének 10 %-a (a kiosztott méreten kívül). Ebben a példában

* 240 GB-os helyi foglalás (2 TB-os többszintű kötet/megosztás esetén)
* 120 GB-os helyi foglalás (1 TB-os többszintű kötet/megosztás esetén)
* 330 GB a helyileg rögzített kötetek vagy megosztások esetén (a helyi foglalás 10 %-ának hozzáadása a 300 GB-os kiosztott mérethez)

A helyi szinten eddig szükséges teljes terület: 240 GB + 120 GB + 330 GB = 690 GB.

Másodszor, legalább annyi helyre van szükségünk a helyi szinten, mint a legnagyobb egyetlen foglalás. Ez a többletösszeg arra az esetre szolgál, ha egy felhőbeli pillanatképből kell visszaállítania. Ebben a példában a legnagyobb helyi foglalás 330 GB (beleértve a fájlrendszer foglalását is), így ezt hozzá kell adni a 690 GB-hoz: 690 GB + 330 GB = 1020 GB.
Ha további további visszaállításokat hajtottunk végre, bármikor felszabadíthatjuk a helyet az előző visszaállítási műveletből.

Harmadszor, az eddigi teljes helyi terület 15%-ára van szükségünk a helyi pillanatképek tárolásához, hogy csak 85%-a legyen elérhető. Ebben a példában ez körülbelül 1020 GB&ast;= 0,85 kiépített adatlemez TB. Tehát a kiosztott adatlemez (1020&ast;(1/0,85))= 1200 GB = 1,20 TB ~ 1,25 TB (kerekítés a legközelebbi kvartilisre)

Figyelembe véve a váratlan növekedés és az új visszaállítások, ki kell építenie egy helyi lemez körülbelül 1,25-1,5 TB.

> [!NOTE]
> Azt is javasoljuk, hogy a helyi lemez vékonyan kiépített. Ez a javaslat azért van, mert a visszaállítási területre csak akkor van szükség, ha öt napnál régebbi adatokat szeretne visszaállítani. Az elemszintű helyreállítás lehetővé teszi az adatok visszaállítását az elmúlt öt napra anélkül, hogy további helyreállítandó helyre lenne szükség.


#### <a name="example-2"></a>2. példa
A virtuális tömbön meg szeretné tudni, hogy

* 2 TB-os többszintű kötet kiépítése
* 300 GB-os helyileg rögzített kötet kiépítése

A többszintű kötetekre/részvényekre vonatkozó helyi helyfoglalás 12 %-a, a helyileg rögzített kötetek/részvények esetében pedig 10 %-a alapján

* 240 GB-os helyi foglalás (2 TB-os többszintű kötet/megosztás esetén)
* 330 GB a helyileg rögzített kötetek vagy megosztások esetén (a helyi foglalás 10%-ának hozzáadása a 300 GB-os kiosztott területhez)

A helyi szinten szükséges teljes terület: 240 GB + 330 GB = 570 GB

A visszaállításhoz szükséges minimális helyi terület 330 GB.

A teljes lemez 15 %-át a pillanatképek tárolására használják, így csak 0,85 áll rendelkezésre. Tehát a lemez mérete (900&ast;(1/0.85)) = 1,06 TB ~ 1,25 TB (kerekítés a legközelebbi kvartilisre)

Figyelembe véve a váratlan növekedés, kiépíthet egy 1,25-1,5 TB-os helyi lemez.

### <a name="group-policy"></a>Csoportházirend
A csoportházirend olyan infrastruktúra, amely lehetővé teszi a felhasználók és számítógépek adott konfigurációinak megvalósítását. A csoportházirend-beállítások a csoportházirend-objektumokban találhatók, amelyek az Active Directory tartományi szolgáltatások (AD DS) következő tárolóihoz kapcsolódnak: helyek, tartományok vagy szervezeti egységek. 

Ha a virtuális tömb tartományhoz van csatlakoztatva, csoportházirend-fennállású csoportházirend-rendszerek alkalmazhatók rá. Ezek a csoportházirend-rendszerek olyan alkalmazásokat telepíthetnek, mint például egy víruskereső szoftver, amely hátrányosan befolyásolhatja a StorSimple virtuális tömb működését.

Ezért azt javasoljuk, hogy:

* Győződjön meg arról, hogy a virtuális tömb a saját szervezeti egységében (OU) található az Active Directory számára.
* Győződjön meg arról, hogy nincs csoportházirend-objektum alkalmazása a virtuális tömbre. Letilthatja az öröklődést annak érdekében, hogy a virtuális tömb (gyermekcsomópont) ne örökölje automatikusan a csoportházirend-rendszereket a szülőtől. További információért nyissa meg az [öröklődés blokkolása](https://technet.microsoft.com/library/cc731076.aspx)című részét.

### <a name="networking"></a>Hálózat
A virtuális tömb hálózati konfigurációja a helyi webes felhasználói felületen keresztül történik. A virtuális hálózati adapter a hipervizoron keresztül engedélyezett, amelyben a virtuális tömb ki van építve. A [Hálózati beállítások](storsimple-virtual-array-deploy3-fs-setup.md) lapon konfigurálhatja a virtuális hálózati adapter IP-címét, alhálózatát és átjáróját.  Az elsődleges és másodlagos DNS-kiszolgálót, az időbeállításokat és az eszköz választható proxybeállításait is konfigurálhatja. A legtöbb hálózati konfiguráció egyszeri beállítás. Tekintse át a [StorSimple hálózati követelmények](storsimple-ova-system-requirements.md#networking-requirements) telepítése előtt a virtuális tömb.

A virtuális tömb telepítésekor azt javasoljuk, hogy kövesse az alábbi gyakorlati tanácsokat:

* Győződjön meg arról, hogy a hálózat, amelyben a virtuális tömb telepítve van, mindig rendelkezik 5 Mb/s sebességű (vagy több) sávszélesség felszentelésére.
  
  * Az internetes sávszélesség-igény a számítási feladatok jellemzőitől és az adatváltozás mértékétől függ.
  * A kezelhető adatváltozás közvetlenül arányos az internetes sávszélességgel. Például egy biztonsági mentés készítésekor az 5 Mb/s sávszélesség 8 óra alatt körülbelül 18 GB-os adatváltozást képes befogadni. A négyszer nagyobb sávszélesség (20 Mbps) négyszer nagyobb adatváltozást (72 GB) képes kezelni.
* Győződjön meg arról, hogy az internetkapcsolat mindig elérhető. Az eszközök szórványos vagy megbízhatatlan internetkapcsolata a felhőben lévő adatokhoz való hozzáférés elvesztését eredményezheti, és nem támogatott konfigurációt eredményezhet.
* Ha az eszközt iSCSI-kiszolgálóként kívánja telepíteni:
  
  * Javasoljuk, hogy tiltsa le az **IP-cím automatikus beszedése** (DHCP) beállítást.
  * Statikus IP-címek konfigurálása. Elsődleges és másodlagos DNS-kiszolgálót kell konfigurálnia.
  * Ha több hálózati adaptert határoz meg a virtuális tömbön, csak az első hálózati csatoló (alapértelmezés szerint ez az **Ethernet)** érheti el a felhőt. A forgalom típusának szabályozásához több virtuális hálózati adaptert hozhat létre a virtuális tömbön (iSCSI-kiszolgálóként konfigurálva), és ezeket az összeköttetéseket különböző alhálózatokhoz csatlakoztathatja.
* Csak a felhősávszélesség szabályozásához (amelyet a virtuális tömb használ) konfigurálja a szabályozást az útválasztón vagy a tűzfalon. Ha a hipervizorban definiálja a szabályozást, az a felhősávszélesség helyett az összes protokollt , beleértve az iSCSI-t és az SMB-t is.
* Győződjön meg arról, hogy a hipervizorok időszinkronizálása engedélyezve van. Ha Hyper-V-t használ, jelölje ki a virtuális tömböt a Hyper-V Kezelőben, nyissa meg a **Beállítások integrációs &gt; szolgáltatásokat,** és győződjön meg arról, hogy az **időszinkronizálás** be van jelölve.

### <a name="storage-accounts"></a>Tárfiókok
A StorSimple virtuális tömb egyetlen tárfiókkal társítható. Ez a tárfiók lehet egy automatikusan generált tárfiók, egy fiók ugyanabban az előfizetésben, mint a szolgáltatás, vagy egy másik előfizetéshez kapcsolódó tárfiók. További információt a [virtuális tömb tárfiókjainak kezeléséről](storsimple-virtual-array-manage-storage-accounts.md)talál.

A virtuális tömbhöz társított tárfiókokhoz az alábbi javaslatokat használja.

* Ha több virtuális tömböt egyetlen tárfiókkal kapcsol össze, vegye figyelembe a virtuális tömb maximális kapacitását (64 TB) és a tárfiók maximális méretét (500 TB). Ez körülbelül 7-re korlátozza az adott tárfiókhoz társítható teljes méretű virtuális tömbök számát.
* Új tárfiók létrehozásakor
  
  * Azt javasoljuk, hogy hozza létre a régióban legközelebb a távoli iroda/fiókiroda, ahol a StorSimple virtuális tömb telepítve van a késések minimalizálása érdekében.
  * Ne feledje, hogy a tárfiók nem helyezhető át különböző régiókközött. A szolgáltatás nem helyezhető át az előfizetések között is.
  * Használjon olyan tárfiókot, amely redundanciát valósít meg az adatközpontok között. Georedundáns tárolás (GRS), zónaredundáns tárolás (ZRS) és helyileg redundáns tárolás (LRS) mind támogatottak a virtuális tömbhasználata. A különböző típusú tárfiókokról az [Azure storage replikációja](../storage/common/storage-redundancy.md)című webhelyen talál további információt.

### <a name="shares-and-volumes"></a>Részvények és mennyiségek
A StorSimple virtuális tömb esetében akkor hozhat ki megosztásokat, ha fájlkiszolgálóként és kötetként van konfigurálva, ha iSCSI-kiszolgálóként van konfigurálva. A megosztások és kötetek létrehozásának ajánlott eljárása a mérethez és a konfigurált típushoz kapcsolódik.

#### <a name="volumeshare-size"></a>Kötet/megosztás mérete
A virtuális tömbön akkor építhet megosztásokat, ha fájlkiszolgálóként és kötetként van konfigurálva, ha iSCSI-kiszolgálóként van konfigurálva. A megosztások és kötetek létrehozásának ajánlott eljárása a beállított mérethez és típushoz kapcsolódik. 

Ne feledje, hogy a következő gyakorlati tanácsok kiépítésekor megosztások vagy kötetek a virtuális eszközön.

* A többszintű megosztás kiépített méretéhez viszonyított fájlméretek befolyásolhatják a rétegződési teljesítményt. A nagy fájlok kal végzett munka lassú szintnélküli kicsomagolást eredményezhet. Nagyméretű fájlokkal végzett munka esetén azt javasoljuk, hogy a legnagyobb fájl kisebb, mint a megosztási méret 3%-a.
* A virtuális tömbön legfeljebb 16 kötet/megosztás hozható létre. A helyileg rögzített és rétegzett kötetek/megosztások méretkorlátait mindig a [StorSimple virtuális tömb korlátokat.](storsimple-ova-limits.md)
* Kötet létrehozásakor vegye figyelembe a várható adatfelhasználást és a jövőbeli növekedést. A kötet vagy megosztás később nem bontható ki.
* A kötet/megosztás létrehozása után nem csökkentheti a kötet/megosztás méretét a StorSimple-en.
* Amikor a StorSimple rétegzett kötetére ír, amikor a kötetadatok elérnek egy bizonyos küszöbértéket (a kötet számára fenntartott helyi területhez viszonyítva), az I/O-t a rendszer szabályozza. Ha továbbra is erre a kötetre ír, az jelentősen lelassítja az I/O-t. Bár a kiosztott kapacitáson túl is írhat rétegzett kötetre (nem akadályozzuk meg aktívan, hogy a felhasználó a kiosztott kapacitáson túl írjon), megjelenik egy értesítés a túlfizetett hatásról. Miután megjelenik a riasztás, elengedhetetlen, hogy tegyen korrekciós intézkedéseket, például törölje a kötet adatok (kötet bővítés jelenleg nem támogatott).
* A vész-helyreállítási használati esetekben, mivel a megengedett részvények/kötetek száma 16, és a párhuzamosan feldolgozható megosztások/kötetek maximális száma is 16, a megosztások/kötetek száma nincs hatással a RPO-ra és az RPO-kra.

#### <a name="volumeshare-type"></a>Kötet/megosztás típusa
A StorSimple két kötet-/megosztási típust támogat a használat alapján: helyileg rögzített és rétegzett. A helyileg rögzített kötetek/részvények vastagon vannak kiépítve, míg a rétegzett kötetek/részvények vékonyan vannak kiépítve. A helyileg rögzített kötet/megosztás nem konvertálható rétegzett é, vagy *fordítva,* ha létrehozták.

Javasoljuk, hogy a StorSimple kötetek/-megosztások konfigurálásakor az alábbi ajánlott eljárásokat valósítsa meg:

* A kötet típusát a kötet létrehozása előtt üzembe helyezni kívánt munkaterhelések alapján azonosítsa. Helyileg rögzített kötetek használata olyan számítási feladatokhoz, amelyek helyi adatgaranciát igényelnek (még egy felhőkimaradás esetén is), és amelyek alacsony felhőbeli késéseket igényelnek. Miután létrehozott egy kötetet a virtuális tömbön, nem módosíthatja a kötet típusát a helyileg rögzítettről rétegzettre vagy *fordítva.* Például hozzon létre helyileg rögzített köteteket SQL-számítási feladatok vagy virtuális gépeket (virtuális gépeket üzemeltető) számítási feladatok üzembe helyezésekor; rétegzett kötetek használata fájlmegosztási számítási feladatokhoz.


#### <a name="volume-format"></a>Kötet formátuma
Miután létrehozta a StorSimple köteteket az iSCSI-kiszolgálón, inicializálnia, csatlakoztatnia és formáznia kell a köteteket. Ez a művelet a StorSimple-eszközhöz csatlakoztatott gazdagépen hajtható végre. Ajánlott eljárásokat ajánlott a StorSimple gazdagépen lévő kötetek csatlakoztatása és formázása ajánlott.

* Gyors formázás végrehajtása az összes StorSimple köteten.
* StorSimple kötet formázásakor 64 KB-os foglalási egységméretet (AUS) használjon (az alapértelmezett érték 4 KB). A 64 KB-os AUS a gyakori StorSimple-munkaterhelések és egyéb számítási feladatok házon belüli tesztelésén alapul.
* Ha iSCSI-kiszolgálóként konfigurált StorSimple virtuális tömböt használ, ne használjon átnyúló köteteket vagy dinamikus lemezeket, mivel ezeket a köteteket vagy lemezeket a StorSimple nem támogatja.

#### <a name="share-access"></a>Hozzáférés megosztása
Amikor megosztásokat hoz létre a virtuális tömbfájl-kiszolgálón, kövesse az alábbi irányelveket:

* Megosztás létrehozásakor egyetlen felhasználó helyett megosztási rendszergazdaként rendeljen hozzá egy felhasználói csoportot.
* Az NTFS-engedélyeket a megosztás okának létrehozása után kezelheti a megosztások Windows Intézőn keresztüli szerkesztésével.

#### <a name="volume-access"></a>Kötetek elérése
Az iSCSI-kötetek konfigurálásakor a StorSimple virtuális tömb, fontos, hogy szükség esetén szabályozza a hozzáférést. Annak meghatározásához, hogy mely gazdakiszolgálók férhetnek hozzá a kötetekhez, hozzon létre és társítson hozzáférés-vezérlési rekordokat (AKR-eket) a StorSimple-kötetekkel.

A StorSimple-kötetek ACR-einek konfigurálásakor az alábbi ajánlott eljárásokat kell alkalmazni:

* Mindig legalább egy ACR-t társítson egy kötethez.

* Ha egynél több ACR-t rendel egy kötethez, győződjön meg arról, hogy a kötet nem érhető el úgy, hogy egynél több nem fürtözött állomás egyidejűleg is elérhető legyen. Ha egy kötethez több AKR-t rendelt, egy figyelmeztető üzenet jelenik meg, amely a konfiguráció áttekintéséhez szolgál.

### <a name="data-security-and-encryption"></a>Adatbiztonság és titkosítás
A StorSimple virtuális tömb adatbiztonsági és titkosítási funkciókkal rendelkezik, amelyek biztosítják az adatok titkosságát és integritását. A szolgáltatások használatakor ajánlott az alábbi gyakorlati tanácsokat követni: 

* Definiáljon egy felhőalapú tárolási titkosítási kulcsot az AES-256 titkosítás létrehozásához, mielőtt az adatokat a virtuális tömbből a felhőbe küldené. Ez a kulcs nem szükséges, ha az adatok titkosítása kezdődik. A kulcs egy kulcskezelő rendszer, például az [Azure key vault](../key-vault/key-vault-overview.md)használatával generálható és őrizhető.
* A tárfiók konfigurálásakor a StorSimple Manager szolgáltatáson keresztül, győződjön meg arról, hogy engedélyezi a TLS-mód biztonságos csatorna létrehozása a StorSimple-eszköz és a felhő közötti hálózati kommunikáció.
* Rendszeresen hozza létre újra a tárfiókok kulcsait (az Azure Storage-szolgáltatás elérésével), hogy figyelembe vegye a hozzáférés változásait a rendszergazdák módosított listája alapján.
* A virtuális tömb adatait tömöríti és duplikálja az Azure-ba való küldés előtt. Nem javasoljuk a Data Deduplikációs szerepkör-szolgáltatás használatát a Windows Server gazdagépen.

## <a name="operational-best-practices"></a>Ajánlott üzemeltetési eljárások
A működési ajánlott eljárások olyan irányelvek, amelyeket a virtuális tömb napi kezelése vagy működése során kell követni. Ezek a gyakorlatok olyan speciális felügyeleti feladatokat fednek le, mint a biztonsági mentések készítése, a biztonsági másolatkészletből való visszaállítás, a feladatátvétel végrehajtása, a tömb kikapcsolása és törlése, a rendszerhasználatának és állapotának figyelése, valamint a vírusvizsgálatok futtatása a virtuális tömbön.

### <a name="backups"></a>Biztonsági másolatok
A virtuális tömb adatait kétféleképpen szolgálják fel a felhőbe, a teljes eszköz alapértelmezett automatikus napi biztonsági mentését 22:30-kor kezdődően vagy egy manuális igény szerinti biztonsági mentésen keresztül. Alapértelmezés szerint az eszköz automatikusan létrehozza a napi felhőbeli pillanatképeket a rajta lévő összes adatról. További információkért lépjen [biztonsági másolatot a StorSimple virtuális tömbről.](storsimple-virtual-array-backup.md)

Az alapértelmezett biztonsági mentések gyakorisága és megőrzése nem módosítható, de beállíthatja a napi biztonsági mentések napi indításának idejét. Az automatikus biztonsági mentések kezdési idejének konfigurálásakor a következőket javasoljuk:

* Ütemezze a biztonsági mentéseket csúcsidőn kívülre. A biztonsági mentés kezdési időpontja nem egyezshet egybe számos gazdai I/O-val.
* Kezdeményezzen egy manuális igény szerinti biztonsági mentést, amikor eszközfeladat-átvételt tervez végrehajtani, vagy a karbantartási időszak előtt, a virtuális tömb adatainak védelme érdekében.

### <a name="restore"></a>Visszaállítás
A biztonságimásolat-készletből kétféleképpen állítható vissza: visszaállítható egy másik kötetre, vagy megoszthatja vagy elemszintű helyreállítást hajthat végre (csak fájlkiszolgálóként konfigurált virtuális tömbön érhető el). Az elemszintű helyreállítás lehetővé teszi a fájlok és mappák részletes helyreállítását a StorSimple eszköz összes megosztásának felhőalapú biztonsági mentéséből. További információért nyissa meg a [visszaállítást a biztonsági másolatból.](storsimple-virtual-array-clone.md)

A visszaállítás végrehajtásakor tartsa szem előtt az alábbi irányelveket:

* A StorSimple virtuális tömb nem támogatja a helyben történő visszaállítást. Ez azonban könnyen elérhető egy kétlépéses folyamat: hogy helyet a virtuális tömb, majd állítsa vissza egy másik kötet /share.
* Amikor helyi kötetről állít vissza, ne feledje, hogy a visszaállítás hosszú ideig futó művelet lesz. Bár a kötet gyorsan online állapotba kerülhet, az adatok továbbra is hidratáltak a háttérben.
* A kötet típusa ugyanaz marad a visszaállítási folyamat során. A rétegzett kötet egy másik rétegzett kötetre, és egy helyileg rögzített kötet egy másik helyileg rögzített kötetre.
* Ha egy biztonságimásolat-készletből próbál visszaállítani egy kötetet vagy megosztást, ha a visszaállítási feladat sikertelen, a portálon továbbra is létrejöhet egy célkötet vagy megosztás. Fontos, hogy törölje ezt a nem használt célkötetet vagy megosztást a portálon az ebből az elemből eredő jövőbeli problémák minimalizálása érdekében.

### <a name="failover-and-disaster-recovery"></a>Feladatátvétel és vészhelyreállítás
Az eszköz feladatátvétel lehetővé teszi, hogy az adatok áttelepítése az adatközpontban lévő *forráseszközről* egy másik *céleszközre,* amely ugyanazon vagy egy másik földrajzi helyen található. Az eszköz feladatátvételaz egész eszközre. Feladatátvétel során a forráseszköz felhőbeli adatai a céleszköz tulajdonjogát módosítják.

A StorSimple virtuális tömb, csak feladatátvételt egy másik virtuális tömb által kezelt ugyanazon StorSimple Manager szolgáltatás. Egy 8000-es sorozatú eszköz vagy egy másik StorSimple Manager-szolgáltatás által felügyelt tömb (mint a forráseszköz) feladatátvétele nem engedélyezett. Ha többet szeretne megtudni a feladatátvételi szempontokról, olvassa el [az eszköz feladatátvételi előfeltételeit.](storsimple-virtual-array-failover-dr.md)

A virtuális tömb feladatátvétele során tartsa szem előtt a következőket:

* A tervezett feladatátvétel ajánlott az összes kötet/megosztás offline állapotba helyezése a feladatátvétel megkezdése előtt. Kövesse az operációs rendszer-specifikus utasításokat, hogy a kötetek / megosztások offline a gazdagépen először, majd azokat offline a virtuális eszközön.
* Fájlkiszolgálóvész-helyreállítás (DR) esetén azt javasoljuk, hogy csatlakozzon a céleszközhöz ugyanahhoz a tartományhoz, mint a forrás, hogy a megosztási engedélyek automatikusan feloldódjanak. Ebben a kiadásban csak a céleszköz ugyanazon tartományban lévő feladatátvételtámogatott.
* A vész-dr sikeres befejezése után a forráseszköz automatikusan törlődik. Bár az eszköz már nem érhető el, a gazdarendszeren kiépített virtuális gép továbbra is erőforrásokat fogyaszt. Azt javasoljuk, hogy törölje ezt a virtuális gépet a gazdagéprendszerből, hogy megakadályozza a díjak felmerülését.
* Ne feledje, hogy még ha a feladatátvétel sikertelen is, **az adatok mindig biztonságosak a felhőben.** Vegye figyelembe a következő három forgatókönyvet, amelyekben a feladatátvétel nem sikerült:
  
  * Hiba történt a feladatátvétel kezdeti szakaszában, például amikor a VÉSZ-előellenőrzések et hajtottak végre. Ebben a helyzetben a céleszköz továbbra is használható. Megpróbálhatja újra a feladatátvételt ugyanazon a céleszközön.
  * Hiba történt a tényleges feladatátvételi folyamat során. Ebben az esetben a céleszköz használhatatlannak van megjelölve. Ki kell építenie és konfigurálnia kell egy másik célvirtuális tömböt, és ezt kell használnia feladatátvételhez.
  * A feladatátvétel befejeződött, amely után a forráseszköz törölve lett, de a céleszköz problémákat, és nem férhet hozzá semmilyen adatot. Az adatok továbbra is biztonságosak a felhőben, és könnyen lehívhatók egy másik virtuális tömb létrehozásával, majd a VÉSZ-erőforrás céleszközeként való használatával.

### <a name="deactivate"></a>Inaktiválás
A StorSimple virtuális tömb inaktiválásakor megszakítja az eszköz és a megfelelő StorSimple Manager-szolgáltatás közötti kapcsolatot. Az inaktiválás **állandó** művelet, és nem lehet visszavonni. Inaktivált eszköz nem regisztrálható újra a StorSimple Manager szolgáltatással. További információért nyissa meg [a StorSimple virtuális tömb inaktiválását és törlését.](storsimple-virtual-array-deactivate-and-delete-device.md)

A virtuális tömb inaktiválásakor tartsa szem előtt az alábbi gyakorlati tanácsokat:

* A virtuális eszköz inaktiválása előtt vegyen egy felhőbeli pillanatképet az összes adatról. Virtuális tömb inaktiválásakor az összes helyi eszközadat elvész. Felhőbeli pillanatkép készítése lehetővé teszi az adatok helyreállítását egy későbbi szakaszban.
* A StorSimple virtuális tömb inaktiválása előtt győződjön meg arról, hogy leállítja vagy törli az adott eszköztől függő ügyfeleket és állomásokat.
* Törölje az inaktivált eszközt, ha már nem használja, hogy ne halmozzon fel díjakat.

### <a name="monitoring"></a>Figyelés
Annak érdekében, hogy a StorSimple virtuális tömb folyamatos kifogástalan állapotban van, figyelnie kell a tömböt, és biztosítania kell, hogy információkat kapjon a rendszertől, beleértve a riasztásokat is. A virtuális tömb általános állapotának figyeléséhez hajtsa végre a következő ajánlott eljárásokat:

* Állítsa be a figyelést a virtuális tömb adatlemezének és az operációsrendszer-lemez lemezhasználatának nyomon követéséhez. Hyper-V futtatása esetén a System Center Virtual Machine Manager (SCVMM) és a System Center Operations Manager kombinációjával figyelheti a virtualizációs állomásokat.
* Konfigurálja az e-mail értesítéseket a virtuális tömbön, hogy bizonyos használati szinteken értesítéseket küldjön.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>Index kereső- és víruskeresési alkalmazások
A StorSimple virtuális tömb automatikusan réteg adatokat a helyi rétegből a Microsoft Azure-felhőbe. Ha egy alkalmazás, például egy index keresés vagy egy víruskeresés a StorSimple-en tárolt adatok vizsgálatához, ügyelnie kell arra, hogy a felhőadatok ne érjenek el, és nem lesz visszaállítva a helyi szintre.

Javasoljuk, hogy az indexkeresés vagy a víruskeresés konfigurálásakor a következő ajánlott eljárásokat valósítsa meg a virtuális tömbön:

* Tiltsa le az automatikusan konfigurált teljes beési műveleteket.
* Rétegzett kötetek esetén konfigurálja az indexkereső vagy víruskeresési alkalmazást növekményes vizsgálat végrehajtására. Ez csak a helyi szinten valószínűleg tartózkodó új adatokat szidná. A felhőbe rétegzett adatok nem érhetők el egy növekményes művelet során.
* Győződjön meg arról, hogy a megfelelő keresési szűrők és beállítások úgy vannak konfigurálva, hogy csak a kívánt típusú fájlokat vizsgálja meg. A képfájlokat (JPEG, GIF és TIFF) és a mérnöki rajzokat például nem szabad beolvasni a növekményes vagy teljes index-újraépítés során.

Ha Windows indexelési folyamatot használ, kövesse az alábbi irányelveket:

* Ne használja a Windows Indexelő rétegzett kötetek, mert nagy mennyiségű adatot (TB) a felhőből, ha az index et kell újraépíteni gyakran. Az index újraépítése az összes fájltípust lekéri a tartalom indexeléséhez.
* Használja a Windows indexelési folyamat helyileg rögzített kötetek, mivel ez csak a helyi rétegek en lévő adatokhoz való hozzáférés az index létrehozásához (a felhőadatok nem érhetők el).

### <a name="byte-range-locking"></a>Bájttartomány zárolása
Az alkalmazások a fájlokon belül egy meghatározott bájttartományt zárolhatnak. Ha a bájttartomány-zárolás engedélyezve van a StorSimple-be írt alkalmazásokon, akkor a rétegezés nem működik a virtuális tömbön. Ahhoz, hogy a rétegezés működjön, az elért fájlok minden területét fel kell oldani. A bájttartomány-zárolás nem támogatott a virtuális tömb rétegzett köteteivel.

A bájttartomány-zárolás enyhítésére a következő ajánlott intézkedéseket kell tenni:

* Kapcsolja ki a bájttartomány zárolását az alkalmazáslogikában.
* Az alkalmazáshoz társított adatokhoz használjon helyileg rögzített köteteket (rétegzett helyett). A helyileg rögzített kötetek nem rétegezik a felhőbe.
* Ha helyileg rögzített köteteket használ, és a bájttartomány-zárolás engedélyezve van, a kötet még a visszaállítás befejezése előtt online állapotba kerülhet. Ezekben az esetekben meg kell várnia a visszaállítás befejezését.

## <a name="multiple-arrays"></a>Több tömb
Több virtuális tömbök szükség lehet telepíteni, hogy figyelembe vegye a növekvő működő adathalmaz, amely kiömlik a felhőbe, így befolyásolja az eszköz teljesítményét. Ezekben az esetekben a legjobb, ha a munkakészlet növekedésével az eszközök méretezése. Ehhez egy vagy több eszközt kell hozzáadni a helyszíni adatközpontba. Az eszközök hozzáadásakor a következőkre tehet:

* Az aktuális adathalmaz felosztása.
* Új számítási feladatok üzembe helyezése új eszköz(ek)re.
* Ha több virtuális tömböt telepít, azt javasoljuk, hogy a terheléselosztási szempontból ossza el a tömböt a különböző hipervizor gazdagépek között.
* Több virtuális tömb (fájlkiszolgálóként vagy iSCSI-kiszolgálóként konfigurálva) telepíthető elosztott fájlrendszeri névtérben. A részletes lépéseket az [Distributed File System Namespace Solution with Hybrid Cloud Storage Deployment Guide (Elosztott fájlrendszernévtérmegoldás hibrid felhőalapú tárolási útmutatóval) című rész ből találja.](https://www.microsoft.com/download/details.aspx?id=45507) Az elosztott fájlrendszer replikációja jelenleg nem ajánlott a virtuális tömbhöz való használatra. 

## <a name="see-also"></a>Lásd még
Ismerje meg, hogyan [felügyelheti a StorSimple virtuális tömb](storsimple-virtual-array-manager-service-administration.md) a StorSimple Manager szolgáltatáson keresztül.

