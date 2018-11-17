---
title: Webes alkalmazás teljesítményének figyelése – az Azure Application Insights |} A Microsoft Docs
description: Hogyan illeszkedik az Application Insights a fejlesztői és üzemeltetői ciklus
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 479522a9-ff5c-471e-a405-b8fa221aedb3
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 521bf044f280b91f668d42d760ba2a726b555f12
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2018
ms.locfileid: "51822670"
---
# <a name="deep-diagnostics-for-web-apps-and-services-with-application-insights"></a>Webalkalmazások és szolgáltatások részletes diagnosztikája az Application Insights szolgáltatással
## <a name="why-do-i-need-application-insights"></a>Miért kell Application Insights?
Az Application Insights figyeli a futó webalkalmazás. Azt mutatja meg hibáiról és teljesítményproblémáiról, és segítségével elemezheti az ügyfelek hogyan használják az alkalmazást. Ez (j2ee-alapú, az ASP.NET, Node.js,...) számos platformon futó alkalmazásokkal működik, és a felhőben vagy a helyszínen üzemel. 

![A webes alkalmazások összetettsége aspektusa](./media/app-insights-devops/010.png)

Ez elengedhetetlen, egy korszerű alkalmazás figyelése futás közben. Ennél is fontosabb szeretné hibák észlelése, még mielőtt az ügyfelek többsége tegye. Is szeretne felderíteni, és a teljesítmény problémák megoldása, amely során nem végzetes, talán lassítják, vagy bizonyos kényelmetlenségért miatt a felhasználók számára. Amikor a rendszer az megfelelően működik, érdemes figyelembe venni, mi a felhasználók általi használatát, és: használ a szolgáltatás legújabb verziója? Azok sikeresek azt?

Modern webes alkalmazások folyamatos szállítási ciklusban fejlesztett: egy új funkció-és javítása; Figyelje meg, milyen jól működik a felhasználók; Tervezze meg a következő növekmény fejlesztési kategorizálják alapján. Ez a ciklus kulcsfontosságú része a megfigyelési fázis. Az Application Insights egy webes alkalmazás teljesítményének és használatának monitorozása az eszközöket biztosít.

A legfontosabb szempont a folyamat során a diagnosztikai és elemzés céljából. Ha az alkalmazás nem tudja, majd üzleti alatt elvesznek. Egy figyelési keretrendszer elsődleges szerepe a megbízhatóan hibák észlelése, azonnal értesíti, és a probléma diagnosztizálása érdekében szükséges információt jelenthet Ön, ezért. Ez a pontosan, az Application Insights leírása.

### <a name="where-do-bugs-come-from"></a>Honnan származnak hibák?
A webes rendszerek hibák általában a konfigurációs problémákat és azok számos összetevő közötti hibás interakciók merülnek fel. Az első tevékenység hozzáadásával egy élő webhely incidens így azonosíthatja a problémát a helyi: mely összetevő vagy kapcsolat okozza?

Néhány szürke haj, az USA egyik számítógépről egy program futtatott egy egyszerűbb era programoztam. A fejlesztők számára szeretné tesztelni, alaposan; mielőtt kellene rendszerrel szállított, lenne ritkán tekintse meg vagy és gondolja át azt újra. A felhasználók és a maradék hibák sok éve helyezi kellene. 

Dolgok váltak tehát nagyon eltérő. Az alkalmazás rendelkezik áll különböző eszközökön futtathatók, és az egyes pontos viselkedést biztosítása is nehézkes lehet. -Alkalmazások felhőbeli üzemeltetéséhez jelenti a hibák gyors lehet meghatározni, de azt is jelenti, folyamatos versenyt és egy gyakori időközönként új funkciók a várt. 

