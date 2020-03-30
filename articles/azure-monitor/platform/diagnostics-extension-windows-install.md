---
title: A Windows Azure diagnosztikai bővítmény (WAD) telepítése és konfigurálása
description: Ismerje meg, hogyan gyűjtheti össze az Azure diagnosztikai adatait egy Azure Storage-fiókban, hogy megtekinthesse őket a számos elérhető eszköz egyikével.
services: azure-monitor
author: bwren
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.openlocfilehash: 929ab4109eb8d0e90b6c561a2135c0b7dd4205bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672259"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>A Windows Azure diagnosztikai bővítmény (WAD) telepítése és konfigurálása
Az Azure diagnostics bővítmény az Azure Monitor egy ügynöke, amely a vendég operációs rendszerből és az Azure virtuális gépek és más számítási erőforrások munkaterheléseiből gyűjtfigyelési adatokat. Ez a cikk a Windows diagnosztikai bővítmény telepítésének és konfigurálásának részleteit, valamint az adatok és az Azure Storage-fiók tárolásának leírását ismerteti.

A diagnosztikai bővítmény az Azure-ban [virtuálisgép-bővítményként](../../virtual-machines/extensions/overview.md) van megvalósítva, így ugyanazokat a telepítési beállításokat támogatja az Erőforrás-kezelő sablonok, a PowerShell és a CLI használatával. A virtuálisgép-bővítmények telepítésével és karbantartásával kapcsolatos részletekért tekintse meg [a Windows virtuálisgép-bővítmények](../../virtual-machines/extensions/features-windows.md) és -szolgáltatások című témakört.

## <a name="install-with-azure-portal"></a>Telepítés az Azure Portalon
Telepítheti és konfigurálhatja a diagnosztikai bővítményt egy adott virtuális gépen az Azure Portalon, amely felületet biztosít, szemben a konfigurációval való közvetlen együttműködéssel. Ha engedélyezi a diagnosztikai bővítményt, az automatikusan a leggyakoribb teljesítményszámlálókkal és eseményekkel rendelkező alapértelmezett konfigurációt fogja használni. Ezt az alapértelmezett konfigurációt az adott követelményeknek megfelelően módosíthatja.

> [!NOTE]
> Vannak diagnosztikai bővítmény beállításait nem konfigurálhatja az Azure Portal használatával, beleértve az adatok küldését az Azure Event Hubs. Ezekhez a beállításokhoz a többi konfigurációs módszer egyikét kell használnia.

1. Nyissa meg egy virtuális gép menüjét az Azure Portalon.
2. Kattintson **a Diagnosztikai beállítások** a virtuális gép menü **Figyelés** szakaszában.
3. Kattintson **a Vendégszintű figyelés engedélyezése elemre,** ha a diagnosztikai bővítmény még nincs engedélyezve.
4. Egy új Azure Storage-fiók jön létre a virtuális gép a neve alapján a virtuális gép erőforráscsoport neve. A virtuális gép egy másik tárfiókhoz csatolható az **Ügynök** lap kiválasztásával.

![Diagnosztikai beállítások](media/diagnostics-extension-windows-install/diagnostic-settings.png)


Az alapértelmezett konfigurációt a diagnosztikai bővítmény engedélyezése után módosíthatja. Az alábbi táblázat a különböző lapokon módosítható beállításokat ismerteti. Néhány lehetőség **rendelkezik** egy Egyéni paranccsal, amely lehetővé teszi a részletesebb konfiguráció megadását; A különböző beállításokkal kapcsolatos részleteket a [Windows diagnosztikai bővítménysémája](diagnostics-extension-schema-windows.md) tartalmazza.

| Tab | Leírás |
|:---|:---|
| Áttekintés | Megjeleníti az aktuális konfigurációt a többi lapra mutató hivatkozásokkal. |
| Teljesítményszámlálók | Válassza ki az összegyűjtő teljesítményszámlálókat és az egyes mintasebességet.  |
| Naplók | Válassza ki az összegyűjtő naplóadatokat. Ide tartoznak a Windows eseménynaplók, az IIS-naplók, a .NET alkalmazásnaplók és az ETW-események.  |
| összeomlási memóriaképek, | Engedélyezze az összeomlási memóriaképet a különböző folyamatokhoz. |
| Mosogató | Engedélyezze az adatgyűjtők számára, hogy az Azure Storage mellett adatokat is küldhessenek a célhelyekre.<br>Azure Monitor – Teljesítményadatok küldése az Azure Monitor metrikák.<br>Application Insights – adatok küldése egy Application Insights-alkalmazás. |
| Ügynök | Módosítsa az ügynök következő konfigurációját:<br>- Változtassa meg a tárfiókot.<br>- Adja meg az ügynökmaximális helyi lemezét.<br>- Konfigurálja naplók az állapota az ügynök is.|


> [!NOTE]
> Míg a diagnosztikai bővítmény konfigurációja JSON-ban vagy XML-ben formázható, az Azure Portalon végzett konfigurációk mindig JSON-ként lesznek tárolva. Ha egy másik konfigurációs módszerrel használja az XML-t, majd módosítja a konfigurációt az Azure Portalon, a beállítások JSON-ra módosulnak.

## <a name="resource-manager-template"></a>Resource Manager-sablon
Lásd: [Figyelés és diagnosztika használata Windows vm- és Azure Resource Manager-sablonokkal](../../virtual-machines/extensions/diagnostics-template.md) a diagnosztikai bővítmény Azure Resource Manager-sablonokkal való üzembe helyezéséről. 

