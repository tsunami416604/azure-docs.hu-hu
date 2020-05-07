---
title: HANA biztonsági mentés és visszaállítás az Azure-beli SAP HANAon (nagyméretű példányok) | Microsoft Docs
description: A HANA biztonsági mentésének és visszaállításának végrehajtása az Azure-beli SAP HANAon (nagyméretű példányok)
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
ms.openlocfilehash: 091c0a8cb4c0db517ab2dbabaf16633227dba42c
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594695"
---
# <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás

>[!IMPORTANT]
>Ez a cikk nem helyettesíti a SAP HANA adminisztrációs dokumentációt vagy az SAP-megjegyzéseket. Reméljük, hogy alapos ismeretekkel rendelkezik a SAP HANA adminisztrációval és műveletekkel kapcsolatban, különösen a biztonsági mentés, a visszaállítás, a magas rendelkezésre állás és a vész-helyreállítás terén. Ebben a cikkben a SAP HANA studióból származó képernyőképek jelennek meg. A tartalom, a struktúra és az SAP felügyeleti eszközeinek és az eszközöknek a tulajdonságai megváltozhatnak SAP HANA kiadásról a kiadásra.

Fontos, hogy a környezetében és a HANA verzióiban és kiadásaiban végrehajtott lépéseket és folyamatokat gyakorolja. A cikkben ismertetett egyes folyamatok egyszerűbben megismerhetik az általános ismereteket. Ezeket nem a végleges művelet-kézikönyvek részletes lépéseiként használják. Ha a konfigurációkhoz szeretne műveleti kézikönyveket létrehozni, tesztelje és gyakorolja a folyamatokat, és dokumentálja az adott konfigurációkhoz kapcsolódó folyamatokat. 

Az operációs adatbázisok egyik legfontosabb szempontja a katasztrofális események elleni védelem. Az események oka az lehet, hogy a természeti katasztrófák egyszerű felhasználói hibákhoz vezethetnek.

Egy adatbázis biztonsági mentése, amely bármely időpontra visszaállítható, például a kritikus fontosságú adatműveletek törlése előtt, lehetővé teszi a helyreállítást olyan állapotba, amely a lehető legkorábbi módon, a megszakadási folyamat előtt van.

A visszaállítási képesség eléréséhez két típusú biztonsági mentést kell végrehajtani:

- Adatbázis biztonsági mentései: teljes, növekményes vagy differenciált biztonsági másolatok
- Tranzakciónapló biztonsági mentései

A teljes adatbázisra vonatkozó biztonsági másolatok alkalmazási szinten történő végrehajtása mellett a tárolási pillanatképekkel rendelkező biztonsági mentéseket is végrehajthat. A tárolási Pillanatképek nem helyettesítik a tranzakciónapló biztonsági másolatait. A tranzakciónapló biztonsági mentései továbbra is fontosak maradnak az adatbázis egy adott időpontra való visszaállításához, vagy a már Véglegesített tranzakciók naplófájljainak kiürítéséhez. A tárolási Pillanatképek felgyorsítják a helyreállítást azáltal, hogy gyorsan továbbítják az adatbázis egy előre látható képét. 

SAP HANA az Azure-ban (nagyméretű példányok) két biztonsági mentési és visszaállítási lehetőséget kínál:

- **Saját magát (DIY).** Miután ellenőrizte, hogy van-e elég szabad lemezterület, hajtsa végre a teljes adatbázist és a naplók biztonsági mentését az alábbi lemezes biztonsági mentési módszerek egyikével. Biztonsági mentést készíthet közvetlenül a HANA nagyméretű példány-egységekhez vagy egy Azure-beli virtuális gépen (VM) beállított NFS-megosztásokhoz csatolt kötetekre. Az utóbbi esetben az ügyfelek egy Linux rendszerű virtuális gépet állítanak be az Azure-ban, csatlakoztatják az Azure Storage-t a virtuális géphez, és megosztják a tárolót az adott virtuális gépen konfigurált NFS-kiszolgálón keresztül. Ha a biztonsági mentést olyan köteteken végzi el, amelyek közvetlenül a HANA nagyméretű példány-egységekhez vannak csatolva, másolja a biztonsági másolatokat egy Azure Storage-fiókba. Ezt egy olyan Azure-beli virtuális gép beállítása után végezze el, amely az Azure Storage-on alapuló NFS-megosztásokat exportál. A Azure Backup-tárolót vagy az Azure-beli hideg tárterületet is használhatja. 

   Egy másik lehetőség, hogy egy külső gyártótól származó adatvédelmi eszköz használatával tárolja a biztonsági mentéseket az Azure Storage-fiókba való másolás után. Szükség lehet a DIY Backup lehetőségre is, ha olyan adatmennyiségre van szükség, amelyet hosszabb ideig kell tárolni a megfelelőségi és naplózási célokra. A biztonsági mentések minden esetben a virtuális gépen és az Azure Storage-on keresztül képviselt NFS-megosztásba másolódnak.

- **Infrastruktúra biztonsági mentési és visszaállítási funkciója.** Használhatja a biztonsági mentési és visszaállítási funkciót is, amely az Azure-beli SAP HANA mögöttes infrastruktúráját (nagyméretű példányok) biztosítja. Ez a beállítás megfelel a biztonsági mentések és a gyors visszaállítások szükségességének. Ennek a szakasznak a többi része a HANA Large instances által kínált biztonsági mentési és visszaállítási funkciókkal foglalkozik. Ez a szakasz azt is ismerteti, hogy a biztonsági mentés és a visszaállítás milyen kapcsolatban áll a nagy méretű HANA-példányok által kínált vész-helyreállítási funkciókkal.

