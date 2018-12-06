---
title: Gyakori alkalmazási helyzetek és a forgatókönyvek az Azure Cosmos DB
description: 'További tudnivalók az első öt használati esetek, az Azure Cosmos DB: felhasználók létrehozott tartalmat, az eseménynaplózás, eseménykatalógus-adatok, felhasználói beállítások adatok és eszközök internetes hálózata (IoT).'
services: cosmos-db
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/07/2017
ms.openlocfilehash: 400ce3ddb2a01db32faa7d3303c89a76c2c4649a
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52960030"
---
# <a name="common-azure-cosmos-db-use-cases"></a>Azure Cosmos DB gyakori alkalmazási helyzetek
Ez a cikk számos gyakori alkalmazási helyzetek áttekintést nyújt az Azure Cosmos DB.  Ebben a cikkben szereplő ajánlások szolgálhat a kiindulási pontként, a Cosmos DB az alkalmazás fejlesztését.   

Ez a cikk elolvasása után is elérheti az alábbi kérdések megválaszolásához: 

* Mik az Azure Cosmos DB gyakori alkalmazási esetei?
* Mik a kereskedelmi alkalmazások Azure Cosmos DB használatának előnyeit?
* Milyen előnyökkel jár az Azure Cosmos DB adattárként Internet of Things (IoT) rendszerek?
* Mik a webes és mobilalkalmazások az Azure Cosmos DB használatának előnyeit?

## <a name="introduction"></a>Bevezetés
[Az Azure Cosmos DB](../cosmos-db/introduction.md) a Microsoft globálisan elosztott adatbázis-szolgáltatása. A szolgáltatás célja, hogy az ügyfelek számára, hogy rugalmasan (és egymástól függetlenül) átviteli sebesség és tárterület tetszőleges számú földrajzi régió között. Az Azure Cosmos DB, az első globálisan elosztott adatbázis-szolgáltatás a piacon elérhető még ma, hogy kínál átfogó [szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/cosmos-db/) mely magában foglalja az átviteli sebesség, a késés, a rendelkezésre állás és a konzisztencia. 

