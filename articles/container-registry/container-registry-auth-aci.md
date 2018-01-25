---
title: "Azure-tárolót beállításjegyzék Azure tároló példányokból hitelesítéshez"
description: "Megtudhatja, hogyan biztosít hozzáférést a személyes tárolót beállításjegyzék lemezképeihez Azure tároló példányokból az Azure Active Directory szolgáltatás egyszerű."
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 01/24/2018
ms.author: marsma
ms.openlocfilehash: 00d9632a5d0c42eceee1b412f8963bbadbea651f
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2018
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-instances"></a>Azure-tárolót beállításjegyzék Azure tároló példányokból hitelesítéshez

Egy Azure Active Directory (Azure AD) egyszerű szolgáltatásnév segítségével hozzáférést biztosítson a saját tároló nyilvántartó Azure tároló beállításjegyzékben.

Ebből a cikkből megismerheti, létrehozása és konfigurálása az Azure AD szolgáltatás egyszerű *lekéréses* a beállításjegyzék engedélyek. Ezt követően először egy tárolót az Azure tároló példányok (ACI), amely lekéri a lemezképet a titkos beállításjegyzék, az egyszerű szolgáltatás használata a hitelesítéshez.

## <a name="when-to-use-a-service-principal"></a>Egy egyszerű szolgáltatás használata

Egy egyszerű szolgáltatást kell használnia a hitelesítéshez a ACI **távfelügyeleti forgatókönyvek**, például alkalmazásokhoz vagy szolgáltatásokhoz, a felügyelet nélküli automatizált vagy egyéb módon tároló példányok létrehozása.

Például, ha az automatizált parancsfájlt, amely minden éjjel hoz létre egy [feladatalapú tároló példány](../container-instances/container-instances-restart-policy.md) bizonyos adatok feldolgozására, is használjon egyszerű szolgáltatás lekéréses (olvasó) engedélyekkel rendelkező hitelesítésére a beállításjegyzékhez. Majd forgassa el az egyszerű szolgáltatás hitelesítő adatait, vagy a hozzáférése teljesen visszavonása anélkül, hogy befolyásolná a szolgáltatások és alkalmazások.

Szolgáltatás rendszerbiztonsági tagoknak is kell használni a beállításjegyzékben [rendszergazdai jogú felhasználó](container-registry-authentication.md#admin-account) le van tiltva.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="authenticate-using-the-service-principal"></a>Hitelesítés az egyszerű szolgáltatás használatával

Egy tároló egy egyszerű szolgáltatást használó Azure-tároló példányokat az indításához adja meg az Azonosítót, a `--registry-username`, és a hozzá tartozó jelszó `--registry-password`.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerregistry.azurecr.io/myimage:v1 \
    --registry-login-server mycontainerregistry.azurecr.io \
    --registry-username <service-principal-ID> \
    --registry-password <service-principal-password>
```

## <a name="next-steps"></a>További lépések

A következő cikkek szolgáltatásnevekről és ACR használatával kapcsolatos további részleteket tartalmaznak:

* [Az Azure tároló beállításjegyzék hitelesítést a szolgáltatás rendszerbiztonsági tagok](container-registry-auth-service-principal.md)
* [A hitelesítést az Azure Tárolószolgáltatás (AKS) Azure-tárolót beállításjegyzék](container-registry-auth-aks.md)

<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->
