---
title: HANA biztonsági mentése és visszaállítása az SAP HANA-n az Azure-on (nagy példányok) | Microsoft dokumentumok
description: Hana biztonsági mentés és visszaállítás végrehajtása az SAP HANA-n az Azure-ban (nagy példányok)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4384d29811d29f06422802abba5d3eb1ea5737e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72430075"
---
# <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás

>[!IMPORTANT]
>Ez a cikk nem helyettesíti az SAP HANA felügyeleti dokumentációt vagy az SAP Notes. Azt várjuk, hogy az SAP HANA felügyeletének és műveleteinek alapos megértésével és szakértelmével rendelkezik, különösen a biztonsági mentés, a visszaállítás, a magas rendelkezésre állás és a vészhelyreállítás terén. Ebben a cikkben az SAP HANA Studio képernyőképei jelennek meg. Tartalom, struktúra, és az SAP felügyeleti eszközök képernyőinek és maguk az eszközök jellege is változhat az SAP HANA kiadás kiadására.

Fontos, hogy gyakorolja a környezetben és a HANA verziók és kiadások során végrehajtott lépéseket és folyamatokat. Az ebben a cikkben ismertetett egyes folyamatok egyszerűbbek a jobb általános megértés érdekében. Ezek nem arra szolgálnak, hogy az esetleges műveleti kézikönyvek részletes lépéseiként használhatók legyenek. Ha műveleti kézikönyveket szeretne létrehozni a konfigurációkhoz, tesztelje és gyakorolja a folyamatokat, és dokumentálja az adott konfigurációkhoz kapcsolódó folyamatokat. 

Az egyik legfontosabb szempontja az operációs adatbázisok, hogy megvédje őket a katasztrofális események. Ezeknek az eseményeknek az oka a természeti katasztrófáktól az egyszerű felhasználói hibákig bármi lehet.

Az adatbázis biztonsági mentése, amely lehetővé teszi, hogy visszaállítsa azt bármely időpontban, például mielőtt valaki kritikus adatokat törölt, lehetővé teszi a helyreállítást egy olyan állapotba, amely a lehető legközelebb áll a megszakítás előtti állapothoz.

Kétféle biztonsági mentést kell végrehajtani a visszaállítási képesség eléréséhez:

- Adatbázis-biztonsági mentések: Teljes, növekményes vagy különbözeti biztonsági mentések
- Tranzakciós napló biztonsági mentései

Az alkalmazás szintjén végrehajtott teljes adatbázis-biztonsági mentések mellett a tárolási pillanatképeket is készíthet biztonsági mentéseket. A tárolási pillanatképek nem helyettesítik a tranzakciónapló biztonsági mentését. A tranzakciós napló biztonsági mentései továbbra is fontosak az adatbázis egy adott időpontra való visszaállításához vagy a már véglegesített tranzakciók naplóinak kiürítéséhez. A tárolási pillanatképek felgyorsíthatják a helyreállítást azáltal, hogy gyorsan biztosítják az adatbázis előregörgetéses lemezképét. 

Az SAP HANA az Azure-ban (nagy példányok) két biztonsági mentési és visszaállítási lehetőséget kínál:

- **Csináld magad (DIY).** Miután meggyőződhet arról, hogy elegendő lemezterület áll rendelkezésre, hajtsa végre a teljes adatbázis-biztonsági mentést és a biztonsági mentéseket az alábbi lemezbiztonsági mentési módszerek egyikével. Biztonsági másolatot lehet vagy közvetlenül a hana nagy példány egységek vagy NFS-megosztások, amelyek egy Azure virtuális gép (VM) vannak beállítva. Az utóbbi esetben az ügyfelek egy Linux virtuális gép az Azure-ban, csatolja az Azure Storage-t a virtuális géphez, és ossza meg a tárolót egy konfigurált NFS-kiszolgálón keresztül az adott virtuális gép. Ha a biztonsági mentést a kötetek, amelyek közvetlenül a HANA nagy példány egységek, másolja a biztonsági mentéseket egy Azure storage-fiókba. Ezt az Azure-beli virtuális gép beállítása után tegye, amely az Azure Storage-on alapuló NFS-megosztásokat exportálja. Azure Backup-tárolót vagy Azure-hűtőtárolót is használhat. 

   Egy másik lehetőség egy külső adatvédelmi eszköz használata a biztonsági mentések tárolására, miután egy Azure-tárfiókba másolt. A barkácsolási biztonsági mentési lehetőség is szükséges lehet az adatok, amelyeket hosszabb ideig kell tárolni a megfelelőségi és naplózási célokra. A biztonsági mentések minden esetben a virtuális gépen és az Azure Storage-on keresztül képviselt NFS-megosztásokba kerülnek.

- **Infrastruktúra biztonsági mentése és visszaállítása.** Használhatja a biztonsági mentési és visszaállítási funkciót, amely az SAP HANA az Azure-beli (nagy példányok) alapul szolgáló infrastruktúrája. Ez a beállítás kielégíti a biztonsági mentések és a gyors visszaállítás okainak szükségességét. A szakasz további célja a biztonsági mentési és visszaállítási funkció, amely a HANA nagy példányok. Ez a szakasz azt is ismerteti, hogy a biztonsági mentés és visszaállítás a HANA nagy példányok által kínált vész-helyreállítási funkciókat is ismerteti.

