---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 88b19257a6a7d335e6a928a9eaf7526fbfd5b02e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75942712"
---
## <a name="application-performance-indicators"></a>Alkalmazásteljesítmény-mutatók

Felmérjük, hogy egy alkalmazás jól teljesít-e vagy sem teljesítménymutatókat használ, például azt, hogy egy alkalmazás milyen gyorsan dolgozza fel a felhasználói kérelmet, mennyi adatot dolgoz fel egy alkalmazás kérésenként, hány kérelmet dolgoz fel egy adott alkalmazás egy adott mennyi ideig kell várnia a felhasználónak arra, hogy a kérelem benyújtása után választ kapjon. Ezek a teljesítménymutatók technikai feltételei: IOPS, átviteli vagy sávszélesség, és késés.

Ebben a szakaszban a prémium szintű storage keretében a közös teljesítménymutatókat tárgyaljuk. A következő szakaszban, az alkalmazáskövetelmények összegyűjtése című szakaszban megtudhatja, hogyan mérheti ezeket a teljesítménymutatókat az alkalmazáshoz. Az alkalmazásteljesítmény optimalizálása későbbi részében megismerheti azokat a tényezőket, amelyek befolyásolják ezeket a teljesítménymutatókat és javaslatokat azok optimalizálásához.

## <a name="iops"></a>IOPS

IOPS, vagy bemeneti/kimeneti műveletek másodpercenként, az alkalmazás által küldött kérelmek száma a tárolólemezek egy másodperc alatt. A bemeneti/kimeneti művelet lehet olvasási vagy írási, szekvenciális vagy véletlenszerű. Online tranzakciófeldolgozási (OLTP) alkalmazások, mint például egy online kiskereskedelmi honlapon kell feldolgozni sok egyidejű felhasználói kérelmek azonnal. A felhasználói kérelmek beszúrási és frissítési igényű adatbázis-tranzakciók, amelyeket az alkalmazásnak gyorsan fel kell dolgoznia. Ezért oltp alkalmazások igényel nagyon magas IOPS. Az ilyen alkalmazások több millió kis és véletlenszerű IO-kérelmet kezelnek. Ha rendelkezik ilyen alkalmazással, meg kell terveznie az alkalmazás-infrastruktúrát az IOPS optimalizálásához. A későbbi szakaszban, *Az alkalmazás teljesítményének optimalizálása*, részletesen tárgyaljuk az összes tényezőt, hogy figyelembe kell vennie, hogy a magas IOPS.

Ha prémium szintű tárolót csatlakoztat a nagy méretű virtuális gépéhez, az Azure a lemez specifikációinak megfelelően kiosztja a garantált IOPS-t. A P50-es lemeznél például 7500 IOPS lesz kiosztva. Minden nagy méretű virtuális gép rendelkezik egy IOPS-korláttal is, amelyet még fenn tud tartani. Például egy standard GS5 virtuális gép 80 000 IOPS-korláttal rendelkezik.

## <a name="throughput"></a>Teljesítmény

Átviteli vagy sávszélesség az az adatmennyiség, amelyet az alkalmazás egy adott időszakban küld a tárolólemezekre. Ha az alkalmazás nagy I/O-egységméretekkel rendelkező bemeneti/kimeneti műveleteket hajt végre, nagy átviteli teljesítményt igényel. Az adattárház-alkalmazások általában olyan intenzív vizsgálati műveleteket adnak ki, amelyek egyszerre nagy mennyiségű adathoz férnek hozzá, és általában tömeges műveleteket hajtanak végre. Más szóval az ilyen alkalmazások nagyobb átviteli. Ha rendelkezik ilyen alkalmazással, meg kell terveznie az infrastruktúráját az átviteli hangoptimalizáláshoz. A következő részben részletesen megbeszéljük azokat a tényezőket, amelyeket ennek eléréséhez be kell hangolnia.

Ha egy prémium szintű tárolólemezt csatlakoztat egy nagy méretű virtuális géphez, az Azure az adott lemezspecifikációnak megfelelően korlátozza az átviteli kapacitást. A P50-es lemeznél például a lemez átviteli sebessége 250 MB másodpercenként. Minden nagy méretű virtuális gép rendelkezik egy átviteli sebessége vonatkozó korláttal, amelyet még fenn tud tartani. A Standard GS5 virtuális gép maximális átviteli sebessége például 2000 MB másodpercenként.

Kapcsolat van az átviteli és az IOPS között, ahogy az az alábbi képletben látható.

![Az IOPS és az átviteli arány viszonya](../articles/virtual-machines/linux/media/premium-storage-performance/image1.png)

Ezért fontos meghatározni az optimális átviteli és IOPS-értékeket, amelyeket az alkalmazás igényel. Ahogy megpróbálja optimalizálni az egyik, a másik is lesz érintett. Egy későbbi szakaszban az *Alkalmazásteljesítmény optimalizálása*című szakaszban az IOPS és az átviteli teljesítmény optimalizálásával kapcsolatos további részleteket is megtárgyaljuk.

## <a name="latency"></a>Késés

A késés az az idő, amerre egy alkalmazás egyetlen kérelem fogadásához, a tárolólemezekre való elküldéséhez és a válasz elküldéséhez szükséges. Ez az alkalmazás teljesítményének kritikus mértéke az IOPS és az átviteli teljesítmény mellett. A prémium szintű tárolólemez késése a kérelem információinak lekéréséhez és az alkalmazásnak való visszakereséséhez szükséges idő. A Prémium szintű storage konzisztens, alacsony késéseket biztosít. A prémium szintű lemezek egyszámjegyű ezredmásodperces késéseket biztosítanak a legtöbb I/O-művelethez. Ha engedélyezi a ReadOnly gazdagép gyorsítótárazását a prémium szintű tárolólemezeken, sokkal alacsonyabb olvasási késleltetést kaphat. Megbeszéljük Disk Caching részletesebben a későbbi szakaszban *optimalizálása Alkalmazás teljesítményét*.

Ha az alkalmazás optimalizálása magasabb IOPS és átviteli, hatással lesz az alkalmazás késése. Az alkalmazás teljesítményének finomhangolása után mindig értékelje ki az alkalmazás késését a váratlan, nagy késéses viselkedés elkerülése érdekében.

A felügyelt lemezeken a következő vezérlősík-műveletek a lemez egyik tárolóhelyről a másikra történő áthelyezését foglalhatják magukban. Ez az adatok háttérmásolatával van elhangolva, amely több órát is igénybe vehet, általában kevesebb, mint 24 óra a lemezekben lévő adatok mennyiségétől függően. Ez idő alatt az alkalmazás a szokásosnál nagyobb olvasási késést tapasztalhat, mivel egyes olvasások átirányíthatók az eredeti helyre, és hosszabb időt vehet igénybe. Ebben az időszakban nincs hatással az írási késésre.

- Frissítse a tároló típusát.
- Leválaszthat egy lemezt az egyik virtuális gépről a másikra.
- Felügyelt lemez létrehozása virtuális merevlemezről.
- Felügyelt lemez létrehozása pillanatképből.
- Nem felügyelt lemezek konvertálása felügyelt lemezekké.

## <a name="performance-application-checklist-for-disks"></a>Teljesítményalkalmazás-ellenőrzőlista lemezekhez

Az Azure Premium Storage-on futó nagy teljesítményű alkalmazások tervezésének első lépése az alkalmazás teljesítménykövetelményeinek megértése. Miután összegyűjtötte a teljesítményre vonatkozó követelményeket, optimalizálhatja az alkalmazást a legoptimálisabb teljesítmény elérése érdekében.

