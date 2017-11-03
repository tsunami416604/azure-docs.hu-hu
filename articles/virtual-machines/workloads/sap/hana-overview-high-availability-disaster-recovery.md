---
title: "Az Azure (nagy példányok) SAP HANA magas rendelkezésre állási és vészhelyreállítási helyreállítási |} Microsoft Docs"
description: "Magas rendelkezésre állás és az Azure (nagy példány) az SAP HANA vész-helyreállítási terv létrehozása"
services: virtual-machines-linux
documentationcenter: 
author: saghorpa
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 09aa98a35fa8286828a99c49a33a80d5938afe3a
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>SAP HANA nagy példányok magas rendelkezésre állási és vészhelyreállítási helyreállítási az Azure-on 

Magas rendelkezésre állás és vészhelyreállítás (DR) a kritikus fontosságú SAP HANA Azure (nagy példányok) kiszolgálón futó fontos szempontot. Fontos, SAP, a rendszer integráló, vagy a Microsoft megfelelően tervezővel, és a megfelelő magas rendelkezésre állású és vész-helyreállítási stratégia megvalósításában. Fontos továbbá kell figyelembe venni a helyreállítási időkorlát (RPO) és a helyreállítási idő célkitűzése a környezetre jellemző.

A Microsoft támogatja a bizonyos SAP HANA magas rendelkezésre állású funkciók HANA nagy osztályt. Ezek a képességek közé tartoznak:

- **Tárolási replikációs**: A tároló nem lesz képes replikálni az összes adatokat egy másik Azure-régió, egy másik HANA nagy példány stamp. SAP HANA működése független ezt a módszert.
- **HANA replikációs**: egy külön SAP HANA-rendszerre a SAP HANA összes adatainak a replikálását. A helyreállítási idő célkitűzése keresztül rendszeres időközönként adatreplikáció másodpercekre csökken. SAP HANA aszinkron, szinkron memórián belüli és szinkron módot támogat. Szinkron módban csak az ugyanabban az adatközpontban, vagy legalább 100 km-egymástól SAP HANA rendszerek ajánlott. Az aktuális terv HANA nagy példány stampgyűjteményt, a HANA replikációs csak magas rendelkezésre állásra is használható. Jelenleg HANA replikációs futtatásához szükség van egy külső fordított proxy összetevőre vész-helyreállítási konfigurációk be egy másik Azure-régiót. 
- **Gazdagép automatikus feladatátvételt**: SAP Hana HANA replikációs alternatívájaként használata helyi hiba-helyreállítási megoldás. A fő csomópont nem érhető el, ha konfigurál egy vagy több készenléti SAP HANA-csomópont kibővített módban, és SAP HANA automatikusan átadja a feladatokat egy készenléti csomópont.

SAP HANA Azure (nagy példányok) a két Azure régióban, a három különböző geopolitikai régiókban (USA Ausztrália és Európa) kínálják. Két, különböző régiókban, hogy a gazdagép HANA nagy példány bélyegzők különálló dedikált hálózati kapcsolatok vész-helyreállítási mód storage-pillanatfelvételekkel replikálásához használt csatlakoznak-e. A replikáció nem jön létre, alapértelmezés szerint. Az ügyfelek számára, a vész-helyreállítási funkció rendezett van beállítva. Tárolási replikációs szolgáltatása a storage-pillanatfelvételekkel nagy HANA-példányok használatát függ. Nincs lehetőség a válasszon ki egy Azure-régió, egy vész-Helyreállítási területet, amely egy másik geopolitikai területén legyen. 

Az alábbi táblázat a jelenleg támogatott magas rendelkezésre állású és vész-helyreállítási módszerek és kombinációit:

| Támogatott HANA nagy példányát forgatókönyv | Magas rendelkezésre állású lehetőség | Vész-helyreállítási lehetőség | Megjegyzések |
| --- | --- | --- | --- |
| Egyetlen csomópont | Nem érhető el. | Dedikált DR-telepítés.<br /> Multipurpose DR-telepítés. | |
| Gazdagép automatikus feladatátvételt: N + m<br /> például 1 + 1 | A készenléti véve az aktív szerepkör használhatóságát.<br /> HANA határozza meg a szerepkör kapcsolót. | Dedikált DR-telepítés.<br /> Multipurpose DR-telepítés.<br /> DR szinkronizálási storage replikáció használatával. | HANA kötet beállítása a csomópontok (n + m) vannak csatolva.<br /> DR helynek kell lennie a csomópontok azonos számú. |
| HANA replikációs | Elsődleges vagy másodlagos telepítés lehetséges.<br /> Másodlagos áthelyezi egy feladatátvételi esetben elsődleges szerepkör.<br /> HANA replikációs és az operációs rendszer szabályozhatja a feladatátvételt. | Dedikált DR-telepítés.<br /> Multipurpose DR-telepítés.<br /> DR szinkronizálási storage replikáció használatával.<br /> DR HANA replikációs használatával még nem lehetséges, külső gyártótól származó összetevőkhöz nélkül. | Minden csomópont csatolt kötetek külön készletét.<br /> A munkakörnyezeti helyet a másodlagos másodpéldány csak lemezkötetek a vész-Helyreállítási helyre replikálja.<br /> A vész-Helyreállítási helyen szükség egy olyan kötetek készlete. | 

A dedikált DR-telepítés, ahol a HANA nagy példány egység a vész-Helyreállítási helyen nem használatos a futó alkalmazások és szolgáltatások vagy nem éles rendszerek. Az egység passzív, és csak akkor, ha katasztrófa feladatátvevő végrehajtja a rendszer van telepítve. Azonban ez az előnyben részesített téve a sok ügyfél.

A többcélú DR-telepítés egy nem éles munkaterhelés futtató a HANA nagy példány egység a vész-Helyreállítási helyen. Vészhelyzet esetén leállítja a nem éles rendszerek, a tároló-rel replikált (további) kötet készletek csatlakoztatja, és majd indítsa el az éles HANA-példány. A legtöbb az ügyfelek, akik a HANA nagy példány vész-helyreállítási funkció, használja ezt a konfigurációt. 


A következő SAP cikkekben található SAP HANA magas rendelkezésre állás további információk: 

