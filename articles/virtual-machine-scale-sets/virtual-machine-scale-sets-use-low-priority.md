---
title: Hozzon létre egy alacsony prioritású virtuális gépek (előzetes verzió) használó Azure méretezési |} Microsoft Docs
description: Megtudhatja, alacsony prioritású virtuális gépek használni kívánja, mentheti a költségek Azure virtuálisgép-méretezési csoportok
services: virtual-machine-scale-sets
documentationcenter: ''
author: mmccrory
manager: rajraj
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: memccror
ms.openlocfilehash: f25e4d1e3906a610e7c60e348f872a78d7db8fd3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="low-priority-vms-on-scale-sets-preview"></a>Alacsony prioritású futó virtuális gépek méretezési csoportok (előzetes verzió)

Alacsony prioritású virtuális gépek méretezési csoportok használatával lehetővé teszi a jelentős költségmegtakarítást a unutilized kapacitás kihasználását. Az Azure-infrastruktúra időpont, amikor Azure kér-e vissza a kapacitás bármikor zárjon alacsony prioritású virtuális gépeket. Ezért alacsony prioritású virtuális gépek nagy munkaterhelések esetén, amelyet kezelni tud a megszakítások számát kötegelt feldolgozáson feladatok, fejlesztési és tesztelési célú környezetekben, nagy számítási feladatok és több mint.

Rendelkezésre álló unutilized kapacitását mérete, régió, időpontot és több függően változhat. Alacsony prioritású virtuális gépek skálán telepítését állítja be, amikor Azure foglal le a virtuális gépek Ha nincs elérhető kapacitást, de nincs nincs SLA-t a virtuális gépeken. Alacsony prioritású méretezési egyetlen tartalék tartomány van telepítve, és biztosítja, hogy nem magas rendelkezésre állást biztosít.

> [!NOTE]
> Alacsony prioritású méretezési csoportok még csak előzetes verziójúak, és készen áll a fejlesztési és tesztelési forgatókönyvekhez. 

## <a name="eviction-policy"></a>A kiürítési házirend

Az alacsony prioritású méretezési virtuális gépek ki vannak zárva, akkor átkerül a Leállítva (felszabadítva) állapotú alapértelmezés szerint. A kiürítési házirend eltávolított példányok központilag telepítheti, de nincs nem biztos, hogy a Foglalás sikeres lesz. A leállított virtuális gépek elleni a méretezési készlet példány kvótájának számít, és a mögöttes lemezek fizetnie kell. 

Ha azt szeretné, hogy a virtuális gépek a törlendő azok elvesszen beállítása alacsony prioritású skálázást, a kiürítés házirendet a törlendő állíthat be a [Azure Resource Manager sablon](#use-azure-resource-manager-templates). A kiürítési házirend törléséhez állítsa be új virtuális gépek méretezési készlet példány count tulajdonság növelésével hozhat létre. Az eltávolított virtuális gépek és az alapjául szolgáló lemezek törlődnek, és ezért nem kell fizetnie a tárolási. Is segítségével a méretezési csoportok az automatikus skálázást szolgáltatása automatikusan próbálja, és ellensúlyozza a eltávolított virtuális gépeket, és van azonban nem biztos, hogy a Foglalás sikeres lesz. Ajánlott, csak az automatikus méretezése funkciót használja alacsony prioritású méretezési csoportok törlése a lemezek és a kvótakorlát elérés költsége elkerülése érdekében a kiürítés házirend beállításakor. 

> [!NOTE]
> Előzetes, lesz a kiürítési házirendjének beállítása használatával [Azure Resource Manager-sablonok](#use-azure-resource-manager-templates). 

## <a name="deploying-low-priority-vms-on-scale-sets"></a>Beállítja a méretezési alacsony prioritású futó virtuális gépek telepítése

Kis prioritású virtuális gépek méretezési csoportok a telepítendő állíthatja be az új *prioritás* jelzőt *alacsony*. Alacsony prioritású állítja be a méretezési csoportban lévő összes virtuális gépet. A skála beállítása alacsony prioritású virtuális gépek létrehozásához használja a következő módszerek egyikét:
- [Azure CLI 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Az Azure Resource Manager-sablonok](#use-azure-resource-manager-templates)

## <a name="use-the-azure-cli-20"></a>Az Azure parancssori felület használatával 2.0

A skála beállítása alacsony prioritású virtuális gépek létrehozásának folyamata megegyezik a részletes a [bevezető cikkben](quick-create-cli.md). Csak adja hozzá a "--prioritás" paraméter a parancssori felület segítségével hívja, és állítsa az értékét *alacsony* az alábbi példában látható módon:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Low
```

## <a name="use-azure-powershell"></a>Azure PowerShell használatával

A skála beállítása alacsony prioritású virtuális gépek létrehozásának folyamata megegyezik a részletes a [bevezető cikkben](quick-create-powershell.md).
Csak adja hozzá a "-prioritás" paramétert a [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) és *alacsony* az alábbi példában látható módon:

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Low"
```

## <a name="use-azure-resource-manager-templates"></a>Az Azure Resource Manager-sablonok használata

A folyamat által használt alacsony prioritású virtuális gépek méretezési készlet létrehozása megegyezik az első lépések cikk részletes [Linux](quick-create-template-linux.md) vagy [Windows](quick-create-template-windows.md). Adja hozzá a "priority" tulajdonságot a *Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile* erőforrás adja meg a sablont, és adja meg *alacsony* értékeként. Használjon *2017-10 – 30 – előzetes* API-verzió vagy újabb verzióját. 

Ahhoz, hogy a kiürítés házirend értékre törlését, adja hozzá a "evictionPolicy" paramétert, és állítsa az értékét *törlése*.

Az alábbi példa létrehoz egy Linux alacsony prioritású méretezési beállítása a nevesített *myScaleSet* a *nyugati középső Régiójában*, amelynél *törlése* a skála kiürítés be a virtuális gépek:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "sku": {
    "name": "Standard_DS2_v2",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
       "priority": "Low",
       "evictionPolicy": "delete",
       "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```
## <a name="next-steps"></a>További lépések
Most, hogy létrehozott egy méretezési, alacsony prioritású virtuális gépek készletben, próbálja üzembe helyezni a [automatikus méretezési sablon használatával alacsony prioritású](https://github.com/Azure/vm-scale-sets/tree/master/preview/lowpri).

Tekintse meg a [virtuálisgép-méretezési csoport árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) a díjszabás részleteit.