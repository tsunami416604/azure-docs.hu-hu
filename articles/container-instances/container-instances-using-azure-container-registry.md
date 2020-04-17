---
title: Tárolórendszerkép üzembe helyezése az Azure Container Registry alkalmazásból
description: Megtudhatja, hogyan helyezheti üzembe a tárolókat az Azure Container Instances-ben az Azure-tároló rendszerpéldányainak leésével.
services: container-instances
ms.topic: article
ms.date: 02/18/2020
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 212624b857d65297830995018603c2627f83369b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453523"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Üzembe helyezés az Azure Container-példányokba az Azure Container Registry-ből

[Az Azure Container Registry](../container-registry/container-registry-intro.md) egy Azure-alapú, felügyelt tároló beállításjegyzék-szolgáltatás, amely a privát Docker-tárolórendszerképek tárolására szolgál. Ez a cikk ismerteti, hogyan lekéréses tárolólemezképek egy Azure container registry az Azure Container-példányok üzembe helyezésekor. A beállításjegyzék-hozzáférés konfigurálásának ajánlott módja az Azure Active Directory egyszerű szolgáltatásés jelszó létrehozása, és a bejelentkezési hitelesítő adatok tárolása egy Azure-kulcstartóban.

## <a name="prerequisites"></a>Előfeltételek

**Azure container registry:** A cikkben leírt lépések végrehajtásához szüksége van egy Azure-tároló beállításjegyzékére – és legalább egy tárolórendszerképre a beállításjegyzékben. Ha szüksége van egy beállításjegyzékre, olvassa el a [Tároló beállításjegyzékének létrehozása az Azure CLI használatával című témakört.](../container-registry/container-registry-get-started-azure-cli.md)

**Azure CLI:** A parancssori példák ebben a cikkben az [Azure CLI](/cli/azure/) használatával, és a Bash shell formázott. [Telepítheti az Azure CLI-t](/cli/azure/install-azure-cli) helyileg, vagy használhatja az [Azure Cloud Shellt.][cloud-shell-bash]

## <a name="configure-registry-authentication"></a>Regisztrációs adatbázis hitelesítésének konfigurálása

Egy éles környezetben, ahol hozzáférést biztosít a "fej nélküli" szolgáltatásokhoz és alkalmazásokhoz, ajánlott a rendszerleíró adatbázis-hozzáférést egyszerű szolgáltatás használatával [konfigurálni.](../container-registry/container-registry-auth-service-principal.md) Egy egyszerű szolgáltatás lehetővé teszi, hogy [szerepköralapú hozzáférés-vezérlést](../container-registry/container-registry-roles.md) biztosítson a tárolórendszerképekhez. Konfigurálhat például egy olyan szolgáltatásnevet, amely csak lekérés céljából férhet hozzá a regisztrációs adatbázishoz.

Az Azure Container Registry további [hitelesítési beállításokat](../container-registry/container-registry-authentication.md)biztosít.

> [!NOTE]
> Nem hitelesíthető az Azure Container Registry rendszerpéldánya a tárolócsoport üzembe helyezése során az ugyanabban a tárolócsoportban konfigurált [felügyelt identitás](container-instances-managed-identity.md) használatával.

A következő szakaszban hozzon létre egy Azure-kulcstartót és egy egyszerű szolgáltatást, és tárolja az egyszerű szolgáltatás hitelesítő adatait a tárolóban. 

### <a name="create-key-vault"></a>Kulcstároló létrehozása

Ha még nem rendelkezik tárolóval az [Azure Key Vaultban](../key-vault/general/overview.md), hozzon létre egyet az Azure CLI alábbi parancsaival.

Frissítse `RES_GROUP` a változót egy meglévő erőforráscsoport nevével, amelyben `ACR_NAME` a kulcstartót létre szeretné hozni, és a tároló beállításjegyzékének nevével. Röviden, a cikkben szereplő parancsok feltételezik, hogy a rendszerleíró adatbázis, a key vault és a tároló példányok mind ugyanabban az erőforráscsoportban jönnek létre.

 Adja meg az új kulcstartó nevét a ban. `AKV_NAME` A tároló nevének egyedinek kell lennie az Azure-on belül, és 3–24 alfanumerikus karakter hosszúságúnak kell lennie, betűvel kell kezdődnie, betűvel vagy számjegyével kell végződnie, és nem tartalmazhat egymást követő kötőjeleket.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>Szolgáltatásnév létrehozása és a hitelesítő adatok tárolása

Most hozzon létre egy egyszerű szolgáltatást, és tárolja a hitelesítő adatait a key vaultban.

A következő parancs az [ad sp create-for-rbac][az-ad-sp-create-for-rbac] segítségével hozza létre a szolgáltatásnév, és az [a keyvault titkos készlet][az-keyvault-secret-set] tárolja a szolgáltatásnév **jelszavát** a tárolóban.

```azurecli
# Create service principal, store its password in vault (the registry *password*)
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

Az `--role` előző parancsargumentum ban található argumentum az *acrpull* szerepkörrel konfigurálja a szolgáltatásnév, amely csak lekéréses hozzáférést biztosít számára a beállításjegyzékhez. A push és pull hozzáférés `--role` engedélyezéséhez módosítsa az argumentumot *acrpush*-ra.

Ezután tárolja az egyszerű szolgáltatás *appId-ját* a tárolóban, amely az Azure Container Registry hitelesítéshez megadott **felhasználóneve.**

```azurecli
# Store service principal ID in vault (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Létrehozott egy Azure Key Vault-tárolót, és tárolt benne két titkos kulcsot:

* `$ACR_NAME-pull-usr`: A szolgáltatásnév azonosítója, amely a tárolóregisztrációs adatbázis **felhasználóneveként** szolgál.
* `$ACR_NAME-pull-pwd`: A szolgáltatásnév jelszava, amely a tárolóregisztrációs adatbázis **jelszavaként** szolgál.

Innentől ezekre a titkos kulcsokra név alapján hivatkozhat, amikor Ön vagy az alkalmazások és szolgáltatások rendszerképeket kérnek le a regisztrációs adatbázisból.

## <a name="deploy-container-with-azure-cli"></a>Tároló üzembe helyezése az Azure CLI használatával

Most, hogy a szolgáltatás egyszerű hitelesítő adatok az Azure Key Vault titkos kulcsai, az alkalmazások és szolgáltatások használhatják őket a privát beállításjegyzék eléréséhez.

Először az [az acr show][az-acr-show] parancsot használja a rendszerleíró adatbázis bejelentkezési kiszolgálójának nevéhez. A bejelentkezési kiszolgáló neve kisbetűs `myregistry.azurecr.io`és hasonló a hoz.

```azurecli
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --resource-group $RES_GROUP --query "loginServer" --output tsv)
```

A következő [az container create][az-container-create] parancs végrehajtásával helyezzen üzembe egy tárolópéldányt. A parancs az Azure Key Vaultban tárolt egyszerű szolgáltatás hitelesítő adatait használja a tároló beállításjegyzékében való hitelesítéshez, és feltételezi, hogy korábban lelökte az [aci-helloworld](container-instances-quickstart.md) lemezképet a rendszerleíró adatbázisba. Frissítse `--image` az értéket, ha a rendszerleíró adatbázistól eltérő lemezképet szeretne használni.

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

Az `--dns-name-label` értéknek egyedinek kell lennie az Azure-on belül, így az előző parancs egy véletlen számot fűz a tároló DNS-névcímkéjéhez. A parancs kimenete a tároló teljes tartománynevét jeleníti meg, például:

```output
"aci-demo-25007.eastus.azurecontainer.io"
```

Miután a tároló sikeresen elindult, a böngészőben a teljes tartománynát a böngészőben navigálva ellenőrizheti, hogy az alkalmazás sikeresen fut-e.

## <a name="deploy-with-azure-resource-manager-template"></a>Üzembe helyezés az Azure Resource Manager sablonnal

Az Azure-tároló beállításjegyzékének tulajdonságait egy Azure Resource Manager-sablonban adhatja meg, ha a `imageRegistryCredentials` tulajdonságot a tárolócsoport-definícióba is beleadja. Megadhatja például közvetlenül a rendszerleíró adatbázis hitelesítő adatait:

```JSON
[...]
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
[...]
```

A tárolócsoport teljes beállításait az [Erőforrás-kezelő sablonhivatkozásban találja.](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups)    

Az Azure Key Vault titkos kulcsainak resource manageri sablonban való hivatkozásáról az [Azure Key Vault használata a telepítés során a biztonságos paraméter értékének átadása című](../azure-resource-manager/templates/key-vault-parameter.md)témakörben olvashat.

## <a name="deploy-with-azure-portal"></a>Üzembe helyezés az Azure Portalon

Ha tárolórendszerképeket tart fenn egy Azure-tároló beállításjegyzékében, az Azure-portál használatával egyszerűen létrehozhat egy tárolót az Azure Container Instances-ben. Ha a portált egy tárolópéldány tárolóbeállításjegyzékből történő központi telepítésére használja, engedélyeznie kell a rendszerleíró adatbázis [rendszergazdai fiókját.](../container-registry/container-registry-authentication.md#admin-account) Az admin fiók célja, hogy egyetlen felhasználó hozzáférjen a rendszerleíró adatbázishoz, elsősorban tesztelési célokra. 

1. Az Azure Portalon keresse meg a tároló beállításjegyzékben.

1. A rendszergazdai fiók engedélyezésének ellenőrzéséhez válassza az **Access-kulcsok**lehetőséget, és a **Rendszergazda felhasználó** csoportban válassza az **Engedélyezés**lehetőséget.

1. Válassza **az Adattárak lehetőséget,** majd jelölje ki azt a tárházat, amelyből telepíteni szeretné, kattintson a jobb gombbal az telepíteni kívánt tárolólemezkép címkéjére, és válassza a **Példány futtatása parancsot.**

    !["Példány futtatása" az Azure Container Registry szolgáltatásban az Azure Portalon][acr-runinstance-contextmenu]

1. Adja meg a tároló nevét és az erőforráscsoport nevét. Az alapértelmezett értékeket is módosíthatja, ha szeretné.

    ![Létrehozás az Azure Container Instances számára][acr-create-deeplink]

1. Miután a központi telepítés befejeződött, az értesítések ablaktábláról a tárolócsoportra navigálva megkeresheti az IP-címét és egyéb tulajdonságait.

    ![Az Azure Container Instances tárolócsoport részletei][aci-detailsview]

## <a name="next-steps"></a>További lépések

Az Azure Container Registry hitelesítésével kapcsolatos további információkért [lásd: Hitelesítés egy Azure-tároló beállításjegyzékével.](../container-registry/container-registry-authentication.md)

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
