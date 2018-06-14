---
title: Ajánlott eljárások a StorSimple virtuális tömb |} Microsoft Docs
description: A központi telepítésére és felügyeletére a StorSimple virtuális tömb bevált gyakorlat ismertetése.
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
ms.date: 03/16/2018
ms.author: alkohli
ms.openlocfilehash: 46fd818d8ca15515c91bb6e65e99b0a3bc1f1fa4
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/17/2018
ms.locfileid: "29972840"
---
# <a name="storsimple-virtual-array-best-practices"></a>A StorSimple virtuális tömb gyakorlati tanácsok
## <a name="overview"></a>Áttekintés
Microsoft Azure StorSimple virtuális tömbjének értéke egy integrált tárolási megoldás, amely kezeli a tárolási feladatok elvégzéséről egy Microsoft Azure felhőalapú tárolást és hipervizor futtató helyszíni virtuális eszköz között. A StorSimple virtuális tömbjének értéke a 8000 sorozat fizikai tömb hatékony, költséghatékony helyett. A virtuális tömb futtathatja a meglévő hipervizor infrastruktúra, az iSCSI és az SMB protokollt is támogatja, és jól használható távoli office/fiókirodák számára. További információ a StorSimple-megoldásokról, Ugrás [Microsoft Azure StorSimple áttekintése](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx).

Ez a cikk ismerteti a gyakorlati tanácsok a kezdeti beállítás, telepítés és a StorSimple virtuális tömb felügyeleti során végrehajtott. Az alábbi gyakorlati tanácsok érvényesített irányelvek a beállítása és kezelése a virtuális tömbjét adja meg. Ez a cikk az informatikai rendszergazdák telepíthetnek és kezelhetnek az adatközpontjaikban virtuális tömbök cél.

Azt javasoljuk ajánlott eljárás végrehajtását, annak biztosítására, hogy az eszköz továbbra is a megfelelőségi akkor, ha a telepítési vagy működési folyamat végrehajtott módosítások időszakosan ellenőrizhetjük. Kell problémába ütközik közben hajt végre az alábbi gyakorlati tanácsok a virtuális tömb a [forduljon a Microsoft Support](storsimple-virtual-array-log-support-ticket.md) segítségért.

## <a name="configuration-best-practices"></a>Technológiának az ajánlott eljárásokkal
Az alábbi gyakorlati tanácsok a kezdeti beállítás és a virtuális tömb központi telepítése során nyomon kell követni útmutatásokat foglalkozik. Az alábbi gyakorlati tanácsok közé tartoznak az kapcsolódik a virtuális gépet, a csoportházirend-beállítások, méretezése, az a hálózat beállítása, a storage-fiókok konfigurálása és a megosztások és a virtuális tömb kötetek létrehozása kiépítését. 

### <a name="provisioning"></a>Kiépítés
A StorSimple virtuális tömb annak a kiszolgálónak egy virtuális gép (VM), a hipervizor (Hyper-V vagy VMware) regisztrálva. Ha a virtuális gépet, győződjön meg arról, hogy a gazdagép képes elegendő erőforrást fordítsanak. További információkért látogasson el [minimális követelményeinek](storsimple-virtual-array-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements) tömb kiépítéséhez.

A virtuális tömb létesítésekor, valósítja meg a következő gyakorlati tanácsok:

|  | Hyper-V | VMware |
| --- | --- | --- |
| **Virtuális gép típusa** |**2. generációs** használata Windows Server 2012 vagy újabb virtuális gép és egy *.vhdx* kép. <br></br> **1. generációs** használata a Windows Server 2008 vagy újabb virtuális gép és egy *.vhd* kép. |Használjon virtuális gép verziójának használatakor 8 *.vmdk* kép. |
| **Memória típusa** |Beállítása **statikus memória**. <br></br> Ne használja a **dinamikus memória** lehetőséget. | |
| **Adattípus-lemez** |Mint kiépítése **dinamikusan bővülő**.<br></br> **Rögzített méretű** hosszú időt vesz igénybe. <br></br> Ne használja a **különbséglemezek** lehetőséget. |Használja a **rendelkezés dinamikusan** lehetőséget. |
| **Adatok lemez módosítása** |Bővítése vagy zsugorítását nem engedélyezett. Ehhez kísérlet az eszköz összes a helyi adatok elvesztését eredményezi. |Bővítése vagy zsugorítását nem engedélyezett. Ehhez kísérlet az eszköz összes a helyi adatok elvesztését eredményezi. |

### <a name="sizing"></a>Méretezése
A StorSimple virtuális tömb osztályozás, a következő tényezőket kell figyelembe venni:

