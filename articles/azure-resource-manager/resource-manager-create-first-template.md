---
title: "Az első Azure Resource Manager-sablon létrehozása | Microsoft Docs"
description: "Lépésenkénti útmutató az első Azure Resource Manager-sablon létrehozásához. Bemutatja, hogyan használható egy tárfiók sablonreferenciája a sablon létrehozásához."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/21/2017
ms.topic: get-started-article
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: f861f2ba0376f00ff21e03cbb81a04a266f38630
ms.lasthandoff: 03/22/2017

---

# <a name="create-your-first-azure-resource-manager-template"></a>Az első Azure-Resource Manager-sablon létrehozása
Ez a témakör bemutatja azon lépéseket, amelyekkel elkészítheti az első Resource Manager-sablonját. A Resource Manager-sablonok JSON-fájlok, melyek az adott megoldáshoz telepítendő erőforrásokat határozzák meg. Az Azure-megoldások telepítésével és kezelésével kapcsolatos fogalmak megismeréséhez lásd: [Az Azure Resource Manager áttekintése](resource-group-overview.md). Ha már rendelkezik erőforrásokkal, és azokhoz kíván sablont használni, lásd: [Azure Resource Manager-sablonok exportálása létező erőforrásokból](resource-manager-export-template.md).

A sablonok létrehozásához és átalakításához JSON-szerkesztő szükséges. A [Visual Studio Code](https://code.visualstudio.com/) egy könnyen használható, nyílt forráskódú, platformfüggetlen kódszerkesztő. Egy bővítményével lehetővé teszi a Resource Manager-sablonok létrehozását és szerkesztését. Ez a témakör a VS Code használatát feltételezi, de használhat egyéb JSON-szerkesztőt is (pl. Visual Studio).

## <a name="get-vs-code-and-extension"></a>A VS Code és a bővítmény beszerzése
1. Ha szükséges,a VS Code a következő címről telepíthető: [https://code.visualstudio.com/](https://code.visualstudio.com/).

2. Telepítse az [Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) bővítményt. Ehhez jelenítse meg a gyorsmegnyitási listát (Ctrl+P), majd futtassa a következőt: 

  ```
  ext install msazurermtools.azurerm-vscode-tools
  ```

3. A bővítmény engedélyezéséhez indítsa újra a VS Code alkalmazást, amikor a rendszer kéri.

## <a name="create-blank-template"></a>Üres sablon létrehozása

Kezdjük egy üres sablonnal, amely csak az alapvető szakaszokat tartalmazza.

1. Hozzon létre egy fájlt. 

2. Másolja és illessze be a következő JSON-szintaxist a létrehozott fájlba:

  ```json
  {
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {  },
    "variables": {  },
    "resources": [  ],
    "outputs": {  }
  }
   ```

3. Mentse ezt a fájlt **azuredeploy.json** néven. 

## <a name="add-storage-account"></a>Tárfiók hozzáadása
1. Egy tárfiók üzembe helyezéshez való meghatározásához adja hozzá azt a sablon **resources** (erőforrások) szakaszához. A tárfiókhoz elérhető értékek azonosításához tekintse meg a [tárfióksablonok referenciáját](/azure/templates/microsoft.storage/storageaccounts). Másolja a vágólapra a tárfiókhoz megjelenített JSON-t. 

3. Másolja ezt a JSON-t a sablonja **resources** (erőforrások) szakaszába az alábbi példában látható módon: 

  ```json
  {
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {  },
    "variables": {  },
    "resources": [
      {
        "name": "string",
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2016-05-01",
        "sku": {
          "name": "string"
        },
        "kind": "string",
        "location": "string",
        "tags": {},
        "properties": {
          "customDomain": {
            "name": "string",
            "useSubDomain": boolean
          },
          "encryption": {
            "services": {
              "blob": {
                "enabled": boolean
              }
            },
            "keySource": "Microsoft.Storage"
          },
          "accessTier": "string"
        }
      }
    ],
    "outputs": {  }
  }
  ```

  A megelőző példában sok helyőrző érték és néhány olyan tulajdonság szerepel, amelyek nem feltétlenül szükségesek az Ön tárfiókjához.

## <a name="set-values-for-storage-account"></a>Értékek beállítása a tárfiókhoz

Készen áll arra, hogy értékeket állítson be a tárfiókjához. 

1. Nézze meg újra a [tárfióksablonok referenciáját](/azure/templates/microsoft.storage/storageaccounts), ahonnan a JSON-t másolta. Számos táblát láthat, amelyek leírják a tulajdonságokat és megadják az elérhető értékeket. 

2. Figyelje meg, hogy a **properties** (tulajdonságok) elemen belüli **customDomain** (egyéni tartomány), **encryption** (titkosítás) és **accessTier** (hozzáférési szint) tulajdonságok nem szükségesként vannak jelölve. Előfordulhat, hogy ezek az értékek az Ön forgatókönyvei esetében fontosak, de ebben a példában az egyszerűség kedvéért távolítsa el őket.

  ```json
  "resources": [
    {
      "name": "string",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "string"
      },
      "kind": "string",
      "location": "string",
      "tags": {},
      "properties": {
      }
    }
  ],
  ```

3. A **kind** (altípus) elem jelenleg a „string” (karakterlánc) helyőrző értékre van állítva. A VS Code számos funkciót tartalmaz, amelyek segítségével értelmezheti a sablonban használandó értékeket. Megfigyelheti, hogy a VS Code érvénytelennek jelöli ezt az értéket. Ha a kurzort a „string” fölé helyezi, megjelenik a VS Code javaslata, amely szerint a **kind** érvényes értékei a következők lehetnek: `Storage` vagy `BlobStorage`. 

  ![a VS Code javasolt értékeinek megjelenítése](./media/resource-manager-create-first-template/vs-code-show-values.png)

  Az elérhető értékek megjelenítéséhez törölje az idézőjelek közötti karaktereket és nyomja le a **Ctrl+Szóköz** billentyűkombinációt. Válassza a **Storage** (Tároló) lehetőséget az elérhető lehetőségek közül.
  
  ![intellisense megjelenítése](./media/resource-manager-create-first-template/intellisense.png)

  Ha nem a VS Code-ot használja, tekintse meg a tárfióksablonok referenciájának oldalát. Figyelje meg, hogy a leírás ugyanezeket az érvényes értékeket sorolja fel. Állítsa az elemet **Storage** (Tároló) értékre.

  ```json
  "kind": "Storage",
  ```

A sablon most a következőképpen néz ki:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {  },
  "variables": {  },
  "resources": [
    {
      "name": "string",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "string"
      },
      "kind": "Storage",
      "location": "string",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="add-template-function"></a>Sablonfüggvény hozzáadása

A sablonban függvények használatával egyszerűsítheti a sablon szintaxisát, valamint kinyerheti azokat az értékeket, amelyek csak a sablon üzembe helyezésekor érhetők el. A sablonokban használható függvények teljes listáját lásd: [Az Azure Resource Manager-sablonok függvényei](resource-group-template-functions.md).

Annak meghatározásához, hogy a tárfiók ugyanazon a helyen üzemeljen, mint az erőforráscsoport, állítsa a **location** (hely) tulajdonságot a következő értékre:

```json
"location": "[resourceGroup().location]",
```

A VS Code ebben az esetben is javasol elérhető függvényeket. 

![függvények megjelenítése](./media/resource-manager-create-first-template/show-functions.png)

Figyelje meg, hogy a függvény szögletes zárójelek közt jelenik meg. A [resourceGroup](resource-group-template-functions.md#resourcegroup) függvény a következő tulajdonsággal rendelkező objektumot adja vissza: `location`. Az erőforráscsoport tartalmazza a megoldás összes kapcsolódó erőforrását. A hely tulajdonságot szoftveresen kötött módon is megadhatná, például az „USA középső régiója” érték használatával, de ez azt jelentené, hogy a más régiókban való üzembe helyezéshez manuálisan módosítania kellene a sablont. A `resourceGroup` függvény használatával a sablon egyszerűen üzembe helyezhető egy másik régióban lévő erőforráscsoportban.



A sablon most a következőképpen néz ki:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {  },
  "variables": {  },
  "resources": [
    {
      "name": "string",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "string"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="add-parameters-and-variables"></a>Paramétereket és változók hozzáadása
Már csak két értéket kell megadni a sablonhoz: a **name** (név) és az **sku.name** (SKU-név) értékét. Ezekhez az értékekhez olyan paramétereket kell adni, amelyek lehetővé teszik az értékek testreszabását az üzembe helyezés során. 

A tárfiókok neveire több korlátozás vonatkozik, amelyek bonyolulttá teszik a megadásukat. A név 3–24 karakter hosszúságú lehet, csak számokból és kisbetűkből állhat, és egyedinek kell lennie. Ahelyett, hogy megpróbálna kitalálni egy, a korlátozásoknak megfelelő egyedi nevet, használja a [uniqueString](resource-group-template-functions.md#uniquestring) függvényt egy kivonatolt érték létrehozásához. Ahhoz, hogy a kivonatolt érték könnyebben értelmezhető legyen, adjon hozzá egy előtagot, amely alapján az üzembe helyezés után felismerhető lesz tárfiókként. 

1. Az Ön elnevezési konvencióinak megfelelő előtag megadásához lépjen a sablon **parameters** (paraméterek) szakaszára. Adjon egy olyan paramétert a sablonhoz, amely elfogadja a tárfiók nevéhez adott előtagot:

  ```json
  "parameters": {
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name."
      }
    }
  },
  ```

  Az előtag legfeljebb 11 karakter hosszúságú lehet, mert a `uniqueString` 13 karaktert ad vissza, és a név nem haladhatja meg a 24 karaktert. Ha nem ad meg értéket a paraméterhez az üzembe helyezés során, a rendszer az alapértelmezett értéket használja.

2. Lépjen a sablon **variables** (változók) szakaszára. Adja hozzá a következő változót, hogy az előtagból és az egyedi karakterláncból létrejöjjön a név:

  ```json
  "variables": {
    "storageName": "[concat(parameters('storageNamePrefix'), uniqueString(resourceGroup().id))]"
  },
  ```

3. A **resources** (erőforrások) szakaszban állítsa a tárfiók nevét erre a változóra.

  ```json
  "name": "[variables('storageName')]",
  ```

3. A különböző SKU-k megadásának engedélyezéséhez a tárfiókhoz lépjen a **parameters** (paraméterek) szakaszra. A tárfiók nevének előtagjához tartozó paraméter után adjon hozzá egy paramétert, amely meghatározza az engedélyezett SKU-értékeket és egy alapértelmezett értéket. Az engedélyezett értékeket megtalálhatja a sablon referenciaoldalán vagy a VS Code-ban. A következő példában az összes érvényes értéket fel fogja venni az SKU-hoz. Azonban korlátozhatja ezeket az engedélyezett értékeket csak azon SKU-típusokra, amelyeket a sablonnal kíván üzembe helyezni.

  ```json
  "parameters": {
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name."
      }
    },
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
  },
  ```

3. Módosítsa az SKU tulajdonságot úgy, hogy az a következő paraméter értékét használja:

  ```json
  "sku": {
    "name": "[parameters('storageSKU')]"
  },
  ```    

4. Mentse a fájlt.

A sablon most a következőképpen néz ki:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name."
      }
    },
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
  },
  "variables": {
    "storageName": "[concat(parameters('storageNamePrefix'), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="next-steps"></a>Következő lépések
* A sablon elkészült, és készen áll az előfizetésben való üzembe helyezésre. Az üzembe helyezéshez lásd: [Erőforrások üzembe helyezése az Azure-ban](resource-manager-quickstart-deploy.md).
* A sablonok struktúrájával kapcsolatos további információk: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md).
* A különböző megoldástípusokhoz használható teljes sablonok megtekintéséhez lásd: [Azure gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates/).