Az előző szakaszban ismertetjük a közös teljesítménymutatókat, az IOPS-t, az átviteli teljesítményt és a késést. Meg kell határoznia, hogy ezek közül a teljesítménymutatók közül melyik kritikus fontosságú az alkalmazás számára a kívánt felhasználói élmény biztosításához. Például a magas IOPS a legfontosabb, hogy OLTP-alkalmazások feldolgozása több millió tranzakció egy másodperc alatt. Mivel a nagy átviteli forgalom kritikus fontosságú az adattárház-alkalmazások nagy mennyiségű adatot feldolgozó egy másodperc alatt. Rendkívül alacsony késleltetés elengedhetetlen a valós idejű alkalmazások, mint az élő video streaming honlapok.

Ezután mérje meg az alkalmazás maximális teljesítménykövetelményeit annak teljes élettartama alatt. Kezdetnek használja az alábbi mintaellenőrző listát. Rögzítse a maximális teljesítménykövetelményeket normál, csúcs- és munkaidőn kívüli munkaterhelési időszakokban. Az összes számítási feladat szintjére vonatkozó követelmények azonosításával meghatározhatja az alkalmazás általános teljesítménykövetelményét. Például egy e-kereskedelmi webhely szokásos munkaterhelése az év legtöbb napján kiszokja az általa kitöltött tranzakciókat. A weboldal csúcsterhelése az ünnepi időszakban kiszolgált tranzakciók vagy különleges értékesítési események lesznek. A maximális számítási feladatok általában korlátozott ideig tapasztalhatók, de az alkalmazás nak két vagy több alkalommal kell skáláznia a normál működést. Ismerje meg az 50 percentilis, 90 percentilis és 99 percentilis követelményeket. Ez segít kiszűrni a teljesítménykövetelmények kiugró értékeit, és az erőfeszítéseket a megfelelő értékek optimalizálására összpontosíthatja.

## <a name="application-performance-requirements-checklist"></a>Alkalmazásteljesítmény-követelmények ellenőrzőlistája

| **Teljesítményre vonatkozó követelmények** | **50 százalék** | **90 százalék** | **99 percentilis** |
| --- | --- | --- | --- |
| Legfeljebb Tranzakciók másodpercenként | | | |
| %Read műveletek | | | |
| Írási műveletek %-os | | | |
| % véletlenszerű műveletek | | | |
| % Szekvenciális műveletek | | | |
| Io-kérelem mérete | | | |
| Átlagos átviteli | | | |
| Legfeljebb Teljesítmény | | | |
| Min. Késés | | | |
| Átlagos késés | | | |
| Legfeljebb CPU | | | |
| Átlagos CPU | | | |
| Legfeljebb Memory (Memória) | | | |
| Átlagos memória | | | |
| Várólista mélysége | | | |

> [!NOTE]
> Érdemes ezeket a számokat az alkalmazás várható jövőbeli növekedése alapján skálázhatja. Jó ötlet előre megtervezni a növekedést, mert később nehezebb lehet megváltoztatni az infrastruktúrát a teljesítmény javítása érdekében.

Ha rendelkezik egy meglévő alkalmazással, és át szeretne helyezni a Prémium szintű storage-ba, először hozza létre a fenti ellenőrzőlistát a meglévő alkalmazáshoz. Ezután készítsen egy prototípust az alkalmazás prémium szintű storage-ra, és tervezze meg az alkalmazást az *alkalmazásteljesítmény optimalizálása* című részben leírt irányelvek alapján a jelen dokumentum egy későbbi szakaszában. A következő cikk a teljesítménymérések összegyűjtéséhez használható eszközöket ismerteti.

### <a name="counters-to-measure-application-performance-requirements"></a>Az alkalmazások teljesítménykövetelményeinek mérésére fellépő számlálók

Az alkalmazás teljesítménykövetelményeinek mérésére a legjobb módszer a kiszolgáló operációs rendszere által biztosított teljesítményfigyelő eszközök használata. Használhatja a PerfMon for Windows és iostat for Linux. Ezek az eszközök a fenti szakaszban ismertetett minden egyes intézkedésnek megfelelő számlálókat rögzítek. Ezeknek a számlálóknak az értékeit kell rögzítenie, amikor az alkalmazás normál, csúcs- és munkaidőn kívüli számítási feladatokat futtat.

A PerfMon számlálók a kiszolgáló processzorához, memóriájába, valamint minden logikai lemezére és fizikai lemezére rendelkezésre állnak. Ha prémium szintű tárolólemezeket használ virtuális gépzel, a fizikai lemezszámlálók minden prémium szintű tárolólemezhez tartoznak, a logikai lemezszámlálók pedig a prémium szintű tárolólemezeken létrehozott kötetek. Az alkalmazásszámítási feladatokat üzemeltető lemezek értékeit rögzítenie kell. Ha a logikai és a fizikai lemezek között egy az egyhez hozzárendelés van, akkor a fizikai lemezszámlálókra hivatkozhat; ellenkező esetben a logikai lemezszámlálókra hivatkozik. Linux on a iostat parancs létrehoz egy CPU és lemez kihasználtsági jelentést. A lemezkihasználtsági jelentés fizikai eszközönként vagy partíciónkénti statisztikát tartalmaz. Ha rendelkezik adatbázis-kiszolgálóval, amelynek adatait és naplóit külön lemezeken tartja, gyűjtse össze ezeket az adatokat mindkét lemezről. Az alábbi táblázat a lemezek, processzorok és memória számlálóit ismerteti:

| Számláló | Leírás | Perfmon | Jostat |
| --- | --- | --- | --- |
| **IOPS vagy tranzakciók másodpercenként** |A tárolólemezre kiadott I/O-kérelmek száma másodpercenként. |Lemezolvasás/mp <br> Lemezírás/mp |Tps <br> r/s <br> w/s |
| **Lemezolvasás és -írás** |A lemezen végrehajtott olvasási és írási műveletek %-a. |Lemez olvasási idejének %-a <br> Lemez írási ideje %-os ideje |r/s <br> w/s |
| **Teljesítmény** |A lemezről másodpercenként beolvasott vagy a lemezre írt adatok mennyisége. |Lemez olvasási bájtjai/mp <br> Lemezírási bájtok/mp |kB_read/ek <br> kB_wrtn/ek |
| **Késés** |A lemez I/o-kérésének befejezéséhez szükséges teljes idő. |Átlagos lemez másodperc/olvasás <br> Átlagos lemez másodperc/írás |Várja <br> svctm |
| **Io-méret** |Az I/O-kérelmek mérete problémákat a tárolólemezek. |Átlagos lemezbájt/olvasás <br> Átlagos lemezbájt/írás |avgrq-sz |
| **Várólista mélysége** |A tárolólemezről való olvasásra vagy írásra váró függőben lévő I/O-kérelmek száma. |Az aktuális lemezvárólista hossza |avgqu-sz |
| **Max. Memória** |Az alkalmazás zökkenőmentes futtatásához szükséges memória mennyisége |Előjegyzett memória kihasználtsága (%) |A vmstat használata |
| **Max. Cpu** |Az alkalmazás zökkenőmentes futtatásához szükséges processzor mennyisége |%processzoridő |%util |

