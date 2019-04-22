---
title: Azure Diagnostics bővítmény hibaelhárítása
description: Kapcsolatos problémák elhárítása az Azure Virtual Machines, Service Fabric és Cloud Services az Azure diagnostics használata esetén.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: robb
ms.openlocfilehash: 81c93900acf2d75eeb8e4fdc8da7d563f3a59595
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59699098"
---
# <a name="azure-diagnostics-troubleshooting"></a>Az Azure Diagnostics hibaelhárítása
Ez a cikk ismerteti a hibaelhárítási információkat, amelyek megfelelőek az Azure Diagnostics használatával. Az Azure diagnostics kapcsolatos további információkért lásd: [Azure Diagnostics – áttekintés](diagnostics-extension-overview.md).

## <a name="logical-components"></a>Logikai összetevőit
**Diagnosztikai beépülő modul indítója (DiagnosticsPluginLauncher.exe)**: Elindítja az Azure Diagnostics bővítmény. Adattárat biztosít, a belépési pont folyamatának.

**Diagnosztikai beépülő modult (DiagnosticsPlugin.exe)**: Konfigurálja, elindul, és kezeli a monitorozási ügynök élettartamát. Ez az a legfontosabb, amely indítja el a gyorsindítóból.

**Monitoring Agent (MonAgent\*.exe folyamatok)**: Figyeli, és gyűjti a diagnosztikai adatokat visz át.  

## <a name="logartifact-paths"></a>Napló/összetevő elérési utak
Az alábbiakban néhány fontos naplókat és az összetevők elérési útja. A dokumentum többi részében ezt az információt nevezzük.

### <a name="azure-cloud-services"></a>Azure Cloud Services
| Összetevő | Útvonal |
| --- | --- |
| **Az Azure Diagnostics-konfigurációs fájl** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\Config.txt |
| **Naplófájlok** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\ |
| **Diagnosztikai adatok a helyi tárolóban** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Tables |
| **Figyelési ügynök konfigurációs fájlját** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MaConfig.xml |
| **Az Azure Diagnostics bővítmény csomag** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version> |
| **Napló gyűjtése segédprogram elérési útja** | %SystemDrive%\Packages\GuestAgent\ |
| **MonAgentHost naplófájl** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

### <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)
| Összetevő | Útvonal |
| --- | --- |
| **Az Azure Diagnostics-konfigurációs fájl** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\RuntimeSettings |
| **Naplófájlok** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\ |
| **Diagnosztikai adatok a helyi tárolóban** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Tables |
| **Figyelési ügynök konfigurációs fájlját** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MaConfig.xml |
| **Állapotfájl** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\Status |
| **Az Azure Diagnostics bővítmény csomag** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>|
| **Napló gyűjtése segédprogram elérési útja** | C:\WindowsAzure\Packages |
| **MonAgentHost naplófájl** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

## <a name="metric-data-doesnt-appear-in-the-azure-portal"></a>Metrikaadatok nem jelenik meg az Azure Portalon
Az Azure Diagnostics biztosít a metrikaadatokat, amelyek az Azure Portalon is megjeleníthetők. Ha problémába ütközik az adatok a portálon jelennek meg, ellenőrizze a WADMetrics\* táblát az Azure Diagnostics storage-fiókban megtekintheti, ha a megfelelő metrika rekordok vannak-e.

Itt a **PartitionKey** a táblázat az a erőforrás azonosító, a virtuális gép vagy virtuálisgép-méretezési csoportot. **RowKey** mérőszám neve (más néven a teljesítményszámláló nevének).

Ellenőrizze az erőforrás-azonosító helytelen, ha **diagnosztikai** **konfigurációs** > **metrikák** > **ResourceId**megtekintheti, ha az erőforrás-azonosító megfelelően van-e beállítva.

