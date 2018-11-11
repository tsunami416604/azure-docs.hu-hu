---
title: Gyakorlati tanácsok a StorSimple Virtual Array |} A Microsoft Docs
description: Üzembe helyezése és kezelése a StorSimple Virtual Array az ajánlott eljárásokat ismertetjük.
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
ms.date: 11/08/2018
ms.author: alkohli
ms.openlocfilehash: b8e9f12a549f71971c2da3b9865f6a74dad58f61
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300914"
---
# <a name="storsimple-virtual-array-best-practices"></a>A StorSimple Virtual Array – ajánlott eljárások
## <a name="overview"></a>Áttekintés
A Microsoft Azure StorSimple Virtual Array egy integrált tárolási megoldás, amely egy helyszíni virtuális eszköz, a hipervizor és a Microsoft Azure cloud storage-ban futó közötti tárolási feladatokat kezeli. A StorSimple Virtual Array alternatívája hatékony és költséghatékony megoldást kínál a 8000-es sorozatú fizikai tömbhöz. A virtuális tömb futtathatja a meglévő hipervizor-infrastruktúrát, az iSCSI és az SMB protokollt is támogatja, és távoli office/fiókirodai forgatókönyvek esetén is használható. A StorSimple megoldások további információért látogasson el [Microsoft Azure StorSimple áttekintése](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx).

Ez a cikk ismerteti az ajánlott eljárásokat, a kezdeti beállítás, telepítés és felügyelete a StorSimple Virtual Array során implementálva. Ajánlott eljárások nyújtanak a ellenőrzött a beállítása és kezelése a virtuális tömb. Ez a cikk a rendszergazdáknak, akik üzembe helyezése és kezelése a virtuális tömbök adatközpontjaikban lévő cél.

Azt javasoljuk, hogy egy időszakosan ellenőrizhetjük, hogy az eszköz továbbra is a szabályzatnak a telepítési vagy működési flow végrehajtott módosítások érdekében ajánlott eljárást. Érdemes problémába ütközik a virtuális tömb, ajánlott eljárások végrehajtása közben [forduljon a Microsoft Support](storsimple-virtual-array-log-support-ticket.md) segítségért.

## <a name="configuration-best-practices"></a>Konfigurációs eljárások
Ajánlott eljárások az irányelveket, amelyek nyomon kell követni a kezdeti beállítás és a virtuális tömbök üzembe helyezés során terjed ki. Ajánlott eljárások közé tartoznak a kapcsolódó csoportházirend-beállítások, méretezésének, a hálózatkezelés beállítása, tárfiókok konfigurálásáról és -megosztásokat és -köteteket a virtuális tömb létrehozása a virtuális gép üzembe helyezése. 

### <a name="provisioning"></a>Kiépítés
A StorSimple Virtual Array a hipervizoron (Hyper-V vagy VMware) kiépített egy virtuális gépet (VM) a gazdagép-kiszolgáló. A virtuális gép kiépítésekor győződjön meg arról, hogy az állomás elegendő erőforrást fordítsanak tudni. További információért ugorjon [minimális erőforrás-igényű](storsimple-virtual-array-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements) kiépítéséhez egy tömb.

A virtuális tömb üzembe helyezésekor megvalósítása a következő bevált gyakorlatát:

|  | Hyper-V | VMware |
| --- | --- | --- |
| **Virtuális gép típusa** |**2. generációs** használata a Windows Server 2012 vagy újabb virtuális gép és a egy *.vhdx* kép. <br></br> **1. generációs** való használatra, és a egy Windows Server 2008 vagy újabb virtuális gép és a egy *.vhd* kép. |Használata virtuális gép verziójának használata esetén 8 *.vmdk* kép. |
| **Memória típusa** |Állítsa be **statikus memóriát**. <br></br> Ne használja a **dinamikus memória** lehetőséget. | |
| **Adattípus-lemez** |Mint kiépítése **dinamikusan bővülő**.<br></br> **Rögzített méretű** hosszú időt vesz igénybe. <br></br> Ne használja a **különbséglemez** lehetőséget. |Használja a **kiépítése dinamikusan** lehetőséget. |
| **Adatok lemez módosítása** |Bővítés vagy zsugorítását nem engedélyezett. Ehhez kísérlet az eszközön tárolt összes helyi adat elvesztését eredményezi. |Bővítés vagy zsugorítását nem engedélyezett. Ehhez kísérlet az eszközön tárolt összes helyi adat elvesztését eredményezi. |

### <a name="sizing"></a>Méretezés
A StorSimple Virtual Array osztályozás, a következő tényezőket kell figyelembe venni:

