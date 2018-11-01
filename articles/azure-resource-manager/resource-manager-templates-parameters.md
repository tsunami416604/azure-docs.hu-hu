---
title: Az Azure Resource Manager-sablon paraméterek szakaszához |} A Microsoft Docs
description: Az Azure Resource Manager-sablonok deklaratív JSON-szintaxist használva a paraméterek szakasza ismerteti.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: tomfitz
ms.openlocfilehash: 83ba1b94413990c0eb8dff42c49d46456a658d5a
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50417769"
---
# <a name="parameters-section-of-azure-resource-manager-templates"></a>Paraméterek szakaszban az Azure Resource Manager-sablonok
A sablon a Paraméterek szakaszban megadhatja az erőforrások üzembe helyezésekor mely értékeket kell megadni. Ezek a paraméterértékek szabhatja testre az üzembe helyezés értékek, amelyek azáltal, hogy egy adott környezetben (például fejlesztési, tesztelési és éles környezetben). Nem kell adnia a sablonban szereplő paraméterekkel, de paraméterek nélkül a sablon mindig telepít az azonos nevek, helyek és tulajdonságok ugyanazokhoz az erőforrásokhoz.

Most már legfeljebb 256 paramétereket a sablonban. A paraméterek számát, csökkentheti a több tulajdonságot tartalmazó objektumok használatával, ahogyan az ebben a cikkben.

## <a name="define-and-use-a-parameter"></a>Határozza meg, és a egy paraméterrel

Az alábbi példa bemutatja egy egyszerű paraméterdefinícióhoz. A paraméter nevét adja meg, és meghatározza, hogy egy karakterlánc értéket vesz igénybe. A paraméter csak fogad értékeket, amelyek használat bírnak. Azt adja meg az alapértelmezett értéket, amikor nem érték van megadva üzembe helyezés során. Végül a paraméter használatakor leírását tartalmazza. 

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  }   
}
```

A sablon a következő szintaxissal a paraméter értékére hivatkozik:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

## <a name="available-properties"></a>Rendelkezésre álló tulajdonságok

Az előző példa bemutatta, csak az egyes tulajdonságok segítségével használhatja a paraméterek szakaszához. A rendelkezésre álló tulajdonságok a következők:

```json
"parameters": {
    "<parameter-name>" : {
        "type" : "<type-of-parameter-value>",
        "defaultValue": "<default-value-of-parameter>",
        "allowedValues": [ "<array-of-allowed-values>" ],
        "minValue": <minimum-value-for-int>,
        "maxValue": <maximum-value-for-int>,
        "minLength": <minimum-length-for-string-or-array>,
        "maxLength": <maximum-length-for-string-or-array-parameters>,
        "metadata": {
            "description": "<description-of-the parameter>" 
        }
    }
}
```

| Elem neve | Szükséges | Leírás |
|:--- |:--- |:--- |
| parameterName |Igen |A paraméter neve. Érvényes JavaScript-azonosítónak kell lennie. |
| type |Igen |A paraméter értékének típusa. Az engedélyezett típusokkal és az értékek **karakterlánc**, **securestring**, **int**, **bool**, **objektum**, **secureObject**, és **tömb**. |
| DefaultValue érték |Nem |A paramétert, ha a nem érték van megadva a paraméter alapértelmezett értéke. |
| allowedValues |Nem |Győződjön meg arról, hogy a megfelelő értéket adott-e, hogy a paraméter megengedett értékek tömbje. |
| a minValue |Nem |Int típusú paraméterekhez minimális értéke, ez az érték, amely naptól. |
| maxValue |Nem |A maximális int típusú paraméterekhez, ez az érték értéke is beleértve. |
| a minLength |Nem |A minimális string, securestring és array típusú paraméterekhez, ez az érték hossza között lehet. |
| maxLength |Nem |String, securestring és array típusú paraméterekhez maximális hosszát, ezt az értéket, amely naptól. |
| leírás |Nem |A portálon keresztül a felhasználók számára megjelenő paraméter leírása. |

## <a name="template-functions-with-parameters"></a>A paraméterekkel sablonfüggvények

Egy paraméter alapértelmezett értékének megadása esetén a legtöbb sablonfüggvények is használhatja. Egy másik paraméter értékének használatával hozhat létre egy alapértelmezett értéket. A következő sablon az alapértelmezett érték a functions használatát mutatja be:

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

Nem használhatja a `reference` függvény a Paraméterek szakaszban. Paraméterek értékeli ki a telepítés előtt úgy a `reference` függvény nem lehet lekérni egy erőforrást futási állapotát. 

## <a name="objects-as-parameters"></a>Objektumok paraméterek

Kapcsolódó értékek rendszerezheti azokat objektumként átadásával könnyebben lehet. Ez a megközelítés a paraméterek számát, a sablonban is csökkenti.

Adja meg a paramétert a sablont, és adja meg a JSON-objektum egyetlen érték helyett üzembe helyezés során. 

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "location": "eastus",
      "addressPrefixes": [
        {
          "name": "firstPrefix",
          "addressPrefix": "10.0.0.0/22"
        }
      ],
      "subnets": [
        {
          "name": "firstSubnet",
          "addressPrefix": "10.0.0.0/24"
        },
        {
          "name": "secondSubnet",
          "addressPrefix": "10.0.1.0/24"
        }
      ]
    }
  }
},
```

