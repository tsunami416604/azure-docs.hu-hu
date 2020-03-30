---
title: Tárolórendszerképek importálása
description: Tárolórendszerképek importálása egy Azure-tároló beállításjegyzékbe az Azure API-k használatával, docker-parancsok futtatása nélkül.
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: caf7a47ac8f7ff0e72d2e049a7013542d274a225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051928"
---
# <a name="import-container-images-to-a-container-registry"></a>Tárolórendszerképek importálása tárolóbeállításjegyzékbe

Könnyedén importálhatja (másolhatja) a tárolórendszerképeket egy Azure-tároló beállításjegyzékébe, Docker-parancsok használata nélkül. Például importáljon lemezképeket egy fejlesztési jegyzékből egy termelési jegyzékbe, vagy másolja az alaplemezképeket egy nyilvános beállításjegyzékből.

Az Azure Container Registry számos gyakori forgatókönyvet kezel a lemezképek egy meglévő beállításjegyzékből történő másolásához:

* Importálás nyilvános jegyzékből

* Importálás egy másik Azure-tároló beállításjegyzékéből ugyanabban vagy egy másik Azure-előfizetésben

* Importálás nem Azure-beli magántároló-beállításjegyzékből

Az Azure-tároló beállításjegyzékébe történő lemezkép-importálás a következő előnyökkel jár a Docker CLI-parancsok használatával szemben:

* Mivel az ügyfélkörnyezetnem igényel helyi Docker-telepítést, importálja a tárolórendszerképet, függetlenül a támogatott operációsrendszer-típustól.

* Ha többarchitektúrájú rendszerképek (például a hivatalos Docker-lemezképek) importálásakor a jegyzéklistában megadott összes architektúrára és platformra vonatkozó rendszerképek et másolja a rendszer.

Tárolólemezképek importálásához ez a cikk megköveteli, hogy futtassa az Azure CLI az Azure Cloud Shell vagy helyileg (2.0.55-ös vagy újabb verzió ajánlott). A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

> [!NOTE]
> Ha azonos tárolórendszerképeket kell terjesztenie több Azure-régióban, az Azure Container Registry is támogatja a [georeplikációt.](container-registry-geo-replication.md) A rendszerleíró adatbázis (prémium szolgáltatási szint szükséges) georeplikálásával több régiót is kiszolgálhat azonos lemezkép- és címkenevekkel egyetlen beállításjegyzékből.
>

## <a name="prerequisites"></a>Előfeltételek

Ha még nem rendelkezik Azure-tároló beállításjegyzékkel, hozzon létre egy beállításjegyzéket. Lépések: [Rövid útmutató: Hozzon létre egy privát tároló beállításjegyzék az Azure CLI használatával.](container-registry-get-started-azure-cli.md)

Egy lemezkép importálásához egy Azure-tároló beállításjegyzékbe, az identitás nak rendelkeznie kell írási engedélyekkel a cél beállításjegyzék (legalább közreműködői szerepkör). Lásd: [Azure Container Registry szerepkörök és engedélyek.](container-registry-roles.md) 

## <a name="import-from-a-public-registry"></a>Importálás nyilvános jegyzékből

### <a name="import-from-docker-hub"></a>Importálás a Docker Hubról

Az [az acr import][az-acr-import] paranccsal például `hello-world:latest` importálhatja a többarchitektúrájú lemezképet a Docker Hubról egy *myregistry*nevű rendszerleíró adatbázisba. Mivel `hello-world` a Docker Hub hivatalos rendszerképe, ez `library` a rendszerkép az alapértelmezett tárházban található. Adja meg a tárház nevét és opcionálisan `--source` egy címkét a képparaméter értékében. (A képet a jegyzékfájl kivonatolása alapján is azonosíthatja címke helyett, ami garantálja a kép egy adott verzióját.)
 
```azurecli
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest
```

A parancs futtatásával ellenőrizheti, hogy több `az acr repository show-manifests` jegyzékfájl van-e társítva ehhez a lemezképhez:

```azurecli
az acr repository show-manifests \
  --name myregistry \
  --repository hello-world
```

