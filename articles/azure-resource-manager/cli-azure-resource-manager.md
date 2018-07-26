---
title: Az Azure CLI-erőforrások kezelése |} A Microsoft Docs
description: Azure-erőforrások és csoportok kezelése az Azure parancssori felület (CLI) használatával
editor: ''
manager: timlt
documentationcenter: ''
author: tfitzmac
services: azure-resource-manager
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: conceptual
ms.date: 10/06/2017
ms.author: tomfitz
ms.openlocfilehash: dd111c33cbd348a05ed0f0c04f7325347612e54d
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39259889"
---
# <a name="use-the-azure-cli-to-manage-azure-resources-and-resource-groups"></a>Azure-erőforrások és -erőforráscsoportok kezelése az Azure CLI használatával

Ebből a cikkből megismerheti, hogyan kezelheti a megoldások az Azure CLI és az Azure Resource Manager. Ha nem ismeri a Resource Managerrel, tekintse meg a [Resource Manager áttekintése](resource-group-overview.md). Ez a cikk a felügyeleti feladatok összpontosít. Az alábbiakat fogja elvégezni:

1. Erőforráscsoport létrehozása
2. Adjon hozzá egy erőforrást az erőforráscsoport
3. Az erőforrás egy címke hozzáadása
4. A neve vagy a címke értéke alapján-erőforrások lekérdezése
5. Érvényesek, és távolítsa el a zárolást az erőforráson
6. Egy erőforráscsoport törlése

