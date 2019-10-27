---
title: BLOB Storage használata IIS-hez és Table Storage a Azure Monitor eseményeihez | Microsoft Docs
description: A Azure Monitor beolvashatja az Azure-szolgáltatások naplóit, amelyek diagnosztikát írnak a Table Storage vagy a blob Storage-ba írt IIS-naplók számára.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 04/12/2017
ms.openlocfilehash: 8f70ecc96269783c29c566fb89bd617f034316b1
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932675"
---
# <a name="collect-azure-diagnostic-logs-from-azure-storage"></a>Azure-beli diagnosztikai naplók gyűjtése az Azure Storage-ból

A Azure Monitor a következő, a blob Storage-ba írt és az IIS-naplókat író szolgáltatások naplóit tudja olvasni:

* Service Fabric-fürtök (előzetes verzió)
* Virtual Machines
* Webes/feldolgozói szerepkörök

Ahhoz, hogy Azure Monitor adatokat gyűjtsön egy Log Analytics munkaterületre ezekhez az erőforrásokhoz, engedélyezni kell az Azure Diagnostics szolgáltatást.

Ha a diagnosztika engedélyezve van, az Azure Portal vagy a PowerShell segítségével konfigurálja a munkaterületet a naplók összegyűjtéséhez.

A Azure Diagnostics egy Azure-bővítmény, amely lehetővé teszi diagnosztikai adatok gyűjtését az Azure-ban futó feldolgozói szerepkörből, webes szerepkörből vagy virtuális gépről. Az adatok tárolása egy Azure Storage-fiókban történik, és a Azure Monitor összegyűjthetők.

Ahhoz, hogy a Azure Monitor összegyűjtse ezeket a Azure Diagnostics naplókat, a naplóknak a következő helyen kell lenniük:

| Napló típusa | Erőforrás típusa | Földrajzi egység |
| --- | --- | --- |
| IIS-naplók |Virtual Machines <br> Webes szerepkörök <br> Feldolgozói szerepkörök |wad-IIS-LogFiles (Blob Storage) |
| Syslog |Virtual Machines |LinuxsyslogVer2v0 (Table Storage) |
| Operatív események Service Fabric |Service Fabric csomópontok |WADServiceFabricSystemEventTable |
| Megbízható Actors-események Service Fabric |Service Fabric csomópontok |WADServiceFabricReliableActorEventTable |
| Megbízható szolgáltatási események Service Fabric |Service Fabric csomópontok |WADServiceFabricReliableServiceEventTable |
| Windows-eseménynaplók |Service Fabric csomópontok <br> Virtual Machines <br> Webes szerepkörök <br> Feldolgozói szerepkörök |WADWindowsEventLogsTable (Table Storage) |
| Windows ETW-naplók |Service Fabric csomópontok <br> Virtual Machines <br> Webes szerepkörök <br> Feldolgozói szerepkörök |WADETWEventTable (Table Storage) |

> [!NOTE]
> Az Azure-webhelyekről származó IIS-naplók jelenleg nem támogatottak.
>
>

A virtuális gépek esetében lehetősége van arra, hogy a [log Analytics-ügynököt](../../azure-monitor/learn/quick-collect-azurevm.md) a virtuális gépre telepítse, hogy további információkhoz lehessen jutni. Az IIS-naplók és az eseménynaplók elemzése mellett további elemzéseket is végrehajthat, például a konfiguráció módosításainak nyomon követését, az SQL-értékelést és a frissítés értékelését.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>Az Azure Diagnostics engedélyezése egy virtuális gépen az Eseménynapló és az IIS-naplók gyűjteménye számára

Az alábbi eljárással engedélyezheti az Azure Diagnostics szolgáltatást egy virtuális gépen az eseménynaplóhoz és az IIS-naplókhoz a Microsoft Azure Portal használatával.

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-portal"></a>Az Azure Diagnostics engedélyezése egy virtuális gépen a Azure Portal

1. Virtuális gép létrehozásakor telepítse a virtuálisgép-ügynököt. Ha a virtuális gép már létezik, ellenőrizze, hogy a virtuálisgép-ügynök már telepítve van-e.

   * A Azure Portal navigáljon a virtuális géphez, válassza a **választható konfiguráció**, majd a **diagnosztika** és az **állapot** beállítása **a**következőre lehetőséget.

     A befejezést követően a virtuális gépen telepítve van és fut a Azure Diagnostics bővítmény. Ennek a bővítménynek a feladata a diagnosztikai adatok összegyűjtése.
2. Engedélyezheti a figyelést, és konfigurálhatja az események naplózását egy meglévő virtuális gépen. A diagnosztika a virtuális gép szintjén engedélyezhető. A diagnosztika engedélyezéséhez, majd az eseménynaplózás konfigurálásához hajtsa végre a következő lépéseket:

   1. Válassza ki a virtuális gépet.
   2. Kattintson a **figyelés**elemre.
   3. Kattintson a **diagnosztika**elemre.
   4. Az **állapot** beállítása **a**következőre:.
   5. Válassza ki a gyűjteni kívánt diagnosztikai naplókat.
   6. Kattintson az **OK** gombra.

## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>Az Azure Diagnostics engedélyezése webes szerepkörben az IIS-napló és az események gyűjtéséhez

