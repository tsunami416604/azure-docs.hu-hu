---
title: Tároló lemezképek importálása Azure Container Registryba
description: A tároló lemezképeit az Azure API-k használatával importálhatja egy Azure Container registrybe anélkül, hogy a Docker-parancsokat kellene futtatnia.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 02/06/2019
ms.author: danlep
ms.openlocfilehash: c44eabffaefe24e15f980c9871a5c65ab958f2fc
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310617"
---
# <a name="import-container-images-to-a-container-registry"></a>Tároló-lemezképek importálása egy tároló-beállításjegyzékbe

A tárolók lemezképeit egyszerűen importálhatja egy Azure Container registrybe Docker-parancsok használata nélkül. Például importálhatja a lemezképeket egy fejlesztői beállításjegyzékből egy éles beállításjegyzékbe, vagy átmásolhatja az alaplemezképeket egy nyilvános beállításjegyzékből.

Azure Container Registry a lemezképek meglévő beállításjegyzékből való másolásának számos gyakori forgatókönyvét kezeli:

* Importálás nyilvános beállításjegyzékből

* Importálás másik Azure-beli tároló-beállításjegyzékből, ugyanabban vagy egy másik Azure-előfizetésben

* Importálás nem Azure Private Container registryből

A rendszerkép importálása az Azure Container registrybe az alábbi előnyökkel jár a Docker CLI-parancsok használatakor:

* Mivel az ügyfél-környezet nem igényel helyi Docker-telepítést, importálja a tárolók rendszerképét a támogatott operációsrendszer-típustól függetlenül.

* Ha többarchitektúrás képeket (például hivatalos Docker-lemezképeket) importál, a jegyzékben megadott összes architektúrához és platformhoz tartozó lemezképet másolja a rendszer.

A tároló-lemezképek importálásához ehhez a cikkhez az Azure CLI-t Azure Cloud Shell vagy helyileg kell futtatni (2.0.55 vagy újabb verzió ajánlott). A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

> [!NOTE]
> Ha azonos tároló-lemezképeket kell terjeszteni több Azure-régióban, Azure Container Registry a [geo-replikációt](container-registry-geo-replication.md)is támogatja. Ha földrajzilag replikálja a beállításjegyzéket (prémium SKU szükséges), több régiót is felhasználhat, amelyek azonos rendszerkép-és kódelem-névvel rendelkeznek egyetlen beállításjegyzékből.
>

## <a name="prerequisites"></a>Előfeltételek

Ha még nem rendelkezik Azure Container registryvel, hozzon létre egy beállításjegyzéket. A lépéseket [a gyors útmutató: Hozzon létre egy privát tároló-beállításjegyzéket](container-registry-get-started-azure-cli.md)az Azure CLI használatával.

Egy rendszerkép Azure Container registrybe való importálásához az identitásnak írási engedéllyel kell rendelkeznie a célként megadott beállításjegyzékhez (legalább közreműködői szerepkör). Lásd: [Azure Container Registry szerepkörök és engedélyek](container-registry-roles.md). 

## <a name="import-from-a-public-registry"></a>Importálás nyilvános beállításjegyzékből

### <a name="import-from-docker-hub"></a>Importálás a Docker hub-ból

Például az az [ACR import][az-acr-import] paranccsal importálhatja a multi-Architecture `hello-world:latest` rendszerképet a Docker hub-ból egy *myregistry*nevű beállításjegyzékbe. Mivel `hello-world` a a Docker hub hivatalos rendszerképe, ez a rendszerkép az alapértelmezett `library` tárházban található. Adja meg az adattár nevét és opcionálisan egy címkét a `--source` rendszerkép paraméter értékében. (A képet a jegyzékfájlja alapján is azonosíthatja, a címke alapján, amely a rendszerkép egy adott verzióját garantálja.)
 
```azurecli
az acr import --name myregistry --source docker.io/library/hello-world:latest --image hello-world:latest
```

A `az acr repository show-manifests` következő parancs futtatásával ellenőrizheti, hogy a képhez több jegyzékfájl is társítva van-e:

```azurecli
az acr repository show-manifests --name myregistry --repository hello-world
```

Az alábbi példa egy nyilvános rendszerképet importál a `tensorflow` tárházból a Docker hub-ban:

