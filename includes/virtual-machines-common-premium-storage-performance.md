# <a name="azure-premium-storage-design-for-high-performance"></a>Az Azure Premium Storage: Nagy teljesítményű rendszer tervezése

Ez a cikk az Azure Premium Storage nagy teljesítményű alkalmazások létrehozásához nyújt útmutatást. Ez a dokumentum az alkalmazása által használt technológiák alkalmazandó ajánlott eljárások teljesítményének kombinálva szereplő utasítások is használhatja. Az irányelvek mutatja be, ebben a dokumentumban példaként a Premium Storage futó SQL Server rendelkezik használtuk.

Azt a ebben a cikkben a tárolási réteg teljesítmény-forgatókönyveket érintenek, miközben kell optimalizálni az alkalmazásrétegre. Például ha a SharePoint-farmok Azure Premium Storage üzemelteti, használhatja az SQL Server példákat, ez a cikk a optimalizálhatja az adatbázis-kiszolgáló. Ezenkívül optimalizálhatja a SharePoint-Farm webkiszolgáló és a legtöbb teljesítmény alkalmazáskiszolgáló.

Ez a cikk segít a válasz a következő gyakori kérdésekre az Azure Premium Storage, az alkalmazás teljesítményének optimalizálása

* Hogyan lehet az alkalmazás teljesítményének méréséhez?  
* Miért nem lát várt nagy teljesítményű?  
* Milyen tényezők befolyásolják az alkalmazás teljesítményét a Premium Storage?  
* Hogyan hajtsa végre ezeket a tényezők befolyásolják a Premium Storage az alkalmazás teljesítményét?  
* Hogyan, optimalizálhatja az IOPS, sávszélesség és késés?  

Adtunk ezeket az irányelveket kifejezetten a Premium Storage számára, mert a prémium szintű Storage futó számítási feladatok magas teljesítmény-és nagybetűket. Példák adtunk meg, ahol szükséges. Is alkalmazhat az egyes ezeket az irányelveket, Standard szintű Storage-lemezekkel IaaS virtuális gépeken futó alkalmazások.

Mielőtt elkezdené, ha új prémium szintű Storage, először olvassa el a [Premium Storage: nagy teljesítményű tárolási szolgáltatás Azure virtuális gépek számítási feladataihoz](../articles/virtual-machines/windows/premium-storage.md) és [Azure Storage méretezhetőségi és Teljesítménycéljai](../articles/storage/common/storage-scalability-targets.md)cikkeket.

## <a name="application-performance-indicators"></a>Alkalmazás teljesítménymutatók
Hogy ellenőrzéséhez, hogy az alkalmazások teljesítményének jól vagy nem a mutatók, például a teljesítmény, milyen gyors egy kérelem feldolgozása folyamatban van egy felhasználói kérelem mennyi adatot, egy kérelem feldolgozása kérelmenként, hány kér az alkalmazás feldolgozása folyamatban van egy adott időtartam, hogy mennyi ideig kapott választ a kérés elküldése után várnia a felhasználó rendelkezik. Ezek teljesítménymutatók műszaki használati, IOPS, az átviteli vagy a sávszélesség és késés.

Ebben a szakaszban ismertetjük az általános teljesítménymutatók Premium Storage keretében. A következő szakaszban gyűjtése Alkalmazáskövetelményeket, akkor megtudhatja, hogyan az alkalmazás ezen teljesítménymutatók mérésére. Később az alkalmazások teljesítményének optimalizálása a ezeket teljesítménymutatók és javaslatokkal optimalizálhatja őket befolyásoló tényezők ismertetik.

## <a name="iops"></a>IO
IOPS érték szám, amely az alkalmazás egy második, a tárolólemezeket által küldött kérelmek. Egy bemeneti/kimeneti műveleti olvashatók vagy ír, egymást sorban követő vagy véletlenszerű. Egy online kereskedelmi webhelyen például OLTP alkalmazásokat kell számos egyidejű felhasználói kérések feldolgozása azonnal elkezdődik. A felhasználói kérések beszúrása és frissítése nagy számításigényű adatbázis-tranzakciók, amely az alkalmazás gyorsan kell feldolgozni. Ezért a OLTP alkalmazások nagyon magas iops-érték szükséges. Az ilyen alkalmazások több millió, a kis- és véletlenszerű i/o-kérelmek kezelésére. Ha van ilyen alkalmazás, az alkalmazás-infrastruktúrával való optimalizálásáról IOPS kell alakítja ki. A későbbi szakaszban *alkalmazások teljesítményének optimalizálása*, tárgyaljuk részletesen összes tényezőt kell figyelembe venni magas iops-érték beolvasása.

Ha csatlakoztat egy prémium szintű tárolólemez a nagy méretű virtuális Gépen, a lemez specifikációnak megfelelően IOPS garantált számos Azure rendelkezések. Ha például P50 lemez 7500 IOPS látja el. Minden egyes nagy méretű virtuális gép méretét is rendelkezik egy is kiálló tárolókkal IOPS-korlátját. Például egy Standard GS5 virtuális gép rendelkezik 80 000 iops-érték korlátozása.

## <a name="throughput"></a>Teljesítmény
Átviteli sebesség vagy sávszélesség az alkalmazás egy megadott időszak, a tárolólemezeket által küldött adatok mennyisége. Ha az alkalmazás által végzett bemeneti/kimeneti műveletek nagy i/o-egység méretű, magas átviteli sebesség szükséges. Data warehouse-alkalmazások általában a probléma, amely egyszerre nagy részét az adatok eléréséhez, és gyakran a tömeges műveletek végrehajtása vizsgálat számításigényes műveletek. Más szóval az ilyen alkalmazásoknak nagyobb átviteli sebességet. Ha van ilyen alkalmazás, az átviteli sebesség optimalizálása az infrastruktúra kell alakítja ki. A következő szakaszban tárgyaljuk részletesen, ennek érdekében kell hangolása tényezőket.

Ha csatlakoztat egy prémium szintű storage-lemez egy nagy méretű virtuális Gépet, az Azure rendelkezések átviteli sebességet, hogy a lemez specifikációnak megfelelően. Például P50 lemez átviteli második lemezenként 250 MB építi ki. Minden egyes nagy méretű virtuális gép méretét is rendelkezik az adott átviteli sebességhatár is kiálló tárolókkal, mint. Például Standard GS5 virtuális gép, amelynek maximális sebessége 2000 MB / másodperc. 

Nincs olyan adatátviteli sebessége és IOPS, ahogyan az alábbi képletet közötti kapcsolat.

![](media/premium-storage-performance/image1.png)

Ezért fontos meghatározni az optimális átviteli sebesség és az IOPS értékek, az alkalmazás számára szükséges. Próbálja ki az egyik optimalizálása, a másik lekérdezi eszköztípusokra is. Egy későbbi szakaszban olvashat *alkalmazások teljesítményének optimalizálása*, részletesebben ismertetjük IOPS és átviteli sebesség optimalizálása.

## <a name="latency"></a>Késés
Késés egy alkalmazás egyetlen kérést kap, küldje el a storage-lemez és az ügyfélnek a válasz elküldéséhez szükséges idő. Ez a kritikus fontosságú méri, IOPS és átviteli sebesség mellett az alkalmazás teljesítményét. Egy prémium szintű tárolólemez késései ideig tart egy kérés adatainak lekérése és a kommunikációhoz, az alkalmazásnak. A Premium Storage egységesen rövid késleltetésével – biztosít. Ha engedélyezi a csak olvasható állomás-gyorsítótárazás a prémium szintű tárolólemezeket, sokkal kisebb olvasási késés beszerezheti. Ismertetjük a lemezek gyorsítótárazása későbbi szakaszban olvashat részletesebben az *alkalmazások teljesítményének optimalizálása*.

Ha az alkalmazás magasabb IOPS és átviteli sebesség optimalizálása, negatív hatással lesz a késés, az alkalmazás. Miután az alkalmazás teljesítményének hangolása, mindig értékelje ki a késés nagy késésű váratlan viselkedés elkerülése érdekében az alkalmazás.

## <a name="gather-application-performance-requirements"></a>Alkalmazás teljesítmény-követelmények összegyűjtése
A futó Azure Premium Storage nagy teljesítményű alkalmazások tervezésének első lépése az, az alkalmazás teljesítmény-követelmények megértéséhez. Miután összegyűjtötte hálózatiteljesítmény-igények, optimalizálhatja az alkalmazás a legjobb teljesítmény elérése érdekében.

Az előző szakaszban tudjuk magyarázni a közös teljesítménymutatók, IOPS, az átviteli sebesség és a késleltetés. Meg kell adnia, hogy ezek teljesítménymutatók, amelyek létfontosságúak az alkalmazást, hogy a kívánt felhasználói élményt nyújthat. Ha például magas iops-érték szervezetünk számára legfontosabb OLTP alkalmazások másodpercenként több millió, a tranzakciók feldolgozása. Mivel a nagy átviteli sebességet, kritikus fontosságú, nagy mennyiségű adat feldolgozása másodpercenként Data Warehouse-alkalmazásokhoz. Valós idejű alkalmazások, mint a webhelyek élő videóstream rendkívül alacsony késésű létfontosságú.

