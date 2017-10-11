---
title: "Webalkalmazás-alkalmazások teljesítményének figyelését - Azure Application Insights |} Microsoft Docs"
description: Hogyan illeszkedik az Application Insights a devOps ciklus
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 479522a9-ff5c-471e-a405-b8fa221aedb3
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 03/14/2017
ms.author: bwren
ms.openlocfilehash: de94633cabaa7a1562a5a4839a8a8924da91a283
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="deep-diagnostics-for-web-apps-and-services-with-application-insights"></a>Webalkalmazások és szolgáltatások részletes diagnosztikája az Application Insights szolgáltatással
## <a name="why-do-i-need-application-insights"></a>Miért kell az Application Insights?
Az Application Insights figyeli a futó webes alkalmazást. Jelzi, hogy kapcsolatos hibáiról és teljesítményproblémáiról, és segít elemzése, hogy az ügyfelek hogyan használják az alkalmazást. (J2EE, az ASP.NET, Node.js,...) számos platformon futó alkalmazások működését, és a Felhőbeli vagy helyszíni van-e tárolva. 

![Webalkalmazások elérése összetettségi szempontok](./media/app-insights-devops/010.png)

Fontos, figyelheti a modern alkalmazások futtatása. A legfontosabb amelyet észlelni szeretne hibák az ügyfelek többsége előtt. Is szeretné felderíteni, és a teljesítmény kapcsolatos problémák megoldása, amelyek, amíg nem végzetes, lehet, hogy lassítják a gépet, vagy bizonyos kellemetlenségért miatt a felhasználók számára. És a rendszer az megfelelően működik, ha szeretné tudni, hogy a felhasználók tevékenységeit vele: használnak a szolgáltatás legújabb verziója? Ezek sikeresen lezajlottak vele?

Modern webalkalmazások fejlesztett folyamatos szállítási ciklusban: kiadás egy új funkció vagy fokozása; Figyelje meg, milyen jól működik a felhasználó használhatja; Tervezze meg a következő biztonsági fejlesztési alapján ezt az információt. Ez a ciklus kulcsfontosságú része a megfigyelési fázis. Az Application Insights biztosítják azokat az eszközöket, a teljesítmény- és használati egy webalkalmazás figyelésére szolgáló.

Az egyik legfontosabb szempontja, hogy ez a folyamat diagnosztika és elemzés céljából. Ha az alkalmazás nem, majd üzleti alatt elvesznek. Az elsődleges figyelési keretrendszer szerepköre ezért észlelésére megbízhatóan, azonnal értesíti, és jelenthet Ön számára a probléma diagnosztizálása érdekében szükséges információkat. Ez az pontosan, az Application Insights funkciója.

### <a name="where-do-bugs-come-from"></a>Honnan származnak hibák?
A webes rendszerek hibák jellemzően akkor keletkeznek, konfigurációs problémák vagy a számos összetevő közötti hibás interakciókat. Az első tevékenységet, ha egy élő webhelyet incidens elleni ezért a helyi, a probléma azonosításához: mely összetevő vagy a kapcsolat okozza-e?

Néhány velünk, szürke haj rendelkező emlékszik egy egyszerűbb éra egy számítógép a programot futtatásakor. A fejlesztők akkor tesztelheti alaposan; mielőtt és azt, hogy rendszerrel szállított volna ritkán tekintse meg vagy gondolja át azt újra. A felhasználók és a maradék hibák sok éve put kellene. 

Minden olyan így nagyon különböző most. Az alkalmazás futtatásához különböző eszközök formátum rendelkezik, és garantálja az egyes pontos viselkedést nehézkes lehet. A felhőben található alkalmazásokat futtató azt jelenti, hogy gyors hibák lehet meghatározni, de azt is jelenti, folyamatos versenyt és a várt eredmény az új funkciók gyakori időközönként. 

