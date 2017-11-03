---
title: "Az Azure-függvény Alkalmazásbeállítások konfigurálása |} Microsoft Docs"
description: "Útmutató: Azure funkció beállításainak konfigurálása."
services: 
documentationcenter: .net
author: rachelappel
manager: cfowler
editor: 
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 04/23/2017
ms.author: glenga
ms.openlocfilehash: 3229fe0b5aacbbc716039a4a6abfd235dbc4c4f7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-a-function-app-in-the-azure-portal"></a>Egy függvény alkalmazást az Azure portálon kezelése 

Az Azure Functions egy függvény alkalmazást a végrehajtási környezet biztosít a az egyes funkcióihoz. Függvény app viselkedések egy adott funkció alkalmazás által futtatott összes funkciójának vonatkozik. Ez a témakör ismerteti, hogyan konfigurálja és kezelje a függvény alkalmazások az Azure portálon.

Első lépésként nyissa meg a [Azure-portálon](http://portal.azure.com) és az Azure-fiókjába történő bejelentkezéshez. A portál tetején a keresősávba írja be a függvényalkalmazás nevét, majd válassza ki a listáról. Miután kiválasztotta a függvény alkalmazás, a következő lap jelenik meg:

![Az Azure portálon függvény app – áttekintés](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

## <a name="manage-app-service-settings"></a>Függvény app beállítások lap

![Függvény-alkalmazás – áttekintés az Azure portálon.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

A **beállítások** lap, ahol frissítheti a függvény alkalmazás által használt funkciók futásidejű verzió. Akkor is, amelyeken kezelheti az állomások kulcsait, HTTP-hozzáférés korlátozása a függvény alkalmazás által futtatott összes funkciójának használatával.

Funkciók fogyasztás futtató és az App Service csomagokban üzemeltető támogatja. További információkért lásd: [válassza ki a megfelelő service-csomag az Azure Functions](functions-scale.md). A felhasználási tervben jobb kiszámítható funkciók lehetővé teszi a platform-használatát korlátozása úgy, hogy a napi használat kvóta GB-másodperc. A napi memóriahasználati kvóta elérésekor, a függvény alkalmazás leállt. Egy függvény alkalmazást a költségkeret kvóta elérése miatt leállt az ugyanabban a környezetben, mint a kvóta költségeik napi létrehozó újra engedélyezni lehet. Tekintse meg a [árképzést ismertető oldalra Azure Functions](http://azure.microsoft.com/pricing/details/functions/) számlázással kapcsolatos részletekért.   

## <a name="platform-features-tab"></a>Platform szolgáltatások lap

![Függvény alkalmazás platform szolgáltatások lapon.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

Függvény alkalmazások futnak, és tartható fenn, az Azure App Service platformon. A függvény alkalmazásokhoz, Azure alapvető webszolgáltatási platform részeit, a legtöbb hozzáféréssel rendelkeznek. A **Platform funkciói** lapon látható, ahol az App Service platform, amely a függvény alkalmazásokban használható sok funkcióhoz férhet hozzá. 

> [!NOTE]
> Nem minden App Service-szolgáltatások érhetők el, ha egy függvény alkalmazás fut a felhasználásra vonatkozó üzemeltetési terv.

Ez a témakör hátralévő része a következő App Service-szolgáltatások az Azure portálon, amelyek hasznosak a funkciók koncentrál:

+ [App Service-szerkesztő](#editor)
+ [Alkalmazásbeállítások](#settings) 
+ [Console](#console)
+ [Speciális eszközöket (a Kudu)](#kudu)
+ [Telepítési lehetőségek](#deployment)
+ [CORS](#cors)
+ [Hitelesítés](#auth)
+ [API-definíció](#swagger)

App Service-beállításokkal kapcsolatos további információkért lásd: [Azure App Service beállításainak konfigurálása](../app-service/web-sites-configure.md).

### <a name="editor"></a>App Service-szerkesztő

| | |
|-|-|
| ![Függvény alkalmazás az App Service-szerkesztőt.](./media/functions-how-to-use-azure-function-app-settings/function-app-appsvc-editor.png)  | Az App Service-szerkesztő egy speciális-portal szerkesztő JSON-konfigurációs fájlok és kód fájlok egyaránt használható. Ez a beállítás egy külön böngészőlapon alapvető szerkesztővel indít. Ez lehetővé teszi a Git-tárház integrálása, futtatása és kód hibakereséséhez és funkció beállításainak módosításához. A szerkesztő az alapértelmezett függvény panelen összehasonlítja a funkciók bővített fejlesztési környezetet biztosít.    |

![Az App Service-szerkesztő](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

### <a name="settings"></a>Alkalmazásbeállítások

| | |
|-|-|
| ![Alkalmazás függvény Alkalmazásbeállítások.](./media/functions-how-to-use-azure-function-app-settings/function-app-application-settings.png) | Az App Service **Alkalmazásbeállítások** panel, ahol konfigurálhatja és kezelheti a keretrendszer-verziók, távoli hibakeresés, Alkalmazásbeállítások és kapcsolati karakterláncok. A függvény alkalmazás integrációjához át az egyéb Azure és a harmadik féltől származó szolgáltatással, módosíthatja itt ezeket a beállításokat. |

![Az Alkalmazásbeállítások konfigurálása](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-settings.png)

### <a name="console"></a>Konzol

| | |
|-|-|
| ![Függvény app konzol az Azure-portálon](./media/functions-how-to-use-azure-function-app-settings/function-app-console.png) | A portál konzol az ideális fejlesztői eszközét esetén interakciót folytatni a függvény app a parancssorból szeretné végrehajtani. Általános jellegű parancsok közé tartozik a címtár és a fájl létrehozásának és navigációs, valamint parancsfájlok végrehajtása. |

![Függvény app konzol](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

### <a name="kudu"></a>Speciális eszközöket (a Kudu)

| | |
|-|-|
| ![Függvényalkalmazásnak Kudu az Azure-portálon](./media/functions-how-to-use-azure-function-app-settings/function-app-advanced-tools.png) | A speciális eszközök az App Service (más néven a Kudu) adja meg a függvény alkalmazás Speciális felügyeleti funkcióhoz hozzáférése. Kudu kezelheti az Rendszerinformáció, Alkalmazásbeállítások, környezeti változókat, helyhez kiterjesztések, HTTP-fejlécek és kiszolgáló-változók. Is **Kudu** az SCM végpont függvény alkalmazás, például tallózással`https://<myfunctionapp>.scm.azurewebsites.net/` |

![A Kudu konfigurálása](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)


### <a name="a-namedeploymentdeployment-options"></a><a name="deployment">Telepítési lehetőségek

| | |
|-|-|
| ![Függvény alkalmazás központi telepítési beállítások az Azure-portálon](./media/functions-how-to-use-azure-function-app-settings/function-app-deployment-source.png) | Funkciók lehetővé teszi, hogy a helyi számítógépen a funkciókódot fejleszthet. A helyi függvény alkalmazásprojektet majd feltöltheti az Azure-bA. Hagyományos FTP feltöltés mellett a funkciók lehetővé teszi a népszerű folyamatos integrációt megoldások, például a Githubon, VSTS, Dropbox, Bitbucket és mások segítségével függvény alkalmazás telepítése. További információkért lásd: [folyamatos üzembe helyezés az Azure Functions](functions-continuous-deployment.md). Töltse fel az FTP- vagy helyi Git segítségével manuálisan, le is kell [az üzembe helyezési hitelesítő adatok konfigurálása](functions-continuous-deployment.md#credentials). |


### <a name="cors"></a>A CORS

| | |
|-|-|
| ![Függvény alkalmazás CORS az Azure-portálon](./media/functions-how-to-use-azure-function-app-settings/function-app-cors.png) | A szolgáltatások rosszindulatú kód végrehajtása érdekében az App Service külső forrásból függvény alkalmazásaira hívások blokkolja. Funkciók támogatja az eltérő eredetű erőforrások megosztása (CORS) lehetővé teszi, hogy adja meg egy engedélyezett eredetet, amelyből a funkciók távoli kérelmek fogadhat "engedélyezett".  |

![Függvény alkalmazás konfigurálása CORS](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

### <a name="auth"></a>Hitelesítés

| | |
|-|-|
| ![Függvény app hitelesítés az Azure-portálon](./media/functions-how-to-use-azure-function-app-settings/function-app-authentication.png) | Ha funkciók egy HTTP-eseményindítóval, megkövetelheti hívások először hitelesítését. App Service támogatja az Azure Active Directory-hitelesítés és be kell jelentkezniük a közösségi szolgáltatók, köztük a Facebook, a Microsoft és a Twitter. További részletek az adott hitelesítési szolgáltatókat konfigurál: [Azure App Service hitelesítés áttekintése](../app-service/app-service-authentication-overview.md). |

![Egy függvény alkalmazások hitelesítésének konfigurálása](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)


### <a name="swagger"></a>API-definíció

| | |
|-|-|
| ![Függvény app API definition az Azure portálon swagger](./media/functions-how-to-use-azure-function-app-settings/function-app-api-definition.png) | Funkciók támogatja az ügyfelek a HTTP-eseményindítókkal aktivált függvényeket könnyebben felhasználásához Swagger. A Swagger API-definíciók létrehozásával kapcsolatos további információkért látogasson el a [Bevezetés az API Apps és az Azure-ban Swagger](../app-service/app-service-web-tutorial-rest-api.md). Funkciók proxyk segítségével több függvények egyetlen API felület meghatározása. További információkért lásd: [használata az Azure Functions proxyk](functions-proxies.md). |

![Függvény App API konfigurálása](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-apidef.png)



## <a name="next-steps"></a>Következő lépések

+ [Az Azure App Service-beállítások konfigurálása](../app-service/web-sites-configure.md)
+ [Azure Functions – folyamatos üzembe helyezés](functions-continuous-deployment.md)



