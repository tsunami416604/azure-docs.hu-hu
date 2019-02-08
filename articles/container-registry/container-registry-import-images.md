---
title: Tárolórendszerképek importálása az Azure Container Registrybe
description: Tárolórendszerképek importálása egy Azure container Registry tárolóregisztrációs adatbázist az Azure API-k, anélkül, hogy a Docker-parancsok futtatását.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 02/06/2019
ms.author: danlep
ms.openlocfilehash: a4187176548a674525d0be0d06bc2557de196af0
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55885660"
---
# <a name="import-container-images-to-a-container-registry"></a>Importálás tárolórendszerképek továbbíthat egy tárolóregisztrációs adatbázisba

Segítségével egyszerűen importálhatja a tárolórendszerképek (Másolás), egy Azure container registryt Docker-parancsok használata nélkül. Például képek fejlesztési beállításjegyzék importálása éles beállításjegyzékhez, vagy nyilvános beállításjegyzék alaplemezképek másolja.

Az Azure Container Registry számos gyakori forgatókönyv rendszerképeket másol a meglévő beállításjegyzékhez kezeli:

* Nyilvános beállításjegyzék importálása

* Importálás az Azure container registryből egy másik, azonos vagy másik Azure-előfizetést

* Nem Azure-beli privát tároló-beállításjegyzék importálása

Kép importálható egy Azure container registryt Docker CLI-parancsok használatához képest a következő előnyökkel jár:

* Ügyfél környezetében nem kell egy helyi Docker-telepítést, mert importálja az összes tárolórendszerképet, és a támogatott operációs rendszer típusától függetlenül.

* Amikor több architektúra rendszerképek (például a hivatalos Docker-rendszerképek), minden architektúrák és a jegyzékfájl listában megadott platformok lemezképek másolja.

Tárolórendszerképek importálásához, ez a cikk igényel, hogy az Azure CLI az Azure Cloud Shellben futtassa vagy helyileg (2.0.55 verzió vagy újabb ajánlott). A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

> [!NOTE]
> Azonos tárolórendszerképek szét a több Azure-régióban van szüksége, ha az Azure Container Registry is támogatja-e [georeplikációs](container-registry-geo-replication.md). Georeplikálásával a beállításjegyzék (prémium szintű Termékváltozat szükséges) a regisztrációs adatbázis rendszerképet, és felcímkézik azonos névvel több régióban is szolgálnak.
>

## <a name="prerequisites"></a>Előfeltételek

Ha még nem rendelkezik egy Azure container Registry tárolóregisztrációs adatbázist, hozzon létre egy beállításjegyzéket. Útmutató: [a rövid útmutató: Az Azure CLI használatával privát tárolóregisztrációs adatbázis létrehozása](container-registry-get-started-azure-cli.md).

Kép importálása az Azure container registry, az identitás írási engedéllyel kell rendelkeznie a célként megadott beállításjegyzék (legalább közreműködői szerepkört). Lásd: [Azure Container Registry-szerepkörökről és engedélyekről](container-registry-roles.md). 

## <a name="import-from-a-public-registry"></a>Nyilvános beállításjegyzék importálása

### <a name="import-from-docker-hub"></a>A Docker Hubból importálása

Például a [az acr-importálási] [ az-acr-import] több architektúrája importálására szolgáló parancs `hello-world:latest` rendszerképet a Docker Hubból való egy jegyzéket *myregistry*. Mivel `hello-world` egy hivatalos kép a Docker Hubból, ez a rendszerkép van az alapértelmezett `library` tárház. Az adattár nevét és opcionálisan egy címkét felvenni értékét a `--source` lemezkép paraméter. (Igény szerint kép alapján azonosíthatja a jegyzékfájl kivonatoló helyett címke, amely garantálja a kép egy adott verzióját szerint.)
 
```azurecli
az acr import --name myregistry --source docker.io/library/hello-world:latest --image hello-world:latest
```

Ellenőrizheti, hogy több jegyzékek társítva a lemezkép futtatásával a `az acr repository show-manifests` parancsot:

