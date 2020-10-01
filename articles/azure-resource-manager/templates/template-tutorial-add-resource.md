---
title: Oktatóanyag – erőforrás hozzáadása sablonhoz
description: Az első Azure Resource Manager-sablon létrehozásának lépéseit ismerteti. Megismerheti a sablonfájl szintaxisát és a Storage-fiók központi telepítését.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 58a6423944abca703a42b68044e58d86187457bc
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91614376"
---
# <a name="tutorial-add-a-resource-to-your-arm-template"></a>Oktatóanyag: erőforrás hozzáadása az ARM-sablonhoz

Az [előző oktatóanyagban](template-tutorial-create-first-template.md)megtanulta, hogyan hozhat létre üres sablont, és hogyan telepítheti azt. Most már készen áll egy tényleges erőforrás üzembe helyezésére. Ebben az oktatóanyagban egy Storage-fiókot fog hozzáadni. Az oktatóanyag elvégzése körülbelül **9 percet** vesz igénybe.

## <a name="prerequisites"></a>Előfeltételek

Javasoljuk, hogy végezze el a [sablonokkal kapcsolatos bevezető oktatóanyagot](template-tutorial-create-first-template.md), de ez nem kötelező.

A Visual Studio Code-nak rendelkeznie kell a Resource Manager-eszközök bővítménnyel, valamint Azure PowerShell vagy az Azure CLI-vel. További információ: [sablon eszközei](template-tutorial-create-first-template.md#get-tools).

## <a name="add-resource"></a>Erőforrás hozzáadása

A Storage-fiók definíciójának meglévő sablonhoz való hozzáadásához tekintse meg a Kiemelt JSON-t a következő példában. A sablon részeinek másolása helyett másolja a teljes fájlt, és cserélje le a sablont a tartalmára.

Cserélje le a **{Unique-Name}** nevet (beleértve a kapcsos zárójeleket is) egyedi Storage-fióknév megadásával.

> [!IMPORTANT]
> A tárfiók nevének egyedinek kell lennie az egész Azure rendszerben. A névnek csak kisbetűvel vagy számmal kell rendelkeznie. Nem lehet hosszabb 24 karakternél. Lehet, hogy megpróbál egy elnevezési mintát használni, például a **store1** előtagként való használatát, majd hozzáadja a monogramját és a mai dátumot. A használt név például a következőhöz hasonló: **store1abc09092019**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json" range="1-19" highlight="5-17":::

A Storage-fiókok egyedi nevének kitalálása nem egyszerű, és nem jól működik a nagyméretű központi telepítések automatizálásához. Az oktatóanyag-Sorozat későbbi részében olyan sablon-funkciókat fog használni, amelyek megkönnyítik egy egyedi név létrehozását.

## <a name="resource-properties"></a>Erőforrás tulajdonságai

Lehet, hogy kíváncsi, hogyan lehet megkeresni az egyes erőforrástípusok által használandó tulajdonságokat. Az [ARM-sablon hivatkozásával](/azure/templates/) megkeresheti a telepíteni kívánt erőforrásokat.

Minden üzembe helyezett erőforrás rendelkezik legalább a következő tulajdonságokkal:

- **típus**: az erőforrás típusa. Ez az érték az erőforrás-szolgáltató névterének és az erőforrás típusának (például a Microsoft. Storage/storageAccounts) a kombinációja.
- **apiVersion**: az erőforrás létrehozásához használandó REST API verziója. Minden erőforrás-szolgáltató közzétette saját API-verzióit, így ez az érték a típusra jellemző.
- **Name (név**): az erőforrás neve.

A legtöbb erőforráshoz tartozik egy **Location** tulajdonság is, amely azt a régiót állítja be, ahol az erőforrás telepítve van.

A többi tulajdonság az erőforrás típusa és az API-verzió szerint változhat. Fontos tisztában lenni az API-verzió és a rendelkezésre álló tulajdonságok közötti kapcsolattal, így részletesebben is megtudhatja.

Ebben az oktatóanyagban egy Storage-fiókot adott hozzá a sablonhoz. Ezt az API-verziót a következő helyen tekintheti meg: [storageAccounts 2019-04-01](/azure/templates/microsoft.storage/2019-04-01/storageaccounts). Figyelje meg, hogy az összes tulajdonságot nem adta hozzá a sablonhoz. A tulajdonságok többsége nem kötelező. A Microsoft. Storage erőforrás-szolgáltató egy új API-verziót szabadíthat fel, de a telepítendő verziónak nem kell megváltoznia. Továbbra is használhatja ezt a verziót, és biztos lehet benne, hogy a telepítés eredményei konzisztensek lesznek.

Ha egy régebbi API-verziót (például [storageAccounts 2016-05-01](/azure/templates/microsoft.storage/2016-05-01/storageaccounts)) tekint meg, láthatja, hogy a tulajdonságok kisebb készlete áll rendelkezésre.

Ha úgy dönt, hogy megváltoztatja egy erőforrás API-verzióját, ügyeljen rá, hogy kiértékelje az adott verzió tulajdonságait, és megfelelően módosítsa a sablont.

## <a name="deploy-template"></a>Sablon üzembe helyezése

A sablont üzembe helyezheti a Storage-fiók létrehozásához. Adjon egy másik nevet az üzembe helyezésnek, hogy könnyen megtalálja az előzményekben.

Ha még nem hozta létre az erőforráscsoportot, tekintse meg az [erőforráscsoport létrehozása](template-tutorial-create-first-template.md#create-resource-group)című témakört. A példa feltételezi, hogy a **templateFile** változót a sablonfájl elérési útjára állította, ahogy az az [első oktatóanyagban](template-tutorial-create-first-template.md#deploy-template)is látható.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addstorage `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az üzembe helyezési parancs futtatásához az [Azure CLI](/cli/azure/install-azure-cli) legújabb verziójára van szükség.

```azurecli
az deployment group create \
  --name addstorage \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

> [!NOTE]
> Ha az üzemelő példány nem sikerült, a **részletes** kapcsoló használatával kérheti le a létrehozott erőforrásokra vonatkozó információkat. A **hibakeresési kapcsoló használatával** további információkat kaphat a hibakeresésről.

Két lehetséges üzembe helyezési hiba merülhet fel:

- Hiba: code = AccountNameInvalid; Az üzenet = {adjon meg egyedi nevet} nem érvényes Storage-fióknév. A Storage-fiók nevének 3 – 24 karakter hosszúnak kell lennie, és csak számokat és kisbetűket használjon.

    A sablonban cserélje le a **{ad-Unique-Name}** nevet egy egyedi Storage-fiók nevére.  Lásd: [erőforrás hozzáadása](#add-resource).

- Hiba: code = StorageAccountAlreadyTaken; Üzenet = a store1abc09092019 nevű Storage-fiók már használatban van.

    A sablonban próbálkozzon egy másik Storage-fiók nevével.

Ez a telepítés hosszabb időt vesz igénybe, mint az üres sablon üzembe helyezése, mivel a rendszer létrehozza a Storage-fiókot. Körülbelül egy percet is igénybe vehet, de általában gyorsabb.

## <a name="verify-deployment"></a>Az üzembe helyezés ellenőrzése

A központi telepítés ellenőrzéséhez tekintse meg az erőforráscsoportot a Azure Portalból.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldali menüben válassza az **erőforráscsoportok**lehetőséget.
1. Válassza ki azt az erőforráscsoportot, amelyet központilag telepített.
1. Láthatja, hogy a Storage-fiók telepítve van.
1. Figyelje meg, hogy az üzembe helyezési címke most már azt mondja: **központi telepítések: 2 sikeres**.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha továbblép a következő oktatóanyagra, nem kell törölnie az erőforráscsoportot.

Ha most leáll, érdemes lehet törölni a telepített erőforrásokat az erőforráscsoport törlésével.

1. A Azure Portal válassza ki a bal oldali menüből az **erőforráscsoportot** .
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza az **erőforráscsoport törlése** lehetőséget a felső menüben.

## <a name="next-steps"></a>További lépések

Létrehozott egy egyszerű sablont egy Azure Storage-fiók üzembe helyezéséhez.  A későbbi oktatóanyagokban megtudhatja, hogyan adhat hozzá paramétereket, változókat, erőforrásokat és kimeneteket egy sablonhoz. Ezek a funkciók sokkal összetettebb sablonok építőelemei.

> [!div class="nextstepaction"]
> [Paraméterek hozzáadása](template-tutorial-add-parameters.md)
