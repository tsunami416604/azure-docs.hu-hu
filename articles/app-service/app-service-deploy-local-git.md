---
title: Helyi üzembe helyezés Git használatával az Azure App Service szolgáltatásban
description: Útmutató az Azure App Service helyi Git-telepítésének engedélyezése.
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
ms.openlocfilehash: 88cc9ff4979c2e6a4a14a7d531054c1a842deaf8
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34849803"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Helyi üzembe helyezés Git használatával az Azure App Service szolgáltatásban

Ez az útmutató útmutató bemutatja, hogyan telepítheti a kódot a [Azure App Service](app-service-web-overview.md) a Git-tárházat a helyi számítógépen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Kövesse a jelen útmutató Útmutató lépéseit:

* [Telepítse a Git szoftvert](http://www.git-scm.com/downloads).
* Egy helyi Git-tárház számára telepíteni kívánja kóddal karbantartása.

Egy minta-tárházat követéséhez használatához futtassa a következő parancsot a helyi terminálablakot:

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git-with-kudu-builds"></a>A Kudu buildek a helyi Git telepítése

Ahhoz, hogy az alkalmazás a Kudu build server a helyi Git-telepítésének legegyszerűbb módja a felhő rendszerhéj használata.

### <a name="create-a-deployment-user"></a>Üzembe helyező felhasználó létrehozása

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>A Kudu rendelkező helyi Git engedélyezése

A Kudu build kiszolgálóval az alkalmazás helyi Git-telepítésének engedélyezéséhez futtassa [ `az webapp deployment source config-local-git` ](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_local_git) a felhő rendszerhéj.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Ehelyett egy Git-kompatibilis alkalmazások létrehozásához futtassa az [ `az webapp create` ](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) és a felhő rendszerhéj a `--deployment-local-git` paraméter.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

A `az webapp create` parancs biztosítani fogja az alábbi kimenet hasonló:

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

### <a name="deploy-your-project"></a>A projekt telepítése

A _helyi terminálablakba_ visszatérve adjon hozzá egy távoli Azure-mappát a helyi Git-adattárhoz. Cserélje le  _\<URL-címe >_ URL-címét a Git távoli során kapott azonosítóértékeket [Git engedélyezése az alkalmazás](#enable-git-for-you-app).

```bash
git remote add azure <url>
```

A távoli Azure-mappához történő küldéssel helyezze üzembe az alkalmazást a következő paranccsal. Amikor a rendszer jelszót kér, ügyeljen arra, hogy az [Üzembe helyező felhasználó konfigurálása](#configure-a-deployment-user) szakaszban létrehozott jelszót adja meg, ne azt, amelyet az Azure Portalra való bejelentkezéshez használ.

```bash
git push azure master
```

Futásidejű-specifikus automatizálási a kimeneten, például az ASP.NET, az MSBuild fog látni `npm install` a Node.js, és `pip install` a Python. 

Keresse meg azt az alkalmazásnak, hogy ellenőrizze, hogy a tartalom központi telepítése.

## <a name="deploy-from-local-git-with-vsts-builds"></a>A helyi Git VSTS-buildek és központi telepítése

> [!NOTE]
> Az App Service segítségével hozzon létre a szükséges összeállítása, és felszabadíthatja a definíciók VSTS-fiókjában, az Azure-fiókjába rendelkeznie kell a szerepe **tulajdonos** az Azure-előfizetésben.
>

Ahhoz, hogy a helyi Git-telepítésének beállítása az alkalmazáshoz, a Kudu build kiszolgálóval, az alkalmazást a navigáljon a [Azure-portálon](https://portal.azure.com).

Kattintson a bal oldali navigációs az alkalmazás oldalát a **Telepítőközpontot** > **helyi Git** > **Folytatás**. 

![](media/app-service-deploy-local-git/portal-enable.png)

Kattintson a **VSTS folyamatos kézbesítési** > **továbbra is**.

![](media/app-service-deploy-local-git/vsts-build-server.png)

Az a **konfigurálása** lapon konfigurálja egy új VSTS-fiókot, vagy adjon meg egy meglévő fiókot. Ha befejezte, kattintson a **Folytatás**.

> [!NOTE]
> Ha egy meglévő VSTS-fiókkal, amely nem szerepel a használni kívánt, akkor [a VSTS-fiók összekötése az Azure-előfizetéshez](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

Az a **teszt** lapon, válassza ki, hogy betöltés tesztek engedélyezése, majd kattintson az **Folytatás**.

Attól függően a [tarifacsomag](/pricing/details/app-service/plans/) az App Service-csomag, szintén megjelenik egy **az átmeneti telepítéséhez** lap. Válasszon, hogy engedélyezze az üzembe helyezési, majd kattintson az **Folytatás**.

Az a **összegzés** lapon ellenőrizze a beállításokat, majd kattintson **Befejezés**.

A VSTS-fiók készen áll arra, hogy néhány percet vesz igénybe. Amikor készen áll a telepítőközpontot másolja át a Git-tárház URL-CÍMÉT.

![](media/app-service-deploy-local-git/vsts-repo-ready.png)

A _helyi terminálablakba_ visszatérve adjon hozzá egy távoli Azure-mappát a helyi Git-adattárhoz. Cserélje le  _\<URL-címe >_ a portáltól kapott utolsó lépése URL-címet.

```bash
git remote add vsts <url>
```

A távoli Azure-mappához történő küldéssel helyezze üzembe az alkalmazást a következő paranccsal. Amikor a rendszer kéri a Git hitelesítőadat-kezelő felügyeli, jelentkezzen be a visualstudio.com webhelyre felhasználó. További hitelesítési módszerek, lásd: [VSTS hitelesítéskonfiguráló áttekintése](/vsts/git/auth-overview?view=vsts).

```bash
git push vsts master
```

Miután a telepítés befejeződött, a build folyamatban található `https://<vsts_account>.visualstudio.com/<project_name>/_build` és a telepítési folyamatban `https://<vsts_account>.visualstudio.com/<project_name>/_release`.

Keresse meg azt az alkalmazásnak, hogy ellenőrizze, hogy a tartalom központi telepítése.

## <a name="troubleshooting-kudu-deployment"></a>A Kudu üzembe helyezés hibaelhárítása

Az alábbiakban gyakori hibák vagy problémák Git használatával az Azure App Service alkalmazás közzététele:

---
**Jelenség**: `Unable to access '[siteURL]': Failed to connect to [scmAddress]`

**OK**: Ez a hiba akkor fordulhat elő, ha az alkalmazás nem válik működőképessé.

**Megoldási**: Indítsa el az alkalmazást az Azure-portálon. Git-telepítés nem érhető el, ha a webalkalmazás le van állítva.

---
**Jelenség**: `Couldn't resolve host 'hostname'`

**OK**: Ez a hiba akkor fordulhat elő, ha a megadott létrehozásakor az "azure" távoli címadatokat helytelen volt.

**Megoldási**: használja a `git remote -v` paranccsal listát készíthet az összes távvezérlő, és a kapcsolódó URL-címe. Ellenőrizze, hogy helyes-e az "azure" távoli URL-CÍMÉT. Ha szükséges, távolítsa el és hozza létre újból a távoli, a helyes URL-cím használatával.

---
**Jelenség**: `No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`

**OK**: Ez a hiba akkor fordulhat elő, ha nem adja meg egy ágat során `git push`, vagy ha még nem állította be a `push.default` értéket `.gitconfig`.

**Megoldási**: futtatása `git push` ebben az esetben adja meg a főágba. Példa:

```bash
git push azure master
```

---
**Jelenség**: `src refspec [branchname] does not match any.`

**OK**: Ez a hiba akkor fordulhat elő, ha megpróbálja leküldése egy ágat a főadatbázison kívül az "Azure" távoli.

**Megoldási**: futtatása `git push` ebben az esetben adja meg a főágba. Példa:

```bash
git push azure master
```

---
**Jelenség**: `RPC failed; result=22, HTTP code = 5xx.`

**OK**: Ez a hiba akkor fordulhat elő, ha egy nagy git-tárház leküldéses HTTPS-KAPCSOLATON keresztül.

**Megoldási**: a helyi gépen a postBuffer nagyobb git konfigurációjának módosítása

```bash
git config --global http.postBuffer 524288000
```

---
**Jelenség**: `Error - Changes committed to remote repository but your web app not updated.`

**OK**: Ez a hiba akkor fordulhat elő, ha a Node.js alkalmazást telepít egy _package.json_ fájl, amely meghatározza a további szükséges modulokat.

**Megoldási**: "npm hiba!" további üzenetek Ez a hiba előtt legyenek naplózva, és a hiba esetén további környezetet biztosíthat. A következő ismert oka ezt a hibát, és a megfelelő "npm hiba!" üzenet:

* **Nem megfelelően formázott package.json fájl**: npm hiba! Nem lehetett olvasni a függőségek.
* **Natív modul, amely nem rendelkezik Windows bináris terjesztési**:

  * `npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1`

      VAGY
  * `npm ERR! [modulename@version] preinstall: \make || gmake\`

## <a name="additional-resources"></a>További források

* [Project Kudu dokumentációja](https://github.com/projectkudu/kudu/wiki)
* [Folyamatos üzembe helyezés az Azure App Service](app-service-continuous-deployment.md)
* [Minta: Webalkalmazás létrehozása és központi telepítése a kódot egy helyi Git-tárház (Azure CLI)](./scripts/app-service-cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
* [Példa: Webalkalmazás létrehozása és egy helyi Git-tárház (PowerShell) a kód telepítésére](./scripts/app-service-powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
