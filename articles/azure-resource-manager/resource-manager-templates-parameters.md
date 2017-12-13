---
title: "Az Azure Resource Manager sablon paraméter szakaszában |} Microsoft Docs"
description: "Ismerteti a Paraméterek szakaszban az Azure Resource Manager-sablonok deklaratív JSON-szintaxis használatával."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/11/2017
ms.author: tomfitz
ms.openlocfilehash: 7d0f53751bf529d52c156a8b9319b10560eb8997
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2017
---
# <a name="parameters-section-of-azure-resource-manager-templates"></a>Paraméterek szakaszban az Azure Resource Manager-sablonok
A sablon a Paraméterek szakaszban adja meg az erőforrások telepítése során is bemeneti értékeket. A paraméterértékek lehetővé teszik a központi telepítés testreszabása egy adott környezetben (például a fejlesztői, tesztelési és éles) is lefednek értékek megadásával. A sablon a paraméterek megadása nem szükséges, de a paraméterek nélkül a sablon mindig központilag ugyanazon a nevét, helyét és tulajdonságok ugyanazokat az erőforrásokat.

Azonban legfeljebb 255 paramétereket a sablonban. A paraméterek számát objektumokat, amelyek több tulajdonságainak megjelenítése a cikkben, csökkentheti.

## <a name="define-and-use-a-parameter"></a>Adja meg, és egy paraméterrel

Az alábbi példában egy egyszerű paraméterek definícióját. A paraméter nevét adja meg, és meghatározza, hogy egy karakterláncértéket vesz igénybe. A paraméter csak a való használat jelentéssel bírnak értékeket fogad el. Ha nem ad meg értéket központi telepítés során megadott alapértelmezett értéket határoz meg. Végezetül a paraméter használata leírását tartalmazza. 

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

A sablon hivatkozik a következő szintaxissal paraméter értékét:

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

Az előző példában bemutatta, csak az egyes tulajdonságok a paraméter szakaszban használható. A rendelkezésre álló tulajdonságok a következők:

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
| parameterName |Igen |A paraméter neve. Egy érvényes JavaScript-azonosítónak kell lennie. |
| type |Igen |A paraméter értékének típusa. Az engedélyezett típusokkal és az értékek **karakterlánc**, **secureString**, **int**, **bool**, **objektum**, **secureObject**, és **tömb**. |
| DefaultValue érték |Nem |A paraméternek, ha nincs érték megadva, a paraméter alapértelmezett értéke. |
| Storageaccount_accounttype |Nem |Győződjön meg arról, hogy a megfelelő érték van megadva a paraméter megengedett értékei tömbje. |
| A MinValue |Nem |A minimális int típusú paraméterekhez, ez az érték értéke között lehet. |
| MaxValue |Nem |A maximális int típusú paraméterekhez, ez az érték értéke között lehet. |
| a minLength |Nem |A minimális string, secureString és array típusú paraméterekhez, ez az érték hossza határokat is beleértve. |
| MaxLength |Nem |A string, secureString és array típusú paraméterekhez, ez az érték hossza legfeljebb határokat is beleértve. |
| leírás |Nem |A paraméter, akkor jelenik meg, a felhasználók számára a portálon keresztül leírása. |

## <a name="template-functions-with-parameters"></a>Sablon függvényeket paraméterekkel

Ha az alapértelmezett értéket ad meg az egyik paraméter, legtöbb sablonfüggvényei is használhatja. Egy másik paraméter értéke hozhat létre egy alapértelmezett érték. A következő sablon bemutatja, hogy az alapértelmezett érték a funkciók:

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

Nem használhatja a `reference` függvény a Paraméterek szakaszban. Paraméterek kiértékelése központi telepítés előtt úgy a `reference` függvény nem tudja megszerezni egy erőforrást a futásidejű állapot. 

## <a name="objects-as-parameters"></a>Paraméterek és

Kapcsolódó érték rendezése úgy, hogy azokat objektumként könnyebben lehet. Ezt a módszert használja a sablon a paraméterek számát is csökkenti.

A paraméter a sablonban megadott, és adjon meg egyetlen érték helyett egy JSON-objektum üzembe helyezése során. 

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
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

Ezt követően hivatkozik a altulajdonságokat paraméter a pont operátor használatával.

