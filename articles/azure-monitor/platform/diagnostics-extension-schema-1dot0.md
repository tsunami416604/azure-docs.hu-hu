---
title: Azure Diagnostics 1.0-konfigurációs séma
description: CSAK akkor érvényes, ha használja az Azure SDK 2.4 és alatt az Azure Virtual Machines, Virtual Machine Scale Sets, Service Fabric és Cloud Services.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/15/2017
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: 333fec5b9459979d7735f0dd292d7367d86353fe
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53326158"
---
# <a name="azure-diagnostics-10-configuration-schema"></a>Azure Diagnostics 1.0-konfigurációs séma
> [!NOTE]
> Az Azure Diagnostics az a komponens, teljesítményszámlálók és más statisztikák gyűjtését az Azure Virtual Machines, Virtual Machine Scale Sets, Service Fabric és Cloud Services segítségével.  Ez a lap csak akkor jelentősége, ha ezek a szolgáltatások valamelyikét használja.
>

Egyéb Microsoft-diagnosztika termékek, mint az Azure Monitor az Application Insights és a Log Analytics az Azure Diagnostics használnak.

Az Azure Diagnostics-konfigurációs fájl határozza meg a diagnosztikai figyelő inicializálása használt értékeket. Ez a fájl inicializálása a diagnosztikai beállításokat a diagnosztikai figyelő indításakor szolgál.  

 Alapértelmezés szerint a fájl az Azure Diagnostics konfigurációs van telepítve a `C:\Program Files\Microsoft SDKs\Azure\.NET SDK\<version>\schemas` könyvtár. Cserélje le `<version>` telepített verziójának a [Azure SDK](http://www.windowsazure.com/develop/downloads/).  

> [!NOTE]
>  A diagnosztika konfigurációs fájl jellemzően a diagnosztikai adatok gyűjtésére, a rendszerindítási folyamat korábbi szakaszában igénylő indítási feladatok. Azure Diagnostics használatával kapcsolatos további információkért lásd: [naplózási adatok gyűjtése az Azure Diagnostics használatával](assetId:///83a91c23-5ca2-4fc9-8df3-62036c37a3d7).  

## <a name="example-of-the-diagnostics-configuration-file"></a>A diagnosztika konfigurációs fájl példa  
 Az alábbi példa bemutatja egy tipikus diagnosztikai konfigurációs fájlban:  

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
 A diagnosztika konfigurációs fájl XML-névtér van:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="schema-elements"></a>Séma elemei  
 A diagnosztika konfigurációs fájlt az alábbi elemeket tartalmazza.


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration elem  
A legfelső szintű elem a diagnosztikai konfigurációs fájl.  

Attribútumok:

|Attribútum  |Típus   |Szükséges| Alapértelmezett | Leírás|  
|-----------|-------|--------|---------|------------|  
|**configurationChangePollInterval**|időtartam|Optional | PT1M| Diagnosztikai konfigurációs módosítások, amelyen a diagnosztikai figyelő kérdezze le az időközt adja meg.|  
|**overallQuotaInMB**|unsignedInt|Optional| 4000 MB. Ha megad egy értéket, nem haladhatja meg ezt a mennyiséget |Összes naplózási puffer számára lefoglalt fájlrendszer-tárhely teljes mennyisége.|  

## <a name="diagnosticinfrastructurelogs-element"></a>DiagnosticInfrastructureLogs elem  
Az alapul szolgáló diagnosztikai infrastruktúra által előállított naplók puffer konfigurációját.

Elem: [DiagnosticMonitorConfiguration elem](#DiagnosticMonitorConfiguration).  

Attribútumok:

|Attribútum|Típus|Leírás|  
|---------|----|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Választható. Meghatározza a maximális fájlrendszer-tárhely, amelyet a megadott adatokat.<br /><br /> Az alapértelmezett érték a 0.|  
|**scheduledTransferLogLevelFilter**|sztring|Választható. Meghatározza a naplóbejegyzéseket, amelyeket a minimális súlyossági szintet. Az alapértelmezett érték **Undefined**. Más lehetséges értékek a következők **részletes**, **információk**, **figyelmeztetés**, **hiba**, és **kritikus**.|  
|**scheduledTransferPeriod**|időtartam|Választható. Ütemezett átvitel az adatokat, a legközelebbi egész percre kerekítve közötti időköz.<br /><br /> Az alapértelmezett érték PT0S.|  

## <a name="logs-element"></a>Naplók elem  
 Az alapszintű Azure-naplók puffer konfigurációját.

 Elem: [DiagnosticMonitorConfiguration elem](#DiagnosticMonitorConfiguration).  

Attribútumok:  

|Attribútum|Típus|Leírás|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Választható. Meghatározza a maximális fájlrendszer-tárhely, amelyet a megadott adatokat.<br /><br /> Az alapértelmezett érték a 0.|  
|**scheduledTransferLogLevelFilter**|sztring|Választható. Meghatározza a naplóbejegyzéseket, amelyeket a minimális súlyossági szintet. Az alapértelmezett érték **Undefined**. Más lehetséges értékek a következők **részletes**, **információk**, **figyelmeztetés**, **hiba**, és **kritikus**.|  
|**scheduledTransferPeriod**|időtartam|Választható. Ütemezett átvitel az adatokat, a legközelebbi egész percre kerekítve közötti időköz.<br /><br /> Az alapértelmezett érték PT0S.|  

## <a name="directories-element"></a>Könyvtárak elem  
A puffer konfigurációs fájl alapú naplókhoz definiálható határozza meg.

Elem: [DiagnosticMonitorConfiguration elem](#DiagnosticMonitorConfiguration).  


Attribútumok:  

|Attribútum|Típus|Leírás|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Választható. Meghatározza a maximális fájlrendszer-tárhely, amelyet a megadott adatokat.<br /><br /> Az alapértelmezett érték a 0.|  
|**scheduledTransferPeriod**|időtartam|Választható. Ütemezett átvitel az adatokat, a legközelebbi egész percre kerekítve közötti időköz.<br /><br /> Az alapértelmezett érték PT0S.|  

## <a name="crashdumps-element"></a>CrashDumps elem  
 Az összeomlási memóriaképek könyvtár határozza meg.

 Elem: [Könyvtárak elem](#Directories).  

Attribútumok:  

|Attribútum|Típus|Leírás|  
|---------------|----------|-----------------|  
|**Tároló**|sztring|A könyvtár tartalmának esetén át lehet adni a tároló neve.|  
|**directoryQuotaInMB**|unsignedInt|Választható. Adja meg a könyvtár maximális mérete (MB).<br /><br /> Az alapértelmezett érték a 0.|  

## <a name="failedrequestlogs-element"></a>FailedRequestLogs elem  
 A sikertelen kérelmek naplókönyvtár határozza meg.

 A szülő elem [könyvtárak elem](#Directories).  

Attribútumok:  

|Attribútum|Típus|Leírás|  
|---------------|----------|-----------------|  
|**Tároló**|sztring|A könyvtár tartalmának esetén át lehet adni a tároló neve.|  
|**directoryQuotaInMB**|unsignedInt|Választható. Adja meg a könyvtár maximális mérete (MB).<br /><br /> Az alapértelmezett érték a 0.|  

##  <a name="iislogs-element"></a>IISLogs elem  
 Meghatározza az IIS-napló könyvtár.

 A szülő elem [könyvtárak elem](#Directories).  

Attribútumok:  

|Attribútum|Típus|Leírás|  
|---------------|----------|-----------------|  
|**Tároló**|sztring|A könyvtár tartalmának esetén át lehet adni a tároló neve.|  
|**directoryQuotaInMB**|unsignedInt|Választható. Adja meg a könyvtár maximális mérete (MB).<br /><br /> Az alapértelmezett érték a 0.|  

## <a name="datasources-element"></a>Adatforrások elem  
 Határozza meg a nulla vagy több további naplókönyvtárat.

 Elem: [Könyvtárak elem](#Directories).

## <a name="directoryconfiguration-element"></a>DirectoryConfiguration elem  
 Határozza meg a könyvtárat a naplófájlok figyeléséhez.

 Elem: [Adatforrások elem](#DataSources).

Attribútumok:

|Attribútum|Típus|Leírás|  
|---------------|----------|-----------------|  
|**Tároló**|sztring|A könyvtár tartalmának esetén át lehet adni a tároló neve.|  
|**directoryQuotaInMB**|unsignedInt|Választható. Adja meg a könyvtár maximális mérete (MB).<br /><br /> Az alapértelmezett érték a 0.|  

## <a name="absolute-element"></a>Abszolút elem  
 A könyvtár nem kötelező környezeti-bővítése figyelő abszolút elérési utat határozza meg.

 Elem: [DirectoryConfiguration elem](#DirectoryConfiguration).  

Attribútumok:  

|Attribútum|Típus|Leírás|  
|---------------|----------|-----------------|  
|**path**|sztring|Kötelező. Figyelni kívánt könyvtár abszolút elérési útja.|  
|**expandEnvironment**|logikai|Kötelező. Ha beállítása **igaz**, az elérési út környezeti változók vannak bontva.|  

## <a name="localresource-element"></a>LocalResource elem  
 Meghatározza egy viszonyítva a szolgáltatás-definícióban meghatározott helyi erőforrás elérési útja.

 Elem: [DirectoryConfiguration elem](#DirectoryConfiguration).  

Attribútumok:  

|Attribútum|Típus|Leírás|  
|---------------|----------|-----------------|  
|**name**|sztring|Kötelező. A helyi erőforrás, amely tartalmazza a figyelni kívánt könyvtár neve.|  
|**RelativePath**|sztring|Kötelező. Az elérési útját a helyi erőforrás monitorozásához viszonyítva.|  

## <a name="performancecounters-element"></a>PerformanceCounters elem  
 Határozza meg az elérési út való gyűjtésére a teljesítményszámláló.

 Elem: [DiagnosticMonitorConfiguration elem](#DiagnosticMonitorConfiguration).


 Attribútumok:  

|Attribútum|Típus|Leírás|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Választható. Meghatározza a maximális fájlrendszer-tárhely, amelyet a megadott adatokat.<br /><br /> Az alapértelmezett érték a 0.|  
|**scheduledTransferPeriod**|időtartam|Választható. Ütemezett átvitel az adatokat, a legközelebbi egész percre kerekítve közötti időköz.<br /><br /> Az alapértelmezett érték PT0S.|  

## <a name="performancecounterconfiguration-element"></a>PerformanceCounterConfiguration elem  
 Határozza meg a teljesítményszámláló gyűjtéséhez.

 Elem: [PerformanceCounters elem](#PerformanceCounters).  

 Attribútumok:  

|Attribútum|Típus|Leírás|  
|---------------|----------|-----------------|  
|**counterSpecifier**|sztring|Kötelező. A teljesítményszámláló gyűjtése az elérési útja.|  
|**sampleRate**|időtartam|Kötelező. Az a sebesség, amellyel a teljesítményszámláló kell gyűjteni.|  

## <a name="windowseventlog-element"></a>WindowsEventLog elem  
 Határozza meg az Eseménynapló bejegyzéseit, amelyek figyelése.

 Elem: [DiagnosticMonitorConfiguration elem](#DiagnosticMonitorConfiguration).

  Attribútumok:

|Attribútum|Típus|Leírás|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Választható. Meghatározza a maximális fájlrendszer-tárhely, amelyet a megadott adatokat.<br /><br /> Az alapértelmezett érték a 0.|  
|**scheduledTransferLogLevelFilter**|sztring|Választható. Meghatározza a naplóbejegyzéseket, amelyeket a minimális súlyossági szintet. Az alapértelmezett érték **Undefined**. Más lehetséges értékek a következők **részletes**, **információk**, **figyelmeztetés**, **hiba**, és **kritikus**.|  
|**scheduledTransferPeriod**|időtartam|Választható. Ütemezett átvitel az adatokat, a legközelebbi egész percre kerekítve közötti időköz.<br /><br /> Az alapértelmezett érték PT0S.|  

## <a name="datasource-element"></a>DataSource elem  
 Határozza meg az Eseménynapló figyelése.

 Elem: [WindowsEventLog elem](#windowsEventLog).  

 Attribútumok:

|Attribútum|Típus|Leírás|  
|---------------|----------|-----------------|  
|**name**|sztring|Kötelező. Adja meg a napló gyűjtése XPath kifejezés.|  