* Kötetek vagy megosztások helyi foglalás. Körülbelül 12 % a terület az egyes kiosztott rétegzett kötetet vagy megosztást a helyi rétegen van fenntartva. Nagyjából 10 % a terület is számára van fenntartva a fájlrendszer egy helyileg rögzített kötet.
* Pillanatkép-terhelés. A pillanatképek nagyjából hely 15 %-ban a helyi rétegen van fenntartva.
* Visszaállítások szükségességét. Ha új műveletként visszaállítás történt, méretezési a visszaállítási művelethez szükséges a tárhely kell fiók. Visszaállítás egy fájlmegosztás vagy kötet azonos méretűnek történik.
* Néhány puffer váratlan növekedésének a kell kiosztani.

A fenti tényezők alapján, a méretezési követelmények az alábbi képlettel ábrázolhatók:

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`

A következő példák bemutatják, hogyan méretét egy virtuális tömb a követelmények alapján.

#### <a name="example-1"></a>1. példa:
A virtuális tömb szeretné tudni

* kiépítése 2 TB rétegzett kötetet vagy megosztást.
* egy 1 TB-os kiépítése rétegzett kötetet vagy megosztást.
* helyileg rögzített kötet 300 GB-os kiépítése, vagy a megosztási.

Az előző kötetek vagy megosztások ossza meg velünk a helyi rétegen a lemezterület kiszámításához.

Első lépésként minden rétegzett kötet vagy megosztás, helyi lefoglalás lenne egyenlő a kötet, illetve megosztási mérete % 12. A helyileg rögzített kötetet vagy megosztást helyi lefoglalás esetén 10 %-a (mellett a kiépített méret) helyileg rögzített kötet, illetve megosztási méretét. Ebben a példában van szüksége

* 240 GB helyi lefoglalás (2 TB rétegzett kötet vagy megosztás)
* 120 GB helyi lefoglalás (1 TB rétegzett kötet vagy megosztás)
* A helyileg rögzített kötetet vagy megosztást (hozzáadása helyi lefoglalás 10 % a kiépített 300 GB-os méret) 330 GB

A teljes lemezterület szükséges a helyi rétegen eddig: 240 GB + 120 GB + 330 GB = 690 GB.

Legalább annyi szabad a helyi rétegen helyre, mint a legnagyobb egyetlen foglalás kell. Ezt a felesleges mennyiséget használják, ha vissza kell állítania egy felhőalapú pillanatképből. Ebben a példában a legnagyobb helyi foglalás 330 GB (beleértve a fájlrendszer foglalási), így szeretné beállítani, hogy a 690 GB: 690 GB + 330 GB = 1020 GB.
Ha azt hajtja végre a következő további visszaállítások, azt is mindig szabadítson fel helyet az előző visszaállítási műveletről.

Harmadik kell 15 %-a a teljes helyi hely eddig helyi pillanatképek, így csak 85 %-a, nem érhető el. Ebben a példában, amely körüli lenne 1020 GB = 0.85&ast;kiosztott adatok lemezre TB. Igen, a kiépített adatlemez lenne (1020&ast;(1/0.85)) 1200-as GB = 1,20 TB = ~ 1,25 TB (kerekítés történő legközelebbi KVARTILIS)

Amíg nem várt növekedés és új visszaállítások, körül a helyi lemezen kell kiépíteni 1,25-1,5 TB.

> [!NOTE]
> Azt javasoljuk, hogy a helyi lemez kiosztása. Ez a javaslat oka az, hogy a visszaállítási területre csak van szükség, ha visszaállítja öt napnál régebbi adatokat. Elemszintű helyreállítás lehetővé teszi adatok helyreállítását a legutóbbi öt napig anélkül, hogy a helyet a visszaállításhoz.


#### <a name="example-2"></a>2. példa
A virtuális tömb szeretné tudni

* kiépítése 2 TB rétegzett kötet
* 300 GB-os helyileg rögzített kötet létrehozása

A rétegzett kötetek vagy megosztások helyi lemezterület-foglalás és a helyileg rögzített kötetek vagy megosztások 10 % 12 % alapján, igazolnia kell

* 240 GB helyi lefoglalás (2 TB rétegzett kötet vagy megosztás)
* A helyileg rögzített kötetet vagy megosztást (a kiépített 300 GB lemezterület 10 %-a helyi foglalás hozzáadása) 330 GB

Teljes terület szükséges a helyi rétegen van: 240 GB + 330 GB = 570 GB

A visszaállítási művelethez szükséges helyi terület 330 GB.

a teljes lemezterület 15 %-át fogja tárolni a pillanatképek, így csak 0.85 érhető el. Igen, a lemez mérete (900&ast;(1/0.85)) 1.06 TB = ~ 1,25 TB (kerekítés történő legközelebbi KVARTILIS)

Amíg nem várt növekedésének, oszthat 1,25-1,5 TB helyi lemezen.

### <a name="group-policy"></a>Csoportházirend
A csoportházirend olyan infrastruktúra, amely lehetővé teszi a megvalósítása bizonyos konfigurációk meghatározását felhasználók és számítógépek. Csoportházirend-beállításokat csoportházirend-objektumok (GPO-k), amelyek kapcsolódnak a következő Active Directory tartományi szolgáltatások (AD DS) tárolók találhatók: webhelyekhez, tartományokhoz vagy szervezeti egységekhez (OU-k). 

Ha a virtuális tömb tartományhoz csatlakoztatott, csoportházirend-objektumok alkalmazhatja azt. A csoportházirend-objektumok, például a víruskereső szoftver, amely hátrányosan befolyásolhatja a művelet a StorSimple virtuális tömb alkalmazásokat telepíthet.

Ezért javasoljuk, hogy:

* Győződjön meg arról, hogy a virtuális tömb saját szervezeti egységben (OU) az Active Directory.
* Győződjön meg arról, hogy a virtuális tömb nem csoportházirend-objektumok (GPO) vonatkoznak. Győződjön meg arról, hogy a virtuális tömb (gyermekcsomópont) nem automatikusan örökölt egyetlen csoportházirend-objektum az az öröklési tilthatja le. További információkért látogasson el [öröklődés blokkolása](https://technet.microsoft.com/library/cc731076.aspx).

### <a name="networking"></a>Hálózat
A virtuális tömb a hálózati konfigurációt a helyi webes felhasználói felületen történik. Egy virtuális hálózati adapteren keresztül, amelyben a virtuális tömb ki van építve a hipervizor engedélyezve van. Használja a [hálózati beállítások](storsimple-virtual-array-deploy3-fs-setup.md) lapon konfigurálhatja a virtuális hálózati illesztő IP-cím, alhálózati és átjáró.  Beállíthatja úgy is az elsődleges és másodlagos DNS-kiszolgáló, a időbeállítást és a nem kötelező proxybeállításainak az eszközhöz. A hálózati konfigurációt a többsége működik egy egyszeri. Tekintse át a [hálózati követelményei StorSimple](storsimple-ova-system-requirements.md#networking-requirements) a virtuális tömb üzembe helyezése előtt.

A virtuális tömb való telepítésekor, azt javasoljuk, hogy pontosan kövesse az alábbi gyakorlati tanácsok:

* Győződjön meg arról, hogy a hálózat, amely a virtuális tömb mindig telepítve van-e az 5 MB/s internetes sávszélességet (vagy több) fordítsanak kapacitása.
  
  * Internetes sávszélesség szükséges a munkaterhelés jellemzőit és az adatcsere sebességétől függ.
  * Az adatok módosítása tudnak kezelni közvetlenül azzal arányos, hogy az internetes sávszélességet. Például ha a biztonsági másolat egy 5 MB/s sávszélesség 8 óra körül 18 GB adatok változás lehetővé teszi. Négy alkalommal nagyobb sávszélességgel (20 MB/s) négy alkalommal további adatok módosítása (72 GB) képes kezelni.
* Ellenőrizze az internetkapcsolat mindig elérhető. Az eszközök internetes kapcsolatokat szórványos vagy nem megbízható a felhőben tárolt adatainak hozzáférni eredményezhet, és nem használható konfigurációt eredményezhet.
* Ha azt tervezi, hogy az iSCSI-kiszolgálóként eszköz üzembe helyezése:
  
  * Azt javasoljuk, hogy tiltsa le a **IP-cím automatikus beszerzése** (DHCP) lehetőséget.
  * Statikus IP-címek konfigurálásához. Konfigurálnia kell egy elsődleges és másodlagos DNS-kiszolgáló.
  * Ha több hálózati adapterrel határozza meg a virtuális tömbben, csak az első hálózati adapter (alapértelmezés szerint ez az interfész van **Ethernet**) el lehet érni a felhőben. A típusú forgalom szabályozásához több virtuális hálózati adapterek létrehozása a virtuális tömb (iSCSI-kiszolgálóként konfigurált), és azok az interfészek csatlakozni más alhálózatokon.
* Csak felhőalapú sávszélesség szabályozása (használja a virtuális tömb), az útválasztó és a tűzfalon a sávszélesség-szabályozás konfigurálása. Ha megadja, hogy a hipervizor szabályozását, azt fogja szabályozás minden protokoll, többek között az iSCSI- és SMB helyett csak a felhő sávszélesség.
* A hipervizorok engedélyezve van, győződjön meg arról, hogy időszinkronizálást. Ha a Hyper-V, válassza ki azt a virtuális tömb a Hyper-V kezelőjében, folytassa a **beállítások &gt; Integration Services**, és győződjön meg arról, hogy a **időszinkronizálás** be van jelölve.

### <a name="storage-accounts"></a>Tárfiókok
A StorSimple virtuális tömb társítható egy tárfiókot. Ez a tárfiók egy automatikusan létrehozott tárfiók ugyanahhoz az előfizetéshez a szolgáltatás fiók vagy a storage-fiók kapcsolódó másik előfizetést. További információkért lásd: hogyan [a virtuális tömb storage-fiókok kezelése](storsimple-virtual-array-manage-storage-accounts.md).

A következő javaslatokat használja a virtuális tömb társított tárfiókok.

* Több virtuális tömbök egy tárfiókkal rendelkező csatoláskor számításba a maximumnál (64 TB) egy virtuális tömb és a storage-fiókok maximális méretét (500 TB). Ez a teljes méretű virtuális tömbökben, amelyek társítható, hogy a tárfiók, és körülbelül 7 számának korlátozása.
* Új tárfiók létrehozásakor
  
  * Azt javasoljuk, hogy létrehozta a régióban legközelebbi a távoli office/fiókirodába, amelyen üzembe van helyezve a StorSimple virtuális tömb késések minimalizálása érdekében.
  * Figyelembe kell vennie, hogy Ön nem helyezhetők át a tárfiók különböző régiókban. Különböző előfizetéseknél is nem helyezhető át a szolgáltatást.
  * A storage-fiók, amely megvalósítja az Adatközpont között redundancia használja. Georedundáns tárolás (GRS), a zóna redundáns tárolás (ZRS) és a helyileg redundáns tárolás (LRS) használatát támogatja a virtuális tömb való használatra. A különböző típusú tárfiókkal további információkért látogasson el [az Azure storage replikációs](../storage/common/storage-redundancy.md).

### <a name="shares-and-volumes"></a>Megosztások és kötetek
A StorSimple virtuális tömb megosztások fájlkiszolgáló és a kötetek, ha az iSCSI-kiszolgálóként konfigurált konfigurálásakor oszthat meg. Az ajánlott eljárások létrehozásához, megosztásokat és -kötetek méretét és a konfigurált kapcsolódnak.

#### <a name="volumeshare-size"></a>Kötet, illetve megosztási mérete
A virtuális tömb megosztások fájlkiszolgáló és a kötetek, ha az iSCSI-kiszolgálóként konfigurált konfigurálásakor oszthat meg. Az ajánlott eljárások létrehozásához, megosztásokat és -kötetek méretét és a konfigurált vonatkoznak. 

Vegye figyelembe a következő gyakorlati tanácsok megosztások vagy kötetek a virtuális eszközön kiépítésekor.

* A fájlméret rétegzett megosztás kiosztott méretéhez képest hatással lehet a rétegezési teljesítményére. Nagy fájlok használata a lassú réteghez kimenő eredményezheti. Amikor olyan nagy fájlok, azt javasoljuk, hogy a legnagyobb fájl a fájlmegosztás méretének % 3-nál kisebb.
* Legfeljebb 16 kötetek vagy megosztások a virtuális tömbben hozhatók létre. A helyileg rögzített és a rétegzett kötetek vagy megosztások méretkorlátai, mindig tekintse meg a [korlátozza a StorSimple virtuális tömb](storsimple-ova-limits.md).
* Egy köteten, a várt adatok felhasználásához, valamint a jövőbeli növekedésre tényező létrehozásakor. A kötet nem bonthatók ki később.
* Ha a kötet létrejött, a StorSimple a kötet mérete nem zsugorítható.
* Történő írásakor a rétegzett kötetek a StorSimple, ha a kötet adatait elér egy meghatározott küszöbérték (viszonyítva álló helyi területet, a kötet számára fenntartva), az I/O folyamatban van. Folytatás írni erre a kötetre lassítja a IO jelentősen. Abban az esetben, ha a rétegzett kötetek (nem aktívan leállítása a felhasználónak kiosztott kapacitásán túlra írása) kiosztott kapacitásánál nagyobbra írhat, megjelenik egy értesítés arról, hogy rendelkezik túllépte. Ha a riasztás jelenik meg, rendkívül fontos, hogy szánjon javító intézkedéseket, például a Törlés a kötet adatait (kötet bővítése jelenleg nem támogatott).
* Vész-helyreállítási használatából adódó engedélyezett megosztások vagy kötetek száma nem 16, és a maximális száma párhuzamos feldolgozható megosztások vagy kötetek is 16, megosztások vagy kötetek számát nem hatással vannak a helyreállítási Időkorlát és RTO.

#### <a name="volumeshare-type"></a>Kötet, illetve megosztási típusa
StorSimple a felhasználás alapján két kötet, illetve megosztási-típusokat támogatja: helyileg rögzített és rétegzett. Helyileg rögzített kötetek vagy megosztások vannak kiosztása meg, mivel a rétegzett kötetek vagy megosztások kiosztása. Nem alakítható át egy helyileg rögzített kötetet vagy megosztást a rétegzett vagy *fordítva* létrehozását követően.

Azt javasoljuk, hogy a következő gyakorlati tanácsok valósítja meg a StorSimple-kötetek vagy megosztások konfigurálásakor:

* A kötet típusa, a kötet létrehozása előtt a telepíteni kívánt munkaterhelések alapján azonosíthatja. Használjon, amely van szükség az adatok helyi garanciákat (még akkor is során egy felhőalapú leállás) és, amely felhő alacsony késleltetésű munkaterhelések helyileg rögzített kötetekhez. Miután egy kötet a virtuális tömböt hoz létre, a kötet típusa nem módosítható a helyileg rögzített a rétegzett vagy *viszont*. Helyileg rögzített kötetekhez például létrehozása, ha SQL munkaterhelések vagy a munkaterhelések virtuális gépeket (VM); használja a rétegzett kötetek fájl megosztás munkaterhelésekhez.
* A beállítást a gyakran használt archív adatokhoz a nagy méretűek meghatározásakor. Egy nagyobb deduplikációs adattömbméret 512 KB-os, ez a lehetőség az adatok átvitele a felhő elősegítésére szolgál.

#### <a name="volume-format"></a>Kötet formátumban
Miután az iSCSI-kiszolgálón létrehozott StorSimple-köteteket, meg kell inicializálása, csatlakoztatása és a kötetek formázása. Ez a művelet a StorSimple eszköz csatlakozik a gazdagépen történik. Következő gyakorlati tanácsokat csatlakoztatása és a StorSimple gazdagépen kötetek formázását.

* Gyorsformázást a StorSimple-köteteket.
* A StorSimple-kötet formázása, amikor egy foglalásiegység-méret (Ausztráliai) 64 KB-os használata (alapértelmezett érték 4 KB-os). A 64 KB-os Ausztráliai alapján házon általános StorSimple munkaterheléseket és egyéb munkaterhelések tesztelése.
* A StorSimple virtuális iSCSI-kiszolgálóként konfigurált tömb használata esetén ezek a kötetek ne használja átnyúló kötetek vagy a dinamikus lemezek vagy lemezek StorSimple használata nem támogatott.

#### <a name="share-access"></a>Fájlmegosztási hozzáférést
A virtuális tömb fájlkiszolgáló megosztások létrehozásakor kövesse az alábbi irányelveket:

* Megosztás létrehozásakor rendelje hozzá a felhasználói csoport helyett egy-egy felhasználóhoz megosztás rendszergazdaként.
* Az NTFS-engedélyek kezelheti a Windows Explorer használatával megosztások szerkesztésével a megosztás létrehozása után.

#### <a name="volume-access"></a>Kötet hozzáférésével
A StorSimple virtuális tömbhöz az iSCSI-kötetek konfigurálásakor fontos szükség való hozzáférést. Annak megállapításához, hogy mely kiszolgálók hozzáfér a kötethez, hozzon létre, és hozzáférés-vezérlési rekordokat (ACRs) társítani a StorSimple-köteteket.

ACRs konfigurálása a StorSimple-köteteket kövesse az alábbi gyakorlati tanácsokat:

* Legalább egy ACR mindig társíthat egy köteten.

* Ha egynél több ACR hozzárendelése egy kötetet, győződjön meg arról, hogy a kötet nincs felfedve oly módon, ahol egyidejűleg hozzáférhetők egynél több nem fürtözött gazdagép. Ha egy köteten több ACRs rendelt, egy figyelmeztető üzenet jelenik meg, ahhoz, hogy ellenőrizze a konfigurációt.

### <a name="data-security-and-encryption"></a>Adatbiztonság és titkosítás
A StorSimple virtuális tömb adatok biztonsági és a titkosítási szolgáltatással, amely titkosítás és az adatok sértetlenségének biztosítása rendelkezik. Ha használja ezeket a funkciókat, javasoljuk, hogy pontosan kövesse az alábbi gyakorlati tanácsok: 

* Adja meg a felhőalapú tárolás titkosítási kulcsát létrehozni az AES-256 titkosítás, az adatokat a felhőbe a virtuális tömbből elküldése előtt. Ez a kulcs nincs szükség, ha először az adatok titkosítva legyenek. A kulcs jön létre, és biztonságos kulcskezelés rendszert használ, mint maradjon [az Azure key vault](../key-vault/key-vault-whatis.md).
* Amikor konfigurálja a tárfiók a StorSimple Manager szolgáltatással, győződjön meg arról, hogy engedélyezi-e az SSL-mód a StorSimple eszköz és a felhő közötti hálózati kommunikációhoz biztonságos csatornát létrehozni.
* Újragenerálja a kulcsokat a storage-fiókok (az Azure Storage szolgáltatás elérésével) rendszeresen fiók eléréséhez módosításai rendszergazdák módosított listája alapján.
* A virtuális tömb adatok tömörített, és a deduplikált Azure elküldés előtt. Nem ajánlott, az Adatdeduplikáció szerepkör-szolgáltatás használata a Windows Server-állomáson.

## <a name="operational-best-practices"></a>Gyakorlati tanácsok az üzemeltetéshez
A gyakorlati tanácsok az üzemeltetéshez olyan irányelvek, a mindennapos felügyeleti vagy a virtuális tömb során betartandó. Ezeket a gyakorlatokat vonatkoznak, adott felügyeleti feladatokhoz, mint a biztonsági másolatok fogadására, a biztonságimásolat-készletből, visszaállítása a feladatátvételt végez, inaktiválása és törlése a tömb, a rendszer használati és figyelése, és fut a vírus keres a virtuális tömbben.

### <a name="backups"></a>Biztonsági másolatok
A virtuális tömbben az adatok biztonsági mentést, nem a felhő két módon, egy alapértelmezett automatikus napi biztonsági másolatot a teljes eszköz 22:30 vagy keresztül manuális igény szerinti biztonsági mentés indítása. Alapértelmezés szerint az eszköz automatikusan létrehozza a rajta található valamennyi adat napi felhőbeli pillanatképeket. További információkért látogasson el [készítsen biztonsági másolatot a StorSimple virtuális tömb](storsimple-virtual-array-backup.md).

A gyakoriság és a társított alapértelmezett biztonsági mentés megőrzési nem módosítható, de be lehet állítani, ahol a napi biztonsági mentései minden nap kezdeményezett idő. Ha konfigurálja az automatikus biztonsági mentés kezdési idejét, azt javasoljuk, hogy:

* A biztonsági mentések ütemezése essen. Biztonsági mentés kezdési ideje számos állomás IO nem kell egyeznie.
* Indítsa el a manuális igény szerinti biztonsági mentés eszköz feladatátvételi vagy a virtuális tömbben az adatok védelme érdekében a karbantartási időszak előtti végrehajtására tervezése során.

### <a name="restore"></a>Visszaállítás
Egy biztonságimentés-készlet két módon állíthatja vissza: állítsa vissza egy másik kötetre vagy megosztásra vagy egy elemszintű helyreállítás (csak egy virtuális fájlkiszolgálóként konfigurált tömb elérhető). Elemszintű helyreállítás teszi fájlok és mappák elemszintű helyreállítása a StorSimple eszközön a megosztások felhőalapú biztonsági másolatából. További információkért látogasson el [állítsa vissza biztonsági másolatból](storsimple-virtual-array-clone.md).

A visszaállítás végrehajtásakor tartsa szem előtt az alábbi irányelveket:

* A StorSimple virtuális tömb nem támogatja a védelem visszaállítása. Ez azonban könnyen lehet megvalósítani egy kétlépéses folyamat: Szabadítson fel helyet a virtuális a tömb, és másik kötet, illetve megosztási majd visszaállítja.
* Ha egy helyi kötet visszaállítása tartsa szem előtt a visszaállítási lesz hosszú ideig futó művelet. Bár előfordulhat, hogy a kötet gyorsan ismét online elérhető, az adatok továbbra is lehet hidratált a háttérben.
* A kötet típusa változatlan marad, a visszaállítás során. A rétegzett kötetek vissza egy másik rétegzett kötet, és egy helyileg rögzített kötet másik helyileg rögzített kötet.
* Próbálja visszaállítani egy kötet vagy megosztás egy biztonsági másolatból, ha a helyreállítási feladat sikertelen lesz, állítsa a célkötetet, vagy a megosztás továbbra is lehet létrehozni a portálon. Fontos, törölje a nem használt célkötetet, vagy a portálon minimalizálása érdekében ez az elem jövőbeli kérdéseket megosztani.

### <a name="failover-and-disaster-recovery"></a>Feladatátvétel és a katasztrófa utáni helyreállítás
Egy eszköz feladatátvétel lehetővé teszi az adatok áttelepítése a *forrás* eszközt az adatközpontban a másikra *cél* eszköz ugyanabban vagy egy különböző földrajzi helyen található. Az eszköz feladatátvétel során a teljes eszköz. A feladatátvételi felhő változnak az adatok a forrás eszköz tulajdonjogát, valamint a céleszközön.

A StorSimple virtuális tömb csak átveheti egy másik, ugyanazt a StorSimple Manager szolgáltatás által kezelt virtuális tömbhöz. A feladatátvételt egy 8000 sorozat eszköz vagy egy másik StorSimple Manager szolgáltatás (mint a forrás eszközhöz) által kezelt tömb nem engedélyezett. A feladatátvételi szempontokat részletező cikkben kapcsolatos további tudnivalókért keresse fel [Előfeltételek az eszköz feladatátvételi](storsimple-virtual-array-failover-dr.md).

Amikor keresztül hajtja végre a sikertelen a virtuális tömb, vegye figyelembe a következőket:

* Egy tervezett feladatátvétel esetén ajánlott eljárás minden a kötetek vagy megosztások offline állapotba a feladatátvétel kezdeményezése előtt életbe. Hajtsa végre az operációsrendszer-specifikus utasításokat a kötetek vagy megosztások offline érvénybe, ha a gazdagép első majd azokat offline állapotba a virtuális eszközön.
* Egy fájl server vészhelyreállítás (DR) javasoljuk, csatlakoztassa a céleszközt ugyanahhoz a tartományhoz, mint a forrás-, hogy a megosztási engedélyek automatikusan feloldani. Ebben a kiadásban a céleszközön ugyanabban a tartományban csak a feladatátvétel támogatott.
* Ha a vész-Helyreállítási sikeresen befejeződött, a rendszer automatikusan törli a forráseszközt. Ha az eszköz már nem érhető el, a rendszer a gazdagép kiépített virtuális gép továbbra is fogyassza az erőforrásokat. Azt javasoljuk, hogy törölje a virtuális gép megakadályozhatja, hogy a díjakat a számoljanak fel Önnek a gazdagép rendszerről.
* Vegye figyelembe, hogy akkor is, ha a feladatátvétel nem jár sikerrel, **adatai mindig biztonságos a felhőben**. Vegye figyelembe a következő három forgatókönyv, amelyben a feladatátvétel nem fejeződött be sikeresen:
  
  * Hiba történt a kezdeti szakaszaiban a feladatátvétel, például amikor a vész-Helyreállítási előtti a rendszer éppen ellenőrzi. Ebben a helyzetben a céleszközön továbbra is használható. Megpróbálkozhat a feladatátvételt a cél ugyanarra az eszközre.
  * Hiba történt a tényleges feladatátvétel során. Ebben az esetben a céleszközt van megjelölve használható. Konfigurálnia kell kiépíteni és egy másik virtuális céltömb valamint, hogy a feladatátvételre használni.
  * A feladatátvétel befejeződött, amely a forráseszközt törölve lett a következő volt, de a céleszközt problémákkal rendelkezik, és adatokat nem férhet hozzá. Az adatok a felhőben továbbra is biztonságban, és egy másik virtuális tömb létrehozásával, és majd használni a céleszközön a Dr könnyen olvasható.

### <a name="deactivate"></a>Inaktiválás
Ha inaktiválja a StorSimple virtuális tömb, akkor az eszköz és a megfelelő StorSimple Manager szolgáltatás közötti kapcsolat Server. Az inaktiválást van egy **állandó** műveletet, és nem vonható vissza. Deaktivált eszköz nem lehet regisztrálni a StorSimple Manager szolgáltatásban újra. További információkért látogasson el [inaktiválja és törölje a StorSimple virtuális tömb](storsimple-virtual-array-deactivate-and-delete-device.md).

Ha a virtuális tömb inaktiválása tartsa szem előtt az alábbi gyakorlati tanácsokat:

* Felhő pillanatkép készítése a virtuális eszköz inaktiválása előtt az adatokat. Ha egy virtuális tömb inaktiválja, minden a helyi eszközön adat elvész. Felhő pillanatképének lehetővé teszi szeretne adatokat helyreállítani egy későbbi időpontban.
* A StorSimple virtuális tömb inaktiválása előtt győződjön meg arról, állítsa le vagy törölje az ügyfelek és kiszolgálók azokon az eszközökön.
* Deaktivált eszköz törlése, ha már nem használ, hogy azt nem keletkeznek költségek.

### <a name="monitoring"></a>Figyelés
Győződjön meg arról, hogy a StorSimple virtuális tömb folyamatos kifogástalan állapotban van, szüksége a tömb figyelésére, és győződjön meg arról, hogy kapott adatokat a rendszer, beleértve a riasztások. A virtuális tömb általános állapotának figyeléséhez, valósítja meg az alábbi gyakorlati tanácsokat:

* A virtuális tömb adatlemez, valamint az operációsrendszer-lemezképet a lemezhasználat nyomon figyelés konfigurálása. Ha a Hyper-v-T futtató, használhatja a System Center Virtual Machine Manager (SCVMM) és a System Center Operations Manager (SCOM) a virtualizációs gazdagépek figyelésére.
* E-mail értesítések beállítása a riasztások küldéséhez bizonyos használati szinten a virtuális tömbjét.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>Index keresési és vírus scan alkalmazások
A StorSimple virtuális tömb automatikusan is réteg a helyi rétegen adatait a Microsoft Azure felhőbe. Egy alkalmazás, például egy index keresési vagy víruskeresést használatakor a StorSimple-on tárolt adatok vizsgálata kell vigyázni, hogy az felhő nem get érhető el, és vissza a helyi réteghez lekért.

Azt javasoljuk, hogy a virtuális tömb a keresési vagy vírus indexvizsgálatot konfigurálásakor megvalósítása a következő gyakorlati tanácsok:

* Tiltsa le a teljes vizsgálat automatikusan konfigurált műveleteket.
* A rétegzett kötetek vagy állítson be az index keresési vírus vizsgálat egy növekményes vizsgálat. Ez lenne ellenőrizni, hogy csak az új adatok valószínűleg elhelyezkedhet a helyi rétegen. Nem érhető el az adatokat, amelyek a felhőbe többszintű növekményes művelet közben.
* Győződjön meg arról, a helyes keresési szűrők és beállítások vannak konfigurálva, hogy csak a megfelelő típusú fájlokat beolvasott beolvasása. Például képfájlok (JPEG, GIF és TIFF), és műszaki osztály rajzok nem kell futtatni a növekményes és teljes index rebuild során.

Windows indexelése folyamat használata esetén kövesse az alábbiakat:

* Ne használja a Windows indexelő rétegzett kötetek, akkor visszahívja nagy mennyiségű adat (több TB-nyi) a felhőből Ha gyakran úgy kell. Az index újraépítése volna beolvasása minden fájltípus indexelésre a tartalmat.
* Használja a Windows, a helyileg rögzített kötetekhez folyamat indexelő, mivel ez csak a helyi rétegeken hozhat létre az index (a felhő adatokat nem fogják elérni) adatok elérésére.

### <a name="byte-range-locking"></a>Bájt tartomány zárolása
Alkalmazások zárolhatja a bájtban megadott címtartomány a fájlokban. Ha bájt tartomány zárolás engedélyezve van az alkalmazásokat, amelyek a StorSimple ír, majd rétegezéséhez nem működik a virtuális tömbjét. Az adatdeduplikációval működéséhez minden területéhez érhető el a fájlokat nem lehet zárolt. A virtuális tömb a rétegzett kötetek bájt tartomány zárolása nem támogatott.

Ajánlott intézkedések kibővítéséért bájt tartomány zárolás a következők:

* Kapcsolja ki a bájttartomány zárolás a az alkalmazás logikáját.
* Használjon helyileg rögzített kötetek (ahelyett, hogy a Szintezett) számára az alkalmazással kapcsolatos adatok. Helyileg rögzített kötetek nem réteg kiterjeszti a felhőbe.
* Ha helyileg használatával rögzített kötetek bájt tartomány zárolás engedélyezve van, a kötet származhatnak online a visszaállítás befejezése előtt. Ezekben az esetekben Várjon, amíg a teljes visszaállításhoz.

## <a name="multiple-arrays"></a>Több tömbök
Több virtuális tömbök kell telepíteni kell egy egyre bővülő munkakészlet sikerült kerülnek be a felhő, amelyek így hatással lesznek a teljesítmény, az eszköz adatok fiókot. Ezekben az esetekben célszerű méretezési eszközök munkakészletének növekedésével. Ehhez a helyszíni adatközpontban lehet hozzáadni egy vagy több eszközön. Az eszköz hozzáadásakor meg a következőket teheti:

* Az aktuális készletében lévő adatok felosztása.
* Az új eszköz(ök) új munkaterhelések telepítése.
* Ha több virtuális tömbök üzembe, azt javasoljuk, hogy a terheléselosztó szempontjából, a tömb szét a különböző hipervizor-állomás.
* Több virtuális tömbök (Ha be van állítva, mint egy fájl vagy iSCSI-kiszolgáló) olyan elosztott fájl rendszer Namespace is telepíthető. A részletes lépéseket lásd a [elosztott fájl rendszer Namespace megoldást hibrid felhőalapú tárolás telepítési útmutató](https://www.microsoft.com/download/details.aspx?id=45507). Az elosztott fájlrendszer replikációs jelenleg nem ajánlott a virtuális tömb való használatra. 

## <a name="see-also"></a>Lásd még
Megtudhatja, hogyan [felügyelete a StorSimple virtuális tömb](storsimple-virtual-array-manager-service-administration.md) a StorSimple Manager szolgáltatással.

