---
title: Az Azure Diagnostics bővítmény hibaelhárítása
description: Az Azure-diagnosztika Azure virtuális gépeken, service fabricben vagy felhőszolgáltatásokban való használata kori problémák elhárítása.
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/08/2019
ms.openlocfilehash: 043369bd6112c4cac36539bbd764393d889439c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274579"
---
# <a name="azure-diagnostics-troubleshooting"></a>Az Azure Diagnostics hibaelhárítása
Ez a cikk ismerteti az Azure Diagnostics használatával kapcsolatos hibaelhárítási információkat. Az Azure-diagnosztikáról az [Azure Diagnosztika áttekintése című témakörben olvashat bővebben.](diagnostics-extension-overview.md)

## <a name="logical-components"></a>Logikai összetevők
**Diagnosztika plugin launcher (DiagnosticsPluginLauncher.exe)**: Elindítja az Azure Diagnosztikai bővítményt. Belépési pontként szolgál.

**Diagnosztikai bővítmény (DiagnosticsPlugin.exe)**: Konfigurálja, elindítja és kezeli a figyelőügynök élettartamát. Ez a fő folyamat, amely elindította a hordozórakéta.

**Figyelési ügynök\*(MonAgent .exe folyamatok)**: Figyeli, összegyűjti és továbbítja a diagnosztikai adatokat.  

## <a name="logartifact-paths"></a>Napló/műtermék elérési útjai
A következőkben néhány fontos napló és műtermék elérési útjai találhatók. Erre az információra a dokumentum többi részében hivatkozunk.

### <a name="azure-cloud-services"></a>Azure Cloud Services
| Összetevő | Útvonal |
| --- | --- |
| **Az Azure Diagnostics konfigurációs fájlja** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<>\Config.txt verzió |
| **Naplófájlok** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<>\ |
| **Helyi adattároló diagnosztikai adatokhoz** | C:\Resources\Directory\<CloudServiceDeploymentID>. \<Szerepkörnév>. DiagnosticStore\WAD0107\Táblák |
| **Figyelési ügynök konfigurációs fájlja** | C:\Resources\Directory\<CloudServiceDeploymentID>. \<Szerepkörnév>. DiagnosticStore\WAD0107\Configuration\MaConfig.xml |
| **Azure Diagnostics bővítménycsomag** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<> |
| **Naplógyűjtési segédprogram elérési útja** | %SystemDrive%\Packages\GuestAgent\ |
| **MonAgentHost naplófájl** | C:\Resources\Directory\<CloudServiceDeploymentID>. \<Szerepkörnév>. DiagnosticStore\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

### <a name="virtual-machines"></a>Virtuális gépek
| Összetevő | Útvonal |
| --- | --- |
| **Az Azure Diagnostics konfigurációs fájlja** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<verzió>\RuntimeSettings |
| **Naplófájlok** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\ |
| **Helyi adattároló diagnosztikai adatokhoz** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Tables |
| **Figyelési ügynök konfigurációs fájlja** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MaConfig.xml |
| **Állapotfájl** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<verzió>\Állapot |
| **Azure Diagnostics bővítménycsomag** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsDiagnosticsVersion>|
| **Naplógyűjtési segédprogram elérési útja** | C:\WindowsAzure\Logs\WaAppAgent.log |
| **MonAgentHost naplófájl** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<Diagnostics DiagnosticsVersion>\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

## <a name="metric-data-doesnt-appear-in-the-azure-portal"></a>A metrikaadatok nem jelennek meg az Azure Portalon
Az Azure Diagnostics metrikaadatokat biztosít, amelyek megjelenhetnek az Azure Portalon. Ha problémái vannak az adatok megtekintésével a\* portálon, ellenőrizze a WADMetrics tábla az Azure Diagnostics tárfiókban, hogy ha a megfelelő metrika rekordok vannak, és győződjön meg arról, hogy az [erőforrás-szolgáltató](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services) Microsoft.Insights regisztrálva van.

Itt a **tábla PartitionKey-ja** az erőforrás-azonosító, a virtuális gép vagy a virtuálisgép-méretezési készlet. **RowKey** a metrika neve (más néven a teljesítményszámláló neve).

Ha az erőforrás-azonosító helytelen, ellenőrizze **a Diagnosztikai konfigurációs** **Configuration** > **metrikák** > **erőforrás-azonosítóját,** hogy az erőforrás-azonosító megfelelően van-e beállítva.

