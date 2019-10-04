---
title: Function app-beállítások konfigurálása az Azure-ban
description: Ismerje meg, hogyan konfigurálhatja az Azure Function app beállításait.
author: ggailey777
manager: gwallace
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/14/2019
ms.author: glenga
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 7ad7f6156bbd8ea86e3e71bda4b23dac9722a0ef
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70170737"
---
# <a name="manage-your-function-app"></a>A Function alkalmazás kezelése 

Azure Functions a functions-alkalmazás biztosítja az egyes függvények végrehajtási környezetét. A Function app Behavior az adott Function app által üzemeltetett összes függvényre vonatkozik. A Function alkalmazás összes függvényének azonos [nyelvnek](supported-languages.md)kell lennie. 

A functions alkalmazásban az egyes függvények együtt vannak telepítve, és a méretezésük együtt történik. Az azonos Function alkalmazásban található összes függvény egy példányban, a Function alkalmazás méretezése szerint. 

A rendszer a kapcsolatok karakterláncait, környezeti változóit és egyéb beállításait külön definiálja az egyes functions-alkalmazásokhoz. A Function Apps-alkalmazások között megosztható összes adatnak külsőleg kell lennie a megőrzött tárolóban.

Ez a cikk bemutatja, hogyan konfigurálhatja és kezelheti a függvények alkalmazásait. 

> [!TIP]  
> Az [Azure CLI]használatával számos konfigurációs beállítás is kezelhető. 

## <a name="get-started-in-the-azure-portal"></a>Első lépések az Azure Portalon

A kezdéshez nyissa meg a [Azure Portal] , és jelentkezzen be az Azure-fiókjába. A portál tetején a keresősávba írja be a függvényalkalmazás nevét, majd válassza ki a listáról. A Function alkalmazás kiválasztása után a következő oldal jelenik meg:

