---
title: Az Azure Diagnostics bővítmény 1.3 és újabb konfigurációs séma
description: 1.3-as verziójú és az Azure diagnostics újabb le szállítani részeként a Microsoft Azure SDK 2.4 és később.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/20/2018
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: a330f92e0a44153cb258ff86299d34aa408496d2
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53326239"
---
# <a name="azure-diagnostics-13-and-later-configuration-schema"></a>Az Azure Diagnostics 1.3 és újabb konfigurációs séma
> [!NOTE]
> Az Azure Diagnostics bővítmény az a komponens, teljesítményszámlálók és más statisztikáin összegyűjtéséhez használt:
> - Azure-alapú virtuális gépek
> - Virtual Machine Scale Sets
> - Service Fabric
> - Cloud Services
> - Network Security Groups (Hálózati biztonsági csoportok)
>
> Ez a lap csak akkor jelentősége, ha ezek a szolgáltatások valamelyikét használja.

Ez az oldal akkor érvényes verziója 1.3 és újabb (az Azure SDK 2.4-es és újabb). Újabb konfigurációs szakaszokat a megjelenítése, melyik verzió a addig adták hozzá vannak ellátva.  

Az itt leírt konfigurációs fájl segítségével diagnosztikai konfigurációs beállítások megadásához a diagnosztikai figyelő indításakor.  

A bővítmény egyéb Microsoft-diagnosztika termékek, mint az Azure Monitor az Application Insights és a Log Analytics együtt használatos.



Töltse le a nyilvános bővítménykonfiguráció sémája fájldefiníciót a következő PowerShell-parancs végrehajtásával:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

Azure Diagnostics használatával kapcsolatos további információkért lásd: [Azure Diagnostics bővítmény](diagnostics-extension-overview.md).  

## <a name="example-of-the-diagnostics-configuration-file"></a>A diagnosztika konfigurációs fájl példa  
 Az alábbi példa bemutatja egy tipikus diagnosztikai konfigurációs fájlban:  

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
> A nyilvános Azure Monitor-fogadó definíció konfigurációs van két tulajdonság, az erőforrás-azonosító és a régió. Ezek csak a klasszikus virtuális gépeket és a klasszikus felhőalapú szolgáltatásokhoz szükséges. Ezek a tulajdonságok nem használhatók a Resource Manager virtuális gépek vagy virtuálisgép-méretezési csoportok.
> Emellett van egy további saját konfigurációs elem számára az Azure Monitor gyűjtőjéhez, amelyet átad a rendszerbiztonsági tag azonosítója és kulcsa. Ez a tulajdonság csak a klasszikus virtuális gépeket és a klasszikus Cloud Services szükséges. A Resource Manager virtuális gépek és az Azure Monitor VMSS zárhatók ki a saját konfigurációs elem definíciója.
>

JSON megfelelője az előző konfigurációs XML-fájl.

A PublicConfig és PrivateConfig vannak elkülönítve, mert az json használati esetek többségében azok adhatók be különböző változók. Ezekben az esetekben a Resource Manager-sablonok tartalmaznak, a virtuálisgép-méretezési csoport beállítása a PowerShell és a Visual Studio.

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
> A nyilvános Azure Monitor-fogadó definíció konfigurációs van két tulajdonság, az erőforrás-azonosító és a régió. Ezek csak a klasszikus virtuális gépeket és a klasszikus felhőalapú szolgáltatásokhoz szükséges.
> Ezek a tulajdonságok nem használhatók a Resource Manager virtuális gépek vagy virtuálisgép-méretezési csoportok.
>

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

> [!NOTE]
> Van egy további saját konfigurációs elem számára az Azure Monitor gyűjtőjéhez, amelyet átad a rendszerbiztonsági tag azonosítója és kulcsa. Ez a tulajdonság csak a klasszikus virtuális gépeket és a klasszikus Cloud Services szükséges. A Resource Manager virtuális gépek és az Azure Monitor VMSS zárhatók ki a saját konfigurációs elem definíciója.
>