Ez a cikk nem jeleníti meg az előfizetés üzembe helyezése Resource Manager-sablonnal. Ez az információ, lásd: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](resource-group-template-deploy-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Helyi telepítése és használata a parancssori felület, lásd: [Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="set-subscription"></a>Előfizetés beállítása

Ha több előfizetéssel rendelkezik, átválthat egy másik előfizetésbe. Először nézzük meg a fiókhoz tartozó előfizetések.

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

Figyelje meg, hogy egy előfizetés az alapértelmezett van megjelölve. Ehhez az előfizetéshez műveletekhez az aktuális környezetben. Váltson egy másik előfizetésben, adja meg az előfizetés nevét a **fiók beállítása az** parancsot.

```azurecli-interactive
az account set -s "Example Subscription Two"
```

Az aktuális előfizetési környezetet, megjelenítéséhez használja **az fiók show** paraméter nélkül:

```azurecli-interactive
az account show
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Üzembe erőforrásokat az előfizetéshez, mielőtt létre kell hoznia egy erőforráscsoportot, mely tartalmazni fogja az erőforrásokat.

Az erőforráscsoport létrehozásához használja az **az group create** parancsot. A parancs a **neve** paramétert adja meg az erőforráscsoport nevét és a **hely** paraméterrel megadhatja a helyét.

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

Ha később beolvasni az erőforráscsoport van szüksége, használja a következő parancsot:

```azurecli-interactive
az group show --name TestRG1
```

Az erőforráscsoportok az előfizetésében, használja:

```azurecli-interactive
az group list
```

## <a name="add-resources-to-a-resource-group"></a>Erőforrások hozzáadása egy erőforráscsoportot

Adjon egy erőforrást az erőforráscsoport, használhatja a **az erőforrás-létrehozás** parancsot vagy egy adott típusú erőforrások létrehozásakor (például **az tárfiók létrehozása**). Előfordulhat, hogy ez egyszerűbbé teszi a parancs, amely jellemző-erőforrástípus mivel paraméter, amely szükséges ahhoz, hogy az új erőforrás-tulajdonságokat tartalmazza. Használandó **az erőforrás-létrehozás**, ismernie kell az összes tulajdonság beállítása őket megadása nélkül.

Azonban hozzáadása egy parancsfájl segítségével erőforrás okozhat a jövőbeli keveredési, mert az új erőforrás nem létezik a Resource Manager-sablonnal. Sablonok lehetővé teszik a megbízható és ismételten üzembe helyezheti megoldását.

A következő parancs létrehoz egy tárfiókot. A példában látható a neve helyett adja meg a tárfiók egyedi nevét. A neve csak 3 és 24 karakter hosszúságúnak kell, és csak számokból és kisbetűkből állhat. A példában látható nevet használja, ha hibaüzenetet kap, mert a név már használatban van.

```azurecli-interactive
az storage account create -n myuniquestorage -g TestRG1 -l westus --sku Standard_LRS
```

Ha ezt az erőforrást beolvasni később szüksége, használja a következő parancsot:

```azurecli-interactive
az storage account show --name myuniquestorage --resource-group TestRG1
```

## <a name="add-a-tag"></a>Egy címke hozzáadása

A címkék lehetővé teszik az erőforrások más tulajdonságok alapján rendezheti. Előfordulhat például, számos erőforrás eltérő erőforráscsoportokban részleghez tartozó. Az érintett erőforrásokra kell megjelölni azonos kategóriába tartozó alkalmazhat a department címke és érték. Másik lehetőségként tudja szabadként megjelölni, hogy egy erőforrás éles vagy tesztelési környezetben használatos. Ebben a cikkben a címkék alkalmazása csak egy erőforráshoz, de a környezetben valószínűleg logikus címkéjét szeretné alkalmazni az erőforrások.

A következő parancsot a tárfiókra vonatkozik két címkét:

```azurecli-interactive
az resource tag --tags Dept=IT Environment=Test -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts"
```

A címkék, egyetlen objektum frissülnek. Egy címke hozzáadása egy erőforrást, amely eleve tartalmazza a címkék, először kérjen le a meglévő címkéket. Az új címke hozzáadása az objektum, amely tartalmazza a meglévő címkéket, és alkalmazza újra az összes címke az erőforráshoz.

```azurecli-interactive
jsonrtag=$(az resource show -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts"
```

## <a name="search-for-resources"></a>Erőforrások keresése

Használja a **az erőforrások listájából** parancs használatával kérje le a különböző keresési feltételek erőforrásokat.

* Erőforrás neve szerint lekéréséhez adja meg a **neve** paramétert:

  ```azurecli-interactive
  az resource list -n myuniquestorage
  ```

* Az erőforráscsoport összes erőforrását lekéréséhez adja meg a **erőforráscsoport** paramétert:

  ```azurecli-interactive
  az resource list --resource-group TestRG1
  ```

* A címke nevét és értékét az összes erőforrás lekéréséhez adja meg a **címke** paramétert:

  ```azurecli-interactive
  az resource list --tag Dept=IT
  ```

* Adja meg az adott erőforrástípus összes az erőforrást a **erőforrástípus** paramétert:

  ```azurecli-interactive
  az resource list --resource-type "Microsoft.Storage/storageAccounts"
  ```

## <a name="get-resource-id"></a>Erőforrás-azonosító beszerzése

Számos parancs egy erőforrás-azonosító paramétert igénybe vehet. Egy erőforrás és a egy változóban tároljuk az azonosító lekéréséhez használja:

```azurecli-interactive
webappID=$(az resource show -g exampleGroup -n exampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
```

## <a name="lock-a-resource"></a>Erőforrás zárolása

Ellenőrizze, hogy kritikus fontosságú erőforrás véletlen törlését vagy módosítását van szüksége, amikor a zárolást az erőforrásra alkalmazni kívánt. Vagy megadhat egy **védve** vagy **ReadOnly**.

Hozzon létre vagy felügyeleti zárolások törlése, hozzáféréssel kell rendelkeznie `Microsoft.Authorization/*` vagy `Microsoft.Authorization/locks/*` műveleteket. A beépített szerepkörök csak a tulajdonos és a felhasználói hozzáférés rendszergazdája megkapják ezeket a műveleteket.

A alkalmazni, a zárolás, a következő paranccsal:

```azurecli-interactive
az lock create --lock-type CanNotDelete --resource-name myuniquestorage --resource-group TestRG1 --resource-type Microsoft.Storage/storageAccounts --name storagelock
```

Az előző példában a zárolt erőforrás nem törölhető, amíg a rendszer eltávolítja a zárolást. Távolítsa el a zárolást, használja:

```azurecli-interactive
az lock delete --name storagelock --resource-group TestRG1 --resource-type Microsoft.Storage/storageAccounts --resource-name myuniquestorage
```

A beállítás zárolások kapcsolatos további információkért lásd: [zárolhat erőforrásokat az Azure Resource Manager](resource-group-lock-resources.md).

## <a name="remove-resources-or-resource-group"></a>Távolítsa el az erőforrás vagy erőforráscsoport
Egy erőforrás vagy erőforráscsoport eltávolítása Amikor eltávolít egy erőforráscsoportot, adott erőforráscsoporton belüli összes erőforrást is eltávolítja.

* Erőforrás törlése az erőforráscsoportból, a Törlés parancs használata az törölni kívánt erőforrás típusát. A parancs törli az erőforrást, de nem törli az erőforráscsoportot.

  ```azurecli-interactive
  az storage account delete -n myuniquestorage -g TestRG1
  ```

* Az erőforráscsoport és az ahhoz tartozó összes erőforrás törléséhez használja az **az group delete** parancsot.

  ```azurecli-interactive
  az group delete -n TestRG1
  ```

Mindkét parancsot, a rendszer felkéri győződjön meg arról, hogy el szeretné távolítani az erőforrás vagy erőforráscsoport.

## <a name="next-steps"></a>További lépések
* Resource Manager-sablonok létrehozásával kapcsolatos további információkért lásd: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md).
* Sablonok üzembe helyezése kapcsolatos további információkért lásd: [alkalmazás üzembe helyezése az Azure Resource Manager-sablon](resource-group-template-deploy-cli.md).
* Meglévő erőforrásokat áthelyezheti egy új erőforráscsoportot. Példák: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](resource-group-move-resources.md).
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.