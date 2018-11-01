---
title: További információ a virtuális gép méretezésicsoport-sablon |} A Microsoft Docs
description: Ismerje meg, hogyan hozzon létre egy virtuálisgép-méretezési csoportokhoz tartozó minimális működőképes méretezési csoport sablon
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: manayar
ms.openlocfilehash: 29c0a1a15db7670d83ff384a1ba0f37499389ef7
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741879"
---
# <a name="learn-about-virtual-machine-scale-set-templates"></a>További információ a virtuális gép méretezési csoportok sablonjairól
Az [Azure Resource Manager-sablonok](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment) remek megoldást kínálnak egymáshoz kapcsolódó erőforráscsoportok üzembe helyezésére. Az oktatóanyag-sorozat bemutatja egy minimális működőképes méretezési csoport sablonjának létrehozása és módosítása a sablon igény szerint a különböző forgatókönyvekben. Valamennyi példa származnak, ez [GitHub-adattár](https://github.com/gatneil/mvss). 

Ez a sablon készült egyszerű. A skála teljes példákat sablonok, olvassa el a [Azure gyorsindítási sablonok GitHub-tárház](https://github.com/Azure/azure-quickstart-templates) , és keresse meg a karakterláncot tartalmazó mappák `vmss`.

Ha már ismeri a sablonok létrehozásával, kihagyhatja a "Következő lépések" szakaszban megtudhatja, hogyan módosíthatja ezt a sablont.

## <a name="review-the-template"></a>Tekintse át a sablon

Tekintse át a minimális működőképes méretezési csoport sablonjában, a GitHub használatával [azuredeploy.json](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json).

Ebben az oktatóanyagban most vizsgálja meg a diff (`git diff master minimum-viable-scale-set`) a minimális működőképes méretezési csoport létrehozása sablon darab által darab beállítása.

## <a name="define-schema-and-contentversion"></a>$Schema és contentVersion megadása
Először határozzon meg `$schema` és `contentVersion` a sablonban. A `$schema` elem határozza meg a sablon nyelvi verzióját, és a Visual Studio Szintaxiselemek kiemelése és hasonló érvényesítési szolgáltatások használatos. A `contentVersion` elem nem használja az Azure-ban. Ehelyett segít is nyomon követheti a tanúsítványsablon verziójának.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
```
## <a name="define-parameters"></a>Paraméterek megadása
Ezt követően adja meg a két paramétert `adminUsername` és `adminPassword`. A paraméterek olyan értékek, megadhatja a központi telepítési idején. A `adminUsername` paraméter egyszerűen egy `string` típusa, de mivel `adminPassword` egy titkos, adja meg, írja be a `securestring`. Később ezek a paraméterek vannak átadott a méretezésicsoport-konfigurációt.

```json
  "parameters": {
    "adminUsername": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    }
  },
```
## <a name="define-variables"></a>Változók meghatározása
Resource Manager-sablonok segítségével később a sablonban használandó változókat határozhat meg is. A példában minden változó nem használ, ezért a JSON-objektum üres.

```json
  "variables": {},
```

## <a name="define-resources"></a>Erőforrások meghatározása
Ezután a rendszer a sablon erőforrás szakaszába. Itt akkor megadhatja, hogy milyen ténylegesen üzembe helyezéséhez. Ellentétben `parameters` és `variables` (amely olyan a JSON-objektumok), `resources` JSON-objektumok JSON listája.

```json
   "resources": [
```

Összes erőforrást igényelnek `type`, `name`, `apiVersion`, és `location` tulajdonságait. Ez a példa első erőforrás típusa rendelkezik `Microsft.Network/virtualNetwork`nevű `myVnet`, és API-verzió `2016-03-30`. (A legújabb API-verzió az erőforrástípushoz, lásd: a [Azure REST API-dokumentáció](https://docs.microsoft.com/rest/api/).)

```json
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "apiVersion": "2016-12-01",
```

## <a name="specify-location"></a>Hely megadása
A virtuális hálózat helyének megadásához használja a [Resource Manager-sablon függvény](../azure-resource-manager/resource-group-template-functions.md). Ez a függvény zárójelbe kell foglalni a idézőjelek között, és ehhez hasonló szögletes zárójelek: `"[<template-function>]"`. Ebben az esetben használja a `resourceGroup` függvény. Ez fogadja a argumentumok nélkül, és adja vissza egy JSON-objektum-metaadatait, az erőforráscsoport, a központi telepítés telepítése. Az erőforráscsoport központi telepítés alkalmával a felhasználó által van beállítva. Ez az érték majd be a JSON-objektum az indexelt `.location` beolvasni a hely JSON-objektumból.

```json
       "location": "[resourceGroup().location]",
```

## <a name="specify-virtual-network-properties"></a>Adja meg a virtuális hálózat tulajdonságai
Minden egyes Resource Manager-erőforrással rendelkezik a saját `properties` konfigurációk jellemző az erőforrás a következő szakaszban. Ebben az esetben adja meg, hogy a virtuális hálózathoz kell rendelkeznie a magánhálózati IP-címtartomány használatával több alhálózat `10.0.0.0/16`. Egy méretezési csoportot mindig szerepel egy alhálózaton belül. Alhálózatok nem terjedhetnek ki.

```json
       "properties": {
         "addressSpace": {
           "addressPrefixes": [
             "10.0.0.0/16"
           ]
         },
         "subnets": [
           {
             "name": "mySubnet",
             "properties": {
               "addressPrefix": "10.0.0.0/16"
             }
           }
         ]
       }
     },
```

## <a name="add-dependson-list"></a>Adja hozzá a dependsOn listája
A szükséges mellett `type`, `name`, `apiVersion`, és `location` tulajdonságai között az egyes erőforrások rendelkezhet egy választható `dependsOn` karakterláncok listáját. Ez a lista megadja, hogy az erőforrás üzembe helyezése előtt Befejezés más erőforrások, a központi telepítésből.

Ebben az esetben nincs csak egy elemet a listában, az előző példában a virtuális hálózat. A függőség meg kell adnia, mert a méretezési csoport szükség van a hálózaton található olyan virtuális gépek létrehozása előtt. Ezzel a módszerrel a méretezési biztosíthat ezen virtuális gépek magánhálózati IP-címek hálózati tulajdonságok korábban megadott IP-címtartományból. A dependsOn listája az egyes karakterlánc formátuma `<type>/<name>`. Ugyanaz, mint `type` és `name` korábban használt a virtuális hálózati erőforrás-definícióban.

```json
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "apiVersion": "2016-04-30-preview",
       "location": "[resourceGroup().location]",
       "dependsOn": [
         "Microsoft.Network/virtualNetworks/myVnet"
       ],
```
## <a name="specify-scale-set-properties"></a>Adja meg a méretezési csoport tulajdonságainak beállítása
A méretezési csoportok a méretezési csoportban lévő virtuális gépek testreszabásának számos tulajdonságokkal rendelkezik. Ezek a tulajdonságok teljes listáját lásd: a [méretezési csoport REST API-dokumentáció](https://docs.microsoft.com/rest/api/virtualmachinescalesets/create-or-update-a-set). Ebben az oktatóanyagban csak néhány gyakran használt tulajdonságok vannak beállítva.
### <a name="supply-vm-size-and-capacity"></a>Adja meg a virtuális gép mérete és a kapacitás
A méretezési csoport a virtuális gép létrehozása ("sku name") milyen méretű tudnia kell, és hány ezekhez a virtuális gépekhez ("kapacitás" sku) létrehozásához. Melyik Virtuálisgép-méret érhető el, olvassa el a [Virtuálisgép-méretek dokumentáció](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes).

```json
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
       },