```azurecli
az acr repository show-manifests --name myregistry --repository hello-world
```

Az alábbi példa egy nyilvános-lemezképet importálja a `tensorflow` Docker Hub-tárház:

```azurecli
az acr import --name myregistry --source docker.io/tensorflow/tensorflow:latest-gpu --image tensorflow:latest-gpu
```

### <a name="import-from-microsoft-container-registry"></a>Importálás a Microsoft Tárolóregisztrációs adatbázis

Például a a legújabb Windows Server Core lemezképet importálja a `windows` Microsoft Container Registry-tárház.

```azurecli
az acr import --name myregistry --source mcr.microsoft.com/windows/servercore:latest --image servercore:latest
```

## <a name="import-from-another-azure-container-registry"></a>Importálhatja egy másik Azure container registryből

Kép importálhatja egy másik az Azure container Registry-integrált Azure Active Directory-engedélyek használatával.

* Az identitás jogosultnak kell lennie az Azure Active Directory a forrás beállításjegyzék (olvasó szerepkör) olvasni és írni a céloldali beállításjegyzék (közreműködői szerepkört).

* A beállításjegyzék lehetnek azonos vagy egy másik Azure-előfizetésben ugyanahhoz az Active Directory-bérlőhöz.

### <a name="import-from-a-registry-in-the-same-subscription"></a>Azonos előfizetésben található beállításjegyzék importálása

Például importálja a `aci-helloworld:latest` forrás tárolójegyzékből származó lemezkép *mysourceregistry* való *myregistry* az Azure-előfizetéshez.

```azurecli
az acr import --name myregistry --source mysourceregistry.azurecr.io/aci-helloworld:latest --image hello-world:latest
```

Az alábbi példa egy képet importálja a jegyzékfájl kivonatos (SHA-256 kivonatoló,-kiszolgálókként `sha256:...`) helyett a címkét:

```azurecli
az acr import --name myregistry --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>Egy másik előfizetésben található beállításjegyzék importálása

A következő példában *mysourceregistry* a egy másik előfizetésben található *myregistry* az ugyanahhoz az Active Directory-bérlőhöz. Adja meg a forrás beállításjegyzék, az erőforrás-Azonosítóját a `--registry` paraméter.
 
```azurecli
az acr import --name myregistry --source mysourceregistry.azurecr.io/aci-helloworld:latest --image hello-world:latest --registry /subscriptions/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Egyszerű szolgáltatás hitelesítő adatai használatával beállításjegyzék importálása

Importálja a beállításjegyzékből, amely nem fér hozzá az Active Directory-engedélyek használatával, használhatja egyszerű szolgáltatás hitelesítő adatai (ha elérhető). Adja meg az appID és a egy Active Directory jelszava [szolgáltatásnév](container-registry-auth-service-principal.md) ACRPull hozzáférést, amely rendelkezik a forrás beállításjegyzékhez. Egyszerű szolgáltatás használatával hasznos a build és egyéb felügyelet nélküli rendszerek igénylő képek importálását a tárolójegyzékbe.

```azurecli
az acr import --name myregistry --source sourceregistry.azurecr.io/sourcerepo/sourceimage:tag --image targetimage:tag --username <SP_App_ID> –-password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Nem Azure-beli privát tároló-beállításjegyzék importálása

Privát regisztrációs adatbázis hitelesítő adatait, amelyek lehetővé teszik a beállításjegyzék elérését lekéréses megadásával kép importálhat. Ha például egy rendszerkép lekéréshez privát Docker-tárolójegyzék: 

```azurecli
az acr import --name myregistry --source docker.io/sourcerepo/sourceimage:tag --image sourceimage:tag --username <username> --password <password>
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerkedett a tárolórendszerképek az Azure container registry nyilvános beállításjegyzék vagy egy másik privát regisztrációs adatbázis importál. További lemezképhez importálási lehetőségeit, tekintse meg a [az acr-importálási] [ az-acr-import] referencia parancsot. 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-import]: /cli/azure/acr#az-acr-import
[azure-cli]: /cli/azure/install-azure-cli