> [!NOTE]
>   A hana nagy példányok mögöttes infrastruktúrája által használt pillanatkép-technológia az SAP HANA-pillanatképektől függ. Ezen a ponton az SAP HANA-pillanatképek nem működnek együtt az SAP HANA több-bérlős adatbázis-tárolók több bérlőjével. Ha csak egy bérlő van telepítve, az SAP HANA-pillanatképek működnek, és használhatja ezt a módszert.

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Sap HANA tárolási pillanatképeinek használata az Azure-ban (nagy példányok)

Az Azure-beli SAP HANA (Nagy példányok) alapjául szolgáló tárolási infrastruktúra támogatja a kötetek tárolási pillanatképeit. A kötetek biztonsági mentése és visszaállítása egyaránt támogatott, a következő szempontokat követve:

- A teljes adatbázis-biztonsági mentések helyett a tárolókötetek pillanatképei gyakran készülnek.
- Amikor egy pillanatkép aktiválódik a /hana/data és a /hana/shared, amely magában foglalja a /usr/sap, kötetek, a pillanatkép-technológia kezdeményezegy SAP HANA pillanatképet, mielőtt futtatja a tárolási pillanatképet. Ez az SAP HANA pillanatkép a tárolási pillanatkép helyreállítása után a végleges napló-visszaállítások beállítási pontja. Ahhoz, hogy egy HANA-pillanatkép sikeres legyen, egy aktív HANA-példányra van szükség. HSR-forgatókönyv esetén a tárolási pillanatkép nem támogatott egy aktuális másodlagos csomóponton, ahol a HANA-pillanatkép nem hajtható végre.
- A tárolási pillanatkép sikeres futtatása után az SAP HANA-pillanatkép törlődik.
- A tranzakciós napló biztonsági mentéseit gyakran készítik és tárolják a /hana/logbackups köteten vagy az Azure-ban. A /hana/logbackups kötetet, amely tartalmazza a tranzakciónapló biztonsági mentéseit, külön készíthet pillanatképet. Ebben az esetben nem kell futtatnia egy HANA pillanatképet.
- Ha egy éles környezetben kimaradás esetén vissza kell állítania egy adatbázist egy adott időpontra, kérje meg, hogy a Microsoft Azure-támogatás vagy az SAP HANA az Azure-ban egy bizonyos tárolási pillanatképre álljon vissza. Erre példa a homokozó rendszer eredeti állapotának tervezett helyreállítása.
- Az SAP HANA pillanatkép, amely a tárolási pillanatkép egy eltolási pont a tranzakciónapló biztonsági mentések, amelyek futottak, és a tárolási pillanatkép készítése után tárolt.
- Ezek a tranzakciós napló biztonsági mentései az adatbázis egy adott időpontra való visszaállításához kerülnek visszaállításra.

A kötetek három osztályát megcélzó tárolási pillanatképeket hajthat végre:

- Kombinált pillanatkép a /hana/data és a /hana/shared felett, amely tartalmazza a /usr/sap kapcsolót. Ez a pillanatkép egy SAP HANA-pillanatkép létrehozásához szükséges a tárolási pillanatkép előkészítéseként. Az SAP HANA pillanatkép biztosítja, hogy az adatbázis egy tárolószempontjából konzisztens állapotban van. A visszaállítási folyamathoz ez egy olyan pont, amelyet be kell állítani.
- Külön pillanatkép a /hana/logbackups felett.
- Operációs rendszer partíciója.

A legújabb pillanatkép-parancsfájlok és dokumentáció beolvassa a [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)című témakört. Amikor letölti a pillanatkép parancsfájlcsomagot a [GitHubról,](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md)három fájlt kap. Az egyik fájl pdf-ben van dokumentálva a rendelkezésre álló funkciókhoz. Az eszközkészlet letöltése után kövesse a pillanatkép-eszközök beszerezni című útmutató utasításait.

## <a name="storage-snapshot-considerations"></a>A tárolás pillanatképének szempontjai

>[!NOTE]
>A tárolási pillanatképek a HANA nagy példányegységei számára lefoglalt tárhelyet foglalnak el. Vegye figyelembe a tárolási pillanatképek ütemezésének alábbi szempontjait és a tárolás pillanatképeinek megtartását. 

Az Azure-beli SAP HANA (nagy példányok) tárolási pillanatképeinek speciális mechanikája a következők:

- Egy adott tárolási pillanatkép abban az időpontban, amikor a készítés e nem használ fel kevés tárhelyet.
- Ahogy az adattartalom megváltozik, és az SAP HANA adatfájlok tartalma megváltozik a tárolóköteten, a pillanatképnek tárolnia kell az eredeti blokktartalmat, és az adatok módosulnak.
- Ennek eredményeképpen a tárolási pillanatkép mérete nő. Minél hosszabb a pillanatkép létezik, annál nagyobb lesz a tárolási pillanatkép.
- Minél több módosítást hajt végre az SAP HANA adatbáziskötete egy tárolási pillanatkép teljes élettartama alatt, annál nagyobb a tárolási pillanatkép helyfelhasználása.

SAP HANA az Azure-ban (nagy példányok) az SAP HANA-adatok és naplókötetek rögzített kötetméreteket tartalmaz. Az ilyen kötetekről készített pillanatképek végrehajtása bekerül a kötetterületére. A következőket kell:

- Határozza meg, hogy mikor kell ütemezni a tárolási pillanatképeket.
- A tárolókötetek helyfelhasználásának figyelése. 
- A tárolt pillanatképek számának kezelése. 

Letilthatja a tárolási pillanatképeket, ha adatok tömegét importálja, vagy más jelentős módosításokat hajt végre a HANA-adatbázisban. 


