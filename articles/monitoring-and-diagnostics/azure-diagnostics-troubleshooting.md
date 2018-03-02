---
title: "Hibaelhárítás az Azure Diagnostics |} Microsoft Docs"
description: "Kapcsolatos problémák elhárítása az Azure diagnostics Azure virtuális gépek, a Service Fabric vagy a Cloud Services használata esetén."
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 66469bce-d457-4d1e-b550-a08d2be4d28c
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/12/2017
ms.author: robb
ms.openlocfilehash: e194c2898616d5a19782039d38592c59f6b0c576
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2018
---
# <a name="azure-diagnostics-troubleshooting"></a>Hibaelhárítás az Azure Diagnostics
Ez a cikk ismerteti az Azure Diagnostics használatára vonatkozó hibaelhárítási információ. Az Azure diagnostics kapcsolatos további információkért lásd: [Azure Diagnostics áttekintése](azure-diagnostics.md).

## <a name="logical-components"></a>Logikai összetevők
**Diagnosztika beépülő modulja indítója (DiagnosticsPluginLauncher.exe)**: elindítja az Azure Diagnostics-bővítmény. Működik, mint a belépési pont folyamatának.

**Diagnosztika beépülő modul (DiagnosticsPlugin.exe)**: konfigurálja, elindul, és kezeli a figyelési ügynök élettartamát. A fő folyamat, amely a indítója indít.

**Figyelési ügynök (MonAgent\*.exe folyamatok)**: figyeli, gyűjt, és átviszi a diagnosztikai adatait.  

## <a name="logartifact-paths"></a>Napló/összetevőinek elérési utak
Az alábbiakban néhány fontos naplókat és az összetevőinek elérési útvonalát. Ez a dokumentum többi részén adatokat hivatkozunk.

### <a name="azure-cloud-services"></a>Azure Cloud Services
| Összetevő | Útvonal |
| --- | --- |
| **Az Azure Diagnostics konfigurációs fájl** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\Config.txt |
| **Naplófájlok** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\ |
| **Diagnosztikai adatok helyi tárolóból.** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Tables |
| **Figyelési ügynök konfigurációs fájl** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MaConfig.xml |
| **Az Azure Diagnostics-kiterjesztési csomag** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version> |
| **Naplófájl elérési útvonala segédprogram** | %SystemDrive%\Packages\GuestAgent\ |
| **MonAgentHost naplófájl** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

### <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)
| Összetevő | Útvonal |
| --- | --- |
| **Az Azure Diagnostics konfigurációs fájl** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\RuntimeSettings |
| **Naplófájlok** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\ |
| **Diagnosztikai adatok helyi tárolóból.** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Tables |
| **Figyelési ügynök konfigurációs fájl** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MaConfig.xml |
| **Állapotfájl** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\Status |
| **Az Azure Diagnostics-kiterjesztési csomag** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>|
| **Naplófájl elérési útvonala segédprogram** | C:\WindowsAzure\Packages |
| **MonAgentHost naplófájl** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

## <a name="metric-data-doesnt-appear-in-the-azure-portal"></a>Metrika adatok nem jelennek meg az Azure-portálon
Az Azure Diagnostics adja meg az Azure-portálon megjelenő metrika adatait. Ha jogosultak a portálon, akkor ellenőrizze a WADMetrics\* az Azure diagnosztikai tárfiók, hogy a megfelelő metrika rekordok hiba-tábla. 

Itt a **PartitionKey** a tábla a erőforrás azonosítója, virtuális gép vagy virtuálisgép-méretezési csoport van. **RowKey** metrika neve (más néven a teljesítményszámláló nevének).

Ha az erőforrás-azonosító helytelen, **diagnosztika** **konfigurációs** > **metrikák** > **ResourceId**megjelenítéséhez, ha az erőforrás-azonosító megfelelően van-e beállítva.

