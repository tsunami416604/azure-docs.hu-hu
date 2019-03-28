---
title: A blob storage használata az IIS és a table storage-események az Azure Monitor |} A Microsoft Docs
description: Az Azure Monitor olvashatja a naplókban az Azure-szolgáltatások diagnosztikai a table storage-be írni vagy az IIS-napló írása blobtárolókba.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: bf444752-ecc1-4306-9489-c29cb37d6045
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/12/2017
ms.author: magoedte
ms.openlocfilehash: 35befe7122f493998d0d91c2721e6013e057fed3
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58540601"
---
# <a name="use-azure-blob-storage-for-iis-and-azure-table-storage-for-events-with-azure-monitor"></a>Az Azure blob storage használata az IIS és az Azure table storage-események az Azure Monitor szolgáltatással

Az Azure Monitor a naplók a következő szolgáltatásokat, hogy az írási diagnosztika a table storage vagy az IIS-napló írása blobtárolókba olvashatja:

* A Service Fabric-fürtök (előzetes verzió)
* Virtuális gépek
* Webes/feldolgozói szerepkörök

Az Azure Monitor gyűjtheti be ezekhez az erőforrásokhoz a Log Analytics-munkaterület, mielőtt az Azure diagnostics engedélyezve kell lennie.

Után diagnosztika engedélyezve van, használhatja az Azure Portalon, vagy PowerShell beállítása a munkaterületen, a naplók gyűjtését.

Az Azure Diagnostics az Azure kiterjesztése, amely lehetővé teszi, hogy egy feldolgozói szerepkör, a webes szerepkör vagy az Azure-ban futó virtuális gép diagnosztikai adatainak összegyűjtése. Az adatok Azure storage-fiók tárolva van, és ezután gyűjthetők a Azure Monitor.

Az Azure Monitor ezeket az Azure diagnosztikai naplók gyűjtésére a naplók a következő helyeken kell lennie:

| Napló típusa | Erőforrás típusa | Hely |
| --- | --- | --- |
| IIS-naplók |Virtuális gépek <br> Webes szerepkörök <br> Feldolgozói szerepkörök |WAD-az iis-naplófájlok (Blob Storage) |
| Rendszernapló |Virtuális gépek |LinuxsyslogVer2v0 (a Table Storage) |
| Service Fabric-működési események |A Service Fabric-csomópont |WADServiceFabricSystemEventTable |
| Service Fabric Reliable Actors-események |A Service Fabric-csomópont |WADServiceFabricReliableActorEventTable |
| Service Fabric Reliable Services-események |A Service Fabric-csomópont |WADServiceFabricReliableServiceEventTable |
| Windows-eseménynaplók |A Service Fabric-csomópont <br> Virtuális gépek <br> Webes szerepkörök <br> Feldolgozói szerepkörök |WADWindowsEventLogsTable (a Table Storage) |
| Windows ETW-naplók |A Service Fabric-csomópont <br> Virtuális gépek <br> Webes szerepkörök <br> Feldolgozói szerepkörök |WADETWEventTable (a Table Storage) |

> [!NOTE]
> Az Azure-webhelyek IIS-naplók jelenleg nem támogatottak.
>
>

Virtuális gépek telepítésének lehetősége van a [Log Analytics-ügynököket](../../azure-monitor/learn/quick-collect-azurevm.md) további elemzések engedélyezése a virtuális géphez. Mellett elemzése az IIS-naplók és -eseménynaplók, többek között a konfigurációs változások követése SQL-felmérés és a frissítésfelmérés további elemzést végezhet.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>Engedélyezze az Azure diagnostics egy virtuális gépen az Eseménynapló és az IIS naplót
Az alábbi eljárás segítségével engedélyezze az Azure diagnostics Eseménynapló és az IIS naplógyűjtést a Microsoft Azure Portalon a virtuális gépen.

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-portal"></a>Ahhoz, hogy az Azure diagnostics egy virtuális gépen az Azure portal használatával
1. Virtuálisgép-ügynök telepítése egy virtuális gép létrehozásakor. Ha a virtuális gép már létezik, győződjön meg arról, hogy a Virtuálisgép-ügynök már telepítve van.

   * Az Azure Portalon lépjen a virtuális gép, jelölje be **opcionális konfigurációs**, majd **diagnosztikai** és **állapot** való **a** .

     Befejeztével a virtuális gépen az Azure Diagnostics bővítmény telepítenie és futtatnia. Ez a bővítmény felelős a diagnosztikai adatok gyűjtése.
2. Engedélyezze a monitorozást és a egy meglévő virtuális Gépet az eseménynaplózás konfigurálásához. Diagnosztika a virtuális gép szintjén engedélyezhető. Diagnosztika engedélyezése, és ezután az eseménynaplózás konfigurálásához, hajtsa végre az alábbi lépéseket:

   1. Válassza ki a virtuális gépet.
   2. Kattintson a **figyelési**.
   3. Kattintson a **diagnosztikai**.
   4. Állítsa be a **állapot** való **ON**.
   5. Válassza ki az egyes diagnosztikai napló, amely szeretne gyűjteni.
   6. Kattintson az **OK** gombra.

## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>Engedélyezze az Azure diagnostics egy webes szerepkörben, az IIS-napló- és esemény gyűjtemény
Tekintse meg [hogyan való engedélyezése diagnosztikai felhőszolgáltatásban](../../cloud-services/cloud-services-dotnet-diagnostics.md) általános lépései az Azure diagnostics engedélyezése. Az alábbi utasítások ezzel az információval, és testre szabni a Log Analytics segítségével.

