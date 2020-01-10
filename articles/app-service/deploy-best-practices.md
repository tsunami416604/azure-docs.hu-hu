---
title: Ajánlott üzembe helyezési eljárások
description: Ismerje meg a Azure App Service üzembe helyezésének főbb mechanizmusait. Nyelvspecifikus javaslatok és egyéb kikötések keresése.
keywords: Azure app Service, webalkalmazás, üzembe helyezés, üzembe helyezés, folyamatok, Build
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: 14946a05f021a9b155fd9a9621f73bde980970fa
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750466"
---
# <a name="deployment-best-practices"></a>Ajánlott eljárások az üzembe helyezéshez

Minden fejlesztői csapat egyedi követelményekkel rendelkezik, amelyekkel megnehezítik a hatékony üzembe helyezési folyamatok bármilyen felhőalapú szolgáltatásban való megvalósítását. Ez a cikk bemutatja az üzembe helyezésének három fő összetevőjét App Service: üzembe helyezési források, folyamatok létrehozása és üzembe helyezési mechanizmusok. Ez a cikk az egyes nyelvi stackekhez kapcsolódó ajánlott eljárásokat és tippeket is tárgyalja.

## <a name="deployment-components"></a>Üzembe helyezési összetevők

### <a name="deployment-source"></a>Központi telepítés forrása

A központi telepítési forrás az alkalmazás kódjának helye. Az éles alkalmazások esetében a központi telepítési forrás általában egy verziókövetés szoftverrel (például [GitHub, BitBucket vagy Azure Repos](deploy-continuous-deployment.md)) üzemeltetett tárház. Fejlesztési és tesztelési helyzetekben a központi telepítési forrás lehet [egy projekt a helyi gépen](deploy-local-git.md). A App Service a [OneDrive és a Dropbox mappákat](deploy-content-sync.md) is támogatja központi telepítési forrásként. A Felhőbeli mappák megkönnyítik a App Service használatának megkezdését, ezért általában nem ajánlott a forrást nagyvállalati szintű üzemi alkalmazásokhoz használni. 

### <a name="build-pipeline"></a>Buildelési folyamat

Ha úgy dönt, hogy egy központi telepítési forrást választ, a következő lépés a létrehozási folyamat kiválasztása. A létrehozási folyamat beolvassa a forráskódot a központi telepítési forrásból, és végrehajt egy sor lépést (például a kód fordítását, a HTML és a JavaScriptek futtatását, a tesztek futtatását és a csomagolási összetevőket), hogy az alkalmazás futtatható állapotba kerüljön. A létrehozási folyamat által végrehajtott parancsok a nyelvi veremtől függenek. Ezek a műveletek egy olyan Build-kiszolgálón hajthatók végre, mint például az Azure-folyamatok, vagy helyileg is végrehajtva.

### <a name="deployment-mechanism"></a>Üzembe helyezési mechanizmus

Az üzembe helyezési mechanizmus az a művelet, amellyel a létrehozott alkalmazást a webalkalmazás */Home/site/wwwroot* könyvtárába helyezheti. A */wwwroot* könyvtár a webalkalmazás összes példánya által megosztott csatlakoztatott tárolási hely. Ha az üzembe helyezési mechanizmus ezen a címtáron helyezi el az alkalmazást, a példányok értesítést kapnak az új fájlok szinkronizálásáról. App Service a következő üzembe helyezési mechanizmusokat támogatja:

- Kudu-végpontok: a [kudu](https://github.com/projectkudu/kudu/wiki) a nyílt forráskódú fejlesztői hatékonyságnövelő eszköz, amely külön folyamatként fut a Windows app Serviceban, és a Linux app Service második tárolója. A kudu kezeli a folyamatos üzembe helyezéseket, és HTTP-végpontokat biztosít a központi telepítéshez, például zipdeploy.
- FTP és webtelepítés: a [webhely vagy a felhasználói hitelesítő adatok](deploy-configure-credentials.md)használatával [FTP-n keresztül](deploy-ftp.md) vagy webtelepítéssel tölthet fel fájlokat. Ezek a mechanizmusok nem haladnak át a kudu.  

Az olyan üzembe helyezési eszközök, mint például az Azure-folyamatok, a Jenkins és a szerkesztő beépülő modulok, a következő telepítési mechanizmusok valamelyikét használják.

## <a name="language-specific-considerations"></a>Nyelvspecifikus megfontolások

### <a name="java"></a>Java

Használja a kudu [zipdeploy/](deploy-zip.md) API-t a jar-alkalmazások üzembe helyezéséhez, valamint a wardeployjal és a War [-](deploy-zip.md#deploy-war-file) alkalmazásokhoz. Ha Jenkins-t használ, ezeket az API-kat közvetlenül az üzembe helyezési fázisban is használhatja. További információkért tekintse meg [ezt a cikket](../jenkins/execute-cli-jenkins-pipeline.md).

### <a name="node"></a>Csomópont

Alapértelmezés szerint a kudu végrehajtja a Node-alkalmazás (`npm install`) létrehozási lépéseit. Ha olyan Build szolgáltatást használ, mint például az Azure DevOps, akkor a kudu-Build szükségtelen. A kudu-Build letiltásához hozzon létre egy `SCM_DO_BUILD_DURING_DEPLOYMENT``false`értékkel rendelkező alkalmazás-beállítást.

### <a name="net"></a>.NET 

Alapértelmezés szerint a kudu végrehajtja a .NET-alkalmazás (`dotnet build`) létrehozási lépéseit. Ha olyan Build szolgáltatást használ, mint például az Azure DevOps, akkor a kudu-Build szükségtelen. A kudu-Build letiltásához hozzon létre egy `SCM_DO_BUILD_DURING_DEPLOYMENT``false`értékkel rendelkező alkalmazás-beállítást.

## <a name="other-deployment-considerations"></a>Egyéb üzembe helyezési megfontolások

### <a name="use-deployment-slots"></a>Üzembe helyezési pontok használata

Amikor csak lehetséges, használja az [üzembe helyezési](deploy-staging-slots.md) pontokat új üzemi buildek telepítésekor. Standard szintű App Service csomag vagy jobb használata esetén üzembe helyezheti az alkalmazást egy átmeneti környezetben, ellenőrizheti a módosításokat, és elvégezheti a füst tesztelését. Ha elkészült, az átmeneti és az üzemi tárolóhelyek is felcserélhetők. A swap művelet bemelegíti a szükséges munkavégző példányokat, hogy az megfeleljen az üzemi méretnek, így kiküszöbölve az állásidőt. 

### <a name="local-cache"></a>Helyi gyorsítótár

Azure App Service tartalom tárolása az Azure Storage-ban történik, és tartós módon van felkészülve, mint a tartalom megosztása. Egyes alkalmazások esetében azonban csak nagy teljesítményű, írásvédett, magas rendelkezésre állású tartalom-tárolóra lehet szükség. Ezek az alkalmazások kihasználhatják a [helyi gyorsítótár](overview-local-cache.md)használatát. A helyi gyorsítótár nem ajánlott olyan tartalomkezelési webhelyekhez, mint a WordPress.

A leállás megakadályozása érdekében mindig a helyi gyorsítótárat használja az [üzembe helyezési tárolóhelyekkel](deploy-staging-slots.md) együtt. A szolgáltatások együttes használatával kapcsolatos információkért tekintse meg [ezt a szakaszt](overview-local-cache.md#best-practices-for-using-app-service-local-cache) .

### <a name="high-cpu-or-memory"></a>Magas CPU vagy memória

Ha a App Service csomag a rendelkezésre álló CPU vagy memória több mint 90%-át használja, előfordulhat, hogy az alapul szolgáló virtuális gép nem tudja feldolgozni az üzemelő példányt. Ebben az esetben a példányok számának ideiglenes skálázásával végezze el az üzembe helyezést. Miután az üzembe helyezés befejeződött, visszaállíthatja a példányszámot az előző értékre.

Az ajánlott eljárásokkal kapcsolatos további információkért látogasson el a [app Service Diagnostics](https://docs.microsoft.com/azure/app-service/overview-diagnostics) webhelyre, ahol az erőforrásra vonatkozó, gyakorlatban alkalmazható ajánlott eljárásokat talál.

- Navigáljon a webalkalmazáshoz a [Azure Portal](https://portal.azure.com).
- Kattintson a bal oldali navigációs sávon található **problémák diagnosztizálásához és megoldásához** , amely megnyitja app Service diagnosztikát.
- Válassza az **ajánlott eljárások** Kezdőlap csempét.
- Kattintson az **ajánlott eljárások a rendelkezésre állás & teljesítmény** vagy **ajánlott eljárások az optimális konfigurációhoz** lehetőségre az alkalmazás aktuális állapotának megtekintéséhez az ajánlott eljárásokkal kapcsolatban.

Ezzel a hivatkozással közvetlenül is megnyithatja App Service diagnosztikát az erőforráshoz: `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot`.
