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
ms.openlocfilehash: bee8b801f46c0018e75d58f941470adcc271daf0
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70032374"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Azure Container Registry hitelesítés egyszerű szolgáltatásokkal

Egy Azure Active Directory (Azure ad) egyszerű szolgáltatásnév használatával biztosíthatja a tárolók `docker push` rendszerképét `pull` , és hozzáférhet a tároló-beállításjegyzékhez. Egyszerű szolgáltatásnév használatával hozzáférést biztosíthat a "fej nélküli" szolgáltatásokhoz és alkalmazásokhoz.

## <a name="what-is-a-service-principal"></a>Mi az a szolgáltatásnév?

Az Azure AD- *szolgáltatások* hozzáférést biztosítanak az előfizetésében található Azure-erőforrásokhoz. Azt is megteheti, hogy egy egyszerű szolgáltatás felhasználói identitása egy szolgáltatáshoz, ahol a "szolgáltatás" bármely olyan alkalmazás, szolgáltatás vagy platform, amelynek hozzá kell férnie az erőforrásokhoz. Az egyszerű szolgáltatásnevet csak a megadott erőforrásokra vonatkozó hozzáférési jogosultságokkal konfigurálhatja. Ezután konfigurálja az alkalmazást vagy szolgáltatást úgy, hogy az egyszerű szolgáltatásnév hitelesítő adatait használja az erőforrásokhoz való hozzáféréshez.

Azure Container Registry kontextusában létrehozhat egy Azure AD-szolgáltatást, amely lekéréses, leküldéses és lekéréses, illetve egyéb engedélyekkel rendelkezik az Azure-beli privát beállításjegyzékhez. A teljes listát itt tekintheti meg: [Azure Container Registry szerepkörök és engedélyek](container-registry-roles.md).

## <a name="why-use-a-service-principal"></a>Miért érdemes egyszerű szolgáltatást használni?

Az Azure AD egyszerű szolgáltatásnév használatával hatókörön belüli hozzáférést biztosíthat a saját tároló-beállításjegyzékhez. Hozzon létre különböző egyszerű szolgáltatásokat az egyes alkalmazásokhoz vagy szolgáltatásokhoz, amelyek mindegyike testreszabott hozzáférési jogokkal rendelkezik a beállításjegyzékhez. Továbbá, mivel elkerülheti a hitelesítő adatok megosztását a szolgáltatások és az alkalmazások között, elforgathatja a hitelesítő adatokat, vagy visszavonhatja a hozzáférést csak az egyszerű szolgáltatásnév (és így az alkalmazás) közül.

Például úgy konfigurálhatja a webalkalmazást, hogy olyan egyszerű szolgáltatásnevet használjon, amely csak `pull` képhozzáférést biztosít, míg a Build rendszer olyan szolgáltatásnevet használ, amely egyaránt `push` és `pull` hozzáféréssel rendelkezik. Ha megváltoztatja az alkalmazás fejlesztését, elforgathatja a szolgáltatási elv hitelesítő adatait anélkül, hogy ez hatással lenne a Build rendszerre.

## <a name="when-to-use-a-service-principal"></a>Mikor kell szolgáltatásnevet használni

Egy egyszerű szolgáltatásnév használatával kell megadni a beállításjegyzék-hozzáférést a **fej**nélküli forgatókönyvekben. Ez azt jelenti, hogy minden olyan alkalmazás, szolgáltatás vagy parancsfájl, amely automatizált vagy más módon felügyelet nélkül leküld vagy lehívhatja a tárolók lemezképeit. Példa:

  * *Pull*: Tárolók üzembe helyezése a beállításjegyzékből a Kubernetes, a DC/OS és a Docker Swarm rendszerbe. Lekérheti a tároló-beállításjegyzékből a kapcsolódó Azure-szolgáltatásokat, például az [Azure Kubernetes Service (ak)](container-registry-auth-aks.md), a [Azure Container Instances](container-registry-auth-aci.md), a [app Service](../app-service/index.yml), a [Batch](../batch/index.yml), a [Service Fabric](/azure/service-fabric/)és más szolgáltatásait is.

  * Leküldés: Hozzon létre tároló-lemezképeket, és küldje el őket egy beállításjegyzékbe folyamatos integráció és üzembe helyezési megoldások, például az Azure-folyamatok vagy a Jenkins használatával.

A beállításjegyzékhez való egyéni hozzáféréshez, például amikor manuálisan húz le egy tároló-rendszerképet a fejlesztői munkaállomásra, javasoljuk, hogy a beállításjegyzék-hozzáférés helyett saját [Azure ad](container-registry-authentication.md#individual-login-with-azure-ad) -identitást használjon (például az [az ACR login][az-acr-login]használatával).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>Mintaszkriptek

Az előző minta parancsfájlokat az Azure CLI-hez, a GitHubon, valamint a Azure PowerShell verzióihoz is megtalálja:

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>Hitelesítés az egyszerű szolgáltatással

Ha már rendelkezik egy olyan egyszerű szolgáltatással, amely hozzáférést kapott a tároló-beállításjegyzékhez, a hitelesítő adatait megadhatja a "fej nélküli" szolgáltatásokhoz és alkalmazásokhoz, vagy `docker login` megadhatja azokat a paranccsal. Használja a következő értékeket:

* **Felhasználónév** – egyszerű szolgáltatásnév alkalmazásának azonosítója (más néven *ügyfél-azonosító*)
* **Jelszó** – egyszerű szolgáltatás jelszava (más néven *ügyfél-titok*)

Minden érték az űrlap `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`GUID-azonosítója. 

> [!TIP]
> Egy egyszerű szolgáltatásnév jelszavát az az [ad SP reset-hitelesítőadats](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset) parancs futtatásával lehet újból előállítani.
>

### <a name="use-credentials-with-azure-services"></a>Hitelesítő adatok használata az Azure-szolgáltatásokkal

Az egyszerű szolgáltatás hitelesítő adatait bármely olyan Azure-szolgáltatáshoz használhatja, amely képes hitelesíteni az Azure Container registryt. Példák erre vonatkozóan:

* [Hitelesítés Azure Container Registry az Azure Kubernetes Service-ből (ak)](container-registry-auth-aks.md)
* [Hitelesítés Azure Container Registry Azure Container Instances (ACI) használatával](container-registry-auth-aci.md)

### <a name="use-with-docker-login"></a>Használat a Docker-bejelentkezéssel

A szolgáltatást egyszerű szolgáltatásnév `docker login` használatával is futtathatja. A következő példában a szolgáltatás egyszerű alkalmazásának azonosítója a környezeti változóban `$SP_APP_ID`, a változóban `$SP_PASSWD`pedig a jelszót adja át. A Docker hitelesítő adatainak kezelésével kapcsolatos ajánlott eljárásokért tekintse meg a [Docker login](https://docs.docker.com/engine/reference/commandline/login/) parancs referenciáját.

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

A bejelentkezést követően a Docker gyorsítótárazza a hitelesítő adatokat.

## <a name="next-steps"></a>További lépések

* Az Azure [](container-registry-authentication.md) Container Registry szolgáltatással történő hitelesítéssel kapcsolatos egyéb forgatókönyvek hitelesítésének áttekintését itt tekintheti meg.

* Az Azure Key Vault egy tároló-beállításjegyzékhez tartozó egyszerű hitelesítő adatok tárolására és lekérésére példaként tekintse meg az oktatóanyagot, amely [egy tároló lemezképének összeépítésére és üzembe helyezésére](container-registry-tutorial-quick-task.md)használható az ACR-feladatokkal.

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
