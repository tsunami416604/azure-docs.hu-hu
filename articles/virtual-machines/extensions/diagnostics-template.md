---
title: Figyelési & diagnosztikai hozzáadása egy Azure virtuális géphez |} Microsoft Docs
description: Egy Azure Resource Manager-sablon használatával hozzon létre egy új Windows rendszerű virtuális gép Azure diagnostics-bővítménnyel.
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
ms.openlocfilehash: 792a3401c483327eb7fb9fcd88039bc09025b3ef
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="use-monitoring-and-diagnostics-with-a-windows-vm-and-azure-resource-manager-templates"></a>Figyelés és diagnosztika használata egy Windows virtuális gép és az Azure Resource Manager sablonok
Az Azure Diagnostics bővítmény a figyelési és diagnosztikai képességeket biztosít a Windows-alapú Azure virtuális gépen. A kiterjesztéssel együtt az Azure Resource Manager sablon részeként engedélyezheti ezeket a képességeket a virtuális gépen. Lásd: [Azure Resource Manager sablonok készítése a Virtuálisgép-bővítmények](../windows/template-description.md#extensions) további információ a virtuálisgép-sablon részeként bármely kiterjesztéssel együtt. Ez a cikk ismerteti, hogyan adhat hozzá az Azure Diagnostics-bővítmény egy windows virtuális gépre vonatkozó sablont.  

## <a name="add-the-azure-diagnostics-extension-to-the-vm-resource-definition"></a>Adja hozzá az Azure Diagnostics bővítményt a virtuális gép erőforrás-definíció
Engedélyezi a diagnosztika bővítményt egy Windows rendszerű virtuális gép, adja hozzá a kiterjesztést a Resource Manager-sablon a virtuális gép erőforrásként kell.

Az egyszerű erőforrás-kezelő alapú virtuális gép hozzáadása a bővítmény konfigurációját, és a *erőforrások* tömb a virtuális géphez: 

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

Egy másik közös egyezmény a bővítmény konfigurációja a gyökércsomópontnál erőforrások a virtuálisgép-erőforrások csomópontban definiáló helyett a sablon hozzáadása. Ezt a módszert kell explicit módon adja meg a bővítményt, és a virtuális gépnek a hierarchikus kapcsolata a *neve* és *típus* értékeket. Példa: 

```json
"name": "[concat(variables('vmName'),'Microsoft.Insights.VMDiagnosticsSettings')]",
"type": "Microsoft.Compute/virtualMachines/extensions",
```

A bővítményt a virtuális gép mindig tartozik, akkor vagy közvetlenül közvetlenül megadása a virtuális gép erőforrás csomópont alatt vagy adjon meg egy alap szintjén, és a hierarchikus elnevezési konvenció használatával rendelje hozzá azt a virtuális gép.

Virtuálisgép-méretezési csoportok a bővítmények konfiguráció van megadva a a *extensionProfile* tulajdonsága a *VirtualMachineProfile*.

A *publisher* tulajdonság értékét **Microsoft.Azure.Diagnostics** és a *típus* tulajdonság értékét **IaaSDiagnostics**egyedi módon azonosítja az Azure Diagnostics-bővítmény.

Értékét a *neve* tulajdonság segítségével tekintse meg az erőforráscsoportot a bővítményt. Kifejezetten a állítaná **Microsoft.Insights.VMDiagnosticsSettings** lehetővé teszi, hogy könnyen azonosítható, ha az Azure-portálon győződjön meg arról, hogy a figyelés diagram megjelenítése be megfelelően az Azure portálon.

A *typeHandlerVersion* határozza meg a használni kívánt bővítmény verzióját. Beállítás *autoUpgradeMinorVersion* való alverzió **igaz** biztosítja, hogy a rendelkezésre álló bővítmény legújabb alverziószáma kap. Javasoljuk, hogy mindig állítsa *autoUpgradeMinorVersion* mindig **igaz** , hogy mindig a legújabb elérhető diagnosztika bővítmény használatához az összes új funkcióit és hibajavításait beolvasása. 

A *beállítások* elem beállítása és olvasni a bővítmény (más néven nyilvános konfigurációs) kiterjesztés konfigurációk tulajdonságait tartalmazza. A *xmlcfg* tulajdonsága tartalmazza XML-alapú konfigurációs a diagnosztikai naplók esetén a diagnosztikai ügynök által gyűjtött teljesítményszámlálókat stb. Lásd: [diagnosztika konfigurációs séma](https://msdn.microsoft.com/library/azure/dn782207.aspx) további információt az XML-séma magát. Általános gyakorlat, hogy a tényleges XML-konfiguráció tárolja az Azure Resource Manager sablon változóként, és majd összefűzésére és base64 kódolás az értéket az *xmlcfg*. Szakaszt [diagnosztika konfigurációs változók](#diagnostics-configuration-variables) bővebb információt az XML-fájl tárolása a változókat. A *storageAccount* tulajdonság határozza meg a tárfiók, mely a diagnosztikai adatok továbbításához a szolgáltatás nevét. 

A Tulajdonságok *protectedSettings* (összetevőjét néha hivatkozott konfiguráció) állítható be, de nem lehet olvasni a beállítása után vissza. A csak írható jellege *protectedSettings* teszi hasznos, ha például a tárfiók hívóbetűjét titkos kulcsok tárolása hol a diagnosztikai adatainak írása.    

## <a name="specifying-diagnostics-storage-account-as-parameters"></a>Diagnosztikai tárfiók paraméter megadása
A diagnosztika bővítmény json részlet fenti azt feltételezi, hogy a két paraméter *existingdiagnosticsStorageAccountName* és *existingdiagnosticsStorageResourceGroup* a diagnosztika tároló megadása a fiók diagnosztikai adatok tárolására. Adja meg a diagnosztikai tárfiók, mint egy paraméter megkönnyíti, hogy a diagnosztikai tárfiók módosítása a különböző környezetek között, például érdemes lehet egy másik diagnosztikai tárfiók tesztelési és egy másikat pedig az a éles környezet.  

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

Ajánlott eljárás, adjon meg egy diagnosztikai tárfiók egy másik erőforráscsoportban található, mint az erőforráscsoport, a virtuális gép. Erőforráscsoport lehet tekinteni a saját élettartamú telepítési egységnek, a virtuális gépek telepítése és újratelepítése az új konfiguráció frissítések válnak hozzá, de előfordulhat, hogy folytatja a diagnosztikai adatok tárolását a tárfiókon keresztül Ezen virtuális gépek telepítéséhez. A storage-fiókot használjon egy másik erőforráscsoportban az lehetővé teszi, hogy a tárfiók különböző virtuális gépek telepítéséhez Ez megkönnyíti a probléma megoldásához az egyes verziók között adatokat fogadhat.

> [!NOTE]
> Visual Studio egy windows virtuális gépre vonatkozó sablont hoz létre, ha az alapértelmezett tárfiókot is be lehet állítani ugyanazt a tárfiókot használni, ahol a virtuális gép virtuális merevlemez feltöltése. Ez az a virtuális gép kezdeti telepítése leegyszerűsítése érdekében. Egy másik tárolási fiókot használjon, amely egy paraméter argumentumként átadhatók a sablon újbóli figyelembe. 
> 
> 

## <a name="diagnostics-configuration-variables"></a>Diagnosztikai konfiguráció változók
Az előző diagnosztika bővítmény json-részlet definiál egy *accountid* változó a tárfiók hívóbetűjét a diagnosztika tárolási első leegyszerűsítése érdekében:   

```json
"accountid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',parameters('existingdiagnosticsStorageResourceGroup'), '/providers/','Microsoft.Storage/storageAccounts/', parameters('existingdiagnosticsStorageAccountName'))]"
```

A *xmlcfg* a diagnosztika bővítmény tulajdonság van definiálva, amelyek együtt halmaz zónanevének több változók használata. A változók értékei az XML-ben, meg kell jelölni megfelelően történik, amikor a json-változók beállítása szükséges.

A következő példa a diagnosztika konfigurációs XML-t, a standard szint rendszerteljesítmény-számlálók együtt egyes windows-Eseménynapló és a diagnosztika infrastruktúra naplókat gyűjt ismerteti. Lett escape-karakterrel megjelölve, és megfelelő formátumú, hogy a konfiguráció közvetlenül a változók szakaszban a sablon lehet beilleszteni. Tekintse meg a [diagnosztika konfigurációs séma](https://msdn.microsoft.com/library/azure/dn782207.aspx) több emberi olvasható példát a konfigurációs XML fájlt.

```json
"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
"wadperfcounters1": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Privileged Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU privileged time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% User Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU user time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Processor Information(_Total)\\Processor Frequency\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"CPU frequency\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\System\\Processes\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Processes\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Threads\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Handle Count\" sampleRate=\"PT15S\" unit=\"Count\"><annotation displayName=\"Handles\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\% Committed Bytes In Use\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Memory usage\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Available Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory available\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Committed Bytes\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory committed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\Memory\\Commit Limit\" sampleRate=\"PT15S\" unit=\"Bytes\"><annotation displayName=\"Memory commit limit\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active time\" locale=\"en-us\"/></PerformanceCounterConfiguration>",
"wadperfcounters2": "<PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Read Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active read time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\% Disk Write Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk active write time\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Transfers/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Reads/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk read operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Writes/sec\" sampleRate=\"PT15S\" unit=\"CountPerSecond\"><annotation displayName=\"Disk write operations\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Read Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk read speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\PhysicalDisk(_Total)\\Disk Write Bytes/sec\" sampleRate=\"PT15S\" unit=\"BytesPerSecond\"><annotation displayName=\"Disk write speed\" locale=\"en-us\"/></PerformanceCounterConfiguration><PerformanceCounterConfiguration counterSpecifier=\"\\LogicalDisk(_Total)\\% Free Space\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"Disk free space (percentage)\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
"wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters1'), variables('wadperfcounters2'), '<Metrics resourceId=\"')]",
"wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name , '/providers/', 'Microsoft.Compute/virtualMachines/')]",
"wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>"
```

A metrikák definition XML-csomópont a fenti konfigurációban megegyezik egy fontos konfigurációs elem azt határozza meg, hogyan a teljesítményszámlálókat meghatározott, az XML-t a korábbi *PerformanceCounter* csomópont összesítve, és tárolja. 

> [!IMPORTANT]
> A metrikák meghajtó figyelési diagramok és értesítések az Azure portálon.  A **metrikák** csomópont, amelynek a *resourceID* és **MetricAggregation** szerepelnie kell a diagnosztika a virtuális gép konfigurációja Ha meg szeretné tekinteni a figyelési adatok, a virtuális gép az Azure-portálon. 
> 
> 

A következő példa bemutatja a metrikák definíciók xml: 

```xml
<Metrics resourceId="/subscriptions/subscription().subscriptionId/resourceGroups/resourceGroup().name/providers/Microsoft.Compute/virtualMachines/vmName">
    <MetricAggregation scheduledTransferPeriod="PT1H"/>
    <MetricAggregation scheduledTransferPeriod="PT1M"/>
</Metrics>
```

A *resourceID* attribútum egyedileg azonosítja a virtuális gépet az előfizetésben. Győződjön meg arról, hogy a sablon automatikusan frissíti ezeket az értékeket az előfizetést és az erőforráscsoport esetében helyez üzembe alapján a subscription() és resourceGroup() funkciók használandó.

Több virtuális gép ismétlődő létrehozásakor, rendelkezik-e tölteni a *resourceID* egy copyIndex() függvény minden egyes virtuális gép megfelelően megkülönböztetéséhez értéket. A *xmlCfg* érték pedig frissíthető támogatja ezt a következőképpen:  

```json
"xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), concat(parameters('vmNamePrefix'), copyindex()), variables('wadcfgxend')))]", 
```

MetricAggregation értékének *PT1M* és *PT1H* egy perc alatt összesítést és összesítést akár egy óráig, illetve jelölésére.

## <a name="wadmetrics-tables-in-storage"></a>A tárolási WADMetrics táblák
A fenti metrikák konfigurációs táblákat hoz létre a diagnosztikai tárfiók a következő tanúsítványelnevezési módszerek:

* **WADMetrics**: szabványos előtag WADMetrics táblák
* **PT1H** vagy **PT1M**: azt jelenti, hogy a tábla tartalmazza-e az összesített adatok több mint 1 óra vagy 1 perc
* **P10D**: azt jelzi, hogy a tábla fog adatokat tartalmazni bekapcsolásakor a adatokat gyűjt a tábla 10 napig
* **V2S**: karakterlánc-konstansra
* **ÉÉÉÉHHNN**: A dátum a tábla kezdésének adatok gyűjtése

Példa: *WADMetricsPT1HP10DV2S20151108* indítása a 11-november-2015-10 napja egy óra alatt összesített értéket mérőszámok-adatokat tartalmaz    

Minden egyes WADMetrics tábla a következő oszlopokat tartalmazza:

* **PartitionKey**: A partíciós kulcs összeállított alapján a *resourceID* értéket a virtuális gép erőforrásához egyedi azonosításához. Például: 002Fsubscriptions:<subscriptionID>: 002FresourceGroups:002F<ResourceGroupName>: 002Fproviders:002FMicrosoft:002ECompute:002FvirtualMachines:002F<vmName>  
* **RowKey**: a formátumot követi `<Descending time tick>:<Performance Counter Name>`. A csökkenő idő osztásjelek számítás maximális idő ticks csökkentve az összesítési időszak kezdete idején. Ha például a mintavételi időszak kezdete: 10-november-2015 és 00:00Hrs UTC, majd a számítási lenne: `DateTime.MaxValue.Ticks - (new DateTime(2015,11,10,0,0,0,DateTimeKind.Utc).Ticks)`. A memória rendelkezésre álló bájtok teljesítményszámláló a sorkulcs hasonlóan fog kinézni: `2519551871999999999__:005CMemory:005CAvailable:0020Bytes`
* **CounterName**: a teljesítményszámláló neve. Ez megegyezik a *counterSpecifier* az XML-konfiguráció sémaellenőrzése definiálva.
* **Maximális**: A maximális érték a teljesítményszámláló az összesítési adott időszakban.
* **Minimális**: A minimális érték a teljesítményszámláló az összesítési adott időszakban.
* **Teljes**: a teljesítményszámláló az összes értékek összegét jelentett az összesítési adott időszakban.
* **Count**: a teljesítményszámláló jelentett értékek száma.
* **Átlagos**: az összesítő adott időszakban a teljesítményszámláló átlagos (összesen és száma) értékét.

## <a name="next-steps"></a>További lépések
* Teljes mintasablon diagnosztika kiterjesztésű Windows virtuális gép, lásd: [201-vm-figyelési-diagnosztika-bővítmény](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-monitoring-diagnostics-extension)   
* Az Azure Resource Manager sablon használatával telepítheti [Azure PowerShell](../windows/ps-template.md) vagy [Azure parancssori felülettel](../linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* További információ [Azure Resource Manager sablonok készítése](../../resource-group-authoring-templates.md)