Ha nem szerepel megjeleníthető adat a megadott metrika, ellenőrizze **diagnosztikai konfigurációja** > **PerformanceCounter** nem tartalmaz-e a metrika (számláló). Alapértelmezés szerint engedélyezzük a következő számlálókat:
- \Processor(_Total)\% Processor Time
- \Memory\Available Bytes
- \ASP.NET alkalmazások (__teljes__) \Requests/Sec
- \ASP.NET alkalmazások (__teljes__) \Errors összes/mp
- \ASP.NET\Requests Queued
- Elutasított \ASP.NET\Requests
- \Processor(w3wp)\% processzoridő
- \Process(w3wp)\Private Bytes
- \Process(WaIISHost)\% Processor Time
- \Process(WaIISHost)\Private Bytes
- \Process(WaWorkerHost)\% Processor Time
- \Process(WaWorkerHost)\Private Bytes
- \Memory\Page Faults/sec
- \.NET CLR memória (_globális_)\% Szemétgyűjtéssel töltött idő
- (C:) \LogicalDisk \Disk Zapsané Bajty/s
- Olvasási bájt/mp (C:) \LogicalDisk \Disk
- \LogicalDisk(D:)\Disk Write Bytes/sec
- \LogicalDisk(D:)\Disk Read Bytes/sec

Ha a konfiguráció megfelelően van-e állítva, de továbbra sem látja a metrikaadatokat, az alábbi irányelvek használatával háríthatja el.


## <a name="azure-diagnostics-is-not-starting"></a>Az Azure Diagnostics nem indul el
További információ az Azure Diagnostics miért nem indult el: a **DiagnosticsPluginLauncher.log** és **DiagnosticsPlugin.log** fájlokat a korábban megadott napló-fájlok helyét.

Ha ezek a naplók alapján `Monitoring Agent not reporting success after launch`, azt jelenti, hogy hiba történt a MonAgentHost.exe indítása. Tekintse meg a naplókat a jelzett helyen `MonAgentHost log file` az előző szakaszban.

