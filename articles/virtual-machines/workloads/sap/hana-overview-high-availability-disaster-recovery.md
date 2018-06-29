---
title: Az Azure (nagy példányok) SAP HANA magas rendelkezésre állási és vészhelyreállítási helyreállítási |} Microsoft Docs
description: Magas rendelkezésre állás és az Azure (nagy példány) az SAP HANA vész-helyreállítási terv létrehozása
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
ms.date: 06/27/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d2445713aa5d6a839950ca0fe9567133c06d1ffa
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062241"
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>SAP HANA nagy példányok magas rendelkezésre állási és vészhelyreállítási helyreállítási az Azure-on 

>[!IMPORTANT]
>Ebben a dokumentációban szolgáltatás nem helyettesíti a SAP HANA-felügyelet dokumentációjában vagy a SAP megjegyzések. Valószínű, hogy az olvasó rendelkezik a alapos ismerete és a SAP HANA-felügyelet és a műveletek, különösen a biztonsági mentés, a visszaállítás, a magas rendelkezésre állású és vész-helyreállítási témakörök segítséget. Ebben a dokumentációban SAP HANA Studio képernyőképeket jelennek meg. Tartalom, felépítését és az SAP felügyeleti eszközöket és az eszközök maguk képernyők jellege SAP HANA kiadás a kiadási megváltozhat.

Fontos lépések és a környezet és a HANA-verziók és kiadások folyamatok megadásával. A jelen dokumentációban ismertetett bizonyos folyamatok egyszerűsítettek, az általános megértéséhez, és nem jogosultak a végleges művelet szakkönyveket részletes lépéseket felhasználhatók. Ha szeretne létrehozni a konfigurációk művelet szakkönyveket, szeretné tesztelése és a folyamatok gyakorolja- és dokumentumszolgáltatások adott konfigurációkhoz tartozó folyamatokat. 


Magas rendelkezésre állás és vészhelyreállítás (DR), a kritikus fontosságú SAP HANA az Azure (nagy példányok) kiszolgálón futó kritikus fontosságú szempontot. Fontos, SAP, a rendszer integráló, vagy a Microsoft megfelelően tervezővel, és a megfelelő magas rendelkezésre állású és vész-helyreállítási stratégiát végrehajtásához használható. Fontos továbbá kell figyelembe venni a helyreállítási időkorlát (RPO) és a helyreállítási idő célkitűzése a környezetre jellemző.

A Microsoft támogatja a bizonyos SAP HANA magas rendelkezésre állású funkciók HANA nagy osztályt. Ezek a képességek közé tartoznak:

- **Tárolási replikációs**: A tároló nem lesz képes replikálni az összes adatokat egy másik Azure-régió, egy másik HANA nagy példány stamp. SAP HANA működése független ezt a módszert. Ez a funkció az alapértelmezett vész helyreállítási mechanizmus érhető el a HANA nagy példány.
- **HANA replikációs**: A [replikáció az összes adat az SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) egy külön SAP HANA-rendszerhez. A helyreállítási idő célkitűzése keresztül rendszeres időközönként adatreplikáció másodpercekre csökken. SAP HANA aszinkron, szinkron memórián belüli és szinkron módot támogat. Szinkron módban csak az ugyanabban az adatközpontban, vagy legalább 100 km-egymástól SAP HANA rendszereken használatos. Az aktuális terv HANA nagy példány stampgyűjteményt HANA replikációs a magas rendelkezésre állás csak egy régió belül használható. HANA replikációs igényel egy külső fordított proxy vagy az útválasztási egy másik Azure-régió, a vész-helyreállítási beállítások. 
- **Gazdagép automatikus feladatátvételt**: A helyi hiba-helyreállítási megoldás, amely alternatív megoldás HANA replikációs SAP Hana. A fő csomópont nem érhető el, ha konfigurál egy vagy több készenléti SAP HANA-csomópont kibővített módban, és SAP HANA automatikusan átadja a feladatokat egy készenléti csomópont.

SAP HANA Azure (nagy példányok) a két Azure-régiók négy geopolitikai területeken (Egyesült Államok, Ausztrália, Európában és japán) kínálják. Két régió egy geopolitikai területen HANA nagy példány bélyegzők üzemeltető különálló dedikált hálózati kapcsolatok csatlakoznak. Ezek használhatók a storage-pillanatfelvételekkel replikálására vész-helyreállítási módszerek biztosításához. A replikáció alapértelmezés szerint nem jön létre, de beállítva az ügyfelek, akik a vész-helyreállítási funkcióhoz sorrendben. Tárolási replikációs szolgáltatása a storage-pillanatfelvételekkel nagy HANA-példányok használatát függ. Nincs lehetőség a válasszon ki egy Azure-régió, egy vész-Helyreállítási területet, amely egy másik geopolitikai területén legyen. 

Az alábbi táblázat a jelenleg támogatott magas rendelkezésre állási és vészhelyreállítási helyreállítási módszerek és kombinációit:

| Támogatott HANA nagy példányát forgatókönyv | Magas rendelkezésre állási beállítása | Vész-helyreállítási lehetőség | Megjegyzések |
| --- | --- | --- | --- |
| Egyetlen csomópont | Nem érhető el. | Dedikált DR-telepítés.<br /> Multipurpose DR-telepítés. | |
| Gazdagép automatikus feladatátvételt: kibővített (vagy anélkül készenléti)<br /> például 1 + 1 | A készenléti véve az aktív szerepkör használhatóságát.<br /> HANA határozza meg a szerepkör kapcsolót. | Dedikált DR-telepítés.<br /> Multipurpose DR-telepítés.<br /> DR szinkronizálási storage replikáció használatával. | HANA kötet beállítása a csomópontok vannak csatolva.<br /> DR helynek kell lennie a csomópontok azonos számú. |
| HANA replikációs | Elsődleges vagy másodlagos telepítés lehetséges.<br /> Másodlagos áthelyezi egy feladatátvételi esetben elsődleges szerepkör.<br /> HANA replikációs és az operációs rendszer szabályozhatja a feladatátvételt. | Dedikált DR-telepítés.<br /> Multipurpose DR-telepítés.<br /> DR szinkronizálási storage replikáció használatával.<br /> DR HANA replikációs használatával még nem lehetséges, külső gyártótól származó összetevőkhöz nélkül. | Minden csomópont csatolt kötetek külön készletét.<br /> A munkakörnyezeti helyet a másodlagos másodpéldány csak lemezkötetek a vész-Helyreállítási helyre replikálja.<br /> A vész-Helyreállítási helyen szükség egy olyan kötetek készlete. | 

A dedikált DR-telepítés, ahol a HANA nagy példány egység a vész-Helyreállítási helyen nem használatos a futó alkalmazások és szolgáltatások vagy nem éles rendszerek. Az egység passzív, és csak akkor, ha katasztrófa feladatátvevő végrehajtja a rendszer van telepítve. Azonban ez a beállítás nincs előnyben részesített téve a sok ügyfél.

Tekintse meg a [HLI támogatott forgatókönyvek](hana-supported-scenario.md) további tárolási elrendezés és ethernet részleteit a architektúra.

