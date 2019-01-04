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
ms.openlocfilehash: 7497255dcad55cea86e0c640e2f1423d7d763a7f
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53713626"
---
# <a name="how-to-manage-a-function-app-in-the-azure-portal"></a>Az Azure Portalon a függvényalkalmazás kezelése 

Az Azure Functions egy függvényalkalmazást az egyes függvények a végrehajtási környezet biztosít. Egy adott függvényalkalmazás által üzemeltetett összes függvényt függvény alkalmazás viselkedések vonatkoznak. Ez a témakör ismerteti, hogyan konfigurálhatja és kezelheti a függvényalkalmazásokat az Azure Portalon.

A kezdéshez nyissa meg a [az Azure portal](https://portal.azure.com) , és jelentkezzen be az Azure-fiókjával. A portál tetején a keresősávba írja be a függvényalkalmazás nevét, majd válassza ki a listáról. Miután kiválasztotta a függvényalkalmazást, a következő oldal jelenik meg:

![A funkció áttekintése az Azure Portalon](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

## <a name="favorite"></a>Kedvenc függvények a portálon 

Előfordulhat, hogy az erőforrások megtalálása nehezebb a [Azure Portal]. Hogy könnyebben megtalálja a létrehozott függvényalkalmazás, vegyen fel Függvényalkalmazásokat a Kedvencek, a portálon. 

1. Jelentkezzen be az [Azure Portal].

2. A bal alsó sarokban található nyílra kattintva bontsa ki az összes szolgáltatást, írja be a `Functions` kifejezést a **Szűrő** mezőbe, majd kattintson a **Függvényalkalmazások** elem melletti csillagra.  
 
    ![Függvényalkalmazás létrehozása az Azure Portalon](./media/functions-how-to-use-azure-function-app-settings/functions-favorite-function-apps.png)

    Ezzel felveheti a Függvények ikont a portál bal oldali menüjébe.

3. Zárja be a menüt, majd a Függvények ikon megtekintéséhez görgessen le a képernyő aljára. Erre az ikonra kattintva megtekintheti a függvényalkalmazásainak listáját. Kattintson a függvényalkalmazásra az alkalmazásban található függvények használatához. 
 
    ![A Függvényalkalmazásokat a Kedvencek](./media/functions-how-to-use-azure-function-app-settings/functions-function-apps-hub.png)
 
[Azure Portal]: https://portal.azure.com/

## <a name="manage-app-service-settings"></a>Függvény alkalmazás Beállítások lap

![A funkció áttekintése az Azure Portalon.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

A **beállítások** lapon látható, amelyen frissítheti a függvényalkalmazás által használt funkciók futtatókörnyezet verziójának. Emellett akkor is, a gazdagép kulcsokat használt HTTP-hozzáférés korlátozása a függvények által az függvényalkalmazás üzemeltetett kezelheti.

Functions támogatja az üzemeltető Használatalapú és App Service-futtatási csomag. További információkért lásd: [válassza ki a megfelelő service-csomag az Azure Functions](functions-scale.md). A Használatalapú csomag jobb kiszámíthatóbbá funkciók lehetővé teszi, hogy GB-másodpercben a napi használati kvóta beállításával korlátozhatja a platform használatát. A napi használati kvóta elérésekor a rendszer leállítja a függvényalkalmazást. Lehet, hogy újra engedélyezni az ugyanabban a környezetben, mint a napi költségkvóta létrehozó egy függvényalkalmazást a költségkeret kvóta elérése miatt leállt. Tekintse meg a [Azure Functions díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/functions/) számlázással kapcsolatos részletekért.   

## <a name="platform-features-tab"></a>Platform szolgáltatásai lapon

![Függvény app platform szolgáltatásai lapon.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

A függvényalkalmazások futnak, és karbantartása, az Azure App Service platformon. A függvényalkalmazásokat, az Azure core webes üzemeltetési platform funkcióját a legtöbb hozzáféréssel rendelkezik. A **platformfunkciók** lapon látható, ahol az App Service platform, amelyet használhat a saját függvényalkalmazások számos funkcióhoz férhet hozzá. 

> [!NOTE]
> Nem minden App Service-szolgáltatások a függvényalkalmazás fut a Használatalapú szolgáltatási csomag esetén érhetők el.

Ez a témakör a következő App Service-szolgáltatások az Azure Portalon, amelyek hasznosak a Functions összpontosít:

+ [Az App Service editor](#editor)
+ [nastavení aplikace](#settings) 
+ [Console](#console)
+ [Speciális eszközök (Kudu)](#kudu)
+ [Központi telepítési beállítások](#deployment)
+ [CORS](#cors)
+ [Hitelesítés](#auth)
+ [API-definíció](#swagger)

Az App Service-beállításokkal kapcsolatos további információkért lásd: [konfigurálása az Azure App Service beállítások](../app-service/web-sites-configure.md).

### <a name="editor"></a>Az App Service Editor

| | |
|-|-|
| ![A függvényalkalmazás az App Service editor.](./media/functions-how-to-use-azure-function-app-settings/function-app-appsvc-editor.png)  | Az App Service editor egy speciális a portálon szerkesztő, amely a JSON konfigurációs fájloknak és a jelenlegi funkcionalitása kódfájlok módosításához használhatja. Ezzel a művelettel elindítja egy külön böngészőlapon, egy alapszintű szerkesztővel. Ez lehetővé teszi, hogy a Git-tárház integrálása, futtatása és a kódok hibakeresése és módosíthatja a függvényalkalmazás-beállításokat. A szerkesztő a függvények az alapértelmezett függvény panelen képest bővített fejlesztési környezetet biztosít.    |

![Az App Service editor](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

### <a name="settings"></a>nastavení aplikace

| | |
|-|-|
| ![Függvényalkalmazás alkalmazás beállításai.](./media/functions-how-to-use-azure-function-app-settings/function-app-application-settings.png) | Az App Service-ben **Alkalmazásbeállítások** panel, ahol konfigurálhatja és kezelheti a keretrendszer-verziókat, távoli hibakeresés, Alkalmazásbeállítások és kapcsolati karakterláncokat. A függvényalkalmazás és az egyéb Azure- és külső szolgáltatások integrálása, ezeket a beállításokat Itt módosíthatja. Törli a beállítást, görgessen jobbra, és válassza a **X** ikonra a jobb végén a sor (az alábbi ábrán nem látható).

![Alkalmazásbeállítások konfigurálása](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-settings.png)

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

+ [Az Azure App Service szolgáltatás beállításainak konfigurálása](../app-service/web-sites-configure.md)
+ [Azure Functions – folyamatos üzembe helyezés](functions-continuous-deployment.md)



