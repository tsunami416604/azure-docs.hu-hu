---
title: Gyakori használati esetek és forgatókönyvek az Azure Cosmos DB-hez
description: 'Ismerje meg az Azure Cosmos DB első öt használati esetét: a felhasználó által létrehozott tartalom, az eseménynaplózás, a katalógusadatok, a felhasználói beállítások adatai és az eszközök internetes hálózata (IoT).'
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: de2bc551547706fb820813e57996e77bf49148d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73888936"
---
# <a name="common-azure-cosmos-db-use-cases"></a>Gyakori Azure Cosmos DB használati esetek
Ez a cikk áttekintést nyújt az Azure Cosmos DB számos gyakori használati esetéről.  Az ebben a cikkben található javaslatok kiindulópontként szolgálnak, ahogy fejleszti az alkalmazást a Cosmos DB-vel.   

A cikk elolvasása után a következő kérdésekre válaszolhat: 

* Melyek az Azure Cosmos DB gyakori használati esetei?
* Milyen előnyökkel jár az Azure Cosmos DB kiskereskedelmi alkalmazásokhoz való használata?
* Milyen előnyökkel jár az Azure Cosmos DB az eszközök internetes hálózata (IoT) rendszereinek adattáraként való használata?
* Milyen előnyökkel jár az Azure Cosmos DB webes és mobilalkalmazásokhoz való használata?

