---
title: A REST API-t és a sablon erőforrások üzembe helyezése |} A Microsoft Docs
description: Azure Resource Manager és a Resource Manager REST API használatával helyezhet üzembe erőforrásokat az Azure-bA. Az erőforrások egy Resource Manager-sablonban vannak meghatározva.
author: tfitzmac
ms.assetid: 1d8fbd4c-78b0-425b-ba76-f2b7fd260b45
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: tomfitz
ms.openlocfilehash: 0490cf6837cb413bc2e869424cd430fd4a824dc9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66688865"
---
# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure Manager REST API-val

Ez a cikk ismerteti a Resource Manager-sablonok, az erőforrások üzembe helyezése az Azure Resource Manager REST API használata.  

Vagy megadhatja a sablon a kérelem törzsében vagy egy fájlra mutató hivatkozást. Amikor fájl használatával lehet egy helyi fájlból vagy egy külső fájl, amely egy URI-t keresztül érhető el. Ha a sablon a storage-fiókban, korlátozza a hozzáférést a sablont, és adjon meg egy közös hozzáférésű jogosultságkód (SAS) üzembe helyezése során.

## <a name="deployment-scope"></a>Üzembe helyezés hatálya

A felügyeleti csoport, egy Azure-előfizetés vagy egy erőforráscsoport, a telepítés célozhat meg. A legtöbb esetben egy erőforráscsoport üzemelő példányok célozhat meg lesz. Felügyeleti csoportba vagy előfizetésbe központi telepítéseket használ, alkalmazza a házirendeket és a szerepkör-hozzárendelések a megadott hatókörön között. Hozzon létre egy erőforráscsoportot, és üzembe helyezni erőforrásokat, előfizetés központi telepítéseket is használ. Az üzembe helyezés, függően különböző parancsokat használhatja.

Központi telepítése egy **erőforráscsoport**, használjon [központi telepítések – hozzon létre](/rest/api/resources/deployments/createorupdate). A kérelmet küldeni:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Központi telepítése egy **előfizetés**, használjon [központi telepítések – hozzon létre egy előfizetésre,](/rest/api/resources/deployments/createorupdateatsubscriptionscope). A kérelmet küldeni:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Központi telepítése egy **felügyeleti csoport**, használjon [központi telepítések – hozzon létre a felügyeleti csoport hatóköre](/rest/api/resources/deployments/createorupdateatmanagementgroupscope). A kérelmet küldeni:

```HTTP
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Ebben a cikkben szereplő példák erőforráscsoportok üzemelő példányainak használja. Előfizetések üzemelő példányai kapcsolatos további információkért lásd: [erőforráscsoport és erőforrások létrehozásához az előfizetés szintjén](deploy-to-subscription.md).

## <a name="deploy-with-the-rest-api"></a>Üzembe helyezés a REST API

1. Állítsa be [gyakori paramétereket és a fejlécek](/rest/api/azure/), beleértve a hitelesítési tokenek.

1. Ha nem rendelkezik egy meglévő erőforráscsoportot, hozzon létre egy erőforráscsoportot. Adja meg az előfizetés-Azonosítóját, nevét az új erőforráscsoport és a helyre, amely a megoldáshoz szükséges. További információkért lásd: [hozzon létre egy erőforráscsoportot](/rest/api/resources/resourcegroups/createorupdate).

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2019-05-01
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

1. A sablon üzembe helyezéséhez, adja meg az előfizetés-Azonosítóját, a nevet az erőforráscsoport, a kérelem URI-t az üzemelő példány neve. 

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2019-05-01
   ```

   A kérelem törzsében szereplő adja meg a sablonnal és paraméterfájlokkal fájlra mutató hivatkozást. Figyelje meg a **mód** értékre van állítva **növekményes**. A teljes üzembe helyezés, állítsuk be **mód** való **Complete**. Ügyeljen arra, hogy a teljes mód használatakor, ha véletlenül is törli, amelyek nem szerepelnek a sablon erőforrások.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental"
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
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "debugSetting": {
        "detailLevel": "requestContent, responseContent"
      }
    }
   }
   ```

    A storage-fiók beállítása egy közös hozzáférésű jogosultságkód (SAS) használata. További információkért lásd: [hozzáférés delegálása közös hozzáférésű Jogosultságkód](https://docs.microsoft.com/rest/api/storageservices/delegating-access-with-a-shared-access-signature).

1. Ahelyett, hogy a sablon és paraméterek összekapcsolása, hozzáadhatja őket a kérelem törzsében. Az alábbi példa bemutatja a kérelem törzsében a sablonnal és paraméterfájlokkal beágyazott:

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

1. A sablon üzembe helyezés állapotának lekérdezéséhez használja [központi telepítések – első](/rest/api/resources/deployments/get).

   ```HTTP
   GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
   ```

## <a name="redeploy-when-deployment-fails"></a>Telepítse újra a központi telepítésének hibája esetén

Ez a funkció más néven van *visszaállítási hiba*. Ha egy központi telepítés nem sikerül, automatikusan is újratelepítése egy korábbi, a sikeres telepítés az üzembe helyezési előzményekből. Újbóli üzembe helyezés megadásához használja a `onErrorDeployment` tulajdonságot a kérelem törzsében. Ez a funkció akkor hasznos, ha van egy korábbi hibátlan állapotra az infrastruktúra üzembe helyezéshez, és szeretné állítani az állapotot. Nincsenek figyelmeztetések és korlátozások:

- Az újbóli üzembe helyezés pontosan, ahogy korábban már volt futtatva ugyanazokkal a paraméterekkel futtatja. A paraméterek nem módosítható.
- A korábbi központi telepítés segítségével futtatja a [teljes mód](./deployment-modes.md#complete-mode). Nem szerepel a korábbi központi telepítés minden erőforrás törlődni, és a minden erőforrás-konfigurációt az előző állapotukba. Győződjön meg arról, hogy megértette a [üzembe helyezési mód](./deployment-modes.md).
- Az újratelepítés csak érinti az erőforrásokat, adatok módosításokat a változás nem érinti.
- Ez a funkció csak az erőforráscsoport központi telepítések, nem előfizetési szintű központi telepítések támogatott. Előfizetés-szintű üzembe helyezéssel kapcsolatos további információkért lásd: [erőforráscsoport és erőforrások létrehozásához az előfizetés szintjén](./deploy-to-subscription.md).

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

- Adja meg, hogyan legyen kezelve az erőforrásokat, az erőforráscsoportban létezik, de nincsenek definiálva a sablonban, lásd: [Azure Resource Manager üzembe helyezési mód](deployment-modes.md).
- Kezelési REST-műveletek aszinkron kapcsolatos további információkért lásd: [Azure aszinkron műveletek követése](resource-manager-async-operations.md).
- A sablonokkal kapcsolatos további információkért lásd: [struktúra és az Azure Resource Manager-sablonok szintaxisát](resource-group-authoring-templates.md).

