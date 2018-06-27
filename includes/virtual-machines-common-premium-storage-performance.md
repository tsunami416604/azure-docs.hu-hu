# <a name="azure-premium-storage-design-for-high-performance"></a>Prémium szintű Azure Storage: Nagy teljesítményű kialakítása

Ez a cikk útmutatást nyújt a prémium szintű Azure Storage használatával nagy teljesítményű alkalmazások létrehozásához. A megjelenő utasításokat az alkalmazás által használt technológiák alkalmazandó ajánlott eljárások teljesítményének együtt dokumentumban is használhatja. Mutatja be az irányelveket, ez a dokumentum példaként, prémium szintű Storage futó SQL Server használta azt.

Ebben a cikkben a tárolási réteg teljesítményének forgatókönyvei oldjuk, amíg szüksége lesz az alkalmazási rétegre optimalizálása érdekében. Például ha prémium szintű Azure Storage SharePoint-Farm üzemeltet, használhatja az SQL Server ebben a cikkben említett példákat követik az adatbázis-kiszolgáló optimalizálása érdekében. Emellett optimalizálhatja a SharePoint-Farm webkiszolgáló és a legtöbb teljesítmény-alkalmazáskiszolgáló.

Ez a cikk segít a prémium szintű Azure Storage alkalmazás teljesítményének optimalizálására kapcsolatos gyakori kérdéseket a következő válasz

* Az alkalmazás teljesítményének méréséhez hogyan?  
* Miért meg nem jelenik meg várt nagy teljesítményű?  
* Mely tényezők befolyásolják az alkalmazások teljesítményéről, a prémium szintű Storage?  
* Hogyan hajtsa végre ezeket a tényezők befolyásolják a prémium szintű Storage az alkalmazás teljesítményének?  
* Hogyan is optimalizálhatja az IOPS, sávszélesség és a késleltetés?  

Adtunk ezeket az irányelveket kifejezetten a prémium szintű Storage, mert prémium szintű Storage munkaterheléseinek magas teljesítmény-és nagybetűket. Példák adtunk meg, ahol. Is alkalmazhat az egyes ezeket az irányelveket a szabványos tárolólemezek IaaS virtuális gépeken futó alkalmazások.

Mielőtt elkezdené, ha még nem ismeri a prémium szintű Storage, először olvassa el a [prémium szintű Storage: nagy teljesítményű tárolást Azure virtuális gépek terheléseihez](../articles/virtual-machines/windows/premium-storage.md) és [Azure Storage méretezhetőségi és Performance Targets](../articles/storage/common/storage-scalability-targets.md)cikkeket.

## <a name="application-performance-indicators"></a>Alkalmazás teljesítménymutatók
Azt ellenőrzéséhez, hogy helyes-e használatával teljesítmény mutatókat, például egy alkalmazás hajt végre, hogy milyen gyorsan alkalmazás feldolgozása egy felhasználói kérelem mennyi adatot az alkalmazás kérés feldolgozása, hány kér az alkalmazás feldolgozása az egy adott időtartam, mennyi ideig vár arra, hogy a kérés elküldése után kapott választ a felhasználó rendelkezik. Ezek teljesítménymutatók műszaki feltételei, IOPS, az átviteli vagy a sávszélesség és a késleltetés.

Ebben a szakaszban ismertetjük a közös teljesítménymutatók prémium szintű Storage környezetében. A következő szakaszban, összegyűjtése Alkalmazáskövetelményeket, akkor megtudhatja, hogyan mérésére ezek teljesítménymutatók, az alkalmazás. Később az alkalmazások teljesítményének optimalizálása megtudhatja, hogyan ezek teljesítménymutatók és a javaslatok érdekében befolyásoló tényezők.

## <a name="iops"></a>IO
IOPS-érték száma, hogy az alkalmazás egy második küld a tárolólemezek a kérelem. Egy bemeneti/kimeneti műveleti olvashatók a sémaadatok, vagy szekvenciális vagy véletlenszerű írását. Sok egyidejű felhasználói kérés feldolgozása azonnal kell OLTP alkalmazások, például egy online kereskedelmi webhelyen. A felhasználói kérelmek insert és igényes adatbázis tranzakciók, amelyek az alkalmazás gyorsan kell feldolgozni frissítése. Ezért OLTP alkalmazásoknak nagyon magas iops-érték. Ilyen alkalmazásokat kezeléséhez a kis- és véletlenszerű I/O kérelmek több millió. Ha egy kérelmet, az alkalmazás infrastruktúrát IOPS optimalizálás kell tervezésekor. A későbbi szakasz ismerteti *alkalmazások teljesítményének optimalizálása*, tárgyaljuk részletesen a tényezőket kell figyelembe venni magas iops-érték eléréséhez.

Ha csatlakoztat egy prémium szintű tároló lemez a virtuális gép, méretezhető, a lemez specifikáció szerint IOPS garantált számos Azure rendelkezések. Például egy P50 lemez 7500 IOPS látja el. Minden nagy méretű Virtuálisgép-méretet is rendelkezik egy adott IOPS-korláttal is kiálló tárolókkal. Például egy szabványos GS5 virtuális gép rendelkezik 80000 IOPS korlátozza.

## <a name="throughput"></a>Teljesítmény
Átviteli vagy a sávszélesség nem az alkalmazás küld a tárolólemezek a megadott időszak adatmennyiséget. Az alkalmazás IO-egység nagy méretű bemeneti/kimeneti műveleteket hajt végre, ha magas teljesítményt igényel. Adatraktár alkalmazások általában ki, amely egyszerre nagy részének adatok eléréséhez, és általában a tömeges műveletek végrehajtása vizsgálat számításigényes műveletek. Ez azt jelenti az ilyen alkalmazások magasabb teljesítményt igényelnek. Ha van ilyen alkalmazás, a teljesítmény optimalizálása érdekében az infrastruktúra tervezése kell. A következő szakaszban arról lesz szó részletesen a tényezőket, ennek érdekében kell beállításakor.

Ha csatlakoztat egy prémium szintű tároló lemez egy nagy méretű virtuális Gépet az Azure rendelkezések átviteli adott lemez specifikáció szerint. Például egy P50 lemez második lemezenként átviteli kiépítését 250 MB. Minden nagy méretű Virtuálisgép-méretet is kiálló tárolókkal adott átviteli korlátozás is rendelkezik. Például szabványos GS5 virtuális Gépnek legyen 2000 MB maximális átviteli sebességgel száma másodpercenként. 

Nincs olyan átviteli sebesség és IOPS, ahogy az alábbi képlet közötti kapcsolat.

![](media/premium-storage-performance/image1.png)

Ezért fontos az optimális átviteli sebesség és IOPS értékeket, amelyeket az alkalmazás által igényelt határozza meg. Próbálja ki valamelyik optimalizálása, a másik is lekérdezi hatással. Egy későbbi szakasz ismerteti *alkalmazások teljesítményének optimalizálása*, iops-érték és a teljesítmény optimalizálása a további részleteket ismertetik.

## <a name="latency"></a>Késés
Késleltetés az az idő szükséges a egy alkalmazás egyetlen kérelem, elküldheti a tárolólemezek küldésére és fogadására a választ az ügyfélnek. Ez az IOPS és átviteli sebesség mellett az alkalmazások teljesítményének kritikus mértékét. A prémium szintű tároló lemez késését az lekérdezni a következő kérelmet, és tájékoztatni az alkalmazás szükséges idő. Prémium szintű Storage konzisztens kis késleltetést biztosít. Ha engedélyezi a csak olvasható-állomás gyorsítótárazását a prémium szintű storage lemezeken, sokkal alacsonyabb olvasási késése kaphat. Ismertetjük lemez gyorsítótárazása a szakasz későbbi részében részletesebben a *alkalmazások teljesítményének optimalizálása*.

Ha az alkalmazás magasabb iops-érték és átviteli sebesség eléréséhez optimalizálása, az hatással a késés az alkalmazás. Miután az alkalmazás teljesítményének hangolása, kiértékelésének eredménye mindig a késés nagy késleltetésű váratlan viselkedés elkerülése érdekében az alkalmazás.

## <a name="gather-application-performance-requirements"></a>Alkalmazás teljesítmény követelmények összegyűjtése
A prémium szintű Azure Storage futó nagy teljesítményű alkalmazások tervezésének első lépése van, az alkalmazás teljesítményének követelmények megértése érdekében. Miután összegyűjtötte teljesítménykövetelményeknek, optimalizálhatja az alkalmazás a legoptimálisabb teljesítmény eléréséhez.

Az előző szakasz azt magyarázza a közös teljesítménymutatók, IOPS, az átviteli sebesség és a késleltetés. Meg kell adnia, amely ezen teljesítménymutatók létfontosságúak az alkalmazás számára a kívánt felhasználói élmény. Például magas iops értéket számít legtöbb másodpercenként több millió tranzakciók feldolgozása OLTP-alkalmazásokhoz. Mivel a magas teljesítmény fontos a nagy mennyiségű adat feldolgozása egy második Data Warehouse-alkalmazások. Valós idejű alkalmazások, például élő videoadatfolyam webhelyek elengedhetetlen a rendkívül alacsony késleltetés.

