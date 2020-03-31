---
title: Figyelési & diagnosztika hozzáadása azure-beli virtuális géphez
description: Azure Resource Manager-sablon használatával hozzon létre egy új Windows virtuális gépet az Azure diagnosztikai bővítményével.
services: virtual-machines-windows
documentationcenter: ''
author: sbtron
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 8cde8fe7-977b-43d2-be74-ad46dc946058
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/31/2017
ms.author: saurabh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2490c3de60e0deac6a1a4ddc5abc95cb46e240b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073842"
---
# <a name="use-monitoring-and-diagnostics-with-a-windows-vm-and-azure-resource-manager-templates"></a>Figyelés és diagnosztika használata Windows virtuális gép- és Azure Resource Manager-sablonokkal
Az Azure Diagnostics Extension a figyelési és diagnosztikai képességek egy Windows-alapú Azure virtuális gép. Ezeket a képességeket a virtuális gépen engedélyezheti, ha a bővítményt az Azure Resource Manager-sablon részeként tartalmazza. [Az Azure Resource Manager-sablonok virtuálisgép-bővítményekkel](../windows/template-description.md#extensions) való szerkesztése című témakörben további információt talál a rról, hogy a virtuálisgép-sablon részeként további információkat tartalmaz-e a bővítmények használatával kapcsolatban. Ez a cikk ismerteti, hogyan adhat hozzá az Azure Diagnostics bővítményt egy Windows virtuálisgép-sablonhoz.  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Az Azure Diagnostics bővítmény hozzáadása a virtuális gép erőforrás-definíciójához
A diagnosztikai bővítmény windowsos virtuális gépen való engedélyezéséhez a bővítményt virtuális géperőforrásként kell hozzáadnia az Erőforrás-kezelő sablonban.

Egyszerű erőforrás-kezelő alapú virtuális gép esetén *resources* adja hozzá a bővítmény konfigurációját a virtuális gép erőforrástömbjéhez: 

```json
"resources": [
    {
        "name": "Microsoft.Insights.VMDiagnosticsSettings",
        "type": "extensions",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-06-15",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "tags": {
            "displayName": "AzureDiagnostics"
        },
        "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.5",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
                "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
            },
            "protectedSettings": {
                "storageAccountName": "[parameters('existingdiagnosticsStorageAccountName')]",
                "storageAccountKey": "[listkeys(variables('accountid'), '2015-05-01-preview').key1]",
                "storageAccountEndPoint": "https://core.windows.net"
            }
        }
    }
]
```

Egy másik gyakori konvenció a bővítmény konfigurációjának hozzáadása a sablon gyökérerőforrás-csomópontján, ahelyett, hogy a virtuális gép erőforrás-csomópontja alatt definiálná. Ezzel a megközelítéssel explicit módon meg kell adnia egy hierarchikus kapcsolatot a bővítmény és a virtuális gép között a *név* és a *típus* értékekkel. Példa: 

```json
"name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
"type": "Microsoft.Compute/virtualMachines/extensions",
```

A bővítmény mindig a virtuális géphez van társítva, közvetlenül definiálhatja azt a virtuális gép erőforráscsomópontja alatt, vagy az alapszinten definiálhatja, és a hierarchikus elnevezési konvenció t használhatja a virtuális géphez társítására.

A Virtual Machine Scale Sets esetében a bővítmények konfigurációja a VirtualMachineProfile *extensionProfile* tulajdonságában van *megadva.*

A **Microsoft.Azure.Diagnostics** értékkel rendelkező *publisher* tulajdonság és az **IaaSDiagnostics** értékkel rendelkező *típustulajdonság* egyedileg azonosítja az Azure Diagnostics bővítményt.

A *name* tulajdonság értéke az erőforráscsoportban lévő bővítményre való hivatkozáshoz használható. Ha kifejezetten a **Microsoft.Insights.VMDiagnosticsSettings** beállítással határozza meg, az Azure Portal könnyen azonosíthatja, biztosítva, hogy a figyelési diagramok megfelelően jelenjenek meg az Azure Portalon.

A *typeHandlerVersion* a használni kívánt bővítmény verzióját adja meg. Ha *az autoUpgradeMinorVersion* alverziót **true** értékre állítja, akkor a bővítmény legújabb alverzióját kapja meg. Erősen ajánlott, hogy mindig állítsa *autoUpgradeMinorVersion,* hogy mindig **igaz,** hogy mindig kap, hogy használja a legújabb elérhető diagnosztikai kiterjesztés az összes új funkciók és hibajavítások. 

A *beállításelem* a bővítmény konfigurációs tulajdonságait tartalmazza, amelyek beállíthatók és visszaolvashatók a bővítményből (más néven nyilvános konfiguráció). Az *xmlcfg* tulajdonság xml alapú konfigurációt tartalmaz a diagnosztikai naplókhoz, teljesítményszámlálókhoz stb., amelyeket a diagnosztikai ügynök gyűjt. Az XML-sémáról további információt a [Diagnosztikai konfigurációs séma](https://msdn.microsoft.com/library/azure/dn782207.aspx) című témakörben talál. Általános gyakorlat, hogy a tényleges xml-konfigurációt változóként tárolja az Azure Resource Manager sablonban, majd összefűzi és base64 kódolja őket az *xmlcfg*értékének beállításához. Tekintse meg a [diagnosztikai konfigurációs változók című szakaszt,](#diagnostics-configuration-variables) amelyből megtudhatja, hogyan tárolhatja az xml-t a változókban. A *storageAccount* tulajdonság annak a tárfióknak a nevét adja meg, amelyre a diagnosztikai adatokat továbbítja. 

A *protectedSettings* (más néven privát konfiguráció) tulajdonságai beállíthatók, de a beállítás után nem olvashatók vissza. A *protectedSettings* csak írási jellege miatt hasznos lehet a titkos kulcsok, például a diagnosztikai adatok at megírása a tárfiók kulcsának tárolásához.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Diagnosztikai tárfiók paraméterként történő megadása
A fenti json-kódrészlet diagnosztikai bővítmény két paramétert feltételez *a existingdiagnosticsStorageAccountName* és *a existingdiagnosticsStorageResourceGroup* paraméterrel a diagnosztikai adatokat tároló diagnosztikai tárfiók meghatározásához. A diagnosztikai tárfiók paraméterként való megadása megkönnyíti a diagnosztikai tárfiók különböző környezetekben történő módosítását, például egy másik diagnosztikai tárfiókot szeretne használni a teszteléshez, és egy másikat a éles környezetben.  

```json
"existingdiagnosticsStorageAccountName": {
    "type": "string",
    "metadata": {
"description": "The name of an existing storage account to which diagnostics data is transfered."
    }
},
"existingdiagnosticsStorageResourceGroup": {
    "type": "string",
    "metadata": {
"description": "The resource group for the storage account specified in existingdiagnosticsStorageAccountName"
    }
}
```

Ajánlott egy diagnosztikai tárfiókot megadni egy másik erőforráscsoportban, mint a virtuális gép erőforráscsoportja. Egy erőforráscsoport saját élettartamú központi egységnek tekinthető, a virtuális gépek üzembe helyezhetők és újratelepíthetők, mivel új konfigurációk frissítései vannak hozzá, de előfordulhat, hogy továbbra is szeretné tárolni a diagnosztikai adatokat ugyanabban a tárfiókban a virtuális gép központi telepítéseit. A tárfiók egy másik erőforrásban lehetővé teszi, hogy a tárfiók adatokat fogadjon a különböző virtuálisgép-központi telepítések megkönnyíti a problémák elhárítása a különböző verziókban.

> [!NOTE]
> Ha a Visual Studio-ból hoz létre egy Windows virtuálisgép-sablont, előfordulhat, hogy az alapértelmezett tárfiók ugyanazt a tárfiókot használja, ahol a virtuális gép virtuális merevlemeze fel töltődik. Ez a virtuális gép kezdeti beállításának egyszerűsítése. A sablon újratényezőzése egy másik tárfiók használatával, amely paraméterként átadható. 
> 
> 

## <a name="diagnostics-configuration-variables"></a>Diagnosztikai konfigurációs változók
Az előző diagnosztikai bővítmény json kódrészlet *accountid* definiálja a fiókváltozót, hogy egyszerűsítse a tárfiók kulcsának beszerzése a diagnosztikai tárolóhoz:   

```json
"accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"
```

A diagnosztikai bővítmény *xmlcfg* tulajdonsága több, összefűzve lévő változó val van definiálva. Ezeknek a változóknak az értékei xml formátumban vannak, ezért a jsonváltozók beállításakor megfelelően meg kell szabadulniuk.

A következő példa a diagnosztikai konfigurációs xml, amely összegyűjti a szabványos rendszerszintű teljesítményszámlálók mellett néhány Windows eseménynaplók és diagnosztikai infrastruktúra naplók. A rendszer elkerülte és megfelelően formázta, így a konfiguráció közvetlenül beilleszthető a sablon változók szakaszába. Tekintse meg a [diagnosztikai konfigurációs séma](https://msdn.microsoft.com/library/azure/dn782207.aspx) egy emberileg olvasható példa a konfigurációs xml.

```json
"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
"wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
"wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
"wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
"wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
"wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"
```

A metrikák meghatározása xml csomópont a fenti konfiguráció fontos konfigurációs elem, mivel meghatározza, hogy a *Teljesítményszámláló* csomópont xml-ben korábban definiált teljesítményszámlálók hogyan legyenek összesítve és tárolva. 

> [!IMPORTANT]
> Ezek a metrikák hajtanak a figyelési diagramok és riasztások az Azure Portalon.  A **metrikák** csomópont az *erőforrás-azonosítóval* és **a Metrikaggregáció** szerepelnie kell a virtuális gép diagnosztikai konfigurációjában, ha szeretné látni a virtuális gép figyelési adatait az Azure Portalon. 
> 
> 

A következő példa a metrikadefiníciók xmljét mutatja be: 

```xml
<Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
    <MetricAggregation scheduledTransferPeriod="PT1H"/>
    <MetricAggregation scheduledTransferPeriod="PT1M"/>
</Metrics>
```

A *resourceID* attribútum egyedileg azonosítja a virtuális gépet az előfizetésben. Győződjön meg arról, hogy használja az subscription() és a resourceGroup() függvényeket, hogy a sablon automatikusan frissíti ezeket az értékeket az előfizetés és az erőforráscsoport alapján, amelybe telepíti.

Ha egy hurokban több virtuális gépet hoz létre, a *resourceID* értékét egy copyIndex() függvénnyel kell feltöltenünk az egyes virtuális gépek helyes megkülönböztetéséhez. Az *xmlCfg* érték frissíthető, hogy ezt az alábbiak szerint támogassa:  

```json
"xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 
```

A *PT1M* és *PT1H* MetricAggregation értéke egy percalatt, illetve egy óra alatt összesítést jelent.

## <a name="wadmetrics-tables-in-storage"></a>WADMetrics táblák a tárolóban
A metrikák fenti konfigurációja táblákat hoz létre a diagnosztikai tárfiókban a következő elnevezési konvenciókkal:

* **WADMetrics**: Standard előtag az összes WADMetrics táblához
* **PT1H** vagy **PT1M**: Azt jelzi, hogy a táblázat 1 óra vagy 1 perc alatt tartalmaz összesített adatokat
* **P10D**: Azt jelenti, hogy a táblázat 10 napig tartalmaz adatokat, amikor a tábla elkezdte az adatgyűjtést
* **V2S**: Karakterlánc állandó
* **yyyymmdd**: Az a dátum, amikor a táblázat megkezdte az adatgyűjtést

Példa: *WADMetricsPT1HP10DV2S20151108* mérőszámokat tartalmaz egy órán keresztül 10 napig, 2015.    

Minden WADMetrics tábla a következő oszlopokat tartalmazza:

* **PartitionKey**: A partíciókulcs az *erőforrásazonosító* értéke alapján épül fel a virtuális gép erőforrás egyedi azonosítására. Például:`002Fsubscriptions:<subscriptionID>:002FresourceGroups:002F<ResourceGroupName>:002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>`  
* **RowKey**: A `<Descending time tick>:<Performance Counter Name>`formátumot követi. A csökkenő időosztás számítása az a max időkullancs, mínusz az összesítési időszak kezdetének időpontja. Ha például a mintaidőszak 2015. `DateTime.MaxValue.Ticks - (new DateTime(2015,11,10,0,0,0,DateTimeKind.Utc).Ticks)` A rendelkezésre álló bájtok teljesítményszámlálója esetében a sorkulcs így fog kinézni:`2519551871999999999__:005CMemory:005CAvailable:0020Bytes`
* **CounterName**: A teljesítményszámláló neve. Ez megegyezik az xml config-ban definiált *counterSpecifier-lel.*
* **Maximum**: A teljesítményszámláló maximális értéke az összesítési időszakban.
* **Minimum**: A teljesítményszámláló minimális értéke az összesítési időszakban.
* **Összesen**: Az összesítési időszakban jelentett teljesítményszámláló összes értékének összege.
* **Darabszám**: A teljesítményszámlálóhoz jelentett értékek teljes száma.
* **Átlag**: A teljesítményszámláló átlagos (összesítése/darabszáma) értéke az összesítési időszakban.

## <a name="next-steps"></a>Következő lépések
* A diagnosztikai bővítményt lebővítő Windows virtuális gépek teljes mintasablonját lásd: [201-vm-monitoring-diagnostics-extension](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
* Az Azure Resource Manager-sablon üzembe helyezése az [Azure PowerShell](../windows/ps-template.md) vagy az [Azure parancssora](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) használatával
* További információ az [Azure Resource Manager-sablonok szerzőiről](../../resource-group-authoring-templates.md)