Ezeket a feltételeket az egyetlen módszer a hiba száma szerint a cég ellenőrzés automatizált egységtesztelés. Manuálisan újra teszteléséhez mindent minden delivery lehetetlen lenne. Test jednotky mostantól a létrehozási folyamat egy hétköznapi részét képezi. Eszközök, például a Xamarin Test Cloud azáltal, hogy a böngésző több verzió tesztelése automatizált felhasználói felület segítségével. A tesztelési rendszerek lehetővé számunkra, hogy Reméljük, hogy az alkalmazáson belül található hibák mértéke a minimális kell tartani.

Szokásos webes alkalmazások rendelkeznek számos élő összetevő. Az ügyfél (böngésző vagy eszköz app) és a webkiszolgáló kívül nincs valószínűleg jelentős jut a háttérbeli feldolgozásnak. A háttérrendszer talán összetevők egy folyamatot, vagy közreműködő darab lazább gyűjteménye. És ezek közül számos nem lesz a vezérlőelem – külső szolgáltatások fog támaszkodni, amelyen azok.

Az alábbiakhoz hasonló konfigurációkban lehet nehéz és uneconomical teszteléséhez vagy bizonyulhat, minden lehetséges hibaállapotának más, mint az élő rendszer magát. 

### <a name="questions-"></a>Kérdések...
Ha egy webes rendszerrel fejlesztést megkérjük kérdéseket:

* Az alkalmazás összeomlik? 
* Mi pontosan történt? – Ha a kérelem sikertelen volt, szeretném tudni, hogyan, ott van. A nyomkövetési események egyre van szükségünk...
* Elég gyorsan az alkalmazásom? Mennyi ideig tart a tipikus kérelmek megválaszolásához?
* A kiszolgáló a terhelés kezeléséhez is? Kérelmek száma nő, ha nem a válaszidő tartsa állandó?
* Hogyan válaszol-e függőségekkel saját – a REST API-k, adatbázisok és más összetevőket, amelyeket az alkalmazás meghívja. Ebben az esetben ha a rendszer lassú, az összetevő jelent, vagy jelenik meg lassúak a válaszok személy?
* Felfelé és lefelé pedig az alkalmazásom? Ez látható a világ különböző pontjain? Tudassa, ha lejár...
* Mi az oka? A saját összetevő és a egy függőségi hiba volt? Ennyi az egész egy kommunikációs hiba?
* Hány felhasználó érintett? Ha egynél több probléma kiszolgálókon, ami a legfontosabb rendelkezem?

## <a name="what-is-application-insights"></a>Mi az Application Insights?
![Az Application Insights alapszintű munkafolyamata](./media/app-insights-devops/020.png)

1. Az Application Insights úgy alakítja ki az alkalmazást, és kapcsolatos telemetriai adatokat küld, az alkalmazás futása közben. Az Application Insights SDK beépítése az alkalmazást, vagy figyelheti a rendszerállapotot futásidőben. Az első módszer az olyan rugalmasabb, mivel a saját telemetriát adhat hozzá a rendszeres modulok.
2. A telemetriai adatokat küld az Application Insights portálra, ahol tárolt és feldolgozott. (Bár az Application Insights a Microsoft Azure-ban üzemel, azt is minden olyan webalkalmazások monitorozása – csak Azure-alkalmazások.)
3. A telemetria megjelenik a képernyőn látható diagramokat és az események táblákban.

Telemetria két fő típusa van: aggregált és nyers példányok. 

* Példányadatok tartalmaz, például egy jelentést a webes alkalmazás által fogadott kérelem. Keresése, és vizsgálja meg a Search eszköz az Application Insights-portál használatával egy kérelem részleteit. A példány tartalmazhat például, hogy mennyi az alkalmazás tartott a kérést, valamint a kért URL-cím válaszol, az ügyfél és az egyéb adatok helye hozzávetőleges adatokat.
* Összesített adatokat tartalmaz a eseményszámok egységnyi idő alatt, úgy, hogy össze lehessen hasonlítani a válaszidők a kérelmek számát. Átlagokat mérőszámokat, például a válaszidők kérelem is tartalmaz.

Az adatok a fő kategóriák a következők:

