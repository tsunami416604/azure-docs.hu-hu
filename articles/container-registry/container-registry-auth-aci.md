---
title: Az Azure tároló-beállításjegyzéket az Azure Container Instances a hitelesítéshez
description: Ismerje meg, hogyan elérést biztosíthat a privát tárolójegyzékben található rendszerképek az Azure Container Instances szolgáltatásban az Azure Active Directory egyszerű szolgáltatás használatával.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 04/23/2018
ms.author: danlep
ms.openlocfilehash: 32b1788d73e1c323d93b40b778bc64a1ba45c4ad
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48855833"
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-instances"></a>Az Azure tároló-beállításjegyzéket az Azure Container Instances a hitelesítéshez

Az Azure Active Directory (Azure AD) egyszerű szolgáltatás használatával hozzáférést biztosítanak a privát tárolójegyzékek az Azure Container Registryben.

Ebből a cikkből megtudhatja, létrehozni és konfigurálni egy Azure AD-szolgáltatásnevet a *lekéréses* engedélyeket a tárolójegyzékbe. Ezt követően először egy tárolót az Azure Container Instances (ACI), amely lekéri a rendszerképet a privát tárolójegyzékből az egyszerű szolgáltatás használatával a hitelesítéshez.

## <a name="when-to-use-a-service-principal"></a>Egyszerű szolgáltatás használata

Egy egyszerű szolgáltatást kell használnia az aci Szolgáltatásban történő hitelesítéshez **távfelügyelt forgatókönyvek**, például alkalmazások és szolgáltatások által létrehozott tárolópéldányok a felügyelet nélküli automatikus vagy egyéb módon.

Ha például van egy automatizált szkript, amely nightly fut, és létrehoz egy [feladatalapú tárolópéldány](../container-instances/container-instances-restart-policy.md) bizonyos adatok feldolgozásához, ezáltal az egyszerű szolgáltatás lekéréses (olvasó) engedélyekkel a beállításjegyzék hitelesítéséhez. Ezután az egyszerű szolgáltatás hitelesítő adatok forgatása vagy teljesen vonni a hozzáférést a más szolgáltatások és alkalmazások befolyásolása nélkül.

A szolgáltatásnevek is kell használni a beállításjegyzék [rendszergazdai felhasználó](container-registry-authentication.md#admin-account) le van tiltva.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="authenticate-using-the-service-principal"></a>Hitelesítés az egyszerű szolgáltatás használatával

Egy tárolót az Azure Container Instances használatával egy egyszerű szolgáltatás elindításához, adja meg az Azonosítóját `--registry-username`, és a kapcsolódó jelszó `--registry-password`.

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

Jól verzióként az Azure PowerShell előző mintaszkriptek találja meg a Githubon, az Azure CLI-hez:

* [Az Azure CLI][acr-scripts-cli]
* [Azure PowerShell-lel][acr-scripts-psh]

## <a name="next-steps"></a>További lépések

A következő cikkeket tartalmaz további részleteket az egyszerű szolgáltatásnevekről és ACR használata:

* [A szolgáltatásnevek Azure Tárolóregisztrációs adatbázis hitelesítési](container-registry-auth-service-principal.md)
* [Hitelesítés az Azure tároló-beállításjegyzéket az Azure Kubernetes Service (AKS)](container-registry-auth-aks.md)

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
