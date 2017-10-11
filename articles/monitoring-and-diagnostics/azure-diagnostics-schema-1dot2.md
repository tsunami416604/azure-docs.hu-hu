---
title: "Az Azure Diagnostics 1.2-es konfigurációs séma |} Microsoft Docs"
description: "CSAK akkor érvényes, ha Azure virtuális gépek, a virtuálisgép-méretezési csoportok, a Service Fabric vagy a Cloud Services Azure SDK 2.5 használ."
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/15/2017
ms.author: robb
ms.openlocfilehash: 1e9cc6d0950945df8c4fba74d8e1f6196be224f0
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="azure-diagnostics-12-configuration-schema"></a>Az Azure Diagnostics 1.2-es konfigurációs séma
> [!NOTE]
> Az Azure Diagnostics teljesítményszámlálók és más statisztika gyűjthet az Azure virtuális gépek, a virtuálisgép-méretezési csoportok, a Service Fabric és a Cloud Services összetevő.  Ezen a lapon csak fontos, ha ilyen szolgáltatást használ.
>

Az Azure Diagnostics más Microsoft-diagnosztika termékekkel, például Azure figyelő, az Application Insights és Naplóelemzési szolgál.

A séma definiálja az a lehetséges értékek segítségével diagnosztikai konfigurációs beállítások inicializálása a diagnosztikai figyelő indításakor.  


 Töltse le a nyilvános konfigurációs fájl sémadefiníciót hajtja végre a következő PowerShell-parancsot:  

