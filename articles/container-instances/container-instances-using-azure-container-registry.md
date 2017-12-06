---
title: "Az Azure-tárolót regisztrációs Azure tároló példányok telepítése"
description: "Az Azure-tárolót regisztrációs Azure tároló példányok telepítése"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 08/02/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 9667a5b840d6c1fab5087cfcf3ede34a732fbe01
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="deploy-to-azure-container-instances-from-the-azure-container-registry"></a>Az Azure-tárolót regisztrációs Azure tároló példányok telepítése

Az Azure-tároló beállításjegyzék egy Azure-alapú, személyes beállításjegyzék Docker-tároló lemezképek. Ez a cikk bemutatja, hogyan telepítheti a beállításjegyzékben Azure tároló Azure tároló példányokhoz tároló lemezképeket.

## <a name="using-the-azure-cli"></a>Az Azure parancssori felületének használata

Az Azure parancssori felület létrehozása és kezelése az Azure-tároló példányok a tárolók parancsokat tartalmaz. Ha egy titkos lemezképet adja meg a `create` parancsot is megadható a kép beállításjegyzék jelszóra van szükség a tároló beállításjegyzék szolgáltatással való hitelesítésre.

```azurecli-interactive
az container create --name myprivatecontainer --image mycontainerregistry.azurecr.io/mycontainerimage:v1 --registry-password myRegistryPassword --resource-group myresourcegroup
```

A `create` parancs is támogatja megadása a `registry-login-server` és `registry-username`. A bejelentkezési kiszolgáló Azure tároló beállításjegyzék azonban mindig *registryname*. azurecr.io és az alapértelmezett felhasználónév *registryname*, így ezek az értékek kikövetkeztetve a lemezkép nevét, ha nem explicit módon megadott.

## <a name="using-an-azure-resource-manager-template"></a>Azure Resource Manager-sablonnal

Tulajdonságok is megadhatók a az Azure-tároló beállításjegyzék Azure Resource Manager sablon-ot a `imageRegistryCredentials` tulajdonságot a tároló meghatározása:

```json
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

A tároló beállításjegyzék jelszó tárolása közvetlenül a sablon elkerüléséhez javasolt tárolják a, a titkos kulcs [Azure Key Vault](../key-vault/key-vault-manage-with-cli2.md) és a sablon használatával hivatkozni rá a [natív integráció az Azure között Erőforrás-kezelő és a kulcstároló](../azure-resource-manager/resource-manager-keyvault-parameter.md).

## <a name="using-the-azure-portal"></a>Az Azure Portal használata

Ha az Azure-tároló beállításjegyzék lemezképeit tároló, Azure-tároló példányát az Azure portál használatával könnyedén létrehozhat egy tárolót.

1. Az Azure portálon keresse meg a tároló beállításjegyzék.

2. Válassza ki a tárházak találhatók.

    ![Az Azure-portálon az Azure tároló beállításjegyzék menü][acr-menu]

3. Válassza ki a telepíteni kívánt a tárházban.

4. Kattintson a jobb gombbal a címke a tároló lemezkép számára telepíteni kívánja.

    ![Helyi menü az Azure-tároló példányaival tároló megnyitása][acr-runinstance-contextmenu]

5. Adja meg a tároló nevét és az erőforráscsoport nevét. Ha kívánja, módosíthatja az alapértelmezett értékeket.

    ![Azure-tároló példányok menü létrehozása][acr-create-deeplink]

6. Miután a telepítés befejeződött, lépjen a tárolócsoport a értesítések panelről az IP-címét és egyéb tulajdonságok kereséséhez.

    ![Az Azure-tároló példányok a tárolócsoport Részletek nézet][aci-detailsview]

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan tárolók létrehozása, küldje le őket a magánfelhő tároló beállításjegyzék és telepítheti őket az Azure tároló példányokat [az oktatóanyagnak az elvégzése](container-instances-tutorial-prepare-app.md).

<!-- IMAGES -->
[acr-menu]: ./media/container-instances-using-azure-container-registry/acr-menu.png

[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png

[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
