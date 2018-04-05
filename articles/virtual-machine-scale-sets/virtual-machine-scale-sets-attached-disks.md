---
title: Azure-beli virtuálisgép-méretezési csoportok és csatlakoztatott adatlemezek | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan használhat csatlakoztatott adatlemezeket virtuálisgép-méretezési csoportokkal.
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/25/2017
ms.author: negat
ms.openlocfilehash: ec11a2d66530129fb61d97681e6882b887c8654c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Azure-beli virtuálisgép-méretezési csoportok és csatlakoztatott adatlemezek
A rendelkezésre álló tárterület kiterjesztése érdekében az Azure-beli [virtuálisgép-méretezési csoportok](/azure/virtual-machine-scale-sets/) támogatják a csatlakoztatott adatlemezekkel rendelkező virtuálisgép-példányokat. Adatlemezeket a méretezési csoport létrehozásakor, vagy egy már létező méretezési csoporthoz is hozzáadhat.

> [!NOTE]
>  Amikor olyan méretezési csoportot hoz létre, amely rendelkezik csatlakoztatott adatlemezekkel, ugyanúgy csatlakoztatnia kell a lemezeket, és formáznia kell őket a virtuális gépen belül a használatukhoz (akárcsak a különálló Azure-beli virtuális gépek esetén). Ez a folyamat kényelmesen elvégezhető egy olyan szkriptbővítmény használatával, amely egy szkript hívásával particionálja és formázza a virtuális gép összes adatlemezét. Példákért lásd: [Azure CLI 2.0](tutorial-use-disks-cli.md#prepare-the-data-disks) [Azure PowerShell](tutorial-use-disks-powershell.md#prepare-the-data-disks).


## <a name="create-and-manage-disks-in-a-scale-set"></a>Lemezek létrehozása és felügyelete egy méretezési csoportban
A csatlakoztatott adatlemezekkel rendelkező méretezési csoport létrehozásával, valamint az adatlemezek előkészítésével, formázásával, hozzáadásával és eltávolításával kapcsolatos részletes információt a következő oktatóanyagokban talál:

- [Azure CLI 2.0](tutorial-use-disks-cli.md)
- [Azure PowerShell](tutorial-use-disks-powershell.md)

A cikk további részében adott használati eseteket mutatunk be, például az adatlemezeket igénylő Service Fabric-fürtöket, vagy a tartalommal rendelkező meglévő adatlemezek csatlakoztatását méretezési csoportokhoz.


## <a name="create-a-service-fabric-cluster-with-attached-data-disks"></a>Service Fabric-fürt létrehozása csatlakoztatott adatlemezekkel
Az Azure-ban futó [Service Fabric](/azure/service-fabric)-fürtök mindegyik [csomóponttípusa](../service-fabric/service-fabric-cluster-nodetypes.md) egy virtuálisgép-skálázási csoporton alapul.  Egy Azure Resource Manager-sablonnal adatlemezeket csatlakoztathat a Service Fabric-fürtöt alkotó méretezési csoport(ok)hoz. Kezdőpontként használhat egy [meglévő sablont](https://github.com/Azure-Samples/service-fabric-cluster-templates). A sablonban adjon egy _dataDisks_ szakaszt a _Microsoft.Compute/virtualMachineScaleSets_ erőforrás(ok) _storageProfile_ eleméhez, és helyezze üzembe a sablont. A következő példa egy 128 GB-os adatlemezt csatlakoztat:

```json
"dataDisks": [
    {
    "diskSizeGB": 128,
    "lun": 0,
    "createOption": "Empty"
    }
]
```

Automatikusan particionálhatja, formázhatja és csatlakoztathatja az adatlemezeket a fürt üzembe helyezésekor.  Adjon egy egyéni szkriptbővítményt a skálázási csoport(ok) _virtualMachineProfile_ elemének _extensionProfile_ profiljához.

Ha Windows-fürtön szeretné automatikusan előkészíteni az adatlemez(eke)t, adja hozzá a következőket:

```json
{
    "name": "customScript",    
    "properties": {    
        "publisher": "Microsoft.Compute",    
        "type": "CustomScriptExtension",    
        "typeHandlerVersion": "1.8",    
        "autoUpgradeMinorVersion": true,    
        "settings": {    
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1"
        ],
        "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
        }
    }
}
```
Ha Linux-fürtön szeretné automatikusan előkészíteni az adatlemez(eke)t, adja hozzá a következőket:
```json
{
    "name": "lapextension",
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"
        ],
        "commandToExecute": "bash prepare_vm_disks.sh"
        }
    }
}
```


## <a name="adding-pre-populated-data-disks-to-an-existent-scale-set"></a>Adatokkal előre feltöltött adatlemezek hozzáadása már létező méretezési csoporthoz 
> Amikor lemezeket ad hozzá egy már létező méretezésicsoport-modellhez, az elvárt működésnek megfelelően a lemezek minden esetben üresen jönnek létre. Ez a forgatókönyv a méretezési csoport által létrehozott új példányokra is vonatkozik. Ennek az oka, hogy a méretezési csoport definíciójában üres adatlemez van meghatározva. Ha adatokkal előre feltöltött adatlemezeket szeretne létrehozni egy meglévő méretezésicsoport-modellhez, az alábbi két lehetőség közül választhat:

* Átmásolhatja a 0. virtuálisgép-példány adatait a többi virtuális gépre egy egyéni szkript futtatásával.
* Létrehozhat egy felügyelt rendszerképet, amely az operációs rendszer adatai mellett az adatlemezt is tartalmazza (a szükséges adatokkal), és létrehozhat egy új méretezési csoportot ezzel a rendszerképpel. Így minden újonnan létrehozott virtuális gép rendelkezik egy adatlemezzel, amely a méretezési csoport definíciójában van megadva. Mivel a definíció egy olyan rendszerképre hivatkozik, amely tartalmaz egy testre szabott adatokkal rendelkező adatlemezt, a méretezési csoportban lévő összes virtuális gép tartalmazza ezeket a módosításokat.

> Az egyéni rendszerkép létrehozásának lépéseit itt találja: [Create a managed image of a generalized VM in Azure](/azure/virtual-machines/windows/capture-image-resource/) (Általánosított virtuális gép felügyelt rendszerképének létrehozása az Azure-ban). 

> A felhasználónak rögzítenie kell a szükséges adatokat tartalmazó 0. virtuálisgép-példányt, majd ezt a virtuális merevlemezt kell használnia a rendszerkép definíciójában.


## <a name="additional-notes"></a>További megjegyzések
Az Azure Managed Disks, valamint a csatlakoztatott adatlemezzel rendelkező méretezési csoportok támogatása elérhető a Microsoft.Compute API [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2016-04-30-preview/swagger/compute.json) és újabb verzióiban.

A csatlakoztatott lemezek méretezési csoportok esetében való támogatásának kezdeti implementációjában nincs lehetőség adatlemezek csatlakoztatására, vagy azok eltávolítására a méretezési csoportokba tartozó különálló virtuális gépek esetében.

Az Azure Portal kezdetben csak korlátozott támogatást biztosít a méretezési csoportok csatlakoztatott adatlemezei számára. A követelményektől függően Azure-sablonok, a parancssori felület, a PowerShell, SDK-k és a REST API használatával is kezelheti a csatlakoztatott lemezeket.


