---
title: Azure Diagnostics 1,0 konfigurációs séma
description: CSAK akkor fontos, ha az Azure SDK 2,4-es és alacsonyabb verzióit használja az Azure Virtual Machines, Virtual Machine Scale Sets, Service Fabric vagy Cloud Services.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/15/2017
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: ac2b79d670b803573a359dfc9f8738f972f2d9b5
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "60237860"
---
# <a name="azure-diagnostics-10-configuration-schema"></a>Azure Diagnostics 1,0 konfigurációs séma
> [!NOTE]
> Azure Diagnostics a teljesítményszámlálók és az Azure Virtual Machines, a Virtual Machine Scale Sets, a Service Fabric és a Cloud Services statisztikáinak összegyűjtésére szolgáló összetevő.  Ez a lap csak akkor fontos, ha az egyik szolgáltatást használja.
>

A Azure Diagnostics más Microsoft diagnosztikai termékekhez, például a Azure Monitorekhez használható, beleértve a Application Insights és a Log Analytics.

A Azure Diagnostics konfigurációs fájl a diagnosztikai figyelő inicializálásához használt értékeket határozza meg. Ezzel a fájllal lehet inicializálni a diagnosztikai konfigurációs beállításokat a diagnosztika figyelője indításakor.  

 Alapértelmezés szerint a rendszer a Azure Diagnostics konfigurációs sémafájl fájlját telepíti a `C:\Program Files\Microsoft SDKs\Azure\.NET SDK\<version>\schemas` könyvtárba. Cserélje `<version>` le az helyére az [Azure SDK](https://www.windowsazure.com/develop/downloads/)telepített verzióját.  

> [!NOTE]
>  A diagnosztikai konfigurációs fájlt általában olyan indítási feladatokhoz használják, amelyek az indítási folyamat során korábban gyűjtött diagnosztikai adatokat igénylik. További információ a Azure Diagnostics használatáról: a [naplózási adatok összegyűjtése Azure Diagnostics használatával](assetId:///83a91c23-5ca2-4fc9-8df3-62036c37a3d7).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Példa a diagnosztika konfigurációs fájljára  
 Az alábbi példa egy tipikus diagnosztikai konfigurációs fájlt mutat be:  

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

## <a name="diagnosticsconfiguration-namespace"></a>DiagnosticsConfiguration-névtér  
 A diagnosztika konfigurációs fájljának XML-névtere a következő:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="schema-elements"></a>Séma elemei  
 A diagnosztikai konfigurációs fájl a következő elemeket tartalmazza.


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration elem  
A diagnosztikai konfigurációs fájl legfelső szintű eleme.  

Attribútumok:

|Attribútum  |Type   |Kötelező| Alapértelmezett | Leírás|  
|-----------|-------|--------|---------|------------|  
|**configurationChangePollInterval**|duration|Választható | PT1M| Meghatározza azt az időközt, amelyen a diagnosztikai figyelő lekérdezi a diagnosztikai konfiguráció változásait.|  
|**overallQuotaInMB**|unsignedInt|Választható| 4000 MB. Ha értéket ad meg, az nem lépheti túl ezt az összeget |Az összes naplózási puffer számára lefoglalt fájlrendszer-tárterület teljes mennyisége.|  

## <a name="diagnosticinfrastructurelogs-element"></a>DiagnosticInfrastructureLogs elem  
Meghatározza az alapul szolgáló diagnosztikai infrastruktúra által létrehozott naplók pufferének konfigurációját.

Szülő elem: DiagnosticMonitorConfiguration elem.  

Attribútumok:

|Attribútum|Type|Leírás|  
|---------|----|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Választható. Meghatározza a fájlrendszer tárterületének maximális számát, amely elérhető a megadott adatmennyiség esetében.<br /><br /> Az alapértelmezett érték a 0.|  
|**scheduledTransferLogLevelFilter**|Karakterlánc|Választható. Megadja az átvitt naplóbejegyzések minimális súlyossági szintjét. Az alapértelmezett érték nincs **definiálva**. A többi lehetséges érték a **részletes**, az **információ**, a **Figyelmeztetés**, a **hiba**és a **kritikus**.|  
|**scheduledTransferPeriod**|duration|Választható. Meghatározza az ütemezett adatátvitelek közötti időközt, a legközelebbi percre kerekítve.<br /><br /> Az alapértelmezett érték a PT0S.|  

## <a name="logs-element"></a>Elemek naplózása  
 Meghatározza az alapszintű Azure-naplók pufferének konfigurációját.

 Szülő elem: DiagnosticMonitorConfiguration elem.  

Attribútumok:  

|Attribútum|Type|Leírás|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Választható. Meghatározza a fájlrendszer tárterületének maximális számát, amely elérhető a megadott adatmennyiség esetében.<br /><br /> Az alapértelmezett érték a 0.|  
|**scheduledTransferLogLevelFilter**|sztring|Választható. Megadja az átvitt naplóbejegyzések minimális súlyossági szintjét. Az alapértelmezett érték nincs **definiálva**. A többi lehetséges érték a **részletes**, az **információ**, a **Figyelmeztetés**, a **hiba**és a **kritikus**.|  
|**scheduledTransferPeriod**|duration|Választható. Meghatározza az ütemezett adatátvitelek közötti időközt, a legközelebbi percre kerekítve.<br /><br /> Az alapértelmezett érték a PT0S.|  

## <a name="directories-element"></a>Címtárak elem  
Meghatározza a megadható fájl alapú naplók pufferének konfigurációját.

Szülő elem: DiagnosticMonitorConfiguration elem.  


Attribútumok:  

|Attribútum|Type|Leírás|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Választható. Meghatározza a fájlrendszer tárterületének maximális számát, amely elérhető a megadott adatmennyiség esetében.<br /><br /> Az alapértelmezett érték a 0.|  
|**scheduledTransferPeriod**|duration|Választható. Meghatározza az ütemezett adatátvitelek közötti időközt, a legközelebbi percre kerekítve.<br /><br /> Az alapértelmezett érték a PT0S.|  

## <a name="crashdumps-element"></a>CrashDumps Element  
 Az összeomlási memóriaképek könyvtárát határozza meg.

 Szülő elem: Címtárak elem.  

Attribútumok:  

|Attribútum|Type|Leírás|  
|---------------|----------|-----------------|  
|**container**|Karakterlánc|Annak a tárolónak a neve, ahol a könyvtár tartalmát át kell vinni.|  
|**directoryQuotaInMB**|unsignedInt|Választható. Megadja a könyvtár maximális méretét megabájtban.<br /><br /> Az alapértelmezett érték a 0.|  

## <a name="failedrequestlogs-element"></a>FailedRequestLogs elem  
 A sikertelen kérelmek naplójának könyvtárát határozza meg.

 Szülő elem könyvtárai elem.  

Attribútumok:  

|Attribútum|Type|Leírás|  
|---------------|----------|-----------------|  
|**container**|Karakterlánc|Annak a tárolónak a neve, ahol a könyvtár tartalmát át kell vinni.|  
|**directoryQuotaInMB**|unsignedInt|Választható. Megadja a könyvtár maximális méretét megabájtban.<br /><br /> Az alapértelmezett érték a 0.|  

##  <a name="iislogs-element"></a>IISLogs elem  
 Az IIS-napló címtárát határozza meg.

 Szülő elem könyvtárai elem.  

Attribútumok:  

|Attribútum|Type|Leírás|  
|---------------|----------|-----------------|  
|**container**|Karakterlánc|Annak a tárolónak a neve, ahol a könyvtár tartalmát át kell vinni.|  
|**directoryQuotaInMB**|unsignedInt|Választható. Megadja a könyvtár maximális méretét megabájtban.<br /><br /> Az alapértelmezett érték a 0.|  

## <a name="datasources-element"></a>Adatforrások eleme  
 Nulla vagy több további naplózási könyvtárat határoz meg.

 Szülő elem: Címtárak elem.

## <a name="directoryconfiguration-element"></a>DirectoryConfiguration elem  
 Meghatározza a figyelni kívánt naplófájlok könyvtárát.

 Szülő elem: Adatforrások eleme.

Attribútumok:

|Attribútum|Type|Leírás|  
|---------------|----------|-----------------|  
|**container**|Karakterlánc|Annak a tárolónak a neve, ahol a könyvtár tartalmát át kell vinni.|  
|**directoryQuotaInMB**|unsignedInt|Választható. Megadja a könyvtár maximális méretét megabájtban.<br /><br /> Az alapértelmezett érték a 0.|  

## <a name="absolute-element"></a>Abszolút elem  
 Meghatározza a figyelésre szolgáló könyvtár abszolút elérési útját a választható környezetek kiterjesztésével.

 Szülő elem: DirectoryConfiguration elem.  

Attribútumok:  

|Attribútum|Type|Leírás|  
|---------------|----------|-----------------|  
|**path**|Karakterlánc|Kötelező. A figyelni kívánt könyvtár abszolút elérési útja.|  
|**expandEnvironment**|boolean|Kötelező. Ha **igaz**értékre van állítva, a rendszer kibontja az elérési út környezeti változóit.|  

## <a name="localresource-element"></a>LocalResource elem  
 A szolgáltatás definíciójában definiált helyi erőforráshoz viszonyított elérési utat határozza meg.

 Szülő elem: DirectoryConfiguration elem.  

Attribútumok:  

|Attribútum|Type|Leírás|  
|---------------|----------|-----------------|  
|**name**|sztring|Kötelező. A figyelni kívánt könyvtárat tartalmazó helyi erőforrás neve.|  
|**relativePath**|sztring|Kötelező. A figyelni kívánt helyi erőforráshoz viszonyított elérési út.|  

## <a name="performancecounters-element"></a>PerformanceCounters elem  
 Megadja a gyűjteni kívánt teljesítményszámláló elérési útját.

 Szülő elem: DiagnosticMonitorConfiguration elem.


 Attribútumok:  

|Attribútum|Type|Leírás|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Választható. Meghatározza a fájlrendszer tárterületének maximális számát, amely elérhető a megadott adatmennyiség esetében.<br /><br /> Az alapértelmezett érték a 0.|  
|**scheduledTransferPeriod**|duration|Választható. Meghatározza az ütemezett adatátvitelek közötti időközt, a legközelebbi percre kerekítve.<br /><br /> Az alapértelmezett érték a PT0S.|  

## <a name="performancecounterconfiguration-element"></a>PerformanceCounterConfiguration elem  
 Meghatározza a gyűjteni kívánt teljesítményszámláló értékét.

 Szülő elem: PerformanceCounters elem.  

 Attribútumok:  

|Attribútum|Type|Leírás|  
|---------------|----------|-----------------|  
|**counterSpecifier**|Karakterlánc|Kötelező. A gyűjteni kívánt teljesítményszámláló elérési útja.|  
|**sampleRate**|duration|Kötelező. A teljesítményszámláló gyűjtési sebessége.|  

## <a name="windowseventlog-element"></a>WindowsEventLog elem  
 Meghatározza a figyelni kívánt eseménynaplókat.

 Szülő elem: DiagnosticMonitorConfiguration elem.

  Attribútumok:

|Attribútum|Type|Leírás|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Választható. Meghatározza a fájlrendszer tárterületének maximális számát, amely elérhető a megadott adatmennyiség esetében.<br /><br /> Az alapértelmezett érték a 0.|  
|**scheduledTransferLogLevelFilter**|Karakterlánc|Választható. Megadja az átvitt naplóbejegyzések minimális súlyossági szintjét. Az alapértelmezett érték nincs **definiálva**. A többi lehetséges érték a **részletes**, az **információ**, a **Figyelmeztetés**, a **hiba**és a **kritikus**.|  
|**scheduledTransferPeriod**|duration|Választható. Meghatározza az ütemezett adatátvitelek közötti időközt, a legközelebbi percre kerekítve.<br /><br /> Az alapértelmezett érték a PT0S.|  

## <a name="datasource-element"></a>DataSource elem  
 Meghatározza a figyelni kívánt eseménynaplót.

 Szülő elem: WindowsEventLog elem.  

 Attribútumok:

|Attribútum|Type|Leírás|  
|---------------|----------|-----------------|  
|**name**|Karakterlánc|Kötelező. Egy XPath-kifejezés, amely a gyűjteni kívánt naplót adja meg.|  

