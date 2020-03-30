---
title: Ajánlott üzembe helyezési eljárások
description: Ismerje meg az Azure App Service üzembe helyezésének kulcsfontosságú mechanizmusait. Keresse meg a nyelv-specifikus ajánlások és egyéb kifogások.
keywords: Azure app szolgáltatás, webapp, üzembe helyezés, üzembe helyezés, folyamatok, build
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: 14946a05f021a9b155fd9a9621f73bde980970fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750466"
---
# <a name="deployment-best-practices"></a>Gyakorlati tanácsok a telepítéshez

Minden fejlesztőcsapat egyedi követelményekkel rendelkezik, amelyek megnehezítik a hatékony üzembe helyezési folyamat megvalósítását bármely felhőszolgáltatáson. Ez a cikk bemutatja az App Service üzembe helyezésének három fő összetevőjét: üzembe helyezési források, folyamatok létrehozása és telepítési mechanizmusok. Ez a cikk néhány gyakorlati tanácsra és tippekre is kiterjed az adott nyelvi halmokkal.

## <a name="deployment-components"></a>Telepítési összetevők

### <a name="deployment-source"></a>Telepítési forrás

A központi telepítési forrás az alkalmazáskód helye. Éles alkalmazások esetében a központi telepítési forrás általában egy adattár, amelyet a verziókezelő szoftverek, például [a GitHub, a BitBucket vagy az Azure Repos](deploy-continuous-deployment.md)üzemeltet. Fejlesztési és tesztelési forgatókönyvek esetén a központi telepítési forrás lehet [egy projekt a helyi számítógépen.](deploy-local-git.md) Az App Service a OneDrive és a [Dropbox mappákat](deploy-content-sync.md) is támogatja telepítési forrásként. Bár a felhőalapú mappák megkönnyíthetiaz App Service használatának megkezdését, általában nem ajánlott ezt a forrást vállalati szintű éles alkalmazásokhoz használni. 

### <a name="build-pipeline"></a>Buildelési folyamat

Miután úgy döntött, egy központi telepítési forrás, a következő lépés az, hogy válasszon egy buildfolyamat. A buildfolyamat beolvassa a forráskódot a központi telepítési forrásból, és egy sor lépést hajt végre (például kód fordítása, a HTML és a JavaScript minifikálása, a tesztek futtatása és a csomagolási összetevők) az alkalmazás futtatható állapotban történő leolvasásához. A buildfolyamat által végrehajtott adott parancsok a nyelvi veremtől függenek. Ezek a műveletek végrehajthatók egy buildelőkiszolgálón, például az Azure-folyamatok, vagy helyileg végrehajtható.

### <a name="deployment-mechanism"></a>Telepítési mechanizmus

Az üzembe helyezési mechanizmus az a művelet, amely a beépített alkalmazást a webalkalmazás */home/site/wwwroot* könyvtárába helyezi. A */wwwroot* könyvtár egy csatlakoztatott tárolóhely, amelyet a webalkalmazás összes példánya megoszt. Amikor a központi telepítési mechanizmus az alkalmazást ebbe a könyvtárba helyezi, a példányok értesítést kapnak az új fájlok szinkronizálásáról. Az App Service a következő telepítési mechanizmusokat támogatja:

