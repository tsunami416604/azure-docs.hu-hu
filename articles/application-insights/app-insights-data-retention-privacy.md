---
title: Az adatok megőrzésére és tárolására az Azure Application Insights |} A Microsoft Docs
description: Megőrzésére és az adatvédelmi nyilatkozat
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
ms.date: 10/10/2018
ms.author: mbullwin
ms.openlocfilehash: 1994c714f691177b526b44e277fea705d18b4335
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51245698"
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Adatgyűjtés, megőrzés és tárolás az Application Insights szolgáltatásban

Amikor telepít [Azure Application Insights] [ start] SDK csomagot az alkalmazásban, küld telemetriai adatokat gyűjthessen az alkalmazás a felhőbe. Természetesen felelős fejlesztői mindenképpen tudni szeretnének pontosan az elküldött adatok, mi történik az adatok, és hogyan azok tartani az irányítást, akkor ismernie. Ebben az esetben sikerült bizalmas adatokat küldött, hol található a tárolt és mennyire vannak biztonságban? 

Első, a rövid választ:

* A normál telemetriai "a kezdő" verzióról futtató modulokra nem valószínű, hogy a bizalmas adatokat küldeni a szolgáltatásnak. A telemetria feladata a terhelés, teljesítmény- és használati metrikák, kivételjelentéseket és más diagnosztikai adatokat. A fő felhasználói adatok láthatók a diagnosztikai jelentéseket a rendszer URL-címek; azonban az alkalmazás nem minden esetben helyezze a bizalmas adatok szövegként egy URL-címben.
* Írhat kódot, amely segítséget nyújt a diagnosztikai és használati figyelési további egyéni telemetriát küld. (A bővíthetőség az Application Insights egy nagyszerű szolgáltatása.) Ki lehet, véletlen, ezt a kódot írni, hogy személyes és egyéb bizalmas adatokat tartalmazza. Ha az alkalmazás működésével az ilyen adatok, egy alapos felülvizsgálati folyamatok vonatkozik írt kódot.
* Fejlesztés és tesztelés az alkalmazás közben könnyedék vizsgálja meg, mit küld a rendszer az SDK-ban. Az adatokat a hibakeresési kimeneti Windows-IDE és böngészőben jelenik meg. 
* Az adatokat a tároló [Microsoft Azure](http://azure.com) kiszolgálók, az USA vagy Európa. (De az alkalmazás bárhol futhat.) Az Azure rendelkezik [erős biztonságot dolgozza fel, és megfelel a számos különböző megfelelőségi szabványok](https://azure.microsoft.com/support/trust-center/). Csak Ön és csapata kijelölt érheti el az adatokat. A Microsoft-alkalmazottak is korlátozott hozzáféréssel rendelkező, hogy csak adott korlátozott körülmények kapcsolatos ismereteit. Az átvitel során, bár nem az a kiszolgáló titkosított.

Ez a cikk teljes körűen alapuló, ezek a válaszok. Feladata, hogy önálló, lehet, hogy nem azonnali csapata részét munkatársaknak megjelenítése.

## <a name="what-is-application-insights"></a>Mi az Application Insights?
[Az Azure Application Insights] [ start] , amely segítséget nyújt a Microsoft által biztosított szolgáltatás teljesítményének és az élő alkalmazása használhatóság javítása. Az alkalmazás fut, tesztelés és már közzétett vagy azt követően folyamatosan figyeli. Az Application Insights a diagramok és táblázatok, amelyek bemutatják, például, mely napszakokban kapja a legtöbb felhasználó, hogyan válaszol-e az alkalmazás, és, attól függ, külső szolgáltatások által kiszolgált arról, hogy hoz létre. Ha összeomlik, sikertelen vagy teljesítménnyel kapcsolatos problémák, a telemetriai adatokat, részletes okát is kereshet. És a szolgáltatás elküldi Önnek e-mailek van-e a rendelkezésre állás és teljesítmény az alkalmazás a módosításokat.

Annak érdekében, hogy ez a funkció lekéréséhez telepítse az Application Insights SDK-t az alkalmazás, amely a kód részévé válik. Ha az alkalmazás fut, az SDK működését figyeli, és telemetriai adatokat küld az Application Insights szolgáltatás. Ez az egy felhőalapú szolgáltatás által üzemeltetett [Microsoft Azure](http://azure.com). (De az Application Insights, nem csak azokat az Azure-ban üzemeltetett alkalmazások esetében működik.)

![Az alkalmazás az SDK-telemetriai adatokat küld az Application Insights szolgáltatásba.](./media/app-insights-data-retention-privacy/01-scheme.png)

Az Application Insights szolgáltatás tárolja, és elemzi a telemetriát. Az elemzés vagy keresést végezhet a tárolt telemetriák között jelenik meg, jelentkezzen be az Azure-fiókjával, és nyissa meg az Application Insights-erőforrást az alkalmazás. Az adatokhoz való hozzáférés, vagy a megadott Azure-előfizetők a csapat tagjaival is megoszthatja.

Rendelkezhet például egy adatbázis vagy a külső eszközök az Application Insights szolgáltatás exportált adatok. Minden eszköz adnia, hogy a szolgáltatás speciális kulccsal. Szükség esetén visszavonhatja a kulcsot. 

Application Insights SDK-k alkalmazástípusok közül érhetők el: a web services a saját j2ee-alapú vagy az ASP.NET-kiszolgálókon, vagy az Azure-ban; azt jelenti, webes ügyfél - weblapra; futtató kódnak asztali alkalmazások és szolgáltatások; eszköz alkalmazások, például a Windows Phone, iOS és Android. Ezek az összes telemetriát ugyanahhoz a szolgáltatáshoz.

## <a name="what-data-does-it-collect"></a>Milyen adatokat, gyűjt?
### <a name="how-is-the-data-is-collected"></a>Hogyan történik az adatok gyűjt?
Nincsenek adatok három forrásának:

* Az SDK-t integrálja az alkalmazás vagy [fejlesztés](app-insights-asp-net.md) vagy [futási időben](app-insights-monitor-performance-live-website-now.md). Számos különböző SDK-k különböző alkalmazástípusokat. Is egy [webes SDK](app-insights-javascript.md), amely betölti a végfelhasználó böngészőbe az oldal együtt.
  
  * Az SDK számos [modulok](app-insights-configuration-with-applicationinsights-config.md), amely különböző technikák használatával különféle telemetriai adatok gyűjtése.
  * Az SDK fejlesztési telepíti, ha az API segítségével, a globális modulok mellett a saját telemetriája küldésére. Az egyéni telemetriát is szeretne küldeni adatokat tartalmazhatnak.
* Az egyes webkiszolgálók is találhatók ügynökök, amelyek mellett az alkalmazás futtatása és a Processzor, memória és a hálózat foglaltsága kapcsolatos telemetriai adatokat küldhet. Például az Azure virtuális gépek, Docker-gazdagépek és [j2ee-alapú kiszolgálók](app-insights-java-agent.md) ilyen ügynökök is rendelkezhet.
* [Rendelkezésre állási tesztek](app-insights-monitor-web-app-availability.md) , amely rendszeres időközönként a webalkalmazás-kérelmeket küldjön a Microsoft által folyamatok futnak. Az eredmények érkeznek az Application Insights szolgáltatás.

### <a name="what-kinds-of-data-are-collected"></a>Milyen típusú adatok gyűjtött?
A fő kategóriák a következők:

* [Webalkalmazás-telemetriát](app-insights-asp-net.md) – HTTP-kérelmekre.  URI-t, a kérelem, válaszkód, ügyfél IP-cím feldolgozásához igénybe vett idő. Munkamenet-azonosító.
* [Weblapok](app-insights-javascript.md) -oldalon, a felhasználók és munkamenetek száma. Lapbetöltési idők. Kivételek. AJAX-hívások.
* Teljesítmény számlálók - memória, Processzor, IO, hálózat foglaltsága.
* Ügyfél- és a környezet – operációs rendszer, nyelv, eszköz típusa, böngésző, képernyőfelbontás.
* [Kivételek](app-insights-asp-net-exceptions.md) és összeomlás - **memóriaképek zásobníku**, hozhat létre az azonosítója, CPU-típus. 
* [Függőségek](app-insights-asp-net-dependencies.md) -külső szolgáltatásokkal, például REST, SQL, AJAX-hívások. URI Azonosítójának vagy kapcsolati karakterlánc, időtartamának, sikeres, a parancs.
* [Rendelkezésre állási tesztek](app-insights-monitor-web-app-availability.md) -tesztelés és a lépéseket, a válaszok időtartama.
* [Nyomkövetési naplók](app-insights-asp-net-trace-logs.md) és [egyéni telemetriát](app-insights-api-custom-events-metrics.md) - **semmit, a naplók és a telemetria kódot**.

[További részletek](#data-sent-by-application-insights).

## <a name="how-can-i-verify-whats-being-collected"></a>Hogyan ellenőrizhetem alatt gyűjtött adatok?
Ha az alkalmazást a Visual Studio használatával fejleszt, futtassa az alkalmazást hibakeresési módban (F5). A telemetria megjelenik a kimeneti ablakban. Itt másolhatja, és könnyen vizsgálatra JSON-fájlként formázza. 

![](./media/app-insights-data-retention-privacy/06-vs.png)

A diagnosztikai ablak is van egy olvashatóbb megtekintése.

Weblapok nyissa meg a böngésző hibakereső ablakot.

![Nyomja le az F12 billentyűt, és nyissa meg a hálózat lapot.](./media/app-insights-data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>Írhat kódot a szűrőtelemetria elküldés előtt?
Ez akkor lehetséges, ha egy [telemetriai processzor beépülő modul](app-insights-api-filtering-sampling.md).

## <a name="how-long-is-the-data-kept"></a>Mennyi az adatok marad?
A nyers adatpontokat (vagyis a cikkek, amelyek Analytics-lekérdezést, és vizsgálja meg a keresés) legfeljebb 90 napig őrzi meg. Ha szeretné megőrizni az adatokat, amelyek hosszabb, akkor használhatja [a folyamatos exportálás](app-insights-export-telemetry.md) másolja azt a tárfiókot.

Összesített adatok (azt jelenti, számát, átlagokat és egyéb statisztikai adatok Metrikaböngésző látható), egy 90 napig 1 perces időfelbontási szint megmaradnak.

[Hibakeresési pillanatképei](app-insights-snapshot-debugger.md) hét napig tárolja. A megőrzési házirend van beállítva a alkalmazásonként. Ha ez az érték növelése van szüksége, kérheti növelését az Azure Portalon nyissa meg egy támogatási esetet.

## <a name="who-can-access-the-data"></a>Ki férhet hozzá az adatokhoz?
Az Ön adatai, és ha már van szervezeti fiókja, a munkacsoportja többi tagjával. 

Azt is exportálható, és a munkacsoportja többi tagjával és sikerült más helyre másolja, és mások pedig továbbítva.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>Mi a Microsoft feladata az alkalmazás által az Application Insightsnak küldött információkkal?
A Microsoft adatokat használja, csak annak érdekében, hogy a szolgáltatás Ön számára.

## <a name="where-is-the-data-held"></a>Az adatok tárolási helye?
* Az Amerikai Egyesült Államok, Európa vagy Délkelet-Ázsia. Kiválaszthatja a helyét, amikor létrehoz egy új Application Insights-erőforrást. 


#### <a name="does-that-mean-my-app-has-to-be-hosted-in-the-usa-europe-or-southeast-asia"></a>Jelent az alkalmazás rendelkezik az Amerikai Egyesült Államok, Európa vagy Délkelet-Ázsia felhőben szeretne?
* Nem. Az alkalmazás bárhol futhat, a felhőben vagy a saját helyszíni gazdagépekre.

## <a name="how-secure-is-my-data"></a>Mennyire vannak biztonságban vannak az adataim?
Az Application Insights egy olyan Azure-szolgáltatás. Biztonsági szabályzatok ismertetett a [Azure biztonsági, adatvédelmi és megfelelőségi tanulmány](https://go.microsoft.com/fwlink/?linkid=392408).

A Microsoft Azure-kiszolgálók tárolja az adatokat. Fiókok az Azure Portalon, fiók-korlátozásokat ismerteti a [Azure biztonsági, adatvédelmi és megfelelőségi dokumentum](https://go.microsoft.com/fwlink/?linkid=392408).

A Microsoft ezért felelős munkatársai adatokhoz való hozzáférés korlátozva. Az adatok csak az Ön engedélyével el, és ha szükség az Application Insights használatát támogatja. 

Az Application Insights javításához adatok között a felhasználók alkalmazásokat (például díjait és a nyomkövetések átlagos mérete) összesítést használja.

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>Valaki más telemetriai akadályozhatja az Application Insights-adataim?
Ezek sikerült további telemetriai adatokat küldi a fiókjához a kialakítási kulcsot, a weblapok a kódban található használatával. Elegendő további adatokat a metrikák lenne nem pontosan jelenítik az alkalmazás teljesítményéről és kihasználtságáról.

Ha megoszt kód más projektekhez, ne felejtse el eltávolítani a kialakítási kulcsot.

## <a name="is-the-data-encrypted"></a>Titkosítja az adatokat?
A kiszolgálók jelenleg nem található.

Összes adat titkosítva van, az adatközpontok között mozog.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>Titkosítja az adatokat átvitel közben az Application Insights-kiszolgálóknak az alkalmazásom?
Igen, használjuk https gyakorlatilag az összes SDK-k, beleértve a webkiszolgálók, eszközök és HTTPS-weblapok a portál adatküldéshez. Az egyetlen kivétel, egyszerű HTTP-weblapok által küldött adatokat.

## <a name="does-the-sdk-create-temporary-local-storage"></a>Hozzon létre ideiglenes helyi tárhely az SDK-t?

Igen, bizonyos Telemetriai csatornák addig megmarad adatok helyben, ha a végpont nem érhető el. Tekintse át alább megtekintheti, mely keretrendszerek és a telemetria csatornák érinti.


Telemetria csatornák, amelyek ténylegesen használják a helyi tároló ideiglenes fájlokat hoz létre a TEMP vagy APPDATA címtárakban, amely korlátozódnak az alkalmazást futtató fiók. Ez akkor fordulhat elő, ha átmenetileg nem érhető el a végpont vagy eléri a sávszélesség-szabályozási korlátot. Ha a probléma nem oldódik meg, a telemetriai adatok csatorna folytatódik az új és a megőrzött adatokat küldi.


A megőrzött adatok **nem titkosított** és az adatgyűjtési szabályzat letiltja a személyes adatoknak a gyűjtését átalakításának erősen ajánlott. (Lásd: [és törlése a személyes adatok exportálása](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data#how-to-export-and-delete-private-data) további információt.)


Ha egy ügyfél be kell állítania ebben a könyvtárban az adott biztonsági követelményeknek, keretrendszerrel is konfigurálható. Győződjön meg arról, hogy a folyamat, az alkalmazás futtatásának rendelkezik írási hozzáféréssel a könyvtárhoz, de ügyeljen arra, hogy ez a könyvtár védett telemetriai adatok olvasása a nem kívánt felhasználók elkerülése érdekében.

### <a name="java"></a>Java

`C:\Users\username\AppData\Local\Temp` adatok megtartását használható. Ezen a helyen nem konfigurálható a config könyvtárból, és az engedélyeket, ez a mappa eléréséhez szükséges hitelesítő adatokkal a konkrét felhasználó korlátozódnak. (Lásd: [megvalósítási](https://github.com/Microsoft/ApplicationInsights-Java/blob/40809cb6857231e572309a5901e1227305c27c1a/core/src/main/java/com/microsoft/applicationinsights/internal/util/LocalFileSystemUtils.java#L48-L72) itt.)

###  <a name="net"></a>.Net

Alapértelmezés szerint `ServerTelemetryChannel` használja az aktuális felhasználó helyi alkalmazás Adatmappa `%localAppData%\Microsoft\ApplicationInsights` vagy ideiglenes mappa `%TMP%`. (Lásd: [megvalósítási](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) itt.)


Konfigurációs fájlra:
```
<TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel,   Microsoft.AI.ServerTelemetryChannel">
    <StorageFolder>D:\NewTestFolder</StorageFolder>
</TelemetryChannel>
```

Kód:

- A konfigurációs fájlból ServerTelemetryChannel eltávolítása
- Ez a kódrészlet hozzá a konfigurációhoz:
```
ServerTelemetryChannel channel = new ServerTelemetryChannel();
channel.StorageFolder = @"D:\NewTestFolder";
channel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = channel;
```

### <a name="netcore"></a>NetCore

Alapértelmezés szerint `ServerTelemetryChannel` használja az aktuális felhasználó helyi alkalmazás Adatmappa `%localAppData%\Microsoft\ApplicationInsights` vagy ideiglenes mappa `%TMP%`. (Lásd: [megvalósítási](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) itt.) A Linux-környezet helyi tároló le lesz tiltva, kivéve, ha egy tároló mappa van megadva.

A következő kódrészlet azt mutatja be, hogyan állíthatja be `ServerTelemetryChannel.StorageFolder` a a `ConfigureServices()`  módszere a `Startup.cs` osztály:

```
services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
```

(Lásd: [AspNetCore egyéni konfiguráció](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) további információt. )

### <a name="nodejs"></a>Node.js

Alapértelmezés szerint `%TEMP%/appInsights-node{INSTRUMENTATION KEY}` használ az adatok megőrzése. Ez a mappa elérésére jogosult korlátozódnak az aktuális felhasználó és a rendszergazdáknak. (Lásd: [megvalósítási](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Sender.ts) itt.)

A mappa előtag `appInsights-node` felülbírálhatja a statikus változó futásidejű értékének megmódosítása `Sender.TEMPDIR_PREFIX` található [Sender.ts](https://github.com/Microsoft/ApplicationInsights-node.js/blob/7a1ecb91da5ea0febf5ceab13d6a4bf01a63933d/Library/Sender.ts#L384).



## <a name="how-do-i-send-data-to-application-insights-using-tls-12"></a>Hogyan küldhetek data do Application Insights a TLS 1.2 használatával?

Az Application Insights-végpontokra irányuló átvitt adatok biztonságának biztosítása érdekében, erősen javasoljuk ügyfeleinknek, hogy az alkalmazás használja legalább konfigurálása Transport Layer Security (TLS) 1.2-es. Sebezhetők régebbi verziói a TLS/Secure Sockets Layer (SSL) találhatók, és jelenleg továbbra is működnek, hogy a visszamenőleges kompatibilitás, amíg azok **nem ajánlott**, és az iparág gyorsan változó kénytelen volt megszakítani ezt támogatása ezen régebbi protokollok. 

A [PCI biztonsági szabványok Tanácsa](https://www.pcisecuritystandards.org/) be van állítva egy [2018. június 30. a határidő](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) régebbi verziói a TLS/SSL és a frissítés biztonságosabb protokoll letiltásához. Miután Azure támogatása, csökken, ha az alkalmazás ügyfelek nem keresztül kommunikálnak, legalább a TLS 1.2-es nem tudná az Application Insights történő adatküldéshez. A módszer, tesztelése és ellenőrzése az alkalmazás TLS-támogatás az operációs rendszer és platformok, valamint az alkalmazás használja nyelv és keretrendszer függően változhat.

Nem javasoljuk, hogy explicit módon beállítása az alkalmazás csak a TLS 1.2 használatára, kivéve, ha ez tönkretehetik platform szintű biztonsági funkciókat, amelyek lehetővé teszik, hogy automatikusan észleli, és kihasználhatja az újabb biztonságosabb protokollok váló feltétlenül szükséges rendelkezésre áll, például a TLS 1.3. Azt javasoljuk, hogy az alkalmazás kódjában hardcoding adott TLS/SSL-verziók ellenőrzéséhez egy alapos sablonesemények naplózását végrehajtása.

### <a name="platformlanguage-specific-guidance"></a>Konkrét útmutatást platformon és nyelven

|Platformon és nyelven | Támogatás | További információ |
| --- | --- | --- |
| Azure App Services  | Támogatott konfigurációra lehet szükség. | Támogatás a 2018 április mutattuk be. A bejelentés a [konfigurációs részletek](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/).  |
| Azure Function Appsszel | Támogatott konfigurációra lehet szükség. | Támogatás a 2018 április mutattuk be. A bejelentés a [konfigurációs részletek](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/). |
|.NET | Konfiguráció verziója által támogatott, változik. | Részletes információ a .NET 4.7 és korábbi verziók hivatkoznak [ezek az utasítások](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12).  |
|Állapotmonitor | Támogatott, konfiguráció szükséges | Az állapotfigyelő támaszkodik [operációs rendszer konfigurációja](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) + [.NET-konfiguráció](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12) a TLS 1.2 támogatása.
|Node.js |  V10.5.0, támogatott konfigurációra lehet szükség. | Használja a [hivatalos Node.js TLS/SSL-dokumentáció](https://nodejs.org/api/tls.html) bármely alkalmazás adott konfigurációhoz. |
|Java | Támogatott, a TLS 1.2 JDK támogatása hozzáadva [JDK 6-frissítés 121](http://www.oracle.com/technetwork/java/javase/overview-156328.html#R160_121) és [JDK 7](http://www.oracle.com/technetwork/java/javase/7u131-relnotes-3338543.html). | JDK 8 használ [a TLS 1.2 alapértelmezés szerint](https://blogs.oracle.com/java-platform-group/jdk-8-will-use-tls-12-as-default).  |
|Linux | Linux-disztribúciók általában támaszkodhat [OpenSSL](https://www.openssl.org) a TLS 1.2 támogatása.  | Ellenőrizze a [OpenSSL változásnaplójában](https://www.openssl.org/news/changelog.html) annak ellenőrzéséhez, hogy az OpenSSL-verziót támogatja.|
| Windows 8.0-s és 10 | Támogatott, és alapértelmezés szerint engedélyezve van. | Ellenőrizze, hogy továbbra is használja a [alapértelmezett beállítások](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings).  |
| A Windows Server 2012-2016-ban | Támogatott, és alapértelmezés szerint engedélyezve van. | Ellenőrizze, hogy továbbra is használja a [alapértelmezett beállításai](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 és a Windows Server 2008 R2 SP1 | Támogatott, de alapértelmezés szerint nincs engedélyezve. | Tekintse meg a [Transport Layer Security (TLS) beállításjegyzék-beállítások](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) lap engedélyezése részleteiért.  |
| Windows Server 2008 SP2 | A TLS 1.2 támogatásához szükséges frissítést. | Lásd: [frissítést a TLS 1.2 támogatása](https://support.microsoft.com/help/4019276/update-to-add-support-for-tls-1-1-and-tls-1-2-in-windows-server-2008-s) a Windows Server 2008 SP2. |
|Windows Vista | Nem támogatott. | –

### <a name="check-what-version-of-openssl-your-linux-distribution-is-running"></a>A Linux-disztribúció melyik verzióját OpenSSL futásának ellenőrzése

Ellenőrizze, hogy melyik OpenSSL verzióját telepítette, nyissa meg a terminált, és futtassa:

```terminal
openssl version -a
```

### <a name="run-a-test-tls-12-transaction-on-linux"></a>Egy teszt a TLS 1.2-es tranzakciós futtatása Linux rendszeren

Megtekintheti, ha a Linux rendszer keresztüli TLS 1.2 kommunikálhatnak egy egyszerű előzetes teszt futtatásához. Nyissa meg a terminált, és futtassa:

```terminal
openssl s_client -connect bing.com:443 -tls1_2
```

## <a name="personal-data-stored-in-application-insights"></a>Az Application insights szolgáltatásban tárolt személyes adatok

A [Application Insights a személyes adatok cikk](../log-analytics/log-analytics-personal-data-mgmt.md) a probléma részletes ismerteti.

#### <a name="can-my-users-turn-off-application-insights"></a>A felhasználók kikapcsolhatja az Application Insights?
Közvetlenül nem. Nem biztosítunk egy kapcsoló, amely a felhasználók működhet a tiltsa le az Application Insights.

A funkció azonban az alkalmazásban is alkalmazható. Minden SDK-t egy API-beállítás, amely eszköztelemetria-gyűjtést kikapcsolása tartalmazza. 

## <a name="data-sent-by-application-insights"></a>Application Insights által küldött adatok
Az SDK-k platformok közötti eltérőek lehetnek, és több összetevőt, amelyet telepíthet. (Tekintse meg [Application Insights – áttekintés][start].) Az egyes összetevők különböző adatokat küld.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>Osztályok különböző helyzetekben küldött adatok
| A művelet | (Lásd a következő tábla) gyűjtött adatok osztályok |
| --- | --- |
| [Application Insights SDK hozzáadása egy .NET webes projekthez][greenbrown] |ServerContext<br/>Következtetni<br/>Teljesítményszámlálók<br/>Kérelmek<br/>**Kivételek**<br/>Munkamenet<br/>felhasználók |
| [Telepítse az Állapotfigyelőt az IIS-kiszolgálón][redfield] |Függőségek<br/>ServerContext<br/>Következtetni<br/>Teljesítményszámlálók |
| [Application Insights SDK hozzáadása a Java-webalkalmazás][java] |ServerContext<br/>Következtetni<br/>Kérés<br/>Munkamenet<br/>felhasználók |
| [A JavaScript SDK-t adhat a weblap][client] |ClientContext <br/>Következtetni<br/>Oldal<br/>ClientPerf<br/>AJAX |
| [Alapértelmezett tulajdonságainak meghatározása][apiproperties] |**Tulajdonságok** összes szabványos és egyéni esemény |
| [Hívás TrackMetric][api] |Numerikus értékek<br/>**Tulajdonságok** |
| [Hívás nyomon követése *][api] |Esemény neve<br/>**Tulajdonságok** |
| [Hívás TrackException][api] |**Kivételek**<br/>Veremkiíratás<br/>**Tulajdonságok** |
| SDK-t nem gyűjt adatokat. Példa: <br/> -teljesítményszámlálók nem fér hozzá.<br/> -kivétel a telemetriainicializálót |SDK-diagnosztika |

A [platformokhoz készült SDK-k][platforms], tekintse meg a dokumentumokat.

#### <a name="the-classes-of-collected-data"></a>Az összegyűjtött adatokat a osztályok
| Összegyűjtött adatok osztályban | Tartalmazza (nem egy kimerítően teljes lista) |
| --- | --- |
| **Tulajdonságok** |**Minden olyan adat - határozza meg a kódot** |
| DeviceContext |Azonosító, IP, területi beállítás, eszközmodell, hálózati, hálózattípus, OEM neve, képernyőfelbontás Szerepkörpéldány, a szerepkör neve, az eszköz típusa |
| ClientContext |Operációs rendszer területi beállítása, nyelv, hálózati, ablakban felbontás |
| Munkamenet |A munkamenet-azonosító |
| ServerContext |A gép neve, területi beállítás, az operációs rendszer, eszköz, felhasználói munkamenet, felhasználói környezetet, a művelet |
| Következtetni |földrajzi hely, IP-címről, timestamp, az operációs rendszer, böngészőben |
| Mérőszámok |Metrika neve és értéke |
| Események |Esemény neve és értéke |
| Oldalmegtekintések |URL-CÍMÉT és a lap neve vagy a képernyő nevét |
| Ügyfél teljesítményoptimalizált |URL-cím/lap neve, a böngésző lapbetöltési ideje |
| AJAX |A kiszolgáló webes HTTP-hívások |
| Kérelmek |URL-CÍMÉT, időtartamának, válaszkód |
| Függőségek |Típus (SQL, a HTTP,...), a kapcsolati karakterlánc vagy URI-t, szinkronizálási vagy aszinkron, időtartama, sikeres, SQL-utasítás (az Állapotfigyelőt) |
| **Kivételek** |Típus, **üzenet**, zásobníky volání, a forrás-fájl és a sor száma, hozzászóláslánc azonosítója |
| Összeomlások |Id procesu, szülőfolyamat azonosítója, összeomlási szálazonosító; alkalmazás-javítás, a-azonosító, a build;  Kivétel típusa, cím, reason; rejtjelezett szimbólumok, és regisztrál, bináris kezdő és záró címet, bináris fájl neve és elérési útja, cpu-típus |
| Nyomkövetés |**Üzenet** és súlyossági szint |
| Teljesítményszámlálók |Processzor kihasználtsága, rendelkezésre álló memória, kérelmek gyakorisága, kivételek gyakorisága, folyamat saját bájtjai, átviteli sebessége, kérelem idejével, kérelem-várólista hossza |
| Rendelkezésre állás |Webes teszt válaszkód, az időtartamot mindegyik teszt. lépés, teszt neve, timestamp, sikeres, válaszideje, teszt helye |
| SDK-diagnosztika |Nyomkövetési üzenet vagy kivétel |

Is [kikapcsolhatja az egyes adatok is az ApplicationInsights.config szerkesztésével][config]

## <a name="credits"></a>Stáblisták
A termék által elérhető MaxMind létrehozott GeoLite2 adatokat tartalmaz [ http://www.maxmind.com ](http://www.maxmind.com).



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

