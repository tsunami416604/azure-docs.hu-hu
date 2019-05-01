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
ms.openlocfilehash: 7c03a7e5763f580bf1e17232a5850064710c8227
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64707275"
---
# <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás

>[!IMPORTANT]
>Ez a dokumentáció a SAP HANA-felügyelet dokumentációja és az SAP-megjegyzések helyettesítése. Valószínű, hogy az olvasó rendelkezik egy szilárd ismeretekkel és szaktudását a SAP HANA-felügyelet és a műveletek, különösen a biztonsági mentés, visszaállítás, magas rendelkezésre állású és vész-helyreállítási témaköröket. Ebben a dokumentációban beállítást mutató képernyőképek a SAP HANA Studio jelennek meg. Tartalom struktúra és az SAP-felügyeleti eszközök és az eszközök magukat a képernyők jellege módosulhat kiadásban a kiadási SAP HANA-ból.

Fontos lépéseket és folyamatokat a környezetben, és a HANA-verziók és kiadások tett gyakorolják. Bizonyos folyamatok, az ebben a dokumentációban leírt általános végiglépkedéshez egyszerűsítettek, és nem jelentenek a részletes lépéseket a végleges művelet szakkönyveket felhasználhatók. Ha azt szeretné, a konfigurációk művelet szakkönyveket létrehozásához, teszteléséhez és gyakorolja a folyamatok és az adott konfigurációkhoz kapcsolódó folyamatok dokumentálása szüksége. 

Egyik legfontosabb szempontja az operatív adatbázis a katasztrofális események elleni védelem érdekében. Ezek az események oka lehet bármit az egyszerű felhasználói hibáinak természeti katasztrófák.

Egy adatbázis biztonsági mentéséhez, lehetővé teszi az visszaállítás bármely időpontra időben (például valaki a kritikus fontosságú adatok törlése előtt), lehetővé teszi, hogy a visszaállítási állapotba, amely a lehető legközelebb a módját a megszakítások időtartamát előtt volt.

Kétféle típusú biztonsági másolatok eléréséhez egy funkció, visszaállítása kell végrehajtani:

- Adatbázisok biztonsági mentése: Teljes körű, a növekményes és a különbözeti biztonsági mentések
- Tranzakciónapló biztonsági mentései

Mellett az alkalmazás szintjén végrehajtott biztonsági mentések teljes adatbázis-biztonsági másolatok tárolási pillanatképekkel hajthat végre. A pillanatképek tárolási ne cserélje le a tranzakciónaplók biztonsági mentését. Tranzakciós naplók biztonsági mentését is fontos, az adatbázis visszaállítása egy bizonyos ponton vagy a már véglegesített tranzakciók naplóinak üres marad. A pillanatképek tárolási azonban gyorsítható recovery azáltal, hogy gyorsan az adatbázishoz egy visszaállítási-továbbító képe. 

SAP HANA az Azure-ban (nagyméretű példányok) két biztonsági mentési és visszaállítási lehetőséget kínál:

- Saját munka (DIY). Miután meggyőződött arról, hogy nincs-e elég hely a lemezen, hajtsa végre a teljes adatbázis és a naplóalapú biztonsági másolatok a következő lemez biztonsági mentési módszerek egyikének használatával. Közvetlenül, a nagyméretű HANA-példány egységeket, illetve a hálózati fájl megosztások (NFS), amely egy Azure virtuális gépen (VM) beállítása csatlakoztatott kötetek készíthető. Az utóbbi esetben az ügyfelek Linux rendszerű virtuális gép az Azure-ban, Azure Storage csatlakoztatása a virtuális gép, és megoszthatja a tárterületet a virtuális gép egy konfigurált NFS-kiszolgáló. Ellen, amelyhez közvetlenül, nagyméretű HANA-példány egységek kötetek a biztonsági mentést hajt végre, ha meg kell másolnia a biztonsági másolatok az Azure storage-fiókba (Miután beállította az Azure virtuális Gépekhez, amelyek az Azure Storage alapuló NFS-megosztások exportálása). Egy Azure backup-tárolóba vagy a ritka elérésű az Azure storage is használható. 

   Egy másik lehetőség, hogy egy harmadik fél adatvédelmi eszköz, ahol a biztonsági után másolja őket egy Azure storage-fiókot. A házi KÉSZÍTÉSŰ biztonsági mentési lehetőséget is szükség lehet a megfelelőségi és naplózási célokra hosszabb ideig tárolni kívánt adatokat. Minden esetben a biztonsági mentések másol át egy virtuális gép és az Azure Storage jelölt NFS-megosztásokat.

- Infrastruktúra biztonsági mentését és visszaállítását funkciót. Is használja a biztonsági mentés és visszaállítás, amely tartalmazza az alapul szolgáló infrastruktúra SAP Hana az Azure-ban (nagyméretű példányok). Ez a beállítás teljesíti a biztonsági mentések és a gyors visszaállítás érdekében van szükség. Ez a szakasz a többi címek érhető el a HANA nagyméretű példányok biztonsági mentési és helyreállítási funkciókat. Ez a szakasz is magában foglalja a kapcsolat biztonsági mentés és visszaállítás kell a vész helyreállítási funkciók nagyméretű HANA-példányok által kínált.

> [!NOTE]
>   A pillanatkép-technológia, amely az alapul szolgáló infrastruktúra HANA nagyméretű példányok által használt maga az SAP HANA-pillanatképeket. Ezen a ponton az SAP HANA-pillanatképek nem működik az SAP HANA database több-bérlős tárolók több bérlői együtt. Ha csak egyetlen bérlő üzembe lett helyezve, az SAP HANA-pillanatképek működnek, és ez a módszer is használható.