```json
"resources": [
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('VNetSettings').name]",
    "location":"[resourceGroup().location]",
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
Az alábbi információ segítségével esetleg megállapítható, paraméterek használata:

* Minimalizálása érdekében a paraméterek használatával. Amikor csak lehetséges, használjon egy változó vagy konstansérték. Paraméterek csak ezek helyzetekben használhatja:
   
   * Környezet (SKU, méret, kapacitás) megfelelően változatait használni kívánt beállításokat.
   * Erőforrás neve, amely könnyebbé teszi a beazonosítást szeretne megadni.
   * (Például egy rendszergazda felhasználóneve) más feladatok elvégzéséhez gyakran használt értékek.
   * A titkos kulcsokat (például a jelszavak).
   * A szám vagy tömb erőforrástípus több példánya létrehozásakor használandó.
* -És nagybetűhasználattal teve a paraméterek nevei.
* A metaadatokban minden paraméter leírását adhatja meg:

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

* Határozza meg (kivéve a jelszavak és SSH-kulcsok) paraméter alapértelmezett értékét. Alapértelmezett érték megadásával a paraméter ilyenkor opcionális üzembe helyezése során. Az alapértelmezett érték lehet üres karakterlánc. 
   
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

* Használjon **SecureString** a jelszavak és a titkos kulcsok. A JSON-objektumból átadni a bizalmas adatokat, ha a **secureObject** típusa. Sablonparaméterek secureString vagy secureObject típusú erőforrások telepítése után nem lehet olvasni. 
   
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

* Amikor csak lehetséges, nem egy paraméter segítségével adjon meg helyet. Ehelyett használja a **hely** az erőforráscsoport tulajdonság. Használatával a **resourceGroup () .location** megadó kifejezést vár, a erőforrások a sablonban szereplő erőforrások vannak telepítve az erőforráscsoport és ugyanazon a helyen:
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         ...
     }
   ]
   ```
   
   Ha egy erőforrás típusa támogatott helyek csak korlátozott számú, érdemes adjon meg egy érvényes helyet közvetlenül a sablonban. Használata esetén egy **hely** paraméter, a lehető legnagyobb mértékben paraméterérték megosztása erőforrásokat, amelyek valószínűleg ugyanazon a helyen. Ez a megközelítés minimálisra csökkenti a szám, ahányszor a felhasználó felkérést kap arra, hogy helyére vonatkozó információkat.
* Kerülje a paraméter vagy változó erőforrástípus API-verzió számára. Erőforrás-tulajdonságok és értékek alapján verziószáma változhat. A kód szerkesztése az IntelliSense nem határozható meg, hogy a megfelelő sémát az API-verzió beállítása egy paraméter vagy változó. Ehelyett a sablonban merevlemez-kódot az API-verzió.
* Ne adjon meg a paraméter neve, amely megfelel a telepítési parancsot kell megadni a paraméter a sablonban. Erőforrás-kezelő a utótag hozzáadásával oldja fel a névütközés **FromTemplate** a sablon paraméterhez. Például, ha nevű paraméter **ResourceGroupName** a sablonban ütközik a **ResourceGroupName** paramétere a [új-AzureRmResourceGroupDeployment ](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) parancsmag. A telepítés során kéri adjon meg egy értéket a **ResourceGroupNameFromTemplate**.

## <a name="example-templates"></a>Példa sablonok

A példa sablonok bemutatják, bizonyos esetekben a paraméterek használatával. Tesztelje a különböző alkalmazási helyzetek kezelésének paraméterek való telepítéséhez.

|Sablon  |Leírás  |
|---------|---------|
|[az alapértelmezett értékek functions paraméterek](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Bemutatja, hogyan használja a sablon funkciók paramétereinek alapértelmezett értékei meghatározásakor. A sablon nem kell telepítenie minden olyan erőforrásnál. A paraméterértékek hoz létre, és ezeket az értékeket adja vissza. |
|[a paraméter objektum](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Azt mutatja be egy objektumot az egyik paraméter használatával. A sablon nem kell telepítenie minden olyan erőforrásnál. A paraméterértékek hoz létre, és ezeket az értékeket adja vissza. |

## <a name="next-steps"></a>Következő lépések

* A különböző megoldástípusokhoz használható teljes sablonok megtekintéséhez lásd: [Azure gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates/).
* A telepítés során az értékek a bemeneti tudnivalókat [Azure Resource Manager-sablon az alkalmazás központi telepítését](resource-group-template-deploy.md). 
* A sablonon belül használhatja a functions szolgáltatással kapcsolatos részletekért lásd: [Azure Resource Manager Sablonfüggvényei](resource-group-template-functions.md).
* Olyan parameter objektumot használatával kapcsolatos információkért lásd: [objektum használata Azure Resource Manager-sablon egyik paraméterének](/azure/architecture/building-blocks/extending-templates/objects-as-parameters).