Az Azure Cosmos DB egy globális elosztott, többmodelles adatbázis, amely az alkalmazások és az alkalmazási helyzetek széles van használatban. A jó választás [kiszolgáló nélküli](http://azure.com/serverless) alkalmazás, amely sorrendben az ezredmásodperc válaszidőt kell, és gyors és globálisan kell. Több adatmodellt is támogat, (kulcs-érték, dokumentum, diagram vagy oszlopos) és több API-t, az adatok eléréséhez, többek között [MongoDB API-val](mongodb-introduction.md), [SQL API](documentdb-introduction.md), [Gremlin API](graph-introduction.md), és [ Táblák API](table-introduction.md) natív módon, és a egy bővíthető módon. 

Az alábbiakban néhány attribútum az Azure Cosmos DB, győződjön meg arról, hogy kiválóan alkalmas nagy teljesítményű alkalmazások globális léptéke.

* Az Azure Cosmos DB natív módon particionálja az adatait, a magas rendelkezésre állás és méretezhetőség érdekében. Az Azure Cosmos DB 99,99 %-os rendelkezésre állási, átviteli sebesség, alacsony késleltetésű és konzisztenciát minden egyrégiós és többrégiós összes fiókok Könnyített konzisztencia garanciát kínál, és 99,999 %-os olvasási rendelkezésre állás minden többrégiós adatbázisfiókhoz.
* Az Azure Cosmos DB közel valós idejű sorrend-az-ezredmásodperces válaszidők SSD-alapú tárolóval rendelkezik.
* Például a végleges, konzisztens előtag, munkamenet és kötött elavulás konzisztenciaszintek támogatása az Azure Cosmos DB teljes rugalmasságra és alacsony költség-teljesítmény arányt tesz lehetővé. Nincs adatbázis-szolgáltatás, az Azure Cosmos DB lehető rugalmasságot nyújt, a szintek konzisztencia. 
* Az Azure Cosmos DB rugalmas adatok mobilbarát díjszabási modell, amelyek egymástól függetlenül mérőszámai dokumentumtárolási és adattovábbítási kapacitással rendelkezik.
* Az Azure Cosmos DB szolgáltatás számára fenntartott átviteli sebesség modell olvasási/írási műveleteket CPU/memória/iops-értéke az alapul szolgáló hardver helyett száma alkalmazásteljesítményre teszi lehetővé.
* Az Azure Cosmos DB-kialakítás lehetővé teszi nagy kérelem kötetek sorrendjében kérelem / nap billiónyi horizontális.

Ezek az attribútumok előnyösek, webes, mobil-, játék és IoT-alkalmazások, amelyek válaszidőt kell, és nagy mennyiségű olvasási és írási kezeléséhez szükséges.

## <a name="iot-and-telematics"></a>IoT és telematika
IoT használatieset-forgatókönyveit általában néhány mintákat hogyan azok videorögzítést, folyamat, megoszthatja és adatok tárolására.  Ezek a rendszerek elsőként kell adatlöketekkel különböző területi beállításokat eszköz érzékelőktől származó adatok betöltését. Ezután ezek a rendszerek adatainak feldolgozására és elemzésére streamelési a valós idejű elemzéseket. Az adatok ezután a batch-elemzés a ritka elérésű tárolási archiválja a rendszer. A Microsoft Azure biztosít, amelyek alkalmazhatók az IoT sokoldalú szolgáltatásai többek között az Azure Cosmos DB, az Azure Event Hubs, az Azure Stream Analytics, Azure Notification Hub, az Azure Machine Learning, Azure HDInsight és a Power bi használja. 

![Az Azure Cosmos DB IoT-referenciaarchitektúra](./media/use-cases/iot.png)

Az adatok adatlöketekkel olvasódnak által az Azure Event Hubs módon, nagy átviteli sebességű adatbetöltést biztosít alacsony késéssel. A funneled betöltött adatokért, fel kell dolgozni a valós idejű betekintés az Azure Stream Analytics a valós idejű elemzési. Adatokat az ad hoc ad hoc tölthetők az Azure Cosmos DB-be. Az Azure Cosmos DB-be az adatok betöltése után lehet lekérdezni, készen áll az adatok. Emellett új adatokat és a meglévő adatok módosításait elolvashatja a módosítási hírcsatorna. Módosítási hírcsatorna egy állandó, a csak a egymást követő sorrendben tárolja a Cosmos DB-tárolók módosításait naplózási hozzáfűzése. Az összes adat vagy csak az Azure Cosmos DB adatok módosításait a valós idejű elemzési részeként referenciaadatok is használható. Emellett adatok is tovább finomítsák és feldolgozása történhet HDInsight Pig, Hive vagy Map/Reduce-feladatok az Azure Cosmos DB-adatai csatlakozik.  Finomított vissza az Azure Cosmos DB majd betölteni a jelentéskészítéshez.   

Egy mintául szolgáló IoT-megoldás az Azure Cosmos DB, EventHubs és a Storm, lásd: a [hdinsight-storm-példák tárházban a Githubon](https://github.com/hdinsight/hdinsight-storm-examples/).

Az Azure IoT-ajánlatok további információkért lásd: [létrehozása a saját IOT-](https://www.microsoft.com/en-us/internet-of-things). 

## <a name="retail-and-marketing"></a>Kiskereskedelem és marketing
Az Azure Cosmos DB a Microsoft saját e-kereskedelmi platformok, futtassa a Windows Store és az XBox Live, széles körben használatos. Azt is használatban van a kiskereskedelmi iparági eseménykatalógus-adatok tárolására és az események forráskezelése minta alkalmazása feldolgozási folyamatok létrehozása ahhoz.

Katalógus adatok felhasználási magában foglalja a tároló és lekérdező attribútumok az entitások, például a személyek, helyek és termékek. Néhány példa a catalog data olyan felhasználói fiókokat, termékkatalógusok, IoT-eszköznyilvántartások és számlázási anyagok rendszerek. Ezen adatok attribútumok eltérőek lehetnek, és idővel az alkalmazás követelményeinek megfelelően.

Érdemes lehet például egy termékkatalógust egy autós részek szállítói számára. Előfordulhat, hogy minden részére a saját attribútumai mellett az általános attribútumokkal rendelkeznek, amelyek minden részére. Emellett egy adott részére attribútumok módosíthatja a következő évre, amikor új modellt jelent meg. Az Azure Cosmos DB támogatja a rugalmas sémák és a hierarchikus adatokkal dolgozik, és így jól használhatók a termék eseménykatalógus-adatok tárolására.

![Az Azure Cosmos DB kiskereskedelmi katalógus referenciaarchitektúra](./media/use-cases/product-catalog.png)

Az Azure Cosmos DB gyakran használják az event sourcing power eseményvezérelt architektúrák használatával, a [módosításcsatornáját](change-feed.md) funkciókat. A módosítási hírcsatorna teszi lehetővé alsóbb rétegbeli mikroszolgáltatások megbízható és Növekményesen olvasási beszúrások és a egy Azure Cosmos DB-hez készült frissítéseket (például rendelés események). Ez a funkció is javítható, ha egy állandó tárolóba üzenetközvetítőként állapot módosítása eseményekhez és a meghajtó munkafolyamatban számos mikroszolgáltatásból között adja meg (amely is megvalósíthatók [kiszolgáló nélküli Azure Functions](http://azure.com/serverless)).

![Az Azure Cosmos DB rendezése a folyamat a referencia-architektúra](./media/use-cases/event-sourcing.png)

Emellett az Azure Cosmos DB-ben tárolt adatok integrálható a HDInsight a big data elemzés, Apache Spark-feladatok keresztül. További információ az Azure Cosmos DB-hez készült Spark-összekötő: [a Cosmos DB és a HDInsight Spark-feladatok futtatása](spark-connector.md).

## <a name="gaming"></a>Játékok
Az adatbázisszint játékalkalmazások felhőtermékekkel. A modern játékok grafikus feldolgozási mobile/konzol ügyfeleken, de a felhőt, például játékon belüli, a közösségimédia-integráció és a ranglistákat testre szabott és személyre szabott tartalmat továbbít. Játékok gyakran szükséges ezredmásodperces olvasási és írási műveletek egy vonzó biztosítanak a játékbeli felhasználói élményt. A játék-adatbázist kell gyors és kérelemarányok jelentkező hatalmas terheléseket kezelje az új játékok indul, és a funkciófrissítéseket.

Az Azure Cosmos DB például a játékok által használt [a the Walking Dead: No Man's Land](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) által [Next Games](http://www.nextgames.com/), és [a Halo 5: Őrei](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Az Azure Cosmos DB a játék fejlesztők számára a következő előnyöket nyújtja:

* Az Azure Cosmos DB lehetővé teszi, hogy a teljesítmény skálázását felfelé vagy lefelé rugalmasan. Ez lehetővé teszi a frissítési profil és -statisztikák a több tucat egyidejű játékélményt nyújthat millió kezelése azáltal, hogy egyetlen API hívással játékokat.
* Az Azure Cosmos DB ezredmásodperces olvasási támogatja, és írja a játék során bármilyen késedelmes jelentések elkerülése érdekében.
* Az Automatikus indexelés az Azure Cosmos DB lehetővé teszi, hogy több különböző tulajdonságok valós idejű szűrése, keresse meg például a játékosok a belső Windows Media Player azonosítók vagy azok GameCenter, Facebook, Google-azonosítók vagy lekérdezés egy guild player csoporttagság alapján. Ez akkor lehetséges, összetett indexelő, akár a horizontális skálázás infrastruktúra nélkül.
* Közösségi funkciókat, beleértve a játék csevegőüzeneteket, player guild csoporttagságok, kihívások befejeződött, ranglistákat és közösségi diagramok használata egyszerűbb, egy rugalmas séma valósíthatók meg.
* Az Azure Cosmos DB, egy felügyelt platform--szolgáltatásként (PaaS) szükséges minimális beállítása és kezelése lehetővé teszik a gyors ismétlését és és, és csökkenteni a piacra kerülési időt.

![Az Azure Cosmos DB-játék referenciaarchitektúra](./media/use-cases/gaming.png)

## <a name="web-and-mobile-applications"></a>Webes és mobilalkalmazások
Az Azure Cosmos DB webes és mobil alkalmazások gyakran használják, és mindez közösségi interakciók modellezési integrálása a külső szolgáltatásokkal, és részletes, személyre szabott élmény létrehozásához. A Cosmos DB SDK lehet használt build gazdag iOS és Android-alkalmazások használatával a népszerű [Xamarin keretrendszer](mobile-apps-with-xamarin.md).  

### <a name="social-applications"></a>Közösségi alkalmazásokat
Egy gyakori alkalmazási helyzet, az Azure Cosmos DB tárolja, és lekérdezi a felhasználó által készített tartalom (UGC), a webes, mobil- és a közösségi média alkalmazás. Néhány példa a UGC, valamint csevegési, Twitter-üzeneteket, blogbejegyzések, értékeléseit és megjegyzéseit. Gyakran előfordul a közösségimédia-alkalmazásaikat a UGC sebesség szabad formátumú szöveg, a tulajdonságai, a címkék és a kapcsolatokat, amelyek nem korlátozódnak merev struktúra kombinációja. Tartalma csevegőszobák például, megjegyzések és a hozzászólások tárolhatók a Cosmos DB átalakítások vagy összetett objektum, objektumrelációs leképezéseket rétegek nélkül.  Adattulajdonságok hozzáadhatja, vagy egyszerűen megfelelően módosítani, követelmények, a fejlesztők ciklustevékenység az alkalmazás kódjának így támogatása az alkalmazások gyors fejlesztéséhez.  

Az alkalmazásokat, amelyek külső közösségi hálózatokkal való integráció válaszolnia kell a sémák korlátlanról ezeket a hálózatokat. Cosmos DB-ben alapértelmezés szerint automatikusan indexelt adatokat, mert adatok készen áll a tetszőleges időpontban kérdezhető le. Ezeket az alkalmazásokat, így rugalmasan saját igényeiknek megfelelően leképezések lekéréséhez.

Számos közösségi alkalmazás globális méretekben futtassa, és előre nem látható használati minták is lehetnek. Rugalmas méretezés az adattár elengedhetetlen, az alkalmazási rétegre megfelelő használati igény szerint méretezhető.  Cosmos DB-fiókja alatt adatkereten felül a partíciók hozzáadásával horizontálisan be.  Több régióban emellett további Cosmos DB-fiókot is létrehozhat. Cosmos DB szolgáltatás régiók rendelkezésre állása, lásd: [Azure-régiók](https://azure.microsoft.com/regions/#services).

![Az Azure Cosmos DB webes alkalmazás referenciaarchitektúra](./media/use-cases/apps-with-global-reach.png)

### <a name="personalization"></a>Személyre szabás
Napjainkban a modern alkalmazások és az összetett nézetek kapható. Ezek a általában dinamikus, élelmiszer-felhasználói beállítások vagy hangulatokra, és a márkakezelés igényeinek megfelelően. Ezért alkalmazásokat kell tudnia lekérdezni a személyre szabott beállítások vizualizációinak felhasználói felületi elemeket és a megoldások gyors, hatékony. 

JSON-t, Cosmos DB által támogatott formátum egy hatékony formátuma felhasználói felület elrendezés adatokat jelöl, nem csak egyszerű, de is könnyen értelmezhetők a JavaScript által. A cosmos DB kínál, amelyek lehetővé teszik a gyors olvasást a közel valós idejű írások aprólékosan beállítható konzisztenciaszintek. Ezért többek között a JSON-dokumentumok formájában személyre szabott beállításokat a Cosmos dB-ben a felhasználói felület elrendezés adatok tárolására egy hatékony azt jelenti, hogy az adatok beolvasása a hálózaton keresztül.

![Az Azure Cosmos DB webes alkalmazás referenciaarchitektúra](./media/use-cases/personalization.png)

## <a name="next-steps"></a>További lépések
Ismerkedés az Azure Cosmos DB használatával, kövesse a [rövid útmutatók](create-sql-api-dotnet.md), amelyek végigvezetik egy fiókot és a Cosmos DB – első lépések. 

Vagy, ha többet szeretne azokról az ügyfelekről a Cosmos DB használatával, a következő ügyfelek beszámolói érhetők el:

* [Jet.com adatfeldolgozási főigazgatója](https://jet.com). E-kereskedelmi úttörő élre a felső helyszíni eyes, a Microsoft cloud futtat, kihasználja a Cosmos DB globális méretű.
* [Asos.com](http://www.asos.com/). Asos.com egy brit online módon és hatalmas áruházban. Célja a fiatal felnőttek, Asos Mit árul, több mint 850 márkákat, valamint a saját tartományán ruházat és kellékek.
* [Toyota](https://www.toyota.com/). Toyota Motor Corporation japán autógyártás gyártó. Toyota Cosmos DB globális IoT-alkalmazás használja.
* [A Citrix](https://customers.microsoft.com/story/citrix). A Citrix fejlesztett single-sign-on megoldás az Azure Service Fabric és Azure Cosmos DB használatával
* [TEXA](https://customers.microsoft.com/story/texaspa) TEXA a Forradalmi IoT-megoldás jármű-tulajdonosok segít időt, költséget takaríthat meg, gáz mentés – és esetleg helyén.
* [A Domino Kétpizzás](https://www.dominos.com). A Domino Kétpizzás Inc.-amerikai kétpizzás étteremlánc.
* [Azt szabályozza, Johnson](http://www.johnsoncontrols.com). Johnson vezérlők egy globális változatos technológia és az ügyfelek már több mint 150 országban számos szolgáltató több ipari vezető.
* [A Microsoft Windows, univerzális Store, az Azure IoT Hub, az Xbox Live és más szolgáltatások](https://azure.microsoft.com/blog/how-azure-documentdb-planet-scale-nosql-helps-run-microsoft-s-own-businesses/). A Microsoft hogyan hoz létre az Azure Cosmos DB használatával nagy mértékben méretezhető szolgáltatásokkal.
* [A Microsoft Data- és elemzési csoport](https://customers.microsoft.com/story/microsoftdataandanalytics). A Microsoft Data- és elemzési csoport éri el az Azure Cosmos DB globális big-data gyűjtemény
* [Sulekha.com](https://customers.microsoft.com/story/sulekha-uses-azure-documentdb-to-connect-customers-and-businesses-across-india). Sulekha az Azure Cosmos DB ügyfeleket és a cégeket összekapcsolásához India.
* [NewOrbit](https://customers.microsoft.com/story/neworbit-takes-flight-with-azure-documentdb). NewOrbit az Azure Cosmos DB repülési vesz igénybe.
* [Az Affinio](https://customers.microsoft.com/doclink/affinio-switches-from-aws-to-azure-documentdb-to-harness-social-data-at-scale). Az affinio az AWS az Azure Cosmos DB, nagy mennyiségű közösségi adatot.
* [Next Games](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/). A the Walking Dead: No Man's Land című játék soars az Azure Cosmos DB által támogatott #1-re.
* [A Halo](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Hogyan Halo 5 miként valósította meg a Azure Cosmos DB használatával közösségi játékfunkciókat.
* [Cortana Analytics katalógusban](https://azure.microsoft.com/blog/cortana-analytics-gallery-a-scalable-community-site-built-on-azure-documentdb/). Cortana Analytics katalógusban – egy Azure Cosmos DB épülő méretezhető közösségi webhelyére.
* [Pofonegyszerű](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602). Rendszerintegrátor vezető betekintést enged multinacionális cégek globális percek alatt, a rugalmas felhőalapú technológiák terén.
* [Hírek Köztársaság](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639). Intelligencia hozzáadása a hírek tájékoztatási céllal szerepet játszanak állampolgár számára. 
* [Az SGS International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653). Egységes színe szerte a világon jelentős márkái SGS kapcsolja be. És az SGS bekapcsolja az Azure-bA.
* [A Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608). Globális vezető Telenor a felhőben használja a tempójával mozog. 
* [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667). A tároló a jövő gyors keresés és az adatok egyszerű folyamatot futtat.
* [Nucleo](https://customers.microsoft.com/story/azure-based-software-platform-breaks-down-barriers-bet). Azure-alapú szoftver platform felszámolja korlátozást cégeknek és ügyfelei között
* [Weka](https://customers.microsoft.com/story/weka-smart-fridge-improves-vaccine-management-so-more-people-can-be-protected-against-diseases). Weka Smart-Fridge javítja a vakcinák kezelési, így több ember is védhetők betegségek ellen
* [Narancssárga Tribes](https://customers.microsoft.com/story/theres-more-to-that-food-app-than-meets-the-eye-or-the-mouth). Nincs több élelmiszer alkalmazáshoz a szemet, vagy a szájához beszéd megfelel-e, mint.
* [A Real Madrid](https://customers.microsoft.com/story/real-madrid-brings-the-stadium-closer-to-450-million-f). A Real Madrid világszerte, a Microsoft Cloud használatával 450 millió szurkolóhoz viszi közelebb a stadiont biztosít.
* [Tuku](https://customers.microsoft.com/story/tuku-makes-car-buying-fun-with-help-from-azure-services). TUKU lehetővé teszi az autó szórakoztató segítségével Azure-szolgáltatások vásárlása
