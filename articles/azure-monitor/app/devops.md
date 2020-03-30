---
title: Webalkalmazás teljesítményfigyelése – Azure Application Insights
description: Hogyan illeszkedik az Application Insights a devOps-ciklusba?
ms.topic: conceptual
ms.date: 12/21/2018
ms.openlocfilehash: 24095aade80022d1e1ebb38357971512bfc873c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669692"
---
# <a name="deep-diagnostics-for-web-apps-and-services-with-application-insights"></a>Webalkalmazások és szolgáltatások részletes diagnosztikája az Application Insights szolgáltatással
## <a name="why-do-i-need-application-insights"></a>Miért van szükségem Az Application Insightsra?
Az Application Insights figyeli a futó webalkalmazást. Ismerteti a hibákat és a teljesítménnyel kapcsolatos problémákat, és segít elemezni, hogy az ügyfelek hogyan használják az alkalmazást. Számos platformon (ASP.NET, Java EE, Node.js, ...) futó alkalmazásokesetében működik, és a felhőben vagy a helyszínen található. 

![A webalkalmazások szolgáltatásának összetettségével kapcsolatos szempontok](./media/devops/010.png)

Fontos, hogy egy modern alkalmazást futás közben figyelhet. A legfontosabb, hogy a hibákat előbb szeretné észlelni, mint a legtöbb ügyfél. Azt is szeretné, hogy felfedezzék és kijavítsák a teljesítmény problémákat, amelyek, bár nem katasztrofális, talán lelassítja a dolgokat, vagy némi kényelmetlenséget okoz a felhasználóknak. És amikor a rendszer teljesít az Ön megelégedésére, szeretné tudni, hogy mit csinálnak vele a felhasználók: A legújabb funkciót használják? Sikerrel járnak?

Modern webes alkalmazások fejlesztése a ciklus folyamatos szállítás: mentesít egy új funkció vagy javítása; figyeljük meg, hogy milyen jól működik a felhasználók számára; megtervezheti a fejlődés következő növekményét ezen ismeretek alapján. Ennek a ciklusnak a kulcsfontosságú része a megfigyelési szakasz. Az Application Insights eszközöket biztosít egy webes alkalmazás teljesítmény és használat figyeléséhez.

Ennek a folyamatnak a legfontosabb szempontja a diagnosztika és a diagnózis. Ha az alkalmazás meghibásodik, akkor az üzleti veszti. A figyelési keretrendszer elsődleges feladata ezért a hibák megbízható észlelése, azonnali értesítése, valamint a probléma diagnosztizálásához szükséges információk bemutatása. Pontosan ez az, amit az Application Insights csinál.

### <a name="where-do-bugs-come-from"></a>Honnan származnak a bogarak?
A webes rendszerek hibái általában konfigurációs problémákból vagy a számos összetevő közötti rossz interakciókból erednek. Az élő webhely-incidens kezelésének első feladata tehát a probléma helyszínének azonosítása: melyik összetevő vagy kapcsolat az oka?

Néhányan közülünk, azok, akik ősz haj, emlékszik egy egyszerűbb korszak, amelyben egy számítógépes program futott egy számítógépen. A fejlesztők alaposan tesztelnék, mielőtt elszállítanák; és miután szállított, ritkán látni, vagy gondolni rá újra. A felhasználóknak évekig el kellett tolniuk a maradék hibákat. 

A dolgok most nagyon megváltoztak. Az alkalmazás számos különböző eszközön futtatható, és nehéz lehet garantálni, hogy pontosan ugyanazt a viselkedést mindegyik. Az alkalmazások felhőben való üzemeltetése azt jelenti, hogy a hibák gyorsan javíthatók, de folyamatos versenyt és az új funkciók gyakori időközönkénti elvárását is jelenti. 

