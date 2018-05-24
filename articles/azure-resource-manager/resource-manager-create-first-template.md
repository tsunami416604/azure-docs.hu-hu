---
title: Az első Azure Resource Manager-sablon létrehozása | Microsoft Docs
description: Lépésenkénti útmutató az első Azure Resource Manager-sablon létrehozásához. Bemutatja, hogyan használható egy tárfiók sablonreferenciája a sablon létrehozásához.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/30/2018
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: 32ff8621eb446f9bcc45398173f47b761b929ac5
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358047"
---
# <a name="create-and-deploy-your-first-azure-resource-manager-template"></a>Az első Azure Resource Manager-sablon létrehozása ás üzembe helyezése
Ez a témakör bemutatja azon lépéseket, amelyekkel elkészítheti az első Resource Manager-sablonját. A Resource Manager-sablonok JSON-fájlok, melyek az adott megoldáshoz telepítendő erőforrásokat határozzák meg. Az Azure-megoldások telepítésével és kezelésével kapcsolatos fogalmak megismeréséhez lásd: [Az Azure Resource Manager áttekintése](resource-group-overview.md). Ha már rendelkezik erőforrásokkal, és azokhoz kíván sablont használni, lásd: [Azure Resource Manager-sablonok exportálása létező erőforrásokból](resource-manager-export-template.md).

A sablonok létrehozásához és átalakításához JSON-szerkesztő szükséges. A [Visual Studio Code](https://code.visualstudio.com/) egy könnyen használható, nyílt forráskódú, platformfüggetlen kódszerkesztő. A Resource Manager-sablonok létrehozásához kifejezetten javasoljuk a Visual Studio Code használatát. A cikk a VS Code használatát feltételezi. Ha más JSON-szerkesztője van (például a Visual Studio), azt is használhatja.

## <a name="prerequisites"></a>Előfeltételek

* Visual Studio Code. Ha szükséges, a következő címről telepíthető: [https://code.visualstudio.com/](https://code.visualstudio.com/).
* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
* [Azure PowerShell](/powershell/azure/install-azurerm-ps) vagy [Azure CLI](/cli/azure/install-azure-cli) helyben telepítve. Ehhez az oktatóanyaghoz helyi telepítés szükséges, mert a sablon mentése helyi fájlként történik. A Cloud Shell használatához [be kell töltenie a sablont egy tárfiókba](resource-group-template-deploy-cli.md#deploy-template-from-cloud-shell).

## <a name="create-template"></a>Sablon létrehozása

Kezdjük egy egyszerű sablonnal, amely egy tárfiókot helyez üzembe az előfizetésben.

1. Válassza a **File** (Fájl) > **New File** (Új fájl) lehetőséget. 

   ![Új fájl](./media/resource-manager-create-first-template/new-file.png)

2. Másolja és illessze be a következő JSON-szintaxist a létrehozott fájlba:

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
     },
     "variables": {
     },
     "resources": [
       {
         "name": "[concat('storage', uniqueString(resourceGroup().id))]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "sku": {
           "name": "Standard_LRS"
         },
         "kind": "Storage",
         "location": "South Central US",
         "tags": {},
         "properties": {}
       }
     ],
     "outputs": {  }
   }
   ```

   A tárfiókok neveire több korlátozás vonatkozik, amelyek bonyolulttá teszik a megadásukat. A név 3–24 karakter hosszúságú lehet, csak számokból és kisbetűkből állhat, és egyedinek kell lennie. Az előző példa a [uniqueString](resource-group-template-functions-string.md#uniquestring) függvény használatával létrehoz egy kivonatértéket. A kivonatolt érték könnyebb értelmezése érdekében hozzáadja a *storage* (tárhely) előtagot. 

3. Mentse ezt a fájlt **azuredeploy.json** néven egy helyi mappába.

   ![Sablon mentése](./media/resource-manager-create-first-template/save-template.png)

## <a name="deploy-template"></a>Sablon üzembe helyezése

Készen áll a sablon üzembe helyezésére. A PowerShell vagy az Azure CLI használatával hozzon létre egy erőforráscsoportot. Ezután helyezze üzembe a tárfiókot az adott erőforráscsoporton.

* A PowerShell esetében használja az alábbi parancsokat a sablont tartalmazó könyvtárban:

   ```powershell
   Connect-AzureRmAccount
   
   New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
   New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json
   ```

* Az Azure CLI helyi telepítése esetében használja az alábbi parancsokat a sablont tartalmazó könyvtárban:

   ```azurecli
   az login

   az group create --name examplegroup --location "South Central US"
   az group deployment create --resource-group examplegroup --template-file azuredeploy.json
   ```

Az üzembe helyezés után a tárfiók létrejön az erőforráscsoportban.

## <a name="customize-the-template"></a>A sablon testreszabása

A sablon jól működik, de nem rugalmas. Minden esetben üzembe helyez egy helyileg redundáns tárolót az USA déli középső régiójában. A név minden esetben *storage* lesz, amelyet egy kivonatérték követ. Ha lehetővé kívánja tenni a sablont különféle forgatókönyvekben való használatát, adjon hozzá paramétereket.

Az alábbi példában a paraméterek szakasz látható két paraméterrel. Az első paraméterrel (`storageSKU`) a redundancia típusát adhatja meg. A paraméter a tárfiókok esetében érvényes értékekre korlátozza a beadható értékeket. Emellett egy alapértelmezett értéket is megad. A második paraméterrel (`storageNamePrefix`) megadható egy legfeljebb 11 hosszúságú karakterkorlát. A paraméter megad egy alapértelmezett értéket.

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
  },
  "storageNamePrefix": {
    "type": "string",
    "maxLength": 11,
    "defaultValue": "storage",
    "metadata": {
      "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
    }
  }
},
```

