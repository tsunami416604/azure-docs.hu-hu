---
title: "Kezelheti az erőforrásokat az Azure parancssori felülettel |} Microsoft Docs"
description: "Azure-erőforrások és csoportok kezelése az Azure parancssori felület (CLI) használatával"
editor: 
manager: timlt
documentationcenter: 
author: tfitzmac
services: azure-resource-manager
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 10/06/2017
ms.author: tomfitz
ms.openlocfilehash: c68f2a8b6e18dc2d51d8bbb5cd05bc037dc2fadb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-azure-cli-to-manage-azure-resources-and-resource-groups"></a>Azure-erőforrások és csoportok kezelése az Azure parancssori felület használatával

Ebből a cikkből megismerheti, hogyan kezelheti a megoldások kiépítését az Azure CLI és az Azure Resource Manager. Ha nem ismeri a Resource Manager, lásd: [Resource Manager áttekintése](resource-group-overview.md). Ez a cikk foglalkozik a felügyeleti feladatokat. Az alábbiakat fogja elvégezni:

1. Hozzon létre egy erőforráscsoportot
2. Az erőforráscsoport erőforrás hozzáadása
3. Az erőforrás egy címke hozzáadása
4. A nevek vagy címke értékek alapján-erőforrások lekérdezése
5. Alkalmazza, és távolítsa el az erőforrás zárolását
6. Egy erőforráscsoportot