A következő mértékcsoport teljes élettartamuk az alkalmazás maximális teljesítménye követelményeinek. Az alábbi minta ellenőrzőlista használják a Kezdés. Jegyezze fel a maximális teljesítménykövetelményeknek normál során, a csúcsidőre és a munkaidőn kívüli munkaterhelés időszakok. Az összes munkaterhelés szint követelményeinek azonosításával fogja tudni határoznia azt, az alkalmazás általános teljesítmény-követelmény. Például az elektronikus kereskedelmi webhely normál munkaterhelését lesz a tranzakciók működik a legtöbb nap során. A maximális munkaterhelést a webhely lesz a tranzakciók során ünnepi vagy különleges pénztári események szolgál. A maximális munkaterhelést általában tapasztalt korlátozott időtartamra, de megkövetelheti az alkalmazás két vagy több alkalommal a normál működésének méretezése. Ismerje meg az 50. percentilis, 90 PERCENTILIS és 99 érték követelményeinek. Ez segít a teljesítmény követelmények minden kiugró szűrheti, és a próbálkozások összpontosíthat optimalizálja a a megfelelő értékeket.

**Alkalmazás teljesítmény követelmények ellenőrzőlista**

| **Teljesítménnyel kapcsolatos követelmények** | **50. percentilis** | **90 százalékos érték** | **99 PERCENTILIS** |
| --- | --- | --- | --- |
| Legfeljebb Másodpercenkénti tranzakciók | | | |
| % Olvasási műveletek | | | |
| % Írási műveletek | | | |
| % Véletlenszerű műveletek | | | |
| % Egymást követő műveletek | | | |
| IO-kérelem méret | | | |
| Átlagos átviteli sebessége | | | |
| Legfeljebb Teljesítmény | | | |
| Perc. Késés | | | |
| Átlagos késleltetése | | | |
| Legfeljebb CPU | | | |
| Átlagos processzorhasználat | | | |
| Legfeljebb Memory (Memória) | | | |
| Átlagos memória | | | |
| Várólistamélység | | | |

> [!NOTE]
> Vegye figyelembe ezeket a számokat az alkalmazás a várt jövőbeli növekedésére alapján méretezés. Célszerű egy tervezése növekedési időben, mert később a teljesítmény fokozása infrastruktúráját módosítása nehezebb lehet.
>
>

Ha egy meglévő alkalmazást, és szeretne áttérni a tanúsítványszolgáltatás prémium szintű Storage, először hozzon létre a meglévő alkalmazáshoz a fent ellenőrzőlista. Ezt követően egy prototípus az alkalmazás a prémium szintű Storage felépítéséhez, és irányelveket leírtak alapján alkalmazás *alkalmazások teljesítményének optimalizálása* a jelen dokumentum későbbi szakaszában. A következő szakasz az segítségével gyűjtse össze a TELJESÍTMÉNYMÉRÉSEK eszközöket írja le.

