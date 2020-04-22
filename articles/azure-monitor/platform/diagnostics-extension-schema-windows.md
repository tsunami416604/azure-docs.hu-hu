---
title: Windows diagnosztikai bővítménysémája
description: Konfigurációs séma hivatkozás a Windows diagnosztikai bővítmény (WAD) az Azure Monitorban.
ms.subservice: diagnostic-extension
ms.topic: reference
author: bwren
ms.author: bwren
ms.date: 01/20/2020
ms.openlocfilehash: c04fc82b8b04e474a656a0849177f7aa5d27b427
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676434"
---
# <a name="windows-diagnostics-extension-schema"></a>Windows diagnosztikai bővítménysémája
Az Azure Diagnostics bővítmény az Azure Monitor egy ügynöke, amely a vendég operációs rendszerből és az Azure számítási erőforrások munkaterheléseiből gyűjtfigyelési adatokat. Ez a cikk a Windows virtuális gépeken és más számítási erőforrásokon a diagnosztikai bővítmény konfigurációjához használt sémát részletezi.

> [!NOTE]
> A jelen cikkben szereplő séma az 1.3-as és újabb verziókra érvényes (Azure SDK 2.4 és újabb). Az újabb konfigurációs szakaszok megjegyzésekkel jelennek meg, hogy milyen verzióban adták hozzá őket. A séma 1.0-s és 1.2-es verziója archiválva lett, és már nem érhető el. 

## <a name="public-configuration-file-schema"></a>Nyilvános konfigurációs fájl sémája

Töltse le a nyilvános konfigurációs fájl sémadefinícióját a következő PowerShell-parancs végrehajtásával:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  


