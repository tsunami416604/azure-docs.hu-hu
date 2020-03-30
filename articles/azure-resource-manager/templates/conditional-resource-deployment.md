---
title: Feltételes telepítés sablonokkal
description: Bemutatja, hogyan lehet feltételesen telepíteni egy erőforrást egy Azure Resource Manager-sablonban.
ms.topic: conceptual
ms.date: 12/03/2019
ms.openlocfilehash: f170710118c0e3de6f3643b6216ed55b83b5c7df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153420"
---
# <a name="conditional-deployment-in-arm-templates"></a>Feltételes telepítés ARM sablonokban

Néha szükség van egy erőforrás üzembe helyezésére egy Azure Resource Manager (ARM) sablonban. Az `condition` elem segítségével adja meg, hogy az erőforrás telepítve van-e. Az elem értéke igaz vagy hamis lesz. Ha az érték igaz, az erőforrás létrejön. Ha az érték hamis, az erőforrás nem jön létre. Az érték csak a teljes erőforrásra alkalmazható.

## <a name="new-or-existing-resource"></a>Új vagy meglévő erőforrás

A feltételes telepítés segítségével új erőforrást hozhat létre, vagy használhat egy meglévőt. A következő példa bemutatja, hogyan használhatja a feltételt egy új tárfiók üzembe helyezéséhez vagy egy meglévő tárfiók használatához.

```json
{
  "condition": "[equals(parameters('newOrExisting'),'new')]",
  "type": "Microsoft.Storage/storageAccounts",
  "apiVersion": "2017-06-01",
  "name": "[variables('storageAccountName')]",
  "location": "[parameters('location')]",
  "sku": {
    "name": "[variables('storageAccountType')]"
  },
  "kind": "Storage",
  "properties": {}
}
```

Ha az **újOrExisting** paraméter **értéke új,** a feltétel igaz értéket ad ki. A tárfiók telepítve van. Azonban ha **newOrExisting** van beállítva **a meglévő,** a feltétel kiértékeli a hamis, és a tárfiók nincs telepítve.

Az elemet használó teljes `condition` példasablonról a [Virtuális gép új vagy meglévő virtuális hálózattal, tárhellyel és nyilvános IP-címvel című témakörben olvashat.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions)

## <a name="allow-condition"></a>Feltétel engedélyezése

Átadhat egy paraméterértéket, amely azt jelzi, hogy egy feltétel engedélyezett-e. A következő példa egy SQL-kiszolgálót telepít, és opcionálisan engedélyezi az Azure IP-címeket.

```json
{
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2015-05-01-preview",
  "name": "[parameters('serverName')]",
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
      "apiVersion": "2015-05-01-preview",
      "name": "AllowAllWindowsAzureIps",
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

A teljes sablonról az [Azure SQL logikai kiszolgálója című témakörben olvashat.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server)

## <a name="runtime-functions"></a>Futásidejű függvények

Ha egy [referencia-](template-functions-resource.md#reference) vagy [listafüggvényt](template-functions-resource.md#list) használ egy feltételesen üzembe helyezett erőforrással, a függvény akkor is kiértékelésre kerül, ha az erőforrás nincs telepítve. Hibaüzenetet kap, ha a függvény nem létező erőforrásra hivatkozik.

Az [if](template-functions-logical.md#if) függvény segítségével győződjön meg arról, hogy a függvény csak az erőforrás üzembe helyezésekor kiértékelt feltételeket. Tekintse meg a [ha függvényt](template-functions-logical.md#if) egy mintasablonhoz, amely feltételesen üzembe helyezett erőforrással használja a ha és a hivatkozás funkciót.

Az erőforrást pontosan úgy állítja be, hogy egy feltételes [erőforrástól függjön,](define-resource-dependency.md) pontosan úgy, mint bármely más erőforrás. Ha egy feltételes erőforrás nincs telepítve, az Azure Resource Manager automatikusan eltávolítja azt a szükséges függőségek.

## <a name="condition-with-complete-mode"></a>Feltétel teljes móddal

Ha egy [sablont teljes móddal](deployment-modes.md) telepít, és egy erőforrás nincs telepítve, mert a feltétel kiértékelése hamis, az eredmény attól függ, hogy melyik REST API-verziót használja a sablon üzembe helyezéséhez. Ha a 2019-05-10-nél korábbi verziót használja, az erőforrás **nem törlődik.** A 2019-05-10 vagy újabb, az erőforrás **törlődik**. Az Azure PowerShell és az Azure CLI legújabb verziói törlik az erőforrást, ha a feltétel hamis.

## <a name="next-steps"></a>További lépések

* A sablonok létrehozásával kapcsolatos javaslatokért olvassa el az [ARM-sablon gyakorlati tanácsait.](template-best-practices.md)
* Egy erőforrás több példányának létrehozásához olvassa el az [Erőforrás-ismétlés az ARM-sablonokban című témakört.](copy-resources.md)