```

### <a name="choose-type-of-updates"></a>A frissítések típusának kiválasztása
A méretezési csoportot is tudnia kell, hogyan kezelje a frissítések a méretezési csoportban. Két lehetőség van jelenleg `Manual` és `Automatic`. A kettő közötti különbségekről további információkért tekintse meg a dokumentációt [frissítése a méretezési](./virtual-machine-scale-sets-upgrade-scale-set.md).

```json
       "properties": {
         "upgradePolicy": {
           "mode": "Manual"
         },
```

### <a name="choose-vm-operating-system"></a>Válassza ki a virtuális gép operációs rendszer
A méretezési csoport milyen operációs rendszert a virtuális gépek elhelyezése tudnia kell. A virtuális gépek itt, hozzon létre egy teljes körűen javított Ubuntu 16.04-LTS-rendszerképhez.

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.04-LTS",
               "version": "latest"
             }
           },
```

### <a name="specify-computernameprefix"></a>Adja meg a gépnév előtagja
A méretezési csoportban több virtuális gépet helyez üzembe. Helyett adja meg mindegyik virtuális gép nevét, adja meg a `computerNamePrefix`. A méretezési fűz index előtagot az egyes virtuális Gépekhez, így a virtuális gépek nevei `<computerNamePrefix>_<auto-generated-index>`.

Az alábbi kódrészletben a paramétereket, mielőtt a használatával állítsa be a rendszergazdai felhasználónevet és jelszót a méretezési csoportban lévő virtuális gépekre. Ez a folyamat használja a `parameters` sablonfüggvény. Ez a függvény egy karakterlánc, amely meghatározza, mely paraméterrel lehet hivatkozni, és visszaadja, hogy a paraméter értéke vesz igénybe.

```json
           "osProfile": {
             "computerNamePrefix": "vm",
             "adminUsername": "[parameters('adminUsername')]",
             "adminPassword": "[parameters('adminPassword')]"
           },
```

### <a name="specify-vm-network-configuration"></a>Adja meg a virtuális gép hálózati konfigurációja
Végül adja meg a hálózati konfigurációt a virtuális gépek a méretezési csoportban. Ebben az esetben csak kell a korábban létrehozott alhálózati azonosító megadása. A méretezési csoport helyezni ezt az alhálózatot a hálózati adapterek azonban igen.

A virtuális hálózat, amely tartalmazza az alhálózat használatával a Azonosítójának lekéréséhez a `resourceId` sablonfüggvény. Ez a funkció a típusa és a egy erőforrás nevét, és adja vissza a teljes azonosítót az erőforrás. Ez az azonosító formában van: `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`

A virtuális hálózat azonosítója viszont nem elegendő. Adja meg az adott alhálózat, a méretezési csoport, hogy virtuális gépeket kell lennie. Ehhez összefűzni `/subnets/mySubnet` , a virtuális hálózat azonosítója. Ez az alhálózat teljesen minősített azonosítója. Ez az összefűzés tegye a `concat` függvény, amely fogadja a karakterláncok egy sorozatát, és azok összefűzésével adja vissza.

```json
           "networkProfile": {
             "networkInterfaceConfigurations": [
               {
                 "name": "myNic",
                 "properties": {
                   "primary": "true",
                   "ipConfigurations": [
                     {
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
                           "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
                         }
                       }
                     }
                   ]
                 }
               }
             ]
           }
         }
       }
     }
   ]
}

```

## <a name="next-steps"></a>További lépések

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