## <a name="common-attribute-types"></a>Közös attribútumtípusok  
 **az scheduledTransferPeriod** attribútum több elemben jelenik meg. Ez a tárolóba történő ütemezett átvitelek közötti időköz, amelyet a legközelebbi percre kell felkerekíteni. Az érték egy ["Időtartam adattípus" XML.](https://www.w3schools.com/xml/schema_dtypes_date.asp)


## <a name="diagnosticsconfiguration-element"></a>Diagnosztikakonfigurációs elem  
 *Fa: Gyökér - Diagnosztikaikonfiguráció*

Hozzáadva az 1.3-as verzióban.  

A diagnosztikai konfigurációs fájl legfelső szintű eleme.  

**Attribute** Attribútum-xmlns – A diagnosztikai konfigurációs fájl XML-névtere:  
`http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration`


|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**Nyilvános config**|Kötelező. Lásd a leírás máshol ezen az oldalon.|  
|**Privát config**|Választható. Lásd a leírás máshol ezen az oldalon.|  
|**IsEnabled**|Logikai. Lásd a leírás máshol ezen az oldalon.|  

## <a name="publicconfig-element"></a>PublicConfig elem  
 *Fa: Gyökér - DiagnosticsConfiguration - PublicConfig*

 A nyilvános diagnosztika konfigurációjának ismertetése.  

|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**WadCfg között**|Kötelező. Lásd a leírás máshol ezen az oldalon.|  
|**StorageAccount**|Az adatok tárolására az Azure Storage-fiók neve. A Set-AzureServiceDiagnosticsExtension parancsmag végrehajtásakor paraméterként is megadható.|  
|**StorageType típusú**|Lehet *Table,* *Blob*vagy *TableAndBlob.* A tábla az alapértelmezett. A TableAndBlob kiválasztásakor a diagnosztikai adatok kétszer íródnak meg – minden típushoz egyszer.|  
|**LocalResourceDirectory**|A virtuális gép azon könyvtára, ahol a figyelőügynök eseményadatokat tárol. Ha nem, akkor állítsa be, az alapértelmezett könyvtárat használja a rendszer:<br /><br /> Feldolgozó/webes szerepkör esetén:`C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Virtuális gép esetén:`C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> A szükséges attribútumok a következők:<br /><br /> - **elérési út** – Az Azure Diagnostics által használandó rendszer könyvtára.<br /><br /> - **expandEnvironment** - Azt szabályozza, hogy a környezeti változók ki legyenek-e bontva az elérési út nevében.|  

## <a name="wadcfg-element"></a>WadCFG elem  
 *Fa: Root - DiagnosticsConfiguration - PublicConfig - WadCFG*

 Azonosítja és konfigurálja az összegyűjtendő telemetriai adatokat.  


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration elem
 *Fa: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration*

 Kötelező

|Attribútumok|Leírás|  
|----------------|-----------------|  
| **overallQuotaInMB** | Az Azure Diagnostics által gyűjtött különböző típusú diagnosztikai adatok által felemésztett helyi lemezterület maximális mennyisége. Az alapértelmezett beállítás 4096 MB.<br />
|**useProxyServer** | Konfigurálja az Azure Diagnostics-t a proxykiszolgáló-beállítások használatára az IE-beállításokban megadott módon.|
|**Mosogató** | Hozzáadva az 1.5-ben. Választható. A fogadó helyére mutat, és diagnosztikai adatokat is küld az összes gyermekelemhez, amely támogatja a fogadókat. A fogadó példa az Application Insights vagy az Event Hubs.|  


<br /> <br />

|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**CrashDumps**|Lásd a leírás máshol ezen az oldalon.|  
|**DiagnosticInfrastructureLogs (Diagnosztikaiinfrastruktúranaplók)**|Engedélyezze az Azure Diagnostics által létrehozott naplók gyűjteményét. A diagnosztikai infrastruktúra-naplók hasznosak a diagnosztikai rendszer hibáinak elhárításához. A választható attribútumok a következők:<br /><br /> - **scheduledTransferLogLevelFilter** - A gyűjtött naplók minimális súlyossági szintjét állítja be.<br /><br /> - **scheduledTransferPeriod** - A tárolóba történő ütemezett átvitelek közötti időköz a legközelebbi percre kerekítve. Az érték egy ["Időtartam adattípus" XML.](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**Könyvtárak**|Lásd a leírás máshol ezen az oldalon.|  
|**EtwProviders (EtwProviders)**|Lásd a leírás máshol ezen az oldalon.|  
|**Mérőszámok**|Lásd a leírás máshol ezen az oldalon.|  
|**Teljesítményszámlálók**|Lásd a leírás máshol ezen az oldalon.|  
|**WindowsEseménynapló**|Lásd a leírás máshol ezen az oldalon.|
|**DockerSources**|Lásd a leírás máshol ezen az oldalon. |



## <a name="crashdumps-element"></a>CrashDumps elem  
 *Fa: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - CrashDumps*

 Engedélyezze az összeomlási memóriaképek gyűjteményét.  

|Attribútumok|Leírás|  
|----------------|-----------------|  
|**containerName (tárolóneve)**|Választható. A blob tároló neve az Azure Storage-fiókban az összeomlási memóriaképek tárolására.|  
|**crashDumpType típusú**|Választható.  Az Azure Diagnostics konfigurálása mini vagy teljes összeomlási memóriaképek gyűjtésére.|  
|**directoryQuotaPercentage**|Választható.  A **teljesquotaInMB** százalékos arányának beállítása a virtuális gép összeomlási memóriaképek számára fenntartva.|  

|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**CrashDumpConfiguration (CrashDumpConfiguration)**|Kötelező. Az egyes folyamatok konfigurációs értékeit határozza meg.<br /><br /> A következő attribútum is szükséges:<br /><br /> **processName** - Annak a folyamatnak a neve, amelyhez az Azure Diagnostics összeomlási memóriaképet szeretne gyűjteni.|  

## <a name="directories-element"></a>Könyvtárak elem
 *Fa: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Könyvtárak*

 Engedélyezi a címtár tartalmának, az IIS sikertelen hozzáférés-kérelemnaplóinak és/vagy Az IIS-naplóknak a gyűjtését.  

 Nem kötelező **scheduledTransferPeriod** attribútum. Lásd a magyarázatot korábban.  

|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**IISLogs**|Ha ezt az elemet beista a konfigurációba, az lehetővé teszi az IIS-naplók gyűjtését:<br /><br /> **containerName** - A blob tároló neve az Azure Storage-fiókban az IIS-naplók tárolására.|   
|**Sikertelen kérőrök**|Ennek az elemnek a konfigurációba való beépítése lehetővé teszi az IIS-helyvagy alkalmazás sikertelen kérelmekre vonatkozó naplók gyűjtését. A rendszer alatt is engedélyeznie kell a nyomkövetési **beállításokat. Webkiszolgáló** a **Web.config fájlban.**|  
|**Datasources**|A figyelni nek ikonra kerülő könyvtárak listája.|




## <a name="datasources-element"></a>DataSources elem  
 *Fa: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Könyvtárak - Adatforrások*

 A figyelni nek ikonra kerülő könyvtárak listája.  

|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**DirectoryConfiguration (Címtárkonfiguráció)**|Kötelező. Kötelező attribútum:<br /><br /> **containerName** - A blob tároló neve az Azure Storage-fiókban, amely a naplófájlok tárolására szolgál.|  





## <a name="directoryconfiguration-element"></a>DirectoryConfiguration elem  
 *Fa: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Könyvtárak - Adatforrások - DirectoryConfiguration*

 Tartalmazhat abszolút **vagy** **helyi erőforrás elemet,** de mindkettőt nem.  

|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**Abszolút**|A figyelni nek iktatandó könyvtár abszolút elérési útja. A következő attribútumokra van szükség:<br /><br /> - **Elérési út** - A figyelni a könyvtár abszolút elérési útja.<br /><br /> - **expandEnvironment** - Azt állítja be, hogy a Path környezeti változói ki vannak-e bontva.|  
|**Helyi erőforrás**|A figyelni nek megfelelő helyi erőforráshoz viszonyított elérési út. A szükséges attribútumok a következők:<br /><br /> - **Név** – A figyelni kívánt könyvtárat tartalmazó helyi erőforrás<br /><br /> - **relativePath** - A figyelni kívánt könyvtárat tartalmazó névhez viszonyított elérési út|  



## <a name="etwproviders-element"></a>EtwProviders elem  
 *Fa: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders*

 EtW-események gyűjteményének konfigurálása az EventSource és/vagy ETW Manifest alapú szolgáltatóktól.  

|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Az [EventSource osztályból](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx)létrehozott események gyűjteményének konfigurálása. Kötelező attribútum:<br /><br /> **provider** - Az EventSource esemény osztályneve.<br /><br /> A választható attribútumok a következők:<br /><br /> - **scheduledTransferLogLevelFilter** - A tárfiókba átvinni a minimális súlyossági szint.<br /><br /> - **scheduledTransferPeriod** - A tárolóba történő ütemezett átvitelek közötti időköz a legközelebbi percre kerekítve. Az érték egy ["Időtartam adattípus" XML.](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**EtwManifestProviderConfiguration**|Kötelező attribútum:<br /><br /> **szolgáltató** - Az eseményszolgáltató GUID azonosítója<br /><br /> A választható attribútumok a következők:<br /><br /> - **scheduledTransferLogLevelFilter** - A tárfiókba átvinni a minimális súlyossági szint.<br /><br /> - **scheduledTransferPeriod** - A tárolóba történő ütemezett átvitelek közötti időköz a legközelebbi percre kerekítve. Az érték egy ["Időtartam adattípus" XML.](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration elem  
 *Fa: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders- EtwEventSourceProviderConfiguration*

 Az [EventSource osztályból](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx)létrehozott események gyűjteményének konfigurálása.  

|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**DefaultEvents (Alapértelmezett események)**|Nem kötelező attribútum:<br/><br/> **eventDestination** - Az események tárolásához a táblának a neve|  
|**Esemény**|Kötelező attribútum:<br /><br /> **id** - Az esemény azonosítója.<br /><br /> Nem kötelező attribútum:<br /><br /> **eventDestination** - Az események tárolásához a táblának a neve|  



## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration elem  
 *Fa: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders - EtwManifestProviderConfiguration*

|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**DefaultEvents (Alapértelmezett események)**|Nem kötelező attribútum:<br /><br /> **eventDestination** - Az események tárolásához a táblának a neve|  
|**Esemény**|Kötelező attribútum:<br /><br /> **id** - Az esemény azonosítója.<br /><br /> Nem kötelező attribútum:<br /><br /> **eventDestination** - Az események tárolásához a táblának a neve|  



## <a name="metrics-element"></a>Metrikák elem  
 *Fa: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Metrikák*

 Lehetővé teszi a gyors lekérdezésekre optimalizált teljesítményszámláló tábla létrehozását. A **PerformanceCounters** elemben definiált minden egyes teljesítményszámláló a Teljesítményszámláló tábla mellett a Metrikák táblában is tárolódik.  

 A **resourceId** attribútum szükséges.  A virtuális gép vagy a virtuálisgép-méretezési készlet erőforrás-azonosítója, amelybe az Azure Diagnosztikát telepíti. Az **erőforrás-azonosító** beszerezniaz [Azure Portalon.](https://portal.azure.com) Válassza az**Erőforráscsoportok**  ->   ->  **tallózása****<név lehetőséget.\>** Kattintson a **Tulajdonságok csempére,** és másolja az értéket az **Azonosító** mezőből.  

|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**Metrikaösszesítés**|Kötelező attribútum:<br /><br /> **scheduledTransferPeriod** - A tárolóba történő ütemezett átvitelek közötti időköz a legközelebbi percre kerekítve. Az érték egy ["Időtartam adattípus" XML.](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="performancecounters-element"></a>PerformanceCounters elem  
 *Fa: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - PerformanceCounters*

 Lehetővé teszi a teljesítményszámlálók gyűjtését.  

 Nem kötelező attribútum:  

 Nem kötelező **scheduledTransferPeriod** attribútum. Lásd a magyarázatot korábban.

|Gyermek elem|Leírás|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguration**|A következő attribútumokra van szükség:<br /><br /> - **counterSpecifier** - A teljesítményszámláló neve. Például: `\Processor(_Total)\% Processor Time`. A gazdagép teljesítményszámlálóinak listájának leéséhez futtassa a parancsot. `typeperf`<br /><br /> - **sampleRate** - Milyen gyakran kell mintát venni a számlálóból.<br /><br /> Nem kötelező attribútum:<br /><br /> **egység** - A számláló mértékegysége. Az értékek az [UnitType osztályban](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.sql.models.unittype?view=azure-dotnet) érhetők el |
|**Mosogató** | Hozzáadva az 1.5-ben. Választható. A fogadó helyére mutat diagnosztikai adatok küldéséhez is. Például az Azure Monitor vagy az Event Hubs.|    




## <a name="windowseventlog-element"></a>WindowsEventLog elem
 *Fa: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - WindowsEventLog*

 Engedélyezi a Windows eseménynaplók gyűjtését.  

 Nem kötelező **scheduledTransferPeriod** attribútum. Lásd a magyarázatot korábban.  

|Gyermek elem|Leírás|  
|-------------------|-----------------|  
|**Datasource**|A Windows eseménynaplói. Kötelező attribútum:<br /><br /> **name** - Az összegyűjtendő Windows-eseményeket leíró XPath-lekérdezés. Például:<br /><br /> `Application!*[System[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level <= 3)]`<br /><br /> Az összes esemény összegyűjtéséhez adja meg a "*" |
|**Mosogató** | Hozzáadva az 1.5-ben. Választható. A fogadó helyére mutat, és diagnosztikai adatokat is küld az összes gyermekelemhez, amely támogatja a fogadókat. A fogadó példa az Application Insights vagy az Event Hubs.|  


## <a name="logs-element"></a>Naplók elem  
 *Fa: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Naplók*

 Az 1.0-s és 1.1-es verzióban van jelen. Hiányzik az 1.2-ből. Hozzáadva az 1.3-hoz.  

 Az alapvető Azure-naplók pufferkonfigurációját határozza meg.  

|Attribútum|Típus|Leírás|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**nem aláírtint**|Választható. Megadja a fájlrendszer által a megadott adatokhoz rendelkezésre álló maximális számú tárterületet.<br /><br /> Az alapértelmezett érték a 0.|  
|**scheduledTransferLogLevelFilter**|**Karakterlánc**|Választható. Megadja az átvitt naplóbejegyzések minimális súlyossági szintjét. Az alapértelmezett érték az **Undefined**, amely az összes naplót átviszi. További lehetséges értékek (a legtöbb és a legkisebb információ sorrendjében) a **részletes**, **információs**, **figyelmeztetési**, **hiba**és **kritikus**értékek.|  
|**scheduledTransferPeriod**|**Időtartam**|Választható. Megadja az ütemezett adattovábbítások közötti intervallumot, a legközelebbi percre felfelé kerekítve.<br /><br /> Az alapértelmezett érték a PT0S.|  
|**Mosogató** |**Karakterlánc**| Hozzáadva az 1.5-ben. Választható. A fogadó helyére mutat diagnosztikai adatok küldéséhez is. Például az Application Insights vagy az Event Hubs.|  

## <a name="dockersources"></a>DockerSources
 *Fa: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - DockerSources*

 Hozzáadva az 1.9-ben.

|Elem neve|Leírás|  
|------------------|-----------------|  
|**Statisztikák**|Azt mondja a rendszernek, hogy gyűjtse össze a Docker-tárolók statisztikáit|  

## <a name="sinksconfig-element"></a>MosogatókKonfigurációs elem  
 *Fa: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig*

 A diagnosztikai adatok küldéséhez helyeket tartalmazó helyek listája és az ezekhez a helyekhez társított konfiguráció.  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**Sink (Fogadó)**|Lásd a leírás máshol ezen az oldalon.|  

## <a name="sink-element"></a>Fogadó elem
 *Fa: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink*

 Hozzáadva az 1.5-ös verzióban.  

 Meghatározza azokat a helyeket, amelyeknek diagnosztikai adatokat kell küldeni. Például az Application Insights szolgáltatás.  

|Attribútum|Típus|Leírás|  
|---------------|----------|-----------------|  
|**név**|sztring|A fogadónevet azonosító karakterlánc.|  

|Elem|Típus|Leírás|  
|-------------|----------|-----------------|  
|**Application Insights**|sztring|Csak akkor használatos, ha adatokat küld az Application Insights. Tartalmazza a Instrumentation kulcs egy aktív Application Insights-fiók, amely hozzáféréssel rendelkezik.|  
|**Csatornák**|sztring|Minden további szűréshez, amelyet|  

## <a name="channels-element"></a>Csatornák elem  
 *Fa: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink - Csatornák*

 Hozzáadva az 1.5-ös verzióban.  

 A fogadón áthaladó naplóadatok adatfolyamainak szűrőit határozza meg.  

|Elem|Típus|Leírás|  
|-------------|----------|-----------------|  
|**Csatorna**|sztring|Lásd a leírás máshol ezen az oldalon.|  

## <a name="channel-element"></a>Csatorna elem
 *Fa: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink - Csatornák - Csatorna*

 Hozzáadva az 1.5-ös verzióban.  

 Meghatározza azokat a helyeket, amelyeknek diagnosztikai adatokat kell küldeni. Például az Application Insights szolgáltatás.  

|Attribútumok|Típus|Leírás|  
|----------------|----------|-----------------|  
|**Loglevel**|**Karakterlánc**|Megadja az átvitt naplóbejegyzések minimális súlyossági szintjét. Az alapértelmezett érték az **Undefined**, amely az összes naplót átviszi. További lehetséges értékek (a legtöbb és a legkisebb információ sorrendjében) a **részletes**, **információs**, **figyelmeztetési**, **hiba**és **kritikus**értékek.|  
|**név**|**Karakterlánc**|A hivatkozandó csatorna egyedi neve|  


## <a name="privateconfig-element"></a>PrivateConfig elem
 *Fa: Gyökér - DiagnosticsConfiguration - PrivateConfig*

 Hozzáadva az 1.3-as verzióban.  

 Optional  

 A tárfiók (név, kulcs és végpont) személyes adatait tárolja. Ez az információ a virtuális gépre kerül, de nem olvasható be belőle.  

|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**StorageAccount**|A használandó tárfiók. A következő attribútumok szükségesek<br /><br /> - **name** - A tárfiók neve.<br /><br /> - **kulcs** - A tárfiók kulcsa.<br /><br /> - **végpont** – A tárfiók eléréséhez szolgáló végpont. <br /><br /> -**sasToken** (hozzáadott 1.8.1) - A privát konfigurációban a tárfiók kulcsa helyett sas-jogkivonatot adhat meg. Ha meg van adva, a tárfiók kulcs figyelmen kívül hagyja. <br />A SAS-tokenre vonatkozó követelmények: <br />- Csak a SAS token támogatja a fiókot <br />- *b*, *t* szolgáltatástípusok szükségesek. <br /> - *a*, *c*, *u*, *w* engedélyek szükségesek. <br /> - *c*, *o* erőforrástípusok szükségesek. <br /> - Csak a HTTPS protokollt támogatja <br /> - A kezdési és lejárati időnek érvényesnek kell lennie.|  


## <a name="isenabled-element"></a>IsEnabled elem  
 *Fa: Gyökér - DiagnosticsConfiguration - IsEnabled*

 Logikai. A `true` diagnosztika engedélyezéséhez vagy `false` a diagnosztika letiltásához használható.

## <a name="example-configuration"></a>Konfigurációs példa
 A következőkben a Windows diagnosztikai bővítményének teljes mintakonfigurációja látható a JSON-ban és az XML-ben is.

 
### <a name="json"></a>JSON

A *PublicConfig* és *a PrivateConfig* külön vannak választva, mert a legtöbb JSON-használati esetben különböző változókként kerülnek átadásra. Ilyen esetek közé tartozik a Resource Manager-sablonok, a PowerShell és a Visual Studio.

> [!NOTE]
> A nyilvános konfigurációs Azure Monitor-fogadó definíciója két tulajdonsággal, *erőforrásazonosítóval* és *régióval*rendelkezik. Ezek csak a klasszikus virtuális gépek és a klasszikus felhőszolgáltatások. Ezeket a tulajdonságokat nem szabad más erőforrásokhoz használni.

```json
"PublicConfig" {
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 10000,
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "AzureMonitorSink",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT1M",
                        "unit": "percent"
                    }
                ]
            },
            "Directories": {
                "scheduledTransferPeriod": "PT5M",
                "IISLogs": {
                    "containerName": "iislogs"
                },
                "FailedRequestLogs": {
                    "containerName": "iisfailed"
                },
                "DataSources": [
                    {
                        "containerName": "mynewprocess",
                        "Absolute": {
                            "path": "C:\\MyNewProcess",
                            "expandEnvironment": false
                        }
                    },
                    {
                        "containerName": "badapp",
                        "Absolute": {
                            "path": "%SYSTEMDRIVE%\\BadApp",
                            "expandEnvironment": true
                        }
                    },
                    {
                        "containerName": "goodapp",
                        "LocalResource": {
                            "relativePath": "..\\PeanutButter",
                            "name": "Skippy"
                        }
                    }
                ]
            },
            "EtwProviders": {
                "sinks": "",
                "EtwEventSourceProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT5M",
                        "provider": "MyProviderClass",
                        "Event": [
                            {
                                "id": 0
                            },
                            {
                                "id": 1,
                                "eventDestination": "errorTable"
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ],
                "EtwManifestProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT2M",
                        "scheduledTransferLogLevelFilter": "Information",
                        "provider": "5974b00b-84c2-44bc-9e58-3a2451b4e3ad",
                        "Event": [
                            {
                                "id": 0
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT5M",
                "DataSource": [
                    {
                        "name": "System!*[System[Provider[@Name='Microsoft Antimalware']]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]"
                    }
                ]
            },
            "Logs": {
                "scheduledTransferPeriod": "PT1M",
                "scheduledTransferLogLevelFilter": "Verbose",
                "sinks": "ApplicationInsights.AppLogs"
            },
            "CrashDumps": {
                "directoryQuotaPercentage": 30,
                "dumpType": "Mini",
                "containerName": "wad-crashdumps",
                "CrashDumpConfiguration": [
                    {
                        "processName": "mynewprocess.exe"
                    },
                    {
                        "processName": "badapp.exe"
                    }
                ]
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "AzureMonitorSink",
                    "AzureMonitor":
                    {
                        "ResourceId": "{insert resourceId if a classic VM or cloud service, else property not needed}",
                        "Region": "{insert Azure region of resource if a classic VM or cloud service, else property not needed}"
                    }
                },
                {
                    "name": "ApplicationInsights",
                    "ApplicationInsights": "{Insert InstrumentationKey}",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "Errors"
                            },
                            {
                                "logLevel": "Verbose",
                                "name": "AppLogs"
                            }
                        ]
                    }
                },
                {
                    "name": "EventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryEventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryStorageAccount",
                    "StorageAccount": {
                        "name": "secondarydiagstorageaccount",
                        "endpoint": "https://core.windows.net"
                    }
                }
            ]
        }
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

> [!NOTE]
> A privát konfigurációs Azure Monitor-fogadó definíciója két tulajdonsággal rendelkezik: *A Kinek* és *a Titkos.* Ezek csak a klasszikus virtuális gépek és a klasszikus felhőszolgáltatások. Ezeket a tulajdonságokat nem szabad más erőforrásokhoz használni.


```json
"PrivateConfig" {
    "storageAccountName": "diagstorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "EventHub": {
        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    },
    "AzureMonitorAccount": {
        "ServicePrincipalMeta": {
            "PrincipalId": "{Insert service principal client Id}",
            "Secret": "{Insert service principal client secret}"
        }
    },
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "key": "{base64 encoded key}",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    },
    "SecondaryEventHubs": {
        "EventHub": [
            {
                "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                "SharedAccessKeyName": "SendRule",
                "SharedAccessKey": "{base64 encoded key}"
            }
        ]
    }
}

```

### <a name="xml"></a>XML

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">   
  <PublicConfig>  
    <WadCfg>  
      <DiagnosticMonitorConfiguration overallQuotaInMB="10000">  

        <PerformanceCounters scheduledTransferPeriod="PT1M", sinks="AzureMonitorSink">  
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
        </PerformanceCounters>  

        <Directories scheduledTransferPeriod="PT5M">  
          <IISLogs containerName="iislogs" />  
          <FailedRequestLogs containerName="iisfailed" />  

          <DataSources>  
            <DirectoryConfiguration containerName="mynewprocess">  
              <Absolute path="C:\MyNewProcess" expandEnvironment="false" />  
            </DirectoryConfiguration>  
            <DirectoryConfiguration containerName="badapp">  
              <Absolute path="%SYSTEMDRIVE%\BadApp" expandEnvironment="true" />  
            </DirectoryConfiguration>  
            <DirectoryConfiguration containerName="goodapp">  
              <LocalResource name="Skippy" relativePath="..\PeanutButter"/>  
            </DirectoryConfiguration>  
          </DataSources>  

        </Directories>  

        <EtwProviders>  
          <EtwEventSourceProviderConfiguration   
                       provider="MyProviderClass"   
                       scheduledTransferPeriod="PT5M">  
            <Event id="0"/>  
            <Event id="1" eventDestination="errorTable"/>  
            <DefaultEvents />  
          </EtwEventSourceProviderConfiguration>  
          <EtwManifestProviderConfiguration provider="5974b00b-84c2-44bc-9e58-3a2451b4e3ad" scheduledTransferLogLevelFilter="Information" scheduledTransferPeriod="PT2M">  
            <Event id="0"/>  
            <DefaultEvents eventDestination="defaultTable"/>  
          </EtwManifestProviderConfiguration>  
        </EtwProviders>  

        <WindowsEventLog scheduledTransferPeriod="PT5M">  
          <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>  
          <DataSource name="System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]" />  
          <DataSource name="System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]" />  
        </WindowsEventLog>  

        <Logs  bufferQuotaInMB="1024"   
             scheduledTransferPeriod="PT1M"   
             scheduledTransferLogLevelFilter="Verbose"   
             sinks="ApplicationInsights.AppLogs"/>  <!-- sinks attribute added in 1.5 -->  

        <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
          <CrashDumpConfiguration processName="mynewprocess.exe" />  
          <CrashDumpConfiguration processName="badapp.exe"/>  
        </CrashDumps>  

        <DockerSources> <!-- Added in 1.9 -->
          <Stats enabled="true" sampleRate="PT1M" scheduledTransferPeriod="PT1M" />
        </DockerSources>

      </DiagnosticMonitorConfiguration>  

      <SinksConfig>   <!-- Added in 1.5 -->  
        <Sink name="AzureMonitorSink">
            <AzureMonitor> <!-- Added in 1.11 -->
                <resourceId>{insert resourceId}</ResourceId> <!-- Parameter only needed for classic VMs and Classic Cloud Services, exclude VMSS and Resource Manager VMs-->
                <Region>{insert Azure region of resource}</Region> <!-- Parameter only needed for classic VMs and Classic Cloud Services, exclude VMSS and Resource Manager VMs -->
            </AzureMonitor>
        </Sink>
        <Sink name="ApplicationInsights">   
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>   
          <Channels>   
            <Channel logLevel="Error" name="Errors"  />   
            <Channel logLevel="Verbose" name="AppLogs"  />   
          </Channels>   
        </Sink>   
        <Sink name="EventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryEventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryStorageAccount"> <!-- Added in 1.7 -->
          <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" />
        </Sink>
   </SinksConfig>

  </WadCfg>  

  <StorageAccount>diagstorageaccount</StorageAccount>
  <StorageType>TableAndBlob</StorageType> <!-- Added in 1.8 -->  
  </PublicConfig>  

  <PrivateConfig>  <!-- Added in 1.3 -->  
    <StorageAccount name="" key="" endpoint="" sasToken="{sas token}"  />  <!-- sasToken in Private config added in 1.8.1 -->  
    <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />

    <AzureMonitorAccount>
        <ServicePrincipalMeta> <!-- Added in 1.11; only needed for classic VMs and Classic cloud services -->
            <PrincipalId>{Insert service principal clientId}</PrincipalId>
            <Secret>{Insert service principal client secret}</Secret>
        </ServicePrincipalMeta>
    </AzureMonitorAccount>

    <SecondaryStorageAccounts>
       <StorageAccount name="secondarydiagstorageaccount" key="{base64 encoded key}" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>

    <SecondaryEventHubs>
       <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
    </SecondaryEventHubs>

  </PrivateConfig>  
  <IsEnabled>true</IsEnabled>  
</DiagnosticsConfiguration>  

```  
> [!NOTE]
> A nyilvános konfigurációs Azure Monitor-fogadó definíciója két tulajdonsággal rendelkezik, erőforrásazonosítóval és régióval. Ezek csak a klasszikus virtuális gépek és a klasszikus felhőszolgáltatások. Ezeket a tulajdonságokat nem szabad használni az Erőforrás-kezelő virtuális gépek vagy a virtuálisgép-méretezési készletek.
> Van is egy további privát konfigurációs elem az Azure Monitor-fogadó, amely átmegy egy egyszerű azonosító és titkos. Ez csak a klasszikus virtuális gépek és a klasszikus felhőszolgáltatások esetén szükséges. A Resource Manager virtuális gépek és vMSS az Azure Monitor definíciója a privát konfigurációs elem kizárható.
>
