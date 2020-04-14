---
title: További információ a virtuálisgép-méretezési csoport sablonjairól
description: Ismerje meg, hogyan hozhat létre egy egyszerű méretezési csoport sablon azure virtuálisgép-méretezési készletek több egyszerű lépésben.
author: mimckitt
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: mimckitt
ms.openlocfilehash: 9c6a30a5f08b33adfa515973962236516f34fbf3
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273392"
---
# <a name="learn-about-virtual-machine-scale-set-templates"></a>További információ a virtuálisgép-méretezési csoport sablonjairól
Az [Azure Resource Manager-sablonok](https://docs.microsoft.com/azure/azure-resource-manager/template-deployment-overview#template-deployment-process) remek megoldást kínálnak egymáshoz kapcsolódó erőforráscsoportok üzembe helyezésére. Ez az oktatóanyag-sorozat bemutatja, hogyan hozhat létre egy alapszintű méretezési csoport sablont, és hogyan módosíthatja ezt a sablont, hogy megfeleljen a különböző forgatókönyvek. Minden példa ebből a [GitHub-tárházból származik.](https://github.com/gatneil/mvss)

Ez a sablon célja, hogy egyszerű legyen. A méretezési csoport sablonjainak részletesebb példáit az [Azure gyorsútmutatósablonok GitHub-tárházában](https://github.com/Azure/azure-quickstart-templates) talál, és megkeresheti a karakterláncot tartalmazó mappákat. `vmss`

Ha már ismeri a sablonok létrehozását, ugorjon a "Következő lépések" szakaszra, és tekintse meg, hogyan módosíthatja ezt a sablont.

## <a name="define-schema-and-contentversion"></a>$schema és contentVersion definiálása
Először `$schema` definiálja, és `contentVersion` a sablonban. Az `$schema` elem határozza meg a sablon nyelvének verzióját, és a Visual Studio szintaxiskiemeléséhez és hasonló érvényesítési szolgáltatásaihoz használatos. Az `contentVersion` elemet az Azure nem használja. Ehelyett segít nyomon követni a sablon verzióját.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
```

## <a name="define-parameters"></a>Paraméterek megadása
Ezután adjon meg `adminUsername` két `adminPassword`paramétert, és . A paraméterek a telepítés időpontjában megadott értékek. A `adminUsername` paraméter egyszerűen `string` egy típus, hanem azért, `adminPassword` `securestring`mert egy titkos, hogy ez a típus . Később ezek a paraméterek átkerülnek a méretezési csoport konfigurációjába.

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
Az Erőforrás-kezelő sablonjai lehetővé teszik a sablon későbbi részében használandó változók definiálását is. A példa nem használ változókat, így a JSON-objektum üres.

```json
  "variables": {},
```

## <a name="define-resources"></a>Erőforrások meghatározása
A következő a sablon erőforrásszakasza. Itt határozhatja meg, hogy valójában mit szeretne telepíteni. Ellentétben, `parameters` és `variables` (amelyek JSON objektumok), `resources` egy JSON listája JSON objektumok.

```json
   "resources": [
```

Minden erőforráshoz `name` `apiVersion`szükség `location` van `type`, , és tulajdonságokra. A példa első erőforrása [Microsoft.Network/virtualNetwork](/azure/templates/microsoft.network/virtualnetworks) `myVnet`, name `2018-11-01`és apiVersion típusú. (Az erőforrástípus legújabb API-verziójának megkereséséhez tekintse meg az [Azure Resource Manager sablonhivatkozását.)](/azure/templates/)

```json
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "apiVersion": "2018-11-01",
```

## <a name="specify-location"></a>Hely megadása
A virtuális hálózat helyének megadásához használjon [Erőforrás-kezelő sablon függvényt.](../azure-resource-manager/templates/template-functions.md) Ezt a funkciót a következőhez hasonló idézőjelek `"[<template-function>]"`és szögletes zárójelek közé kell tenni: . Ebben az esetben `resourceGroup` használja a funkciót. Nem vesz fel argumentumokat, és egy JSON-objektumot ad vissza metaadatokkal arról az erőforráscsoportról, amelybe a központi telepítés van telepítve. Az erőforráscsoportot a felhasználó állítja be a telepítés időpontjában. Ezt az értéket ezután indexeli `.location` ebbe a JSON-objektumba, hogy a json-objektumból leszámítsa a helyet.

```json
       "location": "[resourceGroup().location]",
```

## <a name="specify-virtual-network-properties"></a>Virtuális hálózati tulajdonságok megadása
Minden Erőforrás-kezelő erőforrás `properties` saját szakaszt rendelkezik az erőforrásra jellemző konfigurációkhoz. Ebben az esetben adja meg, hogy a virtuális hálózat nak `10.0.0.0/16`egy alhálózattal kell rendelkeznie a saját IP-címtartomány használatával. A méretezési készlet mindig egy alhálózatban található. Alhálózatokra nem terjed ki.

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

## <a name="add-dependson-list"></a>Depends dependson lista hozzáadása
A szükséges `type`, `name`, `apiVersion`, `location` és tulajdonságok mellett minden `dependsOn` erőforrás nak lehet egy választható karakterlánclistája. Ez a lista határozza meg, hogy a központi telepítés ből származó erőforrásoknak mely erőforrásokat kell befejezniük az erőforrás üzembe helyezése előtt.

Ebben az esetben csak egy elem van a listában, a virtuális hálózat az előző példából. Adja meg ezt a függőséget, mert a méretezési készlet nek szüksége van a hálózat létezésére a virtuális gépek létrehozása előtt. Így a méretezési csoport adhat ezeknek a virtuális gépeknek a hálózati tulajdonságokban korábban megadott IP-címtartományból származó privát IP-címeket. A dependsOn listában szereplő egyes `<type>/<name>`karakterláncok formátuma . Használja ugyanazt, `type` és `name` korábban használt a virtuális hálózati erőforrás-definíció.

```json
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "apiVersion": "2019-03-01",
       "location": "[resourceGroup().location]",
       "dependsOn": [
         "Microsoft.Network/virtualNetworks/myVnet"
       ],
```
## <a name="specify-scale-set-properties"></a>Méretezési halmaz tulajdonságainak megadása
A méretezési csoportok számos tulajdonsággal rendelkeznek a méretezési csoportban lévő virtuális gépek testreszabásához. Ezeknek a tulajdonságoknak a teljes listáját a [sablon hivatkozási részében olvashatja.](/azure/templates/microsoft.compute/virtualmachinescalesets) Ebben az oktatóanyagban csak néhány gyakran használt tulajdonság van beállítva.
### <a name="supply-vm-size-and-capacity"></a>A virtuális gép mérete és kapacitása
A méretezési csoport tudnia kell, hogy milyen méretű virtuális gép létrehozásához ("sku név") és hány ilyen virtuális gépek létrehozásához ("sku kapacitás"). A virtuális gépek méreteiről a [Virtuálisgép-méretek dokumentációban](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes)olvashat.

```json
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
       },
```

### <a name="choose-type-of-updates"></a>A frissítések típusának kiválasztása
A méretezési készlet is tudnia kell, hogyan kell kezelni a frissítéseket a méretezési készlet. Jelenleg három lehetőség `Manual`van, `Rolling` `Automatic`és . A kettő közötti különbségekről további információt a [méretezési csoport frissítésének dokumentációjában](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)talál.

```json
       "properties": {
         "upgradePolicy": {
           "mode": "Manual"
         },
```

### <a name="choose-vm-operating-system"></a>VM operációs rendszer kiválasztása
A méretezési készlet tudnia kell, hogy milyen operációs rendszert kell helyezni a virtuális gépeken. Itt hozza létre a virtuális gépeket egy teljesen javított Ubuntu 16.04-LTS lemezképpel.

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

### <a name="specify-computernameprefix"></a>SzámítógépNamePrefix megadása
A méretezési készlet több virtuális gépet telepít. Ahelyett, hogy minden virtuális gép `computerNamePrefix`nevét megnevezné, adja meg a . A méretezési készlet hozzáfűz egy indexet az előtag hoz minden `<computerNamePrefix>_<auto-generated-index>`virtuális géphez, így a virtuálisgép-nevek rendelkeznek az űrlapgal.

A következő kódrészletben használja a paramétereket korábban a rendszergazdai felhasználónév és jelszó beállításához a méretezési csoport összes virtuális gépéhez. Ez a `parameters` folyamat a sablon függvényt használja. Ez a függvény egy karakterláncot vesz fel, amely meghatározza, hogy melyik paraméterre hivatkozzon, és adja ki az adott paraméter értékét.

```json
           "osProfile": {
             "computerNamePrefix": "vm",
             "adminUsername": "[parameters('adminUsername')]",
             "adminPassword": "[parameters('adminPassword')]"
           },
```

### <a name="specify-vm-network-configuration"></a>Virtuálisgép-hálózat konfigurációjának megadása
Végül adja meg a méretezési csoportban lévő virtuális gépek hálózati konfigurációját. Ebben az esetben csak a korábban létrehozott alhálózat azonosítóját kell megadnia. Ez megmondja a méretezési készlet, hogy a hálózati adapterek ebben az alhálózatban.

Az alhálózatot tartalmazó virtuális hálózat azonosítóját a `resourceId` sablonfüggvény segítségével szerezheti be. Ez a függvény egy erőforrás típusát és nevét veszi fel, és az adott erőforrás teljesen minősített azonosítóját adja vissza. Ez az azonosító a következő formában található:`/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`

A virtuális hálózat azonosítója azonban nem elegendő. Adja meg azt az alhálózatot, amelyben a méretezési készlet virtuális gépeknek kell lenniük. Ehhez fűzze `/subnets/mySubnet` össze a virtuális hálózat azonosítóját. Az eredmény az alhálózat teljesen minősített azonosítója. Ezt fűzzék össze `concat` a funkcióval, amely karakterláncok sorozatát veszi fel, és visszaadja azok összefűzését.

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