* Helyi foglalás kötetek vagy megosztások. Körülbelül 12 %-a helyet a egyes üzembe helyezett rétegzett kötet vagy megosztás a helyi rétegen van fenntartva. Nagyjából 10 %-a terület is foglalt fájlrendszer egy helyileg rögzített kötet.
* Pillanatkép-terhelés. A pillanatképek nagyjából 15 %-os lemezterületet a helyi rétegen van fenntartva.
* A visszaállítás szükség. Ha új műveletként visszaállítási tesz, méretezési a visszaállításhoz szükséges hely kell figyelni. Visszaállítás egy megosztásra vagy az azonos méretű történik.
* Néhány puffert lefoglalni kívánt bármely váratlan növekedés esetén.

Az előző tényezők alapján, a méretezési követelmények az alábbi képlettel jelölhető:

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`

Az alábbi példák bemutatják, hogyan méretét a virtuális tömb igényei alapján.

#### <a name="example-1"></a>1. példa:
Tudni szeretné a virtuális tömb

* 2 TB rétegzett kötet vagy megosztás kiépítése.
* 1 TB méretű rétegzett kötet vagy megosztás kiépítése.
* üzembe helyezhető egy 300 GB-os helyileg rögzített kötetet vagy megosztást.

Az előző kötetek vagy megosztások ossza meg velünk kiszámítani a lemezterületet, a helyi rétegen kell.

Először minden rétegzett kötetet vagy megosztást, a helyi foglalás lenne a kötetet vagy megosztást méretű 12 %-ával egyenlő. A helyileg rögzített kötetet vagy megosztást, a helyi foglalás (mellett a kiépítési méret) helyileg rögzített kötetet vagy megosztást méretének 10 %-át. Ebben a példában van szüksége

* 240 GB-os helyi foglalás (2 TB rétegzett kötetet vagy megosztást)
* Helyi foglalás 120 GB-os (az 1 TB méretű rétegzett kötetet vagy megosztást)
* 330 GB-os helyileg rögzített kötetet vagy megosztást (300 GB kiosztott méretének 10 %-a helyi foglalás hozzáadása)

A teljes terület szükséges, amennyiben a helyi rétegen van: 240 GB + 120 GB + 330 GB = 690 GB.

A helyi rétegen legalább annyi szabad helyre második, a legnagyobb egyetlen foglalást végrehajtó kell. A felhasznált többletmennyiségért szolgál abban az esetben vissza kell állítania a felhőbeli pillanatképet. Ebben a példában a legnagyobb helyi foglalás 330 GB (beleértve a Foglalás fájlrendszer esetén), akkor úgy szeretné beállítani, hogy a 690 GB: 690 GB + 330 GB = 1020 GB.
Ha azt hajtja végre a következő további visszaállítások, azt is mindig ingyenes fel a lemezterületet az előző visszaállítási művelet.

Harmadik kell 15 %, amennyiben a helyi pillanatképek tárolására szolgáló teljes helyi tárhely, hogy csak 85 %, érhető el. Ebben a példában ez lenne az körül 1020 GB = 0.85&ast;kiépített adatok lemezre TB. Tehát az üzembe helyezett adatlemez lesz (1020&ast;(1/0.85)) 1200 GB = 1,20 TB = ~ 1,25 TB (kerekítésre legközelebbi KVARTILIS)

Amíg nem várt növekedés és az új visszaállítások, körül a helyi lemezen kell kiépítenie 1,25 – 1,5 TB.

> [!NOTE]
> Azt javasoljuk, hogy a helyi lemez vékonyan létesített. Ez a javaslat azért, hogy a visszaállítási helyet csak akkor van szükség, ha a visszaállítandó öt napnál régebbi adatokat. Elemszintű helyreállítás anélkül, hogy a további területet a visszaállításhoz az utolsó öt nap az adatok helyreállítását teszi lehetővé.


#### <a name="example-2"></a>2. példa
Tudni szeretné a virtuális tömb

* 2 TB rétegzett kötetek kiépítéséhez
* 300 GB-os helyileg rögzített kötetet kiépítése

A rétegzett kötetek/megosztások helyi lemezterület-foglalás és a helyileg rögzített kötetek/megosztások 10 % 12 % alapján, kell

* Helyi foglalás 240 GB-os (2 TB rétegzett kötetet vagy megosztást)
* 330 GB-os helyileg rögzített kötetet vagy megosztást (a kiépített 300 GB területre 10 %-a helyi foglalás hozzáadása)

Teljes terület szükséges a helyi rétegen van: 240 GB + 330 GB = 570 GB

A visszaállítási művelethez szükséges minimális helyi terület 330 GB.

a teljes lemezterület 15 % segítségével tárolja a pillanatképeket, így csak 0.85 érhető el. Tehát a lemez mérete (900&ast;(1/0.85)) 1.06 TB = ~ 1,25 TB (kerekítésre legközelebbi KVARTILIS)

Amíg bármilyen váratlan növekedés, telepíthet egy helyi 1,25-1,5 TB-os lemezt.

### <a name="group-policy"></a>Csoportházirend
A csoportházirend olyan infrastruktúra, amely lehetővé teszi, hogy a felhasználók és számítógépek specifikus konfigurációk megvalósításához. Csoportházirend-beállításokat csoportházirend-objektumok (GPO), amelyek kapcsolódnak a következő Active Directory Domain Services (AD DS) tárolókban található: webhelyekhez, tartományokhoz vagy szervezeti egység (OU). 

Ha a virtuális tömb tartományhoz csatlakoztatott, csoportházirend-objektumok alkalmazhatók rá. A csoportházirend-objektumok egy víruskereső szoftverek, amelyek hátrányosan befolyásolhatja a StorSimple Virtual Array működésének például is telepítheti.

Ezért azt javasoljuk, hogy Ön:

* Győződjön meg arról, hogy a virtuális tömb a saját szervezeti egység (OU) az Active Directory.
* Győződjön meg arról, hogy a virtuális tömb nem csoportházirend-objektumok (GPO-k) vannak alkalmazva. Letilthatja az öröklést, győződjön meg arról, hogy a virtuális tömb (gyermek csomópont) nem automatikusan örökli csoportházirend-objektum sem a szülő. További információért ugorjon [öröklődés blokkolása](https://technet.microsoft.com/library/cc731076.aspx).

### <a name="networking"></a>Hálózat
A hálózati konfigurációt a virtuális tömb helyi webes felületén keresztül történik. Egy virtuális hálózati adapteren keresztül, amelyben a virtuális tömb ki van építve a hipervizor engedélyezve van. Használja a [hálózati beállítások](storsimple-virtual-array-deploy3-fs-setup.md) lapon konfigurálhatja a virtuális hálózati adapter IP-cím, alhálózat és átjárót.  Beállíthatja az elsődleges és másodlagos DNS-kiszolgáló beállításai és nem kötelező proxybeállításainak az eszközhöz. A hálózati konfiguráció a legtöbb egy egyszeri beállítás. Tekintse át a [hálózati követelményeiben StorSimple](storsimple-ova-system-requirements.md#networking-requirements) a virtuális tömb üzembe helyezése előtt.

A virtuális tömb, javasoljuk, hogy kövesse az ajánlott eljárások:

* Győződjön meg arról, hogy a hálózat, amely a virtuális tömb üzembe lesz helyezve mindig rendelkezik-e a kapacitást 5 MB/s internetes sávszélességet (vagy még több) rendelnie.
  
  * Internetes sávszélesség szükséges a számítási feladatok jellemzői, és az adatcsere sebességétől függően változik.
  * Az adatmódosítás feldolgozható arányos az internetes sávszélességet. Például ha a biztonsági másolat 5 MB/s sávszélességű 8 órás időtartamra letilthatja az adatok körülbelül 18 GB módosítását képes kezelni. Négyszer nagyobb sávszélességet (20 MB/s) négy-szer több adat módosítása (72 GB) képes kezelni.
* Internetkapcsolat mindig elérhető legyen. Az eszközök internetes kapcsolatokat szórványos vagy nem megbízható hálózathatáron adataihoz a felhőben hozzáférés adatvesztést eredményezhet, és hatására konfigurációja nem támogatott.
* Ha azt tervezi, hogy az eszköz egy iSCSI-kiszolgálóként üzembe helyezése:
  
  * Azt javasoljuk, hogy tiltsa le a **IP-cím automatikus beszerzése** (DHCP) lehetőséget.
  * Statikus IP-címek konfigurálásához. Konfigurálnia kell egy elsődleges és másodlagos DNS-kiszolgáló.
  * Ha több hálózati adapterrel határozza meg a virtuális tömböt, csak az első hálózati adapter (alapértelmezés szerint ez az interfész a **Ethernet**) elérheti a felhőben. Típusú forgalom szabályozására, több virtuális hálózati adapterek létrehozása a virtuális tömb (iSCSI-kiszolgálóként konfigurált), és különböző alhálózatokon felületeken csatlakozni.
* Csak felhőalapú sávszélesség szabályozása (amelyet a virtuális tömb), az útválasztó vagy a tűzfal-szabályozás konfigurálása. Ha megadja, hogy a hipervizor a szabályozás, többek között az iSCSI és SMB helyett csak a felhőben sávszélesség minden protokoll fog szabályozás.
* Győződjön meg arról, hogy az idő szinkronizálása, a hipervizorok engedélyezve van. Ha a Hyper-V, válassza ki azt a virtuális tömb a Hyper-V kezelőjében, lépjen a **beállítások &gt; integrációs szolgáltatások**, és ellenőrizze, hogy a **időszinkronizálás** be van jelölve.

### <a name="storage-accounts"></a>Tárfiókok
A StorSimple Virtual Array társítható egy tárfiókban. Ez a tárfiók egy automatikusan létrehozott storage-fiókot, egy fiókot a szolgáltatás ugyanabban az előfizetésben vagy egy másik előfizetéshez kapcsolódó tárfiók. További információkért lásd: hogyan [kezelése a storage-fiókot a virtuális tömb](storsimple-virtual-array-manage-storage-accounts.md).

A következő javaslatokat használja a virtuális tömb társított storage-fiókok.

* Több virtuális tömböket, egyetlen tárfiókban való csatoláskor figyelembe vennie a maximális kapacitás (64 TB) egy virtuális tömböt és egy tárfiók maximális méretét (500 TB). Ez a teljes méretű virtuális tömböket, amely a tárfiók, és körülbelül 7 társítható számának korlátozása.
* Amikor egy új tárfiók létrehozása
  
  * Azt javasoljuk, hogy létrehozta a régióban legközelebb a távoli office/fiókiroda késések minimalizálása érdekében a StorSimple Virtual Array telepítési helyét.
  * Nem lehet áthelyezni egy storage-fiók különböző régiók között figyelembe kell vennie. Több előfizetés is nem helyezhetők át egy szolgáltatás.
  * Storage-fiók, amely megvalósítja az adatközpontok közötti redundancia használja. Georedundáns tárolás (GRS), a Zónaredundáns Társzolgáltatási (ZRS) és a helyileg redundáns tárolás (LRS) támogatja a virtuális tömb való használatra. A különböző típusú storage-fiókokról további információért látogasson el [az Azure storage replikáció](../storage/common/storage-redundancy.md).

### <a name="shares-and-volumes"></a>Megosztások és kötetek
A StorSimple Virtual Array telepíthet a megosztásokat, amikor egy fájlkiszolgáló és köteteket. Ha a konfigurált egy iSCSI-kiszolgálóként van konfigurálva. A megosztások és kötetek létrehozásának ajánlott eljárásai kapcsolódó mérete és a konfigurált típusát.

#### <a name="volumeshare-size"></a>Kötetet vagy megosztást mérete
A virtuális tömb, ha van konfigurálva, a fájlkiszolgáló és köteteket. Ha az iSCSI-kiszolgálóként konfigurált építhet ki az megosztások. A megosztások és kötetek létrehozására vonatkozó ajánlott eljárások a méretét és a konfigurált típus vonatkoznak. 

Vegye figyelembe a következő gyakorlati tanácsok megosztások vagy kötetek a virtuális eszköz üzembe helyezésekor.

* A fájlméret viszonyított rétegzett megosztás kiosztott méretét hatással lehet a rétegzési teljesítmény. Nagy fájlok használata egy lassú réteg felskálázása eredményezhet. Ha nagy méretű fájlok dolgozik, azt javasoljuk, hogy a megosztás méretének % 3-nál kisebb-e a legnagyobb fájlt.
* Legfeljebb 16 kötetek/megosztások a virtuális tömb hozható létre. A helyileg rögzített és a rétegzett kötetek/megosztások méretkorlátai, mindig tekintse meg a [StorSimple virtuális tömb – korlátozások](storsimple-ova-limits.md).
* Egy kötet, a várt adathasználat, valamint a későbbi növekedési tényező létrehozásakor. A kötet nem bonthatók ki később.
* A kötet létrehozása után a StorSimple a kötet mérete nem zsugorítható.
* Való írás esetén a rétegzett kötetek a StorSimple, ha a kötet adatait elér egy bizonyos küszöbértéket (képest a helyi terület, a kötet számára fenntartva), az IO folyamatban van. A kötet írni a Folytatás lelassítja az i/o jelentősen. Bár a kiosztott kapacitás (azt ne aktívan állítsa le a felhasználónak a meghaladja a kiosztott kapacitás írása) meghaladja a rétegzett kötetek is ír, riasztási értesítés arról, hogy rendelkezik oversubscribed láthatja. Ha már látja a riasztást, amihez elengedhetetlen, tegye meg a javító intézkedések, például a Törlés a kötet adatait (kötet bővítése jelenleg nem támogatott).
* A vész-helyreállítási használati esetek, a megengedett megosztások vagy kötetek száma 16, és a párhuzamosan feldolgozható megosztások vagy kötetek maximális számát is 16, megosztások vagy kötetek száma nincs hatással az RPO és RTO-k.

#### <a name="volumeshare-type"></a>Kötetet vagy megosztást típusa
A StorSimple támogatja a használat alapján két kötetet vagy megosztást típusa: helyileg rögzített és a rétegzett. A gyors helyi kötetek/megosztások nem dinamikus kiosztásúak, mivel a rétegzett kötetek/megosztások kiosztása. Nem alakítható át a helyileg rögzített kötetet vagy megosztást a rétegzett vagy *fordítva* egyszer létrehozni.

Javasoljuk, hogy a következő gyakorlati tanácsok valósítható meg a StorSimple-kötetek/megosztások konfigurálásakor:

* Azonosítsa a kötettípus alapján szeretné telepíteni, mielőtt egy kötetet hoz létre a számítási feladatokhoz. Használja a gyors helyi kötetek (akár egy felhőbeli kimaradás) során az adatok helyi garanciákat, amely esetében, és felhőalapú alacsony késések igénylő számítási feladatokhoz. Miután a virtuális tömb kötetet hoz létre, a kötet típusa nem módosítható a helyileg rögzített a rétegzett vagy *fordítva*. A gyors helyi kötetek például létrehozása, ha SQL számítási feladatok vagy a virtuális gépeket (VM); számítási feladatok üzembe helyezése használja a rétegzett kötetek fájl megosztási számítási feladatokhoz.


#### <a name="volume-format"></a>Kötet formátum
Miután az iSCSI-kiszolgálón létrehozott StorSimple-köteteket, kell inicializálni, csatlakoztathatja és formázza a köteteket. Ez a művelet a StorSimple-eszköz csatlakozik, a gazdagépen történik. Következő bevált gyakorlatát használata akkor javasolt, ha csatlakoztatja, és a StorSimple gazdagépen kötetek formázását.

* A StorSimple-kötetek hajtson végre egy gyorsformázást.
* Ha StorSimple-kötet formázása, használja a lemezfoglalási egység méretét (AU-k) 64 KB-os (az alapértelmezett érték 4 KB-os). A 64 KB-os au-k alapján közös StorSimple számítási feladatokhoz és egyéb számítási feladatokat a belső fejlesztésű végzett tesztelése.
* A StorSimple Virtual Array egy iSCSI-kiszolgálóként konfigurált használatakor ne használjon átnyúló kötetek vagy dinamikus lemezek, ezeket a köteteket, lemezek használata nem támogatott a StorSimple.

#### <a name="share-access"></a>Megosztás eléréséhez
Ha a virtuális tömb fájlkiszolgálón megosztásokat hoz létre, kövesse az alábbi irányelveket:

* Megosztás létrehozásakor rendelje hozzá a felhasználói csoport egy adott felhasználó helyett megosztás rendszergazdaként.
* A Windows Exploreren keresztül megosztások szerkesztésével a megosztás létrehozása után kezelheti az NTFS-engedélyeket.

#### <a name="volume-access"></a>Kötet-hozzáférés
A StorSimple Virtual Array az iSCSI-kötet konfigurálásakor fontos hozzáférés vezérléséhez, bárhol is szükséges. Annak megállapításához, hogy melyik gazdagépre kiszolgálók hozzáférhetnének a köteteket, létrehozása és hozzáférés-vezérlési rekordok (ACR-EK) társítani a StorSimple-köteteket.

ACR-EK konfigurálása a StorSimple-kötetek esetén használja a következő bevált gyakorlatát:

* Legalább egy ACR mindig társítani egy kötetet.

* Ha egynél több ACR hozzárendelése egy köteten, győződjön meg arról, hogy a kötet nem érhető el a oly módon, ahol egyidejűleg hozzáférhetők egynél több nem fürtözött gazdagép. Ha több ACR-EK hozzárendelték egy olyan kötetre, figyelmeztető üzenetet kap, miszerint az áttekintheti a konfigurációt.

### <a name="data-security-and-encryption"></a>Adatbiztonság és titkosítás
A StorSimple Virtual Array adatok biztonság és titkosítás funkcióval rendelkezik, amelyeket a titkosítás és az adatok sértetlenségének biztosítása érdekében. Ha használja ezeket a funkciókat, javasoljuk, hogy kövesse az ajánlott eljárások: 

* Adja meg a felhőalapú tárolás titkosítási kulcsa létrehozni az AES-256 titkosítással, az adatokat a felhőbe, a virtuális tömb elküldése előtt. Ez a kulcs nincs szükség, ha először az adatait titkosítjuk. A kulcs is létrehozható és biztonságáról, mint a kulcskezelés rendszert használ [az Azure key vault](../key-vault/key-vault-whatis.md).
* A tárfiók keresztül a StorSimple Manager szolgáltatás konfigurálásakor győződjön meg arról, hogy engedélyezi-e a StorSimple-eszköz és a felhő közötti hálózati kommunikációhoz biztonságos csatornát létrehozni az SSL-módot.
* A kulcsok újragenerálása a storage-fiókok (az Azure Storage szolgáltatás elérésével) rendszeres időközönként módosítania kellene hozzáférni a fiókhoz a rendszergazdák módosított listája alapján.
* A virtuális tömb lévő adatok tömörítve és deduplikált az Azure-bA továbbítás előtt. Nem ajánlott, az Adatdeduplikáció szerepkör-szolgáltatás használata a Windows Server-gazdagépen.

## <a name="operational-best-practices"></a>Gyakorlati tanácsok az üzemeltetéshez
A gyakorlati tanácsok az üzemeltetéshez olyan irányelvek, a napi szintű felügyeleti vagy a virtuális tömb során betartandó. Ezek az eljárások vonatkoznak, adott felügyeleti feladatokhoz, például a biztonsági másolatok készítése, helyreállítása biztonságimásolat-készlet, a feladatátvétel végrehajtásához, inaktiválása és törlése a tömbben, a rendszer használatának és állapotának figyelése, és futtatja a vírus megvizsgálja a virtuális tömb.

### <a name="backups"></a>Biztonsági másolatok
A virtuális tömb tárolt biztonsági másolatot a két módon a felhőben, egy alapértelmezett automatikus napi biztonsági mentés, a teljes eszköz 22:30, vagy manuális igény szerinti biztonsági mentés indítása. Alapértelmezés szerint az eszköz a rajta található valamennyi adat napi felhőbeli pillanatképek automatikusan létrehozza. További információért ugorjon [biztonsági mentése a StorSimple Virtual Array](storsimple-virtual-array-backup.md).

A gyakoriság és a társított alapértelmezett biztonsági másolatok megőrzése nem módosítható, de beállíthatja, hogy az idő, amikor a napi biztonsági mentések minden nap kezdeményezett. Ha konfigurálja az automatikus biztonsági mentés kezdési ideje, azt javasoljuk, hogy:

* A biztonsági mentéseket csúcsidőn ütemezése. Biztonsági mentés kezdő időpontja nem egybe kell számos gazdagép IO.
* Ha egy eszköz feladatátvételi vagy a virtuális tömb az adatok védelme érdekében a karbantartási időszak előtt végrehajtandó tervezési kezdeményezheti a manuális igény szerinti biztonsági mentés.

### <a name="restore"></a>Visszaállítás
Visszaállíthatja egy biztonságimentés-készlet két módon: állítsa vissza egy másik kötetet vagy megosztást vagy egy elemszintű helyreállítás (csak a virtuális tömb fájlkiszolgálóként konfigurált elérhető). Elemszintű helyreállítás lehetővé teszi, hogy a fájlok és mappák részletes helyreállítási felhő biztonsági másolatából a StorSimple eszközön a megosztásokat. További információért ugorjon [visszaállítása biztonsági másolatból](storsimple-virtual-array-clone.md).

A visszaállítás végrehajtásakor tartsa szem előtt az alábbi irányelveket:

* A StorSimple virtuális tömb nem támogatja a helyben visszaállítása. Ez azonban könnyen lehet megvalósítani egy kétlépéses folyamat: Szabadítson fel helyet a virtuális tömböt, és állítsa vissza egy másik kötetre/megosztásra.
* Tartsa szem előtt, helyi kötetté történő visszaállítását a visszaállítást egy hosszú ideig futó művelet lesz. Bár előfordulhat, hogy a kötet gyorsan online állapotba, az adatok továbbra is a háttérben hidratált lehet.
* A kötettípus változatlan marad, a visszaállítási folyamat során. Egy másik rétegzett kötet a rétegzett kötetek visszaállítja, és a egy helyileg rögzített kötet a másikra helyileg rögzített kötet.
* Próbálja visszaállítani egy kötetet vagy megosztást egy biztonsági másolatból beállítása, a visszaállítási feladat sikertelen lesz, ha a célkötet, és megosztása a portálon is létrejön. Fontos, hogy törli a fel nem használt célkötetet, vagy megosztása a portálon az elemből származó kapcsolatos jövőbeli problémák minimalizálása érdekében.

### <a name="failover-and-disaster-recovery"></a>Feladatátvétel és vészhelyreállítás
Egy eszköz feladatátvételi lehetővé teszi, hogy át az adatokat egy *forrás* a másikra az adatközpontban található eszköz *cél* azonos, vagy egy másik földrajzi helyen található eszköz. Az eszköz feladatátvételi van a teljes eszköz számára. A feladatátvétel során a felhőbeli adatok az eszköz tulajdonjogát módosítja a céleszközön a.

A StorSimple Virtual Array az is csak átadja a feladatokat az ugyanazon a StorSimple Manager szolgáltatás által kezelt egy másik virtuális tömbbe. A 8000-es sorozatú eszköz vagy egy (mint a forrás eszköz) egy másik a StorSimple Manager szolgáltatás által kezelt feladatátvételt nem engedélyezett. További információ a feladatátvételi szempontokat részletező cikkben, lépjen a [az eszköz feladatátvételi előfeltételei](storsimple-virtual-array-failover-dr.md).

Feladatátvétel végrehajtásához keresztül a virtuális tömb, vegye figyelembe a következőket:

* Egy tervezett feladatátvétel esetén a kötetek/megosztások kapcsolat nélküli módban a feladatátvétel kezdeményezése előtt elvégzendő ajánlott. Kövesse az operációsrendszer-specifikus utasításokat a kötetek/megosztások offline állapotba, ha a gazdagép a első és a megfelelő azok offline állapotban van a virtuális eszközön.
* Egy fájl server vészhelyreállítási (DR) javasoljuk, hogy csatlakozzon a céleszköz ugyanahhoz a tartományhoz, mint a forrás-, hogy a megosztási engedélyek automatikusan feloldani. Csak a feladatátvételt, hogy ugyanabban a tartományban egy céleszköz ebben a kiadásban támogatott.
* Ha a DR sikeresen befejeződött, a rendszer automatikusan törli a forráseszközt. Ha az eszköz már nem érhető el, a rendszer a gazdagép kiépített virtuális gépet továbbra is fogyassza az erőforrásokat. Azt javasoljuk, hogy a gazdagép rendszerről megakadályozza, hogy a díjakat halmoz fel a virtuális gép törlése.
* Vegye figyelembe, hogy akkor is, ha a feladatátvételi művelet sikertelen, **adatai mindig biztonságban a felhőben**. A következő három forgatókönyv, amelyben a feladatátvétel nem fejeződött be sikeresen, vegye figyelembe:
  
  * Hiba történt a kezdeti fázisában – például amikor a DR előzetes ellenőrzések végrehajtása a feladatátvételt. Ebben a helyzetben a céleszközön továbbra is használható. Újra ugyanazon a céleszközön a feladatátvétel.
  * Hiba történt a tényleges feladatátvétel során. A céleszköz ebben az esetben használhatatlan van megjelölve. Üzembe helyezése és konfigurálása egy másik céloldali virtuális tömb és kell használnia, amely feladatátvételhez.
  * A feladatátvétel befejeződött, ami a forráseszközt törölve lett a következő volt, de a céleszköz problémákkal rendelkezik, és semmilyen adatot nem férhet hozzá. Az adatok biztonságos-e továbbra is a felhőben, és egy másik virtuális tömbbe létrehozásával, és majd használni a céleszközön a vészhelyreállítás egyszerűen lekérhető.

### <a name="deactivate"></a>Inaktiválás
A StorSimple Virtual Array inaktiválása, akkor az eszköz és a megfelelő a StorSimple Manager szolgáltatás közötti kapcsolat kiszolgálófejléc. Inaktiválási van egy **állandó** műveletet, és nem vonható vissza. Inaktivált eszköz nem lehet regisztrálni a StorSimple Manager szolgáltatással újra. További információért ugorjon [inaktiválása és törlése a StorSimple Virtual Array](storsimple-virtual-array-deactivate-and-delete-device.md).

Ha a virtuális tömb inaktiválása tartsa szem előtt az alábbi ajánlott eljárásokat:

* A virtuális eszköz inaktiválása előtt az adatok felhőbeli pillanatkép készítése. A virtuális tömb inaktiválása, minden a helyi eszközön adat elvész. Felhőbeli pillanatkép lehetővé teszi, hogy az adatok helyreállítása egy későbbi időpontban.
* A StorSimple Virtual Array, inaktiválása előtt ügyeljen arra, hogy leállította vagy törölte az ügyfelek és a gazdagépek, amelyek attól függnek, hogy az eszközről.
* Ha Ön már nem használ, hogy azt nem díjköteles előfizetéstípusok inaktivált eszköz törlése

### <a name="monitoring"></a>Figyelés
Győződjön meg arról, hogy a StorSimple Virtual Array egy folyamatos kifogástalan állapotban van, szüksége figyelheti a tömbben, és győződjön meg arról, hogy információkat kap a rendszerből, beleértve a riasztásokat. A virtuális tömb általános állapotának monitorozásához megvalósításához az alábbi gyakorlati tanácsokat:

* A virtuális tömb adatlemez, valamint az operációsrendszer-lemez lemez használatának nyomon követése a figyelés konfigurálása. Ha Hyper-V fut, a System Center Virtual Machine Manager (SCVMM) és a System Center Operations Manager együttes használatával a virtualizálási gazdagépek figyeléséhez.
* E-mail-értesítések konfigurálása a virtuális tömb értesítések küldése megadott használati szinten.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>A keresési index és a vírus alkalmazások vizsgálata
A StorSimple Virtual Array is rétegzi automatikusan az adatokat a helyi szintről a Microsoft Azure felhőbe. Egy alkalmazás, például az indexben való keresés vagy a víruskeresés során megvizsgálja a StorSimple-on tárolt adatok használata esetén körültekintően járjon el, hogy a felhőbeli adatok nem érhető el és lekért vissza a helyi szintre kell.

Javasoljuk, hogy a következő gyakorlati tanácsok valósítható meg a virtuális tömb a keresési vagy virus indexvizsgálatot konfigurálásakor:

* Tiltsa le az automatikusan beállított teljes vizsgálat műveleteket.
* A rétegzett kötetek vagy állítson be az index keresési vírus vizsgálat növekményes vizsgálat. Ez szeretné ellenőrizni, hogy csak az új adatok valószínűleg elhelyezkedhet a helyi rétegen. Az adatok a felhőben van rétegzett növekményes művelet során nem érhető el.
* Győződjön meg arról, a helyes keresési szűrők és beállítások vannak konfigurálva, hogy csak a megfelelő típusú fájlok vizsgálata beolvasása. Például képfájlok (JPEG, GIF és TIFF) és mérnöki rajzok nem kell futtatni a növekményes és teljes indexkészítés során.

Indexelési folyamat Windows használata esetén kövesse az alábbi irányelveket:

* Ne használja a Windows-indexelő rétegzett köteteket, azt visszaírja a nagy mennyiségű adatot (TB-osra bővül) a felhőben, ha az index újraépítését, gyakran kell. Az index újraépítése szeretné beolvasni az index tartalmukra minden fájltípus.
* Használja a Windows-folyamat a helyileg rögzített kötetekhez indexelő, mivel ez csak a helyi rétegeken hozhat létre az indexet (felhőalapú adatokat nem fogják elérni) adatok elérésére.

### <a name="byte-range-locking"></a>Bájt tartomány zárolása
Alkalmazások bájt megadott számos zárolhatja a fájlokon belüli. Ha bájt tartomány zárolás engedélyezve van az alkalmazásokat, amelyek a StorSimple ír, majd rétegezést nem működik a virtuális tömb. A rétegezést működéséhez az elért fájlokat minden területéhez oldják a zárolást kell lennie. Bájt tartomány zárolását a rétegzett köteteket a virtuális tömb nem támogatott.

Ajánlott orvoslására bájt tartomány zárolását a következők:

* Kapcsolja ki az alkalmazáslogika a zárolás bájttartományt.
* Használjon helyi rögzített kötetekről (helyett a Szintezett) a jelen alkalmazáshoz rendelt adatok. A gyors helyi kötetek nem adatszint a felhőbe.
* Használatával a helyileg rögzített kötetekről bájt tartomány zárolás engedélyezve van, ha a kötet is parancsára a visszaállítás befejezése előtt. Ezek a példányok kell Várjon, amíg befejeződik a visszaállítást.

## <a name="multiple-arrays"></a>Több tömbök
Több virtuális Tárolótömböket kell fiókot működő növekvő halmazát, amelyek sikerült kerülnek a felhőbe, amelyek így hatással lesznek a teljesítmény, az eszköz üzembe helyezhető. Ezekben az esetekben érdemes skálázási eszközökre munkakészletének növekedésével. Ehhez a helyi adatközpontban hozzáadandó egy vagy több eszközt. Az eszközök hozzáadásakor, a következőket teheti:

* Ossza fel az adatok aktuális készletét.
* Az új eszköz(ök) új számítási feladatok üzembe helyezéséhez.
* Ha üzembe több virtuális tömböket, azt javasoljuk, hogy a terheléselosztó szempontjából, a tömb szét különböző hipervizor-állomások.
* Több virtuális tömb (Ha be van állítva, a fájlkiszolgáló vagy iSCSI-kiszolgáló) a Distributed File System Namespace is telepíthető. Lépjen a részletes lépéseket [elosztott File System Namespace megoldás a hibrid felhőalapú tárolási üzembe helyezési útmutató](https://www.microsoft.com/download/details.aspx?id=45507). Az elosztott fájlrendszer-replikáció jelenleg nem ajánlott a virtuális tömb való használatra. 

## <a name="see-also"></a>Lásd még
Ismerje meg, hogyan [felügyelete a StorSimple Virtual Array](storsimple-virtual-array-manager-service-administration.md) keresztül a StorSimple Manager szolgáltatásban.

