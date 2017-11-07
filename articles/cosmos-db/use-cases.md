---
title: "Gyakori alkalmazási esetei és Azure Cosmos DB forgatókönyvek |} Microsoft Docs"
description: "További tudnivalók az első öt használati esetekben az Azure Cosmos DB: felhasználók létrehozott tartalom, eseménynaplózás, a katalógus adatainak, felhasználói beállítások adatok és az eszközök internetes hálózatát (IoT)."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: eca68a58-1a8c-4851-8cf8-6e4d2b889905
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: mimig
ms.openlocfilehash: 68800ab14cbca367fd97827a923a2edc377e9aee
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="common-azure-cosmos-db-use-cases"></a>Azure Cosmos DB gyakori alkalmazási esetei
Ez a cikk számos gyakori alkalmazási esetei áttekintést nyújt az Azure Cosmos DB rendszerhez.  Az ebben a cikkben szereplő ajánlásokhoz szolgál, kiindulási pontként a Cosmos DB alkalmazást fejleszt.   

A cikk elolvasása után képes lesz a következő kérdések megválaszolásához: 

* Mik azok a gyakori alkalmazási esetei Azure Cosmos DB?
* Mik a kiskereskedelmi alkalmazások Azure Cosmos DB használatának előnyeit?
* Mik az előnyei Azure Cosmos DB adattárként az eszközök internetes hálózatát (IoT) rendszerek?
* Mik a webes és mobilalkalmazások Azure Cosmos DB használatának előnyeit?

