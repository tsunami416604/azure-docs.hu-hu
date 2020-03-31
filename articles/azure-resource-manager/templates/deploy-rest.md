---
title: Erőforrások üzembe helyezése REST API-val és sablonnal
description: Az Azure Resource Manager és a Resource Manager REST API használatával erőforrásokat helyezhet üzembe az Azure-ba. Az erőforrások egy Resource Manager-sablonban vannak meghatározva.
ms.topic: conceptual
ms.date: 06/04/2019
ms.openlocfilehash: 9cdb7b668e5170917b41ef49639bd9a17e538766
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153233"
---
# <a name="deploy-resources-with-arm-templates-and-resource-manager-rest-api"></a>Erőforrások üzembe helyezése ARM-sablonokkal és Erőforrás-kezelő REST API-val

Ez a cikk bemutatja, hogyan használhatja a Resource Manager REST API-t az Azure Resource Manager (ARM) sablonokkal az erőforrások Azure-ba való üzembe helyezéséhez.

A sablont felveheti a kérelem törzsére, vagy egy fájlra mutató hivatkozást. Fájl használata esetén lehet helyi vagy külső fájl, amely URI-n keresztül érhető el. Ha a sablon egy tárfiókban van, korlátozhatja a sablonhoz való hozzáférést, és a telepítés során biztosíthat egy sas-jogkivonatot.

## <a name="deployment-scope"></a>Központi telepítési hatókör

A központi telepítést egy felügyeleti csoportra, egy Azure-előfizetésre vagy egy erőforráscsoportra célozhatja. A legtöbb esetben a központi telepítéseket egy erőforráscsoportra fogja célozni. A felügyeleti csoport vagy az előfizetés-központi telepítések használatával szabályzatokat és szerepkör-hozzárendeléseket alkalmazhat a megadott hatókörben. Az előfizetési központi telepítések segítségével erőforráscsoportot is hozhat létre, és erőforrásokat helyezhet üzembe. A központi telepítés hatókörétől függően különböző parancsokat kell használnia.

Ha egy **erőforráscsoportra**szeretné telepíteni, használja [a Központi telepítések – Létrehozás .](/rest/api/resources/deployments/createorupdate) A kérelmet a következő kedélybe küldi a rendszer:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Ha egy **előfizetésre**szeretne telepíteni, használja [a Központi telepítések – Létrehozás előfizetési hatókörben](/rest/api/resources/deployments/createorupdateatsubscriptionscope). A kérelmet a következő kedélybe küldi a rendszer:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Az előfizetési szintű központi telepítésekről további információt [az Erőforráscsoportok és -erőforrások létrehozása előfizetési szinten](deploy-to-subscription.md)című témakörben talál.

Ha egy **felügyeleti csoportba**szeretné telepíteni, használja [a Központi telepítések – Létrehozás a felügyeleti csoport hatókörén.](/rest/api/resources/deployments/createorupdateatmanagementgroupscope) A kérelmet a következő kedélybe küldi a rendszer:

```HTTP
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

A felügyeleticsoport szintű központi telepítésekről további információt [az Erőforrások létrehozása a felügyeleti csoport szintjén című témakörben](deploy-to-management-group.md)talál.

Ebben a cikkben szereplő példák erőforráscsoport-telepítések használatát használja.

## <a name="deploy-with-the-rest-api"></a>Üzembe helyezés a REST API-val

1. Állítsa be [a közös paramétereket és fejléceket,](/rest/api/azure/)beleértve a hitelesítési jogkivonatokat is.

1. Ha nem rendelkezik meglévő erőforráscsoporttal, hozzon létre egy erőforráscsoportot. Adja meg az előfizetés azonosítóját, az új erőforráscsoport nevét és a megoldáshoz szükséges helyet. További információt az [Erőforráscsoport létrehozása](/rest/api/resources/resourcegroups/createorupdate)című témakörben talál.

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2019-05-01
   ```

   A kérelem törzs, mint például:

   ```json
   {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
   }
   ```

1. Ellenőrizze a központi telepítést a végrehajtása előtt a [sablon telepítési](/rest/api/resources/deployments/validate) művelet ellenőrzése futtatásával. A központi telepítés tesztelése során adja meg a paramétereket pontosan úgy, ahogy a központi telepítés végrehajtásakor (a következő lépésben látható).

1. Sablon üzembe helyezéséhez adja meg az előfizetés azonosítóját, az erőforráscsoport nevét, a központi telepítés nevét a kérelem URI-jában.

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2019-05-01
   ```

   A kérelem törzsében adjon meg egy hivatkozást a sablonra és a paraméterfájlra. A paraméterfájlról az [Erőforrás-kezelő paraméterfájl létrehozása](parameter-files.md)című témakörben talál további információt.

   Figyelje meg, hogy az **üzemmód** **növekményesre**van állítva. A teljes telepítés futtatásához állítsa a **módot** **Befejezés**módra. Legyen óvatos a teljes mód használatakor, mivel véletlenül törölheti a sablonban nem található erőforrásokat.

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

    Ha naplózni szeretné a válasz tartalmát, tartalmat vagy mindkettőt szeretne naplózni, akkor a **debugSetting-ot** is szerepeljenek a kérelemben.

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

    Beállíthatja a tárfiókot, hogy egy közös hozzáférésű aláírás (SAS) jogkivonatot használjon. További információt a [Hozzáférés delegálása megosztott hozzáférésű aláírással című témakörben talál.](/rest/api/storageservices/delegating-access-with-a-shared-access-signature)

    Ha egy paraméterhez (például egy jelszóhoz) bizalmas értéket kell megadnia, adja hozzá ezt az értéket egy key vaulthoz. A key vault lekérése az üzembe helyezés során, ahogy az előző példában látható. További információ: [Biztonságos értékek áthárítása az üzembe helyezés során.](key-vault-parameter.md)

1. Ahelyett, hogy a sablon és a paraméterek fájljaihoz csatolna, felveheti őket a kérelem törzsébe. A következő példa a kérelem törzsét mutatja be a sablonnal és a paraméterrel a szövegközi:

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

1. A sablon központi telepítésének állapotának leminősítéséhez használja a [Központi telepítések – Get parancsot.](/rest/api/resources/deployments/get)

   ```HTTP
   GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
   ```

## <a name="next-steps"></a>További lépések

- Ha hiba esetén szeretne visszaáll egy sikeres központi telepítésre, olvassa el [a Hiba visszaállítása a sikeres üzembe helyezéshez.](rollback-on-error.md)
- Ha meg szeretné adni, hogyan kezelje az erőforráscsoportban létező, de a sablonban nem definiált erőforrásokat, olvassa el az Azure Resource Manager telepítési módjai című [témakört.](deployment-modes.md)
- Az aszinkron REST-műveletek kezeléséről az [Aszinkron Azure-műveletek nyomon követése](../management/async-operations.md)című témakörben olvashat.
- A sablonokról az [ARM-sablonok szerkezetének és szintaxisának megismerése](template-syntax.md)című témakörben olvashat bővebben.