- Kudu végpontok: [Kudu](https://github.com/projectkudu/kudu/wiki) a nyílt forráskódú fejlesztői hatékonysági eszköz, amely fut, mint egy külön folyamat a Windows App Service, és a második tároló a Linux App Service. Kudu kezeli a folyamatos központi telepítések és HTTP-végpontok központi telepítés, például a zipdeploy.
- FTP és WebDeploy: A [hely vagy a felhasználói hitelesítő adatok](deploy-configure-credentials.md)használatával [ftp-n](deploy-ftp.md) vagy webtelepítésen keresztül tölthet fel fájlokat. Ezek a mechanizmusok nem megy keresztül Kudu.  

A központi telepítési eszközök, például az Azure Pipelines, a Jenkins és a szerkesztő bővítmények ezen üzembe helyezési mechanizmusok egyikét használják.

## <a name="language-specific-considerations"></a>Nyelvspecifikus szempontok

### <a name="java"></a>Java

Használja a Kudu [zipdeploy/](deploy-zip.md) API jar alkalmazások üzembe helyezéséhez, és [wardeploy/](deploy-zip.md#deploy-war-file) WAR alkalmazásokhoz. Jenkins használata esetén ezeket az API-kat közvetlenül a központi telepítési fázisban használhatja. További információt [ebben a cikkben](../jenkins/execute-cli-jenkins-pipeline.md)talál.

### <a name="node"></a>Csomópont

Alapértelmezés szerint a Kudu végrehajtja a csomópontalkalmazás`npm install`( buildlépéseit. Ha egy buildszolgáltatás, például az Azure DevOps, majd a Kudu build szükségtelen. A Kudu build letiltásához hozzon létre egy alkalmazásbeállítást, `SCM_DO_BUILD_DURING_DEPLOYMENT`amelynek `false`értéke .

### <a name="net"></a>.NET 

Alapértelmezés szerint a Kudu végrehajtja a .Net`dotnet build`alkalmazás ( . Ha egy buildszolgáltatás, például az Azure DevOps, majd a Kudu build szükségtelen. A Kudu build letiltásához hozzon létre egy alkalmazásbeállítást, `SCM_DO_BUILD_DURING_DEPLOYMENT`amelynek `false`értéke .

## <a name="other-deployment-considerations"></a>Egyéb telepítési szempontok

### <a name="use-deployment-slots"></a>Központi telepítési tárolóhelyek használata

Amikor csak lehetséges, [használjon központi telepítési helyeket](deploy-staging-slots.md) egy új éles build üzembe helyezésekor. Standard app service-csomag vagy annál jobb használata esetén üzembe helyezheti az alkalmazást egy átmeneti környezetben, ellenőrizheti a módosításokat, és füstteszteket végezhet. Ha készen áll, kicserélheti az átmeneti és a termelési tárolóhelyeket. A csereművelet felmelegíti a szükséges feldolgozópéldányokat, hogy megfeleljen a termelési skálának, így kiküszöbölve az állásidőt. 

### <a name="local-cache"></a>Helyi gyorsítótár

Az Azure App Service-tartalom az Azure Storage-ban tárolódik, és tartós módon, tartalommegosztásként jelenik meg. Egyes alkalmazásoknak azonban csak egy nagy teljesítményű, csak olvasható tartalomtárra van szükségük, amelyet magas rendelkezésre állással futtathatnak. Ezek az alkalmazások a [helyi gyorsítótár](overview-local-cache.md)használatának előnyeit élvezhetik. Helyi cache nem ajánlott tartalomkezelő oldalak, mint a WordPress.

Mindig használja a helyi gyorsítótárat a [központi telepítési helyekkel](deploy-staging-slots.md) együtt az állásidő elkerülése érdekében. A szolgáltatások együttes használatáról ebben a [szakaszban](overview-local-cache.md#best-practices-for-using-app-service-local-cache) talál tájékoztatást.

### <a name="high-cpu-or-memory"></a>Magas CPU vagy memória

Ha az App Service-csomag a rendelkezésre álló processzor vagy memória több mint 90%-át használja, előfordulhat, hogy az alapul szolgáló virtuális gép nem tudja feldolgozni a központi telepítést. Ha ez történik, ideiglenesen felskálázása a példányok száma a központi telepítés végrehajtásához. Miután a központi telepítés befejeződött, visszaadhatja a példányok számát az előző értékre.

Az ajánlott eljárásokról az [App Service Diagnosztika](https://docs.microsoft.com/azure/app-service/overview-diagnostics) webhelyre talál további információt az erőforrásra vonatkozó, bevált gyakorlati tanácsokért.

- Nyissa meg a webalkalmazást az [Azure Portalon.](https://portal.azure.com)
- Kattintson **a Problémák diagnosztizálása és megoldása** a bal oldali navigációs, amely megnyitja App Service Diagnosztika.
- Válassza az **Ajánlott eljárások** kezdőlapcsempét.
- Kattintson **a gyakorlati tanácsok a rendelkezésre állás & teljesítményéhez,** vagy az optimális **konfigurációra vonatkozó gyakorlati tanácsok ra kattintva** megtekintheti az alkalmazás aktuális állapotát az ajánlott eljárások tekintetében.

Ezen a hivatkozáson keresztül közvetlenül is megnyithatja `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot`az App Service Diagnosztikát az erőforráshoz: .
