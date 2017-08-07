---
title: "Az első Azure Resource Manager-sablon létrehozása | Microsoft Docs"
description: "Lépésenkénti útmutató az első Azure Resource Manager-sablon létrehozásához. Bemutatja, hogyan használható egy tárfiók sablonreferenciája a sablon létrehozásához."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 07/27/2017
ms.topic: get-started-article
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 6e76ac40e9da2754de1d1aa50af3cd4e04c067fe
ms.openlocfilehash: 49086b51e2db1aebed45746306ae14b6f1feb631
ms.contentlocale: hu-hu
ms.lasthandoff: 07/31/2017

---

# <a name="create-and-deploy-your-first-azure-resource-manager-template"></a>Az első Azure Resource Manager-sablon létrehozása ás üzembe helyezése
Ez a témakör bemutatja azon lépéseket, amelyekkel elkészítheti az első Resource Manager-sablonját. A Resource Manager-sablonok JSON-fájlok, melyek az adott megoldáshoz telepítendő erőforrásokat határozzák meg. Az Azure-megoldások telepítésével és kezelésével kapcsolatos fogalmak megismeréséhez lásd: [Az Azure Resource Manager áttekintése](resource-group-overview.md). Ha már rendelkezik erőforrásokkal, és azokhoz kíván sablont használni, lásd: [Azure Resource Manager-sablonok exportálása létező erőforrásokból](resource-manager-export-template.md).

A sablonok létrehozásához és átalakításához JSON-szerkesztő szükséges. A [Visual Studio Code](https://code.visualstudio.com/) egy könnyen használható, nyílt forráskódú, platformfüggetlen kódszerkesztő. A Resource Manager-sablonok létrehozásához kifejezetten javasoljuk a Visual Studio Code használatát. Ez a témakör a VS Code használatát feltételezi, de használhat egyéb JSON-szerkesztőt is (pl. Visual Studio).

## <a name="prerequisites"></a>Előfeltételek

* Visual Studio Code. Ha szükséges, a következő címről telepíthető: [https://code.visualstudio.com/](https://code.visualstudio.com/).
* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

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
   Login-AzureRmAccount
   
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

## <a name="deploy-template-from-cloud-shell"></a>Sablon üzembe helyezése a Cloud Shellből

Az Azure CLI-parancsokat a [Cloud Shell](../cloud-shell/overview.md) használatával is futtathatja a sablon üzembe helyezéséhez. Ehhez azonban először be kell töltenie a sablont a Cloud Shell fájlmegosztásába. Ha még nem használta a Cloud Shellt, a telepítésével kapcsolatban lásd [Az Azure Cloud Shell áttekintése](../cloud-shell/overview.md) című cikket.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).   

2. Válassza ki a Cloud Shell-erőforráscsoportot. A névminta a következő: `cloud-shell-storage-<region>`.

   ![Erőforráscsoport kiválasztása](./media/resource-manager-create-first-template/select-cs-resource-group.png)

3. Válassza ki a Cloud Shell tárfiókját.

   ![Adattároló fiók kiválasztása](./media/resource-manager-create-first-template/select-storage.png)

4. Válassza a **Files** (Fájlok) lehetőséget.

   ![Fájlok kiválasztása](./media/resource-manager-create-first-template/select-files.png)

5. Válassza ki a Cloud Shell fájlmegosztását. A névminta a következő: `cs-<user>-<domain>-com-<uniqueGuid>`.

   ![Fájlmegosztás kiválasztása](./media/resource-manager-create-first-template/select-file-share.png)

6. Válassza az **Add directory** (Könyvtár hozzáadása) lehetőséget.

   ![Könyvtár hozzáadása](./media/resource-manager-create-first-template/select-add-directory.png)

7. Nevezze el a könyvtárat **templates** (sablonok) néven, és válassza az **Okay** (OK) gombot.

   ![Könyvtár elnevezése](./media/resource-manager-create-first-template/name-templates.png)

8. Jelölje ki az új könyvtárat.

   ![Könyvtár kijelölése](./media/resource-manager-create-first-template/select-templates.png)

9. Válassza a **Feltöltés** lehetőséget.

   ![Feltöltés kiválasztása](./media/resource-manager-create-first-template/select-upload.png)

10. Keresse meg és töltse fel a sablont.

   ![Fájl feltöltése](./media/resource-manager-create-first-template/upload-files.png)

11. Nyissa meg a parancssort.

   ![Cloud Shell megnyitása](./media/resource-manager-create-first-template/start-cloud-shell.png)

12. Írja be a következő parancsokat a Cloud Shellbe:

   ```azurecli
   az group create --name examplegroup --location "South Central US"
   az group deployment create --resource-group examplegroup --template-file clouddrive/templates/azuredeploy.json
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

A cikkben ismertetett lépések végrehajtása után a sablon a következőképpen néz ki:

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

A Cloud Shell esetén töltse fel a módosított sablont a fájlmegosztásba. Írja felül a meglévő fájlt. Ezután használja az alábbi parancsot:

```azurecli
az group deployment create --resource-group examplegroup --template-file clouddrive/templates/azuredeploy.json --parameters storageSKU=Standard_RAGRS storageNamePrefix=newstore
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

## <a name="next-steps"></a>Következő lépések
* A sablonok struktúrájával kapcsolatos további információk: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md).
* A tárfiókok tulajdonságaival kapcsolatos információkért lásd a [tárfióksablonok referenciáját](/azure/templates/microsoft.storage/storageaccounts).
* A különböző megoldástípusokhoz használható teljes sablonok megtekintéséhez lásd: [Azure gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates/).

