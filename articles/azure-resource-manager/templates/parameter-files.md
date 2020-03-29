---
title: Paraméterfájl létrehozása
description: Paraméterfájl létrehozása értékek átadásához az Azure Resource Manager-sablon telepítése során
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: bb52d9c5ebcb0820362e5de3d6b24b0b18d742e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76155621"
---
# <a name="create-resource-manager-parameter-file"></a>Erőforrás-kezelő paraméterfájl létrehozása

Ahelyett, hogy a paramétereket szövegközi értékként adná át a parancsfájlban, egyszerűbben használhatja a paraméterértékeket tartalmazó JSON-fájlt. Ez a cikk bemutatja, hogyan lehet létrehozni a paraméterfájlt.

## <a name="parameter-file"></a>Paraméterfájl

A paraméterfájl formátuma a következő:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "value": "<second-value>"
    }
  }
}
```

Figyelje meg, hogy a paraméterértékek egyszerű szövegként tárolódnak a paraméterfájlban. Ez a megközelítés nem bizalmas értékek, például egy erőforrás termékváltozatának megadása. Nem működik a bizalmas értékek, például a jelszavak. Ha paraméterként bizalmas értéket kell átadnia, tárolja az értéket egy key vaultban, és hivatkozzon a key vaultra a paraméterfájlban. A bizalmas érték biztonságosan lekérésre kerül az üzembe helyezés során.

A következő paraméterfájl egy egyszerű szöveges értéket és egy key vaultban tárolt értéket tartalmaz.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "reference": {
        "keyVault": {
          "id": "<resource-id-key-vault>"
        },
        "secretName": "<secret-name>"
      }
    }
  }
}
```

A key vaultból származó értékek használatáról az [Azure Key Vault használatával a biztonságos paraméter értékének a telepítés során történő átadásához című](key-vault-parameter.md)témakörben talál további információt.

## <a name="define-parameter-values"></a>Paraméterértékek meghatározása

A paraméterértékek meghatározásának meghatározásához nyissa meg a telepítő sablont. Tekintse meg a paraméterek szakasza a sablon. A következő példa egy sablon paramétereit mutatja be.

```json
"parameters": {
  "storagePrefix": {
    "type": "string",
    "maxLength": 11
  },
  "storageAccountType": {
    "type": "string",
    "defaultValue": "Standard_LRS",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS",
    "Standard_ZRS",
    "Premium_LRS"
    ]
  }
}
```

Az első részlet, hogy észre a neve az egyes paraméterek. A paraméterfájlban szereplő értékeknek meg kell egyezniük a nevekkel.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
    },
    "storageAccountType": {
    }
  }
}
```

Figyelje meg a paraméter típusát. A paraméterfájlban lévő értékeknek azonos típusúaknak kell lennie. Ehhez a sablonhoz mindkét paramétert karakterláncként adhatja meg.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": ""
    },
    "storageAccountType": {
      "value": ""
    }
  }
}
```

Ezután keressen egy alapértelmezett értéket. Ha egy paraméter nek van alapértelmezett értéke, megadhat egy értéket, de nem kell.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "" // This value must be provided.
    },
    "storageAccountType": {
      "value": "" // This value is optional. Template will use default value if not provided.
    }
  }
}
```

Végül, nézd meg a megengedett értékeket, és minden korlátozás, mint a maximális hossza. Megmondják a paraméterhez megadható értéktartományt.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "storage"
    },
    "storageAccountType": {
      "value": "Standard_ZRS"
    }
  }
}
```

## <a name="parameter-type-formats"></a>Paramétertípus-formátumok

A következő példa a különböző paramétertípusok formátumait mutatja be.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "exampleString": {
      "value": "test string"
    },
    "exampleInt": {
      "value": 4
    },
    "exampleBool": {
      "value": true
    },
    "exampleArray": {
      "value": [
        "value 1",
        "value 2"
      ]
    },
    "exampleObject": {
      "value": {
        "property1": "value1",
        "property2": "value2"
      }
    }
   }
}
```

## <a name="file-name"></a>Fájlnév

A paraméterfájl elnevezésének általános konvenciója a **.parameters** hozzáadása a sablon nevéhez. Ha például a sablon neve **azuredeploy.json,** a paraméterfájl neve **azuredeploy.parameters.json.** Ez az elnevezési konvenció segít a sablon és a paraméterek közötti kapcsolat megtekintésében.

Ha különböző környezetekben szeretné telepíteni, hozzon létre egynél több paraméterfájlt. A paraméterfájl elnevezésekéneksorán adjon hozzá egy módot a fájl használatának azonosítására. Használja például **az azuredeploy.parameters-dev.json** és **az azuredeploy.parameters-prod.json használatát.**


## <a name="parameter-precedence"></a>Paraméter prioritása

A beépített paramétereket és a helyi paraméterfájlt ugyanabban a telepítési műveletben használhatja. Megadhat például bizonyos értékeket a helyi paraméterfájlban, és a központi telepítés során további inline értékeket adhat hozzá. Ha a helyi paraméterfájlban és a szövegközi ben is megad értékeket egy paraméterhez, a szövegközi érték élvez elsőbbséget.

Ha azonban külső paraméterfájlt használ, más értékeket nem adhat át sem a szövegközi, sem a helyi fájlból. A rendszer minden szövegközi paramétert figyelmen kívül hagy. Adja meg a külső fájl összes paraméterértékét.

## <a name="parameter-name-conflicts"></a>Paraméternév-ütközések

Ha a sablon tartalmaz egy paramétert, amelynek neve megegyezik a PowerShell parancs egyik paraméterének nevével, a PowerShell a sablon paraméterét a **FromTemplate**postfix-el mutatja be. Például egy **Erőforráscsoportnév** nevű paraméter ütközik a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) parancsmag **ResourceGroupName** paraméterével. A rendszer kéri, hogy adjon meg egy értéket a **ResourceGroupNameFromTemplate sablonhoz.** Ezt a félreértést elkerülheti olyan paraméternevek használatával, amelyek et nem használnak központi telepítési parancsokhoz.

## <a name="next-steps"></a>További lépések

- Ha tudni szeretné, hogyan definiálhatja a paramétereket a sablonban, olvassa el [a Paraméterek az Azure Resource Manager-sablonokban (Paraméterek az Azure Resource Manager-sablonokban) témakört.](template-parameters.md)
- A key vaultból származó értékek használatáról az [Azure Key Vault használatával a biztonságos paraméter értékének a telepítés során történő átadásához című](key-vault-parameter.md)témakörben talál további információt.
- A paraméterekről további információt a [Paraméterek az Azure Resource Manager-sablonokban című témakörben talál.](template-parameters.md)
