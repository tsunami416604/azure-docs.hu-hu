---
title: Áruházi Helm-diagramok
description: Megtudhatja, hogyan használhatók az alkalmazások diagramjai a Azure Container Registry használatával
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: 0c5e66d5f2fc3dd3c2d8c0a975c3e9d1c813732d
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456338"
---
# <a name="use-azure-container-registry-as-a-helm-repository-for-your-application-charts"></a>Azure Container Registry használata az alkalmazás-diagramok Helm-tárháza

A Kubernetes alkalmazások gyors kezeléséhez és üzembe helyezéséhez használhatja a [nyílt forráskódú Helm Package Managert][helm]. A Helm esetében az alkalmazások a Helm chart adattárában tárolt *diagramokként* vannak meghatározva. Ezek a diagramok a konfigurációkat és a függőségeket határozzák meg, és az alkalmazás életciklusa során is telepíthetők. Azure Container Registry használható a Helm chart-Tárházak gazdagépként.

A Azure Container Registry rendelkezik egy privát, biztonságos Helm chart adattárral, amely integrálható a Build-folyamatokkal vagy más Azure-szolgáltatásokkal. A Helm diagram adattárainak Azure Container Registry tartalmaz geo-replikációs funkciókat, hogy a diagramok a központi telepítések és a redundancia érdekében is megmaradjanak. Csak a diagramok által használt tárterületért kell fizetnie, és az összes Azure Container Registry.

Ez a cikk bemutatja, hogyan használhatja a Azure Container Registryban tárolt Helm chart-tárházat.

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="before-you-begin"></a>Előkészületek

A cikkben szereplő lépések végrehajtásához a következő előfeltételek teljesülése szükséges:

- **Azure Container Registry** – hozzon létre egy tároló-beállításjegyzéket az Azure-előfizetésében. Használja például a [Azure Portal](container-registry-get-started-portal.md) vagy az [Azure CLI](container-registry-get-started-azure-cli.md)-t.
- A **Helm-ügyfél verziója 2.11.0 (nem RC verzió) vagy később** – `helm version` futtatásával megkeresheti az aktuális verziót. Szükség van egy Kubernetes-fürtön belül inicializált Helm-kiszolgálóra is. Szükség esetén [létrehozhat egy Azure Kubernetes Service-fürtöt][aks-quickstart]is. A Helm telepítésével és frissítésével kapcsolatos további információkért lásd: a [Helm telepítése][helm-install].
- Az **Azure CLI verziója 2.0.46 vagy újabb** verziójú `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="add-a-repository-to-helm-client"></a>Adattár hozzáadása Helm-ügyfélhez

A Helm-tárház egy olyan HTTP-kiszolgáló, amely képes a Helm-diagramok tárolására. A Azure Container Registry megadhatja ezt a tárolót a Helm-diagramok számára, és kezelheti az index definícióját, amikor diagramokat vesz fel és távolít el a tárházba.

A Azure Container Registry Helm chart adattárként való hozzáadásához használja az Azure CLI-t. Ezzel a módszerrel a Helm-ügyfelet a Azure Container Registry által támogatott adattár URI-ja és hitelesítő adataival frissíti. Nem kell manuálisan megadnia a tárház adatait, így a hitelesítő adatok nem jelennek meg a parancs előzményeiben, például:.

Ha szükséges, jelentkezzen be az Azure CLI-be, és kövesse az utasításokat:

```azurecli
az login
```

Az az [configure][az-configure] paranccsal konfigurálja az Azure CLI alapértelmezett értékeit a Azure Container Registry nevével. Az alábbi példában cserélje le a `<acrName>`t a beállításjegyzék nevére:

```azurecli
az configure --defaults acr=<acrName>
```

Most adja hozzá a Azure Container Registry Helm diagram-tárházát a Helm-ügyfeléhez az az [ACR Helm repo Add][az-acr-helm-repo-add] paranccsal. Ez a parancs beolvassa a Helm-ügyfél által használt Azure Container Registry hitelesítési jogkivonatát. A hitelesítési jogkivonat 1 órára érvényes. A `docker login`hoz hasonlóan a parancs a későbbi CLI-munkamenetekben is futtatható, hogy a Helm-ügyfelet a Azure Container Registry Helm chart adattárral hitelesítse:

```azurecli
az acr helm repo add
```

## <a name="add-a-chart-to-the-repository"></a>Diagram hozzáadása a tárházhoz

Ebből a cikkből megtudhatja, hogyan szerezhet be egy meglévő Helm Chartot a Public Helm *stabil* adattárházból. A *stabil* tárház egy olyan kurátori, nyilvános tárház, amely közös alkalmazás-diagramokat tartalmaz. A Package karbantartók a diagramokat a *stabil* tárházba is beküldhetik, ugyanúgy, ahogy a Docker hub nyilvános beállításjegyzéket biztosít a közös tároló lemezképekhez. Ezután a nyilvános *stabil* tárházból letöltött diagramot leküldheti a privát Azure Container Registry adattárba. A legtöbb esetben felépítheti és feltöltheti saját diagramjait az Ön által fejlesztett alkalmazásokhoz. A saját Helm-diagramok létrehozásával kapcsolatos további információkért lásd: [Helm-diagramok fejlesztése][develop-helm-charts].

Először hozzon létre egy könyvtárat a *~/ACR-Helm*, majd töltse le a meglévő *stabil/WordPress-* diagramot:

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm fetch stable/wordpress
```

