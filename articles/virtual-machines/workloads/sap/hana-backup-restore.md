---
title: HANA biztonsági mentését és visszaállítását, az SAP HANA az Azure-ban (nagyméretű példányok) |} A Microsoft Docs
description: HANA biztonsági mentéshez, és az SAP HANA az Azure-ban (nagyméretű példányok) visszaállítása
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 21232e5a678d6deed920e57cd0433a3b85ca4fdc
ms.sourcegitcommit: 60606c5e9a20b2906f6b6e3a3ddbcb6c826962d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64987899"
---
# <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás

>[!IMPORTANT]
>Ez a cikk nem helyettesíti a felügyeleti dokumentáció SAP HANA vagy SAP-megjegyzések. Terveink szerint már, hogy egy szilárd ismeretekkel és a SAP HANA-felügyelet és a műveletek, különösen a biztonsági mentés, visszaállítás, magas rendelkezésre állású és vész-helyreállítási szaktudását. Ebben a cikkben beállítást mutató képernyőképek a SAP HANA Studio jelennek meg. Tartalom struktúra és az SAP-felügyeleti eszközök és az eszközök magukat a képernyők jellege módosulhat kiadásban a kiadási SAP HANA-ból.

Fontos lépéseket és folyamatokat a környezetben, és a HANA-verziók és kiadások tett gyakorolják. Bizonyos folyamatok ebben a cikkben leírt általános végiglépkedéshez egyszerűsítettek. Ezek nem hivatott részletes lépéseket a végleges művelet szakkönyveket felhasználhatók. Ha azt szeretné, a konfigurációk művelet szakkönyveket létrehozása, tesztelése és gyakorolja a folyamatok, és az adott konfigurációkhoz kapcsolódó folyamatok dokumentálása. 

Egyik legfontosabb szempontja az üzemi adatbázisok a katasztrofális események elleni védelem érdekében. Ezek az események oka lehet bármit az egyszerű felhasználói hibáinak természeti katasztrófák.

Egy adatbázis biztonsági másolatának visszaállítása, bármikor, például előtt valaki törölt kritikus fontosságú adatokat, a lehető legközelebb a módját, azaz állapotba visszaállítás lehetővé teszi, hogy lehetővé teszi a megszakítások időtartamát előtt volt.

Kétféle típusú biztonsági mentések visszaállítása a funkció eléréséhez kell végrehajtani:

- Adatbázisok biztonsági mentése: Teljes körű, a növekményes és a különbözeti biztonsági mentések
- Tranzakciónapló biztonsági mentései

Mellett az alkalmazás szintjén végrehajtott biztonsági mentések teljes adatbázis-biztonsági másolatok tárolási pillanatképekkel hajthat végre. A pillanatképek tárolási ne cserélje le a tranzakciónaplók biztonsági mentését. Tranzakciós naplók biztonsági mentését is fontos, az adatbázis visszaállítása egy bizonyos ponton vagy a már véglegesített tranzakciók naplóinak üres marad. A pillanatképek tárolási helyreállítási gyorsítható fel azáltal, hogy gyorsan az adatbázishoz egy visszaállítási-továbbító képe. 

SAP HANA az Azure-ban (nagyméretű példányok) két biztonsági mentési és visszaállítási lehetőséget kínál:

- **Mindezt saját maga (DIY).** Miután, győződjön meg arról, hogy nincs-e elég hely a lemezen, hajtsa végre teljes adatbázis és a Naplók biztonsági mentését a következő lemez biztonsági mentési módszerek egyikének használatával. Közvetlenül, a nagyméretű HANA-példány egységek vagy NFS-megosztásokat, amely egy Azure virtuális gépen (VM) beállítása a csatlakoztatott kötetek készíthető. Az utóbbi esetben az ügyfelek Linux rendszerű virtuális gép az Azure-ban, Azure Storage csatlakoztatása a virtuális gép, és megoszthatja a tárterületet a virtuális gép egy konfigurált NFS-kiszolgáló. Ellen, amelyhez közvetlenül, nagyméretű HANA-példány egységek kötetek a biztonsági mentést hajt végre, ha a biztonsági másolatok másolása egy Azure storage-fiókot. Ezt követően egy Azure virtuális Gépen, amely az Azure Storage alapuló NFS-megosztások exportálása. Egy Azure Backup-tárolóban vagy egy Azure ritka elérésű tárolási is használhatja. 

   Egy másik lehetőség, hogy egy harmadik fél adatvédelmi eszköz, ahol a biztonsági, miután másolva az Azure storage-fiókba. A házi KÉSZÍTÉSŰ biztonsági mentési lehetőséget is, amely a megfelelőségi és naplózási célokra hosszabb ideig tárolni kívánt adatok szükség lehet. Minden esetben a biztonsági mentések másol át egy virtuális gép és az Azure Storage jelölt NFS-megosztásokat.

- **Infrastruktúra biztonsági mentését és visszaállítását funkciót.** Is használja a biztonsági mentés és visszaállítás, amely tartalmazza az alapul szolgáló infrastruktúra SAP Hana az Azure-ban (nagyméretű példányok). Ez a beállítás teljesíti a biztonsági mentések és a gyors visszaállítás érdekében van szükség. Ez a szakasz a többi címek érhető el a HANA nagyméretű példányok biztonsági mentési és helyreállítási funkciókat. Ez a szakasz is magában foglalja a kapcsolatot, amely a biztonsági mentés és visszaállítás kell a vész helyreállítási funkciók nagyméretű HANA-példányok által kínált.

