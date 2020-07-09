---
title: Üzembe helyezés helyi git-tárházból
description: Megtudhatja, hogyan engedélyezheti a helyi git-telepítést a Azure App Service. A kód a helyi gépről való üzembe helyezésének egyik legegyszerűbb módja.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 06/18/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: efe4c07a6231e0b2c95b049db056a4e5d055db98
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77152992"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Helyi git üzembe helyezése Azure App Service

Ez az útmutató bemutatja, hogyan helyezheti üzembe az alkalmazást [Azure app Service](overview.md) egy git-tárházból a helyi számítógépen.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek követéséhez tegye a következőket:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Telepítse a git](https://www.git-scm.com/downloads)-t.
  
- Rendelkeznie kell egy helyi git-tárházmal, amelybe telepíteni kívánja a kódot. Egy minta tárház letöltéséhez futtassa a következő parancsot a helyi terminál ablakban:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-build-server"></a>Üzembe helyezés a kudu Build Serverrel

A legkönnyebben engedélyezhető a helyi git üzembe helyezése az alkalmazáshoz a kudu App Service Build Server használatával Azure Cloud Shell. 

### <a name="configure-a-deployment-user"></a>Üzembe helyező felhasználó konfigurálása

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>Az üzembe helyezési URL-cím beszerzése

Ha szeretné lekérni az URL-címet, amely lehetővé teszi a helyi git üzembe helyezését egy meglévő alkalmazáshoz, futtassa [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) a parancsot a Cloud shell. Cserélje le a \<app-name> és az \<group-name> alkalmazást az alkalmazás és az Azure-erőforráscsoport nevére.

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```
> [!NOTE]
> Ha Linux app-Service-csomagot használ, hozzá kell adnia a következő paramétert:--Runtime Python | 3.7


Ha új git-kompatibilis alkalmazást szeretne létrehozni, futtassa a parancsot [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) a Cloud Shellban a (z `--deployment-local-git` ) paraméterrel. Cserélje le a, a \<app-name> \<group-name> és \<plan-name> az nevet az új git-alkalmazás, az Azure-erőforráscsoport és a Azure app Service terv helyére.

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

Bármelyik parancs egy URL-címet ad vissza, például: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` . Ezzel az URL-címmel telepítheti az alkalmazást a következő lépésben.

A fiók szintű URL-cím használata helyett az alkalmazás szintű hitelesítő adatokkal is engedélyezheti a helyi git használatát. Azure App Service automatikusan létrehozza ezeket a hitelesítő adatokat minden alkalmazáshoz. 

Az alkalmazás hitelesítő adatainak beszerzéséhez futtassa a következő parancsot a Cloud Shellban. Cserélje \<app-name> le \<group-name> a és az alkalmazást az alkalmazás nevére és az Azure-erőforráscsoport nevére.

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

A következő lépésben adja meg az alkalmazás üzembe helyezéséhez használt URL-címet.

### <a name="deploy-the-web-app"></a>A webalkalmazás üzembe helyezése

1. Nyisson meg egy helyi terminált a helyi git-tárházban, és adjon hozzá egy távoli Azure-adattárat. A következő parancsban cserélje le a \<url> elemet az előző lépésből származó, felhasználói vagy alkalmazásspecifikus URL-címre.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Küldje el az Azure Remote-t a következővel: `git push azure master` . 
   
1. A **git Hitelesítőadat-kezelő** ablakban adja meg az [üzembe helyezési felhasználói jelszót](#configure-a-deployment-user), ne pedig az Azure bejelentkezési jelszavát.
   
1. Tekintse át a kimenetet. Előfordulhat, hogy a futásidejű automatizálás, például az MSBuild for ASP.NET, `npm install` a Node.js és `pip install` a Python. 
   
1. Keresse meg az alkalmazást a Azure Portalban annak ellenőrzéséhez, hogy a tartalom telepítve van-e.

## <a name="deploy-with-azure-pipelines-builds"></a>Üzembe helyezés Azure-folyamatokkal – buildek

Ha a fiókja rendelkezik a szükséges engedélyekkel, beállíthatja az Azure-folyamatokat (előzetes verzió) az alkalmazás helyi git-telepítésének engedélyezéséhez. 

- Az Azure-fióknak engedéllyel kell rendelkeznie a Azure Active Directory írásához és a szolgáltatás létrehozásához. 
  
- Az Azure-fióknak **tulajdonosi** szerepkörrel kell rendelkeznie az Azure-előfizetésében.

- A használni kívánt Azure DevOps-projekt rendszergazdájának kell lennie.

A helyi git üzembe helyezésének engedélyezése az alkalmazáshoz Azure-folyamatokkal (előzetes verzió):

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza a **app Services**lehetőséget. 

1. Válassza ki a Azure App Service alkalmazást, és a bal oldali menüben válassza ki a **központi telepítési központ** elemet.
   
1. A **központi telepítési központ** lapon válassza a **helyi git**lehetőséget, majd kattintson a **Folytatás**gombra. 
   
   ![Válassza a helyi git lehetőséget, majd kattintson a Folytatás gombra.](media/app-service-deploy-local-git/portal-enable.png)
   
1. A **szolgáltató létrehozása** lapon válassza az **Azure-folyamatok (előzetes verzió)** lehetőséget, majd kattintson a **Folytatás**gombra. 
   
   ![Válassza az Azure-folyamatok (előzetes verzió) lehetőséget, majd kattintson a Continue (folytatás) gombra.](media/app-service-deploy-local-git/pipeline-builds.png)

1. A **configure (Konfigurálás** ) lapon konfiguráljon egy új Azure DevOps-szervezetet, vagy adjon meg egy meglévő szervezetet, majd válassza a **Folytatás**lehetőséget.
   
   > [!NOTE]
   > Ha a meglévő Azure DevOps-szervezet nem szerepel a listáján, előfordulhat, hogy az Azure-előfizetéshez kell csatolnia. További információ: [a CD-kiadási folyamat meghatározása](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
   
1. A App Service-csomag [díjszabási szintjétől](https://azure.microsoft.com/pricing/details/app-service/plans/)függően előfordulhat, hogy az **üzembe helyezés az átmeneti** oldalon lehetőség látható. Válassza ki, hogy engedélyezi-e az [üzembe helyezési](deploy-staging-slots.md)pontokat, majd kattintson a **Folytatás**gombra.
   
1. Az **Összefoglalás** lapon tekintse át a beállításokat, majd kattintson a **Befejezés gombra**.
   
1. Ha az Azure-folyamat elkészült, másolja a git-tárház URL-címét a **központi telepítési központ** oldaláról a következő lépésben való használatra. 
   
   ![A git-tárház URL-címének másolása](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. A helyi terminál ablakban adjon hozzá egy távoli Azure-t a helyi git-tárházhoz. A parancsban cserélje le az \<url> elemet az előző lépésben kapott git-tárház URL-címére.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Küldje el az Azure Remote-t a következővel: `git push azure master` . 
   
1. A **git Hitelesítőadat-kezelő** lapon jelentkezzen be a VisualStudio.com-felhasználónevével. További hitelesítési módszerek: az [Azure DevOps Services hitelesítésének áttekintése](/vsts/git/auth-overview?view=vsts).
   
1. Az üzembe helyezés befejezése után tekintse meg a Build előrehaladását a következő helyen: `https://<azure_devops_account>.visualstudio.com/<project_name>/_build` , és a telepítés folyamata `https://<azure_devops_account>.visualstudio.com/<project_name>/_release` .
   
1. Keresse meg az alkalmazást a Azure Portalban annak ellenőrzéséhez, hogy a tartalom telepítve van-e.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>Üzembe helyezési hibák

A következő gyakori hibaüzenetek jelenhetnek meg, ha a git használatával tesz közzé egy App Service alkalmazást az Azure-ban:

|Üzenet|Ok|Megoldás:
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|Az alkalmazás nem működik.|Indítsa el az alkalmazást a Azure Portal. A git-telepítés nem érhető el a webalkalmazás leállításakor.|
|`Couldn't resolve host 'hostname'`|Az "Azure" távoli adatcímeinek adatai helytelenek.|A `git remote -v` parancs használatával listázhatja az összes távoli, valamint a hozzá tartozó URL-címet. Győződjön meg arról, hogy az "Azure" távoli URL-címe helyes. Ha szükséges, távolítsa el, majd hozza létre újra a távoli elérést a megfelelő URL-cím használatával.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`|Nem adott meg ágat a alatt `git push` , vagy nem állította be a `push.default` értéket `.gitconfig` .|Futtassa `git push` újra a főág megadását: `git push azure master` .|
|`src refspec [branchname] does not match any.`|A főkiszolgálón kívül más ágat próbált meg elküldeni az "Azure" távoli gépen.|Futtassa `git push` újra a főág megadását: `git push azure master` .|
|`RPC failed; result=22, HTTP code = 5xx.`|Ez a hiba akkor fordulhat elő, ha egy nagyméretű git-tárházat próbál leküldeni HTTPS-kapcsolaton keresztül.|Módosítsa a git-konfigurációt a helyi gépen, hogy minél `postBuffer` nagyobb legyen. Példa: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|Egy Node.js alkalmazást telepített egy _package.jsa_ fájlon, amely további szükséges modulokat határoz meg.|A hiba előtt tekintse át a hibaüzeneteket `npm ERR!` , hogy a probléma további kontextusban legyen. A hiba ismert okai és a hozzájuk tartozó üzenetek a következők `npm ERR!` :<br /><br />**Helytelenül formázott package.jsa következő fájlon**:`npm ERR! Couldn't read dependencies.`<br /><br />A **natív modul nem rendelkezik bináris terjesztéssel a Windows rendszerhez**:<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />vagy <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>További források

- [A Project kudu dokumentációja](https://github.com/projectkudu/kudu/wiki)
- [Folyamatos üzembe helyezés Azure App Service](deploy-continuous-deployment.md)
- [Minta: Webalkalmazás létrehozása és kód üzembe helyezése helyi git-adattárból (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Példa: Webalkalmazás létrehozása és kód üzembe helyezése helyi git-adattárból (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
