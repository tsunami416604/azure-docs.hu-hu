---
title: "Az Azure Diagnostics bővítmény 1.3 és későbbi konfigurációs séma |} Microsoft Docs"
description: "1.3 sémaverzió és az újabb Azure diagnostics szállított részeként a Microsoft Azure SDK 2.4, később."
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
ms.openlocfilehash: 02656c5bb4d2acd944f565d1397984ce94ced0bd
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
---
# <a name="azure-diagnostics-13-and-later-configuration-schema"></a>Az Azure Diagnostics 1.3 és későbbi konfigurációs séma
> [!NOTE]
> Az Azure Diagnostics kiterjesztés összetevőt használja a teljesítményszámlálók és más Statisztika gyűjtése:
> - Azure-alapú virtuális gépek 
> - Virtual Machine Scale Sets
> - Service Fabric 
> - Cloud Services 
> - Network Security Groups (Hálózati biztonsági csoportok)
> 
> Ezen a lapon csak fontos, ha ilyen szolgáltatást használ.

Ezen a lapon lehet verziók 1.3-as és újabb (az Azure SDK 2.4-es és újabb). Újabb konfigurációs szakaszokat milyen verziójában addig adták hozzá a megjelenítendő megjegyzésnek minősülnek.  

Az itt leírt konfigurációs fájl segítségével diagnosztikai konfigurációs beállítások megadásához a diagnosztikai figyelő indításakor.  

A bővítmény egyéb Microsoft-diagnosztika termékek, például Azure figyelő, az Application Insights és Naplóelemzési együtt használatos.



Töltse le a nyilvános konfigurációs fájl sémadefiníciót hajtja végre a következő PowerShell-parancsot:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

Azure Diagnostics használatával kapcsolatos további információkért lásd: [Azure Diagnostics bővítmény](azure-diagnostics.md).  

## <a name="example-of-the-diagnostics-configuration-file"></a>A diagnosztika konfigurációs fájl példa  
 A következő példa bemutatja egy tipikus diagnosztika konfigurációs fájlt:  

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">   
  <PublicConfig>  
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

JSON megfelelője az előző XML konfigurációs fájlt. 

A PublicConfig és a PrivateConfig egymástól, mert az json használati esetek többségében azok átadása pedig különböző változók. Ezekben az esetekben közé tartozik a Resource Manager-sablonok, a virtuálisgép-méretezési csoport PowerShell és a Visual Studio. 

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

## <a name="reading-this-page"></a>Ez a lap olvasása  
 A következő címkék sorrendje nagyjából az előző példában látható módon.  Ha nem látja a teljes leírás várt azt, keresse meg a lap a elem vagy attribútum.  

