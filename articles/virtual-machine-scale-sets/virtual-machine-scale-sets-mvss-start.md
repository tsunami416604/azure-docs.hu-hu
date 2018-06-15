---
title: Virtuálisgép-méretezési készlet sablonok megismerése |} Microsoft Docs
description: Ismerje meg a virtuálisgép-méretezési csoportok minimális életképes méretezési készlet sablon létrehozása
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
ms.topic: article
ms.date: 06/01/2017
ms.author: negat
ms.openlocfilehash: 5cd495d1332c71d7eae775f933b73d98826f10e4
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2017
ms.locfileid: "26781255"
---
# <a name="learn-about-virtual-machine-scale-set-templates"></a>További tudnivalók a virtuálisgép-méretezési készlet sablonok
Az [Azure Resource Manager-sablonok](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment) remek megoldást kínálnak egymáshoz kapcsolódó erőforráscsoportok üzembe helyezésére. Az oktatóanyag adatsorozat egy minimális életképes méretezési készlet sablon létrehozása és a különböző forgatókönyvekben megfelelően sablon módosításával jeleníti meg. Minden példában ez származhat [GitHub-tárházban](https://github.com/gatneil/mvss). 

Ez a sablon olyan egyszerűek legyenek. Skála teljesebb példákat sablonok, olvassa el a [Azure gyors üzembe helyezési sablonokat GitHub-tárházban](https://github.com/Azure/azure-quickstart-templates) és a keresési karakterláncot tartalmazó mappák `vmss`.

Ha már ismeri a sablonok létrehozásával, ugorjon a "További lépések" című szakaszt a ismerheti meg a sablon módosítására.

## <a name="review-the-template"></a>Tekintse át a sablon

Tekintse át a sablon a minimális életképes méretezési beállítása a GitHub segítségével [azuredeploy.json](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json).

Ebben az oktatóanyagban vizsgáljuk meg a diff (`git diff master minimum-viable-scale-set`) a minimális életképes méretezési készlet létrehozása sablon adat által adat.

## <a name="define-schema-and-contentversion"></a>$Schema és contentVersion megadása
Először határozza meg `$schema` és `contentVersion` a sablonban. A `$schema` elem határozza meg a sablon nyelvi verzióját, és a Visual Studio szintaxis kiemelését és hasonló érvényesítési szolgáltatások használható. A `contentVersion` elem nem használja az Azure-ban. Ehelyett segít nyomon követjük, hogy a sablon verziója.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
```
## <a name="define-parameters"></a>Paraméterek megadása
Ezt követően adja meg a két paraméter `adminUsername` és `adminPassword`. A paraméterek olyan értékek adja meg, ha a telepítés során. A `adminUsername` paraméter egyszerűen egy `string` típusa, de mivel `adminPassword` egy titkos, adja meg, írja be az `securestring`. Később ezek a paraméterek vannak átadott a méretezési készlet konfigurációja.

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
Resource Manager-sablonok segítségével adja meg a sablont később használt változókat is. A példa nem használja a változó, ezért a JSON-objektum üres.

```json
  "variables": {},
```

## <a name="define-resources"></a>Adja meg az erőforrások
Ezután az a sablon a források szakaszában. Itt meg mi ténylegesen számára telepíteni kívánja. Eltérően `parameters` és `variables` (amelyeket JSON-objektumok), `resources` JSON JSON-objektumok listáját.

```json
   "resources": [
```

Összes erőforrást igényelnek `type`, `name`, `apiVersion`, és `location` tulajdonságok. Ez a példa első erőforrás fájltípusú `Microsft.Network/virtualNetwork`nevű `myVnet`, és apiVersion `2016-03-30`. (A legújabb API-verziót egy erőforrástípusra, lásd: a [Azure REST API-dokumentáció](https://docs.microsoft.com/rest/api/).)

```json
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "apiVersion": "2016-12-01",
```

## <a name="specify-location"></a>Adjon meg helyet
A virtuális hálózat helyének megadásához használja a [Resource Manager sablon függvény](../azure-resource-manager/resource-group-template-functions.md). Ez a funkció ajánlatok és a kapcsos zárójeleket kell tenni: `"[<template-function>]"`. Ebben az esetben használja a `resourceGroup` függvény. Veszi a argumentum nélkül, és az erőforráscsoportot, a központi telepítés telepítése kapcsolatos metaadatok egy JSON-objektumot ad vissza. Az erőforráscsoport a telepítés során a felhasználó által megadott. Ez az érték majd a JSON-objektum az az indexelt `.location` lekérni a hely JSON-objektumból.

```json
       "location": "[resourceGroup().location]",
```

## <a name="specify-virtual-network-properties"></a>Adja meg a virtuális hálózati tulajdonságok
Minden erőforrás-kezelő erőforrás rendelkezik saját `properties` szakasz az jellemző az erőforrás-konfigurációhoz. Ebben az esetben adja meg, hogy a virtuális hálózati kell-e a privát IP-címtartományt egy alhálózatot `10.0.0.0/16`. A méretezési mindig szerepel egy alhálózaton belül. Alhálózatok nem foglal magába.

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
A szükséges mellett `type`, `name`, `apiVersion`, és `location` tulajdonságok, az egyes erőforrások lehetnek egy nem kötelező `dependsOn` karakterláncok listáját. Ez a lista megadja, hogy más erőforrások, a központi telepítésből Befejezés ehhez az erőforráshoz telepítése előtt.

Ebben az esetben nincs csak egy elemet a listában, a virtuális hálózat az előző példából. A függőség adja meg, mert a méretezési kell a hálózaton található bármely virtuális gépek létrehozása előtt. Ezzel a módszerrel a méretezési a korábban a hálózati tulajdonságaiban megadott IP-címtartomány a biztosíthat a virtuális gépek magánhálózati IP-címét. A dependsOn listán minden karakterlánc formátuma `<type>/<name>`. Ugyanazon `type` és `name` korábban használt, a virtuális hálózati erőforrás-definícióban.

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
## <a name="specify-scale-set-properties"></a>Adja meg a skála tulajdonságainak beállítása
Méretezési csoportok a a méretezési csoportban lévő virtuális gépek testreszabásának sok tulajdonságokkal rendelkezik. Ezek a tulajdonságok teljes listáját lásd: a [méretezési REST API-dokumentáció](https://docs.microsoft.com/rest/api/virtualmachinescalesets/create-or-update-a-set). A jelen oktatóanyag esetében csak néhány gyakran használt vannak beállítva.
### <a name="supply-vm-size-and-capacity"></a>Adja meg a Virtuálisgép-méretet és a kapacitás
A méretezési milyen méretű virtuális gép ("sku name") létrehozásához tudnia kell, és hogy hány ilyen virtuális gép létrehozása ("termékváltozat-kapacitás"). Mely Virtuálisgép-méretek érhetők el, olvassa el a [Virtuálisgép-méretek dokumentáció](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes).

```json
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
       },
```

### <a name="choose-type-of-updates"></a>Válassza ki a kívánt frissítések típusát
A méretezési készletben is tudnia kell, hogy hogyan kezeljék a méretezési csoportban lévő frissítések. Jelenleg nincsenek két lehetőség `Manual` és `Automatic`. A kettő közötti különbségekkel kapcsolatos további információkért lásd a dokumentációt [frissítése egy méretezési](./virtual-machine-scale-sets-upgrade-scale-set.md).

```json
       "properties": {
         "upgradePolicy": {
           "mode": "Manual"
         },
```

### <a name="choose-vm-operating-system"></a>Válassza ki a virtuális gép operációs rendszer
A méretezési milyen operációs rendszert a virtuális gépek elhelyezése tudnia kell. Itt hozzon létre a virtuális gépeket egy teljes mértékben javított Ubuntu 16.04-es lts verzió lemezképet.

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
A méretezési több virtuális gép telepítését. Minden virtuális gép nevének megadása helyett adja meg a `computerNamePrefix`. A méretezési hozzáfűzi a index előtag az egyes virtuális gépek, így a virtuális gép nevei `<computerNamePrefix>_<auto-generated-index>`.

A következő kódrészletet a paramétereket, mielőtt a segítségével állítsa be a rendszergazdai jogosultságú felhasználónevet és jelszót a méretezési csoportban lévő virtuális gép esetében. Ez a folyamat használja a `parameters` sablonfüggvény. Ez a függvény egy karakterlánc, amely meghatározza, melyik paraméterrel lehet hivatkozni, és kiírja, hogy a paraméter értéke időt vesz igénybe.

```json
           "osProfile": {
             "computerNamePrefix": "vm",
             "adminUsername": "[parameters('adminUsername')]",
             "adminPassword": "[parameters('adminPassword')]"
           },
```

### <a name="specify-vm-network-configuration"></a>Adja meg a Virtuálisgép-hálózati konfiguráció
Végül adja meg a virtuális gépek hálózati beállításait a méretezési csoportban lévő. Ebben az esetben csak szeretné adja meg a korábban létrehozott alhálózati Azonosítót. Ez alapján a méretezési készletben, amelyre a hálózati adapterek az alhálózaton.

Kaphat, hogy a virtuális hálózat használatával a alhálózati tartalmazó Azonosítóját a `resourceId` sablonfüggvény. Ez a funkció időt vesz igénybe, a típus és az erőforrás neve, és az adott erőforrás teljesen minősített azonosítójának beolvasása. Ezt az Azonosítót az űrlap rendelkezik:`/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`

Azonban a virtuális hálózat azonosító nem elegendő. Adja meg, hogy a méretezési virtuális gépek az adott alhálózaton kell lennie. Ehhez összefűzésére `/subnets/mySubnet` számára a virtuális hálózat Azonosítójává. A teljesen minősített azonosítója az alhálózat eredménye. A kapott, tegye a `concat` funkció, amely a karakterláncok több időt vesz igénybe, és visszaadja a kapott.

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
