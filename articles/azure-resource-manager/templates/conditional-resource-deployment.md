---
title: Feltételes üzembe helyezés sablonokkal
description: Ismerteti, hogyan lehet erőforrást feltételesen üzembe helyezni egy Azure Resource Manager-sablonban.
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: effa7fe6ee1393e44a124bc087609da5d4898210
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84259320"
---
# <a name="conditional-deployment-in-arm-templates"></a>Feltételes üzembe helyezés ARM-sablonokban

Előfordulhat, hogy egy Azure Resource Manager (ARM-) sablonban is telepítenie kell egy erőforrást. Az `condition` elem használatával adhatja meg, hogy az erőforrás telepítve van-e. Az elem értéke TRUE (igaz) vagy FALSE (hamis) értékre lesz feloldva. Ha az érték TRUE (igaz), a rendszer létrehozza az erőforrást. Ha az érték false (hamis), az erőforrás nincs létrehozva. Az értéket csak a teljes erőforrásra lehet alkalmazni.

> [!NOTE]
> A feltételes üzembe helyezés nem a [gyermek erőforrásaira](child-resource-name-type.md)van kaszkád. Ha az erőforrást és annak alárendelt erőforrásait feltételesen szeretné üzembe helyezni, akkor minden egyes erőforrástípus esetében ugyanazt a feltételt kell alkalmaznia.

## <a name="new-or-existing-resource"></a>Új vagy meglévő erőforrás

A feltételes telepítés használatával létrehozhat egy új erőforrást, vagy használhat egy meglévőt is. Az alábbi példa bemutatja, hogyan használható a feltétel egy új Storage-fiók üzembe helyezésére vagy egy meglévő Storage-fiók használatára.

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

Ha a **newOrExisting** paraméter értéke **új**, a feltétel igaz értéket ad vissza. A Storage-fiók telepítve van. Ha azonban a **newOrExisting** a **meglévő**értékre van állítva, a feltétel hamis értéket ad vissza, és a Storage-fiók nincs telepítve.

A elemet használó teljes példaként `condition` tekintse meg a [virtuális gép új vagy meglévő Virtual Network, tárterületet és nyilvános IP-címet](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions).

## <a name="allow-condition"></a>Feltétel engedélyezése

Egy paraméter értékét átadhatja, amely jelzi, hogy engedélyezett-e a feltétel. Az alábbi példa egy SQL Servert telepít, és opcionálisan engedélyezi az Azure IP-címeket.

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

A teljes sablonhoz lásd: [Azure SQL logikai kiszolgáló](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server).

## <a name="runtime-functions"></a>Futásidejű függvények

Ha a [hivatkozás](template-functions-resource.md#reference) vagy a [lista](template-functions-resource.md#list) függvényt egy feltételesen telepített erőforrással használja, akkor a függvény akkor is ki lesz értékelve, ha az erőforrás nincs telepítve. Hibaüzenet jelenik meg, ha a függvény olyan erőforrásra hivatkozik, amely nem létezik.

Az [IF](template-functions-logical.md#if) függvény használatával győződjön meg arról, hogy a függvény csak az erőforrás telepítésekor feltételként van kiértékelve. Tekintse meg az IF [függvényt](template-functions-logical.md#if) egy olyan sablon esetében, amely a IF és a hivatkozást egy feltételesen telepített erőforrással használja.

Egy erőforrást egy feltételes erőforrástól [függőként](define-resource-dependency.md) kell beállítania, pontosan úgy, ahogy bármely más erőforrás. Ha egy feltételes erőforrás nincs telepítve, Azure Resource Manager automatikusan eltávolítja a szükséges függőségekről.

## <a name="complete-mode"></a>Teljes mód

Ha [teljes móddal](deployment-modes.md) rendelkező sablont telepít, és az erőforrás nincs telepítve, mert a feltétel hamis értéket ad vissza, akkor az eredmény attól függ, hogy REST API milyen verziót használ a sablon telepítéséhez. Ha 2019-05-10-nál korábbi verziót használ, az erőforrás **nem törlődik**. A 2019-05-10-es vagy újabb verziókban az erőforrás **törölve lesz**. A Azure PowerShell és az Azure CLI legújabb verziói törlik az erőforrást, ha a feltétel hamis.

## <a name="next-steps"></a>További lépések

* A sablonok létrehozásával kapcsolatos javaslatokért lásd: [ARM-sablon – ajánlott eljárások](template-best-practices.md).
* Egy erőforrás több példányának létrehozásához tekintse [meg az erőforrás-iteráció az ARM-sablonokban](copy-resources.md)című témakört.
