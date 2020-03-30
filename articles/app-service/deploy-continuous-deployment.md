---
title: Folyamatos üzembe helyezés konfigurálása
description: Megtudhatja, hogyan engedélyezheti a CI/CD-t az Azure App Service-be a GitHubról, a BitBucketből, az Azure-tárból vagy más tárból. Válassza ki az igényeinek megfelelő buildfolyamatot.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 08/23/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: d58eb333c930d2ffac4eb57340ea776338325181
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266038"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Folyamatos üzembe helyezés az Azure App Service szolgáltatásban

[Az Azure App Service](overview.md) lehetővé teszi a folyamatos üzembe helyezést a GitHubról, a BitBucket-ből és az [Azure Repos-tárházakból](https://azure.microsoft.com/services/devops/repos/) a legújabb frissítések leküldésével. Ez a cikk bemutatja, hogyan használhatja az Azure Portalon az alkalmazás folyamatos üzembe helyezéséhez a Kudu build szolgáltatáson vagy az [Azure Pipelines-on](https://azure.microsoft.com/services/devops/pipelines/)keresztül. 

A forrásvezérlő szolgáltatásokról további információt a [Tártár létrehozása (GitHub)], [Tártár létrehozása (BitBucket)]vagy [Új Git-tártár (Azure-tárterület létrehozása) című]témakörben talál.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Az Azure App Service engedélyezése 

Az Azure-adatpos-ok használatához győződjön meg arról, hogy az Azure DevOps-szervezete kapcsolódik az Azure-előfizetéséhez. További információ: [Azure DevOps Services-fiók beállítása webes alkalmazásokban való üzembe helyezéséhez.](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)

A Bitbucket vagy a GitHub esetén engedélyezze az Azure App Service számára, hogy csatlakozzon a tárházhoz. Csak egyszer kell engedélyeznie egy forrásvezérlő szolgáltatással. 

1. Az [Azure Portalon](https://portal.azure.com)keresse meg és válassza ki **az App Services**lehetőséget. 

   ![Alkalmazásszolgáltatások keresése.](media/app-service-continuous-deployment/search-for-app-services.png)

1. Válassza ki a telepíteni kívánt webalkalmazást.

   ![Válassza ki az alkalmazást.](media/app-service-continuous-deployment/select-your-app.png)
   
1. Az alkalmazás lapon válassza a bal oldali menü **Telepítési központ elemző elemi** elemét.
   
1. A **Deployment Center** lapon válassza a **GitHub** vagy **a Bitbucket**lehetőséget, majd az **Engedélyezés**lehetőséget. 
   
   ![Válassza a forrásvezérlő szolgáltatást, majd az Engedélyezés lehetőséget.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. Ha szükséges, jelentkezzen be a szolgáltatásba, és kövesse az engedélyezési utasításokat. 

## <a name="enable-continuous-deployment"></a>Folyamatos üzembe helyezés engedélyezése 

Miután engedélyezte a forrásvezérlő szolgáltatást, konfigurálja az alkalmazást a folyamatos telepítéshez a beépített [Kudu App Service build kiszolgálón](#option-1-use-the-app-service-build-service)vagy az [Azure Pipelines-on](#option-2-use-azure-pipelines)keresztül. 

### <a name="option-1-use-the-app-service-build-service"></a>1. lehetőség: Az App Service buildszolgáltatás használata

A beépített Kudu App Service buildkiszolgáló segítségével folyamatosan telepítheti a GitHubról, a Bitbucket-ből vagy az Azure-repos-ról. 

1. Az [Azure Portalon](https://portal.azure.com)keresse meg és válassza ki az **App Services**lehetőséget, majd válassza ki a telepíteni kívánt webalkalmazást. 
   
1. Az alkalmazás lapon válassza a bal oldali menü **Telepítési központ elemző elemi** elemét.
   
1. Válassza ki a hivatalos forrásvezérlő szolgáltatót a **Központi telepítési központ** lapon, és válassza a Folytatás **gombot.** A GitHub vagy a Bitbucket esetében a **Fiók módosítása** lehetőséget is választhatja az engedélyezett fiók módosításához. 
   
   > [!NOTE]
   > Az Azure-adatpos-ok használatához győződjön meg arról, hogy az Azure DevOps Services-szervezete kapcsolódik az Azure-előfizetéséhez. További információ: [Azure DevOps Services-fiók beállítása webes alkalmazásokban való üzembe helyezéséhez.](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)
   
1. A GitHub vagy az Azure-repos esetén a **Build-szolgáltató** lapon válassza az **App Service buildszolgáltatás**lehetőséget, majd a **Folytatás**lehetőséget. A Bitbucket mindig az App Service build szolgáltatást használja.
   
   ![Válassza az App Service build szolgáltatás, majd válassza a Folytatás lehetőséget.](media/app-service-continuous-deployment/choose-kudu.png)
   
1. A **Konfigurálás** lapon:
   
   - A GitHub esetében legördülő menüben válassza ki a **szervezet,** **a tárház**és **a részleg** folyamatos üzembe helyezését.
     
     > [!NOTE]
     > Ha nem lát adattárakat, előfordulhat, hogy engedélyeznie kell az Azure App Service-t a GitHubon. Tallózással keresse meg a GitHub-tárházat, és nyissa meg a **Beállítások** > **alkalmazások** > **engedélyezett OAuth alkalmazások at.** Válassza **az Azure App Service**lehetőséget, majd a **Grant**lehetőséget. A szervezeti adattárak, meg kell tulajdonosa a szervezet, hogy az engedélyeket.
     
   - A Bitbucket, válassza ki a Bitbucket **Team**, **Repository**és **ág** szeretné telepíteni folyamatosan.
     
   - Az Azure-adatpos, válassza ki az **Azure DevOps Szervezet,** **Projekt,** **Tárház**és **ág** folyamatosan telepíteni kívánt.
     
     > [!NOTE]
     > Ha az Azure DevOps-szervezet nem szerepel a listában, győződjön meg arról, hogy kapcsolódik az Azure-előfizetéshez. További információ: [Azure DevOps Services-fiók beállítása, hogy egy webalkalmazásra telepíthesse..](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)
     
1. Válassza a **Folytatás** elemet.
   
   ![Töltse ki a tárház adatait, majd válassza a Folytatás lehetőséget.](media/app-service-continuous-deployment/configure-kudu.png)
   
1. A buildszolgáltató konfigurálása után tekintse át az **Összegzés** lap beállításait, és válassza a **Befejezés gombot.**
   
   A kiválasztott tárházban és ágban lévő új véglegesítések mostantól folyamatosan üzembe kerülnek az App Service-alkalmazásban. Nyomon követheti a véglegesítések és a központi telepítések a **Deployment Center** lapon.
   
   ![Véglegesítések és telepítések nyomon követése a Deployment Centerben](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-use-azure-pipelines"></a>2. lehetőség: Azure-folyamatok használata 

Ha a fiók rendelkezik a szükséges engedélyekkel, beállíthatja az Azure-folyamatok folyamatos üzembe helyezéséhez a GitHub vagy az Azure Repos adattárak. Az Azure Pipelines-on keresztüli üzembe helyezésről a [Webalkalmazás üzembe helyezése az Azure App Services szolgáltatásban](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)című témakörben talál további információt.

Az Azure App Service számára folyamatos kézbesítési Azure-folyamatok létrehozása az Azure DevOps-szervezetben: 

- Az Azure-fióknak engedéllyel kell rendelkeznie az Azure Active Directoryba való íráshoz és egy szolgáltatás létrehozásához. 
  
- Az Azure-fióknak tulajdonosi szerepkörsel kell **rendelkeznie** az Azure-előfizetésben.

- Rendszergazdának kell lennie a használni kívánt Azure DevOps-projektben.

Az Azure-folyamatok konfigurálása (előzetes verzió):

1. Az [Azure Portalon](https://portal.azure.com)keresse meg és válassza ki az **App Services**lehetőséget, majd válassza ki a telepíteni kívánt webalkalmazást. 
   
1. Az alkalmazás lapon válassza a bal oldali menü **Telepítési központ elemző elemi** elemét.
   
1. A **Build provider (Build provider)** lapon válassza az **Azure-folyamatok (előzetes verzió)** lehetőséget, majd a **Continue (Folytatás) lehetőséget.** 
   
1. A **Beállítás** lapon, a **Kód** szakaszban:
   
   - A GitHub esetében legördülő menüben válassza ki a **szervezet,** **a tárház**és **a részleg** folyamatos üzembe helyezését.
     
     > [!NOTE]
     > Ha nem lát adattárakat, előfordulhat, hogy engedélyeznie kell az Azure App Service-t a GitHubon. Tallózással keresse meg a GitHub-tárházat, és nyissa meg a **Beállítások** > **alkalmazások** > **engedélyezett OAuth alkalmazások at.** Válassza **az Azure App Service**lehetőséget, majd a **Grant**lehetőséget. A szervezeti adattárak, meg kell tulajdonosa a szervezet, hogy az engedélyeket.
     
   - Az Azure-adatpos, válassza ki az **Azure DevOps Szervezet,** **Project**, **Repository**és **ág** szeretné telepíteni folyamatosan, vagy konfigurálja az új Azure DevOps-szervezet.
     
     > [!NOTE]
     > Ha a meglévő Azure DevOps-szervezet nem szerepel a listában, előfordulhat, hogy össze kell kapcsolnia az Azure-előfizetésével. További információt [a CD-kiadási folyamat megadása](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd)című témakörben talál.
     
1. Válassza a **Folytatás** elemet.
   
1. Az Azure-adatposok esetében a **Build** szakaszban adja meg azt a nyelvi keretrendszert, amelyet az Azure Pipeline-ok a buildelési feladatok futtatásához kell használniuk, majd válassza a **Folytatás lehetőséget.**
   
1. A **Teszt** lapon válassza ki, hogy engedélyezi-e a betöltési teszteket, majd válassza a **Continue (Folytatás) lehetőséget.**
   
1. Az App Service-csomag [tarifacsomagtól](https://azure.microsoft.com/pricing/details/app-service/plans/)függően előfordulhat, hogy megjelenik egy **üzembe helyezés az átmeneti** lap. Adja meg, hogy [engedélyezi-e a telepítési helyeket,](deploy-staging-slots.md)majd válassza **a Continue (Folytatás)** lehetőséget.
   
   > [!NOTE]
   > Az Azure Pipelines nem teszi lehetővé a folyamatos kézbesítést az éles tárolóhelyre. Ez a korlátozás megakadályozza a véletlen üzembe helyezést az éles környezetben. Állítsa be a folyamatos kézbesítést egy átmeneti tárolóhelyre, ellenőrizze az ott végrehajtott módosításokat, majd cserélje ki a tárolóhelyeket, amikor készen áll.
   
1. A buildszolgáltató konfigurálása után tekintse át az **Összegzés** lap beállításait, és válassza a **Befejezés gombot.**
   
   A kiválasztott tárházban és ágban lévő új véglegesítések mostantól folyamatosan üzembe kerülnek az App Service-alkalmazásban. Nyomon követheti a véglegesítések és a központi telepítések a **Deployment Center** lapon.
   
   ![Véglegesítések és telepítések nyomon követése a Deployment Centerben](media/app-service-continuous-deployment/github-finished.png)

## <a name="disable-continuous-deployment"></a>Folyamatos üzembe helyezés letiltása

A folyamatos telepítés letiltásához válassza a **Kapcsolat bontása** lehetőséget az alkalmazás **Központi telepítésével való központi telepítési központ** lapjának tetején.

![Folyamatos üzembe helyezés letiltása](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="use-unsupported-repos"></a>Nem támogatott repók használata

Windows-alkalmazások esetén manuálisan konfigurálhatja a folyamatos üzembe helyezést egy felhőalapú Git- vagy Mercurial-tárházból, amelyet a portál közvetlenül nem támogat, például a [GitLab.](https://gitlab.com/) Ehhez válassza a Külső mezőt a **Központi telepítési központ** lapon. További információt a Folyamatos telepítés beállítása manuális lépések ben című [témakörben talál.](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)

## <a name="additional-resources"></a>További források

* [A folyamatos telepítéssel kapcsolatos gyakori problémák vizsgálata](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Azure PowerShell használatával](/powershell/azureps-cmdlets-docs)
* [Git dokumentáció](https://git-scm.com/documentation)
* [A Kudu projekt](https://github.com/projectkudu/kudu/wiki)

[Adattár létrehozása (GitHub)]: https://help.github.com/articles/create-a-repo
[Adattár létrehozása (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Új Git-tártár létrehozása (Azure-tárpók)]: /azure/devops/repos/git/creatingrepo