További információ az [iostatról](https://linux.die.net/man/1/iostat) és a [PerfMonról.](https://msdn.microsoft.com/library/aa645516.aspx)



## <a name="optimize-application-performance"></a>Az alkalmazások teljesítményének optimalizálása

A prémium szintű storage-on futó alkalmazások teljesítményét befolyásoló fő tényezők az IO-kérelmek jellege, a virtuális gép mérete, a lemezméret, a lemezek száma, a lemezgyorsítótárazás, a többszálas kezelés és a várólista mélysége. Ezeket a tényezőket a rendszer által biztosított gombokkal vezérelheti. Előfordulhat, hogy a legtöbb alkalmazás nem ad lehetőséget az IO méretének és a várólista mélységének közvetlen módosítására. Ha például SQL Servert használ, nem választhatja ki az I/O-méretet és a várólista mélységét. Az SQL Server kiválasztja az optimális I/O-méretet és várólistamélység-értékeket a legnagyobb teljesítmény eléréséhez. Fontos, hogy mindkét típusú tényező hatása az alkalmazás teljesítményére, így a teljesítményigényeknek megfelelő erőforrások kiépítése érdekében.

Ebben a szakaszban tekintse meg az alkalmazás követelmények ellenőrzőlistát, amely a létrehozott, hogy milyen mennyit kell optimalizálni az alkalmazás teljesítményét. Ennek alapján meg tudja határozni, hogy ebből a szakaszból mely tényezőket kell behangolnia. Az egyes tényezők alkalmazásteljesítményre gyakorolt hatásainak tanúi lehetnek, futtasson teljesítményértékelési eszközöket az alkalmazás beállításán. Tekintse meg a teljesítményértékelés cikk, a végén csatolt, a windowsos és linuxos virtuális gépek közös teljesítményértékelési eszközök futtatásának lépéseit.

### <a name="optimize-iops-throughput-and-latency-at-a-glance"></a>Az IOPS, az átviteli képesség és a késés optimalizálása egy pillantással

Az alábbi táblázat összefoglalja a teljesítménytényezőket és az IOPS, az átviteli teljesítmény és a késés optimalizálásához szükséges lépéseket. Az összefoglalót követő szakaszok az egyes tényezőket sokkal mélyebben írják le.

A virtuális gépek méretéről és az IOPS-ról, az átviteli és a késési szintről az egyes virtuális gépek típusaihoz elérhető további információkért lásd: [Linux virtuális gép méretek](../articles/virtual-machines/linux/sizes.md) vagy [Windows virtuális gépek méretei.](../articles/virtual-machines/windows/sizes.md)

| &nbsp; | **IOPS** | **Teljesítmény** | **Késés** |
| --- | --- | --- | --- |
| **Példaforgatókönyv** |Vállalati OLTP alkalmazás, amely nagyon magas másodpercenkénti tranzakciókat igényel. |Nagy mennyiségű adatot feldolgozó vállalati adattárház-alkalmazás. |Közel valós idejű alkalmazások, amelyek azonnali választ igényelnek a felhasználói kérésekre, mint például az online játék. |
| Teljesítménytényezők | &nbsp; | &nbsp; | &nbsp; |
| **Io-méret** |A kisebb I/O-méret magasabb IOPS-t eredményez. |A nagyobb I/O-méret nagyobb átviteli kapacitást eredményez. | &nbsp;|
| **Virtuális gép mérete** |Használjon olyan virtuális gépméretet, amely az alkalmazáskövetelménynél nagyobb IOPS-t kínál. |Használjon virtuális gép mérete átviteli korlát nagyobb, mint az alkalmazás követelménye. |Használjon olyan virtuális gépméretet, amely az alkalmazáskövetelménynél nagyobb méretezési korlátokat kínál. |
| **Lemezméret** |Használjon olyan lemezméretet, amely az alkalmazáskövetelménynél nagyobb IOPS-t kínál. |Használjon olyan lemezméretet, amelynek átviteli határa nagyobb, mint az alkalmazás követelménye. |Használjon olyan lemezméretet, amely az alkalmazáskövetelménynél nagyobb méretezési korlátokat kínál. |
| **Virtuális gép és lemezméretezési korlátok** |A kiválasztott virtuális gép méretének IOPS-korlátjának nagyobbnak kell lennie, mint a hozzá csatlakoztatott tárolólemezek által vezérelt teljes IOPS-korlátnak. |A kiválasztott virtuális gép méretének átviteli korlátjának nagyobbnak kell lennie, mint a hozzá csatlakoztatott prémium szintű tárolólemezek által hajtott teljes átviteli kapacitásnak. |A kiválasztott virtuális gép méretének korlátok nagyobbnak kell lennie, mint a csatlakoztatott prémium szintű tárolólemezek teljes méretezési korlát. |
| **Lemezgyorsítótárazás** |Engedélyezze a csak olvasható gyorsítótárat a magas szintű tárolólemezeken a nagy olvasási műveletekkel, hogy magasabb olvasási IOPS-t kapjon. | &nbsp; |Engedélyezze a csak olvasható gyorsítótárat a ready heavy műveletekkel rendelkező, csak a kész tárolólemezeken, hogy nagyon alacsony olvasási késéseket kapjon. |
| **Lemezcsíkozás** |Használjon több lemezt, és csíkozni őket együtt, hogy egy kombinált magasabb IOPS és átviteli korlát. A virtuális gépre vonatkozó kombinált korlátnak magasabbnak kell lennie, mint a csatlakoztatott prémium szintű lemezek kombinált korlátjainak. | &nbsp; | &nbsp; |
| **Csíkos méret** |Kisebb csíkméret az OLTP alkalmazásokban látható véletlenszerű kis I/O-mintához. Az SQL Server OLTP alkalmazáshoz például használjon 64 KB-os csíkméretet. |Nagyobb csíkméret a data warehouse-alkalmazásokban látható szekvenciális nagy I/O-mintához. Például használja a 256 KB-os csíkméretet az SQL Server Data raktáralkalmazáshoz. | &nbsp; |
| **Több szál használata** |A többszálas ítás használatával nagyobb számú kérelmet küldhet a prémium szintű storage-ba, ami magasabb IOPS- és átviteli csatornázáshoz vezet. Az SQL Server en például magas MAXDOP-értéket állíthat be, hogy több processzort foglaljon le az SQL Server számára. | &nbsp; | &nbsp; |
| **Várólista mélysége** |A nagyobb várólistamélység magasabb IOPS-t eredményez. |A nagyobb várólistamélység nagyobb átviteli kapacitást eredményez. |A kisebb várólistamélység alacsonyabb késéseket eredményez. |

## <a name="nature-of-io-requests"></a>Az IO-kérelmek jellege

Az IO-kérelem az alkalmazás által végrehajtandó bemeneti/kimeneti művelet egy egysége. Az IO-kérelmek jellegének azonosítása, véletlenszerű vagy szekvenciális, olvasási vagy írási, kicsi vagy nagy, segít meghatározni az alkalmazás teljesítménykövetelményeit. Fontos megérteni az IO-kérések jellegét, hogy az alkalmazás-infrastruktúra tervezésekor helyes döntéseket hozzon. A lehető legjobb teljesítmény elérése érdekében a iro-kat egyenletesen kell elosztani.

Az IO mérete az egyik legfontosabb tényező. Az Io-méret az alkalmazás által létrehozott bemeneti/kimeneti műveletkérelem mérete. Az I/O-méret jelentős hatással van a teljesítményre, különösen az IOPS és a sávszélesség, amely az alkalmazás képes elérni. A következő képlet az IOPS, az IO-méret és a sávszélesség/átviteli érték közötti kapcsolatot mutatja be.  
    ![](media/premium-storage-performance/image1.png)

Egyes alkalmazások lehetővé teszik az I/O-méretük módosítását, míg egyes alkalmazások nem. Például az SQL Server határozza meg az optimális I/O-méretet, és nem biztosít a felhasználók számára egyetlen gombot sem a módosításhoz. Másrészt az Oracle egy [DB\_BLOCK\_SIZE](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) nevű paramétert biztosít, amellyel konfigurálhatja az adatbázis I/O-kérelem méretét.

Ha olyan alkalmazást használ, amely nem teszi lehetővé az I/O-méret módosítását, használja a cikkben található irányelveket az alkalmazás szempontjából leginkább releváns kpi teljesítmény optimalizálásához. Például:

* Az OLTP-alkalmazások több millió kis és véletlenszerű IO-kérelmet hoznak létre. Az ilyen típusú I/O-kérelmek kezeléséhez meg kell terveznie az alkalmazás-infrastruktúrát, hogy magasabb IOPS-t kapjon.  
* Egy adattárház-alkalmazás nagy és szekvenciális I/O-kérelmeket hoz létre. Az ilyen típusú I/O-kérelmek kezeléséhez meg kell terveznie az alkalmazás infrastruktúráját, hogy nagyobb sávszélességet vagy átviteli képest kapjon.

Ha olyan alkalmazást használ, amely lehetővé teszi az IO méretének módosítását, használja ezt az ökölszabályt az IO mérethez az egyéb teljesítményirányelvek mellett,

* Kisebb I/O-méret a magasabb IOPS.S maller IO size to get higher IOPS. Például 8 KB egy OLTP alkalmazás.  
* Nagyobb I/O-méret a nagyobb sávszélesség/átviteli arány eléréséhez. Például 1024 KB egy adattárház-alkalmazáshoz.

Íme egy példa, hogyan számíthatja ki az IOPS és az átviteli/sávszélesség az alkalmazás. Fontolja meg egy p30-lemezt használó alkalmazást. A P30-lemez maximális IOPS- és átviteli/sávszélesség-értéke 5000 IOPS, illetve 200 MB másodpercenként. Most, ha az alkalmazás megköveteli a maximális IOPS a P30-lemezről, és egy kisebb I/s, mint 8 KB, az eredményül kapott sávszélesség lesz képes-hoz kap a 40 MB másodpercenként. Ha azonban az alkalmazás a P30-lemez maximális átviteli/sávszélesség-igényes, és nagyobb I/O-méretet használ, például 1024 KB-t, az eredményül kapott IOPS kevesebb lesz, 200 IOPS. Ezért állítsa be az I/O-méretet úgy, hogy az megfeleljen az alkalmazás IOPS és átviteli/sávszélesség követelményének. Az alábbi táblázat összefoglalja a különböző I/O-méreteket és a p30-lemez hez tartozó IOPS-t és átviteli mertét.

| Alkalmazási követelmény | I/O-méret | IOPS | Átviteli/sávszélesség |
| --- | --- | --- | --- |
| Maximális IOPS-érték |8 KB |5000 |40 MB másodpercenként |
| Maximális átviteli sebesség |1024 KB |200 |200 MB másodpercenként |
| Maximális átviteli sebesség + magas IOPS |64 KB |3,200 |200 MB másodpercenként |
| Max IOPS + nagy átviteli sebesség |32 KB |5000 |160 MB másodpercenként |

Ha egyetlen prémium szintű tárolólemez maximális értékét szeretné magasabbra tenni az IOPS és a Sávszélesség, használjon több prémium szintű lemezt, amelyek et egymásba vannak csíkozottak. Például csík két P30 lemezek egy kombinált IOPS 10 000 IOPS vagy kombinált átviteli sebesség 400 MB másodpercenként. A következő szakaszban leírtak szerint olyan virtuális gépméretet kell használnia, amely támogatja a kombinált lemez IOPS-t és átviteli mertét.

> [!NOTE]
> Az IOPS vagy az átviteli mertéteszi a másik is növekszik, győződjön meg róla, hogy nem éri el a lemez vagy a virtuális gép átviteli vagy IOPS-korlátait, ha egyiket növeli.

Az Io-méret az alkalmazás teljesítményére gyakorolt hatások, teljesítményértékelési eszközöket futtathat a virtuális gép és a lemezek. Hozzon létre több tesztfuttatást, és használjon különböző I/O-méretet minden futtatáshoz a hatás megtekintéséhez. További részletekért tekintse meg a záródás című összehasonlító cikket.

## <a name="high-scale-vm-sizes"></a>Nagy méretű virtuális gép méretek

Amikor elkezdi egy alkalmazás tervezését, az egyik első teendő, válasszon egy virtuális gép az alkalmazás üzemeltetéséhez. A prémium szintű storage nagy méretű virtuálisgép-méretekkel rendelkezik, amelyek nagyobb számítási teljesítményt és magas helyi lemez I/O-teljesítményt igénylő alkalmazásokat képesek futtatni. Ezek a virtuális gépek gyorsabb processzorokat, nagyobb memória-mag arányt és ssd-meghajtót (SSD) biztosítanak a helyi lemez számára. Példák a prémium szintű storage-t támogató nagy méretű virtuális gépekre a DS- és GS-sorozatú virtuális gépek.

A nagy méretű virtuális gépek különböző méretekben érhetők el, különböző számú PROCESSZORmaggal, memóriával, operációs rendszerrel és ideiglenes lemezmérettel. Minden virtuális gép mérete is rendelkezik a virtuális géphez csatolható adatlemezek maximális száma. Ezért a kiválasztott virtuális gép mérete befolyásolja, hogy mennyi feldolgozási, memória- és tárolókapacitás áll rendelkezésre az alkalmazás számára. A számítási és tárolási költségeket is befolyásolja. Az alábbiakban például a DS-sorozat és a GS-sorozat legnagyobb virtuálisgép-méretének specifikációi láthatók:

| Virtuális gép mérete | Processzormagok | Memory (Memória) | Virtuális gép lemezméretei | Legfeljebb adatlemezek | Gyorsítótár mérete | IOPS | Sávszélesség-gyorsítótár i/o-korlátai |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS14 |16 |112 GB |OS = 1023 GB <br> Helyi SSD = 224 GB |32 |576 GB |50 000 IOPS <br> 512 MB másodpercenként |4000 IOPS és 33 MB másodpercenként |
| Standard_GS5 |32 |448 GB |OS = 1023 GB <br> Helyi SSD = 896 GB |64 |4224 GB |80 000 IOPS <br> 2000 MB másodpercenként |5000 IOPS és 50 MB másodpercenként |

Az összes elérhető Azure virtuálisgép-méret teljes listájának megtekintéséhez tekintse meg a [Windows virtuálisgép-vagy](../articles/virtual-machines/windows/sizes.md) [Linux-virtuálisgép-méreteket.](../articles/virtual-machines/linux/sizes.md) Válasszon olyan virtuális gépméretet, amely megfelel, és a kívánt alkalmazás teljesítménykövetelményeihez skálázható. Ezen kívül vegye figyelembe a következő fontos szempontokat a virtuális gép méretének kiválasztásakor.

*Méretezési korlátok*  
A virtuális gépenként és lemezenként iops-korlátok száma eltérő és egymástól független. Győződjön meg arról, hogy az alkalmazás a virtuális gép és a hozzá csatlakoztatott prémium szintű lemezek határain belül iops-t vezet. Ellenkező esetben az alkalmazás teljesítménye szabályozást tapasztal.

Tegyük fel például, hogy egy alkalmazáskövetelmény legfeljebb 4000 IOPS.In example, tlet a application requirement is a maximum of 4,000 IOPS. Ennek elérése érdekében egy P30 lemezt létesíteni egy DS1 virtuális gépen. A P30 lemez akár 5000 IOPS-t is képes biztosítani. A DS1 virtuális gép azonban 3200 IOPS-ra korlátozódik. Következésképpen az alkalmazás teljesítményét a virtuális gép 3200 IOPS-kor korlátozza, és a teljesítmény lesz csökkent. A helyzet elkerülése érdekében válasszon egy virtuális gép és a lemez mérete, amely megfelel az alkalmazás követelményeinek.

*A művelet költsége*  
Sok esetben lehetséges, hogy a prémium szintű storage használatával végzett teljes működési költség alacsonyabb, mint a Standard Storage használata.

Vegyünk például egy 16 000 IOPS-t igénylő alkalmazást. A teljesítmény eléréséhez szüksége lesz\_egy szabványos D14-es Azure IaaS virtuális gépre, amely 32 szabványos tárterület1 TB-os lemez használatával legfeljebb 16 000 IOPS-t tud biztosítani. Minden 1 TB-os szabványos tárolólemez legfeljebb 500 IOPS-t érhet el. A virtuális gép becsült költsége havonta $1,570 lesz. A havi költsége 32 szabványos tárolólemezek lesz $ 1,638. A becsült teljes havi költség 3208 dollár lesz.

Ha azonban ugyanazt az alkalmazást üzemelteti a prémium szintű storage-ban, kisebb virtuális gépméretre és kevesebb prémium szintű tárolólemezre lesz szüksége, így csökkentve a teljes költséget. A\_standard DS13 virtuális gép négy P30-lemez használatával megfelel a 16 000 IOPS-követelménynek. A DS13 virtuális gép maximális IOPS-25 600 és minden P30-lemez maximális IOPS 5000. Összességében ez a konfiguráció 5000 x 4 = 20 000 IOPS érhető el. A virtuális gép becsült költsége havonta $1,003 lesz. A négy P30 prémium szintű tárolólemez havi költsége $544.34 lesz. A becsült teljes havi költség 1544 usd lesz.

Az alábbi táblázat összefoglalja a standard és prémium szintű storage-hoz ebben a forgatókönyvben szereplő költségbontást.

| &nbsp; | **Standard** | **Prémium** |
| --- | --- | --- |
| **A virtuális gép költsége havonta** |$1,570.58 (Normál\_D14) |$1,003.66 (normál\_DS13) |
| **A lemezek költsége havonta** |$1,638.40 (32 x 1-TB lemezek) |$544.34 (4 x P30 lemezek) |
| **Teljes havi költség** |$3,208.98 |$1,544.34 |

*Linux disztribúciók*  

Az Azure Premium Storage-szal ugyanolyan szintű teljesítményt kap a Windows és Linux futó virtuális gépekhez. Támogatjuk sok ízek Linux disztribúciók, és láthatjuk a teljes listát [itt](../articles/virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Fontos megjegyezni, hogy a különböző distros jobban megfelelnek a különböző típusú számítási feladatok. Látni fogja a különböző szintű teljesítmény attól függően, hogy a számítási feladatok futtatása a distro.You will see different levels of performance depending on the distro your workload is running on. Tesztelje a Linux disztribúciók az alkalmazás, és válassza ki az egyik, hogy működik a legjobban.

Ha Prémium szintű tárral futtatja a Linuxot, a nagy teljesítmény biztosítása érdekében ellenőrizze a szükséges illesztőprogramokkal kapcsolatos legújabb frissítéseket.

## <a name="premium-storage-disk-sizes"></a>Prémium szintű tárolólemezméretek

Az Azure Premium Storage különböző méreteket kínál, így kiválaszthatja az igényeinek leginkább megfelelőt. Minden lemezméret különböző méretezési korláttal rendelkezik az IOPS, a sávszélesség és a tárhely számára. Válassza ki a megfelelő prémium szintű tárolólemez méretét az alkalmazás követelményeitől és a nagy méretű virtuális gép méretétől függően. Az alábbi táblázat a lemezek méretét és képességeit mutatja. A P4, P6, P15, P60, P70 és P80 méretek jelenleg csak a felügyelt lemezek esetében támogatottak.

[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

A választott lemezek közül hány a kiválasztott lemez méretétől függ. Egyetlen P50-lemezt vagy több P10 lemezt is használhat az alkalmazás követelményének teljesítéséhez. A választás során vegye figyelembe az alábbiakban felsorolt szempontokat.

*Méretezési korlátok (IOPS és átviteli érték)*  
Az Egyes Prémium szintű lemezméretek IOPS- és átviteli-átviteli korlátja eltér a virtuális gép méretezési korlátjától. Győződjön meg arról, hogy a teljes IOPS és a lemezek átviteli keresztülad a kiválasztott virtuális gép méretének határain belül vannak.

Ha például egy alkalmazáskövetelmény legfeljebb 250 MB/mp átviteli sebesség, és egyetlen P30 lemezzel rendelkező DS4 virtuális gép használata. A DS4 virtuális gép legfeljebb 256 MB/s átviteli sebesség. Egyetlen P30-lemez átviteli sebességhatára azonban 200 MB/mp. Ennek következtében az alkalmazás a lemezkorlát miatt 200 MB/s-os korlátozással lesz korlátozva. A korlát leküzdése érdekében több adatlemezek kiépítése a virtuális gép, vagy átméretezni a lemezeket P40 vagy P50.

> [!NOTE]
> A gyorsítótár által kiszolgált olvasások nem szerepelnek a lemez IOPS és átviteli, ezért nem vonatkoznak a lemez korlátok. A gyorsítótár rendelkezik a virtuális gépenkénti külön IOPS- és átviteli-átviteli korláttal.
>
> Például kezdetben az olvasási és írási műveletek 60MB/sec, illetve 40MB/s. Idővel a gyorsítótár bemelegszik, és egyre több olvasást szolgál ki a gyorsítótárból. Ezután nagyobb írási átviteli áteresztőát kaphat a lemezről.

*Lemezek száma*  
Az alkalmazáskövetelményeinek felmérésével határozza meg a szükséges lemezek számát. Minden virtuális gép mérete is rendelkezik egy korlátot a virtuális géphez csatolható lemezek száma. Ez általában kétszer annyi mag, mint a magok száma. Győződjön meg arról, hogy a virtuális gép által választott méret támogatja a szükséges lemezek számát.

Ne feledje, hogy a prémium szintű storage-lemezek nagyobb teljesítményképességekkel rendelkeznek, mint a standard tárolólemezek. Ezért ha az azure IaaS virtuális gépről a Standard Storage használatával telepíti át az alkalmazást prémium szintű storage-ba, valószínűleg kevesebb prémium szintű lemezre lesz szüksége az alkalmazás azonos vagy nagyobb teljesítményének eléréséhez.

## <a name="disk-caching"></a>Lemez gyorsítótárazása

Az Azure Premium Storage-t kihasználó nagy méretű virtuális gépek egy többrétegű gyorsítótárazási technológiával rendelkeznek, amelyet BlobCache-nek hívnak. A BlobCache a virtuális gép RAM és a helyi SSD kombinációját használja a gyorsítótárazáshoz. Ez a gyorsítótár érhető el a prémium szintű storage állandó lemezek és a virtuális gép helyi lemezek. Alapértelmezés szerint ez a gyorsítótár-beállítás írás/írás az operációs rendszer lemezeihez és csak a Csak olvasása a prémium szintű storage-ban tárolt adatlemezekhez. Ha a lemezgyorsítótárazás engedélyezve van a prémium szintű tárolólemezeken, a nagy méretű virtuális gépek rendkívül magas, az alapul szolgáló lemezteljesítményt meghaladó teljesítményt érhetnek el.

> [!WARNING]
> A lemezgyorsítótárazás nem támogatott a 4 TiB és nagyobb lemezek esetében. Ha több lemez van csatlakoztatva a virtuális géphez, minden 4 TiB-nál kisebb lemez támogatja a gyorsítótárazást.
>
> Egy Azure-beli lemez gyorsítótár-beállításainak módosításakor a céllemez le lesz választva, és újra lesz csatlakoztatva. Ha ez az operációs rendszer lemeze, a virtuális gép újraindul. A lemezgyorsítótár beállításainak módosítása előtt állítson le minden olyan alkalmazást és szolgáltatást, amelyet ez a megszakítás érinthet.

Ha többet szeretne megtudni a BlobCache működéséről, tekintse meg az Inside [Azure Premium Storage](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/) blogbejegyzést.

Fontos, hogy a gyorsítótárat a megfelelő lemezkészleten engedélyezze. Az, hogy engedélyezi-e a lemezgyorsítótárazást egy prémium szintű lemezen, vagy sem, attól függ, hogy a lemez hogyan kezeli a számítási feladatok at. Az alábbi táblázat az operációs rendszer és az adatlemezek alapértelmezett gyorsítótár-beállításait mutatja be.

| **Lemez típusa** | **Alapértelmezett gyorsítótár-beállítás** |
| --- | --- |
| Operációsrendszer-lemez |ReadWrite |
| Adatlemez |ReadOnly |

Az alábbiakban az adatlemezekhez ajánlott lemezgyorsítótár-beállításokat,

| **Lemezgyorsítótár-beállítások** | **javaslat a beállítás használatára** |
| --- | --- |
| None |Konfigurálja a gazdagyorsítótárat Nincs ként írási és írási nehéz lemezekesetén. |
| ReadOnly |Konfigurálja a gazdagyorsítótárat írásvédettként írásvédett és írás-író lemezekhez. |
| ReadWrite |A host-cache-t csak akkor konfigurálja readwrite-ként, ha az alkalmazás megfelelően kezeli a gyorsítótárazott adatok írását az állandó lemezekre, ha szükséges. |

*ReadOnly*  
A ReadOnly gyorsítótárazás konfigurálásával a prémium szintű storage-adatlemezeken alacsony olvasási késleltetést érhet el, és nagyon magas olvasási IOPS-t és átviteli kapacitást kaphat az alkalmazáshoz. Ennek két oka van:

1. A virtuális gép memóriájában és a helyi SSD-n lévő gyorsítótárból végrehajtott olvasások sokkal gyorsabbak, mint az azure blobstorage-on található adatlemezről történő olvasás.  
1. Prémium szintű storage nem számít a gyorsítótárból kiszolgált olvasások a lemez IOPS és átviteli. Ezért az alkalmazás képes elérni a magasabb teljes IOPS és átviteli.

*ReadWrite*  
Alapértelmezés szerint az operációs rendszer lemezein engedélyezve van az Olvasási gyorsítótár. Nemrég hozzáadott támogatása ReadWrite gyorsítótárazás adatlemezeken is. Ha ReadWrite gyorsítótárazást használ, megfelelő módon kell írnia az adatokat a gyorsítótárból az állandó lemezekre. Az SQL Server például saját maga kezeli a gyorsítótárazott adatok írását az állandó tárolólemezekre. ReadWrite cache használata egy olyan alkalmazással, amely nem kezeli a szükséges adatok at, adatvesztést okozhat, ha a virtuális gép összeomlik.

*Nincs*  
Jelenleg a **None** csak adatlemezeken támogatott. Operációs rendszer lemezeken nem támogatott. Ha a **Nincs** lehetőséget állítja be egy operációs rendszerlemezre, akkor belsőleg felülbírálja ezt, és readonly -ra **állítja.**

Ezeket az irányelveket például a prémium szintű storage-on futó SQL Serverre alkalmazhatja a következő módon:

1. Konfigurálja a "Csak olvasható" gyorsítótárat az adatfájlokat tároló prémium szintű tárolólemezeken.  
   a.  A gyorsítótárból érkező gyors olvasások csökkentik az SQL Server lekérdezési idejét, mivel az adatlapok beolvasása sokkal gyorsabban történik a gyorsítótárból, mint közvetlenül az adatlemezekről.  
   b.  A gyorsítótárból történő olvasások kiszolgálása azt jelenti, hogy további átviteli igény érhető el a prémium szintű adatlemezekről. Az SQL Server ezt a további átviteli forgalmat további adatlapok és egyéb műveletek, például biztonsági mentés/visszaállítás, kötegelt betöltések és index-újraépítés lekéréséhez használhatja.  
1. Konfigurálja a "Nincs" gyorsítótárat a naplófájlokat tároló prémium szintű tárolólemezeken.  
   a.  A naplófájlok elsősorban írási műveleteket tartalmaznak. Ezért nem részesülnek a csak olvasható gyorsítótár előnyeiből.

## <a name="optimize-performance-on-linux-vms"></a>A teljesítmény optimalizálása Linuxos virtuális gépeken

Minden prémium szintű SSD vagy ultra lemez gyorsítótár beállítása **ReadOnly** vagy **Nincs**, le kell tiltania a "korlátok", amikor csatlakoztatja a fájlrendszer. Ebben a forgatókönyvben nincs szükség akadályokra, mert a prémium szintű tárolólemezekre történő írások tartósak ezekhez a gyorsítótár-beállításokhoz. Ha az írási kérelem sikeresen befejeződik, az adatok az állandó tárolóba lettek írva. Az "akadályok" letiltásához használja az alábbi módszerek egyikét. Válassza ki a fájlrendszeréhez való t:
  
* A **reiserFS**, akadályok letiltásához használja a `barrier=none` csatlakoztatási lehetőséget. (Akadályok engedélyezéséhez használja `barrier=flush`a .)
* Az **ext3/ext4**, a korlátok `barrier=0` letiltásához használja a csatlakoztatási lehetőséget. (Akadályok engedélyezéséhez használja `barrier=1`a .)
* Az **XFS**, a korlátok `nobarrier` letiltásához használja a csatlakoztatási lehetőséget. (Akadályok engedélyezéséhez használja `barrier`a .)
* A **readwrite**gyorsítótárral rendelkező prémium szintű tárolólemezek esetében engedélyezze az írási tartósság akadályait.
* Ahhoz, hogy a kötetcímkék a virtuális gép újraindítása után is megmaradjanak, frissítenie kell az /etc/fstab kapcsolót a lemezekre mutató univerzálisan egyedi azonosító (UUID) hivatkozásokkal. További információ: [Felügyelt lemez hozzáadása Linux os virtuális géphez.](../articles/virtual-machines/linux/add-disk.md)

A következő Linux-disztribúciók prémium SSD-kre lettek érvényesítve. A prémium szintű SSD-k jobb teljesítmény és stabilitás érdekében azt javasoljuk, hogy frissítse a virtuális gépeket ezen verziók egyikére vagy újabb verzióra. 

Egyes verziók az Azure-hoz a legújabb Linux-integrációs szolgáltatásokat (LIS) igénylik. A disztribúció letöltéséhez és telepítéséhez kövesse az alábbi táblázatban található hivatkozást. Hozzáadjuk a képeket a listához, amint befejeztük az ellenőrzést. Az ellenőrzéseink azt mutatják, hogy a teljesítmény az egyes képekhez képest változik. A teljesítmény a számítási feladatok jellemzőitől és a képbeállításoktól függ. A különböző lemezképek különböző típusú számítási feladatokhoz vannak hangolva.

| Disztribúció | Verzió | Támogatott kernel | Részletek |
| --- | --- | --- | --- |
| Ubuntu | 12.04 vagy újabb| 3.2.0-75.110+ | &nbsp; |
| Ubuntu | 14.04 vagy újabb| 3.13.0-44.73+  | &nbsp; |
| Debian | 7.x, 8.x vagy újabb| 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12 vagy újabb| 3.12.36-38.1+ | &nbsp; |
| SUSE | SLES 11 SP4 vagy újabb| 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+ vagy újabb| 3.18.4+ | &nbsp; |
| CentOS | 6.5, 6.6, 6.7, 7.0 vagy újabb| &nbsp; | [LIS4 szükséges](https://www.microsoft.com/download/details.aspx?id=55106) <br> *Lásd a megjegyzést a következő szakaszban* |
| CentOS | 7.1+ vagy újabb| 3.10.0-229.1.2.el7+ | [LIS4 ajánlott](https://www.microsoft.com/download/details.aspx?id=55106) <br> *Lásd a megjegyzést a következő szakaszban* |
| Red Hat Enterprise Linux (RHEL) | 6.8+, 7.2+, vagy újabb | &nbsp; | &nbsp; |
| Oracle | 6.0+, 7.2+, vagy újabb | &nbsp; | UEK4 vagy RHCK |
| Oracle | 7.0-7.1 vagy újabb | &nbsp; | UEK4 vagy RHCK w/[LIS4](https://www.microsoft.com/download/details.aspx?id=55106) |
| Oracle | 6.4-6.7 vagy újabb | &nbsp; | UEK4 vagy RHCK w/[LIS4](https://www.microsoft.com/download/details.aspx?id=55106) |

### <a name="lis-drivers-for-openlogic-centos"></a>LIS-illesztőprogramok az OpenLogic CentOS-hoz

Ha OpenLogic CentOS virtuális gépeket futtat, futtassa a következő parancsot a legújabb illesztőprogramok telepítéséhez:

```
sudo yum remove hypervkvpd  ## (Might return an error if not installed. That's OK.)
sudo yum install microsoft-hyper-v
sudo reboot
```

Bizonyos esetekben a fenti parancs frissíti a kernelt is. Ha kernelfrissítésre van szükség, akkor előfordulhat, hogy a microsoft-hyper-v csomag teljes telepítéséhez újraindítás után újra futtatnia kell a fenti parancsokat.


## <a name="disk-striping"></a>Lemezcsíkozás

Ha egy nagy méretű virtuális gép több prémium szintű tárolási állandó lemezzel van csatlakoztatva, a lemezek csíkot fűzhetnek össze az IPS- ek, a sávszélesség és a tárolókapacitás összesítéséhez.

A Windows rendszerben a Tárolóhelyek segítségével a lemezeket együtt csíkolhatja. A készlet minden lemezéhez egy oszlopot kell konfigurálnia. Ellenkező esetben a csíkozott kötet általános teljesítménye a vártnál alacsonyabb lehet a lemezek közötti forgalom egyenetlen eloszlása miatt.

Fontos: A Kiszolgálókezelő felhasználói felületének használatával a csíkozott kötetek oszlopainak teljes számát legfeljebb 8-ra állíthatja be. Ha nyolcnál több lemezt csatol, a PowerShell segítségével hozza létre a kötetet. A PowerShell használatával beállíthatja a lemezek számával megegyező oszlopok számát. Ha például 16 lemez van egy csíkkészletben; Adjon meg 16 oszlopot az *Új VirtualDisk* PowerShell parancsmag *NumberOfColumns* paraméterében.

Linux on, használja a MDADM segédprogram csík lemezek együtt. A linuxos lemezek csíkozásának részletes lépéseit a [Szoftveres RAID konfigurálása Linuxon](../articles/virtual-machines/linux/configure-raid.md)című dokumentumban található.

*Csíkos méret*  
A lemezcsíkozás egyik fontos konfigurációja a csíkméret. A csíkméret vagy a blokkméret az a legkisebb adattömb, amelyet az alkalmazás egy csíkozott köteten kezelhet. A konfigurált csíkméret az alkalmazás típusától és a kérelemmintájától függ. Ha nem a megfelelő csíkméretet választja, az io-eltolódáshoz vezethet, ami az alkalmazás teljesítményének leromlott állapotához vezethet.

Ha például az alkalmazás által létrehozott Io-kérelem nagyobb, mint a lemezcsík mérete, a tárolórendszer több lemezen írja azt a csíkegységek határain keresztül. Amikor az adatokhoz való hozzáférés ideje van, a kérelem teljesítéséhez több csíkegységet kell keresnie. Az ilyen viselkedés kumulatív hatása jelentős teljesítménycsökkenéshez vezethet. Másrészt, ha az IO-kérelem mérete kisebb, mint a csík mérete, és ha véletlenszerű jellegű, az IO-kérelmek összeadódhatnak ugyanazon a lemezen, ami szűk keresztmetszetet okoz, és végső soron rontja az IO teljesítményét.

Attól függően, hogy milyen típusú számítási feladatok az alkalmazás fut, válassza ki a megfelelő csíkméretet. Véletlenszerű kis I/O-kérelmek esetén használjon kisebb csíkméretet. Míg a nagy szekvenciális I/O-kérelmek használata nagyobb csík mérete. Ismerje meg a csík méret re vonatkozó javaslatokat az alkalmazás lesz fut a prémium szintű storage.Ismerje meg a csík mérete ajánlásokat az alkalmazás lesz fut a prémium szintű storage.Ismerje meg a csík mérete ajánlásokat az alkalmazás lesz fut a prémium szintű storage.Ismerje meg a csík mérete Az SQL Server esetében konfigurálja a 64 KB-os csíkméretet az OLTP-számítási feladatokhoz, és 256 KB-ot az adatraktározási számítási feladatokhoz. További információ: [Az SQL Server teljesítményre vonatkozó gyakorlati tanácsai az Azure-beli virtuális gépeken](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md#disks-guidance) című témakörben olvashat.

> [!NOTE]
> Legfeljebb 32 prémium szintű tárolólemez csíkozása egy DS sorozatú virtuális gépen és 64 prémium szintű tárolólemez egy GS sorozatú virtuális gépen.

## <a name="multi-threading"></a>Többszálas

Az Azure úgy tervezte meg a Premium Storage platformot, hogy masszívan párhuzamos legyen. Ezért egy többszálas alkalmazás sokkal nagyobb teljesítményt ér el, mint egyszálas alkalmazás. A többszálas alkalmazások felosztják a feladatokat több szálközött, és növeli a végrehajtás hatékonyságát a virtuális gép és a lemez erőforrásainak maximális kihasználásával.

Ha például az alkalmazás két szálat használó egyetlen magos virtuális gépen fut, a processzor a hatékonyság elérése érdekében válthat a két szál között. Amíg az egyik szál a lemez I/O-ján várakozik, a processzor átválthat a másik szálra. Ily módon két szál több, mint egy szál lenne. Ha a virtuális gép egynél több maggal rendelkezik, tovább csökkenti a futási időt, mivel minden mag párhuzamosan hajthat végre feladatokat.

Előfordulhat, hogy nem tudja módosítani, hogy egy kész alkalmazás hogyan valósítja meg az egyszálas vagy többszálas műveleteket. Az SQL Server például képes több processzoros és többmagos kezelésére. Az SQL Server azonban eldönti, hogy milyen feltételek mellett használja ki a lekérdezés feldolgozását egy vagy több szálat. Lekérdezéseket futtathat, és indexeket hozhat létre többszálas használatával. Olyan lekérdezésesetén, amely nagy táblák összekapcsolásával és adatok rendezésével jár, mielőtt visszatérne a felhasználóhoz, az SQL Server valószínűleg több szálat fog használni. A felhasználó azonban nem tudja szabályozni, hogy az SQL Server egyetlen szál vagy több szál használatával hajtson-e végre lekérdezést.

Vannak olyan konfigurációs beállítások, amelyek módosíthatják, hogy befolyásolja ezt a többszálas vagy párhuzamos feldolgozásegy alkalmazás. Például az SQL Server esetében ez a parallelizmus konfigurációjának maximális foka. Ez a MAXDOP nevű beállítás lehetővé teszi az SQL Server által párhuzamos feldolgozás esetén használható processzorok maximális számának konfigurálását. A MAXDOP konfigurálható az egyes lekérdezésekhez vagy indexműveletekhez. Ez akkor hasznos, ha a rendszer erőforrásait egy teljesítményszempontjából kritikus alkalmazás hozhatja kiegyensúlyozni.

Tegyük fel például, hogy az SQL Server t használó alkalmazása egyszerre hajt végre egy nagy lekérdezést és egy indexműveletet. Tegyük fel, hogy azt akarta, hogy az indexművelet a nagy lekérdezéshez képest jobban működne. Ebben az esetben beállíthatja, hogy az indexművelet MAXDOP értéke magasabb legyen, mint a lekérdezés MAXDOP értéke. Így az SQL Server több processzorral rendelkezik, amelyeket az indexművelethez használhat, összehasonlítva a nagy lekérdezésnek szentelhető processzorok számával. Ne feledje, hogy nem szabályozhatja, hogy az SQL Server hány szálat fog használni az egyes műveletekhez. Szabályozhatja a többszálas működésre kijelölt processzorok maximális számát.

További információ [a párhuzamosság fokairól](https://technet.microsoft.com/library/ms188611.aspx) az SQL Server ben. Ismerje meg azokat a beállításokat, amelyek befolyásolják a többszálas működést az alkalmazásban és azok konfigurációiban a teljesítmény optimalizálása érdekében.

## <a name="queue-depth"></a>Várólista mélysége

A várólista mélysége, a várólista hossza vagy a várólista mérete a rendszerben függőben lévő IO-kérelmek száma. A várólista mélységének értéke határozza meg, hogy az alkalmazás hány I/O-műveletet tud egyezőre, amelyeket a tárolólemezek fel dolgoznak. Ez érinti a három alkalmazás teljesítménymutatók, hogy ebben a cikkben tárgyalt, viz., IOPS, átviteli teljesítmény és késés.

A várólista mélysége és a többszálas működés szorosan összefügg. A Várólista mélysége érték azt jelzi, hogy az alkalmazás mennyi többszálas működést érhet el. Ha a várólista mélysége nagy, az alkalmazás több műveletet is végrehajthat egyidejűleg, más szóval több többszálas működést. Ha a várólista mélysége kicsi, annak ellenére, hogy az alkalmazás többszálas, nem lesz elég kérelmek sorakoznak az egyidejű végrehajtáshoz.

Általában a polcról szóló alkalmazások nem teszik lehetővé a várólista mélységének módosítását, mert ha helytelenül vannak beállítva, több kárt okoznak, mint hasznot. Az alkalmazások az optimális teljesítmény eléréséhez a várólista mélységének megfelelő értékét állítja be. Fontos azonban megérteni ezt a fogalmat, hogy az alkalmazással kapcsolatos teljesítményproblémák at elháríthassa. A várólista mélységének hatásait is megfigyelheti, ha teljesítményértékelési eszközöket futtat a rendszeren.

Egyes alkalmazások a várólista mélységének befolyásolására vonatkozó beállításokat biztosítanak. Például az SQL Server MAXDOP (maximális párhuzamossági foka) beállítását az előző szakaszban ismertetettük. A MAXDOP segítségével befolyásolhatja a várólista mélységét és a többszálas működést, bár közvetlenül nem módosítja az SQL Server várólistamélység értékét.

*Magas várólista-mélység*  
A magas várólistamélység több műveletet hoz létre a lemezen. A lemez előre ismeri a következő kérést a várólistájában. Ennek következtében a lemez előre ütemezheti a műveleteket, és optimális sorrendben feldolgozhatja azokat. Mivel az alkalmazás több kérelmet küld a lemezre, a lemez több párhuzamos IOs-t tud feldolgozni. Végső soron az alkalmazás képes lesz elérni a magasabb IOPS. Mivel az alkalmazás több kérelmet dolgoz fel, az alkalmazás teljes átviteli valószínűleg is növekszik.

Egy alkalmazás általában a csatlakoztatott lemezenként 8–16+ kiemelkedő IOs-sel képes elérni a maximális átviteli értéket. Ha a várólista mélysége egy, az alkalmazás nem nyomja elég IOs a rendszerbe, és ez kevesebb feldolgozása egy adott időszakban. Más szóval, kevesebb átviteli.

Az SQL Server ben például a lekérdezés MAXDOP értékének "4" értékre állítása azt a tájékoztatást adja az SQL Server kiszolgálóról, hogy legfeljebb négy magot használhat a lekérdezés végrehajtásához. Az SQL Server határozza meg, hogy mi a legjobb várólistamélységi érték és a lekérdezés végrehajtásához szükséges magok száma.

*Optimális várólista-mélység*  
A nagyon magas várólistamélység-értéknek is megvannak a maga hátrányai. Ha a várólista mélységének értéke túl magas, az alkalmazás nagyon magas IOPS-t próbál meg vezetni. Ha az alkalmazás nem rendelkezik elegendő kiosztott IOPS-al rendelkező állandó lemezekkel, ez negatívan befolyásolhatja az alkalmazás késéseit. A következő képlet az IOPS, a késés és a várólista mélysége közötti kapcsolatot mutatja be.  
    ![](media/premium-storage-performance/image6.png)

Ne konfigurálja a várólista mélységét magas értékre, hanem egy optimális értékre, amely elegendő IOPS-t képes biztosítani az alkalmazás számára a késések befolyásolása nélkül. Ha például az alkalmazás késésének 1 ezredmásodpercnek kell lennie, az 5000 IOPS eléréséhez szükséges várólistamélység a QD = 5000 x 0,001 = 5.

*Várólista mélysége csíkozott kötethez*  
Csíkozott kötet esetén tartson elég magas várólistamélységet úgy, hogy minden lemeznek külön-külön csúcsvárólista-mélysége maradjon. Vegyünk például egy olyan alkalmazást, amely 2-es várólistamélységet leválaszt, és négy lemez van a csíkban. A két I/O-kérelem két lemezre kerül, a fennmaradó két lemez pedig tétlen lesz. Ezért konfigurálja a várólista mélységét úgy, hogy az összes lemez foglalt legyen. Az alábbi képlet bemutatja, hogyan határozható meg a csíkozott kötetek várólista-mélysége.  
    ![](media/premium-storage-performance/image7.png)

## <a name="throttling"></a>Throttling

Az Azure Premium Storage-ra vonatkozó rendelkezések a kiválasztott virtuális gépmérettől és lemezmérettől függően az IOPS és az átviteli érték számát határozzák meg. Bármikor, amikor az alkalmazás megpróbálja az IOPS vagy az átviteli kapacitás a virtuális gép vagy lemez által kezelhető korlátok felett, prémium szintű storage-szabályozással. Ez az alkalmazás ban leromlott teljesítmény formájában nyilvánul meg. Ez magasabb késést, alacsonyabb átviteli, vagy alacsonyabb IOPS-t jelenthet. Ha a prémium szintű storage nem szabályozza, az alkalmazás teljesen sikertelen lehet túllépve, amit az erőforrások képesek elérni. Így a sávszélesség-szabályozás miatti teljesítményproblémák elkerülése érdekében mindig elegendő erőforrást az alkalmazáshoz. Vegye figyelembe, amit a virtuális gép méretei és a lemezméretek fent szakaszokban tárgyaltunk. Benchmarking a legjobb módja annak, hogy kitaláljuk, milyen erőforrásokat kell a fogadó az alkalmazás.

## <a name="next-steps"></a>További lépések
