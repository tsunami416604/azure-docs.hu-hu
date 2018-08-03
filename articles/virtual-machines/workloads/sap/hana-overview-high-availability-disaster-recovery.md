---
title: Az Azure-ban (nagyméretű példányok) SAP HANA magas rendelkezésre állás és vészhelyreállítás helyreállítási |} A Microsoft Docs
description: Magas rendelkezésre állást és az Azure-ban (nagyméretű példányok) SAP Hana vész-helyreállítási terv létrehozása
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
ms.openlocfilehash: 554991c7c0f11a095a11ae24dbb693a1a3ba50fd
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39430120"
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>SAP HANA nagyméretű példányok magas rendelkezésre állás és vészhelyreállítás recovery az Azure-ban 

>[!IMPORTANT]
>Ez a dokumentáció a SAP HANA-felügyelet dokumentációja és az SAP-megjegyzések helyettesítése. Valószínű, hogy az olvasó rendelkezik egy szilárd ismeretekkel és szaktudását a SAP HANA-felügyelet és a műveletek, különösen a biztonsági mentés, visszaállítás, magas rendelkezésre állású és vész-helyreállítási témaköröket. Ebben a dokumentációban beállítást mutató képernyőképek a SAP HANA Studio jelennek meg. Tartalom struktúra és az SAP-felügyeleti eszközök és az eszközök magukat a képernyők jellege módosulhat kiadásban a kiadási SAP HANA-ból.

Fontos lépéseket és folyamatokat a környezetben, és a HANA-verziók és kiadások tett gyakorolják. Bizonyos folyamatok, az ebben a dokumentációban leírt általános végiglépkedéshez egyszerűsítettek, és nem jelentenek a részletes lépéseket a végleges művelet szakkönyveket felhasználhatók. Ha azt szeretné, a konfigurációk művelet szakkönyveket létrehozásához, teszteléséhez és gyakorolja a folyamatok és az adott konfigurációkhoz kapcsolódó folyamatok dokumentálása szüksége. 


Magas rendelkezésre állás és vészhelyreállítás (DR) az alapvető fontosságú SAP HANA az Azure-ban (nagyméretű példányok) kiszolgálón futó rendkívül fontos jellemzőit. Fontos, SAP, a rendszerintegrátor vagy a Microsoft megfelelően tervezhet és a megfelelő magas rendelkezésre állású és vész-helyreállítási stratégia megvalósításához dolgozhat. Fontos is érdemes figyelembe venni a helyreállításipont-célkitűzés (RPO) és a helyreállítási időre vonatkozó célkitűzést, amelyek adott környezetre jellemző.

A Microsoft támogatja a HANA nagyméretű példányok néhány SAP HANA magas rendelkezésre állású funkcióit. Ilyen képességek:

- **Tárreplikáció**: minden adat replikálása egy másik HANA nagyméretű szolgáltatáspéldányban egy másik Azure-régióban a storage rendszer riasztásészlelési képességére. SAP HANA működése független ezt a módszert. Ez a funkció az alapértelmezett vész helyreállítási mechanizmusa el a HANA nagyméretű példányok.
- **HANA-rendszerreplikálást**: A [az összes adat az SAP Hana-beli replikációs](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) egy különálló SAP HANA rendszerbe. A helyreállítási időre vonatkozó célkitűzés rendszeres időközönként adatok a replikáció révén minimálisra csökken. SAP HANA aszinkron, a szinkron a memóriában, és szinkron módot támogat. A szinkron módban csak szolgál SAP HANA rendszereit, amelyek az ugyanabban az adatközpontban vagy kevesebb, mint 100 km-re vannak. A jelenlegi kialakítás nagyméretű HANA-példány stampgyűjteményt, a HANA-rendszerreplikálást magas rendelkezésre állás érdekében csak egy régión belül használható. HANA-rendszerreplikálást szükséges külső fordított proxy vagy útválasztási összetevő vész-helyreállítási konfigurációk be egy másik Azure-régióban. 
- **Gazdagép automatikus feladatátvétele**: SAP Hana, amely a HANA-rendszerreplikálást helyett egy helyi kiesésekor helyszíni helyreállító megoldással. A fő csomópont elérhetetlenné válik, ha konfigurál egy vagy több készenléti SAP HANA-csomópont kibővített módban, és az SAP HANA automatikusan átadja a feladatokat a készenléti csomópont.

SAP HANA az Azure-ban (nagyméretű példányok) négy geopolitikai területeken (Egyesült Államok, Ausztrália, Európában és japán) két Azure-régióban érhető el. Egy geopolitikai területen két olyan régióban, amelyek nagyméretű HANA-példány stampek külön dedikált hálózati Kapcsolatcsoportok kapcsolódnak. Ezek használhatók a pillanatképek tárolási replikálásához vész-helyreállítási mód megadásának. A replikáció nem jön létre alapértelmezés szerint, de a megfelelő az ügyfelek, akik order vészhelyreállítási funkciók. Tárreplikáció szolgáltatás nagyméretű HANA-példányok pillanatképeit tároló használata függ. Nem alkalmas a Vészhelyreállítási régióban, amely egy másik geopolitikai területén, egy Azure-régió kiválasztásához. 

Az alábbi táblázat a jelenleg támogatott magas rendelkezésre állás és vészhelyreállítás helyreállítási módok és kombinációit:

| Támogatja a HANA nagyméretű példányok forgatókönyv | Magas rendelkezésre állási lehetőségek | Vész-helyreállítási lehetőség | Megjegyzések |
| --- | --- | --- | --- |
| Egyetlen csomópont | Nem érhető el. | Dedikált DR-telepítés.<br /> Multipurpose DR-telepítés. | |
| Gazdagép automatikus feladatátvétele: horizontális felskálázás (a vagy készenléti nélkül)<br /> például 1 + 1 | A készenléti véve az aktív szerepkör-lehetséges.<br /> HANA azt szabályozza, hogy a szerepkör-kapcsoló. | Dedikált DR-telepítés.<br /> Multipurpose DR-telepítés.<br /> DR-szinkronizálás segítségével tárreplikációt. | HANA kötet csoportok csatlakoztatott összes csomópontjának.<br /> DR-helynek kell lennie a csomópontok azonos számú. |
| HANA-rendszerreplikálást | Elsődleges vagy másodlagos telepítés lehetséges.<br /> Másodlagos abban az esetben feladatátvétel elsődleges szerepkörre helyezi át.<br /> HANA-rendszerreplikálást és az operációs rendszer szabályozza a feladatátvételt. | Dedikált DR-telepítés.<br /> Multipurpose DR-telepítés.<br /> DR-szinkronizálás segítségével tárreplikációt.<br /> A HANA-rendszerreplikálást DR még nem lehet külső gyártóktól származó összetevőket nélkül. | Minden egyes csomópont csatolt kötetek külön készlete.<br /> Az élesben használt helyet a másodlagos másodpéldány csak lemezkötetek a DR-helyre replikálja.<br /> Egy olyan kötetek, szükséges a DR-helyen. | 

Egy dedikált Vészhelyreállítási telepítő nem, ahol a DR-webhely a nagyméretű HANA-példány egység nem használatos a futó számítási feladatok vagy nem éles rendszerek. Az egység passzív, és csak akkor, ha a vészhelyreállítási feladatátvétel végrehajtása üzemel. Ez a beállítás viszont nem sok ügyfél előnyben részesített választást.

Tekintse meg [HLI támogatott forgatókönyvek](hana-supported-scenario.md) tárolási elrendezést, és ethernet részletekkel az architektúrához.