Az Azure diagnostics segítségével engedélyezve:

* IIS-naplók alapértelmezés szerint a napló adatátvitelre scheduledTransferPeriod átviteli időközönként tárolódnak.
* Alapértelmezés szerint a Windows-eseménynaplók nem kerülnek.

### <a name="to-enable-diagnostics"></a>Diagnosztika engedélyezése
Engedélyezéséhez a Windows-eseménynaplók, vagy módosíthatja a scheduledTransferPeriod, az Azure Diagnostics konfigurálása az XML konfigurációs fájl (diagnostics.wadcfg), használatával, ahogyan [4. lépés: A diagnosztika konfigurációs fájl létrehozása és a bővítmény telepítése](../../cloud-services/cloud-services-dotnet-diagnostics.md)

A következő példa konfigurációs fájlt az alkalmazás- és rendszernaplókat IIS-naplók és minden eseményt gyűjti:

```
    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">

      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>

      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>

    </DiagnosticMonitorConfiguration>
```

Győződjön meg arról, hogy a ConfigurationSettings határozza meg a storage-fiók, az alábbi példában látható módon:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

A **AccountName** és **AccountKey** értékek találhatók, a tárolási fiók irányítópultján, a Tárelérési kulcsok kezelése az Azure Portalon. A protokoll, a kapcsolati karakterlánc lehet **https**.

A frissített diagnosztikai konfiguráció alkalmazása a felhőszolgáltatáshoz, és a diagnosztika az Azure Storage-írja, majd készen áll a Log Analytics-munkaterület konfigurálása.

## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Naplók gyűjtése az Azure Storage-ból az Azure portal használatával
Az Azure portal segítségével konfigurálhatja a Log Analytics-munkaterületet az Azure monitorban a naplók a következő Azure-szolgáltatások:

* Service Fabric-fürtök
* Virtuális gépek
* Webes/feldolgozói szerepkörök

Az Azure Portalon keresse meg a Log Analytics-munkaterületet, és hajtsa végre az alábbi feladatokat:

1. Kattintson a *tárfióknaplók*
2. Kattintson a *Hozzáadás* feladat
3. Válassza ki a tárfiókot, amely tartalmazza a diagnosztikai naplók
   * Ez a fiók vagy klasszikus tárfiók, vagy egy Azure Resource Manager-tárfiókot is lehet.
4. Válassza ki a gyűjtendő naplók
   * A választási lehetőségek: az IIS-naplók; Az eseményeket; Syslog (Linux); Protokoly trasování událostí; Service Fabric-események
5. A forrás értéke automatikusan adattípus alapján van feltöltve, és nem módosítható
6. Kattintson az OK gombra a konfiguráció mentése

További tárfiókok és a munkaterületre gyűjtendő adattípusokat ismételje meg a 2 – 6.

Körülbelül 30 percet, az Ön láthatja az adatokat a Log Analytics-munkaterületet a tárfiókból. A konfiguráció alkalmazása után tárolási írt adatok csak megjelennek. A munkaterület nem olvassa a már meglévő adatok a storage-fiókot.

> [!NOTE]
> A portál nem ellenőrzi, hogy a forrás létezik-e a tárfiókban, vagy ha új adatokat ír.
>
>

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>Engedélyezze az Azure diagnostics egy virtuális gépen az Eseménynapló és az IIS naplót PowerShell-lel

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Szereplő lépések segítségével [indexelése az Azure diagnostics konfigurálása az Azure Monitor](powershell-workspace-configuration.md#configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage) írt a table storage, az Azure diagnostics olvasni a PowerShell használatával.

Azure PowerShell használatával pontosabban megadhatja az Azure Storage írt események.
További információkért lásd: [Diagnosztikának az Azure Virtual machines gépeken](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

Engedélyezze, és frissítse az Azure diagnostics a következő PowerShell-parancsfájl használatával.
Ez a szkript egy egyéni naplózási konfigurációt is használhatja.
Módosítsa a parancsfájlt úgy, hogy a storage-fiók, a szolgáltatás neve, valamint a virtuális gép neve.
A parancsfájl parancsmagok a klasszikus virtuális gépek számára.

Tekintse át a következő példaszkript, másolja, igény szerint módosítsa, mentse a minta egy olyan PowerShell-parancsfájlt, és ezután futtassa a szkriptet.

```
    #Connect to Azure
    Add-AzureAccount

    # settings to change:
    $wad_storage_account_name = "myStorageAccount"
    $service_name = "myService"
    $vm_name = "myVM"

    #Construct Azure Diagnostics public config and convert to config format

    # Collect just system error events:
    $wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

    $wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
    $wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

    #Construct Azure diagnostics private config

    $wad_storage_account_key = (Get-AzStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

    #Enable Diagnostics Extension for Virtual Machine

    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
```


## <a name="next-steps"></a>További lépések
* [Naplók és mérőszámok az Azure-szolgáltatásokhoz gyűjtése](collect-azure-metrics-logs.md) támogatott Azure-szolgáltatásokat.
* [Megoldások engedélyezése](../../azure-monitor/insights/solutions.md) nyújt betekintést az adatokat.
* [Keresési lekérdezéseket használnak](../../azure-monitor/log-query/log-query-overview.md) az adatok elemzéséhez.