Ilyen körülmények között az egyetlen módja annak, hogy a hibaszám on-re erősen szabályozhassa az automatikus egységtesztelést. Lehetetlen lenne manuálisan újra tesztelni mindent minden szállításnál. Az egységteszt ma már a létrehozási folyamat általános része. Az olyan eszközök, mint például a Xamarin Test Cloud, segítenek az automatikus felhasználói felület tesztelésének biztosításában több böngészőverzióban. Ezek a tesztelési rendszerek lehetővé teszik számunkra, hogy reméljük, hogy az alkalmazásbelsejében található hibák aránya minimálisra tartható.

Tipikus webes alkalmazások sok élő összetevők. Az ügyfél (böngészőben vagy eszközalkalmazásban) és a webkiszolgáló mellett valószínűleg jelentős háttérfeldolgozás ra is szükség lehet. Lehet, hogy a háttérrendszer egy folyamat az összetevők, vagy egy lazább gyűjteménye együttműködő darabok. És sokan közülük nem lesz az Ön ellenőrzése - ezek a külső szolgáltatások, amelyektől függ.

Az ilyen konfigurációkban nehéz és gazdaságtalan lehet minden lehetséges hibamódot tesztelni vagy előre látni, kivéve magát az élő rendszert. 

### <a name="questions-"></a>Kérdések...
Néhány kérdés, amit felteszünk, amikor webes rendszert fejlesztünk:

* Összeomlik az alkalmazásom? 
* Pontosan mi történt? - Ha megbukott egy kérés, tudni akarom, hogy került oda. Szükségünk van az események nyomára...
* Elég gyors az alkalmazásom? Mennyi ideig tart a tipikus kérések megválaszolása?
* Tudja a szerver kezelni a terhelést? Amikor a kérelmek száma emelkedik, a válaszidő állandó marad?
* Mennyire reagálnak a függőségeim – a REST API-k, adatbázisok és egyéb összetevők, amelyeket az alkalmazás hív. Különösen, ha a rendszer lassú, ez az én komponens, vagy kapok lassú válaszokat valaki más?
* Az alkalmazásom felvan, vagy nem? Látható a világ minden tájáról? Hadd tudjam meg, ha megáll ....
* Mi a kiváltó oka? A hiba az összetevőben vagy függőségként történt? Ez egy kommunikációs kérdés?
* Hány felhasználót érint? Ha egynél több problémával kell foglalkoznom, melyik a legfontosabb?

## <a name="what-is-application-insights"></a>Mi az Application Insights?
![Az Application Insights alapvető munkafolyamata](./media/devops/020.png)

1. Az Application Insights instrumenteli az alkalmazást, és telemetriai adatokat küld róla, miközben az alkalmazás fut. Vagy hozhat létre az Application Insights SDK az alkalmazásba, vagy alkalmazhatja a műszerek futásidőben. Az előbbi módszer rugalmasabb, mivel hozzáadhatja a saját telemetriai adatokat a rendszeres modulokhoz.
2. A telemetriai adatok at az Application Insights-portálra küldi, ahol tárolja és feldolgozza. (Bár az Application Insights a Microsoft Azure-ban található, bármilyen webalkalmazást figyelhet – nem csak az Azure-alkalmazásokat.)
3. A telemetriai adatok diagramok és események táblázatainak formájában jelennek meg.

A telemetriai adatoknak két fő típusa van: összesített és nyers példányok. 

* A példányadatok közé tartozik például egy jelentés egy kérelemről, amelyet a webalkalmazás kapott. Az Application Insights portál keresési eszközével megkeresheti és megvizsgálhatja a kérelmek részleteit. A példány olyan adatokat tartalmazna, mint például az alkalmazás kérésének megválaszolása, valamint a kért URL-cím, az ügyfél hozzávetőleges helye és egyéb adatok.
* Az összesített adatok tartalmazzák az események számát egységnyi időszerint, így összehasonlíthatja a kérelmek arányát a válaszidővel. Azt is tartalmazza a metrikák átlagait, például a kérelem válaszidejét.

