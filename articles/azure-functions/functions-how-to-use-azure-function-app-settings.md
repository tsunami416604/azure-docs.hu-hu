---
title: Függvényalkalmazás-beállítások konfigurálása az Azure-ban
description: További információ az Azure-függvényalkalmazás-beállítások konfigurálásáról.
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.topic: conceptual
ms.date: 08/14/2019
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 662a04dbcc39f3fa95b0098eb8fe556b18b3495b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276945"
---
# <a name="manage-your-function-app"></a>A függvényalkalmazás kezelése 

Az Azure Functions egy függvényalkalmazás biztosítja az egyes függvények végrehajtási környezetét. A függvényalkalmazás viselkedése egy adott függvényalkalmazás által üzemeltetett összes funkcióra vonatkozik. A függvényalkalmazás minden függvényének azonos [nyelvűnek](supported-languages.md)kell lennie. 

A függvényalkalmazások egyes függvényei együtt vannak telepítve, és együtt vannak méretezve. Az azonos függvényalkalmazás összes függvénye példányonként osztja meg az erőforrásokat, ahogy a függvényalkalmazás méretezi. 

A kapcsolati karakterláncok, a környezeti változók és az egyéb alkalmazásbeállítások külön vannak definiálva az egyes függvényalkalmazásokhoz. A függvényalkalmazások között megosztandó adatokat külsőleg kell tárolni egy megőrzött tárolóban.

Ez a cikk a függvényalkalmazások konfigurálását és kezelését ismerteti. 

> [!TIP]  
> Számos konfigurációs lehetőség is kezelhető az [Azure CLI]használatával. 

## <a name="get-started-in-the-azure-portal"></a>Első lépések az Azure Portalon

Először nyissa meg az [Azure Portalon,] és jelentkezzen be az Azure-fiókjába. A portál tetején a keresősávba írja be a függvényalkalmazás nevét, majd válassza ki a listáról. A függvényalkalmazás kiválasztása után a következő oldal jelenik meg:

