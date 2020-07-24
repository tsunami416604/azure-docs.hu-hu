---
title: Folyamatos üzembe helyezés konfigurálása
description: Megtudhatja, hogyan engedélyezheti a CI/CD-t a GitHub, a BitBucket, az Azure Repos vagy más repók Azure App Service. Válassza ki az igényeinek megfelelő Build-folyamatot.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/20/2020
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 51b6be8b4deffd81da6c0b714bc6afeff4b06ab2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87073951"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Folyamatos üzembe helyezés Azure App Service

[Azure app Service](overview.md) lehetővé teszi a GitHub, a BitBucket és az [Azure Repos](https://azure.microsoft.com/services/devops/repos/) -Tárházak folyamatos üzembe helyezését a legújabb frissítések behúzásával. Ez a cikk bemutatja, hogyan használható a Azure Portal az alkalmazás folyamatos üzembe helyezéséhez a kudu Build Service vagy az [Azure-folyamatok](https://azure.microsoft.com/services/devops/pipelines/)használatával. 

A forrás-ellenőrzési szolgáltatásokkal kapcsolatos további információkért lásd: adattár [létrehozása (GitHub)], tárház [létrehozása (BitBucket)], vagy [új git-Tárház létrehozása (Azure-]repók).

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Engedélyezés Azure App Service 

Az Azure Repos használatához ellenőrizze, hogy az Azure DevOps-szervezet kapcsolódik-e az Azure-előfizetéséhez. További információkért lásd: [Azure DevOps Services-fiók beállítása, hogy az üzembe helyezhető egy webalkalmazásban](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops).

A bitbucket vagy a GitHub esetében engedélyezze Azure App Service az adattárhoz való kapcsolódást. Csak egyszer kell engedélyeznie a verziókövetés szolgáltatást. 

1. A [Azure Portal](https://portal.azure.com)keresse meg **app Services** és válassza a elemet.

   ![Keressen rá az App Services kifejezésre.](media/app-service-continuous-deployment/search-for-app-services.png)

1. Válassza ki a telepíteni kívánt App Service.

   ![Válassza ki az alkalmazást.](media/app-service-continuous-deployment/select-your-app.png)
   
1. Az alkalmazás lapon a bal oldali menüben válassza a **központi telepítési központ** elemet.
   
1. A **központi telepítési központ** lapon válassza a **GitHub** vagy a **bitbucket**lehetőséget, majd válassza az **Engedélyezés**lehetőséget. 
   
   ![Válassza a verziókövetés szolgáltatás lehetőséget, majd válassza az Engedélyezés lehetőséget.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. Ha szükséges, jelentkezzen be a szolgáltatásba, és kövesse az engedélyezési utasításokat. 

## <a name="enable-continuous-deployment"></a>Folyamatos üzembe helyezés engedélyezése 

Miután engedélyezte a verziókövetés szolgáltatást, konfigurálja az alkalmazást folyamatos üzembe helyezéshez a beépített [Kudu app Service](#option-1-kudu-app-service) a Build-kiszolgálót vagy az [Azure-folyamatokat](#option-2-azure-pipelines). 

### <a name="option-1-kudu-app-service"></a>1. lehetőség: kudu App Service

A beépített kudu App Service felépíteni a kiszolgálót a GitHub, a bitbucket vagy az Azure Repos szolgáltatásból való folyamatos üzembe helyezéshez. 

1. A [Azure Portal](https://portal.azure.com)keresse meg a **app Services**, majd válassza ki a telepíteni kívánt app Service. 
   
1. Az alkalmazás lapon a bal oldali menüben válassza a **központi telepítési központ** elemet.
   
1. A **központi telepítési központ** lapon válassza ki az Ön által jóváhagyott forrásoldali vezérlő szolgáltatót, majd válassza a **Folytatás**lehetőséget. A GitHub vagy a bitbucket esetében a **fiók módosítása** lehetőségre kattintva módosíthatja a hitelesítő fiókot. 
   
   > [!NOTE]
   > Az Azure Repos használatához ellenőrizze, hogy az Azure DevOps Services-szervezet kapcsolódik-e az Azure-előfizetéséhez. További információkért lásd: [Azure DevOps Services-fiók beállítása, hogy az üzembe helyezhető egy webalkalmazásban](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops).
   
1. A GitHub vagy az Azure Repos esetében a **szolgáltató létrehozása** lapon válassza a **app Service Build szolgáltatás**lehetőséget, majd kattintson a **Folytatás**gombra. A bitbucket mindig a App Service Build szolgáltatást használja.
   
   ![Válassza ki App Service Build szolgáltatást, majd válassza a Folytatás lehetőséget.](media/app-service-continuous-deployment/choose-kudu.png)
   
1. A **configure (Konfigurálás** ) lapon:
   
   - A GitHubnál válassza a legördülő listát, és válassza ki azt a **szervezetet**, **tárat**és **ágat** , amelyet folyamatosan szeretne üzembe helyezni.
     
     > [!NOTE]
     > Ha nem lát adattárakat, lehetséges, hogy engedélyeznie kell Azure App Service a GitHubon. Keresse meg a GitHub-tárházat, és lépjen a **Beállítások**  >  **alkalmazások**által  >  **jóváhagyott OAuth-alkalmazásokhoz**. Válassza a **Azure app Service**lehetőséget, majd válassza a **támogatás**lehetőséget. A szervezeti adattárak esetében a szervezet tulajdonosának kell lennie az engedélyek megadásához.
     
   - A bitbucket területen válassza ki azt a bitbucket- **csoportot**, **tárat**és **ágat** , amelyet folyamatosan telepíteni szeretne.
     
   - Az Azure Repos esetében válassza ki azt az **Azure DevOps-szervezetet**, **projektet**, **tárházat**és **ágat** , amelyet folyamatosan telepíteni szeretne.
     
     > [!NOTE]
     > Ha az Azure DevOps-szervezet nem szerepel a felsorolásban, győződjön meg arról, hogy társítva van az Azure-előfizetéséhez. További információkért lásd: [Azure DevOps Services-fiók beállítása, hogy üzembe helyezhető egy webalkalmazásban](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops).
     
1. Válassza a **Folytatás** elemet.
   
   ![Töltse ki a tárház adatait, majd válassza a Folytatás lehetőséget.](media/app-service-continuous-deployment/configure-kudu.png)
   
1. A Build szolgáltató konfigurálása után tekintse át a beállításokat az **Összefoglalás** lapon, majd válassza a **Befejezés**lehetőséget.
   
1. Új véglegesíti a kiválasztott tárházban és ág-ban, most már folyamatosan üzembe helyezi a App Service alkalmazást. A **központi telepítési központ** lapon nyomon követheti a véglegesítő és központi telepítéseket.
   
   ![Véglegesítő és központi telepítések nyomon követése a központi telepítési központban](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-azure-pipelines"></a>2. lehetőség: Azure-folyamatok 

Ha a fiókja rendelkezik a szükséges engedélyekkel, beállíthatja az Azure-folyamatokat a GitHubról vagy az Azure Reposből való folyamatos üzembe helyezéshez. További információ az Azure-folyamatok üzembe helyezéséről: [webalkalmazás üzembe helyezése az Azure](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)-ban app Services.

#### <a name="prerequisites"></a>Előfeltételek

Ahhoz Azure App Service, hogy az Azure-folyamatok használatával folyamatos kézbesítést hozzon létre, az Azure DevOps-szervezetnek a következő engedélyekkel kell rendelkeznie: 

- Az Azure-fióknak engedéllyel kell rendelkeznie a Azure Active Directory írásához és a szolgáltatás létrehozásához. 
  
- Az Azure-fióknak **tulajdonosi** szerepkörrel kell rendelkeznie az Azure-előfizetésében.

- A használni kívánt Azure DevOps-projekt rendszergazdájának kell lennie.

#### <a name="github--azure-pipelines"></a>GitHub + Azure-folyamatok

1. A [Azure Portal](https://portal.azure.com)keresse meg a **app Services**, majd válassza ki a telepíteni kívánt app Service. 
   
1. Az alkalmazás lapon a bal oldali menüben válassza a **központi telepítési központ** elemet.

1. A **telepítési központ** lapon válassza a **GitHub** lehetőséget, majd válassza a **Folytatás**lehetőséget. A **GitHub**esetében a **fiók módosítása** lehetőségre kattintva módosíthatja a hitelesített fiókot.

    ![Verziókövetés](media/app-service-continuous-deployment/deployment-center-src-control.png)
   
1. A **szolgáltató létrehozása** lapon válassza az **Azure-folyamatok (előzetes verzió)** lehetőséget, majd kattintson a **Folytatás**gombra.

    ![Szolgáltató létrehozása](media/app-service-continuous-deployment/select-build-provider.png)
   
1. A **Konfigurálás** lap **kód** területén válassza ki azt a **szervezetet**, **tárat**és **ágat** , amelyet folyamatosan telepíteni kíván, majd válassza a **Folytatás**lehetőséget.
     
     > [!NOTE]
     > Ha nem lát adattárakat, lehetséges, hogy engedélyeznie kell Azure App Service a GitHubon. Keresse meg a GitHub-tárházat, és lépjen a **Beállítások**  >  **alkalmazások**által  >  **jóváhagyott OAuth-alkalmazásokhoz**. Válassza a **Azure app Service**lehetőséget, majd válassza a **támogatás**lehetőséget. A szervezeti adattárak esetében a szervezet tulajdonosának kell lennie az engedélyek megadásához.
       
    A **build (létrehozás** ) szakaszban adja meg azt az Azure DevOps-szervezetet, projektet és nyelvi keretrendszert, amelyet az Azure-folyamatoknak használnia kell a Build-feladatok futtatásához, majd válassza a **Folytatás**lehetőséget.

   ![Szolgáltató létrehozása](media/app-service-continuous-deployment/build-configure.png)

1. A Build szolgáltató konfigurálása után tekintse át a beállításokat az **Összefoglalás** lapon, majd válassza a **Befejezés**lehetőséget.

   ![Szolgáltató létrehozása](media/app-service-continuous-deployment/summary.png)
   
1. Új véglegesíti a kiválasztott tárházban és ág-ban, most már folyamatosan üzembe helyezi a App Service. A **központi telepítési központ** lapon nyomon követheti a véglegesítő és központi telepítéseket.
   
   ![Véglegesítő és központi telepítések nyomon követése a központi telepítési központban](media/app-service-continuous-deployment/github-finished.png)

#### <a name="azure-repos--azure-pipelines"></a>Azure Repos + Azure-folyamatok

1. A [Azure Portal](https://portal.azure.com)keresse meg a **app Services**, majd válassza ki a telepíteni kívánt app Service. 
   
1. Az alkalmazás lapon a bal oldali menüben válassza a **központi telepítési központ** elemet.

1. Válassza az **Azure reposs** lehetőséget a **telepítési központ** lapon a forrásoldali vezérlő szolgáltatóként, és válassza a **Folytatás**lehetőséget.

    ![Verziókövetés](media/app-service-continuous-deployment/deployment-center-src-control.png)

1. A **szolgáltató létrehozása** lapon válassza az **Azure-folyamatok (előzetes verzió)** lehetőséget, majd kattintson a **Folytatás**gombra.

    ![Verziókövetés](media/app-service-continuous-deployment/azure-pipelines.png)

1. A **Konfigurálás** lap **kód** területén válassza ki azt a **szervezetet**, **tárat**és **ágat** , amelyet folyamatosan telepíteni kíván, majd válassza a **Folytatás**lehetőséget.

   > [!NOTE]
   > Ha a meglévő Azure DevOps-szervezet nem szerepel a listáján, előfordulhat, hogy az Azure-előfizetéshez kell csatolnia. További információ: [a CD-kiadási folyamat meghatározása](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).

   A **build (létrehozás** ) szakaszban adja meg azt az Azure DevOps-szervezetet, projektet és nyelvi keretrendszert, amelyet az Azure-folyamatoknak használnia kell a Build-feladatok futtatásához, majd válassza a **Folytatás**lehetőséget.

   ![Szolgáltató létrehozása](media/app-service-continuous-deployment/build-configure.png)

1. A Build szolgáltató konfigurálása után tekintse át a beállításokat az **Összefoglalás** lapon, majd válassza a **Befejezés**lehetőséget.  
     
   ![Szolgáltató létrehozása](media/app-service-continuous-deployment/summary-azure-pipelines.png)

1. Új véglegesíti a kiválasztott tárházban és ág-ban, most már folyamatosan üzembe helyezi a App Service. A **központi telepítési központ** lapon nyomon követheti a véglegesítő és központi telepítéseket.

## <a name="disable-continuous-deployment"></a>Folyamatos üzembe helyezés letiltása

A folyamatos üzembe helyezés letiltásához válassza a **Leválasztás** lehetőséget az alkalmazás **központi telepítési központ** lapjának tetején.

![Folyamatos üzembe helyezés letiltása](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="use-unsupported-repos"></a>Nem támogatott repók használata

Windows-alkalmazások esetén manuálisan is konfigurálhatja a folyamatos üzembe helyezést egy olyan felhőalapú git-vagy Mercurial-adattárból, amelyet a portál közvetlenül nem támogat, például [GitLab](https://gitlab.com/). Ezt úgy teheti meg, hogy kijelöli a külső mezőt a **központi telepítési központ** lapon. További információ: a [folyamatos üzembe helyezés beállítása manuális lépésekkel](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

## <a name="additional-resources"></a>További források

* [A folyamatos üzembe helyezéssel kapcsolatos gyakori problémák vizsgálata](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Azure PowerShell használatával](/powershell/azure/)
* [A git dokumentációja](https://git-scm.com/documentation)
* [A Kudu projekt](https://github.com/projectkudu/kudu/wiki)

[Adattár létrehozása (GitHub)]: https://help.github.com/articles/create-a-repo
[Adattár létrehozása (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Új git-Tárház létrehozása (Azure Repos)]: /azure/devops/repos/git/creatingrepo
