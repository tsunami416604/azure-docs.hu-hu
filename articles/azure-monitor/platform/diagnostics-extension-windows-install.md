---
title: A Windows Azure Diagnostics bővítmény (WAD) telepítése és konfigurálása
description: Ismerje meg, hogyan gyűjthet Azure-diagnosztikai adatokat egy Azure Storage-fiókban, így megtekintheti azt a számos elérhető eszköz egyikével.
services: azure-monitor
author: bwren
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.openlocfilehash: dd18fd484ac456f0c38cd6d9b73a2395a08ad5d0
ms.sourcegitcommit: d815163a1359f0df6ebfbfe985566d4951e38135
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2020
ms.locfileid: "82883107"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>A Windows Azure Diagnostics bővítmény (WAD) telepítése és konfigurálása
Az Azure Diagnostics bővítmény olyan Azure Monitor ügynöke, amely a vendég operációs rendszerből és az Azure-beli virtuális gépek és egyéb számítási erőforrások munkaterhelésével kapcsolatos figyelési adatokat gyűjt. Ez a cikk részletesen ismerteti a Windows diagnosztikai bővítmény telepítését és konfigurálását, valamint az adatok tárolásának és az Azure Storage-fiókban tárolt információk leírásának ismertetését.

A diagnosztikai bővítmény a [virtuálisgép-bővítményként](../../virtual-machines/extensions/overview.md) van implementálva az Azure-ban, így ugyanazokat a telepítési lehetőségeket támogatja a Resource Manager-sablonok, a PowerShell és a parancssori felület használatával. A virtuálisgép-bővítmények telepítésével és karbantartásával kapcsolatos részletekért lásd: [virtuálisgép-bővítmények és-szolgáltatások a Windows](../../virtual-machines/extensions/features-windows.md) rendszerhez.

## <a name="install-with-azure-portal"></a>Telepítés Azure Portal
A diagnosztikai bővítményt telepítheti és konfigurálhatja egy különálló virtuális gépen a Azure Portal, amely egy felületet biztosít, amely nem működik közvetlenül a konfigurációval. A diagnosztikai bővítmény engedélyezésekor a rendszer automatikusan alapértelmezett konfigurációt használ a leggyakoribb teljesítményszámlálók és események használatával. Ezt az alapértelmezett konfigurációt a konkrét követelmények szerint módosíthatja.

> [!NOTE]
> A diagnosztikai bővítmény beállításai nem konfigurálhatók a Azure Portal használatával, beleértve az adatok küldését az Azure Event Hubsba. Ezekhez a beállításokhoz a többi konfigurációs módszer egyikét kell használnia.

1. Nyissa meg a virtuális gép menüjét a Azure Portal.
2. A virtuális gép menü **figyelés** szakaszában kattintson a **diagnosztikai beállítások** elemre.
3. Kattintson a **vendég szintű figyelés engedélyezése** lehetőségre, ha a diagnosztikai bővítmény még nincs engedélyezve.
4. Létrejön egy új Azure Storage-fiók a virtuális géphez, amelynek a neve a virtuális gép erőforráscsoport neve alapján lesz létrehozva. A virtuális gépet egy másik Storage-fiókhoz is csatlakoztathatja az **ügynök** lapra kattintva.

![Diagnosztikai beállítások](media/diagnostics-extension-windows-install/diagnostic-settings.png)


A diagnosztikai bővítmény engedélyezése után módosíthatja az alapértelmezett konfigurációt. A következő táblázat a különböző lapokon módosítható beállításokat ismerteti. Egyes beállításokban van egy **Egyéni** parancs, amely lehetővé teszi részletesebb konfiguráció megadását; a különböző beállításokkal kapcsolatos részletekért tekintse meg a [Windows Diagnostics bővítmény sémáját](diagnostics-extension-schema-windows.md) .

