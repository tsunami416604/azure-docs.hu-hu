---
title: Az Azure függvényalkalmazás beállításainak konfigurálása | Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja az Azure Function app beállításait.
author: ggailey777
manager: gwallace
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: glenga
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 27e56c3bcaa08fee25c362cdfed64c61b92873cb
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982662"
---
# <a name="how-to-manage-a-function-app"></a>Function-alkalmazás kezelése

Azure Functions a functions-alkalmazás biztosítja az egyes függvények végrehajtási környezetét. A Function app Behavior az adott Function app által üzemeltetett összes függvényre vonatkozik. Ez a témakör ismerteti, hogyan konfigurálhatja és kezelheti a Function apps alkalmazást a Azure Portalban.

A kezdéshez nyissa meg a [Azure Portal](https://portal.azure.com) , és jelentkezzen be az Azure-fiókjába. A portál tetején a keresősávba írja be a függvényalkalmazás nevét, majd válassza ki a listáról. A Function alkalmazás kiválasztása után a következő oldal jelenik meg:

![A Function app áttekintése a Azure Portal](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

A függvény alkalmazásának áttekintés oldaláról, különösen az **[Alkalmazásbeállítások](#settings)** és a **[platform funkcióinak](#platform-features)** kezeléséhez minden szükséges információt megtalálhat.

## <a name="settings"></a>Alkalmazásbeállítások

Az **Alkalmazásbeállítások** lapon megtarthatja a Function alkalmazás által használt beállításokat.

![A Azure Portalban található Function app-beállítások.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

Ezeket a beállításokat a rendszer titkosított formában tárolja, és a portál értékeinek megjelenítéséhez ki kell választania az **értékek megjelenítése** elemet.

Egy beállítás hozzáadásához válassza az **új Alkalmazásbeállítás** lehetőséget, és adja hozzá az új kulcs-érték párokat.

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

Amikor helyileg fejleszt egy Function alkalmazást, ezeket az értékeket a local. Settings. JSON projektfájl tartja karban.

## <a name="platform-features"></a>A platform szolgáltatásai

![Function app platform szolgáltatásai lap.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

A Function apps a Azure App Service platformon fut, és karbantartja őket. Így a Function apps az Azure alapvető webes üzemeltetési platformjának legtöbb funkcióját elérheti. A **platform szolgáltatásai** lapon érheti el a app Service platform számos funkcióját, amelyet a Function apps alkalmazásban használhat. 

> [!NOTE]
> Nem minden App Service funkció érhető el, ha egy Function alkalmazás fut a használati üzemeltetési csomagon.

A témakör további részében a következő, a függvények számára hasznos App Service Azure Portal-funkciókra összpontosít:

+ [App Service szerkesztő](#editor)
+ [Console](#console)
+ [Speciális eszközök (kudu)](#kudu)
+ [Üzembe helyezési beállítások](#deployment)
+ [CORS](#cors)
+ [Hitelesítés](#auth)
+ [API-definíció](#swagger)

További információ a App Service beállítások használatáról: [Azure app Service beállításainak konfigurálása](../app-service/configure-common.md).

### <a name="editor"></a>App Service Editor

| | |
|-|-|
| ![Function app App Service Editor.](./media/functions-how-to-use-azure-function-app-settings/function-app-appsvc-editor.png)  | A App Service szerkesztő egy olyan speciális portál szerkesztő, amely a JSON konfigurációs fájlok és a fájlok fájljainak módosítására használható. Ha ezt a lehetőséget választja, egy külön böngésző fület indít egy alapszintű szerkesztővel. Ez lehetővé teszi a git-tárház, a futtatási és hibakeresési kód integrálását, valamint a Function app beállításainak módosítását. Ez a szerkesztő egy továbbfejlesztett fejlesztési környezetet biztosít a függvények számára az alapértelmezett Function app panelhez képest.    |

![A App Service szerkesztő](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

### <a name="console"></a>Konzol

| | |
|-|-|
| ![Function app Console a Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-console.png) | A portálon található konzol ideális fejlesztői eszköz, ha a parancssorból szeretné használni a Function alkalmazást. Gyakori parancsok például a címtár és a fájlok létrehozása és a Navigálás, valamint a Batch-fájlok és-parancsfájlok futtatása. |

![Function app Console](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

### <a name="kudu"></a>Speciális eszközök (kudu)

| | |
|-|-|
| ![Function alkalmazás kudu a Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-advanced-tools.png) | A App Service speciális eszközei (más néven kudu) hozzáférést biztosítanak a Function alkalmazás speciális felügyeleti funkcióihoz. A kudu a rendszerinformációk, az Alkalmazásbeállítások, a környezeti változók, a helybővítmények, a HTTP-fejlécek és a kiszolgálói változók kezelése. A **kudu** a Function app SCM-végpontjának tallózásával is elindíthatja, például:`https://<myfunctionapp>.scm.azurewebsites.net/` |

![Kudu konfigurálása](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)


### <a name="a-namedeploymentdeployment-options"></a><a name="deployment">Üzembe helyezési beállítások

| | |
|-|-|
| ![A Azure Portal funkció alkalmazás-telepítési lehetőségei](./media/functions-how-to-use-azure-function-app-settings/function-app-deployment-source.png) | A functions lehetővé teszi a függvény kódjának fejlesztését a helyi gépen. Ezután feltöltheti a helyi függvény alkalmazás projektjét az Azure-ba. A hagyományos FTP-feltöltés mellett a functions lehetővé teszi, hogy a Function alkalmazást a népszerű folyamatos integrációs megoldásokkal, például a GitHub, az Azure DevOps, a Dropbox, a bitbucket és mások használatával telepítse. További információ: [Azure functions folyamatos üzembe helyezése](functions-continuous-deployment.md). Az FTP vagy a helyi git használatával történő manuális feltöltéshez konfigurálnia kell [a központi telepítési hitelesítő adatokat](functions-continuous-deployment.md#credentials)is. |


### <a name="cors"></a>CORS

| | |
|-|-|
| ![Function alkalmazás CORS a Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-cors.png) | A szolgáltatásokban a kártékony programkódok végrehajtásának megelőzése érdekében App Service blokkolja a külső forrásokból származó Function apps-hívásokat. A functions támogatja a több eredetű erőforrás-megosztást (CORS), így meghatározhatja az engedélyezett eredetek engedélyezési sorrendjét, amelyből a függvények elfogadják a távoli kérelmeket.  |

![függvényalkalmazás CORS konfigurálása](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

### <a name="auth"></a>Hitelesítés

| | |
|-|-|
| ![Function app-hitelesítés a Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-authentication.png) | Ha a függvények HTTP-triggert használnak, megkövetelheti, hogy először hitelesíteni lehessen a hívásokat. App Service támogatja a Azure Active Directory hitelesítését és a közösségi szolgáltatókkal való bejelentkezést, például a Facebookot, a Microsoftot és a Twittert. Az egyes hitelesítő szolgáltatók konfigurálásával kapcsolatos részletekért tekintse meg a [Azure app Service Authentication áttekintése](../app-service/overview-authentication-authorization.md)című témakört. |

![Egy Function alkalmazás hitelesítésének konfigurálása](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)


### <a name="swagger"></a>API-definíció

| | |
|-|-|
| ![Function app API hencegés meghatározása a Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-api-definition.png) | A függvények lehetővé teszik a hencegés használatát, hogy az ügyfelek könnyebben használják fel a HTTP által aktivált funkciókat. További információ az API-definíciók és a hencegő használatával történő létrehozásáról [: REST API-val történő CORS Azure app Service-ben](../app-service/app-service-web-tutorial-rest-api.md). A functions-proxyk használatával egyetlen API-felületet is meghatározhat több függvényhez. További információ: [a Azure functions-proxyk használata](functions-proxies.md). |

![függvényalkalmazás API konfigurálása](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-apidef.png)



## <a name="next-steps"></a>További lépések

+ [Azure App Service beállítások konfigurálása](../app-service/configure-common.md)
+ [Azure Functions – folyamatos üzembe helyezés](functions-continuous-deployment.md)