![A függvényalkalmazás áttekintése az Azure Portalon](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

A funkcióalkalmazás kezeléséhez szükséges mindent az áttekintő oldalon, különösen az **[Alkalmazás beállításai](#settings)** és a **[Platform funkciói](#platform-features)** között navigálhat.

## <a name="application-settings"></a><a name="settings"></a>Alkalmazásbeállítások

Az **Alkalmazásbeállítások** lap a függvényalkalmazás által használt beállításokat tartja fenn. Ezek a beállítások titkosítottan tárolódnak, és az **értékek megjelenítése** lehetőséget kell választania az értékek megtekintéséhez a portálon. Az Azure CLI használatával is elérheti az alkalmazásbeállításokat.

### <a name="portal"></a>Portál

Ha hozzá szeretne adni egy beállítást a portálon, válassza az **Új alkalmazás beállítást,** és adja hozzá az új kulcs-érték párt.

![Függvényalkalmazás-beállítások az Azure Portalon.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

### <a name="azure-cli"></a>Azure CLI

A [`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-list) parancs visszaadja a meglévő alkalmazásbeállításokat, ahogy az a következő példában is:

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

A [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) parancs hozzáad vagy frissít egy alkalmazásbeállítást. A következő példa létrehoz egy `CUSTOM_FUNCTION_APP_SETTING` kulcsot, amelynek neve és `12345`értéke:


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

### <a name="use-application-settings"></a>Alkalmazásbeállítások használata

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

Ha helyileg fejleszt függvényalkalmazást, a local.settings.json projektfájlban karban kell tartania ezeknek az értékeknek a helyi példányait. További információ: [Helyi beállításfájl](functions-run-local.md#local-settings-file).

## <a name="platform-features"></a>A platform jellemzői

![A Függvényalkalmazás platformfunkciói lapon.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

A függvényalkalmazások az Azure App Service platformon futnak, és karbantartják azokat. Mint ilyen, a függvényalkalmazások hozzáférhetnek az Azure alapvető webhosting platformjának legtöbb funkciójához. A **Platform funkciók** lapon érheti el az App Service platform számos funkcióját, amelyeket a függvényalkalmazásokban használhat. 

> [!NOTE]
> Nem minden App Service-funkció érhető el, ha egy függvényalkalmazás a fogyasztási üzemeltetési csomagon fut.

A cikk további részében az Azure Portal alábbi, függvényfunkciókat hasznos App Service-funkciókra összpontosít:

+ [App Service-szerkesztő](#editor)
+ [Console](#console)
+ [Speciális eszközök (Kudu)](#kudu)
+ [Telepítési lehetőségek](#deployment)
+ [CORS](#cors)
+ [Hitelesítés](#auth)

Az App Service-beállítások kezeléséről az [Azure App Service beállításainak konfigurálása](../app-service/configure-common.md)című témakörben olvashat bővebben.

### <a name="app-service-editor"></a><a name="editor"></a>Alkalmazásszolgáltatás-szerkesztő

![Az App Service szerkesztője](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

Az App Service-szerkesztő egy speciális portálon belüli szerkesztő, amely a JSON konfigurációs fájlok és kódfájlok egyaránt módosításához használható. Ha ezt a lehetőséget választja, egy külön böngészőlap indul el egy alapszerkesztővel. Ez lehetővé teszi a Git-tárházzal való integrációt, a kód futtatását és hibakeresési kódját, valamint a függvényalkalmazás beállításainak módosítását. Ez a szerkesztő továbbfejlesztett fejlesztői környezetet biztosít a funkciókhoz a beépített függvényszerkesztőhöz képest.  

Javasoljuk, hogy fontolja meg a helyi számítógépen a funkciók fejlesztését. Ha helyileg fejleszt és tesz közzé az Azure-ban, a projektfájlok csak olvashatók a portálon. További információ: [Az Azure Functions helyi kódolása és tesztelése.](functions-develop-local.md)

### <a name="console"></a><a name="console"></a>Console

![Funkcióalkalmazás-konzol](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

A portálon belüli konzol ideális fejlesztői eszköz, ha a parancssorból szeretné használni a függvényalkalmazást. A gyakori parancsok közé tartozik a könyvtár- és fájllétrehozás és -navigáció, valamint a kötegfájlok és parancsfájlok végrehajtása. 

Helyi fejlesztés esetén javasoljuk az [Azure Functions Core Tools](functions-run-local.md) és az Azure [CLI]használatát.

### <a name="advanced-tools-kudu"></a><a name="kudu"></a>Speciális eszközök (Kudu)

![Kudu konfigurálása](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

Az App Service speciális eszközei (más néven Kudu) hozzáférést biztosítanak a függvényalkalmazás speciális felügyeleti funkcióihoz. A Kudu-ból kezelheti a rendszerinformációkat, az alkalmazásbeállításokat, a környezeti változókat, a helybővítményeket, a HTTP-fejléceket és a kiszolgálóváltozókat. A **Kudu** indításakor a függvényalkalmazás SCM végpontjára is böngészhet, például`https://<myfunctionapp>.scm.azurewebsites.net/` 


### <a name="deployment-center"></a><a name="deployment"></a>Üzembe helyezési központ

Ha forrásvezérlő megoldást használ a függvénykód fejlesztéséhez és karbantartásához, a Deployment Center lehetővé teszi a forrásvezérlésből történő létrehozást és üzembe helyezést. A projekt a frissítések elkészítésekor épül fel és települ az Azure-ban. További információ: [Telepítési technológiák az Azure Functionsben.](functions-deployment-technologies.md)

### <a name="cross-origin-resource-sharing"></a><a name="cors"></a>Eltérő eredetű erőforrások megosztása

Az ügyfél rosszindulatú kódfuttatásának megakadályozása érdekében a modern böngészők blokkolják a webalkalmazásoktól a külön tartományban futó erőforrásokra irányuló kérelmeket. [A több forrásból származó erőforrás-megosztás (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS) lehetővé teszi, hogy egy `Access-Control-Allow-Origin` fejléc deklarálja, hogy mely eredetek hívhatnak végpontokat a függvényalkalmazásban.

#### <a name="portal"></a>Portál

Amikor konfigurálja az **Engedélyezett eredetlistát** a `Access-Control-Allow-Origin` függvényalkalmazáshoz, a fejléc automatikusan hozzáadódik a függvényalkalmazás HTTP-végpontjaiból érkező összes válaszhoz. 

![A függvényalkalmazás CORS-listájának konfigurálása](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

A helyettesítő karakter`*`( ) használatakor a rendszer figyelmen kívül hagyja az összes többi tartományt. 

A [`az functionapp cors add`](/cli/azure/functionapp/cors#az-functionapp-cors-add) paranccsal tartományt vehet fel az engedélyezett eredetlistára. A következő példa hozzáadja a contoso.com tartományt:

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

A [`az functionapp cors show`](/cli/azure/functionapp/cors#az-functionapp-cors-show) paranccsal felsorolhatja az aktuálisan engedélyezett eredeteket.

### <a name="authentication"></a><a name="auth"></a>Hitelesítés

![Hitelesítés konfigurálása függvényalkalmazáshoz](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

Ha a függvények HTTP-eseményindítót használnak, először a hívások hitelesítését kérheti. Az App Service támogatja az Azure Active Directory hitelesítését és a közösségi szolgáltatókkal, például a Facebookkal, a Microsofttal és a Twitterrel való bejelentkezést. Az adott hitelesítésszolgáltatók konfigurálásáról az [Azure App Service-hitelesítés áttekintése című témakörben olvashat részletesen.](../app-service/overview-authentication-authorization.md) 


## <a name="next-steps"></a>További lépések

+ [Az Azure App Service beállításainak konfigurálása](../app-service/configure-common.md)
+ [Azure Functions – folyamatos üzembe helyezés](functions-continuous-deployment.md)

[Azure CLI]: /cli/azure/
[Azure-portál]: https://portal.azure.com