Ezután mérje maximális teljesítmény-követelmények az alkalmazás teljes élettartama során. Használja az alábbi minta ellenőrzőlista kezdés. Jegyezze fel a maximális teljesítmény-követelmények normál alatt, a csúcsidőre és a munkaidőn kívül munkaterhelés pontokat. Összes számítási feladatok szintjére vonatkozó követelmények azonosításának elősegítése, akkor lesz képes meghatározni az alkalmazás az általános teljesítmény megkövetelését. Például egy e-kereskedelmi webhely a normál számítási feladatok lesz a tranzakciók, a legtöbb nap során szolgál. A maximális számítási feladatok a webhely a tranzakciók szolgál az ünnepi időszakban vagy speciális értékesítés események során lesz. Csúcsértéke általában tapasztalt korlátozott ideig, de megkövetelheti az alkalmazás méretezése két vagy több alkalommal a normál működés. Ismerje meg az 50. percentilis, 90 PERCENTILIS 99. percentilis követelményeinek. Hozzájárul a teljesítmény-követelmények kiugró adatokat kiszűréséhez, és meg is összpontosítsa figyelmét a megfelelő értékek optimalizálására.

**Alkalmazás teljesítményének követelmények ellenőrzőlista**

| **Teljesítmény-követelmények** | **50. percentilis** | **90. percentilis** | **99. percentilis** |
| --- | --- | --- | --- |
| Legfeljebb Másodpercenkénti tranzakciók | | | |
| % Olvasási művelet | | | |
| % Írási műveletek | | | |
| A(z) % véletlenszerű műveletek | | | |
| A(z) % egymást követő műveletek | | | |
| I/o-kérés mérete | | | |
| Átlagos átviteli sebesség | | | |
| Legfeljebb Teljesítmény | | | |
| Perc. Késés | | | |
| Átlagos késés | | | |
| Legfeljebb CPU | | | |
| Átlagos processzorhasználat | | | |
| Legfeljebb Memory (Memória) | | | |
| Átlagos memória | | | |
| Várólistájának mélysége | | | |

> [!NOTE]
> Érdemes megfontolni a számok alapján az alkalmazás várható jövőbeli növekedésének megfelelően. Érdemes számoljon a növekedéssel előre, mert később a teljesítmény fokozása infrastruktúrájának módosításához nehezebb lehet.
>
>

Ha egy meglévő alkalmazást, és szeretne áttérni a Premium Storage, először hozhat létre a meglévő alkalmazás a fenti feladatlista. Ezután hozhat létre az alkalmazás a Premium Storage egy prototípust, és az alkalmazás ismertetett irányelvek alapján *alkalmazások teljesítményének optimalizálása* Ez a dokumentum későbbi szakaszában. A következő szakasz segítségével gyűjtse össze a TELJESÍTMÉNYMÉRÉSEK eszközöket írja le.