![A Function app áttekintése a Azure Portal](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

A függvény alkalmazásának áttekintés oldaláról, különösen az **[Alkalmazásbeállítások](#settings)** és a **[platform funkcióinak](#platform-features)** kezeléséhez minden szükséges információt megtalálhat.

## <a name="settings"></a>Alkalmazásbeállítások

Az **Alkalmazásbeállítások** lapon megtarthatja a Function alkalmazás által használt beállításokat. Ezeket a beállításokat a rendszer titkosított formában tárolja, és a portál értékeinek megjelenítéséhez ki kell választania az **értékek megjelenítése** elemet. Az Alkalmazásbeállítások az Azure CLI használatával is elérhetők.

### <a name="portal"></a>Portál

Egy beállítás a portálon való hozzáadásához válassza az **új Alkalmazásbeállítás** lehetőséget, és adja hozzá az új kulcs-érték párokat.

![A Azure Portalban található Function app-beállítások.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

### <a name="azure-cli"></a>Azure CLI

A [`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-list) parancs az alkalmazás meglévő beállításait adja vissza, ahogy az alábbi példában is látható:

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

A [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) parancs egy Alkalmazásbeállítás hozzáadására vagy frissítésére szolgál. A következő példa létrehoz egy beállítást egy nevű `CUSTOM_FUNCTION_APP_SETTING` kulccsal és egy `12345`értékkel:


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

### <a name="use-application-settings"></a>Alkalmazásbeállítások használata

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

Amikor helyileg fejleszt egy Function-alkalmazást, a local. Settings. JSON projektfájl ezen értékeinek helyi másolatait kell fenntartania. További információ: [helyi beállítások fájl](functions-run-local.md#local-settings-file).

## <a name="platform-features"></a>A platform szolgáltatásai

![Function app platform szolgáltatásai lap.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

A Function apps a Azure App Service platformon fut, és karbantartja őket. Így a Function apps az Azure alapvető webes üzemeltetési platformjának legtöbb funkcióját elérheti. A **platform szolgáltatásai** lapon érheti el a app Service platform számos funkcióját, amelyet a Function apps alkalmazásban használhat. 

> [!NOTE]
> Nem minden App Service funkció érhető el, ha egy Function alkalmazás fut a használati üzemeltetési csomagon.

A cikk további részében a következő, a függvények számára hasznos App Service Azure Portal-funkciókra összpontosít:

+ [App Service szerkesztő](#editor)
+ [Console](#console)
+ [Speciális eszközök (kudu)](#kudu)
+ [Üzembe helyezési beállítások](#deployment)
+ [CORS](#cors)
+ [Hitelesítés](#auth)

További információ a App Service beállítások használatáról: [Azure app Service beállításainak konfigurálása](../app-service/configure-common.md).

### <a name="editor"></a>App Service Editor

![A App Service szerkesztő](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

A App Service szerkesztő egy olyan speciális portál szerkesztő, amely a JSON konfigurációs fájlok és a fájlok fájljainak módosítására használható. Ha ezt a lehetőséget választja, egy külön böngésző fület indít egy alapszintű szerkesztővel. Ez lehetővé teszi a git-tárház, a futtatási és hibakeresési kód integrálását, valamint a Function app beállításainak módosítását. Ez a szerkesztő fejlett fejlesztési környezetet biztosít a függvények összevetéséhez a beépített függvény-szerkesztővel szemben.  

Javasoljuk, hogy vegye fontolóra a függvények fejlesztését a helyi számítógépen. Amikor helyben fejleszti és közzéteszi az Azure-ban, a projektfájlok csak olvashatók a portálon. További információ: [code and test Azure functions helyileg](functions-develop-local.md).

### <a name="console"></a>Konzol

![Function app Console](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

A portálon található konzol ideális fejlesztői eszköz, ha a parancssorból szeretné használni a Function alkalmazást. Gyakori parancsok például a címtár és a fájlok létrehozása és a Navigálás, valamint a Batch-fájlok és-parancsfájlok futtatása. 

Helyi fejlesztés esetén javasolt a [Azure functions Core Tools](functions-run-local.md) és az [Azure CLI]használata.

### <a name="kudu"></a>Speciális eszközök (kudu)

![Kudu konfigurálása](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

A App Service speciális eszközei (más néven kudu) hozzáférést biztosítanak a Function alkalmazás speciális felügyeleti funkcióihoz. A kudu a rendszerinformációk, az Alkalmazásbeállítások, a környezeti változók, a helybővítmények, a HTTP-fejlécek és a kiszolgálói változók kezelése. A **kudu** a Function app SCM-végpontjának tallózásával is elindíthatja, például:`https://<myfunctionapp>.scm.azurewebsites.net/` 


### <a name="deployment"></a>Központi telepítési központ

Ha verziókövetés-megoldást használ a függvények kódjának fejlesztésére és karbantartására, a központi telepítési központ lehetővé teszi a forrás-vezérlőelemből való felépítést és üzembe helyezést. A projekt létrehozása és üzembe helyezése az Azure-ban történik, amikor frissítéseket végez. További információ: [Azure functions telepítési technológiák](functions-deployment-technologies.md).

### <a name="cors"></a>Több eredetű erőforrás-megosztás

A rosszindulatú programkódok végrehajtásának megakadályozása érdekében a modern böngészők letiltják a webalkalmazásokból érkező kéréseket egy különálló tartományban futó erőforrásoknak. A [több eredetű erőforrás-megosztás (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS) lehetővé `Access-Control-Allow-Origin` teszi, hogy egy fejléc deklarálja, hogy mely eredetűek hívhatják meg a végpontokat a Function alkalmazásban.

#### <a name="portal"></a>Portál

Ha beállítja a Function alkalmazás számára **engedélyezett Origins** -listát, a `Access-Control-Allow-Origin` rendszer automatikusan hozzáadja a fejlécet a Function alkalmazásban lévő http-végpontok összes válaszához. 

![Function alkalmazás CORS-listájának konfigurálása](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

Ha a helyettesítő karaktert`*`() használja, az összes többi tartományt figyelmen kívül hagyja a rendszer. 

A [`az functionapp cors add`](/cli/azure/functionapp/cors#az-functionapp-cors-add) parancs használatával adjon hozzá egy tartományt az engedélyezett Origins listához. A következő példa hozzáadja a contoso.com tartományt:

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

[`az functionapp cors show`](/cli/azure/functionapp/cors#az-functionapp-cors-show) A parancs használatával listázhatja az aktuálisan engedélyezett eredeteket.

### <a name="auth"></a>Hitelesítés

![Egy Function alkalmazás hitelesítésének konfigurálása](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

Ha a függvények HTTP-triggert használnak, megkövetelheti, hogy először hitelesíteni lehessen a hívásokat. App Service támogatja a Azure Active Directory hitelesítését és a közösségi szolgáltatókkal való bejelentkezést, például a Facebookot, a Microsoftot és a Twittert. Az egyes hitelesítő szolgáltatók konfigurálásával kapcsolatos részletekért tekintse meg a [Azure app Service Authentication áttekintése](../app-service/overview-authentication-authorization.md)című témakört. 


## <a name="next-steps"></a>További lépések

+ [Azure App Service beállítások konfigurálása](../app-service/configure-common.md)
+ [Azure Functions – folyamatos üzembe helyezés](functions-continuous-deployment.md)

[Azure CLI]: /cli/azure/
[Azure Portal]: https://portal.azure.com
