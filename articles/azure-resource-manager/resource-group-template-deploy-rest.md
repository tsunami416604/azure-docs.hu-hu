---
title: "A REST API-t és a sablon erőforrások telepítése |} Microsoft Docs"
description: "Az Azure-bA egy erőforrások telepítéséhez használható Azure Resource Manager és a Resource Manager REST API-t. Az erőforrások egy Resource Manager-sablonban vannak meghatározva."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 1d8fbd4c-78b0-425b-ba76-f2b7fd260b45
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/10/2017
ms.author: tomfitz
ms.openlocfilehash: b46b36805c2f33b1e066bbee2d0333113a26922a
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure Manager REST API-val
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-template-deploy.md)
> * [Azure CLI](resource-group-template-deploy-cli.md)
> * [Portal](resource-group-template-deploy-portal.md)
> * [REST API](resource-group-template-deploy-rest.md)
> 
> 

Ez a cikk ismerteti az erőforrások telepítése Azure Resource Manager-sablonok a Resource Manager REST API használata.  

> [!TIP]
> A hibakeresés központi telepítése során hibát talál segítséget:
> 
> * [Üzembe helyezési műveleteinek megtekintése](resource-manager-deployment-operations.md) további információkat információkkal könnyíti meg a hiba elhárítása
> * [Erőforrások az Azure-bA az Azure Resource Manager telepítésekor előforduló gyakori hibák elhárítása](resource-manager-common-deployment-errors.md) megtudhatja, hogyan közös telepítési hibák
> 
> 

A sablon lehet egy helyi fájl vagy a külső fájlra, amely egy URI keresztül érhető el. Ha a sablon olyan tárfiókban található, korlátozza a hozzáférést a sablont, és adjon meg egy közös hozzáférésű jogosultságkód (SAS) token üzembe helyezése során.

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

## <a name="deploy-with-the-rest-api"></a>A REST API-t központi telepítése
1. Állítsa be [közös paraméterek és fejlécek](https://docs.microsoft.com/rest/api/index), beleértve a hitelesítési tokenek.
2. Ha nem rendelkezik egy meglévő erőforráscsoportot, hozzon létre egy erőforráscsoportot. Adja meg az előfizetés-Azonosítóval, az új erőforráscsoport és megoldást igénylő helyének nevét. További információkért lásd: [hozzon létre egy erőforráscsoportot](https://docs.microsoft.com/rest/api/resources/resourcegroups#ResourceGroups_CreateOrUpdate).
   
        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
          <common headers>
          {
            "location": "West US",
            "tags": {
               "tagname1": "tagvalue1"
            }
          }
3. A telepítés előtt futtatnia kell a futtatásával ellenőrizze a [egy sablon telepítésének ellenőrzése](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_Validate) műveletet. Ha a központi telepítés tesztelése, adja meg a paramétereket pontosan ugyanúgy, a központi telepítés (lásd a következő lépésben) végrehajtása közben.
4. A központi telepítés létrehozása. Adja meg az előfizetés-Azonosítóval, az erőforráscsoport neve, a központi telepítést, és egy hivatkozást a sablon nevét. A sablon fájllal kapcsolatos információkért lásd: [paraméterfájl](#parameter-file). A REST API-t hozzon létre egy erőforráscsoportot kapcsolatos további információkért lásd: [hozzon létre egy sablon-üzembehelyezés](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_CreateOrUpdate). Figyelje meg a **mód** értéke **növekményes**. Állítsa be a teljes telepítés futtatásához **mód** való **Complete**. Ügyeljen arra, hogy ha a teljes módot, ha véletlenül is törli a sablonban lévő erőforrásokhoz.
   
        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
          <common headers>
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
   
      Jelentkezzen be a válasz tartalmat, a kérés tartalma vagy mindkettőt szeretné, ha **debugSetting** a kérelemben.
   
        "debugSetting": {
          "detailLevel": "requestContent, responseContent"
        }
   
      A tárfiók állíthat be egy közös hozzáférésű jogosultságkód (SAS) token használatával. További információkért lásd: [egy közös hozzáférésű Jogosultságkód hozzáférést delegálása](https://docs.microsoft.com/rest/api/storageservices/delegating-access-with-a-shared-access-signature).
5. A sablon központi telepítési állapotának beolvasása. További információkért lásd: [sablon-üzembehelyezés adatainak beolvasása](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_Get).
   
          GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

## <a name="parameter-file"></a>Paraméterfájl

A paraméterfájl használata paraméterértékek felelt meg a telepítés során, ha szüksége hozzon létre egy JSON-fájl formátuma nem a következőhöz hasonló:

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

A paraméter-fájl mérete nem lehet több mint 64 KB.

Meg kell adnia a kényes értéket a paraméterhez (például jelszót), ha hozzá kulcstároló ezt az értéket. Olvashatók be a key vault üzembe helyezése során, mert az előző példában látható módon. További információkért lásd: [biztonságos értéket átadni a telepítés során](resource-manager-keyvault-parameter.md). 

## <a name="next-steps"></a>További lépések
* Aszinkron REST műveleteinek kezelésére vonatkozó további tudnivalókért lásd: [nyomon követheti a aszinkron Azure műveleteket](resource-manager-async-operations.md).
* Például a .NET ügyféloldali kódtár erőforrásoknak történő telepítésének, [központi telepítése a .NET-kódtárakra és egy sablon használatával erőforrások](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Sablon paraméterek megadásához tekintse meg a [sablonok készítése](resource-group-authoring-templates.md#parameters).
* Útmutató a megoldások különböző környezetekben történő telepítéséhez: [Fejlesztési és tesztelési környezetek a Microsoft Azure eszközben](solution-dev-test-environments.md).
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.

