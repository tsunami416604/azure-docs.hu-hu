---
title: Azure-tárolót beállításjegyzékből Azure tároló példányok telepítése
description: 'Útmutató: Azure tároló példányát az Azure-tárolót beállításjegyzék tároló lemezképek használatával tároló üzembe helyezése.'
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 03/30/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 4978ba43462045716c14bbf7c0c22f6b5188a273
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Azure-tárolót beállításjegyzékből Azure tároló példányok telepítése

Az Azure-tároló beállításjegyzék egy Azure-alapú, személyes beállításjegyzék a Docker-tároló lemezképek. Ez a cikk ismerteti az Azure-tároló példányokhoz egy Azure-tárolót beállításjegyzékben tárolt tároló lemezképek központi telepítéséről.

## <a name="prerequisites"></a>Előfeltételek

**Az Azure tároló beállításjegyzék**: Azure-tárolót beállításjegyzék – és a beállításjegyzék – Ez a cikk a végrehajtásához legalább egy tárolót lemezkép van szükség. Ha módosítania kell a beállításjegyzéket, lásd: [hozza létre a tároló beállításkulcs, az Azure parancssori felület használatával](../container-registry/container-registry-get-started-azure-cli.md).

**Az Azure CLI**: Ez a parancssori példák a következő cikket: használja a [Azure CLI](/cli/azure/) és a rendszerhéjakba formátumát. Is [az Azure parancssori felület telepítése](/cli/azure/install-azure-cli) helyileg, vagy használja a [Azure Cloud rendszerhéj][cloud-shell-bash].

## <a name="configure-registry-authentication"></a>Beállításjegyzék-hitelesítés konfigurálása

A semmilyen éles telepítési forgatókönyvhöz, egy Azure-tárolót beállításjegyzék hozzáférést kell biztosítani a [szolgáltatás rendszerbiztonsági tagok](../container-registry/container-registry-auth-service-principal.md). Szolgáltatásnevekről engedélyezése, hogy adja meg a szerepköralapú hozzáférés-vezérlés a tároló lemezképbe. Egy egyszerű szolgáltatást konfigurálhatja például lekéréses beállításjegyzékbeli hozzáférést.

Ebben a szakaszban egy az Azure key vault és egyszerű szolgáltatás létrehozása, és tárolja az egyszerű szolgáltatás hitelesítő adatait a tárolóban lévő állapottal.

### <a name="create-key-vault"></a>Kulcstartó létrehozása

Ha még nem rendelkezik egy tárolót [Azure Key Vault](/azure/key-vault/), hozzon létre egyet az Azure parancssori felülettel, az alábbi parancsok használatával.

Frissítés a `RES_GROUP` változó nevű az erőforráscsoport, amelyben a kulcstároló létrehozásához, és `ACR_NAME` a tároló beállításjegyzék nevével. Adjon meg egy nevet az új kulcstartó `AKV_NAME`. A tároló neve Azure belül egyedieknek kell lenniük, és 3 – 24 alfanumerikus karakter hosszúságú, betűvel, betűvel vagy számjeggyel, végződnie, és nem tartalmazhat egymást követő kötőjelet.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>Egyszerű szolgáltatásnév létrehozása és a hitelesítő adatok tárolására

Most hozzon létre egy egyszerű szolgáltatást, és a hitelesítő adatok tárolása a kulcstartót kell.

A következő parancsot használja [az ad sp létrehozása-az-rbac] [ az-ad-sp-create-for-rbac] az egyszerű szolgáltatás létrehozása és [az keyvault titkos set] [ az-keyvault-secret-set] tárolja a szolgáltatás egyszerű **jelszó** a tárolóban lévő állapottal.

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

A `--role` argumentum az előző parancsban szereplő konfigurálja a szolgáltatás egyszerű a *olvasó* szerepkört, amely azt lekéréses hozzáférést biztosít a beállításjegyzékben. Leküldéses és lekéréses hozzáférést biztosítani, módosítsa a `--role` argumentumának *közreműködő*.

A következő tárolja a szolgáltatás egyszerű *appId* a tárolóban, vagyis a **felhasználónév** Azure tároló beállításjegyzék hitelesítéshez át.