## <a name="reading-this-page"></a>Ez a lap olvasása  
 A következő címkék pedig körülbelül az előző példában is látható sorrendben.  Ha nem lát teljes leírása, a várt, keresse meg az oldal az elem vagy attribútum.  

## <a name="common-attribute-types"></a>Közös Attribútumtípusok  
 **scheduledTransferPeriod** attribútum több elem jelenik meg. A tároló a legközelebbi egész percre kerekítve ütemezett felé közötti időtartam. Az érték egy [XML "Időtartam adattípus."](http://www.w3schools.com/xml/schema_dtypes_date.asp)


## <a name="diagnosticsconfiguration-element"></a>DiagnosticsConfiguration elem  
 *Fa: Gyökér - DiagnosticsConfiguration*

1.3-as verziójában hozzáadva.  

A legfelső szintű elem a diagnosztikai konfigurációs fájl.  

**Attribútum** xmlns - diagnosztika konfigurációs fájl XML-névtér van:  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  


|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**PublicConfig**|Kötelező. Lásd: leírás máshol ezen az oldalon.|  
|**PrivateConfig**|Választható. Lásd: leírás máshol ezen az oldalon.|  
|**isEnabled**|Logikai érték. Lásd: leírás máshol ezen az oldalon.|  

## <a name="publicconfig-element"></a>PublicConfig elem  
 *Fa: Gyökér - DiagnosticsConfiguration - PublicConfig*

 A nyilvános diagnosztikai konfigurációja ismerteti.  

|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**WadCfg**|Kötelező. Lásd: leírás máshol ezen az oldalon.|  
|**StorageAccount**|Az adatok tárolásához Azure Storage-fiók neve. Előfordulhat, hogy is adható meg paraméterként a Set-AzureServiceDiagnosticsExtension parancsmag végrehajtása közben.|  
|**StorageType**|Lehet *tábla*, *Blob*, vagy *TableAndBlob*. Alapértelmezett érték a táblában. TableAndBlob akkor kell kiválasztani, amikor diagnosztikai adatokat írt kétszer – egyszer minden típusa.|  
|**LocalResourceDirectory**|A könyvtár a virtuális gépen, amelyen a Monitoring Agent tárolja az eseményadatokat. Ha nem, állítsa be, az alapértelmezett könyvtárat használja:<br /><br /> Feldolgozói/webes szerepkör: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Egy virtuális géphez: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Szükséges attribútumok a következők:<br /><br /> - **elérési út** – a rendszer az Azure Diagnostics által használandó könyvtárába.<br /><br /> - **expandEnvironment** – azt szabályozza, hogy környezeti változókat az elérési útban bontva.|  

## <a name="wadcfg-element"></a>WadCFG elem  
 *Fa: Gyökér - DiagnosticsConfiguration - PublicConfig - WadCFG*

 Azonosítja, és konfigurálja a telemetriai adatokat gyűjteni.  


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration elem
 *Fa: Gyökér - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration*

 Szükséges

|Attribútumok|Leírás|  
|----------------|-----------------|  
| **overallQuotaInMB** | Előfordulhat, hogy tudják használni a különféle Azure Diagnostics által gyűjtött diagnosztikai adatok helyi lemezterület maximális mennyisége. Az alapértelmezett érték 4096 MB.<br />
|**useProxyServer** | Az Azure Diagnostics használata a proxykiszolgáló beállításait készletként, az Internet Explorer beállításainak konfigurálása.|
|**fogadóként** | 1.5-ös hozzá. Választható. A fogadó hely is az összes alárendelt elemek, amelyek támogatják a fogadóként diagnosztikai adatok küldése mutat. Az Application Insights és az Event Hubs fogadó példája.|  


<br /> <br />

|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**CrashDumps**|Lásd: leírás máshol ezen az oldalon.|  
|**DiagnosticInfrastructureLogs**|Azure Diagnostics által létrehozott naplók gyűjtésének engedélyezéséhez. A diagnosztikai infrastruktúra naplói hasznosak hibáinak elhárítása a diagnosztikai rendszer magát. Nem kötelező attribútumok a következők:<br /><br /> - **scheduledTransferLogLevelFilter** – konfigurálja a gyűjtött naplók minimális súlyossági szintje.<br /><br /> - **scheduledTransferPeriod** – Storage ütemezett átvitel közötti időtartam kerekítve a legközelebbi egész percre. Az érték egy [XML "Időtartam adattípus."](http://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**Címtárak**|Lásd: leírás máshol ezen az oldalon.|  
|**EtwProviders**|Lásd: leírás máshol ezen az oldalon.|  
|**Metrikák**|Lásd: leírás máshol ezen az oldalon.|  
|**PerformanceCounters**|Lásd: leírás máshol ezen az oldalon.|  
|**WindowsEventLog**|Lásd: leírás máshol ezen az oldalon.|
|**DockerSources**|Lásd: leírás máshol ezen az oldalon. |



## <a name="crashdumps-element"></a>CrashDumps elem  
 *Fa: Gyökér - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - CrashDumps*

 Az összeomlási memóriaképek gyűjtésének engedélyezéséhez.  

|Attribútumok|Leírás|  
|----------------|-----------------|  
|**containerName**|Választható. Az összeomlási memóriaképek tárolásához használt Azure Storage-fiókját a blobtároló neve.|  
|**crashDumpType**|Választható.  Konfigurálja az Azure Diagnostics mini vagy teljes összeomlás memóriaképek összegyűjtése.|  
|**directoryQuotaPercentage**|Választható.  Konfigurálja a százalékos arányát **overallQuotaInMB** kell lefoglalni, az összeomlási memóriaképek a virtuális gépen.|  

|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**CrashDumpConfiguration**|Kötelező. Minden folyamathoz konfigurációs értékeket határozza meg.<br /><br /> A következő attribútumot is szükség:<br /><br /> **processName** – a nevét, a folyamat egy összeomlási memóriakép a gyűjtendő Azure Diagnostics szeretne.|  

## <a name="directories-element"></a>Könyvtárak elem
 *Fa: Gyökér - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - könyvtárak*

 Lehetővé teszi a tartalmát egy könyvtárat, az IIS nem sikerült hozzáférést kérelmekről készült naplók és/vagy IIS-naplók gyűjtését.  

 Nem kötelező **scheduledTransferPeriod** attribútum. Tekintse meg a korábban a magyarázatot.  

|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**IISLogs**|Például ez az elem a konfigurációban lehetővé teszi az IIS-naplók gyűjtésére:<br /><br /> **containerName** -az IIS-naplók tárolására szolgáló Azure Storage-fiókját a blobtároló nevét.|   
|**FailedRequestLogs**|Például ez az elem a konfigurációban lehetővé teszi, hogy a sikertelen kérelmek egy IIS-webhely vagy alkalmazás naplókat gyűjteménye. Is engedélyeznie kell a nyomkövetést **rendszer. Webkiszolgáló** a **Web.config**.|  
|**Adatforrások**|Figyelendő könyvtárainak listáját.|




## <a name="datasources-element"></a>Adatforrások elem  
 *Fa: Gyökér - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - könyvtárak - adatforrások*

 Figyelendő könyvtárainak listáját.  

|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**DirectoryConfiguration**|Kötelező. Kötelező attribútum:<br /><br /> **containerName** – az a blob-tároló az Azure Storage-fiók neve a naplófájlok tárolásához használandó.|  





## <a name="directoryconfiguration-element"></a>DirectoryConfiguration elem  
 *Fa: Gyökér - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - könyvtárak - adatforrások - DirectoryConfiguration*

 Vagy előfordulhat, hogy tartalmazzák a **abszolút** vagy **LocalResource** elemmel, de nem mindkettőt.  

|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**Abszolút**|Figyelni kívánt könyvtár abszolút elérési útja. A következő attribútumok szükség:<br /><br /> - **Elérési út** -a figyelni kívánt könyvtár abszolút elérési útját.<br /><br /> - **expandEnvironment** -segítségével beállítható, hogy az elérési út környezeti változók vannak bontva.|  
|**LocalResource**|Az elérési út képest helyi erőforrás monitorozásához. Szükséges attribútumok a következők:<br /><br /> - **Név** – a helyi erőforrás, amely tartalmazza a figyelni kívánt könyvtár<br /><br /> - **relativePath** -képest relatív nevét, amely tartalmazza a figyelni kívánt könyvtár elérési útja|  



## <a name="etwproviders-element"></a>EtwProviders elem  
 *Fa: Gyökér - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders*

 Konfigurálja az ETW-események az eseményforrás gyűjteménye, illetve az ETW-jegyzékfájl-alapú szolgáltatók.  

|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Konfigurálja a generált események gyűjtését [EventSource osztály](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Kötelező attribútum:<br /><br /> **szolgáltató** -az osztály nevét az EventSource esemény.<br /><br /> Nem kötelező attribútumok a következők:<br /><br /> - **scheduledTransferLogLevelFilter** – minimális súlyossági szintje a tárfiókhoz való átviteléhez.<br /><br /> - **scheduledTransferPeriod** – Storage ütemezett átvitel közötti időtartam kerekítve a legközelebbi egész percre. Az érték egy [XML "Időtartam adattípus."](http://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**EtwManifestProviderConfiguration**|Kötelező attribútum:<br /><br /> **szolgáltató** -esemény szolgáltatójának a GUID-azonosító<br /><br /> Nem kötelező attribútumok a következők:<br /><br /> - **scheduledTransferLogLevelFilter** – minimális súlyossági szintje a tárfiókhoz való átviteléhez.<br /><br /> - **scheduledTransferPeriod** – Storage ütemezett átvitel közötti időtartam kerekítve a legközelebbi egész percre. Az érték egy [XML "Időtartam adattípus."](http://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration Element  
 *Fa: Legfelső szintű – - - WadCFG - PublicConfig DiagnosticMonitorConfiguration - DiagnosticsConfiguration EtwProviders - EtwEventSourceProviderConfiguration*

 Konfigurálja a generált események gyűjtését [EventSource osztály](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx).  

|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**DefaultEvents**|Nem kötelező attribútum:<br/><br/> **eventDestination** -tárolja az eseményeket az a tábla neve|  
|**Esemény**|Kötelező attribútum:<br /><br /> **ID** -esemény azonosítója.<br /><br /> Nem kötelező attribútum:<br /><br /> **eventDestination** -tárolja az eseményeket az a tábla neve|  



## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration Element  
 *Fa: Gyökér - DiagnosticsConfiguration – - - DiagnosticMonitorConfiguration - WadCFG EtwProviders - PublicConfig EtwManifestProviderConfiguration*

|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**DefaultEvents**|Nem kötelező attribútum:<br /><br /> **eventDestination** -tárolja az eseményeket az a tábla neve|  
|**Esemény**|Kötelező attribútum:<br /><br /> **ID** -esemény azonosítója.<br /><br /> Nem kötelező attribútum:<br /><br /> **eventDestination** -tárolja az eseményeket az a tábla neve|  



## <a name="metrics-element"></a>Metrikák elem  
 *Fa: Gyökér - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - metrikák*

 Lehetővé teszi, hogy hozzon létre egy számláló teljesítménytáblája, amely gyors lekérdezéseket van optimalizálva. Minden egyes megadott teljesítményszámláló a **PerformanceCounters** elem a metrikák tábla mellett a teljesítményszámláló tábla tárolja.  

 A **resourceId** attribútumot kötelező megadni.  A virtuális gép vagy virtuálisgép-méretezési készlet erőforrás-Azonosítóját az Azure Diagnostics telepít. Első a **resourceID** származó a [az Azure portal](https://portal.azure.com). Válassza ki **Tallózás** -> **erőforráscsoportok** -> **< név\>**. Kattintson a **tulajdonságok** csempére, és másolja az értéket a **azonosító** mező.  

|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**MetricAggregation**|Kötelező attribútum:<br /><br /> **scheduledTransferPeriod** – Storage ütemezett átvitel közötti időtartam kerekítve a legközelebbi egész percre. Az érték egy [XML "Időtartam adattípus."](http://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="performancecounters-element"></a>PerformanceCounters elem  
 *Fa: Gyökér - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - PerformanceCounters*

 Lehetővé teszi a teljesítményszámláló-gyűjtemény.  

 Nem kötelező attribútum:  

 Nem kötelező **scheduledTransferPeriod** attribútum. Tekintse meg a korábban a magyarázatot.

|Gyermekelemet|Leírás|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguration**|A következő attribútumok szükség:<br /><br /> - **counterSpecifier** – a teljesítményszámláló neve. Például: `\Processor(_Total)\% Processor Time`. A gazdagépen futó teljesítményszámlálók listájának lekéréséhez futtassa a parancsot `typeperf`.<br /><br /> - **sampleRate** -gyakoriságát. a számláló kell mintát venni.<br /><br /> Nem kötelező attribútum:<br /><br /> **egység** – a számláló mértékegysége.|
|**fogadóként** | 1.5-ös hozzá. Választható. A fogadó hely is a diagnosztikai adatok küldése mutat. Ha például az Azure Monitor vagy az Event Hubs.|    




## <a name="windowseventlog-element"></a>WindowsEventLog elem
 *Fa: Gyökér - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - WindowsEventLog*

 Lehetővé teszi, hogy a gyűjtemény Windows eseménynaplók.  

 Nem kötelező **scheduledTransferPeriod** attribútum. Tekintse meg a korábban a magyarázatot.  

|Gyermekelemet|Leírás|  
|-------------------|-----------------|  
|**Adatforrás**|A Windows Eseménynapló bejegyzéseit, amelyek gyűjtése. Kötelező attribútum:<br /><br /> **név** – az XPath-lekérdezés, amely leírja a windows-eseményeket tudjon gyűjteni. Példa:<br /><br /> `Application!*[System[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level <= 3)]`<br /><br /> Összes eseményének gyűjtéséhez, adja meg a "*"|  




## <a name="logs-element"></a>Naplók elem  
 *Fa: Gyökér - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - naplók*

 Jelen verziójában 1.0 és 1.1. Hiányzik az 1.2-es. Térjen vissza a hozzáadott 1.3-tól.  

 Az alapszintű Azure-naplók puffer konfigurációját.  

|Attribútum|Típus|Leírás|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**unsignedInt**|Választható. Meghatározza a maximális fájlrendszer-tárhely, amelyet a megadott adatokat.<br /><br /> Az alapértelmezett érték a 0.|  
|**scheduledTransferLogLevelFilterr**|**string**|Választható. Meghatározza a naplóbejegyzéseket, amelyeket a minimális súlyossági szintet. Az alapértelmezett érték **Undefined**, az összes napló adatforgalmi. Egyéb (a legerősebbtől a legnagyobb legalább információkat) lehetséges értékek a következők **részletes**, **információk**, **figyelmeztetés**, **hiba**, és **Kritikus**.|  
|**scheduledTransferPeriod**|**Időtartam**|Választható. Ütemezett átvitel az adatokat, a legközelebbi egész percre kerekítve közötti időköz.<br /><br /> Az alapértelmezett érték PT0S.|  
|**fogadóként** |**string**| 1.5-ös hozzá. Választható. A fogadó hely is a diagnosztikai adatok küldése mutat. Ha például az Application Insights vagy az Event Hubs.|  

## <a name="dockersources"></a>DockerSources
 *Fa: Gyökér - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - DockerSources*

 1.9 hozzá.

|Elem neve|Leírás|  
|------------------|-----------------|  
|**Stats**|Arra utasítja a rendszert, hogy a Docker-tárolók statisztikák gyűjtése|  

## <a name="sinksconfig-element"></a>SinksConfig elem  
 *Fa: Gyökér - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig*

 Diagnosztikai adatok és az ezeken a helyeken hozzárendelt konfiguráció helyek listáját.  

|Elem neve|Leírás|  
|------------------|-----------------|  
|**Sink**|Lásd: leírás máshol ezen az oldalon.|  

## <a name="sink-element"></a>Fogadó-elem
 *Fa: Gyökér - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig – fogadó*

 Hozzáadva az 1.5-ös verzióját.  

 Határozza meg a helyek között a diagnosztikai adatok küldése. Ha például az Application Insights szolgáltatás.  

|Attribútum|Típus|Leírás|  
|---------------|----------|-----------------|  
|**name**|sztring|A sinkname azonosító karakterlánc.|  

|Elem|Típus|Leírás|  
|-------------|----------|-----------------|  
|**Application Insights**|sztring|Csak akkor, amikor adatokat küld az Application Insights használja. Egy olyan aktív az Application Insights-fiók, amely hozzáfér a kialakítási kulcsot tartalmaz.|  
|**csatornák**|sztring|Minden további szűréshez, amely a stream, amikor az egyik|  

## <a name="channels-element"></a>Csatornák elem  
 *Fa: Gyökér - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - fogadó - csatornák*

 Hozzáadva az 1.5-ös verzióját.  

 Határozza meg a naplózási adatokat továbbítja egy fogadó-adatfolyamokat a szűrőket.  

|Elem|Típus|Leírás|  
|-------------|----------|-----------------|  
|**Csatorna**|sztring|Lásd: leírás máshol ezen az oldalon.|  

## <a name="channel-element"></a>Csatorna elem
 *Fa: Gyökér - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - fogadó - csatornák - csatorna*

 Hozzáadva az 1.5-ös verzióját.  

 Határozza meg a helyek között a diagnosztikai adatok küldése. Ha például az Application Insights szolgáltatás.  

|Attribútumok|Típus|Leírás|  
|----------------|----------|-----------------|  
|**logLevel**|**string**|Meghatározza a naplóbejegyzéseket, amelyeket a minimális súlyossági szintet. Az alapértelmezett érték **Undefined**, az összes napló adatforgalmi. Egyéb (a legerősebbtől a legnagyobb legalább információkat) lehetséges értékek a következők **részletes**, **információk**, **figyelmeztetés**, **hiba**, és **Kritikus**.|  
|**name**|**string**|Tekintse meg a csatorna egyedi nevet|  


## <a name="privateconfig-element"></a>PrivateConfig elem
 *Fa: Gyökér - DiagnosticsConfiguration - PrivateConfig*

 1.3-as verziójában hozzáadva.  

 Optional  

 A storage-fiók (a név, a kulcs és a végpont) személyes adatait tárolja. Ez az információ a virtuális gép továbbítja, de nem kérhető le azt.  

|Gyermekelemek|Leírás|  
|--------------------|-----------------|  
|**StorageAccount**|A storage-fiók használata. A következő attribútumok szükségesek.<br /><br /> - **név** – a tárfiók nevére.<br /><br /> - **kulcs** – a tárfiók kulcsa.<br /><br /> - **végpont** – a végpontot, hogy a tárfiók eléréséhez. <br /><br /> -**sasToken** (megadhat egy tárfiókkulcs helyett SAS-tokent a privát config 1.8.1)-hozzá. Ha meg van adva, a tárfiók-kulcsot a rendszer figyelmen kívül hagyja. <br />A SAS-jogkivonat vonatkozó követelmények: <br />– Támogatja a fiók SAS-token <br />- *b*, *t* szolgáltatástípusok szükség. <br /> - *egy*, *c*, *u*, *w* engedélyekre szükség. <br /> - *c*, *o* erőforrástípusok szükség. <br /> – Csak a HTTPS-protokollt támogatja <br /> -Elindítása és a lejárati idő érvényesnek kell lennie.|  


## <a name="isenabled-element"></a>IsEnabled elem  
 *Fa: Gyökér - DiagnosticsConfiguration - IsEnabled*

 Logikai érték. Használat `true` a diagnosztika engedélyezéséhez vagy `false` a diagnosztika letiltásához.