```azurecli
az acr import --name myregistry --source docker.io/tensorflow/tensorflow:latest-gpu --image tensorflow:latest-gpu
```

### <a name="import-from-microsoft-container-registry"></a>Importálás a Microsoft Container Registryból

Importálhatja például a legújabb Windows Server Core rendszerképet a `windows` tárházból a Microsoft Container Registryban.

```azurecli
az acr import --name myregistry --source mcr.microsoft.com/windows/servercore:latest --image servercore:latest
```

## <a name="import-from-another-azure-container-registry"></a>Importálás másik Azure Container registryből

A rendszerképeket az integrált Azure Active Directory engedélyek használatával importálhatja egy másik Azure Container Registry-adatbázisból.

* Az identitásnak Azure Active Directory engedélyekkel kell rendelkeznie a forrás-beállításjegyzékből (olvasói szerepkör) való olvasáshoz, illetve a cél beállításjegyzékbe való íráshoz (közreműködő szerepkör).

* A beállításjegyzék lehet ugyanabban a Active Directory-bérlőben vagy egy másik Azure-előfizetésben.

### <a name="import-from-a-registry-in-the-same-subscription"></a>Importálás ugyanabba az előfizetésbe tartozó beállításjegyzékből

Importálhatja például a `aci-helloworld:latest` rendszerképet egy forrás-beállításjegyzék *mysourceregistry* , hogy ugyanabba az Azure-előfizetésbe *myregistry* .

```azurecli
az acr import --name myregistry --source mysourceregistry.azurecr.io/aci-helloworld:latest --image hello-world:latest
```

Az alábbi példa egy képet a manifest Digest (SHA-256 kivonat, amely a következőképpen `sha256:...`jelenik meg) alapján importál a címke helyett:

```azurecli
az acr import --name myregistry --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>Importálás egy másik előfizetésben lévő beállításjegyzékből

A következő példában a *mysourceregistry* egy másik előfizetésben található a *myregistry* ugyanazon a Active Directory bérlőn. Adja meg a forrás-beállításjegyzék erőforrás-azonosítóját `--registry` a (z) paraméterrel. Figyelje meg, `--source` hogy a paraméter csak a forrás-tárházat és a rendszerkép nevét adja meg, nem pedig a beállításjegyzék bejelentkezési kiszolgálójának nevét.
 
```azurecli
az acr import --name myregistry --source sourcerepo/aci-helloworld:latest --image aci-hello-world:latest --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Importálás beállításjegyzékből az egyszerű szolgáltatásnév hitelesítő adataival

Ha olyan beállításjegyzékből szeretne importálni, amely Active Directory engedélyekkel nem tud hozzáférni, használhatja a szolgáltatás egyszerű hitelesítő adatait (ha elérhető). Adja meg egy olyan Active Directory [egyszerű szolgáltatásnév](container-registry-auth-service-principal.md) appID és jelszavát, amely ACRPull hozzáféréssel rendelkezik a forrás-beállításjegyzékhez. Egyszerű szolgáltatásnév használata olyan rendszerek és más felügyelet nélküli rendszerek esetében hasznos, amelyeknek lemezképeket kell importálni a beállításjegyzékbe.

```azurecli
az acr import --name myregistry --source sourceregistry.azurecr.io/sourcerepo/sourceimage:tag --image targetimage:tag --username <SP_App_ID> –-password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Importálás nem Azure Private Container registryből

Rendszerkép importálása privát beállításjegyzékből olyan hitelesítő adatok megadásával, amelyek lehetővé teszik a lekéréses hozzáférést a beállításjegyzékhez. Például egy privát Docker-beállításjegyzékből származó rendszerkép lekérése: 

```azurecli
az acr import --name myregistry --source docker.io/sourcerepo/sourceimage:tag --image sourceimage:tag --username <username> --password <password>
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan importálhatja a tároló lemezképeit egy nyilvános beállításjegyzékből vagy egy másik privát beállításjegyzékből egy Azure Container registrybe. További Képimportálási lehetőségekért tekintse meg az az [ACR import][az-acr-import] parancs-referenciát. 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-import]: /cli/azure/acr#az-acr-import
[azure-cli]: /cli/azure/install-azure-cli