A naplófájlok utolsó sora a kilépési kódot tartalmaz.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
Ha talál egy **negatív** kilépési kód, tekintse meg a [kilépési kód tábla](#azure-diagnostics-plugin-exit-codes) a a [szakaszban hivatkozott](#references).

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Diagnosztikai adatokat a program nem naplózza az Azure Storage
Megállapíthatja, hogy egyik adat sem jelennek meg van vagy néhány adat van jelennek meg.

### <a name="diagnostics-infrastructure-logs"></a>Diagnosztikai infrastruktúra naplói
Diagnosztikai hibákat naplózza a diagnosztikai infrastruktúra naplói. Győződjön meg arról, hogy engedélyezte a [jelentkezik be a konfiguráció a rögzítési diagnosztikai infrastruktúra](#how-to-check-diagnostics-extension-configuration). Gyorsan megtalálhatja vonatkozó hibaüzeneteket, amelyek megjelennek majd a `DiagnosticInfrastructureLogsTable` táblájában a konfigurált tárfiók.

### <a name="no-data-is-appearing"></a>Nincs adat jelenik-e meg
A rendszer, amely eseményadatokat nem jelenik meg a leggyakoribb oka, hogy a storage-fiók adatait nem megfelelően van-e definiálva.

Megoldás: Javítsa ki a diagnosztikai konfigurációja, majd telepítse újra a diagnosztikát.

Ha a tárfiók megfelelően konfigurálva, a távoli hozzáférés be a számítógépre, és ellenőrizze, hogy *DiagnosticsPlugin.exe* és *MonAgentCore.exe* futnak. Ha nem futnak, kövesse a [nem indítja el az Azure Diagnostics](#azure-diagnostics-is-not-starting).

Ha a folyamat fut, folytassa a [adatok rögzítése helyben első?](#is-data-getting-captured-locally) , és kövesse az ott található útmutatást.

Ha ez nem oldja meg a problémát, próbálja meg:

1. Az ügynök eltávolítása
2. C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics könyvtár eltávolítása
3. Telepítse újra a házirendügynök


### <a name="part-of-the-data-is-missing"></a>Az adatok egy részét hiányzik.
Ha bizonyos adatokat, de nem az összes, az azt jelenti, hogy a gyűjtemény/átvitel adatfolyamat megfelelően van-e beállítva. Kövesse az itt a struktúrát a probléma szűkítéséhez.

#### <a name="is-the-collection-configured"></a>Van konfigurálva a gyűjtemény?
Diagnosztikai beállítás összegyűjtendő adatok egy adott típusú vonatkozó utasításokat tartalmaz. [Áttekintheti a konfigurációt](#how-to-check-diagnostics-extension-configuration) , győződjön meg arról, hogy csak keres adatainak gyűjtésére konfigurált.

#### <a name="is-the-host-generating-data"></a>A gazdagép adatokat generáló?
- **Teljesítményszámlálók**: Nyissa meg a Teljesítményfigyelőt, és ellenőrizze a számlálót.

- **Nyomkövetési naplók**:  Távoli hozzáférés a virtuális géppel, és egy TextWriterTraceListener hozzáadásához az alkalmazás konfigurációs fájlban.  Lásd: https://msdn.microsoft.com/library/sk36c28t.aspx állíthatja be a szöveget figyelő.  Győződjön meg arról, hogy a `<trace>` elemnek `<trace autoflush="true">`.<br />
Ha nem látja a nyomkövetési naplók generált, több kapcsolatos hiányzik a nyomkövetési naplókat.

- **ETW-nyomkövetések**: Távoli hozzáférés a virtuális gép, és telepítse a PerfView.  A PerfView, futtassa a **fájl** > **felhasználói parancs** > **etwprovder1 figyelésére** > **etwprovider2**, és így tovább. A **figyelésére** parancs kis-és nagybetűket, és nem lehetnek szóközök közötti ETW-szolgáltatók vesszővel elválasztott listája. Ha a parancs futása sikertelen, kiválaszthatja a **Log** gombra a jobb alsó részén a Perfview eszköz mit próbált meg futni, és milyen eredmény megtekintéséhez.  Feltéve, hogy helyesek-e a bemeneti, egy új ablakban jelenik meg. Néhány másodperc alatt megkezdheti a ETW-nyomkövetések jelent meg.

- **Eseménynaplók**: Távoli hozzáférés a virtuális géppel. Nyissa meg `Event Viewer`, majd győződjön meg róla, hogy létezik-e az események.

#### <a name="is-data-getting-captured-locally"></a>Adatok helyben első rögzített?
Ezután ellenőrizze, hogy az adatok rögzítése helyben van beolvasása.
Az adatok helyben van tárolva a `*.tsf` fájljait a helyi tároló diagnosztikai adatokat. Különböző típusú naplók lekérése gyűjtött különböző `.tsf` fájlokat. A nevek hasonlóak a táblanevek, az Azure Storage-ban.

Ha például `Performance Counters` a gyűjtött kigyűjtése `PerformanceCountersTable.tsf`. Eseménynaplók gyűjtött kigyűjtése `WindowsEventLogsTable.tsf`. Útmutatásait a [a helyi kinyerési](#local-log-extraction) szakaszban nyissa meg a helyi gyűjtés fájlokat, és ellenőrizze, hogy azokat a lemezen első gyűjtött látja.

Ha nem jelenik meg az első helyileg gyűjtött naplók, és már ellenőrizte, hogy a gazdagép adatokat generáló, konfigurációs hiba valószínűleg rendelkezik. Gondosan tekintse át a konfigurációt.

Emellett tekintse át a konfigurációt, MonitoringAgent MaConfig.xml számára létrehozott. Győződjön meg arról, hogy nincs-e egy a megfelelő naplófájlok forrás leíró szakaszt. Ellenőrizze, hogy azt ne vesszenek fordítási problémái vannak a diagnosztikai és a monitorozási ügynök konfigurációt között.

#### <a name="is-data-getting-transferred"></a>Az első átvitt adatok?
Ha ellenőrizte, hogy az adatok rögzítése helyben van első, de továbbra sem látja, a storage-fiókban, tegye a következőket:

- Ellenőrizze, hogy a megadott egy helyes tárfiók, valamint, hogy még nem jelennek az adott tárfiók kulcsok. Az Azure Cloud Services esetén néha láthatjuk, hogy nincs-e frissítés személyek `useDevelopmentStorage=true`.

- Győződjön meg arról, hogy helyesen szerepel-e a megadott tárfiókhoz. Ellenőrizze, hogy nem rendelkezik, amely megakadályozza, hogy az összetevők éri el a nyilvános tárolási végpontok hálózati korlátozások. Az egyik lehetőség, amely távelérési be a számítógépre, és írjon valamit ugyanazt a tárfiókot, majd próbálja.

- Végül megtekintheti milyen hibák a monitorozási ügynök által jelentett folyamatban van. A figyelőügynök ír a naplók `maeventtable.tsf`, a helyi tároló diagnosztikai adatok találhatók. Kövesse az utasításokat a [a helyi kinyerési](#local-log-extraction) ennek a fájlnak a következő szakaszban. Próbálja meg meghatározni, hogy van-e `errors` , amely jelzi, hogy a helyi fájlok tárolókba olvasása sikertelen.

### <a name="capturing-and-archiving-logs"></a>A rögzítés és naplói archiválása
Mértékegységeként ügyfélszolgálaton, megkérhetik, hogy elsőként e gyűjteni a gépén. Időt takaríthat módon, hogy saját maga. Futtassa a `CollectGuestLogs.exe` segédprogramját naplóútvonalat adatgyűjtési segédprogramot. Létrehoz egy .zip fájlt az összes vonatkozó Azure-naplók ugyanabban a mappában.

## <a name="diagnostics-data-tables-not-found"></a>Diagnosztikai adatok táblák nem található
A táblák az Azure storage-ban, amelyek rendelkeznek az ETW-események neve a következő kód használatával:

```csharp
        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;
```

Például:

```XML
        <EtwEventSourceProviderConfiguration provider="prov1">
          <Event id="1" />
          <Event id="2" eventDestination="dest1" />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider="prov2">
          <DefaultEvents eventDestination="dest2" />
        </EtwEventSourceProviderConfiguration>
```
```JSON
"EtwEventSourceProviderConfiguration": [
    {
        "provider": "prov1",
        "Event": [
            {
                "id": 1
            },
            {
                "id": 2,
                "eventDestination": "dest1"
            }
        ],
        "DefaultEvents": {
            "eventDestination": "DefaultEventDestination",
            "sinks": ""
        }
    },
    {
        "provider": "prov2",
        "DefaultEvents": {
            "eventDestination": "dest2"
        }
    }
]
```
Ez a kód a négy táblát hoz létre:

| Esemény | Tábla neve |
| --- | --- |
| szolgáltató = "prov1" &lt;Event id = "1" /&gt; |WADEvent + MD5("prov1") + "1" |
| provider=”prov1” &lt;Event id=”2” eventDestination=”dest1” /&gt; |WADdest1 |
| szolgáltató = "prov1" &lt;DefaultEvents /&gt; |WADDefault+MD5("prov1") |
| szolgáltató = "prov2" &lt;DefaultEvents eventDestination = "dest2" /&gt; |WADdest2 |

## <a name="references"></a>Referencia

### <a name="how-to-check-diagnostics-extension-configuration"></a>Diagnosztikai bővítmény konfigurációjának ellenőrzése
A bővítmény konfigurációjának ellenőrzése a legegyszerűbb módja az, hogy lépjen [Azure erőforrás-kezelő](http://resources.azure.com), és lépjen a virtuális gép vagy a cloud service helye az Azure Diagnostics bővítmény (IaaSDiagnostics / PaaDiagnostics) van.

Másik lehetőségként a gépet, és tekintse meg az Azure diagnosztikai konfigurációs fájl a napló összetevők elérésiút-szakaszával leírt távoli asztal.

Mindkét esetben keressen **Microsoft.Azure.Diagnostics**, majd a **xmlCfg** vagy **WadCfg** mező.

Ha a keresett virtuális gépen, és a **WadCfg** mező szerepel, ez azt jelenti, hogy a konfigurációs JSON formátumban vannak. Ha a **xmlCfg** mező szerepel, az azt jelenti, hogy a konfigurációs XML-ben pedig base64-kódolású. Kell [dekódolni a](https://www.bing.com/search?q=base64+decoder) Diagnostics által betöltött XML-kód megtekintéséhez.

A cloud service szerepkörhöz, a konfiguráció a lemezről, válasszon ki az adatok is, ha base64-kódolású, ezért meg kell [dekódolni a](https://www.bing.com/search?q=base64+decoder) Diagnostics által betöltött XML-kód megtekintéséhez.

### <a name="azure-diagnostics-plugin-exit-codes"></a>Az Azure Diagnostics beépülő modul kilépési kódok
A beépülő modul a következő kilépési kódokat ad vissza:

| Kilépési kód | Leírás |
| --- | --- |
| 0 |Siker. |
| -1 |Általános hiba. |
| -2 |Nem sikerült betölteni a rcf fájlt.<p>Ez a belső hiba csak akkor történjen meg, ha a Vendég ügynök beépülő modul indítója manuálisan hív helytelenül a virtuális gépen. |
| -3 |A diagnosztika konfigurációs fájl nem tölthető be.<p><p>Megoldás: Egy konfigurációs fájl nem továbbítja a séma érvényesítése miatt. A megoldás, ha egy konfigurációs fájl, amely megfelel a séma biztosít. |
| -4 |A figyelőügynök diagnosztikai egy másik példánya már használja a helyi erőforrás-könyvtár.<p><p>Megoldás: Adjon meg másik értéket **LocalResourceDirectory**. |
| -6 |A Vendég ügynök beépülő modul indítója indítsa el a diagnosztika érvénytelen parancssorral történt kísérlet.<p><p>Ez a belső hiba csak akkor történjen meg, ha a Vendég ügynök beépülő modul indítója manuálisan hív helytelenül a virtuális gépen. |
| -10 |A diagnosztikai beépülő modul egy nem kezelt kivétel miatt kilépett. |
| -11 |A vendégügynök nem tudta létrehozni a folyamat indításakor, és a figyelőügynök felügyeletéért felelős.<p><p>Megoldás: Győződjön meg arról, hogy elérhetők-e elegendő rendszererőforrásra új folyamatok elindításához.<p> |
| -101 |Érvénytelen argumentumok a diagnosztikai beépülő modul hívása során.<p><p>Ez a belső hiba csak akkor történjen meg, ha a Vendég ügynök beépülő modul indítója manuálisan hív helytelenül a virtuális gépen. |
| -102 |A beépülő modul folyamat nem tudja inicializálása sikertelen.<p><p>Megoldás: Győződjön meg arról, hogy elérhetők-e elegendő rendszererőforrásra új folyamatok elindításához. |
| -103 |A beépülő modul folyamat nem tudja inicializálása sikertelen. Pontosabban nem tudja létrehozni a naplózó objektumot.<p><p>Megoldás: Győződjön meg arról, hogy elérhetők-e elegendő rendszererőforrásra új folyamatok elindításához. |
| -104 |Nem sikerült betölteni a vendégügynök által biztosított rcf fájlt.<p><p>Ez a belső hiba csak akkor történjen meg, ha a Vendég ügynök beépülő modul indítója manuálisan hív helytelenül a virtuális gépen. |
| -105 |A diagnosztikai beépülő modul a diagnosztikai konfigurációs fájl nem nyitható meg.<p><p>Belső hiba csak akkor történjen meg, ha a diagnosztikai beépülő modul manuális hív helytelenül a virtuális gépen. |
| -106 |A diagnosztika konfigurációs fájl nem olvasható.<p><p>Egy konfigurációs fájl nem továbbítja a séma érvényesítése miatt. <br><br>Megoldás: Adja meg a konfigurációs fájlt, amely megfelel a sémával. További információkért lásd: [diagnosztikai bővítmény konfigurációjának ellenőrzésével](#how-to-check-diagnostics-extension-configuration). |
| -107 |Az erőforrás directory fázis a monitorozási ügynök érvénytelen.<p><p>Ez a belső hiba csak akkor történjen meg, ha a monitorozási ügynök manuálisan hív helytelenül a virtuális gépen.</p> |
| -108 |Nem alakítható át a diagnosztikai konfigurációs fájlt a monitorozási ügynök konfigurációs fájlba.<p><p>Ez a belső hiba csak akkor történjen meg, ha a diagnosztikai beépülő modul manuális meghívása egy konfigurációs fájl érvénytelen. |
| -110 |Általános diagnosztikai konfigurációs hiba.<p><p>Ez a belső hiba csak akkor történjen meg, ha a diagnosztikai beépülő modul manuális meghívása egy konfigurációs fájl érvénytelen. |
| -111 |Nem sikerült elindítani a monitorozási ügynök.<p><p>Megoldás: Győződjön meg arról, hogy elegendő rendszererőforrásra érhetők el. |
| -112 |Általános hiba |

### <a name="local-log-extraction"></a>A helyi kivonása
A monitorozási ügynök összegyűjti a naplókat és az összetevők, `.tsf` fájlokat. A `.tsf` fájl nem olvasható, de átalakíthatja őket egy `.csv` módon:

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
Új fájl neve `<relevantLogFile>.csv` jön létre az adott elérési úton a megfelelő `.tsf` fájlt.

>[!NOTE]
> Csak van szükség, a segédprogram futtatásához a fő .tsf fájlt (például PerformanceCountersTable.tsf). A hozzájuk tartozó fájlokat (például PerformanceCountersTables_\*\*001.tsf, PerformanceCountersTables_\*\*002.tsf, és így tovább) feldolgozása automatikusan történik.

### <a name="more-about-missing-trace-logs"></a>További információ a nyomkövetési naplók hiányzik

>[!NOTE]
> A következő információkat vonatkozik többnyire Azure Cloud Servicesben, ha olyan alkalmazást, amely az IaaS virtuális gép fut-e már konfigurálta a DiagnosticsMonitorTraceListener.

- Győződjön meg arról, hogy a **DiagnosticMonitorTraceListener** konfigurálva van a web.config vagy az App.config fájlt.  Ez alapértelmezés szerint a cloud service-projektek van konfigurálva. Azonban egyes ügyfelek comment azt ki, amelynek hatására a nyomkövetési utasításokat diagnosztika nem gyűjthetők.

- Ha a naplók nem az első írt a **OnStart** vagy **futtatása** módot, győződjön meg arról, a **DiagnosticMonitorTraceListener** az app.config fájlban van.  Alapértelmezés szerint a web.config fájlban, de, amely csak a w3wp.exe belül futó vonatkozik. Tehát van szüksége, rögzíthesse a hívásláncokat, a WaIISHost.exe futó az app.config fájlban.

- Győződjön meg arról, hogy használ **Diagnostics.Trace.TraceXXX** helyett **Diagnostics.Debug.WriteXXX.** A hibakeresési utasításokat kiadott buildjét törlődnek.

- Ellenőrizze, hogy a lefordított kódot rendelkezik-e a **Diagnostics.Trace sorok** (használja a magában, ildasm vagy ILSpy ellenőrzése). **Diagnostics.Trace** parancsok el lesznek távolítva a lefordított bináris fájl, kivéve, ha a nyomkövetés feltételes fordítási szimbólumot használhatja. Ez a gyakori probléma akkor fordul elő, amikor msbuild használatával állítsa össze a projektet.   

## <a name="known-issues-and-mitigations"></a>Ismert problémák és megoldások
A következő ismert megoldások szolgáltatással kapcsolatos ismert problémák listáját:

**1. .NET 4.5-ös függőség**

Windows Azure diagnosztikai bővítmény keretrendszer .NET 4.5-ös vagy újabb modul függőséget tartalmaz. Írásának, az Azure Cloud Servicesben üzembe helyezett összes gép, valamint az Azure-beli virtuális gépeken alapuló összes hivatalos kép, .NET 4.5-ös vagy újabb verziója szükséges.

Továbbra is lehetséges esetlegesen fellépő egy olyan helyzetet, ahol megkísérli futtatni a Windows Azure diagnosztikai bővítmény egy gépen, amelyen nincs telepítve a .NET 4.5-ös vagy újabb legyen. Ez akkor történik, a gép régi lemezképet, vagy egy pillanatkép létrehozásakor, vagy ha a saját egyéni lemez állapotba.

Ez általában egy kilépési kóddal jegyzékfájlok **255** futtatásakor **DiagnosticsPluginLauncher.exe.** Hiba történik, a következő nem kezelt kivétel miatt:
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**Megoldás:** Telepítse a .NET 4.5-ös vagy újabb verzióját a számítógépen.

**2. Teljesítményadatok számlálók érhető el a storage, de nem jelenik a portálon**

A portál felülete, a virtual machines bizonyos teljesítményszámlálók alapértelmezés szerint jeleníti meg. Ha a teljesítményszámlálók nem jelenik meg, és tudja, hogy az adatok első jön létre, mert a storage-ban érhető el, ellenőrizze a következőket:

- Az adatok a storage-ban van-e számláló nevek angol nyelven. Ha a számlálók neve nem angol nyelven, a portál metrikadiagram aktívnak, nem történik meg. **Kockázatcsökkentési**: A gép nyelv az angol nyelvű rendszerfiókok értékre módosítani. Ehhez válassza ki a **Vezérlőpult** > **régió** > **felügyeleti** > **beállításainak**. Ezután törölje a jelölését **üdvözlő képernyő és a system fiók** úgy, hogy az egyéni nyelvi a rendszer nem alkalmazza a rendszer fiók.

- Ha helyettesítő karaktereket használ (\*) a teljesítményszámlálók nevét, a portál nem tudnia kell korrelálni a beállított és a gyűjtött számláló, ha a teljesítményszámlálók érkezik az Azure Storage-fogadó. **Kockázatcsökkentési**: Ellenőrizze, hogy használjon helyettesítő elemeket, és bontsa ki a portál rendelkezik, a (\*), a teljesítményszámlálók adatait szeretné irányítani a ["Az Azure Monitor"-fogadó](diagnostics-extension-schema.md#diagnostics-extension-111).

