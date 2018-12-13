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
ms.date: 06/05/2018
ms.author: dariagrigoriu;cephalin
ms.custom: seodec18
ms.openlocfilehash: 242eb906c95b373b2edd538be5f06756cac1e8c9
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53256509"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Helyi üzembe helyezés Git használatával az Azure App Service szolgáltatásban

Ez az útmutató bemutatja, hogyan helyezhet üzembe a kódot a [Azure App Service](app-service-web-overview.md) a helyi számítógépen Git-adattárból.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Ez az Útmutató lépéseit kövesse:

* [Telepítse a Git szoftvert](https://www.git-scm.com/downloads).
* Kód számára telepíteni kívánja a helyi Git-tárház karbantartása.

Hogy követni tudja a mintaadattár használatához a helyi terminálablakban futtassa a következő parancsot:

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git-with-kudu-builds"></a>Üzembe helyezés a Kudu-buildek használatával a helyi Gitből

Helyi Git üzemelő példányt az alkalmazás a Kudu-buildelési kiszolgáló engedélyezése a legegyszerűbb módja, hogy a Cloud Shellt használja.

### <a name="create-a-deployment-user"></a>Üzembe helyező felhasználó létrehozása

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Engedélyezi a helyi Git, a kudu használatával

Helyi Git üzemelő példányt az alkalmazás a Kudu-buildelési kiszolgáló engedélyezéséhez futtassa [ `az webapp deployment source config-local-git` ](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) a Cloud shellben.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Ehelyett egy Git-kompatibilis alkalmazások létrehozásához futtassa [ `az webapp create` ](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) az a Cloud shellben a `--deployment-local-git` paraméter.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

A `az webapp create` parancs adjon meg a következő kimenethez hasonló:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="deploy-your-project"></a>A projekt üzembe helyezése

A _helyi terminálablakba_ visszatérve adjon hozzá egy távoli Azure-mappát a helyi Git-adattárhoz. Cserélje le  _\<URL-címe >_ webhelyről származó alkalmazásazonosítóra távoli Git URL-címével [Git engedélyezése az alkalmazás](#enable-git-for-you-app).

```bash
git remote add azure <url>
```

A távoli Azure-mappához történő küldéssel helyezze üzembe az alkalmazást a következő paranccsal. Amikor a rendszer jelszót kér, ügyeljen arra, hogy az [Üzembe helyező felhasználó konfigurálása](#configure-a-deployment-user) szakaszban létrehozott jelszót adja meg, ne azt, amelyet az Azure Portalra való bejelentkezéshez használ.

```bash
git push azure master
```

A kimenetben, például az ASP.NET, MSBuild futtatókörnyezet-specifikus automation látni `npm install` a node.js-ben, és `pip install` Pythonhoz készült. 

Keresse meg az alkalmazásba és ellenőrizze, hogy telepítve van-e a tartalmat.

## <a name="deploy-from-local-git-with-azure-devops-services-builds"></a>Üzembe helyezés a helyi Gitből az Azure DevOps-szolgáltatásokkal-buildek használatával

> [!NOTE]
> Az App Service a szükséges Azure-folyamatok létrehozására a szervezet az Azure DevOps-szolgáltatásokkal, az Azure-fiókkal kell rendelkeznie szerepe **tulajdonos** az Azure-előfizetésében.
>

Ahhoz, hogy az alkalmazás a Kudu-buildelési kiszolgáló tartozó helyi Git üzemelő példányt, keresse meg az alkalmazás a [az Azure portal](https://portal.azure.com).

Az alkalmazás lapjának bal oldali navigációs sávján kattintson **üzembe helyezési központ** > **helyi Git** > **Folytatás**. 

![](media/app-service-deploy-local-git/portal-enable.png)

Kattintson a **Services az Azure DevOps, a folyamatos teljesítés** > **továbbra is**.

![](media/app-service-deploy-local-git/vsts-build-server.png)

Az a **konfigurálása** oldalon konfigurálása az Azure DevOps-szolgáltatásokkal új szervezetet, vagy adjon meg egy már meglévő szervezet. Ha befejezte, kattintson a **Folytatás**.

> [!NOTE]
> Ha egy már meglévő Azure DevOps-szolgáltatásokkal szervezet, amely nem szerepel a használni kívánt, kell [az Azure DevOps-szolgáltatásokkal szervezet összekapcsolása az Azure-előfizetés](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

Az a **teszt** lapon, döntse el, hogy engedélyezze a terhelési teszteket, majd kattintson a **Folytatás**.

Attól függően, a [tarifacsomag](https://azure.microsoft.com/pricing/details/app-service/plans/) az App Service-csomag is megjelenhetnek egy **üzembe helyezés átmeneti környezetbe** lapot. Döntse el, hogy az üzembe helyezési pontok engedélyezése, majd kattintson a **Folytatás**.

Az a **összefoglalás** oldalon ellenőrizze a beállításokat, kattintson a **Befejezés**.

Készen áll az Azure DevOps-szolgáltatásokkal a szervezet néhány percet vesz igénybe. Amikor elkészült, az üzembe helyezési központ másoljon át a Git-adattár URL-címe.

![](media/app-service-deploy-local-git/vsts-repo-ready.png)

A _helyi terminálablakba_ visszatérve adjon hozzá egy távoli Azure-mappát a helyi Git-adattárhoz. Cserélje le  _\<URL-címe >_ az URL-címmel, az utolsó lépésében kapott.

```bash
git remote add vsts <url>
```

A távoli Azure-mappához történő küldéssel helyezze üzembe az alkalmazást a következő paranccsal. Amikor a Git hitelesítőadat-kezelője kéri, jelentkezzen be a visualstudio.com felhasználói. További hitelesítési módszereket, lásd: [Azure DevOps-szolgáltatásokkal hitelesítést áttekintése](/vsts/git/auth-overview?view=vsts).

```bash
git push vsts master
```

Üzembe helyezés befejeződése után a build folyamatának címen található `https://<vsts_account>.visualstudio.com/<project_name>/_build` és az üzembe helyezés folyamatban `https://<vsts_account>.visualstudio.com/<project_name>/_release`.

Keresse meg az alkalmazásba és ellenőrizze, hogy telepítve van-e a tartalmat.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshooting-kudu-deployment"></a>Kudu üzembe helyezés hibaelhárítása

Az alábbiakban gyakori hibák vagy problémák az Azure App Service-alkalmazások közzététele a Git használata esetén:

---
**Jelenség**: `Unable to access '[siteURL]': Failed to connect to [scmAddress]`

**OK**: Ez a hiba akkor fordulhat elő, ha az alkalmazás működése nem áll.

**Feloldási**: Indítsa el az alkalmazást az Azure Portalon. Git-telepítés nem érhető el, ha a webalkalmazás leállt.

---
**Jelenség**: `Couldn't resolve host 'hostname'`

**OK**: Ez a hiba akkor fordulhat elő, ha a megadott létrehozásakor az "azure" távoli címadatok helytelen volt.

**Feloldási**: Használja a `git remote -v` paranccsal listát készíthet az összes távoli, és a társított URL-címe. Győződjön meg arról, hogy helyesen szerepel-e az "azure" távoli URL-CÍMÉT. Ha szükséges, távolítsa el, és hozza létre újból a távoli, a helyes URL-cím használatával.

---
**Jelenség**: `No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`

**OK**: Ez a hiba akkor fordulhat elő, ha nem ad meg egy ágat során `git push`, vagy ha nem állított be a `push.default` értékét `.gitconfig`.

**Feloldási**: Futtatás `git push` újra, adja meg a master ágról. Példa:

```bash
git push azure master
```

---
**Jelenség**: `src refspec [branchname] does not match any.`

**OK**: Ez a hiba akkor fordulhat elő, ha megpróbálja a főadatbázison kívül egyik ágára való leküldés az "Azure" a távoli mappához küldéssel.

**Feloldási**: Futtatás `git push` újra, adja meg a master ágról. Példa:

```bash
git push azure master
```

---
**Jelenség**: `RPC failed; result=22, HTTP code = 5xx.`

**OK**: Ez a hiba akkor fordulhat elő, ha megpróbál egy nagy git-tárház leküldéses HTTPS-kapcsolaton keresztül.

**Feloldási**: A helyi gépen, hogy a postBuffer nagyobb a git-konfiguráció módosítása

```bash
git config --global http.postBuffer 524288000
```

---
**Jelenség**: `Error - Changes committed to remote repository but your web app not updated.`

**OK**: Ez a hiba akkor fordulhat elő, ha, a Node.js-alkalmazás üzembe helyezése egy _package.json_ fájlt, amely meghatározza a további szükséges modulokat.

**Feloldási**: A(z) "npm ERR!' a további üzeneteket Ez a hiba előtt be kell jelentkeznie, és a hibával kapcsolatban további környezet tud biztosítani. Az alábbiakban néhány ismert oka ezt a hibát, és a megfelelő "npm ERR!" üzenet:

* **Helytelen formátumú package.json fájl**: npm hiba! Nem lehetett olvasni a függőségek.
* **Natív modul, hogy egy nem bináris terjesztési Windows**:

  * `npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1`

      VAGY
  * `npm ERR! [modulename@version] preinstall: \make || gmake\`

## <a name="additional-resources"></a>További források

* [Projekt Kudu-dokumentáció](https://github.com/projectkudu/kudu/wiki)
* [Folyamatos üzembe helyezés az Azure App Service-ben](app-service-continuous-deployment.md)
* [Minta: Webes alkalmazás létrehozása és kód üzembe helyezése a helyi Git-tárház (az Azure parancssori felület)](./scripts/app-service-cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
* [Minta: Webes alkalmazás létrehozása és kód üzembe helyezése a helyi Git-tárház (PowerShell)](./scripts/app-service-powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
