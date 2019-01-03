---
title: Monitorozás és diagnosztika az Azure virtuális gép hozzáadása |} A Microsoft Docs
description: Az Azure Resource Manager-sablon használatával egy új Windows virtuális gép létrehozása az Azure diagnostics bővítmény.
services: virtual-machines-windows
documentationcenter: ''
author: sbtron
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 8cde8fe7-977b-43d2-be74-ad46dc946058
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: saurabh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 85e9b49cb8be1a3f53ca0f3b4816e6165b68bde0
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2019
ms.locfileid: "53993110"
---
# <a name="use-monitoring-and-diagnostics-with-a-windows-vm-and-azure-resource-manager-templates"></a>Monitorozás és diagnosztika használata a Windows virtuális gépek és Azure Resource Manager-sablonok
Az Azure Diagnostics bővítmény a monitorozási és diagnosztikai képességeket biztosít a Windows-alapú Azure virtuális gépen. Engedélyezheti ezeket a képességeket a virtuális gép által a kiterjesztéssel együtt az Azure Resource Manager-sablon részeként. Lásd: [Azure Resource Manager-sablonok készítése VM-bővítményekkel](../windows/template-description.md#extensions) bármely kiterjesztéssel együtt virtuálisgép-sablon részeként további tájékoztatást. Ez a cikk bemutatja, hogyan adhat hozzá az Azure Diagnostics bővítmény windows virtuálisgép-sablont.  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Adja hozzá a az Azure Diagnostics bővítményt a virtuális gép erőforrás-definíció
Ahhoz, hogy a diagnosztikai bővítmény egy Windows virtuális gépen, adja hozzá a bővítményt a Resource Manager-sablon egy virtuális gép erőforrásként kell.

Egy egyszerű Resource Manager alapú virtuális gép hozzáadása a bővítmény konfigurációja, az *erőforrások* a virtuális gép tömb: 

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

Egy másik common konvenciónak, hogy a gyökércsomópont erőforrások a sablon helyett meghatározása, a virtuális gép erőforrások csomópont alatt adja hozzá a bővítmény konfigurációja. Explicit módon kell megadni a bővítmény és a virtuális gép között egy hierarchikus kapcsolat rendelkezik ezzel a módszerrel a *neve* és *típus* értékeket. Példa: 

```json
"name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
"type": "Microsoft.Compute/virtualMachines/extensions",
```

A bővítmény mindig a virtuális géphez kapcsolódó, lehetősége van közvetlenül definiálja azt a virtuális gép erőforrás-csomópont alatt közvetlenül, vagy definiálja azt az alap szinten, majd társítsa a virtuális gép hierarchikus elnevezési használatával.

Virtual Machine Scale Sets a bővítmények konfigurációs van megadva a a *extensionProfile* tulajdonságát a *VirtualMachineProfile*.

A *közzétevő* tulajdonság értékét **Microsoft.Azure.Diagnostics** és a *típus* tulajdonság értékét **IaaSDiagnostics**egyedi módon azonosítja az Azure Diagnostics bővítmény.

Értékét a *neve* tulajdonság segítségével tekintse meg az erőforráscsoport a bővítménynek. Beállítása a kifejezetten a **Microsoft.Insights.VMDiagnosticsSettings** lehetővé teszi, hogy könnyedén azonosíthatók az Azure Portalon, biztosítva, hogy a figyelési diagramok megjelenítése be megfelelően az Azure Portalon.

A *typeHandlerVersion* határozza meg a használni kívánt bővítmény verzióját. Beállítás *autoUpgradeMinorVersion* alverzió, **igaz** biztosítja, hogy megjelenik-e a bővítményt, amely elérhető kisebb legújabb verzióját. Azt javasoljuk, hogy mindig beállította *autoUpgradeMinorVersion* mindig **igaz** annak érdekében, hogy mindig a legújabb elérhető diagnosztikai bővítmény használata az új funkciókat és hibajavításokat tartalmaz. 

A *beállítások* elem a bővítményt, amely állítsa be, és olvassa el a bővítményt (más néven nyilvános bővítménykonfiguráció) ból konfigurációk tulajdonságait tartalmazza. A *xmlcfg* tulajdonság tartalmazza a diagnosztikai naplókhoz xml-alapú konfiguráció a diagnostics-ügynök által gyűjtött teljesítményszámlálókat stb. Lásd: [diagnosztikai konfigurációs séma](https://msdn.microsoft.com/library/azure/dn782207.aspx) további információ az XML-séma magát. Általános gyakorlat az, hogy a tényleges xml-konfiguráció tárolása az Azure Resource Manager-sablonban változóként, és majd összefűzi és base64 kell kódolnia azokat, az értéket az *xmlcfg*. A szakaszban [diagnosztikai konfiguráció változók](#diagnostics-configuration-variables) , hogy az XML-fájl tárolása változókban többet. A *storageAccount* tulajdonság határozza meg, mely a diagnosztikai adatok továbbítása a tárfiók nevére. 

A Tulajdonságok *protectedSettings* (néha hivatkozott, privát konfigurációja) beállítható, de nem lehet olvasni a beállítása után vissza. Csak írási jellege *protectedSettings* teszi, hogy hasznos titkokat – például a tárfiókkulcs tárolására szolgáló ahol írja a diagnosztikai adatokat.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Paraméterként adja meg a diagnosztikai tárfiók
A diagnosztikai bővítmény json-kódrészletben fenti feltételezi, hogy két paramétert *existingdiagnosticsStorageAccountName* és *existingdiagnosticsStorageResourceGroup* , adja meg a diagnosztikai storage a fiók diagnosztikai adatok tárolására. Adjon meg a diagnosztikai tárfiók, mint egy paraméter megkönnyíti a különböző környezetek között a diagnosztikai tárfiók módosítása, például előfordulhat, hogy használni kívánt tesztelési különböző diagnosztikai tárfiók és a egy másik a éles környezet.  

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

Ajánlott eljárás az, diagnosztikai tárfiókot adjon meg egy másik erőforráscsoportban található, mint az erőforráscsoport, a virtuális gép. Egy erőforráscsoportot kell egy telepítési egység a saját élettartamát is tekinthető, a virtuális gép telepíthető és újratelepítése az új konfigurációk frissítések végzett hozzá, de előfordulhat, hogy folytatja között ugyanazt a tárfiókot a diagnosztikai adatok tárolása Ezek a virtuális gépi környezetekben. Létesíteni a storage-fiók egy másik erőforrás lehetővé teszi, hogy a tárfiók-adatokat, így könnyen hibáinak elhárítása a különböző verziók között különböző virtuális gépi környezetekben.

> [!NOTE]
> Ha a Visual Studióból egy windows virtuálisgép-sablont hoz létre, az alapértelmezett tárfiókot használja ugyanazt a tárfiókot, ahol a virtuális gép VHD feltöltése az be lehet állítani. Ez a kezdeti egyszerűsítheti a virtuális gép. A sablon használatához egy másik tárfiókot, amelyet paraméterként adhatók át újra tényező. 
> 
> 

## <a name="diagnostics-configuration-variables"></a>Diagnosztikai konfiguráció változók
Az előző diagnosztikai bővítmény json-kódrészlet definiál egy *accountid* változó a tárfiók-kulcsot a diagnosztikai tárolásához első leegyszerűsítése érdekében:   

```json
"accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"
```

A *xmlcfg* a diagnosztikai bővítmény tulajdonsága van definiálva, amelyek együtt vannak összefűzött több változók használatával. Ezek a változók értékei XML-úgy kell megadni megfelelően történik, ha a json-változók beállítása szükséges.

Az alábbi példa azt ismerteti, hogy a diagnosztikai konfigurációs XML-t, amely összegyűjti a standard szintű rendszerteljesítmény-számlálók együtt bizonyos windows biztonságiesemény-naplóinak és diagnosztikai infrastruktúra naplói. Lett escape-karakterrel, és úgy, hogy a konfiguráció közvetlenül a változók szakaszban a sablont, hogy beillessze formátuma helytelen. Tekintse meg a [diagnosztikai konfigurációs séma](https://msdn.microsoft.com/library/azure/dn782207.aspx) robotokat olvasható példát a konfigurációs XML-t.

```json
"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
"wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
"wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
"wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
"wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
"wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"
```

A metrikák definíció XML-csomópont a fenti konfigurációban fontos konfigurációs elem, ahogy azt határozza meg, hogyan a teljesítményszámlálókat meghatározott az xml-t a korábbi *PerformanceCounter* csomópont összesített, és tárolja. 

> [!IMPORTANT]
> Ezek a metrikák meghajtó diagramok és értesítések figyelésének az Azure Portalon.  A **metrikák** -csomópont a *resourceID* és **MetricAggregation** szerepelnie kell a virtuális gép diagnosztikai konfigurációja Ha meg szeretné tekinteni a monitorozási adatok a virtuális gép az Azure Portalon. 
> 
> 

Az alábbi példa bemutatja az xml for metrikadefinícióit: 

```xml
<Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
    <MetricAggregation scheduledTransferPeriod="PT1H"/>
    <MetricAggregation scheduledTransferPeriod="PT1M"/>
</Metrics>
```

A *resourceID* attribútum egyedileg azonosítja a virtuális gép az előfizetésében. Győződjön meg arról, hogy a sablon automatikusan frissíti az előfizetést és erőforráscsoportot telepíti, akkor ezeket az értékeket a subscription() és resourceGroup() függvények használandó.

Egy hurokba, és több virtuális gépet hoz létre, ha rendelkezik adatokkal való feltöltéséhez a *resourceID* érték és a egy copyIndex() függvény megfelelően megkülönböztetéséhez minden egyes virtuális Géphez. A *xmlCfg* érték támogatásához a következőképpen frissítheti:  

```json
"xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 
```

MetricAggregation értékét *PT1M* és *PT1H* egy percnyi összesítést és a egy óráig összesítést rendre jelölésére.

## <a name="wadmetrics-tables-in-storage"></a>Storage-ban WADMetrics táblák
A mérőszámok a fenti konfigurációs táblák a diagnosztikai tárfiók, az alábbi elnevezési konvenciók hoz létre:

* **WADMetrics**: Standard előtag WADMetrics táblák
* **PT1H** vagy **PT1M**: Jelzi, hogy a tábla tartalmazza-e az összesített adatok több mint 1 óra vagy 1 perc
* **P10D**: Azt jelzi, hogy a tábla adatokat tartalmaz 10 napig az adatgyűjtés a tábla indításakor
* **V2S**: Karakterlánc-konstans
* **ÉÉÉÉHHNN**: A dátum, amelyen a tábla lépések adatok gyűjtése

Példa: *WADMetricsPT1HP10DV2S20151108* összesített értéket jelenít meg egy órát indítása 11 – november – 2015. 10 nap, a metrikák adatait tartalmazza    

Minden egyes WADMetrics tábla a következő oszlopokat tartalmazza:

* **PartitionKey**: A partíciókulcs alapján jön létre a *resourceID* érték egyedi azonosítására szolgál a VM-erőforrás. Például:`002Fsubscriptions:<subscriptionID>:002FresourceGroups:002F<ResourceGroupName>:002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>`  
* **RowKey**: A formátum a következő `<Descending time tick>:<Performance Counter Name>`. A csökkenő idő osztásjelek számítási idő maximális órajel során végbemenő mínusz az összesítési időszak elején idején. Például ha a mintavételi időszak a következőn: 10 – november – 2015-öt és 00:00Hrs (UTC), majd a számítási lenne: `DateTime.MaxValue.Ticks - (new DateTime(2015,11,10,0,0,0,DateTimeKind.Utc).Ticks)`. For a memóriát elérhető bájtok száma teljesítményszámláló a sorkulcs ehhez hasonló lesz: `2519551871999999999__:005CMemory:005CAvailable:0020Bytes`
* **CounterName**: A teljesítményszámláló neve van. Ez megegyezik a *counterSpecifier* meghatározott az xml-konfiguráció.
* **Maximális**: A teljesítményszámláló keresztül az összesítési időszak maximális értéke.
* **Minimális**: A teljesítményszámláló keresztül az összesítési időszak minimális értéke.
* **Teljes**: A teljesítményszámláló szereplő összes érték összegét jelenti a összesítési időszak alatt.
* **Száma**: A teljesítményszámláló jelentett értékek teljes száma.
* **Átlagos**: Az összesítési időszak alatt a teljesítményszámláló átlagos (összesen és száma) értéke.

## <a name="next-steps"></a>További lépések
* A diagnosztikai bővítmény Windows virtuális gép teljes sablonját, lásd: [201-vm-figyelés-diagnosztikai-bővítmény](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
* Üzembe helyezés Azure Resource Manager-sablon [Azure PowerShell-lel](../windows/ps-template.md) vagy [Azure parancssori felületével](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Tudjon meg többet [Azure Resource Manager-sablonok készítése](../../resource-group-authoring-templates.md)