Ha nincs adat az adott metrikához, ellenőrizze **a Diagnosztikai konfigurációs** > **teljesítményszámláló,** hogy ha a metrika (teljesítményszámláló) szerepel. Alapértelmezés szerint a következő számlálókat engedélyezzük:
- \Processor(_Total)\% Processor Time
- \Memory\Available Bytes
- \ASP.NET alkalmazások(__összesen__)\Kérések/Mp
- \ASP.NET alkalmazások(__összesen__)\Összes hiba/mp
- \ASP.NET\Várólistára helyezett kérelmek
- \ASP.NET\Elutasított kérelmek
- \Processzor(w3wp)\% processzoridő
- \Process(w3wp)\Privát bájtok
- \Process(WaIISHost)\% processzoridő
- \Process(WaIISHost)\Privát bájtok
- \Process(WaWorkerHost)\% processzoridő
- \Process(WaWorkerHost)\Privát bájtok
- \Memória\Laphibák/mp
- \.NET CLR_Global_memória(\% globális ) idő gc-ben
- \LogicalDisk(C:)\Lemezírási bájtok/mp
- \LogicalDisk(C:)\Lemezolvasási bájt/mp
- \LogicalDisk(D:)\Lemezírási bájtok/mp
- \LogicalDisk(D:)\Lemezolvasási bájt/mp

Ha a konfiguráció megfelelően van beállítva, de továbbra sem látja a metrikaadatokat, kövesse az alábbi irányelveket a hibaelhárításhoz.


## <a name="azure-diagnostics-is-not-starting"></a>Az Azure Diagnosztika nem indul el
Arról, hogy az Azure Diagnosztika miért nem sikerült elindítani, tekintse meg a **DiagnosticsPluginLauncher.log** és **DiagnosticsPlugin.log** fájlokat a naplófájlok korábban megadott helyen.

Ha ezek a `Monitoring Agent not reporting success after launch`naplók jelzik , az azt jelenti, hogy hiba történt a MonAgentHost.exe indításakor. Tekintse meg a naplókat az előző `MonAgentHost log file` szakaszban jelzett helyen.

