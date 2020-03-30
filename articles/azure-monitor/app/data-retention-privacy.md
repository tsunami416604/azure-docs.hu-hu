---
title: Adatmegőrzés és -tárolás az Azure Application Insightsban | Microsoft dokumentumok
description: Adatmegőrzési és adatvédelmi nyilatkozat
ms.topic: conceptual
ms.date: 09/29/2019
ms.openlocfilehash: 30878eecf795c85713b9f09b8325b326416022b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275996"
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Adatgyűjtés, adatmegőrzés és tárolás az Application Insightsban

Amikor telepíti [az Azure Application Insights][start] SDK az alkalmazásban, elküldi az alkalmazás telemetriai adatokat a felhőbe. Természetesen a felelős fejlesztők szeretnék tudni, hogy pontosan milyen adatokat küld a rendszer, mi történik az adatokkal, és hogyan tudják tartani az irányítást felettük. Különösen érzékeny adatokat lehet-e küldeni, hol tárolják, és mennyire biztonságosak? 

Először is, a rövid válasz:

* A szabványos telemetriai modulok, amelyek futnak "out of the box" nem valószínű, hogy bizalmas adatokat küldeni a szolgáltatásnak. A telemetriai adatok a terhelés, a teljesítmény és a használati metrikák, a kivételjelentések és egyéb diagnosztikai adatok vonatkoznak. A diagnosztikai jelentésekben látható fő felhasználói adatok URL-ek; de az alkalmazás semmilyen esetben sem helyezhet el bizalmas adatokat egyszerű szövegként egy URL-ben.
* Írhat kódot, amely további egyéni telemetriai adatokat küld, hogy segítsen a diagnosztika és a használat figyelése. (Ez a bővíthetőség az Application Insights nagyszerű funkciója.) Hibaből meg lehet írni ezt a kódot, hogy az személyes és egyéb érzékeny adatokat tartalmazzon. Ha az alkalmazás ilyen adatokkal működik, alapos felülvizsgálati folyamatot kell alkalmaznia az összes írt kódra.
* Az alkalmazás fejlesztése és tesztelése során egyszerűen ellenőrizheti, hogy mit küld az SDK. Az adatok az IDE és a böngésző hibakeresési kimeneti ablakaiban jelennek meg. 
* Az adatokat az Egyesült Államokban vagy Európában található Microsoft Azure-kiszolgálókon tartják. [Microsoft Azure](https://azure.com) (Az alkalmazás azonban bárhol futtatható.) Az Azure [erős biztonsági folyamatokkal rendelkezik, és a megfelelőségi szabványok széles körének felel meg.](https://azure.microsoft.com/support/trust-center/) Csak Ön és a kijelölt csapata férhet hozzá az adataihoz. A Microsoft munkatársai csak korlátozott hozzáféréssel rendelkezhetnek az Ön tudomása szerint korlátozott anameddig. Az átvitel és a nyugalom alatt van titkosítva.
*   Tekintse át az összegyűjtött adatokat, mivel ezek bizonyos körülmények között engedélyezett adatokat tartalmazhatnak, másokat azonban nem.  Jó példa erre az eszköznév. A kiszolgálóról származó eszköznévnek nincs adatvédelmi hatása, és hasznos, de a telefonról vagy laptopról származó eszköznév nek adatvédelmi hatása lehet, és kevésbé hasznos lehet. Az elsősorban a célkiszolgálókra kifejlesztett SDK alapértelmezés szerint összegyűjti az eszköz nevét, és ezt felül kell írni mind a normál események, mind a kivételek esetén.

A cikk többi része részletesebben kifejti ezeket a válaszokat. Úgy tervezték, hogy önálló legyen, így megmutathatja azoknak a munkatársaknak, akik nem tagjai a közvetlen csapatnak.

## <a name="what-is-application-insights"></a>Mi az Application Insights?
[Az Azure Application Insights][start] a Microsoft által nyújtott szolgáltatás, amely segít az élő alkalmazás teljesítményének és használhatóságának javításában. Folyamatosan figyeli az alkalmazást, mind a tesztelés során, mind a közzététel vagy üzembe helyezés után. Az Application Insights diagramokat és táblázatokat hoz létre, amelyek megmutatják például, hogy a legtöbb felhasználó milyen napszakokat kap, mennyire reagál az alkalmazás, és milyen jól szolgálják ki azokat a külső szolgáltatások, amelyektől függ. Ha összeomlások, hibák vagy teljesítményproblémák merülnek fel, részletesen kereshet a telemetriai adatok között az ok diagnosztizálásához. És a szolgáltatás e-maileket küld Önnek, ha bármilyen változás van az alkalmazás rendelkezésre állásában és teljesítményében.

Ennek a funkciónak a beszereznie, telepíti az Application Insights SDK-t az alkalmazásban, amely a kód részévé válik. Az alkalmazás futása közben az SDK figyeli annak működését, és telemetriai adatokat küld az Application Insights szolgáltatásnak. Ez a [Microsoft Azure](https://azure.com)felhőszolgáltatása. (Az Application Insights azonban nem csak az Azure-ban üzemeltetett alkalmazásokhoz működik.)

Az Application Insights szolgáltatás tárolja és elemzi a telemetriai adatokat. Az elemzés megtekintéséhez vagy keresés a tárolt telemetriai adatok, jelentkezzen be az Azure-fiókba, és nyissa meg az Application Insights-erőforrást az alkalmazáshoz. Az adatokhoz való hozzáférést megoszthatja a csapat többi tagjával vagy meghatározott Azure-előfizetőkkel is.

Az Application Insights szolgáltatásból exportálhat adatokat, például egy adatbázisba vagy külső eszközökbe. Minden eszköznek egy speciális kulcsot biztosít, amelyet a szolgáltatástól kap. A kulcs szükség esetén visszavonható. 

Az Application Insights SDK-k számos alkalmazástípushoz érhetők el: a saját Java EE vagy ASP.NET kiszolgálóin vagy az Azure-ban üzemeltetett webszolgáltatásokhoz; webes ügyfelek - azaz a kód fut egy weboldalon; asztali alkalmazások és szolgáltatások; például windows Phone-, iOS- és Android-alkalmazásokat. Mind ugyanannak a szolgáltatásnak küldtelemetriát.

## <a name="what-data-does-it-collect"></a>Milyen adatokat gyűjt?
Három adatforrás létezik:

* Az SDK, amelyet az alkalmazással integrál a [fejlesztés](../../azure-monitor/app/asp-net.md) vagy [a futási idő során.](../../azure-monitor/app/monitor-performance-live-website-now.md) A különböző alkalmazástípusokhoz különböző SDK-k tartoznak. Van egy [SDK](../../azure-monitor/app/javascript.md)is a weboldalakhoz , amely betöltődik a végfelhasználó böngészőjében az oldallal együtt.
  
  * Minden SDK számos [modullal rendelkezik,](../../azure-monitor/app/configuration-with-applicationinsights-config.md)amelyek különböző technikákat használnak a különböző típusú telemetriai adatok összegyűjtéséhez.
  * Ha telepíti az SDK-t a fejlesztés, az API-t a standard modulok mellett saját telemetriai adatok küldéséhez. Ez az egyéni telemetriai adatok bármilyen elküldeni kívánt adatokat tartalmazhat.
* Egyes webkiszolgálókon olyan ügynökök is vannak, amelyek az alkalmazás mellett futnak, és telemetriai adatokat küldenek a PROCESSZOR, a memória és a hálózat kihasználtságáról. Például az Azure-beli virtuális gépek, a Docker-állomások és [a Java EE-kiszolgálók](../../azure-monitor/app/java-agent.md) rendelkezhetnek ilyen ügynökökkel.
* [A rendelkezésre állási tesztek](../../azure-monitor/app/monitor-web-app-availability.md) olyan folyamatok, amelyeket a Microsoft futtat, és rendszeres időközönként kéréseket küldenek a webalkalmazásnak. Az eredményeket az Application Insights szolgáltatás küldi el.

### <a name="what-kinds-of-data-are-collected"></a>Milyen típusú adatokat gyűjtenek?
A fő kategóriák a következők:

* [Webkiszolgáló telemetriai adatai](../../azure-monitor/app/asp-net.md) – HTTP-kérelmek.  Uri, a kérelem feldolgozásához szükséges idő, válaszkód, ügyfél IP-címe. `Session id`.
* [Weblapok](../../azure-monitor/app/javascript.md) – A lapok, a felhasználók és a munkamenetek száma. Az oldal betöltési ideje. Kivételek. Az Ajax hív.
* Teljesítményszámlálók – Memória, CPU, IO, hálózati foglaltság.
* Ügyfél- és kiszolgálókörnyezet – operációs rendszer, területi beállítás, eszköztípus, böngésző, képernyőfelbontás.
* [Kivételek](../../azure-monitor/app/asp-net-exceptions.md) és összeomlások - **veremmemóriaképek**, `build id`CPU-típus. 
* [Függőségek](../../azure-monitor/app/asp-net-dependencies.md) - külső szolgáltatások, például REST, SQL, AJAX hívások. URI vagy kapcsolati karakterlánc, időtartam, sikeres parancs.
* [Rendelkezésre állási tesztek](../../azure-monitor/app/monitor-web-app-availability.md) - a teszt időtartama és a lépések, válaszok.
* [Nyomkövetési naplók](../../azure-monitor/app/asp-net-trace-logs.md) és [egyéni telemetriai](../../azure-monitor/app/api-custom-events-metrics.md) - **mindent, amit kód a naplók vagy telemetriai**adatokat.

[További részletek](#data-sent-by-application-insights).

## <a name="how-can-i-verify-whats-being-collected"></a>Hogyan ellenőrizhetem, hogy mit gyűjtenek?
Ha a Visual Studio használatával fejleszti az alkalmazást, futtassa az alkalmazást hibakeresési módban (F5). A telemetriai adatok a Kimenet ablakban jelennek meg. Onnan, akkor másolja, és formázza azt JSON a könnyű ellenőrzés. 

![](./media/data-retention-privacy/06-vs.png)

Van egy olvashatóbb nézet is a Diagnosztika ablakban.

Weblapok esetén nyissa meg a böngésző hibakeresési ablakát.

![Nyomja le az F12 billentyűt, és nyissa meg a Hálózat lapot.](./media/data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>Írhatok kódot a telemetriai adatok szűréséhez az elküldés előtt?
Ez egy [telemetriai processzor beépülő modul](../../azure-monitor/app/api-filtering-sampling.md)írásával lehetséges.

## <a name="how-long-is-the-data-kept"></a>Mennyi ideig őrzi késed el az adatokat?
A nyers adatpontok (azaz az Analytics szolgáltatásban lekérdezhető és a keresésben vizsgálható elemek) legfeljebb 730 napig maradnak. 30, 60, 90, 120, 180, 270, 365, 550 vagy 730 nap [megőrzési időtartamot választhat.](https://docs.microsoft.com/azure/azure-monitor/app/pricing#change-the-data-retention-period) Ha 730 napnál hosszabb ideig kell megőriznie az adatokat, a [Folyamatos exportálás](../../azure-monitor/app/export-telemetry.md) segítségével másolhatja őket egy tárfiókba az adatok betöltése során. 

A 90 napnál hosszabb ideig tárolt adatok további díjakat vonnak maga után. További információ az Application Insights díjszabásáról az [Azure Monitor díjszabási lapján.](https://azure.microsoft.com/pricing/details/monitor/)

Az összesített adatok (azaz a számlálók, az átlagok és a Metrikakezelőben látható egyéb statisztikai adatok) 90 napig 1 perces szemcseszinten őrződnek meg.

[A hibakeresési pillanatképek](../../azure-monitor/app/snapshot-debugger.md) 15 napig tárolódnak. Ez az adatmegőrzési szabály alkalmazásonként van beállítva. Ha növelnie kell ezt az értéket, kérheti a növekedést egy támogatási eset megnyitásával az Azure Portalon.

## <a name="who-can-access-the-data"></a>Ki férhet hozzá az adatokhoz?
Az adatok láthatóak az Ön számára, és ha rendelkezik szervezeti fiókkal, a csapattagjai. 

Ön és a csapattagjai exportálhatják, és más helyekre másolhatók, és továbbadhatók másoknak.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>Mit tesz a Microsoft az alkalmazásom által az Application Insightsnak küldött adatokkal?
A Microsoft az adatokat csak a szolgáltatás nyújtásához használja.

## <a name="where-is-the-data-held"></a>Hol tartják az adatokat?
* Új Application Insights-erőforrás létrehozásakor kiválaszthatja a helyet. Az Application Insights régiónkénti elérhetőségéről itt olvashat [bővebben.](https://azure.microsoft.com/global-infrastructure/services/?products=all)

#### <a name="does-that-mean-my-app-has-to-be-hosted-in-the-usa-europe-or-southeast-asia"></a>Ez azt jelenti, hogy az alkalmazásomat az USA-ban, Európában vagy Délkelet-Ázsiában kell üzemeltetni?
* Nem. Az alkalmazás futtatható bárhol, akár a saját helyszíni gazdagépeken vagy a felhőben.

## <a name="how-secure-is-my-data"></a>Mennyire biztonságosak az adataim?
Az Application Insights egy Azure-szolgáltatás. A biztonsági szabályzatokat az [Azure biztonsági, adatvédelmi és megfelelőségi tanulmánya](https://go.microsoft.com/fwlink/?linkid=392408)ismerteti.

Az adatokat a Microsoft Azure-kiszolgálók tárolják. Az Azure Portalon található fiókok fiókkorlátozásait az [Azure biztonsági, adatvédelmi és megfelelőségi dokumentum](https://go.microsoft.com/fwlink/?linkid=392408)ismerteti.

A Microsoft munkatársai nem férhetnek hozzá az adataihoz. Az ön adataihoz csak az Ön engedélyével férünk hozzá, és ha az az Application Insights használatának támogatásához szükséges. 

Az összes ügyfelünk alkalmazásai között összesített adatokat (például az adatátviteli sebességet és a nyomkövetések átlagos méretét) az Application Insights fejlesztésére használjuk fel.

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>Lehet valaki mástelemetriai zavarhatja az Application Insights-adatok?
További telemetriai adatokat küldhetnek a fiókjába a műszerezési kulccsal, amely a weboldalak kódjában található. Elegendő további adattal a mérőszámok nem megfelelően tükrözik az alkalmazás teljesítményét és használatát.

Ha más projektekkel is megoszt a kódot, ne felejtse el eltávolítani a műszerkulcsot.

## <a name="is-the-data-encrypted"></a>Titkosítva vannak az adatok?
Minden adat titkosítva van inkameddig és az adatközpontok közötti mozgás közben.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>Az adatok titkosítva vannak az alkalmazásból az Application Insights-kiszolgálókra való átvitel közben?
Igen, https használatával küldünk adatokat a portálra szinte az összes SDK-ból, beleértve a webkiszolgálókat, eszközöket és HTTPS-weboldalakat. 

## <a name="does-the-sdk-create-temporary-local-storage"></a>Az SDK ideiglenes helyi tárolót hoz létre?

Igen, bizonyos telemetriai csatornák helyileg megőrzik az adatokat, ha egy végpont nem érhető el. Tekintse át alább, hogy mely keretrendszerek és telemetriai csatornák érintettek.

A helyi tárolót használó telemetriai csatornák ideiglenes fájlokat hoznak létre a TEMP vagy APPDATA könyvtárakban, amelyek az alkalmazás futtatott adott fiókra korlátozódnak. Ez akkor fordulhat elő, ha egy végpont átmenetileg nem érhető el, vagy ha eléri a szabályozási korlátot. A probléma megoldása után a telemetriai csatorna folytatja az összes új és a megőrzött adatok küldését.

A megőrzött adatok nincsenek helyileg titkosítva. Ha ez aggodalomra ad okot, tekintse át az adatokat, és korlátozza a személyes adatok gyűjtését. (További információ: [Személyes adatok exportálása és törlése.)](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data#how-to-export-and-delete-private-data)

Ha egy ügyfélnek speciális biztonsági követelményekkel kell konfigurálnia ezt a könyvtárat, keretrendszerenként konfigurálható. Győződjön meg arról, hogy az alkalmazást futtató folyamat írási hozzáféréssel rendelkezik ehhez a könyvtárhoz, de győződjön meg arról is, hogy ez a könyvtár védett, hogy a nem kívánt felhasználók ne olvassák be a telemetriai adatokat.

### <a name="java"></a>Java

`C:\Users\username\AppData\Local\Temp`adatok megőrzésére szolgál. Ez a hely nem konfigurálható a konfigurációs könyvtárból, és a mappa elérésére vonatkozó engedélyek a szükséges hitelesítő adatokkal rendelkező adott felhasználóra korlátozódnak. (További információ: [végrehajtás](https://github.com/Microsoft/ApplicationInsights-Java/blob/40809cb6857231e572309a5901e1227305c27c1a/core/src/main/java/com/microsoft/applicationinsights/internal/util/LocalFileSystemUtils.java#L48-L72).)

###  <a name="net"></a>.Net

Alapértelmezés `ServerTelemetryChannel` szerint az aktuális felhasználó helyi `%localAppData%\Microsoft\ApplicationInsights` alkalmazásadat-mappáját vagy ideiglenes mappáját `%TMP%`használja. (Lásd [a megvalósítást](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) itt.)


Konfigurációs fájlon keresztül:
```xml
<TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel,   Microsoft.AI.ServerTelemetryChannel">
    <StorageFolder>D:\NewTestFolder</StorageFolder>
</TelemetryChannel>
```

Kódon keresztül:

- ServerTelemetryChannel eltávolítása a konfigurációs fájlból
- Adja hozzá ezt a kódrészletet a konfigurációhoz:
  ```csharp
  ServerTelemetryChannel channel = new ServerTelemetryChannel();
  channel.StorageFolder = @"D:\NewTestFolder";
  channel.Initialize(TelemetryConfiguration.Active);
  TelemetryConfiguration.Active.TelemetryChannel = channel;
  ```

### <a name="netcore"></a>NetCore

Alapértelmezés `ServerTelemetryChannel` szerint az aktuális felhasználó helyi `%localAppData%\Microsoft\ApplicationInsights` alkalmazásadat-mappáját vagy ideiglenes mappáját `%TMP%`használja. (Lásd [a megvalósítást](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) itt.) Linux környezetben a helyi tároló le lesz tiltva, kivéve, ha egy tárolómappa van megadva.

A következő kódrészlet bemutatja, `ServerTelemetryChannel.StorageFolder` hogyan `ConfigureServices()` kell `Startup.cs` beállítani az osztály metódusában:

```csharp
services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
```

(További információ: [AspNetCore egyéni konfiguráció .)](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration)

### <a name="nodejs"></a>Node.js

Alapértelmezés `%TEMP%/appInsights-node{INSTRUMENTATION KEY}` szerint a rendszer az adatok megőrzésére szolgál. A mappa elérésére vonatkozó engedélyek az aktuális felhasználóra és a rendszergazdákra korlátozódnak. (Lásd [a megvalósítást](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Sender.ts) itt.)

A mappaelőtag `appInsights-node` felülbírálható a [Sender.ts](https://github.com/Microsoft/ApplicationInsights-node.js/blob/7a1ecb91da5ea0febf5ceab13d6a4bf01a63933d/Library/Sender.ts#L384)fájlban `Sender.TEMPDIR_PREFIX` található statikus változó futásidejű értékének módosításával.

### <a name="javascript-browser"></a>JavaScript (böngésző)

[A HTML5 munkamenet-tároló](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage) az adatok megőrzésére szolgál. Két külön puffert `AI_buffer` használnak: és `AI_sent_buffer`. A kötegelt és elküldendő telemetria a `AI_buffer`tárolóban tárolódik. Az imént küldött telemetriai adatok kerülnek, `AI_sent_buffer` amíg a betöltési kiszolgáló válaszol, hogy sikeresen megkapta. A telemetriai adatok sikeres fogadása után a rendszer eltávolítja az összes pufferből. Átmeneti hibák esetén (például egy felhasználó elveszíti a hálózati `AI_buffer` kapcsolatot), a telemetriai adatok mindaddig érvényben maradnak, amíg sikeresen meg nem érkezik, vagy a betöltési kiszolgáló nem válaszol arra, hogy a telemetriai adatok érvénytelenek (például rossz séma vagy túl régi).

A telemetriai pufferek [`enableSessionStorageBuffer`](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/legacy/JavaScript/JavaScriptSDK.Interfaces/IConfig.ts#L31) letilthatók a `false`beállítással. Ha a munkamenet-tároló ki van kapcsolva, a helyi tömb helyett állandó tárolóként lesz használva. Mivel a JavaScript SDK egy ügyféleszközön fut, a felhasználó a böngésző fejlesztői eszközein keresztül férhet hozzá ehhez a tárolóhelyhez.

### <a name="opencensus-python"></a>OpenCensus Python

Alapértelmezés szerint az OpenCensus Python SDK az aktuális felhasználói mappát `%username%/.opencensus/.azure/`használja. A mappa elérésére vonatkozó engedélyek az aktuális felhasználóra és a rendszergazdákra korlátozódnak. (Lásd [a megvalósítást](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/storage.py) itt.) A megőrzött adatokat tartalmazó mappa a telemetriai adatokat létrehozó Python-fájlról lesz elnevezve.

A tárolási fájl helyét megváltoztathatja, ha `storage_path` átadja a paramétert az Ön által használt exportőr konstruktorában.

```python
AzureLogHandler(
  connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000',
  storage_path='<your-path-here>',
)
```

## <a name="how-do-i-send-data-to-application-insights-using-tls-12"></a>Hogyan küldhetek adatokat az Application Insightsnak a TLS 1.2 használatával?

Az Application Insights-végpontok ba történő átvitel során az adatok biztonságának biztosítása érdekében javasoljuk, hogy az ügyfelek konfigurálják az alkalmazásukat legalább a Transport Layer Security (TLS) 1.2 használatára. A TLS/Secure Sockets Layer (SSL) régebbi verziói sebezhetőnek bizonyultak, és bár jelenleg is dolgoznak a visszamenőleges kompatibilitás érdekében, **nem ajánlottak,** és az iparág gyorsan elmozdul, hogy felhagyjon a régebbi protokollok támogatásával. 

[2018. június 30-ra](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) tűzte ki a [PcI Biztonsági Szabványügyi Tanácsa](https://www.pcisecuritystandards.org/) a TLS/SSL régebbi verzióinak letiltására és a biztonságosabb protokollokra való frissítésre vonatkozó határidőt. Miután az Azure eldobja az örökölt támogatást, ha az alkalmazás/ügyfél nem tud kommunikálni legalább TLS 1.2-n keresztül, nem tud adatokat küldeni az Application Insightsnak. Az alkalmazás TLS-támogatásának teszteléséhez és érvényesítéséhez alkalmazott módszer az operációs rendszertől/platformtól, valamint az alkalmazás által használt nyelvtől/keretrendszertől függően változik.

Nem javasoljuk kifejezetten, hogy az alkalmazást csak a TLS 1.2 használatára vonatkozóan állítja be, kivéve, ha ez megtöri a platformszintű biztonsági funkciókat, amelyek lehetővé teszik az újabb, biztonságosabb protokollok automatikus észlelését és kihasználását, amint elérhetővé válnak, például TLS 1.3. Javasoljuk, hogy az alkalmazás kódjának alapos naplózását végezze el az adott TLS/SSL-verziók hardcoding-ei hez.

### <a name="platformlanguage-specific-guidance"></a>Platform-/nyelvspecifikus útmutató

|Platform/nyelv | Támogatás | További információ |
| --- | --- | --- |
| Azure App Services  | Támogatott, konfigurációra lehet szükség. | A támogatást 2018 áprilisában jelentették be. Olvassa el a közleményt a [konfigurációrészleteiért.](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/)  |
| Azure-függvényalkalmazások | Támogatott, konfigurációra lehet szükség. | A támogatást 2018 áprilisában jelentették be. Olvassa el a közleményt a [konfigurációrészleteiért.](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/) |
|.NET | Támogatott, a konfiguráció verziónként eltérő. | A .NET 4.7-es és korábbi verziók részletes konfigurációs információiért tekintse meg [az alábbi utasításokat.](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12)  |
|Állapotmonitor | Támogatott, konfiguráció szükséges | Az Állapotfigyelő az [operációs rendszer konfigurációjának](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) + [.NET konfigurációja](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12) alapján támogatja a TLS 1.2-es rendszert.
|Node.js |  A 10.5.0-s részben támogatott konfigurációra lehet szükség. | Használja a [hivatalos Node.js TLS/SSL dokumentációt](https://nodejs.org/api/tls.html) bármely alkalmazásspecifikus konfigurációhoz. |
|Java | A [JDK 6 121-es](https://www.oracle.com/technetwork/java/javase/overview-156328.html#R160_121) és [JDK 7-es](https://www.oracle.com/technetwork/java/javase/7u131-relnotes-3338543.html)frissítése tartalmazza a TLS 1.2-es jdk-támogatását. | A JDK 8 [alapértelmezés szerint a TLS 1.2-t](https://blogs.oracle.com/java-platform-group/jdk-8-will-use-tls-12-as-default)használja.  |
|Linux | A Linux disztribúciók általában az [OpenSSL-re](https://www.openssl.org) támaszkodnak a TLS 1.2 támogatáshoz.  | Ellenőrizze az [OpenSSL Changelog-ot,](https://www.openssl.org/news/changelog.html) hogy az OpenSSL verziója támogatott-e.|
| Windows 8.0 - 10 | Alapértelmezés szerint támogatott és engedélyezett. | Annak ellenőrzése, hogy továbbra is az [alapértelmezett beállításokat](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings)használja.  |
| Windows Server 2012 – 2016 | Alapértelmezés szerint támogatott és engedélyezett. | Annak ellenőrzése, hogy továbbra is az [alapértelmezett beállításokat használja-e](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 és Windows Server 2008 R2 SP1 | Támogatott, de alapértelmezés szerint nincs engedélyezve. | Az engedélyezés engedélyezéséről a [Transport Layer Security (TLS) beállításjegyzék-beállításlapján](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) olvashat.  |
| Windows Server 2008 SP2 | A TLS 1.2 támogatásához frissítés szükséges. | A Frissítés a Windows Server 2008 SP2 [rendszer TLS 1.2-s támogatásának hozzáadásáról](https://support.microsoft.com/help/4019276/update-to-add-support-for-tls-1-1-and-tls-1-2-in-windows-server-2008-s) nyújt tájékoztatást. |
|Windows Vista | Nem támogatott. | N/A

### <a name="check-what-version-of-openssl-your-linux-distribution-is-running"></a>Ellenőrizze, hogy az OpenSSL melyik verziója fut a Linux disztribúcióban

Az OpenSSL telepített verziójának ellenőrzéséhez nyissa meg a terminált, és futtassa a következőket:

```terminal
openssl version -a
```

### <a name="run-a-test-tls-12-transaction-on-linux"></a>Futtasson tesztTLS 1.2 tranzakciót Linuxon

Ha előzetes tesztet szeretne futtatni annak megállapítására, hogy a Linux-rendszer képes-e kommunikálni a TLS 1.2-n keresztül, nyissa meg a terminált, és futtassa:

```terminal
openssl s_client -connect bing.com:443 -tls1_2
```

## <a name="personal-data-stored-in-application-insights"></a>Az Application Insightsban tárolt személyes adatok

[Az Application Insights személyes adatokról szóló cikke](../../azure-monitor/platform/personal-data-mgmt.md) részletesen ismerteti ezt a problémát.

#### <a name="can-my-users-turn-off-application-insights"></a>Kikapcsolhatják a felhasználóim az Application Insightsot?
Nem közvetlenül. Nem biztosítunk olyan kapcsolót, amelyet a felhasználók működtethetnek az Application Insights kikapcsolásához.

Azonban egy ilyen funkciót az alkalmazásban valósíthat meg. Az összes SDK tartalmaz egy API-beállítást, amely kikapcsolja a telemetriai gyűjtemény. 

## <a name="data-sent-by-application-insights"></a>Az Application Insights által küldött adatok
Az SDK-k platformonként eltérőek, és számos összetevőtelepíthető. (Lásd: [Application Insights - áttekintés][start].) Minden összetevő különböző adatokat küld.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>A különböző forgatókönyvekben küldött adatosztályok

| Az Ön tevékenysége | Összegyűjtött adatosztályok (lásd a következő táblázatot) |
| --- | --- |
| [Application Insights SDK hozzáadása .NET webes projekthez][greenbrown] |ServerContext<br/>Következtetni<br/>Teljesítményszámlálók<br/>Kérelmek<br/>**Kivételek**<br/>Munkamenet<br/>felhasználók |
| [Állapotfigyelő telepítése az IIS-re][redfield] |Függőségek<br/>ServerContext<br/>Következtetni<br/>Teljesítményszámlálók |
| [Application Insights SDK hozzáadása Java webalkalmazáshoz][java] |ServerContext<br/>Következtetni<br/>Kérés<br/>Munkamenet<br/>felhasználók |
| [JavaScript SDK hozzáadása a weblaphoz][client] |Ügyfélkörnyezet <br/>Következtetni<br/>Oldal<br/>ClientPerf<br/>Ajax |
| [Alapértelmezett tulajdonságok megadása][apiproperties] |**Tulajdonságok** minden szabványos és egyéni eseményen |
| [TrackMetric hívása][api] |Numerikus értékek<br/>**Tulajdonságok** |
| [Műsorszám hívása*][api] |Esemény neve<br/>**Tulajdonságok** |
| [TrackException hívása][api] |**Kivételek**<br/>Halommemória-memóriakép<br/>**Tulajdonságok** |
| Az SDK nem tud adatokat gyűjteni. Példa: <br/> - nem fér nek hozzá a perf számlálókhoz<br/> - kivétel telemetriai inicializáló |SDK diagnosztika |

[Más platformokra vonatkozó SDK-k][platforms]esetében tekintse meg a dokumentumaikat.

#### <a name="the-classes-of-collected-data"></a>Az összegyűjtött adatok osztályai

| Összegyűjtött adatosztály | Tartalmazza (nem teljes lista) |
| --- | --- |
| **Tulajdonságok** |**Bármilyen adat - amelyet a kód határoz meg** |
| DeviceContext környezet |`Id`, IP, Területi beállítás, eszközmodell, hálózat, hálózattípusa, OEM-név, képernyőfelbontás, szerepkörpéldány, szerepkörnév, eszköztípus |
| Ügyfélkörnyezet |OS, területi beállítás, nyelv, hálózat, ablakfelbontás |
| Munkamenet |`session id` |
| ServerContext |Számítógép neve, területi beállítás, operációs rendszer, eszköz, felhasználói munkamenet, felhasználói környezet, művelet |
| Következtetni |földrajzi hely az IP-cím, időbélyeg, operációs rendszer, böngésző |
| Mérőszámok |Metrika neve és értéke |
| Események |Esemény neve és értéke |
| Oldalmegtekintések |URL és oldalnév vagy -képernyőnév |
| Ügyfél perf |URL/oldal neve, böngészőbetöltési idő |
| Ajax |HTTP-hívások weblapról kiszolgálóra |
| Kérelmek |URL, időtartam, válaszkód |
| Függőségek |Típus(SQL, HTTP, ...), kapcsolati karakterlánc vagy URI, szinkronizálás/aszinkron, időtartam, sikeres, SQL utasítás (állapotfigyelővel) |
| **Kivételek** |Típus, **üzenet**, híváshalmok, forrásfájl, sorszám,`thread id` |
| Összeomlik |`Process id`, `parent process id`, `crash thread id`; alkalmazás javítás, `id`, épít;  kivétel típusa, címe, oka; elhomályosított szimbólumok és regiszterek, bináris kezdő- és végpontcímek, bináris név és elérési út, cpu típus |
| Nyomkövetés |**Üzenet** és súlyossági szint |
| Teljesítményszámlálók |Processzoridő, rendelkezésre álló memória, kérelemarány, kivételarány, privát bájtok feldolgozása, I/O-sebesség, kérelem időtartama, kérési várólista hossza |
| Rendelkezésre állás |Webes teszt válaszkódja, az egyes tesztlépések időtartama, tesztneve, időbélyeg, siker, válaszidő, teszthely |
| SDK diagnosztika |Nyomkövetési üzenet vagy kivétel |

Az [ApplicationInsights.config fájl szerkesztésével az adatok egy részét kikapcsolhatja.][config]

> [!NOTE]
> Az ügyfél IP-címe a földrajzi hely kikövetkeztetésére szolgál, de alapértelmezés szerint az IP-adatok már nem tárolódnak, és az összes nullát a társított mezőbe írják. Ha többet szeretne megtudni a személyes adatok kezeléséről, javasoljuk ezt a [cikket.](../../azure-monitor/platform/personal-data-mgmt.md#application-data) Ha meg kell tárolni az IP-cím adatait [az IP-cím gyűjtési cikkünk](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection) végigvezeti a lehetőségeken.

## <a name="credits"></a>Kreditek
Ez a termék tartalmazza a MaxMind által [https://www.maxmind.com](https://www.maxmind.com)létrehozott GeoLite2 adatokat, amelyek a rendszerből érhetők el.



<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiproperties]: ../../azure-monitor/app/api-custom-events-metrics.md#properties
[client]: ../../azure-monitor/app/javascript.md
[config]: ../../azure-monitor/app/configuration-with-applicationinsights-config.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[java]: ../../azure-monitor/app/java-get-started.md
[platforms]: ../../azure-monitor/app/platforms.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md
