---
title: Üzembe helyezés a helyi Git-adattár – Azure App Service-ben
description: Ismerje meg, hogyan engedélyezheti a helyi Git üzemelő példányt az Azure App Service.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2019
ms.author: dariagrigoriu;cephalin
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: fa961e43408fed014be2266c14c7972d39435b97
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836923"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Helyi Git üzemelő példányt az Azure App Service-ben

Ez az útmutató bemutatja, hogyan helyezhet üzembe az alkalmazást, hogy [Azure App Service](overview.md) a helyi számítógépen Git-adattárból.

## <a name="prerequisites"></a>Előfeltételek

Ez az Útmutató lépéseit kövesse:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Telepítse a Git szoftvert](https://www.git-scm.com/downloads).
  
- Helyi Git-tárház számára telepíteni kívánja kóddal rendelkeznek. A mintaadattár letöltéséhez a helyi terminálablakban futtassa a következő parancsot:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-build-server"></a>Üzembe helyezés a Kudu-buildelési kiszolgáló

A helyi Git üzemelő példányt az alkalmazás az Kudu App Service-build kiszolgálóval engedélyezése legegyszerűbben az Azure Cloud Shell használatához. 

### <a name="configure-a-deployment-user"></a>Üzembe helyező felhasználó konfigurálása

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>Az üzembe helyezési URL-Címének lekéréséhez

Az URL-cím egy meglévő alkalmazáshoz tartozó helyi Git üzemelő példányt engedélyezéséhez futtassa [ `az webapp deployment source config-local-git` ](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) a Cloud shellben. Cserélje le \<alkalmazás-neve > és \<csoport-neve > az alkalmazás és az Azure-erőforráscsoport nevére.

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

Vagy hozzon létre egy új Git-kompatibilis alkalmazást, futtassa a [ `az webapp create` ](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) az a Cloud shellben a `--deployment-local-git` paraméter. Cserélje le \<alkalmazás-neve >, \<csoport neve >, és \<terv-name > az új Git-alkalmazás, az Azure-erőforráscsoportot és az Azure App Service-csomag nevét.

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

Vagy a parancs visszaadja az egy URL-címet, például: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Az URL-cím használatával helyezze üzembe az alkalmazást a következő lépésben.

A fiókszintű URL-cím helyett is engedélyezheti helyi Git alkalmazásszintű hitelesítő adatok használatával. Az Azure App Service automatikusan létrehozza a minden alkalmazás ezeket a hitelesítő adatokat. 

Az alkalmazás hitelesítő adatainak lekérése a következő parancsot a Cloud shellben való futtatásával. Cserélje le \<alkalmazás-neve > és \<csoport-neve > az alkalmazás nevét és az Azure erőforráscsoport-nevet.

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

Ezen az URL-cím, amely helyezze üzembe az alkalmazást a következő lépésben adja vissza.

### <a name="deploy-the-web-app"></a>A webes alkalmazás üzembe helyezése

1. Nyisson meg egy helyi terminálablakban a helyi Git-tárházba, és adjon hozzá egy távoli Azure-mappát. A következő parancsban cserélje le a \<URL-címe >-telepítés felhasználó-specifikus URL-CÍMÉT, illetve az előző lépésből származó alkalmazás-specifikus URL-CÍMÉT.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Az Azure-ban a távoli mappához küldéssel a `git push azure master`. 
   
1. Az a **a Git Credential Manager** ablakban adja meg a [központi telepítés felhasználói jelszó](#configure-a-deployment-user), nem kell az Azure bejelentkezési jelszót.
   
1. Tekintse át a kimenetet. Futtatókörnyezet-specifikus automation, például az ASP.NET, MSBuild látni `npm install` a node.js-ben, és `pip install` Pythonhoz készült. 
   
1. Keresse meg az alkalmazás az Azure Portalon ellenőrizze, hogy telepítve van-e a tartalmat.

## <a name="deploy-with-azure-pipelines-builds"></a>Üzembe helyezés Azure folyamatok buildek

Ha a fiókja rendelkezik a szükséges engedélyekkel, állíthat be Azure folyamatok (előzetes verzió) engedélyezése a Git helyi üzemelő példánya az alkalmazáshoz. 

- Az Azure-fiókkal rendelkeznie kell írni az Azure Active Directory és a egy szolgáltatás létrehozása. 
  
- Az Azure-fiókkal kell rendelkeznie a **tulajdonosa** szerepkört az Azure-előfizetésében.

- Az Azure DevOps-projekt létrehozásához használni szeretne a rendszergazdának kell lennie.

Helyi Git üzemelő példányt az alkalmazás az Azure-folyamatok (előzetes verzió) engedélyezése:

1. Keresse meg az Azure App Service-oldalra a [az Azure portal](https://portal.azure.com), és válassza ki **Telepítőközpontot** a bal oldali menüben.
   
1. Az a **üzembe helyezési központ** lapon jelölje be **helyi Git**, majd válassza ki **Folytatás**. 
   
   ![Helyi Git, majd válassza ki és folytatás](media/app-service-deploy-local-git/portal-enable.png)
   
1. Az a **Build szolgáltató** lapon jelölje be **Azure folyamatok (előzetes verzió)** , majd válassza ki **Folytatás**. 
   
   ![Válassza ki az Azure-folyamatok (előzetes verzió), és kattintson a folytatás.](media/app-service-deploy-local-git/pipeline-builds.png)

1. Az a **konfigurálása** lapon konfigurálása az Azure DevOps új szervezetet, vagy adjon meg egy már meglévő szervezet, és válassza ki **Folytatás**.
   
   > [!NOTE]
   > Ha a meglévő Azure DevOps-szervezet nem szerepel a listán, szükség lehet az Azure-előfizetéshez mutat. További információkért lásd: [határozza meg a CD-kiadási folyamatok](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
   
1. Az App Service-csomag függően [tarifacsomag](https://azure.microsoft.com/pricing/details/app-service/plans/), látni egy **üzembe helyezés átmeneti környezetbe** lapot. Válassza ki, hogy e [üzembe helyezési pontok engedélyezése](deploy-staging-slots.md), majd válassza ki **Folytatás**.
   
1. Az a **összefoglalás** lapon tekintse át a beállításokat, és válassza ki **Befejezés**.
   
1. Amikor készen áll az Azure-folyamat, másolja a Git adattár URL-címe az az **üzembe helyezési központ** a következő lépésben használni kívánt lapot. 
   
   ![Másolja a Git-adattár URL-címe](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. A helyi terminálablakban adjon hozzá egy távoli Azure helyi Git-adattárban. A parancsban cserélje le a \<URL-címe > Git-adattár, amely az előző lépésből származó URL-címét.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Az Azure-ban a távoli mappához küldéssel a `git push azure master`. 
   
1. Az a **a Git Credential Manager** lapon, jelentkezzen be a visualstudio.com felhasználónevét. Más hitelesítési módszerekhez, lásd: [Azure DevOps-szolgáltatásokkal hitelesítést áttekintése](/vsts/git/auth-overview?view=vsts).
   
1. Üzembe helyezés befejeződése után megtekintheti a build folyamatának, `https://<azure_devops_account>.visualstudio.com/<project_name>/_build`, és az üzembe helyezés folyamatban `https://<azure_devops_account>.visualstudio.com/<project_name>/_release`.
   
1. Keresse meg az alkalmazás az Azure Portalon ellenőrizze, hogy telepítve van-e a tartalmat.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>Üzembe helyezés hibaelhárítása

Ha a Git segítségével az Azure App Service-alkalmazások közzététele az alábbi általános hibaüzenetek jelenhetnek meg:

|Message|Ok|Megoldás:
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|Az alkalmazás működése nem áll.|Indítsa el az alkalmazást az Azure Portalon. Git-telepítés nem érhető el, ha a webalkalmazás leállt.|
|`Couldn't resolve host 'hostname'`|Az "azure" távoli cím információi nem megfelelő.|Használja a `git remote -v` paranccsal listát készíthet az összes távoli, és a társított URL-címe. Győződjön meg arról, hogy helyesen szerepel-e az "azure" távoli URL-CÍMÉT. Ha szükséges, távolítsa el, és hozza létre újból a távoli, a helyes URL-cím használatával.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`|Nem adott meg egy ágat során `git push`, vagy nem állított be a `push.default` értékét `.gitconfig`.|Futtatás `git push` újra, adja meg a főágban: `git push azure master`.|
|`src refspec [branchname] does not match any.`|A főadatbázison kívül egyik ágára való leküldés az "Azure" a távoli mappához küldéssel próbált meg.|Futtatás `git push` újra, adja meg a főágban: `git push azure master`.|
|`RPC failed; result=22, HTTP code = 5xx.`|Ez a hiba akkor fordulhat elő, ha megpróbál egy nagy git-tárház leküldéses HTTPS-kapcsolaton keresztül.|A git-konfigurációt a helyi gépen, győződjön meg arról, hogy módosítsa a `postBuffer` nagyobb méretű. Például: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|Egy Node.js-alkalmazás az üzembe helyezett egy _package.json_ fájlt, amely meghatározza a további szükséges modulokat.|Tekintse át a `npm ERR!` hibaüzenetek a hibával kapcsolatban további környezet esetében ez a hiba előtt. Az alábbiakban a ezt a hibát, és a megfelelő ismert okait `npm ERR!` üzenetek:<br /><br />**Helytelen formátumú package.json fájl**: `npm ERR! Couldn't read dependencies.`<br /><br />**Natív modul a Windows nem rendelkezik egy bináris terjesztési**:<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />vagy <br />a(z) "npm hiba! [modulename@version] előtelepítését: \make || gmake\`|

## <a name="additional-resources"></a>További források

- [Projekt Kudu-dokumentáció](https://github.com/projectkudu/kudu/wiki)
- [Folyamatos üzembe helyezés az Azure App Service-ben](deploy-continuous-deployment.md)
- [Minta: Webalkalmazás létrehozása és kód üzembe helyezése a helyi Git-tárház (az Azure parancssori felület)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Minta: Webalkalmazás létrehozása és kód üzembe helyezése a helyi Git-tárház (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