A naplófájlok utolsó sora tartalmazza a kilépési kódot.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
Ha **negatív** kilépési kódot talál, olvassa el a [Kilépési kód táblázatot](#azure-diagnostics-plugin-exit-codes) a [Hivatkozások szakaszban.](#references)

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>A diagnosztikai adatok nem lesznek naplózva az Azure Storage-ba
Határozza meg, hogy az adatok egyike sem jelenik-e meg, vagy az adatok egy része.

### <a name="diagnostics-infrastructure-logs"></a>Diagnosztikai infrastruktúra-naplók
A diagnosztika naplózza a diagnosztikai infrastruktúra naplóiban lévő összes hibát. Győződjön meg arról, hogy engedélyezte a [diagnosztikai infrastruktúra naplóinak rögzítését a konfigurációban.](#how-to-check-diagnostics-extension-configuration) Ezután gyorsan megkeresheti a megfelelő `DiagnosticInfrastructureLogsTable` hibákat, amelyek a beállított tárfiók táblájában jelennek meg.

### <a name="no-data-is-appearing"></a>Nem jelennek meg adatok
A leggyakoribb oka annak, hogy az eseményadatok egyáltalán nem jelennek meg, az, hogy a tárfiók adatai helytelenül vannak definiálva.

Megoldás: Javítsa ki a diagnosztika konfigurációját, és telepítse újra a Diagnosztika programot.

Ha a tárfiók megfelelően van konfigurálva, a számítógéphez való távelérés, és ellenőrizze, hogy a *DiagnosticsPlugin.exe* és a *MonAgentCore.exe* fut-e. Ha nem futnak, kövesse az [Azure Diagnosztika nem induló lépéseit.](#azure-diagnostics-is-not-starting)

Ha a folyamatok futnak, nyissa meg [az Adatok helyileg rögzítésre kerülése?](#is-data-getting-captured-locally)

Ha ez nem oldja meg a problémát, próbálja meg:

1. Ügynök eltávolítása
2. Directory C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics könyvtár eltávolítása
3. Ügynök ismételt telepítése


### <a name="part-of-the-data-is-missing"></a>Az adatok egy része hiányzik
Ha bizonyos adatokat kap, de nem az összeset, az azt jelenti, hogy az adatgyűjtési/-átviteli folyamat megfelelően van beállítva. Kövesse az alszakaszokat itt a probléma leszűkítéséhez.

#### <a name="is-the-collection-configured"></a>Be van állítva a gyűjtemény?
A Diagnosztika konfiguráció utasításokat tartalmaz egy adott típusú adatok gyűjtésére. [Tekintse át a konfigurációt,](#how-to-check-diagnostics-extension-configuration) és ellenőrizze, hogy csak a gyűjteményhez konfigurált adatokat keresi-e.

#### <a name="is-the-host-generating-data"></a>Az állomás adatokat hoz létre?
- **Teljesítményszámlálók**: Nyissa ki a perfmont, és ellenőrizze a számlálót.

- **Nyomkövetési naplók:** Távoli hozzáférés a virtuális gépbe, és adjon hozzá egy TextWriterTraceListener az alkalmazás konfigurációs fájl.  A https://msdn.microsoft.com/library/sk36c28t.aspx szövegfigyelő beállításának megtekintése.  Ellenőrizze, `<trace>` hogy `<trace autoflush="true">`az elem rendelkezik-e.<br />
Ha nem látja a nyomkövetési naplók létrejöttét, olvassa el a hiányzó nyomkövetési naplókról szóló további témakört.

- **ETW-nyomok**: Távoli hozzáférés a virtuális géphez, és telepítse a PerfView-t.  A PerfView, fuss **File** > **User Command** > **Listen etwprovder1** > **etwprovider2**, és így tovább. A **Figyelés** parancs megkülönbözteti a kis- és nagybetűket, és nem lehet szóköz az ETW-szolgáltatók vesszővel tagolt listája között. Ha a parancs nem fut, a Perfview eszköz jobb alsó sarkában található **Napló** gombra kattintva megtekintheti, hogy mi történt a futtatással, és mi volt az eredmény.  Feltéve, hogy a bemenet helyes, egy új ablak jelenik meg. Néhány másodperc múlva elkezded látni az ETW nyomait.

- **Eseménynaplók:** Távoli hozzáférés a virtuális géphez. Nyissa `Event Viewer`meg a , majd győződjön meg arról, hogy az események léteznek.

#### <a name="is-data-getting-captured-locally"></a>Az adatok helyi legrögzítettek?
Ezután győződjön meg arról, hogy az adatok helyileg rögzítésre kerül.
Az adatok helyileg tárolódnak a helyi tárolóban `*.tsf` tárolt fájlokban diagnosztikai adatok. Különböző típusú naplók kap gyűjtött `.tsf` különböző fájlokat. A nevek hasonlóak az Azure Storage táblaneveihez.

Például `Performance Counters` a begyűjtését a alkalmazásban kaphatja `PerformanceCountersTable.tsf`meg. Az eseménynaplók at `WindowsEventLogsTable.tsf`gyűjtik össze. A [Helyi napló kibontása](#local-log-extraction) szakasz utasításaival nyissa meg a helyi gyűjteményfájlokat, és ellenőrizze, hogy a lemezen gyűjtik-e őket.

Ha nem látja a naplók helyi gyűjtése, és már ellenőrizte, hogy az állomás adatokat generál, akkor valószínűleg egy konfigurációs probléma. Figyelmesen tekintse át a konfigurációt.

Tekintse át a MonitoringAgent MaConfig.xml fájlhoz létrehozott konfigurációt is. Ellenőrizze, hogy van-e olyan szakasz, amely leírja a megfelelő naplóforrást. Ezután ellenőrizze, hogy nem vész el a fordítás között a diagnosztika konfiguráció és a figyelési ügynök konfigurációja.

#### <a name="is-data-getting-transferred"></a>Az adatok átvitele történik?
Ha ellenőrizte, hogy az adatok helyileg rögzítésre kerülnek, de továbbra sem látja őket a tárfiókban, kövesse az alábbi lépéseket:

- Ellenőrizze, hogy megfelelő tárfiókot adott-e meg, és hogy nem görgette-e át az adott tárfiók kulcsait. Az Azure Cloud Services esetében néha azt `useDevelopmentStorage=true`látjuk, hogy az emberek nem frissülnek.

- Ellenőrizze, hogy a megadott tárfiók helyes-e. Győződjön meg arról, hogy nincsenek olyan hálózati korlátozások, amelyek megakadályozzák, hogy az összetevők elérjék a nyilvános tárolási végpontokat. Ennek egyik módja a távoli hozzáférés a géphez, majd megpróbál írni valamit ugyanarra a tárfiókba.

- Végül megnézheti, hogy a figyelőügynök milyen hibákat jelent. A figyelőügynök a naplókat `maeventtable.tsf`írja be, amely a helyi tárolóban található diagnosztikai adatokhoz. A fájl megnyitásához kövesse a [Helyi napló kibontása](#local-log-extraction) című szakasz utasításait. Ezután próbálja meg `errors` meghatározni, hogy vannak-e olyan hibák, amelyek a helyi fájlok nak a tárolóba írásban történő olvasását jelzik.

### <a name="capturing-and-archiving-logs"></a>Naplók rögzítése és archiválása
Ha azon gondolkodik, hogy kapcsolatba lép az ügyfélszolgálattal, az első dolog, amit kérhetnek, hogy gyűjtse össze a naplókat a gépéről. Időt takaríthat meg ezzel, hogy magad. Futtassa a segédprogramot a `CollectGuestLogs.exe` Naplógyűjtemény segédprogram elérési útján. Létrehoz egy .zip fájlt az összes releváns Azure-naplóval ugyanabban a mappában.

## <a name="diagnostics-data-tables-not-found"></a>A diagnosztikai adattáblák nem találhatók
Az EtW-eseményeket tároló Azure Storage-beli táblák at a következő kód dal nevezik el:

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
Ez a kód négy táblát hoz létre:

| Esemény | Tábla neve |
| --- | --- |
| provider="prov1" &lt;Esemény azonosító="1" /&gt; |WADEvent+MD5("prov1")+"1" |
| provider="prov1" &lt;Event id="2" eventDestination="dest1" /&gt; |WADdest1 |
| provider="prov1" &lt;DefaultEvents /&gt; |WADDefault+MD5("prov1") |
| provider="prov2" &lt;DefaultEvents eventDestination="dest2" /&gt; |WADdest2 |

## <a name="references"></a>Referencia

### <a name="how-to-check-diagnostics-extension-configuration"></a>A Diagnosztikai bővítmény konfigurációjának ellenőrzése
A bővítmény konfigurációjának ellenőrzéséhez a legegyszerűbb en, hogy az [Azure Resource Explorer,](https://resources.azure.com)majd nyissa meg a virtuális gép vagy felhőszolgáltatás, ahol az Azure Diagnostics bővítmény (IaaSDiagnostics / PaaDiagnostics) van.

Másik lehetőségként a távoli asztalon a gépbe, és tekintse meg az Azure Diagnosztikai konfigurációs fájl, amely a Napló összetevők elérési út szakaszban ismertetjük.

Mindkét esetben keresse meg a **Microsoft.Azure.Diagnostics**, majd az **xmlCfg** vagy **wadCfg** mezőt.

Ha virtuális gépen keres, és a **WadCfg** mező jelen van, az azt jelenti, hogy a konfiguráció JSON formátumban van. Ha az **xmlCfg** mező jelen van, az azt jelenti, hogy a konfiguráció XML-ben van, és base64-kódolású. Meg kell [dekódolni,](https://www.bing.com/search?q=base64+decoder) hogy az XML, amely betöltötte a diagnosztika.

A felhőalapú szolgáltatás szerepkör, ha kiválasztja a konfigurációt a lemezről, az adatok base64-kódolt, így meg kell [dekódolni,](https://www.bing.com/search?q=base64+decoder) hogy az XML, amely betöltötte a diagnosztika.

### <a name="azure-diagnostics-plugin-exit-codes"></a>Az Azure Diagnostics beépülő modul kilépési kódjai
A plugin a következő kilépési kódokat adja vissza:

| Kilépési kód | Leírás |
| --- | --- |
| 0 |Siker. |
| -1 |Általános hiba. |
| -2 |Nem lehet betölteni az rcf fájlt.<p>Ez a belső hiba csak akkor fordulhat elő, ha a vendégügynök bővítményindítója manuálisan helytelenül van meghívva a virtuális gépen. |
| -3 |A Diagnosztika konfigurációs fájl nem tölthető be.<p><p>Megoldás: Egy konfigurációs fájl nem adja át a sémaérvényesítést. A megoldás az, hogy egy konfigurációs fájlt, amely megfelel a séma. |
| -4 |A figyelési ügynök diagnosztika egy másik példánya már használja a helyi erőforráskönyvtárat.<p><p>Megoldás: Adjon meg egy másik értéket a **LocalResourceDirectory**könyvtárhoz. |
| -6 |A vendégügynök bővítményindítója érvénytelen parancssorgal próbálta elindítani a Diagnosztika parancsot.<p><p>Ez a belső hiba csak akkor fordulhat elő, ha a vendégügynök bővítményindítója manuálisan helytelenül van meghívva a virtuális gépen. |
| -10 |A Diagnosztika bővítmény kezeletlen kivétellel távozott. |
| -11 |A vendégügynök nem tudta létrehozni a felügyeleti ügynök elindításáért és figyelésével felelős folyamatot.<p><p>Megoldás: Ellenőrizze, hogy elegendő rendszererőforrás áll-e rendelkezésre az új folyamatok elindításához.<p> |
| -101 |Érvénytelen argumentumok a Diagnosztika bővítmény hívásakor.<p><p>Ez a belső hiba csak akkor fordulhat elő, ha a vendégügynök bővítményindítója manuálisan helytelenül van meghívva a virtuális gépen. |
| -102 |A beépülő modul folyamata nem képes inicializálni magát.<p><p>Megoldás: Ellenőrizze, hogy elegendő rendszererőforrás áll-e rendelkezésre az új folyamatok elindításához. |
| -103 |A beépülő modul folyamata nem képes inicializálni magát. Pontosabban nem tudja létrehozni a naplózó objektumot.<p><p>Megoldás: Ellenőrizze, hogy elegendő rendszererőforrás áll-e rendelkezésre az új folyamatok elindításához. |
| -104 |Nem lehet betölteni a vendégügynök által biztosított rcf fájlt.<p><p>Ez a belső hiba csak akkor fordulhat elő, ha a vendégügynök bővítményindítója manuálisan helytelenül van meghívva a virtuális gépen. |
| -105 |A Diagnosztika bővítmény nem tudja megnyitni a Diagnosztika konfigurációs fájlt.<p><p>Ez a belső hiba csak akkor fordulhat elő, ha a diagnosztika beépülő modul manuálisan helytelenül van meghívva a virtuális gépen. |
| -106 |A Diagnosztika konfigurációs fájl nem olvasható.<p><p>Egy konfigurációs fájl nem adja át a séma-érvényesítést. <br><br>Megoldás: Adjon meg egy konfigurációs fájlt, amely megfelel a sémának. További információt a [Diagnosztikai bővítmény konfigurációjának ellenőrzése című témakörben talál.](#how-to-check-diagnostics-extension-configuration) |
| -107 |Az erőforrás-címtár-átlépés a figyelési ügynök érvénytelen.<p><p>Ez a belső hiba csak akkor fordulhat elő, ha a figyelési ügynök manuálisan helytelenül hívható meg a virtuális gépen.</p> |
| -108 |A Diagnosztikai konfigurációs fájl nem konvertálható a figyelési ügynök konfigurációs fájljává.<p><p>Ez a belső hiba csak akkor fordulhat elő, ha a Diagnosztika bővítmény manuálisan érvénytelen konfigurációs fájllal van meghívva. |
| -110 |Általános diagnosztika konfigurációs hiba.<p><p>Ez a belső hiba csak akkor fordulhat elő, ha a Diagnosztika bővítmény manuálisan érvénytelen konfigurációs fájllal van meghívva. |
| -111 |Nem lehet elindítani a figyelési ügynököt.<p><p>Megoldás: Ellenőrizze, hogy elegendő rendszererőforrás áll-e rendelkezésre. |
| -112 |Általános hiba |

### <a name="local-log-extraction"></a>Helyi napló kibontása
A figyelőügynök naplói és összetevők `.tsf` fájlként gyűjti a naplókat. A `.tsf` fájl nem olvasható, de a `.csv` következőképpen alakítható át:

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
A hívott `<relevantLogFile>.csv` új fájl a megfelelő `.tsf` fájlelérési úton jön létre.

>[!NOTE]
> Ezt a segédprogramot csak a fő .tsf fájlon (például PerformanceCountersTable.tsf) kell futtatnia. A kísérő fájlokat (például PerformanceCountersTables_\*\*001.tsf, PerformanceCountersTables_\*\*002.tsf stb.) a program automatikusan feldolgozza.

### <a name="more-about-missing-trace-logs"></a>További információk a hiányzó nyomkövetési naplókról

>[!NOTE]
> Az alábbi információk főként az Azure Cloud Services, kivéve, ha konfigurálta a DiagnosticsMonitorTraceListener egy alkalmazás, amely fut az IaaS virtuális gép.

- Győződjön meg arról, hogy a **DiagnosticMonitorTraceListener** konfigurálva van a web.config vagy app.config.  Ez alapértelmezés szerint a felhőszolgáltatási projektekben van konfigurálva. Egyes ügyfelek azonban megjegyzésekkel, ami miatt a nyomkövetési utasítások nem gyűjti a diagnosztika.

- Ha a naplók nem az **OnStart** vagy a **Run** metódusból írnak, győződjön meg arról, hogy a **DiagnosticMonitorTraceListener** az app.config fájlban található.  Alapértelmezés szerint a web.config fájlban található, de ez csak a w3wp.exe fájlon belül futó kódra vonatkozik. Tehát szükség van rá az app.config-ban a WaIISHost.exe fájlban futó nyomkövetések rögzítéséhez.

- Győződjön meg arról, hogy **a Diagnostics.Trace.TraceXXX** programot használja a **Diagnostics.Debug.WriteXXX helyett.** A hibakeresési utasítások törlődnek a kiadási buildből.

- Győződjön meg róla, hogy a lefordított kód valóban rendelkezik a **Diagnostics.Trace vonalakkal** (használja a Reflektort, az ildasmot vagy az ILSpy-t az ellenőrzéshez). **Diagnostics.Trace** parancsok törlődnek a lefordított bináris, kivéve, ha a TRACE feltételes fordítás szimbólum. Ez egy gyakori probléma, amely akkor fordul elő, amikor msbuild használatával készít egy projektet.   

## <a name="known-issues-and-mitigations"></a>Ismert problémák és megoldások
Az alábbiakban felsorolunk az ismert kockázatcsökkentésekkel kapcsolatos ismert problémákat:

**1. .NET 4.5 függőség**

A Windows Azure Diagnosztikai bővítmény futásidejű függéssel rendelkezik a .NET 4.5-ös keretrendszertől vagy újabb verziótól. Az írás időpontjában az Azure Cloud Services hez kiépített összes gép, valamint az Azure virtuális gépeken alapuló összes hivatalos rendszerkép .NET 4.5-ös vagy újabb verzióval rendelkezik.

Továbbra is előfordulhat olyan helyzet, amikor a Windows Azure diagnosztikai bővítményt olyan számítógépen próbálja futtatni, amely nem rendelkezik .NET 4.5-ös vagy újabb verzióval. Ez akkor történik, ha a gépet egy régi lemezképből vagy pillanatképből hozza létre, vagy ha saját egyéni lemezt hoz.

Ez általában a **DiagnosticsPluginLauncher.exe futtatásakor** **255-ös** kilépési kódként jelenik meg. A hiba a következő nem kezelt kivétel miatt következik be:
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**Kockázatcsökkentés:** Telepítse a .NET 4.5-ös vagy újabb verziót a készülékre.

**2. A teljesítményszámlálók adatai elérhetők a tárolóban, de nem jelennek meg a portálon**

A portál élménye a virtuális gépeken alapértelmezés szerint bizonyos teljesítményszámlálókat jelenít meg. Ha nem látja a teljesítményszámlálókat, és tudja, hogy az adatok azért jönnek létre, mert a tárolóban elérhetők, ellenőrizze az alábbiakat:

- Azt jelzi, hogy a tárolóban lévő adatoknak van-e számlálóneve angol nyelven. Ha a számlálónevek nem angol nyelvűek, a portálmetrikus diagram nem fogja felismerni. **Kockázatcsökkentés**: A rendszerfiókok esetében módosítsa a gép nyelvét angolra. Ehhez válassza a **Vezérlőpult** > **felügyeleti** > **Administrative** > **másolásának beállításai lehetőséget.** Ezután törölje a jelet **az Üdvözlőképernyő és a rendszerfiókok** jelölőnégyzetből, hogy az egyéni nyelv ne kerüljön alkalmazásra a rendszerfiókra.

- Ha helyettesítő karaktereket\*( ) használ a teljesítményszámláló neveiben, a portál nem tudja korrelálni a konfigurált és összegyűjtött számlálót, amikor a teljesítményszámlálókat elküldi az Azure Storage-fogadóba. **Kockázatcsökkentés: Győződjön**meg arról, hogy használhatja\*a helyettesítő karaktereket, és a portál kibontja a ( ), a teljesítményszámlálók az Azure Monitor fogadó.