> [!NOTE]
>   A pillanatkép-technológia, amely az alapul szolgáló infrastruktúra HANA nagyméretű példányok által használt maga az SAP HANA-pillanatképeket. Ezen a ponton az SAP HANA-pillanatképek nem működnek az SAP HANA database több-bérlős tárolók több bérlői együtt. Ha csak egyetlen bérlő van telepítve, az SAP HANA-pillanatképek működnek, és ez a módszer.

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>SAP Hana tárolási pillanatképek használata az Azure-ban (nagyméretű példányok)

A tároló-infrastruktúra az alapul szolgáló SAP HANA az Azure-ban (nagyméretű példányok) támogatja a pillanatképeket tároló kötetek. Biztonsági mentési és a kötetek visszaállítása támogatott, a következő szempontokat:

- Helyett a teljes adatbázis biztonsági mentése a tárolási kötet-pillanatképek rendszeres időközönként kerül.
- Pillanatkép keresztül /hana/data akkor aktiválódik, és /hana/shared, tartalmazza a /usr/sap, kötetek, a pillanatkép-technológia, amely kezdeményezi egy SAP HANA pillanatkép-előtt futtatja a storage-pillanatkép. Az SAP HANA pillanatképe végleges log visszaállítását a telepítő pontja, a storage-pillanatkép helyreállítása után. Egy sikeres HANA pillanatképe szüksége lesz egy aktív HANA-példány. Egy a HSR-forgatókönyvben egy tároló pillanatképe nem támogatott a egy jelenlegi másodlagos csomópontra, ahol egy HANA pillanatképe nem hajtható végre.
- Miután a tároló pillanatképe sikeresen lefutott, az SAP HANA pillanatképe törlődik.
- Gyakran és a /hana/logbackups kötet vagy az Azure-ban tárolt a tranzakciónaplók biztonsági mentését. /Hana/logbackups tartalmazó kötetet, a tranzakciónapló biztonsági mentései pillanatképének külön-külön is indíthat. Ebben az esetben nem kell futtatnia egy HANA pillanatképe.
- Ha vissza kell állítania egy adatbázis egy bizonyos ponton időpontot, egy éles szolgáltatáskimaradás, a kérelem, hogy a Microsoft Azure támogatási vagy SAP HANA az Azure visszaállításkor egy bizonyos tárolási pillanatfelvétel. Ilyen például, a védőfal rendszer tervezett visszaállítás az eredeti állapotba.
- Az SAP HANA pillanatképe, amely megtalálható a tároló pillanatképe az egy eltolási pont tranzakciónaplók biztonsági mentése lefutott, és mentette, miután a tároló pillanatkép alkalmazása számára.
- Állítsa vissza az adatbázist vissza egy bizonyos ponton a időben megnyílik a tranzakciós napló biztonsági mentése.

Tárolási pillanatképekkel kötetek három osztály célzó végezheti el:

- Kombinált pillanatkép/hana/adatok és a/hana/megosztott, amely /usr/sap tartalmazza. A pillanatkép, a storage-pillanatkép előkészítése az SAP HANA-pillanatkép létrehozása van szükség. Az SAP HANA pillanatképe biztosítja, hogy az adatbázis egy tárolási szempontból konzisztens állapotban van-e. A visszaállítási folyamat, amely az a pont, állítsa be a.
- Egy külön pillanatkép/hana/logbackups keresztül.
- Az operációs rendszer partíciójánál.

A legújabb pillanatkép parancsfájlok és dokumentáció lekéréséhez lásd: [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). Ha tölt le, a pillanatkép parancsfájl csomag [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0), három fájlt kap. A fájlok a megadott funkciójának PDF-szerződését. Miután letöltötte az eszköz beállítása, kövesse az utasításokat, a "pillanatkép-eszközök beszerzése."

## <a name="storage-snapshot-considerations"></a>Storage-pillanatkép szempontok

>[!NOTE]
>A pillanatképek tárolási a nagyméretű HANA-példány egységek számára lefoglalt tárhely felhasználását. Vegye figyelembe a következő szempontokat az ütemezés a pillanatképek tárolási és hány tárolási pillanatképet kíván megőrizni. 

Az adott mechanics tárolási pillanatképek az SAP Hana az Azure-ban (nagyméretű példányok) a következők:

- Egy adott tárolási pillanatképet az időpont, amikor használatban van, kis tárolási használ fel.
- A tartalmi változások adatok és fájlok módosíthatja a tárolási köteten az SAP HANA-adatok tartalma a pillanatkép kell tárolnia az eredeti tartalom blokkolása és az adatok változásait.
- Ennek eredményeképpen a storage-pillanatkép mérete növekszik. Minél hosszabb a pillanatkép létezik, annál nagyobb lesz a storage-pillanatkép.
- A további végzett módosításokat, a SAP HANA-adatbázis kötetének tárolási pillanatkép, a nagyobb élettartama során a lemezterület-felhasználást a storage-pillanatkép.

SAP HANA az Azure-ban (nagyméretű példányok) rögzített kötet mérete az SAP HANA adat- és naplózási köteteket tartalmaz. A köteteket pillanatképeket végrehajtása eats a kötet terén. Kell tennie:

- Határozza meg, mikor kell a pillanatképek tárolási ütemezése.
- A tárolóköteteket a lemezterület-felhasználást figyelésére. 
- Kezelheti a tárolt pillanatképek számát. 

