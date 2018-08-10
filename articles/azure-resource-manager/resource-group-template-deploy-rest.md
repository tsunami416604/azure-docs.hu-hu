---
title: A REST API-t és a sablon erőforrások üzembe helyezése |} A Microsoft Docs
description: Azure Resource Manager és a Resource Manager REST API használatával helyezze üzembe az Azure-erőforrásokat. Az erőforrások egy Resource Manager-sablonban vannak meghatározva.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 1d8fbd4c-78b0-425b-ba76-f2b7fd260b45
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/01/2018
ms.author: tomfitz
ms.openlocfilehash: ae2393d16d2c9c1000b00f5514e63c988303a83c
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628511"
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

A sablon lehet egy helyi fájl vagy egy külső fájl, amely egy URI-t keresztül érhető el. Ha a sablon a storage-fiókban található, korlátozza a hozzáférést a sablont, és adjon meg egy közös hozzáférésű jogosultságkód (SAS) üzembe helyezése során.

## <a name="deploy-with-the-rest-api"></a>Üzembe helyezés a REST API
1. Állítsa be [gyakori paramétereket és a fejlécek](/rest/api/azure/), beleértve a hitelesítési tokenek.

2. Ha nem rendelkezik egy meglévő erőforráscsoportot, hozzon létre egy erőforráscsoportot. Adja meg az előfizetés-Azonosítóját, nevét az új erőforráscsoport és a helyre, amely a megoldáshoz szükséges. További információkért lásd: [hozzon létre egy erőforráscsoportot](/rest/api/resources/resourcegroups/createorupdate).

  ```HTTP
  PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
  {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
  }
  ```

3. A telepítés előtt futtatnia kell a futtatásával ellenőrizze a [egy sablon telepítésének ellenőrzésére](/rest/api/resources/deployments/validate) műveletet. A központi telepítés tesztelésekor paramétereket megadnia, pontosan, mint az üzemelő példány (a következő lépésben látható) végrehajtása közben.

4. Központi telepítés létrehozása. Adja meg az előfizetés-azonosító, az erőforráscsoport nevét, az üzembe helyezés, valamint egy hivatkozás, a sablon nevét. A sablonfájl kapcsolatos információkért lásd: [paraméterfájl](#parameter-file). Hozzon létre egy erőforráscsoportot a REST API-val kapcsolatos további információkért lásd: [sablon üzembe helyezése](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_CreateOrUpdate). Figyelje meg a **mód** értékre van állítva **növekményes**. A teljes üzembe helyezés, állítsuk be **mód** való **Complete**. Ügyeljen arra, hogy a teljes mód használata, ha véletlenül is törli, amelyek nem szerepelnek a sablon erőforrások.

  ```HTTP
  PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
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

  ```HTTP
  "debugSetting": {
    "detailLevel": "requestContent, responseContent"
  }
  ```

    A storage-fiók beállítása egy közös hozzáférésű jogosultságkód (SAS) használata. További információkért lásd: [hozzáférés delegálása közös hozzáférésű Jogosultságkód](https://docs.microsoft.com/rest/api/storageservices/delegating-access-with-a-shared-access-signature).

5. A sablon központi telepítés állapotának lekéréséhez. További információkért lásd: [-sablonalapú telepítéssel kapcsolatos információk lekérése](/rest/api/resources/deployments/get).

  ```HTTP
  GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
  ```

## <a name="redeploy-when-deployment-fails"></a>Telepítse újra a központi telepítésének hibája esetén

Sikertelen telepítések esetén megadhatja, hogy a rendszer automatikusan újratelepítése egy korábbi üzemelő az üzembe helyezési előzményekből. Ez a beállítás használatához az üzemelő példányok egyedi névvel kell rendelkezniük az előzményekben található ellenőrizhető, hogy. Ha nem rendelkezik egyedi nevét, az aktuális telepítése sikertelen volt. a korábban sikeresen végrehajtott központi telepítés előzményei esetleg felülírhatja. Használhatja ezt a beállítást csak a legfelső szintű telepítések. Beágyazott sablonból üzemelő példányok nem újbóli üzembe helyezés érhetők el.

Telepítse újra a legutóbbi sikeres üzembe helyezés, ha a jelenlegi üzemelő példány nem sikerül, használja:

```HTTP
"onErrorDeployment": {
  "type": "LastSuccessful",
},
```

Adott üzembe helyezés ismételt üzembe helyezése, ha a jelenlegi üzemelő példány nem sikerül, használja:

```HTTP
"onErrorDeployment": {
  "type": "SpecificDeployment",
  "deploymentName": "<deploymentname>"
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
* Adja meg, hogyan kezelje az erőforrást, amely az erőforráscsoportban létezik, de nincsenek definiálva a sablonban, lásd: [Azure Resource Manager üzembe helyezési mód](deployment-modes.md).
* Kezelési REST-műveletek aszinkron kapcsolatos további információkért lásd: [Azure aszinkron műveletek követése](resource-manager-async-operations.md).
* Az üzembe helyezni erőforrásokat, a .NET ügyféloldali kódtár használatával egy példa: [erőforrások üzembe helyezése .NET-kódtárak és sablon](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* A paraméterek meghatározása sablonban, lásd: [sablonok készítése](resource-group-authoring-templates.md#parameters).
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.

