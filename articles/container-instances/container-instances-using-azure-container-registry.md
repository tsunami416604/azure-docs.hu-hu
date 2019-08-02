---
title: Üzembe helyezés a Azure Container Instances Azure Container Registry
description: Megtudhatja, hogyan helyezhet üzembe tárolókat a Azure Container Instances tároló-lemezképek használatával egy Azure Container registryben.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 01/04/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 502f178b66e7ba233552d7db4e095363c8bb8628
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68325564"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Üzembe helyezés a Azure Container Instances Azure Container Registry

[Azure Container Registry](../container-registry/container-registry-intro.md) egy Azure-alapú, felügyelt tároló beállításjegyzék-szolgáltatás, amely a privát Docker-tárolók rendszerképeinek tárolására szolgál. Ez a cikk azt ismerteti, hogyan helyezhetők üzembe az Azure Container registryben tárolt tároló lemezképek Azure Container Instances.

## <a name="prerequisites"></a>Előfeltételek

**Azure Container Registry**: Szüksége lesz egy Azure Container registryre – és legalább egy tároló-rendszerképre a beállításjegyzékben – a cikk lépéseinek végrehajtásához. Ha szüksége van egy beállításjegyzékre, tekintse meg a [tároló-beállításjegyzék létrehozása az Azure CLI használatával](../container-registry/container-registry-get-started-azure-cli.md)című témakört.

**Azure CLI**: A cikkben ismertetett parancssori példák az [Azure CLI](/cli/azure/) -t használják, és a bash rendszerhéjhoz vannak formázva. [Az Azure CLI](/cli/azure/install-azure-cli) -t helyileg is telepítheti, vagy használhatja a [Azure Cloud Shell][cloud-shell-bash].

## <a name="configure-registry-authentication"></a>Regisztrációs adatbázis hitelesítésének konfigurálása

Bármilyen éles környezetben az Azure Container Registry elérését [egyszerű szolgáltatásnév](../container-registry/container-registry-auth-service-principal.md)használatával kell megadnia. Az egyszerű szolgáltatások lehetővé teszik, hogy [szerepköralapú hozzáférés-vezérlést](../container-registry/container-registry-roles.md) biztosítson a tárolói lemezképekhez. Konfigurálhat például egy olyan szolgáltatásnevet, amely csak lekérés céljából férhet hozzá a regisztrációs adatbázishoz.

A következő szakaszban létrehoz egy Azure Key vaultot és egy szolgáltatásnevet, és tárolja az egyszerű szolgáltatásnév hitelesítő adatait a tárolóban. 

### <a name="create-key-vault"></a>Kulcstartó létrehozása

Ha még nem rendelkezik tárolóval az [Azure Key Vaultban](../key-vault/key-vault-overview.md), hozzon létre egyet az Azure CLI alábbi parancsaival.

Frissítse a `RES_GROUP` változót egy meglévő erőforráscsoport nevével, amelyben létrehozza a kulcstárolót, valamint `ACR_NAME` a tároló-beállításjegyzék nevét. Adja meg az új kulcstartó nevét a alkalmazásban `AKV_NAME`. A tár nevének egyedinek kell lennie az Azure-on belül, és 3-24 alfanumerikus karakterből kell állnia, betűvel kell kezdődnie, betűvel vagy számmal kell végződnie, és nem tartalmazhat egymást követő kötőjeleket.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>Szolgáltatásnév létrehozása és a hitelesítő adatok tárolása

Létre kell hoznia egy szolgáltatásnevet, és el kell tárolnia annak hitelesítő adatait a kulcstárolóban.

A következő parancs az [az ad SP Create-for-RBAC][az-ad-sp-create-for-rbac] használatával hozza létre az egyszerű szolgáltatásnevet, és az az Key [Vault Secret set][az-keyvault-secret-set] paranccsal tárolja a szolgáltatásnév **jelszavát** a tárolóban.

```azurecli
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name http://$ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role acrpull \
                --query password \
                --output tsv)
```

Az `--role` előző parancsban szereplő argumentum konfigurálja az egyszerű szolgáltatást a *acrpull* szerepkörrel, amely csak lekéréses hozzáférést biztosít a beállításjegyzékhez. A leküldéses és lekéréses hozzáférés engedélyezéséhez módosítsa az `--role` argumentumot *acrpush*értékre.

Ezután tárolja a szolgáltatásnév *AppID* a tárolóban, amely a hitelesítéshez Azure Container Registry átadni kívánt **Felhasználónév** .