Sorolja fel a letöltött diagramot, és jegyezze fel a fájlnévben szereplő WordPress-verziót. A `helm fetch stable/wordpress` parancs nem adott meg egy adott verziót, így a *legújabb* verzió lett beolvasva. Minden Helm-diagram tartalmaz egy verziószámot a fájlnévben, amely a [SemVer 2][semver2] szabványt követi. A következő példa kimenetében a WordPress-diagram a *2.1.10*verziója:

```
$ ls

wordpress-2.1.10.tgz
```

Most küldje el a diagramot a Helm diagram adattárában Azure Container Registry az Azure CLI az [ACR Helm push][az-acr-helm-push] paranccsal. Adja meg az előző lépésben letöltött Helm-diagram nevét, például *WordPress-2.1.10. tgz*:

```azurecli
az acr helm push wordpress-2.1.10.tgz
```

Néhány pillanat elteltével az Azure CLI azt jelenti, hogy a diagram mentése megtörtént, ahogy az a következő példában látható:

```
$ az acr helm push wordpress-2.1.10.tgz

{
  "saved": true
}
```

## <a name="list-charts-in-the-repository"></a>A tárházban lévő diagramok listázása

A Helm-ügyfél karbantartja a távoli Tárházak tartalmának helyi gyorsítótárazott másolatát. A távoli tárház módosításai nem frissítik automatikusan a Helm-ügyfél által helyileg ismert elérhető diagramok listáját. Amikor adattárakban keres diagramokat, a Helm a helyi gyorsítótárazott indexet használja. Az előző lépésben feltöltött diagram használatához frissíteni kell a helyi Helm adattár-indexet. Átindexelheti a tárházat a Helm-ügyfélben, vagy az Azure CLI használatával frissítheti az adattár-indexet. Minden alkalommal, amikor diagramot vesz fel a tárházba, ezt a lépést végre kell hajtania:

```azurecli
az acr helm repo add
```

Ha a tárházban tárolt diagramot és a frissített indexet helyileg is elérhetővé szeretné tenni, a kereséshez vagy a telepítéshez használhatja a normál Helm-ügyfél parancsait. A tárház összes diagramjának megjelenítéséhez használja a `helm search <acrName>`. Adja meg a saját Azure Container Registry nevét:

```console
helm search <acrName>
```

Az előző lépésben leküldett WordPress-diagram a következő példában látható módon jelenik meg:

```
$ helm search myacrhelm

NAME                CHART VERSION   APP VERSION DESCRIPTION
helmdocs/wordpress  2.1.10          4.9.8       Web publishing platform for building blogs and websites.
```

A diagramokat az Azure CLI-vel is listázhatja az az [ACR Helm List][az-acr-helm-list]paranccsal:

```azurecli
az acr helm list
```

## <a name="show-information-for-a-helm-chart"></a>Helm-diagram adatainak megjelenítése

A tárházban található adott diagram adatainak megtekintéséhez újra használhatja a normál Helm-ügyfelet. Ha szeretné megtekinteni a *WordPress*nevű diagram adatait, használja a `helm inspect`.

```console
helm inspect <acrName>/wordpress
```

Ha nincs megadva verziószám, a rendszer a *legújabb* verziót használja. A Helm a diagram részletes információit adja vissza, ahogy az a következő tömörített példában látható:

```
$ helm inspect myacrhelm/wordpress

appVersion: 4.9.8
description: Web publishing platform for building blogs and websites.
engine: gotpl
home: https://www.wordpress.com/
icon: https://bitnami.com/assets/stacks/wordpress/img/wordpress-stack-220x234.png
keywords:
- wordpress
- cms
- blog
- http
- web
- application
- php
maintainers:
- email: containers@bitnami.com
  name: bitnami-bot
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 2.1.10
[...]
```

A diagramra vonatkozó információkat az Azure CLI az [ACR Helm show][az-acr-helm-show] paranccsal is megjelenítheti. A rendszer a diagram *legújabb* verzióját is alapértelmezés szerint visszaadja. `--version` hozzáfűzésével listázhatja a diagram egy adott verzióját, például a *2.1.10*:

```azurecli
az acr helm show wordpress
```

## <a name="install-a-helm-chart-from-the-repository"></a>Helm-diagram telepítése az adattárból

A tárházban található Helm diagramot a tárház nevének, majd a diagram nevének megadásával telepítheti. Telepítse a WordPress-diagramot a Helm ügyfél használatával:

```console
helm install <acrName>/wordpress
```

> [!TIP]
> Ha leküldi a Azure Container Registry Helm diagram adattárát, és később visszatér egy új CLI-munkamenetben, a helyi Helm-ügyfélnek frissített hitelesítési tokenre van szüksége. Új hitelesítési jogkivonat beszerzéséhez használja az az [ACR Helm repo Add][az-acr-helm-repo-add] parancsot.

A telepítési folyamat során a következő lépések végezhetők el:

- A Helm-ügyfél a helyi tárház indexét keresi.
- A rendszer letölti a megfelelő diagramot a Azure Container Registry adattárból.
- A diagram a Kubernetes-fürtön a kormányrúd használatával van üzembe helyezve.

A következő összehasonlított példa kimenete a Helm diagramon üzembe helyezett Kubernetes-erőforrásokat mutatja be:

```
$ helm install myacrhelm/wordpress

NAME:   irreverent-jaguar
LAST DEPLOYED: Thu Sep 13 21:44:20 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                                          READY  STATUS   RESTARTS  AGE
irreverent-jaguar-wordpress-7ff46d9b8c-b7v6m  0/1    Pending  0         1s
irreverent-jaguar-mariadb-0                   0/1    Pending  0         1s
[...]
```

## <a name="delete-a-helm-chart-from-the-repository"></a>Helm-diagram törlése az adattárból

Ha törölni szeretne egy diagramot a tárházból, használja az az [ACR Helm delete][az-acr-helm-delete] parancsot. Adja meg a diagram nevét, például a *WordPress*nevet és a törölni kívánt verziót, például *2.1.10*.

```azurecli
az acr helm delete wordpress --version 2.1.10
```

Ha törölni szeretné a megnevezett diagram összes verzióját, hagyja ki a `--version` paramétert.

A diagramot a `helm search <acrName>`továbbra is visszaadja. A Helm-ügyfél ismét nem frissíti automatikusan az elérhető diagramok listáját egy adattárban. A Helm Client repo index frissítéséhez használja újra az az [ACR Helm repo Add][az-acr-helm-repo-add] parancsot:

```azurecli
az acr helm repo add
```

## <a name="next-steps"></a>További lépések

Ez a cikk egy meglévő Helm-diagramot használt a nyilvános *stabil* adattárból. A Helm-diagramok létrehozásával és telepítésével kapcsolatos további információkért lásd: [Helm-diagramok fejlesztése][develop-helm-charts].

A Helm-diagramok a tároló-létrehozási folyamat részeként használhatók. További információ: [Azure Container Registry feladatok használata][acr-tasks].

További információ a Azure Container Registry használatáról és kezeléséről: [ajánlott eljárások][acr-bestpractices].

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[develop-helm-charts]: https://helm.sh/docs/topics/charts/
[semver2]: https://semver.org/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm-repo-add]: /cli/azure/acr/helm/repo#az-acr-helm-repo-add
[az-acr-helm-push]: /cli/azure/acr/helm#az-acr-helm-push
[az-acr-helm-list]: /cli/azure/acr/helm#az-acr-helm-list
[az-acr-helm-show]: /cli/azure/acr/helm#az-acr-helm-show
[az-acr-helm-delete]: /cli/azure/acr/helm#az-acr-helm-delete
[acr-tasks]: container-registry-tasks-overview.md
