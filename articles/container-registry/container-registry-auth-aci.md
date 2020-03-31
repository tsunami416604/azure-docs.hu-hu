---
title: Hozzáférés tárolópéldányokból
description: Megtudhatja, hogyan biztosíthat hozzáférést a rendszerképekhez a privát tároló beállításjegyzékében az Azure Container Instances egy Azure Active Directory egyszerű szolgáltatás használatával.
ms.topic: article
ms.date: 04/23/2018
ms.openlocfilehash: b1bc8119c495dea99c6bdc4923db198d041a1e9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456519"
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-instances"></a>Hitelesítés az Azure Container Registry használatával az Azure Container Instances-ből

Az Azure Active Directory (Azure AD) egyszerű szolgáltatás használatával hozzáférést biztosíthat a privát tároló-beállításjegyzékekhez az Azure Container Registry-ben.

Ebben a cikkben megtudhatja, hogy hozzon létre és konfiguráljon egy Azure AD szolgáltatásnév *lekéréses* engedélyekkel a beállításjegyzékbe. Ezután elindítja a tárolót az Azure Container Instances (ACI), amely lekéri a lemezképet a privát beállításjegyzékből, a szolgáltatásnév a hitelesítéshez.

## <a name="when-to-use-a-service-principal"></a>Mikor kell egyszerű szolgáltatást használni?

Az ACI egyszerű hitelesítéséhez használjon egyszerű szolgáltatást **fej nélküli forgatókönyvekben,** például olyan alkalmazásokban vagy szolgáltatásokban, amelyek automatikus vagy más módon felügyelet nélküli tárolópéldányokat hoznak létre.

Ha például egy automatikus parancsfájlfut éjszakánként, és létrehoz egy [feladat-alapú tárolópéldányt](../container-instances/container-instances-restart-policy.md) bizonyos adatok feldolgozásához, akkor használhatja a szolgáltatás névjegyzék bekéréses engedélyekkel. Ezután forgassa el az egyszerű szolgáltatás hitelesítő adatait, vagy teljesen visszavonhatja a hozzáférést anélkül, hogy más szolgáltatásokat és alkalmazásokat érintene.

A szolgáltatásnévi tagokat akkor is használni kell, ha a [beállításjegyzék-rendszergazda felhasználója](container-registry-authentication.md#admin-account) le van tiltva.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="authenticate-using-the-service-principal"></a>Hitelesítés az egyszerű szolgáltatás használatával

Ha egy tárolót szeretne elindítani az Azure Container Instances szolgáltatásegyszerű használatával, adja meg a azonosítóját `--registry-username`és jelszavát. `--registry-password`

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerregistry.azurecr.io/myimage:v1 \
    --registry-login-server mycontainerregistry.azurecr.io \
    --registry-username <service-principal-ID> \
    --registry-password <service-principal-password>
```

## <a name="sample-scripts"></a>Mintaszkriptek

Az Azure CLI előző mintaparancsfájljait a GitHubon, valamint az Azure PowerShell verzióit is megtalálhatja:

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>További lépések

Az alábbi cikkek további részleteket tartalmaznak a szolgáltatástagok és az ACR-ekkel való együttműködésről:

* [Azure Container Registry hitelesítés egyszerű szolgáltatásokkal](container-registry-auth-service-principal.md)
* [Hitelesítés az Azure Kubernetes-szolgáltatás (AKS) Azure Container Registry szolgáltatásával](../aks/cluster-container-registry-integration.md)

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
