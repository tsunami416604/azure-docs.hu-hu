---
title: Az Azure Batch-készlet létrehozása esemény
description: Hivatkozás a batch készlet létrehozási eseményére, amelyet a készlet létrehozása után ad ki a program. A napló tartalma általános információkat tartalmaz a készletről.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: dea025b274278aa5fed2900c95b4a274541ffef9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022189"
---
# <a name="pool-create-event"></a>Készlet létrehozása esemény

 Ez az esemény a készlet létrehozása után kerül kibocsátásra. A napló tartalma általános információkat tartalmaz a készletről. Vegye figyelembe, hogy ha a készlet célmérete nagyobb, mint 0 számítási csomópont, a készlet átméretezési indítási esemény követi közvetlenül az esemény után.

 A következő példa egy készlet létrehozási eseményének `CloudServiceConfiguration` törzsét mutatja be a tulajdonság használatával létrehozott készlethez.

```
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Standard_F1s",
    "imageType": "VirtualMachineConfiguration",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "virtualMachineConfiguration": {
          "imageReference": {
            "publisher": " ",
            "offer": " ",
            "sku": " ",
            "version": " "
          },
          "nodeAgentId": " "
        },
    "resizeTimeout": "300000",
    "targetDedicatedNodes": 2,
    "targetLowPriorityNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

|Elem|Típus|Megjegyzések|
|-------------|----------|-----------|
|`id`|Sztring|A készlet azonosítója.|
|`displayName`|Sztring|A készlet megjelenítendő neve.|
|`vmSize`|Sztring|A készletben lévő virtuális gépek mérete. A készletben lévő összes virtuális gép azonos méretű. <br/><br/> A felhőszolgáltatások készleteihez rendelkezésre álló virtuális gépek (a cloudServiceConfiguration szolgáltatással létrehozott készletek) elérhető méreteiről a [Felhőszolgáltatások méretei](https://azure.microsoft.com/documentation/articles/cloud-services-sizes-specs/)című témakörben talál. A Batch támogatja az összes `ExtraSmall`felhőszolgáltatási virtuális gép méretét, kivéve a.<br/><br/> A virtuális gépek piactérről (virtualMachineConfiguration-val létrehozott készletek) lemezképeit használó készletek hez rendelkezésre álló virtuálisgép-méretekről a Virtual Machines (Linux) [méretei](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-sizes/) vagy a Virtuális gépek (Windows) [méretei](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) című témakörben talál további információt. A Batch a `STANDARD_A0`, illetve a Premium Storage típusú méretek (`STANDARD_GS`, `STANDARD_DS` és `STANDARD_DSV2` sorozat) kivételével az összes Azure virtuálisgép-méretet támogatja.|
|`imageType`|Sztring|A lemezkép telepítési módja. A támogatott `virtualMachineConfiguration` értékek`cloudServiceConfiguration`|
|[`cloudServiceConfiguration`](#bk_csconf)|Összetett típus|A készlet felhőszolgáltatás-konfigurációja.|
|[`virtualMachineConfiguration`](#bk_vmconf)|Összetett típus|A készlet virtuálisgép-konfigurációja.|
|[`networkConfiguration`](#bk_netconf)|Összetett típus|A készlet hálózati konfigurációja.|
|`resizeTimeout`|Time|A számítási csomópontok hozzárendelésének időtúlidőpontja a készlet utolsó átméretezési műveletéhez megadott készlethez.  (A készlet létrehozásakor a kezdeti méretezés átméretezésnek számít.)|
|`targetDedicatedNodes`|Int32|A készlethez kért dedikált számítási csomópontok száma.|
|`targetLowPriorityNodes`|Int32|A készlethez kért alacsony prioritású számítási csomópontok száma.|
|`enableAutoScale`|Logikai|Itt adható meg, hogy a készlet mérete automatikusan módosuljon-e az idő múlásával.|
|`enableInterNodeCommunication`|Logikai|Itt adható meg, hogy a készlet be van-e állítva a csomópontok közötti közvetlen kommunikációra.|
|`isAutoPool`|Logikai|Itt adható meg, hogy a készlet a feladat AutoPool-mechanizmusán keresztül jött-e létre.|
|`maxTasksPerNode`|Int32|A készlet egyetlen számítási csomópontján egyidejűleg futtatható feladatok maximális száma.|
|`vmFillType`|Sztring|Azt határozza meg, hogy a Batch szolgáltatás hogyan osztja el a feladatokat a készlet számítási csomópontjai között. Az érvényes értékek: Spread vagy Pack.|

###  <a name="cloudserviceconfiguration"></a><a name="bk_csconf"></a>cloudServiceConfiguration

|Elem neve|Típus|Megjegyzések|
|------------------|----------|-----------|
|`osFamily`|Sztring|Az Azure Vendég operációsrendszer-család a készletben lévő virtuális gépekre telepíthető.<br /><br /> Lehetséges értékek:<br /><br /> **2** – Az operációs rendszer 2-es családja, amely egyenértékű a Windows Server 2008 R2 SP1 rendszerrel.<br /><br /> **3** – Az OPERÁCIÓS RENDSZER 3-as családja, amely egyenértékű a Windows Server 2012 rendszerrel.<br /><br /> **4** – Az OS 4-es családja, amely egyenértékű a Windows Server 2012 R2 rendszerrel.<br /><br /> További információ: [Azure Guest OS Releases](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|
|`targetOSVersion`|Sztring|Az Azure Vendég operációs rendszer verziója a készletben lévő virtuális gépeken való telepítéshez.<br /><br /> Az alapértelmezett **\*** érték az, amely megadja az operációs rendszer legújabb verzióját a megadott családhoz.<br /><br /> Az egyéb engedélyezett értékeket az [Azure Vendég operációsrendszer-kiadások című témakörben tés) című témakörben tetszetik.](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases)|

###  <a name="virtualmachineconfiguration"></a><a name="bk_vmconf"></a>virtualMachineConfiguration

|Elem neve|Típus|Megjegyzések|
|------------------|----------|-----------|
|[`imageReference`](#bk_imgref)|Összetett típus|A platformra vagy a Marketplace-lemezképre vonatkozó információkat adja meg.|
|`nodeAgentId`|Sztring|A számítási csomóponton kiépített Batch-csomópontügynök termékváltozata.|
|[`windowsConfiguration`](#bk_winconf)|Összetett típus|Megadja a Windows operációs rendszer beállításait a virtuális gépen. Ezt a tulajdonságot nem szabad megadni, ha az imageReference egy Linux operációsrendszer-lemezképre hivatkozik.|

###  <a name="imagereference"></a><a name="bk_imgref"></a>képhivatkozás

|Elem neve|Típus|Megjegyzések|
|------------------|----------|-----------|
|`publisher`|Sztring|A kép kiadója.|
|`offer`|Sztring|Az ajánlat a kép.|
|`sku`|Sztring|A kép termékváltozata.|
|`version`|Sztring|A kép verziója.|

###  <a name="windowsconfiguration"></a><a name="bk_winconf"></a>windowsConfiguration

|Elem neve|Típus|Megjegyzések|
|------------------|----------|-----------|
|`enableAutomaticUpdates`|Logikai|Azt jelzi, hogy a virtuális gép engedélyezve van-e az automatikus frissítésekhez. Ha ez a tulajdonság nincs megadva, az alapértelmezett érték igaz.|

###  <a name="networkconfiguration"></a><a name="bk_netconf"></a>hálózati konfiguráció

|Elem neve|Típus|Megjegyzések|
|------------------|--------------|----------|
|`subnetId`|Sztring|Annak az alhálózatnak az erőforrás-azonosítóját adja meg, amelyben a készlet számítási csomópontjai létrejönnek.|
