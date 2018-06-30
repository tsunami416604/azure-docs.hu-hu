---
title: Az adatok megőrzése és a tárolás Azure Application Insights |} Microsoft Docs
description: Megőrzési és adatvédelme házirendutasítás
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: a6268811-c8df-42b5-8b1b-1d5a7e94cbca
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/29/2018
ms.author: mbullwin
ms.openlocfilehash: 897671ef592ac691402a4e452f7a0baa04aa228a
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37129057"
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Adatgyűjtés, megőrzés és tárolás az Application Insights szolgáltatásban


Ha telepíti az [Azure Application Insights] [ start] SDK az alkalmazás, elküldi az alkalmazásra vonatkozó telemetriai adatokat a felhőbe. Természetesen felelős fejlesztők szeretné tudni, hogy pontosan milyen adatokat küldi el, mi történik az adatok és hogyan akkor is megtarthatják az irányítást, akkor. Ebben az esetben sikerült bizalmas adatokat elküldeni, hol található a tárolt, és hogy mennyire vannak biztonságban úgy? 

Első, a rövid választ:

* A szabványos telemetriai modulokról "a kezdő" verzióról valószínűleg nem érzékeny adatokat küldeni a szolgáltatást. A telemetriai adatok aggasztanak terhelés, teljesítmény- és használati metrikák, kivétel jelentések és más diagnosztikai adatokat. A diagnosztikai jelentései látható a fő felhasználói adatok URL-címek; azonban az alkalmazás nem minden esetben bizalmas adatok egyszerű szövegként a URL-címében.
* Írhat kódot, amely segítséget nyújt a diagnosztikai és használati figyelési további egyéni telemetriai adatokat küld. (A bővíthetőség az Application Insights kiváló szolgáltatása.) Ki lehet, tévedésből, ez a kód írása, hogy személyes és más bizalmas adatokat tartalmazza. Az alkalmazás használható-e ezeket az adatokat, akkor egy alapos felülvizsgálati folyamatok írt kódot kell alkalmazni.
* Fejlesztés és tesztelés az alkalmazás során nem vizsgálja meg, mi az SDK küldött. Az adatokat a hibakeresési kimeneti windows IDE és böngésző jelenik meg. 
* Az adatok használatban van [Microsoft Azure](http://azure.com) az Amerikai Egyesült Államok vagy Európa kiszolgálók. (De az alkalmazás bárhol futhatnak.) Azure rendelkezik [erős biztonságot dolgozza fel, és megfelel a megfelelőségi követelményeket széles körének](https://azure.microsoft.com/support/trust-center/). Csak a kijelölt csoport hozzáféréssel rendelkeznek az adatait. A Microsoft személyzete is korlátozott hozzáféréssel rendelkező azt csak bizonyos körülmények korlátozott a ismeretekkel. Az átvitel során, ha nem az a kiszolgáló titkosított.

Ez a cikk többi teljes körűen alapuló, ezek a válaszok. Úgy van kialakítva, hogy önálló, így megjelenítheti, munkatársakat, akik nem tartoznak a azonnali csoport.

## <a name="what-is-application-insights"></a>Mi az Application Insights?
[Az Azure Application Insights] [ start] , amelynek segítségével a Microsoft által biztosított szolgáltatás a teljesítményük és használhatóságuk az élő alkalmazás javítására. Az alkalmazás fut, tesztelés során és azt követően, hogy közzétett vagy telepítve lett folyamatosan figyeli. Az Application Insights diagramok és is láthat, például táblák, mely napszakokban kap a legtöbb felhasználó, mennyire reagáljon az alkalmazást, és attól függ, külső szolgáltatások által kiszolgált mennyire hoz létre. Ha összeomlik, hibák vagy teljesítményproblémák, kereshet a telemetriai adatok ellenőrizze okát részletesen keresztül. És a szolgáltatás elküldi e-maileket a rendelkezésre állási és az alkalmazás teljesítményével kapcsolatos módosításokat esetén.

Ez a funkció eléréséhez telepítse az Application Insights SDK az alkalmazás, amely annak válik. Az alkalmazás futtatásakor az SDK-t annak működését figyeli, és telemetriai adatokat küld az Application Insights szolgáltatással. Ez az által üzemeltetett felhőszolgáltatásként [Microsoft Azure](http://azure.com). (De Application Insights olyan alkalmazások, nem csak azokat az Azure-ban tárolt működik.)

![Az SDK az alkalmazás telemetriai adatokat küld az Application Insights szolgáltatással.](./media/app-insights-data-retention-privacy/01-scheme.png)

Az Application Insights szolgáltatás tárolja, és elemzi a. Az elemzés vagy keresési keresztül tárolt telemetriai adatok megtekintéséhez jelentkezzen be az Azure-fiókjával, és nyissa meg az Application Insights-erőforrást az alkalmazáshoz. A csoport többi tagjával, vagy a megadott Azure-előfizetők is megoszthat férhetnek hozzá az adatokhoz.

Az Application Insights szolgáltatás, például egy adatbázis vagy a külső eszközök exportált adatok is. Minden eszköz a szolgáltatás származó különleges kulccsal adja meg. A kulcs is visszavonhatók, ha szükséges. 

Application Insights SDK-k érhetők el a alkalmazástípusok számos: webszolgáltatások saját J2EE vagy az ASP.NET-kiszolgálókon, vagy egy Azure; Ez azt jelenti, hogy a kódot egy weblapon; futtató ügyfelek - webalkalmazás asztali alkalmazások és szolgáltatások; eszköz alkalmazások, például a Windows Phone, iOS és Android. Minden telemetriai adatokat küldhet a ugyanazt a szolgáltatást.

## <a name="what-data-does-it-collect"></a>Milyen adatok azt gyűjt?
### <a name="how-is-the-data-is-collected"></a>Hogyan van az adatok gyűjtése?
Az adatok három forrásának van:

* Az SDK és az alkalmazás integrációjához vagy [a fejlesztési](app-insights-asp-net.md) vagy [futási időben](app-insights-monitor-performance-live-website-now.md). Nincsenek különböző SDK-k különböző alkalmazás esetében. Is egy [webes SDK](app-insights-javascript.md), amely betölti a végfelhasználó böngészőbe együtt a lapot.
  
  * Minden SDK számos [modulok](app-insights-configuration-with-applicationinsights-config.md), amely különböző típusú telemetriai adatok összegyűjtéséhez használja különböző módszereket.
  * Az SDK telepítésekor a fejlesztési az API segítségével a saját telemetriát szabványos modulokat. Az egyéni telemetriai adatokat szeretne küldeni adatokat tartalmazhatnak.
* Az olyan webkiszolgálók vannak is ügynökök mellett az alkalmazás futtatásához és a Processzor, memória és hálózati Foglaltság kapcsolatos telemetriai adatokat küldhet. Például Azure virtuális gépeken, Docker-gazdagépekből és [J2EE kiszolgálók](app-insights-java-agent.md) ilyen ügynökök rendelkezhet.
* [Rendelkezésreállás figyelésére szolgáló tesztek](app-insights-monitor-web-app-availability.md) folyamatok végzi, amely a webes alkalmazás rendszeres időközönként kérelmeket küldeni a Microsoft. Az eredmények kerülnek az Application Insights szolgáltatással.

### <a name="what-kinds-of-data-are-collected"></a>Milyen típusú adatok összegyűjtése?
A fő kategóriába vannak:

* [Webalkalmazás-kiszolgáló telemetriai](app-insights-asp-net.md) -HTTP-kérelmekre.  URI, időn belül feldolgozni a kérelmet, a válaszkód, az ügyfél IP-címét. Munkamenet-azonosító.
* [Weblapok](app-insights-javascript.md) -oldalon, a felhasználó és a munkamenet számát. Lapbetöltési idők. Kivételek. AJAX-hívások.
* Teljesítmény számlálók - memória, Processzor, IO, hálózati Foglaltság.
* Ügyfél és kiszolgáló context - OS, területi beállítás, eszköztípus, böngésző, felbontást.
* [Kivételek](app-insights-asp-net-exceptions.md) és az összeomlások - **a verem memóriaképek**, build azonosítóját, a CPU típusát. 
* [Függőségek](app-insights-asp-net-dependencies.md) -külső szolgáltatások, mint a többi SQL, az AJAX-hívások. URI vagy kapcsolati karakterlánc, időtartama, sikeres, a parancsot.
* [Rendelkezésreállás figyelésére szolgáló tesztek](app-insights-monitor-web-app-availability.md) -vizsgálati és a lépéseket, a válaszok időtartama.
* [Nyomkövetési naplók](app-insights-asp-net-trace-logs.md) és [egyéni telemetria](app-insights-api-custom-events-metrics.md) - **semmit a naplók vagy telemetriai kódaláírással**.

[További részletek](#data-sent-by-application-insights).

## <a name="how-can-i-verify-whats-being-collected"></a>Hogyan ellenőrizhetem alatt gyűjtött adatok?
Ha a Visual Studio alkalmazásba, futtassa az alkalmazást hibakeresési módban (F5). A telemetriai adatokat a kimeneti ablakban jelenik meg. Ott másolja, és formázza az as JSON egyszerű ellenőrzést. 

![](./media/app-insights-data-retention-privacy/06-vs.png)

A diagnosztika ablakban is van olvashatóbb nézetet.

A weblapokat nyissa meg a böngésző hibakeresési ablakot.

![F12 billentyű megnyomása után nyissa meg a hálózati lapját.](./media/app-insights-data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>Írhat kódot a telemetriai adatok szűrése elküldés előtt?
Ez lehet írásával egy [telemetriai processzor beépülő modul](app-insights-api-filtering-sampling.md).

## <a name="how-long-is-the-data-kept"></a>Mennyi az adatok tartják?
Nyers adatok pontok (Ez azt jelenti, hogy elemekre Analytics-lekérdezést, és vizsgálja meg a keresési) 90 napig maradnak. Ha hosszabb, mint adatainak van szüksége, használhatja [a folyamatos exportálás](app-insights-export-telemetry.md) másolja azt a tárfiók.

Összesített adatokat (Ez azt jelenti, számok, átlagok és más szereplő metrikája Explorer statisztikai adatokat), egy olyan aggregációs időközt 90 napig 1 perces megmaradnak.

## <a name="who-can-access-the-data"></a>Ki férhet hozzá az adatokhoz?
Az adatok számára is elérhetővé válik, és ha a szervezeti fiókkal rendelkezik, a csoport tagjai. 

Akkor exportálható, és a csoport tagjai és sikerült másolható át más helyekre, és mások átadja.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>Mi nem Microsoft teendő velük a begyűjtésük alkalmazásom küld az Application Insights?
A Microsoft csak annak érdekében, hogy a szolgáltatás Ön számára az adatok használja.

## <a name="where-is-the-data-held"></a>Az adatok tárolási helye?
* Az Amerikai Egyesült Államok, Európa vagy Délkelet-Ázsia. Kiválaszthatja a helyét, amikor létrehoz egy új Application Insights-erőforrást. 


#### <a name="does-that-mean-my-app-has-to-be-hosted-in-the-usa-europe-or-southeast-asia"></a>Nem, amely jelenti azt az alkalmazást az Amerikai Egyesült Államok, Európa vagy Délkelet-Ázsiában található?
* Nem. Az alkalmazás bárhol futhatnak, a saját helyszíni állomások vagy a felhőben.

## <a name="how-secure-is-my-data"></a>Hogy mennyire vannak biztonságban vannak az adatok?
Az Application Insights egy olyan Azure-szolgáltatás. Biztonsági házirendek ismerteti a [Azure biztonsági, adatvédelmi és megfelelőségi tanulmány](http://go.microsoft.com/fwlink/?linkid=392408).

A Microsoft Azure-kiszolgálók tárolja az adatokat. A fiókok az Azure portálon, a fiók korlátozásai ismertetett a [Azure biztonsági, adatvédelmi és megfelelőségi dokumentum](http://go.microsoft.com/fwlink/?linkid=392408).

A Microsoft személyzete adatokhoz való hozzáférés korlátozva. Jelenleg csak az Ön engedélyével az adatokat, és ha szükség az Application Insights támogatására. 

Adatok összesítés keresztül a felhasználók alkalmazásokat (például adatforgalmi díjakat és nyomkövetési átlagos mérete) az Application Insights javítására szolgál.

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>Akadályozhatja valaki más telemetriai Application Insights adataimat?
Ezek sikerült további telemetriai adatokat küldhet a fiókjához a instrumentation kulcs, amely a weblapok a kódban található használatával. Elegendő további adatokkal a metrikákat, akkor nem megfelelően képviselje az alkalmazás teljesítményét és használatának.

Ha kód megosztása más projektek, fontos, hogy távolítsa el a rendszerállapot-kulcsot.

## <a name="is-the-data-encrypted"></a>Titkosítja az adatokat?
A kiszolgáló jelenleg nincs belül.

Összes adata titkosításra kerül az adatközpontok közötti átvitel során.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>Az adatok titkosítva legyenek az Application Insights kiszolgálókra átvitel közben?
Https Igen, szeretnék adatokat küldeni a a portál szinte minden SDK-k, beleértve a webkiszolgálók, eszközök és HTTPS weblapok a használjuk. Az egyetlen kivétel, egyszerű HTTP-weblapokhoz küldött adatokat.

## <a name="how-do-i-send-data-to-application-insights-using-tls-12"></a>Hogyan küldhetek adatok az Application Insights segítségével a TLS 1.2?

A biztonsági adatok az átvitel során, az Application Insights végpontokhoz biztosítását, azt javasoljuk az ügyfelek számára az alkalmazás használatához legalább konfigurálása Transport Layer Security (TLS) 1.2-es. TLS/Secure Sockets Layer (SSL) régebbi verziójú található érinti, és jelenleg továbbra is működnek, a visszamenőleges kompatibilitás engedélyezése, amíg azok **nem ajánlott**, és az iparág gyorsan változó kénytelen volt megszakítani ezt támogatása a régebbi ezeket a protokollokat. 

A [PCI biztonsági szabványok Tanács](https://www.pcisecuritystandards.org/) be van állítva egy [2018. június 30 határidő](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) letiltása a TLS/SSL és a frissítés több biztonságos protokollokra régebbi verzióit. Amennyiben az Azure már nem az örökölt támogatja, ha az alkalmazás ügyfelek nem protokollt használó kommunikációra legalább a TLS 1.2, akkor nem tudná szeretnék adatokat küldeni a Application insights szolgáltatással. A módszer akkor tesztelésére és érvényesítésére az alkalmazás TLS-támogatás az operációs rendszer platform, valamint a nyelvi/keretrendszer, az alkalmazás függ.

Explicit módon beállítás csak akkor használja a TLS 1.2, ha az alkalmazás feltétlenül szükséges, mivel a platform biztonsági funkciói, amely lehetővé teszi az automatikus észlelése és előnyeit, így elkerülhetők a újabb biztonságosabb protokollok meghibásodásához vezethet nem ajánlott például a TLS 1.3 érhető el. Azt javasoljuk, hogy az alkalmazás-szabályzat hardcoding adott TLS/SSL-verziók kereséséhez alapos naplózási végrehajtása.

### <a name="platformlanguage-specific-guidance"></a>Platform/nyelvi konkrét útmutatást

|Platform/nyelv | Támogatás | További információ |
| --- | --- | --- |
| Azure App Services  | Támogatott konfigurációra lehet szükség. | Támogatás a április 2018 jelentették. Olvassa el a közleményben [konfigurációs részletek](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/).  |
| Az Azure-függvény alkalmazások | Támogatott konfigurációra lehet szükség. | Támogatás a április 2018 jelentették. Olvassa el a közleményben [konfigurációs részletek](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/). |
|.NET | Változó támogatott, a konfiguráció verziója. | További részletes konfigurációs adatainak .NET 4.7 és a korábbi verzióknál [ezeket az utasításokat](https://docs.microsoft.com/en-us/dotnet/framework/network-programming/tls#support-for-tls-12).  |
|Állapotfigyelője | Támogatott, konfigurálás szükséges | Állapotfigyelő támaszkodik [operációs rendszer konfigurációja](https://docs.microsoft.com/en-us/windows-server/security/tls/tls-registry-settings) + [.NET konfigurációs](https://docs.microsoft.com/en-us/dotnet/framework/network-programming/tls#support-for-tls-12) TLS 1.2 támogatásához.
|Node.js |  V10.5.0, a támogatott konfigurációra lehet szükség. | Használja a [Node.js a TLS/SSL dokumentációs](https://nodejs.org/api/tls.html) bármely alkalmazás adott konfigurációhoz. |
|Java | Támogatott, a TLS 1.2 JDK támogatása hozzá lett adva a [JDK 6 frissítés 121](http://www.oracle.com/technetwork/java/javase/overview-156328.html#R160_121) és [JDK 7](http://www.oracle.com/technetwork/java/javase/7u131-relnotes-3338543.html). | Használja a JDK 8 [TLS 1.2 alapértelmezés szerint](https://blogs.oracle.com/java-platform-group/jdk-8-will-use-tls-12-as-default).  |
|Linux | Általában a Linux terjesztéseket támaszkodnak [OpenSSL](https://www.openssl.org) TLS 1.2 támogatásához.  | Ellenőrizze a [OpenSSL változásnaplója](https://www.openssl.org/news/changelog.html) megerősítéséhez a OpenSSL-verziót támogatja.|
| A Windows 8.0-10 | Támogatott, és alapértelmezés szerint engedélyezett. | Annak ellenőrzéséhez, hogy továbbra is használja a [alapértelmezett beállítások](https://docs.microsoft.com/en-us/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2012-2016 | Támogatott, és alapértelmezés szerint engedélyezett. | Annak ellenőrzéséhez, hogy továbbra is használja a [alapértelmezett beállításai](https://docs.microsoft.com/en-us/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 és Windows Server 2008 R2 SP1 | Támogatott, de alapértelmezés szerint nem engedélyezett. | Tekintse meg a [Transport Layer Security (TLS) beállításjegyzék-beállítások](https://docs.microsoft.com/en-us/windows-server/security/tls/tls-registry-settings) engedélyezésével kapcsolatos részletek lap.  |
| Windows Server 2008 SP2 | Támogatja a TLS 1.2-es frissítést igényel. | Lásd: [támogatásához a TLS 1.2-es frissítés](https://support.microsoft.com/help/4019276/update-to-add-support-for-tls-1-1-and-tls-1-2-in-windows-server-2008-s) a Windows Server 2008 SP2. |
|Windows Vista | Nem támogatott. | –

### <a name="check-what-version-of-openssl-your-linux-distribution-is-running"></a>A Linux-disztribúció milyen OpenSSL-verzióval futásának ellenőrzése

Ellenőrizze, hogy milyen OpenSSL-verzióval telepítette, nyissa meg a terminált, és futtassa:

```terminal
openssl version -a
```

### <a name="run-a-test-tls-12-transaction-on-linux"></a>Futtassa a tesztet a TLS 1.2 tranzakció Linux rendszeren

Egy alapszintű előzetes ellenőrizze, hogy ha a Linux rendszer kommunikálhatnak-e a TLS 1.2 keresztül futtatásához. Nyissa meg a terminál és:

```terminal
openssl s_client -connect bing.com:443 -tls1_2
```

## <a name="personal-data-stored-in-application-insights"></a>Az Application Insightsban tárolt személyes adatok

A [Application Insights személyes adatok cikk](app-insights-customer-data.md) részletes probléma ismerteti.

#### <a name="can-my-users-turn-off-application-insights"></a>A felhasználók kikapcsolható Application Insights?
Közvetlenül nem. A Microsoft nem biztosítja egy kapcsoló, amely a felhasználók kapnak az Application Insights kikapcsolása.

A funkció azonban az alkalmazás is létrehozható. Minden az SDK-k többek között a egy API-t, hogy kikapcsolja a telemetriai adatok gyűjtése. 

## <a name="data-sent-by-application-insights"></a>Az Application Insights által küldött adatokat
Az SDK-k platformok változhat, és több összetevőből is telepítheti. (Hivatkoznak [Application Insights – áttekintés][start].) Minden egyes összetevő különböző adatokat küld.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>A különböző alkalmazási helyzetek küldött adatok osztályok
| A művelet | Adatosztályok gyűjtött (lásd a következő táblázatban) |
| --- | --- |
| [Application Insights SDK hozzáadása egy .NET webes projekt][greenbrown] |ServerContext<br/>Következtetni<br/>Teljesítményszámlálók<br/>Kérelmek<br/>**Kivételek**<br/>Munkamenet<br/>felhasználók |
| [Állapotmonitor telepítése az IIS-kiszolgálón][redfield] |Függőségek<br/>ServerContext<br/>Következtetni<br/>Teljesítményszámlálók |
| [Application Insights SDK hozzáadása a Java-webalkalmazás][java] |ServerContext<br/>Következtetni<br/>Kérés<br/>Munkamenet<br/>felhasználók |
| [JavaScript SDK weblap hozzáadása][client] |ClientContext <br/>Következtetni<br/>Oldal<br/>ClientPerf<br/>AJAX |
| [Alapértelmezett tulajdonságok meghatározása][apiproperties] |**Tulajdonságok** összes szabványos és az egyéni esemény |
| [Hívás TrackMetric][api] |Numerikus értékek<br/>**Tulajdonságok** |
| [Hívás követése *][api] |Esemény neve<br/>**Tulajdonságok** |
| [Hívás TrackException][api] |**Kivételek**<br/>Veremkiíratás<br/>**Tulajdonságok** |
| SDK nem gyűjt adatokat. Példa: <br/> -teljesítményszámlálói nem érhető el.<br/> -Kivétel fordult elő a telemetriai adatok inicializáló |SDK-diagnosztika |

A [más platformokhoz készült SDK-k][platforms], tekintse meg a dokumentumokat.

#### <a name="the-classes-of-collected-data"></a>Az összegyűjtött adatokat osztályok
| Összegyűjtött adatok osztály | (Nem teljesnek) tartalmazza. |
| --- | --- |
| **Tulajdonságok** |**Adatok - határozza meg a kódot** |
| DeviceContext |Azonosító, IP, területi beállítás esetén eszközmodell, hálózati, hálózati típusa, az OEM neve, képernyőfelbontás Szerepkörpéldányt, a szerepkör neve, az eszköz típusa |
| ClientContext |Az operációs rendszer, területi beállítás, nyelv, hálózati, ablakban felbontás |
| Munkamenet |munkamenet-azonosító |
| ServerContext |Számítógép neve, területi beállítás, az operációs rendszer, a eszköz, a felhasználói munkamenet, a felhasználói környezet, a művelet |
| Következtetni |földrajzi hely, IP-cím, timestamp, az operációs rendszer, böngésző |
| Mérőszámok |Metrika neve és értéke |
| Események |Az esemény neve és értéke |
| PageViews |URL-cím és a lap neve vagy a képernyő neve |
| Ügyfél-teljesítmény |URL-cím vagy a lap neve, a böngésző lapbetöltési ideje |
| AJAX |HTTP-hívások weblapról kiszolgálóra |
| Kérelmek |URL-címe, időtartama, válaszkód |
| Függőségek |Típus (SQL, HTTP,...), a kapcsolati karakterlánc vagy a URI, szinkronizálási vagy aszinkron, időtartama, sikeres, SQL-utasítás (az állapotfigyelő) |
| **Kivételek** |Típus, **üzenet**, verem hívja, a forrás-fájl és a sor száma, Szálazonosító |
| Összeomlások |Folyamatazonosító, szülő folyamatazonosító, összeomlási szálazonosító; alkalmazás-javítás, a-azonosító, a build;  Kivétel típusa, a cím, a reason; rejtjelezett szimbólumok és regiszterekben, bináris kezdő és záró címek, bináris nevek és elérési útja, a processzor típusa |
| Nyomkövetés |**Üzenet** és súlyossági szint |
| Teljesítményszámlálók |Processzor kihasználtsága, rendelkezésre álló memória, lekérdezési gyakorisága, kivétel sebessége, folyamat saját bájtok, IO sebessége, kérelem időtartama, kérelem-várólista hossza |
| Rendelkezésre állás |Webalkalmazás-teszt válaszkód, időtartama minden teszt lépés, teszt neve, timestamp, sikeres, válaszideje, teszt helye |
| SDK-diagnosztika |Nyomkövetési üzenet vagy kivétel |

Is [kapcsolja ki az adatok egy részét ApplicationInsights.config szerkesztésével][config]

## <a name="credits"></a>Stáblisták
A termék MaxMind, elérhető által létrehozott GeoLite2 adatokat tartalmaz [ http://www.maxmind.com ](http://www.maxmind.com).



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[platforms]: app-insights-platforms.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md