- [SAP HANA magas rendelkezésre állású tanulmány](http://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [SAP HANA felügyeleti útmutató](http://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [Az SAP HANA replikációs SAP Academy videó](http://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [Támogatási Megjegyzés #1999880 – gyakori kérdések az SAP HANA replikációs SAP](https://bcs.wdf.sap.corp/sap/support/notes/1999880)
- [SAP támogatási Megjegyzés #2165547 – SAP HANA biztonsági mentése és visszaállítása SAP HANA rendszer replikációs környezeten belül](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [Támogatási Megjegyzés #1984882 – SAP HANA replikációs használatával SAP minimális vagy nulla állásidővel hardver Exchange-hez](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>Vész-helyreállítási HANA nagy osztályt hálózati szempontjai

Nagy HANA-példányok a vész-helyreállítási funkciók előnyeit, a két különböző Azure-régiók való hálózati kapcsolat kialakítása kell. A fő Azure-régió, és egy másik kapcsolat kapcsolat a helyszíni és a vész-helyreállítási régiót kell a helyszíni Azure ExpressRoute körön kapcsolat. Ez a mérték hozzá van rendelve egy helyzet ahol probléma van Azure-régióban, beleértve a Microsoft vállalati peremhálózati útválasztó (MSEE) helyét.

Második mérőszámként minden Azure virtuális hálózatot az Azure (nagy példányok) SAP HANA-hez csatlakoztathatja a régió, amely a HANA nagy példányok más régióban ExpressRoute-kapcsolatcsoportot egyikében. Ennek *közötti csatlakozás*, régió # az 1. az Azure virtuális hálózaton futó szolgáltatások, csatlakozni tud-e nagy példány HANA egység régió 2 és fordítva. Ez a mérték megoldást egy esetet, ahol a MSEE helyek csak az egyik, amely kapcsolódik a helyszíni hely Azure-ral offline állapotba kerül.

A következő ábra vész-helyreállítási rugalmas konfigurációját mutatja be:

![Optimális konfigurációs katasztrófa utáni helyreállítás](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-when-you-use-hana-large-instances-storage-replication-for-disaster-recovery"></a>Egyéb követelmények HANA nagy példányok tárolóreplikálást vész-helyreállítási használatakor

A vész-helyreállítási telepítő HANA nagy osztályt további követelményei a következők:

- Az Azure (nagy példányok) termékváltozatok akkora, mint az éles tárhely termékváltozatok az SAP HANA sorrendben kell, és központi telepítésére a vész-helyreállítási régióban. Ezek a példányok az aktuális felhasználói telepítés, nem éles HANA példányok futtatásához használ. Hivatkozunk azokat *többcélú vész-Helyreállítási beállítások*.   
- A vész-Helyreállítási helyen további tárhely az Azure (nagy példányok) SKU, amely a vész-helyreállítási hely helyreállítja a SAP HANA mindegyikének kell rendelni. További tárhely vásárlás lehetővé teszi a tárolási köteteinek lefoglalásához. A tároló az éles Azure-régió, a vész-helyreállítási Azure-régió, a replikáció célját kötetekhez foglalhatja le.
 

## <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás

Az adatbázisok üzleti legfontosabb szempontja egyik elleni védelem érdekében ellenőrizze a különböző katasztrofális eseményekről. Ezek az események ok foglalhatnak egyszerű felhasználói hibáinak természeti katasztrófa lehet.

Egy adatbázis biztonsági mentéséhez, segítségével visszaállíthatja a bármely időpontra (például valaki a kritikus fontosságú adatok törlése előtt), lehetővé teszi, hogy a visszaállítási állapotba, amely a lehető legközelebb a módját a megszakítás előtt volt.

Kétféle típusú biztonsági mentést kell végezni a legjobb eredmények elérése érdekében:

- Adatbázisok biztonsági: teljes, a növekményes és a különbözeti biztonsági mentések
- Replikálásitranzakciónapló-biztonsági mentések

Teljes-adatbázis a biztonsági másolatok az alkalmazás szintjén, valamint a biztonsági másolatok tárolási pillanatképek végezheti el. Storage-pillanatfelvételekkel-tranzakciónapló biztonsági mentések nem váltják ki. Replikálásitranzakciónapló-biztonsági mentések is fontos, állítsa vissza az adatbázist egy bizonyos mértékig időben, vagy már véglegesített tranzakciók naplóit üres marad. Azonban storage-pillanatfelvételekkel meggyorsíthatja a helyreállítás az adatbázis egy összegző-továbbító kép gyorsan megadásával. 

SAP HANA Azure (nagy példányok) két biztonsági mentési és helyreállítási lehetőségeket kínálja:

- Ehhez saját kezűleg (DIY). Gondoskodjon arról, hogy elegendő lemezterület a számítást, követően hajtsa végre a teljes adatbázis, és biztonsági mentések lemez biztonsági mentési módszerek használatával jelentkezik. Biztonsági másolatot, vagy közvetlenül a HANA nagy példány egységek, vagy a hálózati fájl megosztások (NFS) állítsa be az Azure virtuális gép (VM) a csatlakoztatott kötetek. Az utóbbi esetben az ügyfelek Linux virtuális gépet az Azure-ban, Azure Storage csatolása a virtuális gép és a konfigurált NFS-kiszolgáló adott virtuális gépen keresztül a tárolóban. Elvégzi a biztonsági mentés, amelyhez közvetlenül kapcsolódni HANA nagy példány egységek, kötetek ellen, ha szeretne másolni a biztonsági másolatokat az Azure storage-fiók (Miután beállította az Azure virtuális gép, amely az Azure Storage alapuló NFS-megosztások exportálása). Vagy az Azure mentési tárolóval vagy a cold az Azure storage is használhatja. 

   Egy másik lehetőség, hogy a külső Adateszköz védelmi használja a biztonsági másolatok tárolására, miután másolja őket egy Azure storage-fiókot. Lehet, hogy a DIY biztonsági mentési beállításának is szükséges hosszabb időszakokra, megfelelőségi és naplózási célokra tárolásához szükséges adatokat. Minden esetben a biztonsági mentések átkerülnek a virtuális gép és az Azure Storage képviselt NFS-megosztásokat.

- A biztonságimásolat-készítő és működőképes állapotba hozni, amely az alkalmazás mögötti infrastruktúra az Azure (nagy példányok) SAP HANA biztosít. Ezt a beállítást kell biztonsági mentést és gyors visszaállítások teljesíti. Ez a szakasz a többi oldja meg a biztonsági mentési és visszaállítási funkció HANA nagy osztályt ajánlott. Ez a szakasz is magában foglalja a kapcsolat biztonsági mentés és visszaállítás kell a vész-helyreállítási HANA nagy példányok által kínált funkciót.

> [!NOTE]
> A pillanatkép-technológia, amely az alkalmazás mögötti infrastruktúra nagy HANA-példányok által használt függőség SAP HANA-pillanatképekkel rendelkezik. SAP HANA-pillanatképek ezen a ponton nem működnek együtt az SAP HANA több-bérlős adatbázis tárolók több bérlői. Így a biztonsági mentés a metódus nem használható több bérlő több-bérlős adatbázis SAP HANA-tárolókban lévő központi telepítésekor. Ha csak egy bérlő van telepítve, a SAP HANA-pillanatképek működnek.

### <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>(Nagyméretű példányok) Azure storage-pillanatfelvételekkel a SAP HANA használatával

A tároló-infrastruktúra az alapul szolgáló SAP HANA (nagy példányok) Azure storage-pillanatfelvételekkel kötetek támogatja. Biztonsági mentési és a kötetek helyreállítása támogatott, azzal a következőket kell figyelembe venni:

- Teljes-adatbázis biztonsági másolatait, helyett tárolási kötet pillanatfelvételeket készít a gyakran.
- Pillanatkép kiváltó/hana/adatok, hana/log és /hana/shared (tartalmazza a /usr/sap) kötetek, a tárolási pillanatkép indít el egy SAP HANA pillanatkép készítése a tárolási pillanatkép végrehajtása előtt. Az SAP HANA-pillanatkép végleges napló visszaállítását a telepítő pontja a tárolási pillanatkép a helyreállítás után.
- A pont, ahol a tárolási pillanatkép sikeresen végrehajtva, miután a SAP HANA-pillanatkép törlését.
- Tranzakció-naplók biztonsági másolatainak gyakran kerül, és a /hana/logbackups kötet vagy az Azure-ban tárolja. A tranzakciónapló biztonsági mentések külön-külön pillanatképet tartalmazó /hana/logbackups kötet indíthat el. Ebben az esetben nem kell végrehajtani HANA pillanatképet.
- Ha vissza kell állítania egy adatbázis egy bizonyos mértékig időben, a Microsoft Azure támogatási szolgálatához (a termelési kimaradásáról) vagy a SAP HANA az Azure Service Management bizonyos tárolási pillanatkép visszaállítása kérést. Példa: a tervezett visszaállítás védőfal rendszer eredeti állapotát.
- A SAP HANA-pillanatkép, amely megtalálható a tárolási pillanatkép egy eltolási pont alkalmazásának replikálásitranzakciónapló-biztonsági mentések végrehajtása és a tárolási pillanatkép készítése tárolja.
- A tranzakció-napló biztonsági mentése visszaállítani az adatbázist egy bizonyos mértékig időben kerül.

Storage-pillanatfelvételekkel kötetek három különböző osztályú célzó végezheti el:

- Kombinált pillanatkép/hana/adatokat, és /hana/shared (tartalmazza a/usr/sap) keresztül. A pillanatkép, a tárolási pillanatkép előkészítése egy SAP HANA-pillanatkép létrehozását igényli. Az SAP HANA-pillanatkép fog ellenőrizze, hogy az adatbázis konzisztens állapotú legyen a tároló szempontjából.
- Külön pillanatkép/hana/logbackups keresztül.
- Egy operációsrendszer-partíciót (csak típus HANA nagy példányok I).


### <a name="storage-snapshot-considerations"></a>Tárolási pillanatkép kapcsolatos szempontok

>[!NOTE]
>Storage-pillanatfelvételekkel HANA nagy példány egységeire lefoglalt tárhely felhasználását. Ezért figyelembe kell figyelembe venni az ütemezés a storage-pillanatfelvételekkel és hány tárolási pillanatképet kíván megőrizni a következő szempontokat. 

SAP Hana (nagy példányok) Azure storage-pillanatfelvételekkel a megadott idejéről a következők:

- Egy bizonyos tárolási pillanatkép (ha szükséges idő pontján) igényel, csekély tárolási.
- A tartalmi változások adatok és fájlok megváltozik a tárolókötetet az SAP HANA-adatok tartalma a pillanatkép kell tárolni, az eredeti tartalom blokkolása, valamint az adatok változásait.
- Ennek eredményeképpen a tárolási pillanatkép mérete növekszik. Minél hosszabb a pillanatkép van, annál nagyobb lesz a tárolási pillanatkép.
- A további módosításokat végzett a SAP HANA-adatbázis kötetén tárolási pillanatkép, a nagyobb életciklusa alatt a lemezterület-felhasználást a tárolási pillanatkép.

Az SAP HANA-adatainak és naplókönyvtárainak kötetek mérete rögzített SAP HANA Azure (nagy példányok) tartalmaz. Pillanatképek készítése a köteteket végrehajtása eats a kötet térbe kerülnek. Meg kell határoznia, mikor érdemes ütemezni a storage-pillanatfelvételekkel. Szükség a lemezterület-felhasználást a tároló kötetek figyeléséhez, valamint a tárolt pillanatképek számának kezelését. A storage-pillanatfelvételekkel letilthatja vagy tömegek adatok importálása vagy egyéb jelentős változások a HANA-adatbázishoz. 


A következő szakaszok ezeket a pillanatképeket, beleértve az általános ajánlásokat végrehajtásához információkkal:

- Abban az esetben, ha a hardver kötetenként 255 pillanatképek képes elviselni, erősen ajánlott ennek a számnak alatt maradnak.
- Mielőtt elvégezné a storage-pillanatfelvételekkel, figyeléséhez és nyomon követésére szabad terület.
- A szabad lemezterület alapján tárolási pillanatképek számának csökkentéséhez. Mindig a pillanatképek száma csökkenthető, vagy kiterjesztheti a köteteket. További tárhely 1 terabájtnál egységekbe rendezheti.
- Során tevékenységek, például SAP HANA SAP platform áttelepítési eszközökről (R3load) adatok áthelyezése vagy SAP HANA-adatbázisok visszaállítása biztonsági másolatból tiltsa le a storage-pillanatfelvételekkel a /hana/data köteten. 
- SAP HANA-táblák nagyobb átszervezések során storage-pillanatfelvételekkel kerülni kell, ha lehetséges.
- Storage-pillanatfelvételekkel előfeltételt az Azure (nagy példányok) SAP HANA a vész-helyreállítási képességeit kihasználva.

### <a name="setting-up-storage-snapshots"></a>Storage-pillanatfelvételekkel beállítása

A storage-pillanatfelvételekkel HANA nagy osztályt beállításának lépéseit a következők:
1. Győződjön meg arról, hogy Perl a Linux operációs rendszert a HANA nagy példányok kiszolgálón van telepítve.
2. Módosítsa az/etc/ssh/ssh\_adja hozzá a sort a konfigurációs _Mac számítógépekhez hmac-sha1_.
3. Minden SAP HANA-példány futtatja, a fő csomóponton egy SAP HANA biztonsági mentési felhasználói fiók létrehozása, ha van ilyen.
4. Az SAP HANA HDB ügyfél telepítése a SAP HANA nagy példányok összes kiszolgálójára.
5. Minden egyes régió első SAP HANA nagy példányok kiszolgálón hozza létre a mögöttes tároló-infrastruktúra, amely a pillanatkép létrehozása eléréséhez nyilvános kulcsot.
6. Másolja a parancsfájlok és a konfigurációs fájl [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts) helyére **hdbsql** a SAP HANA-telepítésnek.
7. Módosítsa a megfelelő felhasználói előírásoknak megfelelő HANABackupDetails.txt fájlt.

### <a name="step-1-install-the-sap-hana-hdb-client"></a>1. lépés: Az SAP HANA HDB ügyfél telepítése

SAP HANA Azure (nagy példányok) a Linux operációs rendszerrel a mappák és a szükséges biztonsági mentési és vész-helyreállítási célokra SAP HANA storage-pillanatfelvételekkel parancsfájlok tartalmazza. Ellenőrizze a korábbi kiadásokban [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). A parancsfájlok a legújabb verzió a 2.1.
Azonban feladata a SAP HANA HDB ügyfél telepítése a HANA nagy példány egység, SAP HANA telepítése. (A Microsoft nem telepíti a HDB ügyfél vagy egy SAP HANA.)

### <a name="step-2-change-the-etcsshsshconfig"></a>2. lépés: Módosítsa az/etc/ssh/ssh\_config

Változás `/etc/ssh/ssh_config` hozzáadásával a _Mac számítógépekhez hmac-sha1_ sor itt látható módon:
```
#   RhostsRSAAuthentication no
#   RSAAuthentication yes
#   PasswordAuthentication yes
#   HostbasedAuthentication no
#   GSSAPIAuthentication no
#   GSSAPIDelegateCredentials no
#   GSSAPIKeyExchange no
#   GSSAPITrustDNS no
#   BatchMode no
#   CheckHostIP yes
#   AddressFamily any
#   ConnectTimeout 0
#   StrictHostKeyChecking ask
#   IdentityFile ~/.ssh/identity
#   IdentityFile ~/.ssh/id_rsa
#   IdentityFile ~/.ssh/id_dsa
#   Port 22
Protocol 2
#   Cipher 3des
#   Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-cbc
#   MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd160
MACs hmac-sha1
#   EscapeChar ~
#   Tunnel no
#   TunnelDevice any:any
#   PermitLocalCommand no
#   VisualHostKey no
#   ProxyCommand ssh -q -W %h:%p gateway.example.com
```

### <a name="step-3-create-a-public-key"></a>3. lépés:, Hozzon létre egy nyilvános kulcsot

A tárolási pillanatkép felületek HANA nagy példány bérlője elérésének lehetővé tétele, kell megállapítani a bejelentkezéshez nyilvános kulcsot. Az első SAP HANA-bérlőben Azure (nagy példányok) kiszolgálón hozzon létre egy nyilvános kulcs használatával elérhető a tároló-infrastruktúra, pillanatképeket hozhat létre. A nyilvános kulcs biztosítja, hogy jelentkezzen be a tárolási pillanatkép felületek nem szükséges jelszót. A nyilvános kulcs létrehozása is jelenti, hogy nem kell jelszót hitelesítő adatok karbantartása. Linux a SAP HANA nagy példányok kiszolgálón, a hajtsa végre a következő parancsot a nyilvános kulcs létrehozásához:
```
  ssh-keygen –t dsa –b 1024
```
Az új hely **_/root/.ssh/id\_dsa.pub**. Ne adjon meg egy tényleges jelszót, vagy pedig be kell írnia a jelszót, minden egyes bejelentkezéskor. Ehelyett válassza ki **Enter** kétszer a "jelszó" engedélyezése esetén nem jelentkezik be.

Ellenőrizze, hogy, hogy a nyilvános kulcs javította mappák módosításával elvárt **/root/.ssh/** majd végrehajtása és a `ls` parancsot. Ha a kulcs található, másolja azt a következő parancs futtatásával:

![A nyilvános kulcsot másolja a parancs futtatása](./media/hana-overview-high-availability-disaster-recovery/image2-public-key.png)

Ezen a ponton lépjen kapcsolatba az Azure szolgáltatásfelügyelet SAP HANA, és adja meg a nyilvános kulcsot. A szolgáltatás képviselő a nyilvános kulcsával regisztrálja azt a mögöttes tároló-infrastruktúra, amely van faragottnak a HANA nagy példány bérlő számára.

### <a name="step-4-create-an-sap-hana-user-account"></a>4. lépés: Egy SAP HANA-felhasználói fiók létrehozása

SAP HANA-pillanatképek létrehozásának kezdeményez, hozzon létre egy felhasználói fiókot, amellyel a tárolási pillanatkép parancsfájlok SAP HANA kell. Létre kell hozni egy SAP HANA Studio SAP HANA-felhasználói fiókot. Ezt a fiókot a következő jogosultságokkal kell rendelkeznie: **biztonsági mentési rendszergazda** és **katalógus olvasási**. Ebben a példában a felhasználónév az **SCADMIN**. A felhasználói fiók nevét a HANA Studióban létrehozott a kis-és nagybetűket. Ügyeljen arra, hogy válasszon **nem** , amelyek a felhasználó megváltoztassa a jelszavát a következő bejelentkezés esetén.

![A felhasználó létrehozása a HANA Studióban](./media/hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

### <a name="step-5-authorize-the-sap-hana-user-account"></a>5. lépés: Engedélyezze a SAP HANA-felhasználói fiók

Ebben a lépésben is engedélyezni szeretné a létrehozott, SAP HANA-felhasználói fiókot, hogy a parancsfájlok nem kell küldenie a jelszavak futásidőben. Az SAP HANA-parancs `hdbuserstore` létrehozását lehetővé tevő egy SAP HANA felhasználói kulcs, amely legalább egy SAP HANA-csomópontja tárolja. A felhasználói kulcs lehetővé teszi, hogy a felhasználói hozzáférés SAP HANA a parancsfájl-kezelési folyamaton belül a jelszavak kezelése nélkül. A parancsfájl-kezelési folyamat tárgyalt később.

>[!IMPORTANT]
>A következő parancsot, `root`. Ellenkező esetben a parancsfájl nem működik megfelelően.

Adja meg a `hdbuserstore` parancsot a következőképpen:

**A telepítő nem MDC HANA**
```
hdbuserstore set <key> <host>:<3[instance]15> <user> <password>
```

**Az MDC HANA-telepítő**
```
hdbuserstore set <key> <host>:<3[instance]13> <user> <password>
```

A következő példában a felhasználó, **SCADMIN01**, az állomásnév **lhanad01**, és a példány szám **01**:
```
hdbuserstore set SCADMIN01 lhanad01:30115 <backup username> <password>
```
Ha egy SAP HANA-kibővített konfigurációt, kezelje az összes parancsfájl-kezelési ugyanazon a kiszolgálón. Ebben a példában a SAP HANA-kulcs **SCADMIN01** módosítani kell úgy, hogy melyik gazdagépre kapcsolódik a kulcs tükrözi minden állomás számára. Az SAP HANA a példányszámának a HANA-adatbázis biztonsági mentési fiók módosítása. A kulcs rendszergazdai jogosultsággal kell rendelkeznie a gazdagépen hozzá van rendelve, és a biztonsági mentési felhasználó kibővített konfigurációk SAP HANA-példányainak hozzáférési jogosultsággal kell rendelkeznie. Feltéve, hogy a három kibővített csomópontok a nevük legyen **lhanad01**, **lhanad02**, és **lhanad03**, a parancssorrend néz ki:

```
hdbuserstore set SCADMIN01 lhanad01:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad02:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad03:30115 SCADMIN <password>
```

### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>6. lépés: A pillanatkép-parancsfájlok Get, konfigurálja a pillanatképek és a konfiguráció és a kapcsolat tesztelése

Töltse le a legfrissebb verzióját a parancsfájlokat a [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). Másolja a letöltött parancsfájlok és a szöveges fájl munkakönyvtára **hdbsql**. Az aktuális HANA telepítésekor ez a könyvtár hasonlít /hana/shared/D01/exe/linuxx86\_64/hdb. 
``` 
azure_hana_backup.pl 
azure_hana_replication_status.pl 
azure_hana_snapshot_details.pl 
azure_hana_snapshot_delete.pl 
testHANAConnection.pl 
testStorageSnapshotConnection.pl 
removeTestStorageSnapshot.pl 
HANABackupCustomerDetails.txt 
``` 


A különböző parancsfájlok és a fájlok célja van:

- **Azure\_hana\_backup.pl**: ezt a parancsfájlt a storage-pillanatfelvételekkel végrehajtani a HANA napló, adatokat vagy megosztott kötetek, a/hana/logbackups köteten, vagy az operációs rendszer (a i. típusú termékváltozatok a HANA nagy példányok) cron ütemezni.
- **Azure\_hana\_replikációs\_status.pl**: ezt a parancsfájlt biztosít a replikációs állapot a munkakörnyezeti helyet a vész-helyreállítási hely körül alapvető adatait. A parancsfájl figyelőket, és győződjön meg arról, hogy a replikáció, és ebben az elemeknek a méretét mutatja, a rendszer replikálja. Is nyújt útmutatást, ha túl sokáig tart a replikációt, vagy ha a kapcsolat nem működik.
- **Azure\_hana\_pillanatkép\_details.pl**: Ez a parancsfájl minden pillanatképet, kötetenként, a környezetben meglévő kapcsolatos alapvető tudnivalókat listáját tartalmazza. Ez a parancsfájl futtathatja az elsődleges kiszolgálón vagy egy kiszolgáló egységen, a vész-helyreállítási helyen. A parancsfájl a következő adatokat, minden olyan kötetre, amely tartalmazza a pillanatképek bontásban tartalmazza:
   * A teljes pillanatképek a kötet mérete
   * Minden pillanatképet a köteten a következő adatokat tartalmazza: 
      - Pillanatfelvétel neve 
      - Létrehozás ideje 
      - A pillanatkép mérete
      - A pillanatkép gyakorisága
      - Ha szükséges, hogy a pillanatkép társított HANA biztonsági azonosítója
- **Azure\_hana\_pillanatkép\_delete.pl**: Ez a parancsfájl törli tárolási pillanatkép vagy egy pillanatképet. Az SAP HANA biztonsági mentési azonosító található a HANA Studio vagy a pillanatkép nevét is használhatja. A biztonsági mentési azonosító jelenleg csak a HANA napló, adatokat vagy megosztott kötetek készült pillanatkép kötődik. Ellenkező esetben a pillanatkép-azonosító is meg kell adni, ha kíván minden pillanatképet, amelyek megfelelnek a megadott pillanatkép azonosítója.  
- **testHANAConnection.pl**: Ez a parancsfájl létesíthető kapcsolat az SAP HANA-példány és a storage-pillanatfelvételekkel beállításához szükséges.
- **testStorageSnapshotConnection.pl**: Ez a parancsfájl két célja van. Először is biztosítja, hogy a nagy példány HANA egység, amely futtatja a parancsfájlokat hozzáfér a hozzárendelt tároló virtuális gépet, és a nagy HANA-példányok tárolófelület pillanatkép. A második célja a tesztelni kívánt HANA példány ideiglenes pillanatkép létrehozása. Ezt a parancsfájlt annak érdekében, hogy a biztonsági mentési parancsfájlok fognak megfelelően működni a kiszolgálón kell futtatni minden HANA-példányhoz.
- **removeTestStorageSnapshot.pl**: Ez a parancsfájl törli a pillanatkép-parancsfájllal létre teszt **testStorageSnapshotConnection.pl**. 
- **HANABackupCustomerDetails.txt**: ezt a fájlt egy olyan módosítania kell a SAP HANA-konfiguráció támogató módosíthatóvá konfigurációs fájl.

 
HANABackupCustomerDetails.txt fájl a storage-pillanatfelvételekkel futtatott parancsfájl a vezérlő és a konfigurációs fájlban. Állítsa be a fájlt a célokra és a telepítőt. Kapott kell a **tárolási biztonsági másolat neve** és **tárolási IP-cím** az SAP HANA az Azure szolgáltatásfelügyelet, ha a példányok üzembe helyezése. A sorozat nem módosítható, rendezés, vagy bármely, a változók a fájlban található térköz. Ellenkező esetben a parancsfájlok nem fog működni. Emellett kapott az IP-címét a méretezett vagy a fő csomópontot (Ha a kibővített) az Azure szolgáltatásfelügyelet SAP HANA. Az SAP HANA telepítésekor portáltól HANA példányszámának is ismeri. Most szeretne hozzáadni egy biztonsági másolat neve a konfigurációs fájl.

A konfigurációs fájl méretezett és kibővített üzembe helyezés esetén lenne az alábbi példához hasonló után kitöltötte a biztonsági mentési nevét és a tárolási IP-címet. Szükség töltse ki a konfigurációs fájlban a következő adatokat:
- Egyetlen csomópont vagy a fő csomópont IP-cím
- HANA példányszámának
- Biztonsági másolat neve 
    
```
#Provided by Microsoft Service Management
Storage Backup Name: client1hm3backup
Storage IP Address: 10.240.20.31
#Node IP addresses, instance numbers, and HANA backup name
#provided by customer.  HANA backup name created using
#hdbuserstore utility.
Node 1 IP Address: 
Node 1 HANA instance number:
Node 1 HANA userstore Name:
```

>[!NOTE]
>Jelenleg csak az 1. csomóponton részletek szerepelnek a tényleges HANA tárolási pillanatkép parancsfájl. Azt javasoljuk, hogy ellenőrizze hozzáférés vagy minden HANA csomópontról úgy, hogy ha valaha is változik, a biztonsági mentési főcsomópont, már gondoskodott róla, hogy bármely másik csomópont végrehajthatók lesznek az 1. csomópont részletei módosításával.

A konfigurációs adatokat a HANABackupCustomerDetails.txt fájlba helyezni, után kell, hogy a konfiguráció helyes-e a HANA példányadatokat kapcsolatban. A parancsfájllal `testHANAConnection.pl`. Ez a parancsfájl nem függ egy SAP HANA méretezett és kibővített konfigurációt.

```
testHANAConnection.pl
```

Ha egy SAP HANA-kibővített konfigurációt, győződjön meg arról, hogy a fő HANA példány fér hozzá az összes szükséges HANA kiszolgálók és példányok. Nincsenek paraméterei, a teszt-parancsfájlba, de az adatok hozzá kell adnia a megfelelő futtatásához szükséges parancsfájl HANABackupCustomerDetails.txt konfigurációs fájlba. Csak a rendszerhéj parancs hibakódok ad vissza, ezért nem lehetséges a parancsfájl hibáját ellenőrizze mindegyik előfordulását. Még ha így a parancsfájl megjegyzést néhány hasznos ahhoz, hogy újra ellenőrizni.

A parancsfájl futtatásához írja be a következő parancsot:
```
 ./testHANAConnection.pl
```
Ha a parancsfájl sikeresen beszerzi a HANA példány állapotát, hogy sikeres volt-e a HANA kapcsolat üzenetet jelenít meg.


A következő vizsgálat-lépésre, hogy ellenőrizze a kapcsolatot a HANABackupCustomerDetails.txt konfigurációs fájlba adatok-alapú tárolás és egy tesztelési pillanatkép hajthat végre. Végrehajtása előtt a `azure_hana_backup.pl` parancsfájl, végre kell hajtani ezt a vizsgálatot. Ha egy kötet pillanatképek nem tartalmaz, lehetetlen határozza meg, hogy a kötet üres, vagy hogy van-e az SSH-hiba a pillanatkép részletek beszerzése érdekében. Emiatt a parancsfájl végrehajtása két lépésből áll:

- Azt ellenőrzi, hogy a bérlő tároló virtuális gép és felületek a parancsfájlok pillanatképek számára.
- Pillanatképet hoz létre-teszt során, vagy helyőrző, minden olyan kötetre HANA példánya.

Emiatt a HANA példány része, amelynek argumentuma. A végrehajtás sikertelen lesz, ha nincs lehetőség arra, hogy a tárolási kapcsolat ellenőrzését. Akkor is, ha nincs ellenőrzése hiba, a parancsfájl biztosít hasznos mutatók.

A parancsfájl futtatása:
```
 ./testStorageSnapshotConnection.pl <HANA SID>
```
A következő megpróbálja jelentkezzen be a tároló az előző lépésben a telepítő és az adatok konfigurálva a HANABackupCustomerDetails.txt fájlban megadott nyilvános kulccsal. Ha a bejelentkezés sikeres, a következő tartalmat látható:

```
**********************Checking access to Storage**********************
Storage Access successful!!!!!!!!!!!!!!
```

Ha problémák merülnek fel a tárolási konzol csatlakozik, akkor a kimenete néz ki:

```
**********************Checking access to Storage**********************
WARNING: Storage check status command 'volume show -type RW -fields volume' failed: 65280
WARNING: Please check the following:
WARNING: Was publickey sent to Microsoft Service Team?
WARNING: If passphrase entered while using tool, publickey must be re-created and passphrase must be left blank for both entries
WARNING: Ensure correct IP address was entered in HANABackupCustomerDetails.txt
WARNING: Ensure correct Storage backup name was entered in HANABackupCustomerDetails.txt
WARNING: Ensure that no modification in format HANABackupCustomerDetails.txt like additional lines, line numbers or spacing
WARNING: ******************Exiting Script*******************************
```

Után egy sikeres jelentkezzen be a tároló virtuális gép felületek a parancsfájl #2. fázis folytatja, és létrehoz egy tesztelési pillanatképet. A kimeneti itt látható az SAP HANA-három csomópontos kibővített konfigurációt:

```
**********************Creating Storage snapshot**********************
Taking snapshot testStorage.recent for hana_data_hm3_mnt00001_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00001_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00002_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00002_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00003_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00003_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_backups_hm3_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_backups_hm3_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00001_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00002_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00003_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_shared_hm3_t020_vol ...
Snapshot created successfully.
```

Ha a teszt pillanatkép a parancsfájl sikeresen végrehajtva, a tényleges storage-pillanatfelvételekkel konfigurálásánál lépne. Ha nem sikeres, a problémák vizsgálatához a előtt folytassa. A teszt pillanatkép körül kell maradnak, amíg meg nem történik az első valós pillanatképeket.


### <a name="step-7-perform-snapshots"></a>7. lépés:, Hajtsa végre a pillanatképek

Az előkészítő lépések készként megkezdheti a tényleges pillanatkép konfiguráció konfigurálásához. A parancsfájl ütemezhető SAP HANA méretezett és kibővített konfigurációk működik. Olyan időszakra ütemezze keresztül cron parancsfájlok végrehajtása. 

Három típusú pillanatképes biztonsági hozhatók létre:
- **HANA**: kombinált, amelyben a kötetek/hana/adatokat tartalmazó és a/hana/megosztott (/usr/sap is tartalmazó) tartoznak a koordinált pillanatkép pillanatkép biztonsági másolatából. Egyetlen fájlvisszaállításra lehetőség a pillanatképből.
- **Naplók**: a/hana/logbackups kötet pillanatkép biztonsági másolatából. Nincs HANA pillanatkép akkor váltódik ki, a tárolási pillanatkép végrehajtásához. A tárolási kötet kellene tartalmaznia a SAP HANA-tranzakciónapló biztonsági mentések a köteten. SAP HANA-tranzakciónapló mentések gyakrabban és napló növekedés esetleges adatvesztés elkerülése érdekében. Egyetlen fájlvisszaállításra lehetőség a pillanatképből. Öt perc alatt a gyakoriság nem csökkenteni kell.
- **Rendszerindító**: a rendszerindító logikai egységen (LUN) nagy HANA-példány tartalmazó kötet készítése. A pillanatkép biztonsági másolatából csak a Type I termékváltozatok a HANA nagy példányok lehetséges. Nem hajtható végre egy fájlból visszaállítások a kötet, amely tartalmazza a rendszerindító LUN pillanatképből. A típus II termékváltozatok a HANA nagy példányok az operációs rendszer szintű biztonsági másolatok készítéséhez, és állítsa vissza az egyes fájlok. Tekintse meg a dokumentum "[hogyan hajtsa végre az operációs rendszer készült biztonsági mentés típusa II termékváltozatok](os-backup-type-ii-skus.md)" olvashat.


A hívási szintaxis a pillanatképek három különböző típusú így néz ki:
```
HANA backup covering /hana/data and /hana/shared (includes/usr/sap)
./azure_hana_backup.pl hana <HANA SID> manual 30

For /hana/logbackups snapshot
./azure_hana_backup.pl logs <HANA SID> manual 30

For snapshot of the volume storing the boot LUN
./azure_hana_backup.pl boot none manual 30

```

A következő paramétereket kell megadni:

- Az első paraméter karakterizálja a pillanatkép biztonsági másolat típusa. Az engedélyezett értékek a következők **hana**, **naplók**, és **rendszerindító**. 
- A második paraméter **HANA SID** (például HM3) vagy **nincs**. Ha az első paraméter értéke a megadott **hana** vagy **naplók**, akkor ez a paraméter értéke **HANA SID** (például HM3), ellenkező esetben rendszerindító kötetek biztonsági mentésének értéke **nincs**. 
- A harmadik paramétere egy snapshot vagy snapshot típusú biztonsági mentési címke. Két célja van. Az egy az Ön célja adjon neki egy nevet, így ismerjük ezeket a pillanatképeket vonatkozó. A második feladata a parancsfájl Azure\_hana\_backup.pl függvénnyel határozhatja meg, hogy adott címke alatt maradnak storage-pillanatfelvételekkel számát. Ha ütemez két tárolási pillanatképes biztonsági ugyanabba a típusba tartozik (például **hana**), két különböző címkével, és határozza meg, hogy 30 pillanatképek kell tartani minden, az érintett kötetek 60 storage-pillanatfelvételekkel végül kívánja. 
- A negyedik paraméter a pillanatképek megőrzési közvetve, azonos előtaggal pillanatkép (címke) meg kell őrizni a pillanatképek számáról megadásával határozza meg. Ez a paraméter egy ütemezett végrehajtási keresztül cron fontos. 

Egy kibővített esetén a parancsfájl nincs néhány további ellenőrzi, hogy ellenőrizze, hogy el tudja érni a HANA összes kiszolgálójára. A parancsfájl azt is ellenőrzi, hogy példányainak HANA térjen vissza a megfelelő állapot példánya, egy SAP HANA-pillanatkép létrehozása előtt. Az SAP HANA-pillanatkép tárolási pillanatkép követi.

A parancsfájl végrehajtása `azure_hana_backup.pl` hoz létre a pillanatkép készítése a következő három különböző fázisait tároló:

1. Egy SAP HANA-pillanatkép végrehajtása
2. Végrehajtja a tárolási pillanatkép
3. Eltávolítja az SAP HANA-pillanatképet, amely a tárolási pillanatkép végrehajtása előtt lett létrehozva

A parancsfájl végrehajtása hívja, a Másolás HDB végrehajtható mappáját. 

A megőrzési időtartam történik az paraméterként a parancsfájl végrehajtása esetén elküldi a pillanatképek számát (például **30**, korábban látható). Igen, az, hogy mennyi ideig hatálya alá tartozik a storage-pillanatfelvételekkel feladata a következő két dolog: az időtartam, végrehajtási és elküldött paraméterként, amikor a parancsfájl végrehajtása a pillanatképek számára. Ha tárolják a pillanatképek száma meghaladja-e a szám, amelyet a hívás a parancsfájl a legrégebbi tárolási pillanatkép azonos címkékből paraméterként megnevezett (ebben az előző esetben **manuális**) egy új pillanatkép végrehajtása előtt el kell hagyni. Az ad, a hívás utolsó paraméterének száma segítségével tárolják a pillanatképek számát. Ez a számú is szabályozhatja, közvetve, a pillanatképek felhasznált lemezterület. 

> [!NOTE]
>Amint a címkék módosításához a számlálási újraindul. Ez azt jelenti, hogy a címkézés, a pillanatképek nem véletlenül törölt szigorú kell.

### <a name="snapshot-strategies"></a>Pillanatkép stratégiák
A különböző pillanatképek gyakorisága attól függ, hogy használ-e a HANA nagy példány vész-helyreállítási funkció vagy nem. A vész-helyreállítási funkció HANA nagy példányok storage-pillanatfelvételekkel támaszkodik. A storage-pillanatfelvételekkel függő szükség lehet néhány speciális ajánlások a storage-pillanatfelvételekkel gyakoriságát és végrehajtási idő szempontjából. 

A szempontokat és javaslatokat, az alábbi, feltételezzük, hogy végezzen *nem* HANA nagy példányok kínál a vész-helyreállítási funkciójának használatát. Ehelyett használja a storage-pillanatfelvételekkel kíván biztonsági mentések és időpontban helyreállítást biztosít az elmúlt 30 napban. A pillanatképek és a terület száma vonatkozó korlátozások miatt az ügyfelek tekintette az alábbi követelményeknek:

- A helyreállítási idő időpontban helyreállításhoz.
- A használt lemezterület.
- A helyreállításipont-célkitűzés és a helyreállítási idő célkitűzése a lehetséges vész-helyreállítási.
- A végleges végrehajtása HANA teljes-adatbázis biztonsági mentését lemezek ellen. Amikor teljes-adatbázis biztonsági másolatának elleni lemezek vagy a **backint** felület történik, a storage-pillanatfelvételekkel végrehajtása sikertelen lesz. Ha azt tervezi, hogy a teljes-adatbázis biztonsági másolatait fölött storage-pillanatfelvételekkel hajtható végre, ellenőrizze, hogy a storage-pillanatfelvételekkel végrehajtása le van tiltva ebben az időszakban.
- Megfelelő számú pillanatkép kötetenként 255 korlátozódik.


A vész-helyreállítási funkciók nagy HANA-példányok nem használó ügyfelek a pillanatkép időszak ritkábban. Ebben az esetben az ügyfelek a kombinált pillanatképek végrehajtására 12 órás vagy 24 órás időszak /hana/data és /hana/shared (tartalmazza a /usr/sap) látható, és a pillanatképek fedik le a teljes hónap vezetnek. Ugyanez érvényes a napló biztonsági mentési kötet pillanatfelvételekkel. Azonban a napló biztonsági mentési köteten SAP HANA-tranzakciónapló biztonsági mentések végrehajtása történik 5 perces 15 perces időszakokra.

Javasoljuk, hogy ütemezett storage-pillanatfelvételekkel végrehajtása cron használatával. Azt javasoljuk, hogy az összes biztonsági másolatot használja ugyanazt a parancsfájlt, és vész-helyreállítási igényeket. Módosítania kell a parancsfájl a forráson felel meg a különböző biztonsági mentési alkalommal kért. Ezeket a pillanatképeket minden ütemezett eltérően a attól függően, hogy a végrehajtási idő cron: óránkénti, 12 órás, napi vagy heti. 

Példa egy cron-ütemezését a /etc/crontab nézhet ki:
```
00 1-23 * * * ./azure_hana_backup.pl hana HM3 hourlyhana 46
10 00 * * *  ./azure_hana_backup.pl hana HM3 dailyhana 28
00,05,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
22 12 * * *  ./azure_hana_backup.pl log HM3 dailylogback 28
30 00 * * *  ./azure_hana_backup.pl boot dailyboot 28
```
Az előző példában van egy óránkénti kombinált pillanatkép, amely lefedi a/hana/adatokat tartalmazó kötetek és /hana/shared (tartalmazza a/usr/sap) helyét. Az ilyen típusú pillanatkép az elmúlt két nap gyorsabban időpontban helyreállítási volna használható. Ezenkívül nincs napi pillanatkép azokon a köteteken. Igen, két napja van a érvényességének által óránkénti pillanatképeket, valamint a érvényességének négy hétben napi pillanatképek által. A tranzakció-napló biztonsági mentési kötet ezenkívül naponta egyszer készül. Ezek a biztonsági másolatok, valamint négy hétig tartanak. Ahogyan a képen látható, a harmadik sorban a crontab, a HANA tranzakciós napló biztonsági mentését végrehajtani, ötpercenként van ütemezve. A start perc, a különböző cron feladatok, amelyek végrehajtása a storage-pillanatfelvételekkel kerülnek lépcsőzetes elrendezésben, így ezek a pillanatképek nem hajtja végre egyszerre egy bizonyos időpontban időben. 

A következő példában hajtsa végre, amely lefedi a/hana/adatok és a/hana/megosztott (többek között a következőket/usr/sap) helyeket óránként tartalmazó kötetek összesített pillanatképet. Két napig megőrzi ezeket a pillanatképeket. A pillanatképek a tranzakciónapló kötetek biztonsági mentése a egy 5 perces időközönként hajtja végre, és négyórás tartanak. Mint előtt, a biztonsági mentés a HANA tranzakciós naplófájl végrehajtására ütemezett 5 percenként. A pillanatkép-tranzakciónapló biztonsági mentési kötet kétperces késéssel történik, a tranzakciónapló biztonsági mentés megkezdése után. E két percen belül a SAP HANA-tranzakciónapló biztonsági mentés be kell fejeződnie, normál körülmények között. Előtt, a rendszerindító tartalmazó kötet LUN tárolási pillanatkép biztonsági mentését naponta egyszer és négy hétig maradnak.

```
10 0-23 * * * ./azure_hana_backup.pl hana HM3 hourlyhana 48
0,5,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup.pl log HM3 logback 48
30 00 * * *  ./azure_hana_backup.pl boot dailyboot 28
```

A következő ábra az előző példában a logikai egység rendszerindító kivételével sorozatát mutatja be:

![A pillanatképek és a biztonsági mentés közötti kapcsolat](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA hajt végre a /hana/log kötet milyen véglegesített módosításokat az adatbázis rendszeres írására. Rendszeresen SAP HANA ír egy mentési pontra a /hana/data kötetre. Egy SAP HANA-tranzakciónapló biztonsági mentést crontab megadott, ötpercenként végrehajtása. Is láthatja, hogy egy SAP HANA-pillanatkép végrehajtja a rendszer minden órában miatt kombinált tárolási pillanatkép időt a /hana/data és /hana/shared kötetek keresztül. Miután a HANA pillanatkép sikeres, a kombinált tárolási pillanatkép végrehajtása. Útmutatását crontab, a tárolási pillanatkép a /hana/logbackup köteten minden öt perc, a két percig HANA-tranzakciónapló biztonsági mentése után a végrehajtása.


>[!IMPORTANT]
> A storage-pillanatfelvételekkel SAP HANA biztonsági mentés használata értékes csak akkor, ha a pillanatképek SAP HANA-tranzakciónapló biztonsági mentések együtt történik. A tranzakció-napló biztonsági mentése képesnek kell lenniük, amelyek között a storage-pillanatfelvételekkel időszakokat. 

Ha a beállított felhasználók 30 napnyi pont időponthoz kötött helyreállítás kötelezettségvállalás, tegye a következőket:

- Szélsőséges esetben szüksége van egy kombinált tárolási over/hana/pillanatképadatok és /hana/shared, amely 30 napos elérését.
- A kombinált tárolási készített pillanatképeket között eltelő idő összefüggő tranzakciónapló biztonsági mentések rendelkezik. Igen a legrégebbi pillanatkép-tranzakciónapló biztonsági mentési kötet kell lennie a 30 napos. Nem ez a helyzet, ha a tranzakciónapló biztonsági mentések másolása másik NFS-megosztás, az Azure storage található. Ebben az esetben előfordulhat, hogy lekéréses régi tranzakciónapló készített biztonsági másolat, hogy az NFS-megosztások.

Is kihasználhatják a storage-pillanatfelvételekkel és a végleges tárolóreplikálást-tranzakciónapló biztonsági mentések, módosítani szeretné a SAP HANA írja a tranzakció-naplók biztonsági másolatainak helyét. Ez a változás HANA Studio tehet. Bár a SAP HANA biztonsági másolatot készít a naplófájl teljes szegmensek automatikusan, adja meg a napló biztonsági mentési időközt determinisztikus. Ez különösen igaz olyan esetben használata esetén a vész-helyreállítási beállítások általában szeretné végrehajtani a napló-biztonságimásolatokat determinisztikus időtartam. A következő esetben azt a napló biztonsági mentési időszakaként 15 percet vett igénybe.

![Az SAP HANA Studio naplózza az SAP HANA-biztonsági mentés ütemezése](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Dönthet úgy, mint 15 percenként biztonsági mentéseket. Ez gyakran a vész-helyreállítási párhuzamosan történik. Egyes ügyfelek biztonsági másolatokat tranzakciónapló 5 perc.  

Az adatbázis soha nem készült biztonsági másolat, a végső lépés esetén a fájl alapú adatbázis biztonsági mentést készíteni, amely a biztonságimásolat-katalógus léteznie kell egy biztonsági mentési bejegyzés létrehozása. Ellenkező esetben SAP HANA nem indítható el a megadott napló biztonsági másolatokat.

![Fájl alapú biztonsági mentést készíteni a biztonsági mentési egyetlen bejegyzés létrehozásához](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Az első sikeres storage-pillanatfelvételekkel végrehajtása után törölheti a 6. lépésben végrehajtott teszt pillanatkép is. Ehhez futtassa a parancsfájlt `removeTestStorageSnapshot.pl`:
```
./removeTestStorageSnapshot.pl <hana instance>
```

### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Figyelési száma és mérete a lemez köteten pillanatképek

Egy adott tárolási köteten figyelheti a pillanatképek és a tárhelyhasználatot, ezek a pillanatképek számát. A `ls` parancs nem jeleníti meg a pillanatkép könyvtárt vagy fájlokat. Azonban a Linux operációs rendszert futtató parancs `du` ezeket a storage-pillanatfelvételekkel részleteit jeleníti meg, mert ezeket a azonos köteteken tárolja. A parancs is használható a következő beállításokkal:

- `du –sh .snapshot`: A pillanatkép-könyvtárban lévő összes pillanatképet összesen biztosít.
- `du –sh --max-depth=1`: Felsorolja a mentett pillanatképeket a **.snapshot** mappa, és minden pillanatkép méretét.
- `du –hc`: A teljes mérete, a pillanatképek által használt biztosít.

Ezek a parancsok segítségével győződjön meg arról, hogy a végrehajtott és tárolt pillanatképek nem használ fel azokon a köteteken a tárolót.

>[!NOTE]
>A pillanatképek a Boot logikai egység nem láthatók el az előző parancsokkal.

### <a name="getting-details-of-snapshots"></a>A pillanatképek-információk lekérdezése
További információkhoz juthat a pillanatképeket, használhatja a parancsfájl `azure_hana_snapshot_details.pl`. Ez a parancsfájl az egyik helyen futtatható, ha nincs aktív kiszolgáló a vész-helyreállítási helyen. A parancsfájl a következő kimeneti, minden olyan kötetre, amely tartalmazza a pillanatképek bontásban tartalmazza: 
   * A teljes pillanatképek a kötet mérete
   * Minden pillanatképet a köteten a következő adatokat tartalmazza: 
      - Pillanatfelvétel neve 
      - Létrehozás ideje 
      - A pillanatkép mérete
      - A pillanatkép gyakorisága
      - Ha szükséges, hogy a pillanatkép társított HANA biztonsági azonosítója

A parancsfájl végrehajtási szintaxisát így néz ki:

```
./azure_hana_snapshot_details.pl 
```

Megpróbálja beolvasni a HANA biztonsági Azonosítóját, mert azt kapcsolódnia kell az SAP HANA-példány. Ez a kapcsolat szükséges a konfigurációs fájl HANABackupCustomerDetails.txt megfelelően kell beállítani. A két pillanatképek a kötet kimenetnek nézhet ki:

```
**********************************************************
****Volume: hana_shared_SAPTSTHDB100_t020_vol       ***********
**********************************************************
Total Snapshot Size:  411.8MB
----------------------------------------------------------
Snapshot:   customer.2016-09-20_1404.0
Create Time:   "Tue Sep 20 18:08:35 2016"
Size:   2.10MB
Frequency:   customer 
HANA Backup ID:   
----------------------------------------------------------
Snapshot:   customer2.2016-09-20_1532.0
Create Time:   "Tue Sep 20 19:36:21 2016"
Size:   2.37MB
Frequency:   customer2
HANA Backup ID:   
```


### <a name="file-level-restore-from-a-storage-snapshot"></a>Egy tároló pillanatképből fájlszintű visszaállítása
A pillanatkép-típusok hana és a naplókat, képesek közvetlenül azokon a köteteken, a pillanatképek eléréséhez a **.snapshot** könyvtár. Nincs minden, a pillanatképek egyik alkönyvtár. Másolhatja a minden fájl által a pillanatkép helyén, hogy a tényleges directory struktúrába alkönyvtár a pillanatkép kellett állapotban kell lennie.

>[!NOTE]
>Visszaállítás egy fájlból nem működik a rendszerindító LUN pillanatkép-készítési. A **.snapshot** directory a rendszerindítási logikai egység nem lesz közzétéve. 


### <a name="reducing-the-number-of-snapshots-on-a-server"></a>Egy kiszolgálón pillanatképek számának csökkentése

Ahogy korábban, csökkentheti az egyes címkék tárolt pillanatképek számát. Az utolsó két pillanatkép kezdeményezni a parancs paraméterei a címkét, és meg szeretné őrizni a pillanatképek számára.

```
./azure_hana_backup.pl hana HM3 hanadaily 30
```

Az előző példában a pillanatkép címkéje **ügyfél** és a következő címkével megőrzendő pillanatképek száma **30**. Lemezterület-felhasználást válaszolni, mivel a tárolt pillanatképek számának csökkentése érdekében érdemes. A egyszerűen 15, például a pillanatképek számának csökkentése érdekében beállítva az utolsó paraméterrel a parancsfájl futtatásához **15**:

```
./azure_hana_backup.pl hana HM3 hanadaily 15
```

A parancsfájl futtatása ezt a beállítást, ha a pillanatképek számáról, beleértve az új tárolási pillanatkép érték 15. A 15 legutóbbi pillanatfelvételek megmaradnak, mivel a 15 régebbi pillanatképeket a rendszer törli.

 >[!NOTE]
 > Ez a parancsfájl csökkenti a pillanatképek csak akkor, ha vannak, amelyek több mint egy órával korábbiak pillanatképek. A parancsfájl nem törli a pillanatképeket, amelyek kevesebb, mint egy órával korábbiak. Ezek a korlátozások a választható vész-helyreállítási funkciói kapcsolódnak.

Ha már nem szeretné egy adott biztonsági mentési címkével pillanatképek készlete karbantartása **hanadaily** szintaxis példákban hajtható végre a parancsprogram **0** megőrzési számának. Ezzel eltávolítja a label megfelelő összes pillanatképet. Azonban minden pillanatképet eltávolítása hatással lehet a vész-helyreállítási képességeit.

A második lehetőség törölheti az adott pillanatképet, a parancsfájl `azure_hana_snapshot_delete.pl`. Ez a parancsfájl célja, hogy a törölni kívánt pillanatképet vagy állítsa a pillanatképek vagy segítségével a biztonsági mentési HANA-azonosító található a HANA Studio vagy a pillanatkép-név. Jelenleg a biztonsági azonosító csak kötődik készült pillanatkép a **hana** pillanatkép-típus. Pillanatkép készítése a típusú biztonsági mentések **naplók** és **rendszerindító** ne hajtsa végre egy SAP HANA-pillanatkép. Ezért nem találhatók, ezek a pillanatképek nem biztonsági azonosító. A pillanatkép nevét is meg kell adni, ha minden pillanatkép a különböző köteteken, amelyek megfelelnek a megadott pillanatfelvétel neve keresi. A parancsprogram hívása szintaxisa:

```
./azure_hana_snapshot_delete.pl 

```

Futtassa a parancsfájlt felhasználóként **legfelső szintű**.

Ha egy pillanatképet, hogy a számítógép minden pillanatkép külön-külön törlése. Először adja meg a pillanatképet tartalmazó kötet, és a pillanatkép-nevet adja. Ha a pillanatkép, hogy a kötet létezik, és több mint egy órával korábbiak, akkor az törlődik. Találhatja meg a kötet neve és a pillanatkép neve végrehajtása a `azure_hana_snapshot_details` parancsfájl. 

>[!IMPORTANT]
>Ha csak a pillanatkép, amely törli a meglévő adatok, majd ha a törlés végrehajtása a adat elvész tartja.

   

### <a name="recovering-to-the-most-recent-hana-snapshot"></a>A legutóbbi HANA pillanatfelvétel helyreállítása

Ha egy éles lefelé forgatókönyv, tárolási pillanatképből helyreállítható, a Microsoft Azure-támogatással rendelkező ügyfelek, amelyhez kezdeményezhető. Sürgősség szerinti kérdése, ha adat törölve lett a termelési rendszer, és csak az adatok beolvasása, az éles adatbázis visszaállításához.

Egy másik helyzetben pont időponthoz kötött visszaállítás előfordulhat, hogy alacsony sürgősség, és a nappal tervezett. Megtervezheti ennek a helyreállításnak az SAP HANA az Azure szolgáltatásfelügyelet helyett egy magas prioritású probléma előléptetése. Például előfordulhat, hogy lehet tervezi az SAP szoftverfrissítésre új fejlesztés csomag alkalmazásával. Akkor kell állítható vissza olyan pillanatképet, amely a fejlesztés csomag frissítés előtt állapotát jeleníti meg.

A kérelem elküldése előtt elő kell készíteni. Az SAP HANA Azure szolgáltatásfelügyeleti csoport majd kezelni a kérést, és adja meg a visszaállított kötetek. Ezután állítsa vissza a pillanatképek alapján HANA-adatbázisból. Megtudhatja, hogyan készíti elő a kérelem:

>[!NOTE]
>A felhasználói felület eltérhetnek attól függően, hogy az Ön által használt SAP HANA-kiadás az alábbi képernyőképek.

1. Döntse el, melyik pillanatkép visszaállítása. Csak a hana/adatmennyiség helyreáll, kivéve, ha más módon utasította. 

2. Állítsa le a HANA-példány.

 ![Állítsa le a HANA-példány](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

3. Válassza le az adatkötetek minden HANA-adatbázis csomóponton. Ha az adatok továbbra is csatlakoztatva vannak az operációs rendszer, a pillanatkép visszaállítása sikertelen lesz.
 ![Minden egyes HANA adatbázis csomóponton az adatkötetek leválasztása](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

4. Nyissa meg a visszaállítást egy adott pillanatkép kapcsolatos utasítsa őket, az Azure támogatási kérelmet.

 - A visszaállítás során: az Azure szolgáltatásfelügyelet SAP HANA fel, hogy részt, koordinációs, ellenőrző, és hogy a megfelelő tárolási pillanatképből megerősítő konferencia hívásakor. 

 - A visszaállítás után: az Azure szolgáltatásfelügyelet SAP HANA értesíti, amikor a tárolási pillanatkép visszaállítása megtörtént.

5. A visszaállítási folyamat befejezése után csatlakoztassa újra az adatköteteket.

 ![Minden adatkötetnél újracsatlakoztatni](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)

6. SAP HANA Studio, a helyreállítási beállítások akkor válassza, ha azok nem automatikusan kapja meg az újbóli az SAP HANA Studio keresztül HANA-Adatbázishoz. A következő példa bemutatja a visszaállítás, az utolsó HANA pillanatfelvétel. Tárolási pillanatkép beágyaz egy HANA pillanatkép. Ha a legutóbbi tárolási pillanatkép, a legutóbbi HANA pillanatkép kell tenni. (Ha egy régebbi tárolási pillanatkép szeretné visszaállítani, szeretné keresse meg a HANA pillanatkép, a tárolási pillanatkép készítése ideje alapján.)

 ![Válassza ki a helyreállítás beállítások SAP HANA studióban](./media/hana-overview-high-availability-disaster-recovery/image10-recover-options-a.png)

7. Válassza ki **állítsa helyre az adatbázist egy adott adatok biztonsági mentése és a tárolási pillanatképhez**.

 ![A helyreállítás típusának megadása ablak](./media/hana-overview-high-availability-disaster-recovery/image11-recover-options-b.png)

8. Válassza ki **adjon meg biztonsági másolat nélkül katalógus**.

 ![A biztonsági mentési helyét adja meg ablak](./media/hana-overview-high-availability-disaster-recovery/image12-recover-options-c.png)

9. Az a **céltípus** listáról válassza ki **pillanatkép**.

 ![Adja meg a biztonsági mentés helyreállítása ablakra](./media/hana-overview-high-availability-disaster-recovery/image13-recover-options-d.png)

10. Válassza ki **Befejezés** a helyreállítási folyamat elindításához.

 ![Válassza a "Befejezés" gombra a helyreállítási folyamat elindítása](./media/hana-overview-high-availability-disaster-recovery/image14-recover-options-e.png)

11. A HANA-adatbázis visszaállítása és a HANA pillanatfelvétel, amely megtalálható a tárolási pillanatkép helyre.

 ![HANA-adatbázis visszaállítása és a HANA pillanatfelvétel helyre](./media/hana-overview-high-availability-disaster-recovery/image15-recover-options-f.png)

### <a name="recovering-to-the-most-recent-state"></a>A legfrissebb állapotba helyreállítása

A következő folyamat visszaállítja a HANA pillanatkép, amely megtalálható a tárolási pillanatkép. Majd helyreállítja a tranzakciónapló biztonsági mentések a legfrissebb állapotba az adatbázis csak a tárolási pillanatkép visszaállítása után.

>[!IMPORTANT]
>Mielőtt továbblépne, győződjön meg arról, hogy rendelkezik-e a tranzakciónapló biztonsági mentések teljes és összefüggő lánc. A biztonsági mentése nélkül az adatbázis jelenlegi állapotában nem állítható vissza.

1. Végezze el az 1-6. lépéseket a a [a helyreállítást a legújabb HANA pillanatkép](#recovering-to-the-most-recent-hana-snapshot).

2. Válassza ki **állítsa helyre az adatbázist a legújabb állapotában**.

 ![Válassza a "Legutóbbi állapotában az adatbázis helyreállítása"](./media/hana-overview-high-availability-disaster-recovery/image16-recover-database-a.png)

3. Adja meg a legutóbbi HANA naplóalapú biztonsági mentések helyét. A hely összes HANA-tranzakciónapló biztonsági mentés a HANA pillanatképből a legfrissebb állapotba tartalmaznia kell.

 ![A legutóbbi HANA naplók biztonsági másolatainak elérési útja](./media/hana-overview-high-availability-disaster-recovery/image17-recover-database-b.png)

4. Válassza ki a biztonsági mentés alapjaként, amelyből az adatbázis helyreállítása. A fenti példában a HANA pillanatkép ezen a képernyőfelvételen a HANA pillanatképet, amely a tárolási pillanatkép fájlban. 

 ![Válassza ki a biztonsági mentés alapjaként, amelyből az adatbázis helyreállítása](./media/hana-overview-high-availability-disaster-recovery/image18-recover-database-c.png)

5. Törölje a jelet a **használata különbözeti biztonsági mentések** jelölőnégyzetet, ha eltérések nem léteznek a HANA pillanatkép és a legutóbbi állapot között.

 ![Törölje a "Használja a különbözeti biztonsági mentések" jelölőnégyzet jelölését, ha eltéréseit – nem létezik](./media/hana-overview-high-availability-disaster-recovery/image19-recover-database-d.png)

6. Az összefoglaló képernyőn válassza ki a **Befejezés** elindítani a visszaállítási művelet.

 ![Az Összegzés képernyőn kattintson a "Befejezés"](./media/hana-overview-high-availability-disaster-recovery/image20-recover-database-e.png)

### <a name="recovering-to-another-point-in-time"></a>Egy másik pontra időben helyreállítása
A HANA pillanatkép (tartalmazza a tárolási pillanatkép) és egy későbbi, mint a HANA pillanatkép időpontban helyreállítási pontra helyreállítása, tegye a következőket:

1. Győződjön meg arról, hogy rendelkezik-e összes-tranzakciónapló biztonsági mentés a HANA pillanatképből a helyreállítani kívánt idejével.
2. Az eljárás megkezdése [helyreállítása a legfrissebb állapotba](#recovering-to-the-most-recent-state).
3. A 2. lépés az eljárás a a **helyreállítási típus megadása** ablakban válassza ki **állítsa helyre az adatbázist a következő pont időben**, és meg azt az időpontot. Kövesse a lépéseket 3-6.

### <a name="monitoring-the-execution-of-snapshots"></a>A pillanatképek végrehajtásának figyelése

Storage-pillanatfelvételekkel HANA nagy példányok használata is ezeket a storage-pillanatfelvételekkel végrehajtása figyelnie kell. A parancsfájl, amely végrehajtja a tárolási pillanatkép kimeneti fájlba írja, és ezután menti a Perl-parancsfájlokat ugyanazon a helyen. Minden egyes tárolási pillanatkép készült külön fájlt. A kimeneti fájlok is egyértelműen mutatja a különböző fázisait, hogy a pillanatkép-parancsfájl végrehajtása:

1. Létre kell hoznia egy pillanatkép köteteken található.
2. A pillanatképek vett ezeken a köteteken található.
3. Törölje a pillanatképek a megadott számú végleges meglévő pillanatképeket.
4. Hozzon létre egy SAP HANA-pillanatkép.
5. A tárolási pillanatkép létrehozása a kötetek keresztül.
6. Törölje a SAP HANA-pillanatkép.
7. Nevezze át a legutóbbi pillanatképének **.0**.

A parancsfájl cab azonosított legfontosabb részét ez a kijelző:
```
**********************Creating HANA snapshot**********************
Creating the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data create snapshot"" ...
HANA snapshot created successfully.
**********************Creating Storage snapshot**********************
Taking snapshot hourly.recent for hana_data_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_backup_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_shared_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for sapmnt_lhanad01_t020_vol ...
Snapshot created successfully.
**********************Deleting HANA snapshot**********************
Deleting the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data drop snapshot"" ...
HANA snapshot deletion successfully.
```
Ettől a példától láthatja, hogy a parancsfájl rögzíti a HANA pillanatkép létrehozását. A kibővített esetben ez a folyamat a fő csomópont indítható. A fő csomópont létrehozását kezdeményezi a szinkron a SAP HANA-pillanatképek egyes a munkavégző csomópontokhoz. Ezt követően a tároló pillanatkép készítésének időpontjában. A storage-pillanatfelvételekkel sikeres végrehajtása után a HANA pillanatkép törlését. A fő csomópont kezdeményezni a HANA pillanatkép törlését.


## <a name="disaster-recovery-principles"></a>Vész-helyreállítási alapelvei
HANA nagy osztályt a vész-helyreállítási funkciók között a különböző Azure-régiók nagy példány HANA bélyegzők fel. Például ha HANA nagy példány egységek Azure Velünk nyugati régiójában, akár is használhatja a HANA nagy példány egységek amerikai keleti régióban vész-helyreállítási egység. A korábban említett vész-helyreállítási beállítása nem automatikusan, mert ahhoz szükséges, hogy egy másik HANA nagy példány egység a vész-Helyreállítási régióban kell fizetnie. A vész-helyreállítási telepítő méretezett, valamint a kibővített telepítéshez működik. 

Az eddig telepített forgatókönyvekben ügyfeleink használja az egység a vész-Helyreállítási régióban HANA telepített példányát használja, nem éles rendszerek futtatására. A nagy példány HANA egység kell lennie a Termékváltozat üzemi célokra, ugyanazon metódust. A lemez konfigurációját a kiszolgáló egységet az éles Azure-régióban, és a vész-helyreállítási régió közötti így néz ki:

![A lemez szempontjából vész-Helyreállítási beállítási konfiguráció](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

Ahogyan az Áttekintés ábrán látható, majd egy második együttesét lemezkötetek sorrendben kell. A cél lemezkötetek akkora, mint a termelési kötetek, a vész-helyreállítási egység az éles példány. A kötetek társítva a HANA nagy példány server egység a vész-helyreállítási helyen. A következő kötetek replikálva vannak a termelési régióban a vész-Helyreállítási hely:

- / hana/adatok
- / hana/logbackups 
- /Hana/Shared (tartalmazza a/usr/sap)

A /hana/log kötet nem replikálódik, mert a SAP HANA-tranzakciónapló módon történik, ezek a kötetek való visszaállítása nem szükséges. 

A vész-helyreállítási funkció alapján ajánlott a storage-replikáció működése a HANA nagy példány infrastruktúra által kínált. A funkció a tárolási oldali használt nincs replikálása aszinkron módon módosítások fordulhat elő, a tárolási köteten módosítások állandó adatfolyam. Ehelyett egy olyan mechanizmus, amely azt a tényt, hogy a pillanatképek kötetek jöttek létre rendszeresen támaszkodik. Egy már replikált pillanatkép és egy új pillanatkép, amely még nem replikálta a különbözeti majd átkerül a vész-helyreállítási hely a tároló kötetek.  Ezeket a pillanatképeket tárolt azokon a köteteken, és a vész-helyreállítási feladatátvétel esetén, azokon a köteteken kell visszaállítani.  

A teljes kötet első adatátvitelt kell lennie, mielőtt adatok mennyisége kisebb, mint a pillanatképek közötti eltérések válik. A köteteket a vész-Helyreállítási hely eredményeképpen minden egyes, a kötet-pillanatképek végez a munkakörnyezeti helyet tartalmaz. A tény lehetővé teszi, hogy vész-Helyreállítási rendszer megszerezni egy korábbi állapotra a termelési rendszer visszaállítása nélkül elveszett adatok helyreállításához használni.

Azokban az esetekben, ahol HANA rendszer replikáció használata magas rendelkezésre állású funkcióinak a munkakörnyezeti helyet a csak a 2. rétegbeli (vagy replika) példány kötetek replikálódnak. Ez a konfiguráció a vész-Helyreállítási hely a tároló replikációs késés előfordulhat, hogy okozhat, ha karbantartása, vagy a másodlagos replika (2. szint) kiszolgáló egység vagy SAP HANA-példány ebben miatt le. 

>[!IMPORTANT]
>Csakúgy, mint a többrétegű HANA replikációs, a réteg 2 HANA példányt, vagy a kiszolgáló egység leállítására a vész-helyreállítási hely replikációs blokkol, a HANA nagy példány vész-helyreállítási funkciójának használatakor.


>[!NOTE]
>A nagy példány HANA tároló-replikáció működése tükrözött, és a storage-pillanatfelvételekkel replikálása. Ezért ha nem hajtható végre storage-pillanatfelvételekkel jelent meg a biztonsági mentési szakaszban ebben a dokumentumban, nem lehet bármely replikációt, hogy a vész-helyreállítási helyen. Tárolás pillanatkép végrehajtási előfeltétele a vész-helyreállítási hely tárolóreplikálást.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>A vész-helyreállítási forgatókönyv elkészítése
Feltételezzük, hogy rendelkezik-e éles operációs rendszert futtató HANA nagy példányokon az éles Azure-régiót. A dokumentáció következő tegyük fel, hogy az adott HANA rendszer SID-e a "PRD." Azt is feltételezzük, hogy, hogy rendelkezik-e nem éles operációs rendszert futtató HANA nagy példányain fut a vész-helyreállítási Azure-régiót. A dokumentációt feltételezzük, hogy a biztonsági azonosító-e a "Teszt". Így a konfiguráció így néz ki:

![DR-telepítés elindítása](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Ha a kiszolgálópéldány már nem elrendeli a további tárhely kötetre beállítva, az Azure szolgáltatásfelügyelet SAP HANA kapcsolódni fog a további kötetek készlete az éles replika célként, az, hogy futnak-e a teszt HANA nagy példány egység HANA példánya. Erre a célra meg kell adnia a termelési HANA példányát biztonsági azonosítója. Az Azure szolgáltatásfelügyelet SAP HANA megerősíti, hogy a köteteket a mellékletet, után kell csatlakoztatni a köteteket a HANA nagy példány egységhez.

![DR telepítő következő lépés](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

A következő lépés az, hogy a második SAP HANA-példány telepítését a vész-helyreállítási az Azure-régió, a teszt HANA-példányt futtató, a HANA nagy példány egységen. Az újonnan telepített SAP HANA-példány SID AZONOSÍTÓVAL rendelkeznie kell. A felhasználó hozott létre kell UID és csoport azonosítója, amely a termelési példány rendelkezik azonos. Ha a telepítés sikeres volt, szükség:
- Állítsa le az újonnan telepített SAP HANA-példány a HANA nagy példány egység a vész-helyreállítási Azure-régiót.
- Válassza le a PRD ezeket a köteteket, és lépjen kapcsolatba az Azure szolgáltatásfelügyelet SAP HANA. A kötetek nem maradnak csatlakoztatott egységhez, mert nem érhető el, de a tárolási replikációs céljaként.  

![DR telepítő lépés előtt replikációs létrehozó](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

A műveleti csapata lesz a PRD az éles Azure-régió, és a PRD kötetek az Azure-régiót vész-helyreállítási között a replikálási kapcsolatot létesíteni.

>[!IMPORTANT]
>A /hana/log kötet nem replikálható, mert nincs szükség a replikált SAP HANA-adatbázis visszaállítása a vész-helyreállítási hely konzisztens állapotra.

A következő lépés az, hogy, hogy állítsa be, vagy állítsa be a tárolási pillanatkép biztonsági mentés ütemezése a vészhelyreállítás esetében a RTO és a helyreállítási Időkorlát eléréséhez. A helyreállítási időkorlát minimalizálása érdekében állítsa be a következő replikálási időközök HANA nagy példány szolgáltatási:
- A köteteket, amelyekre a kombinált pillanatkép vonatkozik (pillanatkép-típus = **hana**) replikálja a vész-helyreállítási helyen azonos tárolási kötet szoftveres 15 percenként.
- A tranzakció-napló biztonsági mentési kötet (pillanatkép-típus = **naplók**) replikálja a vész-helyreállítási helyen azonos tárolási kötet szoftveres három percenként.

A helyreállítási időkorlát minimalizálása érdekében állítsa be a következőket:
- Hajtsa végre a **hana** típus tárolási pillanatkép (lásd a "7. lépés: hajtsa végre a pillanatképek") 1 óra 30 percenként.
- Biztonsági másolatokat SAP HANA-tranzakciónapló 5 percenként.
- Hajtsa végre a **naplók** írja be az 5-15 percenként pillanatkép-tárolás. Az időköz idővel az RPO körülbelül 15-25 percig érhet el kell lennie.

Ennek beállításához, a feladatütemezési-tranzakciónapló biztonsági mentések, storage-pillanatfelvételekkel és a replikáció HANA-tranzakciónapló biztonsági mentését a kötet és /hana/data, és (tartalmazza a/usr/sap) /hana/shared nézhet ki például az adatokat, az ábrán látható:

 ![Egy tranzakció-napló biztonsági mentési pillanatképet és egy időt tengelyen beépülő tükör közötti kapcsolat](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

A vész-helyreállítási esetben egy még élvezetesebbé RPO eléréséhez átmásolhatja a HANA-tranzakciónapló biztonsági mentések SAP HANA Azure (nagy példány) az egyéb Azure-régiót. A további helyreállítási Időkorlát csökkentése eléréséhez a következő lépésekkel nyers:

1. Készítsen biztonsági másolatot a HANA tranzakció jelentkezzen nyújtott a lehető /hana/logbackups.
2. A tranzakciónapló biztonsági mentések másolja az NFS-megosztások használata rsync Azure virtuális gépeken üzemeltet. A virtuális gépek az Azure virtuális hálózatok és a vész-Helyreállítási régiókban az éles Azure-régióban van. A kapcsolatcsoport az üzemi HANA nagy példányok csatlakozik Azure mindkét Azure virtuális hálózat csatlakoztatni kell. Lásd: a képek a [szempontok vész-helyreállítási HANA nagy osztályt hálózati](#Network-considerations-for-disaster-recovery-with-HANA-Large-Instances) szakasz. 
3. Az NFS csatolva a virtuális gép a régióban a tranzakciónapló biztonsági mentések megtartása tárolási exportált.
4. Vész-feladatátvételi esetben kiegészítik a tranzakciónapló biztonsági mentések megtalálta a /hana/logbackups köteten több nemrég végrehajtott az NFS-tranzakciónapló biztonsági mentések megosztani a vész-helyreállítási helyen. 
5. Most már megkezdheti a tranzakció-napló biztonsági mentését történő visszaállításához, a legutóbbi biztonsági mentése a vész-Helyreállítási terület felett mentheti.

HANA nagy példány műveletek erősítse meg a replikációs kapcsolat telepítő, és elindítja a végrehajtási tárolási pillanatképes biztonsági, az adatok replikálása kezdődik.

![DR telepítő lépés előtt replikációs létrehozó](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

A replikációs előrehaladtával, a pillanatképek a vész-helyreállítási Azure, a PRD köteteken régiók nem állítja vissza. Csak tárolják. A kötetek csatlakoztatva legyenek olyan állapotban, ha az állapot, amelyben meg leválasztani a köteteket a vész-helyreállítási az Azure-régió, a kiszolgáló egységben a PRD SAP HANA-példány telepítése után képviselik. A tároló biztonsági mentések, amelyek nem lettek visszaállítva is képviselik.

Feladatátvétel esetén is használhat egy régebbi tárolási pillanatkép helyett a legújabb tárolási pillanatkép visszaállítása.

## <a name="disaster-recovery-failover-procedure"></a>Vész-helyreállítási feladatátvételi eljárás
Ha azt szeretné, vagy a vész-Helyreállítási hely feladatátvételt kell, a SAP HANA Azure üzemeltetése csapat együttműködhet szeretné. A nyers lépések a folyamat, amennyiben néz ki:

1. Mivel a vész-helyreállítási egység HANA nagy példányok futtatja egy nem éles HANA-példányt, állítsa le ezt a példányt kell. Feltételezzük, hogy egy inaktív HANA éles példány előre telepítve van.
2. Győződjön meg arról, hogy az eljárások nincsenek SAP HANA futnak. Ehhez az ellenőrzéshez használja a következő parancsot: `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`. A kimeneti jelenítsen meg a **hdbdaemon** folyamatok állt le, és nincs más HANA folyamatok futó vagy elindított állapotban.
3. Határozza meg, melyik pillanatfelvétel neve vagy a biztonsági mentési SAP HANA-azonosító szeretné, hogy a vész-helyreállítási visszaállítva. A valódi katasztrófa utáni helyreállítás esetben ebben pillanatképét a általában a legújabb pillanatkép. Ha elveszett adatok visszaállítására van szüksége, válasszon ki egy korábbi pillanatkép.
4. Kapcsolattartási Azure támogatja a magas prioritású támogatási kérelmet segítségével, és kérje meg a visszaállításhoz (név és a pillanatkép dátum) pillanatkép, illetve a biztonsági azonosító HANA a vész-Helyreállítási helyen. Az alapértelmezett érték, hogy a műveletek csak a /hana/data kötet visszaállítása. A/hana/logbackups kötetek is szeretne használni, ha kifejezetten megállapítják, hogy szeretné. *Nem javasoljuk, hogy a /hana/shared kötet visszaállítása.* Ehelyett megadott fájlok, például global.ini ki kell választania a **.snapshot** könyvtár, illetve annak alkönyvtáraiba után újracsatlakoztatása/hana/PRD a megosztott fürtkötet. A műveletek oldalon, a következő lépéseket is megtörténjen-e: egy. A pillanatképek a termelési kötetről a vész-helyreállítási kötet replikációs le van állítva. Ez lehet, hogy már történt az üzemi helyen kimaradás esetén is van szüksége a vész-Helyreállítási okát.
    b. A tárolási pillanatkép-nevet, vagy a pillanatkép készítése a biztonsági azonosító úgy döntött, hogy a vész-helyreállítási kötetek visszaállítása megtörtént.
    c. A visszaállítás után a vész-helyreállítási kötetek használhatók csatlakoztatni kell a vész-helyreállítási régióban HANA nagy példány egységeire.
5. Csatlakoztassa a vész-helyreállítási köteteket a HANA nagy példány egységhez, a vész-helyreállítási helyen. 
6. Indítsa el az eddigi inaktív SAP HANA-éles példányt.
7. Ha úgy döntött, hogy másolja a tranzakció-napló biztonsági mentési naplók továbbá csökkentheti az RPO időtartamát, e-tranzakciónapló biztonsági mentések egyesítése az újonnan csatlakoztatott vész-Helyreállítási/hana/logbackups directory szeretné. Ne írja felül a meglévő biztonsági másolatok. Csak újabb biztonsági, amelyek nem lettek replikálva a legutóbbi replikáció tárolási pillanatkép.
8. A pillanatképek a /hana/shared/PRD kötet a vész-helyreállítási Azure-régió, a replikált kívül egyetlen fájlok is visszaállíthatja.

A következő lépések sorrendjét magában foglalja az SAP HANA éles példányok a visszaállított tárolási pillanatkép és a rendelkezésre álló-tranzakciónapló biztonsági mentések helyreállítása. A lépések néznek ki:

1. Módosítsa a biztonsági mentési helyét, **/hana/logbackups** SAP HANA Studio használatával.
   ![A VÉSZ-helyreállítási biztonsági mentési helyének módosítása](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

2. SAP HANA vizsgálja át a biztonságimásolat-fájl helyét, és vissza a legutóbbi-tranzakciónapló biztonsági javasol. A vizsgálat is igénybe vehet néhány percet, amíg például akkor jelenik meg, a következő képernyő: ![-tranzakciónapló biztonsági másolatok vész-Helyreállítási listája](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

3. Módosítsa az alapértelmezett beállításokat:

      - Törölje a jelet **használja a különbözeti biztonsági mentések**.
      - Válassza ki **napló terület inicializálása**.

   ![Az inicializálás napló terület](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

4. Válassza a **Finish** (Befejezés) elemet.

   ![A vész-Helyreállítási visszaállítás befejezése](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

A folyamatban lévő ablakban hasonló látható itt, meg kell jelennie. Ne feledje, hogy a példában a vész-helyreállítási visszaállítás, a 3-csomópont kibővített SAP HANA-konfiguráció van.

![Visszaállítási folyamat](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Ha a visszaállítási úgy tűnik, hogy időnként a **Befejezés** képernyőn és nem jelenít meg a folyamat képernyője ellenőrzés futtatásával megbizonyosodik arról, hogy az összes SAP HANA-példányok a feldolgozó csomópontokon futnak. Ha szükséges, indítsa el manuálisan a SAP HANA-példányokat.


### <a name="failback-from-dr-to-a-production-site"></a>A vész-Helyreállítási feladat-visszavétel éles helyhez
A vész-Helyreállítási újból az éles helyhez sikertelen. Az, hogy a feladatátvétel, a vész-helyreállítási helyre okozott problémák az éles Azure-régió, és nem az elveszett adatok helyreállításához szükséges esetben vizsgáljuk meg. Ez azt jelenti, hogy futnia a SAP termelési számítási feladatok egy ideje a vész-helyreállítási helyen. A munkakörnyezeti helyet problémákat megoldottként visszaadják feladataikat a munkakörnyezeti helyet szeretne. Adatok nem megszakad, mert újra üzembe a munkakörnyezeti helyet a lépés néhány lépéseket és az Azure üzemeltetése csapat az SAP HANA szoros együttműködésben magában foglalja. Célszerű elindítani a műveleti csapata a problémák feloldása után vissza a munkakörnyezeti helyet a szinkronizálás elindításához.

A lépések sorrendjét így néz ki:

1. Az Azure üzemeltetése csapat az SAP HANA lekérdezi az eseményindító szinkronizálni a termelési tárolókötetek éles állapotát jelzik vész-helyreállítási tároló kötetekről. Ebben az állapotban lévő leállítása a munkakörnyezeti helyet a HANA nagy példány egységet.
2. Az SAP HANA Azure üzemeltetése csapat a replikációs figyeli, és gondoskodik arról, hogy egy olvasottként érhető el, mielőtt tájékoztat ügyfélként.
3. A vész-helyreállítási hely az üzemi HANA példány használó alkalmazások leállítása. Ezután hajtsa végre a HANA tranzakció-napló biztonsági mentését. Ezután állítsa le a HANA-példány futtatási a HANA nagy példány egységeken a vész-helyreállítási helyen.
4. Után a HANA-példány futtatási a vész-helyreállítási hely HANA nagy példány egységben van-e állítva, a műveleti csapata manuálisan szinkronizálja a kötetek újra.
5. Az SAP HANA Azure üzemeltetése csapat a munkakörnyezeti helyet a HANA nagy példány egység indul újra, és átadja Önnek. Győződjön meg arról, hogy az SAP HANA-példány leállítási állapotban van az indításkor a HANA nagy példány egység.
6. Az adatbázis visszaállítási lépéseket hajtsa végre, a vész-helyreállítási hely korábban feladatátadás hasonló módon.

### <a name="monitoring-disaster-recovery-replication"></a>Vész-helyreállítási replikálásának figyelése

A tároló replikálási folyamat állapotának figyelheti az parancsfájl `azure_hana_replication_status.pl`. Ez a parancsfájl futtatása a vész-helyreállítási helyen egységből származó kell futtatni. Ellenkező esetben azt nem lesz megfelelően működjön. A parancsfájl használatára, függetlenül attól, hogy replikációs aktív. A parancsfájlt minden HANA nagy példány egységéhez a bérlő a vész-helyreállítási helyen. A rendszerindító kötet kapcsolatos részletek beszerzése érdekében nem használható.

A parancsfájl például hívása:
```
./replication_status.pl <HANA SID>
```

A kimeneti van szerinti bontásban, kötet a következő szakaszokra:  

- Hivatkozás állapota
- Az aktuális replikációs tevékenység
- Replikált legújabb pillanatkép 
- A legújabb pillanatkép mérete
- Aktuális időbeli pillanatképek – az utolsó befejezett pillanatkép replikáció között, és most  

A hivatkozás állapotát jeleníti meg, mint **aktív** kivéve, ha a helyek közötti kapcsolat nem működik, vagy egy feladatátvételi esemény jelenleg folyamatban. A replikálási műveletek megoldást, hogy minden adatot jelenleg éppen replikált vagy inaktív, vagy más tevékenységek pillanatnyilag folyamatban levő a hivatkozásra. A legutolsó pillanatfelvétel replikált csak meg kell jelennie `snapmirror…`. Ezután megjelenik, a legutolsó pillanatfelvétel méretét. Végül az időbeli jelenik meg. Az időbeli idejét a replikáció befejezése után az ütemezett replikáció időt jelöli. Késéssel lehet nagyobb, mint egy órával az adatreplikáció, különösen a kezdeti replikálás, annak ellenére, hogy a replikáció. Az időbeli lesz tovább növekszik, amíg a folyamatban lévő replikáció befejeződik.

Egy kimeneti példát is néznek ki:

```
hana_data_hm3_mnt00002_t020_dp
-------------------------------------------------
Link Status: Broken-Off
Current Replication Activity: Idle
Latest Snapshot Replicated: snapmirror.c169b434-75c0-11e6-9903-00a098a13ceb_2154095454.2017-04-21_051515
Size of Latest Snapshot Replicated: 244KB
Current Lag Time between snapshots: -   ***Less than 90 minutes is acceptable***
```