```azurecli
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Létrehozott egy Azure Key Vault-tárolót, és eltárolt benne két titkos kulcsot:

* `$ACR_NAME-pull-usr`: Az egyszerű szolgáltatásnév azonosítója, amelyet tároló-beállításjegyzékbeli **felhasználónévként**kell használni.
* `$ACR_NAME-pull-pwd`: A szolgáltatás egyszerű jelszava, a tároló-beállításjegyzék jelszavakéntvaló használathoz.

Innentől ezekre a titkos kulcsokra név alapján hivatkozhat, amikor Ön vagy az alkalmazások és szolgáltatások rendszerképeket kérnek le a regisztrációs adatbázisból.

## <a name="deploy-container-with-azure-cli"></a>Tároló üzembe helyezése az Azure CLI használatával

Most, hogy az egyszerű szolgáltatás hitelesítő adatai Azure Key Vault titkos kulcsokban vannak tárolva, az alkalmazások és a szolgáltatások használhatják őket a privát beállításjegyzék eléréséhez.

Először kérje le a beállításjegyzék bejelentkezési kiszolgálójának nevét az az [ACR show][az-acr-show] parancs használatával. A bejelentkezési kiszolgáló neve minden kisbetűs, és hasonló a `myregistry.azurecr.io`következőhöz:.

```azurecli
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --resource-group $RES_GROUP --query "loginServer" --output tsv)
```

A Container instance üzembe helyezéséhez hajtsa végre a következőt az [az Container Create][az-container-create] paranccsal. A parancs a Azure Key Vault tárolt hitelesítő adatokat használja a tároló beállításjegyzékének hitelesítéséhez, és feltételezi, hogy korábban leküldte az [ACI-HelloWorld](container-instances-quickstart.md) rendszerképet a beállításjegyzékbe. Frissítse az `--image` értéket, ha másik rendszerképet szeretne használni a beállításjegyzékből.

```azurecli
az container create \
    --name aci-demo \
    --resource-group $RES_GROUP \
    --image $ACR_LOGIN_SERVER/aci-helloworld:v1 \
    --registry-login-server $ACR_LOGIN_SERVER \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label aci-demo-$RANDOM \
    --query ipAddress.fqdn
```

Az `--dns-name-label` értéknek egyedinek kell lennie az Azure-on belül, így az előző parancs egy véletlenszerű számot fűz a tároló DNS-neve címkéjéhez. A parancs kimenete a tároló teljes tartománynevét jeleníti meg, például:

```console
$ az container create --name aci-demo --resource-group $RES_GROUP --image $ACR_LOGIN_SERVER/aci-helloworld:v1 --registry-login-server $ACR_LOGIN_SERVER --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) --dns-name-label aci-demo-$RANDOM --query ipAddress.fqdn
"aci-demo-25007.eastus.azurecontainer.io"
```

Miután a tároló sikeresen elindult, megnyithatja a teljes tartománynevet a böngészőben annak ellenőrzéséhez, hogy az alkalmazás sikeresen fut-e.

## <a name="deploy-with-azure-resource-manager-template"></a>Üzembe helyezés Azure Resource Manager sablonnal

A Azure Container Registry tulajdonságait egy Azure Resource Manager sablonban adhatja meg, a tároló csoport definíciójában `imageRegistryCredentials` szereplő tulajdonsággal együtt:

```JSON
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

Egy Resource Manager-sablon Azure Key Vault titkaira hivatkozó részletekért lásd: a [Azure Key Vault használata a biztonságos paraméterek értékének](../azure-resource-manager/resource-manager-keyvault-parameter.md)átadására az üzembe helyezés során.

## <a name="deploy-with-azure-portal"></a>Üzembe helyezés Azure Portal

Ha a tároló lemezképeit egy Azure Container registryben tartja karban, egyszerűen létrehozhat egy tárolót Azure Container Instances a Azure Portal használatával. Ha a portál használatával helyez üzembe egy Container-példányt egy tároló-beállításjegyzékből, engedélyeznie kell a beállításjegyzék [rendszergazdai fiókját](../container-registry/container-registry-authentication.md#admin-account). A rendszergazdai fiók úgy van kialakítva, hogy egyetlen felhasználó hozzáférjen a beállításjegyzékhez, főleg tesztelési célokra. 

1. A Azure Portal navigáljon a tároló-beállításjegyzékhez.

1. Ha szeretné ellenőrizni, hogy a rendszergazdai fiók engedélyezve van-e, válassza a **hozzáférési kulcsok**lehetőséget, majd a **rendszergazda felhasználó** területen válassza az **Engedélyezés**lehetőséget.

1. Válassza ki a **tárak**elemet, majd válassza ki a telepíteni kívánt tárházat, kattintson a jobb gombbal a telepíteni kívánt tároló lemezkép címkéjére, majd válassza a **példány futtatása**parancsot.

    !["Példány futtatása" Azure Container Registry a Azure Portal][acr-runinstance-contextmenu]

1. Adja meg a tároló nevét és az erőforráscsoport nevét. Igény szerint módosíthatja az alapértelmezett értékeket is.

    ![Azure Container Instances menü létrehozása][acr-create-deeplink]

1. Miután az üzembe helyezés befejeződött, a tároló csoportra navigálva megkeresheti az IP-címét és egyéb tulajdonságait.

    ![Azure Container Instances tároló csoport részletes nézete][aci-detailsview]

## <a name="next-steps"></a>További lépések

A Azure Container Registry hitelesítéssel kapcsolatos további információkért lásd: [hitelesítés Azure Container registryvel](../container-registry/container-registry-authentication.md).

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - External -->
[cloud-shell-bash]: https://shell.azure.com/bash
[cloud-shell-try-it]: https://shell.azure.com/powershell

<!-- LINKS - Internal -->
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-create]: /cli/azure/container#az-container-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