> [!NOTE]
> [SAP HANA MCOD központi telepítések](https://launchpad.support.sap.com/#/notes/1681092) (több HANA példányt egy egységen) a táblázatban felsorolt forgatókönyvek munkahelyi felirataként a magas rendelkezésre ÁLLÁSÚ és vész-Helyreállítási módszer. Kivételt jelent a HANA replikációs egy támasztja alapján automatikus feladatátvevő fürttel. Ilyen esetben csak egy HANA példány egységenként támogatja. A [SAP HANA MDC](https://launchpad.support.sap.com/#/notes/2096000) központi telepítések, az csak nem tárolási alapú magas rendelkezésre ÁLLÁSÚ és vész-Helyreállítási módszerek működik, ha egynél több bérlő van telepítve. Egy bérlő telepített, a felsorolt összes módszerek érvényesek.  

A többcélú DR-telepítés egy nem éles munkaterhelés futtató a HANA nagy példány egység a vész-Helyreállítási helyen. Katasztrófa, nem éles rendszer leállítása a storage-rel replikált (további) kötet készletek csatlakoztatni, és indítsa el a az éles HANA-példány. A legtöbb HANA nagy példány vész helyreállítási funkciójának használatát használják, ez a konfiguráció. 


A következő SAP cikkekben található SAP HANA magas rendelkezésre állás további információk: 

- [SAP HANA magas rendelkezésre állású tanulmány](http://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [SAP HANA felügyeleti útmutató](http://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [Az SAP HANA replikációs SAP HANA Academy videó](http://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [Támogatási Megjegyzés #1999880 – gyakori kérdések az SAP HANA replikációs SAP](https://apps.support.sap.com/sap/support/knowledge/preview/en/1999880)
- [SAP támogatási Megjegyzés #2165547 – SAP HANA biztonsági mentése és visszaállítása SAP HANA rendszer replikációs környezeten belül](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [Támogatási Megjegyzés #1984882 – SAP HANA replikációs használatával SAP minimális vagy nulla állásidővel hardver Exchange-hez](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>Vész-helyreállítási HANA nagy osztályt hálózati szempontjai

A vész helyreállítási funkciójának HANA nagy példányok előnyeit, két Azure-régiókban való hálózati kapcsolat kialakítása kell. A fő Azure-régió, a helyszíni Azure ExpressRoute körön kapcsolat és egy másik kapcsolat kapcsolat a helyszíni és a vész-helyreállítási régió van szükség. Ez a mérték olyan helyzet, ahol probléma van Azure-régióban, beleértve a Microsoft vállalati peremhálózati útválasztó (MSEE) helyét ismerteti.

Második mérőszámként kapcsolódhatnak egy régió tartozik, amely a HANA nagy példányok más régióban ExpressRoute-kapcsolatcsoportot az összes Azure virtuális hálózatot az Azure (nagy példányok) SAP HANA-hez. Ennek *közötti csatlakozás*, egy Azure-on futó szolgáltatások régió az 1. virtuális hálózati HANA nagy példány egység régió 2, és megfordítva már képes csatlakozni. Ez a mérték megoldja az esetet, amelyben a MSEE helyek csak az egyik, amely kapcsolódik a helyszíni hely Azure-ral offline állapotba kerül.

A következő ábra a vész-helyreállítási esetekben rugalmas konfigurációját mutatja be:

![Optimális konfigurációs katasztrófa utáni helyreállítás](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-with-hana-large-instances-storage-replication-for-disaster-recovery"></a>Egyéb követelmények HANA nagy példányok storage replikáció katasztrófa utáni helyreállítás

A vészhelyreállítás telepítője HANA nagy osztályt az előző követelményeken kell:

- Az Azure (nagy példányok) termékváltozatok akkora, mint az éles tárhely termékváltozatok az SAP HANA sorrendben, és telepítheti azokat a vész-helyreállítási régióban. Ezek a példányok az aktuális felhasználói telepítés, nem éles HANA példányok futtatásához használ. Ezek a konfigurációk nevezzük *többcélú vész-Helyreállítási beállítások*.   
- Az egyes az Azure (nagy példányok) SKU, amely a vész-helyreállítási hely helyreállítja a SAP HANA-rendezési további tárterületet a vész-Helyreállítási helyen. További tárhely vásárlás lehetővé teszi a tárolási köteteinek lefoglalásához. A tároló az éles Azure-régió, a vész-helyreállítási Azure-régió, a replikáció célját kötetekhez foglalhatja le.
- Abban az esetben, ha HSR telepítés rendelkezik az elsődleges, és a telepítő-alapú tárolás replikációs a vész-Helyreállítási helyre, akkor kell vásárolhat további tárterületet, a vész-Helyreállítási helyen, mind az elsődleges és másodlagos csomópontot adatok replikálását a vész-Helyreállítási helyre.

 

## <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás

A legfontosabb szempontja működési adatbázisokhoz egyik katasztrofális események elleni védelem érdekében. Ezek az események ok foglalhatnak egyszerű felhasználói hibáinak természeti katasztrófa lehet.

Egy adatbázis biztonsági mentéséhez, segítségével visszaállíthatja a bármely időpontra (például valaki a kritikus fontosságú adatok törlése előtt), lehetővé teszi, hogy a visszaállítási állapotba, amely a lehető legközelebb a módját a megszakítás előtt volt.

Kétféle típusú biztonsági mentést kell végezni a legjobb eredmények elérése érdekében:

- Adatbázisok biztonsági: teljes, a növekményes és a különbözeti biztonsági mentések
- Tranzakciónapló biztonsági mentései

Teljes-adatbázis a biztonsági másolatok az alkalmazás szintjén, valamint a biztonsági másolatok tárolási pillanatképek végezheti el. Storage-pillanatfelvételekkel tranzakciónaplók biztonsági mentése nem váltják ki. Tranzakciónapló biztonsági mentései is fontos, állítsa vissza az adatbázist egy bizonyos mértékig időben, vagy már véglegesített tranzakciók naplóit üres marad. Azonban storage-pillanatfelvételekkel meggyorsíthatja a helyreállítás az adatbázis egy összegző-továbbító kép gyorsan megadásával. 

SAP HANA Azure (nagy példányok) két biztonsági mentési és helyreállítási lehetőségeket kínálja:

- Saját munka (DIY). Miután számít gondoskodjon arról, hogy elég szabad lemezterület, hajtsa végre teljes adatbázis és a napló-biztonságimásolatokat a következő lemez biztonsági mentési módszerek egyikének használatával. Biztonsági másolatot, vagy közvetlenül a HANA nagy példány egységek, vagy a hálózati fájlrendszer megosztások (NFS), amely egy Azure virtuális gépen (VM) be vannak állítva csatolt kötetek. Az utóbbi esetben az ügyfelek Linux virtuális gépet az Azure-ban, Azure Storage csatolása a virtuális gép és a konfigurált NFS-kiszolgáló adott virtuális gépen keresztül a tárolóban. Elvégzi a biztonsági mentés, amelyhez közvetlenül kapcsolódni HANA nagy példány egységek, kötetek ellen, ha szeretne másolni a biztonsági másolatokat az Azure storage-fiók (Miután beállította az Azure virtuális gép, amely az Azure Storage alapuló NFS-megosztások exportálása). Az Azure mentési tárolóval vagy a cold az Azure storage is használható. 

   Egy másik lehetőség, hogy a külső Adateszköz védelmi használja a biztonsági másolatok tárolására, miután másolja őket egy Azure storage-fiókot. Lehet, hogy a DIY biztonsági mentési beállításának is szükséges hosszabb időszakokra, megfelelőségi és naplózási célokra tárolásához szükséges adatokat. Minden esetben a biztonsági mentések átkerülnek a virtuális gép és az Azure Storage képviselt NFS-megosztásokat.

- Infrastruktúra biztonsági mentési és visszaállítási funkció. Is használja a biztonsági mentés és visszaállítás funkció, amely az alkalmazás mögötti infrastruktúra az Azure (nagy példányok) SAP HANA. Ezt a beállítást kell biztonsági mentést és gyors visszaállítások teljesíti. Ez a szakasz a többi oldja meg a biztonsági mentési és visszaállítási funkció HANA nagy osztályt ajánlott. Ebben a szakaszban is magában foglalja a kapcsolat biztonsági mentés és visszaállítás kell a katasztrófa utáni helyreállítás nagy HANA-példányok által kínált.

>   [!NOTE]
>   A pillanatkép-technológia, amely az alkalmazás mögötti infrastruktúra nagy HANA-példányok által használt függőség SAP HANA-pillanatképekkel rendelkezik. SAP HANA-pillanatképek ezen a ponton nem működnek együtt az SAP HANA több-bérlős adatbázis tárolók több bérlői. Ha csak egy bérlő van telepítve, SAP HANA-pillanatképek működnek, és ez a módszer is használható.

### <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>(Nagyméretű példányok) Azure storage-pillanatfelvételekkel a SAP HANA használatával

A tároló-infrastruktúra az alapul szolgáló SAP HANA (nagy példányok) Azure storage-pillanatfelvételekkel kötetek támogatja. Biztonsági mentési és a kötetek helyreállítása támogatott, azzal a következőket kell figyelembe venni:

- Helyett adatbázis teljes biztonsági mentést a rendszer az tárolási kötet-pillanatképek hajtja végre a gyakran.
- Pillanatkép kiváltó /hana/data és /hana/shared (tartalmazza a /usr/sap) keresztül kötetek, a pillanatkép-technológia indít el egy SAP HANA pillanatkép készítése a tárolási pillanatkép végrehajtása előtt. Az SAP HANA-pillanatkép végleges napló visszaállítását a telepítő pontja a tárolási pillanatkép a helyreállítás után. A sikeres HANA pillanatkép kell aktív HANA példánya.  HSR esetben a tároló snapshot utasítás nem támogatott az aktuális másodlagos csomópontra, ahol HANA pillanatkép nem hajtható végre.
- Miután a tárolási pillanatkép sikeresen végre lett hajtva, a SAP HANA-pillanatkép törlését.
- Tranzakciónapló biztonsági mentései gyakran kerül, és a /hana/logbackups kötetet, vagy az Azure-ban tárolja. A /hana/logbackups kötet, amely tartalmazza a tranzakciónapló biztonsági mentései egy pillanatkép készítése külön-külön aktiválhatók. Ebben az esetben nem kell végrehajtani egy HANA pillanatkép.
- Ha vissza kell állítania egy adatbázis egy bizonyos mértékig időben, kérelmek, hogy a Microsoft Azure támogatási szolgálatához (a termelési kimaradásáról) vagy a SAP HANA Azure szolgáltatásfelügyelet visszaállításkor egy bizonyos tárolási pillanatképre. Példa: a tervezett visszaállítás védőfal rendszer eredeti állapotát.
- A SAP HANA-pillanatkép, amely megtalálható a tárolási pillanatkép egy eltolási pontot az alkalmazása a tranzakciónapló biztonsági mentései, amelyek végrehajtása és a tárolási pillanatkép készítése tárolja.
- A tranzakciós napló biztonsági mentése visszaállítani az adatbázist egy bizonyos mértékig időben kerül.

Storage-pillanatfelvételekkel kötetek három osztályok célzó végezheti el:

- Kombinált pillanatkép/hana/adatokat, és /hana/shared (tartalmazza a/usr/sap) keresztül. A pillanatkép, a tárolási pillanatkép előkészítése egy SAP HANA-pillanatkép létrehozását igényli. Az SAP HANA-pillanatkép gondoskodik arról, hogy az adatbázis egy tárolási szempontból konzisztens állapotban van-e. És, hogy a helyreállítás dolgozza fel, amely egy pont beállítása a össze.
- Külön pillanatkép/hana/logbackups keresztül.
- Az operációsrendszer-partíció.

A legújabb pillanatkép parancsfájlok és dokumentáció [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). 

### <a name="storage-snapshot-considerations"></a>Tárolási pillanatkép kapcsolatos szempontok

>[!NOTE]
>Storage-pillanatfelvételekkel HANA nagy példány egységeire lefoglalt tárhely felhasználását. Fontolja meg az ütemezés a storage-pillanatfelvételekkel és hány tárolási pillanatképet kíván megőrizni a következő szempontokat kell. 

SAP Hana (nagy példányok) Azure storage-pillanatfelvételekkel a megadott idejéről a következők:

- Egy bizonyos tárolási pillanatkép (ha szükséges idő pontján) igényel, csekély tárolási.
- A tartalmi változások adatok és fájlok megváltozik a tárolókötetet az SAP HANA-adatok tartalma a pillanatkép kell tárolni, az eredeti tartalom blokkolása, valamint az adatok változásait.
- Ennek eredményeképpen a tárolási pillanatkép mérete növekszik. Minél hosszabb a pillanatkép van, annál nagyobb lesz a tárolási pillanatkép.
- A további módosításokat végzett a SAP HANA-adatbázis kötetén tárolási pillanatkép, a nagyobb életciklusa alatt a lemezterület-felhasználást a tárolási pillanatkép.

Az SAP HANA-adatainak és naplókönyvtárainak kötetek mérete rögzített SAP HANA Azure (nagy példányok) tartalmaz. Pillanatképek készítése a köteteket végrehajtása eats a kötet térbe kerülnek. Meg kell határoznia, mikor érdemes ütemezni a storage-pillanatfelvételekkel. Szükség a lemezterület-felhasználást a tároló kötetek figyeléséhez, valamint a tárolt pillanatképek számának kezelését. A storage-pillanatfelvételekkel letilthatja vagy tömegek adatok importálása vagy egyéb jelentős változások a HANA-adatbázishoz. 


A következő szakaszok ezeket a pillanatképeket, beleértve az általános ajánlásokat végrehajtásához információkkal:

- Abban az esetben, ha a hardver kötetenként 255 pillanatképek képes elviselni, ez a szám alatt maradnak szeretné. Javasoljuk, 250 vagy kisebb.
- Mielőtt elvégezné a storage-pillanatfelvételekkel, figyeléséhez és nyomon követésére szabad terület.
- A szabad lemezterület alapján tárolási pillanatképek számának csökkentéséhez. Mindig a pillanatképek száma csökkenthető, vagy kiterjesztheti a köteteket. További tárhely 1 terabájtnál egységekbe rendezheti.
- Során tevékenységek, például SAP HANA SAP platform áttelepítési eszközökről (R3load) adatok áthelyezése vagy SAP HANA-adatbázisok visszaállítása biztonsági másolatból tiltsa le a storage-pillanatfelvételekkel a /hana/data köteten. 
- SAP HANA-táblák nagyobb átszervezések során storage-pillanatfelvételekkel kerülni kell, ha lehetséges.
- Storage-pillanatfelvételekkel előfeltételt alapul véve a vész helyreállítási funkciók az SAP HANA Azure (nagy példány).

### <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Önkiszolgáló storage-pillanatfelvételekkel használatára vonatkozó Előfeltételek

Győződjön meg arról, hogy a pillanatkép parancsprogram sikeresen végrehajtja-e, ellenőrizze, hogy Perl a Linux operációs rendszert a HANA nagy példányok kiszolgálón van telepítve. A nagy példány HANA egység a Perl előre előre telepített. A Perl verzió ellenőrzéséhez használja a következő parancsot:

`perl -v`

![A nyilvános kulcsot másolja a parancs futtatása](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


### <a name="set-up-storage-snapshots"></a>Storage-pillanatfelvételekkel beállítása

Storage-pillanatfelvételekkel HANA nagy osztályt beállításához kövesse az alábbi lépéseket:
1. Győződjön meg arról, hogy Perl a Linux operációs rendszert a HANA nagy példányok kiszolgálón van telepítve.
2. Módosítsa az/etc/ssh/ssh\_adja hozzá a sort a konfigurációs _Mac számítógépekhez hmac-sha1_.
3. Minden SAP HANA-példány futtatja, a fő csomóponton egy SAP HANA biztonsági mentési felhasználói fiók létrehozása, ha van ilyen.
4. Az SAP HANA HDB ügyfél telepítése a SAP HANA nagy példányok összes kiszolgálójára.
5. Minden egyes régió első SAP HANA nagy példányok kiszolgálón hozza létre a mögöttes tároló-infrastruktúra, amely a pillanatkép létrehozása eléréséhez nyilvános kulcsot.
6. Másolja a parancsfájlok és a konfigurációs fájl [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts) helyére **hdbsql** a SAP HANA-telepítésnek.
7. Módosítsa a *HANABackupDetails.txt* fájl a megfelelő felhasználói előírások szerint szükséges.

A legújabb pillanatkép parancsfájlok és dokumentáció [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). 

### <a name="consideration-for-mcod-scenarios"></a>Szempont MCOD forgatókönyvek esetén
Ha futtat egy [MCOD forgatókönyv](https://launchpad.support.sap.com/#/notes/1681092) több SAP HANA példány egy HANA nagy példány egységen, az informatikai részleg külön a SAP HANA-példányok mindegyikének kiosztott köteteket. Az önkiszolgáló pillanatkép-automatizálás jelenlegi verziójában nem kezdeményezhet külön pillanatképek minden HANA példány rendszeren azonosítója (SID). A funkció ellenőrzi a kiszolgáló a konfigurációs fájlban (lásd a cikkben később) regisztrált SAP HANA-példányok nyújt, és végrehajtja a kötetek egységben regisztrált szoftverpéldányok egyidejű pillanatkép.
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>1. lépés: Az SAP HANA HDB ügyfél telepítése

SAP HANA Azure (nagy példányok) a Linux operációs rendszerrel a mappák és a parancsfájlok a biztonsági mentés és katasztrófa-helyreállítás céljából SAP HANA-tárolási pillanatképek végrehajtásához szükséges tartalmazza. Ellenőrizze a korábbi kiadásokban [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). A parancsfájlok a legújabb verzió 3.x. Előfordulhat, hogy a különböző parancsfájlok belül az azonos fő verziók különböző kisebb kiadások.

>[!IMPORTANT]
>Amikor 2.1-es verzióra 3.x a parancsfájlok, vegye figyelembe, hogy a konfigurációs fájlban és néhány szintaxis struktúrája megváltozott. Tekintse meg az adott szakaszok feliratokat. 

A feladata az SAP HANA HDB ügyfél SAP HANA telepíti a HANA nagy példány egységeken telepítéséhez.

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

A tárolási pillanatkép felületek HANA nagy példány bérlője elérésének lehetővé tétele, szüksége bejelentkezési eljárás keresztül a nyilvános kulcs létrehozására. Az első SAP HANA-bérlőben Azure (nagy példányok) kiszolgálón hozzon létre egy nyilvános kulcsot a tároló-infrastruktúra eléréséhez használandó. A nyilvános kulcs biztosítja, hogy jelentkezzen be a tárolási pillanatkép felületek nem szükséges jelszót. A nyilvános kulcs létrehozása is jelenti, hogy nem kell jelszót hitelesítő adatok karbantartása. Linux a SAP HANA nagy példányok kiszolgálón, a hajtsa végre a következő parancsot a nyilvános kulcs létrehozásához:
```
  ssh-keygen –t dsa –b 1024
```
Az új hely **_/root/.ssh/id\_dsa.pub**. Ne adjon meg egy tényleges jelszót, vagy pedig be kell írnia a jelszót, minden egyes bejelentkezéskor. Ehelyett válassza ki **Enter** kétszer a "jelszó" engedélyezése esetén nem jelentkezik be.

Győződjön meg arról, hogy a nyilvános kulcs javította mappák módosításával elvárt **/root/.ssh/** majd végrehajtása és a `ls` parancsot. Ha a kulcs található, másolja azt a következő parancs futtatásával:

![A nyilvános kulcsot másolja a parancs futtatása](./media/hana-overview-high-availability-disaster-recovery/image2-public-key.png)

Ezen a ponton lépjen kapcsolatba az Azure szolgáltatásfelügyelet SAP HANA, és adja meg a nyilvános kulcsot. A szolgáltatás képviselő a nyilvános kulcsával regisztrálja azt a mögöttes tároló-infrastruktúra, amely van faragottnak a HANA nagy példány bérlő számára.

### <a name="step-4-create-an-sap-hana-user-account"></a>4. lépés: Egy SAP HANA-felhasználói fiók létrehozása

SAP HANA-pillanatképek létrehozásának kezdeményez, hozzon létre egy felhasználói fiókot, amellyel a tárolási pillanatkép parancsfájlok SAP HANA kell. Létre kell hozni egy SAP HANA Studio SAP HANA-felhasználói fiókot. A felhasználó léteznie kell a SYSTEMDB és nem a SID-adatbázis a MDC. Egyetlen tároló környezetben a felhasználói beállítások alapján a bérlő adatbázis az. Ezt a fiókot a következő jogosultságokkal kell rendelkeznie: **biztonsági mentési rendszergazda** és **katalógus olvasási**. Ebben a példában a felhasználónév az **SCADMIN**. A felhasználói fiók nevét a HANA Studióban létrehozott a kis-és nagybetűket. Ügyeljen arra, hogy válasszon **nem** , amelyek a felhasználó megváltoztassa a jelszavát a következő bejelentkezés esetén.

![A felhasználó létrehozása a HANA Studióban](./media/hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

Ha több SAP HANA-osztályt egy egységen MCOD központi telepítéseket használ, ismételje meg ezt a lépést minden SAP HANA-példány szeretné.

### <a name="step-5-authorize-the-sap-hana-user-account"></a>5. lépés: Engedélyezze a SAP HANA-felhasználói fiók

Ebben a lépésben is engedélyezni szeretné a létrehozott, SAP HANA-felhasználói fiókot, hogy a parancsfájlok nem kell küldenie a jelszavak futásidőben. Az SAP HANA-parancs `hdbuserstore` létrehozását lehetővé tevő egy SAP HANA felhasználói kulcs, amely legalább egy SAP HANA-csomópontja tárolja. A felhasználói kulcs lehetővé teszi, hogy a felhasználói hozzáférés SAP HANA a parancsfájl-kezelési folyamaton belül a jelszavak kezelése nélkül. A parancsfájl-kezelési folyamat Ez a cikk későbbi részében olvashat.

>[!IMPORTANT]
>A következő parancsot az a felhasználó a parancsfájlok futtatását tervezett. Ellenkező esetben a parancsfájl nem működik megfelelően.

Adja meg a `hdbuserstore` parancsot a következőképpen:

**Az MDC HANA-telepítő**
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
Ha az HANA MCOD központi telepítése egy egységen több SAP HANA-példányt használja, ismételje meg a minden SAP HANA-példány és a kapcsolódó biztonsági mentési felhasználó egységben szeretné.

Ha egy SAP HANA-kibővített konfigurációt, akkor kell kezelniük, az összes parancsfájl-kezelési ugyanazon a kiszolgálón. Ebben a példában a SAP HANA-kulcs **SCADMIN01** módosítani kell úgy, hogy jeleníti meg, melyik gazdagépre kapcsolódik a kulcsot minden állomás számára. Az SAP HANA a példányszámának a HANA-adatbázis biztonsági mentési fiók módosítása. A kulcs rendszergazdai jogosultsággal kell rendelkeznie a gazdagépen, amelyhez hozzá van rendelve, és a biztonsági mentési felhasználó kibővített konfigurációk SAP HANA-példányainak hozzáférési jogosultsággal kell rendelkeznie. Feltéve, hogy a három kibővített csomópontok a nevük legyen **lhanad01**, **lhanad02**, és **lhanad03**, a parancssorrend néz ki:

```
hdbuserstore set SCADMIN01 lhanad01:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad02:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad03:30115 SCADMIN <password>
```

### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>6. lépés: A pillanatkép-parancsfájlok Get, konfigurálja a pillanatképek és a konfiguráció és a kapcsolat tesztelése

Töltse le a legfrissebb verzióját a parancsfájlokat a [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). Másolja a letöltött parancsfájlok és a szöveges fájl munkakönyvtára **hdbsql**. Ebben a könyvtárban van aktuális HANA telepítések esetében a következő formátumban: /hana/shared/D01/exe/linuxx86\_64/hdb. 
``` 
azure_hana_backup.pl 
azure_hana_replication_status.pl 
azure_hana_snapshot_details.pl 
azure_hana_snapshot_delete.pl 
testHANAConnection.pl 
testStorageSnapshotConnection.pl 
removeTestStorageSnapshot.pl
azure_hana_dr_failover.pl
azure_hana_test_dr_failover.pl 
HANABackupCustomerDetails.txt 
``` 

A Perl-parancsfájlokat meghatározásakor: 

- Soha ne módosítsa a parancsfájlok, hacsak ezt a Microsoft Operations kéri.
- Módosítsa a parancsfájl vagy egy paraméterfájl kéri, mindig használjon a Linux szövegszerkesztőbe, például "vi", és nem egy Windows szövegszerkesztőben, például a Jegyzettömbbel. Egy Windows-szerkesztővel előfordulhat, hogy sérült a fájl formátumát.
- Mindig a legújabb parancsfájlok használata. A Githubból letöltheti a legújabb verzióra.
- A fekvő parancsfájlokat ugyanazon verzióját használja.
- A parancsfájlok tesztelése, és lekérése kényelmes szükséges paraméterek és a parancsfájl közvetlenül a termelési rendszerben használat előtt.
- Ne módosítsa a kiszolgáló hozta-e a Microsoft Operations csatlakoztatási pont nevét. Ezek a parancsfájlok a sikeres végrehajtáshoz elérhető legyen a szabványos csatlakoztatási pontok támaszkodnak.


A különböző parancsfájlok és a fájlok célja az alábbiak szerint:

- **Azure\_hana\_backup.pl**: ezt a parancsfájlt a Linux Cron ütemezés segédprogram végrehajtására ütemezett storage-pillanatfelvételekkel a HANA adatok és megosztott kötetek, a/hana/logbackups köteten, vagy az operációs rendszer.
- **Azure\_hana\_replikációs\_status.pl**: ezt a parancsfájlt biztosít a replikációs állapot a munkakörnyezeti helyet a vész-helyreállítási hely körül alapvető adatait. A parancsfájl figyelőket, és győződjön meg arról, hogy a replikáció, és ebben az elemeknek a méretét mutatja, a rendszer replikálja. Is nyújt útmutatást, ha túl sokáig tart a replikációt, vagy ha a kapcsolat nem működik.
- **Azure\_hana\_pillanatkép\_details.pl**: Ez a parancsfájl minden pillanatképet, kötetenként, a környezetben meglévő kapcsolatos alapvető tudnivalókat listáját tartalmazza. Ez a parancsfájl futtathatja az elsődleges kiszolgálón vagy egy kiszolgáló egységen, a vész-helyreállítási helyen. A parancsfájl a következő adatokat, minden olyan kötetre, amely tartalmazza a pillanatképek bontásban tartalmazza:
   * A teljes pillanatképek a kötet mérete
   * Minden egyes pillanatfelvétel a köteten a következő adatokat: 
      - Pillanatfelvétel neve 
      - Létrehozás időpontja 
      - A pillanatkép mérete
      - A pillanatkép gyakorisága
      - Ha szükséges, hogy a pillanatkép társított HANA biztonsági azonosítója
- **Azure\_hana\_pillanatkép\_delete.pl**: Ez a parancsfájl törli tárolási pillanatkép vagy egy pillanatképet. Az SAP HANA biztonsági mentési azonosítója megegyezik a HANA Studióban található, vagy a pillanatkép nevét is használhatja. A biztonsági mentési azonosító jelenleg csak a HANA napló, adatokat vagy megosztott kötetek készült pillanatkép kötődik. Ellenkező esetben a pillanatkép-azonosító is meg kell adni, ha kíván minden pillanatképet, amelyek megfelelnek a megadott pillanatkép azonosítója.  
- **testHANAConnection.pl**: Ez a parancsfájl létesíthető kapcsolat az SAP HANA-példány és a storage-pillanatfelvételekkel beállításához szükséges.
- **testStorageSnapshotConnection.pl**: Ez a parancsfájl két célja van. Először is biztosítja, hogy a nagy példány HANA egység, amely futtatja a parancsfájlokat hozzáfér a hozzárendelt tároló virtuális gépet, és a nagy HANA-példányok tárolófelület pillanatkép. A második célja a tesztelni kívánt HANA példány ideiglenes pillanatkép létrehozása. Ezt a parancsfájlt annak érdekében, hogy a biztonsági mentési parancsfájlok fognak megfelelően működni a kiszolgálón kell futtatni minden HANA-példányhoz.
- **removeTestStorageSnapshot.pl**: Ez a parancsfájl törli a teszt pillanatkép létre a parancsfájl **testStorageSnapshotConnection.pl**.
- **Azure\_hana\_vész-helyreállítási\_failover.pl**: ezt a parancsfájlt a vész-Helyreállítási feladatátvételt indít el egy másik régióba. A parancsfájl kell hajtható végre, a vész-Helyreállítási régióban HANA nagy példány egységben, vagy egységben átadni kívánt. Ezt a parancsfájlt tárolási helyre való replikációt az elsődleges oldal másodlagos mellett leáll, a vész-Helyreállítási köteteken a legújabb pillanatkép visszaállítása, és a csatlakozási pontok le biztosít a vész-Helyreállítási kötetek.
- **Azure\_hana\_tesztelése\_vész-helyreállítási\_failover.pl**: ezt a parancsfájlt a vész-Helyreállítási helyre feladatátvételi tesztet hajt végre. Ellentétben a azure_hana_dr_failover.pl parancsfájl a végrehajtását nem szakítják meg a tárolási replikációt az elsődleges másodlagos. Ehelyett a replikált tároló kötetek klónok jönnek létre, a vész-Helyreállítási oldalon, és a csatlakozási pontok le a klónozott kötetek találhatók. 
- **HANABackupCustomerDetails.txt**: ezt a fájlt egy olyan módosítania kell a SAP HANA-konfiguráció támogató módosíthatóvá konfigurációs fájl. A *HANABackupCustomerDetails.txt* futtatja a storage-pillanatfelvételekkel a parancsfájl-vezérléshez és a konfigurációs fájl. Állítsa be a fájlt a célokra és a telepítőt. Kapni a **tárolási biztonsági másolat neve** és a **tárolási IP-cím** az SAP HANA az Azure szolgáltatásfelügyelet a példányok telepítésekor. A sorozat nem módosítható, rendezés, vagy bármely, a változók a fájlban található térköz. Ha így tesz, akkor ezek a parancsfájlok nem működnek majd megfelelően. Megjelenhet az IP-címét a méretezett vagy a fő csomópontot (Ha a kibővített) az Azure szolgáltatásfelügyelet az SAP HANA. Az SAP HANA telepítése során kapott HANA példányszámának is ismeri. Most akkor hozzon létre egy biztonsági másolat neve a konfigurációs fájl.

A konfigurációs fájl méretezett és kibővített üzembe helyezés esetén lenne az alábbi példához hasonló Miután kitölti a kiszolgálónevet a HANA nagy példány egység és a kiszolgáló IP-címét. Töltse ki az összes szükséges mezők minden SAP HANA SID kívánt biztonsági mentését vagy helyreállítását.

Még nem kívánt biztonsági mentése egy ideig hozzáadása a "#" mezőt kötelező kitölteni elé példányok sorok megjegyzéssé. Akkor is nem kell SAP HANA összes példányát a kiszolgálón található készítsen biztonsági másolatot, vagy állítsa helyre az adott esetben nincs szükség esetén adja meg. A formátum kell tartani, minden mezőbe, vagy minden parancsfájl throw hibaüzenetet, és a parancsfájl befejeződik. Bármely SID adatai nem használja az utolsó SAP HANA-példányok után további szükséges sorát törölheti. Az összes sort kell vagy kitölti, megjegyzésként szerepel, vagy törölve.

>[!IMPORTANT]
>A fájl struktúrája megváltozott a 2.1-es verzióra való áthelyezést 3.x. Ha a 3.x verzió parancsfájlok használni kívánt, akkor igazítja a konfigurációs fájl struktúra. 


```
HANA Server Name: testing01
HANA Server IP Address: 172.18.18.50
```

Az összes olyan példány HANA nagy példány egységben konfigurált, vagy a kibővített konfigurációjához kell határozza meg az adatok a következőképpen:

    
```
######***SID #1 Information***#####
SID1: h01
###Provided by Microsoft Operations###
SID1 Storage Backup Name: clt1h01backup
SID1 Storage IP Address: 172.18.18.11
######     Customer Provided    ######
SID1 HANA instance number: 00
SID1 HANA HDBuserstore Name: SCADMINH01
```
Kibővített és HANA replikációs konfigurációk esetén ismételje meg ezt a konfigurációt, az egyes csomópontokon. Ez a mérték gondoskodik arról, hogy a hiba esetben végleges tárolóreplikálást és biztonsági mentés továbbra is működik.   

Miután a konfigurációs adatok a *HANABackupCustomerDetails.txt* fájlt, ellenőrizze, hogy a beállítások helyesek-e a HANA-példányok adatait. A parancsfájllal `testHANAConnection.pl`, amely nem függ egy SAP HANA méretezett és kibővített konfigurációt.

```
testHANAConnection.pl
```

Ha egy SAP HANA-kibővített konfigurációt, győződjön meg arról, hogy a fő HANA példány fér hozzá az összes szükséges HANA kiszolgálók és példányok. A teszt parancsfájl nincsenek paraméterei, de hozzá kell adnia az adatait a *HANABackupCustomerDetails.txt* ki a parancsfájl futtatásához megfelelő konfigurációs fájlt. Csak a rendszerhéj parancs hibakódok ad vissza, ezért nem lehetséges a parancsfájl hibáját ellenőrizze mindegyik előfordulását. Még ha így a parancsfájl megjegyzést néhány hasznos ahhoz, hogy újra ellenőrizni.

A parancsfájl futtatásához írja be a következő parancsot:
```
 ./testHANAConnection.pl
```
Ha a parancsfájl sikeresen beszerzi a HANA példány állapotát, hogy sikeres volt-e a HANA kapcsolat üzenetet jelenít meg.


A következő vizsgálat-lépésre, hogy ellenőrizze a tároló alapján kerüljenek a kapcsolatot a *HANABackupCustomerDetails.txt* konfigurációs fájlt, és a vizsgálati pillanatkép hajthat végre. Végrehajtása előtt a `azure_hana_backup.pl` parancsfájl, futtatnia kell ezt a vizsgálatot. Ha egy kötet pillanatképek nem tartalmaz, lehetetlen meghatározni, hogy a kötet üres, vagy ha egy SSH sikertelen, a pillanatkép részletek beszerzése érdekében. Emiatt a parancsfájl végrehajtása két lépésből áll:

- Azt ellenőrzi, hogy a bérlő tároló virtuális gép és felületek a parancsfájlok pillanatképek számára.
- Pillanatképet hoz létre-teszt során, vagy helyőrző, minden olyan kötetre HANA példánya.

Emiatt a HANA példány része, amelynek argumentuma. A végrehajtás sikertelen lesz, ha nincs lehetőség arra, hogy a tárolási kapcsolat ellenőrzését. Akkor is, ha nincs ellenőrzése hiba, a parancsfájl biztosít hasznos mutatók.

1. Ez a vizsgálat végrehajtásához parancssorrend hajtható végre:

   ```
   ssh <StorageUserName>@<StorageIP>
   ```

   Mind a felhasználó nevét, és a tárolási IP-címet adtak meg a HANA nagy példány egység átadása.

2. Futtassa a test-parancsfájlt:
   ```
    ./testStorageSnapshotConnection.pl
   ```

A parancsfájl megpróbál a nyilvános kulcs lett megadva az előző lépésben a telepítő és a konfigurált adatokkal használatával jelentkezzen be a tároló a *HANABackupCustomerDetails.txt* fájlt. Ha a bejelentkezés sikeres, a következő tartalmat látható:

```
**********************Checking access to Storage**********************
Storage Access successful!!!!!!!!!!!!!!
```

Ha problémák merülnek fel, a a tárolási konzol csatlakozik, akkor a kimenete néz ki:

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

Után egy sikeres jelentkezzen be a tároló virtuális gép felületek a parancsfájl folytassa a 2. fázis, és létrehoz egy tesztelési pillanatképet. A kimeneti itt látható az SAP HANA-három csomópontos kibővített konfigurációt:

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

Ha a teszt pillanatkép a parancsfájl sikeresen végrehajtva, a tényleges storage-pillanatfelvételekkel konfigurálásánál lépne. Ha nem sikeres, a problémák vizsgálatához a soron előtt. A teszt pillanatkép körül kell maradnak, amíg meg nem történik az első valós pillanatképeket.


### <a name="step-7-perform-snapshots"></a>7. lépés:, Hajtsa végre a pillanatképek

Előkészítő lépések befejeződése után megkezdheti a tényleges pillanatkép konfiguráció konfigurálásához. A parancsfájl ütemezhető SAP HANA méretezett és kibővített konfigurációk működik. A biztonsági mentési parancsprogram ismétlődő és rendszeres végrehajtásra ütemezni a parancsfájl cron segédprogram segítségével. 

Három típusú pillanatképes biztonsági hozhat létre:
- **HANA**: egy kombinált pillanatkép biztonsági másolatából, amely a kötetek/hana/adatokat tartalmazó és a/hana/megosztott (/usr/sap is tartalmazó) a koordinált pillanatkép kezelt. Egyetlen fájlvisszaállításra lehetőség a pillanatképből.
- **Naplók**: a/hana/logbackups kötet egy pillanatkép biztonsági másolatából. Nincs HANA pillanatkép akkor váltódik ki, a tárolási pillanatkép végrehajtásához. A tárolási kötet arra szolgál, hogy a SAP HANA tranzakciónaplók biztonsági mentése tartalmazza. Ezek készül gyakrabban és napló növekedés esetleges adatvesztés elkerülése érdekében. Egyetlen fájlvisszaállításra lehetőség a pillanatképből. A gyakoriság 3 perc alatt nem csökkenthető.
- **Rendszerindító**: pillanatkép készítése a rendszerindító logikai egységen (LUN) nagy HANA-példány tartalmazó kötetet. A pillanatkép biztonsági másolatából csak a Type I termékváltozatok a HANA nagy példányok lehetséges. Nem hajtható végre egy fájlból visszaállítások a kötet, amely tartalmazza a rendszerindító LUN pillanatképből.


>[!NOTE]
> Ezek az áthelyezés MCOD központi telepítések támogatására verzió 3.x parancsfájlok módosítható három különböző hívás szintaxisa. Nincs szükség a HANA SID példány már meg. Győződjön meg arról, hogy egy SAP HANA példányai vannak-e konfigurálva a konfigurációs fájlban kell *HANABackupCustomerDetails.txt*.

>[!NOTE]
> Először hajtsa végre a parancsfájlt, ha azt a váratlan hibák multi-SID környezeti előfordulhat, hogy megjelenítése. A hibát, majd futtassa újból a parancsfájl javítja.



A storage-pillanatfelvételekkel a parancsfájl végrehajtása az új hívási szintaxis *azure_hana_backup.pl* dolgozunk:

```
HANA backup covering /hana/data and /hana/shared (includes/usr/sap)
./azure_hana_backup.pl hana <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

For /hana/logbackups snapshot
./azure_hana_backup.pl logs <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

For snapshot of the volume storing the boot LUN
./azure_hana_backup.pl boot <HANA Large Instance Type> <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

```

A részletek a paraméterek a következők: 

- Az első paraméter karakterizálja a pillanatkép biztonsági másolat típusa. Az engedélyezett értékek a következők **hana**, **naplók**, és **rendszerindító**. 
- A paraméter **<HANA Large Instance Type>** rendszerindító kötetet a biztonsági másolatokat csak szükséges. Nincsenek függő HANA nagy példány egységben "TypeI" vagy "TypeII" két érvényes érték. Tudja meg, hogy milyen típusú a egység című [SAP HANA (nagy példányok) – áttekintés és Azure architektúra](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).  
- A paraméter **< snapshot_prefix >** snapshot vagy snapshot típusú biztonsági mentési címke. Két célja van: egyet az, hogy adjon neki egy nevet, így ismerjük ezeket a pillanatképeket vonatkozó. A második feladata a parancsfájl *azure\_hana\_backup.pl* függvénnyel határozhatja meg, hogy adott címke alatt maradnak storage-pillanatfelvételekkel számát. Ha ütemez két tárolási pillanatképes biztonsági ugyanabba a típusba tartozik (például **hana**), két különböző címkével, és határozza meg, hogy 30 pillanatképek kell tartani az egyes, hogy végül 60 storage-pillanatfelvételekkel a kötetek is hatással. Csak alfanumerikus ("A-Z, a-z, 0-9"), aláhúzás ("_") és kötőjel ("-") karakterek használhatók. 
- A paraméter **< snapshot_frequency >** fenntartja a jövőbeli fejlesztésekkel, és nincs semmilyen hatása. Állítsa be a "3 perc" típusú biztonsági mentés végrehajtásakor **napló**, és a "15 perc" a többi biztonsági mentési típusok végrehajtása közben.
- A paraméter **<number of snapshots retained>** megtartása, a pillanatképek közvetve számú pillanatkép azonos előtaggal pillanatkép (címke) megadásával határozza meg. Ez a paraméter fontos ütemezett végrehajtások cron keresztül. Ha a ugyanazon snapshot_prefix pillanatképei száma meghaladja az ezzel a paraméterrel megadott, a legrégebbi pillanatkép törlését egy új tárolási pillanatkép végrehajtása előtt.

Egy kibővített esetén a parancsfájl nincs további ellenőrzi, hogy ellenőrizze, hogy minden HANA-kiszolgáló el tudja-e érni. A parancsfájl azt is ellenőrzi, hogy példányainak HANA térjen vissza a megfelelő állapot példánya egy SAP HANA-pillanatkép létrehozása előtt. Az SAP HANA-pillanatkép tárolási pillanatkép követi.

A parancsfájl végrehajtása `azure_hana_backup.pl` hoz létre a tárolót, a következő pillanatkép-folyamat három szakaszból:

1. Egy SAP HANA-pillanatkép végrehajtása
2. Végrehajtja a tárolási pillanatkép
3. Eltávolítja az SAP HANA-pillanatképet, amely korábban a végrehajtási, a tárolási pillanatkép jött létre

A parancsfájl végrehajtása hívja, amelyhez másolták HDB végrehajtható mappából. 

A megőrzési időtartam történik az paraméterként a parancsfájl végrehajtása esetén elküldi a pillanatképek számát. Ennyi idő alatt, amelyet a storage-pillanatfelvételekkel jelez, akkor egy függvény végrehajtása, valamint az elküldött paraméterként, amikor a parancsfájl végrehajtása pillanatképek számának időszak. Mindig a pillanatképek száma meghaladja a számát, amelyet a parancsprogram hívása paraméterként megnevezett, ha a legrégebbi tárolási pillanatkép a azonos címke törlését új pillanatkép végrehajtása előtt. Az ad, a hívás utolsó paraméterének száma segítségével tárolják a pillanatképek számát. Ez a számú is szabályozhatja, közvetve, a pillanatképek felhasznált lemezterület. 

> [!NOTE]
>Amint a címkék módosításához a számlálási újraindul. Kell lennie a címkézés szigorú, a pillanatképek véletlenül sem törlődnek.

### <a name="snapshot-strategies"></a>Pillanatkép stratégiák
A különböző pillanatképek gyakorisága attól függ, hogy használ-e a HANA nagy példány vész helyreállítási funkcióhoz. Ez a funkció a storage-pillanatfelvételekkel speciális ajánlások alkalmazáshoz szükséges a storage-pillanatfelvételekkel gyakoriságát és végrehajtási idő támaszkodik. 

A szempontokat és ajánlásokat, az alábbi, a feltételezi, hogy végezzen, *nem* HANA nagy példányok által vész-helyreállítási funkciók használatára. Ehelyett használhatja a storage-pillanatfelvételekkel biztonsági mentések és időpontban helyreállítást biztosít az elmúlt 30 napban. A pillanatképek és a terület száma vonatkozó korlátozások miatt az ügyfelek tekintette az alábbi követelményeknek:

- A helyreállítási idő időpontban helyreállításhoz.
- A használt lemezterület.
- A helyreállítási pont és helyreállítási időre vonatkozó célkitűzések a potenciális helyreállítás során egy olyan vészhelyzet esetén.
- A végleges végrehajtása HANA teljes-adatbázis biztonsági mentését lemezek ellen. Amikor teljes-adatbázis biztonsági másolatának elleni lemezek vagy a **backint** felület történik, a storage-pillanatfelvételekkel végrehajtása sikertelen lesz. Ha azt tervezi, hogy a teljes-adatbázis biztonsági másolatait fölött storage-pillanatfelvételekkel hajtható végre, ellenőrizze, hogy a storage-pillanatfelvételekkel végrehajtása le van tiltva ebben az időszakban.
- A pillanatképek kötetenként (legfeljebb 250) száma.


A vész helyreállítási funkciójának HANA nagy példányok nem használó ügyfelek a pillanatkép időszak ritkábban. Ebben az esetben az ügyfelek a kombinált pillanatképek /hana/data és végezni (tartalmazza a /usr/sap) /hana/shared 12 órás vagy 24 órás időszakban, és a pillanatképek egy hónapig vezetnek. Ugyanez érvényes a napló biztonsági mentési kötet pillanatfelvételekkel. Azonban SAP HANA tranzakciónapló biztonsági mentései a napló biztonsági mentési köteten végrehajtása történik 5 perces 15 perces időszakokra.

Ütemezett storage-pillanatfelvételekkel legjobb cron használatával hajtja végre. Ugyanazt a parancsfájlt használja a biztonsági mentések és a vész-helyreállítási igényekre, és módosítására, hogy a parancsfájl a forráson felel meg a különböző szükség a biztonsági mentési alkalommal. Ezeket a pillanatképeket minden ütemezett eltérően a attól függően, hogy a végrehajtási idő cron: óránkénti, 12 órás, napi vagy heti. 

A következő egy példa egy cron-ütemezését, a/etc/crontab:
```
00 1-23 * * * ./azure_hana_backup.pl hana hourlyhana 15min 46
10 00 * * *  ./azure_hana_backup.pl hana dailyhana 15min 28
00,05,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
22 12 * * *  ./azure_hana_backup.pl log dailylogback 3min 28
30 00 * * *  ./azure_hana_backup.pl boot TypeI dailyboot 15min 28
```
Az előző példában van egy óránkénti kombinált pillanatkép, amely lefedi a/hana/adatokat tartalmazó kötetek és /hana/shared (tartalmazza a/usr/sap) helyét. Az ilyen típusú pillanatkép egy gyorsabb időpontban helyreállításhoz használható az elmúlt két nap. Ezenkívül nincs napi pillanatkép azokon a köteteken. Igen, két napja van a érvényességének által óránkénti pillanatképeket, valamint a érvényességének négy hétben napi pillanatképek által. Továbbá a tranzakciós napló biztonsági mentési kötet készül biztonsági másolat naponta. Ezek a biztonsági másolatok, valamint négy hétig tartanak. Ahogyan a képen látható, a harmadik sorban a crontab, a HANA tranzakciós napló biztonsági mentését 5 percenként végrehajtására van ütemezve. A storage-pillanatfelvételekkel hajtható végre különböző cron szűrőillesztőből indítási idejének kerülnek lépcsőzetes elrendezésben, így ezek a pillanatképek nem hajtja végre egyszerre egy bizonyos időpontban időben. 

A következő példában hajtsa végre, amely lefedi a/hana/adatok és a/hana/megosztott (többek között a következőket/usr/sap) helyeket óránként tartalmazó kötetek összesített pillanatképet. Két napig megőrzi ezeket a pillanatképeket. A tranzakciós napló biztonsági mentési köteteken található egy 5 perces időközönként hajtja végre, és 4 óra tartanak. Mint előtt, a biztonsági mentés a HANA tranzakciós naplófájl végrehajtására ütemezett 5 percenként. A pillanatkép, a tranzakciós napló biztonsági mentési kötet 2 perces késleltetéssel történik, a tranzakciós napló biztonsági mentési megkezdése után. Ezek 2 percen belül a SAP HANA tranzakciós napló biztonsági mentési be kell fejeződnie, normál körülmények között. Előtt, a rendszerindító tartalmazó kötet LUN tárolási pillanatkép biztonsági mentését naponta egyszer és négy hétig maradnak.

```
10 0-23 * * * ./azure_hana_backup.pl hana hourlyhana 15min 48
0,5,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup.pl log logback 3min 48
30 00 * * *  ./azure_hana_backup.pl boot TypeII dailyboot 15min 28
```

A következő ábra az előző példában a logikai egység rendszerindító kivételével sorozatát mutatja be:

![A pillanatképek és a biztonsági mentés közötti kapcsolat](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA hajt végre a /hana/log kötet milyen véglegesített módosításokat az adatbázis rendszeres írására. Rendszeresen SAP HANA ír egy mentési pontra a /hana/data kötetre. Meghatározott crontab 5 percenként végrehajtása egy SAP HANA tranzakciós napló biztonsági mentését. Is láthatja, hogy egy SAP HANA-pillanatkép végrehajtja a rendszer minden órában miatt kombinált tárolási pillanatkép időt a /hana/data és /hana/shared kötetek keresztül. Miután a HANA pillanatkép sikeres, a kombinált tárolási pillanatkép végrehajtása. Útmutatását crontab, a tárolási pillanatkép a /hana/logbackup köteten minden 5 perc, a HANA tranzakciós napló biztonsági mentését követően körülbelül 2 percet végrehajtása.

> 

>[!IMPORTANT]
> A storage-pillanatfelvételekkel SAP HANA biztonsági mentés használata értékes csak akkor, ha a pillanatképek SAP HANA tranzakciónapló biztonsági mentései együtt történik. A tranzakciós napló biztonsági mentése, amelyek között a storage-pillanatfelvételekkel időszakokat kell. 

Ha beállította a 30 napos pont időponthoz kötött helyreállítás felhasználók kötelezettségvállalás, kell:

- Szélsőséges esetben pillanatkép-/hana/data és, hogy a 30 napos /hana/shared kombinált tárolási eléréséhez.
- Rendelkezik összefüggő tranzakciónapló biztonsági mentései a kombinált tárolási készített pillanatképeket között eltelő idő. Igen a legrégebbi pillanatkép, a tranzakciós napló biztonsági mentési kötet kell lennie a 30 napos definícióval. Nem ez a helyzet, ha a tranzakciónapló biztonsági mentései egy másik NFS-megosztás, az Azure storage található másolja. Ebben az esetben előfordulhat, hogy lekéréses régi tranzakciónapló biztonsági mentései az adott NFS-megosztások.

Storage-pillanatfelvételekkel és a végleges tárolóreplikálást tranzakciós naplók biztonsági másolatainak kihasználják, módosítani szeretné a helyet, amelyhez az SAP HANA ír a tranzakciónapló biztonsági mentései. Ez a változás HANA Studio tehet. Bár a SAP HANA biztonsági másolatot készít a naplófájl teljes szegmensek automatikusan, adja meg a napló biztonsági mentési időközt determinisztikus. Ez különösen igaz olyan esetben használata esetén a vész-helyreállítási beállítások általában szeretné végrehajtani a napló-biztonságimásolatokat determinisztikus időszakkal. A következő esetben 15 perc vannak beállítva a napló biztonsági mentési időközt.

![Az SAP HANA Studio naplózza az SAP HANA-biztonsági mentés ütemezése](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Dönthet úgy is, mint 15 percenként biztonsági mentéseket. Egy gyakoribb beállítást gyakran használják együtt vész helyreállítási funkciójának HANA nagy példányok is. Egyes ügyfelek hajtsa végre a tranzakciónapló biztonsági mentései 5 percenként.  

Az adatbázis soha nem készült biztonsági másolat, a végső lépés esetén a fájl alapú adatbázis biztonsági mentést készíteni, amely a biztonságimásolat-katalógus léteznie kell egy biztonsági mentési bejegyzés létrehozása. Ellenkező esetben SAP HANA nem indítható el a megadott napló biztonsági másolatokat.

![Fájl alapú biztonsági mentést készíteni a biztonsági mentési egyetlen bejegyzés létrehozásához](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Az első sikeres storage-pillanatfelvételekkel végrehajtása után törölheti a teszt pillanatképet, amely a 6. lépésben végre lett hajtva. Ehhez futtassa a parancsfájlt `removeTestStorageSnapshot.pl`:
```
./removeTestStorageSnapshot.pl
```

A következő egy példa a parancsfájl kimenete:
```
Checking Snapshot Status for h80
**********************Checking access to Storage**********************
Storage Snapshot Access successful.
**********************Getting list of volumes that match HANA instance specified**********************
Collecting set of volumes hosting HANA matching pattern *h80* ...
Volume show completed successfully.
Adding volume hana_data_h80_mnt00001_t020_vol to the snapshot list.
Adding volume hana_log_backups_h80_t020_vol to the snapshot list.
Adding volume hana_shared_h80_t020_vol to the snapshot list.
**********************Adding list of snapshots to volume list**********************
Collecting set of snapshots for each volume hosting HANA matching pattern *h80* ...
**********************Displaying Snapshots by Volume**********************
hana_data_h80_mnt00001_t020_vol
Test_HANA_Snapshot.2018-02-06_1753.3
Test_HANA_Snapshot.2018-02-06_1815.2
….
Command completed successfully.
Exiting with return code: 0
Command completed successfully.
```


### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Figyelési száma és mérete a lemez köteten pillanatképek

Egy bizonyos tárolási köteten figyelheti a pillanatképek és a tárhelyhasználatot, ezek a pillanatképek számát. A `ls` parancs nem jeleníti meg a pillanatkép könyvtárt vagy fájlokat. Azonban a Linux operációs rendszert futtató parancs `du` ezeket a storage-pillanatfelvételekkel részleteit jeleníti meg, mert ezeket a azonos köteteken tárolja. A parancs is használható a következő beállításokkal:

- `du –sh .snapshot`: Ezt a lehetőséget biztosít a pillanatkép-könyvtárban lévő összes pillanatképet összesen.
- `du –sh --max-depth=1`: Ez a beállítás a pillanatképek a mentett sorolja fel a **.snapshot** mappa, és minden pillanatkép méretét.
- `du –hc`: Ezt a lehetőséget biztosít a pillanatképek által használt teljes mérete.

Ezek a parancsok segítségével győződjön meg arról, hogy a végrehajtott és tárolt pillanatképek nem használ fel azokon a köteteken a tárolót.

>[!NOTE]
>A pillanatképek a Boot logikai egység nem láthatók el az előző parancsokkal.

### <a name="getting-details-of-snapshots"></a>A pillanatképek-információk lekérdezése
További információkhoz juthat a pillanatképeket, használhatja a parancsfájl `azure_hana_snapshot_details.pl`. Ez a parancsfájl az egyik helyen futtatható, ha nincs aktív kiszolgáló a vész-helyreállítási helyen. A parancsfájl a következő kimeneti, minden olyan kötetre, amely tartalmazza a pillanatképek bontásban tartalmazza: 
   * A teljes pillanatképek a kötet mérete
   * Minden egyes pillanatfelvétel a köteten a következő adatokat: 
      - Pillanatfelvétel neve 
      - Létrehozás időpontja 
      - A pillanatkép mérete
      - A pillanatkép gyakorisága
      - Ha szükséges, hogy a pillanatkép társított HANA biztonsági azonosítója

A következő egy példa a parancsfájl végrehajtási szintaxist:

```
./azure_hana_snapshot_details.pl 
```

Megpróbálja beolvasni a HANA biztonsági Azonosítóját, mert azt kapcsolódnia kell az SAP HANA-példány. A kapcsolathoz szükséges konfigurációs fájl *HANABackupCustomerDetails.txt* megfelelően kell beállítani. A két pillanatképek a kötet kimenettel nézhet ki például a következők:

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
A pillanatkép-típusok **hana** és **naplók**, közvetlenül a köteteken a pillanatképek végezheti el a **.snapshot** könyvtár. Nincs minden, a pillanatképek egyik alkönyvtár. Minden fájl másolhatja, mint a pillanatkép, az, hogy a tényleges directory struktúrába alkönyvtár helyén állapotban. A parancsfájl az aktuális verziójában nincs **nem** állítsa vissza a parancsprogram előírt a pillanatkép-visszaállítás egy önkiszolgáló (bár a pillanatkép-visszaállítás segítségével hajtható végre, mert az önkiszolgáló vész-Helyreállítási része a feladatátvétel során a parancsfájlok a vész-Helyreállítási helyen). Nyissa meg a kívánt pillanatkép visszaállítása a meglévő elérhető származó szolgáltatáskérés forduljon a Microsoft operations csoportja.

>[!NOTE]
>Visszaállítás egy fájlból nem működik a rendszerindító logikai egység független a HANA nagy példány mértékegység típusát a pillanatkép-készítési. A **.snapshot** directory a rendszerindítási logikai egység nem lesz közzétéve. 


### <a name="reducing-the-number-of-snapshots-on-a-server"></a>Egy kiszolgálón pillanatképek számának csökkentése

Ahogy korábban, csökkentheti az egyes címkék tárolt pillanatképek számát. Az utolsó két pillanatkép kezdeményezni a parancs paraméterei a címkét, és meg szeretné őrizni a pillanatképek számára.

```
./azure_hana_backup.pl hana dailyhana 15min 28
```

Az előző példában a pillanatkép címkéje **dailyhana** és a következő címkével megőrzendő pillanatképek száma **28**. Lemezterület-felhasználást válaszolni, mivel a tárolt pillanatképek számának csökkentése érdekében érdemes. A egyszerűen 15, például a pillanatképek számának csökkentése érdekében beállítva az utolsó paraméterrel a parancsfájl futtatásához **15**:

```
./azure_hana_backup.pl hana dailyhana 15min 15
```

A parancsfájl futtatása ezt a beállítást, ha a pillanatképek számáról, beleértve az új tárolási pillanatkép érték 15. A 15 legutóbbi pillanatfelvételek megmaradnak, és a 15 régebbi pillanatképeket a rendszer törli.

 >[!NOTE]
 > Ez a parancsfájl csökkenti a pillanatképek csak akkor, ha több mint 1 óra régi pillanatképek vannak. A parancsfájl nem törli a pillanatképeket, amelyek 1 órával korábbiak. Ezek a korlátozások a választható vész helyreállítási funkciói kapcsolódnak.

Ha már nem szeretné a biztonsági mentési címke pillanatképei készlete karbantartása **hanadaily** szintaxis példákban hajtható végre a parancsprogram **0** megőrzési számának. A label megfelelő összes pillanatképet majd törlődnek. Azonban minden pillanatképet eltávolítása hatással lehet a HANA nagy példányok vész-helyreállítási funkcióhoz képességeit.

A második lehetőség törölheti az adott pillanatképet egy parancsfájl `azure_hana_snapshot_delete.pl`. Ez a parancsfájl célja, hogy a törölni kívánt pillanatképet vagy állítsa a pillanatképek vagy segítségével a biztonsági mentési HANA-azonosító található a HANA Studio vagy a pillanatkép-név. Jelenleg a biztonsági azonosító csak kötődik készült pillanatkép a **hana** pillanatkép-típus. Pillanatkép készítése a típusú biztonsági mentések **naplók** és **rendszerindító** ne hajtsa végre egy SAP HANA-pillanatkép, és ezért nincs nem találhatók, ezek a pillanatképek biztonsági azonosító. A pillanatkép nevét is meg kell adni, ha minden pillanatkép a különböző köteteken, amelyek megfelelnek a megadott pillanatfelvétel neve keresi. 

Hívja a parancsfájlt, meg kell adnia a SID HANA-példány a parancsfájl a hívási szintaxis használatával:

```
./azure_hana_snapshot_delete.pl <SID>

```

Futtassa a parancsfájlt felhasználóként **legfelső szintű**.

Ha egy pillanatképet, külön-külön törölheti minden pillanatkép. Először adja meg a pillanatképet tartalmazó kötet, és a pillanatkép-nevet adja. Ha a pillanatkép, hogy a kötet létezik, és több mint 1 óra régi, akkor az törlődik. Találhatja meg a kötet neve és a pillanatkép neve végrehajtása a `azure_hana_snapshot_details` parancsfájl. 

>[!IMPORTANT]
>Ha csak a pillanatkép található adatok törli, a pillanatkép törlését követően, hogy adatok nem vesztek tartja.

   

### <a name="recover-to-the-most-recent-hana-snapshot"></a>A legutóbbi HANA pillanatkép helyreállítás

Egy éles lefelé forgatókönyv esetén helyreállítható tárolási pillanatképből, a Microsoft Azure-támogatással rendelkező ügyfelek, amelyhez kezdeményezhető. A magas sürgősségű függetlenül attól, hogy ha adat törölve lett a termelési rendszer, és csak ennek lekéréséhez, állítsa vissza az éles adatbázist is.

Egy másik helyzetben pont időponthoz kötött visszaállítás előfordulhat, hogy alacsony sürgősség, és a nappal tervezett. Megtervezheti ennek a helyreállításnak az SAP HANA az Azure szolgáltatásfelügyelet helyett kiváltása a magas prioritású jelzőt. Például előfordulhat, hogy lehet tervezi az SAP szoftverfrissítésre új fejlesztés csomag alkalmazásával. Akkor kell állítható vissza olyan pillanatképet, amely a fejlesztés csomag frissítés előtt állapotát jeleníti meg.

A kérelem elküldése előtt elő kell készíteni. Az SAP HANA Azure szolgáltatásfelügyeleti csoport majd kezelni a kérést, és adja meg a visszaállított kötetek. Ezután állítsa vissza a pillanatképek alapján HANA-adatbázisból. 

A következő bemutatja, hogyan készülhet fel a kérelem:

>[!NOTE]
>A felhasználói felület eltérhetnek attól függően, hogy az Ön által használt SAP HANA-kiadás az alábbi képernyőképek.

1. Döntse el, melyik pillanatkép visszaállítása. Csak a hana/adatmennyiség helyreáll, kivéve, ha más módon utasította. 

2. Állítsa le a HANA-példány.

 ![Állítsa le a HANA-példány](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

3. Válassza le az adatkötetek minden HANA-adatbázis csomóponton. Ha az adatok továbbra is csatlakoztatva vannak az operációs rendszer, a pillanatkép visszaállítása sikertelen lesz.
 ![Minden egyes HANA adatbázis csomóponton az adatkötetek leválasztása](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

4. Nyissa meg az Azure támogatási kérelmet, és a visszaállítást egy adott pillanatkép vonatkozó utasításokat tartalmazza.

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

### <a name="recover-to-the-most-recent-state"></a>A legfrissebb állapotba helyreállítása

A következő folyamat visszaállítja a HANA pillanatkép, amely megtalálható a tárolási pillanatkép. Majd helyreállítja a tranzakciónapló biztonsági mentései a legfrissebb állapotba az adatbázis csak a tárolási pillanatkép visszaállítása után.

>[!IMPORTANT]
>Mielőtt továbblépne, győződjön meg arról, hogy rendelkezik-e a tranzakciónapló biztonsági mentései teljes és összefüggő láncolata. A biztonsági mentése nélkül az adatbázis jelenlegi állapotában nem állítható vissza.

1. Végezze el a lépéseket 1-6 [végezze a helyreállítást a legújabb HANA pillanatkép](#recovering-to-the-most-recent-hana-snapshot).

2. Válassza ki **állítsa helyre az adatbázist a legújabb állapotában**.

 ![Válassza a "Legutóbbi állapotában az adatbázis helyreállítása"](./media/hana-overview-high-availability-disaster-recovery/image16-recover-database-a.png)

3. Adja meg a legutóbbi HANA naplóalapú biztonsági mentések helyét. Minden a HANA tranzakciónapló biztonsági mentései a HANA pillanatképből a legfrissebb állapotba tartalmaznia kell a helyre.

 ![A legutóbbi HANA naplók biztonsági másolatainak elérési útja](./media/hana-overview-high-availability-disaster-recovery/image17-recover-database-b.png)

4. Válassza ki a biztonsági mentés alapjaként, amelyből az adatbázis helyreállítása. Ebben a példában a HANA pillanatkép ezen a képernyőfelvételen a HANA pillanatképet, amely a tárolási pillanatkép fájlban. 

 ![Válassza ki a biztonsági mentés alapjaként, amelyből az adatbázis helyreállítása](./media/hana-overview-high-availability-disaster-recovery/image18-recover-database-c.png)

5. Törölje a jelet a **használata különbözeti biztonsági mentések** jelölőnégyzetet, ha eltérések nem léteznek a HANA pillanatkép és a legutóbbi állapot között.

 ![Törölje a "Használja a különbözeti biztonsági mentések" jelölőnégyzet jelölését, ha eltéréseit – nem létezik](./media/hana-overview-high-availability-disaster-recovery/image19-recover-database-d.png)

6. Az összefoglaló képernyőn válassza ki a **Befejezés** elindítani a visszaállítási művelet.

 ![Az Összegzés képernyőn kattintson a "Befejezés"](./media/hana-overview-high-availability-disaster-recovery/image20-recover-database-e.png)

### <a name="recover-to-another-point-in-time"></a>Helyreállítás másik pont időben
A HANA pillanatkép (tartalmazza a tárolási pillanatkép) és egy későbbi, mint a HANA pillanatkép időpontban helyreállítási pontra helyreállítása, hajtsa végre az alábbi lépéseket:

1. Győződjön meg arról, hogy rendelkezik-e az összes a tranzakciónapló biztonsági mentései a HANA pillanatképből a helyreállítani kívánt alkalommal.
2. Az eljárás megkezdése [helyreállítani a legfrissebb állapotba](#recovering-to-the-most-recent-state).
3. A 2. lépés az eljárás a a **helyreállítási típus megadása** ablakban válassza ki **állítsa helyre az adatbázist a következő pont időben**, és majd meg azt az időpontot. 
4. Hajtsa végre a 3-6 lépéseket.

### <a name="monitor-the-execution-of-snapshots"></a>A figyelő pillanatképek végrehajtása

Storage-pillanatfelvételekkel HANA nagy példányok használata akkor is figyelnie kell ezeket a pillanatképeket végrehajtása. A parancsfájl, amely végrehajtja a tárolási pillanatkép kimeneti fájlba írja, és menti azt a Perl-parancsfájlokat ugyanazon a helyen. Minden egyes tárolási pillanatkép készült külön fájlt. Minden fájl kimenetét, hogy a pillanatkép-parancsfájl végrehajtása a különböző fázisait mutatja:

1. Megkeresi a köteteket, létre kell hoznia egy pillanatkép.
2. Megkeresi a pillanatképek venni ezeket a köteteket.
3. Törli a pillanatképeket a megadott számú végleges meglévő pillanatképeket.
4. Létrehoz egy SAP HANA-pillanatképet.
5. A tárolási pillanatképet hoz át a köteteket.
6. Törli a SAP HANA-pillanatkép.
7. A legutóbbi pillanatképének átnevezi **.0**.

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
Ettől a példától láthatja, hogy a parancsfájl rögzíti a HANA pillanatkép létrehozását. A kibővített esetben ez a folyamat a fő csomópont indítható. A fő csomópont létrehozását kezdeményezi a szinkron a SAP HANA-pillanatképek egyes a munkavégző csomópontokhoz. A tárolási majd pillanatfelvétel. A storage-pillanatfelvételekkel sikeres végrehajtása után a HANA pillanatkép törlését. A fő csomópont kezdeményezni a HANA pillanatkép törlését.


## <a name="disaster-recovery-principles"></a>Vész-helyreállítási alapelvei
HANA nagy példányok egy vész-helyreállítási funkcióhoz HANA nagy példány bélyegekre az különböző Azure-régiók közötti kínálnak. Például ha HANA nagy példány egységek Azure Velünk nyugati régiójában, akár is használhatja a HANA nagy példány egységek amerikai keleti régióban vész-helyreállítási egység. A korábban említett vész-helyreállítási beállítása nem automatikusan, mert ahhoz szükséges, hogy egy másik HANA nagy példány egység a vész-Helyreállítási régióban kell fizetnie. A vészhelyreállítás telepítője méretezett, valamint a kibővített telepítéshez működik. 

Az eddig telepített forgatókönyvekben az ügyfelek használja az egység a vész-Helyreállítási régióban HANA telepített példányát használja, nem éles rendszerek futtatására. A nagy példány HANA egység kell lennie a Termékváltozat üzemi célokra, ugyanazon metódust. A következő kép bemutatja, milyen a lemez konfigurációját a kiszolgáló egységet az éles Azure-régióban között, és a vész-helyreállítási régió néz ki:

![A lemez szempontjából vész-Helyreállítási beállítási konfiguráció](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

Ahogyan az Áttekintés ábrán látható, majd egy második együttesét lemezkötetek sorrendben kell. A cél lemezkötetek akkora, mint a termelési kötetek, a vész-helyreállítási egység az éles példány. A kötetek társítva a HANA nagy példány server egység a vész-helyreállítási helyen. A következő kötetek replikálva vannak a termelési régióban a vész-Helyreállítási hely:

- / hana/adatok
- / hana/logbackups 
- /Hana/Shared (tartalmazza a/usr/sap)

A /hana/log kötet nem replikálódik, mert a SAP HANA-tranzakciónapló módon történik, ezek a kötetek való visszaállítása nem szükséges. 

A vész-helyreállítási funkció alapján ajánlott a storage replikáció működése a HANA nagy példány infrastruktúra által kínált. A funkció a tárolási oldali használt nincs replikálása aszinkron módon módosítások fordulhat elő, a tárolási köteten módosítások állandó adatfolyam. Ehelyett egy olyan mechanizmus, amely azt a tényt, hogy a pillanatképek kötetek jöttek létre rendszeresen támaszkodik. Egy már replikált pillanatkép és egy új pillanatkép, amely még nem replikálta a különbözeti majd átkerül a vész-helyreállítási hely a tároló kötetek.  Ezeket a pillanatképeket azokon a köteteken tárolja, és egy vész-helyreállítási feladatátvevő esetén kell állítani a köteteket a.  

A teljes kötet első adatátvitelt kell lennie, mielőtt adatok mennyisége kisebb, mint a pillanatképek közötti eltérések válik. A köteteket a vész-Helyreállítási hely eredményeképpen minden egyes, a kötet-pillanatképek végez a munkakörnyezeti helyet tartalmaz. Végül, hogy vész-Helyreállítási rendszer használatával elveszett adatok helyreállítása nélkül a termelési rendszer visszaállítása egy korábbi állapotba beolvasása.

MCOD központi telepítések egy HANA nagy példány egységet több független SAP HANA osztályt, esetén várható, hogy minden SAP HANA-példány a vész-Helyreállítási oldalon replikált tároló kap.

Olyan esetekben, amikor a munkakörnyezeti helyet a magas rendelkezésre állású funkció HANA replikációs használják, és tároló-alapú replikáció használata a vész-Helyreállítási hely replikálva vannak a kötetek mindkét csomópont elsődleges helyről a vész-Helyreállítási példányhoz. További tárterület (elsődleges csomópont frissítésétől azonos méretűnek) kell vásárolnia, az elsődleges és másodlagos, a vész-Helyreállítási replikációs olyan vész-Helyreállítási helyen. 



>[!NOTE]
>A nagy példány HANA storage replikáció működése tükrözött, és a storage-pillanatfelvételekkel replikálása. Ha jelent meg a storage-pillanatfelvételekkel nem hajtható végre a [biztonsági mentési és visszaállítási](#backup-and-restore) szakasz Ez a cikk nem lehet a replikációt, hogy a vész-helyreállítási helyet. Tárolás pillanatkép végrehajtási előfeltétele a vész-helyreállítási hely tárolóreplikálást.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>A vész-helyreállítási forgatókönyv elkészítése
Ebben a forgatókönyvben éles operációs rendszert futtató HANA nagy példányokon az éles Azure-régióban van. A következő lépések Tételezzük fel, hogy, hogy a biztonsági AZONOSÍTÓT az adott HANA rendszer "PRD", és, hogy rendelkezik-e a vész-Helyreállítási Azure régióban nagy HANA-példányokon nem éles rendszerben. Az utóbbi Tételezzük fel, hogy, hogy a biztonsági azonosító-e a "Teszt". Az alábbi ábrán ez a konfiguráció:

![DR-telepítés elindítása](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Ha a kiszolgálópéldány már nem lett rendezett további tárhely kötet beállított, az Azure szolgáltatásfelügyelet rendeli SAP HANA a további kötetek a HANA nagy példány egységhez, amelyiken futtatja a teszt termelési replikaként cél HANA-példány. Erre a célra meg kell adnia a termelési HANA példányát biztonsági azonosítója. Az Azure szolgáltatásfelügyelet SAP HANA megerősíti, hogy a köteteket a mellékletet, után kell csatlakoztatni a köteteket a HANA nagy példány egységhez.

![DR telepítő következő lépés](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

A következő lépés, hogy a felhasználó a második SAP HANA-példány telepítését a vész-Helyreállítási Azure régióban, a teszt HANA-példányt futtató HANA nagy példány egységben. Az újonnan telepített SAP HANA-példány SID AZONOSÍTÓVAL rendelkeznie kell. A felhasználó hozott létre kell UID és csoport azonosítója, amely a termelési példány rendelkezik azonos. Ha a telepítés sikeres volt, szükség:

- A cikkben korábban tárgyalt pillanatkép előkészítése 2. lépés végrehajtása.
- Hozzon létre egy nyilvános kulcsot a vész-Helyreállítási egység HANA nagy példány egység, ha még nem még meg. A cikkben korábban tárgyalt pillanatkép előkészítése 3. lépése.
- Karbantartása a *HANABackupCustomerDetails.txt* új HANA példány és teszt e kapcsolat a tároló megfelelően működik-e.  
- Állítsa le az újonnan telepített SAP HANA-példányt a vész-Helyreállítási Azure régióban HANA nagy példány egységben.
- Válassza le a PRD ezeket a köteteket, és lépjen kapcsolatba az Azure szolgáltatásfelügyelet SAP HANA. A kötetek nem maradnak csatlakoztatott egységhez, mert nem érhető el, de a tárolási replikációs céljaként.  

![DR telepítő lépés előtt replikációs létrehozó](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

A műveleti csapata hoz létre a replikációs kapcsolatot a PRD az éles Azure-régió, és a PRD kötetek a vész-Helyreállítási Azure régióban között.

>[!IMPORTANT]
>A /hana/log kötet nem replikálódik, mert nincs szükség a replikált SAP HANA-adatbázis visszaállítása a vész-helyreállítási hely konzisztens állapotra.

Ezután állítsa be, vagy állítsa be a tárolási pillanatkép biztonsági mentés ütemezése a vészhelyreállítás esetében a RTO és a helyreállítási Időkorlát eléréséhez. A helyreállítási időkorlát minimalizálása érdekében állítsa be a következő replikálási időközök HANA nagy példány szolgáltatási:
- A kötetek, a kombinált pillanatkép hatálya (pillanatkép-típus **hana**), a vész-helyreállítási helyen azonos tárolási kötet szoftveres 15 percenként replikálásához beállítása.
- A tranzakciós napló biztonsági mentési kötet (pillanatkép-típus **naplók**), állítsa át 3 percenként, a vész-helyreállítási helyen azonos tárolási kötet szoftveres replikálásához.

A helyreállítási időkorlát minimalizálása érdekében állítsa be a következőket:
- Hajtsa végre a **hana** típus tárolási pillanatkép (lásd a "7. lépés: hajtsa végre a pillanatképek") 1 óra 30 percenként.
- Hajtsa végre az SAP HANA tranzakciónapló biztonsági mentései 5 percenként.
- Hajtsa végre a **naplók** írja be az 5-15 percenként pillanatkép-tárolás. Az időköz időtartam érhetők el az RPO körülbelül 15 25 perc.

Ennek beállításához, a tranzakciónapló biztonsági mentései, storage-pillanatfelvételekkel és a replikáció a HANA tranzakció sorozatát jelentkezzen mennyisége és/hana/biztonsági mentési adatokat, és (tartalmazza a/usr/sap) /hana/shared nézhet ki például az adatokat, az ábrán látható:

 ![A tranzakciós napló biztonsági mentési pillanatképet, és egy idő tengelyen beépülő tükör közötti kapcsolat](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

A vész-helyreállítási esetben egy még élvezetesebbé RPO eléréséhez átmásolhatja a HANA tranzakciónapló biztonsági mentései SAP HANA Azure (nagy példányok) számára a más Azure-régiót. A további helyreállítási Időkorlát csökkentése érdekében, hajtsa végre az alábbi lépéseket:

1. Készítsen biztonsági másolatot a HANA tranzakció jelentkezzen nyújtott a lehető /hana/logbackups.
2. Rsync követve másolja át a tranzakciónapló biztonsági mentései a NFS megosztás-kiszolgálón futó Azure virtuális gépeken. A virtuális gépek az Azure virtuális hálózatok és a vész-Helyreállítási régiókban az éles Azure-régióban van. A kapcsolatcsoport az üzemi HANA nagy példányok csatlakozik Azure mindkét Azure virtuális hálózat csatlakoztatni kell. Lásd: a képek a [szempontok vész-helyreállítási HANA nagy osztályt hálózati](#Network-considerations-for-disaster recovery-with-HANA-Large-Instances) szakasz. 
3. A tranzakciónapló biztonsági mentései a régióban, a virtuális gép csatlakozik a NFS megtartása tárolási exportált.
4. Vészhelyreállítás feladatátvételi esetben kiegészítik a tranzakciónapló biztonsági mentései megtalálta a /hana/logbackups köteten több nemrég végrehajtott az NFS a tranzakciós napló biztonsági mentések megosztani a vész-helyreállítási helyen. 
5. Indítsa el a tranzakciós napló biztonsági mentési állítsa vissza a legutóbbi biztonsági mentése a vész-Helyreállítási terület felett mentheti.

Ha elindítja a végrehajtási tárolási pillanatképes biztonsági HANA nagy példány műveletek erősítse meg a replikációs kapcsolat beállítása, a adatreplikáció kezdődik.

![DR telepítő lépés előtt replikációs létrehozó](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

Mivel a replikációs történik, a vész-Helyreállítási Azure-régiókban PRD köteteken a pillanatképeket a rendszer nem állítja vissza. Csak tárolják. A kötetek csatlakoztatva legyenek olyan állapotban, ha az állapot, amelyben meg leválasztani a köteteket a vész-Helyreállítási Azure azon régióját, a kiszolgáló egységben a PRD SAP HANA-példány telepítése után képviselik. A tároló biztonsági mentések, amelyek nem lettek visszaállítva is képviselik.

Feladatátvétel esetén is használhat egy régebbi tárolási pillanatkép helyett a legújabb tárolási pillanatkép visszaállítása.

## <a name="disaster-recovery-failover-procedure"></a>Vész-helyreállítási feladatátvételi folyamatot
Két olyan eset létezik a vész-Helyreállítási hely feladatátadás során figyelembe kell venni:

- Az SAP HANA-adatbázisból, menjen vissza a legutóbbi állapota, az adatok van szüksége. Ebben az esetben van egy önkiszolgáló parancsfájlt, amellyel a feladatátvétel, lépjen kapcsolatba a Microsofttal nélkül végezheti el. Azonban a feladat-visszavételi kell együttműködés a Microsofttal.
- Állítsa vissza egy tárolási pillanatképet, amely nem a legújabb replikált pillanatképet szeretne. Ebben az esetben kell együttműködés a Microsofttal. 

>[!NOTE]
>Az alábbi lépéseket kell hajtható végre a HANA nagy példány egység, amely a vész-Helyreállítási egységet jelöl. 
 
A legújabb replikált tárhelyet pillanatképek visszaállításához hajtsa végre az alábbi lépéseket: 

1. Állítsa le a HANA nem éles példányát az Ön által futtatott HANA-nagy példányok vész helyreállítási egység. Ennek az az oka egy inaktív HANA éles példány előre telepítve.
2. Győződjön meg arról, hogy az eljárások nincsenek SAP HANA futnak. Ezt az ellenőrzést a következő paranccsal: `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`. A kimeneti jelenítsen meg a **hdbdaemon** folyamatok állt le, és nincs más HANA folyamatok futó vagy elindított állapotban.
3. A parancsfájl végrehajtása a vész-Helyreállítási hely HANA nagy példány egységen *azure_hana_dr_failover.pl*. A parancsfájl által kért egy SAP HANA SID állítani. Kérés esetén írja be egy vagy az egyetlen SAP HANA SID-vel replikálta és megőrződik a *HANABackupCustomerDetails.txt* fájl a vész-Helyreállítási hely HANA nagy példány egységben. 

      Ha több SAP HANA-példány feladatátvételt szeretne használni, több alkalommal a parancsfájl futtatásához szükséges. Ha kérelem érkezik, adja meg az SAP HANA SID feladatátvételt, és állítsa vissza. Befejezésekor a parancsfájl a HANA nagy példány egység adott kötet csatlakoztatási pontok listáját tartalmazza. Ez a lista tartalmazza, valamint a visszaállított vész-Helyreállítási köteteket.

4. A visszaállított vész-helyreállítási kötet csatlakoztatási parancsaival Linux operációs rendszer HANA nagy példány egységhez a vész-helyreállítási helyet. 
6. Indítsa el az inaktív SAP HANA-éles példányt.
7. Ha úgy döntött, hogy másolja a tranzakciós napló biztonsági mentési naplók csökkentheti az RPO időtartamát, ezen tranzakciónapló biztonsági mentései egyesítése az újonnan csatlakoztatott vész-Helyreállítási/hana/logbackups directory szeretné. Ne írja felül a meglévő biztonsági másolatok. Újabb biztonsági, amelyek nem lettek replikálva a legutóbbi replikáció tárolási pillanatkép.
8. A pillanatképek a vész-Helyreállítási Azure régióban /hana/shared/PRD kötet replikált kívül egyetlen fájlok is állíthatja. 

A vész-Helyreállítási feladatátvételt is tesztelheti a tényleges replikációs kapcsolat befolyásolása nélkül. Feladatátvételi teszt végrehajtásához kövesse a fenti lépéseket, 1 és 2, és folytassa a következő 3 lépést a.

>[!IMPORTANT]
>Tegye *nem* éles tranzakciók futtatható a példányon, a folyamatot, amely a vész-Helyreállítási helyen létrehozott **a feladatátvétel tesztelésének** rendszerben jelent meg a 3. lépésben a parancsfájllal. Ez a parancs jönnek létre a kapcsolat nem rendelkezik az elsődleges helyhez kötetek. Ennek eredményeképpen vissza az elsődleges helyen a szinkronizálás van *nem* lehetséges. 

A feladatátvételi teszt 3 lépést:

A parancsfájl végrehajtása a vész-Helyreállítási hely HANA nagy példány egységen **azure_hana_test_dr_failover.pl**. A parancsfájl *nem* leállítása a replikációs kapcsolatot az elsődleges hely és a vész-Helyreállítási hely között. Ehelyett ezt a parancsfájlt a vész-Helyreállítási tárolókötetek van Klónozás. Miután a Klónozási folyamat sikeres, a klónozott kötetek vissza a legutóbbi pillanatképének állapotát és majd a vész-Helyreállítási egység csatlakoztatva. A parancsfájl által kért egy SAP HANA SID állítani. Egy vagy az egyetlen típus SAP HANA SID-vel replikálta és megőrződik a *HANABackupCustomerDetails.txt* fájl a vész-Helyreállítási hely HANA nagy példány egységben. 

Ha több SAP HANA-példány teszteléséhez szeretne használni, több alkalommal a parancsfájl futtatásához szükséges. Kérés esetén adja meg a feladatátvételi teszt kívánt példány SAP HANA biztonsági azonosítója. Létrehozása után a parancsfájl a HANA nagy példány egység adott kötet csatlakoztatási pontok listáját tartalmazza. Ez a lista tartalmazza, valamint a klónozott vész-Helyreállítási köteteket.

Folytassa a 4. lépés.

   >[!NOTE]
   >Feladatok átadása a vész-Helyreállítási helyet, hogy néhány adat, amely törölve lett a órával ezelőtt történt, és szüksége van egy korábbi pillanatkép értékűre kell beállítani a vész-Helyreállítási kötetek mentési van szüksége, ha ez az eljárás alkalmazható. 

4. Állítsa le a HANA nem éles példányát az Ön által futtatott HANA-nagy példányok vész helyreállítási egység. Ennek az az oka egy inaktív HANA éles példány előre telepítve.
5. Győződjön meg arról, hogy az eljárások nincsenek SAP HANA futnak. Ezt az ellenőrzést a következő paranccsal: `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`. A kimeneti jelenítsen meg a **hdbdaemon** folyamatok állt le, és nincs más HANA folyamatok futó vagy elindított állapotban.
6. Határozza meg, melyik pillanatfelvétel neve vagy a biztonsági mentési SAP HANA-azonosító szeretné, hogy a vész helyreállítási visszaállítva. A valódi katasztrófa utáni helyreállítás esetben ebben pillanatképét az általában a legújabb pillanatkép. Ha elveszett adatok visszaállítására van szüksége, válasszon ki egy korábbi pillanatkép.
7. Lépjen kapcsolatba az Azure támogatja keresztül kiemelt fontosságú támogatási kérelmet. Kérje meg a visszaállításhoz, hogy pillanatfelvételt (a név és a pillanatkép dátum) vagy a biztonsági mentési HANA-azonosító, a vész-Helyreállítási helyen. Az alapértelmezett érték a műveletek oldalon, hogy visszaállítja az/hana/adatmennyiség csak. A/hana/logbackups kötetek is szeretne használni, ha kifejezetten megállapítják, hogy szeretné. *Ne állítsa vissza a /hana/shared kötet.* Ehelyett kívüli global.ini például meghatározott fájlokat kell kiválasztani a **.snapshot** könyvtár, illetve annak alkönyvtáraiba után újracsatlakoztatása/hana/PRD a megosztott fürtkötet. 

   A műveletek oldalon a következő lépések következnek:

   a. A pillanatképek a termelési kötetről a vész-helyreállítási kötetek replikációját le van állítva. Előfordulhat, hogy a megszakítás már történt az üzemi helyen kimaradás esetén is végre kell hajtania a vész-helyreállítási folyamattal okát.
   
   b. A tárolási pillanatkép-neve, vagy a pillanatkép készítése a biztonsági mentési azonosítójú úgy döntött, hogy a vész-helyreállítási kötetek visszaállítása megtörtént.
   
   c. A visszaállítás után a vész-helyreállítási kötet érhetők el a vész-helyreállítási régióban HANA nagy példány egységeire csatlakoztatni.
      
8. Csatlakoztassa a vész-helyreállítási köteteket a HANA nagy példány egységhez, a vész-helyreállítási helyen. 
9. Indítsa el az inaktív SAP HANA-éles példányt.
10. Ha úgy döntött, hogy másolja a tranzakciós napló biztonsági mentési naplók csökkentheti az RPO időtartamát, ezen tranzakciónapló biztonsági mentései egyesítése az újonnan csatlakoztatott vész-Helyreállítási/hana/logbackups directory szeretné. Ne írja felül a meglévő biztonsági másolatok. Újabb biztonsági, amelyek nem lettek replikálva a legutóbbi replikáció tárolási pillanatkép.
11. A pillanatképek a vész-Helyreállítási Azure régióban /hana/shared/PRD kötet replikált kívül egyetlen fájlok is állíthatja.

A következő feladatütemezési lépéseket foglalja magában az SAP HANA éles példányok a visszaállított tárolási pillanatkép és a tranzakciónapló biztonsági mentései által biztosított helyreállítása:

1. Módosítsa a biztonsági mentési helyét, **/hana/logbackups** SAP HANA Studio használatával.
   ![A VÉSZ-helyreállítási biztonsági mentési helyének módosítása](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

2. SAP HANA vizsgálja át a biztonságimásolat-fájl helyét, és a legutóbbi tranzakciós napló biztonsági mentési visszaállítása javasol. A vizsgálat is igénybe vehet néhány percet, amíg például akkor jelenik meg, a következő képernyő: ![tranzakciónapló biztonsági mentései a VÉSZ-helyreállítási listája](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

3. Módosítsa az alapértelmezett beállításokat:

      - Törölje a jelet **használja a különbözeti biztonsági mentések**.
      - Válassza ki **napló terület inicializálása**.

   ![Az inicializálás napló terület](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

4. Válassza a **Finish** (Befejezés) elemet.

   ![A vész-Helyreállítási visszaállítás befejezése](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

A folyamatban lévő ablakban hasonló látható itt, meg kell jelennie. Ne feledje, hogy a példában a egy vész-helyreállítási visszaállítás egy három csomópontos kibővített SAP HANA-konfiguráció van.

![Visszaállítási folyamat](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Ha a visszaállítási úgy tűnik, hogy időnként a **Befejezés** képernyőn és nem a folyamat képernyője megjelenítése, győződjön meg arról, hogy fut-e a feldolgozó csomópontokon SAP HANA-példányokat. Ha szükséges, indítsa el manuálisan a SAP HANA-példányokat.


### <a name="failback-from-a-dr-to-a-production-site"></a>Feladat-visszavétel a vész-Helyreállítási az éles helyhez
A vész-Helyreállítási újból az éles helyhez sikertelen. Vizsgáljuk meg egy olyan forgatókönyvet, amelyben a feladatátvétel, a vész-helyreállítási helyen történő okozott problémák az éles Azure-régiót, és nem az elveszett adatok helyreállításához szükséges. Ön futnia a SAP termelési számítási feladatok egy ideje a vész-helyreállítási helyen. A munkakörnyezeti helyet problémákat megoldottként visszaadják feladataikat a munkakörnyezeti helyet szeretne. Adatok nem megszakad, mert újra üzembe a munkakörnyezeti helyet a lépés néhány lépéseket és az Azure üzemeltetése csapat az SAP HANA szoros együttműködésben magában foglalja. A műveleti csapata elindítani a munkakörnyezeti helyet a szinkronizálás után a rendszer feloldja a problémák kiváltó Önnek van.

Ez a feladatütemezési lépés a következő:

1. Az Azure üzemeltetése csapat az SAP HANA lekérdezi az eseményindító a vész helyreállítási tároló kötetekről, ami éles állapotát jelzik az éles tároló kötetek szinkronizálását. Ebben az állapotban lévő leállítása a munkakörnyezeti helyet a HANA nagy példány egységet.
2. Az SAP HANA Azure üzemeltetése csapat a replikációs figyeli, és gondoskodik arról, hogy azt kiszűri előtt tájékoztat.
3. A vész-helyreállítási hely az üzemi HANA példány használó alkalmazások leállítása. Ezután hajtsa végre egy HANA tranzakciós napló biztonsági mentését. Ezt követően állítsa le a HANA-példány futtatási a HANA nagy példány egységeken a vész-helyreállítási helyen.
4. Után a HANA-példány futtatási a vész-helyreállítási hely HANA nagy példány egységben van-e állítva, a műveleti csapata manuálisan szinkronizálja a kötetek újra.
5. Az SAP HANA Azure üzemeltetése csapat a munkakörnyezeti helyet a HANA nagy példány egység indul újra, és átadja Önnek. Akkor gondoskodjon arról, hogy az SAP HANA-példány a Leállítás utáni állapotban levő HANA nagy példány egység indításakor.
6. Ugyanazon adatbázis visszaállítási lépések elvégzése korábban feladatátadás a vész-helyreállítási hely hasonló módon.

### <a name="monitor-disaster-recovery-replication"></a>Vész-helyreállítási replikálás

A tároló replikálási folyamat állapotának figyelheti az parancsfájl `azure_hana_replication_status.pl`. A parancsfájlt a kívánt módon működik a vész-helyreállítási helyen futó egységből származó kell futtatni. A parancsfájl használatára, függetlenül attól, hogy replikációs aktív. A parancsfájlt minden HANA nagy példány egységéhez a bérlő a vész-helyreállítási helyen. A rendszerindító kötet kapcsolatos részletek beszerzése érdekében nem használható.

Ez a parancs a parancsprogram hívása:
```
./replication_status.pl <HANA SID>
```

A kimeneti van szerinti bontásban, kötet a következő szakaszokra:  

- Kapcsolat állapota
- Az aktuális replikációs tevékenység
- Replikált legújabb pillanatkép 
- A legújabb pillanatkép mérete
- Aktuális időbeli pillanatképek között (az utolsó befejezett pillanatkép replikáció között, és most)

A hivatkozás állapotát jeleníti meg, mint **aktív** kivéve, ha a helyek közötti kapcsolat nem működik, vagy egy jelenleg folyamatban lévő feladatátvételi esemény. A replikálási műveletek megoldást, hogy minden adatot jelenleg éppen replikált vagy inaktív, vagy más tevékenységek pillanatnyilag folyamatban levő a hivatkozásra. A legutolsó pillanatfelvétel replikált csak meg kell jelennie `snapmirror…`. Ezután megjelenik, a legutolsó pillanatfelvétel méretét. Végül az időbeli jelenik meg. Az időbeli idejét a replikáció befejezése után a ütemezett replikáció jelöli. Késéssel lehet nagyobb, mint egy órával az adatreplikáció, különösen a kezdeti replikálás, annak ellenére, hogy a replikáció. Az időbeli folyamatosan nő, amíg a folyamatban lévő replikáció befejeződik.

A következő egy példa a kimenetre:

```
hana_data_hm3_mnt00002_t020_dp
-------------------------------------------------
Link Status: Broken-Off
Current Replication Activity: Idle
Latest Snapshot Replicated: snapmirror.c169b434-75c0-11e6-9903-00a098a13ceb_2154095454.2017-04-21_051515
Size of Latest Snapshot Replicated: 244KB
Current Lag Time between snapshots: -   ***Less than 90 minutes is acceptable***
```












