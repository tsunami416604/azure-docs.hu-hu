---
title: Azure-alkalmazás konfigurációs tárolójának létrehozása Azure Resource Manager sablon használatával (ARM-sablon)
titleSuffix: Azure App Configuration
description: Ismerje meg, hogyan hozhat létre Azure-alkalmazás-konfigurációs tárolót Azure Resource Manager sablon (ARM-sablon) használatával.
author: ZhijunZhao
ms.author: zhijzhao
ms.date: 09/21/2020
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: 840f907015e9673caba46998493b5cb705de5fb7
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91824173"
---
# <a name="quickstart-create-an-azure-app-configuration-store-by-using-an-arm-template"></a>Rövid útmutató: Azure-alkalmazás konfigurációs tárolójának létrehozása ARM-sablonnal

Ez a rövid útmutató a következőket ismerteti:

- Alkalmazás-konfigurációs tároló üzembe helyezése ARM-sablon használatával
- Kulcs-érték létrehozása az alkalmazás konfigurációs tárolójában ARM-sablon használatával
- Az ARM-sablonban lévő alkalmazás-konfigurációs tárolóban lévő kulcs-értékek olvasása

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/en-us/resources/templates/101-app-configuration-store-kv/) közül származik. Létrehoz egy új alkalmazás-konfigurációs tárolót, amely két kulcs-értékkel rendelkezik belül. Ezután a függvény használatával `reference` kiírja a két kulcs-érték erőforrás értékét. A kulcs értékének olvasása így lehetővé teszi, hogy a sablon más helyein is használni lehessen.

A rövid útmutató a `copy` kulcs-érték erőforrás több példányának létrehozásához használja az elemet. Az elemmel kapcsolatos további tudnivalókért `copy` tekintse meg az [erőforrás-ITERÁCIÓ az ARM-sablonokban](../azure-resource-manager/templates/copy-resources.md)című témakört.

> [!IMPORTANT]
> Ehhez a sablonhoz az alkalmazás-konfiguráció erőforrás-szolgáltatójának vagy újabb verziójára van szükség `2020-07-01-preview` . Ez a verzió a `reference` függvényt használja a kulcsok értékének olvasására. Az `listKeyValue` előző verzióban a kulcs-érték olvasására használt függvény nem érhető el a verziótól kezdődően `2020-07-01-preview` .

:::code language="json" source="~/quickstart-templates/101-app-configuration-store-kv/azuredeploy.json" range="1-88" highlight="52-58,61-75,80,84":::

Két Azure-erőforrás van definiálva a sablonban:

- [Microsoft. AppConfiguration/configurationStores](/azure/templates/microsoft.appconfiguration/2020-06-01/configurationstores): hozzon létre egy alkalmazás-konfigurációs tárolót.
- Microsoft. AppConfiguration/configurationStores/Key értékek: hozzon létre egy kulcs-értéket az alkalmazás konfigurációs tárolóján belül.

> [!NOTE]
> Az `keyValues` Erőforrás neve a kulcs és a címke kombinációja. A kulcs és a címke a határolójeltel van összekötve `$` . A címke nem kötelező. A fenti példában a `keyValues` Name nevű erőforrás egy `myKey` címke nélküli kulcs-értéket hoz létre.
>
> A kódolás, más néven URL-kódolás, lehetővé teszi, hogy a kulcsok vagy címkék olyan karaktereket tartalmazzanak, amelyek nem engedélyezettek az ARM-sablon erőforrásainak neveiben. `%` a nem engedélyezett karakter vagy, ezért a `~` helyén kell használni. A név megfelelő kódolásához kövesse az alábbi lépéseket:
>
> 1. URL-kódolás alkalmazása
> 2. Csere `~` erre `~7E`
> 3. Csere `%` erre `~`
>
> Ha például kulcs-érték párokat szeretne létrehozni a kulcsnévvel `AppName:DbEndpoint` és a címke nevével `Test` , az erőforrás nevének a következőnek kell lennie: `AppName~3ADbEndpoint$Test` .

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy alkalmazás-konfigurációs tárolót két kulcs-értékkel belül.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store-kv%2Fazuredeploy.json)

A sablont a következő PowerShell-parancsmag használatával is üzembe helyezheti. A kulcs-érték a PowerShell-konzol kimenetében jelenik meg.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-configuration-store-kv/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)
1. A Azure Portal keresőmezőbe írja be az **alkalmazás konfigurációja**kifejezést. Válassza ki az **alkalmazás konfigurációját** a listából.
1. Válassza ki az újonnan létrehozott alkalmazás-konfigurációs erőforrást.
1. A **műveletek**területen kattintson a **Configuration Explorer** elemre.
1. Ellenőrizze, hogy a két kulcs-érték létezik-e.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, az alkalmazás konfigurációs tárolóját és az összes kapcsolódó erőforrást. Ha a jövőben az alkalmazás-konfigurációs tárolót tervezi használni, kihagyhatja a törlését. Ha nem kívánja tovább használni ezt a tárolót, törölje az ebben a rövid útmutatóban létrehozott összes erőforrást a következő parancsmag futtatásával:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Következő lépések

További információt az Azure-alkalmazás konfigurálásával kapcsolatos egyéb alkalmazások létrehozásáról a következő cikkben talál:

> [!div class="nextstepaction"]
> [Gyors útmutató: ASP.NET Core-alkalmazás létrehozása az Azure-alkalmazás konfigurálásával](quickstart-aspnet-core-app.md)