A tárolási pillanatképekkel, letilthatja, vagy az adatok tömege importálása vagy egyéb jelentős változások a HANA-adatbázishoz. 


Az alábbi szakaszok ismertetik ezeket a pillanatképeket végrehajtásához, és általános javaslatok a következők:

- Bár a hardver kötetenként 255 pillanatképek képes elviselni, érdemes alatt ez a szám maradjon. Az ajánlás 250 vagy kevesebb.
- Mielőtt végrehajtaná a pillanatképek tárolási, figyelheti és nyomon követheti, szabad lemezterület.
- Szabad terület alapján tárolási pillanatképek számának csökkentéséhez. Csökkentheti a tartani a pillanatképek számát, vagy a köteteket bővítheti. További tárolási egység 1 terabájtos rendezheti.
- Például az adatok áthelyezése az SAP HANA, SAP platform áttelepítési eszközökről (R3load) vagy az SAP HANA-adatbázisok biztonsági másolatokból való visszaállítással idejére tiltsa le a storage-pillanatképeket a /hana/data köteten. 
- Során nagyobb átszervezések SAP HANA-táblák ne a pillanatképek tárolási lehetőség.
- A pillanatképeket tároló kihasználhatja a vész-helyreállítási képességeit az SAP HANA az Azure-ban (nagyméretű példányok) előfeltétele.

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Önkiszolgáló storage snapshots használatával előfeltételei

Győződjön meg arról, hogy a pillanatkép szkript sikeresen lefutott, ellenőrizze, hogy a Perl, a Linux operációs rendszeren a HANA nagyméretű példányok kiszolgálón telepítve. A nagyméretű HANA-példány egységen Perl előtelepítve. Ellenőrizze a Perl-verziót, használja a következő parancsot:

`perl -v`