Ezeket a feltételeket, a csak egy fixen szabályozása a hiba száma, az automatikus egységek tesztelése. Nem lehet manuálisan újra mindent teszteljen minden kézbesítés lenne. Egységteszt mostantól az összeállítási folyamat alkotómunkájának részét. Eszközök, például a Xamarin teszt felhő, adja meg a böngésző-verziókban tesztelés automatizált felhasználói felület segítségével. A tesztelési rendszerek engedélyezése, hogy legyen, hogy legalább egy alkalmazás belül található hibák mértéke kell tartani.

Tipikus webalkalmazások számos élő összetevő rendelkezik. Az ügyfél (alkalmazásban egy böngésző vagy eszköz) és a webkiszolgáló kívül nincs valószínűleg jelentős háttérbeli feldolgozás. Lehet, hogy a háttér egy folyamatot, az összetevők, vagy együttműködés darab lazább gyűjteménye. És ezek a vezérlőben nem lehet – külső, amelyen függő szolgáltatások fontosságúak.

Ezek például konfigurációk azok nehéz és teszteléséhez, vagy várható, minden lehetséges hibaállapotba, az élő rendszer magát a uneconomical. 

### <a name="questions-"></a>Kérdés...
Jelenleg folyamatban fejlesztésekor, hogy egy webes rendszer megkérjük kérdések merülhetnek fel:

* Saját alkalmazás összeomló? 
* Pontosan történtekről? -Ha a kérelem sikertelen volt, szeretnék tudni, hogyan, nem kapott. Igazolnia kell a nyomkövetési események...
* Elég gyorsan megnövelni az alkalmazásom? Mennyi időt vesz igénybe a tipikus kérelmek megválaszolásához?
* A kiszolgáló képes kezelni tudja a terhelést? A kérelmek száma nő, amikor azonban a válaszidő tartalmaz állandó?
* Hogyan teheti gyorsabban kezelhetővé vannak a függőségek - a REST API-k, adatbázisok és más összetevőket, meghívja a saját alkalmazás. Ebben az esetben ha a rendszer lassú, akkor a összetevő, vagy jelenik meg lassú válasz a valaki más?
* Felfelé vagy lefelé, az alkalmazásom? Ez látható a világszerte? Értesítsen Ha leállítása...
* Mi az az alapvető OK? Az összetevő vagy egy függőségi hiba volt? Ennyi az egész egy kommunikációs hiba?
* Hány felhasználó érintett? Ha egynél több probléma-kiszolgálókon, ami a legfontosabb van?

## <a name="what-is-application-insights"></a>Mi az Application Insights?
![Az Application Insights alapvető munkafolyamata](./media/app-insights-devops/020.png)

1. Az Application Insights eszközök az alkalmazást, és elküldi a telemetriai adatainak azt, az alkalmazás futtatása közben. Az Application Insights SDK hozhat létre az alkalmazásba vagy futásidőben instrumentation is alkalmazhat. Az első módszer nem rugalmasabb, mert a rendszeres modulok adhat hozzá a saját telemetriai adatokat.
2. A telemetriai adatokat küld az Application Insights portáljáról, amennyiben tárolása és feldolgozása. (Bár az Application Insights a Microsoft Azure-ban üzemel, figyelheti minden webes alkalmazás - csak Azure apps.)
3. A telemetriai események táblák és diagramok formájában a áll rendelkezésre.

Telemetria két fő típusa van: nyers, összesített és példányok. 

* Példányok adatait tartalmazza, például a webes alkalmazás által fogadott kérelem jelentést. Keresse meg a, és vizsgálja meg a keresési eszközzel az Application Insights portálon találja meg a kérelem részletes adatait. A példány adatok, például, hogy mennyi ideig az alkalmazás által a kérelmet, valamint a kért URL-cím válaszol, hozzávetőleges helyét az ügyfél és egyéb adatokat tartalmazhat.
* Összesített adatokat tartalmaz események száma egység idő összesítése összehasonlíthatja a válaszidők rendelkező kérelmek száma. A kérelem válaszidejének például mérőszámok átlagok is tartalmaz.

