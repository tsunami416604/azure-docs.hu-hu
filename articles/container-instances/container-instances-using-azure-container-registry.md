---
title: Az Azure Container Instances szolgáltatásban az Azure Container Registry üzembe helyezése
description: Ismerje meg, hogyan helyezhet üzembe az Azure Container Instances használatával a tárolórendszerképeket az Azure container registry a tárolók.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/30/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 5c3cf162caf5cf9aa88b012257d4caab37b7893c
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39424211"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Az Azure Container Instances szolgáltatásban az Azure Container Registry üzembe helyezése

Az Azure Container Registry egy Azure-alapú privát regisztrációs adatbázis Docker-tárolórendszerképekhez. Ez a cikk azt ismerteti, hogyan helyezhet üzembe az Azure Container Instances szolgáltatásban az Azure container registryben tárolt rendszerképeket.

## <a name="prerequisites"></a>Előfeltételek

**Az Azure Container Registry**: szüksége van egy Azure container registry – és legalább egy tárolórendszerképet a beállításjegyzék – ebben a cikkben szereplő lépések végrehajtásához. Ha a beállításjegyzék van szüksége, tekintse meg [hozzon létre egy tároló-beállításjegyzéket az Azure CLI-vel](../container-registry/container-registry-get-started-azure-cli.md).

**Az Azure CLI**: A parancssori példák a jelen cikk használja a [Azure CLI-vel](/cli/azure/) és a Bash felületen vannak formázva. Is [az Azure CLI telepítése](/cli/azure/install-azure-cli) helyileg, vagy használja a [Azure Cloud Shell][cloud-shell-bash].

## <a name="configure-registry-authentication"></a>Regisztrációs adatbázis hitelesítésének konfigurálása

A semmilyen éles telepítési forgatókönyvhöz egy Azure container registrybe hozzáférést kell biztosítani a [egyszerű szolgáltatások](../container-registry/container-registry-auth-service-principal.md). A szolgáltatásnevek lehetővé teszik a szerepköralapú hozzáférés-vezérlés biztosítását a tárolórendszerképek számára. Konfigurálhat például egy olyan szolgáltatásnevet, amely csak lekérés céljából férhet hozzá a regisztrációs adatbázishoz.

Ebben a szakaszban egy Azure key vault és a egy egyszerű szolgáltatás létrehozásához, és a szolgáltatásnév hitelesítő adatainak tárolása a tárolóban.

### <a name="create-key-vault"></a>Kulcstároló létrehozása

Ha még nem rendelkezik tárolóval az [Azure Key Vaultban](/azure/key-vault/), hozzon létre egyet az Azure CLI alábbi parancsaival.

Frissítés a `RES_GROUP` az erőforráscsoport, amelyben létrehozza a key vault nevű változó és `ACR_NAME` a tárolóregisztrációs adatbázis nevére. Adjon meg egy nevet az új kulcstartó `AKV_NAME`. Azure-on belül egyedinek kell lennie, és 3 – 24 alfanumerikus karakterből kell állnia a tároló neve hosszabb, betűvel, betűvel vagy számjeggyel, végződnie, és nem tartalmazhat egymást követő kötőjelet.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>Szolgáltatásnév létrehozása és a hitelesítő adatok tárolása

Létre kell hoznia egy szolgáltatásnevet, és el kell tárolnia annak hitelesítő adatait a kulcstárolóban.

A következő parancsot használja [az ad sp create-for-rbac] [ az-ad-sp-create-for-rbac] az egyszerű szolgáltatás létrehozása és [az keyvault secret set] [ az-keyvault-secret-set] tárolására a egyszerű szolgáltatás **jelszó** a tárolóban.

```azurecli
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name $ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role reader \
                --query password \
                --output tsv)
```

Az előző parancs `--role` argumentuma a *reader* (olvasó) szerepkörrel konfigurálja a szolgáltatásnevet, amely csak lekérési jogosultságot biztosít a regisztrációs adatbázishoz. Ha lekérési és leküldési jogosultságot egyaránt biztosítani kíván, módosítsa a `--role` argumentum értékét a *contributor* (közreműködő) értékre.

Ezután tárolja a szolgáltatásnév *appId* a tárolóban, azaz a **felhasználónév** adja át az Azure Container Registrybe a hitelesítéshez.