A következő szakaszok a pillanatképek végrehajtásával kapcsolatos információkat tartalmaznak, és általános javaslatokat tartalmaznak:

- Bár a hardver képes fenntartani 255 pillanatképek kötetenként, szeretné, hogy jóval ez alatt a szám alatt maradjon. Az ajánlás leg250 vagy kevesebb.
- A tárhelypillanatképek végrehajtása előtt figyelje és nyomon kövesse a szabad területet.
- Csökkentse a tárhelypillanatképek számát a szabad terület alapján. Csökkentheti a megtartani kívánt pillanatképek számát, vagy kiterjesztheti a köteteket. További tárhelyet 1 terabájtos egységekben rendelhet.
- Olyan tevékenységek során, mint például az SAP HANA-ba való áthelyezése az SAP platformáttelepítési eszközeivel (R3load) vagy az SAP HANA-adatbázisok biztonsági mentésekből való visszaállítása során tiltsa le a tárolási pillanatképeket a /hana/data köteten. 
- Az SAP HANA-táblák nagyobb átszervezések során kerülje a tárolási pillanatképeket, ha lehetséges.
- A tárolási pillanatképek előfeltételei az SAP HANA vész-helyreállítási képességeinek az Azure-ban (nagy példányok) kihasználásához.

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Az önkiszolgáló tárolási pillanatképek használatának előfeltételei

Győződjön meg arról, hogy a pillanatkép-parancsfájl sikeresen fut, győződjön meg arról, hogy perl telepítve van a Linux operációs rendszer a HANA nagy példányok kiszolgálón. Perl jön előre telepítve a HANA nagy példány egység. A Perl-verzió ellenőrzéséhez használja a következő parancsot:

`perl -v`

