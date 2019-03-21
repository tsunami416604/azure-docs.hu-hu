---
title: A REST API-t és a sablon erőforrások üzembe helyezése |} A Microsoft Docs
description: Azure Resource Manager és a Resource Manager REST API használatával helyezze üzembe az Azure-erőforrásokat. Az erőforrások egy Resource Manager-sablonban vannak meghatározva.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 1d8fbd4c-78b0-425b-ba76-f2b7fd260b45
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/14/2019
ms.author: tomfitz
ms.openlocfilehash: bd574eb2d3537d3e5c0774f57e37283817cc7879
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58112024"
---
# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure Manager REST API-val

Ez a cikk ismerteti a Resource Manager-sablonok, az erőforrások üzembe helyezése az Azure Resource Manager REST API használata.  

> [!TIP]
> A hibakeresést a telepítés során hibát talál segítséget:
> 
> * [Üzembehelyezési műveletek megtekintése](resource-manager-deployment-operations.md) további információ első információkkal könnyíti meg a hiba elhárítása
> * [Gyakori hibák elhárítása az erőforrások üzembe helyezésekor az Azure-bA az Azure Resource Manager](resource-manager-common-deployment-errors.md) hogyan oldja meg a gyakori telepítési hibák
> 
> 

Vagy megadhatja a sablon a kérelem törzsében vagy egy fájlra mutató hivatkozást. Amikor fájl használatával lehet egy helyi fájlból vagy egy külső fájl, amely egy URI-t keresztül érhető el. Ha a sablon a storage-fiókban, korlátozza a hozzáférést a sablont, és adjon meg egy közös hozzáférésű jogosultságkód (SAS) üzembe helyezése során.

## <a name="deploy-with-the-rest-api"></a>Üzembe helyezés a REST API
1. Állítsa be [gyakori paramétereket és a fejlécek](/rest/api/azure/), beleértve a hitelesítési tokenek.

