---
title: Telepítés a helyi Git-tárlaton
description: Ismerje meg, hogyan engedélyezheti a helyi Git-telepítést az Azure App Service-ben. A helyi számítógépről történő kódüzembe helyezés egyik legegyszerűbb módja.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 06/18/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: efe4c07a6231e0b2c95b049db056a4e5d055db98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152992"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Helyi Git-telepítés az Azure App Service szolgáltatásba

Ez az útmutató bemutatja, hogyan telepítheti alkalmazását az [Azure App Service-be](overview.md) a helyi számítógépen lévő Git-tárházból.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató lépéseinek követése:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Telepítse a Git](https://www.git-scm.com/downloads)- alkalmazást.
  
- Van egy helyi Git-tárház a telepíteni kívánt kódot. Mintatár letöltéséhez futtassa a következő parancsot a helyi terminálablakban:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-build-server"></a>Telepítés kudu buildkiszolgálóval

A kudu app service build-kiszolgálóval az alkalmazás helyi Git-üzembe helyezésének legegyszerűbb engedélyezése az Azure Cloud Shell használata. 

### <a name="configure-a-deployment-user"></a>Üzembe helyező felhasználó konfigurálása

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>A központi telepítés URL-címének beszereznie

Az URL-cím lekérni egy meglévő alkalmazás helyi [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) Git-központi telepítését, futtassa a Cloud Shell ben. Cserélje \<le az \<alkalmazásnév-> és a csoportnév> az alkalmazás és az Azure-erőforráscsoport nevére.

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```
> [!NOTE]
> Ha linux app-service-plan-t használ, hozzá kell adnia ezt a paramétert: --runtime python|3.7


Vagy hozzon létre egy új Git-kompatibilis alkalmazást, [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) `--deployment-local-git` futtassa a Cloud Shell a paraméterrel. Cserélje \<le az \<alkalmazásnevet>, \<a csoportnév> és a csomagnév-> az új Git-alkalmazás, az Azure-erőforráscsoport és az Azure App Service-csomag neveire.

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

Mindkét parancs a következő `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git`URL-címet adja vissza: . . Ezzel az URL-címmel telepítheti az alkalmazást a következő lépésben.

A fiókszintű URL-cím használata helyett engedélyezheti a helyi Git-et alkalmazásszintű hitelesítő adatok használatával is. Az Azure App Service automatikusan létrehozza ezeket a hitelesítő adatokat minden alkalmazáshoz. 

Az alkalmazás hitelesítő adatainak beszerezése a következő parancs futtatásával a Cloud Shellben. Cserélje \<le az \<alkalmazásnév> és a csoportnév> az alkalmazás nevére és az Azure-erőforráscsoport nevére.

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

Használja az URL-címet, amely visszatér az alkalmazás központi telepítéséhez a következő lépésben.

### <a name="deploy-the-web-app"></a>A webalkalmazás telepítése

1. Nyisson meg egy helyi terminálablakot a helyi Git-tárházba, és adjon hozzá egy Azure-távvezérlőt. A következő parancsban \<cserélje le az URL-> az előző lépésből kapott központi telepítési felhasználó- vagy alkalmazásspecifikus URL-címre.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Nyomja meg az `git push azure master`Azure-távvezérlőt a segítségével. 
   
1. A **Git Credential Manager** ablakban adja meg a [központi telepítési felhasználói jelszót,](#configure-a-deployment-user)nem az Azure bejelentkezési jelszavát.
   
1. Tekintse át a kimenetet. Előfordulhat, hogy futásidejű automatizálás, például MSBuild `npm install` ASP.NET, Node.js és `pip install` Python. 
   
1. Tallózással keresse meg az alkalmazást az Azure Portalon, és ellenőrizze, hogy a tartalom telepítve van-e.

## <a name="deploy-with-azure-pipelines-builds"></a>Üzembe helyezés az Azure Pipelines buildeléssel

Ha a fiók rendelkezik a szükséges engedélyekkel, beállíthatja az Azure Pipelines (Előzetes verzió) az alkalmazás helyi Git-üzembe helyezésének engedélyezéséhez. 

- Az Azure-fióknak engedéllyel kell rendelkeznie az Azure Active Directoryba való íráshoz és egy szolgáltatás létrehozásához. 
  
- Az Azure-fióknak tulajdonosi szerepkörsel kell **rendelkeznie** az Azure-előfizetésben.

- Rendszergazdának kell lennie a használni kívánt Azure DevOps-projektben.

A helyi Git-üzembe helyezés engedélyezése az alkalmazáshoz az Azure Pipelines használatával (előzetes verzió):

1. Az [Azure Portalon](https://portal.azure.com)keresse meg és válassza ki **az App Services**lehetőséget. 

1. Válassza ki az Azure App Service alkalmazást, és válassza a **Telepítési központ** a bal oldali menüben.
   
1. A **Központi telepítési központ** lapon válassza a **Helyi git**lehetőséget, majd kattintson a **Folytatás gombra.** 
   
   ![Válassza a Helyi git lehetőséget, majd a Continue (Folytatás) lehetőséget.](media/app-service-deploy-local-git/portal-enable.png)
   
1. A **Build provider (Build provider)** lapon válassza az **Azure-folyamatok (előzetes verzió)** lehetőséget, majd a **Continue (Folytatás) lehetőséget.** 
   
   ![Válassza az Azure-folyamatok (előzetes verzió), majd válassza a Folytatás lehetőséget.](media/app-service-deploy-local-git/pipeline-builds.png)

1. A **Konfigurálás** lapon konfiguráljon egy új Azure DevOps-szervezetet, vagy adjon meg egy meglévő szervezetet, majd válassza a **Folytatás**lehetőséget.
   
   > [!NOTE]
   > Ha a meglévő Azure DevOps-szervezet nem szerepel a listában, előfordulhat, hogy össze kell kapcsolnia az Azure-előfizetésével. További információt [a CD-kiadási folyamat megadása](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd)című témakörben talál.
   
1. Az App Service-csomag [tarifacsomagtól](https://azure.microsoft.com/pricing/details/app-service/plans/)függően előfordulhat, hogy megjelenik egy **üzembe helyezés az átmeneti** lap. Adja meg, hogy [engedélyezi-e a telepítési helyeket,](deploy-staging-slots.md)majd válassza **a Continue (Folytatás)** lehetőséget.
   
1. Az **Összegzés** lapon tekintse át a beállításokat, majd kattintson a **Befejezés gombra.**
   
1. Amikor az Azure Pipeline készen áll, másolja a Git-tárház URL-címét a **Deployment Center** lapról, amelyet a következő lépésben használhat. 
   
   ![A Git-tárház URL-címének másolása](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. A helyi terminálablakban adjon hozzá egy Azure-távvezérlőt a helyi Git-tárházhoz. A parancsban \<cserélje le az URL-> az előző lépésből kapott Git-tárház URL-címével.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Nyomja meg az `git push azure master`Azure-távvezérlőt a segítségével. 
   
1. A **Git Credential Manager** lapon jelentkezzen be a visualstudio.com felhasználónevével. További hitelesítési módszerek: [Azure DevOps Services hitelesítési áttekintés.](/vsts/git/auth-overview?view=vsts)
   
1. A telepítés befejezése után tekintse `https://<azure_devops_account>.visualstudio.com/<project_name>/_build`meg a build `https://<azure_devops_account>.visualstudio.com/<project_name>/_release`előrehaladását a alkalmazásban, és a telepítés előrehaladását a helyen.
   
1. Tallózással keresse meg az alkalmazást az Azure Portalon, és ellenőrizze, hogy a tartalom telepítve van-e.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>Telepítés – problémamegoldás

A következő gyakori hibaüzenetek jelenhetnek meg, amikor a Git segítségével teszi közzé az Azure-beli App Service-alkalmazásban:

|Üzenet|Ok|Megoldás:
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|Az alkalmazás nem működik.|Indítsa el az alkalmazást az Azure Portalon. Git-telepítés nem érhető el, ha a webalkalmazás le van állítva.|
|`Couldn't resolve host 'hostname'`|Az "azure" távvezérlő címadatai helytelenek.|A `git remote -v` paranccsal felsorolhatod az összes távirányítót a kapcsolódó URL-címmel együtt. Ellenőrizze, hogy az "azure" távoli URL-címe helyes-e. Szükség esetén távolítsa el és hozza létre újra a távvezérlőt a megfelelő URL-címmel.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`|Nem adott meg elágazása közben, `git push`vagy `push.default` nem `.gitconfig`állította be az értéket a alkalmazásban.|Futtassa `git push` újra, megadva `git push azure master`a főágat: .|
|`src refspec [branchname] does not match any.`|Az "azure" távirányítón kívül más ágra is próbálkozott.|Futtassa `git push` újra, megadva `git push azure master`a főágat: .|
|`RPC failed; result=22, HTTP code = 5xx.`|Ez a hiba akkor fordulhat elő, ha megpróbál leadni egy nagy git-tárházat HTTPS-en keresztül.|Módosítsa a git konfigurációját a `postBuffer` helyi gépen, hogy a nagyobb. Például: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|Telepített egy Node.js alkalmazást egy _package.json_ fájllal, amely további szükséges modulokat ad meg.|Tekintse `npm ERR!` át a hibaüzeneteket, mielőtt ez a hiba további környezetben a hiba. A hiba ismert okai és a `npm ERR!` megfelelő üzenetek a következők:<br /><br />**Hibás package.json fájl:**`npm ERR! Couldn't read dependencies.`<br /><br />**A natív modul nem rendelkezik bináris terjesztéssel a Windows rendszerhez:**<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />vagy <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>További források

- [Kudu projekt dokumentációja](https://github.com/projectkudu/kudu/wiki)
- [Folyamatos üzembe helyezés az Azure App Service szolgáltatásban](deploy-continuous-deployment.md)
- [Minta: Hozzon létre egy webalkalmazást, és telepítse a kódot egy helyi Git-tárházból (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Minta: Hozzon létre egy webalkalmazást, és telepítse a kódot egy helyi Git-tárházból (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
