---
title: "Az Azure Storage táblázat kialakítási útmutató |} Microsoft Docs"
description: "A Tervező méretezhető és Performant táblák Azure Table Storage-ban"
services: cosmos-db
documentationcenter: na
author: mimig1
manager: tadb
editor: tysonn
ms.assetid: 8e228b0c-2998-4462-8101-9f16517393ca
ms.service: cosmos-db
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: a5511b8b2e76c6c651a8e05bda1322293601c92c
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="azure-storage-table-design-guide-designing-scalable-and-performant-tables"></a>Az Azure Storage táblázat kialakítási Útmutató: Méretezhető tervezésével és Performant táblák
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

A Tervező méretezhető és performant táblák, például a teljesítmény, méretezhetőség és költség tényező figyelembe kell vennie. Ha korábban létrehozott sémák a relációs adatbázisok, ezeket a szempontokat ismerős lehet, de amíg vannak bizonyos az az Azure Table storage modell és a relációs modellek közötti Hasonlóságok, is számos fontos különbségek vannak. Ezek a különbségek általában nagyon különböző kialakításokról, előfordulhat, hogy keresse meg a counter-intuitive vagy valaki ismeri a relációs adatbázisok nem megfelelő, de amelyek tegye célszerű jó például az Azure Table szolgáltatás egy NoSQL kulcs-érték tároló tervezésekor vezethet. A Tervező különbségek számos módon változik meg a tényt, hogy a Table szolgáltatás célja, hogy az entitások (relációs adatbázis-terminológia sorainak) vagy olyan adatkészletekhez, amelyek rendkívül nagy tranzakció kötetek támogatnia kell az adatok több milliárd tartalmazó felhőméretű alkalmazások támogatását: ezért figyelembe kell vennie eltérően, hogyan tárolja az adatait, és a Table szolgáltatás működésének megismerése. Egy jól kidolgozott NoSQL-adattár engedélyezheti a megoldás méretezése sokkal tovább (és alacsonyabb költségekkel) mint olyan megoldás, amely egy relációs adatbázist használ. Az útmutató az alábbi témakörök segítségével.  

## <a name="about-the-azure-table-service"></a>Az Azure Table szolgáltatással kapcsolatos
Ez a szakasz azt mutatja be néhány fő funkciója a Table szolgáltatás szempontjából különösen teljesítményének és méretezhetőségének tervezése. Ha most ismerkedik az Azure Storage és a Table szolgáltatás, elolvashatja [Microsoft Azure Storage bemutatása](../storage/common/storage-introduction.md) és [Ismerkedés az Azure Table Storage használatának .NET](table-storage-how-to-use-dotnet.md) további része a cikk elolvasása előtt. Bár ez az útmutató célja azoknak a Table szolgáltatásban, ez magában foglalja a néhány semmiről nem kell az Azure üzenetsor és a Blob szolgáltatás, és hogyan használhatja őket a megoldás a Table szolgáltatás együtt.  

Mi az a Table szolgáltatás? Előfordulhat, hogy a neve alapján várt, a Table szolgáltatás táblázatos formátumú adatok használ. A ismertetésében a tábla minden egyes sorára jelöli egy entitás, és az oszlopok tárolja, hogy az entitás tulajdonságait. Minden entitás egy kulcspárra van szüksége, egyedi azonosításához, és a Timestamp típusú oszlop, amely a Table szolgáltatás segítségével nyomon követheti az entitás legutóbbi frissítése (Ez automatikusan megtörténik, és adjon meg egy tetszőleges értéket nem lehet felülírni a Timestamp típusú manuálisan). A Table szolgáltatás az utolsó módosításának időbélyegző (LMT) egyidejű hozzáférések optimista kezelésére használ.  

> [!NOTE]
> A Table szolgáltatás REST API-műveleteket is vissza egy **ETag** érték, amely azt a last-modified időbélyeg (LMT) származik. Ebben a dokumentumban lesz a kifejezéseket használjuk ETag és LMT azonos értelemben mert ugyanazokat az alapul szolgáló adatokat hivatkoznak.  
> 
> 

Az alábbi példában egy egyszerű Táblatervezés alkalmazott és részleg entitások tárolásához. Ez a kialakítás egyszerű számos az útmutató későbbi részében látható példája alapul.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Időbélyeg</th>
<th></th>
</tr>
<tr>
<td>Marketing</td>
<td>00001</td>
<td>2014-08-22T00:50:32Z</td>
<td>
<table>
<tr>
<th>Utónév</th>
<th>Vezetéknév</th>
<th>Kor</th>
<th>E-mail cím</th>
</tr>
<tr>
<td>Nincs</td>
<td>Hall</td>
<td>34</td>
<td>donh@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>00002</td>
<td>2014-08-22T00:50:34Z</td>
<td>
<table>
<tr>
<th>Utónév</th>
<th>Vezetéknév</th>
<th>Kor</th>
<th>E-mail cím</th>
</tr>
<tr>
<td>jún.</td>
<td>CaO</td>
<td>47</td>
<td>junc@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>Részleg</td>
<td>2014-08-22T00:50:30Z</td>
<td>
<table>
<tr>
<th>Részleg neve</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Marketing</td>
<td>153</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>Értékesítés</td>
<td>00010</td>
<td>2014-08-22T00:50:44Z</td>
<td>
<table>
<tr>
<th>Utónév</th>
<th>Vezetéknév</th>
<th>Kor</th>
<th>E-mail cím</th>
</tr>
<tr>
<td>Ken</td>
<td>Kwok</td>
<td>23</td>
<td>kenk@contoso.com</td>
</tr>
</table>
</td>
</tr>
</table>