A fő kategóriába az adatok a következők:

* Az alkalmazáshoz (általában a HTTP-kérések) adatokkal URL-címet, a válaszidőt, és a sikeres vagy sikertelen kérelmek száma.
* Függőségek - által az alkalmazás, is URI, válaszidejét és sikerességi REST és SQL hívások
* Kivételek, beleértve a híváslánc megjelenik.
* Lapmegtekintések adatainak, amely a felhasználók böngészőjének származhat.
* Például a teljesítményszámlálók metrikák, valamint metrikák lehet írni. 
* Egyéni események, amelyek segítségével nyomon követheti az üzleti eseményeket
* A naplókivonatokat hibakereséshez.

## <a name="case-study-real-madrid-fc"></a>Esettanulmány: Valós Madridi F.C.
A webszolgáltatás a [valós Madridi bemutatjuk Club](http://www.realmadrid.com/) világszerte készül 450 millió ventilátorok szolgál. Ventilátorok férni a webböngészők és a Club mobilalkalmazások keresztül. Ventilátorok is nemcsak lefoglalhatja a jegyektől, de is elérhetik az adatokat és a videó videóklipeket eredmények, a játékosok és a közelgő játékok. Akkor is keresési szűrők célok számú program pontozza a mennyiségeket. Közösségi mutató hivatkozásokat is vannak. A felhasználói élmény nagymértékben testreszabott, és ventilátorok végezhetnek szolgál, amely kétirányú kommunikációt.

A megoldás [egy rendszer, a szolgáltatások és alkalmazások a Microsoft Azure](https://www.microsoft.com/en-us/enterprise/microsoftcloud/realmadrid.aspx). A méretezhetőség azt a kulcsfontosságú követelmény: forgalom változó, és képes elérni nagyon nagy kötetek során, és megfelel körül.

Valós Madrid, létfontosságú a rendszer teljesítményének figyelése. Azure Application Insights lehetővé teszi az átfogó képet kaphat a rendszer a megbízható és magas szintű szolgáltatást biztosítva. 

A Club is lekérdezi a ventilátorok részletes ismertetése: hol vannak (csak 3 % vannak Spanyolország), milyen érdeklődési van lejátszókról, korábbi eredmények jövőbeli játékok és hogyan válaszoljanak eredmények kereséséhez.

A telemetriai adatok automatikusan összegyűjtött nem hozzáadott kódra, amely a megoldás egyszerűsített, és működési összetettsége csökken.  Valós Madridi, az Application Insights foglalkozik 3.8 milliárd telemetriai pontok minden hónapban.

Valós Madridi használja a Power BI-modul a telemetriai adatok megtekintéséhez.

![Az Application Insights telemetria ábrázolása a Power BI](./media/app-insights-devops/080.png)

## <a name="smart-detection"></a>Intelligens észlelése
[Proaktív diagnosztika](app-insights-proactive-diagnostics.md) legutóbbi szolgáltatása. Ön semmiféle speciális beállítást nélkül a Application Insights automatikusan észleli, és értesítést kaphat a az alkalmazás hiba díjszabás szokatlan emelkedést. Intelligens ahhoz alkalmi hibák, valamint is, amelyek egyszerűen arányos megnövekedhet a kérelmek emelkedést háttér figyelmen kívül. Így például ha hiba történik az Ön függő szolgáltatások egyike, vagy ha az új build csak telepített nem működik úgy is, akkor tudhatja, információt, amint az e-maileket tekinti meg. (És webhookokkal, hogy más alkalmazásokat indíthat el.)

Ez a szolgáltatás egy másik aspektusa hajt végre a telemetriai, teljesítmény, amelyeket nehéz felderíteni szokatlan mintáinak keres egy napi részletes elemzéséhez. Például egy adott földrajzi területen, vagy egy adott böngészőverzió kapcsolódó teljesítménycsökkenést talál.

Mindkét esetben a riasztás nem csak azt ismerteti, a jelenség fel van derítve, de is lehetővé teszi az adatokat, például a jelentések vonatkozó kivételt a probléma diagnosztizálása érdekében.

![A proaktív diagnosztika e-mailekhez](./media/app-insights-devops/030.png)

Ügyfél Samtec említett: "cutover legutóbbi funkció során észleltünk egy alapján méretezi adatbázis elérte-e az erőforrás-korlátozások és időtúllépéseket okoz. A proaktív azonosítási riasztások érkezett hirdetett azt volt triaging a problémát, nagyon közel valós idejű szó. Ez a riasztás az Azure platformon riasztások alapján kialakulhat segített velünk szinte azonnal hárítsa el a problémát. Összesített állásidő < 10 perc."

## <a name="live-metrics-stream"></a>Élő Stream metrikák
A legújabb buildjével telepítése lehet törekedve arra élményt. Bármely problémák vannak, ha meg szeretné ismerni róluk azonnal, úgy, hogy szükség esetén készítsen biztonsági. Élő metrikák adatfolyam lehetővé teszi az alapvető metrikákat körülbelül egy második késéssel.

![Élő metrikák](./media/app-insights-devops/040.png)

És lehetővé teszi, hogy azonnal ellenőrizze a hibákat és kivételeket minta.

![Élő hibaesemények](./media/app-insights-devops/live-stream-failures.png)

## <a name="application-map"></a>Alkalmazástérkép
Alkalmazás-hozzárendelés automatikusan észleli a az alkalmazás topológiát, a teljesítményadatok azt, hogy könnyen azonosíthassa a teljesítmény szűk keresztmetszetei és a problematikus adatfolyamok teljes az elosztott környezet felett elrendezése. Lehetővé teszi a Azure-szolgáltatásokra alkalmazásfüggőségek észlelése. Ha kód kapcsolatos vagy függőségi kapcsolódó és a kapcsolódó diagnosztikai egy egyetlen hely részletek tapasztalnak osztályozhatja is a probléma által ismertetése. Például az alkalmazás sikertelenségét miatt teljesítménycsökkenést SQL rétegben. Az alkalmazás-hozzárendelés tekintse meg azonnal, és elemezze az SQL-Index Advisor, vagy lekérdezési merülhetnek fel.

![Alkalmazástérkép](./media/app-insights-devops/050.png)

## <a name="application-insights-analytics"></a>Application Insights elemzés
A [Analytics](app-insights-analytics.md), tetszőleges lekérdezéseket írhat egy hatékony SQL-szerű nyelven.  A teljes alkalmazás vermen keresztül diagnosztizálása lesz könnyen, különböző szempontok szerint felveheti a kapcsolatot, és kérje meg a megfelelő kérdéseket tehesse fel szolgáltatás teljesítményének összefüggéseket üzleti mérőszámok és a felhasználói élmény. 

Lekérheti az telemetria-példány és a portálon tárolt metrika nyers adatok. A nyelvi szűrő, illesztési, összesítési és más műveleteket tartalmaz. Kiszámíthatja a mezők és statisztikai elemzést. Nincsenek a tabular és a grafikus képi megjelenítések.

![Lekérdezés- és eredmények diagramra](./media/app-insights-devops/025.png)

Például is könnyen:

* Az alkalmazás kérelem teljesítményadatokat szegmentálja ügyfél rétegek a felhasználói élmény megértése.
* Keresés adott hibakódok vagy egyéni esemény élő webhelyet vizsgálatok során.
* Részletekbe menően tárhatják fel annak megértése, hogyan az szolgáltatások szerezte be, illetve elfogadott adott ügyfélnek az alkalmazás használatát.
* Nyomon követheti a munkamenetek és a támogatási és műveletek csapat azonnali ügyfél támogatásához engedélyezése adott felhasználói válaszidejét.
* Határozza meg a gyakran használt Alkalmazásfunkciók szolgáltatás rangsorolási kérdések megválaszolása.

Ügyfél DNN említett: "Application Insights nyújtott velünk hiányzó része a egyenlet ahhoz, hogy meg tudja csoportosítás, rendezés, lekérdezés és adatok szűrése igény szerint. A csapat használatát a saját nyújt, és a felhasználói élmény adatok hatékony lekérdezési nyelv lehetővé tette az elemzések keresése és problémák megoldására, így nem is tudjuk volt. Nagy mennyiségű érdekes válaszok származhat kezdve a kérdések *"I lett, ha...".*"

## <a name="development-tools-integration"></a>Fejlesztői eszközök integráció
### <a name="configuring-application-insights"></a>Az Application Insights konfigurálása
A Visual Studio és az Eclipse rendelkezik konfigurálása a megfelelő SDK csomagokat a projekthez, fejlesztői eszközöket. Az Application Insights hozzáadása menüparancs van.

Használja például Log4N, NLog vagy a System.Diagnostics.Trace nyomkövetési naplózási keretrendszer fordulhat elő, ha majd kap tudják elküldeni a naplókat az Application Insights egyéb telemetriai adatok, valamint a beállítást, hogy a nyomkövetéseket az által, függőség egyszerűen hozhatók hívások, és kivételeket.

### <a name="search-telemetry-in-visual-studio"></a>A Visual Studio keresési telemetria
Fejlesztési, és egy szolgáltatás hibakeresési, miközben tekintheti meg és keressen a telemetriai adatok közvetlenül a Visual Studio, a azonos keresési funkciók hasonlóan a web portal használatával.

És az Application Insights kivétel naplózza, ha az adatpont a Visual Studio tekintheti meg és rögtön a megfelelő kódot ugorhat.

![A Visual Studio-keresés](./media/app-insights-devops/060.png)

Hibakeresési, lehetősége van a telemetriai adatok megtartja a fejlesztői számítógépén, megtekintés, a Visual Studióban, de a portál küldés nélkül. A helyi beállítás elkerüli a termelési telemetriai feltárására keverése.

### <a name="build-annotations"></a>Jegyzetek létrehozása
Ha a Visual Studio Team Services segítségével hozza létre és telepítse az alkalmazást, központi telepítési jegyzetek jelenik meg a portálon diagramok. Ha a legújabb kiadás hatása, ha a metrikák a rendelkezett, válik egyértelművé.

![Jegyzetek létrehozása](./media/app-insights-devops/070.png)

### <a name="work-items"></a>A munkaelemek
Riasztást hoz létre, amikor az Application Insights automatikusan létrehozhat az munkaelem nyomkövetési rendszer munkáját.

## <a name="but-what-about"></a>De mi a helyzet...?
* [Adatvédelem és a tárolási](app-insights-data-retention-privacy.md) -a telemetriai adatok másolatok Azure kiszolgálók védelmének biztosításához.
* Teljesítmény - szempontjából nagyon alacsony. Telemetria kötegelni van.
* [Árképzési](app-insights-pricing.md) - szabad kezdheti, és, hogy továbbra is fennáll, kis terjedelmű közben.


## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Következő lépések
Ismerkedés az Application Insights szolgáltatással is könnyen. A fő lehetőségek közül választhat:

* Egy már futó webalkalmazás állíthatnak be. Ez lehetővé teszi a beépített teljesítmény telemetriai adatokat. Használható a [Java](app-insights-java-live.md) és [IIS-kiszolgálókkal](app-insights-monitor-performance-live-website-now.md), és is [Azure-webalkalmazásokban](app-insights-azure.md).
* A projekt állíthatnak be a fejlesztés során. Ehhez [ASP.NET](app-insights-asp-net.md) vagy [Java](app-insights-java-get-started.md) alkalmazások esetén, valamint [Node.js](app-insights-nodejs.md) és [más](app-insights-platforms.md). 
* Eszköz [bármilyen weblap](app-insights-javascript.md) egy rövid kódrészletet hozzáadásával.