Ha az adott metrika nincsenek adatok, **diagnosztikai konfigurációja** > **PerformanceCounter** meg, ha a mérték (teljesítményszámláló) tartalmazza. Alapértelmezés szerint engedélyezzük az alábbi számlálók értékét:
- \Processor(_Total)\% Processor Time
- \Memory\Available Bytes
- \ASP.NET alkalmazások (__teljes__) \Requests/Sec
- \ASP.NET alkalmazások (__teljes__) \Errors száma/s
- A várólistára \ASP.NET\Requests
- \ASP.NET\Requests Rejected
- \Processor(w3wp)\% processzoridő
- \Process(w3wp)\Private Bytes
- \Process(WaIISHost)\% processzoridő
- \Process(WaIISHost)\Private Bytes
- \Process(WaWorkerHost)\% Processor Time
- \Process(WaWorkerHost)\Private Bytes
- \Memory\Page hiba/mp
- \.NET CLR-memória (_globális_)\% töltött idő
- \LogicalDisk (C:) \Disk írt bájt/mp
- \LogicalDisk (C:) \Disk olvasott bájt/mp
- (D) \LogicalDisk \Disk írt bájt/mp
- (D) \LogicalDisk \Disk olvasott bájt/mp

Ha a konfiguráció megfelelően van beállítva, de még nem látja a metrikaadatokat, használja a következő irányelveket háríthatja el.


## <a name="azure-diagnostics-isnt-starting"></a>Az Azure Diagnostics nem indítása
További információ az Azure Diagnostics miért nem indult el: a **DiagnosticsPluginLauncher.log** és **DiagnosticsPlugin.log** fájlokat a korábban megadott napló-fájlok helyét. 

Ha ezek a naplók alapján `Monitoring Agent not reporting success after launch`, az azt jelenti, hogy hiba történt a MonAgentHost.exe indítása. Az talál a helyen, a feltüntetett `MonAgentHost log file` az előző szakaszban.