1. Ha nem rendelkezik egy meglévő erőforráscsoportot, hozzon létre egy erőforráscsoportot. Adja meg az előfizetés-Azonosítóját, nevét az új erőforráscsoport és a helyre, amely a megoldáshoz szükséges. További információkért lásd: [hozzon létre egy erőforráscsoportot](/rest/api/resources/resourcegroups/createorupdate).

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2018-05-01
   ```

   A hasonló kéréstörzset:
   ```json
   {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
   }
   ```

1. A telepítés előtt futtatnia kell a futtatásával ellenőrizze a [egy sablon telepítésének ellenőrzésére](/rest/api/resources/deployments/validate) műveletet. A központi telepítés tesztelésekor paramétereket megadnia, pontosan, mint az üzemelő példány (a következő lépésben látható) végrehajtása közben.

1. Központi telepítés létrehozása. Adja meg az előfizetés-azonosító, az erőforráscsoport nevét, az üzembe helyezés, valamint egy hivatkozás, a sablon nevét. A sablonfájl kapcsolatos információkért lásd: [paraméterfájl](#parameter-file). Hozzon létre egy erőforráscsoportot a REST API-val kapcsolatos további információkért lásd: [sablon üzembe helyezése](/rest/api/resources/deployments/createorupdate). Figyelje meg a **mód** értékre van állítva **növekményes**. A teljes üzembe helyezés, állítsuk be **mód** való **Complete**. Ügyeljen arra, hogy a teljes mód használatakor, ha véletlenül is törli, amelyek nem szerepelnek a sablon erőforrások.

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
   ```

   A hasonló kéréstörzset:

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      }
    }
   }
   ```

    Ha azt szeretné, válasz tartalma, a kérelem tartalma vagy mindkettő bejelentkezni, például **debugSetting** a kérésben.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "debugSetting": {
        "detailLevel": "requestContent, responseContent"
      }
    }
   }
   ```

    A storage-fiók beállítása egy közös hozzáférésű jogosultságkód (SAS) használata. További információkért lásd: [hozzáférés delegálása közös hozzáférésű Jogosultságkód](https://docs.microsoft.com/rest/api/storageservices/delegating-access-with-a-shared-access-signature).

1. Ahelyett, hogy a sablon és paraméterek összekapcsolása, hozzáadhatja őket a kérelem törzsében.

   ```json
   {
      "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "allowedValues": [
              "Standard_LRS",
              "Standard_GRS",
              "Standard_ZRS",
              "Premium_LRS"
            ],
            "metadata": {
              "description": "Storage Account type"
            }
          },
          "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
              "description": "Location for all resources."
            }
          }
        },
        "variables": {
          "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2018-02-01",
            "location": "[parameters('location')]",
            "sku": {
              "name": "[parameters('storageAccountType')]"
            },
            "kind": "StorageV2",
            "properties": {}
          }
        ],
        "outputs": {
          "storageAccountName": {
            "type": "string",
            "value": "[variables('storageAccountName')]"
          }
        }
      },
      "parameters": {
        "location": {
          "value": "eastus2"
        }
      }
    }
   }
   ```

5. A sablon központi telepítés állapotának lekéréséhez. További információkért lásd: [-sablonalapú telepítéssel kapcsolatos információk lekérése](/rest/api/resources/deployments/get).

   ```HTTP
   GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
   ```

## <a name="redeploy-when-deployment-fails"></a>Telepítse újra a központi telepítésének hibája esetén

Ha egy központi telepítés nem sikerül, automatikusan is újratelepítése egy korábbi, a sikeres telepítés az üzembe helyezési előzményekből. Újbóli üzembe helyezés megadásához használja a `onErrorDeployment` tulajdonságot a kérelem törzsében.

Ez a beállítás használatához az üzemelő példányok egyedi névvel kell rendelkezniük az előzményekben található ellenőrizhető, hogy. Ha nem rendelkezik egyedi nevét, az aktuális telepítése sikertelen volt. a korábban sikeresen végrehajtott központi telepítés előzményei esetleg felülírhatja. Használhatja ezt a beállítást csak a legfelső szintű telepítések. Beágyazott sablonból üzemelő példányok nem újbóli üzembe helyezés érhetők el.

Telepítse újra a legutóbbi sikeres üzembe helyezés, ha a jelenlegi üzemelő példány nem sikerül, használja:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "LastSuccessful",
    }
  }
}
```

Adott üzembe helyezés ismételt üzembe helyezése, ha a jelenlegi üzemelő példány nem sikerül, használja:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "SpecificDeployment",
      "deploymentName": "<deploymentname>"
    }
  }
}
```

A megadott központi telepítés sikeres volt kell rendelkeznie.

## <a name="parameter-file"></a>Alkalmazásparaméter-fájlt

Paraméterfájl használatával paramétert értékek továbbítása üzembe helyezés során, ha szeretne létrehozni egy JSON-fájlt az formátumú az alábbi példához hasonló:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "webSiteName": {
            "value": "ExampleSite"
        },
        "webSiteHostingPlanName": {
            "value": "DefaultPlan"
        },
        "webSiteLocation": {
            "value": "West US"
        },
        "adminPassword": {
            "reference": {
               "keyVault": {
                  "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
               }, 
               "secretName": "sqlAdminPassword" 
            }   
        }
   }
}
```

A paraméter-fájl mérete nem lehet több mint 64 KB-os.

Ha meg kell adnia a kényes értéket a paraméterhez (például a jelszó), adja hozzá ezt az értéket, egy kulcstárolóba. Lekérése a key vault üzembe helyezése során, az előző példában látható módon. További információkért lásd: [biztonságos értékek továbbítása üzembe helyezés során](resource-manager-keyvault-parameter.md). 

## <a name="next-steps"></a>További lépések
* Adja meg, hogyan legyen kezelve az erőforrásokat, az erőforráscsoportban létezik, de nincsenek definiálva a sablonban, lásd: [Azure Resource Manager üzembe helyezési mód](deployment-modes.md).
* Kezelési REST-műveletek aszinkron kapcsolatos további információkért lásd: [Azure aszinkron műveletek követése](resource-manager-async-operations.md).
* Az üzembe helyezni erőforrásokat, a .NET ügyféloldali kódtár használatával egy példa: [erőforrások üzembe helyezése .NET-kódtárak és sablon](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* A paraméterek meghatározása sablonban, lásd: [sablonok készítése](resource-group-authoring-templates.md#parameters).
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.