Az eddigi Ez hasonlít nagyon a egy relációs adatbázisban a legfőbb különbségek a kötelező oszlopok, és ugyanabban a táblában több entitástípusok tárolásának képessége. Emellett egyes többek között a felhasználó által definiált tulajdonságok **Keresztnév** vagy **kora** adattípusú, például az egész szám vagy karakterlánc, csak, például egy relációs adatbázisban oszlop. Bár eltérően egy relációs adatbázisban, a Table szolgáltatás séma nélküli jellege azt jelenti, hogy a tulajdonság nem szükséges minden entitáshoz ugyanolyan adattípusúak. Összetett adattípusú egy adott tulajdonságra vannak tárolva, például a JSON- vagy XML-szerializált formátum kell használnia. A table szolgáltatás például a támogatott adattípusok, támogatott dátumtartományok, elnevezési szabályok és mérete megkötések kapcsolatos további információkért lásd: [ismertetése a Table szolgáltatás adatmodell](http://msdn.microsoft.com/library/azure/dd179338.aspx).

Mivel látni fogja, a választott **PartitionKey** és **RowKey** jó Táblatervezés alapvető. Minden entitás egy táblázatban tárolja rendelkeznie kell egy egyedi kombinációja **PartitionKey** és **RowKey**. Csakúgy, mint a kulcsokat egy relációs adatbázis tábláinak a **PartitionKey** és **RowKey** értékek egy fürtözött index, amely lehetővé teszi, hogy a gyors look-ups indexelt; azonban a Table szolgáltatás nem hoz létre az bármely másodlagos indexek, és így ezek a csak két az indexelt tulajdonságok (néhány későbbi mintázatokat megjelenítése hogyan oldható meg a nyilvánvaló korlátozás).  

Egy tábla egy vagy több partíció épül fel, és látni fogja, a tervezési döntéseit számos lesz körül kiválasztása megfelelő **PartitionKey** és **RowKey** a megoldás optimalizálása érdekében. A megoldás csak egyetlen tábla összes, a partíciók szervezve entitásokat tartalmazó sikerült alkotják, de általában egy megoldás több táblák esetében. Táblázatok segítséget logikailag rendezheti az entitások, a hozzáférés-vezérlési listák segítségével adataihoz való hozzáférés kezeléséhez nyújt segítséget, és egyetlen tárolási művelettel teljes táblázat elvetné.  

### <a name="table-partitions"></a>Táblapartíciók
A fiók neve, a tábla nevét és **PartitionKey** együtt azonosíthatja a partíción belül a társzolgáltatás, ahol a table szolgáltatás tárolja az entitás. Amellett, hogy az entitások címzési séma része, a partíciók az egyes tranzakciókra vonatkozóan hatókör meghatározása (lásd: [entitás csoport tranzakciók](#entity-group-transactions) alább), és hogyan méretezze át a table szolgáltatás alapját. További információk a partíciókon: [Azure Storage méretezhetőségi és teljesítménycéloknak](../storage/common/storage-scalability-targets.md).  

A Table szolgáltatásban, az egyes csomópontok szolgáltatások egy vagy több befejezéseként partíciókat és a szolgáltatás méretezik dinamikus terheléselosztás partíciók csomópontjai között. Egy csomópont terhelésnek van kitéve, ha a table szolgáltatás is *vágási* a partíciók száma a tartományon, más csomópontok csomópont által kiszolgált; enyhül forgalmat, ha a szolgáltatás képes *egyesítési* csendes csomópontjáról partíció tartományok biztonsági alakzatot egyetlen csomópont.  

További információ a belső részleteit a Table szolgáltatás, és ebben az esetben a szolgáltatás kezeli a partíciók, hogyan talál a [Microsoft Azure Storage: A magas rendelkezésre álló felhőalapú tárolási szolgáltatásba az erős konzisztencia](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

### <a name="entity-group-transactions"></a>Entitás csoport tranzakciók
A Table szolgáltatásban entitás csoport tranzakciók (EGTs) atomi frissítések végrehajtásához a több egység közötti csak beépített mechanizmus. EGTs is nevezzük *kötegelt tranzakciókat* bizonyos dokumentációkban. EGTs csak működhessenek entitások tárolt partícióra (ugyanazzal a partíciókulccsal egy adott táblában megosztás), így bármikor atomi tranzakciós viselkedését kell gondoskodnia kell arról, hogy ezeket az entitásokat tartalmazó partícióra vannak több egység közötti. Ez gyakran az több entitástípusok megőrzi a ugyanahhoz a táblához (és a partíció) található, és több tábla nem használ másik entitástípusok okát. Egyetlen EGT legfeljebb 100 entitást is működik.  Ha több egyidejű EGTs is fontos, hogy ezek EGTs működés nem lehetséges, amelyek közösek a EGTs között, mivel ellenkező esetben feldolgozása késleltethető entitások feldolgozásra.

EGTs is vezethet, hogy kipróbálhassa a Tervező egy potenciális kompromisszum: több partíciót használata növeli a méretezhetőség, az alkalmazás Azure-csomópontokon keresztüli kérelmek terheléselosztási további lehetőségekkel rendelkezik, de ez korlátozhatja a lehetőségét, mert az alkalmazás atomi tranzakciók elvégzéséhez, és erős konzisztenciát biztosít az adatok karbantartása. Emellett nincsenek egyedi méretezhetőségi célok javasoljuk, hogy előfordulhat, hogy korlátozza az egyetlen csomópont számíthat tranzakciók átviteli szinten: az Azure storage-fiókok és a table szolgáltatás vonatkozó méretezhetőségi célok kapcsolatos további információkért lásd: [Az azure Storage méretezhetőségi és teljesítménycéloknak](../storage/common/storage-scalability-targets.md). A jelen útmutató későbbi szakaszok tárgyalják különböző kialakítási stratégiák, amelyek segítenek kompromisszumot alakítson ki például a kezelése, és hogyan érdemes ismertetik a partíciós kulcs, az ügyfélalkalmazás a meghatározott követelmények alapján kiválaszthatja.  

### <a name="capacity-considerations"></a>A kapacitás kapcsolatos szempontok
Az alábbi táblázat tartalmaz néhány érdemes figyelembe vennie, amikor tervezésekor a Table szolgáltatás megoldás értékek:  

| Az Azure storage-fiók teljes kapacitás | 500 TB |
| --- | --- |
| Egy Azure storage-fiókot a táblák száma |Csak a tárfiók kapacitásának korlátozva |
| Egy tábla partíciók száma |Csak a tárfiók kapacitásának korlátozva |
| Partíció entitástartományának száma |Csak a tárfiók kapacitásának korlátozva |
| Az egyes entitás mérete |Legfeljebb 255 tulajdonságok legfeljebb 1 MB (beleértve a **PartitionKey**, **RowKey**, és **időbélyeg**) |
| A méret a **PartitionKey** |A karakterlánc legfeljebb 1 KB méretű |
| A méret a **RowKey** |A karakterlánc legfeljebb 1 KB méretű |
| Egy entitás csoport tranzakció mérete |Egy tranzakció legfeljebb 100 entitást tartalmazhat, és a tartalom 4 MB-nál kevesebb kell lennie. Egy EGT csak frissíthető entitás egyszer. |

További információkért lásd: [ismertetése a Table szolgáltatás adatmodell](http://msdn.microsoft.com/library/azure/dd179338.aspx).  

### <a name="cost-considerations"></a>Költség kapcsolatos szempontok
A TABLE storage költségei viszonylag alacsonyak, de a Table szolgáltatás használó megoldások próbaidőszakában részeként tartalmaznia kell a kapacitás és a tranzakciók mennyisége költség becslése. Azonban számos forgatókönyvben denormalizált vagy ismétlődő adatok tárolására javítása érdekében a teljesítmény vagy a méretezhetőség miatt a megoldás nem egy érvényes megközelítés érvénybe. Az árazással kapcsolatos további információkért lásd: [Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="guidelines-for-table-design"></a>Táblatervezés vonatkozó irányelvek
A listák összesítésének néhány a kulcs irányelveket kell figyelembe venni a táblák tervezése során, és ez az útmutató foglalkozni fog velük az összes későbbi részében részletesebben. Ezeket az irányelveket nagyon eltérnek a általában végrehajtania a relációs adatbázis tervezési irányelveket.  

A Table szolgáltatás megoldás kell tervezése *olvasási* hatékony:

* ***Tervezze meg az olvasási műveleteket alkalmazásokban lekérdezése.*** A táblák tervezésekor gondolja át a lekérdezések (különösen a várakozási bizalmas is), amely, végrehajtja a véleménye hogyan frissíti az entitások előtt. Ez általában annak az eredménye a hatékony és performant megoldás.  
* ***Adja meg a lekérdezések PartitionKey és RowKey is.*** *Mutasson a lekérdezések* például ezek azok a leghatékonyabb tábla szolgáltatás lekérdezéseket.  
* ***Érdemes tárolni a duplikált entitásokat.*** A TABLE storage olcsó, érdemes a ugyanaz az entitás tárolni többször (különböző kulccsal rendelkező) a hatékonyabb lekérdezések engedélyezéséhez.  
* ***Vegye figyelembe az adatok denormalizing.*** A TABLE storage olcsó ezért fontolja meg az adatok denormalizing. Összegző entitások például tárolja, hogy az összesített adatok lekérdezések csak egyetlen entitáshoz eléréséhez szükséges.  
* ***Használja az összetett kulcs értékeket.*** A csak akkor kulcsokban **PartitionKey** és **RowKey**. Például összetett kulcsértékei használatával engedélyezze a másodlagos kulccsal elérési utakat a részére.  
* ***Lekérdezés vetítéshez használni.*** Válassza ki a szükséges mezők lekérdezésekkel a hálózati átvitel adatmennyiség csökkentése érdekében.  

A Table szolgáltatás megoldás kell tervezése *írási* hatékony:  

* ***Ne hozzon létre a gyakran használt adatok partíciókat.*** Válassza ki a kulcsokat, amelyek lehetővé teszik, hogy a kérelmek elosztva idő minden helyen több partíciót.  
* ***Kerülje a forgalmat a teljesítményt.*** Elfogadható időn keresztül a forgalom sima, és elkerülheti a forgalmat a teljesítményt.
* ***Feltétlenül ne hozzon létre egy külön táblázat az egyes entitás.*** Ha atomi tranzakciók entitástípusok között, több entitás típusaival tárolhat partícióra ugyanabban a táblában.
* ***Fontolja meg a maximális átviteli sebesség kell elérni.*** Kell figyelembe vennie a méretezhetőségi célok a Table szolgáltatás, és győződjön meg arról, hogy a tervező nem okoz Önnek, hogy azokat.  

Ez az útmutató olvasás példák, amelyek a gyakorlatban az összes alapelvek jelenik meg.  

## <a name="design-for-querying"></a>Tervezési lekérdezése
TABLE szolgáltatási megoldások intenzív, írási intenzív vagy a két vegyesen olvashatók. Ez a szakasz a Table szolgáltatás az olvasási műveletek hatékonyan támogatásához tervezése során figyelembe kell vennie a következőket összpontosít. A Tervező, támogatja a hatékony olvassa el az operations általában is hatékony az írási műveletek. Van azonban az írási műveletek, a következő szakaszban tárgyalt tervezésekor figyelembe kell vennie további szempontok [adatmódosítás kialakítása](#design-for-data-modification).

Ahhoz, hogy hatékonyan-adatok olvasása a Table szolgáltatás megoldás tervezése az jó kiindulási pont, kérje meg a "milyen lekérdezések alkalmazás kell végrehajtani a szükséges adatokat lekérdezni a Table szolgáltatás?"  

> [!NOTE]
> A Table szolgáltatás fontos beolvasni a Tervező megfelelő előre mert bonyolult és költséges később módosítható. Például egy relációs adatbázisban is gyakran lehet cím teljesítményproblémák egyszerűen, amelyet indexek hozzáadása egy meglévő adatbázist: Ez a lehetőség nem érhető a Table szolgáltatással.  
> 
> 

Ez a szakasz meg kell oldania a táblák lekérdezése tervezésekor kulcs témákra összpontosít. Ebben a szakaszban szereplő témakörök az alábbiak:

* [Hogy a választott PartitionKey és RowKey milyen hatással van a teljesítmény-küszöbérték](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [Egy megfelelő PartitionKey kiválasztása](#choosing-an-appropriate-partitionkey)
* [A Table szolgáltatás lekérdezések optimalizálása](#optimizing-queries-for-the-table-service)
* [A Table szolgáltatás az adatok rendezése](#sorting-data-in-the-table-service)

### <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Hogy a választott PartitionKey és RowKey milyen hatással van a teljesítmény-küszöbérték
Az alábbi példák azt feltételezik, hogy a table szolgáltatás a következő struktúrával alkalmazott entitások tárolja (a példák a legtöbb hagyja ki ezt a **időbélyeg** jobb érthetőség kedvéért bizonyos tulajdonság):  

| *Oszlop neve* | *Adattípus* |
| --- | --- |
| **PartitionKey** (részleg neve) |Karakterlánc |
| **RowKey** (alkalmazott azonosítója) |Karakterlánc |
| **Utónév** |Karakterlánc |
| **Vezetéknév** |Karakterlánc |
| **Kora** |Egész szám |
| **E-mail cím** |Karakterlánc |

A korábbi szakaszban [Azure Table szolgáltatás áttekintése](#overview) néhány szolgáltatását az Azure Table szolgáltatás, amely közvetlenül befolyásolják a lekérdezéshez tervezéséről foglalja össze. Ezek a Table szolgáltatás Lekérdezéstervezés vonatkozó általános irányelveket eredményez. Vegye figyelembe, hogy a Table szolgáltatásból REST API-t a további tudnivalókat lásd az alábbi példákban szereplő szűrőszintaxisának [lekérdezés entitások](http://msdn.microsoft.com/library/azure/dd179421.aspx).  

* A ***pont lekérdezés*** a leghatékonyabb keresési használatára, és nagy mennyiségű keresések vagy a legkisebb mértékű késleltetést igénylő keresések használandó ajánlott. Ilyen lekérdezést az indexek segítségével egyedi entitás nagyon hatékonyan mindkét megadásával keresse meg a **PartitionKey** és **RowKey** értékeket. Például: $filter = (PartitionKey eq 'Értékesítés') és (RowKey eq '2')  
* Második legjobb van egy ***értéktartomány lekérdezésének*** , amely használja a **PartitionKey** és a szűrők számos **RowKey** egynél több entitáskészlet visszaadandó értékek száma. A **PartitionKey** érték azonosít egy adott partícióra, és a **RowKey** értékek azonosítsa az adott partíció entitástartományának részhalmazát. Például: $filter = "Értékesítési és RowKey ge" PartitionKey eq és RowKey lt jelzést "  
* Harmadik legjobb van egy ***partíció vizsgálata*** , amely használja a **PartitionKey** és olyan nem kulcs tulajdonsága, és hogy a szűrők térhetnek vissza egy entitást. A **PartitionKey** érték egy adott partícióra azonosítja, és a tulajdonság értékek válassza ki az adott partíció entitást egy részéhez. Például: $filter PartitionKey eq "Értékesítési" és a Vezetéknév eq 'Smith' =  
* A ***tábla vizsgálata*** nem tartalmazza a **PartitionKey** és nem nagyon hatékony, mivel az összes pedig a hozzá tartozó entitások az a táblázat partíciókat keresi. A táblázatbeolvasás, függetlenül a szűrő használja-e hajtja végre a **RowKey**. Például: $filter Vezetéknév eq "János" =  
* Több entitás visszaadó lekérdezések Alapértelmezések rendezve **PartitionKey** és **RowKey** sorrendje. Az ügyfél entitást keresésére átrendezésével elkerüléséhez válassza ki a **RowKey** , amely meghatározza, hogy a leggyakrabban használt rendezési sorrend.  

Vegye figyelembe, hogy használja az "**vagy**" alapján egy szűrő meghatározására **RowKey** értéket egy partíció vizsgálati eredményeket, majd egy értéktartomány lekérdezésének nem számít. Ezért kerülje el a lekérdezéseket, szűrők, mint amelyekkel: $filter = PartitionKey eq 'Értékesítés' és (RowKey eq "121" vagy "322" RowKey eq)  

Az ügyféloldali kódot, amely hatékony-lekérdezéseket hajt végre a Storage ügyféloldali kódtár használatával című részben talál példákat:  

* [A Storage ügyféloldali kódtár segítségével pont lekérdezése](#executing-a-point-query-using-the-storage-client-library)
* [LINQ használatával több entitás beolvasásakor](#retrieving-multiple-entities-using-linq)
* [Kiszolgálóoldali leképezése](#server-side-projection)  

Ügyféloldali kódot, amelyet kezelni ugyanabban a táblában tárolt több entitás típusokat tud példákért lásd:  

* [Heterogén entitástípusok használata](#working-with-heterogeneous-entity-types)  

### <a name="choosing-an-appropriate-partitionkey"></a>Egy megfelelő PartitionKey kiválasztása
A választott **PartitionKey** kell terheléselosztást kell EGTs (konzisztencia biztosításához) használatát teszi lehetővé a követelménnyel szemben az entitások szét több partíciót (méretezhető megoldás biztosításához).  

Egy rendkívüli az entitások sikerült egyetlen partícióra vannak tárolva, de ez korlátozottá teheti méretezhetőségét a megoldás, és akadályozzák a table szolgáltatás terheléselosztásához kérelmek igényt. A rendkívüli egy entitás partíció található, amely magas szinten méretezhető lenne és amely lehetővé teszi a table szolgáltatás terheléselosztási kérésekre, de amelyek volna újrafelhasználásának megtiltása entitás csoport tranzakciók száma tárolhatja.  

Az ideális **PartitionKey** , amely lehetővé teszi, hogy használni lehessen a hatékony lekérdezések és annak biztosítása érdekében, a megoldás méretezhető elegendő partíciókkal rendelkezik, amelyek egyike. Általában találja, hogy az entitások lesz-e az entitások elosztja elegendő partíciók megfelelő tulajdonság.

> [!NOTE]
> A rendszer, hogy a felhasználók vagy az alkalmazottak kapcsolatos információkat tárolja, például UserID lehet egy jó PartitionKey. Előfordulhat, hogy több entitás, amely egy adott felhasználói azonosítóját használják a partíciós kulcs. Minden entitás, amely a felhasználó adatait tárolja egyetlen partícióra vannak csoportosítva, és ezért ezeket az entitásokat elérhetők entitás csoport tranzakciók, miközben továbbra is magas szinten méretezhető.
> 
> 

A választott további szempontot **PartitionKey** hogyan meg fog beszúrási, frissítési és törlési entitások kapcsolódó: című [adatmódosítás kialakítása](#design-for-data-modification) alatt.  

### <a name="optimizing-queries-for-the-table-service"></a>A Table szolgáltatás lekérdezések optimalizálása
A Table szolgáltatás automatikusan indexeli az entitások használata a **PartitionKey** és **RowKey** egy fürtözött index, ezért az oka, hogy a lekérdezések pont értékei a leghatékonyabb használatára. Van azonban nem indexei eltérő a fürtözött index a a **PartitionKey** és **RowKey**.

Sok ahhoz, hogy az entitások több feltétel alapján keresési kell megfelelnie. Például az e-mailek alapján alkalmazott entitások keresése Alkalmazottazonosító vagy vezetéknevét. A szakasz a következő minták [táblázat kialakítási minta](#table-design-patterns) követelmény az ilyen típusú cím, és azt a tényt, hogy a Table szolgáltatás nem nyújt másodlagos indexek körül módon ismertetik:  

* [Intra-partíció másodlagos index mintát](#intra-partition-secondary-index-pattern) -tárolja a több másolatot minden entitás használatával különböző **RowKey** engedélyezése gyors és hatékony keresések és másodlagos rendezési sorrend különböző használatával (a partícióra) értékek **RowKey** értékeket.  
* [Másodlagos index közötti particionálásához mintát](#inter-partition-secondary-index-pattern) -tárolja a több másolatot minden entitás használatával különböző **RowKey** értékek a különböző partíciók, vagy a külön a táblák gyors és hatékony keresést és a másodlagos rendezési sorrend engedélyezése a különböző rendelések **RowKey** értékeket.  
* [Index entitások mintát](#index-entities-pattern) -index entitások entitások listájának visszaadó hatékony keresések engedélyezése karbantartása.  

### <a name="sorting-data-in-the-table-service"></a>A Table szolgáltatás az adatok rendezése
A Table szolgáltatás adja vissza növekvő sorrendben rendezve entitások **PartitionKey** és a majd **RowKey**. Ezek a kulcsok karakterlánc-értékek és numerikus értékek megfelelően rendezése érdekében kell alakíthatja át őket egy rögzített hosszúságú és nulla kitölti őket. Például, ha az alkalmazott azonosítóérték használja a **RowKey** megadott egész szám, alkalmazottazonosító átalakíthatja **123** való **00000123**.  

Számos alkalmazás más-más sorrendben rendezett adatok használata követelményekkel rendelkezik: az alkalmazottak például rendezést a neve vagy való csatlakozás dátumát. A szakasz a következő minták [táblázat kialakítási minta](#table-design-patterns) számára, hogy a rendezési sorrend a entitások hogyan cím:  

* [Helyen belüli-partíció másodlagos index mintát](#intra-partition-secondary-index-pattern) - különböző RowKey értékei alapján (a partícióra) gyors engedélyezése minden entitás több példányát tárolja, és hatékony keresést és a másodlagos rendezési sorrend rendelések különböző RowKey értékek használatával.  
* [Másodlagos helyek közötti partíció index mintát](#inter-partition-secondary-index-pattern) – minden külön táblázatban külön partíciók különböző RowKey értékek segítségével engedélyezi a gyors entitás több példányát tárolja, és hatékony keresést és a másodlagos rendezési sorrend rendelések különböző RowKey értékek használatával .
* [Napló végéről mintát](#log-tail-pattern) -beolvasni a  *n*  partíció legutóbb hozzáadott entitások egy **RowKey** érték, amely fordított dátum és idő sorrendben rendezi.  

## <a name="design-for-data-modification"></a>Adatmódosítás kialakítása
Ez a szakasz a beszúrások, a frissítések optimalizálása kialakítási szempontjai összpontosít, és törli. Néhány esetben szüksége lesz a kompromisszum terveket, terveket, hasonlóan a relációs adatbázisok terveket (bár a Tervező kompromisszumot kezelésére szolgáló módszerek optimalizálás adatok módosítása ellen lekérdezése optimalizálása közötti kiértékelése különböző egy relációs adatbázisban). A szakasz [táblázat kialakítási minta](#table-design-patterns) néhány részletes kialakítási minta a Table szolgáltatás ismerteti, és kiemeli a néhányat ezek kompromisszumot. A gyakorlatban találja, hogy az entitás lekérdezése optimalizált sok tervek is alkalmas entitások módosítása.  

### <a name="optimizing-the-performance-of-insert-update-and-delete-operations"></a>A teljesítmény optimalizálása a beszúrási, frissítési és törlési műveletek
Frissíteni vagy törölni egy entitást, meg kell tudni használatával azonosíthassák a **PartitionKey** és **RowKey** értékeket. Ebben a tekintetben a választott **PartitionKey** és **RowKey** az entitások módosítása nyújtanak hasonló feltételek pont lekérdezések támogatja, mert a szervezetek, mint a azonosítani szeretné az Ön választása lehető leghatékonyabb módon. Nem használni kívánt nem elég hatékony partíció vagy tábla vizsgálat entitás található felderítéséhez a **PartitionKey** és **RowKey** értékek frissíteni vagy törölni kell.  

A szakasz a következő minták [táblázat kialakítási minta](#table-design-patterns) cím optimalizálása a teljesítmény vagy a beszúrási, frissítési és törlési műveletek:  

* [Nagy mennyiségű törlése mintát](#high-volume-delete-pattern) -a nagyszámú entitások törlésének engedélyezése a entitásokhoz egyidejű törlésre tárolása saját különálló táblában; a tábla törlésével törli az entitásokat.  
* [Adatsorozat adatmintát](#data-series-pattern) -tároló teljes adatsorok egyetlen entitás minimalizálása érdekében elvégezte kérelmek számát jelenti.  
* [Széles entitások mintát](#wide-entities-pattern) -használjon több fizikai entitás legfeljebb 252 tulajdonságot rendelkező logikai entitás tárolására.  
* [Nagy entitások mintát](#large-entities-pattern) -blob-tároló használjon nagy tulajdonságértékek tárolásához.  

### <a name="ensuring-consistency-in-your-stored-entities"></a>A tárolt entitásokat következetes biztosítása
A más kulcsfontosságú tényező, amely befolyásolja a választott módosítása optimalizálási kulcsok, hogyan biztosíthatja a atomi tranzakciók használatával. Csak használhat egy EGT az entitásokat tartalmazó partícióra tárolt való működésre.  

A szakasz a következő minták [táblázat kialakítási minta](#table-design-patterns) cím konzisztencia kezelése:  

* [Intra-partíció másodlagos index mintát](#intra-partition-secondary-index-pattern) -tárolja a több másolatot minden entitás használatával különböző **RowKey** engedélyezése gyors és hatékony keresések és másodlagos rendezési sorrend különböző használatával (a partícióra) értékek **RowKey** értékeket.  
* [Másodlagos helyek közötti partíció index mintát](#inter-partition-secondary-index-pattern) - értékekkel különböző RowKey külön partíciók vagy külön táblázatban gyors engedélyezése minden entitás több példányát tárolja, és a különböző rendelésekhatékonykereséstésamásodlagosrendezésisorrend**RowKey** értékeket.  
* [Idővel konzisztenssé tranzakciók mintát](#eventually-consistent-transactions-pattern) -engedélyezni a idővel konzisztenssé partícióhatárok vagy tárolási rendszer határok Azure üzenetsorok használatával.
* [Index entitások mintát](#index-entities-pattern) -index entitások entitások listájának visszaadó hatékony keresések engedélyezése karbantartása.  
* [Denormalization mintát](#denormalization-pattern) -egyesítése kapcsolódó adatok együtt az egyetlen entitás ahhoz, hogy beolvasni az összes adatot a hibaérzékeny pontok lekérdezéssel van szüksége.  
* [Adatsorozat adatmintát](#data-series-pattern) -tároló teljes adatsorok egyetlen entitás minimalizálása érdekében elvégezte kérelmek számát jelenti.  

Entitás csoport tranzakciókkal kapcsolatos információkért lásd: a szakasz [entitás csoport tranzakciók](#entity-group-transactions).  

### <a name="ensuring-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>A módosítások hatékony kialakítása biztosítása elősegíti a hatékony lekérdezések
Sok esetben egy tervezési módosítások hatékony, de hatékony lekérdező eredményez kell mindig mérlegelje, hogy ez a helyzet az adott forgatókönyv szerint. A szakasz a minták némelyike [táblázat kialakítási minta](#table-design-patterns) explicit módon értékeli az kérdez le, és módosítja az entitások közötti kompromisszumot, és hogy mindig figyelembe kell vennie a művelet típusonkénti darabszámot.  

A szakasz a következő minták [táblázat kialakítási minta](#table-design-patterns) hatékony lekérdezések tervezése és kialakítása hatékony adatok módosítása a közötti kompromisszumot cím:  

* [Összetett kulcs mintát](#compound-key-pattern) – használható összetett **RowKey** értékek kapcsolódó adatok egyetlen pont lekérdezéssel talált ügyfél engedélyezése.  
* [Napló végéről mintát](#log-tail-pattern) -beolvasni a  *n*  partíció legutóbb hozzáadott entitások egy **RowKey** érték, amely fordított dátum és idő sorrendben rendezi.  

## <a name="encrypting-table-data"></a>Tábla adatok titkosítása
A .NET Azure Storage ügyféloldali kódtár támogatja a titkosítást a karakterlánc az Entitástulajdonságok szúrhatók be, és cserélje le a műveletek. A titkosított karakterláncok tárolja a szolgáltatás bináris tulajdonságként, és telepítésekké lesznek átalakítva vissza karakterláncok a visszafejtés után.    

Táblák, a titkosítási házirenden kívül a felhasználók fiók kell adnia a titkosítani kell. Ezt megteheti megadásával vagy (az POCO entitások, amelyek a TableEntity) [EncryptProperty] attribútum vagy egy titkosítási feloldó lehetőségek. Egy titkosítási feloldó egy delegált veszi a partíciós kulcs, sorkulcsot és tulajdonság nevét, és logikai érték beolvasása, amely jelzi, hogy a tulajdonság titkosítani kell-e. Titkosítás során az ügyféloldali kódtár döntse el, hogy a tulajdonság titkosítani kell-e az átvitel közbeni írása során fogja használni ezt az információt. A delegált is körül hogyan tulajdonságok vannak titkosítva logika lehetőségét biztosítja. (Például, ha X, majd titkosítása A tulajdonság; ellenkező esetben a Tulajdonságok A és b titkosítása) Vegye figyelembe, hogy nincs szükség arra, hogy ezek az információk olvasása vagy entitás lekérdezése közben.

Vegye figyelembe, hogy a merge jelenleg nem támogatott. Mivel a Tulajdonságok részhalmazát, előfordulhat, hogy korábban használatával titkosított egy másik kulcsot, egyszerűen csak az új tulajdonságok egyesítése és a metaadatok frissítése adatok elvesztését eredményezi. Az egyesítés vagy megköveteli a már meglévő entitás olvasni a szolgáltatás további szolgáltatás-hívások, vagy tulajdonságonként egy új kulcsot használ, amelyek mindegyikét nem alkalmasak a teljesítményre vonatkozó megfontolásból.     

További információ a táblabeli adatok titkosítása: [ügyféloldali titkosítás és a Microsoft Azure tárolás az Azure Key Vault](../storage/common/storage-client-side-encryption.md).  

## <a name="modelling-relationships"></a>Kapcsolatok modellezésére
A Tervező összetett rendszerek kulcsfontosságú lépés létrehozási modelleket tartomány. Általában a modellezési folyamat segítségével azonosíthatja a entitásokat és a köztük lévő viszonyt is egyik módja az üzleti tartomány megértéséhez, valamint a tervezési, a rendszer tájékoztatja. Ez a szakasz összpontosít hogyan tudja lefordítani néhány a közös kapcsolattípusok tartomány modellek mintájára a Table szolgáltatás található. Egy logikai adatmodell-leképezés egy fizikai alapú NoSQL-adatmodellhez folyamat nagyon eltér a relációs tervezése során használt. Relációs adatbázisok terv általában azt feltételezi, hogy a normalizálási adatfeldolgozás minimalizálja a redundancia – és a deklaratív lekérdező képessége, hogy hogyan végrehajtásának az adatbázis működésének kivonatolja optimalizálva.  

### <a name="one-to-many-relationships"></a>Egy-a-többhöz kapcsolatok
Egy-a-többhöz kapcsolatok üzleti tartomány objektumok közötti nagyon gyakran előfordulnak: például egy részleg rendelkezik sok alkalmazott. Többféleképpen is egy-a-többhöz kapcsolatok szolgáltatásban a tábla minden egyes az előnyei és hátrányai, amely fontos lehet az adott forgatókönyv megvalósításához.  

Vegye figyelembe a példa egy nagy több nemzeti vállalat osztályai és ahol minden részlege rendelkezik sok alkalmazott, és minden alkalmazott, egy adott részleg társított alkalmazott entitások tízezreit tartalmazó. Egy megoldás, külön részleg és az alkalmazott entitások, például a tároló:  

![][1]

Ez a példa bemutatja egy implicit egy-a-többhöz kapcsolat alapján típusa közötti a **PartitionKey** érték. Minden részleghez rendelkezhet sok alkalmazott.  

Ez a példa egy szervezeti egység és a kapcsolódó alkalmazott entitásokat is tartalmazó partícióra szerepel. Megadhatja, hogy a különböző entitástípusok különböző partíciók, táblák, vagy még akkor is, storage-fiókok használandó.  

Egy másik módszert is, hogy az adatok denormalize és csak alkalmazott entitások denormalizált részleg adatokkal a következő példában látható módon tárolja. Adott esetben ez a megközelítés denormalizált nem lehet a legjobb lehet módosítani a részleg vezetője részleteit, mert ehhez frissítenie kell az osztály minden alkalmazott számára, ha.  

![][2]

További információkért lásd: a [Denormalization mintát](#denormalization-pattern) című útmutatóban.  

Az alábbi táblázat foglalja össze, és az egyes az alkalmazottak és a részleg entitások, amelyek egy-a-többhöz tárolását a fent vázolt módszerekkel. Emellett érdemes milyen gyakran várható a különböző műveletek végrehajtásához: elfogadható kell rendelkeznie, amely tartalmaz egy drága művelet, ha ez a művelet csak akkor fordul elő, a ritkán lehet.  

<table>
<tr>
<th>Módszer</th>
<th>Informatikai szakemberek</th>
<th>Hátrányok</th>
</tr>
<tr>
<td>Külön entitástípusok, egyazon partícióra kerüljenek, ugyanahhoz a táblához</td>
<td>
<ul>
<li>A részleg entitás egyetlen művelettel frissítheti.</li>
<li>Egy EGT használatával biztosítja az egységességet, ha egy szervezeti egység módosítása követelmény amikor Ön frissítés/Beszúrás/törlés alkalmazott entitás. Például ha a részlegszintű alkalmazott száma minden részleg számára.</li>
</ul>
</td>
<td>
<ul>
<li>Az alkalmazottak és a bizonyos ügyfél tevékenységek egy részleg entitást szeretne.</li>
<li>Tárolási műveletekre kerül sor a partícióra. A magas tranzakció kötetek Ez azt eredményezheti, interaktív terület.</li>
<li>Egy alkalmazott egy új osztály használatával egy EGT nem helyezhető át.</li>
</ul>
</td>
</tr>
<tr>
<td>Külön entitástípusok, a különböző partíciók vagy a táblák vagy a storage-fiókok</td>
<td>
<ul>
<li>A részleg entitás vagy az alkalmazott entitás egyetlen művelettel frissítheti.</li>
<li>A magas tranzakció kötetek ez elősegítheti a terhelés elosztva több partíciót.</li>
</ul>
</td>
<td>
<ul>
<li>Az alkalmazottak és a bizonyos ügyfél tevékenységek egy részleg entitást szeretne.</li>
<li>Nem használhat EGTs biztosítja az egységességet amikor akkor frissítés/Beszúrás/Törlés az alkalmazottak és a frissítés részleget. Például frissítése folyamatban van egy szervezeti egységben egy alkalmazott száma.</li>
<li>Egy alkalmazott egy új osztály használatával egy EGT nem helyezhető át.</li>
</ul>
</td>
</tr>
<tr>
<td>Az egyetlen entitás típusa denormalize</td>
<td>
<ul>
<li>Az egy kérelemhez szükséges összes információ kérheti le.</li>
</ul>
</td>
<td>
<ul>
<li>Biztosítja az egységességet, ha a szervezeti adatok (Ez akkor miatt frissítenie kell egy osztály minden alkalmazott) kell drága lehet.</li>
</ul>
</td>
</tr>
</table>

Hogyan választja ezeket a beállításokat, és mely előnyének és hátrányának jelentős, függ az adott alkalmazás forgatókönyvek között. Például milyen gyakran tegye módosítása részleg entitások; szükség van a további részlegre vonatkozó információkat; az alkalmazott lekérdezés milyen közel legyenek, a partíciók vagy a tárfiók a méretezhetőségének korlátai?  

### <a name="one-to-one-relationships"></a>-Az-egyhez kapcsolat
Tartomány modellek egy az egyhez típusú entitások közötti kapcsolatok is tartalmazhat. Kell megvalósítani a Table szolgáltatás egy az egyhez kapcsolat, a két kapcsolódó entitás csatolása, ha vissza mindkét kell is ki kell választania. Ez a hivatkozás lehet implicit, egy egyezmény értékek alapján, vagy explicit hivatkozás formájában elhelyezésével **PartitionKey** és **RowKey** értékek az entitásban, a kapcsolódó entitás. E tárolja a kapcsolódó entitásokból partícióra tárgyalását című [egy-a-többhöz kapcsolatok](#one-to-many-relationships).  

Vegye figyelembe, hogy nincsenek-e is vezethet, hogy egy az egyhez kapcsolat megvalósíthatja a Table szolgáltatás megvalósítási szempontokat:  

* Nagy entitások kezelése (további információkért lásd: [nagy entitások mintát](#large-entities-pattern)).  
* A végrehajtási hozzáférés-vezérlést (további információkért lásd: [megosztott hozzáférési aláírásokkal-hozzáférés szabályozásáról](#controlling-access-with-shared-access-signatures)).  

### <a name="join-in-the-client"></a>Az ügyfél csatlakozott
Bár vannak lehetőségek kapcsolatok modellezésére a Table szolgáltatásban, érdemes nem elfelejti, hogy a két elsődleges oka a Table szolgáltatás használatára vonatkozó-e a méretezhetőséget és teljesítményt nyújt. Ha meg vannak modellezés több kapcsolatot, amelyek veszélyeztetik a teljesítmény és méretezhetőség a megoldás, kérdezze meg saját kezűleg esetén az adatok minden olyan kapcsolat létrehozásához rendszerbe tábla szükséges. Előfordulhat, hogy a tervezési és javítását célzó méretezhetőségét és teljesítményét a megoldás, ha az ügyfélalkalmazást, hajtsa végre a szükséges illesztések.  

Például ha adatokat tartalmaznak, amelyek nem változik gyakran nagyon kis táblákhoz, majd után lekérdezhetik ezeket az adatokat többször is gyorsítótárazása azt az ügyfélen. Ennek elkerülése érdekében ismételt használatával ugyanazokat az adatokat beolvasni. A példákban azt kell venni, a jelen útmutató kis szervezet szervezeti egységek készletét valószínű, hogy rövid, és módosítsa a ritkán így egy jó jelölt az ügyfélalkalmazás tölthetik le egyszer adatok és a gyorsítótár, keresse meg a adatok.  

### <a name="inheritance-relationships"></a>Öröklődés kapcsolatok
Ha az ügyfélalkalmazást használ, amely egy üzleti entitásokat képviselő öröklési kapcsolatban részét osztályok, egyszerűen a a Table szolgáltatásban entitásokból maradnak meg. Például lehetséges, hogy az ügyfél alkalmazásban megadott üzenetosztályának beolvasása a következő funkciócsoport ahol **személy** absztrakt osztály.

![][3]

A Table szolgáltatásban entitások használata, hogy a hely egyetlen személy tábla használatával két konkrét osztályok példányai is továbbra is fennáll:  

![][4]

További információt az Ügyfélkód ugyanabban a táblában a több entitás, című [használata heterogén entitástípusok](#working-with-heterogeneous-entity-types) Ez az útmutató későbbi. Ez az Ügyfélkód entitástípus felismerése példákat tartalmaz.  

## <a name="table-design-patterns"></a>Táblázat kialakítási minták
A korábbi szakaszokban láthatta, néhány részletes beszélgetéseket optimalizálása a Táblatervezés mindkét lekérése során Entitásadatok lekérdezésekkel és beszúrását, frissítését és entitás adatok törlése. Ez a szakasz ismerteti az egyes mintázatok megfelelő Table szolgáltatási megoldások való használatra. Ezenkívül láthatja, hogyan meg is gyakorlatilag cím egyes problémák és kompromisszumot alakítson ki az útmutatóban korábban következik be. Az alábbi ábra a különböző minták kapcsolatai foglalja össze:  

![][5]

A minta térkép fent néhány minták (kék) és a jelen útmutatóban leírt elleni minták (narancs) közötti kapcsolatokat mutatja be. Természetesen nincsenek sok más mintákat, amelyek érdemes figyelembe véve. Például a Table szolgáltatás azon kulcsfontosságú forgatókönyvek egyike használatára a [materializált nézet mintát](https://msdn.microsoft.com/library/azure/dn589782.aspx) a a [parancs lekérdezés felelősségi elkülönítése (CQRS)](https://msdn.microsoft.com/library/azure/jj554200.aspx) mintát.  

### <a name="intra-partition-secondary-index-pattern"></a>Helyen belüli-partíció másodlagos index minta
Több másolatot minden entitás használatával különböző tárolására **RowKey** értékeket (a partícióra) engedélyezése gyors és hatékony keresések és a másodlagos rendezési sorrend különböző **RowKey** értékeket. Példányok közötti frissítéseket is konzisztens EGT tartozó használatával.  

#### <a name="context-and-problem"></a>A környezetben, és probléma
A Table szolgáltatás automatikusan elvégzi a segítségével a **PartitionKey** és **RowKey** értékeket. Ez lehetővé teszi, hogy hatékonyan végez ezeket az értékeket entitást lekérdezni egy ügyfélalkalmazást. Például a lent látható módon táblaszerkezet, ügyfélalkalmazás használatával pont lekérdezés egy adott alkalmazott bejegyzés lekérdezésére a részleg neve és az alkalmazott azonosítója (a **PartitionKey** és **RowKey** értékek). Egy ügyfél is kérje le az entitásokat alkalmazottazonosító belül minden részleg szerint rendezve.

![][6]

Ha is érdemes lehet egy másik tulajdonság, például az e-mail cím alapján alkalmazott entitás megkeresni a kevésbé hatékony partíció ellenőrzések keres kell használnia. Ennek oka az, a table szolgáltatás nem adja meg a másodlagos kulcsot. Ezenkívül nincs lehetőség az alkalmazottakat, mint egy másik sorrendbe rendezve kéréséhez **RowKey** sorrendje.  

#### <a name="solution"></a>Megoldás
Másodlagos indexek hiánya megoldása érdekében minden egyes másolataihoz, a különböző entitás több példányát is tárolhatja **RowKey** érték. Ha egy entitás tárolja az alább látható struktúrákat, e-mail címét vagy az alkalmazott azonosítója alapján alkalmazott entitások hatékonyan kérheti le. Az előtag értékei a **RowKey**, "empid_" és "email_" lehetővé teszi egyetlen alkalmazott vagy alkalmazottak számos lekérdezése a számos különböző e-mail címek vagy alkalmazott-azonosítók használatával.  

![][7]

Az alábbi két feltételeket (egy keresésekor által alkalmazott- és egy keresése e-mail cím alapján) is adja meg a pont lekérdezéseket:  

* $filter = (PartitionKey eq 'Értékesítés') és (RowKey eq "empid_000223")  
* $filter = (PartitionKey eq 'Értékesítés') és (RowKey eq 'email_jonesj@contoso.com")  

Ha alkalmazott entitástartományának kérdezze le, megadhatja a alkalmazott azonosítója sorrendbe rendezve tartománya vagy egy tartományt, a megfelelő előtaggal rendelkező entitások lekérdezésével e-mail cím sorrendbe rendezve a **RowKey**.  

* Az alkalmazottak található a tartomány 000100 való 000199 használatban alkalmazott azonosítóval rendelkező az értékesítési osztályon: $filter = (PartitionKey eq 'Értékesítés') és (RowKey ge "empid_000100") és (RowKey le "empid_000199")  
* Az alkalmazottak található az értékesítési osztályon kezdve "a" használja e-mail címmel rendelkező: $filter = (PartitionKey eq 'Értékesítés') és (RowKey ge "email_a") és (RowKey lt "email_b")  
  
  Vegye figyelembe, hogy a Table szolgáltatásból REST API-t a további tudnivalókat lásd a fenti példákban használt szűrőszintaxisának [lekérdezés entitások](http://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problémákat és szempontok
Ebben a mintában megvalósításához meghatározásakor, vegye figyelembe a következő szempontokat:  

* A TABLE storage viszonylag olcsó használatára, így költség növeli a duplikált adatok tárolására nem szabad jelentős probléma. Azonban kell kiértékelésének eredménye mindig a tervező a várható tárolási szükségletek költségeinek, és csak hozzá az ügyfélalkalmazást hajtja végre a lekérdezéseket kettős bejegyzés.  
* Mivel a másodlagos index entitások az eredeti entitásokat tartalmazó partícióra vannak tárolva, ellenőrizze, hogy nem haladhatja meg a a méretezhetőségi célok, az egyes partíciók.  
* Beállíthatja, hogy az ismétlődő entitások egymással konzisztenssé i frissíteni az entitást két példányával EGTs használatával. Ez azt jelenti, hogy ugyanazon partíció egy entitás összes másolatát tárolja. További információkért tekintse meg a szakasz [entitás csoport tranzakciók használatával](#entity-group-transactions).  
* A használt érték a **RowKey** minden egyes entitásnál egyedinek kell lennie. Érdemes lehet összetett kulcs értékeket.  
* Kitöltési szerepel benne numerikus érték a **RowKey** (például a alkalmazottazonosító 000223), lehetővé teszi, hogy javítsa ki, rendezési és szűrési alapján felső és alsó határait.  
* Nem feltétlenül kell ismétlődő az entitás tulajdonságait. Például ha a lekérdezések, hogy a Keresés az entitásokat, az e-mail cím a **RowKey** soha nem kell az alkalmazott életkora, ezeket az entitásokat eredményezhet. az alábbi szerkezettel:

![][8]

* Általában jobb megoldás, az ismétlődő adatokat tárolhatnak, és győződjön meg arról, hogy egyetlen lekérdezést, a szükséges összes adatot kérheti le egy lekérdezés használata egy entitás, majd egy másikat talált a szükséges adatok kereséséhez.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes használni ezt a mintát
Ezt a mintát használja, ha az ügyfélalkalmazást kérje le az entitásokat, különböző kulccsal, számos használatát, ha az ügyfél eltérő rendezési sorrend entitásának lekérése, valamint olyan esetben minden entitás egyedi értékeket számos segítségével azonosíthatja. Azonban ügyelnie kell, hogy Ön nem lépik túl a partíció méretezhetőségének korlátai a különböző entitás keresések végrehajtásakor **RowKey** értékeket.  

#### <a name="related-patterns-and-guidance"></a>Útmutató és a kapcsolódó minták
A következő mintákat és útmutatókat is megfelelő ebben a mintában végrehajtása során:  

* [Másodlagos helyek közötti partíció index minta](#inter-partition-secondary-index-pattern)
* [Összetett kulcs minta](#compound-key-pattern)
* [Entitás csoport tranzakciók](#entity-group-transactions)
* [Heterogén entitástípusok használata](#working-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Másodlagos helyek közötti partíció index minta
Több másolatot minden entitás használatával különböző tárolására **RowKey** értékek a különböző partíciók, vagy a külön táblázatokat engedélyezése gyors és hatékony keresések és a másodlagos rendezési sorrend különböző **RowKey** értékeket.  

#### <a name="context-and-problem"></a>A környezetben, és probléma
A Table szolgáltatás automatikusan elvégzi a segítségével a **PartitionKey** és **RowKey** értékeket. Ez lehetővé teszi, hogy hatékonyan végez ezeket az értékeket entitást lekérdezni egy ügyfélalkalmazást. Például a lent látható módon táblaszerkezet, ügyfélalkalmazás használatával pont lekérdezés egy adott alkalmazott bejegyzés lekérdezésére a részleg neve és az alkalmazott azonosítója (a **PartitionKey** és **RowKey** értékek). Egy ügyfél is kérje le az entitásokat alkalmazottazonosító belül minden részleg szerint rendezve.  

![][9]

Ha is érdemes lehet egy másik tulajdonság, például az e-mail cím alapján alkalmazott entitás megkeresni a kevésbé hatékony partíció ellenőrzések keres kell használnia. Ennek oka az, a table szolgáltatás nem adja meg a másodlagos kulcsot. Ezenkívül nincs lehetőség az alkalmazottakat, mint egy másik sorrendbe rendezve kéréséhez **RowKey** sorrendje.  

Ezeket az entitásokat tranzakciókról nagyon nagy mennyiségű rendszer előrejelző, és szeretné, hogy a Table szolgáltatás szabályozása az ügyfél kockázatának minimalizálása érdekében.  

#### <a name="solution"></a>Megoldás
Másodlagos indexek hiánya megkerüléséhez tárolhat több másolatot minden példány használatával minden entitás különböző **PartitionKey** és **RowKey** értékeket. Ha egy entitás tárolja az alább látható struktúrákat, e-mail címét vagy az alkalmazott azonosítója alapján alkalmazott entitások hatékonyan kérheti le. Az előtag értékei a **PartitionKey**, "empid_" és "email_" engedélyezi, hogy melyik index lekérdezéshez használni kívánt azonosításához.  

![][10]

Az alábbi két feltételeket (egy keresésekor által alkalmazott- és egy keresése e-mail cím alapján) is adja meg a pont lekérdezéseket:  

* $filter = (PartitionKey eq ' empid_Sales") és (RowKey eq"000223")
* $filter = (PartitionKey eq ' email_Sales") és (RowKey eq 'jonesj@contoso.com")  

Ha alkalmazott entitástartományának kérdezze le, megadhatja a alkalmazott azonosítója sorrendbe rendezve tartománya vagy egy tartományt, a megfelelő előtaggal rendelkező entitások lekérdezésével e-mail cím sorrendbe rendezve a **RowKey**.  

* Az alkalmazottak az értékesítési osztályon az alkalmazott ID tartományban található **000100** való **000199** alkalmazott azonosítója sorrendben használja rendezve: $filter = (PartitionKey eq ' empid_Sales") és (RowKey ge"000100") és (RowKey le "000199")  
* Az alkalmazottak az értékesítési részleg kezdetű "a" e-mail cím sorrendben használja rendezett e-mail címmel rendelkező kereséséhez: $filter = (PartitionKey eq ' email_Sales") és (RowKey ge"a") és (RowKey lt"b")  

Vegye figyelembe, hogy a Table szolgáltatásból REST API-t a további tudnivalókat lásd a fenti példákban használt szűrőszintaxisának [lekérdezés entitások](http://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problémákat és szempontok
Ebben a mintában megvalósításához meghatározásakor, vegye figyelembe a következő szempontokat:  

* Hálózati adaptere esetében megtarthatja az ismétlődő entitások idővel konzisztenssé egymással használatával a [idővel konzisztenssé tranzakciók mintát](#eventually-consistent-transactions-pattern) az elsődleges és másodlagos index entitások fenntartásához.  
* A TABLE storage viszonylag olcsó használatára, így költség növeli a duplikált adatok tárolására nem szabad jelentős probléma. Azonban kell kiértékelésének eredménye mindig a tervező a várható tárolási szükségletek költségeinek, és csak hozzá az ügyfélalkalmazást hajtja végre a lekérdezéseket kettős bejegyzés.  
* A használt érték a **RowKey** minden egyes entitásnál egyedinek kell lennie. Érdemes lehet összetett kulcs értékeket.  
* Kitöltési szerepel benne numerikus érték a **RowKey** (például a alkalmazottazonosító 000223), lehetővé teszi, hogy javítsa ki, rendezési és szűrési alapján felső és alsó határait.  
* Nem feltétlenül kell ismétlődő az entitás tulajdonságait. Például ha a lekérdezések, hogy a Keresés az entitásokat, az e-mail cím a **RowKey** soha nem kell az alkalmazott életkora, ezeket az entitásokat eredményezhet. az alábbi szerkezettel:
  
  ![][11]
* Érdemes jellemzően ismétlődő adatokat tárolhatnak, és győződjön meg arról, hogy egyetlen lekérdezést egy lekérdezési használata a másodlagos index, míg a másik keresési a szükséges adatok használatát az elsődleges index entitás található a szükséges összes adatot kérheti le.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes használni ezt a mintát
Ezt a mintát használja, ha az ügyfélalkalmazást kérje le az entitásokat, különböző kulccsal, számos használatát, ha az ügyfél eltérő rendezési sorrend entitásának lekérése, valamint olyan esetben minden entitás egyedi értékeket számos segítségével azonosíthatja. Ezt a mintát használja, ha el szeretné kerülni, a partíció méretezhetőségének korlátai meghaladja a különböző entitás keresések végrehajtásakor **RowKey** értékeket.  

#### <a name="related-patterns-and-guidance"></a>Útmutató és a kapcsolódó minták
A következő mintákat és útmutatókat is megfelelő ebben a mintában végrehajtása során:  

* [Idővel konzisztenssé tranzakciók minta](#eventually-consistent-transactions-pattern)  
* [Helyen belüli-partíció másodlagos index minta](#intra-partition-secondary-index-pattern)  
* [Összetett kulcs minta](#compound-key-pattern)  
* [Entitás csoport tranzakciók](#entity-group-transactions)  
* [Heterogén entitástípusok használata](#working-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Idővel konzisztenssé tranzakciók minta
Engedélyezze a idővel konzisztenssé viselkedés partícióhatárok vagy tárolási rendszer határok az Azure-üzenetsorok használatával.  

#### <a name="context-and-problem"></a>A környezetben, és probléma
EGTs atomi tranzakciók engedélyezhető a kiszolgálók közötti ugyanazzal a partíciós kulccsal rendelkező entitásokat. A teljesítmény és méretezhetőség érdekében dönthet külön partíciók vagy egy különálló tárhelyet rendszer konzisztencia-követelményekkel rendelkező entitások tárolására: ilyen esetben nem használhat EGTs biztosítja az egységességet. Például lehetséges, hogy követelmény a végleges konzisztencia közötti karbantartása:  

* Az entitások tárolva két különböző partíciók ugyanabban a táblában, a különböző táblák, a másik tárfiókokban.  
* A Table szolgáltatásban tárolni entitás és a Blob szolgáltatásban tárolt blob.  
* A Table szolgáltatás és a fájl a fájlrendszerben tárolt entitás.  
* A Table szolgáltatásban az entitást áruházra még indexelése az Azure Search szolgáltatás használatával.  

#### <a name="solution"></a>Megoldás
Azure üzenetsorok használata esetén alkalmazhat olyan megoldás, amely továbbítja a végleges konzisztencia két vagy több partíciót, vagy tárolási rendszerek között.
Ezt a módszert mutatja be, során feltételezzük, hogy archiválja a régi alkalmazott entitások tennie követelmény. Régi alkalmazott entitások ritkán a rendszer megkérdezi, és olyan tevékenységet, amely az aktuális alkalmazottak kezelésére ki kell zárni. Ez a követelmény végrehajtásához aktív alkalmazottak tárolja a **aktuális** tábla és a régi alkalmazottak a **archív** tábla. Egy alkalmazott archiválásához törlését a vállalat, a **aktuális** tábla, és adja hozzá az entitás a **archív** táblában, de nem használható egy EGT két művelet végrehajtásához. Annak elkerülése érdekében okozó hiba jelenik meg egy entitás mindkét vagy egyiket se táblázatokban, az archiválási művelet idővel konzisztenssé kell lennie. A következő Szekvenciadiagram ebben a műveletben lépéseit mutatja be. Kivétel elérési utak, a következő szöveg a biztosított további információkhoz juthat.  

![][12]

Üzenet helyez el egy Azure-üzenetsorba, ebben a példában alkalmazott #456 archiválására az ügyfél kezdeményez az archiválási művelet. A feldolgozói szerepkör kérdezze le a várólista új üzenetek; Ha talál egyet, kiolvassa az üzenetet, és egy rejtett másolatot elhagyja a várólistán. A feldolgozói szerepkör ezután lekéri az entitást egy példányát a **aktuális** table, a másolja a **archív** tábla, és végül törli az eredeti a **aktuális** tábla. Végül ha nincs hiba az előző lépésekben vannak, a feldolgozói szerepkör törli az rejtett üzenetet az üzenetsorból.  

Ebben a példában a 4. lépés szúr be az alkalmazott a **archív** tábla. Az alkalmazott a Blob szolgáltatás blob vagy egy fájl a fájlrendszerben hozzáadhatja azt.  

#### <a name="recovering-from-failures"></a>Végezze el a hibák
Fontos, hogy lépéseket műveleteknek **4** és **5** kell *idempotent* abban az esetben a feldolgozói szerepkör újra kell indítani az archiválási művelet. Ha használ a Table szolgáltatás lépés **4** kell használnia az "insert vagy cserélje le a" művelet; lépés **5** kell használnia egy "törlése, ha létezik-e" az ügyféloldali kódtár használata a műveletet. Ha egy másik tárolási rendszert használ, egy megfelelő idempotent műveletet kell használnia.  

Ha a feldolgozói szerepkör soha nem fejeződik be a lépés **6**, majd után egy időtúllépési az üzenet ismét megjelenik a várólistán, készen áll arra, hogy újból feldolgozza, próbáljon a feldolgozói szerepkör esetében. A feldolgozói szerepkör ellenőrizheti, hogy hányszor egy üzenetet a várólistában lett, olvassa el, és ha szükséges, ez a jelző külön várólista küldésével egy "elhalt" üzenet, hogy meg lehessen vizsgálni. Várólista-üzenetek olvasásához és a dequeue számának ellenőrzése kapcsolatos további információkért lásd: [üzeneteket beolvasni](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

A tábla- és várólista szolgáltatásokból hibák átmeneti hibák, és az ügyfélalkalmazás tartalmaznia kell a megfelelő újrapróbálkozási logika kezelni azokat.  

#### <a name="issues-and-considerations"></a>Problémákat és szempontok
Ebben a mintában megvalósításához meghatározásakor, vegye figyelembe a következő szempontokat:  

* Ez a megoldás elkülönítési tranzakció nem ad meg. Például egy ügyfél olvasható a **aktuális** és **archív** táblák, ha a feldolgozói szerepkör lépések között **4** és **5**, és tekintse meg egy az adatok inkonzisztens nézetét. Vegye figyelembe, hogy az adatok konzisztens idővel.  
* Lehet, hogy-e 4. és 5 idempotent végleges konzisztencia biztosításához.  
* A megoldás több várólisták és feldolgozópéldányok szerepkör segítségével méretezhető.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes használni ezt a mintát
Ebben a mintában használja, ha azt szeretné, amely szerepel a különböző partíciók, illetve olyan táblázatok entitások közötti végleges konzisztencia biztosításához. Ebben a mintában biztosíthatja a végleges műveletekhez a tábla és a Blob szolgáltatás és más nem Azure Storage között adatforrások, például az adatbázis vagy a fájlrendszer terjeszthetők ki.  

#### <a name="related-patterns-and-guidance"></a>Útmutató és a kapcsolódó minták
A következő mintákat és útmutatókat is megfelelő ebben a mintában végrehajtása során:  

* [Entitás csoport tranzakciók](#entity-group-transactions)  
* [Egyesítés vagy cseréje](#merge-or-replace)  

> [!NOTE]
> Ha a tranzakció elkülönítési fontos, hogy a megoldás, vegye figyelembe a táblák lehetővé váljon EGTs újratervezése.  
> 
> 

### <a name="index-entities-pattern"></a>Index entitások minta
Ahhoz, hogy térjen vissza az entitások listák hatékony kereséseket index entitások karbantartása.  

#### <a name="context-and-problem"></a>A környezetben, és probléma
A Table szolgáltatás automatikusan elvégzi a segítségével a **PartitionKey** és **RowKey** értékeket. Ez lehetővé teszi, hogy egy entitás hatékonyan pont lekérdezéssel lekérni ügyfélalkalmazást. Például használja az alábbi táblázat szerkezetét, ügyfélalkalmazás hatékonyan alkalmazott entitás használatával lekérhető a részleg neve és az alkalmazott azonosítója (a **PartitionKey** és **RowKey**).  

![][13]

Ha szeretné tenni a vezetéknevét, például egy másik nem egyedi tulajdonság alapján alkalmazott entitások listájának beolvasása a kevésbé hatékony partíció ellenőrzések található egyező helyett index kereshető azokat közvetlenül kell használnia. Ennek oka az, a table szolgáltatás nem adja meg a másodlagos kulcsot.  

#### <a name="solution"></a>Megoldás
Vezetéknév keresési engedélyezze a fent látható entitás struktúrával, kezelnie kell az alkalmazott azonosítók listája. Szeretné beolvasni a alkalmazott entitások egy adott nevű utolsó, például a János, ha először keresse meg az alkalmazott azonosítók listáját János a Vezetéknév, az alkalmazottak számára, és az majd lekérheti az alkalmazott entitásokból. Az alkalmazott azonosítók listájának tárolására három fő lehetőség áll rendelkezésre:  

* A blob storage használata.  
* Hozzon létre indexet entitások az alkalmazott entitásokat tartalmazó partícióra.  
* Hozzon létre indexet entitások külön partíció vagy tábla.  

<u>#1. lehetőség: Használja a blob storage</u>  

Az első lehetőség létrehoz minden egyedi Vezetéknév, és minden egyes blob tárolóban blob listáját a **PartitionKey** (osztály) és **RowKey** adott Vezetéknév rendelkező alkalmazottak (alkalmazott azonosítója) értékeit. Adja hozzá, vagy egy alkalmazott törlésekor biztosítania kell, hogy idővel konzisztenssé váljanak a alkalmazott entitások a megfelelő blob tartalma.  

<u>#2. lehetőség:</u> ugyanazon partíció index entitások létrehozása  

A második lehetőség használja a következő adatok tárolására szolgáló index entitás:  

![][14]

A **EmployeeIDs** tárolt utolsó nevű tulajdonsága tartalmazza az alkalmazottak számára alkalmazott azonosítók listáját a **RowKey**.  

Az alábbi lépéseket kell követnie, amikor a hozzáadni kívánt új alkalmazott a második lehetőség használata vázoltuk. Ebben a példában azt ad hozzá egy alkalmazott azonosítója 000152 és a Vezetéknév János az értékesítési részleg:  

1. Az index bejegyzés lekérdezésére egy **PartitionKey** "Értékesítési" értéket, és a **RowKey** érték "János." Mentse ezt az entitást a 2. lépésben használandó ETag címkét.  
2. Hozzon létre egy entitás-csoport tranzakció (Ez azt jelenti, hogy a kötegelt művelet), amely az új alkalmazottak entitás beszúrása (**PartitionKey** "Értékesítési" érték és **RowKey** "000152" értéket), és frissíti az index entitás (**PartitionKey** "Értékesítési" értéket, és **RowKey** "János" érték) az új alkalmazottazonosító ad hozzá a EmployeeIDs mező listáján. Entitás csoport tranzakciókkal kapcsolatos további információkért lásd: [entitás csoport tranzakciók](#entity-group-transactions).  
3. Ha az entitás csoport tranzakció (valaki más csak módosította az index entitás) egyidejű hozzáférések optimista hiba miatt nem sikerül, akkor szüksége az 1. lépés: újra újrakezdéshez.  

Egy alkalmazott törlése, a második lehetőség használata hasonló megközelítése is használhatja. Egy alkalmazott Vezetéknév módosítása nem kicsit bonyolultabb, mert szüksége lesz egy entitás csoport tranzakció, amely három entitások frissíti hajtható végre: az alkalmazott, a régi vezetékneve index entitás, és a index entitás az új utolsó nevét. Minden entitás optimista konkurenciát alkalmazott frissítések végrehajtásához használhatja ETag értékek beolvasása érdekében módosítások végrehajtása előtt be kell olvasni.  

Az alábbi lépéseket kell követnie, amikor meg kell keresnie egy osztály adott utolsó nevű az alkalmazottak, a második lehetőség használata vázoltuk. Ebben a példában azt keressük az értékesítési részleg utolsó nevű János az alkalmazottak:  

1. Az index bejegyzés lekérdezésére egy **PartitionKey** "Értékesítési" értéket, és a **RowKey** érték "János."  
2. Elemezni alkalmazott a EmployeeIDs mezőben azonosítók listáját.  
3. Ha további információ az egyes ezeknek a dolgozóknak (például az e-mail címmel) van szüksége, lekéri az egyes használatával alkalmazott entitások **PartitionKey** "Értékesítési" értéket, és **RowKey** közötti értéket a 2. lépésben beolvasott alkalmazottak listája.  

<u>#3. lehetőség:</u> index entitások külön partíció vagy tábla létrehozása  

A harmadik lehetőség használja a következő adatok tárolására szolgáló index entitás:  

![][15]

A **EmployeeIDs** tárolt utolsó nevű tulajdonsága tartalmazza az alkalmazottak számára alkalmazott azonosítók listáját a **RowKey**.  

A harmadik beállítással biztosítja az egységességet, mert az index entitások az alkalmazott entitások külön partícióra EGTs nem használható. Ellenőrizze, hogy idővel konzisztenssé váljanak a alkalmazott entitások-e az index entitásokat.  

#### <a name="issues-and-considerations"></a>Problémákat és szempontok
Ebben a mintában megvalósításához meghatározásakor, vegye figyelembe a következő szempontokat:  

* Ehhez a megoldáshoz szükségesek egyező entitást lekérdezni legalább két lekérdezést: egyet az index entitásokat a listájának lekérdezése **RowKey** értékek, majd beolvasni a listában szereplő lekérdezések.  
* Fényében, hogy az egyes entitásnak van a maximális méret 1 MB, #2 és a megoldás #3 lehetőség feltételezik, hogy bármely adott Vezetéknév alkalmazott azonosítóinak listáját soha nem 1 MB-nál nagyobb. Ha alkalmazott azonosítók listáját mérete 1 MB-nál nagyobbnak kell lennie, #1. lehetőség és az index adatokat a blob Storage tárolóban tárolja.  
* #2. beállítás (EGTs használ hozzáadása és az alkalmazottak törlése és módosítása az alkalmazott Vezetéknév kezeléséhez) ki kell értékelnie, ha a tranzakciók mennyisége fog megközelíti a méretezhetőségének korlátai egy adott partíció. Ha ez a helyzet, fontolja meg egy idővel konzisztenssé megoldás (#1. lehetőség vagy #3. lehetőség), amely várólisták használja a frissítés-kérelmeket kezelnek, és lehetővé teszi, hogy az index entitások az alkalmazott entitások külön partícióra.  
* Ebben a megoldásban #2. lehetőség azt feltételezi, hogy szeretné-e kereshet meg egy részleg vezetéknév: például szeretné beolvasni az alkalmazottakat az értékesítési részleg János utolsó néven. Ha szeretné tudni nevű utolsó János a teljes szervezeten belül minden alkalmazott kereshet, #1. lehetőség vagy a #3. lehetőség használatával.
* Megvalósíthat egy várólista-alapú megoldás, amely a végleges konzisztencia biztosítja (lásd a [idővel konzisztenssé tranzakciók mintát](#eventually-consistent-transactions-pattern) további részletekért).  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes használni ezt a mintát
Ezt a mintát használja, ha meg szeretné megkeresni az, hogy minden közös közös tulajdonság értéke, például minden alkalmazott Vezetéknév János a entitások készletének.  

#### <a name="related-patterns-and-guidance"></a>Útmutató és a kapcsolódó minták
A következő mintákat és útmutatókat is megfelelő ebben a mintában végrehajtása során:  

* [Összetett kulcs minta](#compound-key-pattern)  
* [Idővel konzisztenssé tranzakciók minta](#eventually-consistent-transactions-pattern)  
* [Entitás csoport tranzakciók](#entity-group-transactions)  
* [Heterogén entitástípusok használata](#working-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Denormalization minta
Együtt kapcsolódó adatok egyesítése egyetlen entitás ahhoz, hogy beolvasni az összes adatot a hibaérzékeny pontok lekérdezéssel van szüksége.  

#### <a name="context-and-problem"></a>A környezetben, és probléma
Egy relációs adatbázisban akkor általában normalizálása az adatok eltávolítása a lekérdezéseiben, amelyeket adatainak lekérése több táblából származó ismétlődést. Ha normalizálása az adatok Azure-táblákban, meg kell győződnie több kiszolgálókkal való adatváltások számát az ügyfélről a kiszolgálónak a kapcsolódó adatok beolvasása. Például a táblázat szerkezetét alábbi értékekre, akkor a két adatváltások beolvasni egy részleg részleteit kell: egyet a részleg entitás, amely tartalmazza a kezelő azonosítója és a kezelő részletek alkalmazott entitás lehívása majd egy másik kérelem beolvasása.  

![][16]

#### <a name="solution"></a>Megoldás
Helyett két külön entitás tárolni az adatokat, az adatok denormalize, és a részleg entitás megőrzi a manager részletei. Példa:  

![][17]

Ezekkel a tulajdonságokkal tárolt részleg entitásokkal kapcsolatos pont lekérdezéssel részleget szükséges összes információt most le.  

#### <a name="issues-and-considerations"></a>Problémákat és szempontok
Ebben a mintában megvalósításához meghatározásakor, vegye figyelembe a következő szempontokat:  

* Van néhány költsége terhet kétszer néhány adatainak tárolásához. A teljesítmény előny (kevesebb kéréseket a tárolási szolgáltatásba) általában ez fontosabb, mint a tárolási költségeket marginális növekedése (és az ehhez kapcsolódó költséget részben eltolva csökkentése szüksége van egy részleg részleteinek beolvasása tranzakciók száma ).  
* A kezelők vonatkozó információt tároló két entitások konzisztencia kell fenntartani. Egy atomi tranzakción belül több entitás frissítése EGTs használatával kezelni tud a konzisztencia problémát: Ebben az esetben a részleg, és a alkalmazott entitás részleg kezelőjének partícióra tárolják.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes használni ezt a mintát
Ebben a mintában használja, ha gyakran kell a kapcsolódó tudnivalókat. Ebben a mintában csökkenti az ügyfél biztosítsa a szükséges adatok beolvasásához lekérdezések száma.  

#### <a name="related-patterns-and-guidance"></a>Útmutató és a kapcsolódó minták
A következő mintákat és útmutatókat is megfelelő ebben a mintában végrehajtása során:  

* [Összetett kulcs minta](#compound-key-pattern)  
* [Entitás csoport tranzakciók](#entity-group-transactions)  
* [Heterogén entitástípusok használata](#working-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Összetett kulcs minta
Használjon összetett **RowKey** értékek kapcsolódó adatok egyetlen pont lekérdezéssel talált ügyfél engedélyezése.  

#### <a name="context-and-problem"></a>A környezetben, és probléma
Egy relációs adatbázisban hogy a rendszer teljesen illesztések lekérdezésekben használatához kapcsolódó adatok kódrészletek vissza az ügyfélnek egyetlen lekérdezést természetes. Például használhatja a alkalmazottazonosító megkeresheti a kapcsolódó entitásokból, melyek teljesítményét, és tekintse át az adatokat azt listáját.  

Tegyük fel, a Table szolgáltatás használatával az alábbi szerkezettel alkalmazott entitások tárolja:  

![][18]

Szükség értékelést, és a teljesítmény évente az alkalmazott működött a szervezet számára vonatkozó előzményadatok tárolja, és érhetik el ezeket az információkat évente kell. Egy elem egy másik tábla tárolja az alábbi szerkezettel rendelkező entitások létrehozásához:  

![][19]

Figyelje meg, hogy ezt a módszert is dönthet, hogy néhány adat (például a Keresztnév és Vezetéknév) ismétlődő ahhoz, hogy az egy kérelemhez adatok beolvasása az új entitásban. Azonban az erős konzisztencia nem fenntartása, mivel a két entitások frissítésére i egy EGT nem használható.  

#### <a name="solution"></a>Megoldás
Egy új entitástípus tárolása az eredeti tábla entitások használata az alábbi szerkezettel:  

![][20]

Értesítés az **RowKey** mostantól az alkalmazott azonosítója és az év, amely lehetővé teszi az alkalmazott beolvasásához, és ellenőrizze az adatokat egyetlen entitás egyetlen kérelmet tartalmazó felülvizsgálati adatok összetett kulcs.  

A következő példa bemutatja, hogyan kérheti le a felülvizsgálati adatok (például alkalmazott 000123 az értékesítési részleg) egy alkalmazott:  

$filter = (PartitionKey eq 'Értékesítés') és (RowKey ge "empid_000123") és (RowKey lt "empid_000124") & $select = RowKey, Manager értékelése, társ értékelése, megjegyzések  

#### <a name="issues-and-considerations"></a>Problémákat és szempontok
Ebben a mintában megvalósításához meghatározásakor, vegye figyelembe a következő szempontokat:  

* Könnyen értelmezhető megfelelő elválasztó karaktert kell használnia a **RowKey** érték: például **000123_2012**.  
* Ehhez az entitáshoz kapcsolódó adatokat tartalmaznak az azonos alkalmazott, ami azt jelenti, EGTs segítségével karbantartása erős konzisztenciát végző más entitásokkal partícióra is tárolja.
* Akkor érdemes megfontolni, hogy milyen gyakran fogja kérdezni az adatokat a megállapításához, hogy ez a minta megfelelő.  Például ha az felülvizsgálati ritkán, és a főbb alkalmazotti adatok gyakran férnek hozzá legyen őket, különálló entitások.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes használni ezt a mintát
Ebben a mintában használatára kell tárolni egy vagy több kapcsolódó entitások lekérdezett gyakran.  

#### <a name="related-patterns-and-guidance"></a>Útmutató és a kapcsolódó minták
A következő mintákat és útmutatókat is megfelelő ebben a mintában végrehajtása során:  

* [Entitás csoport tranzakciók](#entity-group-transactions)  
* [Heterogén entitástípusok használata](#working-with-heterogeneous-entity-types)  
* [Idővel konzisztenssé tranzakciók minta](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Napló végéről minta
Beolvasni a  *n*  partíció legutóbb hozzáadott entitások egy **RowKey** érték, amely fordított dátum és idő sorrendben rendezi.  

#### <a name="context-and-problem"></a>A környezetben, és probléma
A közös vonatkozó követelmény akkor állítható vissza a legutóbb létrehozott entitások, például a tíz legújabb kiadás az alkalmazottak által küldött jogcímek. Táblázat a támogatási lekérdezi egy **$top** lekérdezési művelet vissza az első  *n*  egy entitás: az utolsó n entitások vissza egy készlet nincs egyenértékű lekérdezési művelet.  

#### <a name="solution"></a>Megoldás
Az entitások használatával tárolja a **RowKey** , hogy természetes rendezi fordított dátum/idő ahhoz, így a legutóbbi bejegyzés használatával, mindig az első egy a táblázatban.  

Például nem fogja tudni kérik le az alkalmazottak által küldött tíz legújabb kiadás jogcímeket, használhatja a fordított osztásjelek származó az aktuális dátum/idő értékkel. A következő C# kódminta jeleníti meg a megfelelő "fordított ticks" értéket a létrehozásának egyik módja egy **RowKey** , amely a legújabb rendezi a legrégebbi:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Is vissza a dátum idő értéknek megfelelően a következő kódot:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

A lekérdezés így néz ki:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Problémákat és szempontok
Ebben a mintában megvalósításához meghatározásakor, vegye figyelembe a következő szempontokat:  

* Annak érdekében, hogy a karakterlánc rendezi a várt módon nullából kezdő fordított osztásjelek értékének kell írni.  
* A partíció szinten vonatkozó méretezhetőségi célok tisztában kell lennie. Legyen óvatos nem interaktív terület partíciókat.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes használni ezt a mintát
Ebben a mintában felhasználhatja hozzá kell férnie a fordított dátum/idő sorrend, illetve ha a közelmúltban felvett entitások eléréséhez szükséges szerepelnek.  

#### <a name="related-patterns-and-guidance"></a>Útmutató és a kapcsolódó minták
A következő mintákat és útmutatókat is megfelelő ebben a mintában végrehajtása során:  

* [Illesztenie elleni mintát hozzáfűzése](#prepend-append-anti-pattern)  
* [Entitások beolvasása](#retrieving-entities)  

### <a name="high-volume-delete-pattern"></a>Nagy mennyiségű delete minta
Az entitások nagy mennyiségű törlésének engedélyezése a entitásokhoz egyidejű törlésre tárolása saját külön táblázatban; a tábla törlésével törli az entitásokat.  

#### <a name="context-and-problem"></a>A környezetben, és probléma
Számos alkalmazás törlése a régi adatokat, amely már nem elérhető egy ügyfélalkalmazást, vagy az alkalmazás rendelkezik archivált más adattárolóra. Általában azonosíthatja az ilyen adatokat a dátum: például, hogy egy követelmény, törlendő rekordok, amelyek több mint 60 napos bejelentkezési kérelmek.  

Egy lehetséges tervezési, hogy a dátum és idő, a bejelentkezési kérelem a használja a **RowKey**:  

![][21]

Ez a megközelítés partíció elérési pontokhoz való elkerülhető, mert az alkalmazás beszúrása, és minden felhasználóhoz külön partícióra bejelentkezési entitások törlésére. Azonban ez a megközelítés lehet költséges és időigényes Ha sok entitást mivel először végre kell hajtania a táblázatbeolvasás ahhoz, hogy törli a entitások azonosítása, és törölnie kell minden egyes régi entitás. Vegye figyelembe, hogy adatváltások számát csökkentheti a kiszolgálóra, törölje a régi entitások több törlési kérelmek kötegelés történő EGTs szükséges.  

#### <a name="solution"></a>Megoldás
Egy külön táblázattal minden nap a bejelentkezési kísérletek. A fenti entitás tervező segítségével elkerülése csatlakozási pontokhoz entities beszúrt, és most már egyszerűen törlése egy tábla minden nap adott esetben régi entitások törlése (egyetlen tárhelyművelettel) keresése és több száz és egyéni több ezer törlése helyett bejelentkezési entitások minden nap.  

#### <a name="issues-and-considerations"></a>Problémákat és szempontok
Ebben a mintában megvalósításához meghatározásakor, vegye figyelembe a következő szempontokat:  

* Támogatja a Tervező más módon, az alkalmazás fogja használni az adatok, például adott entitások kapcsolódik-e más adatok, vagy előállítása összesített adatát keresésekor?  
* Nem a Tervező elkerülése interaktív területek új entitások beszúráskor?  
* A késleltetés várható, ha azt szeretné, hogy a tábla néven törlése után újból. Érdemes mindig használjon egyedi tábla neve.  
* Várt néhány szabályozás első használata alkalmával érdemes egy új tábla a Table szolgáltatás Tanulja meg a hozzáférési minták, és a partíciók elosztja a csomópontok között. Milyen gyakran kell létrehoznia az új táblákat vegye figyelembe.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes használni ezt a mintát
Ezt a mintát használja, ha az entitásokat, törölnie kell az egyszerre nagy mennyiségű van.  

#### <a name="related-patterns-and-guidance"></a>Útmutató és a kapcsolódó minták
A következő mintákat és útmutatókat is megfelelő ebben a mintában végrehajtása során:  

* [Entitás csoport tranzakciók](#entity-group-transactions)
* [Entitások módosítása](#modifying-entities)  

### <a name="data-series-pattern"></a>Adatsorozat adatmintát
Tároló teljes adatsorok egyetlen entitás minimalizálása érdekében elvégezte kérelmek számát jelenti.  

#### <a name="context-and-problem"></a>A környezetben, és probléma
Egy általános forgatókönyv van az alkalmazás számára az adatok általában szükséges egyszerre beolvasandó sorozatát tárolja. Például az alkalmazás előfordulhat, hogy minden alkalmazott óránként küld hány Csevegési üzeneteket rögzíti, és majd használja az információt megrajzolásához hány üzenetek minden felhasználóhoz az előző 24 óra során küldött. Lehet, hogy egy tervezési tárolni 24 entitások minden alkalmazott számára:  

![][22]

Ezzel a kialakítással egyszerűen keresse meg és frissítése minden alkalmazott számára, amikor az alkalmazás az üzenet számérték frissíteni kell az entitás módosítására. Azonban a tevékenység diagram megrajzolásához az előző 24 órával kapcsolatos információk lekéréséhez be 24 entitások kell olvasni.  

#### <a name="solution"></a>Megoldás
Külön tulajdonsággal a következő tervezési használatával tárolja az üzenetek száma óránként:  

![][23]

Ezzel a kialakítással használhatja a partícióegyesítési művelet az üzenetek száma az alkalmazott frissíteni az adott időpont. Most kérheti le az összes kérelem használatával egyetlen entitás diagram megrajzolásához szükséges információkat.  

#### <a name="issues-and-considerations"></a>Problémákat és szempontok
Ebben a mintában megvalósításához meghatározásakor, vegye figyelembe a következő szempontokat:  

* Ha a teljes adatsor nem fér el egyetlen entitás (egy entitás legfeljebb 252 tulajdonságot is rendelkeznek), például blob alternatív adattár használata.  
* Ha egy entitás frissítése egyszerre több ügyfélnek, akkor használja a **ETag** egyidejű hozzáférések optimista végrehajtásához. Ha sok ügyfél, magas versengés tapasztalhatja.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes használni ezt a mintát
Akkor használja ezt a mintát, ha kell frissíteni, és az egyes entitáshoz kapcsolódó adatsor beolvasása.  

#### <a name="related-patterns-and-guidance"></a>Útmutató és a kapcsolódó minták
A következő mintákat és útmutatókat is megfelelő ebben a mintában végrehajtása során:  

* [Nagy entitások minta](#large-entities-pattern)  
* [Egyesítés vagy cseréje](#merge-or-replace)  
* [Idővel konzisztenssé tranzakciók mintát](#eventually-consistent-transactions-pattern) (ha tárolja az adatsorozat blob)  

### <a name="wide-entities-pattern"></a>Széles entitások minta
Használni több fizikai entitás legfeljebb 252 tulajdonságot rendelkező logikai entitás tárolni.  

#### <a name="context-and-problem"></a>A környezetben, és probléma
Egyes entitás legfeljebb 252 tulajdonságot (kivéve a kötelező tulajdonságai) állhat, és nem tárolható 1 MB-nál több adatok összesen. Egy relációs adatbázisban általában számíthat round semmilyen határnak sor mérete hozzáadása egy új tábla és a közöttük 1-1 kapcsolatot.  

#### <a name="solution"></a>Megoldás
A Table szolgáltatás több entitás legfeljebb 252 tulajdonságot az egyetlen nagy üzleti objektumot képviselő tárolhatja. Például ha szeretné tárolni az elmúlt 365 napban minden alkalmazott által küldött IM üzenetek száma számát, a következő kialakítás ezért más sémák használ, két olyan entitásra használhatja:  

![][24]

Ha olyan módosítást igénylő frissítése mindkét entitások, hogy továbbra is szinkronizálja egymással kell egy EGT is használhatja. Ellenkező esetben egy egyetlen egyesítési művelet használatával frissítse az üzenetek száma egy adott napjára. Az adatok beolvasása egyetlen alkalmazott be kell olvasni mindkét entitások, amely két hatékony kérelmekre is elvégezhető a **PartitionKey** és egy **RowKey** érték.  

#### <a name="issues-and-considerations"></a>Problémákat és szempontok
Ebben a mintában megvalósításához meghatározásakor, vegye figyelembe a következő szempontokat:  

* A teljes logikai entitás beolvasásakor magában foglalja a legalább két storage-tranzakció: egy minden fizikai bejegyzés lekérdezésére.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes használni ezt a mintát
Használja ezt a mintát mikor kell entitások, amelynek méretét vagy a tulajdonságok száma meghaladja az egyes entitásnál a Table szolgáltatásban tárolni.  

#### <a name="related-patterns-and-guidance"></a>Útmutató és a kapcsolódó minták
A következő mintákat és útmutatókat is megfelelő ebben a mintában végrehajtása során:  

* [Entitás csoport tranzakciók](#entity-group-transactions)
* [Egyesítés vagy cseréje](#merge-or-replace)

### <a name="large-entities-pattern"></a>Nagy entitások minta
A blob storage használatával nagy tulajdonságértékek tárolja.  

#### <a name="context-and-problem"></a>A környezetben, és probléma
Egyes entitás nem 1 MB-nál több adat tárolása összesen. Ha egy vagy több, a tulajdonság tárolja az értékeket, amelyek a teljes mérete meghaladja ezt az értéket az entitás, a teljes entitás nem tárolja a Table szolgáltatás.  

#### <a name="solution"></a>Megoldás
Ha az entitás meghaladja mérete 1 MB, mert egy vagy több tulajdonságának nagy mennyiségű adatot tartalmaz, adatok tárolása a Blob szolgáltatás, és majd tárolja a blob címét az entitásban található egy tulajdonság. Például egy alkalmazott fénykép a blob Storage tárolóban tárolja és tárolására is használható a fénykép mutató hivatkozást a **fénykép** tulajdonság az alkalmazott entitás:  

![][25]

#### <a name="issues-and-considerations"></a>Problémákat és szempontok
Ebben a mintában megvalósításához meghatározásakor, vegye figyelembe a következő szempontokat:  

* Az entitás a Table szolgáltatás és az adatokat a Blob szolgáltatás közötti végleges konzisztencia fenntartása érdekében használja a [idővel konzisztenssé tranzakciók mintát](#eventually-consistent-transactions-pattern) az entitások fenntartásához.
* Legalább két storage-tranzakció teljes entitásnak beolvasása foglalja magában: egyet az entitás és egyet a blob adatainak beolvasása.  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes használni ezt a mintát
Használja ezt a mintát, ha a kell entitások, amelyek mérete meghaladja az egyes entitásnál a Table szolgáltatásban tárolni.  

#### <a name="related-patterns-and-guidance"></a>Útmutató és a kapcsolódó minták
A következő mintákat és útmutatókat is megfelelő ebben a mintában végrehajtása során:  

* [Idővel konzisztenssé tranzakciók minta](#eventually-consistent-transactions-pattern)  
* [Széles entitások minta](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Víruskereső mintát illesztenie hozzáfűzése
Méretezhetőség fokozása, ha nagyszámú Beszúrások által a Beszúrás több partíciót keresztül terjednek.  

#### <a name="context-and-problem"></a>A környezetben, és probléma
Fertőző vagy entitások hozzáfűzi a tárolt entitásokat általában az alkalmazás új entitásokat ad hozzá az első vagy utolsó partíció partíciók több eredményez. Ebben az esetben egy adott időpontban Beszúrások mindegyikét történnek az egyazon partícióra kerüljenek, létrehozása, amely megakadályozza a table szolgáltatás betöltési eseménybeszúrások terheléselosztás több csomópont között, és ami miatt az alkalmazás elérte a méretezhetőségi célok az interaktív terület a partíció. Például, ha az alkalmazottak által elérhető naplók hálózati és az erőforrás alkalmazás, majd egy entitás struktúra alább látható módon eredményezhet a forgalmas adattömbök váljon, ha a tranzakciók mennyisége eléri a méretezhetőség célja a jelenlegi órán partíció egy egyes partíció:  

![][26]

#### <a name="solution"></a>Megoldás
A következő alternatív entitás struktúra bármely adott partíció interaktív terület elkerülhető az alkalmazás naplók eseményként is:  

![][27]

Figyelje meg, az ebben a példában hogyan mindkét a **PartitionKey** és **RowKey** összetett kulcs. A **PartitionKey** a részleg és az alkalmazott azonosítóját használja a naplózás szét több partíciót.  

#### <a name="issues-and-considerations"></a>Problémákat és szempontok
Ebben a mintában megvalósításához meghatározásakor, vegye figyelembe a következő szempontokat:  

* Támogatja az alternatív struktúra, amely hatékonyan létrehozása a gyakran használt adatok partíciók a Beszúrás az ügyfélalkalmazás lekérdezések?  
* A tranzakciók várható mennyisége azt jelenti, hogy valószínűleg, az egyes partíciók a méretezhetőségi célok eléréséhez, és a tároló szolgáltatás által szabályozott kell?  

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes használni ezt a mintát
A víruskereső prepend hozzáfűzése minta elkerülése a tranzakciók mennyisége valószínű, hogy a társzolgáltatás általi szabályozás, amikor hozzáfér a gyakran használt adatok partíció eredményez.  

#### <a name="related-patterns-and-guidance"></a>Útmutató és a kapcsolódó minták
A következő mintákat és útmutatókat is megfelelő ebben a mintában végrehajtása során:  

* [Összetett kulcs minta](#compound-key-pattern)  
* [Napló végéről minta](#log-tail-pattern)  
* [Entitások módosítása](#modifying-entities)  

### <a name="log-data-anti-pattern"></a>Napló elleni adatmintát
Általában kell használnia a Blob szolgáltatás helyett a Table szolgáltatás naplózási adatok tárolására.  

#### <a name="context-and-problem"></a>A környezetben, és probléma
Egy gyakori használati eset, a naplózási adatokat beolvasni a kijelölt naplóbejegyzések adott dátum/idő köre: például keresendő összes hiba és kritikus üzeneteihez, az alkalmazás a rendszer 15:04 és 15:06 egy adott dátumon között. Nem szeretné, hogy a dátum és idő a naplóüzenet segítségével határozza meg a napló entitásokat mentése partíció:, amely az okozza, a működés közbeni partíció egy adott időpontban a napló entitások aliashoz ugyanaz **PartitionKey** érték (lásd: a a szakasz [elleni mintát Prepend hozzáfűzése](#prepend-append-anti-pattern)). Például egy naplófájlüzenetre a következő entitás sémát eredményezi egy gyakran használt adatok partíció, mert az alkalmazás összes naplóüzenetek ír a partíció az aktuális dátum és az óra:  

![][28]

Ebben a példában a **RowKey** tartalmazza a dátum és idő a naplóüzenet annak érdekében, hogy naplóüzenetek kell tárolni, dátum/idő sorrendbe rendezve, és egy üzenetazonosítója abban az esetben, ha több naplóüzenetek megosztása azonos dátumát és idejét.  

Egy másik megoldás, használatához egy **PartitionKey** , amely biztosítja, hogy az alkalmazás írja az üzenetek között számos különböző partíciókat. Például ha a napló-üzenet forrása üzenetek szét sok partíciót lehetőséget biztosít, a következő entitás séma használhatja:  

![][29]

A probléma a séma azonban, hogy egy adott időtartam összes a naplózott üzeneteket beolvasni akkor kell megkeresni minden partíció a táblában.

#### <a name="solution"></a>Megoldás
Az előző szakaszban kiemeli a problémát, a Table szolgáltatás használatakor a naplóbejegyzések és a javasolt a két, nem megfelelő, a terveinek tárolásához. Naplóüzenetek; írása gyenge teljesítményt veszélye kiemelt partíció vezetett egy megoldást a más megoldás eredményezett gyenge lekérdezési teljesítmény megvizsgálja az adott ideig üzeneteket beolvasni a táblázatban minden partíció követelmény miatt. A BLOB storage ilyen esetben jobb megoldást kínál, és ez az az Azure Storage Analytics tárolja a naplózási adatokat gyűjt.  

Ez a szakasz ismerteti, hogyan tárolási analitika napló adatot tárol a blob storage szemléltetésére ezt a módszert, amely általában szerint kíván lekérdezni tartomány adatainak tárolásához.  

Tárolási analitika naplóüzenetek több blobok tagolt formátumban tárolja. A tagolt formátumú megkönnyíti, hogy a napló üzenetben az adatok egy ügyfél alkalmazáshoz.  

Tárolási analitika elnevezési szabályait, amely lehetővé teszi a blob található blobok (vagy blobok), amelyek tartalmazzák a naplófájlüzeneteket keresést, amelynek használja. Például a "queue/2014/07/31/1800/000001.log" nevű blob a queue szolgáltatás a kezdő pozíció: 31 2014. július 18:00 óra kapcsolódó naplóüzenetek tartalmazza. A "000001" azt jelzi, hogy ez az első naplófájlja erre az időszakra. Tárolási analitika az első és utolsó naplóüzenetek a blob metaadatai részeként fájlban tárolja az időbélyegeket is rögzíti. Az API a blob storage lehetővé teszi, hogy blobok keresse meg a tartománynév előtagján alapuló tároló: keresse meg a kezdődő 18:00 órára várólistára naplózási adatokat tartalmazó összes BLOB, használja az előtag "várólista/2014/07/31/1800."  

Storage Analytics pufferek üzenetek naplózása belső majd rendszeres időközönként frissíti a megfelelő blob vagy hoz létre egy újat a legújabb naplóbejegyzéseket tranzakcióköteghez. Ez csökkenti azt végezze el a blob szolgáltatás írási műveletek számát.  

Egy hasonló megoldás webkiszolgálókból a saját alkalmazásban, meg kell fontolnia és megbízhatóság (írása minden naplóbejegyzés a blob storage módon történik, akkor) és méretezhetőség (pufferelés az alkalmazás és az írás a frissítések közötti kompromisszum kezelése azokat a blob-tároló kötegekben).  

#### <a name="issues-and-considerations"></a>Problémákat és szempontok
Módjának napló adatok tárolására, vegye figyelembe a következő szempontokat:  

* Ha létrehoz egy Táblatervezés, amellyel elkerülhető a potenciális kiemelt partíciók, előfordulhat, hogy nem férhet hozzá a naplóadatok hatékonyan.  
* Egy ügyfél naplóadatokat feldolgozni, gyakran kell sok rekord betöltése.  
* Bár gyakran felépítése naplóadatokat, a blob storage egy jobb megoldás lehet.  

### <a name="implementation-considerations"></a>Megvalósítási kapcsolatos szempontok
Ez a szakasz ismerteti a szempontokat tartalmaz, amelyek a korábbi szakaszokban ismertetett mintázatokat bevezetésekor figyelembe kell vennie néhány. Ez a szakasz a legtöbb a Storage ügyféloldali kódtára (verziója 4.3.0 időpontjában írása) használó C# nyelven írt példák használja.  

### <a name="retrieving-entities"></a>Entitások beolvasása
A szakaszban bemutatott [lekérdezése tervezési](#design-for-querying), a legtöbb hatékony Ez egy pont lekérdezés. Azonban bizonyos esetekben szükség lehet lekérdezni a több entitás. Ez a szakasz ismerteti az egyes közös megközelítés beolvasása a Storage ügyféloldali kódtár segítségével.  

#### <a name="executing-a-point-query-using-the-storage-client-library"></a>A Storage ügyféloldali kódtár segítségével pont lekérdezése
Pont lekérdezés végrehajtása legegyszerűbb módja a használja a **beolvasása** művelet tábla, ahogy az az alábbi C# kódrészletet, amely lekéri az entitás egy **PartitionKey** érték "Értékesítési" és a  **RowKey** "212" érték:  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Figyelje meg, hogyan ebben a példában vár az entitás típusa lekérdezi **EmployeeEntity**.  

#### <a name="retrieving-multiple-entities-using-linq"></a>LINQ használatával több entitás beolvasásakor
A Storage ügyféloldali kódtára a LINQ használatával, és a lekérdezés megadásával több entitás le egy **ahol** záradékban. Egy táblázatbeolvasás elkerülése érdekében meg kell adnia a **PartitionKey** érték WHERE záradék, és ha lehetséges a **RowKey** érték tábla és a partíció vizsgálatok elkerülése érdekében. A table szolgáltatás támogatja a korlátozott számú összehasonlító operátorok (nagyobb, mint, nagyobb vagy egyenlő, kevesebb mint, kisebb vagy egyenlő, egyenlő, és nem egyenlő) használható a where záradékban. A következő C# kódrészletet megállapítja az alkalmazottak, amelynek utolsó neve kezdődik, "B" (feltéve, hogy a **RowKey** vezetékneve tárolja) az értékesítési részleg (feltéve, hogy a **PartitionKey** tárolja a osztály neve):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Figyelje meg, hogyan a lekérdezés határoz meg, mindkét egy **RowKey** és egy **PartitionKey** jobb teljesítmény érdekében.  

Az alábbi példakód mutatja Folyékonyan beszél API használatával egyenértékű funkciókat (További információ a Folyékonyan beszél API-k általában: [Folyékonyan beszél API tervezéséhez gyakorlati tanácsok](http://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(
    TableQuery.CombineFilters(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition(
    "PartitionKey", QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.GenerateFilterCondition(
    "RowKey", QueryComparisons.GreaterThanOrEqual, "B")
),
TableOperators.And,
TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")
    )
);
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> A minta ágyazza több **CombineFilters** , melyek a szűrési feltételek.  
> 
> 

#### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Egy lekérdezés által nagyszámú entitások beolvasása
Az optimális lekérdezési alapuló egyéni entitást adja vissza egy **PartitionKey** érték és egy **RowKey** érték. Azonban bizonyos esetekben előfordulhat, hogy ad vissza a sok entitásokat tartalmazó partícióra vagy akár több partíciók számára.  

Ilyen esetekben mindig teljes tesztelje az alkalmazás teljesítményét.  

Egy lekérdezést hajtanak a table szolgáltatás térhetnek vissza egy adott időpontban legfeljebb 1000 entitásokat, és előfordulhat, hogy öt másodpercenként legfeljebb hajtható végre. Ha az eredménykészlet 1000-nél több entitásokat tartalmaz, ha a lekérdezés nem fejeződött be 5 másodpercen belül, vagy ha a lekérdezés áthalad a partíció határán, a Table szolgáltatás a folytatási kód ahhoz, hogy az ügyfélalkalmazás igényelni az entitások következő készletét adja vissza. Hogyan folytatási jogkivonatok munkahelyi kapcsolatos további információkért lásd: [lekérdezés időkorlátja és tördelési](http://msdn.microsoft.com/library/azure/dd135718.aspx).  

A Storage ügyféloldali kódtár használ, ha azt automatikusan kezelik a folytatási jogkivonatok meg, az entitásokat ad vissza a Table szolgáltatásból. A következő C# kódminta a Storage ügyféloldali kódtár segítségével automatikusan kezeli a folytatási jogkivonatokat, ha a table szolgáltatás visszaadja azokat a válasz:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
        ...
}  
```

A következő C#-kódban explicit módon kezeli a folytatási jogkivonatokat:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;

do
{
        var employees = employeeTable.ExecuteQuerySegmented(
        employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
    ...
    }
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

Jogkivonatok segítségével képes folytatási explicit módon, megadhatja, ha az alkalmazás kéri le a következő szegmensnél az adatok. Például ha az ügyfélalkalmazást lehetővé teszi, hogy a felhasználók a táblában tárolt entitásokat között, a felhasználó dönthet nem között, az alkalmazás csak használna a folytatási kód beolvasása a Tovább gombra a lekérdezés által lekért összes entitás szegmenseket, mikor a felhasználó a jelenlegi szegmens összes entitást lapozást kellett befejeződött. Ez a megközelítés azzal számos előnnyel jár:  

* Ez lehetővé teszi, hogy korlátozza az adatok beolvasása a Table szolgáltatásból, és a hálózaton keresztül áthelyeznie.  
* Lehetővé teszi a .NET aszinkron IO végrehajtásához.  
* Ez lehetővé teszi, hogy szerializálni a folytatási kód állandó tárhelyre, így egy alkalmazás összeomlása esetén is.  

> [!NOTE]
> A folytatási kód általában 1000 entitásokat tartalmazó szegmens adja vissza, bár kevesebb lehet. Ez is így, ha a lekérdezés visszaadja a bejegyzések számának korlátozása **érvénybe** vissza az első n entitásokat, a keresési feltételeknek megfelelő: a table szolgáltatás térhetnek vissza egy szegmenst mentén rendelkező n entitások nem lépi-e a a folytatási ahhoz, hogy a többi entitások beolvasása.  
> 
> 

A következő C# kód bemutatja, hogyan lehet módosítani egy szegmens belül visszaadott entitások száma:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Kiszolgálóoldali leképezése
Egy entitás legfeljebb 255 jellemzőkkel rendelkezik, és legfeljebb 1 MB méretű lehet. Ha a tábla lekérdezése, és kérje le az entitásokat, nem feltétlenül kell a tulajdonságok, és elkerülheti a adatátvitel feleslegesen (a késés és a költségek csökkentése érdekében). Kiszolgálóoldali leképezése vihet át kell tulajdonságait. A következő példa egy lekéri csak a **E-mail** tulajdonság (valamint **PartitionKey**, **RowKey**, **időbélyeg**, és **ETag**) az a lekérdezés által kiválasztott entitások.  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
        Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

Értesítés az **RowKey** érték érhető el annak ellenére, hogy azt meg beolvasandó tulajdonságként listája nem tartalmazott.  

### <a name="modifying-entities"></a>Entitások módosítása
A Storage ügyféloldali kódtár lehetővé teszi a Beszúrás, törlésével és entitások frissítése a table szolgáltatásban tárolt entitásokat módosítását. EGTs segítségével több insert, update és delete műveletek segítségével csökkentheti a szükséges adatváltások számát kötegelt és a megoldás a teljesítmény javításához.  

Fontos megjegyezni, hogy a kivételek, amikor a Storage ügyféloldali kódtár végrehajt egy EGT általában az index az entitás, ami miatt sikertelen a kötegelt tartalmazza. Ez akkor hasznos, ha EGTs kód hibakeresés alatt.  

Azt is figyelembe kell venni, hogyan a kialakítás befolyásolja, miként kezeli az ügyfélalkalmazást a feldolgozási mód és a frissítési művelet.  

#### <a name="managing-concurrency"></a>Párhuzamossági kezelése
Alapértelmezés szerint a table szolgáltatás megvalósítja az optimista feldolgozási ellenőrzi az egyes entitásokat szintjén **beszúrása**, **egyesítése**, és **törlése** műveletek, bár ez az ügyfél számára a table szolgáltatás ellenőrzés elkerülésére lehetőség. Hogyan kezeli a table szolgáltatás a feldolgozási kapcsolatos további információkért lásd: [egyidejűségi kezelése a Microsoft Azure Storage](../storage/common/storage-concurrency.md).  

#### <a name="merge-or-replace"></a>Egyesítés vagy cseréje
A **cserélje le** metódusában a **TableOperation** mindig a felváltja a Table szolgáltatásban teljes entitásnak osztály. Ha nem adja meg a tulajdonság a kérelemben szereplő tulajdonság a tárolt entitás létezik, a kérelem tulajdonság eltávolítja a tárolt entitás. Hacsak nem szeretné tulajdonság explicit módon eltávolítása egy tárolt entitás, meg kell adnia minden tulajdonsághoz a kérelemben.  

Használhatja a **egyesítése** metódusában a **TableOperation** osztály, amely a Table szolgáltatás helyzet frissíthető entitás küldött adatmennyiség csökkentése érdekében. A **egyesítése** módszer semmilyen tulajdonságot a tárolt entitás lecseréli a kérelemben szereplő entitásból tulajdonságértékek, de érintetlenül hagyja a tárolt entitás bármelyik tulajdonságot, amelyek nem szerepelnek a kérelmet. Ez akkor hasznos, ha nagy entitások és csak a kérelem tulajdonságok kis számú frissíteni kell.  

> [!NOTE]
> A **cserélje le** és **egyesítése** módszer sem jár sikerrel, ha az entitás nem létezik. Alternatív megoldásként használhatja a **InsertOrReplace** és **InsertOrMerge** módszereket, amelyek új entitás létrehozása, ha még nem létezik.  
> 
> 

### <a name="working-with-heterogeneous-entity-types"></a>Heterogén entitástípusok használata
A Table szolgáltatás egy *séma nélküli* tábla tároló, amely azt jelenti, hogy egyetlen tábla tud tárolni a Tervező nagyfokú rugalmasságot biztosít több típusú entitásokat. Az alábbi példában látható egy tábla dolgozó és a részleg entitások tárolására:  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Időbélyeg</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Utónév</th>
<th>Vezetéknév</th>
<th>Kor</th>
<th>E-mail cím</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Utónév</th>
<th>Vezetéknév</th>
<th>Kor</th>
<th>E-mail cím</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Részleg neve</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Utónév</th>
<th>Vezetéknév</th>
<th>Kor</th>
<th>E-mail cím</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Vegye figyelembe, hogy minden egyes szervezet továbbra is rendelkeznie kell **PartitionKey**, **RowKey**, és **időbélyeg** értékeket, de rendelkezhetnek bármely tulajdonságkészletbe. Ezenkívül nincs mit entitás típusát jelzi, kivéve, ha úgy dönt, hogy valahol az információk tárolására. Az entitástípus azonosítására szolgáló két lehetőség áll rendelkezésre:  

* Az entitástípus illesztenie a **RowKey** (vagy esetleg a **PartitionKey**). Például **EMPLOYEE_000123** vagy **DEPARTMENT_SALES** , **RowKey** értékeket.  
* Egy külön tulajdonsággal entitástípus jegyezze fel az alábbi táblázatban látható módon.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Időbélyeg</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>Utónév</th>
<th>Vezetéknév</th>
<th>Kor</th>
<th>E-mail cím</th>
</tr>
<tr>
<td>Alkalmazott</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>Utónév</th>
<th>Vezetéknév</th>
<th>Kor</th>
<th>E-mail cím</th>
</tr>
<tr>
<td>Alkalmazott</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>Részleg neve</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Részleg</td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>Utónév</th>
<th>Vezetéknév</th>
<th>Kor</th>
<th>E-mail cím</th>
</tr>
<tr>
<td>Alkalmazott</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Az első a beállítást, az entitás prepending a **RowKey**, akkor hasznos, ha lehetséges, hogy két különböző típusú előfordulhat, hogy entitásnak azonos kulcs értéke. Azt is csoportokat együtt a partíció az azonos típusú entitásokat.  

Az ebben a szakaszban bemutatott eljárások gyakran a vitafórum [öröklési kapcsolatok](#inheritance-relationships) korábbi szakaszában az útmutatóban [kapcsolatok modellezésére](#modelling-relationships).  

> [!NOTE]
> Érdemes lehet például egy verziószámot ügyfél alkalmazások POCO objektumok fejlődnek, és különböző verziói együttműködve entitás típus értéke.  
> 
> 

Ez a szakasz többi néhány a Storage ügyféloldali kódtára a funkciója, amely ugyanabban a táblában a több entitás munka megkönnyítése ismerteti.  

#### <a name="retrieving-heterogeneous-entity-types"></a>Heterogén entitástípusok beolvasása
A Storage ügyféloldali kódtár használ, az entitástípusok több három beállításai lesz.  

Ha tudja, hogy az entitás egy adott tárolt típusú **RowKey** és **PartitionKey** érték található, akkor megadhatja az entitás típusa, ha a bejegyzés lekérdezésére, ahogy az előző két példán típusú entitásokat beolvasása az **EmployeeEntity**: [a Storage ügyféloldali kódtár segítségével pont lekérdezése](#executing-a-point-query-using-the-storage-client-library) és [beolvasása a LINQ használatával több entitás](#retrieving-multiple-entities-using-linq).  

A második lehetőség a **DynamicTableEntity** típusú (egy tulajdonságcsomagot) helyett egy konkrét POCO entitástípus (ezt a lehetőséget is javíthatja a teljesítményt, mert nincs szükség szerializálása és deszerializálása .NET típusú entitás). A következő C#-kódban potenciálisan eltérő típusú entitásokat táblából, de mint minden entitásokat ad vissza **DynamicTableEntity** példányok. Ezután a **EntityType** tulajdonság minden entitás típusának meghatározására:  

```csharp
string filter = TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("PartitionKey",
    QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("RowKey",
                    QueryComparisons.GreaterThanOrEqual, "B"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey",
        QueryComparisons.LessThan, "F")
    )
);
TableQuery<DynamicTableEntity> entityQuery =
    new TableQuery<DynamicTableEntity>().Where(filter);
var employees = employeeTable.ExecuteQuery(entityQuery);

IEnumerable<DynamicTableEntity> entities = employeeTable.ExecuteQuery(entityQuery);
foreach (var e in entities)
{
EntityProperty entityTypeProperty;
if (e.Properties.TryGetValue("EntityType", out entityTypeProperty))
{
    if (entityTypeProperty.StringValue == "Employee")
    {
        // Use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

Vegye figyelembe, hogy beolvasni más tulajdonságokat kell használnia a **TryGetValue** metódust a **tulajdonságok** tulajdonsága a **DynamicTableEntity** osztály.  

Egy harmadik lehetőség egy kombinálhatja a használatával a **DynamicTableEntity** típust és egy **EntityResolver** példány. Ez lehetővé teszi, hogy oldja fel ugyanabban a lekérdezésben többféle POCO. Ebben a példában a **EntityResolver** delegált használ a **EntityType** tulajdonság használatával tudja megkülönböztetni a két típusú entitás, amely a lekérdezés visszaadja. A **megoldásához** módszert használ a **feloldó** megoldásához delegált **DynamicTableEntity** a példányok **TableEntity** példányok.  

```csharp
EntityResolver<TableEntity> resolver = (pk, rk, ts, props, etag) =>
{

        TableEntity resolvedEntity = null;
        if (props["EntityType"].StringValue == "Department")
        {
        resolvedEntity = new DepartmentEntity();
        }
        else if (props["EntityType"].StringValue == "Employee")
        {
        resolvedEntity = new EmployeeEntity();
        }
        else throw new ArgumentException("Unrecognized entity", "props");

        resolvedEntity.PartitionKey = pk;
        resolvedEntity.RowKey = rk;
        resolvedEntity.Timestamp = ts;
        resolvedEntity.ETag = etag;
        resolvedEntity.ReadEntity(props, null);
        return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<DynamicTableEntity> entityQuery =
        new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
        if (e is DepartmentEntity)
        {
    ...
        }
        if (e is EmployeeEntity)
        {
    ...
        }
}  
```

#### <a name="modifying-heterogeneous-entity-types"></a>Heterogén entitástípusok módosítása
Nem kell tudni, hogy milyen típusú entitás törli-e, és mindig ismeri a jogi, amikor a meghajtóba. Használhat azonban **DynamicTableEntity** típus frissíthető entitás, anélkül, hogy tudnák típusára és egy POCO entitásosztályt használata nélkül. A következő példakód egyetlen entitás kéri le, és ellenőrzi a **EmployeeCount** tulajdonság létezik-e frissítése előtt.  

```csharp
TableResult result =
        employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;

if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
        throw new
        InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}
countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));  
```

### <a name="controlling-access-with-shared-access-signatures"></a>Megosztott hozzáférési aláírásokkal-hozzáférés szabályozásáról
Közös hozzáférésű Jogosultságkód (SAS) jogkivonatok segítségével engedélyezheti az ügyfélalkalmazások (és módosításához lekérdezése) táblaentitásokat közvetlenül nem közvetlenül a table szolgáltatás hitelesítéshez szükséges. Nincsenek általában három fő előnyei a SAS használatával az alkalmazásban:  

* Nem kell terjeszteni a tárfiók kulcsára egy nem biztonságos platformon (például egy mobileszköz) annak érdekében, hogy adott eszköz eléréséhez, és módosítsa a Table szolgáltatás szerepelnek.  
* Kiürítési egy része az, hogy a webes és feldolgozói szerepkörök hajtsa végre az entitások ügyfél eszközök, például a végfelhasználói számítógépek és mobileszközök kezeléséhez.  
* Hozzárendelhet egy korlátozott, és idő korlátozott engedélyekkel (például a csak olvasási hozzáféréssel egy adott erőforráshoz való engedélyezése) ügyfél számára.  

A Table szolgáltatás SAS-tokenje használatával kapcsolatban további információkért lásd: [használatával megosztott hozzáférési aláírásokkal (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).  

Azonban továbbra is kell létrehoznia, amely adja meg a table szolgáltatás entitást ügyfélalkalmazás SAS-tokenje: akkor tegye ezt egy olyan környezetben, a tárfiókok kulcsait biztonságos hozzáféréssel rendelkezik. Egy webes vagy feldolgozói szerepkör általában a rendszer az SAS-jogkivonatokat hoz létre, és letöltheti az ügyfélalkalmazások számára az entitások hozzáférést igénylő használják. Mivel nincs még egy terhelés létrehozásakor, és kézbesíti SAS-tokenje az ügyfelek számára, hogyan lehet a legjobban érdemes lehet csökkenteni a terhelést, különösen a nagy mennyiségű forgatókönyvek részt.  

Úgy is, amely hozzáférést biztosít egy tábla az entitások egy részét a SAS-token létrehozásához. Alapértelmezés szerint egy SAS-jogkivonat teljes táblát hoz létre, de akkor is meg, hogy a SAS-jogkivonat való hozzáférés engedélyezése vagy számos **PartitionKey** értékeket, vagy számos **PartitionKey** és **RowKey** értékeket. Akkor célszerű használni, úgy, hogy minden felhasználó SAS-jogkivonat csak lehetővé teszi őket elérését a saját részére a table szolgáltatás egyes felhasználókat a rendszer az SAS-tokenje létrehozásához.  

### <a name="asynchronous-and-parallel-operations"></a>Aszinkron és párhuzamos műveletek
A kérelmek több partíciót között vannak oszlik, feltéve aszinkron vagy párhuzamos lekérdezések használatával is javítható az átviteli sebesség és az ügyfél válaszképességét.
Például lehetséges, hogy két vagy több szerepkör feldolgozópéldányok párhuzamosan a táblák elérése. Adott partíciók-készletek felelős az egyes feldolgozói szerepkörök rendelkezik, vagy egyszerűen több feldolgozói szerepkör példánya lehet, minden egyes érhessék el az egy tábla minden egyes partícióra.  

Belül egy ügyfél példány aszinkron módon tárolási műveletek végrehajtásával javíthatja a teljesítményt. A Storage ügyféloldali kódtár egyszerűen aszinkron lekérdezések és módosításokat írni. Például előfordulhat, hogy a kiindulási pont a szinkron módszer, amely lekéri az alábbi C#-kódban látható módon egy partíció összes entitásának:  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

        TableContinuationToken continuationToken = null;

        do
        {
        var employees = employeeTable.ExecuteQuerySegmented(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
    {
        ...
    }
        continuationToken = employees.ContinuationToken;
        } while (continuationToken != null);
}  
```

Könnyen módosíthatja ezt a kódot, hogy a lekérdezés aszinkron módon az alábbiak szerint:  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);
        TableContinuationToken continuationToken = null;

        do
        {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            ...
        }
        continuationToken = employees.ContinuationToken;
            } while (continuationToken != null);
}  
```

A aszinkron példában láthatja a szinkron verziójáról az alábbi módosításokat:  

* A metódus-aláírás mostantól tartalmazza a **aszinkron** módosító és értéket ad vissza egy **feladat** példány.  
* Hívása helyett a **ExecuteSegmented** metódusának segítéségével lekérheti az eredményeket, most metódus meghívja a **ExecuteSegmentedAsync** metódus, és használja a **await** módosító eredmények aszinkron beolvasásához.  

Az ügyfélalkalmazás a metódus hívása többször (a különböző értékekkel a **részleg** paraméter), és minden egyes lekérdezés külön szálban futtatandó.  

Vegye figyelembe, hogy van-e nem aszinkron verziója a **Execute** metódust a **TableQuery** osztálynál, mert a **IEnumerable** illesztőfelület nem támogatja a aszinkron enumerálása.  

Helyezze, frissítése, és aszinkron módon entitások törlésére. Az alábbi C# példában egy egyszerű, a szinkron módszer szúrható be, illetve alkalmazott entitás cseréje:  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Könnyen módosíthatja ezt a kódot, hogy a frissítés aszinkron módon az alábbiak szerint:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = await employeeTable
        .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

A aszinkron példában láthatja a szinkron verziójáról az alábbi módosításokat:  

* A metódus-aláírás mostantól tartalmazza a **aszinkron** módosító és értéket ad vissza egy **feladat** példány.  
* Hívása helyett a **Execute** metódushívások az entitás-, a metódus most frissíteni a **ExecuteAsync** metódus, és használja a **await** módosító eredmények beolvasásához aszinkron módon.  

Az ügyfélalkalmazás ehhez hasonló több aszinkron metódus meghívása, és minden metódushívás külön szálban futtatandó.  

### <a name="credits"></a>Kreditek
Örömmel vesszük az Azure-csapat a hozzájárulásuk következő tagjai szeretnénk: Dominic Betts, Jason Hogg, Jean Ghanem, Jai Haridas, Jeff Irwin, Vamshidhar Kommineni, Vinay Shah Serdar Ozler, valamint a Microsoft DX Tom Hollander. 

Azt is szeretne felülvizsgálati ciklus során a következő Microsoft MVP meg az értékes visszajelzés Köszönjük: Igor Papirov és Edward Bakker.

[1]: ./media/storage-table-design-guide/storage-table-design-IMAGE01.png
[2]: ./media/storage-table-design-guide/storage-table-design-IMAGE02.png
[3]: ./media/storage-table-design-guide/storage-table-design-IMAGE03.png
[4]: ./media/storage-table-design-guide/storage-table-design-IMAGE04.png
[5]: ./media/storage-table-design-guide/storage-table-design-IMAGE05.png
[6]: ./media/storage-table-design-guide/storage-table-design-IMAGE06.png
[7]: ./media/storage-table-design-guide/storage-table-design-IMAGE07.png
[8]: ./media/storage-table-design-guide/storage-table-design-IMAGE08.png
[9]: ./media/storage-table-design-guide/storage-table-design-IMAGE09.png
[10]: ./media/storage-table-design-guide/storage-table-design-IMAGE10.png
[11]: ./media/storage-table-design-guide/storage-table-design-IMAGE11.png
[12]: ./media/storage-table-design-guide/storage-table-design-IMAGE12.png
[13]: ./media/storage-table-design-guide/storage-table-design-IMAGE13.png
[14]: ./media/storage-table-design-guide/storage-table-design-IMAGE14.png
[15]: ./media/storage-table-design-guide/storage-table-design-IMAGE15.png
[16]: ./media/storage-table-design-guide/storage-table-design-IMAGE16.png
[17]: ./media/storage-table-design-guide/storage-table-design-IMAGE17.png
[18]: ./media/storage-table-design-guide/storage-table-design-IMAGE18.png
[19]: ./media/storage-table-design-guide/storage-table-design-IMAGE19.png
[20]: ./media/storage-table-design-guide/storage-table-design-IMAGE20.png
[21]: ./media/storage-table-design-guide/storage-table-design-IMAGE21.png
[22]: ./media/storage-table-design-guide/storage-table-design-IMAGE22.png
[23]: ./media/storage-table-design-guide/storage-table-design-IMAGE23.png
[24]: ./media/storage-table-design-guide/storage-table-design-IMAGE24.png
[25]: ./media/storage-table-design-guide/storage-table-design-IMAGE25.png
[26]: ./media/storage-table-design-guide/storage-table-design-IMAGE26.png
[27]: ./media/storage-table-design-guide/storage-table-design-IMAGE27.png
[28]: ./media/storage-table-design-guide/storage-table-design-IMAGE28.png
[29]: ./media/storage-table-design-guide/storage-table-design-IMAGE29.png