## <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Storage snapshots SAP Hana használatával az Azure-ban (nagyméretű példányok)

A tároló-infrastruktúra az alapul szolgáló SAP HANA az Azure-ban (nagyméretű példányok) támogatja a pillanatképeket tároló kötetek. Biztonsági mentési és a kötetek visszaállítása támogatott, a következő szempontokat:

- Helyett a teljes adatbázis biztonsági mentése a tárolási kötet-pillanatképek rendszeres időközönként kerül.
- Amikor elindítása egy pillanatkép /hana/data és /hana/shared (tartalmazza a /usr/sap) a kötetek, a pillanatkép-technológia egy SAP HANA hajtja végre a rendszer a storage-pillanatkép pillanatkép indítja el. Az SAP HANA pillanatképe végleges log visszaállítását a telepítő pontja, a storage-pillanatkép helyreállítása után. HANA pillanatképe sikeres legyen szüksége lesz egy aktív HANA-példány.  HSR esetben tároló pillanatképe nem támogatott az aktuális másodlagos csomópontra, ahol a HANA pillanatképe nem hajtható végre.
- Miután a tároló pillanatképe sikeresen végre lett hajtva, az SAP HANA pillanatképe törlődik.
- Tranzakciónapló biztonsági mentései gyakran kerül, és a /hana/logbackups kötetet, vagy az Azure-ban tárolja. /Hana/logbackups tartalmazó kötetet, a tranzakciónapló biztonsági mentései pillanatképének külön-külön is indíthat. Ebben az esetben nem kell végrehajtani egy HANA pillanatképe.
- Ha vissza kell állítania egy adatbázis egy bizonyos ponton az ideje, kérelmek, hogy a Microsoft Azure-támogatást (egy éles kimaradásáról) vagy az SAP HANA Azure visszaállításkor egy bizonyos tárolási pillanatfelvétel. Ilyen például, a védőfal rendszer tervezett visszaállítás az eredeti állapotba.
- Az SAP HANA pillanatképe, amely megtalálható a tároló pillanatképe az egy eltolási pont, a tranzakciónapló biztonsági mentéseivel, amelyek végrehajtása és a tárolt, miután a tároló pillanatkép alkalmazása.
- Állítsa vissza az adatbázist vissza egy bizonyos ponton a időben megnyílik a tranzakciós napló biztonsági mentése.

Kötetek három osztály célzó tárolási pillanatképekkel végezheti el:

- Egy kombinált pillanatkép/hana/adatok, valamint /hana/shared (a/usr/sap tartalmazza). A pillanatkép, a storage-pillanatkép előkészítése az SAP HANA-pillanatkép létrehozása van szükség. Az SAP HANA pillanatképe gondoskodik arról, hogy az adatbázis egy tárolási szempontból konzisztens állapotban van-e. A működőképes és, hogy a visszaállításhoz, amely egy pont beállítása
- Egy külön pillanatkép/hana/logbackups keresztül.
- Az operációs rendszer partíciójánál.

A legújabb pillanatkép parancsfájlok és dokumentáció [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). Ha tölt le, a pillanatkép parancsfájl csomagot a [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0), három fájlokat, amelyeknek egy PDF-dokumentáció megadott funkciójának egyike kap. Győződjön meg arról, hogy folytatja a műveletet a fejezet utasításait követve mentén "első a pillanatkép-eszközök: Ha az eszköz beállítása letöltése.

## <a name="storage-snapshot-considerations"></a>Storage-pillanatkép szempontok

>[!NOTE]
>A pillanatképek tárolási tárhely rendelve, a nagyméretű HANA-példány egységek felhasználását. Fontolja meg az ütemezés a pillanatképek tárolási és hány tárolási pillanatképet kíván megőrizni a következő szempontokat kell. 

Az adott mechanics tárolási pillanatképek az SAP Hana az Azure-ban (nagyméretű példányok) a következők:

- (Ha szükséges idő pontján) egy adott tároló pillanatképe csekély tárolási használ fel.
- A tartalmi változások adatok és fájlok módosíthatja a tárolási köteten az SAP HANA-adatok tartalma a pillanatkép kell tárolnia az eredeti tartalom blokkolása, valamint az adatok változásait.
- Ennek eredményeképpen a storage-pillanatkép mérete növekszik. Minél hosszabb a pillanatkép létezik, annál nagyobb lesz a storage-pillanatkép.
- A további végzett módosításokat, a SAP HANA-adatbázis kötetének tárolási pillanatkép, a nagyobb élettartama során a lemezterület-felhasználást a storage-pillanatkép.

SAP HANA az Azure-ban (nagyméretű példányok) rögzített kötet mérete az SAP HANA adat- és naplózási köteteket tartalmaz. A köteteket pillanatképeket végrehajtása eats a kötet terén. Meg kell határoznia, amikor ütemezett tárolási pillanatképek. Is kell figyelni a lemezterület-felhasználást a tároló kötetek, valamint kezelheti a tárolt pillanatképek számát. A tárolási pillanatképekkel, letilthatja, vagy az adatok tömege importálása vagy egyéb jelentős változások a HANA-adatbázishoz. 


Az alábbi szakaszok nyújtanak információt a pillanatképek, beleértve az általános javaslatok végrehajtásához:

- Bár a hardver kötetenként 255 pillanatképek képes elviselni, érdemes alatt ez a szám maradjon. Az ajánlás 250 vagy kevesebb.
- Mielőtt végrehajtaná a pillanatképek tárolási, figyelheti és nyomon követheti, szabad lemezterület.
- Szabad terület alapján tárolási pillanatképek számának csökkentéséhez. Csökkentheti a tartani a pillanatképek számát, vagy a köteteket bővítheti. További tárolási egység 1 terabájtos rendezheti.
- Például az adatok áthelyezése az SAP HANA, SAP platform áttelepítési eszközökről (R3load) vagy az SAP HANA-adatbázisok biztonsági másolatokból való visszaállítással idejére tiltsa le a storage-pillanatképeket a /hana/data köteten. 
- Során nagyobb átszervezések SAP HANA-táblák tárolási pillanatképekkel kerülni kell, ha lehetséges.
- A pillanatképeket tároló kihasználhatja a vész-helyreállítási képességeit az SAP HANA az Azure-ban (nagyméretű példányok) előfeltétele.

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Önkiszolgáló storage snapshots használatával előfeltételei

Győződjön meg arról, hogy a pillanatkép-parancsfájl végrehajtása sikeresen megtörtént, ellenőrizze, hogy a Perl telepítve van a nagyméretű HANA-példányokhoz kiszolgálón a Linux operációs rendszeren. Perl előre telepítve áll rendelkezésre a nagyméretű HANA-példány található. Ellenőrizze a Perl-verziót, használja a következő parancsot:

`perl -v`