Tekintse meg, [hogyan engedélyezheti a diagnosztika szolgáltatást egy felhőalapú szolgáltatásban](../../cloud-services/cloud-services-dotnet-diagnostics.md) az Azure Diagnostics engedélyezésének általános lépésein. Az alábbi utasítások ezt az információt használják, és testre szabják az Log Analytics használatával való használatra.

Ha engedélyezve van az Azure Diagnostics:

* Az IIS-naplókat a rendszer alapértelmezés szerint tárolja, és a scheduledTransferPeriod-átvitel időintervallumában továbbított naplózási adatokkal.
* A Windows-eseménynaplók alapértelmezés szerint nem vihetők át.

### <a name="to-enable-diagnostics"></a>A diagnosztika engedélyezése

A Windows-eseménynaplók engedélyezéséhez, illetve a scheduledTransferPeriod módosításához konfigurálja Azure Diagnosticst az XML konfigurációs fájl (Diagnostics. wadcfg) használatával, a [4. lépés: a diagnosztika konfigurációs fájljának létrehozása és a bővítmény telepítése című témakörben](../../cloud-services/cloud-services-dotnet-diagnostics.md) leírtak szerint.

Az alábbi konfigurációs fájl az IIS-naplókat és az alkalmazás és a rendszernaplók összes eseményét gyűjti:

```xml
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

Gondoskodjon arról, hogy a ConfigurationSettings a következő példában látható módon adja meg a Storage-fiókot:

```xml
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

A **accountname** és a **AccountKey** értékek a Storage-fiók irányítópultjának Azure Portal találhatók a hozzáférési kulcsok kezelése területen. A kapcsolati karakterlánc protokolljának **https**-nek kell lennie.

Miután a rendszer alkalmazza a frissített diagnosztikai konfigurációt a felhőalapú szolgáltatásra, és az Azure Storage-ba is beírja a diagnosztikát, készen áll a Log Analytics munkaterület konfigurálására.

## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Naplók gyűjtése az Azure Storage-ban a Azure Portal használatával

A következő Azure-szolgáltatásokhoz tartozó naplók összegyűjtéséhez használhatja a Azure Portal Log Analytics munkaterület Azure Monitorban való konfigurálásához:

* Fürtök Service Fabric
* Virtual Machines
* Webes/feldolgozói szerepkörök

A Azure Portal navigáljon a Log Analytics munkaterületre, és hajtsa végre a következő feladatokat:

1. Kattintson a *Storage-fiókok naplói* elemre
2. Kattintson a *Hozzáadás* feladat
3. Válassza ki azt a Storage-fiókot, amely a diagnosztikai naplókat tartalmazza
   * Ez a fiók lehet klasszikus Storage-fiók vagy Azure Resource Manager Storage-fiók.
4. Válassza ki azt az adattípust, amelyhez naplókat szeretne gyűjteni
   * A választható lehetőségek az IIS-naplók; Események Syslog (Linux); ETW-naplók; Események Service Fabric
5. A forrás értékének automatikus kitöltése az adattípus alapján történik, és nem módosítható.
6. A konfiguráció mentéséhez kattintson az OK gombra.

Ismételje meg a 2-6. lépést a további Storage-fiókok és a munkaterületre gyűjteni kívánt adattípusok esetében.

Körülbelül 30 percen belül megtekintheti a Storage-fiók adatait a Log Analytics munkaterületen. A rendszer csak a konfiguráció után a tárolóba írt információt fogja látni. A munkaterület nem olvassa be a meglévő, a Storage-fiókból származó adatait.

> [!NOTE]
> A portál nem ellenőrzi, hogy a forrás létezik-e a Storage-fiókban, vagy új adatírás történik.
>
>

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>Azure Diagnostics engedélyezése virtuális gépen az eseménynaplóhoz és az IIS-naplókhoz a PowerShell használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A következő Azure Monitor témakörben ismertetett lépéseket követve [indexelheti az Azure diagnosticst](powershell-workspace-configuration.md#configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage) , hogy a PowerShell használatával beolvassa a Table Storage-ba írt Azure Diagnostics alkalmazást.

A Azure PowerShell használatával pontosabban megadhatja az Azure Storage-ba írt eseményeket.
További információ: [a diagnosztika engedélyezése az Azure-ban Virtual Machines](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

Az Azure Diagnostics az alábbi PowerShell-parancsfájl használatával engedélyezhető és frissíthető.
Ezt a parancsfájlt egyéni naplózási konfigurációval is használhatja.
Módosítsa a parancsfájlt a Storage-fiók, a szolgáltatásnév és a virtuális gép nevének megadásához.
A szkript parancsmagokat használ a klasszikus virtuális gépekhez.

Tekintse át a következő parancsfájl-mintát, másolja, szükség szerint módosítsa, mentse a mintát PowerShell-parancsfájlként, majd futtassa a parancsfájlt.

```powershell
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


## <a name="next-steps"></a>Következő lépések

* [Naplók és mérőszámok gyűjtése az Azure-szolgáltatásokhoz](collect-azure-metrics-logs.md) a támogatott Azure-szolgáltatásokhoz.
* [Lehetővé teszi](../../azure-monitor/insights/solutions.md) , hogy a megoldások betekintést nyújtsanak az adataiba.
* Az adatelemzéshez [használjon keresési lekérdezéseket](../../azure-monitor/log-query/log-query-overview.md) .