| Tab | Leírás |
|:---|:---|
| Áttekintés | Megjeleníti az aktuális konfigurációt a többi lapra mutató hivatkozásokkal. |
| Teljesítményszámlálók | Válassza ki a gyűjteni kívánt teljesítményszámlálókat és a mintavételezési arányt.  |
| Naplók | Válassza ki a gyűjteni kívánt naplózási adatokat. Ez magában foglalja a Windows-eseménynaplókat, az IIS-naplókat, a .NET-alkalmazások naplóit és a ETW eseményeket.  |
| összeomlási memóriaképek, | Összeomlási memóriakép engedélyezése különböző folyamatokhoz. |
| Fogadóként | Engedélyezheti az adatnyelők számára az adatok célhelyekre való küldését az Azure Storage mellett.<br>Azure Monitor – teljesítményadatokat küld Azure Monitor mérőszámoknak.<br>Application Insights – az adatküldés egy Application Insights alkalmazásba. |
| Ügynök | Módosítsa az ügynök következő konfigurációját:<br>– Módosítsa a Storage-fiókot.<br>-Az ügynökhöz használt maximális helyi lemez megadása.<br>-Konfigurálja a naplókat az ügynök állapotához.|


> [!NOTE]
> Míg a diagnosztika bővítmény konfigurációja a JSON-ban vagy XML-ben formázható, a Azure Portalban végzett konfigurálás mindig JSON-ként lesz tárolva. Ha az XML-t egy másik konfigurációs módszerrel használja, majd a Azure Portal módosítja a konfigurációt, a rendszer a beállításokat a JSON-ra cseréli.

## <a name="resource-manager-template"></a>Resource Manager-sablon
Lásd: [figyelés és diagnosztika használata Windows rendszerű virtuális gépekkel és Azure Resource Manager sablonok](../../virtual-machines/extensions/diagnostics-template.md) a diagnosztikai bővítmény üzembe helyezéséhez Azure Resource Manager-sablonokkal. 