Ellenőrzőlista a meglévő alkalmazás a prototípus hasonló létrehozásához. Benchmarking eszközökkel a számítási feladatokat szimulálhat, és mérhetik a teljesítményt, a prototípus-alkalmazás. A szakaszban [Benchmarking](#benchmarking) további. Így megadhatja, hogy e Premium Storage felel meg, vagy az alkalmazás teljesítményének követelményeinek előremutató módon. Ezután az alkalmazás éles üzemét ugyanez az útmutató is alkalmazható.

### <a name="counters-to-measure-application-performance-requirements"></a>Alkalmazáskövetelmények teljesítmény mérésére számlálók
A legjobb módszer az alkalmazás teljesítmény-követelmények mérheti, hogy annak a kiszolgálónak az operációs rendszer által biztosított teljesítményfigyelő eszközök használhatók. Használhatja az Windows Teljesítményfigyelőt és iostat Linux rendszeren. Ezek az eszközök tartozó összes mérték, a fenti szakaszban leírt teljesítményszámlálók rögzítése. Ezek a számlálók értékeit kell rögzíteni, amikor az alkalmazás fut, a normál, a csúcsidőre és a munkaidőn kívül számítási feladatok.

A teljesítményszámlálók processzor, memória, és minden egyes logikai lemez és a kiszolgáló fizikai lemez érhetők el. Ha prémium szintű tárolólemezeket a virtuális gép használja, a fizikai lemez számlálók prémium szintű storage lemezek, és logikai lemez számlálók a premium storage-lemezekkel létrehozott minden kötet esetében. A lemezeket, az alkalmazás számítási feladatait futtató értékeit kell rögzíteni. Ha a logikai és fizikai lemezek között egy-egy leképezést, olvassa el a fizikai lemez számlálók; Ellenkező esetben tekintse meg a logikai lemez számlálókat. Linux rendszeren a iostat parancs lemez- és CPU-kihasználtság jelentést hoz létre. A lemezhasználati jelentés biztosít a fizikai eszközön vagy a partíció statisztikai. Ha az adatok és a napló az adatbázis-kiszolgáló rendelkezik külön lemezeken, gyűjtése ezeket az adatokat a két lemez. Alábbi táblázat ismerteti a lemezek, a processzor és memória számlálói:

| Számláló | Leírás | Teljesítményfigyelés | iostat |
| --- | --- | --- | --- |
| **Iops-t vagy a tranzakció / másodperc** |Ki a tároló lemez másodpercenkénti i/o-kérések száma. |Lemezolvasások/mp <br> Lemezírások/mp |tps <br> r/s <br> w/s |
| **Lemez olvasása és írása** |% Olvasási és írási műveleteket a lemezen végzett. |% Olvasási kihasználtsága (%) <br> A(z) % lemezre írási ideje |r/s <br> w/s |
| **Átviteli sebesség** |Olvasni, vagy a lemezre másodpercenként írt adatok mennyisége. |Lemezolvasási sebesség (bájt/s) <br> Lemezírási sebesség (bájt/s) |kB_read/s <br> kB_wrtn/s |
| **Késés** |Teljes ideje a lemez i/o-kérelem végrehajtásához. |Átlagos lemez mp/Olvasás <br> Átlagos lemez mp/írás |await <br> svctm |
| **I/o-mérete** |I/o-mérete, a tárolólemezeket problémák kérelmeket. |Átlagos/olvasott bájtok <br> Átlagos írási idejének bájt/írás |avgrq-sz |
| **Várólistájának mélysége** |A kérelmek várakozási történő olvasását vagy a storage-lemezre írt szálankénti függőben lévő i/o száma. |Lemezvárólista jelenlegi hossza |avgqu-sz |
| **Max. Memória** |Zökkenőmentesen alkalmazás futtatásához szükséges memória mennyisége |Előjegyzett kihasználtsága (%) |Vmstat használata |
| **Max. PROCESSZOR** |Összeg zökkenőmentesen alkalmazás futtatásához szükséges CPU |Processzoridő |a(z) % util |

Tudjon meg többet [iostat](https://linux.die.net/man/1/iostat) és [PerfMon](https://msdn.microsoft.com/library/aa645516.aspx).

## <a name="optimizing-application-performance"></a>Alkalmazások teljesítményének optimalizálása
A Premium Storage futó alkalmazás teljesítményét befolyásoló főbb tényezőket jellegét, i/o kérések, Virtuálisgép-méretet, a lemez mérete, a lemezszám, a lemezek gyorsítótárazása, a többszálas végrehajtás és Várólistamélységének. A rendszer által biztosított belül ezek közül néhány tényező vezérelhető. A legtöbb alkalmazás nem kaphat közvetlenül módosítja az i/o-mérete és Várólistamélységének lehetőség. Ha az SQL Servert használ, például nem IO mérete és a várólista mélysége választható. Az SQL Server úgy dönt, hogy az optimális i/o mérete és a várólista mélysége értékeket a legtöbb teljesítmény. Fontos tudni, hogy az alkalmazás teljesítményre gyakorolt tényezők mindkét típusú, hogy kioszthatja a megfelelő erőforrásokon a teljesítményigények kielégítése érdekében.

Ebben a szakaszban tekintse meg az alkalmazás követelményeinek ellenőrzőlista létrehozott, azonosíthatja, hogy mennyit kell az alkalmazás teljesítményének optimalizálásához. Ez alapján fogja meg tudja határozni, mely tényezők ebben a szakaszban lévő kell finomhangolásához. Az alkalmazás teljesítményre gyakorolt egyes tényező tanúsítsa, futtassa az Alkalmazásbeállítás teljesítménymérési eszközökről. Tekintse meg a [Benchmarking](#Benchmarking) szakasz lépések Windows és Linux rendszerű virtuális gépek közös teljesítménymérési eszközökről futtathatók a jelen cikk végén található.

### <a name="optimizing-iops-throughput-and-latency-at-a-glance"></a>Egyetlen pillantással IOPS, az átviteli sebesség és a késleltetés optimalizálása

Az alábbi táblázat összefoglalja a teljesítmény tényezők és az IOPS, az átviteli sebesség és a késleltetés optimalizálásához szükséges lépések. Fogja ezt az összefoglalót alatti részek ismertetik tényező sokkal részletesebben olvashat róluk.

További tájékoztatást a Virtuálisgép-méretek és az IOPS, az átviteli sebesség és a késés érhető el az egyes virtuális Géphez, tekintse meg a [Linux Virtuálisgép-méretek](../articles/virtual-machines/linux/sizes.md) vagy [Windows Virtuálisgép-méretek](../articles/virtual-machines/windows/sizes.md).

| &nbsp; | **IOPS-ÉRTÉK** | **Átviteli sebesség** | **Késés** |
| --- | --- | --- | --- |
| **Példaforgatókönyv** |Az alkalmazás második arány nagyon magas tranzakciós igénylő vállalati OLTP. |Vállalati adattárház alkalmazás feldolgozási nagy mennyiségű adat. |Közel valós idejű a felhasználói kérések, például az online játékok azonnali válaszokat igénylő alkalmazásokhoz. |
| Teljesítmény tényezők | &nbsp; | &nbsp; | &nbsp; |
| **I/o-mérete** |I/o-mérete kisebb magasabb iops-érték alapján. |Nagyobb méretű i/o-méretezés, a nagyobb átviteli sebességet eredményez. | &nbsp;|
| **Virtuális gép mérete** |Használja a virtuális gép mérete által kínált iops-érték nagyobb, mint az alkalmazás követelményeinek. |Virtuálisgép-méretének használata átviteli sebességhatár nagyobb, mint az alkalmazás követelményeinek. |Használja a virtuális gép méretét, hogy az ajánlatok méretezése korlátok nagyobb, mint az alkalmazás követelményeinek. |
| **Lemezméret** |Használjon olyan lemezméretet, amely nagyobb, mint az alkalmazás követelményeinek IOPS kínál. |A lemez mérete nagyobb, mint az alkalmazás követelményeinek átviteli sebességhatár használja. |Használja a lemez méretét, hogy az ajánlatok méretezése korlátok nagyobb, mint az alkalmazás követelményeinek. |
| **Virtuális gép és a méretezési korlátok** |A Virtuálisgép-méretet választott IOPS-korlátját hátterében a prémium szintű tárolólemezeket csatlakoztatott teljes IOPS-nál nagyobbnak kell lennie. |A kiválasztott virtuális gép méretű átviteli sebességhatár hátterében a prémium szintű tárolólemezeket csatlakoztatott teljes átviteli sebesség nagyobbnak kell lennie. |A kiválasztott virtuális gép méretű méretkorlátai csatolt prémium szintű tárolólemezeket a teljes méretezési korlátok nagyobbnak kell lennie. |
| **A lemezek gyorsítótárazása** |Csak olvasható gyorsítótárának engedélyezése a prémium szintű tárolólemezeket olvasási nehéz műveletekkel magasabb olvasási iops-érték beolvasása. | &nbsp; |Csak olvasható gyorsítótárának engedélyezése a prémium szintű tárolólemezeket a kész nehéz műveletekkel első rendkívül kis olvasási késéseket. |
| **Lemez szétosztottsága befolyásolhatja.** |Több lemez és a stripe-el együtt egy kombinált magasabb IOPS és átviteli sebesség korlátot. Vegye figyelembe, hogy a virtuális gépenkénti kombinált korlát magasabb, mint a csatolt prémium szintű lemezek együttes keretein legyen-e. | &nbsp; | &nbsp; |
| **STRIPE-mérete** |Stripe mérete kisebb véletlenszerű kisméretű i/o minta OLTP alkalmazások látható. Például használja a stripe mérete 64 KB-os SQL-kiszolgáló OLTP-alkalmazáshoz. |Stripe mérete nagyobb látható a Data Warehouse alkalmazások szekvenciális nagy i/o minta. Például használja a stripe mérete 256 KB-os SQL Server Data warehouse alkalmazáshoz. | &nbsp; |
| **Többszálas** |Leküldéses kérelmek száma nagyobb prémium szintű Storage, amelyek magasabb IOPS és átviteli sebesség többszálas használata. Például SQL-kiszolgálón állítsa be az SQL Server több processzort lefoglalni egy nagy MAXDOP értéke. | &nbsp; | &nbsp; |
| **Várólistájának mélysége** |Nagyobb Várólistamélységének magasabb iops-érték alapján. |Nagyobb Várólistamélységének nagyobb átviteli sebességet eredményez. |Kisebb Várólistamélységének poskytne kisebb a késésük. |

## <a name="nature-of-io-requests"></a>I/o-kérések jellege
I/o-kérelmet, amely az alkalmazás fogja hajt végre a bemeneti/kimeneti műveleti egység. I/o-kérelmek, véletlenszerű vagy egymást követő, jellege azonosítása olvasási vagy írási, kis és nagy, segít az alkalmazás teljesítmény-követelmények meghatározása. Nagyon fontos i/o-kérések a helyes döntések meghozatalában az alkalmazás-infrastruktúra tervezésekor jellegének megismerése.

I/o-mérete az egyik legfontosabb tényezők. Az IO érték a bemeneti/kimeneti műveleti kérelem, az alkalmazás által generált méretét. Az i/o-mérete jelentős hatással van a teljesítményre, különösen az IOPS és sávszélesség, amelyekkel az alkalmazás érhető el. A következő képletet az IOPS, közötti kapcsolat látható i/o-mérete és a sávszélesség/átviteli sebesség.  
    ![](media/premium-storage-performance/image1.png)

Bizonyos alkalmazások lehetővé teszik, hogy azok i/o-mérete, alter, míg bizonyos alkalmazások nem. Például az SQL Server maga optimális i/o-méretét határozza meg, és nem biztosít semmilyen belül módosítani, a felhasználók. Másrészről, az Oracle biztosít egy paraméter, [DB\_LETILTÁSA\_mérete](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) használatával konfigurálható az adatbázis i/o-kérés méretétől.

Ha egy alkalmazás, amely nem engedélyezi, hogy módosítani az i/o-mérete, használja, akkor ez a cikk használja az útmutatást KPI-t, amely a legjobban megfelelő az alkalmazás teljesítményének optimalizálásához. Például:

* OLTP alkalmazások több millió, a kis- és véletlenszerű i/o-kérések állít elő. Az ilyen típusú i/o-kérelmek kezelésére, az alkalmazás-infrastruktúra beolvasni a magasabb IOPS alakítja ki kell.  
* Egy adattárház-alkalmazást hoz létre nagy és szekvenciális i/o-kérések. Az ilyen típusú i/o-kérelmek kezelésére, az alkalmazás-infrastruktúra nagyobb sávszélességet vagy átviteli sebesség alakítja ki kell.

Ha egy alkalmazás, amely lehetővé teszi, hogy módosítani az i/o-mérete, használja a tapasztalatok használata az i/o-mérethez mellett egyéb teljesítmény irányelvek

* Kisebb i/o-méretet az magasabb iops-érték beolvasása. Ha például 8 KB-os OLTP-alkalmazáshoz.  
* Nagyobb i/o-mérete az nagyobb sávszélesség/átviteli sebesség lekérdezése. Ha például 1024 KB egy data warehouse alkalmazáshoz.

Íme egy példa a módját, kiszámíthatja az IOPS és átviteli sebesség/sávszélesség az alkalmazáshoz. Vegyünk egy alkalmazást, a típus P30 lemez használatával. A maximális IOPS és átviteli sebesség/sávszélesség P30 lemez érhető el érték 5000 iops értékre, és 200 MB másodpercenként jelölik. Most ha az alkalmazás által kért a maximális P30 lemez IOPS és i/o kisebb mint 8 KB-os méretet használja, az eredményül kapott fogja tudni beolvasni a sávszélesség 40 MB / másodperc. Azonban ha az alkalmazás P30 lemez maximális átviteli sebesség/sávszélességet igényel, például 1024 KB nagyobb i/o-méretet használja, az eredményül kapott IOPS lesz kisebb, 200 iops-t. Az i/o-mérete ezért hangolása úgy, hogy mind az alkalmazás IOPS és átviteli sebesség/sávszélesség követelménynek megfelel-e. Az alábbi táblázat összefoglalja a különböző méretű i/o és azok megfelelő IOPS és átviteli sebesség P30 lemez.

| Alkalmazás követelményeinek | I/o-mérete | IO | A sávszélesség/átviteli sebesség |
| --- | --- | --- | --- |
| Maximális IOPS-érték |8 KB |5000 |40 MB / s |
| Maximális átviteli sebesség |1024 KB |200 |200 MB / s |
| Maximális átviteli sebesség és a magas iops-érték |64 KB |3,200 |200 MB / s |
| Maximális iops-érték és nagy átviteli sebesség |32 KB |5000 |160 MB / s |

Iops-t és a egy egyetlen prémium szintű tárolólemez maximális értéke magasabb sávszélesség, amelyet több prémium szintű lemezek csíkozott együtt. Például stripe két P30 lemez beolvasni egy 10 000 IOPS kombinált iops-érték vagy egy összesített átviteli sebesség 400 MB / másodperc. A következő szakaszban leírtak kell használnia a virtuális gép méretét, amely támogatja a kombinált lemez IOPS és átviteli sebesség.

> [!NOTE]
> Növelésével vagy iops-érték, vagy a másik is növeli a teljesítményt, ellenőrizze, hogy nem kattint az átviteli sebesség vagy IOPS-korlátok, a lemez vagy a virtuális gép Ha valamelyikre növelése.
>
>

A virtuális gép és a lemezek i/o-mérete hatásai alkalmazásteljesítmény tanúsítsa, futtathatja teljesítménymérési eszközökről. Hozzon létre több tesztelések és az egyes futtatások különböző IO-méret-azonosítókra gyakorolt hatást. Tekintse meg a [Benchmarking](#Benchmarking) szakaszban olvashat a jelen cikk végén található.

## <a name="high-scale-vm-sizes"></a>Nagy méretű Virtuálisgép-méretek
Amikor elindít egy alkalmazást, ehhez az első dolog, válasszon egy virtuális Gépet az alkalmazások üzemeltetéséhez. A Premium Storage nagy méretezési csoport Virtuálisgép-méretek, nagyobb számítási teljesítmény és a egy helyi lemez magas i/o-teljesítményt igénylő alkalmazások futtatható tartalmaz. Ezek a virtuális gépek gyorsabb processzorokkal rendelkeznek, a nagyobb memória – mag arány és a egy Solid-State meghajtó (SSD) adja meg a helyi lemezen. Példák a nagy méretezési csoport virtuális gépek támogatná a Premium Storage a DS, DSv2 és GS sorozatú virtuális gépek.

Virtuális gépek méretezéséhez magas CPU magok, memória, az operációs rendszer és ideiglenes lemez méretét az eltérő számú különböző méretű érhető el. Minden Virtuálisgép-méret legfeljebb hány adatlemez csatolható a virtuális géphez is tartalmaz. Ezért hatással lesz a kiválasztott Virtuálisgép-méretet, mekkora feldolgozási, memória, és a tárolási kapacitás érhető el az alkalmazást. Azt is hatással van a számítási és tárolási költsége. Ha például az alábbiakban egy DS, DSv2 sorozat és a egy GS-sorozat a legnagyobb virtuális gép méretének előírásait:

| Virtuális gép mérete | Processzormagok | Memory (Memória) | Virtuális gép lemezméretek | Legfeljebb Adatlemezek | Gyorsítótár mérete | IO | A sávszélesség-gyorsítótár i/o-korlátozások |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS14 |16 |112 GB |AZ OPERÁCIÓS RENDSZER 1023 GB = <br> Helyi SSD 224 GB = |32 |576 GB |50 000 IOPS-ÉRTÉK <br> 512 MB / s |4000 IOPS és 33 MB / s |
| Például a Standard_GS5 |32 |448 GB |AZ OPERÁCIÓS RENDSZER 1023 GB = <br> Helyi SSD 896 GB = |64 |4224 GB |80 000 IOPS <br> 2000 MB / s |5000 IOPS és az 50 MB / s |

Szeretné megtekinteni az összes elérhető Azure-beli Virtuálisgép-méretek teljes listáját, tekintse meg [Windows Virtuálisgép-méretek](../articles/virtual-machines/windows/sizes.md) vagy [Linux Virtuálisgép-méretek](../articles/virtual-machines/linux/sizes.md). Válassza ki a virtuális gép méretét, amelyek megfelelnek és méretezhető, a kívánt alkalmazás teljesítményre vonatkozó követelmények. Ezenkívül a Virtuálisgép-méretek kiválasztásakor a következő fontos szempontokat figyelembe venni.

*Kibővített skálázási korlátai*  
A maximális IOPS-korlátok virtuális gépenként és a egy lemezen különböző és egymástól független. Győződjön meg arról, hogy az alkalmazás a virtuális gép, valamint a hozzá csatolt prémium szintű lemezeket keretein belül okoznának iops-t. Ellenkező esetben alkalmazás teljesítményének szabályozása fog tapasztalni.

Például tegyük fel, hogy egy alkalmazás követelmény, legfeljebb 4000 iops-t. Ennek érdekében a virtuális gép DS1 P30 lemezek üzembe helyezésekor. A típus P30 lemez legfeljebb 5000 iops-t biztosíthat. Azonban a DS1 virtuális Gépet a 3,200 IOPS korlátozódik. Ebből következően fog kell korlátozza az alkalmazás teljesítményének 3,200 iops-t a virtuális gép a korlátot, és lesz a teljesítmény csökkenését. Ennek elkerülése érdekében válasszon egy virtuális gép és a lemez mérete, amely mindkét alkalmazás követelményeinek.

*A művelet költsége*  
Sok esetben az lehet, hogy a művelet a Premium Storage használatával teljes költsége alacsonyabb, mint a standard szintű tárolást használ.

Vegyük példaként egy 16 000 iops-t igénylő alkalmazás. A teljesítmény eléréséhez szüksége lesz egy Standard\_D14 Azure IaaS virtuális gépek, amelyek megadják a maximális IOPS-16000 32 standard storage-1 TB-os lemez használatával. Minden 1TB standard szintű tárolólemez-500 IOPS maximális érheti el. Ez a virtuális gép havi becsült költsége $1,570 lesz. A havi költségét 32 standard szintű tárolólemezek $1,638 lesz. A becsült havi összköltség $3,208 lesz.

Azonban ha, a Premium Storage ugyanazt az alkalmazást, szüksége lesz kisebb Virtuálisgép-mérettel és kevesebb prémium szintű tárolólemezeket, így csökken a teljes költség. Standard\_DS13 virtuális gép megfelel a 16 000 IOPS követelmény négy P30 lemez használatával. A DS13 virtuális gépen a maximális IOPS-25,600, és minden P30 lemezhez tartozik egy maximális 5000 IOPS. Összességében érheti el ezt a konfigurációt, 5 000 x 4 = 20 000 iops-t. Ez a virtuális gép havi becsült költsége $1,003 lesz. A havi költségét négy P30 prémium szintű tárolólemezeket $544.34 lesz. A becsült havi összköltség $1,544 lesz.

Az alábbi táblázat összefoglalja a költségek bontásban tartalmazza az ebben a forgatókönyvben a Standard és prémium szintű Storage.

| &nbsp; | **Standard** | **Prémium** |
| --- | --- | --- |
| **Virtuális gép költsége havonként** |$1,570.58 (standard\_D14) |$1,003.66 (standard\_DS13) |
| **Disks díja havonta** |$1,638.40 (32 x 1 TB-os lemez) |$544.34 (4 x P30 lemez) |
| **Havi összköltség** |$3,208.98 |$1,544.34 |

*Linux-Disztribúciók*  

Az Azure Premium Storage szolgáltatás teljesítménye a virtuális gépek azonos szintű Windows és Linux rendszerű. Támogatjuk a Linux-disztribúciók számos változata, és láthatja, hogy a teljes listát [Itt](../articles/virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Fontos megjegyezni, hogy különböző disztribúciók jobban megfelelnek a különböző típusú számítási feladatokat. Látni fogja a disztribúció, a számítási feladat fut a függően különböző teljesítményszintet. Tesztelje az alkalmazását a Linux-disztribúciók, és válassza ki azt, amelyik leginkább megfelel.

Ha Linux rendszerű a Premium Storage, ellenőrizze a nagy teljesítmény biztosítása érdekében szükséges illesztőprogramokkal kapcsolatos a legújabb frissítéseket.

## <a name="premium-storage-disk-sizes"></a>Prémium tárolási lemezméretek
Az Azure Premium Storage jelenleg nyolc adatlemez-méretet biztosít. Minden lemezméret esetében a különböző méretezési IOPS-, sávszélesség-és tárolási. Válassza ki a jobb oldalon az alkalmazás követelményei és a nagy méretű virtuális gép méretétől függően a prémium szintű Storage-lemez mérete. Az alábbi táblázat a nyolc lemezek méretek és azok képességeinek. P4 P6 és p15-ös méretek jelenleg csak a Managed Disks esetében támogatott.

| Prémium szintű lemezek típusa  | P4    | P6    | P10   | P15 | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| Lemezméret           | 32 GB | 64 GB | 128 GB| 256 GB| 512 GB            | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOPS-érték lemezenként       | 120   | 240   | 500   | 1100 | 2300              | 5000              | 7500              | 7500              | 
| Adattovábbítás lemezenként | 25 MB / s  | 50 MB / s  | 100 MB / s |125 MB / s | 150 MB / s | 200 MB / s | 250 MB / s | 250 MB / s | 


Hány lemezek határozza meg, hogy a lemez méretét a választott. Egyetlen P50 lemez vagy több P10 lemezt használhat az alkalmazás követelményeinek kielégítése érdekében. Amikor a választás az alább felsorolt fiókok és jogosultságok figyelembe.

*Skálázási korlátait (IOPS és átviteli sebesség)*  
Minden egyes prémium szintű lemez méretének IOPS és átviteli sebesség korlátairól a virtuális gépek kibővített skálázási korlátai különböző és független. Ügyeljen arra, hogy a teljes IOPS és átviteli sebesség a lemezeket a méretezési korlátok a kiválasztott virtuális gép mérete.

Például ha egy alkalmazás követelmény, legfeljebb 250 MB/s átviteli sebesség és a egy DS4 virtuális gép használata egyetlen P30 lemez. DS4 virtuális gép akár 256 MB/s teljesítményt biztosíthat. Egyetlen P30 lemez viszont 200 MB/s átviteli sebességhatár rendelkezik. Ennek következtében az alkalmazás korlátozott 200 MB/s a lemez miatt lehet. Ez a korlátozás áthidalható, adatlemezeket a virtuális gép kiépítése egynél több, vagy a P40 vagy P50 lemez átméretezése.

> [!NOTE]
> Olvasás a gyorsítótárból által kiszolgált nem szerepelnek a lemez IOPS és átviteli sebesség, ezért nem tárgy, a korlátok. Gyorsítótár tartalmaz virtuális gépenként külön IOPS és átviteli sebesség korlátját.
>
> Például kezdetben az írások és olvasások: 60MB/mp és 40MB/s jelölik. Az idő múlásával a gyorsítótár bemelegedett, és több és több az olvasások szolgál a gyorsítótárból. Ezután magasabb írási teljesítményt érheti el a lemez.
>
>

*Lemezek száma*  
Alkalmazáskövetelmények értékelése alapján kell lemezek számát határozza meg. Minden virtuális gép méretét is esetében, amely lehet kapcsolódni a virtuális gép lemezeinek száma. Ez általában kétszer a magok számát. Győződjön meg arról, hogy a Virtuálisgép-méret mellett dönt is támogatja, a lemezek szükséges számát.

Ne feledje, hogy a prémium szintű tárolólemezeket a standard szintű tárolólemezek képest nagyobb teljesítményt képességek rendelkezik. Ezért ha alkalmazását az Azure IaaS virtuális gépek Premium Storage Standard Storage használatával telepít át, valószínűleg szüksége lesz az alkalmazás a megegyező vagy annál nagyobb teljesítmény elérése érdekében kevesebb prémium szintű lemezek.

## <a name="disk-caching"></a>A lemezek gyorsítótárazása
Nagy virtuális gépek méretezéséhez, az Azure Premium Storage egy többrétegű gyorsítótárazási technológia BlobCache nevű rendelkezik. BlobCache a virtuális gép RAM memória és helyi SSD felhasználja a gyorsítótárazáshoz. Ez a gyorsítótár állandó Premium Storage-lemez és a virtuális gép helyi lemezek érhető el. Alapértelmezés szerint a gyorsítótár beállítás értéke az írási/olvasási operációsrendszer-lemezek és a csak olvasható a Premium Storage-ban üzemeltetett adatlemezek. A lemez engedélyezve van, a prémium szintű tárolólemezeket a gyorsítótárazás, a nagy méretű virtuális gépeket rendkívül magas teljesítményszintet is garantálja az alapul szolgáló lemez teljesítménye keretet túllépő érheti el.

> [!WARNING]
> Az Azure-lemez gyorsítótár beállításainak megváltoztatása leválasztja, és újra csatolja a céllemezt. Ha az operációsrendszer-lemez, a virtuális gép újraindul. Állítsa le az összes alkalmazások és szolgáltatások, amelyek hatással lehetnek a megszakítás a lemez gyorsítótár-beállítás módosítása előtt szerint.
>
>

BlobCache működésével kapcsolatos további tudnivalókért tekintse meg a belső [Azure Premium Storage](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/) blogbejegyzést.

Fontos a megfelelő lemezek készlete, a gyorsítótár engedélyezése. E lehetővé kell tennie, a lemezek gyorsítótárazása a prémium szintű lemezt, vagy nem függ a számítási feladatok mintájának lemezen kezelése lesz. Az alábbi táblázat az alapértelmezett operációsrendszer- és adatlemezek gyorsítótárazási beállításai.

| **Lemez típusa** | **Alapértelmezett az Ügyfélgyorsítótár beállítása** |
| --- | --- |
| Operációsrendszer-lemez |Az olvasási és írási |
| Adatlemez |ReadOnly |

Az alábbiakban az adatlemezeket, ajánlott lemez gyorsítótárazási beállításai

| **Gyorsítótárazási beállítás** | **Mikor érdemes ezt a beállítást a javaslat** |
| --- | --- |
| None |Gazdagép-gyorsítótár sem csak írási és írási műveltekből lemezek konfigurálása. |
| ReadOnly |Csak olvasható gazdagép-gyorsítótár konfigurálása az olvasási és írási-olvasási lemezek. |
| Az olvasási és írási |Gazdagép-gyorsítótár konfigurálja az olvasási és írási csak akkor, ha az alkalmazás megfelelően kezeli a gyorsítótárazott adatok írását szükség esetén állandó lemezt. |

*Csak olvasható*  
A Premium Storage-adatok gyorsítótárazása lemezek ReadOnly konfigurálásával érhet el alacsony olvasási késés, és nagyon magas olvasási IOPS és átviteli sebesség lekérése az alkalmazáshoz. Ez a két okok miatt

1. Olvasás a gyorsítótárból, amely a Virtuálisgép-memória és helyi SSD történik, sokkal gyorsabb, mint az olvasások az adatlemezt, amely az Azure blob storage-ból.  
1. A Premium Storage nem számít az olvasási gyorsítótár, a lemez iops-érték felé és az átviteli sebesség szolgáltatja. Az alkalmazás ezért magasabb teljes IOPS és átviteli sebességet érhet el.

*Az olvasási és írási*  
Alapértelmezés szerint az operációsrendszer-lemezek rendelkezik olvasási és írási gyorsítótárazás engedélyezve van. Nemrég hozzáadtuk az olvasási és írási gyorsítótárazást az adatokat, valamint a lemezek támogatása. Ha az olvasási és írási gyorsítótárazást használ, egy megfelelő módon az adatokat a gyorsítótárból írni állandó lemezt kell rendelkeznie. Például az SQL Server kezeli a gyorsítótárazott adatok írása az állandó tárolólemezeket önállóan. Az olvasási és írási gyorsítótár használata egy alkalmazás, amely nem kezeli a szükséges adatok megőrzése vezethet az adatvesztést, ha a virtuális gép leáll.

Tegyük fel, alkalmazhatja ezeket az irányelveket az SQL Server a Premium Storage az alábbiak szerint

1. "Csak olvasható" gyorsítótár konfigurálja a premium storage-lemezeket üzemeltető adatfájlokat.  
   a.  A gyors beolvassa a gyorsítótár alacsonyabb az SQL Server lekérdezéskor, mivel adatoldalas sokkal gyorsabban lekért a gyorsítótár képest az adatok közvetlenül a lemezeket.  
   b.  Szolgáltató olvasás a gyorsítótárból, azt jelenti, hogy további átviteli áll rendelkezésre a prémium szintű adatlemezeket. Az SQL Server is használja fel az adatok több lapot, és más műveletek, például a biztonsági mentési és visszaállítási beolvasása a további átviteli, a batch-terhelések és index újraépíti.  
1. Konfigurálja a prémium szintű tárolólemezeket a naplófájlokat tároló "None" gyorsítótárazni.  
   a.  Naplófájlok elsősorban az írási műveletek rendelkezik. Ezért azok nem tudják igénybe a csak olvasható gyorsítótárának.

## <a name="disk-striping"></a>Lemez szétosztottsága befolyásolhatja.
Amikor egy nagy méretű virtuális gép csatlakoztatva van a több prémium szintű storage állandó lemezt, a lemezek az IOPs, sávszélesség- és tárolási kapacitás összesítenie együtt is csíkozott.

A Windows használhatja a tárolóhelyek stripe lemezekre együtt. Konfigurálnia kell egy oszlopot az egyes lemezek a készletben. Ellenkező esetben csíkozott kötetek általános teljesítményét alacsonyabb, mint a lemezek adatforgalom eloszlása egyenletlen miatt a várt lehet.

Fontos: A kiszolgáló-kezelő felhasználói felületén, legfeljebb 8 csíkozott kötetek az oszlopok száma állíthatók be. Több mint 8 lemez csatolásához a PowerShell használatával létrehozni a kötetet. PowerShell használatával beállíthatja az oszlopok száma egyenlő a lemezek számát. Például, ha nincsenek 16 lemez egyetlen stripe meg; Adja meg a 16 oszlopai a *NumberOfColumns* paraméterében a *New-VirtualDisk* PowerShell-parancsmagot.

Linux rendszeren használja a stripe-lemezek MDADM segédprogrammal együtt. A részletes lépéseket csíkozást lemezeken Linux rendszeren való [szoftver RAID konfigurálása linuxon](../articles/virtual-machines/linux/configure-raid.md).

*STRIPE-mérete*  
Egy lemez szétosztottsága befolyásolhatja a fontos konfigurációs a stripe-méretét. A stripe-mérettel vagy blokkméret a legkisebb adattömb, amely alkalmazás a csíkozott kötetek. A stripe mérete, konfigurálja a típusa, alkalmazás és a kérelem minta függ. Ha úgy dönt, hogy a megfelelő stripe mérete, i/o-illesztését, ami az alkalmazás teljesítményének csökkenését eredményezheti.

Például ha az alkalmazás által generált egy i/o-kérelem a lemezméret stripe-nál nagyobb méretű, a tárolórendszer írja azt határai között egynél több lemezen. Amikor az adatok eléréséhez, hogy a kérés teljesítéséhez egynél több stripe száma közötti lesz. Viselkedést halmozott hatása jelentős teljesítményromlást eredményezhet. Másrészt az i/o-kérés mérete kisebb, mint a stripe-méret esetén, és ha véletlenszerű jellegűek, az i/o-kérelmek vehet ugyanazon a lemezen szűk keresztmetszetet okoz, és végső soron gyorsítótárazhatók az i/o-teljesítményt.

Számítási feladat típusától függően az alkalmazás fut, válassza ki egy megfelelő stripe-méretet. A véletlenszerű kisméretű i/o kéréseket használja a stripe kisebb méretet. Mivel a kérések nagy szekvenciális i/o nagyobb stripe-méretet használja. Ismerje meg, a paritásos javaslatok a méretekkel kapcsolatban az alkalmazás a Premium Storage fog futni. Az SQL Server esetében konfigurálja a stripe mérete 64 KB-os OLTP számítási feladatok és az adattárházak munkaterhelése 256KB. Lásd: [teljesítmény ajánlott eljárások az SQL Server Azure virtuális gépeken](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md#disks-guidance) további.

> [!NOTE]
> Akkor is stripe-együtt legfeljebb 32 prémium szintű tárolólemezeket a DS sorozatú virtuális gép és a egy GS sorozatú virtuális gépek 64 prémium szintű tárolólemezeket.
>
>

## <a name="multi-threading"></a>Többszálas
Az Azure Premium Storage platform, amely a nagymértékben párhuzamos van kialakítva. Egy több szálon futó alkalmazást, ezért sokkal nagyobb teljesítményű, mint egy egyszálas alkalmazás éri el. Egy több szálon futó alkalmazás bontja fel a feladatokat több szál között, és növelheti a hatékonyságot futtatása a virtuális gép és a lemez erőforrások maximális kihasználásával.

Például ha az alkalmazás egyetlen mag két szálat használ a virtuális gép fut, a Processzor között válthat a két szállal hatékonyság elérése érdekében. Amíg egy szál vár a lemez i/o végrehajtásához, a Processzor válthat más szálat. Ezzel a módszerrel két szállal végezheti el több mint egyetlen szálból lenne. Ha a virtuális gépen egynél több mag, tovább csökkenti futási időt, mivel egyes maghoz feladatokat hajthat végre párhuzamosan.

Nem lehet megváltoztatni olyan megoldásszolgáltatóknál alkalmazás valósít meg egyetlen szálkezelési vagy többszálas. Például az SQL Server Többprocesszoros és Többmagos kezelésére alkalmas állapotban. Azonban az SQL Server úgy dönt, hogy milyen feltételek mellett, a lekérdezés feldolgozása egy vagy több szál használja. Lekérdezéseket futtathat, és használja a többszálas indexeket készíthet. Egy lekérdezést, amely magában foglalja a nagy táblázatok összekapcsolása és az adatok rendezése a felhasználó való visszatérés előtt az SQL Server több szálon valószínűleg alkalmaznak. Azonban a felhasználó-e az SQL Server végrehajtja a lekérdezést az egyetlen szálból vagy több szál nem szabályozhatja.

Nincsenek konfigurációs beállítások, amely befolyásolhatja ennek megváltoztathatja, többszálas vagy párhuzamos kérelem feldolgozása. Például az SQL Server esetén fontos a legnagyobb mértékben a párhuzamosság konfiguráció. Ez a beállítás nevű MAXDOP, konfigurálhatja az SQL-kiszolgáló is használhatja, amikor párhuzamos processzorok maximális száma a feldolgozási. Az egyes lekérdezések vagy Indexműveletek MAXDOP konfigurálható. Ez akkor hasznos, ha a rendszer a teljesítmény kritikus fontosságú alkalmazásokhoz erőforrások terheléselosztással szétosztani kívánt.

Tegyük fel például, az alkalmazás használatával az SQL Server nagy lekérdezés és a egy időben index művelet végrehajtása. Tegyük fel, hogy szeretné-e az index művelet több nagy teljesítményű, a nagy lekérdezési képest. Ebben az esetben beállíthatja a MAXDOP értéke magasabb, mint a lekérdezés a MAXDOP értéke lehet a index művelet. Ezzel a módszerrel az SQL Server rendszer további processzorok számát, amelyek kihasználhatják a az index művelet képest, a nagy méretű lekérdezést is forrásfájloktól processzorok száma. Ne feledje, hogy Ön szabályozza az SQL Server minden művelethez használandó szálak számát. Szabályozhatja, hogy folyamatban van a dedikált processzorok maximális száma többszálas.

Tudjon meg többet [a párhuzamossági fokot](https://technet.microsoft.com/library/ms188611.aspx) az SQL Server. Ismerje meg, amelyek befolyásolják a beállítások többszálas, a teljesítmény optimalizálása érdekében az alkalmazás és a konfigurációját.

## <a name="queue-depth"></a>Várólistájának mélysége
A Várólistamélységének vagy a várólista hossza vagy a várólista mérete függőben lévő i/o-kérések számát a rendszer. Várólistamélység értéke határozza meg, hány i/o-műveletek, az alkalmazás is vyrovnat, a tárolólemezeket meg fogja feldolgozni. A három alkalmazást az összes teljesítménymutatók, amely már volt szó az ebben a cikkben ti, IOPS, az átviteli sebesség és a késés hatással van.

Várólista-mélységének és a többszálas szorosan kapcsolódó. Várólistamélység értéke azt jelzi, hogy mennyit többszálas lehet érhető el, hogy az alkalmazás. Ha a Várólistamélységének nagy, alkalmazás is további műveletek végrehajtásához egyidejűleg, más szóval több többszálas. Ha a Várólistamélységének kicsi, annak ellenére, hogy alkalmazás több szálon futó, nem lesz elég kérelmek egyidejű végrehajtás egymás mellett.

Általában kész alkalmazások tiltása módosíthatja a várólistamélységének, mert ha a beállított helytelenül fog tenni több károkat, mint a megfelelő választás. Kérelmek várólistájának mélysége az optimális teljesítmény eléréséhez megfelelő értékét állítja be. Fontos azonban, a fogalom megértéséhez, hogy az alkalmazás a teljesítménnyel kapcsolatos problémák elhárítását. Eredő várólistamélységének is is megfigyelheti, hogy a rendszer teljesítménymérési eszközökről történő futtatásával.

Egyes alkalmazások adja meg a beállításokat a Várólistamélységének befolyásolhatja. Például a MAXDOP (párhuzamosság maximális foka) beállítást az előző szakaszban ismertetett SQL Serveren. MAXDOP módja a befolyásolhatja az Várólistamélységének és többszálas, bár ez nem közvetlenül módosítja az SQL Server Várólistamélységének értékét.

*Magas várólistájának mélysége*  
Magas várólistamélységének vonalak másolatot a lemezen lévő több műveletet. A lemez a következő kérelmet a várólistában előre tudja. A lemez rendszernek, operations előre ütemezhet, és a egy optimális sorrendben feldolgozni azokat. Az alkalmazás több kérést küld a lemezen, mivel a lemez több párhuzamos IOs tud feldolgozni. Végső soron az alkalmazás magasabb IOPS érhető el lesz. Mivel az alkalmazás több a kérés feldolgozása folyamatban van, az alkalmazás teljes átviteli is nő.

Általában egy alkalmazás érhető el, maximális átviteli sebesség 8 – 16 + csatolt lemezenkénti szálankénti függőben lévő IOs. Ha egy Várólistamélységének, alkalmazás van nem leküldése elegendő IOs a rendszer, és kisebb mennyiségű fogja feldolgozni egy adott időszakban. Más szóval kisebb teljesítményt.

Például az SQL Server, a MAXDOP értéke egy lekérdezés "4"-re állítja tájékoztatja az SQL Server, hogy ezáltal az legfeljebb négy magot hajtsa végre a lekérdezést. Az SQL Server meghatározza, milyen ajánlott várólista mélységének értékét, és a lekérdezés végrehajtása a magok számát.

*Optimális Várólistamélységének*  
Nagyon magas várólista mélységének értékét a hátrányai is tartalmaz. Ha várólista mélység értéke túl magas, az alkalmazás megpróbálja nagyon magas IOPS-meghajtó. Ha az alkalmazás állandó lemezt, és elegendő kiosztott IOPS rendelkezik, ez negatív hatással lehet alkalmazás késéseket. Az IOPS, késés és Várólistamélységének közötti kapcsolat a következő képlet mutatja.  
    ![](media/premium-storage-performance/image6.png)

Minden nagy értékű, de az optimális érték, amely továbbítható elegendő IOPS, az alkalmazás működésének megzavarása nélkül megtesztelheti az késleltetések ne konfigurálja a Várólistamélységet. Például az alkalmazás késés kell lennie az 1 MS, a Várólistamélységének 5000 IOPS eléréséhez szükséges-e, QD = 5000 x 0,001 = 5.

*A csíkozott kötetek várólistájának mélysége*  
Egy csíkozott kötet úgy, hogy minden lemezhez tartozik egy maximális várólistamélységének külön-külön karbantartása elég nagy a várólistamélységet. Vegyük példaként egy alkalmazás, amely a leküldéses értesítések található 2 és 4 lemez található a stripe. A két i/o-kérések halad át a két lemez és a fennmaradó két lemez inaktív lesz. A várólistamélységének ezért konfigurálni úgy, hogy az összes lemez lehet foglalt. Az alábbi képlet mutatja be a csíkozott kötetek várólistamélységének meghatározásához.  
    ![](media/premium-storage-performance/image7.png)

## <a name="throttling"></a>Throttling
Az Azure Premium Storage rendelkezések megadott IOPS és átviteli sebesség száma attól függően, a Virtuálisgép-méretek és úgy dönt, adatlemez-méretet. Bármikor az alkalmazás próbál IOPS és átviteli sebesség meghajtó felett ezeket a korlátokat, mi a virtuális gép vagy lemez képes kezelni, a Premium Storage szabályozás fogja azt. Ez akkor jelentkezik, az alkalmazás teljesítménycsökkenésért formájában. Ez is jelenti azt, hogy nagyobb késést, csökkentheti az átviteli sebesség vagy IOPS csökkentheti. Ha prémium szintű Storage nem szabályozás, az alkalmazás teljesen feladatátadáshoz által meghaladja a mi erőforrásainak képesek jövedelmezőbb munkát tesznek lehetővé. Tehát teljesítménnyel kapcsolatos problémák miatt szabályozás elkerülése érdekében mindig kiépítése az alkalmazás számára elegendő erőforrással. Figyelembe venni, mi beszéltünk a Virtuálisgép-méretek és a lemez méretét a fenti szakaszban. A teljesítménytesztek a legjobb módszer döntse el, milyen erőforrásokra lesz szüksége az alkalmazás futtatásához.

## <a name="benchmarking"></a>A teljesítménytesztek
A teljesítménytesztek az a folyamat számos különböző számítási feladatok az alkalmazás és az egyes munkaterhelésekhez tartozó az alkalmazás teljesítményének méréséhez. Egy korábbi szakasz lépéseit követve kigyűjtötte alkalmazás teljesítmény-követelmények. Teljesítménymérési eszközökről futtatja az alkalmazást futtató virtuális gépeken, megadhatja, hogy a teljesítményi szint, amely az alkalmazás a Premium Storage segítségével érheti el. Ebben a szakaszban biztosítunk egy Standard DS14 virtuális gép kiépítése az Azure Premium Storage-lemez teljesítményértékelési példákat.

A Microsoft rendelkezik használt közös teljesítménymérési eszközökről Iometer és FIO, Windows és Linux rendszereken. Ezek az eszközök elindítanak egy éles számítási feladat például szimuláló több szálon, és a rendszer teljesítményének mérésére. Az eszközök használatával is konfigurálhatja blokk mérete és a várólista mélysége, amelyek normál esetben nem módosítható az alkalmazás paraméterek. Ez nagyobb rugalmasságot biztosít, prémium szintű lemezek a különböző típusú alkalmazások számítási feladatai együtt üzembe helyezett virtuális gépek nagy léptékű a maximális teljesítmény érdekében. Minden egyes teljesítménymérési eszköz olvashat további [Iometer](http://www.iometer.org/) és [FIO](http://freecode.com/projects/fio).

Kövesse az alábbi példák, hozzon létre egy Standard DS14 virtuális Gépet, és 11 Premium Storage-lemez csatolása a virtuális gép. A 11 lemezek 10 lemez konfigurálása az állomás-gyorsítótárazás, "None", és stripe-NoCacheWrites nevű kötet be őket. Állomás-gyorsítótárazás "Csak olvasható", a fennmaradó lemez konfigurálása, és hozzon létre egy kötetet CacheReads nevű ezt a lemezt. Ezt a beállítást használja, akkor fogja tudni tekintse meg a maximális olvasási és írási teljesítményt, a Standard DS14 virtuális gépről. A prémium szintű lemezekkel rendelkező virtuális gép – DS14 létrehozásáról részletes lépéseket Ugrás [létrehozása és használata egy prémium szintű tárfiók a virtuálisgép-adatlemez](../articles/virtual-machines/windows/premium-storage.md).

*A gyorsítótár Bemelegedés*  
A lemezt a csak olvasható állomás-gyorsítótárazás lesz tudni visszajelzést adni magasabb iops-érték meghaladja a lemez maximális korlátot. A maximális olvasási teljesítmény eléréséhez a gazdagép-gyorsítótárból, először meg kell ízelítőt kapott a gyorsítótár a lemezről. Ez biztosítja, hogy az olvasási IOs mely teljesítménymérési eszköz vonják CacheReads köteten ténylegesen eléri a gyorsítótár és nem a lemezt közvetlenül. Az egyetlen gyorsítótár további IOPS gyorsítótár-találatok eredményez engedélyezve van a lemez.

> **Fontos:**  
> A gyorsítótár futtatása, a teljesítmény mérésére, minden alkalommal, amikor a virtuális gép újraindítása előtt kell ízelítőt.
>
>

#### <a name="iometer"></a>Iometer
[Töltse le a Iometer eszközt](http://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download) a virtuális gépen.

*Fájl tesztelése*  
Iometer egy tesztfájlt a köteten, amelyen futtatni fogja a teljesítménymérési teszt tárolt használ. Ez olvasási meghajtók, és a fájl tesztelése és mérhető, a lemez írási IOPS és átviteli sebesség. Iometer a tesztfájlt hoz létre, ha nincs megadva, az egyik. Hozzon létre egy 200 GB-os tesztfájlt iobw.tst nevű CacheReads és NoCacheWrites kötetekre.

*Access-leírások*  
A specifikációk kérés i/o-mérete, % olvasási/írási, % véletlenszerű/soros konfigurált Iometer "Access specifikációi" lapján. Hozzon létre egy hozzáférési leírása az alábbiakban leírt forgatókönyveket. Hozzon létre hozzáférés előírásoknak és "Mentés" megfelelő nevezze hasonlóan – RandomWrites\_8K RandomReads\_8 K. Válassza ki a megfelelő specifikáció, ha a vizsgálati eset futtatása.

Hozzáférés specifikációk maximális írási IOPS-forgatókönyvhöz példa az alábbi  
    ![](media/premium-storage-performance/image8.png)

*Maximális IOPS-teszt specifikációk*  
Maximális IOPs mutatja be, használja a kérést kisebb méretet. Használja a 8K kérés méretét, és hozzon létre a véletlenszerű írások és olvasások specifikációk.

| Hozzáférési leírása | Kérés mérete | Véletlenszerű % | Olvassa el a(z) % |
| --- | --- | --- | --- |
| RandomWrites\_8 K |8K |100 |0 |
| RandomReads\_8 K |8K |100 |100 |

*Maximális átviteli sebesség tesztelése specifikációk*  
Maximális átviteli sebesség bemutatása érdekében használja a kérelem nagyobb méretű. 64K kérés mérete használja, és a véletlenszerű írások és olvasások leírások létrehozásához.

| Hozzáférési leírása | Kérés mérete | Véletlenszerű % | Olvassa el a(z) % |
| --- | --- | --- | --- |
| RandomWrites\_64 K |64K |100 |0 |
| RandomReads\_64 K |64K |100 |100 |

*A Iometer teszt futtatása*  
Hajtsa végre a gyorsítótár ízelítőt az alábbi lépéseket

1. Hozzon létre két hozzáférési specifikációk alább látható értékekkel

   | Name (Név) | Kérés mérete | Véletlenszerű % | Olvassa el a(z) % |
   | --- | --- | --- | --- |
   | RandomWrites\_1 MB |1MB |100 |0 |
   | RandomReads\_1 MB |1MB |100 |100 |
1. Futtassa a következő paraméterekkel gyorsítótárlemez inicializálása Iometer tesztet. Három munkaszálak használata a célkötet és a egy várólistamélység 128. A teszt "Futási idő" időtartamának beállítása 2hrs, kattintson a "Teszt beállításai" lap.

   | Forgatókönyv | Célkötet | Name (Név) | Időtartam |
   | --- | --- | --- | --- |
   | Gyorsítótárlemez inicializálása |CacheReads |RandomWrites\_1 MB |2hrs |
1. Futtassa a következő paraméterekkel gyorsítótárlemez Bemelegedés Iometer tesztet. Három munkaszálak használata a célkötet és a egy várólistamélység 128. A teszt "Futási idő" időtartamának beállítása 2hrs, kattintson a "Teszt beállításai" lap.

   | Forgatókönyv | Célkötet | Name (Név) | Időtartam |
   | --- | --- | --- | --- |
   | Meleg Gyorsítótárlemez mentése |CacheReads |RandomReads\_1 MB |2hrs |

Után gyorsítótárlemez bemelegíteni van, folytassa az alább felsorolt tesztcélú forgatókönyveket. A Iometer teszt futtatásához használja legalább három feldolgozó szálak **egyes** mennyiségi cél. Az egyes munkavégző szál válassza ki a célkötet, várólistamélységének beállítása, és válasszon a mentett teszt előírások, ahogyan az az alábbi táblázat a megfelelő vizsgálati eset futtatása. Ezek a tesztek futtatása a táblázatban is látható várt eredmény IOPS és átviteli sebességet. Minden esetben egy kis méretű i/o 8 KB-os és a egy 128 magas várólistamélység szolgál.

| Tesztkörnyezet | Célkötet | Name (Név) | Eredmény |
| --- | --- | --- | --- |
| Legfeljebb Olvasási iops-érték |CacheReads |RandomWrites\_8 K |50 000 IOPS-ÉRTÉK |
| Legfeljebb Az írási IOPS |NoCacheWrites |RandomReads\_8 K |AKÁR 64 000 IOPS-ÉRTÉK |
| Legfeljebb Kombinált IOPS |CacheReads |RandomWrites\_8 K |100 000 IOPS-ÉRTÉK |
| NoCacheWrites |RandomReads\_8 K | &nbsp; | &nbsp; |
| Legfeljebb Olvassa el a MB/mp |CacheReads |RandomWrites\_64 K |524 MB/mp |
| Legfeljebb MB/mp/írás |NoCacheWrites |RandomReads\_64 K |524 MB/mp |
| Kombinált MB/mp |CacheReads |RandomWrites\_64 K |1000 MB/mp |
| NoCacheWrites |RandomReads\_64 K | &nbsp; | &nbsp; |

Az alábbiakban a Iometer pillanatképeiért teszteredmények kombinált IOPS és átviteli sebesség forgatókönyvekhez.

*Egyesített írási és olvasási maximális iops-érték*  
![](media/premium-storage-performance/image9.png)

*Egyesített írási és olvasási maximális átviteli sebesség*  
![](media/premium-storage-performance/image10.png)

### <a name="fio"></a>FIO
FIO egy olyan népszerű eszköz a teljesítményteszt storage a Linuxos virtuális gépeken. Azt a rugalmasságot, jelölje be a különböző IO méretű, egymást követő vagy véletlenszerű olvasást, és írja. Ez indít a munkaszálak vagy folyamatok a megadott i/o-műveletek végrehajtásához. Megadhatja az egyes munkavégző szál feladat fájlok használatával kell végrehajtania i/o-műveletek típusa. Létrehoztunk egy feladat-fájlt az alábbi példákban szemléltetett forgatókönyv szerint. A feladat fájlokban becslésére a Premium Storage futó különböző számítási feladatok specifikációit módosíthatja. Standard DS 14 virtuális gép futó használjuk a példákban **Ubuntu**. A elején leírt azonos telepítőjével a [szakasz teljesítménytesztek](#Benchmarking) és a meleg fel a gyorsítótárat a teljesítménymérési tesztek futtatása előtt.

Mielőtt elkezdené, [FIO letöltése](https://github.com/axboe/fio) , és telepítse a virtuális gépen.

Futtassa a következő parancsot az Ubuntu rendszeren

```
apt-get install fio
```

A lemezeken környezetbarát olvasási művelet használjuk az írási műveletek a négy munkaszálak és négy munkavégző szál. Az írási feldolgozók forgalmat a "nocache" az értéke "Nincs" cache 10 lemezzel rendelkezik köteten lévő fog kell vezetési. Az olvasási feldolgozók forgalom gyorsítótár beállítása "Csak olvasható" 1 lemezzel rendelkező "readcache" köteten fog kell vezetési.

*Maximális írási iops-érték*  
Hozzon létre következő előírásoknak írási IOPS maximális beolvasni a feladat-fájlt. Nevezze el "fiowrite.ini".

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

Vegye figyelembe a következő főbb dolog, amely a korábbi szakaszokban ismertetett tervezési útmutató összhangban legyenek. Ezek a specifikációk nélkülözhetetlenek maximális IOPS-meghajtó  

* Egy 256 magas várólista mélységét.  
* Kis blokkméret 8 KB-os.  
* Véletlenszerű írások végrehajtása több szálon.

Futtassa a következő parancsot a FIO teszt 30 másodpercig elindítson  

```
sudo fio --runtime 30 fiowrite.ini
```

A teszt végrehajtása közben lesz száma írási iops-t a virtuális gép és a prémium szintű lemezek kézbesítéséhez. Az alábbi példában látható módon a DS14 VM elkötelezett az írási iops-érték 50 000 IOPS-korlátját.  
    ![](media/premium-storage-performance/image11.png)

*Maximális olvasási iops-érték*  
Hozzon létre a feladat-fájlt az alábbi előírások beolvasni a maximális olvasási iops-t. Nevezze el "fioread.ini".

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

Vegye figyelembe a következő főbb dolog, amely a korábbi szakaszokban ismertetett tervezési útmutató összhangban legyenek. Ezek a specifikációk nélkülözhetetlenek maximális IOPS-meghajtó

* Egy 256 magas várólista mélységét.  
* Kis blokkméret 8 KB-os.  
* Véletlenszerű írások végrehajtása több szálon.

Futtassa a következő parancsot a FIO teszt 30 másodpercig elindítson

```
sudo fio --runtime 30 fioread.ini
```

A teszt végrehajtása közben lesz száma olvasási iops-t a virtuális gép és a prémium szintű lemezek kézbesítéséhez. Ahogy az alábbi példában is látható, a DS14 VM elkötelezett olvasási több mint 64 000 IOPS. Ez az a lemez és a gyorsítótár teljesítménye.  
    ![](media/premium-storage-performance/image12.png)

*Maximális olvasási és írási iops-érték*  
A feladat-fájlt létrehozni a következő beolvasni a maximális előírások együttesen olvasási és írási iops-t. Nevezze el "fioreadwrite.ini".

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

Vegye figyelembe a következő főbb dolog, amely a korábbi szakaszokban ismertetett tervezési útmutató összhangban legyenek. Ezek a specifikációk nélkülözhetetlenek maximális IOPS-meghajtó

* Magas várólista mélysége 128.  
* Kis blokkméret 4 KB-os.  
* Több szál végrehajtása véletlenszerű olvassa be és ír.

Futtassa a következő parancsot a FIO teszt 30 másodpercig elindítson

```
sudo fio --runtime 30 fioreadwrite.ini
```

A teszt végrehajtása közben lesz tekintsük meg összesített olvasási és írási iops-t a virtuális gép, és a prémium szintű lemezek kézbesítéséhez. Ahogy az alábbi példában is látható, a DS14 VM elkötelezett kombinált több mint 100 000 olvasási és írási iops-érték. Ez az a lemez és a gyorsítótár teljesítménye.  
    ![](media/premium-storage-performance/image13.png)

*Maximális átviteli sebesség kombinált*  
Beolvasni a maximális kombinált olvasási és írási teljesítményt, a nagyobb blokkméret és nagy várólistamélységének használata az olvasások és írások végrehajtása több szálon. A 64 KB-os blokkméret és 128 várólistamélység is használhatja.

## <a name="next-steps"></a>További lépések
További információ az Azure Premium Storage:

* [Premium Storage: Nagy teljesítményű tárolási szolgáltatás Azure-beli virtuális gépek számítási feladataihoz](../articles/virtual-machines/windows/premium-storage.md)  

Az SQL Server-felhasználók számára olvassa el az SQL Server ajánlott eljárások teljesítményének javításához cikkeket:

* [Teljesítmény Azure-beli virtuális gépeken az SQL Serverhez kapcsolódó ajánlott eljárások](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md)
* [Az SQL Server Azure-beli virtuális gépen legmagasabb szintű teljesítményt nyújt, az Azure Premium Storage](http://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)
