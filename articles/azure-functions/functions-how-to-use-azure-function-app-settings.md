---
title: Azure-Függvényalkalmazás beállításainak konfigurálása |} A Microsoft Docs
description: Ismerje meg, hogyan lehet Azure-függvényalkalmazás beállításainak konfigurálása.
services: ''
documentationcenter: .net
author: ggailey777
manager: jeconnoc
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: glenga
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 92ca09040836dfc55a9d709b12a0ee01192d6bac
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957390"
---
# <a name="how-to-manage-a-function-app-in-the-azure-portal"></a>Az Azure Portalon a függvényalkalmazás kezelése 

Az Azure Functions egy függvényalkalmazást az egyes függvények a végrehajtási környezet biztosít. Egy adott függvényalkalmazás által üzemeltetett összes függvényt függvény alkalmazás viselkedések vonatkoznak. Ez a témakör ismerteti, hogyan konfigurálhatja és kezelheti a függvényalkalmazásokat az Azure Portalon.

A kezdéshez nyissa meg a [az Azure portal](https://portal.azure.com) , és jelentkezzen be az Azure-fiókjával. A portál tetején a keresősávba írja be a függvényalkalmazás nevét, majd válassza ki a listáról. Miután kiválasztotta a függvényalkalmazást, a következő oldal jelenik meg:

![A funkció áttekintése az Azure Portalon](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

Minden, amit a függvényalkalmazás kezelése – áttekintés lapon, különösen is lépjen a **[Alkalmazásbeállítások](#settings)** és **[platformfunkciók](#platform-features)**.

## <a name="settings"></a>nastavení aplikace

A **Alkalmazásbeállítások** lap megőrzi a függvényalkalmazás által használt beállításokat.

![Függvényalkalmazás beállításai az Azure Portalon.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

Ezek a beállítások titkosítva tárolódnak, és ki kell választania **értékek megjelenítése** értékeit a portálon.

Egy beállítás hozzáadásához válassza **új Alkalmazásbeállítás** , és adja hozzá az új kulcs-érték pár.

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

Egy függvényalkalmazás helyileg fejlesztésekor ezeket az értékeket a local.settings.json soubor projektu karbantartása.

## <a name="platform-features"></a>A platform szolgáltatásai

![Függvény app platform szolgáltatásai lapon.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

A függvényalkalmazások futnak, és karbantartása, az Azure App Service platformon. A függvényalkalmazásokat, az Azure core webes üzemeltetési platform funkcióját a legtöbb hozzáféréssel rendelkezik. A **platformfunkciók** lapon látható, ahol az App Service platform, amelyet használhat a saját függvényalkalmazások számos funkcióhoz férhet hozzá. 

> [!NOTE]
> Nem minden App Service-szolgáltatások a függvényalkalmazás fut a Használatalapú szolgáltatási csomag esetén érhetők el.

Ez a témakör a következő App Service-szolgáltatások az Azure Portalon, amelyek hasznosak a Functions összpontosít:

+ [Az App Service editor](#editor)
+ [Console](#console)
+ [Speciális eszközök (Kudu)](#kudu)
+ [Központi telepítési beállítások](#deployment)
+ [CORS](#cors)
+ [Hitelesítés](#auth)
+ [API-definíció](#swagger)

Az App Service-beállításokkal kapcsolatos további információkért lásd: [konfigurálása az Azure App Service beállítások](../app-service/configure-common.md).

### <a name="editor"></a>App Service Editor

| | |
|-|-|
| ![A függvényalkalmazás az App Service editor.](./media/functions-how-to-use-azure-function-app-settings/function-app-appsvc-editor.png)  | Az App Service editor egy speciális a portálon szerkesztő, amely a JSON konfigurációs fájloknak és a jelenlegi funkcionalitása kódfájlok módosításához használhatja. Ezzel a művelettel elindítja egy külön böngészőlapon, egy alapszintű szerkesztővel. Ez lehetővé teszi, hogy a Git-tárház integrálása, futtatása és a kódok hibakeresése és módosíthatja a függvényalkalmazás-beállításokat. A szerkesztő a függvények az alapértelmezett függvény panelen képest bővített fejlesztési környezetet biztosít.    |

![Az App Service editor](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

### <a name="console"></a>Konzol

| | |
|-|-|
| ![Alkalmazás-konzolon a funkció az Azure Portalon](./media/functions-how-to-use-azure-function-app-settings/function-app-console.png) | A portálon a konzolt egy ideális fejlesztői eszköz esetén inkább a függvényalkalmazás a parancssorban kommunikál. Gyakori parancsok a következők: könyvtár és a fájl létrehozása és a navigáció, valamint parancsfájlok végrehajtása. |

![Függvény alkalmazás-konzolon](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

### <a name="kudu"></a>Speciális eszközök (Kudu)

| | |
|-|-|
| ![Függvényalkalmazás Kudu az Azure Portalon](./media/functions-how-to-use-azure-function-app-settings/function-app-advanced-tools.png) | A speciális eszközök (Kudu) App Service-hez adja meg a függvényalkalmazás speciális felügyeleti funkciókat biztosít. A Kudu kezelheti a rendszer-információkat, Alkalmazásbeállítások, a környezeti változók, bővítmények, HTTP-fejlécek és kiszolgálói változók. Is elindíthat, **Kudu** például keresse a függvényalkalmazás az SCM-végponthoz `https://<myfunctionapp>.scm.azurewebsites.net/` |

![Kudu konfigurálása](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)


### <a name="a-namedeploymentdeployment-options"></a><a name="deployment">Központi telepítési beállítások

| | |
|-|-|
| ![Függvény alkalmazás központi telepítési beállítások az Azure Portalon](./media/functions-how-to-use-azure-function-app-settings/function-app-deployment-source.png) | Függvények fejlesztése a függvény kódját a helyi gépen teszi lehetővé. Ezt követően feltöltheti a helyi függvényalkalmazás projektjét az Azure-bA. Hagyományos FTP-feltöltéshez mellett a Functions segítségével üzembe helyezheti a népszerű folyamatos integrációs megoldásokkal, például a GitHub, a Azure DevOps, a Dropbox, a Bitbucket és mások függvényalkalmazást. További információkért lásd: [Azure Functions – folyamatos üzembe helyezés](functions-continuous-deployment.md). Töltse fel az FTP- vagy a helyi Git segítségével manuálisan, is kell [az üzembe helyezési hitelesítő adatok konfigurálása](functions-continuous-deployment.md#credentials). |


### <a name="cors"></a>CORS

| | |
|-|-|
| ![Függvényalkalmazás CORS az Azure Portalon](./media/functions-how-to-use-azure-function-app-settings/function-app-cors.png) | Hogy a kártevő kód végrehajtása a szolgáltatások, az App Service letiltja a hívásokat a függvényalkalmazások külső forrásból. Functions támogatja az eltérő eredetű erőforrások megosztása (CORS) lehetővé teszi, hogy adja meg, amelyből a függvények távoli kérelmek elfogadhat engedélyezett eredetek "engedélyezett".  |

![Függvényalkalmazás konfigurálja a CORS](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

### <a name="auth"></a>Hitelesítés

| | |
|-|-|
| ![Függvény alkalmazás hitelesítése az Azure Portalon](./media/functions-how-to-use-azure-function-app-settings/function-app-authentication.png) | HTTP-trigger funkciók használatakor megkövetelheti hívások először hitelesíteni kell. App Service támogatja az Azure Active Directory-hitelesítést, és jelentkezzen be a közösségi szolgáltatók, például a Facebook, a Microsoft és a Twitter. További információ a konkrét hitelesítési szolgáltatók konfigurálása: [Azure App Service-hitelesítés áttekintése](../app-service/overview-authentication-authorization.md). |

![Függvényalkalmazás-hitelesítés konfigurálása](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)


### <a name="swagger"></a>API-definíció

| | |
|-|-|
| ![Függvény app API swagger-definíció az Azure Portalon](./media/functions-how-to-use-azure-function-app-settings/function-app-api-definition.png) | A Functions szolgáltatás támogatja, hogy az ügyfelek a HTTP által aktivált függvények könnyebben felhasználásához Swagger. A Swagger API-definíciók létrehozásával kapcsolatos további információkért látogasson el a [az Azure App Service cors-támogatással rendelkező RESTful API üzemeltetése](../app-service/app-service-web-tutorial-rest-api.md). Több függvények egyetlen API-felületet meghatározásához használhatja a Functions-proxyk. További információkért lásd: [használata az Azure Functions-proxyk](functions-proxies.md). |

![Függvényalkalmazás API konfigurálása](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-apidef.png)



## <a name="next-steps"></a>További lépések

+ [Az Azure App Service szolgáltatás beállításainak konfigurálása](../app-service/configure-common.md)
+ [Azure Functions – folyamatos üzembe helyezés](functions-continuous-deployment.md)