> [!NOTE]
>   A HANA nagyméretű példányok mögöttes infrastruktúrája által használt pillanatkép-technológia SAP HANA pillanatképekkel való függőséggel rendelkezik. Ezen a ponton SAP HANA Pillanatképek nem működnek együtt SAP HANA több-bérlős adatbázis-tárolók több Bérlővel. Ha csak egy bérlő van telepítve, SAP HANA Pillanatképek működnek, és ezt a módszert használhatja.

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Azure-beli SAP HANA tárolási Pillanatképek használata (nagyméretű példányok)

Az Azure-ban (nagyméretű példányokon) alapuló SAP HANA tárolási infrastruktúra támogatja a kötetek tárolási pillanatképeit. A kötetek biztonsági mentése és helyreállítása is támogatott, a következő szempontokkal:

- Az adatbázis teljes biztonsági mentése helyett a tárolási kötetek pillanatképeit gyakran vesszük figyelembe.
- Ha egy pillanatképet a/Hana/Data és a/Hana/Shared keresztül indít el, amely magában foglalja a/usr/SAP, a köteteket, a pillanatkép-technológia elindít egy SAP HANA pillanatképet, mielőtt futtatja a tárolási pillanatképet. Ez a SAP HANA pillanatkép a tárolási pillanatkép helyreállítását követően a telepítési pont a végleges naplók visszaállításához. Ahhoz, hogy egy HANA-pillanatkép sikeres legyen, aktív HANA-példányra van szüksége. Egy HSR-forgatókönyvben nem támogatott a tárolási pillanatkép olyan aktuális másodlagos csomóponton, ahol a HANA-pillanatkép nem hajtható végre.
- A tárolási pillanatkép sikeres futtatása után a rendszer törli a SAP HANA pillanatképet.
- A tranzakciós napló biztonsági mentései gyakran és a/Hana/logbackups-kötetben vagy az Azure-ban tárolódnak. A tranzakciós napló biztonsági másolatait tartalmazó/Hana/logbackups-kötetet elindíthatja külön pillanatkép készítéséhez. Ebben az esetben nem kell HANA-pillanatképet futtatnia.
- Ha egy adatbázist egy adott időpontra kell visszaállítani, éles leállás esetén kérje meg, hogy Microsoft Azure támogassa vagy SAP HANA az Azure Restore szolgáltatásban egy bizonyos tárolási pillanatképre. Ilyen például a homokozó rendszer tervezett visszaállítása az eredeti állapotába.
- A tárolási pillanatképben szereplő SAP HANA pillanatkép egy eltolási pont a tranzakciónaplók futtatására szolgáló, és a tárolási pillanatkép készítése után tárolt biztonsági másolatok alkalmazásához.
- Ezek a tranzakciónaplók biztonsági másolatokat készítenek az adatbázis visszaállítására egy adott időpontra.

Olyan tárolási pillanatképeket is végrehajthat, amelyek a kötetek három osztályát célozzák meg:

- A/Hana/Data és a/Hana/Shared együttes pillanatképe, amely magában foglalja A/usr/SAP. Ehhez a pillanatképhez szükség van egy SAP HANA pillanatkép létrehozására a tárolási pillanatkép előkészítéséhez. A SAP HANA pillanatkép biztosítja, hogy az adatbázis konzisztens állapotban legyen a tárolási szempontból. A visszaállítási folyamat esetében ez a beállítás egy pont.
- Külön pillanatkép A/Hana/logbackups.
- Operációs rendszer partíciója.

