---
title: Feltételes üzembe helyezés Azure Resource Manager-sablonokkal
description: Ismerteti, hogyan lehet erőforrást feltételesen üzembe helyezni egy Azure Resource Manager-sablonban.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: tomfitz
ms.openlocfilehash: 88f8b6a8dcce0e498a7b81b8741072bcf4cfcad8
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259508"
---
# <a name="conditional-deployment-in-resource-manager-templates"></a>Feltételes üzembe helyezés Resource Manager-sablonokban

Néha szükség van egy erőforrás üzembe helyezésére egy sablonban. Az `condition` elem használatával adhatja meg, hogy az erőforrás telepítve van-e. Az elem értéke TRUE (igaz) vagy FALSE (hamis) értékre lesz feloldva. Ha az érték TRUE (igaz), a rendszer létrehozza az erőforrást. Ha az érték false (hamis), az erőforrás nincs létrehozva. Az értéket csak a teljes erőforrásra lehet alkalmazni.

## <a name="new-or-existing-resource"></a>Új vagy meglévő erőforrás

A feltételes telepítés használatával létrehozhat egy új erőforrást, vagy használhat egy meglévőt is. Az alábbi példa bemutatja, hogyan használható a feltétel egy új Storage-fiók üzembe helyezésére vagy egy meglévő Storage-fiók használatára.

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[parameters('location')]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

Ha a **newOrExisting** paraméter értéke **új**, a feltétel igaz értéket ad vissza. A Storage-fiók telepítve van. Ha azonban a **newOrExisting** a **meglévő**értékre van állítva, a feltétel hamis értéket ad vissza, és a Storage-fiók nincs telepítve.

A `condition` elemet használó teljes példaként tekintse meg a [virtuális gép új vagy meglévő Virtual Network, tárterületet és nyilvános IP-címet](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions).

## <a name="allow-condition"></a>Feltétel engedélyezése

Egy paraméter értékét átadhatja, amely jelzi, hogy engedélyezett-e a feltétel. Az alábbi példa egy SQL Servert telepít, és opcionálisan engedélyezi az Azure IP-címeket.

```json
{
    "type": "Microsoft.Sql/servers",
    "name": "[parameters('serverName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[parameters('location')]",
    "properties": {
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
        "version": "12.0"
    },
    "resources": [
        {
            "condition": "[parameters('allowAzureIPs')]",
            "type": "firewallRules",
            "name": "AllowAllWindowsAzureIps",
            "apiVersion": "2015-05-01-preview",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Sql/servers/', parameters('serverName'))]"
            ],
            "properties": {
                "endIpAddress": "0.0.0.0",
                "startIpAddress": "0.0.0.0"
            }
        }
    ]
}
```

A teljes sablonhoz lásd: [Azure SQL logikai kiszolgáló](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server).

## <a name="runtime-functions"></a>Futásidejű függvények

Ha a [hivatkozás](resource-group-template-functions-resource.md#reference) vagy a [lista](resource-group-template-functions-resource.md#list) függvényt egy feltételesen telepített erőforrással használja, akkor a függvény akkor is ki lesz értékelve, ha az erőforrás nincs telepítve. Hibaüzenet jelenik meg, ha a függvény olyan erőforrásra hivatkozik, amely nem létezik.

Az [IF](resource-group-template-functions-logical.md#if) függvény használatával győződjön meg arról, hogy a függvény csak az erőforrás telepítésekor feltételként van kiértékelve. Tekintse meg az IF [függvényt](resource-group-template-functions-logical.md#if) egy olyan sablon esetében, amely a IF és a hivatkozást egy feltételesen telepített erőforrással használja.

## <a name="next-steps"></a>További lépések

* A sablonok létrehozásával kapcsolatos javaslatokért lásd: [Azure Resource Manager sablon ajánlott eljárásai](template-best-practices.md).
* Egy erőforrás több példányának létrehozásához tekintse [meg Azure Resource Manager sablonok erőforrás, tulajdonság vagy változó iterációját](resource-group-create-multiple.md).