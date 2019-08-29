---
title: Üzembe helyezés helyi git-tárházból – Azure App Service
description: Megtudhatja, hogyan engedélyezheti a helyi git-telepítést a Azure App Service.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/18/2019
ms.author: cephalin
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 9714052de5cdfbdbf0789c7b4711469ee9202bac
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070643"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Helyi git üzembe helyezése Azure App Service

Ez az útmutató bemutatja, hogyan helyezheti üzembe az alkalmazást [Azure app Service](overview.md) egy git-tárházból a helyi számítógépen.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek követéséhez tegye a következőket:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Telepítse a Git szoftvert](https://www.git-scm.com/downloads).
  
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

Ha szeretné lekérni az URL-címet, amely lehetővé teszi a helyi git [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) üzembe helyezését egy meglévő alkalmazáshoz, futtassa a parancsot a Cloud shell. Cserélje \<le az alkalmazás neve > \<és a csoportnév > az alkalmazás és az Azure-erőforráscsoport nevére.

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

Ha új git-kompatibilis alkalmazást szeretne létrehozni, futtassa a parancsot [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) a Cloud Shellban a `--deployment-local-git` (z) paraméterrel. Cserélje \<le az alkalmazás neve > \<, a csoport neve > és \<a terv neve > az új git-alkalmazás, az Azure-erőforráscsoport és az Azure app Service terv nevére.

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

Bármelyik parancs egy URL-címet ad `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git`vissza, például:. Ezzel az URL-címmel telepítheti az alkalmazást a következő lépésben.

A fiók szintű URL-cím használata helyett az alkalmazás szintű hitelesítő adatokkal is engedélyezheti a helyi git használatát. Azure App Service automatikusan létrehozza ezeket a hitelesítő adatokat minden alkalmazáshoz. 

Az alkalmazás hitelesítő adatainak beszerzéséhez futtassa a következő parancsot a Cloud Shellban. Cserélje \<le az App-name \<> és a Group-Name > az alkalmazás nevére és az Azure-erőforráscsoport nevére.

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

A következő lépésben adja meg az alkalmazás üzembe helyezéséhez használt URL-címet.

### <a name="deploy-the-web-app"></a>A webalkalmazás üzembe helyezése

1. Nyisson meg egy helyi terminált a helyi git-tárházban, és adjon hozzá egy távoli Azure-adattárat. Az alábbi parancsban cserélje le \<az URL->t a központi telepítéshez tartozó, felhasználóra jellemző URL-cím vagy az alkalmazás-specifikus URL-címére, amelyet az előző lépésben kapott.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Küldje el az Azure Remote `git push azure master`-t a következővel:. 
   
1. A **git Hitelesítőadat-kezelő** ablakban adja meg az [üzembe helyezési felhasználói jelszót](#configure-a-deployment-user), ne pedig az Azure bejelentkezési jelszavát.
   
1. Tekintse át a kimenetet. A Node. js-hez és `npm install` `pip install` a Pythonhoz olyan futtatókörnyezet-specifikus automatizálást is láthat, mint például az MSBuild for ASP.net. 
   
1. Keresse meg az alkalmazást a Azure Portalban annak ellenőrzéséhez, hogy a tartalom telepítve van-e.

## <a name="deploy-with-azure-pipelines-builds"></a>Üzembe helyezés Azure-folyamatokkal – buildek

Ha a fiókja rendelkezik a szükséges engedélyekkel, beállíthatja az Azure-folyamatokat (előzetes verzió) az alkalmazás helyi git-telepítésének engedélyezéséhez. 

- Az Azure-fióknak engedéllyel kell rendelkeznie a Azure Active Directory írásához és a szolgáltatás létrehozásához. 
  
- Az Azure-fióknak **tulajdonosi** szerepkörrel kell rendelkeznie az Azure-előfizetésében.

- A használni kívánt Azure DevOps-projekt rendszergazdájának kell lennie.

A helyi git üzembe helyezésének engedélyezése az alkalmazáshoz Azure-folyamatokkal (előzetes verzió):

1. Navigáljon a [Azure Portal](https://portal.azure.com)Azure app Service alkalmazás lapjára, és a bal oldali menüben válassza a **központi telepítési központ** elemet.
   
1. A **központi telepítési központ** lapon válassza a **helyi git**lehetőséget, majd kattintson a **Folytatás**gombra. 
   
   ![Válassza a helyi git lehetőséget, majd kattintson a Folytatás gombra.](media/app-service-deploy-local-git/portal-enable.png)
   
1. A **szolgáltató létrehozása** lapon válassza az **Azure-folyamatok (előzetes verzió)** lehetőséget, majd kattintson a **Folytatás**gombra. 
   
   ![Válassza az Azure-folyamatok (előzetes verzió) lehetőséget, majd kattintson a Continue (folytatás) gombra.](media/app-service-deploy-local-git/pipeline-builds.png)

1. A **configure (Konfigurálás** ) lapon konfiguráljon egy új Azure DevOps-szervezetet, vagy adjon meg egy meglévő szervezetet, majd válassza a **Folytatás**lehetőséget.
   
   > [!NOTE]
   > Ha a meglévő Azure DevOps-szervezet nem szerepel a listáján, előfordulhat, hogy az Azure-előfizetéshez kell csatolnia. További információ: [a CD-kiadási folyamat meghatározása](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
   
1. A App Service-csomag díjszabási [szintjétől](https://azure.microsoft.com/pricing/details/app-service/plans/)függően előfordulhat, hogy az **üzembe helyezés az átmeneti** oldalon lehetőség látható. Válassza ki, hogy engedélyezi-e az [üzembe helyezési](deploy-staging-slots.md)pontokat, majd kattintson a **Folytatás**gombra.
   
1. Az **Összefoglalás** lapon tekintse át a beállításokat, majd kattintson a **Befejezés gombra**.
   
1. Ha az Azure-folyamat elkészült, másolja a git-tárház URL-címét a **központi telepítési központ** oldaláról a következő lépésben való használatra. 
   
   ![A git-tárház URL-címének másolása](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. A helyi terminál ablakban adjon hozzá egy távoli Azure-t a helyi git-tárházhoz. A parancsban cserélje le \<az URL-címet > az előző lépésben kapott git-tárház URL-címére.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Küldje el az Azure Remote `git push azure master`-t a következővel:. 
   
1. A **git Hitelesítőadat-kezelő** lapon jelentkezzen be a VisualStudio.com-felhasználónevével. További hitelesítési módszerek: az [Azure DevOps Services hitelesítésének áttekintése](/vsts/git/auth-overview?view=vsts).
   
1. Az üzembe helyezés befejezése után tekintse meg a Build `https://<azure_devops_account>.visualstudio.com/<project_name>/_build`előrehaladását a következő helyen: `https://<azure_devops_account>.visualstudio.com/<project_name>/_release`, és a telepítés folyamata.
   
1. Keresse meg az alkalmazást a Azure Portalban annak ellenőrzéséhez, hogy a tartalom telepítve van-e.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>Üzembe helyezési hibák

A következő gyakori hibaüzenetek jelenhetnek meg, ha a git használatával tesz közzé egy App Service alkalmazást az Azure-ban:

|Message|Ok|Megoldás:
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|Az alkalmazás nem működik.|Indítsa el az alkalmazást a Azure Portal. A git-telepítés nem érhető el a webalkalmazás leállításakor.|
|`Couldn't resolve host 'hostname'`|Az "Azure" távoli adatcímeinek adatai helytelenek.|A `git remote -v` parancs használatával listázhatja az összes távoli, valamint a hozzá tartozó URL-címet. Győződjön meg arról, hogy az "Azure" távoli URL-címe helyes. Ha szükséges, távolítsa el, majd hozza létre újra a távoli elérést a megfelelő URL-cím használatával.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`|Nem adott meg ágat a alatt `git push`, vagy nem állította be a `push.default` értéket `.gitconfig`.|Futtassa `git push` újra a főág megadását: `git push azure master`.|
|`src refspec [branchname] does not match any.`|A főkiszolgálón kívül más ágat próbált meg elküldeni az "Azure" távoli gépen.|Futtassa `git push` újra a főág megadását: `git push azure master`.|
|`RPC failed; result=22, HTTP code = 5xx.`|Ez a hiba akkor fordulhat elő, ha egy nagyméretű git-tárházat próbál leküldeni HTTPS-kapcsolaton keresztül.|Módosítsa a git-konfigurációt a helyi gépen, hogy `postBuffer` minél nagyobb legyen. Például: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|A Node. js-alkalmazások üzembe helyezése egy _Package. JSON_ fájllal, amely további szükséges modulokat határoz meg.|A hiba `npm ERR!` előtt tekintse át a hibaüzeneteket, hogy a probléma további kontextusban legyen. A hiba ismert okai és a hozzájuk tartozó `npm ERR!` üzenetek a következők:<br /><br />**Helytelen formátumú Package. JSON fájl**:`npm ERR! Couldn't read dependencies.`<br /><br />A **natív modul nem rendelkezik bináris terjesztéssel a Windows rendszerhez**:<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />or <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>További források

- [A Project kudu dokumentációja](https://github.com/projectkudu/kudu/wiki)
- [Folyamatos üzembe helyezés Azure App Service](deploy-continuous-deployment.md)
- [Minta: Webalkalmazás létrehozása és kód üzembe helyezése helyi git-adattárból (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Minta: Webalkalmazás létrehozása és kód üzembe helyezése helyi git-adattárból (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
