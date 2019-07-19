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
ms.openlocfilehash: 97c45a009b155eea7bc61a9dd337090b9e3c1b42
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309948"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Azure Container Registry hitelesítés egyszerű szolgáltatásokkal

Egy Azure Active Directory (Azure ad) egyszerű szolgáltatásnév használatával biztosíthatja a tárolók `docker push` rendszerképét `pull` , és hozzáférhet a tároló-beállításjegyzékhez. Egyszerű szolgáltatásnév használatával hozzáférést biztosíthat a "fej nélküli" szolgáltatásokhoz és alkalmazásokhoz.

## <a name="what-is-a-service-principal"></a>Mi az a szolgáltatásnév?

Az Azure AD- *szolgáltatások* hozzáférést biztosítanak az előfizetésében található Azure-erőforrásokhoz. Azt is megteheti, hogy egy egyszerű szolgáltatás felhasználói identitása egy szolgáltatáshoz, ahol a "szolgáltatás" bármely olyan alkalmazás, szolgáltatás vagy platform, amelynek hozzá kell férnie az erőforrásokhoz. Az egyszerű szolgáltatásnevet csak a megadott erőforrásokra vonatkozó hozzáférési jogosultságokkal konfigurálhatja. Ezután konfigurálja az alkalmazást vagy szolgáltatást úgy, hogy az egyszerű szolgáltatásnév hitelesítő adatait használja az erőforrásokhoz való hozzáféréshez.

Azure Container Registry kontextusában létrehozhat egy Azure AD-szolgáltatást, amely lekéréses, leküldéses és lekéréses, illetve egyéb engedélyekkel rendelkezik az Azure-beli privát beállításjegyzékhez. A teljes listát itt tekintheti meg: [Azure Container Registry szerepkörök és engedélyek](container-registry-roles.md).

## <a name="why-use-a-service-principal"></a>Miért érdemes egyszerű szolgáltatást használni?

Az Azure AD egyszerű szolgáltatásnév használatával hatókörön belüli hozzáférést biztosíthat a saját tároló-beállításjegyzékhez. Az egyes alkalmazásokhoz és szolgáltatásokhoz különböző egyszerű szolgáltatásokat hozhat létre, amelyek mindegyike testreszabott hozzáférési jogosultságokkal rendelkezik a beállításjegyzékhez. Továbbá, mivel elkerülheti a hitelesítő adatok megosztását a szolgáltatások és az alkalmazások között, elforgathatja a hitelesítő adatokat, vagy visszavonhatja a hozzáférést csak az egyszerű szolgáltatásnév (és így az alkalmazás) közül.

A webalkalmazás például használhat olyan egyszerű szolgáltatást, amely csak `pull` képhozzáférést biztosít, míg a Build `push` -rendszer használhatja a szolgáltatást és `pull` a hozzáférést is biztosító egyszerű szolgáltatásnevet. Ha megváltoztatja az alkalmazás fejlesztését, elforgathatja a szolgáltatási elv hitelesítő adatait anélkül, hogy ez hatással lenne a Build rendszerre.

## <a name="when-to-use-a-service-principal"></a>Mikor kell szolgáltatásnevet használni

Egy egyszerű szolgáltatásnév használatával kell megadni a beállításjegyzék-hozzáférést a **fej**nélküli forgatókönyvekben. Ez azt jelenti, hogy minden olyan alkalmazás, szolgáltatás vagy parancsfájl, amely automatizált vagy más módon felügyelet nélkül leküld vagy lehívhatja a tárolók lemezképeit.

A beállításjegyzékhez való egyéni hozzáféréshez, például amikor manuálisan húz le egy tároló-rendszerképet a fejlesztői munkaállomásra, ehelyett saját [Azure ad](container-registry-authentication.md#individual-login-with-azure-ad) -identitást kell használnia a beállításjegyzékhez való hozzáféréshez (például az [az ACR login][az-acr-login]használatával).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="sample-scripts"></a>Mintaszkriptek

Az előző minta parancsfájlokat az Azure CLI-hez a GitHubon, valamint a Azure PowerShell-verziókhoz is megtalálhatja:

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>További lépések

Ha már rendelkezik egy egyszerű szolgáltatással, amely hozzáférést kapott a tároló-beállításjegyzékhez, használhatja a hitelesítő adatait az alkalmazásokban és a szolgáltatásokban az alárendelt beállításjegyzékbeli interakcióhoz. Az egyszerű szolgáltatás hitelesítő adatait bármely olyan Azure-szolgáltatáshoz használhatja, amely képes hitelesíteni az Azure Container registryt. Példák erre vonatkozóan:

* [Hitelesítés Azure Container Registry az Azure Kubernetes Service-ből (ak)](container-registry-auth-aks.md)
* [Hitelesítés Azure Container Registry Azure Container Instances (ACI) használatával](container-registry-auth-aci.md)

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
