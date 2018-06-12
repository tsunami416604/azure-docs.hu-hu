---
title: Az Azure Diagnostics 1.0 konfigurációs séma
description: CSAK akkor érvényes, ha az Azure SDK 2.4 használ és az alacsonyabb Azure virtuális gépek, a virtuálisgép-méretezési csoportok, a Service Fabric vagy a Cloud Services.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/15/2017
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: 916e2123262402e23f35778e66683ecce2cec4b7
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262585"
---
# <a name="azure-diagnostics-10-configuration-schema"></a>Az Azure Diagnostics 1.0 konfigurációs séma
> [!NOTE]
> Az Azure Diagnostics teljesítményszámlálók és más statisztika gyűjthet az Azure virtuális gépek, a virtuálisgép-méretezési csoportok, a Service Fabric és a Cloud Services összetevő.  Ezen a lapon csak fontos, ha ilyen szolgáltatást használ.
>

Az Azure Diagnostics más Microsoft-diagnosztika termékekkel, például Azure figyelő, az Application Insights és Naplóelemzési szolgál.

Az Azure Diagnostics konfigurációs fájl határozza meg a diagnosztikai figyelő inicializálása használt értékek. Ez a fájl inicializálása a diagnosztikai beállításokat a diagnosztikai figyelő indításakor szolgál.  

 Alapértelmezés szerint a Azure Diagnostics-konfigurációs fájl van telepítve a `C:\Program Files\Microsoft SDKs\Azure\.NET SDK\<version>\schemas` könyvtár. Cserélje le `<version>` a telepített verzióját az [Azure SDK](http://www.windowsazure.com/develop/downloads/).  

> [!NOTE]
>  A diagnosztika konfigurációs fájl általában használt diagnosztikai adatok gyűjtésére, a rendszerindítási folyamat korábbi szakaszában igénylő indítási feladatokkal. Azure Diagnostics használatával kapcsolatos további információkért lásd: [naplózási adatok gyűjtése Azure Diagnostics használatával](assetId:///83a91c23-5ca2-4fc9-8df3-62036c37a3d7).  

## <a name="example-of-the-diagnostics-configuration-file"></a>A diagnosztika konfigurációs fájl példa  
 A következő példa bemutatja egy tipikus diagnosztika konfigurációs fájlt:  

```xml  
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"  
      configurationChangePollInterval="PT1M"  
      overallQuotaInMB="4096">  
   <DiagnosticInfrastructureLogs bufferQuotaInMB="1024"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M" />  
   <Logs bufferQuotaInMB="1024"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M" />  

   <Directories bufferQuotaInMB="1024"   
      scheduledTransferPeriod="PT1M">  

      <!-- These three elements specify the special directories   
           that are set up for the log types -->  
      <CrashDumps container="wad-crash-dumps" directoryQuotaInMB="256" />  
      <FailedRequestLogs container="wad-frq" directoryQuotaInMB="256" />  
      <IISLogs container="wad-iis" directoryQuotaInMB="256" />  

      <!-- For regular directories the DataSources element is used -->  
      <DataSources>  
         <DirectoryConfiguration container="wad-panther" directoryQuotaInMB="128">  
            <!-- Absolute specifies an absolute path with optional environment expansion -->  
            <Absolute expandEnvironment="true" path="%SystemRoot%\system32\sysprep\Panther" />  
         </DirectoryConfiguration>  
         <DirectoryConfiguration container="wad-custom" directoryQuotaInMB="128">  
            <!-- LocalResource specifies a path relative to a local   
                 resource defined in the service definition -->  
            <LocalResource name="MyLoggingLocalResource" relativePath="logs" />  
         </DirectoryConfiguration>  
      </DataSources>  
   </Directories>  

   <PerformanceCounters bufferQuotaInMB="512" scheduledTransferPeriod="PT1M">  
      <!-- The counter specifier is in the same format as the imperative   
           diagnostics configuration API -->  
      <PerformanceCounterConfiguration   
         counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT5S" />  
   </PerformanceCounters>  

   <WindowsEventLog bufferQuotaInMB="512"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M">  
      <!-- The event log name is in the same format as the imperative   
           diagnostics configuration API -->  
      <DataSource name="System!*" />  
   </WindowsEventLog>  
</DiagnosticMonitorConfiguration>  
```  

## <a name="diagnosticsconfiguration-namespace"></a>DiagnosticsConfiguration Namespace  
 A diagnosztika konfigurációs fájl XML-névtere van:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="schema-elements"></a>Séma elemei  
 A diagnosztika konfigurációs fájl az alábbi elemeket tartalmazza.


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration elem  
A legfelső szintű elem a diagnosztika konfigurációs fájl.  

Attribútumok:

|Attribútum  |Típus   |Szükséges| Alapértelmezett | Leírás|  
|-----------|-------|--------|---------|------------|  
|**configurationChangePollInterval**|időtartam|Optional | PT1M| Meghatározza a diagnosztikai konfigurációs módosítások időközét a diagnosztikai figyelő kérdezi le.|  
|**overallQuotaInMB**|unsignedInt|Optional| 4000 MB. Ha megad egy értéket, nem haladhatja meg ezt a mennyiséget |Az összes naplózási pufferek lefoglalt rendszer fájltároló teljes mennyisége.|  

## <a name="diagnosticinfrastructurelogs-element"></a>DiagnosticInfrastructureLogs elem  
A naplók az alapul szolgáló diagnosztika infrastruktúra által létrehozott puffer konfigurációjának meghatározása.

Szülőelem: [DiagnosticMonitorConfiguration elem](#DiagnosticMonitorConfiguration).  

Attribútumok:

|Attribútum|Típus|Leírás|  
|---------|----|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Választható. Meghatározza a maximális időt a rendszer fájltárolók érhetők el a megadott adatokat.<br /><br /> Az alapértelmezett érték 0.|  
|**scheduledTransferLogLevelFilter**|sztring|Választható. Meghatározza a naplóbejegyzéseket, amelyeket a minimális súlyossági szintet. Az alapértelmezett érték **meghatározatlan**. Más lehetséges értékei **részletes**, **információk**, **figyelmeztetés**, **hiba**, és **kritikus**.|  
|**scheduledTransferPeriod**|időtartam|Választható. Az időköz ütemezett átvitelek adatok felfelé kerekítve a legközelebbi perc között.<br /><br /> Az alapértelmezett érték PT0S.|  

## <a name="logs-element"></a>Naplók elem  
 Határozza meg a puffer konfigurációs az alapszintű Azure-naplók.

 Szülőelem: [DiagnosticMonitorConfiguration elem](#DiagnosticMonitorConfiguration).  

Attribútumok:  

|Attribútum|Típus|Leírás|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Választható. Meghatározza a maximális időt a rendszer fájltárolók érhetők el a megadott adatokat.<br /><br /> Az alapértelmezett érték 0.|  
|**scheduledTransferLogLevelFilter**|sztring|Választható. Meghatározza a naplóbejegyzéseket, amelyeket a minimális súlyossági szintet. Az alapértelmezett érték **meghatározatlan**. Más lehetséges értékei **részletes**, **információk**, **figyelmeztetés**, **hiba**, és **kritikus**.|  
|**scheduledTransferPeriod**|időtartam|Választható. Az időköz ütemezett átvitelek adatok felfelé kerekítve a legközelebbi perc között.<br /><br /> Az alapértelmezett érték PT0S.|  

## <a name="directories-element"></a>Könyvtárak elem  
Határozza meg a fájl alapú naplófájlokat, amelyek alapján meghatározhatja a puffer konfigurációját.

Szülőelem: [DiagnosticMonitorConfiguration elem](#DiagnosticMonitorConfiguration).  


Attribútumok:  

|Attribútum|Típus|Leírás|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Választható. Meghatározza a maximális időt a rendszer fájltárolók érhetők el a megadott adatokat.<br /><br /> Az alapértelmezett érték 0.|  
|**scheduledTransferPeriod**|időtartam|Választható. Az időköz ütemezett átvitelek adatok felfelé kerekítve a legközelebbi perc között.<br /><br /> Az alapértelmezett érték PT0S.|  

## <a name="crashdumps-element"></a>CrashDumps elem  
 Meghatározza a összeomlási memóriaképek könyvtár.

 Szülőelem: [könyvtárak elem](#Directories).  

Attribútumok:  

|Attribútum|Típus|Leírás|  
|---------------|----------|-----------------|  
|**Tároló**|sztring|A könyvtár tartalmát esetén át kell helyezni a tároló nevét.|  
|**directoryQuotaInMB**|unsignedInt|Választható. A könyvtár maximális méretét megabájtban megadva.<br /><br /> Az alapértelmezett érték 0.|  

## <a name="failedrequestlogs-element"></a>FailedRequestLogs elem  
 Határozza meg a sikertelen kérelmek naplókönyvtár.

 Szülő elem [könyvtárak elem](#Directories).  

Attribútumok:  

|Attribútum|Típus|Leírás|  
|---------------|----------|-----------------|  
|**Tároló**|sztring|A könyvtár tartalmát esetén át kell helyezni a tároló nevét.|  
|**directoryQuotaInMB**|unsignedInt|Választható. A könyvtár maximális méretét megabájtban megadva.<br /><br /> Az alapértelmezett érték 0.|  

##  <a name="iislogs-element"></a>IISLogs elem  
 Meghatározza az IIS-napló könyvtár.

 Szülő elem [könyvtárak elem](#Directories).  

Attribútumok:  

|Attribútum|Típus|Leírás|  
|---------------|----------|-----------------|  
|**Tároló**|sztring|A könyvtár tartalmát esetén át kell helyezni a tároló nevét.|  
|**directoryQuotaInMB**|unsignedInt|Választható. A könyvtár maximális méretét megabájtban megadva.<br /><br /> Az alapértelmezett érték 0.|  

## <a name="datasources-element"></a>Adatforrások elem  
 Határozza meg a nulla vagy több további naplók könyvtárak.

 Szülőelem: [könyvtárak elem](#Directories).

## <a name="directoryconfiguration-element"></a>DirectoryConfiguration elem  
 Határozza meg a könyvtár a naplófájlok figyeléséhez.

 Szülőelem: [adatforrások elem](#DataSources).

Attribútumok:

|Attribútum|Típus|Leírás|  
|---------------|----------|-----------------|  
|**Tároló**|sztring|A könyvtár tartalmát esetén át kell helyezni a tároló nevét.|  
|**directoryQuotaInMB**|unsignedInt|Választható. A könyvtár maximális méretét megabájtban megadva.<br /><br /> Az alapértelmezett érték 0.|  

## <a name="absolute-element"></a>Abszolút elem  
 Meghatározza a nem kötelező környezeti bővítéssel rendelkező figyelni kívánt könyvtár abszolút elérési utat.

 Szülőelem: [DirectoryConfiguration elem](#DirectoryConfiguration).  

Attribútumok:  

|Attribútum|Típus|Leírás|  
|---------------|----------|-----------------|  
|**path**|sztring|Kötelező. A figyelni kívánt könyvtár abszolút elérési útja|  
|**expandEnvironment**|logikai|Kötelező. Ha beállítása **igaz**, az elérési út környezeti változókat levő figyelő kibontva látható.|  

## <a name="localresource-element"></a>LocalResource elem  
 Határozza meg egy elérési útját a helyi erőforrás szolgáltatásdefinícióban megadott viszonyítva.

 Szülőelem: [DirectoryConfiguration elem](#DirectoryConfiguration).  

Attribútumok:  

|Attribútum|Típus|Leírás|  
|---------------|----------|-----------------|  
|**name**|sztring|Kötelező. A helyi erőforrást, amely tartalmazza a figyelni kívánt könyvtár neve.|  
|**RelativePath**|sztring|Kötelező. Az elérési út a figyelheti a helyi erőforrás viszonyítva.|  

## <a name="performancecounters-element"></a>PerformanceCounters elem  
 Meghatározza azt a teljesítményszámláló gyűjthet a.

 Szülőelem: [DiagnosticMonitorConfiguration elem](#DiagnosticMonitorConfiguration).


 Attribútumok:  

|Attribútum|Típus|Leírás|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Választható. Meghatározza a maximális időt a rendszer fájltárolók érhetők el a megadott adatokat.<br /><br /> Az alapértelmezett érték 0.|  
|**scheduledTransferPeriod**|időtartam|Választható. Az időköz ütemezett átvitelek adatok felfelé kerekítve a legközelebbi perc között.<br /><br /> Az alapértelmezett érték PT0S.|  

## <a name="performancecounterconfiguration-element"></a>PerformanceCounterConfiguration elem  
 Határozza meg a teljesítményszámláló gyűjtéséhez.

 Szülőelem: [PerformanceCounters elem](#PerformanceCounters).  

 Attribútumok:  

|Attribútum|Típus|Leírás|  
|---------------|----------|-----------------|  
|**counterSpecifier**|sztring|Kötelező. A teljesítményszámláló gyűjtéséhez elérési útja.|  
|**sampleRate**|időtartam|Kötelező. Az a sebesség, amellyel a teljesítményszámláló kell gyűjteni.|  

## <a name="windowseventlog-element"></a>WindowsEventLog elem  
 Határozza meg az eseménynaplókat annak figyelésére.

 Szülőelem: [DiagnosticMonitorConfiguration elem](#DiagnosticMonitorConfiguration).

  Attribútumok:

|Attribútum|Típus|Leírás|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Választható. Meghatározza a maximális időt a rendszer fájltárolók érhetők el a megadott adatokat.<br /><br /> Az alapértelmezett érték 0.|  
|**scheduledTransferLogLevelFilter**|sztring|Választható. Meghatározza a naplóbejegyzéseket, amelyeket a minimális súlyossági szintet. Az alapértelmezett érték **meghatározatlan**. Más lehetséges értékei **részletes**, **információk**, **figyelmeztetés**, **hiba**, és **kritikus**.|  
|**scheduledTransferPeriod**|időtartam|Választható. Az időköz ütemezett átvitelek adatok felfelé kerekítve a legközelebbi perc között.<br /><br /> Az alapértelmezett érték PT0S.|  

## <a name="datasource-element"></a>DataSource elem  
 Határozza meg az Eseménynapló figyelése.

 Szülőelem: [WindowsEventLog elem](#windowsEventLog).  

 Attribútumok:

|Attribútum|Típus|Leírás|  
|---------------|----------|-----------------|  
|**name**|sztring|Kötelező. Adja meg a napló gyűjtése XPath kifejezés.|  