```azurecli
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Egy Azure Key Vault elkészítette és két titkos kulcsok tárolva:

* `$ACR_NAME-pull-usr`: A szolgáltatás résztvevő-azonosító, a tároló beállításjegyzék használni **felhasználónév**.
* `$ACR_NAME-pull-pwd`: A szolgáltatás egyszerű jelszó, a tároló beállításjegyzék használni **jelszó**.

Hivatkozhat ezeknek a kulcsoknak név szerint amikor Ön vagy az alkalmazások és szolgáltatások lekéréses képek a beállításjegyzékből.

## <a name="deploy-container-with-azure-cli"></a>Azure parancssori felülettel tároló üzembe

Most, hogy a szolgáltatás egyszerű hitelesítő adatait az Azure Key Vault titkos kulcsok vannak tárolva, az alkalmazások és szolgáltatások segítségével azokat a személyes beállításjegyzék elérése.

Hajtsa végre az alábbiakat [az tároló létrehozása] [ az-container-create] parancs központi telepítése egy tároló-példányt. A parancs az Azure Key Vault tárolt az egyszerű szolgáltatás hitelesítő adatok segítségével a tároló beállításjegyzék hitelesíti, és azt feltételezi, hogy már korábban leküldött a [aci-helloworld](container-instances-quickstart.md) kép a beállításjegyzékhez. Frissítés a `--image` értéket, ha szeretne egy másik lemezképet használja a beállításjegyzékből.

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

A `--dns-name-label` érték egyedinek kell lennie az Azure, az előző parancs véletlenszerűen hozzáfűzi a tároló DNS-névcímke. A parancs kimenetében jelennek meg a tároló teljesen minősített tartománynevét (FQDN), például:

```console
$ az container create --name aci-demo --resource-group $RES_GROUP --image $ACR_NAME.azurecr.io/aci-helloworld:v1 --registry-login-server $ACR_NAME.azurecr.io --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) --dns-name-label aci-demo-$RANDOM --query ipAddress.fqdn
"aci-demo-25007.eastus.azurecontainer.io"
```

Ha a tároló sikeresen elindult, lépjen a teljesen minősített tartománynév ellenőrizze az alkalmazás sikeresen fut-e a böngészőben.

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

További tudnivalók az Azure Key Vault titkos kulcsainak Resource Manager-sablon hivatkozik: [használja az Azure Key Vault üzembe helyezése során biztonságos paraméterérték átadni](../azure-resource-manager/resource-manager-keyvault-parameter.md).

## <a name="deploy-with-azure-portal"></a>Az Azure-portál telepítése

Ha az Azure-tároló beállításjegyzék lemezképeit tároló, Azure-tároló példányát az Azure portál használatával könnyedén létrehozhat egy tárolót.

1. Az Azure portálon keresse meg a tároló beállításjegyzék.

1. Válassza ki **Tárházak**, majd válassza ki a tárházban, amelyet szeretne telepíteni, kattintson a jobb gombbal a tároló lemezkép központi telepítése, és válassza ki a címke **példány futtatása**.

    !["Futtatás példány" Azure tároló beállításjegyzék az Azure-portálon][acr-runinstance-contextmenu]

1. Adja meg a tároló nevét és az erőforráscsoport nevét. Ha kívánja, módosíthatja az alapértelmezett értékeket.

    ![Azure-tároló példányok menü létrehozása][acr-create-deeplink]

1. Miután a telepítés befejeződött, lépjen a tárolócsoport a értesítések panelről az IP-címét és egyéb tulajdonságok kereséséhez.

    ![Az Azure-tároló példányok a tárolócsoport Részletek nézet][aci-detailsview]

## <a name="next-steps"></a>További lépések

Azure-tároló beállításjegyzék hitelesítéssel kapcsolatos további információkért lásd: [hitelesítés az Azure-tárolót rendszerleíró](../container-registry/container-registry-authentication.md).

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - External -->
[cloud-shell-bash]: https://shell.azure.com/bash
[cloud-shell-powershell]: https://shell.azure.com/powershell

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-create]: /cli/azure/container#az_container_create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set