## <a name="azure-cli-deployment"></a>Azure CLI üzembe helyezése
Az Azure CLI használatával a Azure Diagnostics-bővítményt telepítheti egy meglévő virtuális gépre az az [VM Extension set](https://docs.microsoft.com/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-set) paranccsal az alábbi példában látható módon. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

A védett beállítások a konfigurációs séma [PrivateConfig elemében](diagnostics-extension-schema-windows.md#privateconfig-element) vannak meghatározva. A következő példa egy, a Storage-fiókot meghatározó védett beállítási fájl minimális példáját szemlélteti. A privát beállítások részletes ismertetését lásd: [példa konfiguráció](diagnostics-extension-schema-windows.md#privateconfig-element) .

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```
A nyilvános beállítások a konfigurációs séma [nyilvános elemében](diagnostics-extension-schema-windows.md#publicconfig-element) vannak meghatározva. A következő példa egy olyan nyilvános beállítási fájlt mutat be, amely lehetővé teszi a diagnosztikai infrastruktúra naplói, egyetlen teljesítményszámláló és egyetlen Eseménynapló gyűjtését. A nyilvános beállítások részletes ismertetését lásd: [példa konfiguráció](diagnostics-extension-schema-windows.md#publicconfig-element) .

```JSON
{
  "StorageAccount": "mystorageaccount",
  "WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": 5120,
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": [
          {
            "counterSpecifier": "\\Processor Information(_Total)\\% Processor Time",
            "unit": "Percent",
            "sampleRate": "PT60S"
          }
        ]
      },
      "WindowsEventLog": {
        "scheduledTransferPeriod": "PT1M",
        "DataSource": [
          {
            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
          }
        ]
      }
    }
  }
}
```



## <a name="powershell-deployment"></a>PowerShell-telepítés
A PowerShell segítségével az alábbi példában látható módon telepítheti az Azure Diagnostics-bővítményt egy meglévő virtuális gépre a [set-AzVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) használatával. 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

A privát beállítások a [PrivateConfig elemben](diagnostics-extension-schema-windows.md#privateconfig-element)vannak definiálva, míg a nyilvános beállítások a konfigurációs séma [nyilvános elemében](diagnostics-extension-schema-windows.md#publicconfig-element) vannak meghatározva. Dönthet úgy is, hogy a Storage-fiók részleteit a set-AzVMDiagnosticsExtension parancsmag paraméterei adja meg, nem pedig a privát beállítások között.

A következő példa egy olyan konfigurációs fájl minimális példáját mutatja be, amely lehetővé teszi a diagnosztikai infrastruktúra naplói, egyetlen teljesítményszámláló és egyetlen Eseménynapló gyűjtését. A privát és a nyilvános beállítások részletes ismertetését lásd: [példa konfiguráció](diagnostics-extension-schema-windows.md#publicconfig-element) . 

```JSON
{
    "PublicConfig": {
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
                            "sampleRate": "PT3M",
                            "unit": "percent"
                        }
                    ]
                },
                "WindowsEventLog": {
                    "scheduledTransferPeriod": "PT1M",
                        "DataSource": [
                        {
                            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                        }
                    ]
                }
            }
        },
        "StorageAccount": "mystorageaccount",
        "StorageType": "TableAndBlob"
    },
    "PrivateConfig": {
        "storageAccountName": "mystorageaccount",
        "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
    }
}
```

Lásd még: a [PowerShell használata a Azure Diagnostics Windows rendszerű virtuális gépeken való engedélyezéséhez](../../virtual-machines/extensions/diagnostics-windows.md).

## <a name="data-storage"></a>Adattárolás
A következő táblázat felsorolja a diagnosztikai bővítményből összegyűjtött különböző típusú adatokat, valamint azt, hogy táblázatként vagy blobként vannak tárolva. A táblákban tárolt adattárolók a nyilvános konfiguráció [StorageType beállításától](diagnostics-extension-schema-windows.md#publicconfig-element) függően a blobokban is tárolhatók.


| Adatok | Tárolási típus | Leírás |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | Tábla | A diagnosztikai figyelő és a konfiguráció módosításai. |
| WADDirectoriesTable | Tábla | A diagnosztikai figyelő által figyelt címtárak.  Ez magában foglalja az IIS-naplókat, az IIS sikertelen kérelmek naplóit és az egyéni címtárakat.  A blob naplófájljának helye a tároló mezőben van megadva, és a blob neve a RelativePath mezőben található.  A AbsolutePath mező a fájl helyét és nevét jelzi, ahogy az Azure-beli virtuális gépen létezett. |
| WadLogsTable | Tábla | A nyomkövetési figyelő használatával kódban írt naplók. |
| WADPerformanceCountersTable | Tábla | Teljesítményszámlálók. |
| WADWindowsEventLogsTable | Tábla | Windows-eseménynaplók. |
| wad-IIS-failedreqlogfiles | Blob | Az IIS sikertelen kérelmek naplóiból származó információkat tartalmaz. |
| wad-IIS-LogFiles | Blob | AZ IIS-naplókkal kapcsolatos információkat tartalmaz. |
| Egyéni | Blob | Egyéni tároló, amely a diagnosztikai figyelő által figyelt könyvtárak konfigurálásán alapul.  A blob-tároló neve a WADDirectoriesTable-ben lesz meghatározva. |

## <a name="tools-to-view-diagnostic-data"></a>Eszközök a diagnosztikai adatgyűjtés megtekintéséhez
Több eszköz is elérhető az adattárolásra a Storage szolgáltatásba való átvitelük után. Például:

* Kiszolgálókezelő a Visual Studióban – ha telepítette a Microsoft Visual studióhoz készült Azure-eszközöket, a Server Explorerben az Azure Storage csomópont használatával megtekintheti az Azure Storage-fiókok írásvédett blob-és táblázat-adatait. A helyi Storage Emulator-fiókból és az Azure-hoz létrehozott Storage-fiókoktól származó adatok is megjeleníthetők. További információ: [Storage-erőforrások tallózása és kezelése a Server Explorerben](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* A [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) egy önálló alkalmazás, amely lehetővé teszi az Azure Storage-alapú adattárolást Windows, OSX és Linux rendszeren.
* Az [azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) tartalmaz Azure Diagnostics kezelőt, amely lehetővé teszi az Azure-on futó alkalmazások által gyűjtött diagnosztikai adatok megtekintését, letöltését és kezelését.

## <a name="next-steps"></a>Következő lépések
- A monitorozási adatok Azure-Event Hubs való továbbításával kapcsolatos részletekért tekintse meg az [adatok küldése a Windows Azure diagnostics Event Hubs bővítményből](diagnostics-extension-stream-event-hubs.md) című témakört.
