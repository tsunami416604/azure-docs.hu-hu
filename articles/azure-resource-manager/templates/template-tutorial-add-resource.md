---
title: Oktatóanyag – Erőforrás hozzáadása a sablonhoz
description: Az első Azure Resource Manager-sablon létrehozásának lépéseit ismerteti. Megtudhatja, hogy a sablonfájl szintaxisa és hogyan telepítheti a tárfiókot.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 79e337b411f9d115d93050ebeee346a526913d39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371736"
---
# <a name="tutorial-add-a-resource-to-your-arm-template"></a>Oktatóanyag: Erőforrás hozzáadása az ARM-sablonhoz

Az [előző oktatóanyagból](template-tutorial-create-first-template.md)megtanulta, hogyan hozhat létre üres sablont, és hogyan telepítheti azt. Most már készen áll egy tényleges erőforrás üzembe helyezésére. Ebben az oktatóanyagban egy tárfiókot ad hozzá. Az oktatóanyag körülbelül **9 percet** vesz igénybe.

## <a name="prerequisites"></a>Előfeltételek

Javasoljuk, hogy töltse ki a [sablonokról szóló bevezető oktatóanyagot,](template-tutorial-create-first-template.md)de ez nem kötelező.

A Visual Studio-kódnak rendelkeznie kell a Resource Manager Tools bővítménylel, valamint az Azure PowerShell vagy az Azure CLI használatával. További információt a [sabloneszközök című témakörben talál.](template-tutorial-create-first-template.md#get-tools)

## <a name="add-resource"></a>Erőforrás hozzáadása

Tárfiók-definíció hozzáadása a meglévő sablonhoz, tekintse meg a kiemelt JSON a következő példában. Ahelyett, hogy a sablon szakaszait másolná, másolja a teljes fájlt, és cserélje le a sablon tartalmát.

Cserélje le **a(z) {provide-unique-name}** nevet egy egyedi tárfióknévre.

> [!IMPORTANT]
> A tárfiók nevének egyedinek kell lennie az egész Azure rendszerben. A névnek csak kisbetűs vagy számok lehetnek. Nem lehet hosszabb 24 karakternél. Megpróbálhat egy elnevezési mintát, például **a store1** előtagként való használatát, majd a monogram és a mai dátum hozzáadását. A használt név például úgy nézhet ki, mint **a store1abc09092019**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json" range="1-19" highlight="5-17":::

A tárfiók egyedi nevének kitalálása nem könnyű, és nem működik jól a nagy méretű telepítések automatizálásához. Az oktatóanyag-sorozat későbbi részében olyan sablonfunkciókat fog használni, amelyek megkönnyítik az egyedi név létrehozását.

## <a name="resource-properties"></a>Erőforrás tulajdonságai

Lehet, hogy kíváncsi, hogyan lehet megtalálni az egyes erőforrás-típusok használandó tulajdonságokat. Az ARM [sablonhivatkozás segítségével](/azure/templates/) megkeresheti a telepíteni kívánt erőforrástípusokat.

Minden üzembe helyezett erőforrás legalább a következő három tulajdonsággal rendelkezik:

- **típus**: Az erőforrás típusa. Ez az érték az erőforrás-szolgáltató névterének és az erőforrástípusnak (például A Microsoft.Storage/storageAccounts) kombinációja.
- **apiVersion**: A REST API-t az erőforrás létrehozásához használandó verzió. Minden erőforrás-szolgáltató saját API-verziókat tett közzé, így ez az érték a típusra jellemző.
- **name**: Az erőforrás neve.

A legtöbb erőforrás rendelkezik egy **helytulajdonsággal** is, amely beállítja azt a régiót, ahol az erőforrás telepítve van.

A többi tulajdonság az erőforrás típusától és az API-verziótól függően változik. Fontos, hogy tisztában legyen az API-verzió és a rendelkezésre álló tulajdonságok közötti kapcsolat megértéséhez, ezért ugorjunk be részletesebben.

Ebben az oktatóanyagban egy tárfiókot adott hozzá a sablonhoz. Ezt az API-verziót a [storageAccounts 2019-04-01 oldalon láthatja.](/azure/templates/microsoft.storage/2019-04-01/storageaccounts) Figyelje meg, hogy nem adta hozzá az összes tulajdonságot a sablonhoz. Számos tulajdonság nem kötelező. A Microsoft.Storage erőforrás-szolgáltató feladhat egy új API-verziót, de a telepített verziónak nem kell módosulnia. Továbbra is használhatja ezt a verziót, és tudja, hogy a központi telepítés eredményei konzisztensek lesznek.

Ha egy régebbi API-verziót, például [a storageAccounts 2016-05-01-et](/azure/templates/microsoft.storage/2016-05-01/storageaccounts)tekint meg, láthatja, hogy kisebb tulajdonságok érhetők el.

Ha úgy dönt, hogy módosítja egy erőforrás API-verzióját, győződjön meg arról, hogy kiértékeli az adott verzió tulajdonságait, és megfelelően módosítja a sablont.

## <a name="deploy-template"></a>Sablon üzembe helyezése

A tárfiók létrehozásához telepítheti a sablont. Adjon a központi telepítésnek egy másik nevet, hogy könnyen megtalálhassa azt az előzményekben.

Ha még nem hozta létre az erőforráscsoportot, olvassa el az Erőforráscsoport létrehozása című [témakört.](template-tutorial-create-first-template.md#create-resource-group) A példa feltételezi, hogy beállította a **templateFile** változót a sablonfájl elérési útvonalára, ahogy az az [első oktatóanyagban](template-tutorial-create-first-template.md#deploy-template)látható.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addstorage `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A központi telepítési parancs futtatásához az Azure CLI [legújabb verziójával](/cli/azure/install-azure-cli) kell rendelkeznie.

```azurecli
az deployment group create \
  --name addstorage \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

Két lehetséges üzembe helyezési hiba, amely nek találkozhat:

- Hiba: Code=AccountNameInvalid; A Message={provide-unique-name} nem érvényes tárfióknév. A tárfiók nevének 3 és 24 karakter közötti hosszúságúnak kell lennie, és csak számokat és kisbetűket használhat.

    A sablonban cserélje le **{provide-unique-name}** elemet egy egyedi tárfióknévre.  Lásd: [Erőforrás hozzáadása](#add-resource).

- Hiba: Code=StorageAccountAlreadyTaken; Message=A store1abc09092019 nevű tárfiók már foglalt.

    A sablonban próbálkozzon egy másik tárfiók névvel.

Ez a központi telepítés hosszabb időt vesz igénybe, mint az üres sablon központi telepítése, mert a tárfiók jön létre. Ez eltarthat körülbelül egy percig, de általában gyorsabb.

## <a name="verify-deployment"></a>Az üzembe helyezés ellenőrzése

Az üzembe helyezést az Azure Portalon található erőforráscsoport feltárásával ellenőrizheti.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. A bal oldali menüben válassza az **Erőforráscsoportok**lehetőséget.
1. Válassza ki azt az erőforráscsoportot, amelybe telepítette.
1. Láthatja, hogy egy tárfiók telepítve van.
1. Figyelje meg, hogy a központi telepítési címke most azt mondja: **Telepítések: 2 sikeres.**

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha továbblép a következő oktatóanyagra, nem kell törölnie az erőforráscsoportot.

Ha most leáll, érdemes lehet az erőforráscsoport törlésével felvetheti a telepített erőforrásokat.

1. Az Azure Portalon válassza a bal oldali menü **Erőforráscsoport** lehetőséget.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza a felső menü **Erőforráscsoport törlése** parancsát.

## <a name="next-steps"></a>További lépések

Létrehozott egy egyszerű sablont egy Azure storage-fiók üzembe helyezéséhez.  A későbbi oktatóanyagokból megtudhatja, hogyan adhat hozzá paramétereket, változókat, erőforrásokat és kimeneteket egy sablonhoz. Ezek a funkciók sokkal összetettebb sablonok építőkövei.

> [!div class="nextstepaction"]
> [Paraméterek hozzáadása](template-tutorial-add-parameters.md)