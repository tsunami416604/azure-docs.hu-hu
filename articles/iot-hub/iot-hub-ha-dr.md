---
title: Az Azure IoT Hub magas rendelkezésre állású és vészhelyreállítás | Microsoft dokumentumok
description: Ismerteti az Azure és az IoT Hub azon funkcióit, amelyek segítségével magas rendelkezésre állású Azure IoT-megoldásokat hozhat létre vész-helyreállítási képességekkel.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: philmea
ms.openlocfilehash: 615dc1b7bd1a31069a542ebb7ea44693c404cb40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499108"
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>IoT Hub magas rendelkezésre állása és vészhelyreállítása

A rugalmas IoT-megoldás megvalósítása felé tett első lépésként az építészeknek, fejlesztőknek és cégtulajdonosoknak meg kell határozniuk az általuk létrehozott megoldások előállási céljait. Ezek a célok elsősorban az egyes forgatókönyvek konkrét üzleti céljai alapján határozhatók meg. Ebben a környezetben az [Azure Üzletmenet-folytonosságtechnikai útmutató](https://docs.microsoft.com/azure/architecture/resiliency/) egy általános keretrendszert ír le, amely segít az üzletmenet folytonosságának és a vészhelyreállításnak a gondolkodásában. A [vész-helyreállítási és magas rendelkezésre állású Azure-alkalmazások](https://docs.microsoft.com/azure/architecture/reliability/disaster-recovery) papír architektúra útmutatást nyújt az Azure-alkalmazások stratégiák magas rendelkezésre állású (HA) és a vész-helyreállítási (DR).

Ez a cikk ismerteti a HA és a VÉSZ-funkciók kifejezetten az IoT Hub szolgáltatás által kínált. Az ebben a cikkben tárgyalt területek a következők:

- Régión belüli HA
- Régióközi DR
- Régióközi HA elérése

Az IoT-megoldásokhoz megadott rendelkezésre állási céloktól függően meg kell határoznia, hogy az alábbiakban vázolt lehetőségek közül melyik felel meg legjobban az üzleti céloknak. Ezen HA/DR alternatívák bármelyikének az IoT-megoldásba való beépítése a következők közötti kompromisszumok gondos értékelését igényli:

- A rugalmasság identite 
- A végrehajtás és a karbantartás összetettsége
- ELÁBÉ hatása

## <a name="intra-region-ha"></a>Régión belüli HA

Az IoT Hub szolgáltatás régión belüli HA-t biztosít a szolgáltatás szinte minden rétegében redundanciák megvalósításával. Az [IoT Hub szolgáltatás által közzétett SLA-t](https://azure.microsoft.com/support/legal/sla/iot-hub) ezek az elbocsátások kihasználásával érik el. Nincs szükség további munkára az IoT-megoldások fejlesztőinek, hogy kihasználhassák ezeket a HA-funkciókat. Bár az IoT Hub ésszerűen magas állásidő-garanciát kínál, átmeneti hibák rakhatók ki, mint bármely elosztott számítástechnikai platform. Ha csak most kezdi el átváltani a megoldásokat a felhőbe egy helyszíni megoldásból, a fókusznak át kell térnie a "hibák közötti átlagos idő" optimalizálásáról a "helyreállítás átlagos idejére". Más szóval, átmeneti hibák at kell tekinteni normális, miközben működik a felhő a mix. Megfelelő [újrapróbálkozási szabályzatokat](iot-hub-reliability-features-in-sdks.md) kell beépíteni az összetevők egy felhőalapú alkalmazással átmeneti hibák kezelésére.

> [!NOTE]
> Egyes Azure-szolgáltatások további kapacitási rétegeket is biztosítanak egy régión belül a [rendelkezésre állási zónákkal (AZs)](../availability-zones/az-overview.md)való integrálással. Az AZs-t jelenleg nem támogatja az IoT Hub szolgáltatás.

## <a name="cross-region-dr"></a>Régióközi DR

Előfordulhat, hogy néhány ritka helyzetekben, amikor egy adatközpont ban tapasztal hosszabb áramkimaradások áramkimaradások miatt, vagy más hibák at fizikai eszközök. Az ilyen események ritkák, amelyek során a régión belüli HA képesség a fent leírt nem mindig segít. Az IoT Hub több megoldást kínál az ilyen kiterjesztett kimaradások helyreállításához. 

Ilyen helyzetben az ügyfelek számára elérhető helyreállítási lehetőségek a [Microsoft által kezdeményezett feladatátvétel](#microsoft-initiated-failover) és [a manuális feladatátvétel](#manual-failover). A kettő közötti alapvető különbség az, hogy a Microsoft kezdeményezi az előbbit, a felhasználó pedig az utóbbit. Emellett a manuális feladatátvétel alacsonyabb helyreállítási idő célt (RTO) biztosít a Microsoft által kezdeményezett feladatátvételi beállításhoz képest. Az egyes opciókkal kínált konkrét RtOs-t az alábbi szakaszok tárgyaljuk. Ha ezen lehetőségek valamelyike feladatátvételt hajt végre az elsődleges régióból, a hub teljes mértékben működőképessé válik a megfelelő [Azure geopárosított régióban.](../best-practices-availability-paired-regions.md)

Mindkét feladatátvételi lehetőség a következő helyreállítási pont-célkitűzéseket kínálja:

| Adattípus | A helyreállítási pont célkitűzései (RPO) |
| --- | --- |
| Azonosító beállításjegyzék |0-5 perces adatvesztés |
| Az ikereszköz adatai |0-5 perces adatvesztés |
| Felhőből eszközre irányuló üzenetek<sup>1</sup> |0-5 perces adatvesztés |
| Szülő<sup>1</sup> és eszköz feladatok |0-5 perces adatvesztés |
| Az eszközről a felhőbe irányuló üzenetek |Minden olvasatlan üzenet elvész |
| Műveletek figyelése üzenetek |Minden olvasatlan üzenet elvész |
| Felhőből eszközre irányuló visszajelzési üzenetek |Minden olvasatlan üzenet elvész |

<sup>1 1</sup> A felhőből az eszközre irányuló üzenetek és a szülőfeladatok nem lesznek helyreállva a manuális feladatátvétel részeként.

Miután az IoT hub feladatátvételi műveletbefejeződött, az eszközről és a háttéralkalmazásokból származó összes művelet várhatóan manuális beavatkozás nélkül folytatja a munkát. Ez azt jelenti, hogy az eszközről a felhőbe irányuló üzeneteknek továbbra is működniük kell, és a teljes eszközbeállítási lista érintetlen. Az Event Grid en keresztül kibocsátott események a korábban konfigurált előfizetés(ek)en keresztül használhatók fel, feltéve, hogy ezek az Event Grid-előfizetések továbbra is elérhetők maradnak.

> [!CAUTION]
> - Az Event Hub-kompatibilis neve és végpontja az IoT Hub beépített események végpont változása után feladatátvétel, és a konfigurált fogyasztói csoportok eltávolítása (ez a hiba, amely 2020 májusa előtt kijavítható). Telemetriai üzenetek fogadása a beépített végpont segítségével az Event Hub-ügyfél vagy az eseményfeldolgozó gazdagép, [az IoT hub kapcsolati karakterlánc a](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) kapcsolat létrehozásához. Ez biztosítja, hogy a háttéralkalmazások továbbra is működjenek manuális beavatkozás utáni feladatátvétel nélkül. Ha közvetlenül használja az Event Hub-kompatibilis nevet és végpontot az alkalmazásban, újra kell [konfigurálnia az általuk használt fogyasztói csoportot, és a](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) feladatátvétel után le kell kérnie az új Event Hub-kompatibilis végpontot a műveletek folytatásához. Ha az Azure Functions vagy az Azure Stream Analytics használatával csatlakoztatja a beépített végpontot, előfordulhat, hogy újra kell **indítania az újraindítást.**
>
> - A tárolóba történő továbbításkor azt javasoljuk, hogy sorolja fel a blobokat vagy fájlokat, majd iterálja őket, hogy minden blob vagy fájl olvasása nélkül olvassa el a partíciót. A partíciótartomány potenciálisan változhat a Microsoft által kezdeményezett feladatátvétel vagy manuális feladatátvétel során. A List [Blobs API segítségével](https://docs.microsoft.com/rest/api/storageservices/list-blobs) felsorolhatja a blobok listáját, vagy [lista ADLS Gen2 API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/list) a fájlok listáját. 

## <a name="microsoft-initiated-failover"></a>A Microsoft által kezdeményezett feladatátvétel

A Microsoft által kezdeményezett feladatátvételt a Microsoft ritka esetekben gyakorolja az érintett régióból a megfelelő földrajzilag párosított régióba tartozó összes IoT-központ feladatátvétele. Ez a folyamat egy alapértelmezett beállítás (a felhasználók nem választhatják le a leiratkozást), és nem igényel beavatkozást a felhasználótól. A Microsoft fenntartja magának a jogot, hogy megállapítsa, mikor lép ez a lehetőség. Ez a mechanizmus nem jár a felhasználó beleegyezését, mielőtt a felhasználó hub feladatátvétel. A Microsoft által kezdeményezett feladatátvétel 2–26 órás helyreállítási időcélt (RTO) használ. 

A nagy RTO azért van, mert a Microsoft nak el kell végeznie a feladatátvételi műveletet az adott régió összes érintett ügyfele nevében. Ha egy kevésbé kritikus IoT-megoldást futtat, amely nagyjából egy napos állásidőt képes fenntartani, rendben van, ha függőséget vállal ettől a lehetőségtől az IoT-megoldás általános vész-helyreállítási céljainak teljesítéséhez. A futásidejű műveletek teljes élettartama a folyamat aktiválása után teljes mértékben működőképessé válik, a "Helyreállítás ideje" című szakaszban található.

## <a name="manual-failover"></a>Manuális feladatátvétel

Ha a vállalati készenléti célok nem teljesülnek az RTO, hogy a Microsoft által kezdeményezett feladatátvételi biztosítja, fontolja meg a manuális feladatátvétel a feladatátvételi folyamat indításához. Az RTO ezt az opciót használó bárhol között 10 perc és néhány óra. Az RTO jelenleg az IoT hub-példány feladatátvételével regisztrált eszközök számának függvénye. Az RTO-ra számíthat, amely körülbelül 100 000 eszközt üzemeltet, 15 perc alatt a stadionban lesz. A futásidejű műveletek teljes élettartama a folyamat aktiválása után teljes mértékben működőképessé válik, a "Helyreállítás ideje" című szakaszban található.

A manuális feladatátvételi lehetőség mindig használható, függetlenül attól, hogy az elsődleges régióban tapasztalható állásidő- e vagy sem. Ezért ez a lehetőség potenciálisan használható tervezett feladatátvételek végrehajtására. A tervezett feladatátvételek egyik példája az időszakos feladatátvételi gyakorlatok végrehajtása. Egy figyelmeztető szó azonban az, hogy a tervezett feladatátvételi művelet a hub leállását eredményezi az RTO által ehhez a beállításhoz meghatározott időszakra vonatkozóan, és adatvesztést is eredményez a fenti RPO-táblázatban meghatározottak szerint. Érdemes lehet egy teszt IoT hub-példány a tervezett feladatátvételi lehetőség rendszeres időközönként gyakorolni a bizalmat a képességét, hogy a vég-to-end megoldások, és futtatása, ha egy valódi katasztrófa történik.

Részletes útmutatásért olvassa el [az Oktatóanyag: Manuális feladatátvétel ioT hub esetén](tutorial-manual-failover.md)

### <a name="running-test-drills"></a>Futó tesztgyakorlatok

A tesztgyakorlatok nem hajthatók végre az éles környezetekben használt IoT-központokon.

### <a name="dont-use-manual-failover-to-migrate-iot-hub-to-a-different-region"></a>Ne használjon manuális feladatátvételt az IoT hub áttelepítéséhez egy másik régióba

A manuális feladatátvétel *nem* használható mechanizmusként a hub végleges áttelepítéséhez az Azure geopárosított régiói között. Ezzel növeli a késést az IoT hubon a régi elsődleges régióban található eszközökről végrehajtott műveletek késése.

## <a name="failback"></a>Feladat-visszavétel

A régi elsődleges régióra való visszalépés a feladatátvételi művelet egy másik alkalommal történő aktiválásával érhető el. Ha az eredeti feladatátvételi műveletet hajtották végre az eredeti elsődleges régióban egy kiterjesztett kimaradás helyreállítása érdekében, azt javasoljuk, hogy a hubot vissza kell állítani az eredeti helyre, miután a hely helyreállt a kimaradási helyzetből.

> [!IMPORTANT]
> - A felhasználók csak 2 sikeres feladatátvételt és 2 sikeres feladat-visszavételi műveletet hajthatnak végre naponta.
>
> - A feladatátvételi/feladat-visszavételi műveletek nem engedélyezettek. A műveletek között 1 órát kell várnia.

## <a name="time-to-recover"></a>Ideje a helyreállításnak

Míg az IT hub-példány fqdn (és így a kapcsolati karakterlánc) ugyanaz a feladatátvétel után marad, az alapul szolgáló IP-cím megváltozik. Ezért a teljes idő a futásidejű műveletek et az IoT hub-példány teljes mértékben működőképessé a feladatátvételi folyamat aktiválása után a következő függvény használatával fejezhető ki.

Helyreállítási idő = RTO [10 perc - 2 óra manuális feladatátvétel | 2 - 26 óra a Microsoft által kezdeményezett feladatátvétel esetén] + DNS-propagálási késleltetés + Az ügyfélalkalmazás által a gyorsítótárazott IoT Hub IP-cím frissítéséhez szükséges idő.

> [!IMPORTANT]
> Az IoT SDK-k nem gyorsítótárazják az IoT hub IP-címét. Azt javasoljuk, hogy az SDK-kkal való felhasználói kód nem gyorsítótárazhatja az IoT hub IP-címét.

## <a name="achieve-cross-region-ha"></a>Régióközi HA elérése

Ha a vállalati rendelkezésre állási célok nem teljesülnek az RTO, hogy akár a Microsoft által kezdeményezett feladatátvételi vagy manuális feladatátvételi beállításokat biztosít, érdemes egy eszközenkénti automatikus régióközi feladatátvételi mechanizmus megvalósítása.
Az IoT-megoldások üzembe helyezési topológiáinak teljes kezelése nem tartozik a jelen cikk hatálya alá. A cikk ismerteti a *regionális feladatátvételi* telepítési modell magas rendelkezésre állás és a vészhelyreállítás céljából.

Egy regionális feladatátvételi modellben a megoldás háttérrendszerelsősorban egy adatközpont-helyen fut. Egy másodlagos IoT-központ és háttérrendszer egy másik adatközpontban van telepítve. Ha az elsődleges régióban az IoT hub kimaradást szenved, vagy az eszköz és az elsődleges régió közötti hálózati kapcsolat megszakad, az eszközök másodlagos szolgáltatásvégpontot használnak. Javíthatja a megoldás rendelkezésre állását egy régiók közötti feladatátvételi modell megvalósításával, ahelyett, hogy egyetlen régión belül maradna. 

Magas szinten egy regionális feladatátvételi modell az IoT Hubbal valósítható meg, a következő lépéseket kell végrehajtania:

* **Másodlagos IoT-központ és eszközútválasztási logika:** Ha az elsődleges régióban a szolgáltatás megszakad, az eszközök nek meg kell kezdeniük a másodlagos régióhoz való csatlakozást. Tekintettel a legtöbb érintett szolgáltatás állapottudatos jellegére, gyakori, hogy a megoldás-rendszergazdák elindítják a régiók közötti feladatátvételi folyamatot. Az új végpont eszközökkel való kommunikációjának legjobb módja, miközben fenntartja a *concierge* folyamat irányítását, ha rendszeresen ellenőrzi kérjük őket az aktuális aktív végpont concierge-szolgáltatásának ellenőrzésére. A concierge-szolgáltatás lehet egy webalkalmazás, amely replikálása és elérhetőként tárolható dns-átirányítási technikák (például [az Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)használatával).

   > [!NOTE]
   > Az IoT-központ szolgáltatás nem támogatott végponttípus az Azure Traffic Managerben. A javaslat az, hogy integrálja a javasolt concierge-szolgáltatás az Azure traffic manager azáltal, hogy megvalósítja a végpont állapotmintavétel API-t.

* **Identitás-beállításjegyzék-replikáció**: A másodlagos IoT hub nak tartalmaznia kell az összes olyan eszközidentitást, amely csatlakozhat a megoldáshoz. A megoldás nak meg kell őriznie az eszközidentitások georeplikált biztonsági másolatait, és feltöltenie kell őket a másodlagos IoT hubra, mielőtt az eszközök aktív végpontját váltana. Az IoT Hub eszközidentitás-exportálási funkciója ebben a környezetben hasznos. További információ: [IoT Hub developer guide - identity registry](iot-hub-devguide-identity-registry.md).

* **Összevonási logika:** Amikor az elsődleges régió ismét elérhetővé válik, a másodlagos helyen létrehozott összes állapotot és adatot vissza kell telepíteni az elsődleges régióba. Ez az állapot és az adatok többnyire az eszközidentitások és az alkalmazás metaadatok, amelyeket egyesíteni kell az elsődleges IoT hub és bármely más alkalmazás-specifikus tárolja az elsődleges régióban. 

A lépés egyszerűsítése érdekében idempotens műveleteket kell használnia. Idempotens műveletek minimálisra csökkenti konzisztens események egyenletes eloszlása, és az ismétlődések vagy a sorrenden kívüli kézbesítése események. Emellett az alkalmazás logikáját úgy kell megtervezni, hogy tolerálja a lehetséges inkonzisztenciákat vagy kissé elavult állapotot. Ez a helyzet azért fordulhat elő, mert a helyreállítási pont (RPO) alapján a rendszer gyógyulásához szükséges további idő szükséges.

## <a name="choose-the-right-hadr-option"></a>Válassza ki a megfelelő HA/DR beállítást

Az alábbiakban összefoglaljuk a cikkben bemutatott HA/DR beállításokat, amelyek hivatkozási keretként használhatók a megoldáshoz megfelelő beállítás kiválasztásához.

| HA/DR beállítás | RTO | RPO | Kézi beavatkozást igényel? | A végrehajtás összetettsége | További költséghatás|
| --- | --- | --- | --- | --- | --- |
| A Microsoft által kezdeményezett feladatátvétel |2 - 26 óra|Hivatkozás a fenti RPO-táblázatra|Nem|None|None|
| Manuális feladatátvétel |10 min - 2 óra|Hivatkozás a fenti RPO-táblázatra|Igen|Nagyon alacsonyan. Ezt a műveletet csak a portálról kell elindítania.|None|
| Régióközi HA |< 1 min|Az egyéni HA-megoldás replikációs gyakoriságától függ|Nem|Magasság|> 1 IoT-központ költségének 1-szerese|

## <a name="next-steps"></a>További lépések

* [Mi az Azure IoT Hub?](about-iot-hub.md)
* [Az IoT Hubok – rövid útmutató – első lépések](quickstart-send-telemetry-dotnet.md)
* [Oktatóanyag: Manuális feladatátvétel végrehajtása egy IoT-központszámára](tutorial-manual-failover.md)