Az adatok fő kategóriái a következők:

* Az alkalmazásnak (általában HTTP-kérelmeknek) szóló kérések, amelyek az URL-adatokra, a válaszidőre, valamint a sikerre vagy a sikertelenségre vonatkozó adatokkal szolgálnak.
* Függőségek – Az alkalmazás által kezdeményezett REST- és SQL-hívások, uri-val, válaszidővel és sikerrel is
* Kivételek, beleértve a veremnyomkövetéseket is.
* Oldalnézet-adatok, amelyek a felhasználók böngészőiből származnak.
* Metrikák, például a teljesítményszámlálók, valamint a saját maga által írt mérőszámok. 
* Egyéni események, amelyek segítségével nyomon követheti az üzleti eseményeket
* A hibakereséshez használt naplónyomkövetések.

## <a name="case-study-real-madrid-fc"></a>Esettanulmány: Real Madrid F.C.
A Real [Madrid Football Club](https://www.realmadrid.com/) webszolgáltatása világszerte mintegy 450 millió rajongót szolgál ki. A rajongók mind a webböngészőkön, mind a klub mobilalkalmazásain keresztül érhetik el. A rajongók nem csak jegyet foglalhatnak, hanem információkhoz és videoklipekhez is hozzáférhetnek az eredményekről, a játékosokról és a közelgő játékokról. Olyan szűrőkkel kereshetnek, mint a szerzett gólok száma. Vannak linkek a közösségi médiára is. A felhasználói élmény rendkívül személyre szabott, és úgy tervezték, mint egy kétirányú kommunikáció, hogy vegyenek részt a rajongók.

A megoldás [a Microsoft Azure szolgáltatásainak és alkalmazásainak rendszere.](https://www.microsoft.com/inculture/sports/real-madrid/) A méretezhetőség kulcsfontosságú követelmény: a forgalom változó, és nagyon nagy mennyiségben érhető el a mérkőzések során és környékén.

A Real Madrid számára létfontosságú a rendszer teljesítményének figyelemmel kísérése. Az Azure Application Insights átfogó képet nyújt a rendszerről, amely megbízható és magas szintű szolgáltatást biztosít. 

A klub is kap mélyreható megértése a rajongók: hol vannak (csak 3%, Spanyolországban), milyen érdeklődést mutatnak a játékosok, a történelmi eredmények, és a közelgő játékok, és hogyan reagálnak a mérkőzés eredményeket.

A telemetriai adatok nagy része automatikusan gyűjti a hozzáadott kód nélkül, ami egyszerűsítette a megoldást és csökkentette a működési összetettséget.  A Real Madrid esetében az Application Insights havonta 3,8 milliárd telemetriai ponttal foglalkozik.

A Real Madrid a Power BI modul segítségével tekinti meg a telemetriai adatokat.

![Az Application Insights telemetriai adatainak Power BI nézete](./media/devops/080.png)

## <a name="smart-detection"></a>Intelligens észlelés
[A proaktív diagnosztika](../../azure-monitor/app/proactive-diagnostics.md) egy újabb funkció. Az Ön által speciális konfiguráció nélkül az Application Insights automatikusan észleli és figyelmezteti önt az alkalmazás sikertelenségi arányának szokatlan növekedéséről. Elég okos ahhoz, hogy figyelmen kívül hagyja az alkalmi hibák hátterét, és olyan emelkedéseket is, amelyek egyszerűen arányosak a kérelmek számának növekedésével. Így például, ha van egy hiba az egyik szolgáltatás, amelytől függ, vagy ha az új build, amit csak telepített nem működik olyan jól, akkor tudni fog róla, amint megnézed az e-mail. (És vannak webhooks, így kiváltó más alkalmazásokat.)

A szolgáltatás egy másik aspektusa napi részletes elemzést végez a telemetriai adatokról, és olyan szokatlan teljesítménymintákat keres, amelyeket nehéz felfedezni. Például egy adott földrajzi területhez vagy egy adott böngészőverzióhoz kapcsolódó lassú teljesítményt találhat.

Mindkét esetben a riasztás nem csak azt jelzi, hogy a tünetek a felfedezett, hanem olyan adatokat is ad, amelyek a probléma diagnosztizálásához szükséges adatokat, például a vonatkozó kivételjelentések.

![E-mail a proaktív diagnosztikától](./media/devops/030.png)

Ügyfél Samtec azt mondta: "Egy újabb funkció cutover, találtunk egy alulméretezett adatbázis, amely üti az erőforrás-korlátok at, ami időtúllépés. Proaktív észlelési riasztások jött át a szó szoros értelmében voltunk triaging a kérdést, nagyon közel valós időben hirdetett. Ez a riasztás az Azure platform riasztásaival együtt szinte azonnal segített a probléma megoldásában. A teljes állásidő 10 perc <."

## <a name="live-metrics-stream"></a>Élő metrikastream
A legújabb build telepítése lehet egy aggódó élmény. Ha bármilyen probléma merül fel, azonnal szeretne tudni róluk, hogy szükség esetén kihátrálhass. Élő metrikák stream ad kulcsfontosságú metrikák egy késés körülbelül egy másodperc.

![Élő mutatók](./media/devops/0040.png)

És lehetővé teszi, hogy azonnal vizsgálja meg a mintát az esetleges hibák vagy kivételek.

![Élő hibaesemények](./media/devops/002-live-stream-failures.png)

## <a name="application-map"></a>Alkalmazástérkép
Az Application Map automatikusan felderíti az alkalmazás topológiáját, és a teljesítményadatokat felülírja, így könnyen azonosíthatja a teljesítménybeli szűk keresztmetszeteket és a problémás folyamatokat az elosztott környezetben. Lehetővé teszi az Azure Services alkalmazásfüggőségének felderítését. A probléma osztályozása a kódhoz kapcsolódó vagy függőségi kapcsolatos, és egy helyen a kapcsolódó diagnosztikai élményt. Előfordulhat például, hogy az alkalmazás az SQL-szint teljesítményromlása miatt meghibásodik. Az alkalmazástérkép segítségével azonnal láthatja, és az SQL Index Advisor vagy a Query Insights-élmény beáshatja.

![Alkalmazástérkép](./media/devops/0050.png)

## <a name="application-insights-analytics"></a>Application Insights-statisztika
Az [Analytics](../../azure-monitor/app/analytics.md)segítségével tetszőleges lekérdezéseket írhat egy hatékony SQL-szerű nyelven.  A teljes alkalmazásverem diagnosztizálása egyszerűvé válik, ahogy a különböző perspektívák összekapcsolódnak, és felteheti a megfelelő kérdéseket a szolgáltatás teljesítményének és az üzleti mutatóknak és az ügyfélélménynek a korrelációjához. 

Lekérdezheti az összes telemetriai példány és metrika nyers tárolt adatok a portálon. A nyelv magában foglalja a szűrőt, az illesztést, az összesítést és az egyéb műveleteket. Kiszámíthatja a mezőket, és statisztikai elemzést végezhet. Vannak táblázatos és grafikus vizualizációk.

![Analytics-lekérdezési és eredménydiagram](./media/devops/0025.png)

Például, ez könnyen:

* Szegmentálja az alkalmazás teljesítményadatok at ügyfélszintek szerint, hogy megismerjék a felhasználói élményt.
* Keressen konkrét hibakódokat vagy egyéni eseményneveket az élő webhely-vizsgálatok során.
* Részletezze az egyes ügyfelek alkalmazáshasználatát, hogy megértse, hogyan szerzi be és fogadja el a funkciókat.
* Nyomon követheti az adott felhasználók munkameneteit és válaszidejét, hogy a támogatási és üzemeltetési csapatok azonnali ügyfélszolgálatot nyújthassanak.
* Határozza meg a gyakran használt alkalmazásfunkciókat a funkciók rangsorolási kérdéseinek megválaszolásához.

Ügyfél DNN mondta: "Application Insights biztosította számunkra a hiányzó része az egyenlet, hogy képes kombinálni, rendezni, lekérdezés, és a szűrő adatok szükség szerint. Lehetővé teszi a csapatunk, hogy használja a saját találékonyság és a tapasztalat, hogy megtalálja az adatokat egy hatékony lekérdezési nyelv lehetővé tette számunkra, hogy betekintést találni, és megoldani a problémákat, mi nem is tudom, mi volt. Sok érdekes válasz érkezik a kérdésekből kezdve *"Kíváncsi vagyok, ha ....""*

## <a name="development-tools-integration"></a>Fejlesztési eszközök integrációja
### <a name="configuring-application-insights"></a>Az Application Insights konfigurálása
A Visual Studio és az Eclipse rendelkezik eszközökkel a megfelelő SDK-csomagok konfigurálásához a fejlesztés alatt álló projekthez. Van egy menüparancs az Application Insights hozzáadásához.

Ha történetesen egy nyomkövetési naplózási keretrendszert használ, például a Log4N, az NLog vagy a System.Diagnostics.Trace, akkor lehetősége van arra, hogy a naplókat a többi telemetriai adatokkal együtt küldje el az Application Insightsnak, így könnyen korrelálhatja a nyomkövetéseket kérésekkel, függőséggel hívásokat és kivételeket.

### <a name="search-telemetry-in-visual-studio"></a>Telemetriai adatok keresése a Visual Studióban
Egy szolgáltatás fejlesztése és hibakeresése közben megtekintheti és kereshet a telemetriai adatokat közvetlenül a Visual Studio-ban, ugyanazokat a keresési lehetőségeket használva, mint a webportálon.

Amikor az Application Insights kivételt naplóz, megtekintheti az adatpontot a Visual Studióban, és egyenesen a megfelelő kódra ugorhat.

![Visual Studio keresés](./media/devops/060.png)

A hibakeresés során lehetősége van arra, hogy a telemetriai adatokat a fejlesztői gépen tartsa, a Visual Studióban való megtekintése, de anélkül, hogy elszeretné küldeni a portálra. Ez a helyi beállítás elkerüli a hibakeresést az éles telemetriai adatokkal.

### <a name="work-items"></a>Munkaelemek
Ha egy riasztás tavást, az Application Insights automatikusan létrehozhat egy munkaelemet a munkakövetési rendszerben.

## <a name="but-what-about"></a>De mi a helyzet a...?
* [Adatvédelem és tárolás](../../azure-monitor/app/data-retention-privacy.md) – A telemetriai adatok az Azure biztonságos kiszolgálóin vannak tárolva.
* Teljesítmény - a hatás nagyon alacsony. A telemetria imitátria kötegelve van el.
* [Árképzés](../../azure-monitor/app/pricing.md) – Ingyenesen kezdheti el, és ez akkor is folytatódik, ha kis mennyiségben van.


## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>További lépések
Az Application Insights első lépések egyszerű. A fő lehetőségek a következők:

* [Az IIS-kiszolgálókat](../../azure-monitor/app/monitor-performance-live-website-now.md)és az [Azure App Service szolgáltatást](../../azure-monitor/app/app-insights-overview.md)is.
* A fejlesztés során műszerezheti a projektet. Ezt [megteheti ASP.NET](../../azure-monitor/app/asp-net.md) vagy [Java](../../azure-monitor/app/java-get-started.md) alkalmazások, valamint [node.js](../../azure-monitor/app/nodejs.md) és számos [más típusú](../../azure-monitor/app/platforms.md). 
* Instrument [bármely weboldal](../../azure-monitor/app/javascript.md) hozzáadásával egy rövid kódrészlet.

