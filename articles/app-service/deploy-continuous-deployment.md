---
title: Folyamatos üzembe helyezés – az Azure App Service |} A Microsoft Docs
description: A cikkből többet tudhat meg arról, hogyan engedélyezheti az Azure App Service szolgáltatásba való folyamatos üzembe helyezést.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2019
ms.author: cephalin;dariagrigoriu
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 3a207f5513c24f2c33571df929f7490ec7f2eba5
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836841"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Folyamatos üzembe helyezés az Azure App Service-ben

[Az Azure App Service](overview.md) lehetővé teszi, hogy a github, bitbucket-alapú folyamatos üzembe helyezés és [Azure Adattárakkal](https://azure.microsoft.com/services/devops/repos/) tárházak kiindulásként legújabb frissítések. Ez a cikk bemutatja, hogyan használhatja az Azure Portalt folyamatosan helyezze üzembe az alkalmazást, a Kudu-buildelési szolgáltatáson keresztül vagy [Azure folyamatok](https://azure.microsoft.com/services/devops/pipelines/). 

A verziókövetési szolgáltatások további információkért lásd: [adattár létrehozása (GitHub)], [adattár létrehozása (BitBucket)], vagy [hozzon létre egy új Git-adattárból (Azure-Adattárral)].

Manuálisan konfigurálnia a folyamatos üzembe helyezés a portál nem támogatja közvetlenül, mint például egy felhőalapú adattárból [GitLab](https://gitlab.com/), lásd: [állítsa be a folyamatos üzembe helyezés manuális lépések használatával](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Engedélyezze az Azure App Service-ben 

Az Azure-kódtárak használata esetén győződjön meg róla, Azure DevOps-szervezet az Azure-előfizetéshez van csatolva. További információkért lásd: [, helyezhet üzembe egy webalkalmazás, Azure DevOps-Services-fiók beállítása](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

A Bitbucket- és GitHub engedélyezése Azure App Service-ben való csatlakozáshoz a tárházhoz. Csak a hitelesítés egy source control Service szolgáltatást az egyszer kell. 

1. Válassza ki **App Services** a a [az Azure portal](https://portal.azure.com) bal oldali navigációs, és válassza ki a webalkalmazás számára telepíteni kívánja. 
   
1. Az alkalmazás oldalán válassza **Telepítőközpontot** a bal oldali menüben.
   
1. Az a **üzembe helyezési központ** lapon jelölje be **GitHub** vagy **Bitbucket**, majd válassza ki **engedélyezés**. 
   
   ![Válassza ki a source-control Service szolgáltatást, majd válassza az engedélyezés.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. Jelentkezzen be a szolgáltatásba, ha szükséges, és az engedélyezési utasításokat. 

## <a name="enable-continuous-deployment"></a>Folyamatos üzembe helyezés engedélyezése 

Miután egy adatforrás control Service szolgáltatást is engedélyezi, az alkalmazás a beépített folyamatos üzembe helyezés konfigurálása [Kudu App Service-buildelési kiszolgáló](#option-1-use-the-app-service-build-service), vagy a Kiszolgálókezelő [Azure folyamatok](#option-2-use-azure-pipelines). 

### <a name="option-1-use-the-app-service-build-service"></a>Option 1: Az App Service-build szolgáltatással

A beépített Kudu App Service-buildkiszolgáló való folyamatos üzembe helyezését a GitHub, Bitbucket vagy az Azure-Adattárakkal is használhatja. 

1. Válassza ki **App Services** a a [az Azure portal](https://portal.azure.com) bal oldali navigációs, és válassza ki a webalkalmazás számára telepíteni kívánja. 
   
1. Az alkalmazás oldalán válassza **Telepítőközpontot** a bal oldali menüben.
   
1. Válassza ki a jogosult Forrásvezérlő-szolgáltatónál az a **üzembe helyezési központ** lapon, és válassza ki **Folytatás**. A GitHub vagy bitbucket-alapú, választhatja **fiók módosítása** engedéllyel rendelkező fiók módosításával. 
   
   > [!NOTE]
   > Használja az Azure-Adattárakkal, győződjön meg róla, hogy az Azure DevOps-szolgáltatásokkal szervezet az Azure-előfizetéshez van csatolva. További információkért lásd: [, helyezhet üzembe egy webalkalmazás, Azure DevOps-Services-fiók beállítása](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).
   
1. A GitHub vagy az Azure-Adattárakkal a a **Build szolgáltató** lapon jelölje be **App Service-szolgáltatás készítése**, majd válassza ki **Folytatás**. Bitbucket mindig használja az App Service-összeállító szolgáltatás.
   
   ![Válassza ki az App Service-build szolgáltatást, majd kattintson a folytatás.](media/app-service-continuous-deployment/choose-kudu.png)
   
1. Az a **konfigurálása** oldalon:
   
   - A GitHub-legördülő listára, és válassza ki a **szervezet**, **tárház**, és **ág** folyamatos üzembe helyezés kívánt.
     
     > [!NOTE]
     > Ha nem látja az összes, szükség lehet engedélyezése Azure App Service a Githubon. Keresse meg a GitHub-tárban, és nyissa meg **beállítások** > **alkalmazások** > **engedélyezett OAuth alkalmazások**. Válassza ki **Azure App Service**, majd válassza ki **Grant**.
     
   - A Bitbucket, válassza ki a Bitbucket **csapat**, **tárház**, és **ág** folyamatos üzembe helyezés szeretné.
     
   - Az Azure-Adattárakkal, válassza ki a **Azure DevOps-szervezet**, **projekt**, **tárház**, és **ág** folyamatos üzembe helyezés szeretné.
     
     > [!NOTE]
     > Ha nem találja a szervezet Azure DevOps, ellenőrizze, hogy azt az Azure-előfizetéshez van csatolva. További információkért lásd: [, helyezhet üzembe egy webalkalmazás, Azure DevOps-Services-fiók beállítása](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)...
     
1. Válassza a **Folytatás** elemet.
   
   ![Töltse ki az adattár információit, majd kattintson a folytatás.](media/app-service-continuous-deployment/configure-kudu.png)
   
1. Miután konfigurálta a build-szolgáltató, tekintse át a beállításokat az a **összefoglalás** oldalra, és kattintson **Befejezés**.
   
   A kiválasztott adattár és a fiókiroda új véglegesítéseket most folyamatos üzembe helyezés az App Service alkalmazásba. Nyomon követheti a véglegesítéseket, és a központi telepítések a a **Telepítőközpontot** lapot.
   
   ![Nyomon követheti a véglegesítéseket, és a központi telepítések az üzembe helyezési központ](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-use-azure-pipelines"></a>Option 2: Használja az Azure Pipelinest 

Ha a fiókja rendelkezik a szükséges engedélyekkel, állíthat be Azure-folyamatok való folyamatos üzembe helyezés a GitHub vagy az Azure-Adattárakkal tárházak. Az üzembe helyezést az Azure-folyamatok kapcsolatos további információkért lásd: [webalkalmazás üzembe helyezése az Azure App Services](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps).

Az Azure App Service folyamatos készregyártás az Azure-folyamatok létrehozása az Azure DevOps-szervezet: 

- Az Azure-fiókkal rendelkeznie kell írni az Azure Active Directory és a egy szolgáltatás létrehozása. 
  
- Az Azure-fiókkal kell rendelkeznie a **tulajdonosa** szerepkört az Azure-előfizetésében.

- Az Azure DevOps-projekt létrehozásához használni szeretne a rendszergazdának kell lennie.

Konfigurálása (előzetes verzió) az Azure-folyamatok:

1. Válassza ki **App Services** a a [az Azure portal](https://portal.azure.com) bal oldali navigációs, és válassza ki a webalkalmazás számára telepíteni kívánja. 
   
1. Az alkalmazás oldalán válassza **Telepítőközpontot** a bal oldali menüben.
   
1. Az a **Build szolgáltató** lapon jelölje be **Azure folyamatok (előzetes verzió)** , majd válassza ki **Folytatás**. 
   
1. Az a **konfigurálása** lap a **kód** szakaszban:
   
   - A GitHub-legördülő listára, és válassza ki a **szervezet**, **tárház**, és **ág** folyamatos üzembe helyezés kívánt.
     
     > [!NOTE]
     > Ha nem látja az összes, szükség lehet engedélyezése Azure App Service a Githubon. Keresse meg a GitHub-tárban, és nyissa meg **beállítások** > **alkalmazások** > **engedélyezett OAuth alkalmazások**. Válassza ki **Azure App Service**, majd válassza ki **Grant**.
     
   - Az Azure-Adattárakkal, válassza ki a **Azure DevOps-szervezet**, **projekt**, **tárház**, és **ág** , folyamatos üzembe helyezés kívánt vagy egy új Azure DevOps-szervezet konfigurálása.
     
     > [!NOTE]
     > Ha a meglévő Azure DevOps-szervezet nem szerepel a listán, szükség lehet az Azure-előfizetéshez mutat. További információkért lásd: [határozza meg a CD-kiadási folyamatok](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
     
1. Válassza a **Folytatás** elemet.
   
1. Az Azure-Adattárakkal az a **összeállítása** területén adja meg a nyelvi keretrendszerébe, amely az Azure-folyamatok kell használni az összeállítási feladatok futtatásához, és válassza **Folytatás**.
   
1. Az a **teszt** lapon, döntse el, hogy engedélyezze a terhelési teszteket, és válassza ki **Folytatás**.
   
1. Az App Service-csomag függően [tarifacsomag](https://azure.microsoft.com/pricing/details/app-service/plans/), látni egy **üzembe helyezés átmeneti környezetbe** lapot. Válassza ki, hogy e [üzembe helyezési pontok engedélyezése](deploy-staging-slots.md), majd válassza ki **Folytatás**.
   
   > [!NOTE]
   > A folyamatok az Azure nem teszi lehetővé a folyamatos készregyártás az éles webalkalmazásra. Ez a korlátozás meggátolja a véletlen éles üzemelő példányok. Az előkészítési pontra való folyamatos készregyártás beállítása, és ott a módosítások ellenőrzéséhez majd felcserélheti a tárolóhely, amikor készen áll.
   
1. Miután konfigurálta a build-szolgáltató, tekintse át a beállításokat az a **összefoglalás** oldalra, és kattintson **Befejezés**.
   
   A kiválasztott adattár és a fiókiroda új véglegesítéseket most folyamatos üzembe helyezés az App Service alkalmazásba. Nyomon követheti a véglegesítéseket, és a központi telepítések a a **Telepítőközpontot** lapot.
   
   ![Nyomon követheti a véglegesítéseket, és a központi telepítések az üzembe helyezési központ](media/app-service-continuous-deployment/github-finished.png)

## <a name="disable-continuous-deployment"></a>Folyamatos üzembe helyezés letiltása

Válassza ki a folyamatos üzembe helyezés letiltása **Disconnect** az alkalmazás tetején **üzembe helyezési központ** lap.

![Folyamatos üzembe helyezés letiltása](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="additional-resources"></a>További források

* [Folyamatos üzembe helyezéssel kapcsolatos gyakori problémák vizsgálata](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Azure PowerShell használata](/powershell/azureps-cmdlets-docs)
* [A Git dokumentációja](https://git-scm.com/documentation)
* [A Kudu projekt](https://github.com/projectkudu/kudu/wiki)

[Adattár létrehozása (GitHub)]: https://help.github.com/articles/create-a-repo
[Adattár létrehozása (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Hozzon létre egy új Git-adattárból (Azure-Adattárral)]: /azure/devops/repos/git/creatingrepo