A naplófájlok utolsó sora a kilépési kódot tartalmaz.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
Ha talál egy **negatív** kilépési kód, tekintse meg a [kilépési kód tábla](#azure-diagnostics-plugin-exit-codes) a a [szakasz hivatkozik](#references).

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Azure Storage nem naplózza a diagnosztikai adatok
Határozza meg, ha van szereplő adatokat, vagy az adatok egy részét jelenik-e az meg.

### <a name="diagnostics-infrastructure-logs"></a>Diagnosztika infrastruktúra naplók
Diagnosztika a diagnosztika infrastruktúra naplók hibákat naplózza. Győződjön meg arról, hogy engedélyezte a [diagnosztika infrastruktúra rögzítési naplózza a konfiguráció](#how-to-check-diagnostics-extension-configuration). Gyorsan megtekintheti megjelenő vonatkozó hibákat, majd a `DiagnosticInfrastructureLogsTable` tábla konfigurált tárfiókba.

### <a name="no-data-is-appearing"></a>Nincs adat jelenik-e meg
A leggyakoribb események adatait nem látható minden oka, hogy a tárfiók adatait nem megfelelően van-e definiálva.

Megoldás: A diagnosztika-konfiguráció kijavítása, majd telepítse újra a diagnosztika.

Ha a tárfiók megfelelően konfigurált távoli elérést a számítógép be-e, és ellenőrizze, hogy DiagnosticsPlugin.exe és MonAgentCore.exe futnak. Ha nem futnak, kövesse a [nem indítja az Azure Diagnostics](#azure-diagnostics-is-not-starting). 

Ha a folyamat fut, folytassa a [első helyileg rögzített adatok?](#is-data-getting-captured-locally) és kövesse az utasításokat van.

### <a name="part-of-the-data-is-missing"></a>Az adatok egy részét hiányzik.
Ha bizonyos adatokat, de nem mindegyik kap, az azt jelenti, hogy az adatok gyűjtemény/átvitel folyamat helyesen van-e állítva. Kövesse az itt a alszakaszokat szűkítéséhez a problémát.

#### <a name="is-the-collection-configured"></a>Van konfigurálva a gyűjtemény? 
A diagnosztikai konfigurációja egy bizonyos típusú adatok összegyűjtésére utasításait tartalmazza. [Tekintse át a konfigurációs](#how-to-check-diagnostics-extension-configuration) ellenőrzése, hogy csak keres, amelyet a gyűjteményhez beállított adatokat.

#### <a name="is-the-host-generating-data"></a>A gazdagép előállító adatokat?
- **Teljesítményszámlálók**: Nyissa meg a Teljesítményfigyelőben, és ellenőrizze a számlálót.

- **Nyomkövetési naplók**: távoli majd a virtuális Gépet, majd adja hozzá a értékének a TextWriterTraceListener figyelőre az alkalmazás konfigurációs fájljában.  Tekintse meg a szöveg figyelő beállítása http://msdn.microsoft.com/library/sk36c28t.aspx.  Győződjön meg arról, hogy a `<trace>` elem `<trace autoflush="true">`.<br />
Ha nem látja a nyomkövetési naplók létrehozása folyamatban, lásd: [további információk a nyomkövetési naplók hiányzó](#more-about-trace-logs-missing).

- **ETW-nyomkövetési**: a távelérés a virtuális gép és a telepítés PerfView.  A PerfView, futtassa az **fájl** > **felhasználói parancs** > **etwprovder1 figyelésére** > **etwprovider2**, és így tovább. A **figyelésére** parancs kis-és nagybetűket, és nem lehetnek szóközök közötti ETW-szolgáltató vesszővel elválasztott listája. Ha a parancs nem fut, válassza a **napló** gombra a jobb alsó sarkában, mi próbált meg futtatni, és milyen az eredmény a Perfview eszköz.  Ha az adatok helyesek, egy új ablakban jelenik meg. Néhány másodpercen belül megkezdheti a ETW-nyomkövetési jelent.

- **Eseménynaplók**: távelérés-be a virtuális Gépet. Nyissa meg `Event Viewer`, majd győződjön meg róla, hogy létezik-e az eseményeket.

#### <a name="is-data-getting-captured-locally"></a>Adatok helyben első rögzített?
Ezt követően győződjön meg arról, hogy az adatok helyileg rögzített első.
Az adatok helyben tárolódnak `*.tsf` -fájlok [diagnosztikai adatok a helyi tárolójába](#log-artifacts-path). Különböző típusú naplók beolvasása gyűjtött különböző `.tsf` fájlokat. A nevek hasonlóak a táblanevek, az Azure Storage. 

Például `Performance Counters` kigyűjtése az összegyűjtött `PerformanceCountersTable.tsf`. Eseménynaplók gyűjtött beolvasása `WindowsEventLogsTable.tsf`. Kövesse az utasításokat a a [helyi napló kinyerés](#local-log-extraction) a helyi gyűjtés fájlok megnyitásához, és győződjön meg arról, hogy látja első összegyűjtése, a lemez szakasz.

Ha nem látja a helyileg gyűjtött első naplókat, és már ellenőrizte, hogy a gazdagép adatokat előállító, valószínűleg rendelkezik konfigurációs hiba lépett fel. Gondosan tekintse át a konfigurációt. 

Emellett nézze át a konfigurációs MonitoringAgent számára előállított [MaConfig.xml](#log-artifacts-path). Győződjön meg arról, hogy a megfelelő naplófájlok forrás ismertető szakasz. Ellenőrizze, hogy azt nem elvesznek a diagnosztikai konfigurációja és figyelési ügynök konfigurációjának közötti címfordítás.

#### <a name="is-data-getting-transferred"></a>Az első átvitt adatok?
Ha ellenőrizte, hogy az adatok helyileg rögzített első, de még nem látja a tárfiókban lévő, a következő lépéseket: 

- Győződjön meg arról, hogy megadta-e a megfelelő tárfiók, és, hogy még nem-e tanúsítványváltást a megadott tárfiók kulcsainak listázása. Azure-szolgáltatásokhoz, néha látható, hogy nincs-e frissítés személyek `useDevelopmentStorage=true`.

- Ellenőrizze, hogy helyes-e a megadott tárfiók. Győződjön meg arról, hogy nem rendelkezik, amelyek meggátolják az összetevők a nyilvános tárolási végpontok elérése korlátozásait a hálózati. Ennek egyik módja való távoli hozzáférés a számítógép be van, és próbálja Írjon valamit ugyanazt a tárfiókot.

- Végül milyen hibák vannak a figyelési ügynök által jelentett megtekintésével meggyőződhet. A figyelési ügynök írási naplók `maeventtable.tsf`, található [diagnosztikai adatok a helyi tárolójába](#log-artifacts-path). Kövesse az utasításokat a [helyi napló kinyerés](#local-log-extraction) szakasz a fájl megnyitását. Próbálja meg meghatározni, hogy van-e `errors` , amely jelzi, hogy tárolási írása helyi fájlok olvasása sikertelen.

### <a name="capturing-and-archiving-logs"></a>A rögzítés és a naplók archiválása
Gondolat lépjen kapcsolatba az ügyfélszolgálattal, elsőként azokat fel, akkor a gép gyűjteni. Időt takaríthat módon, hogy saját maga. Futtassa a `CollectGuestLogs.exe` segédprogram, [gyűjtemény segédprogram naplójának](#log-artifacts-path). Létrehozott egy .zip fájl az összes vonatkozó Azure jelentkezik be ugyanabban a mappában.

## <a name="diagnostics-data-tables-not-found"></a>Diagnosztikai adatok táblák nem található
A táblák ETW-események vonatkozik, amelyek az Azure storage megnevezett az alábbi kód használatával:

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
Ezt a kódot állít elő, négy táblák:

| Esemény | Tábla neve |
| --- | --- |
| szolgáltató = "prov1" &lt;eseményazonosító = "1" /&gt; |WADEvent + MD5("prov1") + "1" |
| szolgáltató = "prov1" &lt;eseményazonosító = "2" eventDestination = "dest1" /&gt; |WADdest1 |
| szolgáltató = "prov1" &lt;DefaultEvents /&gt; |WADDefault+MD5("prov1") |
| szolgáltató = "prov2" &lt;DefaultEvents eventDestination = "dest2" /&gt; |WADdest2 |

## <a name="references"></a>Referencia

### <a name="how-to-check-diagnostics-extension-configuration"></a>Diagnosztika bővítmény konfigurációjának ellenőrzése
Ellenőrizze a bővítmény konfigurációja legegyszerűbb módja a Ugrás [Azure erőforrás-kezelő](http://resources.azure.com), és lépjen a virtuális gép vagy a felhőalapú szolgáltatás helyét az Azure Diagnostics-bővítmény (IaaSDiagnostics / PaaDiagnostics) van.

Azt is megteheti távoli asztali kapcsolatot a gépre, és nézze meg az Azure Diagnostics konfigurációs fájl ismertetett a [összetevők elérésiút-szakaszával jelentkezzen](#log-artifacts-path).

Mindkét esetben a keresse meg a **Microsoft.Azure.Diagnostics**, és majd a **xmlCfg** vagy **WadCfg** mező. 

Ha egy virtuális gépen keres, és a **WadCfg** mező szerepel, az azt jelenti, hogy a konfigurációs JSON formátumban kell megadni. Ha a **xmlCfg** mező szerepel, ez azt jelenti, hogy a konfigurációs XML-kódban pedig base64-kódolású. Kell [dekódolni a](http://www.bing.com/search?q=base64+decoder) a diagnosztika által betöltött XML-kód megjelenítéséhez.

A felhőalapú szolgáltatás-szerepkör, válassza ki a konfiguráció a lemezről, az adatok is, ha base64-kódolású, ezért meg kell [dekódolni a](http://www.bing.com/search?q=base64+decoder) a diagnosztika által betöltött XML-kód megjelenítéséhez.

### <a name="azure-diagnostics-plugin-exit-codes"></a>Az Azure Diagnostics beépülő modul olyan kilépési kódot
A beépülő modul a következő kilépési kódot adja vissza:

| Kilépési kód | Leírás |
| --- | --- |
| 0 |Siker. |
| -1 |Általános hiba. |
| -2 |Nem sikerült betölteni a rcf fájlt.<p>Ez a belső hiba csak történjen, ha a Vendég ügynök beépülő modulja indítója manuálisan helytelenül hívják meg a virtuális Gépen. |
| -3 |A diagnosztika konfigurációs fájl nem tölthető be.<p><p>Megoldás: A konfigurációs fájl nem továbbítja a séma érvényesítését a okozza. A megoldás, hogy módszereket biztosítsanak a konfigurációs fájlt, amely megfelel a séma. |
| -4 |A figyelési, diagnosztikai ügynök egy másik példánya már használja a helyi erőforrás-könyvtár.<p><p>Megoldás: Adjon meg más értéket a **LocalResourceDirectory**. |
| -6 |A Vendég ügynök beépülő modulja indítója próbál megnyitni a diagnosztika a parancssor érvénytelen.<p><p>Ez a belső hiba csak történjen, ha a Vendég ügynök beépülő modulja indítója manuálisan helytelenül hívják meg a virtuális Gépen. |
| -10 |A diagnosztika beépülő modul kilépett a nem kezelt kivétel. |
| -11 |A vendégügynök nem tudta létrehozni a folyamatot elindításához, és a figyelési ügynök felügyeletéért felelős.<p><p>Megoldás: Ellenőrizze, hogy elérhetők-e elegendő rendszererőforrás új folyamatok elindításához.<p> |
| -101 |Érvénytelen argumentumok a diagnosztika beépülő modul hívásakor.<p><p>Ez a belső hiba csak történjen, ha a Vendég ügynök beépülő modulja indítója manuálisan helytelenül hívják meg a virtuális Gépen. |
| -102 |A beépülő modul folyamat inicializálása sikertelen.<p><p>Megoldás: Ellenőrizze, hogy elérhetők-e elegendő rendszererőforrás új folyamatok elindításához. |
| -103 |A beépülő modul folyamat inicializálása sikertelen. Ez többek között a tranzakciónaplókat tartalmazó objektum nem hozható létre.<p><p>Megoldás: Ellenőrizze, hogy elérhetők-e elegendő rendszererőforrás új folyamatok elindításához. |
| -104 |Nem sikerült betölteni a vendégügynök által biztosított rcf fájlt.<p><p>Ez a belső hiba csak történjen, ha a Vendég ügynök beépülő modulja indítója manuálisan helytelenül hívják meg a virtuális Gépen. |
| -105 |A diagnosztika beépülő modul a diagnosztika konfigurációs fájl nem nyitható meg.<p><p>Ez a belső hiba a kell csak fordulhat elő, ha a diagnosztika beépülő modul manuális helytelenül hívják meg a virtuális Gépen. |
| -106 |A diagnosztika konfigurációs fájl nem olvasható.<p><p>Egy konfigurációs fájl nem továbbítja a séma érvényesítését a okozza. <br><br>Megoldás: Adja meg a konfigurációs fájlt, amely megfelel a sémával. További információkért lásd: [diagnosztika bővítmény konfigurációjának ellenőrzése](#how-to-check-diagnostics-extension-configuration). |
| -107 |Érvénytelen az erőforrás directory fázis a figyelési ügynökhöz.<p><p>Ez a belső hiba a kell csak fordulhat elő, ha a figyelési ügynök manuálisan helytelenül hívják meg a virtuális Gépen.</p> |
| -108 |Nem lehet konvertálni a diagnosztika konfigurációs fájl figyelési ügynök konfigurációs fájlba.<p><p>Ez a belső hiba a kell csak fordulhat elő, ha a diagnosztika beépülő modul manuális hívása érvénytelen konfigurációs fájlt. |
| -110 |Általános diagnosztika konfigurációs hiba.<p><p>Ez a belső hiba a kell csak fordulhat elő, ha a diagnosztika beépülő modul manuális hívása érvénytelen konfigurációs fájlt. |
| -111 |Nem lehet elindítani a figyelőügynököt.<p><p>Megoldás: Ellenőrizze, hogy rendelkezésre áll-e elegendő rendszer erőforrás. |
| -112 |Általános hiba |

### <a name="local-log-extraction"></a>Helyi napló kinyerés
A figyelési ügynök gyűjti a naplókat, és mint `.tsf` fájlokat. A `.tsf` fájl nem olvasható, de a átalakíthatja egy `.csv` az alábbiak szerint: 

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
Új fájl neve `<relevantLogFile>.csv` jön létre, mint a megfelelő elérési útja `.tsf` fájlt.

>[!NOTE] 
> Csak a fő .tsf fájlt (például PerformanceCountersTable.tsf) szemben a segédprogram futtatásához szükséges. A hozzá tartozó fájlokat (például PerformanceCountersTables_\*\*001.tsf, PerformanceCountersTables_\*\*002.tsf, és így tovább) automatikusan dolgoznak fel.

### <a name="more-about-missing-trace-logs"></a>További információ a nyomkövetési naplók hiányzik 

>[!NOTE]
> A következő információkat legtöbbször az Azure Cloud Services vonatkozik, kivéve, ha a kérelmet, hogy az infrastruktúra-szolgáltatási virtuális gép fut a DiagnosticsMonitorTraceListener van konfigurálva. 

- Győződjön meg arról, hogy a **DiagnosticMonitorTraceListener** konfigurálva van a web.config vagy az App.config fájlt.  Ez a cloud service projektek alapértelmezés szerint van konfigurálva. Azonban bizonyos felhasználók hozzászólási azt ki, amely azt eredményezi, a nyomkövetési utasítást, hogy nem lehet összegyűjteni diagnosztika. 

- Ha az nem első írja a naplókat a **OnStart** vagy **futtatása** módszert, győződjön meg arról, hogy a **DiagnosticMonitorTraceListener** az az App.config fájlban van.  Alapértelmezés szerint a Web.config fájlban, de, amely csak w3wp.exe belül futó vonatkozik. Így kell azt az App.config fájlban WaIISHost.exe futó nyomok rögzítését.

- Győződjön meg arról, hogy azok be **Diagnostics.Trace.TraceXXX** helyett **Diagnostics.Debug.WriteXXX.** A hibakeresési utasításokat kiadott buildjét el lesznek távolítva.

- Győződjön meg arról, hogy a lefordított kódot ténylegesen a **Diagnostics.Trace sorok** (használja a megfelelési, ildasm vagy ILSpy ellenőrzése). **Diagnostics.Trace** parancsok el lesznek távolítva a lefordított bináris a NYOMKÖVETÉSI feltételes fordítási szimbólum használata. Ez a gyakori probléma akkor fordul elő, amikor msbuild segítségével a projekt felépítéséhez.   

## <a name="known-issues-and-mitigations"></a>Ismert problémák és azok mérséklési
Ismert azok mérséklési szolgáltatással kapcsolatos ismert problémák listája itt található:

**1. a .NET 4.5 függőség**

Windows Azure diagnosztikai bővítmény rendelkezik egy runtime függőségdefiniáló keretrendszer .NET 4.5-ös vagy újabb. Az írás engedélyezéséhez az Azure Felhőszolgáltatások kiépített összes gép, valamint minden olyan hivatalos képek Azure virtuális gépeken alapuló időpontjában .NET 4.5-ös vagy újabb verziója szükséges. 

Továbbra is lehetséges esetlegesen fellépő olyan helyzet, ahol próbálja meg futtatni a Windows Azure diagnosztikai bővítmény olyan gépen, amely nem rendelkezik a .NET 4.5-ös vagy újabb verzió. Ez akkor fordul elő, a számítógép egy régi lemezképről, vagy a pillanatkép létrehozásakor, vagy ha a saját egyéni lemez állapotba.

Ez általában akkor jelentkezik, mint a kilépési kódot **255** futtatásakor **DiagnosticsPluginLauncher.exe.** Hiba történik, a következő nem kezelt kivétel miatt: 
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**Megoldás:** telepítse a .NET 4.5-ös vagy újabb verzióját a számítógépen.

**2. Teljesítményadatok számlálók érhető el a tárolási, de nem jelennek meg a portálon**

A portál élményt a virtuális gépek egyes teljesítményszámlálók alapértelmezés szerint jeleníti meg. Ha a teljesítményszámlálók nem jelenik meg, és tudja, hogy az adatok kezdeti keletkezik, mert tárolási érhető el, ellenőrizze a következőket:

- Van-e az adatok a számláló neve angol. Ha a számláló nevek nem angol nyelvű, a portál metrika diagram nem képes felismerni.

- Ha helyettesítő karaktereket használ (\*) a teljesítményszámláló-neveket, a portál nem képes a konfigurált és gyűjtött számláló összefüggéseket.

**Megoldás**: módosítsa a gép nyelvi angol rendszer fiókok. Ehhez az szükséges, válassza ki a **Vezérlőpult** > **régió** > **felügyeleti** > **beállításainak**. Ezután törölje **és rendszerfiókok üdvözli** , hogy az egyéni nyelv nem alkalmazható a rendszerfiók. Győződjön meg arról is, ha azt szeretné, hogy a portálra, ahol a elsődleges felhasználási felhasználói élményt kell, ne használjon helyettesítő karaktereket.