![Másolja a nyilvános kulcsot a parancs futtatása](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>A pillanatképek tárolási beállítása

Storage-pillanatképeket HANA nagyméretű példányok beállításához, kövesse az alábbi lépéseket.
1. Győződjön meg arról, hogy a Perl telepítve van a nagyméretű HANA-példányokhoz kiszolgálón a Linux operációs rendszeren.
1. Módosítsa az/etc/ssh/ssh\_adja hozzá a sort. konfiguráció _Mac számítógépek hmac-sha1_.
1. SAP HANA biztonsági mentési fiók létrehozása a fő csomópont minden SAP HANA-példány futtatja, ha van ilyen.
1. Az SAP HANA HDB ügyfél telepítése a SAP HANA nagyméretű példányok összes kiszolgálójára.
1. Az első SAP HANA nagyméretű példányok kiszolgáló minden egyes régió hozzon létre egy nyilvános kulcs, amely hozzáfér a mögöttes tároló-infrastruktúra, amely szabályozza a pillanatkép létrehozása.
1. Másolja a parancsfájlok és a konfigurációs fájl [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) helyére **hdbsql** az SAP HANA telepítése.
1. Módosítsa a *HANABackupDetails.txt* fájlt a megfelelő ügyfél-specifikációk szükség szerint.

A legújabb pillanatkép parancsfájlok és dokumentáció [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). A korábban felsorolt lépéseket lásd: [a Microsoft Azure-beli SAP HANA eszközök pillanatfelvétel](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

### <a name="consideration-for-mcod-scenarios"></a>MCOD forgatókönyvekhez szempontok
Ha egy [MCOD forgatókönyv](https://launchpad.support.sap.com/#/notes/1681092) egy nagyméretű HANA-példány egység több SAP HANA példányok, az üzembe helyezett minden egyes, az SAP HANA-példányok külön tárolókötet rendelkezik. MDC és egyéb szempontok további információkért lásd: "Fontos tudnivalókat" a [a Microsoft Azure-beli SAP HANA eszközök pillanatfelvétel](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>1. lépés: Az SAP HANA HDB ügyfél telepítése

A Linux operációs rendszer telepítve van, az SAP HANA az Azure-ban (nagyméretű példányok) a mappákat és az SAP HANA tárolási pillanatképek a biztonsági mentés és katasztrófa utáni helyreállítás céljából futtatásához szükséges parancsfájlokat tartalmazza. Ellenőrizze az újabb kiadásokban [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). A parancsfájlok a legfrissebb verzió 4.0-s verzióját. Előfordulhat, hogy a különböző parancsfájlok fő azonos kiadási belül különböző kisebb kiadások.

A feladata az SAP HANA HDB ügyfél telepítéséhez a nagyméretű HANA-példány egységekben lévő, az SAP HANA telepítése közben.

### <a name="step-2-change-the-etcsshsshconfig"></a>2. lépés: Módosítsa az/etc/ssh/ssh\_config

Ebben a lépésben ismertetett az "Enable tárolóeszközökkel való kommunikációhoz" [a Microsoft Azure-beli SAP HANA eszközök pillanatfelvétel](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).


### <a name="step-3-create-a-public-key"></a>3. lépés: Hozzon létre egy nyilvános kulcs

A storage pillanatkép felületek a nagyméretű HANA-példány bérlőre való hozzáférés engedélyezéséhez, létrehoz egy bejelentkezési eljárást keresztül a nyilvános kulcsot. 

Az első SAP HANA az Azure-ban (nagyméretű példányok) kiszolgáló a saját bérlőjében hozzon létre egy nyilvános kulcsot a tároló-infrastruktúra eléréséhez. A nyilvános kulcsot, a jelszó nem szükséges jelentkezzen be a storage-pillanatkép felületek. Emellett nem kell jelszót karbantartása nyilvános kulccsal. 

Hozzon létre egy nyilvános kulcsot, lásd: "Enable tárolóeszközökkel való kommunikációhoz" a [a Microsoft Azure-beli SAP HANA eszközök pillanatfelvétel](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).


### <a name="step-4-create-an-sap-hana-user-account"></a>4. lépés: Az SAP HANA-felhasználói fiók létrehozása

SAP HANA-pillanatképek létrehozásának megkezdéséhez hozzon létre egy felhasználói fiókot, amellyel a storage-pillanatkép parancsfájlok SAP HANA-ban. Hozzon létre egy SAP HANA-felhasználói fiókot, az SAP HANA Studio erre a célra. A felhasználó a SYSTEMDB kell létrehozni és *nem* alatt MDC SID adatbázisát. Egyetlen tároló a környezetben a felhasználók a bérlői adatbázis létrehozása. Ennek a fióknak rendelkeznie kell **biztonsági mentési rendszergazda** és **katalógus olvasási** jogosultságokkal. 

Állítsa be, és a egy felhasználói fiókot használja, lásd: "A kommunikáció engedélyezése az SAP HANA" a [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0).


### <a name="step-5-authorize-the-sap-hana-user-account"></a>5. lépés: Az SAP HANA-felhasználói fiók engedélyezése

Ebben a lépésben engedélyezze a SAP HANA, így nem kell a parancsfájlok futásidőben jelszavak küldése létrehozott felhasználói fiók. Az SAP HANA parancs `hdbuserstore` lehetővé teszi egy SAP HANA felhasználói kulcs létrehozását. A kulcs egy vagy több SAP HANA-csomópontja tárolja. A felhasználói kulcs lehetővé teszi, hogy a felhasználói hozzáférést az SAP HANA a parancsfájl-kezelési folyamaton belül a jelszavak kezelése nélkül. A parancsfájl-kezelési folyamat Ez a cikk későbbi részében olvashat.

>[!IMPORTANT]
>Futtassa az ugyanazon felhasználói környezetet, amely a pillanatkép-parancsok futtatása a konfigurációs parancsok. Ellenkező esetben a pillanatkép-parancsok nem fog megfelelően működni.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>6. lépés: A pillanatkép-parancsfájlok GET, a pillanatképek konfigurálása és a konfiguráció és a kapcsolat tesztelése

Töltse le a legújabb verzióját a parancsfájlok [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). A parancsfájlok telepítési módját a parancsfájlok 4.0-s kiadása óta megváltozott. További információkért lásd a "Kommunikáció engedélyezése az SAP HANA" [a Microsoft Azure-beli SAP HANA eszközök pillanatfelvétel](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

A pontos parancssorozat, lásd: "A telepítés egyszerű pillanatkép eszközök (alapértelmezett)" a [a Microsoft Azure-beli SAP HANA eszközök pillanatfelvétel](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). Az alapértelmezett telepítés használatát javasoljuk. 

Verzió frissítése 4.0-s, 3.x látható "Frissítse a meglévő" [a Microsoft Azure-beli SAP HANA eszközök pillanatfelvétel](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). Az eszköz 4.0-s készlet eltávolításához lásd: "A pillanatkép-eszközök eltávolítása" a [a Microsoft Azure-beli SAP HANA eszközök pillanatfelvétel](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Ne felejtse el hajtsa végre az "A telepítés befejezéséhez a pillanatkép-eszközök" leírt lépéseket a [a Microsoft Azure-beli SAP HANA eszközök pillanatfelvétel](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

A különböző parancsprogramokat és fájlokat célja van telepítettként leírt "Mik ezek az eszközök pillanatkép?" a [a Microsoft Azure-beli SAP HANA eszközök pillanatfelvétel](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Mielőtt beállítaná a pillanatkép-eszközöket, győződjön meg arról, hogy is konfigurálta a HANA biztonsági mentési helyek és beállítások megfelelően. További információkért lásd az "Az SAP HANA-konfiguráció" [a Microsoft Azure-beli SAP HANA eszközök pillanatfelvétel](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

A konfiguráció a Pillanatkép-eszköz csoport leírása a "Konfigurációs fájlban - HANABackupCustomerDetails.txt" [a Microsoft Azure-beli SAP HANA eszközök pillanatfelvétel](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

#### <a name="test-connectivity-with-sap-hana"></a>Az SAP HANA-kapcsolat tesztelése

Miután az összes konfigurációs adatát nyilvántartani a *HANABackupCustomerDetails.txt* fájlt, ellenőrzése, hogy a konfiguráció megfelelő a HANA-példányok adatait. A parancsfájllal `testHANAConnection`, amely független az SAP HANA vertikális vagy horizontális felskálázás konfigurációs.

További információkért lásd a "Ellenőrizze a kapcsolatot az SAP HANA - testHANAConnection" [a Microsoft Azure-beli SAP HANA eszközök pillanatfelvétel](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

#### <a name="test-storage-connectivity"></a>Tárolási kapcsolódásának tesztelése

A következő vizsgálati lépés az, hogy ellenőrizze a kapcsolatot a tárolóhoz, mindössze az adatok alapján a *HANABackupCustomerDetails.txt* konfigurációs fájlt. Ezután futtassa a test pillanatképet. Mielőtt futtatja a `azure_hana_backup` parancsot futtatnia kell ezt a vizsgálatot. Ebben a tesztben parancsok sorozatát, lásd: "Ellenőrizze a kapcsolatot a storage - testStorageSnapshotConnection" "a [a Microsoft Azure-beli SAP HANA eszközök pillanatfelvétel](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

A sikeres bejelentkezést követően a tároló virtuális gép felületek a parancsfájl továbbra is fennáll, a 2. fázis, és létrehoz egy tesztelési pillanatképet. A kimenet itt látható egy három csomópontos horizontális felskálázás konfigurálásához az SAP HANA.

Ha a teszt pillanatképének a szkript sikeresen lefutott, a tényleges tárterület-pillanatképeket is ütemezhető. Ha nem sikeres, a problémák vizsgálatára, mielőtt folytatná. A teszt pillanatképének köré kell maradnak, addig, amíg az első valós pillanatképek történik.


### <a name="step-7-perform-snapshots"></a>7. lépés: Hajtsa végre a pillanatképek

Előkészítő lépések befejeződése után elkezdheti, konfigurálására és ütemezésére a tényleges tárterület pillanatképeket. Ütemezni a szkript SAP HANA vertikális és horizontális felskálázás konfigurációval működik. A biztonsági mentési parancsprogram-ismétlődő és rendszeres végrehajtásához ütemezni a parancsfájl a cron segédprogram használatával. 

A pontos parancs szintaxisát és funkcióit, tekintse meg az "Végezze el pillanatkép backup - azure_hana_backup" a [a Microsoft Azure-beli SAP HANA eszközök pillanatfelvétel](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 

Ha a parancsfájl `azure_hana_backup` fut, a pillanatkép készítése a következő három fázisra tárolót hoz létre:

1. Futtatja az SAP HANA pillanatképe.
1. Egy tároló pillanatképe futtatja.
1. Eltávolítja az SAP HANA pillanatképe, amely előtt a storage-pillanatkép futott.

A szkript futtatásához meghívására, amelyhez másolták HDB végrehajtható mappából. 

A megőrzési időszak a parancsfájl futtatásakor paraméterként elküldött pillanatképek számát kezel. Az, hogy mennyi ideig hatálya alá tartozik a tárolási pillanatképekkel, akkor egy függvény az időszak, végrehajtás és a paraméterként küldött futtatott parancsfájl pillanatképek számát. 

Őrzi meg a pillanatképek száma meghaladja a számát, amelyet a parancsfájl hívásában paraméterként vannak elnevezve, ha a legrégebbi tárolási pillanatképet címkét törlődik, új pillanatkép futtatása előtt. A szám adjon, az utolsó paraméter, a hívások száma segítségével őrzi meg a pillanatképek számának. Ezzel a számmal is szabályozhatja, közvetett, a pillanatképek használt lemezterületet. 


## <a name="snapshot-strategies"></a>Pillanatkép stratégiák
A különböző tevékenységtípusokkal kapcsolatban pillanatképek gyakorisága attól függ, ha használja a nagyméretű HANA-példány vészhelyreállítási funkciók. Ez a funkció speciális ajánlások lehet szükség a gyakoriságát és végrehajtási időszakokra készített pillanatképeket tároló tárolási pillanatképeket támaszkodik. 

A szempontok és az alábbi, feltételezzük, hogy *nem* használja a vészhelyreállítási funkciók által kínált nagyméretű HANA-példányokhoz. Ehelyett használja a pillanatképeket tároló biztonsági mentések és időponthoz helyreállítási nyújthatnak az elmúlt 30 napra vonatkozóan. Adja meg a korlátozások a pillanatképeket és terület számát, vegye figyelembe az alábbi követelményeknek:

- A helyreállítási pont az adott helyreállítási ideje.
- A használt területet.
- A helyreállítási pont és helyreállításiidő-célkitűzések lehetséges helyreállítási történő vészhelyreállítás.
- A végleges végrehajtásának HANA teljes-adatbázis biztonsági mentését lemezek ellen. Amikor elleni lemezek teljes-adatbázis biztonsági másolatát, vagy a **backint** felületen történik, a storage-pillanatképek végrehajtása meghiúsul. Ha azt tervezi, hogy a teljes adatbázis-biztonsági másolat készíthető, a pillanatképek tárolási felett, ellenőrizze, hogy a storage-pillanatképek végrehajtásának le van tiltva ebben az időszakban.
- A legfeljebb 250 kötetenként pillanatképek számát.

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

HANA nagyméretű példányok vész-helyreállítási funkciójának használatakor nem ritkábban a pillanatkép időszak. Ezekben az esetekben hajtsa végre a kombinált pillanatképek /hana/data és /hana/shared /usr/sap, beleértve a 12 óránként vagy 24 órás időszakban. A pillanatképek folyamatosan a hónap. Ugyanez igaz a napló biztonsági mentési mennyiségi a pillanatkép-készítési. 15 perces időszakokra, 5 perces SAP HANA tranzakciós naplók biztonsági mentését a napló biztonsági mentési mennyiségi elleni végrehajtása történik.

Ütemezett tárolási pillanatképekkel végezhető el a legjobban cron használatával. Ugyanazt a parancsprogramot használja a biztonsági mentések és a vész-helyreállítási igényekre. Módosítsa a parancsfájlt a különböző megfelelő bemenetek kért biztonsági mentés időpontjai. Ezeket a pillanatképeket az összes ütemezett eltérően a cron végrehajtási idejű függően. Lehet, óradíj minden 12 óra naponta vagy hetente. 

Az alábbi példa egy cron-ütemezését /etc/crontab látható:
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
Az előző példában egy óránként összesített pillanatkép terjed ki a köteteket, amelyek tartalmazzák a /hana/data és /hana/shared/SID, amely /usr/sap, helyeket tartalmaz. Az ilyen típusú pillanatkép használata időpontban a gyorsabb helyreállítás az elmúlt két napban. Nincs napi azokon a köteteken pillanatképet készít. Ezért kell érvényességének két nap óránkénti pillanatképek és négy héttel érvényességének által a napi pillanatképeket. A tranzakciós napló biztonsági mentési mennyiségi is biztonsági másolat napi. Négy héttel őrzi meg ezeket a biztonsági másolatokat. 

Ahogyan crontab a harmadik sorban az látható, a HANA-tranzakciónapló biztonsági mentésének 5 percenként van ütemezve. A kezdési idejének a pillanatképek tárolási futtató különböző cron feladatot is egyenletesen elosztani. Ezzel a módszerrel a pillanatképek ne futtassa egyszerre egy bizonyos ponton időben. 

A következő példában hajt végre, amely lefedi a /hana/data és /hana/shared/SID /usr/sap tartalmaz, amelyek a helyek óránként tartalmazó kötetek összesített pillanatképet. Két napig megőrzi ezeket a pillanatképeket. A pillanatképek a tranzakciós napló biztonsági mentési kötetek egy 5 perces időközönként fut, és négy órán át őrzi meg. Mint előtt, a biztonsági mentés a HANA tranzakciós naplófájl való futásra van ütemezve 5 percenként. 

A tranzakciós napló biztonsági mentési mennyiségi pillanatképét a tranzakciónapló biztonsági mentését megkezdése után egy 2 perces késéssel történik. Normál körülmények között az SAP HANA tranzakciónapló biztonsági mentését ezeket 2 percen belül befejeződik. Előtt, a kötet, amely tartalmazza a rendszerindító LUN biztonsági másolatot naponta egyszer készít egy tároló pillanatképe és négy héttel marad.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

A következő ábra mutatja be az előző példában sorrendje. A rendszerindító logikai egység ki van zárva.

![Biztonsági mentések és a pillanatképek közötti kapcsolat](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA végez a /hana/log kötet dokumentálni a véglegesített módosításokat az adatbázis rendszeres írására. Rendszeres időközönként az SAP HANA mentési pont ír a /hana/data kötetet. Az crontab meghatározottak szerint, egy SAP HANA tranzakciónapló biztonsági mentését 5 percenként fut. 

Is megjelenik, hogy az SAP HANA pillanatképe eredményeként a pillanatkép-keresztül a /hana/data és /hana/shared/SID kötetek összesített tárolási elindítása óránként fut-e. A HANA pillanatképe sikeres lesz, miután a kombinált tárolási pillanatkép-futtatások. Utasításai crontab, a storage-pillanatkép a /hana/logbackup köteten futtat minden 5 perc, a HANA tranzakciónapló biztonsági mentését követően körülbelül 2 percet.

> 

>[!IMPORTANT]
> SAP HANA biztonsági mentésekhez tárolási pillanatképekkel használatát az értékes, csak akkor, amikor a pillanatképeket az SAP HANA tranzakciónapló biztonsági mentései együtt történik. A tranzakciós napló biztonsági mentése szükséges ahhoz, hogy biztosítsák az időszakok, a storage-pillanatképek közötti. 

Ha beállította a felhasználók egy 30 napig időponthoz helyreállítási kötelezettségvállalás, kell tennie:

- Egy kombinált tároló pillanatképe /hana/data és, amely 30 napnál régebbi, szélsőséges esetben /hana/shared/SID hozzáféréssel. 
- Összefüggő tranzakciós naplók biztonsági mentését, a kombinált tárolási pillanatképek bármelyike között eltelt idő mind rendelkeznek. Ezért a legrégebbi pillanatkép, a tranzakciós napló biztonsági mentési mennyiségi kell lennie a 30 napnál régebbi. Ez nem az esetben, ha a tranzakciónapló biztonsági mentései másolja egy másik NFS-megosztás található, az Azure Storage. Ebben az esetben előfordulhat, hogy kérje le régi tranzakciónaplók biztonsági mentését, hogy az NFS-megosztásból.

Számára, hogy a pillanatképek tárolási és a tranzakciós naplók biztonsági másolatainak végleges tárreplikáció, módosítsa a helyet, amelybe az SAP HANA ír a tranzakciónapló biztonsági mentései. Ezt a módosítást a HANA Studio. 

Bár az SAP HANA biztonsági mentését teljes log szegmensek automatikusan, adja meg a napló biztonsági mentési gyakoriságát, determinisztikus. Ez akkor különösen igaz olyankor, amikor a vész-helyreállítási beállítást használja, mivel általában szeretné futtatni a naplóalapú biztonsági mentések determinisztikus időszakkal. A következő esetben a napló biztonsági mentési időköz 15 perc van beállítva.

![Az SAP HANA Studio bejelentkezik az SAP HANA biztonsági mentés ütemezése](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

15 percenként üzemezésnél gyakoribb biztonsági mentéseket is választhat. Egy gyakoribb beállítást gyakran használják együtt a HANA nagyméretű példányok vész-helyreállítási funkciójának. Egyes ügyfeleink hajtsa végre a tranzakciónapló biztonsági mentései 5 percenként.

Ha az adatbázis a nem készített biztonsági másolatot, az utolsó lépés az fájlalapú adatbázis biztonsági mentését, hogy hozzon létre egy egyetlen biztonsági mentési bejegyzést, amely a biztonságimásolat-katalógus léteznie kell. SAP HANA ellenkező esetben a megadott naplóalapú biztonsági mentések nem kezdeményezhető.

![Győződjön meg arról, egy fájl alapú biztonsági mentés egyetlen biztonsági mentési bejegyzés létrehozása](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Az első sikeres tárfiók-pillanatképek futtatása után törölje a tesztelési pillanatkép, a 6. lépésben. További információkért lásd: "Test pillanatképek eltávolítása – removeTestStorageSnapshot" a [a Microsoft Azure-beli SAP HANA eszközök pillanatfelvétel](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>A számát és méretét, a pillanatképek a lemez köteten figyelése

Egy adott tárolási köteten figyelemmel kísérheti a pillanatképek és a tárhelyhasználat ezen pillanatképek számát. A `ls` parancs nem jelenít meg, a pillanatkép könyvtárat vagy fájlokat. A Linux operációs rendszer parancs `du` tárolási pillanatképeket kapcsolatos részleteket jeleníti meg, mivel tárolásuk azonos kötetekre. A paranccsal a következő beállításokkal:

- `du –sh .snapshot`: Ezt a lehetőséget biztosít a pillanatkép-könyvtárban lévő összes pillanatképet összesen.
- `du –sh --max-depth=1`: Ez a beállítás a pillanatképek a mentett sorolja fel a **.snapshot** mappát, és minden pillanatkép mérete.
- `du –hc`: Ezt a lehetőséget biztosít az összes pillanatképet által használt teljes mérete.

Ezek a parancsok használatával győződjön meg arról, hogy az elvégzett és tárolt pillanatképek nem felhasználása a köteteken a tárolót.

>[!NOTE]
>A pillanatképek a Boot logikai egység nem láthatók az előző parancsokkal.

### <a name="get-details-of-snapshots"></a>Részletek a pillanatképek
További részleteket a pillanatképeket, amelyet a parancsfájl `azure_hana_snapshot_details`. Ez a szkript futtatható az egyik helyen, ha az aktív kiszolgáló szerepel a vész-helyreállítási helyet. A szkript a következő kimenet, minden olyan kötetre, amely tartalmazza a pillanatképek bontásban tartalmazza: 
   * Teljes pillanatképek a kötet mérete
   * Az adott köteten lévő minden egyes pillanatkép a következő adatokat: 
      - Pillanatkép neve 
      - Létrehozás időpontja 
      - Pillanatkép mérete
      - A pillanatkép gyakorisága
      - Ha szükséges, hogy a pillanatkép társított HANA biztonsági mentés azonosítója

A parancs és kimenetek szintaxisát, lásd: "Lista pillanatképek - azure_hana_snapshot_details" a [a Microsoft Azure-beli SAP HANA eszközök pillanatfelvétel](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>A kiszolgálón a pillanatképek számának csökkentése

Amint azt korábban csökkentheti az egyes címkék tárolt pillanatképek számát. Az utolsó két pillanatkép kezdeményezni a parancs paraméterei a címkét, és meg szeretné őrizni a pillanatképek számát.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

Az előző példában a pillanatkép-címke van **dailyhana**. Ezt a címkét, meg kell őrizni a pillanatképek száma **28**. Lemezterület-felhasználást válaszolni, előfordulhat, hogy szeretne tárolt pillanatképek számának csökkentése érdekében. A pillanatképek számát csökkentheti a 15., ha például egyszerű módszert beállítva az utolsó paraméterrel a parancsfájl futtatásához **15**:

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

Ha a parancsfájl futtatja ezt a beállítást, a pillanatképek, számát, amely tartalmazza az új storage-pillanatkép, érték 15. Őrzi meg a 15 legutóbbi pillanatképeket, és a 15 régebbi pillanatképeket a rendszer törli.

 >[!NOTE]
 > Ez a szkript csökkenti a pillanatképek számát, csak akkor, ha több mint egy órával korábbiak pillanatképek vannak. A parancsfájl nem törli a pillanatképeket, amelyek kevesebb, mint egy órával korábbiak. Ezek a korlátozások kapcsolódó érhető el a nem kötelező vészhelyreállítási funkciók.

Ha már nem szeretné a biztonsági mentési előtaggal pillanatképek készletét karbantartása **dailyhana** szintaxispéldáival, futtassa a parancsfájlt **0** a megőrzési számát. Összes pillanatkép, amelyek megfelelnek a Label értékeként majd el lesznek távolítva. Összes pillanatkép eltávolítása hatással lehet a HANA nagyméretű példányok vészhelyreállítási funkciók képességeit.

Adott pillanatképek törlése egy második lehetőség egy parancsfájl `azure_hana_snapshot_delete`. Ez a szkript készült egy pillanatkép vagy vagy formában, HANA Studio vagy a pillanatkép neve magát a HANA biztonsági másolat azonosítója használatával a pillanatképek törlése. Jelenleg a biztonsági másolat azonosítója csak vannak kötve, a készült pillanatkép a **hana** pillanatkép típusa. Pillanatkép-típusú biztonsági mentések **naplók** és **rendszerindító** nem végrehajtásához egy pillanatkép-, SAP HANA, így nincs biztonsági mentési azonosító is meg lehet találni a pillanatképeket. Ha a pillanatkép neve van megadva, a különböző köteteken, amelyek megfelelnek a megadott pillanatkép neve minden pillanatkép keres. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

A szkript további információkért lásd: "A pillanatkép - azure_hana_snapshot_delete törlése" a [a Microsoft Azure-beli SAP HANA eszközök pillanatfelvétel](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

A parancsfájl futtatása felhasználóként **legfelső szintű**.

>[!IMPORTANT]
>Ha csak a pillanatkép a meglévő adatok törléséhez a pillanatkép törlését követően, hogy adat elvész örökre tervezi.


## <a name="file-level-restore-from-a-storage-snapshot"></a>Fájlszintű visszaállítás tárolási pillanatképből

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
A pillanatkép-típusok **hana** és **naplók**, közvetlenül a köteteken a pillanatképeket is elérheti a **.snapshot** könyvtár. A pillanatképek minden alkönyvtár van. Egyes fájlok másolása a állapotba, amelyben a pillanatkép, az adott alkönyvtárban azokat a tényleges könyvtárstruktúrát ponton kell megadni. 

A jelenlegi verzióban a parancsfájl nincs *nincs* állítsa vissza a pillanatkép-visszaállítás, önkiszolgáló megadott parancsfájlt. Pillanatkép-visszaállítás feladatátvétel során a vész-helyreállítási webhelyként, az önkiszolgáló vészhelyreállítási szkriptek részeként is elvégezhető. A meglévő elérhető pillanatképekből kívánt pillanatkép visszaállítása, forduljon a Microsoft üzemeltetési csapat szolgáltatáskérés megnyitásával.

>[!NOTE]
>Egyetlen fájl visszaállítása a rendszerindítás független a nagyméretű HANA-példány mértékegység típusát a logikai egység pillanatkép-készítési nem működik. A **.snapshot** könyvtár a Boot logikai egység nem elérhető. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>A helyreállítás a legfrissebb HANA pillanatképe

Egy éles válassza ki a forgatókönyvben egy tárolási pillanatképből helyreállításának folyamatán, a Microsoft Azure támogatási szolgálatával egy ügyfél incidens indíthatók el. Magas sürgősségű kérdése, ha a adatok törölve lett a termelési rendszer, és az egyetlen módszer is lekérheti azt, hogy az éles adatbázis visszaállításához.

Egy másik esetben a időponthoz helyreállítás lehet, hogy alacsony sürgősség, és a tervezett nappal. Megtervezheti a helyreállítás, az SAP HANA az Azure-ban egy magas prioritású jelző előléptetése helyett. Például előfordulhat, hogy szeretné az SAP-szoftverek frissítése a fejlesztés új csomag alkalmazásával. Kell visszaállítania egy pillanatkép, amely előtt a fejlesztés Csomagfrissítés állapotát jelöli.

Mielőtt elküldi a kérelmet, kell előkészíteni. Az SAP HANA az Azure-csapat majd kezelni a kérést, és adja meg a visszaállított kötetek. Ezután állítsa vissza a HANA-adatbázis, a pillanatképek alapján.

Az eszközt újonnan létrehozott, visszaállított pillanatkép első lehetőségeit a lásd: "How to pillanatkép visszaállítása" [manuális helyreállítási útmutató az SAP Hana az Azure storage pillanatképből](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf).

A kérelem előkészítése, kövesse az alábbi lépéseket.

1. Döntse el, melyik pillanatkép visszaállítása. Csak a hana/adatmennyiség helyreáll, kivéve, ha más módon utasíthatja. 

1. Állítsa le a HANA-példány.

   ![A HANA-példány leállítása](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Válassza le az adatkötetek a HANA-adatbázis csomópontokon. Ha az adatkötetek továbbra is csatlakoztatva vannak az operációs rendszer, a pillanatkép visszaállítása sikertelen lesz.

   ![Válassza le az adatkötetek a HANA-adatbázis csomópontokon](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Nyisson meg egy Azure-támogatási kérést, és a egy adott pillanatkép visszaállítását kapcsolatos utasításokat is tartalmazzák:

   - A visszaállítás: alatt SAP HANA az Azure Service fel, hogy vegyen részt egy konferenciahívás koordinálja, győződjön meg arról, és győződjön meg arról, hogy a helyes tárfiók-pillanatkép visszaállítása. 

   - Miután a visszaállítás: SAP HANA az Azure Service értesíti, amikor a storage-pillanatkép visszaállítása.

1. A visszaállítási folyamat befejezése után csatlakoztassa újra az adatköteteket.

   ![Minden adatkötetnél újracsatlakoztathatja](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



Az első, például SAP HANA az adatfájlok egy tároló pillanatképe után állt helyre egy másik lehetőség a 7. lépésben dokumentált [manuális helyreállítási útmutató az SAP Hana az Azure storage pillanatképből](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf).

Tekintse meg a biztonsági másolatból történő visszaállítását egy pillanatkép, [manuális helyreállítási útmutató az SAP Hana az Azure storage pillanatképből](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf). 

>[!Note]
>Ha a pillanatkép által a Microsoft operations vissza lett állítva, nem kell 7. lépés.


### <a name="recover-to-another-point-in-time"></a>Helyreállítása egy másik időpontra állapotra
Visszaállítása egy bizonyos ponton, lásd: "Az adatbázis helyreállítása az a következő pontot időben" a [manuális helyreállítási útmutató az SAP Hana az Azure storage pillanatképből](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf). 


## <a name="next-steps"></a>További lépések
- Lásd: [vész-helyreállítási alapelvek és -előkészítés](hana-concept-preparation.md).