A változók szakaszban adjon meg egy `storageName` nevű változót. Ez a paraméterekből származó előtagértéket és a [uniqueString](resource-group-template-functions-string.md#uniquestring) függvényből származó kivonatértéket kombinálja. A [toLower](resource-group-template-functions-string.md#tolower) függvény használatával az összes karaktert kisbetűs karakterekké alakítja.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Az új értékek a tárfiókra való alkalmazásához módosítsa az erőforrás definícióját:

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    "kind": "Storage",
    "location": "[resourceGroup().location]",
    "tags": {},
    "properties": {}
  }
],
```

Figyelje meg, hogy a tárfiók neve megváltozott a hozzáadott változóra. Az SKU neve a paraméter értékére változik. A hely az erőforráscsoporttal egyező helyre van beállítva.

Mentse a fájlt. 

A sablon most a következőképpen néz ki:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
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
    },   
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
      }
    }
  },
  "variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {}
    }
  ],
  "outputs": {  }
}
```

## <a name="redeploy-template"></a>Sablon ismételt üzembe helyezése

Újra üzembe helyezheti a sablont eltérő értékekkel.

PowerShell esetén használja az alábbi parancsot:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json -storageNamePrefix newstore -storageSKU Standard_RAGRS
```

Azure CLI esetén használja az alábbi parancsot:

```azurecli
az group deployment create --resource-group examplegroup --template-file azuredeploy.json --parameters storageSKU=Standard_RAGRS storageNamePrefix=newstore
```

## <a name="use-autocomplete"></a>Az automatikus kiegészítés használata

Eddig a sablonon végzett munka csak a cikkben szereplő JSON másolásából és beillesztéséből állt. Amikor azonban saját sablonokat fejleszt, az erőforrástípushoz elérhető tulajdonságokat és értékeket szeretné megkeresni és megadni. A VS Code beolvassa az erőforrástípus sémáját és tulajdonságokat és értékeket javasol. Az automatikus kiegészítés szolgáltatás megtekintéséhez lépjen a sablon tulajdonságok eleméhez, és adjon hozzá egy új sort. Írjon be egy idézőjelet, és figyelje meg, hogy a VS Code azonnal a tulajdonságok elemben elérhető neveket javasol.

![Elérhető tulajdonságok megjelenítése](./media/resource-manager-create-first-template/show-properties.png)

Válassza az **titkosítás** lehetőséget. Írjon be egy kettőspontot (:), és a VS Code új objektum hozzáadását javasolja.

![Objektum hozzáadása](./media/resource-manager-create-first-template/add-object.png)

Nyomja le a Tab vagy az Enter billentyűt az objektum hozzáadásához.

Ismét írjon be egy idézőjelet, és figyelje meg, hogy a VS Code most a titkosításhoz elérhető tulajdonságokat javasol.

![Titkosítás tulajdonságainak megjelenítése](./media/resource-manager-create-first-template/show-encryption-properties.png)

Válassza a **szolgáltatások** lehetőséget, és folytassa az értékek VS Code-bővítmények alapján végzett hozzáadását, amíg el nem éri a következőket:

```json
"properties": {
    "encryption":{
        "services":{
            "blob":{
              "enabled":true
            }
        }
    }
}
```

Engedélyezte a tárfiók blob titkosítását. A VS Code azonban problémát észlelt. Figyelje meg, hogy a titkosítás figyelmeztetéssel rendelkezik.

![Titkosítás figyelmeztetése](./media/resource-manager-create-first-template/encryption-warning.png)

A figyelmeztetés megtekintéséhez helyezze a mutatót a zöld sor fölé.

![Hiányzó tulajdonság](./media/resource-manager-create-first-template/missing-property.png)

Látja, hogy a titkosítás eleme keySource tulajdonságot igényel. Adjon egy vesszőt a szolgáltatások objektum után, és adja hozzá a keySource tulajdonságot. A VS Code a **"Microsoft.Storage"** értéket javasolja érvényes értékként. Amikor végzett, a tulajdonságok elem a következő:

```json
"properties": {
    "encryption":{
        "services":{
            "blob":{
              "enabled":true
            }
        },
        "keySource":"Microsoft.Storage"
    }
}
```

A végső sablon a következő:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
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
    },   
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
      }
    }
  },
  "variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "encryption":{
          "services":{
            "blob":{
              "enabled":true
            }
          },
          "keySource":"Microsoft.Storage"
        }
      }
    }
  ],
  "outputs": {}
}
```