```PowerShell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

 Azure Diagnostics használatával kapcsolatos további információkért lásd: [Azure Cloud Services diagnosztika engedélyezésével](http://azure.microsoft.com/documentation/articles/cloud-services-dotnet-diagnostics/).  

## <a name="example-of-the-diagnostics-configuration-file"></a>A diagnosztika konfigurációs fájl példa  
 A következő példa bemutatja egy tipikus diagnosztika konfigurációs fájlt:  

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

## <a name="diagnostics-configuration-namespace"></a>Diagnosztikai konfiguráció Namespace  
 A diagnosztika konfigurációs fájl XML-névtere van:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="publicconfig-element"></a>PublicConfig elem  
 Legfelső szintű elem a diagnosztika konfigurációs fájl. A következő táblázat ismerteti az elemek a konfigurációs fájl.  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**WadCfg**|Szükséges. Konfigurációs beállításait a telemetriai adatok összegyűjtésére.|  
|**StorageAccount**|Az adatok tárolásához Azure Storage-fiók neve. Ez is adható meg paraméterként a Set-AzureServiceDiagnosticsExtension parancsmag végrehajtása közben.|  
|**LocalResourceDirectory**|A könyvtár a figyelési ügynök eseményadatok tárolására használt virtuális gépen. Ha nem be van állítva, az alapértelmezett mappát használja:<br /><br /> A munkavégző vagy webes szerepkör:`C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> A virtuális gép:`C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Szükséges attribútumok pedig a következők:<br /><br /> -                      **elérési út** – a rendszer Azure Diagnostics által használandó könyvtárához.<br /><br /> -                      **expandEnvironment** -szabályozza, hogy az elérési útban környezeti változók bontva.|  

## <a name="wadcfg-element"></a>WadCFG elem  
A telemetriai adatok összegyűjtésére konfigurációs beállításokat határoz meg. A következő táblázat a gyermekelemek:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**DiagnosticMonitorConfiguration**|Szükséges. Nem kötelező attribútumok pedig a következők:<br /><br /> -                     **overallQuotaInMB** -Azure Diagnostics által gyűjtött, amelyeket lehet használni a különböző típusú diagnosztikai adatok helyi lemezterület maximális mennyisége. Az alapértelmezett érték 5120MB.<br /><br /> -                     **useProxyServer** -Azure Diagnostics konfigurálja a proxykiszolgáló beállításait használni az Internet Explorer beállításainak megfelelően.|  
|**CrashDumps**|Az összeomlási memóriaképek gyűjtésének engedélyezése. Nem kötelező attribútumok pedig a következők:<br /><br /> -                     **containerName** -a blob tároló összeomlási memóriaképek tárolására használható az Azure Storage-fiók nevét.<br /><br /> -                     **crashDumpType** -konfigurálja az Azure diagnosztikai gyűjtéséhez Mini vagy teljes összeomlási memóriaképek.<br /><br /> -                     **directoryQuotaPercentage**-százaléka konfigurálja **overallQuotaInMB** kell lefoglalni, az összeomlási memóriaképek a virtuális Gépen.|  
|**DiagnosticInfrastructureLogs**|Az Azure diagnosztikai által létrehozott naplók gyűjtésének engedélyezése. A diagnosztikai infrastruktúra naplók hasznosak a diagnosztika rendszer magát a hibaelhárításhoz. Nem kötelező attribútumok pedig a következők:<br /><br /> -                     **scheduledTransferLogLevelFilter** -konfigurálja a minimális súlyossági szintet a gyűjtött naplók.<br /><br /> -                     **scheduledTransferPeriod** -tároló ütemezett átvitelek között felfelé kerekítve a legközelebbi perc. Az érték egy [XML "Duration adattípusú."](http://www.w3schools.com/schema/schema_dtypes_date.asp)|  
|**Könyvtárak**|Lehetővé teszi, hogy egy könyvtárat, az IIS nem tudta belépési kérelem naplók és/vagy IIS-napló tartalmát gyűjteménye. Nem kötelező attribútum:<br /><br /> **scheduledTransferPeriod** -tároló ütemezett átvitelek között felfelé kerekítve a legközelebbi perc. Az érték egy [XML "Duration adattípusú."](http://www.w3schools.com/schema/schema_dtypes_date.asp)|  
|**EtwProviders**|Konfigurálja az EventSource ETW-eseményeinek gyűjtése és/vagy a szolgáltatók ETW Manifest alapján.|  
|**Metrikák**|Ez az elem lehetővé teszi a gyors lekérdezéseket optimalizált teljesítményt számláló tábla létrehozásához. Minden, a megadott teljesítményszámláló a **PerformanceCounters** elem a metrikák tábla mellett a teljesítményszámláló tábla tárolja. Kötelező attribútum:<br /><br /> **resourceId** -Ez az erőforrás-azonosítója, a virtuális gép Azure Diagnostics meg tudja telepít. Beolvasása a **resourceID** a a [Azure-portálon](https://portal.azure.com). Válassza ki **Tallózás** -> **erőforráscsoportok** -> **< név\>**. Kattintson a **tulajdonságok** csempére, majd másolja az értéket a **azonosító** mező.|  
|**PerformanceCounters**|Lehetővé teszi, hogy a teljesítményszámlálók gyűjteményét. Nem kötelező attribútum:<br /><br /> **scheduledTransferPeriod** -tároló ütemezett átvitelek között felfelé kerekítve a legközelebbi perc. Az érték egy [XML "Duration adattípus".](http://www.w3schools.com/schema/schema_dtypes_date.asp)|  
|**WindowsEventLog**|Lehetővé teszi a gyűjteményt, a Windows eseménynaplóiban keresse meg. Nem kötelező attribútum:<br /><br /> **scheduledTransferPeriod** -tároló ütemezett átvitelek között felfelé kerekítve a legközelebbi perc. Az érték egy [XML "Duration adattípus".](http://www.w3schools.com/schema/schema_dtypes_date.asp)|  

## <a name="crashdumps-element"></a>CrashDumps elem  
 Lehetővé teszi, hogy összeomlási memóriaképek gyűjteménye. A következő táblázat a gyermekelemek:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**CrashDumpConfiguration**|Szükséges. Kötelező attribútum:<br /><br /> **Folyamatnév** -nevét a folyamat kívánt Azure Diagnostics meg tudja gyűjteni az összeomlási memóriaképet.|  
|**crashDumpType**|Konfigurálja az Azure Diagnostics mini vagy teljes összeomlási memóriaképek összegyűjtése.|  
|**directoryQuotaPercentage**|Konfigurálja a százaléka **overallQuotaInMB** kell lefoglalni, az összeomlási memóriaképek a virtuális Gépen.|  

## <a name="directories-element"></a>Könyvtárak elem  
 Lehetővé teszi, hogy egy könyvtárat, az IIS nem tudta belépési kérelem naplók és/vagy IIS-napló tartalmát gyűjteménye. A következő táblázat a gyermekelemek:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**Adatforrások**|Figyelendő könyvtárainak listáját.|  
|**FailedRequestLogs**|Többek között az elem a konfigurációban lehetővé teszi, hogy az IIS-webhelyet vagy alkalmazást a sikertelen kérelmek kapcsolatos információkat naplózza gyűjteménye. Engedélyeznie kell a nyomkövetést **rendszer. Webkiszolgáló** a **Web.config**.|  
|**IISLogs**|Többek között az elem a konfigurációban lehetővé teszi, hogy az IIS-naplók a gyűjteményben:<br /><br /> **containerName** – az Azure Storage-fiók használható az IIS-napló tárolására a blob tároló nevét.|  

## <a name="datasources-element"></a>Adatforrások elem  
 Figyelendő könyvtárainak listáját. A következő táblázat a gyermekelemek:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**DirectoryConfiguration**|Szükséges. Kötelező attribútum:<br /><br /> **containerName** -a blob tároló a naplófájlok tárolására szolgáló használható az Azure Storage-fiók nevét.|  

## <a name="directoryconfiguration-element"></a>DirectoryConfiguration elem  
 **DirectoryConfiguration** vagy előfordulhat, hogy tartalmazza a **abszolút** vagy **LocalResource** elem, de soha sem egyszerre mindkettőre. A következő táblázat a gyermekelemek:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**Abszolút**|A figyelni kívánt könyvtár abszolút elérési útja A következő attribútumok szükségesek:<br /><br /> -                     **Elérési út** -a figyelni kívánt könyvtár abszolút elérési útja.<br /><br /> -                      **expandEnvironment** -konfigurálható, hogy az elérési út környezeti változókat levő figyelő kibontva látható.|  
|**LocalResource**|Az elérési út a figyelheti a helyi erőforrás viszonyítva. Szükséges attribútumok pedig a következők:<br /><br /> -                     **Név** -a helyi erőforrást, amely tartalmazza a figyelni kívánt könyvtár<br /><br /> -                     **relativePath** – a relatív nevét, amely tartalmazza a figyelni kívánt könyvtár elérési útja|  

## <a name="etwproviders-element"></a>EtwProviders elem  
 Konfigurálja az EventSource ETW-eseményeinek gyűjtése és/vagy a szolgáltatók ETW Manifest alapján. A következő táblázat a gyermekelemek:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Konfigurálja az előállított eseményeinek gyűjtése [EventSource osztály](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Kötelező attribútum:<br /><br /> **szolgáltató** -az EventSource esemény osztály neve.<br /><br /> Nem kötelező attribútumok pedig a következők:<br /><br /> -                     **scheduledTransferLogLevelFilter** – a minimális súlyossági szintet a tárfiók át.<br /><br /> -                     **scheduledTransferPeriod** -tároló ütemezett átvitelek között felfelé kerekítve a legközelebbi perc. Az érték egy [XML-időtartam adattípust](http://www.w3schools.com/schema/schema_dtypes_date.asp).|  
|**EtwManifestProviderConfiguration**|Kötelező attribútum:<br /><br /> **szolgáltató** -esemény szolgáltatójának a GUID-azonosító<br /><br /> Nem kötelező attribútumok pedig a következők:<br /><br /> - **scheduledTransferLogLevelFilter** – a minimális súlyossági szintet a tárfiók át.<br /><br /> -                     **scheduledTransferPeriod** -tároló ütemezett átvitelek között felfelé kerekítve a legközelebbi perc. Az érték egy [XML-időtartam adattípust](http://www.w3schools.com/schema/schema_dtypes_date.asp).|  

## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration elem  
 Konfigurálja az előállított eseményeinek gyűjtése [EventSource osztály](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). A következő táblázat a gyermekelemek:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**DefaultEvents**|Nem kötelező attribútum:<br /><br /> **eventDestination** -tárolja az eseményeket az a táblázat neve|  
|**Esemény**|Kötelező attribútum:<br /><br /> **azonosító** -esemény azonosítója.<br /><br /> Nem kötelező attribútum:<br /><br /> **eventDestination** -tárolja az eseményeket az a táblázat neve|  

## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration elem  
 A következő táblázat a gyermekelemek:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**DefaultEvents**|Nem kötelező attribútum:<br /><br /> **eventDestination** -tárolja az eseményeket az a táblázat neve|  
|**Esemény**|Kötelező attribútum:<br /><br /> **azonosító** -esemény azonosítója.<br /><br /> Nem kötelező attribútum:<br /><br /> **eventDestination** -tárolja az eseményeket az a táblázat neve|  

## <a name="metrics-element"></a>Metrikák elem  
 Lehetővé teszi a gyors lekérdezéseket optimalizált teljesítményt számláló tábla létrehozásához. A következő táblázat a gyermekelemek:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**MetricAggregation**|Kötelező attribútum:<br /><br /> **scheduledTransferPeriod** -tároló ütemezett átvitelek között felfelé kerekítve a legközelebbi perc. Az érték egy [XML-időtartam adattípust](http://www.w3schools.com/schema/schema_dtypes_date.asp).|  

## <a name="performancecounters-element"></a>PerformanceCounters elem  
 Lehetővé teszi, hogy a teljesítményszámlálók gyűjteményét. A következő táblázat a gyermekelemek:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**PerformanceCounterConfiguration**|A következő attribútumok szükségesek:<br /><br /> -                     **counterSpecifier** -teljesítményszámláló nevét. Például: `\Processor(_Total)\% Processor Time`. Teljesítmény listájának beolvasása a gazdagépen futó számlálók futtassa a parancsot `typeperf`.<br /><br /> -                     **sampleRate** -gyakoriságát. a számláló mintát venni.<br /><br /> Nem kötelező attribútum:<br /><br /> **egység** -számláló mértékegysége.|  

## <a name="performancecounterconfiguration-element"></a>PerformanceCounterConfiguration elem  
 A következő táblázat a gyermekelemek:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**Megjegyzés**|Kötelező attribútum:<br /><br /> **displayName** -számláló megjelenített neve<br /><br /> Nem kötelező attribútum:<br /><br /> **területi beállítás** -a területi beállítás jelenjen meg a számláló neve|  

## <a name="windowseventlog-element"></a>WindowsEventLog elem  
 A következő táblázat a gyermekelemek:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**Adatforrás**|A Windows eseménynaplóiban gyűjtéséhez. Kötelező attribútum:<br /><br /> **név** – a windows-eseményeket tudjon gyűjteni leíró XPath-lekérdezést. Példa:<br /><br /> `Application!*[System[(Level >= 3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level >= 3]]`<br /><br /> Minden eseményt gyűjt, adja meg a "*".|
