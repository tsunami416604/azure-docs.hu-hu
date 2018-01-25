---
title: "Azure-tárolót beállításjegyzékből Azure tároló példányok telepítése"
description: "Útmutató: Azure tároló példányát az Azure-tároló beállításjegyzék tároló lemezképek használatával tároló üzembe helyezése."
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 01/24/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: c69b95f66bf2eaf4975961da5b25f5ac6172798c
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2018
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Azure-tárolót beállításjegyzékből Azure tároló példányok telepítése

Az Azure-tároló beállításjegyzék egy Azure-alapú, személyes beállításjegyzék Docker-tároló lemezképek. Ez a cikk bemutatja, hogyan telepítheti a beállításjegyzékben Azure tároló Azure tároló példányokhoz tároló lemezképeket.

## <a name="deploy-with-azure-cli"></a>Üzembe helyezés az Azure parancssori felületén keresztül

Az Azure parancssori felület létrehozása és kezelése az Azure-tároló példányok a tárolók parancsokat tartalmaz. Ha egy titkos lemezképet adja meg a [az tároló létrehozása] [ az-container-create] parancsot is megadható a kép beállításjegyzék jelszóra van szükség a tároló beállításjegyzék szolgáltatással való hitelesítésre.

```azurecli-interactive
az container create --resource-group myResourceGroup --name myprivatecontainer --image mycontainerregistry.azurecr.io/mycontainerimage:v1 --registry-password myRegistryPassword
```

A [az tároló létrehozása] [ az-container-create] parancs is támogatja megadó `--registry-login-server` és `--registry-username`. A bejelentkezési kiszolgáló Azure tároló beállításjegyzék azonban mindig *registryname*. azurecr.io és az alapértelmezett felhasználónév *registryname*, így ezek az értékek kikövetkeztetve a lemezkép nevét, ha nem explicit módon megadott.

## <a name="deploy-with-azure-resource-manager-template"></a>Az Azure Resource Manager sablon telepítése

Tulajdonságok is megadhatók a az Azure-tároló beállításjegyzék Azure Resource Manager sablon-ot a `imageRegistryCredentials` tulajdonságot a tároló meghatározása:

```JSON
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

A tároló beállításjegyzék jelszó tárolása közvetlenül a sablon elkerüléséhez javasolt tárolják a, a titkos kulcs [Azure Key Vault](../key-vault/key-vault-manage-with-cli2.md) és a sablon használatával hivatkozni rá a [natív integráció az Azure között Erőforrás-kezelő és a kulcstároló](../azure-resource-manager/resource-manager-keyvault-parameter.md).

## <a name="deploy-with-azure-portal"></a>Az Azure-portál telepítése

Ha az Azure-tároló beállításjegyzék lemezképeit tároló, Azure-tároló példányát az Azure portál használatával könnyedén létrehozhat egy tárolót.

1. Az Azure portálon keresse meg a tároló beállításjegyzék.

1. Válassza ki **Tárházak**, majd válassza ki a tárházban, amelyet szeretne telepíteni, kattintson a jobb gombbal a tároló lemezkép központi telepítése, és válassza ki a címke **példány futtatása**.

    !["Futtatás példány" Azure tároló beállításjegyzék az Azure-portálon][acr-runinstance-contextmenu]

1. Adja meg a tároló nevét és az erőforráscsoport nevét. Ha kívánja, módosíthatja az alapértelmezett értékeket.

    ![Azure-tároló példányok menü létrehozása][acr-create-deeplink]

1. Miután a telepítés befejeződött, lépjen a tárolócsoport a értesítések panelről az IP-címét és egyéb tulajdonságok kereséséhez.

    ![Az Azure-tároló példányok a tárolócsoport Részletek nézet][aci-detailsview]

## <a name="service-principal-authentication"></a>Egyszerű szolgáltatásnév hitelesítése

Ha a felhasználót a rendszergazda az Azure-tárolót beállításjegyzék le van tiltva, használhatja az Azure Active Directory-alapú [egyszerű](../container-registry/container-registry-auth-service-principal.md) hitelesítéséhez a beállításjegyzéket, a tároló példányának létrehozásakor. Távfelügyeleti forgatókönyvek, például egy parancsfájl vagy az alkalmazás által létrehozott tároló példányok felügyelet nélkül is ajánlott az egyszerű szolgáltatás használata a hitelesítéshez.

További információkért lásd: [hitelesítés az Azure-tároló beállításjegyzék Azure tároló példányokból](../container-registry/container-registry-auth-aci.md).

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan tárolók létrehozása, küldje le őket a magánfelhő tároló beállításjegyzék és telepítheti őket az Azure tároló példányokat [az oktatóanyagnak az elvégzése](container-instances-tutorial-prepare-app.md).

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create