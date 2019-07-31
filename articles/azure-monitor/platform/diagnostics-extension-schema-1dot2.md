---
title: Azure Diagnostics Extension 1,2 konfigurációs séma
description: CSAK akkor fontos, ha az Azure SDK 2,5-t használja az Azure Virtual Machines, Virtual Machine Scale Sets, Service Fabric vagy Cloud Services használatával.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/15/2017
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: dae74e730d6e175fa3e447150adce4caecd3d7a3
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "60237838"
---
# <a name="azure-diagnostics-12-configuration-schema"></a>Azure Diagnostics 1,2 konfigurációs séma
> [!NOTE]
> Azure Diagnostics a teljesítményszámlálók és az Azure Virtual Machines, a Virtual Machine Scale Sets, a Service Fabric és a Cloud Services statisztikáinak összegyűjtésére szolgáló összetevő.  Ez a lap csak akkor fontos, ha az egyik szolgáltatást használja.
>

A Azure Diagnostics más Microsoft diagnosztikai termékekhez, például a Azure Monitorekhez használható, beleértve a Application Insights és a Log Analytics.

Ez a séma határozza meg azokat a lehetséges értékeket, amelyekkel a diagnosztika-figyelő indításakor inicializálhatja a diagnosztikai konfigurációs beállításokat.  


 Töltse le a nyilvános konfigurációs fájl sémájának definícióját a következő PowerShell-parancs végrehajtásával:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

 További információ a Azure Diagnostics használatáról: a [diagnosztika engedélyezése az Azure-ban Cloud Services](https://azure.microsoft.com/documentation/articles/cloud-services-dotnet-diagnostics/).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Példa a diagnosztika konfigurációs fájljára  
 Az alábbi példa egy tipikus diagnosztikai konfigurációs fájlt mutat be:  

```xml
<?xml version="1.0" encoding="utf-8"?>  
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">  
  <WadCfg>  
    <DiagnosticMonitorConfiguration overallQuotaInMB="10000">  
      <PerformanceCounters scheduledTransferPeriod="PT1M">  
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
        <EtwEventSourceProviderConfiguration provider="MyProviderClass" scheduledTransferPeriod="PT5M">  
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
      <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
        <CrashDumpConfiguration processName="mynewprocess.exe" />  
        <CrashDumpConfiguration processName="badapp.exe"/>  
      </CrashDumps>  
    </DiagnosticMonitorConfiguration>  
  </WadCfg>  
</PublicConfig>  

```  

## <a name="diagnostics-configuration-namespace"></a>Diagnosztikai konfigurációs névtér  
 A diagnosztika konfigurációs fájljának XML-névtere a következő:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="publicconfig-element"></a>PublicConfig elem  
 A diagnosztikai konfigurációs fájl legfelső szintű eleme. A következő táblázat a konfigurációs fájl elemeit ismerteti.  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**WadCfg**|Kötelező. A telemetria adatok gyűjtésére szolgáló konfigurációs beállítások.|  
|**StorageAccount**|Annak az Azure Storage-fióknak a neve, amelybe az adattárakat tárolni szeretné. Ez paraméterként is megadható a set-AzureServiceDiagnosticsExtension parancsmag futtatásakor.|  
|**LocalResourceDirectory**|Annak a virtuális gépnek a könyvtára, amelyet a monitoring ügynök az események tárolására használ. Ha nincs beállítva, a rendszer az alapértelmezett könyvtárat használja:<br /><br /> Feldolgozói/webes szerepkör esetén:`C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Virtuális gépek esetén:`C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> A kötelező attribútumok a következők:<br /><br /> -                      **elérési út** – a rendszer Azure Diagnostics által használandó könyvtára.<br /><br /> -                      **expandEnvironment** – meghatározza, hogy a környezeti változók ki vannak-e bontva az elérési útban.|  

## <a name="wadcfg-element"></a>WadCFG elem  
Meghatározza a telemetria adatok gyűjtésének konfigurációs beállításait. A következő táblázat a gyermek elemeket ismerteti:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**DiagnosticMonitorConfiguration**|Kötelező. A választható attribútumok a következők:<br /><br /> -                     **overallQuotaInMB** – a Azure Diagnostics által gyűjtött különböző diagnosztikai adatok által felhasználható helyi lemezterület maximális mérete. Az alapértelmezett beállítás a 5120MB.<br /><br /> -                     **useProxyServer** – konfigurálja a Azure Diagnostics az IE-beállításokban beállított proxykiszolgáló-beállítások használatára.|  
|**CrashDumps**|Összeomlási memóriaképek gyűjtésének engedélyezése. A választható attribútumok a következők:<br /><br /> -                     **containerName** – az összeomlási memóriaképek tárolására szolgáló Azure Storage-fiókban található blob-tároló neve.<br /><br /> -                     **crashDumpType** – a Azure Diagnostics konfigurálja a mini-vagy teljes összeomlási memóriaképek gyűjtésére.<br /><br /> -                     **directoryQuotaPercentage**– a virtuális gépen lévő összeomlási memóriaképek számára fenntartott **overallQuotaInMB** százalékos arányát adja meg.|  
|**DiagnosticInfrastructureLogs**|A Azure Diagnostics által létrehozott naplók gyűjtésének engedélyezése. A diagnosztikai infrastruktúra naplói a diagnosztikai rendszer hibaelhárításához hasznosak. A választható attribútumok a következők:<br /><br /> -                     **scheduledTransferLogLevelFilter** – a begyűjtött naplók minimális súlyossági szintjét konfigurálja.<br /><br /> -                     **scheduledTransferPeriod** – az ütemezett átvitelek közötti időköz a legközelebbi percre kerekítve. Az érték egy [XML "időtartam adattípusa".](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**Címtárak**|Engedélyezi egy könyvtár tartalmának gyűjtését, az IIS nem tudta elérni a hozzáférési kérelmek naplóit és/vagy az IIS-naplókat. Nem kötelező attribútum:<br /><br /> **scheduledTransferPeriod** – az ütemezett átvitelek közötti időköz a legközelebbi percre kerekítve. Az érték egy [XML "időtartam adattípusa".](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**EtwProviders**|A EventSource és/vagy a ETW manifest-alapú szolgáltatók ETW-eseményeinek gyűjteményét konfigurálja.|  
|**Metrikák**|Ez az elem lehetővé teszi a gyors lekérdezésekhez optimalizált teljesítményszámláló-tábla létrehozását. A **PerformanceCounters** elemben definiált minden teljesítményszámláló a teljesítményszámláló táblán kívül a metrikák táblázatban található. Kötelező attribútum:<br /><br /> **resourceId** – ez annak a virtuális gépnek az erőforrás-azonosítója, amelyre a Azure Diagnostics központilag telepíti. Szerezze be a **resourceID** a [Azure Portal](https://portal.azure.com). Válassza a **Tallózás** -> **erőforráscsoportok** ->  **< anév\>** elemet. Kattintson a **Tulajdonságok** csempére, és másolja az értéket az **azonosító** mezőből.|  
|**PerformanceCounters**|A teljesítményszámlálók gyűjteményének engedélyezése. Nem kötelező attribútum:<br /><br /> **scheduledTransferPeriod** – az ütemezett átvitelek közötti időköz a legközelebbi percre kerekítve. Az érték egy [XML "időtartam adattípusa".](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**WindowsEventLog**|Engedélyezi a Windows-eseménynaplók gyűjteményét. Nem kötelező attribútum:<br /><br /> **scheduledTransferPeriod** – az ütemezett átvitelek közötti időköz a legközelebbi percre kerekítve. Az érték egy [XML "időtartam adattípusa".](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  

## <a name="crashdumps-element"></a>CrashDumps Element  
 Összeomlási memóriaképek gyűjtését teszi lehetővé. A következő táblázat a gyermek elemeket ismerteti:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**CrashDumpConfiguration**|Kötelező. Kötelező attribútum:<br /><br /> **processName** – annak a folyamatnak a neve, amelyet az összeomlási memóriakép gyűjtéséhez Azure Diagnostics szeretne.|  
|**crashDumpType**|A Azure Diagnostics konfigurálja a mini-vagy teljes összeomlási memóriaképek gyűjtésére.|  
|**directoryQuotaPercentage**|Az összeomlási memóriaképek számára fenntartott **overallQuotaInMB** százalékos arányát konfigurálja a virtuális gépen.|  

## <a name="directories-element"></a>Címtárak elem  
 Engedélyezi egy könyvtár tartalmának gyűjtését, az IIS nem tudta elérni a hozzáférési kérelmek naplóit és/vagy az IIS-naplókat. A következő táblázat a gyermek elemeket ismerteti:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**Adatforrások**|A figyelni kívánt címtárak listája.|  
|**FailedRequestLogs**|A konfigurációban található elem belefoglalása lehetővé teszi, hogy az IIS-helyekre vagy-alkalmazásokra vonatkozó sikertelen kérelmeket tartalmazó naplók gyűjtése megtörténjen. A nyomkövetési beállításokat is engedélyeznie kell a **rendszeren. Webkiszolgáló** a **web. config fájlban**.|  
|**IISLogs**|A konfigurációban található elemet is beleértve az IIS-naplók gyűjteményének engedélyezése:<br /><br /> **containerName** – az IIS-naplók tárolására szolgáló Azure Storage-fiókban található blob-tároló neve.|  

## <a name="datasources-element"></a>Adatforrások eleme  
 A figyelni kívánt címtárak listája. A következő táblázat a gyermek elemeket ismerteti:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**DirectoryConfiguration**|Kötelező. Kötelező attribútum:<br /><br /> **containerName** – a naplófájlok tárolására szolgáló Azure Storage-fiókban található blob-tároló neve.|  

## <a name="directoryconfiguration-element"></a>DirectoryConfiguration elem  
 A **DirectoryConfiguration** az **abszolút** vagy a **LocalResource** elemet is tartalmazhatják, de mindkettőt nem. A következő táblázat a gyermek elemeket ismerteti:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**Abszolút**|A figyelni kívánt könyvtár abszolút elérési útja. A következő attribútumok szükségesek:<br /><br /> -                     **Elérési út** – a figyelni kívánt könyvtár abszolút elérési útja.<br /><br /> -                      **expandEnvironment** – azt határozza meg, hogy az elérési úton lévő környezeti változók kibontása megtörténjen-e.|  
|**LocalResource**|A figyelni kívánt helyi erőforráshoz viszonyított elérési út. A kötelező attribútumok a következők:<br /><br /> -                     **Név** – a figyelni kívánt könyvtárat tartalmazó helyi erőforrás<br /><br /> -                     **relativePath** – a figyelni kívánt könyvtárat tartalmazó névhez viszonyított elérési út|  

## <a name="etwproviders-element"></a>EtwProviders elem  
 A EventSource és/vagy a ETW manifest-alapú szolgáltatók ETW-eseményeinek gyűjteményét konfigurálja. A következő táblázat a gyermek elemeket ismerteti:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|A [EventSource osztályból](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx)generált események gyűjteményét konfigurálja. Kötelező attribútum:<br /><br /> **szolgáltató** – a EventSource esemény osztályának neve.<br /><br /> A választható attribútumok a következők:<br /><br /> -                     **scheduledTransferLogLevelFilter** – a minimális súlyossági szint, amelyet át kell vinni a Storage-fiókjába.<br /><br /> -                     **scheduledTransferPeriod** – az ütemezett átvitelek közötti időköz a legközelebbi percre kerekítve. Az érték egy [XML-időtartam](https://www.w3schools.com/xml/schema_dtypes_date.asp)adattípusa.|  
|**EtwManifestProviderConfiguration**|Kötelező attribútum:<br /><br /> **Provider** – az esemény SZOLGÁLTATÓJÁNAK GUID azonosítója<br /><br /> A választható attribútumok a következők:<br /><br /> - **scheduledTransferLogLevelFilter** – a minimális súlyossági szint, amelyet át kell vinni a Storage-fiókjába.<br /><br /> -                     **scheduledTransferPeriod** – az ütemezett átvitelek közötti időköz a legközelebbi percre kerekítve. Az érték egy [XML-időtartam](https://www.w3schools.com/xml/schema_dtypes_date.asp)adattípusa.|  

## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration Element  
 A [EventSource osztályból](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx)generált események gyűjteményét konfigurálja. A következő táblázat a gyermek elemeket ismerteti:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**DefaultEvents**|Nem kötelező attribútum:<br /><br /> **eventDestination** – az eseményeket tároló tábla neve|  
|**Esemény**|Kötelező attribútum:<br /><br /> **azonosító** – az esemény azonosítója.<br /><br /> Nem kötelező attribútum:<br /><br /> **eventDestination** – az eseményeket tároló tábla neve|  

## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration Element  
 A következő táblázat a gyermek elemeket ismerteti:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**DefaultEvents**|Nem kötelező attribútum:<br /><br /> **eventDestination** – az eseményeket tároló tábla neve|  
|**Esemény**|Kötelező attribútum:<br /><br /> **azonosító** – az esemény azonosítója.<br /><br /> Nem kötelező attribútum:<br /><br /> **eventDestination** – az eseményeket tároló tábla neve|  

## <a name="metrics-element"></a>Metrikák elem  
 Lehetővé teszi a gyors lekérdezésekhez optimalizált teljesítményszámláló-tábla létrehozását. A következő táblázat a gyermek elemeket ismerteti:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**MetricAggregation**|Kötelező attribútum:<br /><br /> **scheduledTransferPeriod** – az ütemezett átvitelek közötti időköz a legközelebbi percre kerekítve. Az érték egy [XML-időtartam](https://www.w3schools.com/xml/schema_dtypes_date.asp)adattípusa.|  

## <a name="performancecounters-element"></a>PerformanceCounters elem  
 A teljesítményszámlálók gyűjteményének engedélyezése. A következő táblázat a gyermek elemeket ismerteti:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**PerformanceCounterConfiguration**|A következő attribútumok szükségesek:<br /><br /> -                     **counterSpecifier** – a teljesítményszámláló neve. Például: `\Processor(_Total)\% Processor Time`. A gazdagépen található teljesítményszámlálók listájának lekéréséhez futtassa a parancsot `typeperf`.<br /><br /> -                     **mintavételi** – milyen gyakran kell mintát venni a számlálóból.<br /><br /> Nem kötelező attribútum:<br /><br /> **Unit (egység** ) – a számláló mértékegysége.|  

## <a name="performancecounterconfiguration-element"></a>PerformanceCounterConfiguration elem  
 A következő táblázat a gyermek elemeket ismerteti:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**annotation**|Kötelező attribútum:<br /><br /> **DisplayName** – a számláló megjelenítendő neve<br /><br /> Nem kötelező attribútum:<br /><br /> **területi** beállítás – a számláló nevének megjelenítésekor használandó területi beállítás|  

## <a name="windowseventlog-element"></a>WindowsEventLog elem  
 A következő táblázat a gyermek elemeket ismerteti:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**DataSource**|A gyűjteni kívánt Windows-eseménynaplók. Kötelező attribútum:<br /><br /> **név** – a gyűjteni kívánt Windows-eseményeket leíró XPath-lekérdezés. Példa:<br /><br /> `Application!*[System[(Level >= 3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level >= 3]]`<br /><br /> Az összes esemény összegyűjtéséhez válassza a "*" lehetőséget.|