```azurecli
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Létrehozott egy Azure Key Vault-tárolót, és eltárolt benne két titkos kulcsot:

* `$ACR_NAME-pull-usr`: A szolgáltatásnév azonosítója, amely a tárolóregisztrációs adatbázis **felhasználóneveként** szolgál.
* `$ACR_NAME-pull-pwd`: A szolgáltatásnév jelszava, amely a tárolóregisztrációs adatbázis **jelszavaként** szolgál.

Innentől ezekre a titkos kulcsokra név alapján hivatkozhat, amikor Ön vagy az alkalmazások és szolgáltatások rendszerképeket kérnek le a regisztrációs adatbázisból.

## <a name="deploy-container-with-azure-cli"></a>Tároló üzembe helyezése az Azure CLI használatával

Most, hogy az egyszerű szolgáltatás hitelesítő adatai az Azure Key Vault titkos kulcsok vannak tárolva, az alkalmazások és szolgáltatások használatával őket a privát tárolójegyzék eléréséhez.

A következő [az container create][az-container-create] parancs végrehajtásával helyezzen üzembe egy tárolópéldányt. A parancs az egyszerű szolgáltatás hitelesítő adatait az Azure Key Vaultban tárolt használja a hitelesítéshez a tárolóregisztrációs adatbázisba, és feltételezi, hogy már korábban feltöltötte a [aci-helloworld](container-instances-quickstart.md) rendszerképet a tárolójegyzékbe. Frissítés a `--image` értékét, ha a regisztrációs adatbázisból egy másik képet használni szeretne.

```azurecli
az container create \
    --name aci-demo \
    --resource-group $RES_GROUP \
    --image $ACR_NAME.azurecr.io/aci-helloworld:v1 \
    --registry-login-server $ACR_NAME.azurecr.io \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label aci-demo-$RANDOM \
    --query ipAddress.fqdn
```

A `--dns-name-label` egyedi értékkel kell Azure-ban, így az előző parancs egy véletlenszerű számot fűz a tároló DNS-névcímke. A parancs kimenete a tároló teljes tartománynevét jeleníti meg, például:

```console
$ az container create --name aci-demo --resource-group $RES_GROUP --image $ACR_NAME.azurecr.io/aci-helloworld:v1 --registry-login-server $ACR_NAME.azurecr.io --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) --dns-name-label aci-demo-$RANDOM --query ipAddress.fqdn
"aci-demo-25007.eastus.azurecontainer.io"
```

Miután a tároló sikeresen elindult, navigálhat a teljes tartománynév ellenőrzése az alkalmazás sikeresen fut a böngészőben.

## <a name="deploy-with-azure-resource-manager-template"></a>Üzembe helyezés Azure Resource Manager-sablon használatával

Fel az Azure Resource Manager-sablon tulajdonságait, az Azure Container Registry is megadhat a `imageRegistryCredentials` tulajdonságot a tárolócsoport definíciója:

```JSON
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

További információ az Azure Key Vault titkos kulcsainak Resource Manager-sablon hivatkozik: [használata Azure Key Vaultban történő biztonságos paraméter értéke továbbítása üzembe helyezés során](../azure-resource-manager/resource-manager-keyvault-parameter.md).

## <a name="deploy-with-azure-portal"></a>Üzembe helyezés az Azure portállal

Ha a tárolólemezképeket az Azure Container Registry, az Azure Container Instances szolgáltatásban az Azure portal használatával egyszerűen létrehozhat egy tárolót.

1. Az Azure Portalon lépjen a tárolóregisztrációs adatbázisba.

1. Válassza ki **Tárházak**, majd válassza ki a tárház üzembe helyezés, a kívánt kattintson a jobb gombbal a tárolórendszerkép üzembe helyezése, és válassza ki a kívánt címkéjének **példány futtatásához**.

    !["Példány futtatása" az Azure Container Registryben az Azure Portalon][acr-runinstance-contextmenu]

1. Adja meg a tároló nevét és az erőforráscsoport nevét. Ha szeretné, módosíthatja az alapértelmezett értékeket.

    ![Menü létrehozása az Azure Container Instances szolgáltatásban][acr-create-deeplink]

1. Az üzembe helyezés befejezése után a tárolócsoport léphet az értesítési panelről az IP-címét és egyéb tulajdonságokat.

    ![Azure Container Instances a tárolócsoport részleteinek megtekintése][aci-detailsview]

## <a name="next-steps"></a>További lépések

Azure Container Registry-hitelesítéssel kapcsolatos további információkért lásd: [Azure tároló-beállításjegyzékkel való hitelesítéssel](../container-registry/container-registry-authentication.md).

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - External -->
[cloud-shell-bash]: https://shell.azure.com/bash
[cloud-shell-powershell]: https://shell.azure.com/powershell

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-create]: /cli/azure/container#az-container-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set