## <a name="deploy-encrypted-storage"></a>Titkosított tárterület üzembe helyezése

Ismét helyezze üzembe a sablont, és adjon meg egy új nevet a tárfiókhoz.

PowerShell esetén használja az alábbi parancsot:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json -storageNamePrefix storesecure
```

Azure CLI esetén használja az alábbi parancsot:

```azurecli
az group deployment create --resource-group examplegroup --template-file azuredeploy.json --parameters storageNamePrefix=storesecure
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

PowerShell esetén használja az alábbi parancsot:

```powershell
Remove-AzureRmResourceGroup -Name examplegroup
```

Azure CLI esetén használja az alábbi parancsot:

```azurecli
az group delete --name examplegroup
```

## <a name="next-steps"></a>További lépések
* Ha több segítségre van szüksége a sablonok fejlesztéséhez, telepíthet egy VS Code-bővítményt. További információkért lásd: [Azure Resource Manager-sablon létrehozása Visual Studio Code-bővítménnyel](resource-manager-vscode-extension.md)
* A sablonok struktúrájával kapcsolatos további információk: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md).
* A tárfiókok tulajdonságaival kapcsolatos információkért lásd a [tárfióksablonok referenciáját](/azure/templates/microsoft.storage/storageaccounts).
* A különböző megoldástípusokhoz használható teljes sablonok megtekintéséhez lásd: [Azure gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates/).