## <a name="introduction"></a>Bevezetés
[Az Azure Cosmos DB](../cosmos-db/introduction.md) a Microsoft globálisan elosztott adatbázis-szolgáltatása. A szolgáltatás célja, hogy lehetővé tegye az ügyfelek számára, hogy rugalmasan (és egymástól függetlenül) skálázhassák az átviteli és tárolási átviteli kapacitást és tárolást tetszőleges számú földrajzi régióban. Az Azure Cosmos DB az első globálisan elosztott adatbázis-szolgáltatás a piacon, amely átfogó [szolgáltatásiszint-szerződéseket](https://azure.microsoft.com/support/legal/sla/cosmos-db/) kínál, amelyek átfogó átviteli, késési, rendelkezésre állási és konzisztenciát foglalnak magukban. 

Az Azure Cosmos DB egy globális elosztott, többmodelles adatbázis, amely et alkalmazások és használati esetek széles körében használják. Ez egy jó választás minden [kiszolgáló nélküli](https://azure.com/serverless) alkalmazás, amely nek szüksége van az alacsony sorrendben-a-milliszekundumos válaszidő, és gyorsan kell skálázni, és globálisan. Támogatja a több adatmodellek (kulcs-érték, dokumentumok, grafikonok és oszlopos) és számos API-k adathozzáférés, beleértve [az Azure Cosmos DB API-t MongoDB,](mongodb-introduction.md) [SQL API,](documentdb-introduction.md) [Gremlin API](graph-introduction.md), és a táblák [API](table-introduction.md) natív módon, és bővíthető módon. 

Az alábbiakban az Azure Cosmos DB néhány olyan attribútumát ismertetjük, amelyek kiválóan alkalmasak a globális ambícióval rendelkező nagy teljesítményű alkalmazásokhoz.

* Az Azure Cosmos DB natív módon particionálja az adatokat a magas rendelkezésre állás és a méretezhetőség érdekében. Az Azure Cosmos DB 99,99%-os garanciát kínál a rendelkezésre állás, az átviteli képesség, az alacsony késés és a konzisztencia minden egyrégiós fiókok és az összes több régiós fiókok nyugodt konzisztencia és 99,999%-os olvasási rendelkezésre állásminden többrégiós adatbázis-fiókok.
* Az Azure Cosmos DB ssd-alapú tárolóval rendelkezik, alacsony késésű, ezredmásodperces válaszidővel.
* Az Azure Cosmos DB támogatja a konzisztenciaszintek, például a végleges, konzisztens előtag, munkamenet és a határolt állottság lehetővé teszi a teljes rugalmasságot és az alacsony költség-teljesítmény arány. Egyetlen adatbázis-szolgáltatás sem kínál olyan rugalmasságot, mint az Azure Cosmos DB a szintek konzisztenciájában. 
* Az Azure Cosmos DB rugalmas, adatbarát díjszabási modellel rendelkezik, amely egymástól függetlenül méri a tárolást és az átviteli forgalmat.
* Az Azure Cosmos DB fenntartott átviteli sebességét lehetővé teszi, hogy az alapul szolgáló hardver CPU/memória/IPP-k helyett az olvasások/írások számát tekintve.
* Az Azure Cosmos DB terve lehetővé teszi, hogy a kérelmek száma naponta több billió nagyságrenddel méretezhető.

Ezek az attribútumok előnyösek a webes, mobil-, játék- és IoT-alkalmazásokban, amelyeknek alacsony válaszidőt igényelnek, és nagy mennyiségű olvasást és írást kell kezelni.

## <a name="iot-and-telematics"></a>IoT és telematika
Az IoT használati esetei gyakran megosztanak bizonyos mintákat az adatok betöltésében, feldolgozásában és tárolásában.  Először is, ezek a rendszerek kell betöltése adatlöket ek eszköz érzékelők különböző helyszíneken. Ezután ezek a rendszerek feldolgozzák és elemzik a streamelési adatokat, hogy valós idejű elemzéseket nyerjenek. Az adatok ezután archiválva a hűtőtároló a kötegelt elemzés. A Microsoft Azure olyan gazdag szolgáltatásokat kínál, amelyek az IoT-használati esetekben alkalmazhatók, például az Azure Cosmos DB, az Azure Event Hubs, az Azure Stream Analytics, az Azure Notification Hub, az Azure Machine Learning, az Azure HDInsight és a Power BI. 

![Az Azure Cosmos DB IoT referenciaarchitektúrája](./media/use-cases/iot.png)

Adatfelszakítások az Azure Event Hubs által, mivel magas átviteli sebességű adatbetöltésalacsony késésű kínál. A valós idejű elemzéshez feldolgozandó adatok at az Azure Stream Analytics valós idejű elemzéshez továbbíthatja. Az adatok betölthetők az Azure Cosmos DB-be adhoc lekérdezéshez. Miután az adatok betöltődnek az Azure Cosmos DB-be, az adatok lekérdezhetők. Ezenkívül az új adatok és a meglévő adatok módosításai a módosítási hírcsatornában olvashatók. A változáscsatorna egy állandó, csak hozzáfűzési napló, amely a Cosmos-tárolók változásait sorrendben tárolja. Az Azure Cosmos DB-ben az összes adat vagy csak az adatok módosítása referenciaadatként használható a valós idejű elemzés részeként. Emellett az adatok tovább finomíthatók és feldolgozhatók az Azure Cosmos DB-adatok nak a HDInsightpig, Hive vagy Map/Reduce feladatokhoz való csatlakoztatásával.  A finomított adatok ezután visszatöltődnek az Azure Cosmos DB-be a jelentéskészítéshez.   

Az Azure Cosmos DB, az EventHubs és a Storm használatával egy minta IoT-megoldásért tekintse meg a [GitHub hdinsight-storm-examples tárházát.](https://github.com/hdinsight/hdinsight-storm-examples/)

Az IoT-hez való Azure-ajánlatokról a [Saját dolgai internetes hálózatának létrehozása](https://www.microsoft.com/en-us/internet-of-things)című témakörben talál további információt. 

## <a name="retail-and-marketing"></a>Kiskereskedelem és marketing
Az Azure Cosmos DB széles körben használható a Microsoft saját e-kereskedelmi platformjain, amelyek a Windows Áruházat és az XBox Live-ot futtatják. A kiskereskedelmi ágazatban katalógusadatok tárolására és eseménybeszerzésre is használják a rendelésfeldolgozási folyamatokban.

A katalógusadat-használati forgatókönyvek magukban foglalják az entitások, például személyek, helyek és termékek attribútumkészletének tárolását és lekérdezését. A katalógusadatok közé tartoznak például a felhasználói fiókok, a termékkatalógusok, az IoT-eszköznyilvántartások és az anyagjegyzék-rendszerek. Az adatok attribútumai változhatnak, és idővel változhatnak, hogy megfeleljenek az alkalmazás követelményeinek.

Vegyünk egy példát egy autóipari alkatrész-beszállító termékkatalógusára. Minden résznek lehetnek saját attribútumai a közös jellemzők mellett, amelyekminden alkatrészben osztoznak. Továbbá egy adott alkatrész attribútumai módosíthatják a következő évben, amikor egy új modell tanévben jelenik meg. Az Azure Cosmos DB támogatja a rugalmas sémákat és a hierarchikus adatokat, és így kiválóan alkalmas a termékkatalógus-adatok tárolására.

![Az Azure Cosmos DB kiskereskedelmi katalógus referenciaarchitektúrája](./media/use-cases/product-catalog.png)

Az Azure Cosmos DB gyakran használják az esemény beszerzése a hatalom eseményvezérelt architektúrák segítségével a [változáscsatorna-funkció.](change-feed.md) A változáscsatorna lehetővé teszi az alsóbb rétegbeli mikroszolgáltatások lehetővé teszi, hogy megbízhatóan és növekményesen olvassa be a beszúrások és frissítések (például rendelési események) egy Azure Cosmos DB- hez készült. Ez a funkció használható, hogy egy állandó eseménytároló, mint egy üzenet közvetítő az állapotváltozási események és a meghajtó rendelésfeldolgozási munkafolyamat között számos mikroszolgáltatások (amely lehet megvalósítani, mint [kiszolgáló nélküli Azure Functions).](https://azure.com/serverless)

![Az Azure Cosmos DB rendelési folyamat referenciaarchitektúrája](./media/use-cases/event-sourcing.png)

Emellett az Azure Cosmos DB-ben tárolt adatok integrálhatók a HDInsightba az Apache Spark-feladatokon keresztül a big data-elemzéshez. A Spark Connector for Azure Cosmos DB című témakörben talál további [információt: Spark-feladat futtatása a Cosmos DB és a HDInsight használatával című témakörben.](spark-connector.md)

## <a name="gaming"></a>Játékok
Az adatbázisszint a játékalkalmazások kulcsfontosságú összetevője. A modern játékok grafikus feldolgozást végeznek mobil/konzolos ügyfeleken, de a felhőre támaszkodva személyre szabott és személyre szabott tartalmakat, például játékon belüli statisztikákat, közösségi média integrációt és magas pontszámot mutató ranglistákat biztosítanak. A játékok gyakran egyezredmásodperces késleltetést igényelnek az olvasáshoz és íráshoz, hogy vonzó játékélményt nyújtsanak. A játék adatbázisának gyorsnak kell lennie, és képesnek kell lennie arra, hogy kezelni tudja a kérelmek arányának hatalmas kiugrásait az új játékindítások és a funkciófrissítések során.

Az Azure Cosmos DB-t olyan játékok használják, mint [a The Walking Dead: No Man's Land](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) by Next [Games](https://www.nextgames.com/)és a [Halo 5: Guardians.](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/) Az Azure Cosmos DB a következő előnyöket nyújtja a játékfejlesztőknek:

* Az Azure Cosmos DB lehetővé teszi a teljesítmény rugalmasan skálázható vagy leskálázható. Ez lehetővé teszi a játékok számára, hogy egyetlen API-hívással kezeljék a profil és a statisztikák frissítését több tucat ról több millió egyidejű játékosra.
* Az Azure Cosmos DB támogatja a ezredmásodperces olvasási és írási műveleteket, hogy elkerülje a játék közbeni késéseket.
* Az Azure Cosmos DB automatikus indexelése lehetővé teszi a szűrést több különböző tulajdonságellen valós időben, például a játékosok at a belső játékosazonosítóik, vagy a GameCenter, a Facebook, a Google ID-k vagy a lekérdezés alapján a játékosok tagsága egy céh. Ez összetett indexelési vagy érgésezési infrastruktúra kiépítése nélkül lehetséges.
* A közösségi funkciók, beleértve a játékon belüli csevegési üzeneteket, a játékoscéh tagságot, a befejezett kihívásokat, a magas pontszámot mutató ranglistákat és a közösségi grafikonokat, könnyebben megvalósíthatók egy rugalmas sémával.
* Az Azure Cosmos DB szolgáltatásként (PaaS) felügyelt platformszolgáltatásként (PaaS) minimális beállítási és felügyeleti munkát igényelt a gyors iteráció lehetővé tételéhez és a piacra jutáshoz szükséges idő csökkentéséhez.

![Az Azure Cosmos DB játékreferencia-architektúrája](./media/use-cases/gaming.png)

## <a name="web-and-mobile-applications"></a>Webes és mobilalkalmazások
Az Azure Cosmos DB-t gyakran használják webes és mobilalkalmazásokban, és kiválóan alkalmas a közösségi interakciók modellezésére, a harmadik fél től származó szolgáltatásokkal való integrációra és a gazdag, személyre szabott élmények kialakítására. A Cosmos DB SDK-k a népszerű [Xamarin keretrendszer](mobile-apps-with-xamarin.md)használatával használható gazdag iOS és Android alkalmazásokat.  

### <a name="social-applications"></a>Közösségi alkalmazások
Az Azure Cosmos DB gyakori használati esete a felhasználók által létrehozott tartalom (UGC) tárolása és lekérdezése webes, mobil- és közösségimédia-alkalmazásokhoz. Néhány példa az UGC-re a csevegések, tweetek, blogbejegyzések, értékelések és megjegyzések. Gyakran előfordul, hogy az UGC a szociális média alkalmazások keveréke szabad formában szöveg, tulajdonságok, címkék, és kapcsolatok, amelyek nem határolja merev szerkezet. Az olyan tartalmak, mint a csevegések, a megjegyzések és a bejegyzések a Cosmos DB-ben tárolhatók anélkül, hogy átalakításokat vagy összetett objektumot kellene alkalmazni a relációs leképezési rétegekhez.  Az adattulajdonságok könnyen hozzáadhatók vagy módosíthatók, hogy megfeleljenek a követelményeknek, mivel a fejlesztők az alkalmazáskódon keresztül iterálnak, így elősegítve a gyors fejlődést.  

A külső közösségi hálózatokkal integrálható alkalmazásoknak válaszolniuk kell az ilyen hálózatokból származó sémák ra. Mivel a Cosmos DB alapértelmezés szerint automatikusan indexeli az adatokat, az adatok bármikor lekérdezhetők. Ezért ezek az alkalmazások rendelkeznek a megfelelő igényeknek megfelelő előrejelzések lekéréséhez szükséges rugalmassággal.

A közösségi alkalmazások nagy része globális szinten fut, és kiszámíthatatlan használati mintákat mutathat. Az adattár méretezésének rugalmassága alapvető fontosságú, mivel az alkalmazásréteg a használati igényeknek megfelelően méretezhető.  További adatpartíciók hozzáadásával a Cosmos DB-fiók.  Emellett további Cosmos DB-fiókokat is létrehozhat több régióban. A Cosmos DB szolgáltatásrégió elérhetősége az [Azure Regions](https://azure.microsoft.com/regions/#services).

![Az Azure Cosmos DB webalkalmazás referenciaarchitektúrája](./media/use-cases/apps-with-global-reach.png)

### <a name="personalization"></a>Személyre szabás
Napjainkban a modern alkalmazások összetett nézetekkel és tapasztalatokkal rendelkeznek. Ezek jellemzően dinamikus, vendéglátás a felhasználói preferenciák vagy hangulatok és branding igényeket. Ezért az alkalmazásoknak képesnek kell lenniük a személyre szabott beállítások hatékony lekérésére, hogy a felhasználói felület elemei és felhasználói élményei gyorsan megjelenjenek. 

JSON, a formátum által támogatott Cosmos DB, egy hatékony formátum, hogy képviselje UI elrendezés adatokat, mivel nem csak a könnyű, hanem könnyen értelmezhető a JavaScript. Cosmos DB kínál hangolható konzisztenciaszintek, amelyek lehetővé teszik a gyors olvasás alacsony késleltetésű írások. Ezért a felhasználói felület elrendezési adatainak tárolása, beleértve a személyre szabott beállításokat, mint a JSON-dokumentumok a Cosmos DB-ben hatékony eszköz az adatok vezetéken keresztül történő be, hatékony eszköz.

![Az Azure Cosmos DB webalkalmazás referenciaarchitektúrája](./media/use-cases/personalization.png)

## <a name="next-steps"></a>További lépések

* Az Azure Cosmos DB első lépéseihez kövesse [a gyorsindításokat,](create-sql-api-dotnet.md)amelyek végigvezetik a fiók létrehozásán és a Cosmos DB használatával való ismerkedésen.

* Ha szeretne többet megtudni az Azure Cosmos DB-t használó ügyfelekről, tekintse meg az [ügyfél esettanulmányok](https://azure.microsoft.com/case-studies/?service=cosmos-db) oldalát.
