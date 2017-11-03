---
title: "A blob storage használata az Azure Naplóelemzés eseményeket az IIS és a tábla tárolási |} Microsoft Docs"
description: "A Naplóelemzési olvashatja a naplók az Azure-szolgáltatásokat, hogy az írási diagnostics meg tudja a table storage vagy a blob-tároló írni az IIS-naplókba."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: bf444752-ecc1-4306-9489-c29cb37d6045
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 459ef90ca1d76bada6565bfefd7b4bd1086197d5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-blob-storage-for-iis-and-azure-table-storage-for-events-with-log-analytics"></a>Az IIS és az Azure Naplóelemzés eseményeit a table storage Azure blob storage használata

A Naplóelemzési a keresse meg a következő szolgáltatásokat, hogy az írási diagnostics meg tudja a table storage vagy a blob-tároló írni az IIS-napló olvashatja:

* A Service Fabric-fürtök (előzetes verzió)
* Virtuális gépek
* Webes vagy feldolgozói szerepkörök

A Naplóelemzési ezekhez az erőforrásokhoz tartozó adatokat gyűjthet, mielőtt Azure diagnostics engedélyezve kell lennie.

Ha diagnosztikai engedélyezve vannak, használhatja az Azure-portálon, vagy PowerShell Naplóelemzési a naplók gyűjtéséhez konfigurálja.

Az Azure Diagnostics egy Azure-bővítményt, amely lehetővé teszi a feldolgozói szerepkör, a webes szerepkör vagy az Azure-beli virtuális gép diagnosztikai adatainak összegyűjtése. Az adatok Azure storage-fiók tárolva van, és ezután gyűjthetők a Naplóelemzési.

A Naplóelemzési, ezek az Azure diagnosztikai naplók gyűjtésére a naplók kell a következő helyeken:

| Napló típusa | Erőforrás típusa | Hely |
| --- | --- | --- |
| IIS-naplók |Virtuális gépek <br> Webes szerepkörök <br> Feldolgozói szerepkörök |üvegvatta-az iis-naplófájlok (Blob-tároló) |
| Rendszernapló |Virtuális gépek |LinuxsyslogVer2v0 (Table Storage) |
| Service Fabric működési események |Service Fabric-csomópontokon |WADServiceFabricSystemEventTable |
| Service Fabric megbízható szereplő események |Service Fabric-csomópontokon |WADServiceFabricReliableActorEventTable |
| Service Fabric megbízható eseményei |Service Fabric-csomópontokon |WADServiceFabricReliableServiceEventTable |
| Windows-Eseménynapló |Service Fabric-csomópontokon <br> Virtuális gépek <br> Webes szerepkörök <br> Feldolgozói szerepkörök |WADWindowsEventLogsTable (Table-tároló) |
| A Windows ETW-naplók |Service Fabric-csomópontokon <br> Virtuális gépek <br> Webes szerepkörök <br> Feldolgozói szerepkörök |WADETWEventTable (Table-tároló) |

> [!NOTE]
> Az Azure-webhelyek IIS-napló jelenleg nem támogatottak.
>
>

A virtuális gépek, lehetősége van telepíteni a [Naplóelemzési ügynök](log-analytics-azure-vm-extension.md) ahhoz, hogy további betekintést nyerjen a virtuális géppé. Nem csak az IIS naplókat és az eseménynaplók elemzése, többek között a konfigurációs változások követését, az SQL értékelése és a frissítések értékelését további elemzés végezheti el.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>A virtuális gép Azure diagnostics engedélyezése az eseménynaplót, és az IIS naplózása gyűjtemény
A következő eljárással engedélyezheti a virtuális gépen a Microsoft Azure portál használatával Eseménynapló és az IIS-napló gyűjtemény Azure diagnostics.

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-portal"></a>Ahhoz, hogy a virtuális gépen, és az Azure portál Azure diagnostics
1. A Virtuálisgép-ügynök telepítése a virtuális gép létrehozásakor. Ha a virtuális gép már létezik, győződjön meg arról, hogy a Virtuálisgép-ügynök telepítve van.

   * A virtuális gépet, jelölje be az Azure-portálon lépjen **opcionális konfigurációs**, majd **diagnosztika** és **állapota** a **a**.

     Létrehozása után a virtuális gép kiterjesztése az Azure Diagnostics telepítve és fut. A bővítmény felelős a diagnosztikai adatainak összegyűjtése.
2. Engedélyezze a megfigyelést és a meglévő virtuális az eseménynaplózás konfigurálásához. Diagnosztika a virtuális gép szintjén engedélyezhető. Diagnosztika engedélyezése, majd válassza az eseménynaplózás, a következő lépésekkel:

   1. Válassza ki a virtuális Gépet.
   2. Kattintson a **figyelési**.
   3. Kattintson a **diagnosztika**.
   4. Állítsa be a **állapot** való **ON**.
   5. Válassza ki a gyűjteni kívánt diagnosztikai naplófájlok.
   6. Kattintson az **OK** gombra.

## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>Az IIS-napló és a esemény gyűjteményhez webes szerepkörrel rendelkező Azure diagnostics engedélyezése
Tekintse meg [hogyan való engedélyezése diagnosztikai felhőszolgáltatásban](../cloud-services/cloud-services-dotnet-diagnostics.md) általános lépései az Azure diagnostics engedélyezése. Az alábbi utasításokat ezekkel az adatokkal, és szabja testre a Log Analyticshez való használatra.

Az Azure diagnostics engedélyezése:

* IIS-napló alapértelmezés szerint a scheduledTransferPeriod átviteli időközönként átvitt naplóadatokat tárolja.
* Alapértelmezés szerint a Windows-Eseménynapló nem kerülnek.

### <a name="to-enable-diagnostics"></a>Diagnosztika engedélyezése
Engedélyezése a Windows eseménynaplóiban keresse meg vagy módosíthatja a scheduledTransferPeriod, állítsa be az XML konfigurációs fájl (diagnostics.wadcfg), Azure-diagnosztika látható módon [4. lépés: a diagnosztika konfigurációs fájl létrehozása és a kiterjesztés telepítése](../cloud-services/cloud-services-dotnet-diagnostics.md)

A következő példa konfigurációs fájlt az IIS naplókat és az összes eseményt gyűjti az alkalmazás és rendszer kategóriájának bejegyzéseit:

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

A **AccountName** és **AccountKey** értékek találhatók, a tárolási fiók Tárelérési kulcsok kezelése irányítópulton Azure-portálon. A kapcsolati karakterlánc protokollja kell **https**.

Miután a frissített diagnosztikai konfiguráció alkalmazása a felhőalapú szolgáltatáshoz és diagnosztika ír, az Azure-tárhelyre, majd készen áll Naplóelemzési konfigurálása.

## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Az Azure portál segítségével naplógyűjtéshez Azure Storage-ból
Az Azure portál segítségével konfigurálása az Azure-szolgáltatásokat a naplók összegyűjtésére Naplóelemzési:

* Service Fabric-fürtök
* Virtuális gépek
* Webes vagy feldolgozói szerepkörök

Az Azure portálon nyissa meg a Naplóelemzési munkaterületet, és hajtsa végre a következő feladatokat:

1. Kattintson a *tárfiókok naplók*
2. Kattintson a *Hozzáadás* feladat
3. Válassza ki a tárfiókot, amely tartalmazza a diagnosztikai naplók
   * Ez a fiók lehet egy hagyományos tárolási fiók vagy egy Azure Resource Manager storage-fiók
4. Válassza ki az adattípust szeretne gyűjteni a naplókat
   * A választási lehetőségek: IIS-naplóit; Az eseményeket; Syslog (Linux); ETW naplók; Service Fabric-események
5. Forrás értéket automatikusan kitölti a rendszer az adatok alapján, és nem módosítható
6. Kattintson az OK gombra a konfiguráció mentéséhez

A további tárfiókok és a Naplóelemzési gyűjtéséhez kívánt adattípusok ismételje meg a 2 – 6.

A körülbelül 30 percet is a a Naplóelemzési tárkonfigurációt adatok megtekintéséhez. Miután a konfiguráció alkalmazása Storage írt adatok csak akkor jelenik meg. A Naplóelemzési nem olvassa a már létező adatokat a tárfiók.

> [!NOTE]
> A portál nem ellenőrzi, hogy a forrás létezik-e a tárfiókban lévő, vagy ha új adatok írása.
>
>

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>A virtuális gép Azure diagnostics engedélyezése az eseménynaplót, és IIS töltsék PowerShell használatával
Az alábbi témakörben található lépésekkel [Naplóelemzési konfigurálása az Azure diagnostics indexelésre](log-analytics-powershell-workspace-configuration.md#configuring-log-analytics-to-index-azure-diagnostics) lehet olvasni a table storage írt Azure diagnostics a PowerShell használatával.

Azure PowerShell használatával pontosabban megadhatja az Azure Storage írt események.
További információkért lásd: [diagnosztika engedélyezésével az Azure virtuális gépek](../virtual-machines-dotnet-diagnostics.md).

Engedélyezi, és frissítse az Azure diagnostics a következő PowerShell-parancsfájl használatával.
Ez a parancsfájl egy egyéni naplózási konfiguráció is használható.
Módosítsa a parancsfájlt úgy állítsa be a tárfiók, a szolgáltatás neve és a virtuális gép nevét.
A parancsfájl parancsmagok a klasszikus virtuális gépekhez.

Tekintse át az alábbi parancsfájl-mintában, másolja, szükség szerint módosítsa a minta elmentse egy olyan PowerShell-parancsfájlt, és futtassa a parancsfájlt.

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

    $wad_storage_account_key = (Get-AzureStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

    #Enable Diagnostics Extension for Virtual Machine

    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
```


## <a name="next-steps"></a>Következő lépések
* [Gyűjteni a naplókat és az Azure-szolgáltatások metrikáját](log-analytics-azure-storage.md) támogatott Azure-szolgáltatásokhoz.
* [Megoldások](log-analytics-add-solutions.md) betekintést az adatokba, így.
* [Használja a keresési lekérdezések](log-analytics-log-searches.md) az adatok elemzésére.