A pillanatképek legújabb parancsfájljainak és dokumentációjának lekéréséhez lásd: [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Amikor letölti a pillanatkép-parancsfájl csomagot a [githubról](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md), három fájlt kap. Az egyik fájl dokumentálva van egy PDF-fájlban a megadott funkciókhoz. Az eszközkészlet letöltése után kövesse a "pillanatkép-eszközök beszerzése" című témakör utasításait.

## <a name="storage-snapshot-considerations"></a>Tárolási pillanatképgel kapcsolatos megfontolások

>[!NOTE]
>A tárolási Pillanatképek a HANA nagyméretű példány-egységekhez lefoglalt tárolóhelyet használják fel. Vegye figyelembe a tárolási Pillanatképek ütemezésének és a tárolási Pillanatképek számának a következő aspektusait. 

Az Azure-beli SAP HANA tároló-Pillanatképek (nagyméretű példányok) konkrét mechanikája a következők:

- Egy adott tárolási pillanatkép abban az időpontban, amikor a használat során kevés tárterületet használ fel.
- Az adattartalom változásai és a SAP HANA adatfájlok tartalmának változása megváltozik a tárolási köteten, a pillanatképnek az eredeti blokk tartalmát és az adatok változásait kell tárolnia.
- Ennek eredményeképpen a tárolási pillanatkép mérete növekszik. Minél hosszabb a pillanatkép, annál nagyobb lesz a tárolási pillanatkép.
- A tárolási Pillanatképek élettartama során a SAP HANA adatbázis-köteten végrehajtott módosítások nagyobbak lesznek, mint a tárolási Pillanatképek lemezterület-felhasználása.

SAP HANA az Azure-ban (nagyméretű példányok) a SAP HANA-és a naplózási kötetek számára rögzített méretű köteteket tartalmaz. Ezeknek a köteteknek a pillanatképei megeszik a kötet területére. A következőket kell tennie:

- A tárolási Pillanatképek ütemezett időpontjának meghatározása.
- A tárolási kötetek lemezterület-felhasználásának figyelése. 
- Kezelheti a tárolt Pillanatképek számát. 

A tárolási pillanatképeket letilthatja, ha adattömeget importál vagy egyéb jelentős változtatásokat hajt végre a HANA-adatbázisban. 


A következő szakaszokban a pillanatképek végrehajtásával kapcsolatos információkat talál, és általános javaslatokat is tartalmaz:

- Bár a hardver 255 pillanatképet képes fenntartani köteten, a szám alatt jóval többet szeretne tudni. Az ajánlás 250 vagy kevesebb.
- A tárolási Pillanatképek elvégzése előtt figyelje meg és tartsa nyomon a szabad területet.
- Csökkentse a tárolási Pillanatképek számát a szabad terület alapján. Csökkentheti a megmaradt Pillanatképek számát, vagy kiterjesztheti a köteteket. A további tárterület 1 terabájtos egységekben is megrendelhető.
- Olyan tevékenységek során, mint például az adatok áthelyezése a SAP HANAba az SAP platform áttelepítési eszközeivel (R3load) vagy a SAP HANA adatbázisok biztonsági másolatokból való visszaállításával, tiltsa le a tárolási pillanatképeket a/Hana/Data köteten 
- SAP HANA táblázatok nagyobb újraszervezése során Kerülje a tárolási pillanatképeket, ha lehetséges.
- A tárolási Pillanatképek előfeltétele, hogy kihasználhassa az Azure-beli SAP HANA vész-helyreállítási képességeit (nagyméretű példányok).

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Az önkiszolgáló tárolási pillanatképek használatának előfeltételei

Győződjön meg arról, hogy a pillanatkép-parancsfájl futtatása sikeresen megtörtént, ellenőrizze, hogy a Perl a HANA Large instances Server Linux operációs rendszerére van-e telepítve. A Perl előre telepítve van a HANA nagyméretű példányának egységén. A Perl verziójának vizsgálatához használja a következő parancsot:

`perl -v`

![A nyilvános kulcsot a parancs futtatásával másolhatja.](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>Tárolási Pillanatképek beállítása

A nagyméretű HANA-példányokkal rendelkező tárolási Pillanatképek beállításához kövesse az alábbi lépéseket.
1. Győződjön meg arról, hogy a Perl a HANA Large instances-kiszolgálón a Linux operációs rendszerre van telepítve.
1. Módosítsa a/etc/ssh/SSH\_konfigurációját, és adja hozzá a következő sort: _Mac HMAC-SHA1_.
1. Hozzon létre egy SAP HANA biztonsági mentési felhasználói fiókot a főcsomóponton minden egyes futtatott SAP HANA-példányhoz, ha van ilyen.
1. Telepítse a SAP HANA HDB-ügyfelet az összes SAP HANA Large Instances-kiszolgálóra.
1. Az egyes régiók első SAP HANA Large Instances kiszolgálóján hozzon létre egy nyilvános kulcsot a pillanatkép-készítést vezérlő mögöttes tárolási infrastruktúra eléréséhez.
1. Másolja a szkripteket és a konfigurációs fájlt a [githubról](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) a **hdbsql** helyére a SAP HANA telepítésben.
1. Módosítsa a *HANABackupDetails. txt* fájlt a megfelelő ügyfél-specifikációk szerint.

Szerezze be a legfrissebb pillanatkép-szkripteket és dokumentációt a [githubról](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). A korábban felsorolt lépésekért lásd: [Microsoft Snapshot Tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

### <a name="consideration-for-mcod-scenarios"></a>MCOD-forgatókönyvek megfontolása
Ha egy HANA nagyméretű példány-egységen több SAP HANA példánnyal rendelkező [MCOD-forgatókönyvet](https://launchpad.support.sap.com/#/notes/1681092) futtat, külön tárolási köteteket kell kiépíteni az egyes SAP HANA példányokhoz. A MDC és egyéb szempontokkal kapcsolatos további információkért tekintse meg az Azure-beli [SAP HANA Microsoft pillanatkép-eszközeinek](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)"fontos tudnivalók" című témakörét.
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>1. lépés: a SAP HANA HDB-ügyfél telepítése

Az Azure-on SAP HANA telepített Linux operációs rendszer (nagyméretű példányok) tartalmazza azokat a mappákat és parancsfájlokat, amelyek szükségesek SAP HANA tárolási Pillanatképek futtatásához a biztonsági mentéshez és a vész-helyreállítási célokra. Újabb verziók a [githubban](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). 

Az Ön felelőssége, hogy a SAP HANA HDB-ügyfelet a HANA nagyméretű példányok egységére telepítse a SAP HANA telepítésekor.

### <a name="step-2-change-the-etcsshssh_config"></a>2. lépés: a/etc/ssh/SSH\_konfigurációjának módosítása

Ez a lépés a [Microsoft Snapshot Tools for SAP HANA on Azure-on](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)való kommunikáció engedélyezése a Storage szolgáltatással című témakörben található.


### <a name="step-3-create-a-public-key"></a>3. lépés: nyilvános kulcs létrehozása

Ha engedélyezni szeretné a HANA nagyméretű példányának bérlője számára a Storage pillanatkép-interfészek elérését, hozzon létre egy bejelentkezési eljárást egy nyilvános kulcson keresztül. 

A bérlőben az Azure-beli (nagyméretű példányok) kiszolgáló első SAP HANA hozzon létre egy nyilvános kulcsot a tárolási infrastruktúra eléréséhez. Nyilvános kulccsal nem kell jelszót bejelentkezni a Storage pillanatkép-felületekre. Nem kell nyilvános kulccsal megtartania a jelszó hitelesítő adatait is. 

Nyilvános kulcs létrehozásához tekintse meg az Azure-beli [SAP HANAhez készült Microsoft pillanatkép-eszközök](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)"a tárolóval való kommunikáció engedélyezése" címűét.


### <a name="step-4-create-an-sap-hana-user-account"></a>4. lépés: SAP HANA felhasználói fiók létrehozása

SAP HANA Pillanatképek létrehozásának megkezdéséhez hozzon létre egy felhasználói fiókot SAP HANA, hogy a tárolási pillanatkép parancsfájljai használhatók. Hozzon létre egy SAP HANA felhasználói fiókot a SAP HANA Studióban erre a célra. A felhasználót a SYSTEMDB alatt kell létrehozni, *nem* a MDC SID-adatbázisában. Az egytárolós környezetben a felhasználó létrehozása a bérlői adatbázisban történik. Ennek a fióknak a **Backup admin** és a **Catalog olvasási** jogosultsággal kell rendelkeznie. 

Felhasználói fiók beállításához és használatához tekintse meg a "kommunikáció engedélyezése SAP HANA használatával" című részt a [githubon](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-5-authorize-the-sap-hana-user-account"></a>5. lépés: a SAP HANA felhasználói fiók engedélyezése

Ebben a lépésben engedélyezi a létrehozott SAP HANA felhasználói fiókot, hogy a parancsfájlok ne kelljen a jelszavakat futásidőben elküldeni. Az SAP HANA parancs `hdbuserstore` lehetővé teszi egy SAP HANA felhasználói kulcs létrehozását. A kulcsot egy vagy több SAP HANA csomópont tárolja. A felhasználói kulcs lehetővé teszi a felhasználó hozzáférését SAP HANA anélkül, hogy a jelszavakat a parancsfájl-kezelési folyamaton belül kellene kezelnie. A parancsfájlkezelési folyamatot a cikk későbbi részében tárgyaljuk.

>[!IMPORTANT]
>Futtassa ezeket a konfigurációs parancsokat ugyanazzal a felhasználói környezettel, mint amelyen a pillanatkép-parancsok futnak. Ellenkező esetben a pillanatkép-parancsok nem fognak megfelelően működni.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>6. lépés: a pillanatkép-parancsfájlok beszerzése, a pillanatképek konfigurálása és a konfiguráció és a kapcsolat tesztelése

Töltse le a szkriptek legújabb verzióját a [githubról](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.2). A parancsfájlok telepítésének módja a parancsfájlok 4,1-es kiadásával módosult. További információ: "a kommunikáció engedélyezése SAP HANAvel" a [Microsoft Snapshot Tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)-ban.

A parancsok pontos sorrendjének megtekintéséhez tekintse meg az Azure-beli [SAP HANAhoz készült Microsoft Snapshot](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)Tools (alapértelmezett) a pillanatkép-eszközök egyszerű telepítését ismertető témakört. Az alapértelmezett telepítés használatát javasoljuk. 

A 3. x verzióról a 4,1-re való frissítéshez tekintse meg a "meglévő telepítés frissítése" című részt a [Microsoft pillanatkép-eszközök SAP HANA az Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)-ban című témakörben. Az 4,1 eszközkészlet eltávolításához tekintse meg az Azure-beli [SAP HANA Microsoft pillanatkép](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)-eszközeinek "a pillanatkép-eszközök eltávolítása" című részét.

Ne felejtse el futtatni a "pillanatkép-eszközök telepítésének befejezése" című témakörben leírt lépéseket az Azure-beli [SAP HANAhez készült Microsoft Snapshot Tools eszközben](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

A különböző parancsfájlok és fájlok telepítésének célját a "Mik ezek a pillanatkép-eszközök?" című cikk ismerteti. a [Microsoft Snapshot Tools for SAP HANA az Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)-ban.

A pillanatkép-eszközök konfigurálása előtt győződjön meg arról, hogy a HANA biztonsági mentési helyei és beállításai helyesen vannak konfigurálva. További információkért tekintse meg az Azure-beli [SAP HANA Microsoft pillanatkép-eszközök](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)"SAP HANA konfigurálása" című témakörét.

A pillanatkép-eszközkészlet konfigurációját az Azure-beli [SAP HANA a Microsoft Snapshot Tools (](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)konfigurációs fájl-HANABackupCustomerDetails. txt) című szakaszában találja.

#### <a name="test-connectivity-with-sap-hana"></a>Kapcsolat tesztelése SAP HANA

Miután az összes konfigurációs adatok bekerültek a *HANABackupCustomerDetails. txt* fájlba, győződjön meg arról, hogy a konfigurációk helyesek-e a HANA-példányok számára. Használja a parancsfájlt `testHANAConnection`, amely független egy SAP HANA vertikális Felskálázási vagy kibővíthető konfigurációtól.

További információkért tekintse meg a "kapcsolat ellenőrzése SAP HANA-testHANAConnection" című részt az Azure-beli [SAP HANA Microsoft Snapshot-eszközökben](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

#### <a name="test-storage-connectivity"></a>Tárolási kapcsolat tesztelése

A következő lépés a *HANABackupCustomerDetails. txt* konfigurációs fájlba helyezett adatkapcsolaton keresztül ellenőrzi a tárolóhoz való kapcsolódást. Ezután futtasson egy teszt pillanatképet. A `azure_hana_backup` parancs futtatása előtt futtatnia kell ezt a tesztet. A teszt parancsainak sorozata a [Microsoft Snapshot Tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)-ban című témakör "kapcsolat ellenőrzése a Storage-testStorageSnapshotConnection" című részében található.

A tárolási virtuálisgép-felületek sikeres bejelentkezését követően a parancsfájl a 2. fázissal folytatódik, és egy teszt pillanatképet hoz létre. A kimenet itt látható a SAP HANA három csomópontos kibővíthető konfigurációjában.

Ha a teszt pillanatképe sikeresen lefut a parancsfájllal, a tényleges tárolási Pillanatképek ütemezhetők. Ha nem sikerül, a továbblépés előtt vizsgálja meg a problémákat. A teszt pillanatképe csak az első valódi Pillanatképek elvégzése után marad.


### <a name="step-7-perform-snapshots"></a>7. lépés: Pillanatképek elvégzése

Az előkészítési lépések elvégzése után megkezdheti a tényleges tárolási Pillanatképek konfigurálását és beosztását. Az ütemezni kívánt szkript SAP HANA Felskálázási és kibővíthető konfigurációkkal működik. A biztonsági mentési parancsfájl rendszeres és rendszeres végrehajtásához a cron segédprogram használatával ütemezze a parancsfájlt. 

A parancs szintaxisának és működésének pontos szintaxisát lásd: "a pillanatképek biztonsági mentésének elvégzése azure_hana_backup" a [Microsoft Snapshot Tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)-ban. 

A szkript `azure_hana_backup` futtatásakor a a következő három fázisban hozza létre a tárolási pillanatképet:

1. SAP HANA pillanatképet futtat.
1. Egy tárolási pillanatképet futtat.
1. Eltávolítja a tárolási pillanatkép futtatása előtt létrehozott SAP HANA pillanatképet.

A parancsfájl futtatásához hívja meg azt a HDB végrehajtható mappából, amelybe másolták. 

A megőrzési időszak a parancsfájl futtatásakor paraméterként elküldött Pillanatképek számával lesz felügyelve. A tárolási Pillanatképek által lefedett idő a végrehajtás időszakának függvénye, valamint a parancsfájl futtatásakor paraméterként elküldött Pillanatképek száma. 

Ha a megőrzött Pillanatképek száma meghaladja a parancsfájl hívásában paraméterként megnevezett számot, a rendszer az új pillanatkép futtatása előtt törli ugyanazon címke legrégebbi tárolási pillanatképét. A hívás utolsó paramétereként megadott szám a megőrzött Pillanatképek számának szabályozására használható szám. Ezzel a számmal a pillanatképekhez használt lemezterületet is vezérelheti, közvetve is. 


## <a name="snapshot-strategies"></a>Pillanatkép-stratégiák
A különböző típusú Pillanatképek gyakorisága attól függ, hogy a HANA nagyméretű példány vész-helyreállítási funkcióját használja-e. Ez a funkció a tárolási pillanatképekre támaszkodik, amelyekre speciális javaslatok szükségesek a tárolási Pillanatképek gyakoriságára és végrehajtási idejére. 

Az ezt követő megfontolások és javaslatok alapján feltételezhető, hogy *nem* használja fel a nagy számú, nagyméretű példányok által kínált vész-helyreállítási funkciót. Ehelyett a tárolási Pillanatképek segítségével biztonsági másolatokat készíthet, és az elmúlt 30 napban biztosíthatja az időponthoz tartozó helyreállítást. A pillanatképek és a tárhely számának korlátai miatt vegye figyelembe a következő követelményeket:

- Az időponthoz tartozó helyreállítás helyreállítási ideje.
- A felhasznált terület.
- A helyreállítási pontra és a helyreállítási időre vonatkozó célkitűzések a katasztrófák lehetséges helyreállításához.
- A HANA teljes adatbázis biztonsági mentésének végleges végrehajtása a lemezeken. Ha a teljes adatbázis biztonsági mentést végez a lemezekről vagy a **backint** felületről, a tárolási Pillanatképek végrehajtása sikertelen lesz. Ha a teljes adatbázis biztonsági mentését tervezi a tárolási Pillanatképek tetején, győződjön meg arról, hogy a tárolási Pillanatképek végrehajtása ebben az időszakban le van tiltva.
- A pillanatképek másodpercenkénti száma, amely a 250-ra korlátozódik.

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

Ha nem használja a HANA Large-példányok vész-helyreállítási funkcióját, a pillanatkép időtartama ritkább. Ilyen esetekben végezze el a/Hana/Data és a/Hana/Shared együttes pillanatképeit, amelyek 12 órás vagy 24 órás időszakokban tartalmazzák a/usr/SAP-t. A pillanatképek megtartása egy hónapig. Ugyanez vonatkozik a napló biztonsági mentési kötetének pillanatképére is. SAP HANA tranzakciónaplók biztonsági mentésének végrehajtása a napló biztonsági másolati kötetén 5 – 15 perces időszakra történik.

Az ütemezett tárolási Pillanatképek a cron használatával érhetők el legjobban. Ugyanazt a parancsfájlt használja az összes biztonsági mentéshez és a vész-helyreállítási igényekhez. Módosítsa a parancsfájl bemeneteit, hogy azok megfeleljenek a különböző kért biztonsági mentési időpontoknak. Ezek a pillanatképek az összes, a cron-ben ütemezett módon, a végrehajtásuk idejétől függően változnak. Ez lehet óránként, 12 óránként, naponta vagy hetente. 

A következő példa egy cron-ütemtervet mutat be a/etc/crontab-ben:
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
Az előző példában egy óránként összevont pillanatkép tartalmazza azokat a köteteket, amelyek tartalmazzák a/Hana/Data és a/hana/shared/SID, amely magában foglalja a/usr/SAP, a helyszíneket. Használja ezt a pillanatképet az elmúlt két napban egy gyorsabb, időponthoz való helyreállításhoz. Ezen kötetek napi pillanatképe is rendelkezésre áll. Tehát két napos lefedettséggel rendelkezik óránkénti pillanatképekkel, valamint a napi Pillanatképek négy hetes lefedettséggel. A tranzakciós napló biztonsági másolati kötete is naponta biztonsági mentést készít. Ezek a biztonsági másolatok négy hétig tartanak. 

Ahogy a crontab harmadik sorában is látható, a HANA-tranzakciónapló biztonsági másolata 5 percenként fut. A tárolási pillanatképeket futtató különböző cron-feladatok indítási időpontjai lépcsőzetesen jelennek meg. Így a pillanatképek nem futnak egyszerre egy adott időpontban. 

A következő példában egy kombinált pillanatképet fog végrehajtani, amely tartalmazza a/Hana/Data és a/hana/shared/SID tartalmazó köteteket, beleértve a/usr/SAP, az óránkénti tárolóhelyeket. Ezeket a pillanatképeket két napig őrzi meg. A tranzakciónapló biztonsági mentési köteteinek pillanatképei 5 percenként futnak, és négy órán keresztül vannak tárolva. Ahogy korábban is, a HANA-tranzakciós naplófájl biztonsági mentése 5 percenként fut. 

A tranzakciónapló biztonsági mentési kötetének pillanatképét 2 perces késleltetéssel hajtja végre a tranzakciónapló biztonsági mentésének megkezdése után. Normális körülmények között a SAP HANA tranzakciónapló biztonsági mentése a 2 percen belül befejeződik. Ahogy korábban is, a rendszerindító LUN-ot tartalmazó kötetről naponta egyszer biztonsági mentés készül, és a tárolási Pillanatképek egy része négy hétig tart.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

Az alábbi ábra az előző példában szereplő folyamatokat szemlélteti. A rendszerindító LUN ki van zárva.

![A biztonsági másolatok és a pillanatképek közötti kapcsolat](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA rendszeres írásokat végez a/Hana/log köteten, hogy dokumentálja az adatbázis véglegesített módosításait. A SAP HANA rendszeresen ír egy mentésipont a/Hana/Data kötetre. A crontabban megadott módon egy SAP HANA tranzakciós napló biztonsági mentése 5 percenként fut. 

Azt is láthatja, hogy egy SAP HANA pillanatkép óránként fut a/Hana/Data-és/hana/shared/SID-kötetek összesített tárolási pillanatképének elindításának eredményeképpen. Miután a HANA-pillanatkép sikeres volt, a kombinált tároló pillanatképe fut. A crontab utasításai szerint a/Hana/logbackup köteten lévő tárolási pillanatkép 5 percenként fut, körülbelül 2 perccel a HANA-tranzakciónapló biztonsági mentése után.

> 

>[!IMPORTANT]
> A SAP HANA biztonsági másolatokhoz tartozó tárolási Pillanatképek használata csak akkor hasznos, ha a pillanatképeket SAP HANA tranzakciónaplók biztonsági mentésével együtt hajtják végre. Ezeknek a tranzakciónaplóknak a biztonsági mentéseknek le kell terjedniük a tárolási Pillanatképek közötti időszakokra. 

Ha egy 30 napos időpontra vonatkozó helyreállítási időszakra vonatkozó kötelezettségvállalást állított be a felhasználók számára, a következőket kell tennie:

- A/Hana/Data és a 30 napos/hana/shared/SID, szélsőséges esetekben elérheti a közös tárolási pillanatképet. 
- Olyan összefüggő tranzakciós naplókból álló biztonsági másolatokkal rendelkezik, amelyek a kombinált tároló-Pillanatképek közötti időt fedik le. Így a tranzakciónapló biztonsági mentési kötetének legrégebbi pillanatképének 30 naposnak kell lennie. Ez nem igaz, ha a tranzakciós napló biztonsági másolatait az Azure Storage-ban található másik NFS-megosztásra másolja. Ebben az esetben előfordulhat, hogy lekéri a régi tranzakciós napló biztonsági másolatait az NFS-megosztásról.

A tárolási Pillanatképek kihasználása és a tranzakciónaplók biztonsági mentésének végleges tárolási replikálása érdekében módosítsa azt a helyet, ahová a SAP HANA a tranzakciónapló biztonsági másolatait írja. Ezt a módosítást a HANA Studióban teheti meg. 

Bár a SAP HANA automatikusan biztonsági másolatot készít a teljes naplókról, a napló biztonsági mentési intervallumát determinisztikus kell megadni. Ez különösen akkor igaz, ha a vész-helyreállítási beállítást használja, mert általában a determinisztikus-időszak használatával szeretné futtatni a naplók biztonsági másolatait. A következő esetben a rendszer 15 percet állít be a napló biztonsági mentési időközéként.

![SAP HANA biztonsági mentési naplók ütemezett SAP HANA Studióban](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

A 15 percenként gyakoribb biztonsági mentéseket is kiválaszthatja. A gyakori beállítást gyakran használják a HANA Large-példányok vész-helyreállítási funkcióival együtt. Néhány ügyfél 5 percenként hajtja végre a tranzakciónapló biztonsági mentését.

Ha az adatbázisról még soha nem készült biztonsági másolat, az utolsó lépés egy fájl alapú adatbázis biztonsági másolatának elvégzése, amely egy olyan biztonsági mentési bejegyzést hoz létre, amelynek léteznie kell a biztonsági mentési katalógusban. Ellenkező esetben a SAP HANA nem tudja elindítani a megadott naplók biztonsági másolatait.

![Fájl alapú biztonsági másolat készítése egyetlen biztonsági mentési bejegyzés létrehozásához](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Az első sikeres tárolási Pillanatképek futtatása után törölje a 6. lépésben futtatott teszt-pillanatképet. További információ: "test Snapshots-removeTestStorageSnapshot eltávolítása" a [Microsoft Snapshot Tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>A lemez kötetén található Pillanatképek számának és méretének figyelése

Egy adott tárolási köteten figyelheti a pillanatképek számát és a pillanatképek tárolási felhasználását. A `ls` parancs nem jeleníti meg a pillanatképek könyvtárát vagy fájljait. A Linux operációs rendszer `du` parancs megjeleníti a tárolási Pillanatképek részleteit, mivel azok ugyanazon a köteten vannak tárolva. Használja a parancsot a következő beállításokkal:

- `du –sh .snapshot`: Ez a beállítás a pillanatképek könyvtárában lévő összes pillanatképet tartalmazza.
- `du –sh --max-depth=1`: Ez a lehetőség felsorolja a **. Snapshot** mappában mentett összes pillanatképet, valamint az egyes Pillanatképek méretét.
- `du –hc`: Ez a beállítás megadja az összes pillanatkép által használt teljes méretet.

Ezekkel a parancsokkal gondoskodhat arról, hogy a végrehajtott és tárolt Pillanatképek ne használják a kötetek összes tárolóját.

>[!NOTE]
>A rendszerindító LUN pillanatképei nem láthatók az előző parancsokkal.

### <a name="get-details-of-snapshots"></a>Pillanatképek részleteinek beolvasása
A pillanatképekkel kapcsolatos további részletekért használja a parancsfájlt `azure_hana_snapshot_details`. Ezt a parancsfájlt bármelyik helyen futtathatja, ha van aktív kiszolgáló a vész-helyreállítási helyen. A parancsfájl a következő kimenetet adja meg, amely a pillanatképeket tartalmazó kötetek szerinti bontásban található: 
   * Egy kötet összes pillanatképének mérete
   * Az alábbi részletek az adott kötet minden pillanatképében: 
      - Pillanatkép neve 
      - Létrehozás ideje 
      - A pillanatkép mérete
      - A pillanatkép gyakorisága
      - Az adott pillanatképhez társított HANA biztonsági mentési azonosító, ha van ilyen

A parancsok és kimenetek szintaxisát lásd: "Pillanatképek listázása azure_hana_snapshot_details" a [Microsoft Snapshot Tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)-ban. 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>A pillanatképek számának csökkentése egy kiszolgálón

Az előzőekben leírtak szerint csökkenthető a tárolt Pillanatképek egyes címkéjének száma. A parancs utolsó két paramétere a pillanatkép elindításához a címkét és a megtartani kívánt Pillanatképek számát jelöli.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

Az előző példában a pillanatkép címkéje **dailyhana**. Az ezzel a címkével megőrzött Pillanatképek száma **28**. A lemezterület-felhasználásra adott válaszként érdemes csökkenteni a tárolt Pillanatképek számát. Egy egyszerű módszer a pillanatképek számának 15-re való csökkentésére, például az, hogy a szkriptet az utolsó paraméterrel állítsa **15**-re:

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

Ha ezzel a beállítással futtatja a parancsfájlt, a pillanatképek száma, amely tartalmazza az új tárolási pillanatképet, 15. A 15 legutóbbi Pillanatképek megmaradnak, és a 15 régebbi pillanatkép törlődik.

 >[!NOTE]
 > Ez a szkript csak akkor csökkenti a pillanatképek számát, ha a pillanatképek száma több, mint egy órával korábbi. A parancsfájl nem törli az egy óránál régebbi pillanatképeket. Ezek a korlátozások a választható vész-helyreállítási funkciókkal kapcsolatosak.

Ha már nem szeretné, hogy a **dailyhana** található biztonsági mentési előtaggal rendelkező Pillanatképek ne legyenek karbantartva, futtassa a szkriptet **0** értékkel a megőrzési számként. Ezután eltávolítja a címkének megfelelő összes pillanatképet. Az összes pillanatkép eltávolítása hatással lehet a HANA nagy példányszámú vész-helyreállítási funkció képességeire.

Az adott Pillanatképek törlésére szolgáló második lehetőség a szkript `azure_hana_snapshot_delete`használata. Ez a szkript egy pillanatkép vagy Pillanatképek készletének törlésére szolgál a Hana Studióban található HANA Backup-azonosító használatával vagy maga a pillanatkép-névvel. Jelenleg a biztonsági mentési azonosító csak a **Hana** pillanatkép-típushoz létrehozott pillanatképekhez van kötve. A **naplók** és a **rendszerindítás** pillanatképes biztonsági mentései nem végeznek SAP HANA pillanatképet, így nem található biztonsági mentési azonosító a pillanatképekhez. Ha a pillanatkép neve meg van adva, a rendszer az összes pillanatképet keresi a különböző köteteken, amelyek megfelelnek a megadott pillanatkép nevének. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

A parancsfájllal kapcsolatos további információkért tekintse meg a "pillanatkép törlése – azure_hana_snapshot_delete" című részt a [Microsoft Snapshot Tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)-ban.

Futtassa a parancsfájlt felhasználói **gyökérként**.

>[!IMPORTANT]
>Ha vannak olyan adatok, amelyek csak a törölni kívánt pillanatképen szerepelnek, a pillanatkép törlése után az adatok örökre elvesznek.


## <a name="file-level-restore-from-a-storage-snapshot"></a>Fájl szintű visszaállítás tárolási pillanatképből

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
A **Hana** és a **logs**típusú Pillanatképek esetében közvetlenül a **. Snapshot** könyvtár kötetein érheti el a pillanatképeket. Mindegyik pillanatképhez van egy alkönyvtár. Másolja az összes fájlt abba az állapotba, amely az adott alkönyvtárból származó pillanatkép pontján volt a tényleges címtár-struktúrába. 

A parancsfájl aktuális verziójában *nincs* olyan visszaállítási parancsfájl, amely a pillanatkép-visszaállításhoz önkiszolgálóként van megadva. A pillanatkép-visszaállítás az önkiszolgáló vész-helyreállítási parancsfájlok részeként is elvégezhető a vész-helyreállítási helyen a feladatátvétel során. Ha vissza szeretne állítani egy kívánt pillanatképet a meglévő elérhető pillanatképekről, a szolgáltatási kérelem megnyitásával kapcsolatba kell lépnie a Microsoft operatív csapatával.

>[!NOTE]
>Az egyetlen fájl visszaállítása nem működik a nagyméretű HANA-példány-egységek típusától független rendszerindító LUN pillanatképei esetében. A **. Snapshot** könyvtár nincs kitéve a rendszerindító LUN-ban. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Helyreállítás a legutóbbi HANA-pillanatképre

Éles környezetben a tárolási Pillanatképek helyreállításának folyamata Microsoft Azure támogatással rendelkező ügyféli incidensként indítható el. Ez nagy sürgősséget jelent, ha az adatfeldolgozási rendszer törölve lett, és az egyetlen módja annak, hogy visszaállítsa a termelési adatbázist.

Egy másik helyzetben az időponthoz tartozó helyreállítás alacsony sürgősséggel és tervezett nappal előre is elvégezhető. A helyreállítást az Azure-SAP HANA is megtervezheti a magas prioritású jelzők növelése helyett. Előfordulhat például, hogy egy új javító csomag alkalmazásával tervezi frissíteni az SAP-szoftvert. Ezután egy olyan pillanatképre kell visszaállítania, amely a javító csomag frissítése előtt állapotot jelöl.

A kérelem elküldése előtt elő kell készítenie a következőt:. A SAP HANA az Azure csapata képes kezelni a kérést, és megadhatja a visszaállított köteteket. Ezt követően visszaállíthatja a HANA-adatbázist a pillanatképek alapján.

Az új eszközkészlettel visszaállított Pillanatképek lekérésének lehetőségeiről "pillanatkép visszaállítása" című részében talál további információt az [Azure-beli SAP HANAról a tárolási pillanatképből](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

A kérelem előkészítéséhez kövesse az alábbi lépéseket.

1. Döntse el, hogy melyik pillanatképet szeretné visszaállítani. Csak a Hana/az adatmennyiség állítható vissza, hacsak nem utasítja másképpen. 

1. Állítsa le a HANA-példányt.

   ![A HANA-példány leállítása](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Válassza le az adatköteteket az egyes HANA-adatbázisok csomópontjain. Ha az adatkötetek továbbra is csatlakoztatva vannak az operációs rendszerhez, a pillanatkép visszaállítása sikertelen lesz.

   ![Az adatkötetek leválasztása az egyes HANA-adatbázisok csomópontjain](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Nyisson meg egy Azure-támogatási kérést, és adjon meg egy adott pillanatkép visszaállítására vonatkozó utasításokat:

   - A helyreállítás során: SAP HANA az Azure szolgáltatásban megkérheti, hogy vegyen részt egy konferenciahívásban, hogy koordinálja, ellenőrizze és erősítse meg, hogy a megfelelő tárolási pillanatkép vissza lett-e állítva. 

   - A visszaállítást követően a SAP HANA az Azure szolgáltatásban értesítést küld, amikor visszaállítja a tárolási pillanatképet.

1. A visszaállítási folyamat befejezése után csatlakoztassa újra az összes adatkötetet.

   ![Az összes adatkötet újracsatlakoztatása](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



Egy másik lehetőség a tárolási pillanatképből visszaállított adatfájlok beszerzésére, például az Azure-beli SAP HANA- [re vonatkozó kézi helyreállítási útmutató](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)7. lépésében, a tárolási pillanatképből való SAP HANA.

A pillanatképek biztonsági másolatból való visszaállításáról további információt [Az Azure-beli SAP HANA manuális helyreállítási útmutatójában talál a tárolási pillanatképből](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 

>[!Note]
>Ha a pillanatképet a Microsoft műveletei visszaállították, nem kell elvégeznie a 7. lépést.


### <a name="recover-to-another-point-in-time"></a>Helyreállítás másik időpontra
Egy adott időpontra való visszaállításhoz tekintse meg a "adatbázis helyreállítása a következő időpontra" című részt a [manuális helyreállítási útmutatóban SAP HANA Azure-ban egy tárolási pillanatképből](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 


## <a name="next-steps"></a>További lépések
- Lásd: vész [-helyreállítási alapelvek és előkészítés](hana-concept-preparation.md).
