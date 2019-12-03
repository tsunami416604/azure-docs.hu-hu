---
title: Folyamatos üzembe helyezés konfigurálása
description: Megtudhatja, hogyan engedélyezheti a CI/CD-t a GitHub, a BitBucket, az Azure Repos vagy más repók Azure App Service. Válassza ki az igényeinek megfelelő Build-folyamatot.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 08/23/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 692b07c82c329a93d79ad3a87beec5dbe1c595d3
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74669985"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Folyamatos üzembe helyezés Azure App Service

[Azure app Service](overview.md) lehetővé teszi a GitHub, a BitBucket és az [Azure Repos](https://azure.microsoft.com/services/devops/repos/) -Tárházak folyamatos üzembe helyezését a legújabb frissítések behúzásával. Ez a cikk bemutatja, hogyan használható a Azure Portal az alkalmazás folyamatos üzembe helyezéséhez a kudu Build Service vagy az [Azure-folyamatok](https://azure.microsoft.com/services/devops/pipelines/)használatával. 

A forrás-ellenőrzési szolgáltatásokkal kapcsolatos további információkért lásd: adattár [Adattár létrehozása (GitHub)], tárház [Adattár létrehozása (BitBucket)], vagy [Új git-Tárház létrehozása (Azure Repos)]repók).

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Engedélyezés Azure App Service 

Az Azure Repos használatához ellenőrizze, hogy az Azure DevOps-szervezet kapcsolódik-e az Azure-előfizetéséhez. További információkért lásd: [Azure DevOps Services-fiók beállítása, hogy az üzembe helyezhető egy webalkalmazásban](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops).

A bitbucket vagy a GitHub esetében engedélyezze Azure App Service az adattárhoz való kapcsolódást. Csak egyszer kell engedélyeznie a verziókövetés szolgáltatást. 