![A nyilvános kulcsot a parancs futtatásával másolja a rendszer](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>Tárolási pillanatképek beállítása

A tárolópillanatképek beállítása hana nagy példányok, kövesse az alábbi lépéseket.
1. Győződjön meg arról, hogy a Perl telepítve van a Linux operációs rendszeren a HANA nagy példányok kiszolgálón.
1. Módosítsa az /etc/ssh/ssh\_config kapcsolót a _Hmac-sha1_vonal hozzáadásához.
1. Hozzon létre egy SAP HANA biztonsági mentési felhasználói fiókot a fő csomóponton minden egyes futtatott SAP HANA-példányhoz, ha van ilyen.
1. Telepítse az SAP HANA HDB-ügyfelet az összes SAP HANA nagy példánykiszolgálóra.
1. Az egyes régiók első SAP HANA nagy példányai kiszolgálóján hozzon létre egy nyilvános kulcsot az alapul szolgáló tárolási infrastruktúra eléréséhez, amely a pillanatkép létrehozását szabályozza.
1. Másolja a parancsfájlokat és a konfigurációs fájlt a [GitHubról](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) a **hdbsql** helyére az SAP HANA telepítésben.
1. Módosítsa a *HANABackupDetails.txt* fájlt, ha szükséges a megfelelő vevői specifikációkhoz.

A legújabb pillanatkép-parancsfájlok és dokumentáció beszerezhető a [GitHubról.](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) A korábban felsorolt lépésekről az [Azure-beli SAP HANA Microsoft pillanatkép-eszközei című témakörben olvashat.](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)

### <a name="consideration-for-mcod-scenarios"></a>Az MCOD-forgatókönyvek megfontolása
Ha egy [MCOD-forgatókönyvet](https://launchpad.support.sap.com/#/notes/1681092) több SAP HANA-példányok egy HANA nagy példány egységen futtat, az SAP HANA-példányok hoz létre külön tárolási kötetek. Az MDC-ről és egyéb szempontokról az [Azure-beli SAP HANA Microsoft-pillanatkép-eszközeiben](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)található "Fontos tudnivalók" című témakörben olvashat bővebben.
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>1. lépés: Telepítse az SAP HANA HDB klienst

Az Azure-beli SAP HANA-ra telepített Linux operációs rendszer tartalmazza az SAP HANA-tárolási pillanatképek biztonsági mentési és vész-helyreállítási célokra szükséges mappákat és parancsfájlokat. Ellenőrizze, hogy vannak-e újabb kiadások a [GitHubon.](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) 

Az Ön felelőssége, hogy telepítse az SAP HANA HDB-ügyfél a HANA nagy példány egységek telepítése közben AZ SAP HANA telepítése közben.

### <a name="step-2-change-the-etcsshssh_config"></a>2. lépés: Az /etc/ssh/ssh\_config módosítása

Ezt a lépést az [Azure-beli SAP HANA Microsoft pillanatkép-eszközeinek](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)"Kommunikáció engedélyezése a tármivel" című ismertetése című ismertetése ismerteti.


### <a name="step-3-create-a-public-key"></a>3. lépés: Nyilvános kulcs létrehozása

A hana nagypéldány-bérlő tárolópillanatkép-felületeinek eléréséhez való hozzáférés engedélyezéséhez hozzon létre egy bejelentkezési eljárást egy nyilvános kulcson keresztül. 

Az első SAP HANA az Azure (nagy példányok) kiszolgálóa a bérlőben, hozzon létre egy nyilvános kulcsot a tárolási infrastruktúra eléréséhez. Nyilvános kulccsal nem szükséges jelszó a tárolási pillanatkép-felületekre való bejelentkezéshez. Emellett nem kell nyilvános kulccsal karbantartania a jelszóhitelesítő adatokat. 

Nyilvános kulcs létrehozásához olvassa el a "Kommunikáció engedélyezése a tárterülettel" című témakört az [Azure-beli SAP HANA Microsoft pillanatkép-eszközeiben.](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)


### <a name="step-4-create-an-sap-hana-user-account"></a>4. lépés: SAP HANA felhasználói fiók létrehozása

Az SAP HANA-pillanatképek létrehozásához hozzon létre egy felhasználói fiókot az SAP HANA,amely a storage snapshot parancsfájlok használható. Ehhez a célból hozzon létre egy SAP HANA felhasználói fiókot az SAP HANA Studio-n belül. A felhasználót a SYSTEMDB alatt kell *létrehozni, nem* pedig az MDC SID-adatbázisa alatt. Az egytárolós környezetben a felhasználó jön létre a bérlői adatbázisban. Ennek a fióknak **biztonsági mentési rendszergazdai** és **katalógusolvasási** jogosultságokkal kell rendelkeznie. 

Felhasználói fiók beállításához és használatához olvassa el a "Kommunikáció engedélyezése az SAP HANA-val" című témakört a [GitHubon.](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)


### <a name="step-5-authorize-the-sap-hana-user-account"></a>5. lépés: Az SAP HANA felhasználói fiók engedélyezése

Ebben a lépésben engedélyezi az SAP HANA felhasználói fiókot, amelyet azért hozott létre, hogy a parancsfájlok nem kell jelszavakat küldeni e futásidőben. Az SAP HANA parancs `hdbuserstore` lehetővé teszi egy SAP HANA felhasználói kulcs létrehozását. A kulcs egy vagy több SAP HANA-csomóponton tárolódik. A felhasználói kulcs lehetővé teszi, hogy a felhasználó anélkül férhozzá az SAP HANA-hoz, hogy a parancsfájl-kezelési folyamaton belül kezelnie kellene a jelszavakat. A parancsfájlok futtatásának folyamatát a cikk későbbi részében tárgyaljuk.

>[!IMPORTANT]
>Futtassa ezeket a konfigurációs parancsokat ugyanazzal a felhasználói környezettel, amelyben a pillanatkép-parancsok futnak. Ellenkező esetben a pillanatképparancsok nem fognak megfelelően működni.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>6. lépés: A pillanatkép-parancsfájlok beszerezni, konfigurálni a pillanatképeket, és tesztelni a konfigurációt és a kapcsolatot

Töltse le a szkriptek legújabb verzióját a [GitHubról.](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.1) A parancsfájlok telepítésének módja megváltozott a parancsfájlok 4.1-es kiadásával. További információ: "Kommunikáció engedélyezése az SAP HANA-val" című témakörben az [Azure-beli SAP HANA Microsoft pillanatkép-eszközeiben](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)olvashat.

A parancsok pontos sorrendjét az [Azure-beli SAP HANA Microsoft pillanatkép-eszközeinek](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)"Pillanatkép-eszközök egyszerű telepítése (alapértelmezett)" című részében olvashat. Javasoljuk az alapértelmezett telepítés használatát. 

A 3.x-es verzióról a 4.1-es verzióra való frissítésről az [Azure-beli SAP HANA Microsoft pillanatkép-eszközeinek](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)"Meglévő telepítés frissítése" című témakörében olvashat. A 4.1-es eszközkészlet eltávolításához olvassa el a "Pillanatkép-eszközök eltávolítása" című témakört az [Azure SAP HANA Microsoft-pillanatkép-eszközeiben.](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)

Ne felejtse el futtatni a "Pillanatkép-eszközök teljes beállítása" című, az [Azure-beli SAP HANA-eszközkép-eszközök](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)"Pillanatkép-eszközök teljes beállítása" című részében ismertetett lépéseket.

A különböző parancsfájlok és fájlok célja a telepített fájlok nak leírása a "Mik ezek a pillanatkép-eszközök?" című részben. a [Microsoft pillanatkép-eszközei az SAP HANA azure-ban.](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)

A pillanatkép-eszközök konfigurálása előtt győződjön meg arról, hogy a HANA biztonsági mentési helyeket és beállításokat is megfelelően konfigurálta. További információ: "SAP HANA-konfiguráció" a [Microsoft pillanatkép-eszközei az Sap HANA az Azure-ban.](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)

A pillanatkép-eszközkészlet konfigurációját az [Azure SAP HANA microsoftos pillanatkép-eszközeinek](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)"Config file - HANABackupCustomerDetails.txt" című részében ismertetik.

#### <a name="test-connectivity-with-sap-hana"></a>Kapcsolat tesztelése az SAP HANA-val

Miután az összes konfigurációs adatot a *HANABackupCustomerDetails.txt* fájlba helyezte, ellenőrizze, hogy a konfigurációk megfelelőek-e a HANA-példány adataihoz. Használja a `testHANAConnection`parancsfájlt, amely független az SAP HANA horizontális felskálázási vagy horizontális felskálázási konfigurációjától.

További információ: "Ellenőrizze a kapcsolatot az SAP HANA - testHANAConnection" a [Microsoft snapshot eszközök SAP HANA az Azure-ban.](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)

#### <a name="test-storage-connectivity"></a>Tárolókapcsolat tesztelése

A következő teszt lépés a *hanabackupcustomerdetails.txt* konfigurációs fájlba helyezett adatok alapján ellenőrizze a tárolóhoz való kapcsolódást. Ezután futtasson egy tesztpillanatképet. A parancs `azure_hana_backup` futtatása előtt el kell futtatnia ezt a tesztet. A teszt parancssorozatát az [Azure-beli SAP HANA Microsoft pillanatkép-eszközeinek](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)"A kapcsolat ellenőrzése a tárterülettel – testStorageSnapshotConnection" című részében olvashat.

Sikeres bejelentkezés után a tároló virtuálisgép-összeköttetések, a parancsfájl folytatódik a 2. A kimenet itt látható az SAP HANA háromcsomópontos horizontális felskálázási konfigurációjához.

Ha a teszt pillanatkép sikeresen fut a parancsfájllal, ütemezheti a tényleges tárolási pillanatképek. Ha nem sikerül, vizsgálja meg a problémákat, mielőtt továbblépne. A teszt pillanatkép kell maradnia, amíg az első valós pillanatképek történik.


### <a name="step-7-perform-snapshots"></a>7. lépés: Pillanatképek végrehajtása

Amikor az előkészítési lépések befejeződtek, elkezdheti konfigurálni és ütemezni a tényleges tárolási pillanatképeket. Az ütemezendő parancsfájl sap HANA horizontális felskálázási és horizontális felskálázási konfigurációkkal működik. A biztonsági másolat parancsfájljának rendszeres és rendszeres végrehajtásához ütemezze a parancsfájlt a cron segédprogrammal. 

A pontos parancsszintaxist és funkcionalitást a "Pillanatkép biztonsági mentése – azure_hana_backup" című témakörben az [Azure-beli SAP HANA Microsoft pillanatkép-eszközeiben](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)olvashat. 

A parancsfájl `azure_hana_backup` futtatásakor a következő három fázisban hozza létre a tárolási pillanatképet:

1. Egy SAP HANA pillanatképet futtat.
1. Egy tárolási pillanatképet futtat.
1. Eltávolítja az SAP HANA pillanatképet, amely a tárolási pillanatkép lefutotta.

A parancsfájl futtatásához hívja meg a HDB végrehajtható mappából, amelybe másolta. 

A megőrzési időszak a parancsfájl futtatásakor paraméterként elküldött pillanatképek számával van feladva. A tárolási pillanatképek által lefedett idő a végrehajtás időszakának függvénye, és a parancsfájl futtatásakor paraméterként elküldött pillanatképek száma. 

Ha a megőrzött pillanatképek száma meghaladja a parancsfájl hívásában paraméterként megnevezett számot, az új pillanatkép futtatása előtt törlődik az azonos címke legrégebbi tárolási pillanatképe. A hívás utolsó paramétereként megadott szám az a szám, amelya megőrzött pillanatképek számának szabályozására szolgál. Ezzel a számmal közvetve szabályozhatja a pillanatképekhez használt lemezterületet is. 


## <a name="snapshot-strategies"></a>Pillanatkép stratégiák
A különböző típusú pillanatképek gyakorisága attól függ, hogy használja-e a HANA nagy példány vész-helyreállítási funkciót. Ez a funkció a tárolási pillanatképekre támaszkodik, amelyek speciális javaslatokat igényelhetnek a tárolási pillanatképek gyakoriságához és végrehajtási időszakaihoz. 

Az alábbi szempontok és javaslatok, a feltételezés az, hogy *nem* használja a vész-helyreállítási funkciót, amely hana nagy példányok kínál. Ehelyett a tárolási pillanatképek segítségével biztonsági mentéseket, és képes biztosítani a pont-in-time helyreállításaz elmúlt 30 napban. Tekintettel a pillanatképek és a tárhely számának korlátaira, vegye figyelembe a következő követelményeket:

- Az időponthoz és időponthoz való helyreállítás helyreállítási ideje.
- A felhasznált hely.
- A helyreállítási pont és a helyreállítási idő célkitűzései a katasztrófa utáni lehetséges helyreállításhoz.
- A HANA teljes adatbázis-biztonsági mentések végleges végrehajtása lemezeken. Amikor a lemezeken vagy a **backint-csatolón** teljes adatbázis-biztonsági mentés történik, a tárolási pillanatképek végrehajtása sikertelen lesz. Ha azt tervezi, hogy teljes adatbázis-biztonsági mentések a tárolási pillanatképek, győződjön meg arról, hogy a tárolási pillanatképek végrehajtása le van tiltva ez idő alatt.
- A kötetenkénti pillanatképek száma, amely 250-re korlátozódik.

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

Ha nem használja a vész-helyreállítási funkciót a HANA nagy példányok, a pillanatkép időszak a kevésbé gyakori. Ilyen esetekben hajtsa végre a kombinált pillanatképeket a /hana/data és a /hana/shared kapcsolón, amely tartalmazza a /usr/sap kapcsolót 12 órás vagy 24 órás időszakokban. Tartsa meg a pillanatképeket egy hónapig. Ugyanez igaz a napló biztonsági mentési kötetének pillanatképeire is. Az SAP HANA tranzakciós napló biztonsági mentési biztonsági mentések végrehajtása a napló biztonsági mentési kötete 5 perc és 15 perc között történik.

Az ütemezett tárolási pillanatképek a cron használatával végezhető el a legjobban. Használja ugyanazt a parancsfájlt az összes biztonsági mentéshez és a vész-helyreállítási igényekhez. Módosítsa a parancsfájlbemeneteket úgy, hogy azok megfeleljenek a különböző kért biztonsági mentési időknek. Ezek a pillanatképek a végrehajtási időtől függően különböző ütemezve vannak cron-ban. Ez lehet óránként, 12 óránként, naponta vagy hetente. 

A következő példa az /etc/crontab-ban mutatja be a cron ütemezést:
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
Az előző példában egy óránkénti kombinált pillanatkép a /hana/data és a /hana/shared/SID azonosítót tartalmazó köteteket fedi le, amely tartalmazza a /usr/sap helyeket. Az ilyen típusú pillanatkép az elmúlt két nap ban gyorsabb időponthoz való helyreállításhoz használható. Van is egy napi pillanatkép ezeket a köteteket. Így két nap lefedettség óránkénti pillanatképek és négy hét lefedettség napi pillanatképek. A tranzakciónapló biztonsági mentési kötetéről is készül naponta biztonsági másolat. Ezeket a biztonsági mentéseket négy hétig őrzik. 

Amint a crontab harmadik sorában látható, a HANA tranzakciós napló biztonsági mentése 5 percenként fut. A tárolási pillanatképeket futtató különböző cron-feladatok kezdési időpontjai lépcsőzetesen vannak elévülve. Ily módon a pillanatképek nem futnak egyszerre egy bizonyos időpontban. 

A következő példában egy kombinált pillanatképet hajt végre, amely a /hana/data és a /hana/shared/SID azonosítót tartalmazó köteteket fedi le, amely óránként tartalmazza a /usr/sap helyeket. Ezeket a pillanatképeket két napig őrizheti. A tranzakciónapló biztonsági mentési köteteinek pillanatképei 5 perces alapon futnak, és négy órán keresztül vannak tárolva. Mint korábban, a HANA tranzakciós naplófájl biztonsági mentése 5 percenként fut. 

A tranzakciónapló biztonsági mentési kötetének pillanatképe 2 perces késleltetéssel történik a tranzakciónapló biztonsági mentésének megkezdése után. Normál körülmények között az SAP HANA tranzakciónapló biztonsági mentése 2 percen belül befejeződik. Mint korábban, a rendszerindító főegységbeli felszolgálást tartalmazó kötetről naponta egyszer biztonsági másolatot készíthet egy tárolási pillanatkép, és négy hétig őrzi meg.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

Az alábbi ábra az előző példa sorozatait mutatja be. A rendszerindító lun ki van zárva.

![A biztonsági mentések és a pillanatképek közötti kapcsolat](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

Az SAP HANA rendszeres írásokat hajt végre a /hana/log köteten az adatbázis véglegesített módosítási módosítási tevékenységeinek dokumentálása érdekében. Rendszeresen SAP HANA ír egy mentési pontot a /hana/data kötetre. A crontab ban megadottak szerint az SAP HANA tranzakciónapló biztonsági mentése 5 percenként fut. 

Azt is láthatja, hogy egy SAP HANA pillanatkép óránként fut a /hana/data és a /hana/shared/SID kötetek kombinált tárolási pillanatkép aktiválása miatt. Miután a HANA-pillanatkép sikeres, a kombinált tárolási pillanatkép fut. A crontab utasításoknak megfelelően a /hana/logbackup köteten lévő tárolási pillanatkép 5 percenként fut, körülbelül 2 perccel a HANA tranzakciónapló biztonsági mentése után.

> 

>[!IMPORTANT]
> Az SAP HANA biztonsági mentések tárolási pillanatképek használata csak akkor értékes, ha a pillanatképek az SAP HANA tranzakciós napló biztonsági mentések együtt történik. Ezek a tranzakciós napló biztonsági mentések kell a tárolási pillanatképek közötti időszakokra. 

Ha 30 napos időponthoz és időpontban helyreállításához a felhasználók felé vállalt kötelezettséget állított be, a következőket kell a következőkre:

- A /hana/data és a /hana/shared/SID kombinált tárolási pillanatkép elérése, amely szélsőséges esetekben 30 napos. 
- Folytonos tranzakciónapló-biztonsági másolatokkal rendelkezik, amelyek lefedik a kombinált tárolási pillanatképek közötti időt. Így a tranzakciónapló biztonsági mentési kötetének legrégebbi pillanatképének 30 naposnak kell lennie. Nem ez a helyzet, ha a tranzakciós napló biztonsági mentéseit egy másik NFS-megosztásra másolja, amely az Azure Storage-ban található. Ebben az esetben előfordulhat, hogy régi tranzakciónapló-biztonsági mentéseket kér le az NFS-megosztásról.

A tárolási pillanatképek és a tranzakciónapló biztonsági másolatainak esetleges tárolása érdekében módosítsa azt a helyet, amelyre az SAP HANA írja a tranzakciónapló biztonsági mentését. Ezt a módosítást a HANA Studio-ban is elérheti. 

Bár az SAP HANA automatikusan biztonsági másolatot készít a teljes naplószegmensekről, adja meg a napló biztonsági mentési időközét determinisztikusként. Ez különösen igaz, ha a vész-helyreállítási lehetőséget használja, mert általában egy determinisztikus időszakkal szeretné futtatni a naplóbiztonsági mentéseket. A következő esetben 15 perc van beállítva a napló biztonsági mentési időközében.

![SAP HANA biztonsági mentési naplók ütemezése az SAP HANA Studióban](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

15 percenként gyakoribb biztonsági másolatokat is választhat. Egy gyakoribb beállítás gyakran használják együtt vész-helyreállítási funkció hana nagy példányok. Egyes ügyfelek 5 percenként hajtanak végre tranzakciós naplóbiztonsági mentéseket.

Ha az adatbázisról még soha nem készített biztonsági másolatot, az utolsó lépés egy fájlalapú adatbázis biztonsági mentésének végrehajtása egyetlen biztonsági mentési bejegyzés létrehozásához, amelynek a biztonsági másolat katalógusában kell lennie. Ellenkező esetben az SAP HANA nem kezdeményezheti a megadott naplóbiztonsági mentéseket.

![Fájlalapú biztonsági másolat készítése egyetlen biztonsági másolat létrehozásához](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Az első sikeres tárolási pillanatképek futtatása után törölje a 6. További információ: "A tesztpillanatképek eltávolítása – removeTestStorageSnapshot" a [Microsoft pillanatkép-eszközeiaz Azure-beli SAP HANA eszközben.](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md) 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>A lemezköteten lévő pillanatképek számának és méretének figyelése

Egy adott tárolóköteten figyelheti a pillanatképek számát és a pillanatképek tárolási felhasználását. A `ls` parancs nem jeleníti meg a pillanatkép könyvtárát vagy fájljait. A Linux `du` operációs rendszer parancs a tárolási pillanatképek részleteit jeleníti meg, mert azok ugyanazon a köteten vannak tárolva. Használja a parancsot a következő beállításokkal:

- `du –sh .snapshot`: Ez a beállítás a pillanatkép-könyvtárösszes pillanatképét tartalmazza.
- `du –sh --max-depth=1`: Ez a beállítás felsorolja a **.snapshot** mappába mentett összes pillanatképet és az egyes pillanatképek méretét.
- `du –hc`: Ez a beállítás az összes pillanatkép által használt teljes méretet biztosítja.

Ezekkel a parancsokkal győződjön meg arról, hogy a pillanatképek, amelyek készülnek, és tárolt nem használja fel az összes tároló a köteteken.

>[!NOTE]
>A rendszerindító lun pillanatképei nem láthatók az előző parancsokkal.

### <a name="get-details-of-snapshots"></a>A pillanatképek részleteinek megismerése
Ha további részleteket szeretne megtudni `azure_hana_snapshot_details`a pillanatképekről, használja a parancsfájlt. Ezt a parancsfájlt mindkét helyen futtathatja, ha van egy aktív kiszolgáló a vész-helyreállítási helyen. A parancsfájl a következő kimenetet biztosítja, minden egyes kötet, amely pillanatképeket tartalmaz: 
   * A kötetösszes pillanatképének mérete
   * A kötet minden pillanatképében a következő részletek: 
      - Pillanatkép neve 
      - Idő létrehozása 
      - A pillanatkép mérete
      - A pillanatfelvétel gyakorisága
      - A pillanatképhez társított HANA biztonsági másolat azonosítója, ha releváns

A parancs és a kimenetek szintaxisát az [Azure-beli SAP HANA Microsoft pillanatkép-eszközeinek](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)"Pillanatképek listája – azure_hana_snapshot_details" című részében olvashat. 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>A kiszolgálón lévő pillanatképek számának csökkentése

Ahogy korábban már kifejtették, csökkentheti a tárolt pillanatképek bizonyos címkéinek számát. A pillanatkép kezdeményezéséhez a parancs utolsó két paramétere a címke és a megőrzésre kívánt pillanatképek száma.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

Az előző példában a pillanatkép címke **dailyhana**. Az ezzel a címkével tárolandó pillanatképek száma **28**. A lemezterület-felhasználás megválaszolása kor előfordulhat, hogy csökkenteni szeretné a tárolt pillanatképek számát. Egy egyszerű módja annak, hogy csökkentsék a pillanatképek száma 15, például, hogy futtassa a forgatókönyvet az utolsó paraméter beállítása **15:**

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

Ha ezzel a beállítással futtatja a parancsfájlt, a pillanatképek száma, amely tartalmazza az új tárolási pillanatképet, 15. A 15 legutóbbi pillanatképek megmaradnak, és a 15 régebbi pillanatképek törlődnek.

 >[!NOTE]
 > Ez a parancsfájl csak akkor csökkenti a pillanatképek számát, ha egy óránál több pillanatkép van. A parancsfájl nem törli az egy óránál kisebb pillanatképeket. Ezek a korlátozások a kínált opcionális vész-helyreállítási funkciókhoz kapcsolódnak.

Ha már nem szeretné fenntartani a pillanatképek készletét a **napihana** biztonsági mentési előtaggal a szintaxispéldákban, futtassa a parancsfájlt **a 0-vel** megőrzési számként. A rendszer eltávolítja a címkének megfelelő összes pillanatképet. Az összes pillanatkép eltávolítása hatással lehet a HANA nagy példányok vész-helyreállítási funkció képességeit.

A második lehetőség, hogy törölje az `azure_hana_snapshot_delete`egyes pillanatképek, hogy használja a parancsfájlt . Ez a parancsfájl egy pillanatkép vagy pillanatképek készletének törlésére szolgál a HANA-stúdióban található HANA biztonsági mentési azonosító használatával vagy magával a pillanatkép nevével. Jelenleg a biztonsági másolat azonosítója csak a **hana** pillanatkép típusához létrehozott pillanatképekhez van kötve. Pillanatkép-biztonsági mentések a **típusú naplók** és **a rendszerindítás** nem hajt végre egy SAP HANA pillanatképet, így nincs biztonsági mentési azonosító található a pillanatképek. Ha a pillanatkép nevét adja meg, akkor megkeresi az összes pillanatképet a különböző köteteken, amelyek megfelelnek a megadott pillanatkép neve. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

A parancsfájlról további információt a "Pillanatkép törlése – azure_hana_snapshot_delete" című témakörben talál az [Azure-beli SAP HANA Microsoft-pillanatkép-eszközeiben.](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)

Futtassa a parancsfájlt felhasználói **gyökérként.**

>[!IMPORTANT]
>Ha vannak olyan adatok, amelyek csak a törölni kívánt pillanatképen léteznek, a pillanatkép törlése után, az adatok örökre elvesznek.


## <a name="file-level-restore-from-a-storage-snapshot"></a>Fájlszintű visszaállítás a tárolási pillanatképből

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
A **hana** és a **naplók**pillanatképtípusok hoz a pillanatképek közvetlenül a kötetek a **.snapshot** könyvtárban. Minden pillanatképhez van egy alkönyvtár. Másolja az egyes fájlokat abban az állapotban, amelyben az adott alkönyvtár pillanatképének helyén volt, másolja a tényleges könyvtárstruktúrába. 

A parancsfájl jelenlegi verziójában *nincs* visszaállítási parancsfájl a pillanatkép-visszaállításhoz önkiszolgálóként. Pillanatkép-visszaállítás az önkiszolgáló vész-helyreállítási parancsfájlok részeként hajtható végre a vész-helyreállítási helyen feladatátvétel során. A meglévő elérhető pillanatképek kívánt pillanatképének visszaállításához egy szolgáltatási kérelem megnyitásával kapcsolatba kell lépnie a Microsoft műveleti csapatával.

>[!NOTE]
>Egyetlen fájl visszaállítása nem működik a pillanatképek a rendszerindító lun típusától függetlenül a HANA nagy példány egységek. A **.snapshot** könyvtár nem található meg a rendszerindító lun-ban. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Helyreállítás a legutóbbi HANA pillanatképhez

Egy termelés-down forgatókönyv, a folyamat a kapacitás pillanatkép lehet kezdeni, mint egy ügyfél incidens a Microsoft Azure-támogatás. Ez egy rendkívül sürgős kérdés, ha az adatokat törölték egy éles rendszerben, és az egyetlen módja annak, hogy letölteni, hogy visszaállítsa az éles adatbázis.

Egy másik helyzetben, a point-in-time hasznosítás lehet alacsony sürgősségű és a tervezett nappal előre. Ezt a helyreállítást az SAP HANA-val tervezheti meg az Azure-ban egy magas prioritású jelző felemelése helyett. Például előfordulhat, hogy egy új fejlesztési csomag alkalmazásával szeretné frissíteni az SAP-szoftvert. Ezután vissza kell állítania egy pillanatképet, amely a fejlesztési csomag frissítése előtt az állapotot jelöli.

A kérelem elküldése előtt elő kell készülnie. Az SAP HANA az Azure-csapat ezután kezelni a kérelmet, és adja meg a visszaállított kötetek. Ezután visszaállítja a HANA-adatbázist a pillanatképek alapján.

Az új eszközkészlettel visszaállított pillanatkép-visszaállítás lehetőségeiről az Sap HANA manuális helyreállítási útmutatójában a "Pillanatkép visszaállítása" című témakörben [olvashat egy tárolási pillanatképből.](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)

A kérésre való felkészüléshez kövesse az alábbi lépéseket.

1. Döntse el, hogy melyik pillanatképet szeretné visszaállítani. Csak a hana/adatkötet visszaáll, hacsak nem utasítja másképp. 

1. Állítsa le a HANA-példányt.

   ![A HANA-példány leállítása](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Az egyes HANA-adatbázis-csomópontok adatköteteinek leválasztása. Ha az adatkötetek továbbra is csatlakoztatva vannak az operációs rendszerhez, a pillanatkép visszaállítása sikertelen lesz.

   ![Az adatkötetek leválasztása az egyes HANA-adatbázis-csomópontokon](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Nyisson meg egy Azure-támogatási kérelmet, és adjon meg utasításokat egy adott pillanatkép visszaállításáról:

   - A visszaállítás során: SAP HANA az Azure Service kérheti, hogy vegyen részt egy konferenciahívás koordinálása, ellenőrzése, és ellenőrizze, hogy a megfelelő tárolási pillanatkép visszaállítása. 

   - A visszaállítás után: SAP HANA az Azure Service értesíti, ha a tárolási pillanatkép visszaállítása.

1. A visszaállítási folyamat befejezése után csatlakoztassa újra az összes adatkötetet.

   ![Az összes adatkötet újracsatlakoztatása](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



Egy másik lehetőség például a tárolási pillanatképből helyreállított SAP HANA-adatfájlok beszerzésére, az [Sap HANA manuális helyreállítási útmutatójának](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)7.

Pillanatkép biztonsági mentésvisszaállítása, lásd: [Manuális helyreállítási útmutató az SAP HANA az Azure-ban egy tárolási pillanatkép.](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md) 

>[!Note]
>Ha a pillanatképet a Microsoft-műveletek visszaállították, nem kell megtennie a 7.


### <a name="recover-to-another-point-in-time"></a>Helyreállítás egy másik időpontra
Egy adott időpontra való visszaállításhoz tekintse meg "Az adatbázis helyreállítása a következő időpontra" [című, az Azure-beli SAP HANA manuális helyreállítási útmutatójában egy tárolási pillanatképből](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)című témakört. 


## <a name="next-steps"></a>További lépések
- Lásd: [Katasztrófautáni helyreállítás alapelvei és előkészítése](hana-concept-preparation.md).
