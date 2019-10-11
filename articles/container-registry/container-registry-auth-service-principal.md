---
title: Azure Container Registry hitelesítés egy egyszerű szolgáltatással
description: Hozzáférés biztosítása a privát tároló beállításjegyzékében lévő rendszerképekhez egy Azure Active Directory egyszerű szolgáltatásnév használatával.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 12/13/2018
ms.author: danlep
ms.openlocfilehash: 16ad37eaa50f0c3825d131338cc4a0abdc369978
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72262872"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Azure Container Registry hitelesítés egyszerű szolgáltatásokkal

Az Azure Active Directory (Azure AD) egyszerű szolgáltatásnév használatával biztosíthatja a tárolók rendszerképének `docker push` és a `pull` hozzáférését a tároló-beállításjegyzékhez. Egyszerű szolgáltatásnév használatával hozzáférést biztosíthat a "fej nélküli" szolgáltatásokhoz és alkalmazásokhoz.

## <a name="what-is-a-service-principal"></a>Mi az a szolgáltatásnév?

Az Azure AD- *szolgáltatások* hozzáférést biztosítanak az előfizetésében található Azure-erőforrásokhoz. Azt is megteheti, hogy egy egyszerű szolgáltatás felhasználói identitása egy szolgáltatáshoz, ahol a "szolgáltatás" bármely olyan alkalmazás, szolgáltatás vagy platform, amelynek hozzá kell férnie az erőforrásokhoz. Az egyszerű szolgáltatásnevet csak a megadott erőforrásokra vonatkozó hozzáférési jogosultságokkal konfigurálhatja. Ezután konfigurálja az alkalmazást vagy szolgáltatást úgy, hogy az egyszerű szolgáltatásnév hitelesítő adatait használja az erőforrásokhoz való hozzáféréshez.

Azure Container Registry kontextusában létrehozhat egy Azure AD-szolgáltatást, amely lekéréses, leküldéses és lekéréses, illetve egyéb engedélyekkel rendelkezik az Azure-beli privát beállításjegyzékhez. A teljes listát itt tekintheti meg: [Azure Container Registry szerepkörök és engedélyek](container-registry-roles.md).

## <a name="why-use-a-service-principal"></a>Miért érdemes egyszerű szolgáltatást használni?

Az Azure AD egyszerű szolgáltatásnév használatával hatókörön belüli hozzáférést biztosíthat a saját tároló-beállításjegyzékhez. Hozzon létre különböző egyszerű szolgáltatásokat az egyes alkalmazásokhoz vagy szolgáltatásokhoz, amelyek mindegyike testreszabott hozzáférési jogokkal rendelkezik a beállításjegyzékhez. Továbbá, mivel elkerülheti a hitelesítő adatok megosztását a szolgáltatások és az alkalmazások között, elforgathatja a hitelesítő adatokat, vagy visszavonhatja a hozzáférést csak az egyszerű szolgáltatásnév (és így az alkalmazás) közül.

Például úgy konfigurálhatja a webalkalmazást, hogy olyan egyszerű szolgáltatásnevet használjon, amely `pull` hozzáféréssel rendelkezik, míg a kiépítési rendszer egy egyszerű szolgáltatásnevet használ, amely `push` és `pull` hozzáféréssel is rendelkezik. Ha megváltoztatja az alkalmazás fejlesztését, elforgathatja a szolgáltatási elv hitelesítő adatait anélkül, hogy ez hatással lenne a Build rendszerre.

## <a name="when-to-use-a-service-principal"></a>Mikor kell szolgáltatásnevet használni

