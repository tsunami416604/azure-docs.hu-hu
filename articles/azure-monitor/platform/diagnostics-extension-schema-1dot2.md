---
title: Az Azure Diagnostics bővítmény 1.2-es konfigurációs séma
description: CSAK akkor érvényes, ha az Azure Virtual Machines, Virtual Machine Scale Sets, Service Fabric és Cloud Services az Azure SDK 2.5-ös használja.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/15/2017
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: 322cd75fe9198bae459e7c22bed794f583d13363
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53326199"
---
# <a name="azure-diagnostics-12-configuration-schema"></a>Az Azure Diagnostics 1.2-es konfigurációs séma
> [!NOTE]
> Az Azure Diagnostics az a komponens, teljesítményszámlálók és más statisztikák gyűjtését az Azure Virtual Machines, Virtual Machine Scale Sets, Service Fabric és Cloud Services segítségével.  Ez a lap csak akkor jelentősége, ha ezek a szolgáltatások valamelyikét használja.
>

Egyéb Microsoft-diagnosztika termékek, mint az Azure Monitor az Application Insights és a Log Analytics az Azure Diagnostics használnak.

Ebben a sémában a lehetséges értékek inicializálása a diagnosztikai beállításokat a diagnosztikai figyelő indításakor segítségével határozza meg.  


 Töltse le a nyilvános bővítménykonfiguráció sémája fájldefiníciót a következő PowerShell-parancs végrehajtásával:  

