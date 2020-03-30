---
title: Gyakorlati tanácsok az Azure Maps útvonalszolgáltatásához | Microsoft Azure Maps
description: Megtudhatja, hogyan irányíthat hatékonyan a Route Service segítségével a Microsoft Azure Maps szolgáltatásból.
author: philmea
ms.author: philmea
ms.date: 03/11/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 85ce29d088b8fbd110988db67776d89346215e5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335417"
---
# <a name="best-practices-for-azure-maps-route-service"></a>Gyakorlati tanácsok az Azure Maps Route szolgáltatáshoz

Az Azure Maps [Route Service](https://docs.microsoft.com/rest/api/maps/route) útvonalútvonal- és útvonalmátrix-API-jai az egyes kért útvonalak becsült érkezési idejének (ETA-k) kiszámításához használhatók. Az útvonal-API-k olyan tényezőket vesznek figyelembe, mint a valós idejű forgalmi információk és a korábbi forgalmi adatok, például a hét kért napján és a napszakban jellemző útsebességeket. Az API-k a legrövidebb vagy leggyorsabb útvonalakat adják vissza egyszerre több célállomásra egymás után vagy optimalizált sorrendben, az idő vagy a távolság alapján. A felhasználók is kérhetik speciális útvonalak és részletek a gyalogosok, kerékpárosok, és a haszongépjárművek, mint a teherautók. Ebben a cikkben az Azure Maps [Route-szolgáltatás](https://docs.microsoft.com/rest/api/maps/route)hívásának ajánlott módjait osztjuk meg, és megtudhatja, hogyan:

* Válasszon az Útvonalútvonal API-k és a Mátrixútválasztási API közül
* Történelmi és előre jelzett utazási idők kérése valós idejű és korábbi forgalmi adatok alapján
* Útvonaladatok kérése, például idő és távolság a teljes útvonalra és az útvonal minden egyes szakaszára vonatkozóan
* Útvonal igénylése haszongépjárműhöz, például teherautóhoz
* Forgalmi információk kérése egy útvonal mentén, például elakadások és útdíjadatok
* Egy vagy több megállóból (útpontokból) álló útvonal kérése
* Egy vagy több megálló útvonalának optimalizálása az egyes megállók (útpont) legjobb megnevezéséhez
* Optimalizálja az alternatív útvonalakat támogató pontok használatával. Például kínálnak alternatív útvonalakat, amelyek átmennek egy elektromos jármű töltőállomáson.
* Az [útvonalszolgáltatás](https://docs.microsoft.com/rest/api/maps/route) használata az Azure Maps Web SDK-val

## <a name="prerequisites"></a>Előfeltételek

Az Azure Maps API-k hívásához szüksége van egy Azure Maps-fiókra és egy kulcsra. További információt a [Fiók létrehozása](quick-demo-map-app.md#create-an-account-with-azure-maps) és az elsődleges kulcs [benyomása](quick-demo-map-app.md#get-the-primary-key-for-your-account)című témakörben talál. Az elsődleges kulcs is ismert, mint az elsődleges előfizetési kulcs, vagy előfizetési kulcs.

Az Azure Maps hitelesítésével kapcsolatos további tudnivalókért olvassa el a Hitelesítés kezelése az Azure Mapsben című [témakört.](./how-to-manage-authentication.md) Az útvonalszolgáltatás lefedettségéről pedig további információt az [Útvonal-lefedettség című](routing-coverage.md)témakörben talál.

Ez a cikk a [Postman alkalmazást](https://www.postman.com/downloads/) használja a REST-hívások létrehozásához, de bármelyik API-fejlesztői környezetet kiválaszthatja.

## <a name="choose-between-route-directions-and-matrix-routing"></a>Útvonalútvonalak és mátrixútválasztás közötti választás

Az Útvonalútvonal-API-k utasításokat adnak vissza, beleértve az utazási időt és az útvonalútvonal koordinátáit. A Route Matrix API lehetővé teszi az utazási idő és a távolságok kiszámítását a kiindulási és a célhelyek által meghatározott útvonalak készletéhez. A Matrix API minden adott forrás esetében kiszámítja az útvonal azadott forrástól az adott célig történő útválasztás költségét (utazási időt és távolságot). Mindezek az API-k lehetővé teszik olyan paraméterek megadását, mint például a kívánt indulási idő, az érkezési idő és a járműtípus, például az autó vagy a teherautó. Ezek mindegyike valós idejű vagy prediktív forgalmi adatokat használ a legoptimálisabb útvonalak visszaadásához.

Fontolja meg az Útvonalútvonal-API-k hívását, ha a forgatókönyv a következő:

* Kérje a legrövidebb vagy leggyorsabb vezetési útvonalat két vagy több ismert helyszín között, hogy pontos érkezési időket kapjon a szállítójárművekhez.
* Részletes útvonal-útmutatás kérése, beleértve az útvonalgeometriát is, az útvonalak térképen való megjelenítéséhez
* Adott az ügyfelek helyeinek listája, számítsa ki a lehető legrövidebb útvonalat, hogy látogassa meg az egyes ügyfelek helyét, és térjen vissza az eredeti. Ez a forgatókönyv közismert nevén az utazó ügynök probléma. Egy kérelemben legfeljebb 150 útpontot (megállót) adhat át.
* A lekérdezések kötegeit egyetlen API-hívással küldheti el a Route Directions Batch API-ba.

Fontolja meg a Matrix Routing API hívását, ha a forgatókönyv a következőket szeretné:

* Számítsa ki az utazási időt vagy a távolságot az eredeti helyek és úti célok között. Például, van 12 vezetők, és meg kell találni a legközelebbi rendelkezésre álló vezető, hogy vegye fel az élelmiszer-szállítás az étteremben.
* A lehetséges útvonalakat a tényleges utazási távolság uk vagy az idő szerint rendezheti. A Mátrix API csak az egyes kiindulási és célkombinációk utazási idejét és távolságait adja vissza.
* Fürtadatok az utazási idő vagy a távolságok alapján. A vállalat például 50 alkalmazottal rendelkezik, és az irodából 20 perc nél elérhető összes alkalmazottat megkeresi.

Az alábbiakban az Útvonalútvonalak és a Mátrix API-k néhány képességének megjelenítéséhez hasonlít:

| Azure Maps API | A lekérdezések maximális száma a kérelemben | Kerülje a területeket | Tehergépkocsi és elektromos járművek irányítása | útpontok és utazó ügynök optimalizálása | Támogató pontok |
| :--------------: |  :--------------: |  :--------------: | :--------------: | :--------------: | :--------------: |
| Útvonalútvonal-utasítások beszereznie | 1 | | X | X | |
| Útvonalútvonal-könyvelés | 1 | X | X | X | X |
| Útvonalútvonal-köteg feladása | 700 | | X | X | |
| Útvonalmátrix feladása | 700 | | X | | |

Ha többet szeretne megtudni az elektromos járművek útválasztási képességeiről, tekintse meg az elektromos járművek továbbítását az [Azure Notebookok Pythonnal történő irányításáról.](tutorial-ev-routing.md)

## <a name="request-historic-and-real-time-data"></a>Történelmi és valós idejű adatok kérése

Alapértelmezés szerint az Útvonal szolgáltatás feltételezi, hogy az utazási mód egy autó, és az indulási idő most van. Valós idejű forgalmi feltételek alapján adja vissza az útvonalat, kivéve, ha az útvonalszámítási kérelem másként határoz meg. Rögzített időfüggő forgalmi korlátozások, mint például a "Bal kanyarok nem megengedett között 4:00-18:00" rögzítik, és figyelembe veszi az irányító motor. Az útlezárásokat, például az útépítéseket figyelembe vesszük, kivéve, ha kifejezetten olyan útvonalat kér, amely figyelmen kívül hagyja az aktuális élő forgalmat. Az aktuális forgalom figyelmen kívül hagyásához állítsa be `traffic` az `false` API-kérelemben.

Az útvonalszámítás **travelTimeInSeconds** értéke tartalmazza a forgalom miatti késést. Ez az aktuális és a korábbi utazási idő adatok kihasználásával jön létre, amikor az indulási idő most van beállítva. Ha az indulási idő a jövőben van beállítva, az API-k a korábbi adatok alapján adják vissza az előre jelzett utazási időket.

Ha a kérésben szerepel a **computeTravelTime=all** paraméter, akkor a válasz összefoglaló eleme a következő további mezőket tartalmazza, beleértve a korábbi forgalmi feltételeket is:

| Elem | Leírás|
| :--- | :--- |
| noTrafficTravelTimeInSeconds | Becsült utazási idő, amelyet úgy számítanak ki, mintha a forgalmi viszonyok miatt nem lenne késés az útvonalon, például a torlódás oka miatt |
| történelmiTrafficTravelTimeInSeconds | Időfüggő múltbeli forgalmi adatok alapján számított becsült utazási idő |
| élőTrafficIncidentsTravelTimeInSeconds | Valós idejű sebességadatok alapján számított becsült utazási idő |

A következő szakaszok bemutatják, hogyan lehet hívásokat kezdeményezni az útvonal API-k a tárgyalt paraméterek használatával.

### <a name="sample-query"></a>Mintalekérdezés

Az első példa alatt az indulási idő van beállítva, hogy a jövőben, abban az időben az írás.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=51.368752,-0.118332:51.385426,-0.128929&travelMode=car&traffic=true&departAt=2025-03-29T08:00:20&computeTravelTimeFor=all
```

A válasz tartalmaz egy összefoglaló elemet, mint az alábbi. Mivel az indulási idő a jövőre van állítva, a **trafficDelayInSeconds** érték nulla. A **travelTimeInSeconds** értéket időfüggő történelmi forgalmi adatok alapján számítja ki a rendszer. Tehát ebben az esetben a **travelTimeInSeconds** érték megegyezik a **historicTrafficTravelTimeInSeconds** értékkel.

```json
"summary": {
    "lengthInMeters": 2131,
    "travelTimeInSeconds": 248,
    "trafficDelayInSeconds": 0,
    "departureTime": "2025-03-29T08:00:20Z",
    "arrivalTime": "2025-03-29T08:04:28Z",
    "noTrafficTravelTimeInSeconds": 225,
    "historicTrafficTravelTimeInSeconds": 248,
    "liveTrafficIncidentsTravelTimeInSeconds": 248
},
```

### <a name="sample-query"></a>Mintalekérdezés

Az alábbi második példában valós idejű útvonal-műveletet szeretnénk, ahol az indulási idő most van. Nincs kifejezetten megadva az URL-ben, mert ez az alapértelmezett érték.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=47.6422356,-122.1389797:47.6641142,-122.3011268&travelMode=car&traffic=true&computeTravelTimeFor=all
```

A válasz az alábbiak szerint összefoglalót tartalmaz. Torlódások miatt a **trafficDelaysInSeconds** értéke nagyobb, mint nulla. Ez is nagyobb, mint **historicTrafficTravelTimeInSeconds**.

```json
"summary": {
    "lengthInMeters": 16637, 
    "travelTimeInSeconds": 2905, 
    "trafficDelayInSeconds": 1604, 
    "departureTime": "2020-02-28T01:00:20+00:00",
    "arrivalTime": "2020-02-28T01:48:45+00:00", 
    "noTrafficTravelTimeInSeconds": 872, 
    "historicTrafficTravelTimeInSeconds": 1976, 
    "liveTrafficIncidentsTravelTimeInSeconds": 2905 
},
```

## <a name="request-route-and-leg-details"></a>Útvonal- és lábrészletek kérése

Alapértelmezés szerint az Útvonal szolgáltatás egy koordinátatömböt ad vissza. A válasz a görbét a elérési utat összeadó koordinátákat tartalmazza a . `points` Az útvonal-reagálás magában foglalja az útvonal kezdetétől és a becsült eltelt időtől való távolságot is. Ezek az értékek a teljes útvonal átlagsebességének kiszámítására használhatók.

Az alábbi képen `points` látható az elem.

<center>

![pontlista](media/how-to-use-best-practices-for-routing/points-list-is-hidden-img.png)

</center>

Bontsa ki az `point` elemet az elérési út koordinátáinak listájának megtekintéséhez:

<center>

![pontlista](media/how-to-use-best-practices-for-routing/points-list-img.png)

</center>

Az Útvonalirányok API-k az **instructionsType** paraméter megadásával használható utasítások különböző formátumait támogatják. Az egyszerű számítógép-feldolgozásra vonatkozó utasítások formázásához használja az **instructionsType=coded parancsot.** Az **instructionsType=tagged** használatával az utasításokat szövegként jelenítheti meg a felhasználó számára. Az utasítások szövegként is formázhatók, ahol az utasítások egyes elemei vannak megjelölve, és az utasítás speciális formázással jelenik meg. További információt a [támogatott utasítástípusok listájában talál.](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#routeinstructionstype)

Utasítások kérésekor a válasz egy új `guidance`elemet ad vissza, melynek neve . Az `guidance` elem két információt tartalmaz: részletes útmutatást és összefoglaló utasításokat.

<center>

![Utasítások típusa](media/how-to-use-best-practices-for-routing/instructions-type-img.png)

</center>

Az `instructions` elem rendelkezik turn-by-turn irányban `instructionGroups` az utazás, és az összefoglalta utasításokat. Minden utasítás összefoglalója az utazás egy olyan szakaszára vonatkozik, amely több utat is lefedhet. Az API-k az útvonal szakaszainak részleteit is visszaadhatják. például a forgalmi dugó koordináta-tartománya vagy a forgalom aktuális sebessége.

<center>

![Kapcsolja be a fordulási utasításokat](media/how-to-use-best-practices-for-routing/instructions-turn-by-turn-img.png)

</center>

<center>

![Összefoglalva utasítások](media/how-to-use-best-practices-for-routing/instructions-summary-img.png)

</center>

## <a name="request-a-route-for-a-commercial-vehicle"></a>Útvonal igénylése haszongépjárműhöz

Az Azure Maps routing API-k támogatják a haszongépjárművek útválasztását, amely a haszongépjárművek útválasztását fedi le. Az API-k figyelembe veszik a megadott korlátokat. Mint például a jármű magassága és súlya, és ha a jármű veszélyes rakományt szállít. Ha például egy jármű gyúlékony anyagot szállít, az útválasztási motor elkerül bizonyos, lakóövezetek közelében lévő alagutakat.

### <a name="sample-query"></a>Mintalekérdezés

Az alábbi mintakérelem egy kereskedelmi teherautó útvonalát kérdezi le. A teherautó 1-es osztályú veszélyes hulladékot szállít.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass1&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

A Route API olyan utasításokat ad vissza, amelyek befogadják a teherautó méreteit és a veszélyes hulladékot. Az útvonalutasításokat az `guidance` elem kibontásával olvashatja el.

<center>

![Teherautó 1-es osztályú hazwaste](media/how-to-use-best-practices-for-routing/truck-with-hazwaste-img.png)

</center>

### <a name="sample-query"></a>Mintalekérdezés

Ha módosítja az amerikai Hazmat osztályt a fenti lekérdezésből, akkor a módosítás nak más útvonalat kell választania.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass9&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

Az alábbi válasz egy 9-es osztályú veszélyes anyagot szállító teherautóra van, amely kevésbé veszélyes, mint az 1- es osztályú veszélyes anyag. Amikor kibontja az `guidance` elemet, hogy olvassa el az utasításokat, észre fogod venni, hogy az irányok nem azonosak. Az 1-es osztályú veszélyes anyagot szállító teherautóra további útvonal-utasítások tartoznak.

<center>

![9-es osztályú teherautó hazwaste](media/how-to-use-best-practices-for-routing/truck-with-hazwaste9-img.png)

</center>

## <a name="request-traffic-information-along-a-route"></a>Forgalmi információk kérése egy útvonal mentén

Az Azure Maps Route Direction API-k, a fejlesztők kérhetik az egyes szakasztípusok részleteit a `sectionType` paraméter a kérelemben. Például kérheti a sebességre vonatkozó információkat az egyes forgalmi dugók szegmenseiről. A [sectionType kulcs értéklistáját a](https://docs.microsoft.com/rest/api/maps/route/getroutedirections#sectiontype) kérésre vonatkozó különböző részletekről tájékozódhat.

### <a name="sample-query"></a>Mintalekérdezés

A következő lekérdezés `sectionType` `traffic`a . Kéri azokat a szakaszokat, amelyek forgalmi információkat tartalmaznak Seattle-ből San Diego-ba.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&sectionType=traffic&query=47.6062,-122.3321:32.7157,-117.1611
```

A válasz tartalmazza azokat a szakaszokat, amelyek alkalmasak a megadott koordináták mentén a forgalomra.

<center>

![forgalmi szakaszok](media/how-to-use-best-practices-for-routing/traffic-section-type-img.png)

</center>

Ez a beállítás a szakaszok színezésére használható a térkép renderelésekor, mint az alábbi képen: 

<center>

![forgalmi szakaszok](media/how-to-use-best-practices-for-routing/show-traffic-sections-img.png)

</center>

## <a name="calculate-and-optimize-a-multi-stop-route"></a>Többállomásos útvonal kiszámítása és optimalizálása

Az Azure Maps jelenleg az útvonaloptimalizálás két formáját kínálja:

* Optimalizálás a kért útvonal típusa alapján, az útpontok sorrendjének módosítása nélkül. A támogatott [útvonaltípusokat itt találja.](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#routetype)

* Utazó eladó optimalizálás, amely megváltoztatja a sorrendben az útpontok, hogy a legjobb, hogy látogassa meg az egyes megállók

Többállomásos útválasztás esetén legfeljebb 150 útpont adható meg egyetlen útvonalkérelemben. A kezdő és záró koordináta-helyek megegyezhetnek, mint az oda-vissza út esetében. Az útvonal számításához azonban legalább egy további útpontot meg kell adnia. Útpontok adhatók hozzá a lekérdezéshez a kiindulási és a célkoordináták között.

Ha optimalizálni szeretné a legjobb sorrendet az adott útpontok meglátogatásához, akkor meg kell **adnia a computeBestOrder=true**értéket. Ez a forgatókönyv is ismert, mint az utazó ügynök optimalizálási probléma.

### <a name="sample-query"></a>Mintalekérdezés

A következő lekérdezés hat útpont elérési `computeBestOrder` útját kéri, a paraméter beállítása `false`. Ez is a `computeBestOrder` paraméter alapértelmezett értéke.

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=false&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

A válasz leírja az útvonal hossza, hogy 140.851 méter, és hogy lenne 9991 másodperc alatt utazni, hogy az utat.

<center>

![Nem optimalizált válasz](media/how-to-use-best-practices-for-routing/non-optimized-response-img.png)

</center>

Az alábbi képen a lekérdezés ből származó elérési út látható. Ez az elérési út az egyik lehetséges útvonal. Ez nem az optimális út az idő vagy a távolság alapján.

<center>

![Nem optimalizált kép](media/how-to-use-best-practices-for-routing/non-optimized-image-img.png)

</center>

Ez az útvonal útpont sorrend: 0, 1, 2, 3, 4, 5 és 6.

### <a name="sample-query"></a>Mintalekérdezés

A következő lekérdezés ugyanazt a hat útpontot kéri, mint a fenti mintában. Ez alkalommal `computeBestOrder` a paraméter `true` beállítása (az utazó ügynök optimalizálása).

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=true&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

A válasz azt írja le, hogy az útvonal hossza 91 814 méter, és hogy 7797 másodpercbe telik, amíg ezen az úton halad. Az utazási távolság és az utazási idő egyaránt alacsonyabb itt, mert az API visszaadta az optimalizált útvonalat.

<center>

![Nem optimalizált válasz](media/how-to-use-best-practices-for-routing/optimized-response-img.png)

</center>

Az alábbi képen a lekérdezés ből származó elérési út látható.

<center>

![Nem optimalizált kép](media/how-to-use-best-practices-for-routing/optimized-image-img.png)

</center>

Az optimális útvonal sorrendje a következő: 0, 5, 1, 2, 4, 3 és 6.

>[!TIP]
> Az Útválasztás szolgáltatás optimalizált útpont-rendelési adatai indexeket tartalmaznak. Ezek nem tartalmazzák a származási és a célindexeket. Ezeket az értékeket 1-re kell megvásárolnia az origóhoz. Ezután adja hozzá az úticélt a végére, hogy megkapja a teljes rendezett útpont listát.

## <a name="calculate-and-bias-alternative-routes-using-supporting-points"></a>Alternatív útvonalak kiszámítása és torzítása alátámasztó pontok használatával

Előfordulhatnak olyan helyzetek, amikor egy útvonalat egy referenciaútvonal nulla vagy több alternatív útvonalának kiszámításához szeretne rekonstruálni. Előfordulhat például, hogy alternatív útvonalakat szeretne megjeleníteni a vevőknek, amelyek megfelelnek a kiskereskedelmi üzletnek. Ebben az esetben a helyeket támogató pontok használatával kell elfogulttá tenni. A hely elfogultságának lépései:

1. Útvonal kiszámítása a következőképpen, és az útvonal-válasz bekéselése
2. Az útvonalútvonal segítségével keresse meg a kívánt helyeket az útvonal útvonala mentén vagy közelében. Használhatja például az Azure Maps [érdekes ségi pontot API-t,](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) vagy lekérdezheti a saját adatait az adatbázisban.  
3. Rendelje meg a helyeket az útvonal kezdetétől való távolság alapján
4. Adja hozzá ezeket a helyeket támogató pontként egy új útvonalkérelemhez a [Post Route Directions API-hoz.](https://docs.microsoft.com/rest/api/maps/route/postroutedirections) A támogató pontokról a [Post Route Directions API dokumentációjában](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#supportingpoints)olvashat bővebben. 

Az [Útvonal könyvelése API hívásakor beállíthatja](https://docs.microsoft.com/rest/api/maps/route/postroutedirections)a minimális eltérési időt vagy a távolsági kényszereket, valamint a támogató pontokat. Használja ezeket a paramétereket, ha alternatív útvonalakat szeretne kínálni, de korlátozni szeretné az utazási időt is. Ha ezeket a korlátozásokat használja, az alternatív útvonalak a referenciaútvonalat követik a kiindulási ponttól az adott időre vagy távolságra. Más szóval a többi útvonal a megadott korlátozások szerint eltér a referenciaútvonaltól.

Az alábbi kép egy példa az alternatív útvonalak renderelésére, meghatározott eltérési korlátokkal az idő és a távolság tekintetében.

<center>

![Alternatív útvonalak](media/how-to-use-best-practices-for-routing/alternative-routes-img.png)

</center>

## <a name="use-the-routing-service-in-a-web-app"></a>Az Útválasztás szolgáltatás használata webalkalmazásban

Az Azure Maps Web SDK [szolgáltatásmodult](https://docs.microsoft.com/javascript/api/azure-maps-rest/?view=azure-maps-typescript-latest)biztosít. Ez a modul egy segítő könyvtár, amely megkönnyíti az Azure Maps REST API-k használatát a webes vagy node.js alkalmazásokban JavaScript vagy TypeScript használatával. A Szolgáltatás modul segítségével renderelheti a visszaadott útvonalakat a térképen. A modul automatikusan meghatározza, hogy melyik API-t használja a GET és a POST kérelmekkel.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni, kérjük, olvassa el:

> [!div class="nextstepaction"]
> [Azure Maps Route szolgáltatás](https://docs.microsoft.com/rest/api/maps/route)

> [!div class="nextstepaction"]
> [A Szolgáltatás modul használata](https://docs.microsoft.com/azure/azure-maps/how-to-use-services-module)

> [!div class="nextstepaction"]
> [Útvonal megjelenítése a térképen](https://docs.microsoft.com/azure/azure-maps/map-route)

> [!div class="nextstepaction"]
> [Az Azure Maps NPM-csomagja](https://www.npmjs.com/package/azure-maps-rest  )