Egy egyszerű szolgáltatásnév használatával kell megadni a beállításjegyzék-hozzáférést a **fej nélküli forgatókönyvekben**. Ez azt jelenti, hogy minden olyan alkalmazás, szolgáltatás vagy parancsfájl, amely automatizált vagy más módon felügyelet nélkül leküld vagy lehívhatja a tárolók lemezképeit. Példa:

  * *Pull*: tárolók üzembe helyezése a beállításjegyzékből a Kubernetes, a DC/os és a Docker Swarm rendszerbe. Lekérheti a tároló-beállításjegyzékből a kapcsolódó Azure-szolgáltatásokat, például az [Azure Kubernetes Service (ak)](../aks/cluster-container-registry-integration.md), a [Azure Container Instances](container-registry-auth-aci.md), a [app Service](../app-service/index.yml), a [Batch](../batch/index.yml), a [Service Fabric](/azure/service-fabric/)és más szolgáltatásait is.

  * *Leküldés*: tároló-lemezképek létrehozása és továbbítása egy beállításjegyzékbe folyamatos integrációs és üzembe helyezési megoldások, például az Azure-folyamatok vagy a Jenkins használatával.

A beállításjegyzékhez való egyéni hozzáféréshez, például amikor manuálisan húz le egy tároló-rendszerképet a fejlesztői munkaállomásra, javasoljuk, hogy a beállításjegyzék-hozzáférés helyett saját [Azure ad-identitást](container-registry-authentication.md#individual-login-with-azure-ad) használjon (például az [az ACR login][az-acr-login]használatával).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>Mintaszkriptek

Az előző minta parancsfájlokat az Azure CLI-hez, a GitHubon, valamint a Azure PowerShell verzióihoz is megtalálja:

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>Hitelesítés az egyszerű szolgáltatással

Ha már rendelkezik egy egyszerű szolgáltatással, amely hozzáférést kapott a tároló-beállításjegyzékhez, a hitelesítő adatait megadhatja a "fej nélküli" szolgáltatásokhoz és alkalmazásokhoz, vagy megadhatja azokat a `docker login` paranccsal. Használja a következő értékeket:

* **Felhasználónév** – egyszerű szolgáltatásnév alkalmazásának azonosítója (más néven *ügyfél-azonosító*)
* **Jelszó** – egyszerű szolgáltatás jelszava (más néven *ügyfél-titok*)

Minden érték a `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` alakú GUID azonosító. 

> [!TIP]
> Egy egyszerű szolgáltatásnév jelszavát az az [ad SP reset-hitelesítőadats](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset) parancs futtatásával lehet újból előállítani.
>

### <a name="use-credentials-with-azure-services"></a>Hitelesítő adatok használata az Azure-szolgáltatásokkal

Az egyszerű szolgáltatás hitelesítő adatait bármely olyan Azure-szolgáltatáshoz használhatja, amely képes hitelesíteni az Azure Container registryt.  A beállításjegyzék rendszergazdai hitelesítő adatai helyett használja a szolgáltatás egyszerű hitelesítő adatait a különböző forgatókönyvek esetében.

A hitelesítő adatok használatával például lekérheti a rendszerképet egy Azure Container Registry-ből a [Azure Container Instancesba](container-registry-auth-aci.md).

### <a name="use-with-docker-login"></a>Használat a Docker-bejelentkezéssel

A `docker login` egy egyszerű szolgáltatásnév használatával is futtatható. A következő példában a szolgáltatás egyszerű alkalmazásának AZONOSÍTÓját a `$SP_APP_ID` környezeti változó adja át, és az `$SP_PASSWD` változóban található jelszót. A Docker hitelesítő adatainak kezelésével kapcsolatos ajánlott eljárásokért tekintse meg a [Docker login](https://docs.docker.com/engine/reference/commandline/login/) parancs referenciáját.

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

A bejelentkezést követően a Docker gyorsítótárazza a hitelesítő adatokat.

## <a name="next-steps"></a>Következő lépések

* Az Azure Container Registry szolgáltatással történő hitelesítéssel kapcsolatos egyéb forgatókönyvek [hitelesítésének áttekintését](container-registry-authentication.md) itt tekintheti meg.

* Az Azure Key Vault egy tároló-beállításjegyzékhez tartozó egyszerű hitelesítő adatok tárolására és lekérésére példaként tekintse meg az oktatóanyagot, amely [egy tároló lemezképének összeépítésére és üzembe helyezésére használható az ACR-feladatokkal](container-registry-tutorial-quick-task.md).

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