## <a name="introduction"></a>Bevezetés
[Az Azure Cosmos DB](../cosmos-db/introduction.md) globálisan elosztott adatbázis a Microsoft-szolgáltatás. A szolgáltatás célja, hogy lehetővé teszi az ügyfeleknek rugalmasan (és egymástól függetlenül) méretezési átviteli sebesség és tárterület tetszőleges számú földrajzi régiók között. Azure Cosmos DB az első globálisan elosztott adatbázis-szolgáltatás a piacon ma kínálta átfogó [szolgáltatási szintek](https://azure.microsoft.com/support/legal/sla/cosmos-db/) átviteli, a késés, a rendelkezésre állási és a konzisztencia magába. 

Az Azure Cosmos DB projekt indítja el a 2011 "Projekt Firenzében" cím fejlesztői problémás-pontokra kihívással szembesülnek nagy méretű Internet alkalmazások Microsoft vállalaton belül. Tartják be, hogy ezek a problémák a Microsoft-alkalmazások nem egyediek, azt úgy döntött, hogy elérhetővé Azure Cosmos DB általában külső fejlesztők formájában 2015 [Azure DocumentDB](https://azure.microsoft.com/blog/documentdb-moving-to-general-availability/). A szolgáltatás a belső használatú ubiquitously a Microsofton belül, és a leggyorsabban szolgáltatások külsőleg Azure fejlesztők által használt egyik. 

Azure Cosmos-adatbázis egy olyan globális elosztott, több modellre adatbázis, az alkalmazások és a használati esetek széles használt. Akkor érdemes használni az összes [kiszolgáló nélküli](http://azure.com/serverless) alkalmazás, amely kis sorrendben az ezredmásodperces válaszidők kell, és gyorsan és globálisan méretezhető kell. Támogatja a több adatmodellekben (kulcs-érték, a dokumentumok, a diagramok és oszlopos) és az adatok sok API-k eléréséhez, beleértve [MongoDB API](mongodb-introduction.md), [DocumentDB API (SQL)](documentdb-introduction.md), [Graph API-val (Gremlin)](graph-introduction.md), és [táblák API](table-introduction.md) natív módon, és a bővíthető módon. 

Az alábbiakban néhány attribútumok, amelyek nagy teljesítményű alkalmazások, a globális léptéke jól alkalmazható Azure Cosmos-adatbázis.

* Azure Cosmos-adatbázis natív módon felosztja a magas rendelkezésre állás és méretezhetőség adatait. Azure Cosmos DB 99,99 % garanciák rendelkezésre állási, átviteli sebességet, alacsony késéssel és konzisztencia nyújtja.
* Azure Cosmos-adatbázis biztonsági SSD-tároló, a kis késleltetésű sorrendben az ezredmásodperces válaszidők rendelkezik.
* Például végleges, egységes előtag, munkamenet és kötött elavulás konzisztenciaszintek Azure Cosmos DB tartozó támogatása lehetővé teszi, hogy teljesen rugalmasan és alacsony költség-teljesítmény aránya. Nincs adatbázis-szolgáltatás, Azure Cosmos DB mértékű rugalmasságot nyújt, szintek egységességét. 
* Azure Cosmos DB rugalmas adatok mobilbarát árképzési modellt, amely mérőszámok tárolási és átviteli egymástól függetlenül rendelkezik.
* Azure Cosmos-adatbázis fenntartott átviteli sebességet modell lehetővé teszi gondolja, hogy olvasása/írása az alapul szolgáló hardverben Processzor/memória/IOPs helyett számát.
* Azure Cosmos DB tervezési lehetővé nagy kérelem kötetek sorrendjében naponta kérelmek trillions méretezni.

Ezek az attribútumok előnyösek webes, mobil-, játék, és az IoT-alkalmazásokhoz, amelyek alacsony válaszidők kell és nagy mennyiségű olvasási és írási kezelésére.

## <a name="iot-and-telematics"></a>IoT és telematika
Az IoT alkalmazási helyzetek általában néhány minták hogyan azok betöltési, folyamat, megoszthatja, és adatok tárolásához.  Ezek a rendszerek elsőként kell az eszköz érzékelők a különböző országokban élő adatok felszakadásáig betöltési. Ezután ezek a rendszerek feldolgozni, és elemezni kapcsolattípusokból valós idejű elemzése. Az adatok majd archivált cold storage kötegelt elemzéséhez. A Microsoft Azure kínál, amelyek használhatók az IoT gazdag szolgáltatások használati esetekben Azure Cosmos DB, az Azure Event Hubs, Azure Stream Analytics, Azure Notification Hub, Azure Machine Learning, Azure HDInsight és Power bi. 

![Az Azure Cosmos DB IoT-referenciaarchitektúra](./media/use-cases/iot.png)

Az adatok felszakadásáig meghatározták Azure Event Hubs által, a magas teljesítmény adatfeldolgozást, kisebb késést biztosít. A funneled okozhatnak, amelyet a valós idejű betekintés a feldolgozandó adatok Azure Stream Analytics valós idejű elemzések. Adatok lekérdezése ad hoc Azure Cosmos DB betölthető. Az adatok Azure Cosmos DB tölti be, ha az adatok készen áll a kérdezhető le. Emellett új adatokat és a meglévő adatok módosításait elolvashatja a módosítás adatcsatorna. Változás adatcsatorna egy állandó, a csak a tároló Cosmos DB gyűjtemények módosításai sorrendben napló hozzáfűzése. A minden adathoz és adatok Azure Cosmos DB csak módosításait referenciaadatok valós idejű elemzési részeként használható. Emellett adatok tovább kell kifinomultabb lett és dolgozza fel a HDInsight a Pig, a Hive vagy a térkép vagy csökkentse a feladatok Azure Cosmos DB adatok csatlakozik.  Kifinomultabb adatok majd betöltése Azure Cosmos DB vissza a jelentéskészítéshez.   

A minta az IoT-megoldások Azure Cosmos DB, EventHubs és Storm használatával, lásd: a [GitHub tárházából hdinsight-storm-példák](https://github.com/hdinsight/hdinsight-storm-examples/).

Az Azure IoT ajánlatok további információkért lásd: [létrehozása a dolgok Internet](http://www.microsoft.com/server-cloud/internet-of-things.aspx). 

## <a name="retail-and-marketing"></a>Kereskedelmi és marketing
Azure Cosmos-adatbázis a Microsoft saját elektronikus kereskedelmi platformok, futtassa a Windows áruház és az XBox Live nagymértékben használt. Is használatban van a kiskereskedelmi iparági eseménykatalógus-adatok tárolásához és folyamatok feldolgozási sorrendben forrás esemény.

Katalógus adatok használati forgatókönyvek tartalmaz, amely tárolja, és lekérdezi-attribútumok entitások, például a személyek, helyek és termékek. Eseménykatalógus-adatok egyes példák felhasználói fiókok, termék katalógusok, IoT eszköz nyilvántartó és számlázási anyagok rendszerek. Ezekre az adatokra vonatkozóan attribútumok változhat, és az alkalmazás követelményeinek megfelelő idővel megváltozhat.

Vegye figyelembe a termékkatalógus például egy való részek szállító. Minden egyes részben rendelkezhet saját attribútumok mellett az általános attribútumokkal rendelkeznek, amelyek bármely részét. Ezenkívül egy adott részére attribútumok módosíthatja a következő év, amikor megjelenik egy új modell. Azure Cosmos DB támogatja a rugalmas sémák és a hierarchikus adatokhoz, és ezért kiválóan alkalmas termék katalógus adatainak tárolásához.

![Az Azure Cosmos DB kereskedelmi katalógus referencia-architektúrában](./media/use-cases/product-catalog.png)

Azure Cosmos DB esemény power eseményvezérelt architektúrák használatával a forrás gyakran használják a [adatcsatorna módosítása](change-feed.md) funkciót. A módosítás hírcsatorna teszi lehetővé alárendelt mikroszolgáltatások megbízhatóan és fokozatosan olvasási beszúrások és egy Azure Cosmos DB végrehajtott frissítéseket (például rendelés esemény). Ezt a funkciót is javítható, ha egy állandó esemény tárolót biztosítanak üzenet közvetítőként állapot módosítása események és a meghajtó rendelés feldolgozása munkafolyamat között számos mikroszolgáltatások (amely is implementálható [kiszolgáló nélküli Azure Functions](http://azure.com/serverless)).

![Azure Cosmos-adatbázis rendezési csővezeték referencia-architektúrában](./media/use-cases/event-sourcing.png)

Emellett Azure Cosmos DB tárolt adatok integrálható a HDInsight a big data elemzésre szolgáló Apache Spark feladatok keresztül. A Spark on Azure Cosmos DB-összekötő a részletekért lásd: [Cosmos-adatbázis és a HDInsight Spark feladat futtatása](spark-connector.md).

## <a name="gaming"></a>Játékok
Az adatbázis-rétegből játékalkalmazások kritikus fontosságú része. Modern játékok hajtsa végre a grafikus feldolgozási mobile/konzol ügyfeleken, de a felhőben, mint a játékbeli statisztikák, a közösségi integrációs és a nagy-pontszám ranglisták testreszabott és személyre szabott tartalmat továbbít támaszkodnak. Játékok gyakran megkövetelik a single-ezredmásodperces késések fordulnak elő az olvasási és írási műveleteket ad meg egy módon vegye fel a játékbeli élmény. A game-adatbázist vissza kell gyors és kezelje az új játék elindítja a kérelem díjszabás nagy teljesítményt és a szolgáltatás a frissítések.

Például a játékok által használt Azure Cosmos-adatbázis [az elhalt érdekében: nem Man föld](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) által [következő játékok](http://www.nextgames.com/), és [Halo 5: felügyeletet](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Azure Cosmos-adatbázis a game fejlesztők számára a következő előnyöket nyújtja:

* Azure Cosmos DB lehetővé teszi a méretezhető teljesítmény felfelé vagy lefelé, rugalmasan. Ez lehetővé teszi, hogy a frissítési profil és statisztikák több tucatnyi az egyidejű gamers millióira egyetlen API-hívással játékok.
* Azure Cosmos DB ezredmásodperces olvasások támogatja, és írja játék során bármilyen késedelmes jelentések elkerülése érdekében.
* Azure Cosmos-adatbázis automatikus indexeléshez lehetővé teszi, hogy több különböző tulajdonságai valós idejű szűrése, keresse meg például a belső Windows Media Player azonosítók lejátszókról, vagy a GameCenter, a Facebook, a Google azonosítóját vagy a lekérdezés alapján egy guild player tagjának kell lennie. Ez nem lehetséges összetett indexelő vagy horizontális infrastruktúra.
* Közösségi szolgáltatásai, beleértve a játékbeli csevegési üzeneteket, a player guild tagságát, a kihívás befejeződött, a nagy-pontszám ranglisták és a közösségi diagramjait egyszerűbb, rugalmas sémával.
* Egy felügyelt platformon,--szolgáltatás (PaaS), Azure Cosmos-adatbázis szükséges minimális beállítása és felügyelete lehetővé teszik a gyors iterációs hogyan működik, és piacra idejének csökkentése érdekében.

![Az Azure Cosmos DB játék referencia-architektúrában](./media/use-cases/gaming.png)

## <a name="web-and-mobile-applications"></a>Webes és mobilalkalmazások
Azure Cosmos DB webes és mobilalkalmazásokhoz belül általában arra használják, és kiválóan alkalmas közösségi kapcsolati modellezési integrálása a harmadik féltől származó szolgáltatással, és a gazdag személyre szabott élmény felépítése. A Cosmos DB SDK-k lehet használt build gazdag iOS és Android-alkalmazások használata a népszerű [Xamarin keretrendszer](mobile-apps-with-xamarin.md).  

### <a name="social-applications"></a>Közösségi alkalmazások
Az Azure Cosmos DB gyakori használati eset webes, mobil és közösségi médiaalkalmazások tárolja, és lekérdezi a felhasználók által létrehozott tartalmakat (UGC). Néhány példa a UGC munkameneteket, Twitter-üzeneteket, blogbejegyzések, értékelések és hozzászólások. A közösségi média alkalmazások UGC gyakran, szabad formátumú szöveg, a tulajdonságok, a címkék és a kapcsolatokat, amelyek nem korlátozódnak képzési struktúra keverékéből. Tartalom például csevegés, megjegyzések és a POST tárolhatja Cosmos DB átalakítások vagy összetett objektumot relációs leképezés rétegek nélkül.  Adatok tulajdonságok is, illetve módosítása könnyen követelményeinek megfelelő módon a fejlesztők Ismétlés az alkalmazáskódban, így a gyors fejlesztés támogatása.  

Alkalmazások, amelyek integrálják a külső közösségi hálózatokkal válaszolnia kell a sémák módosítása az ezeket a hálózatokat. Adatok automatikusan indexelésre alapértelmezés szerint a Cosmos DB, adatok készen áll a bármikor kérdezhetők le. Ezeket az alkalmazásokat, így rugalmasan saját igényeiknek megfelelően leképezések beolvasása.

A közösségi alkalmazások számos globális méretekben futtatható, és előre nem látható használati minták is lehetnek. Az adattár skálázás rugalmasságot, az alkalmazási rétegre megfelelő használati igény szerint arányosan elengedhetetlen.  Ki lehet terjeszteni további adatok partíciók egy Cosmos DB fiókkal hozzáadásával.  Különféle régiókban ezenkívül további Cosmos DB fiókot is létrehozhat. A Cosmos DB szolgáltatás régiónkénti elérhetőség, lásd: [Azure-régiókat](https://azure.microsoft.com/regions/#services).

![Az Azure Cosmos DB webes alkalmazás referencia-architektúrában](./media/use-cases/apps-with-global-reach.png)

### <a name="personalization"></a>Személyre szabás
Napjainkban a modern alkalmazások összetett nézetek és elemek rendelkeznek. Ezek a általában dinamikus, felhasználói beállítások vagy hangulatának megfelelően Élelmezési és branding igényeinek. Emiatt alkalmazásokat kell tudni hatékonyan megjelenítéséhez felhasználói felületi elemek és lép gyorsan személyre szabott beállítások beolvasása. 

JSON formátuma nem támogatott Cosmos DB egy hatékony formátuma nem csak egyszerűsített, de is könnyen által értelmezhető JavaScript felhasználói felület elrendezése adatok jelöl. Cosmos DB konzisztenciaszinteket, amelyek lehetővé teszik a gyors olvasást a kis késleltetésű írások kínál. Emiatt az adattárolás felhasználói felület elrendezése Cosmos-adatbázis a JSON-dokumentumokként személyre szabott beállításokat is beleértve egy hatékony azt jelenti, hogy az adatok lekérése a hálózaton keresztül.

![Az Azure Cosmos DB webes alkalmazás referencia-architektúrában](./media/use-cases/personalization.png)

## <a name="next-steps"></a>Következő lépések
Ismerkedés az Azure Cosmos DB, hajtsa végre a [gyors üzembe helyezések](create-documentdb-dotnet.md), amely végigvezetik Önt egy fiókot és az első lépések Cosmos DB létrehozása. 

Vagy, ha azt szeretné, hogy további kapcsolatos Cosmos DB használatával az ügyfelek, a következő felhasználói szövegek érhetők el:

* [Jet.com](https://jet.com). Elektronikus kereskedelmi kihívó a felső helyszíni eyes, futtatja a Microsoft-felhő, globális léptékű Cosmos DB kihasználja.
* [Asos.com](http://www.asos.com/). Asos.com brit online módon és szépséget áruházbeli. Célja a fiatal felnőttek, Asos értékesít több mint 850 márkákat, valamint a saját számos ruházati és kellékek.
* [Toyota](https://www.toyota.com/). Toyota Motor Corporation a japán való gyártó. Toyota Cosmos DB globális IoT alkalmazások elkészítéséhez használja.
* [Citrix](https://customers.microsoft.com/story/citrix). Citrix házon belül fejlesztett alkalmazásokra single-sign-on megoldás Azure Service Fabric és Azure Cosmos DB használatával
* [TEXA](https://customers.microsoft.com/story/texaspa) TEXA tartozó Forradalmi IoT-megoldás a vehicle tulajdonosai Mentés időpontja, a pénz, a gáz segítségével –, és esetleg.
* [Domino tartozó pizzaszósz](https://www.dominos.com). Domino tartozó pizzaszósz Inc. egy amerikai pizzaszósz éttermi lánc.
* [Meghatározza, Johnson](http://www.johnsoncontrols.com). Johnson vezérlők egy globális változatos technológia és a kiszolgáló egy ügyfelek széles köre által 150-nél több országban több ipari vezető.
* [Microsoft Windows, univerzális tár, Azure IoT Hub, Xbox Live és egyéb Internet-skálázási szolgáltatások](https://azure.microsoft.com/blog/how-azure-documentdb-planet-scale-nosql-helps-run-microsoft-s-own-businesses/). A Microsoft hogyan nagymértékben méretezhető szolgáltatások, Azure Cosmos DB használatával hoz létre.
* [A Microsoft Data és az elemzések team](https://customers.microsoft.com/story/microsoftdataandanalytics). A Microsoft adatok és analitikák team bolygónk méretű big data gyűjtemény Azure Cosmos DB éri el.
* [Sulekha.com](https://customers.microsoft.com/story/sulekha-uses-azure-documentdb-to-connect-customers-and-businesses-across-india). Sulekha Azure Cosmos DB használatával az ügyfelek és a vállalatok India keresztül kapcsolódó.
* [NewOrbit](https://customers.microsoft.com/story/neworbit-takes-flight-with-azure-documentdb). NewOrbit a felhőszolgáltató közötti átviteléhez az Azure Cosmos DB vesz igénybe.
* [Affinio](https://customers.microsoft.com/doclink/affinio-switches-from-aws-to-azure-documentdb-to-harness-social-data-at-scale). Affinio Azure Cosmos DB léptékű közösségi adatok hasznosítására AWS vált.
* [Ezután játékokhoz](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/). A Walking kézbesítetlen: nem Man Népi föld játék soars # 1 Azure Cosmos DB által támogatott.
* [Halo](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Hogyan Halo 5 megvalósított közösségi játék Azure Cosmos DB használatával.
* [A Cortana Analytics gyűjtemény](https://azure.microsoft.com/blog/cortana-analytics-gallery-a-scalable-community-site-built-on-azure-documentdb/). Cortana Analytics Gallery - épülő Azure Cosmos DB méretezhető közösségi hely.
* [Gyerekjáték](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602). Nemzetközi vállalkozások globális Insight integráló vezető adja meg a rugalmas felhőalapú technológiái által nyújtott perc.
* [Hírek Köztársaság](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639). Az eszközintelligencia hozzáadása a tájékoztatási céllal minden polgárok híreket. 
* [SGS nemzetközi](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653). Világ körüli konzisztens szín bankkártyát SGS kapcsolja be. És SGS bekapcsolja az Azure-bA.
* [Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608). Vezető Telenor a felhő egy indítási sebességétől együtt használja. 
* [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667). A tároló a jövőbeli fut, a gyors keresési és az adatok könnyen áramlását.
* [Nucleo](https://customers.microsoft.com/story/azure-based-software-platform-breaks-down-barriers-bet). Azure-alapú szoftver platform vállalatok és az ügyfelek közötti akadályok megszakad.
* [Weka](https://customers.microsoft.com/story/weka-smart-fridge-improves-vaccine-management-so-more-people-can-be-protected-against-diseases). Weka intelligens kombinált hűtőszekrények javítja a vakcina felügyeleti, így több ember betegségek elleni védelme
* [Narancssárga Tribes](https://customers.microsoft.com/story/theres-more-to-that-food-app-than-meets-the-eye-or-the-mouth). Nincs több, mint a szem, vagy a szájához megfelel-e, hogy étele alkalmazásba.
* [Valós Madridi](https://customers.microsoft.com/story/real-madrid-brings-the-stadium-closer-to-450-million-f). Valós Madridi közelebb a stadium 450 millió ventilátorok együtt a Microsoft Cloud a világ minden táján biztosítható az.
* [Tuku](https://customers.microsoft.com/story/tuku-makes-car-buying-fun-with-help-from-azure-services). TUKU lehetővé teszi az Azure-szolgáltatások súgójában Szórakozás vásárlás autó
