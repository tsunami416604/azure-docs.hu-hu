---
title: Ajánlott eljárások az üzembe helyezéshez – Azure App Service | Microsoft Docs
description: További információ a Azure App Service üzembe helyezésének főbb összetevőiről.
keywords: Azure app Service, webalkalmazás, üzembe helyezés, üzembe helyezés, folyamatok, Build
services: app-service
documentationcenter: ''
author: jasonfreeberg
manager: ''
editor: ''
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.custom: ''
ms.openlocfilehash: 121ea4b7e29510ef86b61350ed97ffca5d133d56
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199494"
---
# <a name="deployment-best-practices"></a>Ajánlott eljárások az üzembe helyezéshez

Minden fejlesztői csapat egyedi követelményekkel rendelkezik, amelyekkel megnehezítik a hatékony üzembe helyezési folyamatok bármilyen felhőalapú szolgáltatásban való megvalósítását. Ez a cikk bemutatja az üzembe helyezésének három fő összetevőjét App Service: üzembe helyezési források, folyamatok létrehozása és üzembe helyezési mechanizmusok. Ez a cikk az egyes nyelvi stackekhez kapcsolódó ajánlott eljárásokat és tippeket is tárgyalja.

## <a name="deployment-components"></a>Üzembe helyezési összetevők

### <a name="deployment-source"></a>Központi telepítés forrása

A központi telepítési forrás az alkalmazás kódjának helye. Az éles alkalmazások esetében a központi telepítési forrás általában egy verziókövetés szoftverrel (például [GitHub, BitBucket vagy Azure Repos](deploy-continuous-deployment.md)) üzemeltetett tárház. Fejlesztési és tesztelési helyzetekben a központi telepítési forrás lehet [egy projekt a helyi gépen](deploy-local-git.md). A App Service a [OneDrive és a Dropbox mappákat](deploy-content-sync.md) is támogatja központi telepítési forrásként. A Felhőbeli mappák megkönnyítik a App Service használatának megkezdését, ezért általában nem ajánlott a forrást nagyvállalati szintű üzemi alkalmazásokhoz használni. 

### <a name="build-pipeline"></a>Folyamat létrehozása

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