A következő példa nyilvános lemezképet importál a `tensorflow` Docker Hub tárházából:

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/tensorflow/tensorflow:latest-gpu \
  --image tensorflow:latest-gpu
```

### <a name="import-from-microsoft-container-registry"></a>Importálás a Microsoft tárolóbeállítás-jegyzékből

Importálja például a legújabb Windows `windows` Server Core lemezképet a Microsoft Container Registry tárházából.

```azurecli
az acr import \
--name myregistry \
--source mcr.microsoft.com/windows/servercore:latest \
--image servercore:latest
```

## <a name="import-from-another-azure-container-registry"></a>Importálás másik Azure-tároló beállításjegyzékéből

Importálhat egy lemezképet egy másik Azure-tároló beállításjegyzékintegrált Azure Active Directory-engedélyek használatával.

* Az identitásnak Rendelkeznie kell az Azure Active Directory engedélyekkel a forrásbeállítási beállításjegyzékből (Olvasószerepkör) való olvasáshoz és a célbeállításjegyzékbe (közreműködői szerepkör) való íráshoz.

* A beállításjegyzék lehet ugyanabban az Azure-előfizetésben ugyanabban az Active Directory-bérlőben.

### <a name="import-from-a-registry-in-the-same-subscription"></a>Importálás rendszerleíró adatbázisból ugyanabban az előfizetésben

Például importálja `aci-helloworld:latest` a lemezképet egy forrásrendszerleíró *adatbázis mysourceregistry* *a myregistry* ugyanabban az Azure-előfizetésben.

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld:latest \
  --image aci-helloworld:latest
```

A következő példa egy képet importál manifesztkivonattal (SHA-256 kivonat, `sha256:...`amelyet a címke jelöl:

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>Importálás rendszerleíró adatbázisból egy másik előfizetésben

A következő példában a *mysourceregistry* ugyanabban az Active Directory-bérlőben található *myregistry-előfizetésben* van. Adja meg a forrásjegyzék erőforrásazonosítóját a `--registry` paraméterrel. Figyelje `--source` meg, hogy a paraméter csak a forrástárházat és a címkét adja meg, a rendszerleíró adatbázis bejelentkezési kiszolgálójának nevét nem.

```azurecli
az acr import \
  --name myregistry \
  --source samples/aci-helloworld:latest \
  --image aci-hello-world:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Importálás rendszerleíró adatbázisból egyszerű szolgáltatáshitelesítő adatokkal

Ha olyan beállításjegyzékből szeretne importálni, amelyet az Active Directory-engedélyekkel nem tud elérni, egyszerű szolgáltatáshitelesítő adatokat használhat (ha elérhető). Adja meg egy Active Directory [egyszerű szolgáltatásának](container-registry-auth-service-principal.md) alkalmazásazonosítóját és jelszavát, amely ACRPull hozzáféréssel rendelkezik a forrásbeállításjegyzékhez. Egyszerű szolgáltatás használata akkor hasznos, a build rendszerek és más felügyelet nélküli rendszerek, amelyek lemezképek importálása a rendszerleíró adatbázisba.

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  –-password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Importálás nem Azure-beli magántároló-beállításjegyzékből

Importáljon egy lemezképet egy magánrendszerleíró adatbázisból olyan hitelesítő adatok megadásával, amelyek lehetővé teszik a lekéréses hozzáférést a rendszerleíró adatbázishoz. Például egy lemezképet egy privát Docker-beállításjegyzékből lekell kérni: 

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/sourcerepo/sourceimage:tag \
  --image sourceimage:tag \
  --username <username> \
  --password <password>
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben atárolós rendszerképek importálásáról egy Azure-tároló beállításjegyzékbe egy nyilvános beállításjegyzékből vagy egy másik magánszférabeli beállításjegyzékből. További képimportálási beállításokat az [az acr import][az-acr-import] parancs hivatkozási listájában. 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-import]: /cli/azure/acr#az-acr-import
[azure-cli]: /cli/azure/install-azure-cli
