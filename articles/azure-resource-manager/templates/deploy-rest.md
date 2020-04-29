---
title: Erőforrások üzembe helyezése REST API és sablonnal
description: Erőforrások üzembe helyezése az Azure-ban Azure Resource Manager és Resource Manager REST API használatával. Az erőforrások egy Resource Manager-sablonban vannak meghatározva.
ms.topic: conceptual
ms.date: 06/04/2019
ms.openlocfilehash: 9cdb7b668e5170917b41ef49639bd9a17e538766
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80153233"
---
# <a name="deploy-resources-with-arm-templates-and-resource-manager-rest-api"></a>Erőforrások üzembe helyezése ARM-sablonokkal és Resource Manager-REST API

Ez a cikk azt ismerteti, hogyan használható a Resource Manager-REST API Azure Resource Manager-(ARM-) sablonokkal az erőforrások Azure-beli üzembe helyezéséhez.

Megadhatja a sablont a kérelem törzsében, vagy csatolhat egy fájlhoz. Fájl használata esetén helyi fájl vagy egy URI-n keresztül elérhető külső fájl lehet. Ha a sablon egy Storage-fiókban található, korlátozhatja a hozzáférést a sablonhoz, és megadhat egy közös hozzáférési aláírási (SAS-) tokent az üzembe helyezés során.

## <a name="deployment-scope"></a>Központi telepítés hatóköre

Az üzembe helyezést egy felügyeleti csoportra, egy Azure-előfizetésre vagy egy erőforráscsoporthoz is megcélozhatja. A legtöbb esetben az üzemelő példányokat egy erőforráscsoporthoz fogja megcélozni. Felügyeleti csoport vagy előfizetés központi telepítése a házirendek és a szerepkör-hozzárendelések alkalmazására a megadott hatókörön belül. Az előfizetések központi telepítését is használhatja az erőforráscsoport létrehozásához és az erőforrások üzembe helyezéséhez. A központi telepítés hatókörének függvényében különböző parancsokat kell használnia.

Egy **erőforráscsoporthoz**való üzembe helyezéshez használja a [központi telepítések – létrehozás](/rest/api/resources/deployments/createorupdate)lehetőséget. A kérelmet a következő címre küldi a rendszer:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Az **előfizetéshez**való üzembe helyezéshez használja a [központi telepítések – létrehozás az előfizetések hatókörében](/rest/api/resources/deployments/createorupdateatsubscriptionscope)lehetőséget. A kérelmet a következő címre küldi a rendszer:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Az előfizetési szintű központi telepítésekkel kapcsolatos további információkért lásd: [erőforráscsoportok és erőforrások létrehozása az előfizetési szinten](deploy-to-subscription.md).

Egy **felügyeleti csoportba**történő központi telepítéshez használja a [központi telepítések – létrehozás felügyeleti csoport hatókörét](/rest/api/resources/deployments/createorupdateatmanagementgroupscope). A kérelmet a következő címre küldi a rendszer:

```HTTP
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

További információ a felügyeleti csoport szintű központi telepítésekről: [erőforrások létrehozása a felügyeleti csoport szintjén](deploy-to-management-group.md).

A cikkben szereplő példák az erőforráscsoportok központi telepítését használják.

## <a name="deploy-with-the-rest-api"></a>Üzembe helyezés a REST API-val

1. [Közös paraméterek és fejlécek](/rest/api/azure/)beállítása, beleértve a hitelesítési jogkivonatokat.

1. Ha nem rendelkezik meglévő erőforráscsoporthoz, hozzon létre egy erőforráscsoportot. Adja meg az előfizetés-AZONOSÍTÓját, az új erőforráscsoport nevét és a megoldáshoz szükséges helyet. További információ: Create an [Resource Group (erőforráscsoport létrehozása](/rest/api/resources/resourcegroups/createorupdate)).

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2019-05-01
   ```

   A kérelem törzse például:

   ```json
   {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
   }
   ```

1. Ellenőrizze az üzemelő példányt, mielőtt végrehajtja a [sablon-telepítési művelet ellenőrzése](/rest/api/resources/deployments/validate) műveletet. A központi telepítés tesztelésekor pontosan úgy adja meg a paramétereket, ahogy a telepítés végrehajtásakor (a következő lépésben látható).

1. Sablon üzembe helyezéséhez adja meg az előfizetés AZONOSÍTÓját, az erőforráscsoport nevét, a központi telepítés nevét a kérelem URI-ja alapján.

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2019-05-01
   ```

   A kérelem törzsében adjon meg egy hivatkozást a sablonra és a paraméter fájlra. További információ a paraméter fájlról: [Resource Manager-paraméter fájljának létrehozása](parameter-files.md).

   Figyelje meg **mode** , hogy a mód **növekményes**értékre van állítva. Teljes telepítés futtatásához állítsa be a **módot** a **befejezéshez**. Ügyeljen arra, hogy a teljes módot használja, mert a sablonban nem szereplő erőforrásokat véletlenül nem lehet törölni.

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

    Ha a válasz tartalmát, a tartalom kérését vagy mindkettőt szeretné naplózni, vegyen fel **debugSetting** a kérelembe.

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

    Beállíthatja, hogy a Storage-fiók közös hozzáférésű aláírás (SAS) jogkivonatot használjon. További információ: [hozzáférés delegálása közös hozzáférési aláírással](/rest/api/storageservices/delegating-access-with-a-shared-access-signature).

    Ha egy paraméterhez (például jelszóhoz) bizalmas értéket kell megadnia, adja hozzá ezt az értéket egy kulcstartóhoz. Kérje le a Key vaultot az üzembe helyezés során, ahogy az az előző példában is látható. További információ: [biztonságos értékek továbbítása az üzembe helyezés során](key-vault-parameter.md).

1. A sablonhoz és paraméterekhez kapcsolódó fájlokhoz való csatolás helyett a kérés törzsében is felveheti őket. A következő példa a kérelem törzsét mutatja be a sablonnal és paraméterrel:

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
            "apiVersion": "2018-02-01",
            "name": "[variables('storageAccountName')]",
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

1. A sablon üzembe helyezési állapotának lekéréséhez használja a [Get](/rest/api/resources/deployments/get)lehetőséget.

   ```HTTP
   GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
   ```

## <a name="next-steps"></a>További lépések

- Ha hibát tapasztal a sikeres üzembe helyezéshez, olvassa el a [hiba visszaállítása a sikeres központi telepítéshez](rollback-on-error.md)című témakört.
- Ha meg szeretné adni, hogyan kezelje az erőforráscsoport meglévő erőforrásait, de a sablonban nincs definiálva, tekintse meg a [Azure Resource Manager üzembe helyezési módokat](deployment-modes.md).
- További információ az aszinkron REST-műveletek kezeléséről: az [aszinkron Azure-műveletek nyomon követése](../management/async-operations.md).
- A sablonokkal kapcsolatos további tudnivalókért tekintse meg [az ARM-sablonok szerkezetének és szintaxisának megismerését](template-syntax.md)ismertető témakört.

