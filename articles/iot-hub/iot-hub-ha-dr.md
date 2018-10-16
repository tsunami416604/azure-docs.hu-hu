---
title: Az Azure IoT Hub magas rendelkezésre állás és vészhelyreállítás recovery |} A Microsoft Docs
description: Az Azure és az IoT Hub szolgáltatásokat, amelyek segítségével hozhat létre magas rendelkezésre állású Azure IoT-megoldások vész-helyreállítási funkciókat ismerteti.
author: rkmanda
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/07/2018
ms.author: rkmanda
ms.openlocfilehash: 1596cf1337fa084fe6a160c99e52ae80ee3e2491
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341973"
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>Az IoT Hub magas rendelkezésre állás és vészhelyreállítás helyreállítási

Az első lépés végrehajtásához egy rugalmas IoT megoldás, a tervezőket, a fejlesztők és az üzleti tulajdonosai meg kell határoznia a hasznos üzemidő célokat, azok készít megoldások. Ezen célok elsősorban az adott üzleti célok az egyes forgatókönyvek alapján lehet definiálni. Ebben a környezetben, a cikk [Azure üzleti folytonossági műszaki útmutatóját](https://docs.microsoft.com/azure/architecture/resiliency/) segítséget nyújt az üzleti folytonossági és vészhelyreállítási helyreállítási kapcsolatos általános keretet ismerteti. A [vészhelyreállítás és magas rendelkezésre állás az Azure-alkalmazások](https://msdn.microsoft.com/library/dn251004.aspx) tanulmány architektúra útmutatást nyújt az Azure-alkalmazások elérjék a magas rendelkezésre állású (HA) és a vészhelyreállítás (DR) helyreállítási stratégiákról.

Ez a cikk a magas rendelkezésre ÁLLÁS és Vészhelyreállítás által nyújtott szolgáltatások kifejezetten az IoT Hub szolgáltatást ismerteti. Az ebben a cikkben tárgyalt széles körű területek a következők:

- Régiók belüli magas rendelkezésre ÁLLÁS
- DR régiók közötti
- Régiók közötti magas rendelkezésre ÁLLÁS elérése

Attól függően, a hasznos üzemidő célokat, az IoT-megoldások kell megadni ellenőrizze a beállításokat, amely legjobban megfeleljen alább ismertetett az üzleti célok. HA/DR másik módszert bármelyikét beépítése az IoT-megoldás szükséges közti kompromisszummal gondos értékelése a:

- Szükséges rugalmassági szint 
- Megvalósítás és a karbantartás bonyolultsága
- COGS gyakorolt hatás

## <a name="intra-region-ha"></a>Régiók belüli magas rendelkezésre ÁLLÁS

Az IoT Hub szolgáltatás biztosítja, hogy a régiók belüli magas rendelkezésre ÁLLÁSÚ szinte minden rétegekben a szolgáltatás megvalósítása a redundanciát. A [SLA-t az IoT Hub szolgáltatás által közzétett](https://azure.microsoft.com/support/legal/sla/iot-hub) érik el, így ezek redundanciát használatát. Nincs további feladata a fejlesztőket az IoT-megoldás a magas rendelkezésre ÁLLÁSÚ szolgáltatások előnyeinek szükséges. Bár az IoT Hub biztosít a viszonylag magas rendelkezésre állását garantálja, átmeneti meghibásodások továbbra is várható csakúgy, mint bármely elosztott számítási platform. Hogy még csak most ismerkedik a megoldások a felhőre való áttérés egy helyszíni megoldás, ha a fókusz kell a "hibák közötti átlagos idő" optimalizálása eltolási "átlagos idő helyreállítása". Más szóval az átmeneti meghibásodások a rendszer normál a mix-ben a felhőhöz való működtetése során. Megfelelő [újrapróbálkozási szabályzatok](iot-hub-reliability-features-in-sdks.md) interakció során egy átmeneti hibák kezelésére felhőalkalmazás-összetevőkkel kell elkészíteni.

> [!NOTE]
> Bizonyos Azure-szolgáltatások is biztosít egy adott régión belül rendelkezésre állás további rétegeit integrálásával [rendelkezésre állási zónák (AZs)](../availability-zones/az-overview.md). AZs jelenleg nem támogatottak az IoT Hub szolgáltatás.

## <a name="cross-region-dr"></a>DR régiók közötti

Lehetnek egyes ritka esetekben amikor egy adatközpont kiterjesztett kimaradások, áramkimaradások vagy más fizikai eszközöket érintő hibák miatt. Ilyen esetek ritkák során, ami a belüli régióban magas rendelkezésre ÁLLÁSÚ képesség a fent leírt nem mindig segítségére lehetnek. Az IoT Hub több megoldásokat kínál ilyen kiterjesztett üzemkimaradások utáni helyreállításhoz. 

A helyreállítási beállítások érhetők el ügyfeleink számára, ebben az esetben a "a Microsoft által kezdeményezett feladatátvételi" és "manuális feladatátvétele". A kettő közötti alapvető különbség, hogy a Microsoft kezdeményezi a korábbi, és a felhasználó alkalmazástelepítést kezdeményez az utóbbi. Manuális feladatátvétel emellett egy alacsonyabb helyreállítási időre vonatkozó célkitűzés (RTO) a Microsoft által kezdeményezett feladatátvétel képest. A megadott RTO-k az egyes opciókhoz érhető el az alábbi szakaszok ismertetik. Az IoT hub a feladatátvétel végrehajtásához az elsődleges régióból történő bármelyik történik, ha a hub válik teljesen működőképes, a megfelelő [földrajzilag párosított régióba](../best-practices-availability-paired-regions.md).

A feladatátvételi beállításokat ajánlat az alábbi helyreállításipont-célkitűzések (Rpo):

| Adattípus | Helyreállításipont-célkitűzések (RPO) |
| --- | --- |
| Eszközidentitás-jegyzék |0 – 5 perc az adatvesztést |
| Eszközadatok ikereszköz |0 – 5 perc az adatvesztést |
| Felhőből az eszközre irányuló üzenetek<sup>1</sup> |0 – 5 perc az adatvesztést |
| Szülő<sup>1</sup> és eszközfeladatok |0 – 5 perc az adatvesztést |
| Az eszközről a felhőbe irányuló üzenetek |Az összes olvasatlan üzenetek elvesznek. |
| Műveletek figyelése üzenetek |Az összes olvasatlan üzenetek elvesznek. |
| Visszajelzés a felhőből az eszközre irányuló üzenetek |Az összes olvasatlan üzenetek elvesznek. |

<sup>1</sup>felhőből az eszközre irányuló üzenetek és a szülő feladatok beolvasása helyreállítása nem ez a funkció előzetes verziójára a manuális feladatátvétel részeként.

Az IoT hub a feladatátvételi művelet befejezése után az eszköz és a háttér-alkalmazások az összes művelet várható a manuális beavatkozás nélkül szeretné folytatni.

> [!CAUTION]
> - Az Event Hub-kompatibilis névvel és a végponthoz az IoT Hub beépített események végpont módosítsa a feladatátvételt követően. Ha telemetriai üzeneteket fogad az event hub-ügyfél vagy az event processor host használatával a beépített végpontról, akkor [használja az IoT hub kapcsolati karakterláncra](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) a kapcsolat létrehozásához. Ez biztosítja, hogy a háttér-alkalmazások továbbra is működik, anélkül, hogy manuális beavatkozásra a feladatátvétel után. Használja az Event Hub-kompatibilis névvel és a végpont a háttéralkalmazás közvetlenül, ha szüksége lesz az alkalmazás által újrakonfigurálása [beolvasása az új Event Hub-kompatibilis névvel és a végpont](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) folytatja a feladatátvétel után műveletek.
>
> - A feladatátvételt követően az Event Griden keresztül kibocsátott események felhasználhatók a korábban beállított mindaddig, amíg ezen Event Grid-előfizetések továbbra is elérhetők az azonos előfizetés(ek) keresztül.
>

### <a name="microsoft-initiated-failover"></a>A Microsoft által kezdeményezett feladatátvétel

A Microsoft által kezdeményezett feladatátvételi gyakorolja a Microsoft ritka esetekben feladatátvétel minden IoT hubs egy érintett régióból a megfelelő földrajzilag párosított régióba. Ez a folyamat egy alapértelmezett beállítás (nem lehet a felhasználók számára, kikapcsolhatja az újat), és nem igényel felhasználói beavatkozást a felhasználó elől. A Microsoft fenntartja a jogot arra, hogy a meghatározását, ha ezt a beállítást kerül sor. Ezt a mechanizmust nem járnak egy felhasználói beleegyezés, mielőtt a felhasználó hub feladatátvétel alatt áll. A Microsoft által kezdeményezett feladatátvételi rendelkezik a helyreállítási idő célkitűzése (RTO) 2 – 26 óra. 

A nagy RTO azért, hogy a Microsoft nevében az érintett ügyfeleket a feladatátvételi műveletet kell végrehajtania az adott régióban. Ha egy kevésbé fontos IoT-megoldás, amely nagyjából egy nap üzemkimaradást is kiálló tárolókkal futtatja, ok, hogy ezt a beállítást, az IoT-megoldás az általános vész helyreállítási célok teljesítéséhez a függőséget. Futásidejű fordított időt műveletek teljesen működőképes lesz, a folyamat akkor aktiválódik, ha a "Helyreállítása idő" részében leírt.

### <a name="manual-failover-preview"></a>Manuális feladatátvétel (előzetes verzió)

Ha üzemidő üzleti céljaihoz alapján az RTO, amely a Microsoft által kezdeményezett nem teljesült, feladatátvételt biztosít, érdemes manuális feladatátvétel használatával indítható el a feladatátvételi folyamat saját magának. Ezzel a beállítással az RTO bárhol, néhány óra múlva 10 perc között lehet. Az RTO jelenleg csak egy függvényt a regisztrálva az IoT hub-példány feladatátvétel alatt álló eszközök számát. Az RTO körülbelül 100 000 eszközt futtató hub kell lennie a 15 perces ballpark várható. Futásidejű fordított időt műveletek teljesen működőképes lesz, a folyamat akkor aktiválódik, ha a "Helyreállítása idő" részében leírt.

A manuális feladatátvételt lehetőség mindig használatra attól függetlenül, hogy az elsődleges régió üzemen kívül van vagy nem érhető el. Ezért ezt a beállítást potenciálisan használható tervezett feladatátvételek elvégzéséhez. Egy példa a használatra tervezett feladatátvételeket a, hogy rendszeres időközönként feladatátvételi gyakorlatok végrehajtása. Egy szót, járjon el, azonban, hogy a tervezett feladatátvételi művelet eredményeinek a hub állásidőt jelent ez a beállítás az RTO által meghatározott időszakban, és is egy az adatvesztést eredményez a fenti RPO-táblázat szerint. Érdemes lehet egy teszt beállítása IoT hub-példány rendszeres időközönként a bizalom arra, hogy másolatot a teljes körű megoldások és a egy valódi katasztrófa történik, ha fut a tervezett feladatátvétel lehetőséget.

> [!IMPORTANT]
> - Vizsgálat működése nem kell végrehajtani a IoT hubok az éles környezetben használt.
>
> - Manuális feladatátvétel nem használandó mechanizmusként véglegesen a a hub Azure földrajzilag párosított régiók közötti áttelepítéséhez. Ezzel egy nagyobb késéseket, a régi elsődleges régióban előbbi ellen a hub eszközökön végrehajtott műveletek miatt.
>
> - Manuális feladatátvétel jelenleg előzetes verzióban érhető el, és nem érhető el a következő Azure-régióban. USA keleti RÉGIÓJA, USA nyugati RÉGIÓJA, Észak-Európa, Nyugat-Európa, Dél-Brazíliában, USA déli középső RÉGIÓJA.

### <a name="failback"></a>Feladat-visszavétel

A régi elsődleges régióba sikertelen legyen elérhető a feladatátvételi művelet elindítása egy másik időpontot. Ha az eredeti feladatátvételi műveletet hajtottak végre a helyreállítás az eredeti elsődleges régióban kiterjesztett szolgáltatáskimaradás, javasoljuk, hogy az eredeti helyén a hub kell sikertelen, miután az adott helyen a szolgáltatáskimaradás helyzet állt.

> [!IMPORTANT]
> - A felhasználók csak számára engedélyezett 2 sikeres feladatátvételi és naponta 2 sikeres feladat-visszavételi műveletet végrehajtani.
>
> - Elejétől feladatátvétel és feladat-visszavételi műveletek nem engedélyezettek. Várjon, amíg ezek a műveletek közötti 1 óra felhasználók kell.

### <a name="time-to-recover"></a>Helyreállítási ideje

A teljes tartománynév (és így a kapcsolati karakterlánc), az IoT hub-példány továbbra is ugyanazt a feladatátvétel után, amíg az alapul szolgáló IP-cím változik. Ezért a teljesen működőképes lesz, a feladatátvételi folyamat indítása után az IoT hub-példány ellen végrehajtott futásidejű műveletek teljes időtartamának fejezhető ki a következő függvény használatával.

Helyreállítási ideje = RTO [10 perc – 2 óra kézi feladatátvételre |} a Microsoft által kezdeményezett feladatátvételi 2 – 26 óra] + DNS-propagálás késleltetés + frissítéséhez bármely, az ügyfélalkalmazás által igénybe vett idő a gyorsítótárba IoT-központ IP-címet.

> [!IMPORTANT]
> Az IoT SDK-k nem gyorsítótárazzák az IoT hub IP-címét. Azt javasoljuk, hogy felhasználói kód kapcsolatba, az SDK-k nem gyorsítótárazza az IoT hub IP-címét.

## <a name="achieve-cross-region-ha"></a>Magas rendelkezésre ÁLLÁSÚ régiók közötti elérése

Ha üzemidő üzleti céljaihoz által az RTO nem teljesül, a Microsoft által kezdeményezett feladatátvétel vagy kézi feladatátvételi beállításokat biztosít, fontolja meg egy eszközre irányuló automatikus régiók közötti adatátvételi mechanizmus megvalósításához.
Egy teljes körű IoT-megoldások üzembe helyezési topológiák kezelése nem ez a cikk foglalkozik. A cikk ismerteti a *regionális feladatátvétel* üzemi modell magas rendelkezésre állás és vészhelyreállítás helyreállítás céljából.

Egy regionális feladatátvétel modellben a megoldás biztonsági célból futtatások elsősorban az egyik adatközpont-helyen. Egy másodlagos IoT hubot és a háttérrendszer egy másik adatközpontba helyen vannak telepítve. Az IoT hub, az elsődleges régióban romlik a kimaradás vagy az elsődleges régió felé az eszköz a hálózati kapcsolat megszakad, ha az eszközök egy másodlagos végpontot használja. A megoldás rendelkezésre állási helyett egy adott régión belül tartózkodó régiók közötti feladatátvétel modell alkalmazásával növelheti. 

Magas szinten egy regionális feladatátvétel modell megvalósításához az IoT hubbal, meg kell az alábbi lépéseket:

* **Egy másodlagos IoT hub és az Útválasztás logikai eszköz**: Ha az elsődleges régióban szolgáltatás megszakad, eszközöket kell elindítania, csatlakozás a másodlagos régióhoz. A legtöbb szolgáltatások állapotközpontú természetéből szokás megoldás segítségével a rendszergazdák a régiók közötti feladatátvétel folyamat aktiválásához. A folyamat kézben tarthassa az eszközökre, az új végpont kommunikálni a legjobb módszer az, hogy rendszeresen ellenőrzi őket egy *recepciószolgálata* szolgáltatásba az aktuális aktív végpontot. A recepciószolgálata szolgáltatás lehet egy webalkalmazást, amely replikálja, és elérhető marad DNS-átirányítás módszerrel (például [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)).

   > [!NOTE]
   > IoT hub szolgáltatás állapota nem támogatott végponttípusnál az Azure Traffic Managerben. A javaslat, hogy a javasolt recepciószolgálata szolgáltatás integrálása az Azure traffic manager azáltal, hogy azt a végponti állapotadat-mintavétel API megvalósításához.

* **Identitás adatbázis replikációja**: használhatóvá válik, a másodlagos IoT hub tartalmaznia kell az összes eszközidentitások, amely a megoldás képes kapcsolódni. A megoldás kell megőrizni a biztonsági mentések georeplikált eszközidentitások, és feltölti őket a másodlagos IoT hub az eszközök aktív végpontja váltása előtt. Az IoT Hub eszköz identitás exportálási funkció akkor hasznos, ebben a környezetben. További információkért lásd: [az IoT Hub fejlesztői útmutató – eszközidentitás-jegyzék](iot-hub-devguide-identity-registry.md).

* **Logikai egyesítésével**: Ha az elsődleges régió újra elérhetővé válik, állapota és adatai, a másodlagos helyen létrehozott kell áttelepíteni az összes biztonsági másolatot az elsődleges régióba. Ez állapota és adatai többnyire kapcsolódnak eszközidentitások és -alkalmazás metaadatait, amelyet egyesíteni kell az elsődleges IoT hub és egyéb alkalmazásspecifikus tárolja az elsődleges régióban. 

Ebben a lépésben leegyszerűsítése idempotens műveleteket kell használnia. Idempotens műveletek minimalizálása érdekében a mellékhatásai modul, az események a végleges konzisztens terjesztési, illetve az ismétlődések vagy out soron kívüli kézbesíti az eseményeket. Emellett az alkalmazáslogika elviselni esetleges inkonzisztenciákat vagy kissé elavult állapotban kell megtervezni. Ez a helyzet akkor fordulhat elő, miatt a további szükséges időt a rendszer javítandó helyreállításipont-célkitűzések (RPO) alapján.

## <a name="choose-the-right-hadr-option"></a>Válassza ki a megfelelő HA/DR lehetőséget

Az alábbiakban egy magas rendelkezésre ÁLLÁS/Vészhelyreállítás beállítások összesítését jelenik meg ebben a cikkben, válassza ki a megfelelő megoldás, amely a megoldás működik, mint egy hivatkozási használható.

| HA/DR-beállítás | RTO | HELYREÁLLÍTÁSI IDŐKORLÁT | Manuális beavatkozásra van szükség? | Megvalósítás összetettségét | További költségek gyakorolt hatás|
| --- | --- | --- | --- | --- | --- | --- |
| A Microsoft által kezdeményezett feladatátvétel |2 – 26 óra|Tekintse meg a fenti RPO-táblázat|Nem|None|None|
| Manuális feladatátvétel |10 perc – 2 óra|Tekintse meg a fenti RPO-táblázat|Igen|Nagyon alacsony. Csak kell elindítani a műveletet a portálról.|None|
| Adatbázisközi régióban a magas rendelkezésre ÁLLÁS |< 1 perc|A replikálási gyakoriságot az egyéni magas rendelkezésre ÁLLÁSÚ megoldás függ|Nem|Magas|> 1 x 1, az IoT hub költsége|

## <a name="next-steps"></a>További lépések

Az alábbi hivatkozásokból tudhat meg többet az Azure IoT hubbal:

* [Ismerkedés az IoT hubok (Gyorsindítás)](quickstart-send-telemetry-dotnet.md)
* [Mi az Azure IoT Hub?](about-iot-hub.md)