A feladatlista létrehozása a meglévő alkalmazás a prototípus hasonló. Benchmarking eszközökkel szimulálása a munkaterhelések, és mérheti a prototípus alkalmazás teljesítményére. Szakaszt [Benchmarking](#benchmarking) további. Így ellenőrizheti, hogy prémium szintű Storage egyezik-e, vagy művelet túllépje a alkalmazás teljesítményre vonatkozó követelmények módon. Az azonos útmutatást majd éles alkalmazás is létrehozható.

### <a name="counters-to-measure-application-performance-requirements"></a>Alkalmazás teljesítménykövetelményeknek mérésére számlálók
A legjobb méréséhez az alkalmazás teljesítménykövetelményeknek módja Teljesítményfigyelő, a kiszolgálón futó operációs rendszer által biztosított eszközök segítségével. A Windows Teljesítményfigyelő és iostat Linux használhatja. Ezek az eszközök rögzítése mindegyik mértéknek, tekintse meg a fenti szakaszban a megfelelő számlálókat. Ezek a számlálók értékeit rögzíteni kell, amikor az alkalmazás fut, a normál, maximális és a munkaidőn kívüli munkaterhelések.

A teljesítményszámlálók processzor, memória, és minden egyes logikai lemez és a kiszolgáló fizikai lemez érhetők el. Prémium szintű storage lemezek használatakor egy virtuális gép a fizikai lemez számlálók prémium szintű storage lemezek, és logikai lemez számlálók a prémium szintű storage lemezeken létrehozott minden kötet. A lemezeket, az alkalmazás munkaterhelés üzemeltető értékeit kell rögzíti. Ha egy-egy logikai és fizikai lemezek közötti leképezéseket, olvassa el a fizikai lemez számlálók; Ellenkező esetben tekintse meg a logikai lemez számlálókat. Linux a iostat parancs lemez- és CPU-kihasználtság jelentést hoz létre. A lemezhasználati jelentés nyújt a fizikai eszköz vagy a partíció statisztikai adatokat. Ha az adatokhoz és a naplófájlhoz az adatbázis-kiszolgáló külön lemezeken, az adatgyűjtést mindkét lemezhez. Alábbi táblázat ismerteti a lemezek, a processzor és memóriájára vonatkozóan:

| Számláló | Leírás | Teljesítményfigyelő | Iostat |
| --- | --- | --- | --- |
| **IOPS vagy tranzakciók száma másodpercenként** |A tárolási lemezre másodpercenként kiadott i/o-kérelmek száma. |Lemezolvasások/mp <br> Lemezírás/mp |TP-k <br> r/s <br> w/s |
| **Lemez olvasása és írása** |%-át olvasási és írási műveleteket végrehajtani a lemezen. |% Lemez olvasási idő <br> % Lemezre írási ideje |r/s <br> w/s |
| **Átviteli sebesség** |Írni vagy olvasni a lemezre másodpercenként adatok mennyisége. |Lemezolvasási sebesség (bájt/s) <br> Lemezírási sebesség (bájt/s) |kB_read/s <br> kB_wrtn/s |
| **Késés** |A lemez IO-kérelem végrehajtásához teljes időtartam. |Átlagos lemez mp/Olvasás <br> Átlagos lemez mp/írás |await <br> svctm |
| **IO-méret** |I/o méretét a tárolólemezek problémák kéri. |Átlagos lemezátviteli/beolvasott bájtok száma <br> Átlagos írási bájtok/írás |avgrq-sz |
| **Várólistamélység** |A lekérdezések Várakozás a tároló lemez írni vagy olvasni függőben lévő i/o száma. |Lemezvárólista jelenlegi hossza |avgqu-sz |
| **Max. Memória** |Zökkenőmentesen működjön az alkalmazás futtatásához szükséges memória mérete |% Előjegyzett memória |Vmstat használata |
| **Max. PROCESSZOR** |Zökkenőmentesen működjön az alkalmazás futtatásához szükséges CPU összeg |Processzor kihasználtsága |% haszn. |

További információ [iostat](https://linux.die.net/man/1/iostat) és [PerfMon](https://msdn.microsoft.com/library/aa645516.aspx).

## <a name="optimizing-application-performance"></a>Az alkalmazások teljesítményének optimalizálása
A fő prémium szintű Storage futó alkalmazást teljesítményét befolyásoló tényezők jellegét a IO kérelmeket, Virtuálisgép-méretet, a lemez mérete, lemezek, a lemez gyorsítótár, a többszálas végrehajtás és várólistamélység számát is. A rendszer által biztosított forgatógombját ezek közül néhány tényező vezérelhető. Legtöbb alkalmazás nem adhat meg egy lehetőség, hogy a IO-méret és várólistamélység alter közvetlenül. Például az SQL Server használatakor nem választható, I/O méret- és várólista mélységét. SQL Server úgy dönt, hogy optimális I/O méret- és várólista mélysége értékeket a legtöbb teljesítményre van szüksége. Fontos tudni, hogy mindkét típusú tényezők hatással az alkalmazások teljesítményéről, hogy a teljesítmény igényeinek megfelelő erőforrásokat oszthat.

Ebben a szakaszban tekintse meg az alkalmazás követelményeinek ellenőrzőlista létrehozott, mennyire szükséges az alkalmazás teljesítményének optimalizálása érdekében. Amely alapján, fogja meg tudja határozni, mely tényezők ebben a szakaszban ismertetett kell hangolását. Tanúsítsa minden tényező hatással az alkalmazások teljesítményéről, futtassa az alkalmazást telepítő összehasonlítási eszközöket. Tekintse meg a [Benchmarking](#Benchmarking) szakasz lépései a Windows és Linux virtuális gépek közös összehasonlítási eszközök futtatása a jelen cikk végén.

### <a name="optimizing-iops-throughput-and-latency-at-a-glance"></a>Egy pillanat alatt IOPS, az átviteli sebesség és a késleltetés optimalizálása

Az alábbi táblázat összefoglalja a teljesítmény tényezők és IOPS, az átviteli sebesség és a késleltetés optimalizálására szükséges lépéseket. A az összegzés a következő részekben egyes tényező sokkal több mélységét.

További információ a Virtuálisgép-méretek és az IOPS, az átviteli sebesség és a késés érhető el a az egyes virtuális gép: [Linux Virtuálisgép-méretek](../articles/virtual-machines/linux/sizes.md) vagy [Windows Virtuálisgép-méretek](../articles/virtual-machines/windows/sizes.md).

| &nbsp; | **IOPS** | **Átviteli sebesség** | **Késés** |
| --- | --- | --- | --- |
| **Példa** |Vállalati OLTP alkalmazás igénylő nagyon magas tranzakciók második sebessége. |Vállalati adatraktározási alkalmazás feldolgozási nagymennyiségű adat. |Közel valós idejű alkalmazások felhasználói kérésekre, például online játékok azonnali válasz igényelnek. |
| Teljesítmény tényezők | &nbsp; | &nbsp; | &nbsp; |
| **IO-méret** |Kisebb IO-méretet eredményez magasabb iops-érték. |Nagyobb IO mérete nagyobb átviteli teljesítményt eredményez. | &nbsp;|
| **Virtuálisgép-mérettel** |A Virtuálisgép-méretet, amely nagyobb, mint az alkalmazás követelményeinek IOPS nyújt használja. |A Virtuálisgép-méretet használata átviteli korlát nagyobb, mint az alkalmazás követelményeinek. |Használja a virtuális gép méretét, hogy ajánlatok méretezési korlátok nagyobb, mint az alkalmazás követelményeinek. |
| **Lemez mérete** |A lemez mérete nagyobb, mint az alkalmazás követelményeinek IOPS által használható. |A lemez mérete nagyobb, mint az alkalmazás követelményeinek átviteli korlátot használja. |Használja a lemez méretét, hogy ajánlatok méretezési korlátok nagyobb, mint az alkalmazás követelményeinek. |
| **Virtuális gép és a lemez méretkorlátai** |A kiválasztott Virtuálisgép-méretet a IOPS-korláttal prémium szintű tároló lemez nem csatlakoztatható által vezérelt teljes IOPS-nál nagyobbnak kell lennie. |A Virtuálisgép-méretet választott átviteli legfeljebb teljes átviteli sebesség nem csatlakoztatható prémium tárolólemezek által vezérelt nagyobbnak kell lennie. |A kiválasztott Virtuálisgép-méretet a méretkorlátai teljes méretkorlátai csatolt prémium tárolólemezek nagyobbnak kell lennie. |
| **A lemezes gyorsítótárazás** |Csak olvasható gyorsítótárának engedélyezése a prémium szintű tároló lemez is van olvasási nehéz műveletek olvasási magasabb IOPS eléréséhez. | &nbsp; |Csak olvasható gyorsítótárának engedélyezése a prémium szintű storage lemezeken készen nehéz műveletekkel lekérni nagyon alacsony olvasási késések fordulnak elő. |
| **Lemez csíkozást** |Több lemez használata, és paritásos azok egymáshoz kombinált magasabb iops-érték és átviteli határérték beolvasása. Vegye figyelembe, hogy a kombinált felső határ az egyes virtuális gép csatolt premium lemezek kombinált határain nagyobbnak kell lennie. | &nbsp; | &nbsp; |
| **Paritásos mérete** |Paritásos mérete kisebb véletlenszerű kisméretű I/O minta OLTP alkalmazások látható. SQL-kiszolgáló OLTP alkalmazás például használja a 64 KB-os méret paritásos. |Adatraktár alkalmazások látható szekvenciális nagy IO minta nagyobb paritásos mérete. Például használható a paritásos mérete 256 KB-os SQL Server Data warehouse alkalmazás. | &nbsp; |
| **Többszálas** |A használt többszálas, amelyekkel kérelmek száma nagyobb a prémium szintű Storage, amelyek magasabb iops-érték és az átvitel. SQL-kiszolgálón állítsa például SQL Server több processzort lefoglalni nagy MAXDOP érték. | &nbsp; | &nbsp; |
| **Várólistamélység** |Nagyobb várólistamélység magasabb IOPS adja eredményül. |Nagyobb várólistamélység nagyobb átviteli teljesítményt eredményez. |Kisebb várólistamélység kisebb késések adja eredményül. |

## <a name="nature-of-io-requests"></a>IO-kérelmek jellege
IO-kérelmet, amely az alkalmazás végrehajtását fogja bemeneti/kimeneti műveleti egység. IO-kérelmeket, véletlenszerű vagy egymást követő, jellege azonosító olvasási vagy írási, kisebb vagy nagyobb, segítenek meghatározni az alkalmazás teljesítményének követelményeinek. Nagyon fontos IO kérelmekről, a jobb oldali vonatkozó döntések meghozatalában az alkalmazás-infrastruktúra tervezésekor jellege megértése.

IO-méret a legfontosabb tényezők egyike. Az IO érték a bemeneti/kimeneti műveleti kérelem, az alkalmazás által generált méretét. Az IO-méret jelentős hatással van a teljesítményre, különösen ha az iops-érték és a sávszélesség, amelyekkel az alkalmazás érhet el a. A következő képlet mutatja az IOPS, kapcsolatát IO-méret és a sávszélesség/átviteli sebességet.  
    ![](media/premium-storage-performance/image1.png)

Egyes alkalmazások IO mérete, alter, míg egyes alkalmazások azonban nem teszik lehetővé. Például az SQL Server maga optimális IO méretét határozza meg, és nem biztosít a felhasználók bármilyen módosításhoz forgatógombját. Másrészről, Oracle biztosít egy paraméter, [DB\_BLOKKOLÁSA\_mérete](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) használatával, amely konfigurálható a i/o-kérés méretét az adatbázis.

Ha egy alkalmazás, amely nem engedélyezi, hogy a IO Oldalméret módosítása oly módon, használ használja az útmutatást ebben a cikkben a KPI-t, amely a legjobban megfelelő az alkalmazás teljesítményének optimalizálása érdekében. Például:

* OLTP okoz a kis- és véletlenszerű I/O kérelmek több millió. Az ilyen típusú IO kérelmek kezeléséhez, tervezésekor kell az alkalmazás infrastruktúráját magasabb iops-érték beolvasása.  
* Egy alkalmazás adatraktározási nagy és a szekvenciális I/O kérelmeket állít elő. Az ilyen típusú IO kérelmek kezeléséhez, a nagyobb sávszélességet vagy átviteli sebesség eléréséhez infrastruktúrát kell tervezésekor.

Egy alkalmazás, amely lehetővé teszi a IO Oldalméret módosítása oly módon, használata a szokásos megoldás használata mellett egyéb teljesítmény irányelvek IO mérete

* IO-méret kisebb magasabb IOPS eléréséhez. Például 8 KB-os OLTP alkalmazásokra vonatkozóan.  
* IO-méret nagyobb nagyobb sávszélesség/átviteli sebesség eléréséhez. Például 1024 KB data warehouse alkalmazáshoz.

Íme egy példa a hogyan kiszámíthatja az iops-érték és átviteli/sávszélesség az alkalmazáshoz. Érdemes lehet egy alkalmazás P30 lemezt használ. A maximális iops-érték és P30 lemez átviteli/sávszélesség érhető el egy 5000 iops teljesítményt és 200 MB / s kulcsattribútumokkal. Most ha az alkalmazás által a maximális IOPS a P30 lemezről, és használhatja például a 8 KB-os kisebb IO-méretet, a létrejövő fogja tudni lekérni sávszélessége 40 MB másodpercenkénti számát. Azonban ha az alkalmazás által a maximális átviteli sebesség/sávszélesség P30 lemezről, és IO nagyobb mint 1024 KB méretű használ, az eredményül kapott IOPS lesz, 200 iops-érték. Ezért hangolja a IO-méret, úgy, hogy mind az alkalmazás iops-érték és átviteli/sávszélesség követelménynek megfelel-e. Az alábbi táblázat összefoglalja a különböző IO méretű és a megfelelő IOPS és átviteli P30 lemez.

| Alkalmazás követelményeinek | I/o-mérete | IO | Átviteli sebesség/sávszélesség |
| --- | --- | --- | --- |
| Maximális IOPS-érték |8 KB |5000 |40 MB / s |
| Maximális átviteli sebesség |1024 KB |200 |200 MB / s |
| Maximális átviteli sebesség + magas iops-érték |64 KB |3,200 |200 MB / s |
| Maximális iops-érték + magas teljesítmény |32 KB |5000 |160 MB / s |

Iops-érték és egy prémium szintű tároló lemez a maximális értéknél nagyobb sávszélességet, amelyet több premium lemezek csíkozott együtt. Például paritásos két P30 lemezek egy 10 000 IOPS kombinált IOPS vagy egy kombinált átviteli sebesség 400 MB másodpercenként eléréséhez. A következő szakaszban leírtak kell használnia a virtuális gép méretét, amely támogatja a kombinált IOPS és-átviteli sebességet.

> [!NOTE]
> Növelésével vagy IOPS, vagy a másik is növeli a teljesítményt, ellenőrizze, hogy nem kattint az átviteli vagy IOPS-korlátok vonatkoznak, a lemez vagy a virtuális gép amikor vagy az egyik növelését.
>
>

IO-méret hatással az alkalmazások teljesítményének tanúsítsa, futtathatja a virtuális gép és a lemezek összehasonlítási eszközöket. Hozzon létre több tesztelési futtatják, és minden egyes futtatásához különböző IO-méret a hatás láthatja. Tekintse meg a [Benchmarking](#Benchmarking) szakaszban olvashat a jelen cikk végén.

## <a name="high-scale-vm-sizes"></a>Nagy méretű VM-méretek
Amikor elkezdi az alkalmazások fejlesztése, az első tennivalók egyik, válassza ki a virtuális gépek, az alkalmazás futtatásához. Prémium szintű Storage nagy skálázási Virtuálisgép-méretek, futtatható egy magas helyi lemez i/o-teljesítmény és nagyobb számítási teljesítményt igénylő alkalmazásokhoz tartalmaz. Virtuális gépeken gyorsabb processzorok, memória-core magasabb arány és egy Solid-State meghajtót (SSD) adja meg a helyi lemezen. Prémium szintű Storage támogató magas méretezési virtuális gépek többek között a DS, DSv2 és GS adatsorozat virtuális gépek.

Magas méretezési virtuális gépek különböző méretű mag, memória, az operációs rendszer és ideiglenes lemezméretet eltérő számú érhetők el. Minden egyes Virtuálisgép-méretet is rendelkezik, összekapcsolhatja azt a virtuális gép adatok lemezek maximális számát. Ezért hatással lesz a kiválasztott Virtuálisgép-méretet, mennyi feldolgozást, memória, és a tárolási kapacitás érhető el az alkalmazást. Hatással van a számítási és tárolási költségű. Például az alábbiakban a legnagyobb Virtuálisgép-méretet a DS-ben több, DSv2 adatsorozat és GS több előírásainak:

| Virtuális gép mérete | Processzormagok | Memory (Memória) | Virtuális gép mérete | Legfeljebb Az adatlemezek | Gyorsítótár mérete | IO | Gyorsítótár IO sávszélességkorlátok |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS14 |16 |112 GB |AZ OPERÁCIÓS RENDSZER 1023 GB-OS = <br> Helyi SSD 224 GB = |32 |576 GB |50 000 IOPS <br> 512 MB / s |4000 IOPS és 33 MB / s |
| Standard_GS5 |32 |448 GB |AZ OPERÁCIÓS RENDSZER 1023 GB-OS = <br> Helyi SSD 896 GB = |64 |4224 GB |80000 IOPS <br> 2000 MB / s |5000 iops teljesítményt és 50 MB / s |

Az összes elérhető Azure Virtuálisgép-méretek teljes listájának megtekintéséhez lásd [Windows Virtuálisgép-méretek](../articles/virtual-machines/windows/sizes.md) vagy [Linux Virtuálisgép-méretek](../articles/virtual-machines/linux/sizes.md). Válassza ki a virtuális gép méretét, amelyek megfelelnek és méretezhető, a kívánt alkalmazáshoz teljesítményre vonatkozó követelmények. Továbbá a Virtuálisgép-méretek kiválasztásakor a következő szempontokat figyelembe venni.

*Méretkorlátai*  
A maximális IOPS-korlátok vonatkoznak virtuális gépenként, és lemezenként különböző és egymástól független. Győződjön meg arról, hogy az alkalmazás befolyásoló tényezők iops-érték a virtuális Gépet, valamint a premium lemezek nem csatlakoztatható keretein belül. Ellenkező esetben az alkalmazások teljesítményének szabályozása fog tapasztalni.

Tegyük fel tegyük fel, hogy egy alkalmazás legfeljebb 4000 IOPS mérete. Ennek érdekében a virtuális gép DS1 P30 lemez kiépítéséhez. A P30 lemez legfeljebb 5000 iops teljesítményt biztosíthat. Azonban a DS1 virtuális Gépet a 3,200 IOPS korlátozódik. Ennek következtében az alkalmazások teljesítményéről fog kell korlátozza a virtuális gép határérték 3,200 IOPS, és lesz a teljesítmény csökkenését. Ennek elkerülése érdekében válasszon, amelynek mindkét virtuális gép és a lemez mérete: alkalmazás követelményeinek.

*A művelet költsége*  
Sok esetben az is elképzelhető, hogy a prémium szintű Storage használatával művelet teljes költsége alacsonyabb, mint a standard szintű tárolást használ.

Vegyük példaként 16 000 IOPS igénylő alkalmazás. A teljesítmény eléréséhez szüksége lesz egy szabványos\_biztosíthat 16000 32 standard tárolási 1 TB méretű lemezek használatával a maximális IOPS D14 Azure IaaS virtuális. Minden 1TB standard tároló lemez legfeljebb 500 IOPS érhető el. A virtuális gép havonkénti becsült költségét $1,570 lesz. A havi költségét 32 szabványos tárolólemezek $1,638 lesz. Becsült havi összköltsége $3,208 lesz.

Azonban ha üzemeltetett ugyanahhoz az alkalmazáshoz, a prémium szintű Storage, szüksége lesz kisebb Virtuálisgép-méretet és kevesebb a prémium szintű storage lemezt, így csökkentve a teljes költség szempontjából. Szabványos\_DS13 VM is megfeleljenek a 16 000 IOPS négy P30 lemezekkel. A DS13 virtuális Gépnek legyen a maximális IOPS 25,600, és minden P30 lemezhez tartozik egy maximális IOPS az 5000. A teljes, ez a konfiguráció érhető el 5000 x 4 = 20 000 iops-érték. A virtuális gép havonkénti becsült költségét $1,003 lesz. Prémium szintű storage-lemezek négy P30 havi költségét $544.34 lesz. Becsült havi összköltsége $1,544 lesz.

Az alábbi táblázat összefoglalja a költség bontásban tartalmazza az ebben a forgatókönyvben a Standard és prémium szintű Storage.

| &nbsp; | **Standard** | **Prémium** |
| --- | --- | --- |
| **Havonta VM költsége** |$1,570.58 (szabványos\_D14) |$1,003.66 (szabványos\_DS13) |
| **A lemezekkel kapcsolatos költségek havonta** |$1,638.40 (32 x 1 TB-os lemezeken) |$544.34 (4 x P30 lemez) |
| **Teljes költség szempontjából, havonta** |$3,208.98 |$1,544.34 |

*Linux Disztribúciókkal*  

Prémium szintű Azure Storage le a virtuális gépek teljesítménye azonos szinten Windows és Linux rendszerű. Sok változatban is elkészíti a Linux disztribúciókkal támogatott, és a teljes listája látható [Itt](../articles/virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Fontos megjegyezni, hogy különböző disztribúciókkal alkalmasabbak a különböző típusú munkaterhelések. Attól függően, hogy a munkaterhelésnek futó distro különböző teljesítményszintet jelenik meg. A Linux disztribúciókkal el az alkalmazás tesztelése, és válassza ki azt, amelyik a legjobban.

Prémium szintű Storage a Linux operációs rendszert futtató, ellenőrizze a legújabb frissítéseket a szükséges illesztőprogramokról magas teljesítmény biztosítása érdekében.

## <a name="premium-storage-disk-sizes"></a>Prémium szintű tároló lemez mérete
Prémium szintű Storage jelenleg kínál hét mérete. Minden lemez mérethatár különböző méretezési IOPS, sávszélesség és tárolására. Válassza ki a jobb oldali prémium szintű tároló lemez mérete attól függően, hogy az alkalmazás követelményei és a nagy méretű Virtuálisgép-méretet. Az alábbi táblázat a hét lemezt méretek és azok képességeinek. P4 és P6 méretek jelenleg csak a felügyelt lemezek támogatott.

| Prémium szintű lemezek típusa  | P4    | P6    | P10   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| Lemezméret           | 32 GB | 64 GB | 128 GB| 512 GB            | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOPS-érték lemezenként       | 120   | 240   | 500   | 2300              | 5000              | 7500              | 7500              | 
| Adattovábbítás lemezenként | 25 MB / s  | 50 MB / s  | 100 MB / s | 150 MB / s | 200 MB / s | 250 MB / s | 250 MB / s | 


Hány lemezek határozza meg, hogy a lemez méretet választott. Használhat egyetlen P50 lemez vagy több lemez is P10 az alkalmazás követelményeinek teljesítéséhez. A választott meghozásakor lenti fiókokkal kapcsolatos megfontolások figyelembe.

*(Iops-érték és átviteli) méretkorlátai*  
Az iops-érték és az átviteli Sebességkorlát minden prémium szintű lemez méretének különböző és független származik a virtuális gép méretezési korlátok. Győződjön meg arról, hogy a teljes IOPS és átviteli lemezeiről van-e a kiválasztott Virtuálisgép-méretet a méretezési korlátok.

Ha például egy alkalmazás mérete legfeljebb 250 MB/s átviteli sebesség és DS4 virtuális gép egyetlen P30 lemezt használ. DS4 virtuális gép akár 256 MB/s átviteli sebesség biztosíthat. Azonban egyetlen P30 lemez rendelkezik 200 MB/s átviteli sebesség korlátot. Ezért az alkalmazás korlátozott, 200 MB/s a lemez korlát miatt kell. Megoldásához, ezt a határt, a virtuális gép adatok lemezek létrehozásához egynél több, vagy P40 vagy P50 a lemezek átméretezése.

> [!NOTE]
> Olvasási szolgálja ki a gyorsítótár nem szerepelnek a IOPS lemez és a teljesítményt, ezért nem tárgy lemez határértékekhez. Gyorsítótár a külön iops-érték és átviteli felső határ az egyes virtuális gép van.
>
> Például kezdetben az olvasási és írási műveletek a következők 60MB/s és 40MB/s kulcsattribútumokkal. Az idő múlásával a gyorsítótár bemelegedett, és több és több olvasási szolgál a gyorsítótárból. Ezt követően magasabb írási teljesítményt érheti el a lemez.
>
>

*Lemezek száma*  
Szüksége lesz, mivel felméri alkalmazáskövetelményeket lemezek számának meghatározásához. Minden egyes Virtuálisgép-méretet is van korlátozva, összekapcsolhatja azt a virtuális lemezek számát. Ez általában kétszer magok száma. Győződjön meg arról, hogy a Virtuálisgép-méretet választott képesek támogatni a szükséges lemezek számát.

Ne feledje, hogy a prémium szintű Storage lemezeken lehetnek szabványos tárolólemezek képest magasabb teljesítménybeli képességek. Ezért ha telepít át az alkalmazást az Azure infrastruktúra-szolgáltatási virtuális gép a szabványos tároló prémium szintű Storage, valószínűleg szüksége lesz a az alkalmazás azonosnak vagy annál magasabb teljesítmény érdekében kevesebb premium lemezek.

## <a name="disk-caching"></a>A lemezes gyorsítótárazás
Magas méretezési virtuális gépeket, amely kihasználja a prémium szintű Azure Storage BlobCache nevezett többrétegű gyorsítótárazási technológiát rendelkezik. BlobCache a virtuális gép memória és a helyi SSD használ a gyorsítótárazáshoz. Ez a gyorsítótár a prémium szintű Storage állandó és a virtuális gép helyi lemezek érhető el. Alapértelmezés szerint a gyorsítótár beállítás értéke olvasási/írási operációsrendszer-lemezek és a csak olvasható: prémium szintű Storage üzemeltetett adatlemezek. A lemez gyorsítótárazás engedélyezve van a prémium szintű Storage lemezeken, a nagy méretű virtuális gépek érhető el a rendkívül magas szintű teljesítmény, mint az alapul szolgáló lemez teljesítménye.

> [!WARNING]
> Az Azure-lemezek gyorsítótár beállításainak megváltoztatása szervezőről, és újra csatlakoztatja a céllemezt. Ha az operációs rendszer tárolására, a virtuális gép újraindul. Minden alkalmazások és szolgáltatások, amely befolyásolhatja a megszakítás a lemezgyorsítótár-beállítás módosítása előtt állítsa le.
>
>

BlobCache működésével kapcsolatos további tudnivalókért tekintse meg a belső [prémium szintű Azure Storage](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/) blogbejegyzést.

Fontos, a lemezek megfelelő halmazát gyorsítótárának engedélyezése. E kell gyorsítótárazása lemezen prémium lemezen, vagy nem függ a munkaterhelés mintát, hogy a lemez végző. Az alábbi táblázat az alapértelmezett operációsrendszer- és adatlemezek-gyorsítótárának beállításait.

| **Lemeztípus** | **Alapértelmezett gyorsítótár beállítás** |
| --- | --- |
| Operációsrendszer-lemez |ReadWrite |
| Adatlemez |ReadOnly |

Az alábbiakban az adatlemezek, ajánlott lemez-gyorsítótárának beállításait

| **Gyorsítótárazása lemezen** | **Mikor érdemes használni ezt a beállítást a javaslat** |
| --- | --- |
| None |Gazdagép-gyorsítótár beállítása None a csak írható és írási műveleteket. |
| ReadOnly |Gazdagép-gyorsítótár beállítása csak olvasható a csak olvasható és írható-olvasható. |
| ReadWrite |Gazdagép-gyorsítótár beállítása ReadWrite csak akkor, ha az alkalmazás megfelelően kezeli a gyorsítótárazott adatok írása állandó lemezekre, amikor szükséges. |

*Csak olvasható*  
Csak olvasható, a prémium szintű Storage-adatok gyorsítótárazása lemezek konfigurálásával olvasási kis késleltetésű eléréséhez, és nagyon magas IOPS olvasási és az átvitel lekérése az alkalmazáshoz. Ez a két okok miatt

1. Olvasási gyorsítótár, amely a Virtuálisgép-memória és a helyi SSD végre, és sokkal gyorsabb, mint az olvasások az adatlemezt, amely az Azure blob storage-ból.  
2. Prémium szintű Storage nem tartoznak bele az olvasási gyorsítótár, a lemez IOPS felé, és az átvitel helyről. Ezért az alkalmazás, magasabb teljes iops-érték és a teljesítményt érhet el.

*ReadWrite*  
Alapértelmezés szerint az operációs rendszer lemezek rendelkeznek ReadWrite gyorsítótárazás engedélyezve van. Támogatja a gyorsítótárazást az adatokat, valamint a lemezek ReadWrite nemrég jelentek meg. Gyorsítótárazás ReadWrite használ, ha az adatokat írni a gyorsítótár állandó lemezek megfelelő úgy kell rendelkeznie. Például az SQL Server kezeli a gyorsítótárazott adatok írása az állandó tároló lemezek önállóan. ReadWrite gyorsítótár használata olyan alkalmazás, amely nem kezeli a szükséges adatok megőrzése adatok elvesztéséhez vezethet, ha a virtuális gép összeomlik.

Tegyük fel, alkalmazhatja ezeket az irányelveket SQL Serveren megtalálható a prémium szintű Storage a következő tevékenységek végrehajtásával

1. "ReadOnly" gyorsítótár konfigurálása prémium szintű storage lemezeken adatok-fájlokat tartalmazó.  
   a.  A gyors olvassa be az gyorsítótár alacsonyabb az SQL Server lekérdezési idő óta lapok sokkal gyorsabb olvassa be a gyorsítótár képest közvetlenül az adatok a lemezeket.  
   b.  Olvasási kiszolgálás a gyorsítótárból, azt jelenti, hogy van további átviteli sebesség érhető el prémium adatlemezek. SQL Server is használja a további átviteli további lapok és más műveletek, például a biztonsági mentés/visszaállítás felé, a batch-terhelések és index újraépíti.  
2. Konfigurálja a naplófájlokat tároló prémium szintű storage lemezeken "None" gyorsítótárazza.  
   a.  Naplófájlok elsősorban írási műveleteket műveletek rendelkezik. Ezért azokat nem tudják igénybe venni a csak olvasható gyorsítótárának.

## <a name="disk-striping"></a>Lemez csíkozást
Ha egy virtuális gép csatlakozik a állandó lemezek több prémium szintű storage nagy adatmennyiség esetén a lemezt az IOPs, a sávszélesség és a tárolási kapacitás összesítésére együtt is csíkozott.

Windows, a segítségével a tárolóhelyek paritásos lemezek együtt. A készletbe konfigurálnia kell egy oszlopot az egyes lemezek. Ellenkező esetben csíkozott kötetek általános teljesítménye lehet kisebb, mint a várt, a forgalom a lemezeken egyenetlen eloszlását eredményezheti.

Fontos: A kiszolgáló-kezelő felhasználói felületén, legfeljebb 8 csíkozott kötetek oszlopok száma állíthatók be. Több mint 8 lemez csatlakoztatása, a PowerShell segítségével hozza létre a kötetet. PowerShell használatával beállíthatja az oszlopok számának egyenlőnek a lemezek számát. Például, ha egy egyetlen paritásos készlet; 16 lemezek vannak Adja meg a 16 oszlopai a *numberofcolumns tulajdonsághoz* paramétere a *New-VirtualDisk* PowerShell-parancsmagot.

Linux esetében használható együtt a paritásos lemezek MDADM segédprogramot. A részletes lépéseket Linux csíkozást lemezeinek tekintse meg a [szoftver RAID konfigurálása Linux](../articles/virtual-machines/linux/configure-raid.md).

*Paritásos mérete*  
A lemez csíkozást egy fontos konfigurációs paritásos mérete. A paritásos méretét vagy a blokkméret a legkisebb adattömb, alkalmazás csíkozott kötetek meg lehet oldani. A paritásos konfigurálnia függ típusát és az alkalmazás a kérelem mintát. Ha úgy dönt, hogy a megfelelő paritásos mérete, előfordulhat, hogy IO hibás illesztés hibákat, ami az alkalmazás teljesítménye.

Például ha az alkalmazás által generált IO kérelmet a paritásos lemez mérete nagyobb, a tárolórendszer írja azt paritásos egység határain túlnyúló egynél több lemezen. Amikor az adatok eléréséhez, hogy a kérés teljesítéséhez el egynél több paritásos egységek között lesz. Az ilyen viselkedést kumulatív hatása teljesítményének jelentős csökkenéséhez vezethet. Másrészről Ha IO kérés mérete kisebb, mint paritásos mérete, és ha véletlenszerű ideiglenesek, a IO kérelmek előfordulhat, hogy egyezzen azon a lemezen szűk keresztmetszetet, és végső soron terhelése IO teljesítményét.

Munkaterhelés típusától függően az alkalmazás fut, válassza ki a megfelelő paritásos méretét. A véletlenszerű kisméretű I/O kéréseket használja a paritásos mérete kisebb lesz. Mivel a nagy szekvenciális I/O kérelmek paritásos nagyobb méretű használja. Ismerje meg a paritásos mérete javaslatok az alkalmazás a prémium szintű Storage fog futni. Az SQL Server 64 KB-os OLTP-munkaterhelések és 256 KB-os adatok vámraktározási munkaterhelések paritásos méretének beállítása. Lásd: [teljesítmény az Azure virtuális gépeken futó SQL Server ajánlott eljárásai](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md#disks-guidance) további.

> [!NOTE]
> Akkor is paritásos együtt legfeljebb 32 prémium szintű storage DS több virtuális gép és 64 prémium szintű storage lemezek a GS több virtuális gép.
>
>

## <a name="multi-threading"></a>Többszálú
Azure platform prémium szintű Storage nagymértékben párhuzamos van kialakítva. Ezért a többszálas alkalmazások egyszálas alkalmazásnál jobb teljesítményt éri el. A többszálas alkalmazások felosztja a feladatai be több szálak között, és növeli az hatékonyságát, a futtatása a virtuális gép és a lemez erőforrások maximális felügyelniük.

Például ha az alkalmazás egy processzormag két szálat használ a virtuális gép fut, a CPU válthat a két szál a hatékonyság elérése. Egy szál a lemez IO befejezéséhez vár, amíg a CPU-t a más szál válthat. Ezzel a módszerrel több mint egy szál volna két szállal érhető el. Ha a virtuális gépen a több mag, továbbá csökkenti futási időt mivel minden egyes core feladatokat is végre párhuzamosan.

Nem lehet változtatni vásárolt alkalmazás valósít meg egyetlen szálkezelési vagy többszálas. Például SQL-kiszolgáló nem képes Többprocesszoros és Többmagos kezelésére. SQL Server azonban úgy dönt, milyen feltételek mellett azt fogja használni, egy vagy több szál a lekérdezés feldolgozásához. Ez lekérdezéseinek futtatásához, és használatával többszálas indexeket építenek. Egy lekérdezést, amely magában foglalja a nagy táblák illesztése és az adatok rendezése visszatér a felhasználó, az SQL Server valószínűleg több szál fogja használni. A felhasználó azonban nem tudja ellenőrizni, hogy az SQL Server használatával egyetlen szálon vagy több szál lekérdezést hajtanak.

Nincsenek konfigurációs beállítások, amelyek befolyásolják a módosíthatja, többszálas vagy párhuzamos feldolgozása: egy alkalmazást. Például az SQL Server esetén is a legnagyobb mértékben a párhuzamosság konfigurációs. Ez a beállítás nevű MAXDOP, konfigurálhatja az SQL-kiszolgáló is használhatja, amikor párhuzamos processzorok maximális száma feldolgozása. Egyéni lekérdezések vagy indexművelet MAXDOP konfigurálható. Ez akkor hasznos, ha a rendszer teljesítményének kritikus fontosságú alkalmazások erőforrások szeretné.

Tegyük fel, hogy az alkalmazás SQL Server használatával nagy lekérdezés és egyszerre egy index művelet végrehajtása történik. Tételezzük fel, hogy tényleg az index művelet további performant a nagy lekérdezés képest. Ebben az esetben a lekérdezés MAXDOP értéke meghaladja az indexművelet MAXDOP értékét állíthatja be. Ezzel a módszerrel az SQL Server több processzorral, amelyek kihasználhatják a az index művelet képest, a nagy lekérdezéshez kezelésével processzorok számát tartalmazza. Ne feledje, hogy az SQL Server minden művelethez használandó szálak száma nem szabályozhatja. Szabályozhatja az éppen kijelölt processzorok maximális száma többszálas.

További információ [szintű párhuzamosság fok](https://technet.microsoft.com/library/ms188611.aspx) az SQL Server kiszolgálón. Ismerje meg ezek a beállítások, amelyek befolyásolják többszálas teljesítményének optimalizálása érdekében az alkalmazás és a konfigurációját.

## <a name="queue-depth"></a>Várólistamélység
A várólistamélység vagy a várólista hossza vagy a várólista-méret függőben lévő I/O kérelmek számát a rendszer. Várólistamélység értéke határozza meg, hány I/O műveletek az alkalmazás is sor fel, a tárolólemezek meg fog feldolgozása. Ez a cikk ti, IOPS, az átviteli sebesség és a késleltetés a tárgyalt összes három alkalmazás teljesítménymutatók is érint.

Feldolgozási sor mélysége és a többszálas szorosan kapcsolódó. Várólistamélység azt jelzi, hogy mennyit többszálas lehet érhetik el az alkalmazást. Ha a várólistamélység túl nagy, alkalmazás végrehajtható további műveletek egyidejűleg, ez azt jelenti, több többszálas. Ha a várólistamélység kicsi, annak ellenére, hogy az alkalmazás többszálas, nem lesz elég kéréseket a párhuzamos végrehajtás egymás mellett.

Általában kész alkalmazások esetében nem végezhető módosíthatja a várólistamélység, mert ha beállított helytelenül jó-nál több kárt fog tenni. Alkalmazások várólistamélységének optimális teljesítményének eléréséhez megfelelő értékét állítja be. Fontos azonban megérteni a fogalom, hogy a teljesítménnyel kapcsolatos problémák elhárítása az alkalmazás. Várólistamélység eredő összehasonlítási eszközök futtatásával a rendszeren is láthatja.

Egyes alkalmazások adja meg annak a várólistamélység befolyásolják. Például a MAXDOP (legnagyobb mértékben párhuzamossági) beállítást az előző szakaszban ismertetett SQL Server. MAXDOP módja a várólistamélység befolyásolják és többszálas, bár nem módosíthatók közvetlenül a SQL Server Várólistamélységének értékét.

*Magas várólistamélység*  
Magas várólistamélység sorok be több műveletet a lemezen. A lemez tudja a következő kérés időben a várólistán. Következésképpen a lemez időben műveletek ütemezése és az optimális sorrendben dolgozza fel őket. Az alkalmazás további kérelmeket küld a lemezt, mert a lemez további párhuzamos IOs tud feldolgozni. Végső soron az alkalmazás magasabb IOPS érhet el lesz. Alkalmazás feldolgozása további kérelmeket, mert a teljes átviteli képessége – az alkalmazás is növeli.

Általában az alkalmazás érhető el maximális átviteli sebesség a 8-16 + kiugró IO-csatlakoztatott lemez. Ha egy várólistamélység, alkalmazás elég IOs nem küldését a rendszer, és kisebb mennyiségű fogja feldolgozni egy adott időszakban. Ez azt jelenti kevesebb átviteli sebességet.

Például az SQL Server egy lekérdezést "4" MAXDOP értékének beállításakor arról értesíti az SQL Server, hogy legfeljebb négy magok használhat a lekérdezés végrehajtásához. SQL Server ajánlott várólista mélységének értékét, és a lekérdezés végrehajtása a magok száma határozza meg.

*Optimális Várólistamélységének*  
Nagyon magas várólista mélységének értékét is rendelkezik a hátrányai is. Ha várólista mélység értéke túl magas, az alkalmazás megpróbálja meghajtó nagyon magas iops-érték. Ha alkalmazás állandó lemezek, amelyek elegendő kiosztott IOPS van, ez negatívan befolyásoló kódokat is alkalmazáskéséseknek. IOPS, a késés és a várólistamélység közötti kapcsolatot a következő képlet mutatja be.  
    ![](media/premium-storage-performance/image6.png)

Minden nagy értékű, de az optimális érték, amely elegendő IOPS, az alkalmazás által biztosított késések befolyásolása nélkül ne konfigurálja a Várólistamélységet. Például, ha a kérelem várakozási ideje 1 ezredmásodpercre kell, 5000 iops teljesítményt eléréséhez szükséges várólistamélység, QD = 5000 x 0,001 = 5.

*Várólistamélység csíkozott kötetek*  
A csíkozott kötetek karbantartása elég magas várólistamélység úgy, hogy minden lemezhez tartozik egy maximális várólistamélység külön-külön. Vegye figyelembe például olyan alkalmazás, amely a leküldéses értesítések egy várólistamélység 2, és a paritásos 4 lemezek szerepelnek. A két IO kérelmek kerül, a két lemez, és két lemez fennmaradó lesz tétlen. A várólistamélység ezért konfigurálni úgy, hogy a lemezek foglalt lehet. Az alábbi képlet mutatja be a csíkozott kötetek várólistamélységének meghatározásához.  
    ![](media/premium-storage-performance/image7.png)

## <a name="throttling"></a>Throttling
Prémium szintű Storage Azure rendelkezések megadott iops-érték és átviteli száma attól függően, hogy a Virtuálisgép-méretek és a választott mérete. Az alkalmazás próbál IOPS vagy átviteli meghajtó felett Mi a virtuális gép vagy a lemez kezelni tud, működés felső korlátjának, bármikor prémium szintű Storage szabályozás fogja azt. Ez akkor jelentkezik, az alkalmazás teljesítménye formájában. Ez is nagyobb késleltetéssel járhat jelenti azt, csökkentheti a teljesítményt, illetve nem csökkentheti iops-érték. Ha prémium szintű Storage nem szabályozás, az alkalmazás teljesen feladatátadáshoz alapján meghaladó mi erőforrásaihoz képesek elérése érdekében. Igen szabályozás miatt a teljesítményproblémák elkerüléséhez mindig kiépíteni az alkalmazás elegendő erőforrással. Vegye figyelembe, mi ismertettük a Virtuálisgép-méretek és a lemez mérete a fenti szakaszban. A legjobb módja, hogy megtudja, milyen erőforrásokra lesz szüksége a az alkalmazás futtatásához teljesítménymérésre.

## <a name="benchmarking"></a>Teljesítménymérésre
Az alkalmazás a különböző terhelésekhez szimulálva és az alkalmazások teljesítményéről, az egyes munkaterhelésekhez tartozó mérési teljesítménymérésre. Egy korábbi szakaszában leírt lépések alapján összegyűjtötte alkalmazások teljesítménybeli követelményei. Összehasonlítási eszközök futtatja az alkalmazást futtató gépeken, azt is meghatározhatja, hogy az alkalmazás érhető el prémium szintű Storage a teljesítményszintet. Ez a szakasz azt biztosít egy szabványos DS14 virtuális Gépet, a prémium szintű Azure Storage lemezek kiépített teljesítménymérésre példái.

Rendelkezik használtuk közös összehasonlítási eszközök Iometer és FIO, a Windows és Linux kulcsattribútumokkal. Ezek az eszközök nem tudott származtatni egy éleshez hasonlító munkaterhelés szimulálva több szál, és mérheti a rendszer teljesítményét. Eszközök segítségével is konfigurálhatja például blokk méretét és a várólista mélység, amely általában nem módosítható az alkalmazás paramétereit. Ez lehetővé teszi annak a maximális teljesítmény nagy méretű virtuális gép különböző típusú alkalmazások és szolgáltatások a premium lemezek látva nagyobb rugalmasságot. Minden egyes összehasonlítási eszköz olvashat további [Iometer](http://www.iometer.org/) és [FIO](http://freecode.com/projects/fio).

Kövesse az alábbi példák, hozzon létre egy szabványos DS14 virtuális Gépet, és 11 prémium szintű Storage lemez csatolása a virtuális gép. A 11 lemezek-állomás gyorsítótárazását, "None" 10 lemezek konfigurálásához, valamint azokat a NoCacheWrites nevű kötet paritásos. Állomás, mint a "ReadOnly" fennmaradó gyorsítótárazás konfigurálása, és hozzon létre egy kötetet CacheReads meghívásra ezt a lemezt. A telepítővel lesz a maximális olvasási és írási teljesítményt szabványos DS14 VM megjelenítéséhez. Részletes lépéseket a premium lemezek DS14 virtuális gép létrehozása, Ugrás [létrehozása és használata a prémium szintű Storage fiókot a virtuális gép adatlemezt](../articles/virtual-machines/windows/premium-storage.md).

*A gyorsítótár előkészítése*  
A lemezt a csak olvasható-állomás gyorsítótárazását fog tudni lemez korlátnál magasabb IOPS biztosítják. Ahhoz, hogy a maximális olvasási teljesítmény a gazdagép-gyorsítótárból, először meg kell kezeléséből a gyorsítótár, a lemez. Ez biztosítja, hogy az olvasási IOs összehasonlítási eszköztől találkozik CacheReads köteten ténylegesen találatok száma a gyorsítótárban, és nem a lemezt közvetlenül. A gyorsítótár találatok eredményt a egyetlen gyorsítótárból további IOPS lemez engedélyezve van.

> **Fontos:**  
> Meg kell kezeléséből a gyorsítótár futtatása teljesítménymérésre, minden alkalommal, amikor a virtuális gép újraindítása előtt.
>
>

#### <a name="iometer"></a>Iometer
[Töltse le a Iometer eszközt](http://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download) a virtuális Gépen.

*Fájl tesztelése*  
Iometer a köteten, amelyen futtatja az összehasonlítási teszt tárolt teszt fájlt használ. Olvasási meghajtók, és írja be a teszt fájlhoz, a lemez mérésére az iops-érték és az átvitel. Iometer hoz létre a fájl tesztelése, ha nincs megadva egy. Hozzon létre egy 200 GB-os tesztfájl iobw.tst nevű CacheReads és NoCacheWrites köteteken.

*Access specifikációi*  
A specifikációk kérelem I/O méret, olvasási/írási %, % véletlenszerű/szekvenciális használatával vannak konfigurálva az "Access specifikációi" fülre az Iometer. Hozzon létre egy hozzáférési leírása az alábbiakban ismertetett forgatókönyvet. A hozzáférés specifikációk létrehozása és "Mentés" megfelelő nevet hasonlóan – RandomWrites\_8 KB-os, RandomReads\_8 KB-os. Válassza ki a megfelelő megadását, a tesztkörnyezet futtatásakor.

Maximális IOPS írási forgatókönyv vonatkozó hozzáférési példa az alábbi  
    ![](media/premium-storage-performance/image8.png)

*Maximális IOPS vizsgálati előírások*  
Maximális IOPs bemutatásához, használja a kisebb kérés méretét. Használja a 8 KB-os kérés méretét, és hozzon létre specifikációk véletlenszerű írási műveletek és olvasási műveletek.

| Hozzáférési leírása | Kérelem mérete | Véletlenszerű % | Olvasási % |
| --- | --- | --- | --- |
| RandomWrites\_8 KB-os |8 KB-OS |100 |0 |
| RandomReads\_8 KB-os |8 KB-OS |100 |100 |

*Maximális átviteli sebesség vizsgálati előírások*  
Maximális átviteli sebesség bemutatásához, használja a kérelem nagyobb méretű. Használja a 64 KB-os kérés méretét, és hozzon létre specifikációk véletlenszerű írása és olvasása.

| Hozzáférési leírása | Kérelem mérete | Véletlenszerű % | Olvasási % |
| --- | --- | --- | --- |
| RandomWrites\_64 K |64K |100 |0 |
| RandomReads\_64 K |64K |100 |100 |

*A Iometer teszt futtatása*  
Hajtsa végre a gyorsítótár kezeléséből, az alábbi lépéseket

1. Hozzon létre két access specifikációi az alábbi értékek

   | Name (Név) | Kérelem mérete | Véletlenszerű % | Olvasási % |
   | --- | --- | --- | --- |
   | RandomWrites\_1 MB |1MB |100 |0 |
   | RandomReads\_1 MB |1MB |100 |100 |
2. Futtassa a következő paraméterekkel gyorsítótár lemez inicializálása a Iometer tesztet. Három munkavégző szálat használ a célkötet és egy várólistamélység 128. Az időtartamot "Futásidejű" a vizsgálat 2hrs a "Teszt telepítő" lapon.

   | Forgatókönyv | Célkötet | Name (Név) | Időtartam |
   | --- | --- | --- | --- |
   | Gyorsítótár lemez inicializálása |CacheReads |RandomWrites\_1 MB |2hrs |
3. A gyorsítótár lemez a következő paraméterekkel előkészítése a Iometer teszt futtatása. Három munkavégző szálat használ a célkötet és egy várólistamélység 128. Az időtartamot "Futásidejű" a vizsgálat 2hrs a "Teszt telepítő" lapon.

   | Forgatókönyv | Célkötet | Name (Név) | Időtartam |
   | --- | --- | --- | --- |
   | Meleg gyorsítótár lemezre mentése |CacheReads |RandomReads\_1 MB |2hrs |

Ha gyorsítótár lemez tárolóhelyspecifikus, lépjen az alább felsorolt tesztesetek. A Iometer teszt futtatása, használja a legalább három worker threads **minden** céloz kötet. Az egyes munkavégző szál válassza ki a célkötet, várólistamélység beállítása, és válassza ki a mentett vizsgálati előírásoknak egyikét a megfelelő tesztkörnyezet futtatásához az alábbi táblázatban látható módon. Ezek a tesztek futtatása a táblázatban is látható kívánt eredmény elérése érdekében az iops-érték és a teljesítményt. Minden esetben egy kisméretű I/O mérete 8 KB-os és egy magas várólista mélysége 128 szolgál.

| Tesztkörnyezet | Célkötet | Name (Név) | Eredmény |
| --- | --- | --- | --- |
| Legfeljebb Olvasási IOPS |CacheReads |RandomWrites\_8 KB-os |50 000 IOPS |
| Legfeljebb IOPS írása |NoCacheWrites |RandomReads\_8 KB-os |64 000 IOPS |
| Legfeljebb Kombinált IOPS |CacheReads |RandomWrites\_8 KB-os |100 000 IOPS |
| NoCacheWrites |RandomReads\_8 KB-os | &nbsp; | &nbsp; |
| Legfeljebb Olvassa el a MB/s |CacheReads |RandomWrites\_64 K |524 bájtnyi MB/s |
| Legfeljebb Írás a MB/s |NoCacheWrites |RandomReads\_64 K |524 bájtnyi MB/s |
| Kombinált MB/s |CacheReads |RandomWrites\_64 K |1000 MB/s |
| NoCacheWrites |RandomReads\_64 K | &nbsp; | &nbsp; |

Az alábbiakban a vizsgálati eredmények kombinált IOPS és átviteli forgatókönyvek a Iometer példaként bemutató képernyőképeket láthat.

*Egyesített írási és olvasási maximális iops-érték*  
![](media/premium-storage-performance/image9.png)

*Egyesített írási és olvasási maximális átviteli sebesség*  
![](media/premium-storage-performance/image10.png)

### <a name="fio"></a>FIO
FIO egy olyan népszerű eszköz teljesítményteszt Storage a Linux virtuális gépeken. Jelölje be a különböző IO méretű szekvenciális rugalmasságot vagy véletlenszerű olvasási rendelkezik, és írja. Az indít munkavégző szál vagy a folyamat a megadott i/o-műveletek végrehajtásához. Megadhatja, hogy milyen típusú bemeneti és kimeneti műveletet minden egyes munkavégző szál feladat fájlok használatával kell végrehajtania. Létrehoztunk egy feladat fájl / forgatókönyv az alábbi példák mutatják be. A feladat fájlokban is elvégez egy teljesítménytesztet prémium szintű Storage különböző munkaterheléseinek specifikációk módosíthatja. A példákban a szabványos DS 14 virtuális gép futó használjuk **Ubuntu**. Használja az ugyanazon a elején leírt a [szakasz teljesítménymérésre](#Benchmarking) és meleg fel a gyorsítótárban a összehasonlítási tesztek futtatása előtt.

Mielőtt elkezdené, [FIO letöltése](https://github.com/axboe/fio) , és telepíteni a virtuális gép.

A következő parancsot az Ubuntu,

```
apt-get install fio
```

A lemezeken vezetői olvasási műveletek használjuk az írási műveletek vezetéséhez négy munkaszál és négy munkaszál. Az írási munkavállalók fog kell befolyásoló tényezők forgalom értéke "None" gyorsítótárával 10 lemezekkel rendelkező "nocache lehet" köteten. Az olvasási munkavállalók fog kell befolyásoló tényezők forgalom a "readcache" köteten, amelynek 1 "ReadOnly" gyorsítótár-készlettel.

*Maximális írási IOPS*  
A feladat-fájl létrehozása a következő specifikációnak írási maximális IOPS eléréséhez. Nevezze el a "fiowrite.ini".

```
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[writer1]
rw=randwrite
directory=/mnt/nocache
[writer2]
rw=randwrite
directory=/mnt/nocache
[writer3]
rw=randwrite
directory=/mnt/nocache
[writer4]
rw=randwrite
directory=/mnt/nocache
```

Vegye figyelembe a kövesse főbb tényezőket a korábbi szakaszokban ismertetett tervezési irányelveket összhangban legyenek. Ezen specifikációk elengedhetetlenek meghajtó maximális iops-ről,  

* Magas várólistamélység 256.  
* Kis blokkméret 8 KB-os.  
* Több szál véletlenszerű írási műveleteket végez.

A következő parancsot a FIO teszt 30 másodpercig indítsa  

```
sudo fio --runtime 30 fiowrite.ini
```

A teszt végrehajtása közben lesz olvassa el a ennyi írási iops-érték a virtuális gép és a Premium lemezek hoz létre. Ahogy az alábbi minta, a DS14 VM elkötelezett a maximális IOPS legfeljebb 50 000 IOPS írási.  
    ![](media/premium-storage-performance/image11.png)

*Maximális olvasási IOPS*  
A feladat-fájl létrehozása a következő specifikációnak olvasási maximális IOPS eléréséhez. Nevezze el a "fioread.ini".

```
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache
```

Vegye figyelembe a kövesse főbb tényezőket a korábbi szakaszokban ismertetett tervezési irányelveket összhangban legyenek. Ezen specifikációk elengedhetetlenek meghajtó maximális iops-ről,

* Magas várólistamélység 256.  
* Kis blokkméret 8 KB-os.  
* Több szál véletlenszerű írási műveleteket végez.

A következő parancsot a FIO teszt 30 másodpercig indítsa

```
sudo fio --runtime 30 fioread.ini
```

A teszt végrehajtása közben lesz jelenik meg: száma iops-érték a virtuális gép és a prémium szintű lemezekhez hoz létre. Ahogy az alábbi minta, a DS14 VM elkötelezett több mint 64 000 olvasási iops-érték. Ez az a lemez és a gyorsítótár teljesítménye.  
    ![](media/premium-storage-performance/image12.png)

*Maximális olvasási és írási IOPS*  
A feladat-fájl létrehozása a következő specifikációk megszerezni a maximális olvasási és írási IOPS együtt. Nevezze el a "fioreadwrite.ini".

```
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer2]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer3]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer4]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
```

Vegye figyelembe a kövesse főbb tényezőket a korábbi szakaszokban ismertetett tervezési irányelveket összhangban legyenek. Ezen specifikációk elengedhetetlenek meghajtó maximális iops-ről,

* Magas várólista mélysége 128.  
* Kis blokkméret 4 KB-os.  
* Véletlenszerű végrehajtása több szál beolvassa és.

A következő parancsot a FIO teszt 30 másodpercig indítsa

```
sudo fio --runtime 30 fioreadwrite.ini
```

A teszt végrehajtása közben lesz, kombinált olvasás száma, és írja iops-érték a virtuális gép, és a Premium lemezek hoz létre. Ahogy az alábbi minta, a DS14 VM elkötelezett több mint 100 000 kombinált olvasási és írási iops-érték. Ez az a lemez és a gyorsítótár teljesítménye.  
    ![](media/premium-storage-performance/image13.png)

*Maximális átviteli sebesség kombinált*  
A maximális beolvasandó kombinált olvasási és írási teljesítményt, a nagyobb blokkméret és nagy várólistamélység használata több szál olvasási és írási műveletek végrehajtása. Egy 64 KB-os blokkméretet és 128 várólistamélység is használhatja.

## <a name="next-steps"></a>További lépések
Ismerje meg a prémium szintű Azure Storage:

* [Premium Storage: Nagy teljesítményű tárolási szolgáltatás Azure-beli virtuális gépek számítási feladataihoz](../articles/virtual-machines/windows/premium-storage.md)  

SQL Server-felhasználók számára olvassa el az SQL Server teljesítményét gyakorlati tanácsok a cikkek:

* [Teljesítmény ajánlott eljárások az SQL Server Azure virtuális gépeken](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md)
* [Prémium szintű Storage nyújt az SQL Server Azure virtuális gép a lehető legjobb teljesítmény](http://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)