Ezután hivatkozhat a altulajdonságokat paraméter a pont operátor használatával.

```json
"resources": [
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

## <a name="recommendations"></a>Javaslatok
Paraméterek való munka során hasznos lehet a következő információkat:

* Minimalizálja a paraméterek használatát. Amikor csak lehetséges, használja a változó vagy konstans érték. Paraméterek használata csak az ilyen feladatokhoz szükséges:
   
   * Környezet (Termékváltozat, méret, kapacitás) megfelelően változata használni kívánt beállításokat.
   * Erőforrás neve, amely egyszerű azonosító szeretne megadni.
   * Egyéb (például rendszergazdai felhasználójának neve) a feladatokat gyakran használt értékeket.
   * A titkos kulcsokat (például jelszavakat).
   * A szám vagy értékek tömbje az erőforrástípus több példány létrehozásakor használja.
* Nagybetűs használata a paraméterek nevei.
* Adja meg a metaadatokban minden paraméter leírása:

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* (Kivéve a jelszó és SSH-kulcsok) paraméterek alapértelmezett értékeinek meghatározása. Alapértelmezett érték megadásával a paraméter lesz választható központi telepítése során. Az alapértelmezett érték egy üres karakterlánc lehet. 
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* Használat **securestring** jelszavak és a titkos kulcsok. Ha a bizalmas adatokat egy JSON-objektumot adja át, a **secureObject** típusa. Sablonparaméterek securestring vagy secureObject típusú erőforrás üzembe helyezés után nem lehet olvasni. 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* Egy paraméter használatával adja meg a helyet, és megoszthatja a lehető legnagyobb mértékben paraméterérték erőforrások, amelyek ugyanazon a helyen valószínűleg. Ez a megközelítés minimálisra csökkenti a helyre vonatkozó adatokat adja meg a rendszer kéri a felhasználóktól számát. Ha egy erőforrás típusa támogatott helyek száma korlátozott, érdemes adjon meg egy érvényes helyet közvetlenül a sablonban, vagy egy másik hely paraméter hozzáadásához. Amikor egy szervezet korlátozza a felhasználók számára az engedélyezett régiók a **resourceGroup () .location** kifejezés megakadályozhatja a felhasználó helyezi üzembe a sablont. Például egy felhasználó egy régióban egy erőforráscsoportot hoz létre. Egy második felhasználó az adott erőforráscsoporton kell telepíteni, de nem rendelkezik hozzáféréssel ahhoz a régióhoz. 
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[parameters('location')]",
         ...
     }
   ]
   ```
    
* Kerülje a következő paraméternek vagy változónak az API-verzió, az erőforrástípushoz. Erőforrás-tulajdonságok és értékek verziószám alapján változhat. A Kódszerkesztő az IntelliSense nem állapítható meg a megfelelő sémát, ha egy paraméter vagy változó értéke az API-verziót. Ehelyett a sablonban rögzítse szoftveresen az API-verzió.
* Ne adjon meg a paraméter neve, amely megfelel a telepítési parancs egy paramétert a sablonban. Resource Manager az elnevezési ütközés feloldása az utótag hozzáadásával **FromTemplate** sablon paraméteréhez. Például, ha nevű paraméter adja meg az **ResourceGroupName** a sablonban ütközik a **ResourceGroupName** paramétert a [New-AzureRmResourceGroupDeployment ](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) parancsmagot. Üzembe helyezés során a kéri adjon meg egy értéket a **ResourceGroupNameFromTemplate**.

## <a name="example-templates"></a>Példa sablonok

A példa sablonok bemutatják, bizonyos forgatókönyvek paraméterek használatával. Tesztelje a paraméterek kezelésének módját a különböző helyzetekben való telepítéséhez.

|Sablon  |Leírás  |
|---------|---------|
|[a functions alapértelmezett értékeket a paraméterek](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Bemutatja, hogyan használható a sablonokban használható függvények paramétereinek alapértelmezett értékei meghatározásakor. A sablon nem üzembe erőforrásokat. Ez hoz létre a paraméterértékeket, és ezeket az értékeket ad vissza. |
|[A paraméter objektum](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Azt mutatja be, a paraméter egy objektum használatával. A sablon nem üzembe erőforrásokat. Ez hoz létre a paraméterértékeket, és ezeket az értékeket ad vissza. |

## <a name="next-steps"></a>További lépések

* A különböző megoldástípusokhoz használható teljes sablonok megtekintéséhez lásd: [Azure gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates/).
* Bemeneti paraméter értékét üzembe helyezés során, olvassa el a [alkalmazás üzembe helyezése Azure Resource Manager-sablonnal](resource-group-template-deploy.md). 
* A sablonon belül használhatja függvényeivel kapcsolatos részletekért lásd: [Azure Resource Manager-Sablonfüggvények](resource-group-template-functions.md).
* Egy paraméter objektum használatával kapcsolatos információkért lásd: [objektum használata paraméterként egy Azure Resource Manager-sablonban](/azure/architecture/building-blocks/extending-templates/objects-as-parameters).