Ez a cikk nem szerepelnek a Resource Manager-sablon üzembe helyezése az előfizetéshez. Ez az információ, lásd: [erőforrások a Resource Manager-sablonok és az Azure parancssori felület telepítése](resource-group-template-deploy-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Telepítéséhez, és a parancssori felület helyileg használatához, tekintse meg a [Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli).

## <a name="set-subscription"></a>Set-előfizetés

Ha egynél több előfizetéssel rendelkezik, átválthat egy másik előfizetést. Első lépésként Ismerkedjen meg az összes olyan előfizetést, a fiókjához.

```azurecli-interactive
az account list
```

Az engedélyezett és letiltott előfizetések listáját adja vissza.

```json
[
  {
    "cloudName": "AzureCloud",
    "id": "<guid>",
    "isDefault": true,
    "name": "Example Subscription One",
    "registeredProviders": [],
    "state": "Enabled",
    "tenantId": "<guid>",
    "user": {
      "name": "example@contoso.org",
      "type": "user"
    }
  },
  ...
]
```

Figyelje meg, hogy egy előfizetés az alapértelmezett van megjelölve. Ez az előfizetés a jelenlegi környezet műveletekhez. Váltson át egy másik előfizetésben található, adja meg az előfizetés nevét a **fiók beállítása az** parancsot.

```azurecli-interactive
az account set -s "Example Subscription Two"
```

Az aktuális előfizetési kontextust megjelenítéséhez használja **az fiók megjelenítése** paraméter nélkül:

```azurecli-interactive
az account show
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az előfizetéshez erőforrásokat telepítés megkezdése előtt létre kell hoznia az erőforrásokat tartalmazó erőforráscsoport.

Erőforráscsoport létrehozásához használja a **az csoport létrehozása** parancsot. A parancs a **neve** paraméter segítségével adjon meg egy nevet az erőforráscsoporthoz tartozó és a **hely** paramétert adja meg a helyet.

```azurecli-interactive
az group create --name TestRG1 --location "South Central US"
```

A kimenet a következő formátumban kell megadni:

```json
{
  "id": "/subscriptions/<subscription-id>/resourceGroups/TestRG1",
  "location": "southcentralus",
  "managedBy": null,
  "name": "TestRG1",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

Ha szeretné beolvasni az erőforráscsoport később, a következő paranccsal:

```azurecli-interactive
az group show --name TestRG1
```

Az erőforráscsoportok az előfizetésében használatához:

```azurecli-interactive
az group list
```

## <a name="add-resources-to-a-resource-group"></a>Erőforrások hozzáadása egy erőforráscsoportot

Erőforrás hozzáadása az erőforráscsoporthoz, használhatja a **az erőforrás létrehozása** vagy egy adott típusú erőforrások létrehozásakor parancsot (például **az storage-fiók létrehozása**). Előfordulhat, hogy ez egyszerűbbé teszi a parancs, amely csak az erőforrástípus, mert tartalmazza az új erőforrás szükséges a Tulajdonságok paramétereinek használata. Használandó **az erőforrás létrehozása**, ismernie kell az összes tulajdonság beállítása nélkül meg kell adni őket.

Azonban hozzáadása egy erőforrást parancsprogram keresztül okozhat jövőbeli zavart, mert az új erőforrás nem létezik a Resource Manager-sablon. Sablonok lehetővé teszik megbízható és ismételten a megoldás üzembe helyezéséhez.

A következő parancs létrehoz egy tárfiókot. A példában látható módon a neve helyett adja meg egy egyedi nevet a tárfiók. A névnek kell 3 és 24 karakter hosszúságúnak kell, és csak számokat és kisbetűket tartalmazhatnak. A példában látható módon a nevet használja, ha egy hibaüzenetet kapja, mert a név már használatban van.

```azurecli-interactive
az storage account create -n myuniquestorage -g TestRG1 -l westus --sku Standard_LRS
```

Ha szeretné ezt az erőforrást beolvasni a később, a következő paranccsal:

```azurecli-interactive
az storage account show --name myuniquestorage --resource-group TestRG1
```

## <a name="add-a-tag"></a>Címke hozzáadása

Címkék lehetővé teszik az erőforrások más tulajdonságokkal szerint rendezheti. Például előfordulhat, hogy több erőforrás részleghez tartozó különböző erőforrás csoportokban. Ezeket az erőforrásokat megjelölése azonos kategóriába tartozó alkalmazhat egy részleg címke és értéket. Vagy jelölheti meg használja-e egy üzemi és tesztkörnyezetben környezetben egy erőforrást. Ebben a cikkben címkékkel csak egy erőforráshoz, de a környezetben valószínűleg érdemes címkék azokra az erőforrások.

A következő parancsot a tárfiók két címkék vonatkozik:

```azurecli-interactive
az resource tag --tags Dept=IT Environment=Test -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts"
```

Címkék egyetlen objektumként frissülnek. Egy címke hozzáadása egy erőforrást, amely már tartalmazza a címkék, először kérjen le a meglévő címkéket. Az új címke hozzáadása a meglévő címkék tartalmazó objektumot, és alkalmazza újra a címkék az erőforráshoz.

```azurecli-interactive
jsonrtag=$(az resource show -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts"
```

## <a name="search-for-resources"></a>Erőforrások keresése

Használja a **az erőforráslistát** parancs beolvasása az erőforrások más keresési feltételeket.

* Ahhoz, hogy az erőforrás neve, adja meg a **neve** paraméter:

  ```azurecli-interactive
  az resource list -n myuniquestorage
  ```

* Ahhoz, hogy az összes erőforrást erőforráscsoportban, adja meg a **erőforráscsoport** paraméter:

  ```azurecli-interactive
  az resource list --resource-group TestRG1
  ```

* Ahhoz, hogy a címke nevét és értékét az erőforrások, adja meg a **címke** paraméter:

  ```azurecli-interactive
  az resource list --tag Dept=IT
  ```

* Az összes erőforrást az adott erőforrástípus, adjon meg a **erőforrástípus** paraméter:

  ```azurecli-interactive
  az resource list --resource-type "Microsoft.Storage/storageAccounts"
  ```

## <a name="get-resource-id"></a>Erőforrás-azonosító lekérése

Sok parancsok egy erőforrás-azonosító paramétert igénybe vehet. Az azonosító egy erőforrás és a tároló egy változóban, amelyet:

```azurecli-interactive
webappID=$(az resource show -g exampleGroup -n exampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
```

## <a name="lock-a-resource"></a>Egy erőforrás zárolása

Győződjön meg arról, hogy a kritikus fontosságú erőforrás nem véletlenül törölték vagy módosították van szüksége, alkalmazza a zárolási az erőforráshoz. Vagy megadhat egy **CanNotDelete** vagy **ReadOnly**.

Hozzon létre, vagy törölje a felügyeleti zárolás, hozzáféréssel kell rendelkeznie `Microsoft.Authorization/*` vagy `Microsoft.Authorization/locks/*` műveletek. A beépített szerepkörök csak a tulajdonos és a felhasználói hozzáférés adminisztrátora kapnak ezeket a műveleteket.

A zárolási alkalmazásához használja a következő parancsot:

```azurecli-interactive
az lock create --lock-type CanNotDelete --resource-name myuniquestorage --resource-group TestRG1 --resource-type Microsoft.Storage/storageAccounts --name storagelock
```

Az előző példában a zárolt erőforrás nem törölhető, amíg a rendszer eltávolítja a zárolást. A zárolási eltávolításához használja:

```azurecli-interactive
az lock delete --name storagelock --resource-group TestRG1 --resource-type Microsoft.Storage/storageAccounts --resource-name myuniquestorage
```

A beállítás zárolások kapcsolatos további információkért lásd: [erőforrások az Azure Resource Manager zárolása](resource-group-lock-resources.md).

## <a name="remove-resources-or-resource-group"></a>Távolítsa el az erőforrás vagy erőforráscsoport
Egy erőforrás vagy egy erőforráscsoportot is eltávolíthat. Egy erőforráscsoport, akkor is távolítsa el erőforrás csoportba tartozó összes erőforrást.

* A erőforráscsoportból erőforrás törléséhez használja a delete parancs törli az erőforrástípus. A parancs törli az erőforrást, de nem törli az erőforráscsoportot.

  ```azurecli-interactive
  az storage account delete -n myuniquestorage -g TestRG1
  ```

* Az erőforráscsoport és az ahhoz tartozó összes erőforrást törölheti a **az csoport törlése** parancsot.

  ```azurecli-interactive
  az group delete -n TestRG1
  ```

Mindkét parancsok a rendszer felkéri győződjön meg arról, hogy szeretne-e az erőforrás vagy erőforráscsoport eltávolítani.

## <a name="next-steps"></a>Következő lépések
* Resource Manager-sablonok létrehozásával kapcsolatos további tudnivalókért lásd: [Azure Resource Manager sablonok készítése](resource-group-authoring-templates.md).
* Sablonok telepítésével kapcsolatos további tudnivalókért lásd: [Azure Resource Manager-sablon az alkalmazás központi telepítését](resource-group-template-deploy-cli.md).
* Meglévő erőforrásokat áthelyezheti egy új erőforráscsoportot. Tekintse meg a [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](resource-group-move-resources.md).
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.