```PowerShell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

 Azure Diagnostics használatával kapcsolatos további információkért lásd: [Diagnosztikának az Azure Cloud Servicesben](https://azure.microsoft.com/documentation/articles/cloud-services-dotnet-diagnostics/).  

## <a name="example-of-the-diagnostics-configuration-file"></a>A diagnosztika konfigurációs fájl példa  
 Az alábbi példa bemutatja egy tipikus diagnosztikai konfigurációs fájlban:  

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
 A diagnosztika konfigurációs fájl XML-névtér van:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="publicconfig-element"></a>PublicConfig elem  
 Legfelső szintű elem a diagnosztikai konfigurációs fájl. A következő táblázat ismerteti az elemek a konfigurációs fájl.  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**WadCfg**|Kötelező. Konfigurációs beállításait a telemetriai adatokat gyűjteni.|  
|**StorageAccount**|Az adatok tárolásához Azure Storage-fiók neve. Ez is megadható paramétert a Set-AzureServiceDiagnosticsExtension parancsmag végrehajtása közben.|  
|**LocalResourceDirectory**|A könyvtár a virtuális gép által a Monitoring Agent eseményadatok tárolására használható. Ha nem, az alapértelmezett címtár használatos:<br /><br /> Feldolgozói/webes szerepkör: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Egy virtuális géphez: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Szükséges attribútumok a következők:<br /><br /> -                      **elérési út** – a rendszer az Azure Diagnostics által használandó könyvtárába.<br /><br /> -                      **expandEnvironment** – azt szabályozza, hogy környezeti változókat az elérési útban bontva.|  

## <a name="wadcfg-element"></a>WadCFG elem  
A telemetriai adatokat gyűjteni konfigurációs beállításait határozza meg. A következő táblázat ismerteti a gyermekelemek:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**DiagnosticMonitorConfiguration**|Kötelező. Nem kötelező attribútumok a következők:<br /><br /> -                     **overallQuotaInMB** – Azure Diagnostics által gyűjtött lehet használni a diagnosztikai adatok különböző típusú helyi lemezterület maximális mennyisége. Az alapértelmezett érték 5120MB.<br /><br /> -                     **useProxyServer** – a proxykiszolgáló beállításait használatára beállított, az Internet Explorer beállításainak konfigurálása az Azure Diagnostics.|  
|**CrashDumps**|Összeomlási memóriaképek gyűjtésének engedélyezéséhez. Nem kötelező attribútumok a következők:<br /><br /> -                     **containerName** – az összeomlási memóriaképek tárolásához használt Azure Storage-fiókját a blobtároló nevét.<br /><br /> -                     **crashDumpType** – Azure Diagnostics konfigurálja a gyűjtendő Mini vagy teljes összeomlási memóriaképek.<br /><br /> -                     **directoryQuotaPercentage**– konfigurálja a százalékos arányát **overallQuotaInMB** kell lefoglalni, az összeomlási memóriaképek a virtuális gépen.|  
|**DiagnosticInfrastructureLogs**|Azure Diagnostics által létrehozott naplók gyűjtésének engedélyezéséhez. A diagnosztikai infrastruktúra naplói hasznosak hibáinak elhárítása a diagnosztikai rendszer magát. Nem kötelező attribútumok a következők:<br /><br /> -                     **scheduledTransferLogLevelFilter** – konfigurálja a gyűjtött naplók minimális súlyossági szintje.<br /><br /> -                     **scheduledTransferPeriod** – Storage ütemezett átvitel közötti időtartam kerekítve a legközelebbi egész percre. Az érték egy [XML "Időtartam adattípus."](http://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**Címtárak**|Lehetővé teszi a tartalmát egy könyvtárat, az IIS nem sikerült hozzáférést kérelmekről készült naplók és/vagy IIS-naplók gyűjtését. Nem kötelező attribútum:<br /><br /> **scheduledTransferPeriod** – Storage ütemezett átvitel közötti időtartam kerekítve a legközelebbi egész percre. Az érték egy [XML "Időtartam adattípus."](http://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**EtwProviders**|Konfigurálja az ETW-események az eseményforrás gyűjteménye, illetve az ETW-jegyzékfájl-alapú szolgáltatók.|  
|**Metrikák**|Ez az elem lehetővé teszi, hogy hozzon létre egy számláló teljesítménytáblája, amely gyors lekérdezéseket van optimalizálva. Minden egyes megadott teljesítményszámláló a **PerformanceCounters** elem a metrikák tábla mellett a teljesítményszámláló tábla tárolja. Kötelező attribútum:<br /><br /> **erőforrás-azonosító** – Ez az erőforrás-Azonosítóját a virtuális gép az Azure Diagnostics telepítésekor. Első a **resourceID** származó a [az Azure portal](https://portal.azure.com). Válassza ki **Tallózás** -> **erőforráscsoportok** -> **< név\>**. Kattintson a **tulajdonságok** csempére, és másolja az értéket a **azonosító** mező.|  
|**PerformanceCounters**|Lehetővé teszi a teljesítményszámláló-gyűjtemény. Nem kötelező attribútum:<br /><br /> **scheduledTransferPeriod** – Storage ütemezett átvitel közötti időtartam kerekítve a legközelebbi egész percre. Az érték egy [XML "Időtartam adattípus".](http://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**WindowsEventLog**|Lehetővé teszi, hogy a gyűjtemény Windows eseménynaplók. Nem kötelező attribútum:<br /><br /> **scheduledTransferPeriod** – Storage ütemezett átvitel közötti időtartam kerekítve a legközelebbi egész percre. Az érték egy [XML "Időtartam adattípus".](http://www.w3schools.com/xml/schema_dtypes_date.asp)|  

## <a name="crashdumps-element"></a>CrashDumps elem  
 Lehetővé teszi az összeomlási memóriaképek gyűjteménye. A következő táblázat ismerteti a gyermekelemek:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**CrashDumpConfiguration**|Kötelező. Kötelező attribútum:<br /><br /> **processName** – a nevét, a folyamat egy összeomlási memóriakép a gyűjtendő Azure Diagnostics szeretne.|  
|**crashDumpType**|Konfigurálja az Azure Diagnostics mini vagy teljes összeomlás memóriaképek összegyűjtése.|  
|**directoryQuotaPercentage**|Konfigurálja a százalékos arányát **overallQuotaInMB** kell lefoglalni, az összeomlási memóriaképek a virtuális gépen.|  

## <a name="directories-element"></a>Könyvtárak elem  
 Lehetővé teszi a tartalmát egy könyvtárat, az IIS nem sikerült hozzáférést kérelmekről készült naplók és/vagy IIS-naplók gyűjtését. A következő táblázat ismerteti a gyermekelemek:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**Adatforrások**|Figyelendő könyvtárainak listáját.|  
|**FailedRequestLogs**|Például ez az elem a konfigurációban lehetővé teszi, hogy a sikertelen kérelmek egy IIS-webhely vagy alkalmazás naplókat gyűjteménye. Is engedélyeznie kell a nyomkövetést **rendszer. Webkiszolgáló** a **Web.config**.|  
|**IISLogs**|Például ez az elem a konfigurációban lehetővé teszi az IIS-naplók gyűjtésére:<br /><br /> **containerName** -az IIS-naplók tárolására szolgáló Azure Storage-fiókját a blobtároló nevét.|  

## <a name="datasources-element"></a>Adatforrások elem  
 Figyelendő könyvtárainak listáját. A következő táblázat ismerteti a gyermekelemek:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**DirectoryConfiguration**|Kötelező. Kötelező attribútum:<br /><br /> **containerName** – az Azure Storage-fiókot a naplófájlok tárolásához használandó blobtároló neve.|  

## <a name="directoryconfiguration-element"></a>DirectoryConfiguration elem  
 **DirectoryConfiguration** tartalmazhat vagy a **abszolút** vagy **LocalResource** elemmel, de nem mindkettőt. A következő táblázat ismerteti a gyermekelemek:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**Abszolút**|Figyelni kívánt könyvtár abszolút elérési útja. A következő attribútumok szükség:<br /><br /> -                     **Elérési út** -a figyelni kívánt könyvtár abszolút elérési útját.<br /><br /> -                      **expandEnvironment** -segítségével beállítható, hogy az elérési út környezeti változók vannak bontva.|  
|**LocalResource**|Az elérési út képest helyi erőforrás monitorozásához. Szükséges attribútumok a következők:<br /><br /> -                     **Név** – a helyi erőforrás, amely tartalmazza a figyelni kívánt könyvtár<br /><br /> -                     **relativePath** -képest relatív nevét, amely tartalmazza a figyelni kívánt könyvtár elérési útja|  

## <a name="etwproviders-element"></a>EtwProviders elem  
 Konfigurálja az ETW-események az eseményforrás gyűjteménye, illetve az ETW-jegyzékfájl-alapú szolgáltatók. A következő táblázat ismerteti a gyermekelemek:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Konfigurálja a generált események gyűjtését [EventSource osztály](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Kötelező attribútum:<br /><br /> **szolgáltató** -az osztály nevét az EventSource esemény.<br /><br /> Nem kötelező attribútumok a következők:<br /><br /> -                     **scheduledTransferLogLevelFilter** – minimális súlyossági szintje a tárfiókhoz való átviteléhez.<br /><br /> -                     **scheduledTransferPeriod** – Storage ütemezett átvitel közötti időtartam kerekítve a legközelebbi egész percre. Az érték egy [XML-adattípus időtartama](http://www.w3schools.com/xml/schema_dtypes_date.asp).|  
|**EtwManifestProviderConfiguration**|Kötelező attribútum:<br /><br /> **szolgáltató** -esemény szolgáltatójának a GUID-azonosító<br /><br /> Nem kötelező attribútumok a következők:<br /><br /> - **scheduledTransferLogLevelFilter** – minimális súlyossági szintje a tárfiókhoz való átviteléhez.<br /><br /> -                     **scheduledTransferPeriod** – Storage ütemezett átvitel közötti időtartam kerekítve a legközelebbi egész percre. Az érték egy [XML-adattípus időtartama](http://www.w3schools.com/xml/schema_dtypes_date.asp).|  

## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration Element  
 Konfigurálja a generált események gyűjtését [EventSource osztály](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). A következő táblázat ismerteti a gyermekelemek:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**DefaultEvents**|Nem kötelező attribútum:<br /><br /> **eventDestination** -tárolja az eseményeket az a tábla neve|  
|**Esemény**|Kötelező attribútum:<br /><br /> **ID** -esemény azonosítója.<br /><br /> Nem kötelező attribútum:<br /><br /> **eventDestination** -tárolja az eseményeket az a tábla neve|  

## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration Element  
 A következő táblázat ismerteti a gyermekelemek:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**DefaultEvents**|Nem kötelező attribútum:<br /><br /> **eventDestination** -tárolja az eseményeket az a tábla neve|  
|**Esemény**|Kötelező attribútum:<br /><br /> **ID** -esemény azonosítója.<br /><br /> Nem kötelező attribútum:<br /><br /> **eventDestination** -tárolja az eseményeket az a tábla neve|  

## <a name="metrics-element"></a>Metrikák elem  
 Lehetővé teszi, hogy hozzon létre egy számláló teljesítménytáblája, amely gyors lekérdezéseket van optimalizálva. A következő táblázat ismerteti a gyermekelemek:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**MetricAggregation**|Kötelező attribútum:<br /><br /> **scheduledTransferPeriod** – Storage ütemezett átvitel közötti időtartam kerekítve a legközelebbi egész percre. Az érték egy [XML-adattípus időtartama](http://www.w3schools.com/xml/schema_dtypes_date.asp).|  

## <a name="performancecounters-element"></a>PerformanceCounters elem  
 Lehetővé teszi a teljesítményszámláló-gyűjtemény. A következő táblázat ismerteti a gyermekelemek:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**PerformanceCounterConfiguration**|A következő attribútumok szükség:<br /><br /> -                     **counterSpecifier** – a teljesítményszámláló neve. Például: `\Processor(_Total)\% Processor Time`. Teljesítmény listája számlálókat a gazdagépen futó futtassa a parancsot `typeperf`.<br /><br /> -                     **sampleRate** -gyakoriságát. a számláló kell mintát venni.<br /><br /> Nem kötelező attribútum:<br /><br /> **egység** – a számláló mértékegysége.|  

## <a name="performancecounterconfiguration-element"></a>PerformanceCounterConfiguration elem  
 A következő táblázat ismerteti a gyermekelemek:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**annotation**|Kötelező attribútum:<br /><br /> **displayName** – Ez a számláló megjelenített neve<br /><br /> Nem kötelező attribútum:<br /><br /> **területi beállítás** – a területi beállítása során használatos évformátum meghatározása a számláló neve|  

## <a name="windowseventlog-element"></a>WindowsEventLog elem  
 A következő táblázat ismerteti a gyermekelemek:  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**Adatforrás**|A Windows Eseménynapló bejegyzéseit, amelyek gyűjtése. Kötelező attribútum:<br /><br /> **név** – az XPath-lekérdezés, amely leírja a windows-eseményeket tudjon gyűjteni. Példa:<br /><br /> `Application!*[System[(Level >= 3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level >= 3]]`<br /><br /> Összes eseményének gyűjtéséhez, adja meg a "*".|