* Az alkalmazás (HTTP-kérések rendszerint), az adatok URL-címet, a válaszidő és a sikeres vagy sikertelen kérelmek száma.
* Függőségek – az alkalmazás is az URI-t, a válaszidők és a sikeres hívások REST- és SQL
* Kivételek, beleértve a hívásláncokat.
* Oldal nézet adatokat, amelyeket a felhasználók böngészőinek származnak.
* Teljesítményszámlálók metrikákra, valamint a metrikák, amelyeket Ön írt. 
* Egyéni események, amelyek segítségével nyomon követheti üzleti eseményeket
* Nyomkövetési naplók hibakereséshez.

## <a name="case-study-real-madrid-fc"></a>Esettanulmány: Real Madrid F.C.
A web service a [Real Madrid labdarúgó Club](http://www.realmadrid.com/) mintegy 450 millió rajongóhoz szolgál a világ különböző pontjain. Ventilátorok eléréséhez egyaránt webböngészők és a Club a mobile apps segítségével. Ventilátorok is nemcsak jegyek foglalható le, de is elérheti az adatokat és videoklipeket eredmények, a lejátszók és a közelgő játékokat. Azokat a keresési szűrőket például célok számú pontozunk. Közösségi média mutató hivatkozásokat is vannak. A felhasználói élmény magas személyre szabott, és végezhetnek ventilátorok szolgál, amely kétirányú kommunikációt.

A megoldás [a szolgáltatások és alkalmazások a Microsoft Azure egy rendszer](https://www.microsoft.com/inculture/sports/real-madrid/). Méretezhetőség azt a fő követelmény: forgalom változó, és elérheti a nagyon nagy mennyiségű során, és egyezés körül.

A Real Madrid, elengedhetetlen, hogy a rendszer teljesítményének figyelése. Az Azure Application Insights nyújt átfogó képet a rendszer egy megbízható és magas szintű szolgáltatást biztosítva. 

A Club is lekérdezi a hozzá tartozó ventilátorok részletes ismertetése:, hogy hol vannak (csak 3 % olyan spanyolországi), a játékosok, a korábbi eredményeit, és a közelgő játékok és a hogyan felel meg az eredményeket válaszoljanak rendelkeznek milyen érdeklődési körök.

Nincs hozzáadva kódra, amely a megoldás egyszerűsített és az üzemeltetés bonyolult csökkenteni a telemetriai adatok többsége automatikusan gyűjti.  A Real Madrid valamennyi, az Application Insights foglalkozik 3.8 milliárd telemetriai pontok minden hónapban.

A Real Madrid a telemetria megtekintése a Power BI-modul használ.

![A Power BI megtekintése az Application Insights telemetria](./media/app-insights-devops/080.png)

## <a name="smart-detection"></a>Intelligens detektálás
[Proaktív diagnosztika](app-insights-proactive-diagnostics.md) legújabb szolgáltatása. Anélkül, hogy Ön semmiféle speciális beállítást az Application Insights automatikusan észleli és hibaarányok az alkalmazásban a szokatlan megnövekszik kapcsolatos figyelmeztetést küld. Legyen elég intelligensek ahhoz, figyelmen kívül hagyja a háttér alkalmanként sikertelen, és is, amelyek egyszerűen arányos megnövekedhet a kérelmek megnövekszik. Így például ha az egyik az alapszolgáltatásokkal hiba történik, vagy ha az új hozhat létre, az imént telepített nem működik olyan jól, hogy tudni fog, amint az e-mailben tekinti meg, majd. (És webhookok, hogy más alkalmazások is indíthat.)

Ez a szolgáltatás másik része a telemetriai adatok, amelyeket nehéz felderíteni Teljesítmény szokatlan minták keres egy napi részletes elemzés hajt végre. Ha például megtalálhatja a lassú teljesítmény az kapcsolódik egy adott földrajzi területen, vagy egy adott böngészőben verzióval.

Mindkét esetben a riasztás nem csak arra kéri, a tünetek fel van derítve, de emellett segít diagnosztizálni a problémát, például a vonatkozó kivételjelentéseket szeretne adatokat biztosít.

![A proaktív diagnosztikai e-mail](./media/app-insights-devops/030.png)

Ügyfél Samtec mondta: "során átváltás egy közelmúltbeli funkciót, találtunk egy alatt méretezett adatbázis, amely a erőforráskorlátok lenyomásával, és időtúllépéseket okoz. Proaktív problémaészlelés riasztások kapott hirdetett azt is sorolása a problémát, nagyon közel valós idejű szó. Ez a riasztás az Azure-platform riasztások ügyfélparancsfájl kommunikációnkhoz szinte azonnal kijavíthatja a hibát. Üzemen kívüli perc összesen < 10 perc."

## <a name="live-metrics-stream"></a>Élő metrikastream
A legújabb buildre helyezhetők üzembe tart felületet nyújt. Problémák merülnének fel, ha szeretné azonnal tudnia kell, velük kapcsolatban, úgy, hogy a biztonsági meg, ha szükséges. Élő metrikák Stream kulcsfontosságú olyan mérőszámokat biztosít, körülbelül egy másodperc késéssel.

![Élő metrikák](./media/app-insights-devops/040.png)

És lehetővé teszi, hogy azonnal ellenőrizze az előforduló hibákat és kivételeket mintát.

![Élő események](./media/app-insights-devops/live-stream-failures.png)

## <a name="application-map"></a>Alkalmazástérkép
Alkalmazás-hozzárendelés automatikusan felderíti a az Alkalmazástopológia fákat alkotó teljesítményadatok felett, lehetővé teszi, hogy könnyedén azonosíthatja a teljesítmény szűk keresztmetszeteket és problémás folyamatok az elosztott környezetekben. Ez lehetővé teszi, hogy az Azure-szolgáltatások alkalmazásfüggőségek felderítése. Ha kódokkal kapcsolatos vagy függőségi kapcsolatos, és a egy kapcsolódó diagnosztikai egyetlen helyen részletes tapasztalatok szerint ismertetése is osztályozni a problémát. Például az alkalmazás sikertelenségét teljesítménycsökkenés SQL szinten miatt. Az alkalmazástérkép tekintse meg azonnal, és az SQL Index Advisor részletesen, vagy betekintés a Lekérdezésekbe merülhetnek fel.

![Alkalmazástérkép](./media/app-insights-devops/050.png)

## <a name="application-insights-analytics"></a>Application Insights-elemzési
A [Analytics](app-insights-analytics.md), tetszőleges lekérdezéseket írhat egy hatékony SQL-szerű nyelven.  Diagnosztizálás között a teljes alkalmazás verem válik egyszerű, különböző szempontok szerint csatlakozhat, és megkérheti, hogy a megfelelő kérdések korrelációját, ha a szolgáltatás teljesítményét az üzleti metrikákat és a felhasználói élmény. 

A telemetria-példány és a portálon tárolt nyers metrikaadatok lekérdezheti. A nyelvi szűrő, való csatlakozás, aggregációs és egyéb műveleteket tartalmaz. Kiszámíthatja a mezők és statisztikai elemzést. Nincsenek táblázatos és a grafikus vizualizációkat.

![Elemzési lekérdezés, és az eredmények diagram](./media/app-insights-devops/025.png)

Ha például is könnyen:

* Az alkalmazás kérelem teljesítményadatok szegmentáljon ügyfél rétegek szerzett megértéséhez.
* Keresés az adott hibakódok vagy egyéni események neve élő webhely vizsgálatok során.
* Az Alkalmazáshasználat megértéséhez funkciók megszerzett és elfogadott adott ügyfelek feltárásához.
* Nyomon követheti a munkamenetek és az azonnali ügyfélszolgálati és üzemeltetési csapatok engedélyezése adott felhasználói válaszidejét.
* Határozza meg, hogy a gyakran használt funkciók funkció rangsorolási kérdésre.

A DNN ügyfél mondta: "Application Insights biztosított velünk a kapcsolatot a folyamatban a következő hiányzó részét képes egyesítése, rendezés, lekérdezés és adatok szűrése, igény szerint. Csapatunk saját nyújt, és felület használatával kereshet egy hatékony lekérdezési nyelvet az adatok lehetővé tette számunkra, hogy az információkhoz, és a problémák megoldásában, így nem is tudjuk történt. Sok érdekes válaszokat származnak kezdve a kérdések *' I wonder, ha... ".*"

## <a name="development-tools-integration"></a>Fejlesztői eszközök integrációja
### <a name="configuring-application-insights"></a>Az Application Insights konfigurálása
A Visual Studio és az Eclipse rendelkezik az eszközök a megfelelő SDK-csomagok fejleszti a projekt konfigurálásához. Egy Application Insights hozzáadása parancs van.

Ha használja a nyomkövetési naplózási keretrendszert, például a Log4N, az NLog vagy a System.Diagnostics.trace keretrendszert használja, majd kap arra, hogy a naplók küldése az Application Insights a telemetriát, valamint, hogy a kérések, függőségi egyszerűen összehasonlíthatja a nyomkövetések hívásokat, és kivételeket.

### <a name="search-telemetry-in-visual-studio"></a>Keresési telemetria a Visual Studióban
Fejlesztés és hibakeresés funkció, miközben tekintheti meg és keressen telemetriai adatokat közvetlenül a Visual Studióban, ugyanazokat a keresési lehetőségeket, mint a webportál használatával.

És amikor az Application Insights naplózza a kivételt, az adatpont megtekintése a Visual Studióban, és rögtön a megfelelő kódot ugorhat.

![A Visual Studio-keresés](./media/app-insights-devops/060.png)

Hibakeresés során lehetősége van, hogy a telemetria a fejlesztői gépén való tekintse meg a Visual Studióban, de anélkül, hogy elküldené a portálon. Ezzel a beállítással helyi elkerülhető, hogy éles telemetriával végzett hibakereséssel keverése.

### <a name="build-annotations"></a>Jegyzetek létrehozása
Ha Azure DevOps használatával hozhat létre, és helyezze üzembe az alkalmazást, üzembe helyezési jegyzetek megjelenik a diagramok a portálon. Ha a legújabb verziót a metrikák gyakorolt hatását, válik nyilvánvalóvá.

![Jegyzetek létrehozása](./media/app-insights-devops/070.png)

### <a name="work-items"></a>Munkaelemek
Riasztást hoz létre, amikor az Application Insights automatikusan létrehozhat az egy munkaelemet a nyomon követési rendszer munkáját.

## <a name="but-what-about"></a>De mi a helyzet...?
* [Adatvédelmi és tárolási](app-insights-data-retention-privacy.md) – a telemetria másolatok Azure kiszolgálók védelmének biztosításához.
* Teljesítmény – a hatás az, nagyon alacsony. Telemetria a kötegelni.
* [Díjszabás](app-insights-pricing.md) – ingyenes kezdheti, és, hogy továbbra is fennáll, kevés közben.


## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>További lépések
Ismerkedés az Application Insights ördöngösség. A főbb lehetőségek közül választhat:

* Alakítsa ki egy már futó webalkalmazás. Ez lehetővé teszi a beépített teljesítményfigyelő telemetriai adatokat. Érhető el [Java](app-insights-java-live.md) és [IIS-kiszolgálókkal](app-insights-monitor-performance-live-website-now.md), és emellett a [Azure web apps](app-insights-overview.md).
* Alakítsa ki a projektet a fejlesztés során. Ehhez [ASP.NET](app-insights-asp-net.md) vagy [Java](app-insights-java-get-started.md) alkalmazások esetében, valamint [Node.js](app-insights-nodejs.md) és a egy sereg [más típusú](app-insights-platforms.md). 
* Eszköz [bármilyen weblapon](app-insights-javascript.md) rövid kódtöredék hozzáadásával.