## <a name="common-attribute-types"></a>Közös attribútum típusát  
 **scheduledTransferPeriod** attribútum több elem szerepel. Ütemezett átvitelek tárolási felfelé kerekítve a legközelebbi perc között. Az érték egy [XML "Duration adattípusú."](http://www.w3schools.com/schema/schema_dtypes_date.asp)


## <a name="diagnosticsconfiguration-element"></a>DiagnosticsConfiguration elem  
 *: Tartománygyökér - DiagnosticsConfiguration*

1.3-as verzió felvételére.  

A legfelső szintű elem a diagnosztika konfigurációs fájl.  

**Attribútum** xmlns - diagnosztika konfigurációs fájl XML-névtere van:  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  


|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**PublicConfig**|Kötelező. Ezen az oldalon máshol lásd a leírást.|  
|**PrivateConfig**|Választható. Ezen az oldalon máshol lásd a leírást.|  
|**IsEnabled**|Logikai érték. Ezen az oldalon máshol lásd a leírást.|  

## <a name="publicconfig-element"></a>PublicConfig elem  
 *Fa: Gyökér - DiagnosticsConfiguration - PublicConfig*

 A nyilvános diagnosztikai konfigurációja ismerteti.  

|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**WadCfg**|Kötelező. Ezen az oldalon máshol lásd a leírást.|  
|**StorageAccount**|Az adatok tárolásához Azure Storage-fiók neve. Előfordulhat, hogy is meg kell adni egy paraméterként a Set-AzureServiceDiagnosticsExtension parancsmag végrehajtása közben.|  
|**StorageType**|Lehet *tábla*, *Blob*, vagy *TableAndBlob*. Tábla alapértelmezett beállítás. Amikor a TableAndBlob van kiválasztva, diagnosztikai adatok bekerül kétszer – egyszer minden.|  
|**LocalResourceDirectory**|A könyvtár a virtuális gépen, ahol a a Figyelőügynök események adatait tárolja. Ha nem, állítsa be, az alapértelmezett mappát használja:<br /><br /> A munkavégző vagy webes szerepkör:`C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> A virtuális gép:`C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Szükséges attribútumok pedig a következők:<br /><br /> - **elérési út** – a rendszer Azure Diagnostics által használandó könyvtárához.<br /><br /> - **expandEnvironment** -szabályozza, hogy az elérési útban környezeti változók bontva.|  

## <a name="wadcfg-element"></a>WadCFG elem  
 *Fa: A gyökérkönyvtár - DiagnosticsConfiguration - PublicConfig - WadCFG*
 
 Azonosítja, és konfigurálja a telemetriai adatok gyűjtésére.  


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration elem 
 *Fa: Gyökér - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration*

 Szükséges 

|Attribútumok|Leírás|  
|----------------|-----------------|  
| **overallQuotaInMB** | Előfordulhat, hogy használni a különböző típusú Azure Diagnostics által gyűjtött diagnosztikai adatok helyi lemezterület maximális mennyisége. Az alapértelmezett érték 4096 MB.<br />
|**useProxyServer** | Azure Diagnostics használatára a proxykiszolgáló beállításait ahogyan az Internet Explorer beállításainak konfigurálása.|  

<br /> <br />

|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**CrashDumps**|Ezen az oldalon máshol lásd a leírást.|  
|**DiagnosticInfrastructureLogs**|Az Azure diagnosztikai által létrehozott naplók gyűjtésének engedélyezése. A diagnosztikai infrastruktúra naplók hasznosak a diagnosztika rendszer magát a hibaelhárításhoz. Nem kötelező attribútumok pedig a következők:<br /><br /> - **scheduledTransferLogLevelFilter** -konfigurálja a minimális súlyossági szintet a gyűjtött naplók.<br /><br /> - **scheduledTransferPeriod** -tároló ütemezett átvitelek között felfelé kerekítve a legközelebbi perc. Az érték egy [XML "Duration adattípusú."](http://www.w3schools.com/schema/schema_dtypes_date.asp) |  
|**Könyvtárak**|Ezen az oldalon máshol lásd a leírást.|  
|**EtwProviders**|Ezen az oldalon máshol lásd a leírást.|  
|**Metrikák**|Ezen az oldalon máshol lásd a leírást.|  
|**PerformanceCounters**|Ezen az oldalon máshol lásd a leírást.|  
|**WindowsEventLog**|Ezen az oldalon máshol lásd a leírást.| 
|**DockerSources**|Ezen az oldalon máshol lásd a leírást. | 



## <a name="crashdumps-element"></a>CrashDumps Element  
 *Fa: A gyökérkönyvtár - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - CrashDumps*
 
 Összeomlási memóriaképek gyűjtésének engedélyezése.  

|Attribútumok|Leírás|  
|----------------|-----------------|  
|**containerName**|Választható. A blob tároló összeomlási memóriaképek tárolására használható az Azure Storage-fiók neve.|  
|**crashDumpType**|Választható.  Konfigurálja az Azure Diagnostics mini vagy teljes összeomlási memóriaképek összegyűjtése.|  
|**directoryQuotaPercentage**|Választható.  Konfigurálja a százaléka **overallQuotaInMB** kell lefoglalni, az összeomlási memóriaképek a virtuális Gépen.|  

|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**CrashDumpConfiguration**|Kötelező. Határozza meg a konfigurációs értékeket az összes folyamat.<br /><br /> A következő attribútum is szükség:<br /><br /> **Folyamatnév** -nevét a folyamat kívánt Azure Diagnostics meg tudja gyűjteni az összeomlási memóriaképet.|  

## <a name="directories-element"></a>Könyvtárak elem 
 *Fa: Gyökér - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - könyvtárak*

 Lehetővé teszi, hogy egy könyvtárat, az IIS nem tudta belépési kérelem naplók és/vagy IIS-napló tartalmát gyűjteménye.  

 Nem kötelező **scheduledTransferPeriod** attribútum. Tekintse meg a korábban magyarázatot.  

|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**IISLogs**|Többek között az elem a konfigurációban lehetővé teszi, hogy az IIS-naplók a gyűjteményben:<br /><br /> **containerName** – az Azure Storage-fiók használható az IIS-napló tárolására a blob tároló nevét.|   
|**FailedRequestLogs**|Többek között az elem a konfigurációban lehetővé teszi, hogy az IIS-webhelyet vagy alkalmazást a sikertelen kérelmek kapcsolatos információkat naplózza gyűjteménye. Engedélyeznie kell a nyomkövetést **rendszer. Webkiszolgáló** a **Web.config**.|  
|**Adatforrások**|Figyelendő könyvtárainak listáját.| 




## <a name="datasources-element"></a>Adatforrások elem  
 *Fa: Gyökér - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - könyvtárak - adatforrás*

 Figyelendő könyvtárainak listáját.  

|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**DirectoryConfiguration**|Kötelező. Kötelező attribútum:<br /><br /> **containerName** -, a naplófájlok tárolására használt fiók a blob-tároló az Azure Storage neve.|  





## <a name="directoryconfiguration-element"></a>DirectoryConfiguration elem  
 *Fa: A gyökérkönyvtár - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - könyvtárak - adatforrás - DirectoryConfiguration*

 Vagy előfordulhat, hogy tartalmazza a **abszolút** vagy **LocalResource** elem, de soha sem egyszerre mindkettőre.  

|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**Abszolút**|A figyelni kívánt könyvtár abszolút elérési útja A következő attribútumok szükségesek:<br /><br /> - **Elérési út** -a figyelni kívánt könyvtár abszolút elérési útja.<br /><br /> - **expandEnvironment** -konfigurálható, hogy az elérési út környezeti változókat levő figyelő kibontva látható.|  
|**LocalResource**|Az elérési út a figyelheti a helyi erőforrás viszonyítva. Szükséges attribútumok pedig a következők:<br /><br /> - **Név** -a helyi erőforrást, amely tartalmazza a figyelni kívánt könyvtár<br /><br /> - **relativePath** – a relatív nevét, amely tartalmazza a figyelni kívánt könyvtár elérési útja|  



## <a name="etwproviders-element"></a>EtwProviders elem  
 *Fa: A gyökérkönyvtár - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders*

 Konfigurálja az EventSource ETW-eseményeinek gyűjtése és/vagy a szolgáltatók ETW Manifest alapján.  

|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Konfigurálja az előállított eseményeinek gyűjtése [EventSource osztály](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Kötelező attribútum:<br /><br /> **szolgáltató** -az EventSource esemény osztály neve.<br /><br /> Nem kötelező attribútumok pedig a következők:<br /><br /> - **scheduledTransferLogLevelFilter** – a minimális súlyossági szintet a tárfiók át.<br /><br /> - **scheduledTransferPeriod** -tároló ütemezett átvitelek között felfelé kerekítve a legközelebbi perc. Az érték egy [XML "Duration adattípusú."](http://www.w3schools.com/schema/schema_dtypes_date.asp) |  
|**EtwManifestProviderConfiguration**|Kötelező attribútum:<br /><br /> **szolgáltató** -esemény szolgáltatójának a GUID-azonosító<br /><br /> Nem kötelező attribútumok pedig a következők:<br /><br /> - **scheduledTransferLogLevelFilter** – a minimális súlyossági szintet a tárfiók át.<br /><br /> - **scheduledTransferPeriod** -tároló ütemezett átvitelek között felfelé kerekítve a legközelebbi perc. Az érték egy [XML "Duration adattípusú."](http://www.w3schools.com/schema/schema_dtypes_date.asp) |  



## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration Element  
 *: Tartománygyökér - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders - EtwEventSourceProviderConfiguration*

 Konfigurálja az előállított eseményeinek gyűjtése [EventSource osztály](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx).  

|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**DefaultEvents**|Nem kötelező attribútum:<br/><br/> **eventDestination** -tárolja az eseményeket az a táblázat neve|  
|**Esemény**|Kötelező attribútum:<br /><br /> **azonosító** -esemény azonosítója.<br /><br /> Nem kötelező attribútum:<br /><br /> **eventDestination** -tárolja az eseményeket az a táblázat neve|  



## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration Element  
 *Fa: Gyökér - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders - EtwManifestProviderConfiguration*

|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**DefaultEvents**|Nem kötelező attribútum:<br /><br /> **eventDestination** -tárolja az eseményeket az a táblázat neve|  
|**Esemény**|Kötelező attribútum:<br /><br /> **azonosító** -esemény azonosítója.<br /><br /> Nem kötelező attribútum:<br /><br /> **eventDestination** -tárolja az eseményeket az a táblázat neve|  



## <a name="metrics-element"></a>Metrikák elem  
 *Fa: Gyökér - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - metrikák*

 Lehetővé teszi a gyors lekérdezéseket optimalizált teljesítményt számláló tábla létrehozásához. Minden, a megadott teljesítményszámláló a **PerformanceCounters** elem a metrikák tábla mellett a teljesítményszámláló tábla tárolja.  

 A **resourceId** attribútumot kell megadni.  Az erőforrás-azonosítója a virtuális gép vagy virtuálisgép-méretezési csoport Azure Diagnostics meg tudja telepíti. Beolvasása a **resourceID** a a [Azure-portálon](https://portal.azure.com). Válassza ki **Tallózás** -> **erőforráscsoportok** -> **< név\>**. Kattintson a **tulajdonságok** csempére, majd másolja az értéket a **azonosító** mező.  

|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**MetricAggregation**|Kötelező attribútum:<br /><br /> **scheduledTransferPeriod** -tároló ütemezett átvitelek között felfelé kerekítve a legközelebbi perc. Az érték egy [XML "Duration adattípusú."](http://www.w3schools.com/schema/schema_dtypes_date.asp) |  



## <a name="performancecounters-element"></a>PerformanceCounters elem  
 *Fa: A gyökérkönyvtár - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - PerformanceCounters*

 Lehetővé teszi, hogy a teljesítményszámlálók gyűjteményét.  

 Nem kötelező attribútum:  

 Nem kötelező **scheduledTransferPeriod** attribútum. Tekintse meg a korábban magyarázatot.

|Gyermekelem|Leírás|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguration**|A következő attribútumok szükségesek:<br /><br /> - **counterSpecifier** -teljesítményszámláló nevét. Például: `\Processor(_Total)\% Processor Time`. Ahhoz, hogy a teljesítményszámlálók listája a gazdagépen, futtassa a parancsot `typeperf`.<br /><br /> - **sampleRate** -gyakoriságát. a számláló mintát venni.<br /><br /> Nem kötelező attribútum:<br /><br /> **egység** -számláló mértékegysége.|  




## <a name="windowseventlog-element"></a>WindowsEventLog elem
 *Fa: A gyökérkönyvtár - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - WindowsEventLog*
 
 Lehetővé teszi a gyűjteményt, a Windows eseménynaplóiban keresse meg.  

 Nem kötelező **scheduledTransferPeriod** attribútum. Tekintse meg a korábban magyarázatot.  

|Gyermekelem|Leírás|  
|-------------------|-----------------|  
|**DataSource**|A Windows eseménynaplóiban gyűjtéséhez. Kötelező attribútum:<br /><br /> **név** – a windows-eseményeket tudjon gyűjteni leíró XPath-lekérdezést. Példa:<br /><br /> `Application!*[System[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level <= 3)]`<br /><br /> Minden eseményt gyűjt, adja meg a "*"|  




## <a name="logs-element"></a>Naplók elem  
 *Fa: Gyökér - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - naplók*

 Jelen verziójában 1.0 és 1.1. Hiányzik a 1.2-es. A hozzáadott 1.3.  

 Határozza meg a puffer konfigurációs az alapszintű Azure-naplók.  

|Attribútum|Típus|Leírás|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**unsignedInt**|Választható. Meghatározza a maximális időt a rendszer fájltárolók érhetők el a megadott adatokat.<br /><br /> Az alapértelmezett érték 0.|  
|**scheduledTransferLogLevelFilterr**|**string**|Választható. Meghatározza a naplóbejegyzéseket, amelyeket a minimális súlyossági szintet. Az alapértelmezett érték **meghatározatlan**, amely továbbítja az összes naplófájlt. Más lehetséges értékek (legalább információkat a legtöbb sorrendben): **részletes**, **információk**, **figyelmeztetés**, **hiba**, és **kritikus**.|  
|**scheduledTransferPeriod**|**időtartam**|Választható. Az időköz ütemezett átvitelek adatok felfelé kerekítve a legközelebbi perc között.<br /><br /> Az alapértelmezett érték PT0S.|  
|**fogadók esetében** 1.5-ös hozzáadva|**string**|Választható. A fogadó hely is a diagnosztikai adatok küldése mutat. Például az Application Insights.|  

## <a name="dockersources"></a>DockerSources
 *Fa: A gyökérkönyvtár - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - DockerSources*

 1.9 felvételére.

|Elem neve|Leírás|  
|------------------|-----------------|  
|**Statisztikák**|Docker-tároló vonatkozó statisztikák gyűjtése arra utasítja a rendszert|  

## <a name="sinksconfig-element"></a>SinksConfig elem  
 *Fa: Gyökér - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig*

 Diagnosztikai adatok és az azokon a helyeken társított konfigurációs küldendő helyek listáját.  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**Sink**|Ezen az oldalon máshol lásd a leírást.|  

## <a name="sink-element"></a>Elem gyűjtése
 *Fa: A gyökérkönyvtár - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - fogadó*

 1.5-ös verziójának felvételére.  

 Határozza meg a helyek a diagnosztikai adatok küldése. Például az Application Insights szolgáltatás.  

|Attribútum|Típus|Leírás|  
|---------------|----------|-----------------|  
|**name**|karakterlánc|A sinkname azonosító karakterláncot.|  

|Elem|Típus|Leírás|  
|-------------|----------|-----------------|  
|**Application Insights**|karakterlánc|Csak akkor, amikor adatokat küld az Application Insights használt. Aktív Application Insights-fiók, amely rendelkezik hozzáféréssel a Instrumentation kulcsot tartalmaz.|  
|**Csatornák**|karakterlánc|Minden további szűréséhez, hogy adatfolyamként küldje el, amikor egy|  

## <a name="channels-element"></a>Csatornák elem  
 *Fa: Gyökér - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - fogadó - csatornák*

 1.5-ös verziójának felvételére.  

 Határozza meg a naplózási adatokat, hogy át kellene haladnia egy fogadó adatfolyamokat szűrőket.  

|Elem|Típus|Leírás|  
|-------------|----------|-----------------|  
|**Channel**|karakterlánc|Ezen az oldalon máshol lásd a leírást.|  

## <a name="channel-element"></a>Csatorna elem
 *Fa: Gyökér - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - fogadó - csatornák - csatorna*

 1.5-ös verziójának felvételére.  

 Határozza meg a helyek a diagnosztikai adatok küldése. Például az Application Insights szolgáltatás.  

|Attribútumok|Típus|Leírás|  
|----------------|----------|-----------------|  
|**logLevel**|**string**|Meghatározza a naplóbejegyzéseket, amelyeket a minimális súlyossági szintet. Az alapértelmezett érték **meghatározatlan**, amely továbbítja az összes naplófájlt. Más lehetséges értékek (legalább információkat a legtöbb sorrendben): **részletes**, **információk**, **figyelmeztetés**, **hiba**, és **kritikus**.|  
|**name**|**string**|A csatornát, tekintse meg egy egyedi neve|  


## <a name="privateconfig-element"></a>PrivateConfig elem 
 *Fa: Gyökér - DiagnosticsConfiguration - PrivateConfig*

 1.3-as verzió felvételére.  

 Optional  

 A storage-fiók (a nevét, a kulcs és a végpont) személyes adatait tárolja. Ezt az információt küld a virtuális gép, de nem kérhetők le azt.  

|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**StorageAccount**|A használt tárfiók. A következő attribútumok szükségesek.<br /><br /> - **név** -a tárfiók nevét.<br /><br /> - **kulcs** -a tárfiók kulcsa.<br /><br /> - **végpont** -a végpont a tárfiók eléréséhez. <br /><br /> -**sasToken** (egy SAS-token helyett a tárfiók kulcsa megadhatja a saját config 1.8.1)-hozzá. Ha adott, a rendszer figyelmen kívül hagyja a tárfiók kulcsára. <br />A SAS-jogkivonat vonatkozó követelmények: <br />-Fiók SAS-jogkivonat csak támogatja <br />- *b*, *t* szolgáltatástípusok szükség. <br /> - *egy*, *c*, *u*, *w* engedélyekre szükség. <br /> - *c*, *o* típusú erőforrások szükségesek. <br /> -Csak a HTTPS protokollt támogatja <br /> -Elindításához és a lejárati időpont érvényesnek kell lennie.|  


## <a name="isenabled-element"></a>IsEnabled elem  
 *Fa: Gyökér - DiagnosticsConfiguration - IsEnabled*

 Logikai érték. Használjon `true` a diagnosztika engedélyezéséhez vagy `false` letiltása a diagnosztika.
