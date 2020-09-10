---
title: App Service alkalmazás létrehozása Azure Resource Manager sablon használatával
description: Hozza létre az első alkalmazást, hogy másodpercek alatt Azure App Service a Azure Resource Manager sablon használatával, amely a App Service üzembe helyezésének számos módszerének egyike.
author: msangapu-msft
ms.author: msangapu
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 05/25/2020
ms.custom: subject-armqs
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: e577616e0976ca050a55c8524e68129545ed1912
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89653574"
---
# <a name="create-app-service-app-using-an-azure-resource-manager-template"></a>App Service alkalmazás létrehozása Azure Resource Manager sablon használatával

A [Azure app Service](overview.md) használatának első lépései az alkalmazások felhőbe való telepítésével Azure Resource Manager sablonnal és az [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) -vel Cloud shell. Mivel ingyenes App Service szintet használ, a rövid útmutató elvégzéséhez nem jár költséggel.

 [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-azure-app-service-app"></a>Azure App Service-alkalmazás létrehozása

### <a name="review-the-template"></a>A sablon áttekintése

::: zone pivot="platform-windows"
Az ebben a gyorsútmutatóban használt sablon az [Azure gyorsindítási sablontárból](https://github.com/Azure/azure-quickstart-templates/) származik. Üzembe helyez egy App Service csomagot és egy App Service alkalmazást a Windows rendszeren. Kompatibilis a .NET Core, a .NET Framework, a PHP, a Node.js és a statikus HTML-alkalmazásokkal. A Java esetében lásd: [Java-alkalmazás létrehozása](app-service-web-get-started-java.md). 

[!code-json[<Azure Resource Manager template App Service Windows app>](~/quickstart-templates/101-app-service-docs-windows/azuredeploy.json)]

Két Azure-erőforrás van definiálva a sablonban:

* [**Microsoft. Web/kiszolgálófarmok**](/azure/templates/microsoft.web/serverfarms): hozzon létre egy app Service csomagot.
* [**Microsoft. Web/Sites**](/azure/templates/microsoft.web/sites): hozzon létre egy app Service alkalmazást.

Ez a sablon számos olyan paramétert tartalmaz, amelyek előre definiálva vannak az Ön kényelme érdekében. Tekintse meg az alábbi táblázatot a paraméterek alapértelmezett értékeinek és leírásának megtekintéséhez:

| Paraméterek | Típus    | Alapértelmezett érték                | Leírás |
|------------|---------|------------------------------|-------------|
| webAppName | sztring  | "webApp- **[`<uniqueString>`](/azure/azure-resource-manager/templates/template-functions-string#uniquestring)** " | Alkalmazás neve |
| location   | sztring  | "[[resourceGroup (). location](/azure/azure-resource-manager/templates/template-functions-resource#resourcegroup)]" | Alkalmazás-régió |
| SKU        | sztring  | F1                         | Példány mérete (F1 = ingyenes csomag) |
| language   | sztring  | .net                       | Programozási nyelv stack (.net, php, node, HTML) |
| helloWorld | boolean | Hamis                        | True = ""Helló világ!"alkalmazás" alkalmazás üzembe helyezése |
| reszakadás    | sztring  | " "                          | Külső git-tárház (opcionális) |
::: zone-end
::: zone pivot="platform-linux"
Az ebben a gyorsútmutatóban használt sablon az [Azure gyorsindítási sablontárból](https://github.com/Azure/azure-quickstart-templates/) származik. Üzembe helyez egy App Service csomagot és egy App Service alkalmazást Linuxon. Kompatibilis a App Service összes támogatott programozási nyelvével.

[!code-json[<Azure Resource Manager template App Service Linux app>](~/quickstart-templates/101-app-service-docs-linux/azuredeploy.json)]

Két Azure-erőforrás van definiálva a sablonban:

* [**Microsoft. Web/kiszolgálófarmok**](/azure/templates/microsoft.web/serverfarms): hozzon létre egy app Service csomagot.
* [**Microsoft. Web/Sites**](/azure/templates/microsoft.web/sites): hozzon létre egy app Service alkalmazást.

Ez a sablon számos olyan paramétert tartalmaz, amelyek előre definiálva vannak az Ön kényelme érdekében. Tekintse meg az alábbi táblázatot a paraméterek alapértelmezett értékeinek és leírásának megtekintéséhez:

| Paraméterek | Típus    | Alapértelmezett érték                | Leírás |
|------------|---------|------------------------------|-------------|
| webAppName | sztring  | "webApp- **[`<uniqueString>`](/azure/azure-resource-manager/templates/template-functions-string#uniquestring)** " | Alkalmazás neve |
| location   | sztring  | "[[resourceGroup (). location](/azure/azure-resource-manager/templates/template-functions-resource#resourcegroup)]" | Alkalmazás-régió |
| SKU        | sztring  | F1                         | Példány mérete (F1 = ingyenes csomag) |
| linuxFxVersion   | sztring  | "DOTNETCORE&#124;3,0        | "Programozási nyelvi verem &#124; verziója" |
| reszakadás    | sztring  | " "                          | Külső git-tárház (opcionális) |

---
::: zone-end


### <a name="deploy-the-template"></a>A sablon üzembe helyezése

A sablon üzembe helyezéséhez az Azure CLI használatos. Használhatja a Azure Portal, Azure PowerShell és REST API is. További információ az üzembe helyezési módszerekről: [sablonok üzembe helyezése](../azure-resource-manager/templates/deploy-powershell.md). 

A következő kód egy erőforráscsoportot, egy App Service tervet és egy webalkalmazást hoz létre. A rendszer alapértelmezett erőforráscsoportot, App Service tervet és helyet állított be. Cserélje le `<app-name>` egy globálisan egyedi alkalmazás nevére (érvényes karakterek:, `a-z` `0-9` és `-` ).

::: zone pivot="platform-windows"
Futtassa az alábbi kódot a .NET-keretrendszer alkalmazás Windows rendszeren való üzembe helyezéséhez.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup \
--parameters language=".net" sample="true" webAppName="<app-name>" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-windows/azuredeploy.json"
::: zone-end
::: zone pivot="platform-linux"
Run the code below to create a Python app on Linux. 

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --parameters webAppName="<app-name>" linuxFxVersion="PYTHON|3.7" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-linux/azuredeploy.json"
```

Egy másik nyelvi verem telepítéséhez a `linuxFxVersion` megfelelő értékekkel kell frissíteni. A minták alább láthatók. Az aktuális verziók megjelenítéséhez futtassa a következő parancsot a Cloud Shellban: `az webapp config show --resource-group myResourceGroup --name <app-name> --query linuxFxVersion`

| Nyelv    | Példa                                              |
|-------------|------------------------------------------------------|
| **.NET**    | linuxFxVersion = "DOTNETCORE&#124;3,0"                 |
| **PHP**     | linuxFxVersion = "PHP&#124;7,4"                        |
| **Node.js** | linuxFxVersion = "NODE&#124;10,15"                     |
| **Java**    | linuxFxVersion = "JAVA&#124;1,8 &#124;TOMCAT&#124;9,0" |
| **Python**  | linuxFxVersion = "PYTHON&#124;3,7"                     |
| **Ruby**    | linuxFxVersion = "RUBY&#124;2,6"                       |

---
::: zone-end

> [!NOTE]
> Itt találhat további [Azure app Service sablon mintákat](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sites).


## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

Tallózással keresse `http://<app_name>.azurewebsites.net/` meg és ellenőrizze, hogy létrejött-e.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, [törölje az erőforráscsoportot](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Üzembe helyezés a helyi Gitből](deploy-local-git.md)

> [!div class="nextstepaction"]
> [ASP.NET Core és SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Python és Postgres](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [PHP és MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Kapcsolódás az Azure SQL Database-hez a Javával](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
> [Egyéni tartomány leképezése](app-service-web-tutorial-custom-domain.md)