1. Válassza a **app Services** lehetőséget a [Azure Portal](https://portal.azure.com) bal oldali navigációs menüben, majd válassza ki a telepíteni kívánt webalkalmazást. 
   
1. Az alkalmazás lapon a bal oldali menüben válassza a **központi telepítési központ** elemet.
   
1. A **központi telepítési központ** lapon válassza a **GitHub** vagy a **bitbucket**lehetőséget, majd válassza az **Engedélyezés**lehetőséget. 
   
   ![Válassza a verziókövetés szolgáltatás lehetőséget, majd válassza az Engedélyezés lehetőséget.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. Ha szükséges, jelentkezzen be a szolgáltatásba, és kövesse az engedélyezési utasításokat. 

## <a name="enable-continuous-deployment"></a>Folyamatos üzembe helyezés engedélyezése 

Miután engedélyezte a verziókövetés szolgáltatást, konfigurálja az alkalmazást folyamatos üzembe helyezéshez a beépített [Kudu app Service a Build-kiszolgálót](#option-1-use-the-app-service-build-service)vagy az [Azure-folyamatokat](#option-2-use-azure-pipelines). 

### <a name="option-1-use-the-app-service-build-service"></a>1\. lehetőség: a App Service Build szolgáltatás használata

A beépített kudu App Service felépíteni a kiszolgálót a GitHub, a bitbucket vagy az Azure Repos szolgáltatásból való folyamatos üzembe helyezéshez. 

1. Válassza a **app Services** lehetőséget a [Azure Portal](https://portal.azure.com) bal oldali navigációs menüben, majd válassza ki a telepíteni kívánt webalkalmazást. 
   
1. Az alkalmazás lapon a bal oldali menüben válassza a **központi telepítési központ** elemet.
   
1. A **központi telepítési központ** lapon válassza ki az Ön által jóváhagyott forrásoldali vezérlő szolgáltatót, majd válassza a **Folytatás**lehetőséget. A GitHub vagy a bitbucket esetében a **fiók módosítása** lehetőségre kattintva módosíthatja a hitelesítő fiókot. 
   
   > [!NOTE]
   > Az Azure Repos használatához ellenőrizze, hogy az Azure DevOps Services-szervezet kapcsolódik-e az Azure-előfizetéséhez. További információkért lásd: [Azure DevOps Services-fiók beállítása, hogy az üzembe helyezhető egy webalkalmazásban](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops).
   
1. A GitHub vagy az Azure Repos esetében a **szolgáltató létrehozása** lapon válassza a **app Service Build szolgáltatás**lehetőséget, majd kattintson a **Folytatás**gombra. A bitbucket mindig a App Service Build szolgáltatást használja.
   
   ![Válassza ki App Service Build szolgáltatást, majd válassza a Folytatás lehetőséget.](media/app-service-continuous-deployment/choose-kudu.png)
   
1. A **configure (Konfigurálás** ) lapon:
   
   - A GitHubnál válassza a legördülő listát, és válassza ki azt a **szervezetet**, **tárat**és **ágat** , amelyet folyamatosan szeretne üzembe helyezni.
     
     > [!NOTE]
     > Ha nem lát adattárakat, lehetséges, hogy engedélyeznie kell Azure App Service a GitHubon. Keresse meg a GitHub-tárházat, és lépjen a **beállítások** > **alkalmazások** > a **OAuth-alkalmazások**lehetőségre. Válassza a **Azure app Service**lehetőséget, majd válassza a **támogatás**lehetőséget.
     
   - A bitbucket területen válassza ki azt a bitbucket- **csoportot**, **tárat**és **ágat** , amelyet folyamatosan telepíteni szeretne.
     
   - Az Azure Repos esetében válassza ki azt az **Azure DevOps-szervezetet**, **projektet**, **tárházat**és **ágat** , amelyet folyamatosan telepíteni szeretne.
     
     > [!NOTE]
     > Ha az Azure DevOps-szervezet nem szerepel a listáján, győződjön meg arról, hogy az Azure-előfizetéshez van csatolva. További információkért lásd: [Azure DevOps Services-fiók beállítása, hogy üzembe helyezhető egy webalkalmazásban](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops).
     
1. Válassza a **Folytatás** elemet.
   
   ![Töltse ki a tárház adatait, majd válassza a Folytatás lehetőséget.](media/app-service-continuous-deployment/configure-kudu.png)
   
1. A Build szolgáltató konfigurálása után tekintse át a beállításokat az **Összefoglalás** lapon, majd válassza a **Befejezés**lehetőséget.
   
   Új véglegesíti a kiválasztott tárházban és ág-ban, most már folyamatosan üzembe helyezi a App Service alkalmazást. A **központi telepítési központ** lapon nyomon követheti a véglegesítő és központi telepítéseket.
   
   ![Véglegesítő és központi telepítések nyomon követése a központi telepítési központban](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-use-azure-pipelines"></a>2\. lehetőség: az Azure-folyamatok használata 

Ha a fiókja rendelkezik a szükséges engedélyekkel, beállíthatja az Azure-folyamatokat a GitHubról vagy az Azure Repos-adattárakból történő folyamatos üzembe helyezéshez. További információ az Azure-folyamatok üzembe helyezéséről: [webalkalmazás üzembe helyezése az Azure](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)-ban app Services.

Az Azure DevOps-szervezetben folyamatos kézbesítésű Azure-folyamatok létrehozásához Azure App Service: 

- Az Azure-fióknak engedéllyel kell rendelkeznie a Azure Active Directory írásához és a szolgáltatás létrehozásához. 
  
- Az Azure-fióknak **tulajdonosi** szerepkörrel kell rendelkeznie az Azure-előfizetésében.

- A használni kívánt Azure DevOps-projekt rendszergazdájának kell lennie.

Az Azure-folyamatok konfigurálása (előzetes verzió):

1. Válassza a **app Services** lehetőséget a [Azure Portal](https://portal.azure.com) bal oldali navigációs menüben, majd válassza ki a telepíteni kívánt webalkalmazást. 
   
1. Az alkalmazás lapon a bal oldali menüben válassza a **központi telepítési központ** elemet.
   
1. A **szolgáltató létrehozása** lapon válassza az **Azure-folyamatok (előzetes verzió)** lehetőséget, majd kattintson a **Folytatás**gombra. 
   
1. A **configure (Konfigurálás** ) lap **kód** szakaszában:
   
   - A GitHubnál válassza a legördülő listát, és válassza ki azt a **szervezetet**, **tárat**és **ágat** , amelyet folyamatosan szeretne üzembe helyezni.
     
     > [!NOTE]
     > Ha nem lát adattárakat, lehetséges, hogy engedélyeznie kell Azure App Service a GitHubon. Keresse meg a GitHub-tárházat, és lépjen a **beállítások** > **alkalmazások** > a **OAuth-alkalmazások**lehetőségre. Válassza a **Azure app Service**lehetőséget, majd válassza a **támogatás**lehetőséget.
     
   - Az Azure Repos esetében válassza ki a folyamatosan telepíteni kívánt **Azure DevOps-szervezetet**, **projektet**, **tárházat**és **ágat** , vagy állítson be egy új Azure DevOps-szervezetet.
     
     > [!NOTE]
     > Ha a meglévő Azure DevOps-szervezet nem szerepel a listáján, előfordulhat, hogy az Azure-előfizetéshez kell csatolnia. További információ: [a CD-kiadási folyamat meghatározása](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
     
1. Válassza a **Folytatás** elemet.
   
1. Az Azure Repos esetében az **összeállítás** szakaszban adja meg azt a nyelvi keretrendszert, amelyet az Azure-folyamatoknak a Build feladatok futtatásához használniuk kell, majd válassza a **Folytatás**lehetőséget.
   
1. A **teszt** lapon válassza ki, hogy engedélyezi-e a terhelési teszteket, majd kattintson a **Folytatás**gombra.
   
1. A App Service-csomag [díjszabási szintjétől](https://azure.microsoft.com/pricing/details/app-service/plans/)függően előfordulhat, hogy az **üzembe helyezés az átmeneti** oldalon lehetőség látható. Válassza ki, hogy engedélyezi-e az [üzembe helyezési](deploy-staging-slots.md)pontokat, majd kattintson a **Folytatás**gombra.
   
   > [!NOTE]
   > Az Azure-folyamatok nem teszik lehetővé a folyamatos kézbesítést az üzemi tárolóhelyre. Ez a korlátozás megakadályozza a véletlen üzembe helyezést az éles környezetben. Állítsa be a folyamatos kézbesítést egy átmeneti tárolóhelyre, ellenőrizze a módosításokat, majd ha elkészült, cserélje ki a tárolóhelyeket.
   
1. A Build szolgáltató konfigurálása után tekintse át a beállításokat az **Összefoglalás** lapon, majd válassza a **Befejezés**lehetőséget.
   
   Új véglegesíti a kiválasztott tárházban és ág-ban, most már folyamatosan üzembe helyezi a App Service alkalmazást. A **központi telepítési központ** lapon nyomon követheti a véglegesítő és központi telepítéseket.
   
   ![Véglegesítő és központi telepítések nyomon követése a központi telepítési központban](media/app-service-continuous-deployment/github-finished.png)

## <a name="disable-continuous-deployment"></a>Folyamatos üzembe helyezés letiltása

A folyamatos üzembe helyezés letiltásához válassza a **Leválasztás** lehetőséget az alkalmazás **központi telepítési központ** lapjának tetején.

![Folyamatos üzembe helyezés letiltása](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="use-unsupported-repos"></a>Nem támogatott repók használata

Windows-alkalmazások esetén manuálisan is konfigurálhatja a folyamatos üzembe helyezést egy felhőalapú git-vagy Mecurial-tárházból, amelyet a portál nem támogat közvetlenül, például [GitLab](https://gitlab.com/). Ezt úgy teheti meg, hogy kijelöli a külső mezőt a **központi telepítési központ** lapon. További információ: a [folyamatos üzembe helyezés beállítása manuális lépésekkel](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

## <a name="additional-resources"></a>További források

* [A folyamatos üzembe helyezéssel kapcsolatos gyakori problémák vizsgálata](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Azure PowerShell használata](/powershell/azureps-cmdlets-docs)
* [A Git dokumentációja](https://git-scm.com/documentation)
* [A Kudu projekt](https://github.com/projectkudu/kudu/wiki)

[Adattár létrehozása (GitHub)]: https://help.github.com/articles/create-a-repo
[Adattár létrehozása (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Új git-Tárház létrehozása (Azure Repos)]: /azure/devops/repos/git/creatingrepo