![Másolja a nyilvános kulcsot a parancs futtatása](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>A pillanatképek tárolási beállítása

Storage-pillanatképeket HANA nagyméretű példányok beállításához, kövesse az alábbi lépéseket:
1. Győződjön meg arról, hogy a Perl telepítve van a nagyméretű HANA-példányokhoz kiszolgálón a Linux operációs rendszeren.
1. Módosítsa az/etc/ssh/ssh\_adja hozzá a sort. konfiguráció _Mac számítógépek hmac-sha1_.
1. SAP HANA biztonsági mentési fiók létrehozása a fő csomópont minden SAP HANA-példány futtatja, ha van ilyen.
1. Az SAP HANA HDB ügyfél telepítése a SAP HANA nagyméretű példányok összes kiszolgálójára.
1. Az első SAP HANA nagyméretű példányok kiszolgáló minden egyes régió hozzon létre egy nyilvános kulcs, amely hozzáfér a mögöttes tároló-infrastruktúra, amely szabályozza a pillanatkép létrehozása.
1. Másolja a parancsfájlok és a konfigurációs fájl [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) helyére **hdbsql** az SAP HANA telepítése.
1. Módosítsa a *HANABackupDetails.txt* fájlt a megfelelő ügyfél-specifikációk szükség szerint.

A legújabb pillanatkép parancsfájlok és dokumentáció [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). A fent felsorolt részletes lépéseiért tekintse meg [a Microsoft Azure-beli SAP Hana pillanatkép-eszközök](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)

### <a name="consideration-for-mcod-scenarios"></a>MCOD forgatókönyvekhez szempontok
Ha egy [MCOD forgatókönyv](https://launchpad.support.sap.com/#/notes/1681092) egy nagyméretű HANA-példány egység több SAP HANA példányok, az üzembe helyezett minden egyes, az SAP HANA-példányok külön tárolókötet rendelkezik. MDC és egyéb szempontok részleteiért ellenőrizze [a Microsoft Azure-beli SAP HANA eszközök pillanatfelvétel](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) fejezet **"Fontos Megjegyzendő tudnivalók"**.
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>1. lépés: Az SAP HANA HDB ügyfél telepítése

A Linux operációs rendszer telepítve van, az SAP HANA az Azure-ban (nagyméretű példányok) a mappákat és az SAP HANA tárolási pillanatképekkel biztonsági mentési és vész-helyreállítási céllal végrehajtásához szükséges parancsfájlokat tartalmazza. Ellenőrizze az újabb kiadásokban [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). A parancsfájlok a legfrissebb verzió 4.0-s verzióját. Előfordulhat, hogy a különböző parancsfájlok fő azonos kiadási belül különböző kisebb kiadások.

A feladata az SAP HANA HDB ügyfél telepítése akkor, amikor telepíti az SAP HANA nagyméretű HANA-példány egységek.

### <a name="step-2-change-the-etcsshsshconfig"></a>2. lépés: Módosítsa az/etc/ssh/ssh\_config

Ebben a lépésben az újabb kiadásokban az ellenőrzés részletes leírása a [a Microsoft Azure-beli SAP HANA pillanatfelvétel eszközök](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) fejezet **"storage való kommunikáció engedélyezése:**


### <a name="step-3-create-a-public-key"></a>3. lépés: Hozzon létre egy nyilvános kulcs

Engedélyezi a hozzáférést a storage pillanatkép felületek a nagyméretű HANA-példány bérlő, meg kell létrehoznia egy bejelentkezési eljárást keresztül a nyilvános kulcs. Az első SAP HANA az Azure-ban (nagyméretű példányok) kiszolgáló a saját bérlőjében hozzon létre a tároló-infrastruktúra eléréséhez használt nyilvános kulcsot. A nyilvános kulcs biztosítja, hogy jelentkezzen be a storage-pillanatkép felületek nem szükséges jelszó. Nyilvános kulcs létrehozása is jelenti, hogy nem kell fenntartani a jelszavas hitelesítő adatokat. A pontos lépések létrehozása a nyilvános kulcs leírt [a Microsoft Azure-beli SAP HANA eszközök pillanatfelvétel](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) fejezetben **"Storage való kommunikáció engedélyezése"**


### <a name="step-4-create-an-sap-hana-user-account"></a>4. lépés: Az SAP HANA-felhasználói fiók létrehozása

SAP HANA-pillanatképek létrehozásának kezdeményezni, hozzon létre egy felhasználói fiókot, amellyel a storage-pillanatkép parancsfájlok SAP HANA-ban kell. Hozzon létre egy SAP HANA-felhasználói fiókot, az SAP HANA Studio erre a célra. A felhasználónak kell létrehozni a SYSTEMDB alatt, és nem vonatkozik a SID-adatbázis MDC számára. Egyetlen tároló a környezetben a felhasználók a bérlői adatbázis létrehozása. Ezt a fiókot a következő jogosultságokkal kell rendelkeznie: **Biztonsági mentési rendszergazda** és **olvassa el a katalógus**. Állítsa be a felhasználó, és a felhasználó a pontos lépések, olvassa el a [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) fejezetben **"az SAP HANA-kommunikáció engedélyezése:**


### <a name="step-5-authorize-the-sap-hana-user-account"></a>5. lépés: Az SAP HANA-felhasználói fiók engedélyezése

Ebben a lépésben is engedélyezni szeretné a létrehozott, az SAP HANA-felhasználói fiókot, hogy a parancsfájlok nem kell elküldeni a jelszavak futásidőben. Az SAP HANA parancs `hdbuserstore` lehetővé teszi egy felhasználó egy vagy több SAP HANA-csomóponton tárolt SAP HANA kulcs létrehozását. A felhasználói kulcs lehetővé teszi, hogy a felhasználói hozzáférést az SAP HANA a parancsfájl-kezelési folyamaton belül a jelszavak kezelése nélkül. A parancsfájl-kezelési folyamat Ez a cikk későbbi részében olvashat.

>[!IMPORTANT]
>Futtassa a konfigurációs parancsok a pillanatkép-parancsok végrehajtása az azonos felhasználói környezetben. Ellenkező esetben a pillanatkép-parancsok nem fog megfelelően működni.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>6. lépés: A pillanatkép-parancsfájlok GET, a pillanatképek konfigurálása és a konfiguráció és a kapcsolat tesztelése

Töltse le a legújabb verzióját a parancsfájlok [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). A módszer a parancsfájlok fogják telepíteni a parancsfájlok 4.0-s kiadása majorly megváltozott. Pontos részleteiért olvassa el a [a Microsoft Azure-beli SAP HANA pillanatfelvétel eszközök](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) fejezetben **"az SAP HANA-kommunikáció engedélyezése:**

A pontos parancssorozat, olvassa el a fejezet **: egyszerű telepítés pillanatkép eszközök (alapértelmezett)"** a dokumentum [a Microsoft Azure-beli SAP HANA pillanatfelvétel eszközök](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). Az alapértelmezett telepítés használatát javasoljuk. Ha azt szeretné, frissítsen a verzió 3.x 4.0-s, ellenőrizze a szakasz **"Frissíti a meglévő"** , [a Microsoft Azure-beli SAP HANA eszközök pillanatfelvétel](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). Az eszköz 4.0-s készlet eltávolításához, kövesse a **"A pillanatkép-eszközök eltávolítása"** a [a Microsoft Azure-beli SAP HANA pillanatfelvétel eszközök](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Ne felejtse el leírt lépések végrehajtásához **"A telepítés befejezéséhez a pillanatkép-eszközök"** , [a Microsoft Azure-beli SAP HANA eszközök pillanatfelvétel](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

A különböző parancsprogramokat és fájlokat célja van telepítettként felsorolt és részletes leírást talál az **"Mik ezek pillanatképet az eszközök?"** a dokumentum [a Microsoft Azure-beli SAP HANA eszközök pillanatfelvétel](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Mielőtt konfigurálná a pillanatkép-eszközöket, győződjön meg arról, hogy is konfigurálta a HANA biztonsági mentési helyek és beállítások megfelelően leírtak **"Az SAP HANA-konfiguráció"** a dokumentum [Microsoft pillanatkép-eszközök az SAP Hana-hoz az Azure-ban](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

A pillanatkép-eszközkészlet konfigurálása részletesen ismertetett **"Konfigurációs fájl – HANABackupCustomerDetails.txt"** a dokumentum [a Microsoft Azure-beli SAP HANA eszközök pillanatfelvétel](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

#### <a name="testing-connectivity-with-sap-hana"></a>SAP HANA-kapcsolat tesztelése

Miután az összes konfigurációs adatát nyilvántartani a *HANABackupCustomerDetails.txt* fájlt, ellenőrzése, hogy a konfiguráció megfelelő a HANA-példányok adatait. A parancsfájllal `testHANAConnection`, amely független az SAP HANA vertikális vagy horizontális felskálázás konfigurációs.

További részletekért lásd: **"Ellenőrizze a kapcsolatot az SAP HANA - testHANAConnection"** a dokumentum [a Microsoft Azure-beli SAP Hana pillanatkép-eszközök](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)

#### <a name="testing-storage-connectivity"></a>Storage-kapcsolat tesztelése

A következő vizsgálati lépés az, hogy ellenőrizze a kapcsolatot a tárolóhoz, mindössze az adatok alapján a *HANABackupCustomerDetails.txt* konfigurációs fájlt, és a egy teszt pillanatkép végre. Végrehajtása előtt a `azure_hana_backup` parancsot futtatnia kell ezt a vizsgálatot. Ebben a tesztben a parancssorozat szerepel-e **"Ellenőrizze a kapcsolatot a storage - testStorageSnapshotConnection"** a dokumentum [a Microsoft Azure-beli SAP HANA eszközök pillanatfelvétel](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

A sikeres bejelentkezést követően a tároló virtuális gép felületek a parancsfájl továbbra is fennáll, a 2. fázis, és létrehoz egy tesztelési pillanatképet. A kimenet itt látható egy három csomópontos horizontális felskálázás konfigurálásához az SAP HANA:

Ha a teszt pillanatképének a szkript sikeresen végrehajtva, folytathatja a tényleges tárterület pillanatképek ütemezését. Ha nem sikeres, a problémák vizsgálatához a előtt. A teszt pillanatképének köré kell maradnak, addig, amíg az első valós pillanatképek történik.


### <a name="step-7-perform-snapshots"></a>7. lépés: Hajtsa végre a pillanatképek

Előkészítő lépések befejeződése után elkezdheti, konfigurálására és ütemezésére a tényleges tárterület pillanatképeket. Ütemezni a szkript SAP HANA vertikális és horizontális felskálázás konfigurációval működik. A biztonsági mentési parancsprogram-ismétlődő és rendszeres végrehajtásához ütemezni a parancsfájl a cron segédprogram használatával. 

A pontos parancs szintaxisát és -funkcióinak, olvassa el **"Végezze el pillanatkép biztonsági másolatából - azure_hana_backup"** a dokumentum [a Microsoft Azure-beli SAP HANA eszközök pillanatfelvétel](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).  

A szkript végrehajtásának `azure_hana_backup` hoz létre a storage-pillanatkép készítése a következő három fázisra:

1. Végrehajt egy SAP HANA pillanatképe
1. Végrehajt egy storage-pillanatkép
1. Eltávolítja az SAP HANA pillanatképe, amely a tároló pillanatképe végrehajtása előtt lett létrehozva

A szkript végrehajtásához hívja a mappából HDB végrehajtható, amelyhez másolták. 

A megőrzési időszak a paramétert, ha a parancsprogram futtatása küldött pillanatképek számát kezel. Az, hogy mennyi ideig hatálya alá tartozik a tárolási pillanatképekkel, akkor egy függvény az időszak, végrehajtás és a beküldött paraméterként, a parancsfájl végrehajtása közben pillanatképek számát. Őrzi meg a pillanatképek száma meghaladja a számát, amelyet a parancsfájl hívásában paraméterként vannak elnevezve, ha a legrégebbi tárolási pillanatképet címkét törlődik, új pillanatkép végrehajtása előtt. A szám adjon, az utolsó paraméter, a hívások száma segítségével őrzi meg a pillanatképek számának. Ezzel a számmal is szabályozhatja, közvetett, a pillanatképek felhasznált lemezterületet. 


## <a name="snapshot-strategies"></a>Pillanatkép stratégiák
A különböző tevékenységtípusokkal kapcsolatban pillanatképek gyakorisága attól függ, ha használja a nagyméretű HANA-példány vészhelyreállítási funkciók. Ez a funkció speciális ajánlások lehet szükség a gyakoriságát és végrehajtási időszakokra készített pillanatképeket tároló tárolási pillanatképeket támaszkodik. 

A szempontok és az alábbi, feltételezzük, hogy *nem* használja a vészhelyreállítási funkciók által kínált nagyméretű HANA-példányokhoz. Ehelyett használja a pillanatképeket tároló biztonsági mentések és időponthoz helyreállítási nyújthatnak az elmúlt 30 napra vonatkozóan. A korlátozások a pillanatképeket és terület számát adja meg, ügyfeleink tekintette az alábbi követelményeknek:

- A helyreállítási pont az adott helyreállítási ideje.
- A használt területet.
- A helyreállítási pont és helyreállításiidő-célkitűzések lehetséges helyreállítási történő vészhelyreállítás.
- A végleges végrehajtásának HANA teljes-adatbázis biztonsági mentését lemezek ellen. Amikor elleni lemezek teljes-adatbázis biztonsági másolatát, vagy a **backint** felületen történik, a storage-pillanatképek végrehajtása meghiúsul. Ha azt tervezi, hogy hajtsa végre a teljes adatbázis-biztonsági mentések tárolási pillanatképekkel felett, ellenőrizze, hogy a storage-pillanatképek végrehajtásának le van tiltva ebben az időszakban.
- A pillanatképek (legfeljebb 250) kötetenként számát.

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

Azok a vásárlóknak, akik HANA nagyméretű példányok vész-helyreállítási funkciójának ne használja a pillanatkép idő pedig kevésbé gyakori. Ezekben az esetekben ügyfelek hajtsa végre a kombinált pillanatképek /hana/data és /hana/shared (tartalmazza a /usr/sap) 12 óránként vagy 24 órás időszakban, és a pillanatképek egy hónapig vezetnek. Ugyanez vonatkozik a napló biztonsági mentési kötet pillanatképekkel. Az SAP HANA tranzakciós naplók biztonsági mentését a napló biztonsági mentési mennyiségi elleni végrehajtását azonban 5-15 perces időszakokra történik.

Ütemezett tárolási pillanatképekkel végezhető el a legjobban cron használatával. Biztonsági mentések és a vész-helyreállítási igényekre ugyanazt a parancsprogramot használja, és módosítsa a parancsfájl bemeneti adatok felel meg a különböző biztonsági mentés időpontjai kért. Ezek a pillanatképek minden ütemezett eltérően a végrehajtási idejű függően cron: óránként, 12 óránként, napi vagy heti. 

Az alábbiakban látható egy példa az/etc/crontab egy cron-ütemezését:
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
Az előző példában van egy óránként összesített pillanatkép, amely lefedi a/hana/adatokat tartalmazó kötetek és /hana/shared/SID (beleértve a/usr/sap) helyen. Az ilyen típusú pillanatkép használata időpontban a gyorsabb helyreállítás az elmúlt két napban. Emellett nincs napi pillanatképet a köteteket. Ezért kell érvényességének két nap óránkénti pillanatképeket, valamint négy héttel érvényességének által a napi pillanatképeket. Ezenkívül a tranzakciós napló biztonsági mentési mennyiségi készüljön biztonsági mentés naponta. Négy héttel is őrzi meg ezeket a biztonsági másolatokat. Ahogyan crontab a harmadik sorban az látható, a HANA-tranzakciónapló biztonsági mentésének van ütemezve, hogy 5 percenként. A kezdési idejének különböző cron feladatot, a pillanatképek tárolási végrehajtható egyenletesen vannak elosztani, így ezen pillanatképek nem hajtja végre egyszerre egy egy adott időpontban. 

A következő példában hajt végre, amely lefedi a /hana/shared/SID (beleértve a/usr/sap), az / hana/adatokat tartalmazó kötetek összesített pillanatkép helyek óránként. Két napig megőrzi ezeket a pillanatképeket. A pillanatképek a tranzakciós napló biztonsági mentési kötetek egy 5 perces időközönként hajtja végre, és 4 órán át őrzi meg. Mint előtt, a biztonsági mentés a HANA tranzakciós naplófájl van ütemezve, hogy 5 percenként. A tranzakciós napló biztonsági mentési mennyiségi pillanatképét a tranzakciónapló biztonsági mentését megkezdése után egy 2 perces késéssel történik. Az SAP HANA tranzakciónapló biztonsági mentését ezeket 2 percen belül befejeződik normál körülmények között. Előtt, a kötet, amely tartalmazza a rendszerindító LUN biztonsági másolatot naponta egyszer készít egy tároló pillanatképe és négy héttel marad.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

A következő ábra mutatja be az előző példában, kivéve a rendszerindítás LUN sorrendje:

![Biztonsági mentések és a pillanatképek közötti kapcsolat](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA végez a /hana/log kötet dokumentálni a véglegesített módosításokat az adatbázis rendszeres írására. Rendszeres időközönként az SAP HANA mentési pont ír a /hana/data kötetet. Az crontab meghatározottak szerint egy SAP HANA tranzakciónapló biztonsági mentését hajtja végre 5 percenként. Is megjelenik, hogy az SAP HANA pillanatképe óránként eredményeként egy kombinált tároló pillanatképe elindítása a /hana/data és /hana/shared/SID kötetek keresztül hajtja végre. Miután a HANA pillanatképe sikeres, a kombinált tároló pillanatképe hajtja végre. Crontab utasításai minden 5 perc, körülbelül 2 percet a HANA tranzakciónapló biztonsági mentését követően a tároló pillanatképe a /hana/logbackup köteten hajtja végre.

> 

>[!IMPORTANT]
> SAP HANA biztonsági mentésekhez tárolási pillanatképekkel használatát az értékes, csak akkor, amikor a pillanatképeket az SAP HANA tranzakciónapló biztonsági mentései együtt történik. A tranzakciós napló biztonsági mentése szükséges ahhoz, hogy biztosítsák az időszakok, a storage-pillanatképek közötti. 

Ha beállította a felhasználók egy 30 napig időponthoz helyreállítási kötelezettségvállalás, kell tennie:

- A szélsőséges esetben egy pillanatkép-/hana/data és, amely 30 napnál régebbi /hana/shared/SID kombinált tároló eléréséhez.
- Összefüggő tranzakciós naplók biztonsági mentését, a kombinált tárolási pillanatképek bármelyike között eltelt idő mind rendelkeznek. Ezért a legrégebbi pillanatkép, a tranzakciós napló biztonsági mentési mennyiségi kell lennie a 30 napnál régebbi. Nem ez a helyzet, ha a tranzakciónapló biztonsági mentései másolja egy másik NFS-megosztás, az Azure storage található. Ebben az esetben előfordulhat, hogy kérje le régi tranzakciónaplók biztonsági mentését, hogy az NFS-megosztásból.

A pillanatképek tárolási és a tranzakciós naplók biztonsági másolatainak végleges tárreplikáció kihasználhatják módosítani szeretné a helyet, amelyhez az SAP HANA ír a tranzakciónapló biztonsági mentései. Ezt a módosítást a HANA Studio. Bár az SAP HANA biztonsági mentését teljes log szegmensek automatikusan, meg kell adni a napló biztonsági mentési gyakoriságát, determinisztikus. Ez különösen igaz a vész-helyreállítási beállítások használatakor mivel általában determinisztikus időszakkal a naplóalapú biztonsági mentések végrehajtásához. A következő esetben a napló biztonsági mentési időköz 15 perc vannak beállítva.

![Az SAP HANA Studio bejelentkezik az SAP HANA biztonsági mentés ütemezése](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

15 percenként üzemezésnél gyakoribb biztonsági mentéseket is beállíthatja. Egy gyakoribb beállítást gyakran használják együtt a HANA nagyméretű példányok vész-helyreállítási funkciójának. Egyes ügyfeleink hajtsa végre a tranzakciónapló biztonsági mentései 5 percenként.  

Ha az adatbázis a nem készített biztonsági másolatot, az utolsó lépés az fájlalapú adatbázis biztonsági mentését, hogy hozzon létre egy egyetlen biztonsági mentési bejegyzést, amely a biztonságimásolat-katalógus léteznie kell. SAP HANA ellenkező esetben a megadott naplóalapú biztonsági mentések nem kezdeményezhető.

![Győződjön meg arról, egy fájl alapú biztonsági mentés egyetlen biztonsági mentési bejegyzés létrehozása](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Után az első sikeres tárfiók-pillanatképek többségén futtatva volt, a 6. lépésben végrehajtott teszt pillanatképet törölni kell. Olvasási **"Test pillanatképek eltávolítása – removeTestStorageSnapshot"** a dokumentum [a Microsoft Azure-beli SAP HANA eszközök pillanatfelvétel](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) részleteiről. 


### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>A számát és méretét, a pillanatképek a lemez köteten figyelése

Egy adott tárolási köteten figyelemmel kísérheti a pillanatképek és a tárhelyhasználat ezen pillanatképek számát. A `ls` parancs nem jelenít meg, a pillanatkép könyvtárat vagy fájlokat. Ugyanakkor a Linux operációs rendszer parancs `du` tárolási pillanatképeket, kapcsolatos részleteket jeleníti meg, mert azok az azonos köteteken tárolt. A parancs is használható a következő beállításokkal:

- `du –sh .snapshot`: Ezt a lehetőséget biztosít a pillanatkép-könyvtárban lévő összes pillanatképet összesen.
- `du –sh --max-depth=1`: Ez a beállítás a pillanatképek a mentett sorolja fel a **.snapshot** mappát, és minden pillanatkép mérete.
- `du –hc`: Ezt a lehetőséget biztosít az összes pillanatképet által használt teljes mérete.

Ezek a parancsok használatával győződjön meg arról, hogy az elvégzett és tárolt pillanatképek nem használ fel a köteteken a tárolót.

>[!NOTE]
>A pillanatképek a Boot logikai egység nem láthatók az előző parancsokkal.

### <a name="getting-details-of-snapshots"></a>A pillanatképek részleteinek beolvasása közben
További részletekért a pillanatképeket, használhatja a parancsfájl `azure_hana_snapshot_details`. Ez a szkript az egyik helyen futtatható, ha az aktív kiszolgáló a vész-helyreállítási hely található. A szkript a következő kimenet, minden olyan kötetre, amely tartalmazza a pillanatképek bontásban tartalmazza: 
   * Teljes pillanatképek a kötet mérete
   * Az adott köteten lévő minden egyes pillanatkép a következő adatokat: 
      - Pillanatkép neve 
      - Létrehozás időpontja 
      - Pillanatkép mérete
      - A pillanatkép gyakorisága
      - Ha szükséges, hogy a pillanatkép társított HANA biztonsági mentés azonosítója

A parancs- vagy kimenetként ellenőrzésekor a szintaxis **"listája pillanatkép - azure_hana_snapshot_details"** a dokumentum [a Microsoft Azure-beli SAP HANA eszközök pillanatfelvétel](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 



### <a name="reducing-the-number-of-snapshots-on-a-server"></a>A kiszolgálón a pillanatképek számának csökkentése

Korábban leírtak csökkentheti az egyes címkék tárolt pillanatképek számát. Az utolsó két pillanatkép kezdeményezni a parancs paraméterei a címkét, és meg szeretné őrizni a pillanatképek számát.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

Az előző példában a pillanatkép-címke van **dailyhana** , és meg kell őrizni, ezzel a címkével ellátott pillanatképek számát **28**. Lemezterület-felhasználást válaszolni, előfordulhat, hogy szeretne tárolt pillanatképek számának csökkentése érdekében. A pillanatképek számát csökkentheti a 15., például az egyszerű módot beállítva az utolsó paraméterrel a parancsfájl futtatásához **15**:

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

A parancsfájl futtatja ezt a beállítást, ha a pillanatképeket, beleértve az új tároló pillanatképe szám 15. Őrzi meg a 15 legutóbbi pillanatképeket, és a 15 régebbi pillanatképeket a rendszer törli.

 >[!NOTE]
 > Ez a szkript csökkenti a pillanatképek számát, csak akkor, ha több mint 1 óra régi pillanatképek vannak. A parancsfájl nem törli a pillanatképeket, amelyek 1 órával korábbiak. Ezek a korlátozások kapcsolódó érhető el a nem kötelező vészhelyreállítási funkciók.

Ha már nem szeretné a biztonsági mentési előtaggal pillanatképek készletét karbantartása **dailyhana** a szintaxis példák a parancsfájlt futtathat **0** a megőrzési számát. A Label értékeként megfelelő összes pillanatképet majd el lesznek távolítva. Azonban minden pillanatkép eltávolítása hatással lehet a HANA nagyméretű példányok vészhelyreállítási funkciók képességeit.

Adott pillanatképek törlése egy második lehetőség egy parancsfájl `azure_hana_snapshot_delete`. Ez a szkript készült egy pillanatkép vagy vagy formában, HANA Studio vagy a pillanatkép neve magát a HANA biztonsági másolat azonosítója használatával a pillanatképek törlése. Jelenleg a biztonsági másolat azonosítója csak vannak kötve, a készült pillanatkép a **hana** pillanatkép típusa. Pillanatkép-típusú biztonsági mentések **naplók** és **rendszerindító** nem hajt végre egy SAP HANA pillanatképe és így nem található pillanatképeket, nincs biztonsági mentési azonosító. Ha a pillanatkép neve van megadva, a különböző köteteken, amelyek megfelelnek a megadott pillanatkép neve minden pillanatkép keres. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

A parancsfájl a részletekért tekintse meg **"Pillanatkép - azure_hana_snapshot_delete törlése"** a dokumentum [a Microsoft Azure-beli SAP HANA eszközök pillanatfelvétel](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Hajtsa végre a parancsfájl felhasználóként **legfelső szintű**.

>[!IMPORTANT]
>Ha csak a pillanatkép a meglévő adatot törölni, a pillanatkép törlését követően, hogy adat elvész örökre.

  
## <a name="file-level-restore-from-a-storage-snapshot"></a>Fájlszintű visszaállítás tárolási pillanatképből

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
A pillanatkép-típusok **hana** és **naplók**, közvetlenül a köteteken a pillanatképeket is elérheti a **.snapshot** könyvtár. A pillanatképek minden alkönyvtár van. Minden egyes fájl másolhat az állapotba, amelyben a pillanatkép, az adott alkönyvtárban azokat a tényleges könyvtárstruktúrát ponton kell megadni. A parancsfájl jelenlegi verziójában, nincs **nem** visszaállítása (bár a pillanatkép-visszaállítás végezhető, mint a feladatátvétel során a DR helyen parancsprogramjait a önkiszolgáló DR része) egy önkiszolgáló, a pillanatkép-visszaállítás a megadott szkriptet. Nyissa meg a visszaállítani kívánt pillanatkép a meglévő elérhető pillanatképekből szolgáltatáskérés kapcsolatba kell lépnie a Microsoft üzemeltetési csapat.

>[!NOTE]
>Egyetlen fájl visszaállítása nem működik a rendszerindítás független a nagyméretű HANA-példány mértékegység típusát a logikai egység pillanatkép-készítési. A **.snapshot** könyvtár nem érhető el a rendszerindítás LUN-t. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>A helyreállítás a legfrissebb HANA pillanatképe

Egy éles válassza ki a forgatókönyvben egy tárolási pillanatképből helyreállításának folyamatán, egy ügyfél eseményt a Microsoft Azure támogatási szolgálatával kezdeményezhetők. A magas sürgősségű függetlenül attól, hogy, akkor, ha adatokat törölte a termelési rendszer, és az egyetlen módszer is lekérheti azt, hogy az éles adatbázis visszaállításához.

Egy másik esetben a időponthoz helyreállítás lehet, hogy alacsony sürgősség, és a tervezett nappal. Megtervezheti a helyreállítás, az SAP HANA az Azure-ban egy magas prioritású jelző előléptetése helyett. Például előfordulhat, hogy lehet tervezi az SAP-szoftverek frissítése a fejlesztés új csomag alkalmazásával. Kell visszaállítania egy pillanatkép, amely előtt a fejlesztés Csomagfrissítés állapotát jelöli.

Mielőtt elküldi a kérelmet, kell előkészíteni. Az SAP HANA az Azure-csapat majd kezelni a kérést, és adja meg a visszaállított kötetek. Ezután állítsa vissza a HANA-adatbázis, a pillanatképek alapján.

Az új eszközzel együtt visszaállított pillanatképet fel a lehetőségeket vannak dokumentálva szakaszban **"pillanatkép visszaállítása című"** a dokumentum [manuális helyreállítási útmutató az SAP HANA az Azure Storage-pillanatkép](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf).

A következő bemutatja, hogyan készülhet fel a kérelmet:

1. Döntse el, melyik pillanatkép visszaállítása. Csak a hana/adatmennyiség helyreáll, kivéve, ha más módon utasíthatja. 

1. Állítsa le a HANA-példány.

   ![A HANA-példány leállítása](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Válassza le az adatkötetek a HANA-adatbázis csomópontokon. Ha az adatkötetek továbbra is csatlakoztatva vannak az operációs rendszer, a pillanatkép visszaállítása sikertelen lesz.
   ![Válassza le az adatkötetek a HANA-adatbázis csomópontokon](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Nyisson meg egy Azure-támogatási kérést, és a visszaállítást egy adott pillanatkép kapcsolatos utasításokat is tartalmazzák.

   - A visszaállítás: alatt Azure-beli SAP HANA fel, hogy vegyen részt egy konferenciahívás koordinálása, ellenőrzése és megerősítése, hogy a helyes tárfiók-pillanatkép visszaállítása biztosítása érdekében. 

   - Miután a visszaállítás: SAP HANA az Azure Service értesíti, amikor a storage-pillanatkép-visszaállítás.

1. A visszaállítási folyamat befejezése után csatlakoztassa újra az adatköteteket.

   ![Minden adatkötetnél újracsatlakoztathatja](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



Az első, például SAP HANA-adatfájlok állt helyre egy storage-pillanatkép egy másik lehetőség a 7. lépésben a dokumentum dokumentált [manuális helyreállítási útmutató az SAP HANA az Azure Storage-pillanatkép](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf).

A dokumentum [manuális helyreállítási útmutató az SAP HANA az Azure Storage-pillanatkép](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf) mutatja be a visszaállítási menetet pillanatkép biztonsági másolatból. A visszaállítás végrehajtása használja a dokumentációt. 

>[!Note]
>7. lépés nincs szükség hajtható végre, ha a pillanatkép visszaállítása a Microsoft műveletek által kapott.


### <a name="recover-to-another-point-in-time"></a>Helyreállítása egy másik időpontra állapotra
A dokumentum [manuális helyreállítási útmutató az SAP HANA az Azure Storage-pillanatkép](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf) mutatja be a visszaállítási menetet, hogy egy bizonyos ponton időben szakaszában **'Állítsa helyre az adatbázist a következő pontra időben'**. Használja a dokumentációt időben egy bizonyos ponton a visszaállítás végrehajtása. 


## <a name="next-steps"></a>További lépések
- Lásd: [vész-helyreállítási alapelvek és -előkészítés](hana-concept-preparation.md).