> [!NOTE]
> [Az SAP HANA MCOD központi telepítések](https://launchpad.support.sap.com/#/notes/1681092) (egy egység több HANA példányok) a táblázatban felsorolt forgatókönyvek munkahelyi átfedő a magas rendelkezésre ÁLLÁS és Vészhelyreállítás módszerek. Kivétel ez alól egy támasztja alapuló automatikus feladatátvételi fürtöt a HANA-Rendszerreplikálást használatát. Ilyen esetben csak az egység egy HANA-példány támogatja. A [SAP HANA MDC](https://launchpad.support.sap.com/#/notes/2096000) telepítések esetén csak nem a storage-alapú magas rendelkezésre ÁLLÁS és Vészhelyreállítás metódusai Ha egynél több bérlőhöz van telepítve. Egyetlen bérlő számára üzembe helyezett, a felsorolt összes módszerek érvényesek.  

A többcélú DR-telepítés, ahol a nagyméretű HANA-példány egység DR a helyszínen fut, nem éles számítási feladatok. Vészhelyzet esetén állítsa le a nem éles rendszer csatlakoztassa a storage replikált (új) kötetet készletek, és indítsa el a termelési HANA-példány. A legtöbb ügyfeleink, akik a nagyméretű HANA-példány vészhelyreállítási funkciók használja ezt a konfigurációt. 


További információ az SAP HANA magas rendelkezésre állás a következő cikkekben SAP találja meg: 

- [Az SAP HANA magas rendelkezésre állású tanulmány](http://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [Az SAP HANA-felügyeleti útmutató](http://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [Az SAP HANA-Rendszerreplikálást SAP HANA Academy-videó](http://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [SAP támogatási Megjegyzés #1999880 – az SAP HANA Rendszerreplikáció – gyakori kérdések](https://apps.support.sap.com/sap/support/knowledge/preview/en/1999880)
- [SAP támogatási Megjegyzés #2165547 – SAP HANA biztonsági mentése és visszaállítása az SAP HANA rendszer replikációs környezeten belül](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP támogatási Megjegyzés #1984882 – SAP HANA-Rendszerreplikálást segítségével a minimális/üzemszünet hardver Exchange-hez](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>A hálózatokkal kapcsolatos szempontok vész-helyreállítási a nagyméretű HANA-példányok

Nagyméretű HANA-példányokhoz vész-helyreállítási funkciójának előnyeit, hálózati kapcsolat a két Azure-régiók és tervezési kell. Az Azure ExpressRoute-kapcsolatcsoport kapcsolatok a helyszíni rendszerből a fő Azure-régióban, és a egy másik kapcsolatcsoport kapcsolat a helyszíni és a vészhelyreállítási régióban van szüksége. Ezt a mértéket egy olyan helyzetet, ahol probléma van egy Azure-régióban, beleértve a Microsoft Enterprise peremhálózati útválasztóhoz (MSEE) helyét ismerteti.

Egy második mérték csatlakoztathat, minden Azure virtuális hálózatok, amelyek az Azure-ban (nagyméretű példányok) SAP Hana-val csatlakoznak egy adott régióban az ExpressRoute-kapcsolatcsoport, amely kapcsolódik a HANA nagyméretű példányok más régióban található. Ez *közötti kapcsolódás*, egy Azure-on futó szolgáltatások régió 1 virtuális hálózat kapcsolódhat nagyméretű HANA-példány egységek régió 2 és fordítva. Ez a mérték címek egy esetet, amelyben az MSEE-helyek csak az egyik, amely kapcsolódik a helyszíni hellyel, az Azure-ral offline állapotba kerül.

A következő ábra mutatja be egy rugalmas konfigurációs vész-helyreállítási esetek:

![Vész-helyreállítási optimális konfigurációt](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-with-hana-large-instances-storage-replication-for-disaster-recovery"></a>A nagyméretű HANA-példányokhoz tárreplikáció vész-helyreállítási egyéb követelmények

A vészhelyreállítás telepítője a HANA nagyméretű példányok az előző követelmények mellett a következőket kell tennie:

- SAP HANA ORDER akkora, mint az éles üzemben futó SKU-k az Azure-ban (nagyméretű példányok) termékváltozatokban, és telepítse őket a vészhelyreállítási régióban lévő. Az aktuális felhasználói telepítések ezek a példányok nem éles HANA-példányok futtatásához használt. Ezek a konfigurációk nevezzük *többcélú DR mátrixok*.   
- A rendelés a DR webhelyen további tárterületet az SAP HANA-szeretné helyreállítani a vész-helyreállítási helyként az Azure-ban (nagyméretű példányok) SKU-k mindegyikéhez. További tárterület vásárlása lehetővé teszi a tárolási köteteket foglal le. A köteteket, amelyek a cél az éles Azure-régió, a vészhelyreállítást az Azure-régióban a storage replikációs foglalhat le.
- Abban az esetben, ahol elsődleges HSR-telepítő rendelkezik, és, konfigurálja a storage-alapú replikálás a DR-helyre, akkor kell tárterület-vásárlási lehetőségekről a DR-helyen, mind az elsődleges és másodlagos csomópontot adatok replikálását a DR-helyre.

 

## <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás

Egyik legfontosabb szempontja az operatív adatbázis a katasztrofális események elleni védelem érdekében. Ezek az események oka lehet bármit az egyszerű felhasználói hibáinak természeti katasztrófák.

Egy adatbázis biztonsági mentéséhez, lehetővé teszi az visszaállítás bármely időpontra időben (például valaki a kritikus fontosságú adatok törlése előtt), lehetővé teszi, hogy a visszaállítási állapotba, amely a lehető legközelebb a módját a megszakítások időtartamát előtt volt.

A legjobb eredmények elérése érdekében kétféle típusú biztonsági mentéseket kell végrehajtani:

- Adatbázis-biztonsági mentések: teljes, a növekményes és a különbözeti biztonsági mentések
- Tranzakciónapló biztonsági mentései

Mellett az alkalmazás szintjén végrehajtott biztonsági mentések teljes adatbázis-biztonsági másolatok tárolási pillanatképekkel hajthat végre. A pillanatképek tárolási ne cserélje le a tranzakciónaplók biztonsági mentését. Tranzakciós naplók biztonsági mentését is fontos, az adatbázis visszaállítása egy bizonyos ponton vagy a már véglegesített tranzakciók naplóinak üres marad. A pillanatképek tárolási azonban gyorsítható recovery azáltal, hogy gyorsan az adatbázishoz egy visszaállítási-továbbító képe. 

SAP HANA az Azure-ban (nagyméretű példányok) két biztonsági mentési és visszaállítási lehetőséget kínál:

- Saját munka (DIY). Miután díjkalkulációt biztosít annak érdekében, hogy nincs elegendő lemezterület, hajtsa végre teljes adatbázis és a Naplók biztonsági mentését a következő lemez biztonsági mentési módszerek egyikének használatával. Közvetlenül, a nagyméretű HANA-példány egységeket, illetve a hálózati fájl megosztások (NFS), amely egy Azure virtuális gépen (VM) beállítása csatlakoztatott kötetek készíthető. Az utóbbi esetben az ügyfelek Linux rendszerű virtuális gép az Azure-ban, Azure Storage csatlakoztatása a virtuális gép, és megoszthatja a tárterületet a virtuális gép egy konfigurált NFS-kiszolgáló. Ellen, amelyhez közvetlenül, nagyméretű HANA-példány egységek kötetek a biztonsági mentést hajt végre, ha meg kell másolnia a biztonsági másolatok az Azure storage-fiókba (Miután beállította az Azure virtuális Gépekhez, amelyek az Azure Storage alapuló NFS-megosztások exportálása). Egy Azure backup-tárolóba vagy a ritka elérésű az Azure storage is használható. 

   Egy másik lehetőség, hogy egy harmadik fél adatvédelmi eszköz, ahol a biztonsági után másolja őket egy Azure storage-fiókot. A házi KÉSZÍTÉSŰ biztonsági mentési lehetőséget is szükség lehet a megfelelőségi és naplózási célokra hosszabb ideig tárolni kívánt adatokat. Minden esetben a biztonsági mentések másol át egy virtuális gép és az Azure Storage jelölt NFS-megosztásokat.

- Infrastruktúra biztonsági mentését és visszaállítását funkciót. Is használja a biztonsági mentés és visszaállítás, amely tartalmazza az alapul szolgáló infrastruktúra SAP Hana az Azure-ban (nagyméretű példányok). Ez a beállítás teljesíti a biztonsági mentések és a gyors visszaállítás érdekében van szükség. Ez a szakasz a többi címek érhető el a HANA nagyméretű példányok biztonsági mentési és helyreállítási funkciókat. Ez a szakasz is magában foglalja a kapcsolat biztonsági mentés és visszaállítás kell a vész helyreállítási funkciók nagyméretű HANA-példányok által kínált.

>   [!NOTE]
>   A pillanatkép-technológia, amely az alapul szolgáló infrastruktúra HANA nagyméretű példányok által használt maga az SAP HANA-pillanatképeket. Ezen a ponton az SAP HANA-pillanatképek nem működik az SAP HANA database több-bérlős tárolók több bérlői együtt. Ha csak egyetlen bérlő üzembe lett helyezve, az SAP HANA-pillanatképek működnek, és ez a módszer is használható.

### <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Storage snapshots SAP Hana használatával az Azure-ban (nagyméretű példányok)

A tároló-infrastruktúra az alapul szolgáló SAP HANA az Azure-ban (nagyméretű példányok) támogatja a pillanatképeket tároló kötetek. Biztonsági mentési és a kötetek visszaállítása támogatott, a következő szempontokat:

- Helyett a teljes adatbázis biztonsági mentése a tárolási kötet-pillanatképek rendszeres időközönként kerül.
- Amikor elindítása egy pillanatkép /hana/data és /hana/shared (tartalmazza a /usr/sap) a kötetek, a pillanatkép-technológia egy SAP HANA hajtja végre a rendszer a storage-pillanatkép pillanatkép indítja el. Az SAP HANA pillanatképe végleges log visszaállítását a telepítő pontja, a storage-pillanatkép helyreállítása után. A sikeres HANA pillanatképe kell egy aktív HANA-példány.  HSR esetben tároló pillanatképe nem támogatott az aktuális másodlagos csomópontra, ahol a HANA pillanatképe nem hajtható végre.
- Miután a tároló pillanatképe sikeresen végre lett hajtva, az SAP HANA pillanatképe törlődik.
- Tranzakciónapló biztonsági mentései gyakran kerül, és a /hana/logbackups kötetet, vagy az Azure-ban tárolja. /Hana/logbackups tartalmazó kötetet, a tranzakciónapló biztonsági mentései pillanatképének külön-külön is indíthat. Ebben az esetben nem kell végrehajtani egy HANA pillanatképe.
- Ha vissza kell állítania egy adatbázis egy bizonyos ponton időben, kérhet, hogy a Microsoft Azure-támogatást (egy éles kimaradásáról) vagy az SAP HANA az Azure Service Management egy bizonyos tárolási pillanatkép visszaállítása. Ilyen például, a védőfal rendszer tervezett visszaállítás az eredeti állapotba.
- Az SAP HANA pillanatképe, amely megtalálható a tároló pillanatképe az egy eltolási pont, a tranzakciónapló biztonsági mentéseivel, amelyek végrehajtása és a tárolt, miután a tároló pillanatkép alkalmazása.
- Állítsa vissza az adatbázist vissza egy bizonyos ponton a időben megnyílik a tranzakciós napló biztonsági mentése.

Kötetek három osztály célzó tárolási pillanatképekkel végezheti el:

- Egy kombinált pillanatkép/hana/adatok, valamint /hana/shared (a/usr/sap tartalmazza). A pillanatkép, a storage-pillanatkép előkészítése az SAP HANA-pillanatkép létrehozása van szükség. Az SAP HANA pillanatképe gondoskodik arról, hogy az adatbázis egy tárolási szempontból konzisztens állapotban van-e. A működőképes és, hogy a visszaállításhoz, amely egy pont beállítása
- Egy külön pillanatkép/hana/logbackups keresztül.
- Az operációs rendszer partíciójánál.

A legújabb pillanatkép parancsfájlok és dokumentáció [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). 

### <a name="storage-snapshot-considerations"></a>Storage-pillanatkép szempontok

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

### <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Önkiszolgáló storage snapshots használatával előfeltételei

Győződjön meg arról, hogy a pillanatkép-parancsfájl végrehajtása sikeresen megtörtént, ellenőrizze, hogy a Perl telepítve van a nagyméretű HANA-példányokhoz kiszolgálón a Linux operációs rendszeren. Perl előre telepítve áll rendelkezésre a nagyméretű HANA-példány található. Ellenőrizze a Perl-verziót, használja a következő parancsot:

`perl -v`

![Másolja a nyilvános kulcsot a parancs futtatása](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


### <a name="set-up-storage-snapshots"></a>A pillanatképek tárolási beállítása

Storage-pillanatképeket HANA nagyméretű példányok beállításához, kövesse az alábbi lépéseket:
1. Győződjön meg arról, hogy a Perl telepítve van a nagyméretű HANA-példányokhoz kiszolgálón a Linux operációs rendszeren.
1. Módosítsa az/etc/ssh/ssh\_adja hozzá a sort. konfiguráció _Mac számítógépek hmac-sha1_.
1. SAP HANA biztonsági mentési fiók létrehozása a fő csomópont minden SAP HANA-példány futtatja, ha van ilyen.
1. Az SAP HANA HDB ügyfél telepítése a SAP HANA nagyméretű példányok összes kiszolgálójára.
1. Az első SAP HANA nagyméretű példányok kiszolgáló minden egyes régió hozzon létre egy nyilvános kulcs, amely hozzáfér a mögöttes tároló-infrastruktúra, amely szabályozza a pillanatkép létrehozása.
1. Másolja a parancsfájlok és a konfigurációs fájl [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts) helyére **hdbsql** az SAP HANA telepítése.
1. Módosítsa a *HANABackupDetails.txt* fájlt a megfelelő ügyfél-specifikációk szükség szerint.

A legújabb pillanatkép parancsfájlok és dokumentáció [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). 

### <a name="consideration-for-mcod-scenarios"></a>MCOD forgatókönyvekhez szempontok
Ha egy [MCOD forgatókönyv](https://launchpad.support.sap.com/#/notes/1681092) egy nagyméretű HANA-példány egység több SAP HANA példányok, az üzembe helyezett minden egyes, az SAP HANA-példányok külön tárolókötet rendelkezik. Az önkiszolgáló pillanatkép-automatizálás jelenlegi verzióban minden HANA-példány rendszer azonosítója (SID) külön pillanatképek nem kezdeményezhető. A funkciókat biztosít a konfigurációs fájlban (lásd a cikk későbbi részében) a kiszolgáló regisztrált SAP HANA-példányok keresése, és végrehajtja a köteteket a egységen regisztrált összes példány egyidejű pillanatkép.
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>1. lépés: Az SAP HANA HDB ügyfél telepítése

A Linux operációs rendszer telepítve van, az SAP HANA az Azure-ban (nagyméretű példányok) a mappákat és az SAP HANA tárolási pillanatképekkel biztonsági mentési és vész-helyreállítási céllal végrehajtásához szükséges parancsfájlokat tartalmazza. Ellenőrizze az újabb kiadásokban [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). A legújabb kiadás a szkriptek verziója 3.x. Előfordulhat, hogy a különböző parancsfájlok fő azonos kiadási belül különböző kisebb kiadások.

>[!IMPORTANT]
>A verzió a 2.1-es verzió áthelyezésekor 3.x a parancsfájlok, vegye figyelembe, hogy a konfigurációs fájlt, és bizonyos szintaxisok szerkezete megváltozott. Tekintse meg az adott szakaszok a felhasználásával. 

A feladata az SAP HANA HDB ügyfél telepítése akkor, amikor telepíti az SAP HANA nagyméretű HANA-példány egységek.

### <a name="step-2-change-the-etcsshsshconfig"></a>2. lépés: Módosítsa az/etc/ssh/ssh\_config

Változás `/etc/ssh/ssh_config` hozzáadásával a _Mac számítógépek hmac-sha1_ sor itt látható módon:
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

### <a name="step-3-create-a-public-key"></a>3. lépés: Hozzon létre egy nyilvános kulcs

Engedélyezi a hozzáférést a storage pillanatkép felületek a nagyméretű HANA-példány bérlő, meg kell létrehoznia egy bejelentkezési eljárást keresztül a nyilvános kulcs. Az első SAP HANA az Azure-ban (nagyméretű példányok) kiszolgáló a saját bérlőjében hozzon létre a tároló-infrastruktúra eléréséhez használt nyilvános kulcsot. A nyilvános kulcs biztosítja, hogy jelentkezzen be a storage-pillanatkép felületek nem szükséges jelszó. Nyilvános kulcs létrehozása is jelenti, hogy nem kell fenntartani a jelszavas hitelesítő adatokat. Az SAP HANA nagyméretű példányok kiszolgálón Linux hajtsa végre a következő parancsot a nyilvános kulcs létrehozásához:
```
  ssh-keygen –t dsa –b 1024
```
Az új hely **_/root/.ssh/id\_dsa.pub**. Ne adjon meg egy tényleges jelszót, vagy pedig adja meg a jelszavát minden egyes bejelentkezés alkalmával kell. Ehelyett válassza **Enter** kétszer, távolítsa el a "jelszó" követelmény a bejelentkezéshez.

Győződjön meg arról, hogy a nyilvános kulcs javította módosítása a mappákban elvárt **/root/.ssh/** majd végrehajtása és a `ls` parancsot. Ha a kulcs található, másolja azt a következő parancs futtatásával:

![Másolja a nyilvános kulcsot a parancs futtatása](./media/hana-overview-high-availability-disaster-recovery/image2-public-key.png)

Ezen a ponton lépjen kapcsolatba az SAP HANA az Azure Service Management szolgáltatáshoz, és adja meg a nyilvános kulcsot. A szolgáltatás képviselő a nyilvános kulcs használatával regisztrálja a mögöttes tároló-infrastruktúra, amely van faragottnak nagyméretű HANA-példány-bérlője számára.

### <a name="step-4-create-an-sap-hana-user-account"></a>4. lépés: Hozzon létre egy SAP HANA-felhasználói fiókot

SAP HANA-pillanatképek létrehozásának kezdeményezni, hozzon létre egy felhasználói fiókot, amellyel a storage-pillanatkép parancsfájlok SAP HANA-ban kell. Hozzon létre egy SAP HANA-felhasználói fiókot, az SAP HANA Studio erre a célra. A felhasználónak kell létrehozni a SYSTEMDB alatt, és nem vonatkozik a SID-adatbázis MDC számára. Egyetlen tároló a környezetben a felhasználó beállítva a bérlői adatbázis alapján. Ezt a fiókot a következő jogosultságokkal kell rendelkeznie: **biztonsági mentési rendszergazda** és **katalógus olvasási**. Ebben a példában a felhasználónév az **SCADMIN**. A felhasználói fiók nevét, HANA Studio létrehozott a kis-és nagybetűket. Ügyeljen arra, hogy válassza ki, hogy **nem** megkövetelő a felhasználó számára a következő bejelentkezéskor a jelszavának módosítása.

![A HANA Studio felhasználó létrehozása](./media/hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

Ha több SAP HANA-példányok egy egységen a MCOD központi telepítéseket használ, ismételje meg ezt a lépést minden SAP HANA-példányt szeretné.

### <a name="step-5-authorize-the-sap-hana-user-account"></a>5. lépés: Engedélyezze az SAP HANA-felhasználói fiók

Ebben a lépésben is engedélyezni szeretné a létrehozott, az SAP HANA-felhasználói fiókot, hogy a parancsfájlok nem kell elküldeni a jelszavak futásidőben. Az SAP HANA parancs `hdbuserstore` lehetővé teszi egy felhasználó egy vagy több SAP HANA-csomóponton tárolt SAP HANA kulcs létrehozását. A felhasználói kulcs lehetővé teszi, hogy a felhasználói hozzáférést az SAP HANA a parancsfájl-kezelési folyamaton belül a jelszavak kezelése nélkül. A parancsfájl-kezelési folyamat Ez a cikk későbbi részében olvashat.

>[!IMPORTANT]
>A következő parancsot a felhasználói csoportban a parancsfájlok végrehajtását bevezetését tervezzük. Ellenkező esetben a parancsfájl nem működik megfelelően.

Adja meg a `hdbuserstore` paranccsal a következőképpen:

**A telepítő MDC HANA**
```
hdbuserstore set <key> <host>:<3[instance]15> <user> <password>
```

**A telepítő MDC HANA**
```
hdbuserstore set <key> <host>:<3[instance]13> <user> <password>
```

A következő példában a felhasználó nem **SCADMIN01**, az állomásnév **lhanad01**, és a példány szám **01**:
```
hdbuserstore set SCADMIN01 lhanad01:30115 <backup username> <password>
```
Ha egy HANA MCOD üzembe helyezés több SAP HANA-példányok egy egységen használja, ismételje meg a minden SAP HANA-példány és a kapcsolódó biztonsági mentési felhasználó a egységen szeretné.

Ha egy SAP HANA kibővített konfigurációs van, kezelheti az összes parancsfájl-kezelési egy kiszolgálóról szeretné. Ebben a példában az SAP HANA-kulcs **SCADMIN01** módosítani kell úgy, hogy jeleníti meg, melyik gazdagépcsoportok kapcsolódik a kulcsot minden állomás számára. Módosítani kell az SAP HANA biztonsági mentési fiók a HANA-adatbázis a példányok számát. A kulcs a gazdagépen, amelyhez hozzá van rendelve, és rendelkeznie kell a biztonsági mentési felhasználó kibővített konfigurációkhoz hozzáférési jogosultsága ahhoz, hogy az SAP HANA-példányok rendszergazdai jogosultsággal kell rendelkeznie. Feltéve, hogy a három horizontális felskálázás csomóponttal rendelkezik a nevek **lhanad01**, **lhanad02**, és **lhanad03**, parancsok sorozatát néz ki:

```
hdbuserstore set SCADMIN01 lhanad01:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad02:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad03:30115 SCADMIN <password>
```

### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>6. lépés: A pillanatkép-parancsfájlok Get, a pillanatképek konfigurálása és a konfiguráció és a kapcsolat tesztelése

Töltse le a legújabb verzióját a parancsfájlok [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). Másolja a letöltött parancsfájlok és a szöveges fájlt a munkakönyvtárban történő **hdbsql**. Aktuális HANA-példányok esetén ebben a könyvtárban van, a következő formátumban: /hana/shared/D01/exe/linuxx86\_64/hdb. 
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

Perl-parancsfájlokat esetén: 

- Soha ne módosítsa a parancsfájlok, kivéve, ha utasításai szerint a Microsoft Operations.
- Ha a rendszer kéri, a parancsfájl vagy egy paraméterfájl módosítására, mindig használja a Linux szövegszerkesztőben, például a "vi", és nem egy Windows-szerkesztő, például a Jegyzettömb. Egy Windows-szerkesztő használatával, előfordulhat, hogy sérült a fájl formátuma.
- Mindig a legújabb parancsprogramokkal. Letöltheti a legújabb verzióra a Githubról.
- A fekvő tájolású parancsfájlok ugyanazt a verzióját használja.
- Tesztelje a parancsfájlokat és kaphat okoz gondot a szükséges paramétereket és a szkript kimenetének előtt közvetlenül az éles rendszer használják őket.
- A kiszolgáló kiépítette a Microsoft Operations a csatlakoztatási pont nevét ne módosítsa. Ezek a szkriptek ezek szabványos csatlakoztatási pontot érhetők el a sikeres végrehajtás támaszkodnak.


A különböző parancsprogramokat és fájlokat célja a következőképpen történik:

- **Azure\_hana\_backup.pl**: Ez a szkript a Linux Cron-ütemezését segédprogrammal van ütemezve, hogy a pillanatképek tárolási a HANA-adatok és megosztott kötetek, a/hana/logbackups köteten, vagy az operációs rendszer.
- **Azure\_hana\_replikációs\_status.pl**: Ez a szkript biztosítja az alapvető adatokat a replikációs állapot a munkakörnyezeti helyet a vész-helyreállítási webhelyként körül. Győződjön meg arról, hogy a replikáció folyik, és az elemek méretének összege mutatja, hogy a parancsfájl figyelők replikálva. Is nyújt útmutatást, ha túl sokáig tart a replikációt, vagy ha a hivatkozás nem működik.
- **Azure\_hana\_pillanatkép\_details.pl**: Ez a szkript az összes pillanatképet, köteteként halad, a környezetben meglévő kapcsolatos alapvető tudnivalókat listáját tartalmazza. Ez a szkript futtatható, az elsődleges kiszolgálón vagy a vész-helyreállítási helyet az kiszolgáló található. A szkript a következő adatokat, és minden olyan kötetre, amely tartalmazza a pillanatképek bontásban tartalmazza:
   * Teljes pillanatképek a kötet mérete
   * Az adott köteten lévő minden egyes pillanatkép a következő adatokat: 
      - Pillanatkép neve 
      - Létrehozás időpontja 
      - Pillanatkép mérete
      - A pillanatkép gyakorisága
      - Ha szükséges, hogy a pillanatkép társított HANA biztonsági mentés azonosítója
- **Azure\_hana\_pillanatkép\_delete.pl**: Ez a szkript törli a storage pillanatkép vagy egy pillanatkép. A SAP HANA biztonsági másolat azonosítója, HANA Studio formában, vagy a pillanatkép nevét is használhatja. A biztonsági másolat azonosítója jelenleg csak vannak kötve, a pillanatképek a HANA-adatok/log/megosztott kötetek létrehozása. Ellenkező esetben a pillanatkép-azonosító is meg kell adni, ha azt kívánja összes pillanatképet, amelyek megfelelnek a megadott pillanatkép azonosítója.  
- **testHANAConnection.pl**: Ez a szkript létesíthető kapcsolat az SAP HANA-példány és a storage-pillanatképek beállításához szükséges.
- **testStorageSnapshotConnection.pl**: Ez a szkript két célja van. Első lépésként azt biztosítja, hogy futtatja a parancsfájlokat, a nagyméretű HANA-példány egység hozzáférés a tárolási hozzárendelt virtuális gépet, és a storage-pillanatkép felületén, a nagyméretű HANA-példányokhoz. A második célja a HANA-példány teszteli egy ideiglenes pillanatképének létrehozása. Ez a szkript egy kiszolgálón, győződjön meg arról, hogy a biztonsági mentés parancsprogramjai várt módon működik-e minden HANA-példány fusson.
- **removeTestStorageSnapshot.pl**: Ez a szkript törli a létrehozott szkripttel teszt pillanatképének **testStorageSnapshotConnection.pl**.
- **Azure\_hana\_dr\_failover.pl**: Ez a szkript DR feladatátvételét kezdeményezi egy másik régióba. A parancsfájl kell a nagyméretű HANA-példány egységen a Vészhelyreállítás régióban található, vagy a egység szeretné átadja a feladatokat. Ez a szkript a másodlagos oldalának elsődleges oldaláról storage replikálását, visszaállítja a legutóbbi pillanatképet a DR-köteteken, és a DR akkor a csatlakozási biztosít köteteket.
- **Azure\_hana\_tesztelése\_dr\_failover.pl**: Ez a szkript a DR-helyre egy feladatátvételi tesztet hajt végre. A végrehajtási ellentétben a azure_hana_dr_failover.pl parancsfájl nem szakítja meg a tárreplikáció, az elsődleges, másodlagos. Ehelyett a replikált tároló kötetek klónok jönnek létre a DR oldalra, és a csatlakozási pontok le a klónozott kötetek találhatók. 
- **HANABackupCustomerDetails.txt**: Ez a fájl egy módosíthatóvá konfigurációs fájlt, amely alkalmazkodni az SAP HANA konfigurációját módosítani kell. A *HANABackupCustomerDetails.txt* a vezérlő és a konfigurációs fájlt a parancsprogramot, amelynek a tárolási pillanatképek számára. Állítsa be a fájlt a célokra és a telepítőt. Kap a **Storage biztonsági mentés neve** és a **tárolási IP-cím** az SAP HANA az Azure Service Management-példányokat telepítésekor. Nem módosíthatja a feladatütemezési, rendezés, vagy bármely, a fájlban a változók térköz. Ha így tesz, a parancsfájlok nem működnek megfelelően. Megjelenhet az IP-címét a vertikális felskálázás vagy a fő csomópontot (ha horizontális felskálázás) SAP HANA az Azure Service Management-ból. Azt is tudnia a HANA-példány szám, amelyet kap az SAP HANA telepítése során. Most szüksége egy biztonsági mentési név a konfigurációs fájl hozzáadásához.

Vertikális vagy horizontális felskálázás központi telepítés a konfigurációs fájl módon jelenik meg az alábbi példában a kiszolgáló nevét a nagyméretű HANA-példány és a kiszolgáló IP-cím kitöltése után. Töltse ki az összes szükséges mezőket minden SAP HANA biztonsági azonosító biztonsági mentése vagy helyreállítása.

Sorok tagjai, amelyek nem szeretne biztonsági mentése egy ideig a "#" elé egy kötelező mező hozzáadásával is megjegyzésbe. Akkor is nem kell megadnia minden SAP HANA-példányok egy kiszolgálón található, ha nem kell biztonsági másolatot készíteni, vagy adott esetben helyreállítását. A formátumot kell tartani az összes mezőre, vagy az összes parancsfájl throw hibaüzenetet, és a parancsfájl befejeződik. Minden olyan SID információ részletek az utolsó az SAP HANA-példányok után nem használ további szükséges sorokat törölheti. Az összes sor kell vagy kitölti a rendszer, megjegyzésként szerepel, vagy törölni.

>[!IMPORTANT]
>A fájl szerkezete való verzió a 2.1-es verzió átálláskor módosított 3.x. Ha azt szeretné használni a 3.x verziót szkripteket, a konfigurációs fájl struktúra alkalmazkodni szüksége. 


```
HANA Server Name: testing01
HANA Server IP Address: 172.18.18.50
```

Minden példány, amelyet állít be a nagyméretű HANA-példány egység, vagy a kibővített konfigurációs esetén kell határozhatja meg a következő:

    
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
Horizontális felskálázás és HANA-Rendszerreplikálást konfigurációk esetén ismételje meg ezt a konfigurációt, az egyes csomópontokon. Ez a mérték gondoskodik arról, hogy a hiba esetben a biztonsági mentések és végleges tárreplikáció továbbra is működni.   

Miután az összes konfigurációs adatát nyilvántartani a *HANABackupCustomerDetails.txt* fájlt, ellenőrzése, hogy a konfiguráció megfelelő a HANA-példányok adatait. A parancsfájllal `testHANAConnection.pl`, amely független az SAP HANA vertikális vagy horizontális felskálázás konfigurációs.

```
testHANAConnection.pl
```

Ha az SAP HANA kibővített konfigurációs van, győződjön meg arról, hogy a fő HANA-példány hozzáfér az összes szükséges HANA-kiszolgálók és példányok. Nincsenek paraméterei a teszt-szkript, de az adatokat hozzá kell adnia a *HANABackupCustomerDetails.txt* konfigurációs fájlt a parancsfájl megfelelően fusson. Csak a rendszerhéj parancssori hiba kódokat ad vissza, ezért nem lehetséges a parancsfájl hibáját Ellenőrizze minden példány. Még ha így a parancsfájl megjegyzéseket néhány hasznos lehet, hogy ellenőrizni.

A parancsfájl futtatásához írja be a következő parancsot:
```
 ./testHANAConnection.pl
```
Ha a parancsfájl sikeresen beszerzi a HANA-példány állapotát, hogy sikeres volt-e a HANA-kapcsolatot üzenetet jelenít meg.


A következő vizsgálati lépés az, hogy ellenőrizze a kapcsolatot a tárolóhoz, mindössze az adatok alapján a *HANABackupCustomerDetails.txt* konfigurációs fájlt, és a egy teszt pillanatkép végre. Végrehajtása előtt a `azure_hana_backup.pl` parancsfájl, futtatnia kell ezt a vizsgálatot. Ha egy kötet a pillanatképek nem tartalmaz, nem lehet megállapítani, hogy a kötet üres, vagy ha egy SSH sikertelen, a pillanatkép részletek beszerzése érdekében. Ebből kifolyólag a szkript két lépést hajt végre:

- Ellenőrzi, hogy a bérlő tároló virtuális gép és a felületek érhetők el, a parancsprogramok pillanatképek végrehajtásához.
- Minden kötet tesztelési vagy dummy, pillanatkép a HANA-példányt hoz létre.

Ebből kifolyólag a HANA-példány megtalálható argumentumként. Ha a végrehajtás sikertelen lesz, azt, nem lehet biztosítani a storage-kapcsolat ellenőrzése hibát. Akkor is, ha van a rendszer nem ellenőrzi, a parancsfájl biztosít hasznos tippek.

1. Hajtsa végre a parancssorozat a tesztelés végrehajtásához:

   ```
   ssh <StorageUserName>@<StorageIP>
   ```

   A felhasználó nevét és a storage IP-címet adtak meg Önnek a készenléti feladat átadása a nagyméretű HANA-példány egység található.

1. Futtassa a test-parancsfájlt:
   ```
    ./testStorageSnapshotConnection.pl
   ```

A parancsfájl megpróbálja a Storage elérhető az előző lépésekben a telepítő, valamint a konfigurált adatokkal a nyilvános kulccsal jelentkezzen be a *HANABackupCustomerDetails.txt* fájlt. Ha a bejelentkezési művelet sikeres, az alábbi tartalommal jelenik meg:

```
**********************Checking access to Storage**********************
Storage Access successful!!!!!!!!!!!!!!
```

Ha problémák fordulnak elő a storage-konzol csatlakozik, a kimenet a következőhöz hasonló:

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

A sikeres bejelentkezést követően a tároló virtuális gép felületek a parancsfájl továbbra is fennáll, a 2. fázis, és létrehoz egy tesztelési pillanatképet. A kimenet itt látható egy három csomópontos horizontális felskálázás konfigurálásához az SAP HANA:

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

Ha a teszt pillanatképének a szkript sikeresen végrehajtva, folytathatja a tényleges tárterület pillanatképek konfigurálása. Ha nem sikeres, a problémák vizsgálatához a előtt. A teszt pillanatképének köré kell maradnak, addig, amíg az első valós pillanatképek történik.


### <a name="step-7-perform-snapshots"></a>7. lépés: Hajtsa végre a pillanatképek

Előkészítő lépések befejeződése után a tényleges pillanatkép tárolókonfigurációt konfigurálása megkezdése. Ütemezni a szkript SAP HANA vertikális és horizontális felskálázás konfigurációval működik. A biztonsági mentési parancsprogram-ismétlődő és rendszeres végrehajtásához ütemezni a parancsfájl a cron segédprogram használatával. 

Pillanatkép készítése Adatbázisfájlokról három típusú hozhat létre:
- **HANA**: egy kombinált pillanatkép biztonsági másolatából, amelyben a kötetek/hana/adatokat tartalmazó és a/hana/megosztott (amely tartalmazza, valamint /usr/sap) esnek a koordinált pillanatkép. Egyetlen fájlvisszaállításra lehetőség a pillanatképből.
- **Naplók**: / hana/logbackups kötet egy pillanatkép biztonsági másolatából. Nincs HANA pillanatképe akkor aktiválódik, a storage-pillanatkép végrehajtásához. A tárkötet hivatott tartalmaz az SAP HANA tranzakciónaplók biztonsági mentését. Ezek történik gyakrabban log növekedésének korlátozása és a potenciális adatvesztés megelőzése érdekében. Egyetlen fájlvisszaállításra lehetőség a pillanatképből. 3 perc alatt az a gyakoriság nem csökken.
- **Rendszerindító**: a rendszerindító logikaiegység-szám (LUN), a nagyméretű HANA-példányt tartalmazó kötet pillanatképét. A pillanatkép biztonsági mentésre lehetőség, csak a a i. típus SKU-k a nagyméretű HANA-példányokhoz. Nem végezhet egyetlen fájl helyreállítást a pillanatképből a kötet, amely tartalmazza a rendszerindító LUN-t.


>[!NOTE]
> A hívási szintaxis, ezek a pillanatképek a 3.x MCOD központi telepítések támogatásához verzió parancsfájloknak való átálláskor módosított három típusú. Hiba esetén nem kell többé megadása a HANA SID-példány. Győződjön meg arról, hogy az SAP HANA-példányok egység vannak-e konfigurálva a konfigurációs fájlban kell *HANABackupCustomerDetails.txt*.

>[!NOTE]
> A parancsprogram futtatása az első alkalommal, amikor, előfordulhat, hogy a több biztonsági AZONOSÍTÓVAL környezet váratlan hibák megjelenítése. A parancsfájl megismétlése megoldja a problémát.



A storage-pillanatképeket a parancsfájl végrehajtása az új hívási szintaxis *azure_hana_backup.pl* kell kinéznie:

```
HANA backup covering /hana/data and /hana/shared (includes/usr/sap)
./azure_hana_backup.pl hana <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

For /hana/logbackups snapshot
./azure_hana_backup.pl logs <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

For snapshot of the volume storing the boot LUN
./azure_hana_backup.pl boot <HANA Large Instance Type> <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

```

A részleteket a paraméterek a következők: 

- Az első paraméterként a pillanatkép biztonsági másolat típusa jellemzi. Az engedélyezett értékek a következők **hana**, **naplók**, és **rendszerindító**. 
- A paraméter **<HANA Large Instance Type>** rendszerindító kötetet a biztonsági másolatokat csak szükség. Nincsenek függ a HANA nagyméretű példány egység két érvényes érték a "TypeI" vagy "TypeII". Ismerje meg, hogy az egység típusa van, lásd: [SAP HANA (nagyméretű példányok) áttekintése és architektúrája az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).  
- A paraméter **< snapshot_prefix >** snapshot vagy snapshot típusú biztonsági mentési címke. Két célja van: egyet az, hogy adjon meg egy nevet, hogy tudja, Mik azok a pillanatképek. A második célja a parancsfájl *azure\_hana\_backup.pl* meghatározni, hogy az adott címke alatt megmaradnak a storage pillanatképek számát. Ha ütemez be két tárhely pillanatképes biztonsági mentései az azonos típusú (például **hana**), két különböző címkét, és határozza meg, hogy minden 30 pillanatképek kell tárolni, az érintett kötetek 60 tárolási pillanatképekkel megtörténhet. Csak alfanumerikus ("A-Z, a-z, 0-9"), aláhúzásjel ("_") és kötőjel ("-") karakter használata engedélyezett. 
- A paraméter **< snapshot_frequency >** fenntartja a jövőbeli fejlesztésekkel, és nincs hatással. Állítsa be a "3 perccel" típusú biztonsági mentés végrehajtásakor **log**, és a "15 perc a" a többi biztonsági mentési típusok végrehajtása közben.
- A paraméter **<number of snapshots retained>** megőrzési idejét a pillanatképek közvetve a pillanatkép ugyanazon előtaggal (címke) rendelkező pillanatképek számának megadásával határozza meg. Ez a paraméter fontos ütemezett végrehajtás cron keresztül. Az azonos snapshot_prefix-pillanatképeket száma meghaladja a paraméter által megadott, ha a legrégebbi pillanatkép törlődik egy új storage-pillanatkép végrehajtása előtt.

Egy kibővített esetén a parancsfájl hajtja végre további annak ellenőrzése, győződjön meg arról, hogy minden HANA-kiszolgáló elérhető. A parancsfájl azt is ellenőrzi, hogy az összes HANA-példányok adják vissza megfelelő-példánya egy SAP HANA-pillanatkép létrehozása előtt. Az SAP HANA pillanatképe tárolási pillanatkép követ.

A szkript végrehajtásának `azure_hana_backup.pl` hoz létre a storage-pillanatkép készítése a következő három fázisra:

1. Végrehajt egy SAP HANA pillanatképe
1. Végrehajt egy storage-pillanatkép
1. Eltávolítja az SAP HANA pillanatképe, amely a tároló pillanatképe végrehajtása előtt lett létrehozva

A szkript végrehajtásához hívja a mappából HDB végrehajtható, amelyhez másolták. 

A megőrzési időszak a paramétert, ha a parancsprogram futtatása küldött pillanatképek számát kezel. Az, hogy mennyi ideig hatálya alá tartozik a tárolási pillanatképekkel, akkor egy függvény az időszak, végrehajtás és a beküldött paraméterként, a parancsfájl végrehajtása közben pillanatképek számát. Őrzi meg a pillanatképek száma meghaladja a számát, amelyet a parancsfájl hívásában paraméterként vannak elnevezve, ha a legrégebbi tárolási pillanatképet címkét törlődik, új pillanatkép végrehajtása előtt. A szám adjon, az utolsó paraméter, a hívások száma segítségével őrzi meg a pillanatképek számának. Ezzel a számmal is szabályozhatja, közvetett, a pillanatképek felhasznált lemezterületet. 

> [!NOTE]
>Amint módosíthatja a címke, a leltárt ismét elindul. Szükség lehet kötött a címkézésre, így a pillanatképek véletlenül sem törlődnek.

### <a name="snapshot-strategies"></a>Pillanatkép stratégiák
A különböző tevékenységtípusokkal kapcsolatban pillanatképek gyakorisága attól függ, ha használja a nagyméretű HANA-példány vészhelyreállítási funkciók. Ez a funkció speciális ajánlások lehet szükség a gyakoriságát és végrehajtási időszakokra készített pillanatképeket tároló tárolási pillanatképeket támaszkodik. 

A szempontok és az alábbi, feltételezzük, hogy *nem* használja a vészhelyreállítási funkciók által kínált nagyméretű HANA-példányokhoz. Ehelyett használja a pillanatképeket tároló biztonsági mentések és időponthoz helyreállítási nyújthatnak az elmúlt 30 napra vonatkozóan. A korlátozások a pillanatképeket és terület számát adja meg, ügyfeleink tekintette az alábbi követelményeknek:

- A helyreállítási pont az adott helyreállítási ideje.
- A használt területet.
- A helyreállítási pont és helyreállításiidő-célkitűzések lehetséges helyreállítási történő vészhelyreállítás.
- A végleges végrehajtásának HANA teljes-adatbázis biztonsági mentését lemezek ellen. Amikor elleni lemezek teljes-adatbázis biztonsági másolatát, vagy a **backint** felületen történik, a storage-pillanatképek végrehajtása meghiúsul. Ha azt tervezi, hogy hajtsa végre a teljes adatbázis-biztonsági mentések tárolási pillanatképekkel felett, ellenőrizze, hogy a storage-pillanatképek végrehajtásának le van tiltva ebben az időszakban.
- A pillanatképek (legfeljebb 250) kötetenként számát.


Azok a vásárlóknak, akik HANA nagyméretű példányok vész-helyreállítási funkciójának ne használja a pillanatkép idő pedig kevésbé gyakori. Ezekben az esetekben ügyfelek hajtsa végre a kombinált pillanatképek /hana/data és /hana/shared (tartalmazza a /usr/sap) 12 óránként vagy 24 órás időszakban, és a pillanatképek egy hónapig vezetnek. Ugyanez vonatkozik a napló biztonsági mentési kötet pillanatképekkel. Azonban az SAP HANA tranzakciós naplók biztonsági mentését a napló biztonsági mentési mennyiségi elleni végrehajtását lép fel az 5 perces 15 perces időszakokra.

Ütemezett tárolási pillanatképekkel végezhető el a legjobban cron használatával. Biztonsági mentések és a vész-helyreállítási igényekre ugyanazt a parancsprogramot használja, és módosítsa a parancsfájl bemeneti adatok felel meg a különböző biztonsági mentés időpontjai kért. Ezek a pillanatképek minden ütemezett eltérően a végrehajtási idejű függően cron: óránként, 12 óránként, napi vagy heti. 

Az alábbiakban látható egy példa az/etc/crontab egy cron-ütemezését:
```
00 1-23 * * * ./azure_hana_backup.pl hana hourlyhana 15min 46
10 00 * * *  ./azure_hana_backup.pl hana dailyhana 15min 28
00,05,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
22 12 * * *  ./azure_hana_backup.pl log dailylogback 3min 28
30 00 * * *  ./azure_hana_backup.pl boot TypeI dailyboot 15min 28
```
Az előző példában van egy óránként összesített pillanatkép, amely lefedi a/hana/adatokat tartalmazó kötetek és /hana/shared (beleértve a/usr/sap) helyen. Az ilyen típusú pillanatkép használata időpontban a gyorsabb helyreállítás az elmúlt két napban. Emellett nincs napi pillanatképet a köteteket. Ezért kell érvényességének két nap óránkénti pillanatképeket, valamint négy héttel érvényességének által a napi pillanatképeket. Ezenkívül a tranzakciós napló biztonsági mentési mennyiségi készüljön biztonsági mentés naponta. Négy héttel is őrzi meg ezeket a biztonsági másolatokat. Ahogyan crontab a harmadik sorban az látható, a HANA-tranzakciónapló biztonsági mentésének van ütemezve, hogy 5 percenként. A kezdési idejének különböző cron feladatot, a pillanatképek tárolási végrehajtható egyenletesen vannak elosztani, így ezen pillanatképek nem hajtja végre egyszerre egy egy adott időpontban. 

A következő példában hajt végre, amely lefedi a/hana vagy adat- és/hana/megosztott (például/usr/sap) hely óránként tartalmazó kötetek összesített pillanatképet. Két napig megőrzi ezeket a pillanatképeket. A pillanatképek a tranzakciós napló biztonsági mentési kötetek egy 5 perces időközönként hajtja végre, és 4 órán át őrzi meg. Mint előtt, a biztonsági mentés a HANA tranzakciós naplófájl van ütemezve, hogy 5 percenként. A tranzakciós napló biztonsági mentési mennyiségi pillanatképét a tranzakciónapló biztonsági mentését megkezdése után egy 2 perces késéssel történik. Az SAP HANA tranzakciónapló biztonsági mentését ezeket 2 percen belül befejeződik normál körülmények között. Előtt, a kötet, amely tartalmazza a rendszerindító LUN biztonsági másolatot naponta egyszer készít egy tároló pillanatképe és négy héttel marad.

```
10 0-23 * * * ./azure_hana_backup.pl hana hourlyhana 15min 48
0,5,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup.pl log logback 3min 48
30 00 * * *  ./azure_hana_backup.pl boot TypeII dailyboot 15min 28
```

A következő ábra mutatja be az előző példában, kivéve a rendszerindítás LUN sorrendje:

![Biztonsági mentések és a pillanatképek közötti kapcsolat](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA végez a /hana/log kötet dokumentálni a véglegesített módosításokat az adatbázis rendszeres írására. Rendszeres időközönként az SAP HANA mentési pont ír a /hana/data kötetet. Az crontab meghatározottak szerint egy SAP HANA tranzakciónapló biztonsági mentését hajtja végre 5 percenként. Is megjelenik, hogy az SAP HANA pillanatképe óránként eredményeként egy kombinált tároló pillanatképe elindítása a /hana/data és /hana/shared kötetek keresztül hajtja végre. Miután a HANA pillanatképe sikeres, a kombinált tároló pillanatképe hajtja végre. Crontab utasításai minden 5 perc, körülbelül 2 percet a HANA tranzakciónapló biztonsági mentését követően a tároló pillanatképe a /hana/logbackup köteten hajtja végre.

> 

>[!IMPORTANT]
> SAP HANA biztonsági mentésekhez tárolási pillanatképekkel használatát az értékes, csak akkor, amikor a pillanatképeket az SAP HANA tranzakciónapló biztonsági mentései együtt történik. A tranzakciós napló biztonsági mentése szükséges ahhoz, hogy biztosítsák az időszakok, a storage-pillanatképek közötti. 

Ha beállította a felhasználók egy 30 napig időponthoz helyreállítási kötelezettségvállalás, kell tennie:

- A szélsőséges esetben egy pillanatkép-/hana/data és, amely 30 napnál régebbi /hana/shared kombinált tároló eléréséhez.
- Összefüggő tranzakciós naplók biztonsági mentését, a kombinált tárolási pillanatképek bármelyike között eltelt idő mind rendelkeznek. Ezért a legrégebbi pillanatkép, a tranzakciós napló biztonsági mentési mennyiségi kell lennie a 30 napnál régebbi. Nem ez a helyzet, ha a tranzakciónapló biztonsági mentései másolja egy másik NFS-megosztás, az Azure storage található. Ebben az esetben előfordulhat, hogy kérje le régi tranzakciónaplók biztonsági mentését, hogy az NFS-megosztásból.

A pillanatképek tárolási és a tranzakciós naplók biztonsági másolatainak végleges tárreplikáció kihasználhatják módosítani szeretné a helyet, amelyhez az SAP HANA ír a tranzakciónapló biztonsági mentései. Ezt a módosítást a HANA Studio. Bár az SAP HANA biztonsági mentését teljes log szegmensek automatikusan, meg kell adni a napló biztonsági mentési gyakoriságát, determinisztikus. Ez különösen igaz a vész-helyreállítási beállítások használatakor mivel általában determinisztikus időszakkal a naplóalapú biztonsági mentések végrehajtásához. A következő esetben a napló biztonsági mentési időköz 15 perc vannak beállítva.

![Az SAP HANA Studio bejelentkezik az SAP HANA biztonsági mentés ütemezése](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

15 percenként üzemezésnél gyakoribb biztonsági mentéseket is beállíthatja. Egy gyakoribb beállítást gyakran használják együtt a HANA nagyméretű példányok vész-helyreállítási funkciójának. Egyes ügyfeleink hajtsa végre a tranzakciónapló biztonsági mentései 5 percenként.  

Ha az adatbázis a nem készített biztonsági másolatot, az utolsó lépés az fájlalapú adatbázis biztonsági mentését, hogy hozzon létre egy egyetlen biztonsági mentési bejegyzést, amely a biztonságimásolat-katalógus léteznie kell. SAP HANA ellenkező esetben a megadott naplóalapú biztonsági mentések nem kezdeményezhető.

![Győződjön meg arról, egy fájl alapú biztonsági mentés egyetlen biztonsági mentési bejegyzés létrehozása](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Miután az első sikeres tárfiók-pillanatképek többségén futtatva volt, törölheti a 6. lépésben végrehajtott teszt pillanatkép. Ehhez futtassa a parancsfájlt `removeTestStorageSnapshot.pl`:
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


### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>A számát és méretét, a pillanatképek a lemez köteten figyelése

Egy adott tárolási köteten figyelemmel kísérheti a pillanatképek és a tárhelyhasználat ezen pillanatképek számát. A `ls` parancs nem jelenít meg, a pillanatkép könyvtárat vagy fájlokat. Ugyanakkor a Linux operációs rendszer parancs `du` tárolási pillanatképeket, kapcsolatos részleteket jeleníti meg, mert azok az azonos köteteken tárolt. A parancs is használható a következő beállításokkal:

- `du –sh .snapshot`: Ezt a lehetőséget biztosít a pillanatkép-könyvtárban lévő összes pillanatképet összesen.
- `du –sh --max-depth=1`: Ez a beállítás felsorolja a mentett összes pillanatképet a **.snapshot** mappát, és minden pillanatkép mérete.
- `du –hc`: Ezt a lehetőséget biztosít az összes pillanatképet által használt teljes mérete.

Ezek a parancsok használatával győződjön meg arról, hogy az elvégzett és tárolt pillanatképek nem használ fel a köteteken a tárolót.

>[!NOTE]
>A pillanatképek a Boot logikai egység nem láthatók az előző parancsokkal.

### <a name="getting-details-of-snapshots"></a>A pillanatképek részleteinek beolvasása közben
További részletekért a pillanatképeket, használhatja a parancsfájl `azure_hana_snapshot_details.pl`. Ez a szkript az egyik helyen futtatható, ha az aktív kiszolgáló a vész-helyreállítási hely található. A szkript a következő kimenet, minden olyan kötetre, amely tartalmazza a pillanatképek bontásban tartalmazza: 
   * Teljes pillanatképek a kötet mérete
   * Az adott köteten lévő minden egyes pillanatkép a következő adatokat: 
      - Pillanatkép neve 
      - Létrehozás időpontja 
      - Pillanatkép mérete
      - A pillanatkép gyakorisága
      - Ha szükséges, hogy a pillanatkép társított HANA biztonsági mentés azonosítója

Az alábbiakban látható egy példa a parancsfájl végrehajtási szintaxist:

```
./azure_hana_snapshot_details.pl 
```

Mivel a szkript megpróbálja beolvasni a HANA biztonsági Azonosítóját, az SAP HANA-példányhoz való kapcsolódáshoz szükséges. Ezt a kapcsolatot igényel a konfigurációs fájl *HANABackupCustomerDetails.txt* helyesen kell beállítani. Két pillanatképek a kötet kimenet előfordulhat, hogy a következőhöz hasonló:

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


### <a name="file-level-restore-from-a-storage-snapshot"></a>Fájlszintű visszaállítás tárolási pillanatképből
A pillanatkép-típusok **hana** és **naplók**, közvetlenül a köteteken a pillanatképeket is elérheti a **.snapshot** könyvtár. A pillanatképek minden alkönyvtár van. Minden egyes fájl másolhat az állapotba, amelyben a pillanatkép, az adott alkönyvtárban azokat a tényleges könyvtárstruktúrát ponton kell megadni. A parancsfájl jelenlegi verziójában, nincs **nem** visszaállítása (bár a pillanatkép-visszaállítás végezhető, mint a feladatátvétel során a DR helyen parancsprogramjait a önkiszolgáló DR része) egy önkiszolgáló, a pillanatkép-visszaállítás a megadott szkriptet. Nyissa meg a visszaállítani kívánt pillanatkép a meglévő elérhető pillanatképekből szolgáltatáskérés kapcsolatba kell lépnie a Microsoft üzemeltetési csapat.

>[!NOTE]
>Egyetlen fájl visszaállítása nem működik a rendszerindítás független a nagyméretű HANA-példány mértékegység típusát a logikai egység pillanatkép-készítési. A **.snapshot** könyvtár nem érhető el a rendszerindítás LUN-t. 


### <a name="reducing-the-number-of-snapshots-on-a-server"></a>A kiszolgálón a pillanatképek számának csökkentése

Korábban leírtak csökkentheti az egyes címkék tárolt pillanatképek számát. Az utolsó két pillanatkép kezdeményezni a parancs paraméterei a címkét, és meg szeretné őrizni a pillanatképek számát.

```
./azure_hana_backup.pl hana dailyhana 15min 28
```

Az előző példában a pillanatkép-címke van **dailyhana** , és meg kell őrizni, ezzel a címkével ellátott pillanatképek számát **28**. Lemezterület-felhasználást válaszolni, előfordulhat, hogy szeretne tárolt pillanatképek számának csökkentése érdekében. A pillanatképek számát csökkentheti a 15., például az egyszerű módot beállítva az utolsó paraméterrel a parancsfájl futtatásához **15**:

```
./azure_hana_backup.pl hana dailyhana 15min 15
```

A parancsfájl futtatja ezt a beállítást, ha a pillanatképeket, beleértve az új tároló pillanatképe szám 15. Őrzi meg a 15 legutóbbi pillanatképeket, és a 15 régebbi pillanatképeket a rendszer törli.

 >[!NOTE]
 > Ez a szkript csökkenti a pillanatképek számát, csak akkor, ha több mint 1 óra régi pillanatképek vannak. A parancsfájl nem törli a pillanatképeket, amelyek 1 órával korábbiak. Ezek a korlátozások kapcsolódó érhető el a nem kötelező vészhelyreállítási funkciók.

Ha már nem szeretné a biztonsági mentési címke-pillanatképeket készletét karbantartása **hanadaily** a szintaxis példák a parancsfájlt futtathat **0** a megőrzési számát. A Label értékeként megfelelő összes pillanatképet majd el lesznek távolítva. Azonban minden pillanatkép eltávolítása hatással lehet a HANA nagyméretű példányok vészhelyreállítási funkciók képességeit.

Adott pillanatképek törlése egy második lehetőség egy parancsfájl `azure_hana_snapshot_delete.pl`. Ez a szkript készült egy pillanatkép vagy vagy formában, HANA Studio vagy a pillanatkép neve magát a HANA biztonsági másolat azonosítója használatával a pillanatképek törlése. Jelenleg a biztonsági másolat azonosítója csak vannak kötve, a készült pillanatkép a **hana** pillanatkép típusa. Pillanatkép-típusú biztonsági mentések **naplók** és **rendszerindító** nem hajt végre egy SAP HANA pillanatképe és így nem található pillanatképeket, nincs biztonsági mentési azonosító. Ha a pillanatkép neve van megadva, a különböző köteteken, amelyek megfelelnek a megadott pillanatkép neve minden pillanatkép keres. 

Hívja a parancsfájl, meg kell adnia a biztonsági AZONOSÍTÓJÁT, a HANA-példány a parancsfájl a hívási szintaxis használatával:

```
./azure_hana_snapshot_delete.pl <SID>

```

Hajtsa végre a parancsfájl felhasználóként **legfelső szintű**.

Ha kiválaszt egy pillanatkép, külön-külön törölheti egyes pillanatkép. Először adja meg a pillanatképet tartalmazó kötetet, és ezután adja meg a pillanatkép neve. Ha a pillanatkép létezik a kötetet, és több mint 1 óra régi, akkor törlődik. Annak a kötet nevét és a pillanatkép neve úgy, hogy végrehajtja a `azure_hana_snapshot_details` parancsfájlt. 

>[!IMPORTANT]
>Ha csak a pillanatkép a meglévő adatot törölni, a pillanatkép törlését követően, hogy adat elvész örökre.

   

### <a name="recover-to-the-most-recent-hana-snapshot"></a>A helyreállítás a legfrissebb HANA pillanatképe

Egy éles válassza ki a forgatókönyvben egy tárolási pillanatképből helyreállításának folyamatán, egy ügyfél eseményt a Microsoft Azure támogatási szolgálatával kezdeményezhetők. A magas sürgősségű függetlenül attól, hogy, akkor, ha adatokat törölte a termelési rendszer, és az egyetlen módszer is lekérheti azt, hogy az éles adatbázis visszaállításához.

Egy másik esetben a időponthoz helyreállítás lehet, hogy alacsony sürgősség, és a tervezett nappal. Megtervezheti a helyreállítás, az SAP HANA az Azure Service Management helyett egy magas prioritású jelző előléptetése. Például előfordulhat, hogy lehet tervezi az SAP-szoftverek frissítése a fejlesztés új csomag alkalmazásával. Kell visszaállítania egy pillanatkép, amely előtt a fejlesztés Csomagfrissítés állapotát jelöli.

Mielőtt elküldi a kérelmet, kell előkészíteni. Az SAP HANA az Azure Service Management csapata majd kezelni a kérést, és adja meg a visszaállított kötetek. Ezután állítsa vissza a HANA-adatbázis, a pillanatképek alapján. 

A következő bemutatja, hogyan készülhet fel a kérelmet:

>[!NOTE]
>A felhasználói felület kissé eltérhetnek attól függően, a SAP HANA kiadás, amely használja az alábbi képernyőfelvételnek megfelelően.

1. Döntse el, melyik pillanatkép visszaállítása. Csak a hana/adatmennyiség helyreáll, kivéve, ha más módon utasíthatja. 

1. Állítsa le a HANA-példány.

 ![A HANA-példány leállítása](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Válassza le az adatkötetek a HANA-adatbázis csomópontokon. Ha az adatkötetek továbbra is csatlakoztatva vannak az operációs rendszer, a pillanatkép visszaállítása sikertelen lesz.
 ![Válassza le az adatkötetek a HANA-adatbázis csomópontokon](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Nyisson meg egy Azure-támogatási kérést, és a visszaállítást egy adott pillanatkép kapcsolatos utasításokat is tartalmazzák.

 - A visszaállítás során: SAP HANA az Azure Service Management fel, hogy vegyen részt egy konferenciahívás koordinálása, ellenőrzése és megerősítése, hogy a helyes tárfiók-pillanatkép visszaállítása biztosítása érdekében. 

 - A visszaállítás után: SAP HANA az Azure Service Management értesíti, amikor a storage-pillanatkép-visszaállítás.

1. A visszaállítási folyamat befejezése után csatlakoztassa újra az adatköteteket.

 ![Minden adatkötetnél újracsatlakoztathatja](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)

1. Jelölje be az SAP HANA Studio, a helyreállítási beállításokat, ha nem automatikusan származnak, ha újból csatlakozik a HANA-adatbázis az SAP HANA Studio eszközön keresztül. Az alábbi példa bemutatja a visszaállítás, az utolsó HANA pillanatképe. Tárolási pillanatkép beágyazása egy HANA pillanatképe. Ha a legutóbbi tárolási pillanatkép, a legfrissebb HANA pillanatképe kell lennie. (Ha egy régebbi tároló pillanatképe, kell keresse meg a HANA pillanatkép, a storage pillanatkép-időn alapulnak.)

 ![Válassza ki az SAP HANA Studio beállítások helyreállítása](./media/hana-overview-high-availability-disaster-recovery/image10-recover-options-a.png)

1. Válassza ki **az adatbázis helyreállítása az adatok biztonsági mentése és a storage pillanatkép**.

 ![A helyreállítás típusának megadása ablak](./media/hana-overview-high-availability-disaster-recovery/image11-recover-options-b.png)

1. Válassza ki **adjon meg biztonsági mentés nélkül katalógus**.

 ![A biztonsági mentési hely megadása ablak](./media/hana-overview-high-availability-disaster-recovery/image12-recover-options-c.png)

1. Az a **céltípus** listáról válassza ki **pillanatkép**.

 ![Adja meg a biztonsági mentés, helyreállítás ablak](./media/hana-overview-high-availability-disaster-recovery/image13-recover-options-d.png)

1. Válassza ki **Befejezés** a helyreállítási folyamat elindításához.

 ![Válassza a "Befejezés" gombra a helyreállítási folyamat elindítása](./media/hana-overview-high-availability-disaster-recovery/image14-recover-options-e.png)

1. A HANA-adatbázis visszaállítása és helyreállított a HANA-pillanatkép, amely megtalálható a storage-pillanatkép.

 ![HANA-adatbázis visszaállítása és helyreállított a HANA pillanatképe](./media/hana-overview-high-availability-disaster-recovery/image15-recover-options-f.png)

### <a name="recover-to-the-most-recent-state"></a>A helyreállítás a legutóbbi állapot

A következő folyamat visszaállítja a HANA-pillanatkép, amely megtalálható a storage-pillanatkép. Majd visszaállítja a tranzakciónaplók biztonsági mentését az adatbázis legutóbbi állapotát a storage-pillanatkép visszaállítása előtt.

>[!IMPORTANT]
>Mielőtt folytatná, ellenőrizze, hogy egy teljes körű és összefüggő lánc tranzakciós naplók biztonsági másolatainak. Ezeket a biztonsági másolatokat, nélkül az adatbázis jelenlegi állapota nem állítható vissza.

1. Hajtsa végre a lépéseket 1 – 6 [a legfrissebb HANA pillanatképe helyreállítás](#recovering-to-the-most-recent-hana-snapshot).

1. Válassza ki **állítsa helyre az adatbázist a legutóbbi állapotára**.

 ![Válassza a "Állítsa helyre az adatbázist a legutóbbi állapotára"](./media/hana-overview-high-availability-disaster-recovery/image16-recover-database-a.png)

1. Adja meg a legfrissebb HANA naplóalapú biztonsági mentések helyét. A hely összes HANA tranzakciós napló biztonsági mentés a HANA-pillanatképből a legfrissebb állapotba tartalmaznia kell.

 ![Adja meg a legfrissebb HANA naplóalapú biztonsági mentések helyét](./media/hana-overview-high-availability-disaster-recovery/image17-recover-database-b.png)

1. Válasszon biztonsági másolatot egy alapjaként, amelyből az adatbázis helyreállításához. Ebben a példában a HANA pillanatképe a képernyőképen a HANA pillanatkép, a storage-pillanatkép található. 

 ![Egy alapjaként, amelyből az adatbázis helyreállítása biztonsági másolat kiválasztása](./media/hana-overview-high-availability-disaster-recovery/image18-recover-database-c.png)

1. Törölje a **használja a különbözeti biztonsági mentések** jelölőnégyzetet, ha az eltérések nem léteznek a HANA-pillanatkép és a legutóbbi állapot között.

 ![Törölje a "Használata a különbözeti biztonsági mentések" jelölőnégyzet jelölését, ha nincsenek eltérések létezik](./media/hana-overview-high-availability-disaster-recovery/image19-recover-database-d.png)

1. Az Összegzés képernyőn válassza ki a **Befejezés** megkezdheti a visszaállítást.

 ![Kattintson a "Befejezés" gombra az Összegzés képernyőn](./media/hana-overview-high-availability-disaster-recovery/image20-recover-database-e.png)

### <a name="recover-to-another-point-in-time"></a>Helyreállítása egy másik időpontra állapotra
Helyreállítás egy időben a HANA-pillanatkép (tartalmazza a storage-pillanatkép) és a egy későbbi, mint a pillanatkép-időponthoz HANA helyreállítási között, hajtsa végre az alábbi lépéseket:

1. Győződjön meg arról, hogy rendelkezik-e az összes a tranzakciónapló biztonsági mentései a HANA-pillanatképből a helyreállítani kívánt alkalommal.
1. Az eljárás alatt megkezdése [, a legutóbbi állapot helyreállításához](#recovering-to-the-most-recent-state).
1. A 2. lépés: az eljárás a a **helyreállítási típus megadása** ablakban válassza **időben a következő pontot az adatbázis helyreállítása**, majd adja meg a pont időben. 
1. 3 – 6. lépések elvégzésével.

### <a name="monitor-the-execution-of-snapshots"></a>A pillanatképek végrehajtásának figyelése

Használja a HANA nagyméretű példányok pillanatképeit tároló, is ezen pillanatképek végrehajtásának figyelni kell. A parancsfájl, amely végrehajtja a tárolási pillanatkép kimenetet egy fájlba írja, és ezután menti a Perl-parancsfájlokat ugyanazon a helyen. Minden egyes tárolási pillanatkép készült egy különálló fájlban. Az egyes fájlok megjelenítheti a különböző fázisait, hogy a pillanatkép-parancsfájl végrehajtása:

1. Megkeresi a köteteket, létre kell hoznia egy pillanatkép.
1. Megkeresi a kötetekről készített pillanatképeket.
1. Törli a megadott pillanatképek számának megfelelően végleges meglévő pillanatképeket.
1. Létrehoz egy SAP HANA pillanatképe.
1. A kötetek keresztül hoz létre a storage-pillanatkép.
1. Törli az SAP HANA pillanatképe.
1. Átnevezi a legutóbbi pillanatképet **.0**.

A parancsfájl cab azonosított legfontosabb részét ez a rész:
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
Ez a példa a láthatja, hogyan a parancsfájl rögzíti-e a HANA-pillanatkép létrehozását. A horizontális felskálázás esetben ez a folyamat a főcsomóponton indul el. A fő csomópont létrehozását kezdeményezi a szinkron a munkavégző csomópontok az SAP HANA készített pillanatképeket. A storage-pillanatkép kerül. A storage-pillanatképek sikeres végrehajtását követően a HANA pillanatképe törlődik. A HANA-pillanatkép törlését kezdeményezni a fő csomóponttal.


## <a name="disaster-recovery-principles"></a>Vész-helyreállítási alapelvek
Nagyméretű HANA-példányok ajánlat egy vészhelyreállítási funkciók nagyméretű HANA-példány bélyegekre különböző Azure-régiók között. Például ha nagyméretű HANA-példányt az USA nyugati régiójában, az Azure egységeket telepít, használhatja a nagyméretű HANA-példány egységek USA keleti, vész-helyreállítási egység. Ahogy korábban említettük, vész-helyreállítási nem történik meg automatikusan, mert a szükséges, hogy a DR régióban egy másik nagyméretű HANA-példány egység kell fizetnie. A vészhelyreállítás telepítője vertikális valamint horizontális felskálázás mátrixok esetében működik. 

A forgatókönyvekben, amennyiben a telepített ügyfelek használatával az egység a Vészhelyreállítás régióban található telepített HANA-példány nem éles rendszerek futtatása. A nagyméretű HANA-példány egységet kell lennie a ugyanazon termékváltozat, a Termékváltozat éles üzemi célokat szolgál. Az alábbi képen látható, hogy milyen a lemezkonfigurációt a kiszolgáló egységet az éles Azure-régió között, és a vészhelyreállítási régióban néz ki:

![DR-telepítés konfigurációs lemez szempontból](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

Az áttekintése ábrán látható, akkor egy második együttesét lemezkötetek order kell. A célként megadott kötetek mérete megegyezik az éles üzemelő példányok a vész-helyreállítási egységekben éles kötetek. Ezek a lemez-kötetek társítva a nagyméretű HANA-példányt kiszolgáló egységet a vész-helyreállítási helyként. A következő kötetek éles régióban lesznek replikálva a DR-helyre:

- / hana/adatok
- / hana/logbackups 
- /Hana/Shared (a/usr/sap tartalmazza)

A /hana/log kötet nem replikálódnak, mivel az SAP HANA-tranzakciónapló nincs szükség a módon, amely ezekről a kötetekről a visszaállítás történik. 

A vészhelyreállítási funkciók alapjául szolgáló érhető el, a storage-replikáció funkciót a nagyméretű HANA-példány infrastruktúra által kínált. A használt tárolási funkciókat nem állandó adatfolyam bekövetkező változások replikálása aszinkron módon, a tárolási kötet változások történhetnek. Ehelyett érdemes olyan mechanizmus, amely a rendszeres időközönként pillanatképek a kötetek létrehozása a tény támaszkodik. Között egy már replikált pillanatkép és a egy új pillanatkép, amely még nem replikálta a különbözeti cél köteteket, majd átkerülnek a vész-helyreállítási helyként.  Ezek a pillanatképek a köteteken tárolja, és egy vész-helyreállítási feladatátvétel esetén sem kell azokon a köteteken állítható vissza.  

A teljes kötet első adatátvitelt kell lennie, mielőtt adatok mennyisége kisebb, mint az-pillanatképek közötti eltéréseket válik. Ennek eredményeképpen a a kötetek a DR hely a kötet-pillanatképek végrehajtani az élesben használt helyet a egyes tartalmaznak. Végül a DR rendszer használatával elveszett adatok helyreállításához az éles rendszer visszaállítása egy korábbi állapotba beolvasása.

Több független SAP HANA-példányok egy nagyméretű HANA-példány egységen MCOD központi telepítéseknél, esetén várható, hogy minden SAP HANA-példányok kihozhatják a tároló replikálásának a DR oldalra.

Azokban az esetekben, ahol HANA Rendszerreplikáció használata magas rendelkezésre állású funkcióinak az éles webhelyét és storage-alapú replikálás használata a DR-hely mind a csomópontok elsődleges helyről a DR-példányhoz a kötetek lesznek replikálva. Kiegészítő tárolás (azonos méretű kezdődően az elsődleges csomópont) DR hely az elsődleges és másodlagos a Vészhelyreállítási a replikáció megfelelően meg kell vásárolnia. 



>[!NOTE]
>A nagyméretű HANA-példány storage replikációs funkció tükrözés és a pillanatképek tárolási replikálásához. Ha megfelelésről a pillanatképek tárolási nem hajtható végre a [biztonsági mentési és visszaállítási](#backup-and-restore) szakaszban Ez a cikk nem lehetnek a vész-helyreállítási webhelyként bármely replikáció. Storage-pillanatkép-végrehajtás feltétele a storage-bA a vész-helyreállítási helyként.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>A vész-helyreállítási helyzetekre előkészítése
Ebben a forgatókönyvben egy éles rendszer HANA nagyméretű példányok az Azure-régió, éles környezetben futó rendelkezik. A következő lépések feltételezzük, hogy a biztonsági AZONOSÍTÓJÁT, a HANA system "PRD", és, hogy rendelkezik-e a HANA nagyméretű példányok az DR Azure régióban nem éles a rendszert. Az utóbbi tegyük fel, hogy a biztonsági azonosító-e a "Teszt". Ez a konfiguráció az alábbi képen látható:

![DR-telepítés kezdete](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Ha a kiszolgálópéldány nem már megrendelt együtt a további tárolási kötet, SAP HANA az Azure Service Management rendeli a további kötetek készlete, a nagyméretű HANA-példány egységet, amelyen a teszt futtatásakor az éles replika célként HANA-példány. Erre a célra meg kell adnia a biztonsági AZONOSÍTÓT az éles HANA-példány. SAP HANA az Azure Service Management megerősíti, hogy ezek a kötetek a mellékletet, miután kell csatlakoztatni a köteteket a nagyméretű HANA-példány egységhez.

![DR-telepítés következő lépésre](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

A következő lépés a második az SAP HANA-példány telepítésére a nagyméretű HANA-példány egységen, a teszt HANA-példányt futtató Vészhelyreállítás Azure-régióban van. Az újonnan telepített SAP HANA-példányt kell rendelkeznie a SID AZONOSÍTÓVAL. A létrehozott felhasználók UID és a csoport azonosítója, amely rendelkezik az éles üzemelő példányok rendelkeznie kell. Ha a telepítés sikeres volt, kell tennie:

- Hajtsa végre a jelen cikkben korábban tárgyalt pillanatkép előkészítése 2. lépés.
- Ha még nem még meg, hozzon létre egy nyilvános kulcsot a DR egység nagyméretű HANA-példány egység. A cikkben korábban tárgyalt pillanatkép előkészítése 3. lépése.
- Karbantartása a *HANABackupCustomerDetails.txt* az új HANA-példány és a teszt-e kapcsolat a tároló megfelelően működik-e.  
- Az újonnan telepített SAP HANA-példány leállítása a nagyméretű HANA-példány egységen a Vészhelyreállítás Azure-régióban.
- Válassza le ezeket a PRD köteteket, és forduljon az SAP HANA az Azure Service Management. A kötetek nem marad csatlakoztatott egységhez, mert storage replikációs célként, de azok nem lesznek elérhetőek.  

![DR-telepítési lépés replikációs kialakítása előtt](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

Az üzemeltetési csapat létrehozza a replikációs kapcsolat az éles Azure-régióban lévő PRD kötetek és a Vészhelyreállítás Azure-régióban a PRD kötetek között.

>[!IMPORTANT]
>A /hana/log kötet nincs replikálva, mivel nem szükséges a replikált SAP HANA-adatbázis visszaállítása a vész-helyreállítási webhelyként a konzisztens állapotra.

Ezután állítsa be, vagy módosítsa a tárolási pillanatkép biztonsági mentési ütemezés abban a vészhelyreállítási esetben az RTO és RPO elérése. A helyreállításipont-célkitűzés minimalizálása érdekében állítsa be a következő replikálási időközök a nagyméretű HANA-példány szolgáltatásban:
- A a kombinált pillanatkép által szabályozott kötetek (pillanatkép típusa **hana**), 15 percenként, megfelelő tárolási kötet célelemein kerüljön a vész-helyreállítási webhelyként replikálásához beállítása.
- A tranzakciós napló biztonsági mentési kötet (pillanatkép típusa **naplók**), készlet replikálni a vész-helyreállítási webhelyként azonos tárolási kötet célelemein kerüljön át 3 percenként.

A helyreállításipont-célkitűzés minimalizálása érdekében állítsa be a következőket:
- Hajtsa végre egy **hana** típusú storage-pillanatkép (lásd: "7. lépés: hajtsa végre a pillanatképek") 30 percenként 1 óra.
- Hajtsa végre az SAP HANA tranzakciónapló biztonsági mentései 5 percenként.
- Hajtsa végre egy **naplók** írja be az 5 – 15 percenként pillanatkép-tárolás. Az intervallum idővel körülbelül 15-25 percig helyreállítási Időkorlátot érhet el.

Ez a beállítás, a tranzakciós naplók biztonsági mentését, a pillanatképek tárolási és a HANA-tranzakció a replikáció végrehajtása naplózhatja a biztonsági mentési mennyiségi és/hana/adatait, és (beleértve a/usr/sap) /hana/shared hasonló lehet az adatokat, az ábrán látható:

 ![A tranzakciós napló biztonsági mentési pillanatképet és a egy időtengelye beépülő tükör közötti kapcsolat](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

Egy katasztrófa utáni helyreállítás esetben még jobb RPO eléréséhez a más Azure-régió másolhatja a HANA tranzakciónaplók biztonsági mentése az Azure-ban (nagyméretű példányok) SAP HANA-ból. A további helyreállítási Időkorlát csökkentését eléréséhez hajtsa végre az alábbi lépéseket:

1. Készítsen biztonsági másolatot a HANA-tranzakció naplózása gyakorisággal a lehető /hana/logbackups.
1. Rsync használatával másolja a tranzakciónapló biztonsági mentései az NFS megosztás által üzemeltetett Azure virtuális gépeket. A virtuális gépek az Azure virtuális hálózatok az éles Azure-régióban, és a DR régióban találhatók. Mind az Azure virtuális hálózatok összekapcsolása az üzemi nagyméretű HANA-példányokhoz csatlakozhat Azure-kapcsolatcsoporthoz szeretne. Tekintse meg a grafikus elemek az [hálózati szempontok vész-helyreállítási HANA nagyméretű példányok az](#Network-considerations-for-disaster recovery-with-HANA-Large-Instances) szakaszban. 
1. Tartsa a tranzakciónapló biztonsági mentései a régióban, a virtuális gép csatlakozik-e az NFS tárolási exportált.
1. Vészhelyreállítási feladatátvétel esetben kiegészítik a tranzakciónapló biztonsági mentéseivel, keresse meg a /hana/logbackups köteten több nemrég végrehajtott tranzakciónapló biztonsági mentései az NFS megosztani a vész-helyreállítási helyként. 
1. Indítsa el a tranzakciónapló biztonsági mentését, állítsa vissza a legutóbbi biztonsági másolatra, amely a Vészhelyreállítási régióban keresztül lehet menteni.

Amikor hozzákezd a végrehajtási tárhely pillanatképes biztonsági mentései nagyméretű HANA-példány műveletek erősítse meg a replikációs kapcsolat beállítása, megkezdi az adatok replikációját.

![DR-telepítési lépés replikációs kialakítása előtt](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

Mivel a replikáció különböző fázisokon halad, a Vészhelyreállítás Azure-régiókban PRD köteteken a pillanatképeket a rendszer nem állítja vissza. Csak tárolódnak. Ha a kötetek csatlakoztatva legyenek olyan állapotban, az állam, amelyben meg leválasztani a köteteket a kiszolgáló egységben, a Vészhelyreállítás Azure-régióban a PRD SAP HANA-példány telepítését követő jelölnek. A tároló biztonsági mentések, amelyek még nem állítja vissza is képviselik.

Feladatátvétel esetén is választhat egy régebbi tárolási pillanatkép helyett a legújabb tárolási pillanatkép visszaállítása.

## <a name="disaster-recovery-failover-procedure"></a>Vészhelyreállítási feladatátvétel eljárást
Nincsenek figyelembe kell venni a DR-helyre feladatátvétele két kapcsolattípushoz:

- Szüksége lesz az SAP HANA-adatbázis legutóbbi állapota, valamint adatok való visszatéréshez. Ebben az esetben van egy önkiszolgáló parancsfájlt, amellyel anélkül, hogy forduljon a Microsofthoz kellene a feladatátvételt hajthat végre. Azonban a feladat-visszavételhez szüksége a Microsofthoz.
- Szeretné visszaállítani, amely nem a legújabb replikált snapshot storage pillanatkép. Ebben az esetben kell együttműködés a Microsofttal. 

>[!NOTE]
>Az alábbi lépéseket kell hajtható végre, a nagyméretű HANA-példány egységen, amely DR egységét jelöli. 
 
A legújabb replikált tárhelyet pillanatképek visszaállításához hajtsa végre az alábbi lépéseket: 

1. Állítsa le a nem éles üzemi példány Hana HANA nagyméretű példányok, Ön által futtatott vész-helyreállítási egységen. Ennek oka az, egy előre telepített inaktív HANA éles példánya.
1. Győződjön meg arról, hogy futnak-e egyetlen SAP HANA-folyamat. Ezt az ellenőrzést a következő paranccsal: `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`. A kimenet meg kell jelenítenie a **hdbdaemon** feldolgozása a leállított állapotba, és nincs más HANA folyamatok futó vagy elindított állapotú.
1. A DR hely nagyméretű HANA-példány egységen a szkript végrehajtása *azure_hana_dr_failover.pl*. A parancsfájl által kért egy SAP HANA biztonsági azonosítója, vissza kell állítani. Kérés esetén írja be az egyik vagy az egyetlen SAP HANA biztonsági azonosítója, amely a rendszer replikálta és megőrződik a a *HANABackupCustomerDetails.txt* fájl a nagyméretű HANA-példány egységen a DR-helyen. 

      Több SAP HANA-példányok feladatátvételt szeretne használni, ha több alkalommal a parancsfájl futtatásához szükséges. Kérés esetén írja be a SAP HANA biztonsági azonosító átadja a feladatokat, és állítsa vissza. A művelet befejeződött a parancsfájl a csatlakoztatási pontokról a nagyméretű HANA-példány egység felvett kötetek látható. Ez a lista tartalmazza, valamint a visszaállított DR köteteket.

1. A visszaállított vész-helyreállítási kötet csatlakoztatási parancsokkal Linux operációs rendszer, a nagyméretű HANA-példány egység a vész-helyreállítási helyként. 
1. Indítsa el az inaktív SAP HANA éles üzemelő példányok.
1. Ha úgy döntött, hogy másolja a tranzakciós napló biztonsági mentési naplók csökkenteni az RPO, kell ezeket a tranzakciós napló biztonsági mentéseket egyesítse az újonnan csatlakoztatott DR/hana/logbackups könyvtár. Ne írja felül a meglévő biztonsági másolatok. Másolja az újabb biztonsági mentések, amelyek nem replikálta a legutóbbi replikációs tárolási pillanatkép.
1. A pillanatképek replikálta a /hana/shared/PRD kötetre, a Vészhelyreállítás Azure-régióban egyetlen fájljait is helyreállíthatja. 

A tényleges replikációs kapcsolat befolyásolása nélkül is tesztelheti a Vészhelyreállítási feladatátvétel. Feladatátvételi teszt végrehajtásához kövesse a fenti lépéseket, 1. és 2, és folytassa a 3. a következő lépés.

>[!IMPORTANT]
>Tegye *nem* éles tranzakciók futtassa a folyamatot a DR helyen létrehozott példányon **tesztelési feladatátvétel** a szkript jelent meg a 3. lépésben. Ez a parancs létrehoz egy csoportot kötetek, amelyek semmiféle kapcsolatban nem az elsődleges helyhez. Ennek eredményeképpen a szinkronizálás az elsődleges hely van *nem* lehetséges. 

A feladatátvételi teszthez. 3. lépés:

A DR hely nagyméretű HANA-példány egységen a szkript végrehajtása **azure_hana_test_dr_failover.pl**. Ez a szkript akkor *nem* leállítása folyamatban van a replikációs kapcsolatot az elsődleges hely és a DR-webhely között. Ehelyett ez a szkript a DR köteteinek a Klónozás. Után a Klónozási folyamat sikeres, a klónozott kötetek helyreállítása a legutolsó pillanatfelvétel állapota és a DR egység majd csatlakoztatva. A parancsfájl által kért egy SAP HANA biztonsági azonosítója, vissza kell állítani. Írjon be egy vagy az egyetlen SAP HANA biztonsági azonosítója, amely a rendszer replikálta és megőrződik a a *HANABackupCustomerDetails.txt* fájl a nagyméretű HANA-példány egységen a DR-helyen. 

Több SAP HANA-példányok tesztelni szeretne használni, ha több alkalommal a parancsfájl futtatásához szükséges. Kérés esetén írja be a feladatátvételhez tesztelni kívánt példány SAP HANA biztonsági azonosítója. Befejezéskor a parancsfájl a csatlakoztatási pontok, a nagyméretű HANA-példány egység felvett kötetek listáját tartalmazza. Ez a lista tartalmazza a klónozott DR köteteket is.

Folytassa a 4. lépés.

   >[!NOTE]
   >Ha átadja a feladatokat a DR helyet, hogy néhány adat, amely órája törölték, és a DR-kötetek kell beállítani egy korábbi pillanatkép kell mentési van szüksége, ez az eljárás alkalmazható. 

1. Állítsa le a nem éles üzemi példány Hana HANA nagyméretű példányok, Ön által futtatott vész-helyreállítási egységen. Ennek oka az, egy előre telepített inaktív HANA éles példánya.
1. Győződjön meg arról, hogy futnak-e egyetlen SAP HANA-folyamat. Ezt az ellenőrzést a következő paranccsal: `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`. A kimenet meg kell jelenítenie a **hdbdaemon** feldolgozása a leállított állapotba, és nincs más HANA folyamatok futó vagy elindított állapotú.
1. Határozza meg, melyik pillanatkép neve vagy az SAP HANA biztonsági másolat azonosítója szeretne használni a vész-helyreállítási webhelyként, visszaállítva. A valódi katasztrófa utáni helyreállítás esetben a pillanatkép általában a a legújabb pillanatkép. Ha az elveszett adatok visszaállítására van szüksége, válasszon ki egy korábbi pillanatkép.
1. Lépjen kapcsolatba az Azure-támogatási keresztül magas prioritású támogatási kérést. Kérje meg a visszaállításhoz, a pillanatkép (a név és a pillanatkép) vagy a HANA biztonsági másolat azonosítója a DR-helyen. Az alapértelmezett érték a műveletek oldalon, hogy visszaállítja a/hana/adatmennyiség csak. A/hana/logbackups köteteket is szeretne használni, ha kifejezetten megállapítják, hogy szeretné. *Ne állítsa vissza a /hana/shared kötetet.* Ehelyett megadott fájlok, például global.ini közül kell választania a **.snapshot** könyvtár, illetve annak alkönyvtáraiba, miután újracsatlakoztatása/hana/PRD a megosztott fürtkötet. 

   A műveletek oldalon a következő lépések történnek:

   a. A pillanatképek a termelési kötetről a vész-helyreállítási kötet replikációja le van állítva. Előfordulhat, hogy ez a megszakítás már elvégzett kell elvégeznie a vész-helyreállítási folyamattal okát az éles webhely-e.
   
   b. A storage pillanatkép neve, vagy pillanatkép készítése a biztonsági mentési azonosítójú választotta, a vész-helyreállítási kötetek visszaállítása megtörtént.
   
   c. A visszaállítás után a vész-helyreállítási kötet csatlakoztatnia kell a vészhelyreállítási régióban lévő nagyméretű HANA-példány-egységre érhető el.
      
1. Csatlakoztassa a vész helyreállítási kötetek a nagyméretű HANA-példány egységet a vész-helyreállítási helyként. 
1. Indítsa el az inaktív SAP HANA éles üzemelő példányok.
1. Ha úgy döntött, hogy másolja a tranzakciós napló biztonsági mentési naplók csökkenteni az RPO, kell ezeket a tranzakciós napló biztonsági mentéseket egyesítse az újonnan csatlakoztatott DR/hana/logbackups könyvtár. Ne írja felül a meglévő biztonsági másolatok. Másolja az újabb biztonsági mentések, amelyek nem replikálta a legutóbbi replikációs tárolási pillanatkép.
1. A pillanatképek replikálta a /hana/shared/PRD kötetre, a Vészhelyreállítás Azure-régióban egyetlen fájljait is helyreállíthatja.

A következő feladatütemezési lépéseket foglalja magában, helyreállítás, az SAP HANA éles üzemelő példányok, a storage visszaállított pillanatkép és a tranzakciónapló biztonsági mentései által biztosított alapján:

1. A biztonsági mentési helyének módosításához **/hana/logbackups** SAP HANA Studio használatával.
   ![Módosítsa a biztonsági mentési DR-helyreállítás helyének megadása](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA megvizsgálja a biztonságimásolat-fájl helyeken keresztül, és a legutóbbi tranzakciónapló biztonsági mentését való visszaállításához javasol. Az ellenőrzés eltarthat néhány percig, amíg egy képernyő, például a következő jelenik meg: ![tranzakciónaplók biztonsági mentését a DR-helyreállítás listája](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Módosítsa az alapértelmezett beállításokat:

      - Egyértelmű **használja a különbözeti biztonsági mentések**.
      - Válassza ki **inicializálni a naplózási terület**.

   ![Az inicializálás log terület beállítása](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Válassza a **Finish** (Befejezés) elemet.

   ![A Vészhelyreállítási visszaállítás befejezése](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Egy folyamat ablakban is látható itt, például meg kell jelennie. Ne feledje, hogy a példában azonban egy katasztrófa utáni helyreállítást egy három csomópontos horizontális felskálázás az SAP HANA-konfiguráció.

![Visszaállítási állapota](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Ha a visszaállítás úgy tűnik, hogy időnként a **Befejezés** képernyőn, és nem a folyamat képernyő megjelenítése, győződjön meg arról, hogy a feldolgozó csomópontokon futó SAP HANA-példányok futnak. Ha szükséges, indítsa el manuálisan a SAP HANA-példányokhoz.


### <a name="failback-from-a-dr-to-a-production-site"></a>A Vészhelyreállítás a feladat-visszavétel éles helyhez
A DR ból éles helyhez sikertelen. Tekintsünk meg egy olyan forgatókönyvet, amelyben a feladatátvétel, a vész-helyreállítási webhelyként okozta problémák az éles Azure-régióban, és nem az elveszett adatok helyreállításához szükséges. Ön előzetesen működniük az SAP az éles számítási feladatok egy ideig a vész-helyreállítási helyként. Az élesben használt helyet a problémák megoldásával szeretne feladat-visszavételhez az éles webhelyét. Adatok nem megszakad, mert az élesben használt helyet be újra a lépés magában foglalja a több lépéseket és az SAP HANA az Azure üzemeltetési csapat szorosan együttműködve. Van arra, hogy aktiválja az üzemeltetési csapat a problémák feloldása után vissza az élesben használt helyet a szinkronizálás elindításához.

Ez a lépés a feladatütemezés:

1. Az SAP HANA az Azure üzemeltetési csapat az eseményindító szinkronizálni az éles tárolóköteteket a vész helyreállítási tároló kötetekről, amelyek most már tartalmazzák a állapotig beolvasása. Ebben az állapotban leállítja az élesben használt helyet a nagyméretű HANA-példány egységet.
1. Az SAP HANA az Azure üzemeltetési csapat a replikáció figyeli, és gondoskodik arról, hogy ez történt arról értesíti, mielőtt.
1. Leállítja az alkalmazások, amelyek az üzemi HANA-példány használata a vész-helyreállítási helyként. Ezt követően elvégezheti egy HANA tranzakciónapló biztonsági mentését. Következő lépésként állítsa le a HANA-példány fut a vész-helyreállítási webhelyként nagyméretű HANA-példány egységek.
1. A vész-helyreállítási webhelyként, a nagyméretű HANA-példány egységben futó HANA-példány leáll, miután az üzemeltetési csapat manuálisan szinkronizálja a kötetek újra.
1. Az SAP HANA az Azure üzemeltetési csapat az élesben használt helyet a nagyméretű HANA-példány egység indul újra, és átadja is. Győződjön meg arról, hogy az SAP HANA-példány leállítási állapotban van a nagyméretű HANA-példány egység indításkor.
1. Az azonos adatbázis visszaállítási lépéseket fogja végrehajtani, korábban feladatátvétele a vész-helyreállítási webhelyként hasonló módon.

### <a name="monitor-disaster-recovery-replication"></a>Vész-helyreállítási replikálás figyeléséről

A storage replikáció folyamatának állapotát figyelemmel hajtsa végre a parancsprogramot `azure_hana_replication_status.pl`. Ez a szkript a várt módon működik a vész-helyreállítási hely futó egységből kell futtatni. A parancsfájl működik, függetlenül attól, hogy replikálás aktív-e. A parancsfájlt a vész-helyreállítási hely található a bérlő minden nagyméretű HANA-példány egységéhez. A rendszerindító kötet kapcsolatos részletek beszerzése érdekében nem használható.

Hívja meg a szkript a következő paranccsal:
```
./replication_status.pl <HANA SID>
```

A kimenet van bontásban, kötet, a következő szakaszokra osztottuk:  

- Kapcsolat állapota
- Az aktuális replikációs tevékenység
- Replikált pillanatképe 
- A legújabb pillanatkép mérete
- Aktuális késéssel-pillanatképek közötti (az utolsó befejezett pillanatkép-replikációt között, és most)

A hivatkozás állapota **aktív** , kivéve ha helyek közötti kapcsolat nem működik, vagy nincs az aktuálisan folyamatban lévő feladatátvételi esemény. A replikálási műveletek címek adatok jelenleg éppen replikált vagy inaktív-e, vagy ha más tevékenységek pillanatnyilag folyamatban levő a hivatkozás. A legutolsó pillanatfelvétel replikált csak meg kell jelennie `snapmirror…`. Ekkor megjelenik a legutolsó pillanatfelvétel méretét. Végül a késéssel jelenik meg. Az időbeli idejét a replikáció befejeztével ütemezett replikálás jelöli. Késéssel lehet nagyobb, mint a adatreplikációt, különösen a kezdeti replikációt, és a egy órát, annak ellenére, hogy a replikáció. Az időbeli továbbra is fennáll, növelheti a folyamatban lévő replikáció befejezéséig.

Az alábbiakban látható egy példa a kimenetre:

```
hana_data_hm3_mnt00002_t020_dp
-------------------------------------------------
Link Status: Broken-Off
Current Replication Activity: Idle
Latest Snapshot Replicated: snapmirror.c169b434-75c0-11e6-9903-00a098a13ceb_2154095454.2017-04-21_051515
Size of Latest Snapshot Replicated: 244KB
Current Lag Time between snapshots: -   ***Less than 90 minutes is acceptable***
```