## <a name="azure-cli-deployment"></a>Az Azure CLI üzembe helyezése
Az Azure CLI használható az Azure Diagnostics bővítmény üzembe helyezéséhez egy meglévő virtuális gép az [vm-bővítmény készlet](https://docs.microsoft.com/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-set) használatával, mint az alábbi példában. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

A védett beállítások a konfigurációs séma [PrivateConfig elemében](diagnostics-extension-schema-windows.md#privateconfig-element) vannak definiálva. A következőkben egy minimális példa egy védett beállítási fájl, amely meghatározza a tárfiókot. A személyes beállítások teljes részleteiről a [Példakonfiguráció](diagnostics-extension-schema-windows.md#privateconfig-element) című témakörben olvashat.

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```
A nyilvános beállítások a konfigurációs séma [Nyilvános elemében](diagnostics-extension-schema-windows.md#publicconfig-element) vannak definiálva. A következőkben egy minimális példa egy nyilvános beállítási fájl, amely lehetővé teszi a diagnosztikai infrastruktúra-naplók, egyetlen teljesítményszámláló és egyetlen eseménynapló gyűjtése. A nyilvános beállítások teljes részleteiről a [Példakonfiguráció](diagnostics-extension-schema-windows.md#publicconfig-element) című témakörben olvashat.

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
A PowerShell segítségével üzembe helyezheti az Azure Diagnostics bővítményt egy meglévő virtuális gépre a [Set-AzVMDiagnosticsExtension használatával,](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) mint a következő példában. 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

A privát beállítások a [PrivateConfig elemben](diagnostics-extension-schema-windows.md#privateconfig-element)vannak definiálva, míg a nyilvános beállítások a konfigurációs séma [Nyilvános elemében](diagnostics-extension-schema-windows.md#publicconfig-element) vannak definiálva. Azt is választhatja, hogy adja meg a tárfiók részleteit a Set-AzVMDiagnosticsExtension parancsmag paramétereiként, nem pedig a privát beállításokban való szerepeltetésük.

A következőkben egy minimális példa egy konfigurációs fájl, amely lehetővé teszi a diagnosztikai infrastruktúra naplók gyűjtése, egyetlen teljesítményszámláló és egyetlen eseménynapló. A személyes és nyilvános beállítások teljes részleteiről a [Példakonfiguráció](diagnostics-extension-schema-windows.md#publicconfig-element) című témakörben olvashat. 

```JSON
{
    "PublicConfig": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                "overallQuotaInMB": 10000
            },
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

Lásd: [A PowerShell használata az Azure Diagnosztika engedélyezéséhez Windows rendszert futtató virtuális gépeken.](../../virtual-machines/extensions/diagnostics-windows.md)

## <a name="data-storage"></a>Adattárolás
Az alábbi táblázat a diagnosztikai bővítményből gyűjtött különböző típusú adatokat, valamint azt sorolja fel, hogy táblában vagy blobként vannak-e tárolva. A táblákban tárolt adatok is tárolhatók blobokban a [storagetype beállítástól](diagnostics-extension-schema-windows.md#publicconfig-element) függően a nyilvános konfigurációban.


| Adatok | Tárolási típus | Leírás |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | Tábla | Diagnosztikai monitor és konfigurációs változások. |
| WADDirectoriesTábla | Tábla | A diagnosztikai figyelő által figyelt könyvtárak.  Ide tartoznak az IIS-naplók, az IIS sikertelen kérelemnaplói és az egyéni könyvtárak.  A blob naplófájl helye a Tároló mezőben van megadva, és a blob neve a RelativePath mezőben van megadva.  Az AbsolutePath mező jelzi a helyét és nevét a fájl, ahogy létezett az Azure virtuális gépen. |
| WadLogsTábla | Tábla | A nyomfigyelővel kódba írt naplók. |
| WADPerformanceCountersTábla | Tábla | Teljesítményszámlálók. |
| WADWindowsEventLogsTable | Tábla | Windows eseménynaplók. |
| wad-iis-failedreqlogfiles | Blob | Az IIS sikertelen kérelmek naplóiból származó információkat tartalmazza. |
| wad-iis-logfiles | Blob | Az IIS-naplókkal kapcsolatos információkat tartalmaz. |
| "egyéni" | Blob | A diagnosztikai figyelő által figyelt könyvtárak konfigurálásán alapuló egyéni tároló.  A blobtároló neve a WADDirectoriesTable táblában lesz megadva. |

## <a name="tools-to-view-diagnostic-data"></a>A diagnosztikai adatok megtekintésének eszközei
Számos eszköz áll rendelkezésre az adatok tárolásra való átvitele utáni megtekintéséhez. Példa:

* Server Explorer a Visual Studio – Ha telepítette az Azure Tools for Microsoft Visual Studio, használhatja az Azure Storage-csomópont a Server Explorer megtekintheti az írásvédett blob és a táblázat adatait az Azure storage-fiókok. A helyi táremulátorfiókból és az Azure-hoz létrehozott tárfiókokból is megjelenítheti az adatokat. További információt a [Tárolási erőforrások böngészése és kezelése a Kiszolgálókezelővel](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)című témakörben talál.
* [A Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) egy önálló alkalmazás, amely lehetővé teszi, hogy könnyedén dolgozzon az Azure Storage-adatokkal Windows, OSX és Linux rendszeren.
* [Az Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) tartalmazza az Azure Diagnostics Managert, amely lehetővé teszi az Azure-ban futó alkalmazások által gyűjtött diagnosztikai adatok megtekintését, letöltését és kezelését.

## <a name="next-steps"></a>Következő lépések
- A figyelési adatok Azure-eseményközpontokba történő továbbításáról a [Windows Azure diagnosztikai bővítményéből az Event Hubs-ba](diagnostics-extension-stream-event-hubs.md) című témakörben talál.
