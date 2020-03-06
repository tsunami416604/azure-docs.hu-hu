---
title: Áruházi Helm-diagramok
description: Megtudhatja, hogyan tárolhatja a Kubernetes-alkalmazásokhoz tartozó Helm-diagramokat a Azure Container Registry-Tárházak használatával
ms.topic: article
ms.date: 01/28/2020
ms.openlocfilehash: 7969efe37558fffb26b983131c56ae11f3ef9368
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78398960"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>A Helm-diagramok leküldése és lekérése egy Azure Container registrybe

A Kubernetes alkalmazások gyors kezeléséhez és üzembe helyezéséhez használhatja a [nyílt forráskódú Helm Package Managert][helm]. A Helm esetében az alkalmazáscsomag olyan [diagramokként](https://helm.sh/docs/topics/charts/)van definiálva, amelyeket a rendszer a [Helm diagram adattárában](https://helm.sh/docs/topics/chart_repository/)gyűjt és tárol.

Ebből a cikkből megtudhatja, hogyan futtathat Helm-diagramokat az Azure Container registryben lévő adattárakban egy Helm 3 vagy Helm 2 telepítés használatával. Ebben a példában egy meglévő Helm-diagramot tárolunk a Public Helm *stabil* adattárházból. Számos esetben saját diagramokat hozhat létre és tölthet fel a fejlesztés alatt lévő alkalmazásokhoz. A saját Helm-diagramok létrehozásával kapcsolatos további információkért lásd a [diagram sablon fejlesztői útmutatóját][develop-helm-charts].

> [!IMPORTANT]
> A Azure Container Registry Helm-diagramok támogatása jelenleg előzetes verzióban érhető el. Az előzetes verziók abban a feltételben érhetők el, hogy Ön elfogadja a kiegészítő [használati feltételeket][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="helm-3-or-helm-2"></a>3\. Helm vagy Helm 2?

A Helm-diagramok tárolásához, kezeléséhez és telepítéséhez egy Helm-ügyfelet és a Helm CLI-t használhatja. A Helm-ügyfél főbb kiadásai között a Helm 3 és a Helm 2 is szerepel. A Helm 3 új diagram formátumot támogat, és már nem telepíti a kormányrúd kiszolgálóoldali összetevőt. A verziók közötti különbségekről a [Gyakori kérdések](https://helm.sh/docs/faq/)című témakörben talál további információt. Ha korábban már telepítette a Helm 2 diagramot, tekintse meg [a Helm v2 áttelepítése v3-](https://helm.sh/docs/topics/v2_v3_migration/)re című témakört.

A Helm 3 vagy a Helm 2 használatával a Helm-diagramokat Azure Container Registry, az egyes verziókhoz tartozó munkafolyamatokkal futtathatja:

* [3. Helm-ügyfél](#use-the-helm-3-client) – `helm chart` parancsok használatával kezelheti a beállításjegyzékben lévő diagramokat [OCI](container-registry-image-formats.md#oci-artifacts) -összetevőkként
* [2. Helm-ügyfél](#use-the-helm-2-client) – használja az az [ACR Helm][az-acr-helm] parancsait az Azure CLI-ben a tároló-beállításjegyzék Helm diagram-tárházként való hozzáadásához és kezeléséhez

### <a name="additional-information"></a>További információ

* Javasoljuk, hogy használja a Helm 3 munkafolyamatot natív `helm chart` parancsokkal a diagramok OCI-összetevőkként való kezeléséhez.
* Az örökölt az [ACR Helm][az-acr-helm] Azure CLI-parancsokat és-munkafolyamatokat a Helm 3 ügyféllel és diagramokkal is használhatja. Azonban bizonyos parancsok, például a `az acr helm list` nem kompatibilisek a Helm 3 diagramjaival.
* A Helm 3 esetében az [az ACR Helm][az-acr-helm] parancsok elsősorban a Helm 2 ügyfél-és diagram-formátumával való kompatibilitás érdekében támogatottak. A parancsok jövőbeli fejlesztése jelenleg nem tervezett.

## <a name="use-the-helm-3-client"></a>A Helm 3-ügyfél használata

### <a name="prerequisites"></a>Előfeltételek

- Azure **Container Registry** az Azure-előfizetésében. Ha szükséges, hozzon létre egy beállításjegyzéket a [Azure Portal](container-registry-get-started-portal.md) vagy az [Azure CLI](container-registry-get-started-azure-cli.md)használatával.
- A **Helm-ügyfél 3.0.0-es vagy újabb verziója** – futtassa a `helm version`t a jelenlegi verzió megkereséséhez. A Helm telepítésével és frissítésével kapcsolatos további információkért lásd: a [Helm telepítése][helm-install].
- **Egy Kubernetes-fürt** , ahol egy Helm-diagramot fog telepíteni. Ha szükséges, hozzon létre egy [Azure Kubernetes Service-fürtöt][aks-quickstart]. 
- Az **Azure CLI verziója 2.0.71 vagy újabb** verziójú `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

### <a name="high-level-workflow"></a>Magas szintű munkafolyamat

A **Helm 3** esetében:

* Létrehozhat egy vagy több Helm-tárházat egy Azure Container registryben
* A [OCI](container-registry-image-formats.md#oci-artifacts)-összetevőket tartalmazó Helm 3 diagramokat egy beállításjegyzékben tárolja. Jelenleg a Helm 3 támogatása a OCI esetében *kísérleti jellegűnek*tekintendő.
* `helm chart` parancsok közvetlen használata a Helm CLI-ből egy beállításjegyzékben a Helm-diagramok leküldéséhez, lekéréséhez és kezeléséhez
* Az Azure CLI-n keresztül végezheti el a hitelesítést a beállításjegyzékben, amely ezután automatikusan frissíti a Helm-ügyfelet a beállításjegyzék URI azonosítójának és hitelesítő adataival. Nem kell manuálisan megadnia a beállításjegyzék adatait, így a hitelesítő adatok nem jelennek meg a parancs előzményeiben.
* A `helm install` használatával diagramokat telepíthet egy Kubernetes-fürtre egy helyi adattár-gyorsítótárból.

Példákat a következő részekben talál.

### <a name="enable-oci-support"></a>OCI-támogatás engedélyezése

Állítsa be az alábbi környezeti változót, hogy engedélyezze a OCI támogatását a Helm 3 ügyfélen. Jelenleg ez a támogatás kísérleti jellegű. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

### <a name="pull-an-existing-helm-package"></a>Meglévő Helm-csomag lekérése

Ha még nem adta hozzá a `stable` Helm chart-tárházat, futtassa a `helm repo add` parancsot:

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com
```

Húzzon egy diagram-csomagot a `stable`-tárházból helyileg. Hozzon létre például egy helyi könyvtárat, például *~/ACR-Helm*, majd töltse le a meglévő *stabil/WordPress* chart csomagot. (Ez a példa és a cikkben szereplő egyéb parancsok a bash-rendszerhéjhoz vannak formázva.)

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm pull stable/wordpress --untar
```

A `helm pull stable/wordpress` parancs nem adott meg egy adott verziót, ezért a rendszer lehúzta és kibontja a *legújabb* verziót a `wordpress` alkönyvtárban.

### <a name="save-chart-to-local-registry-cache"></a>Diagram mentése helyi beállításjegyzék-gyorsítótárba

Módosítsa a könyvtárat a `wordpress` alkönyvtárra, amely a Helm-diagram fájljait tartalmazza. Ezután futtassa `helm chart save` a diagram helyi másolatának mentéséhez, és hozzon létre egy aliast a beállításjegyzék teljes nevével és a célként megadott adattárral és címkével. 

A következő példában a beállításjegyzék neve *mycontainerregistry*, a cél tárháza *WordPress*, a célként megadott diagram címkéje pedig *legújabb*, de a környezethez tartozó helyettesítő értékek:

```console
cd wordpress
helm chart save . wordpress:latest
helm chart save . mycontainerregistry.azurecr.io/helm/wordpress:latest
```

`helm chart list` futtatásával erősítse meg, hogy mentette a diagramokat a helyi beállításjegyzék-gyorsítótárban. A kimenet a következőhöz hasonló:

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
wordpress:latest                                         wordpress       8.1.0   5899db0 29.1 KiB        1 day 
mycontainerregistry.azurecr.io/helm/wordpress:latest     wordpress       8.1.0   5899db0 29.1 KiB        1 day 
```

### <a name="push-chart-to-azure-container-registry"></a>Diagram leküldése Azure Container Registry

Futtassa az `helm chart push` parancsot a Helm 3 CLI-ben, hogy a Helm diagramot egy adattárba küldje el az Azure Container registryben. Ha nem létezik, a rendszer létrehozza a tárházat.

Először használja az Azure CLI-parancsot az [ACR login][az-acr-login] használatával a beállításjegyzékben való hitelesítéshez:

```azurecli
az acr login --name mycontainerregistry
```

A diagram leküldése a teljes cél tárházba:

```console
helm chart push mycontainerregistry.azurecr.io/helm/wordpress:latest
```

Sikeres leküldést követően a kimenet a következőhöz hasonló:

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/wordpress]
ref:     mycontainerregistry.azurecr.io/helm/wordpress:latest
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    29.1 KiB
name:    wordpress
version: 8.1.0
```

### <a name="list-charts-in-the-repository"></a>A tárházban lévő diagramok listázása

Az Azure Container registryben tárolt rendszerképekhez hasonlóan az [az ACR adattár][az-acr-repository] parancsaival is megjelenítheti a diagramokat üzemeltető adattárakat, valamint a diagram címkéit és jegyzékeit. 

Futtassa például az az [ACR adattár show][az-acr-repository-show] parancsot az előző lépésben létrehozott tárház tulajdonságainak megtekintéséhez:

```azurecli
az acr repository show \
  --name mycontainerregistry \
  --repository helm/wordpress
```

A kimenet a következőhöz hasonló:

```output
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2020-01-29T16:54:30.1514833Z",
  "imageName": "helm/wordpress",
  "lastUpdateTime": "2020-01-29T16:54:30.4992247Z",
  "manifestCount": 1,
  "registry": "mycontainerregistry.azurecr.io",
  "tagCount": 1
}
```

A tárházban tárolt diagram részleteinek megtekintéséhez futtassa az az [ACR repository show-Manifests][az-acr-repository-show-manifests] parancsot. Például:

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/wordpress --detail
```

Az ebben a példában szereplő kimenet a `application/vnd.cncf.helm.config.v1+json``configMediaType`ét jeleníti meg:

```output
[
  {
    [...]
    "configMediaType": "application/vnd.cncf.helm.config.v1+json",
    "createdTime": "2020-01-29T16:54:30.2382436Z",
    "digest": "sha256:xxxxxxxx51bc0807bfa97cb647e493ac381b96c1f18749b7388c24bbxxxxxxxxx",
    "imageSize": 29995,
    "lastUpdateTime": "2020-01-29T16:54:30.3492436Z",
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "tags": [
      "latest"
    ]
  }
]
```

### <a name="pull-chart-to-local-cache"></a>Diagram lekérése a helyi gyorsítótárba

Ha egy Helm-diagramot szeretne Kubernetes telepíteni, a diagramnak a helyi gyorsítótárban kell lennie. Ebben a példában először futtassa `helm chart remove` a `mycontainerregistry.azurecr.io/helm/wordpress:latest`nevű meglévő helyi diagram eltávolításához:

```console
helm chart remove mycontainerregistry.azurecr.io/helm/wordpress:latest
```

`helm chart pull` futtatásával töltse le a diagramot az Azure Container registryből a helyi gyorsítótárba:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/wordpress:latest
```

### <a name="export-helm-chart"></a>Helm-diagram exportálása

A diagram további működéséhez exportálja azt egy helyi könyvtárba `helm chart export`használatával. Exportálja például a `install` könyvtárba húzott diagramot:

```console
helm chart export mycontainerregistry.azurecr.io/helm/wordpress:latest --destination ./install
```

A tárházban lévő exportált diagram adatainak megtekintéséhez futtassa a `helm inspect chart` parancsot abban a könyvtárban, ahol a diagramot exportálta.

```console
cd install
helm inspect chart wordpress
```

Ha nincs megadva verziószám, a rendszer a *legújabb* verziót használja. A Helm a diagram részletes információit adja vissza, ahogy az a következő tömörített kimenetben látható:

```output
apiVersion: v1
appVersion: 5.3.2
dependencies:
- condition: mariadb.enabled
  name: mariadb
  repository: https://kubernetes-charts.storage.googleapis.com/
  tags:
  - wordpress-database
  version: 7.x.x
description: Web publishing platform for building blogs and websites.
home: http://www.wordpress.com/
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
  name: Bitnami
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 8.1.0
```

### <a name="install-helm-chart"></a>A Helm-diagram telepítése

A `helm install` futtatásával telepítheti a Helm-diagramot a helyi gyorsítótárba, és exportálhatja. Adja meg a kiadás nevét, vagy adja át a `--generate-name` paramétert. Például:

```console
helm install wordpress --generate-name
```

A telepítés folytatásához kövesse a parancs kimenetében található utasításokat a WorPress URL-címeinek és hitelesítő adatainak megtekintéséhez. Az `kubectl get pods` parancs futtatásával is megtekintheti a Helm-diagramon üzembe helyezett Kubernetes-erőforrásokat:

```output
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Helm-diagram törlése az adattárból

Ha törölni szeretne egy diagramot a tárházból, használja az az [ACR repository delete][az-acr-repository-delete] parancsot. Futtassa a következő parancsot, és erősítse meg a műveletet, amikor a rendszer kéri:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/wordpress:latest
```

## <a name="use-the-helm-2-client"></a>A Helm 2 ügyfél használata

### <a name="prerequisites"></a>Előfeltételek

- Azure **Container Registry** az Azure-előfizetésében. Ha szükséges, hozzon létre egy beállításjegyzéket a [Azure Portal](container-registry-get-started-portal.md) vagy az [Azure CLI](container-registry-get-started-azure-cli.md)használatával.
- A **Helm-ügyfél verziója 2.11.0 (nem RC verzió) vagy később** – `helm version` futtatásával megkeresheti az aktuális verziót. Szükség van egy Kubernetes-fürtön belül inicializált Helm-kiszolgálóra is. Ha szükséges, hozzon létre egy [Azure Kubernetes Service-fürtöt][aks-quickstart]. A Helm telepítésével és frissítésével kapcsolatos további információkért lásd: a [Helm telepítése][helm-install-v2].
- Az **Azure CLI verziója 2.0.46 vagy újabb** verziójú `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

### <a name="high-level-workflow"></a>Magas szintű munkafolyamat

A **Helm 2** esetében:

* Konfigurálja az Azure Container registryt *egyetlen* Helm diagram adattárként. Azure Container Registry kezeli az index definícióját, amikor diagramokat vesz fel és távolít el a tárházba.
* Az Azure CLI-ben az az [ACR Helm][az-acr-helm] paranccsal adhatja hozzá az Azure Container registryt a Helm chart-tárházhoz, és leküldheti és kezelheti a diagramokat. Ezek az Azure CLI-parancsok a Helm 2-ügyfél parancsait takarják.
* Adja hozzá a diagram tárházát az Azure Container registryben a helyi Helm-adattár indexéhez, amely támogatja a diagram keresését
* Az Azure CLI használatával hitelesítheti magát az Azure Container registryben, amely ezután automatikusan frissíti a Helm-ügyfelet a beállításjegyzék URI azonosítójának és hitelesítő adataival. Nem kell manuálisan megadnia a beállításjegyzék adatait, így a hitelesítő adatok nem jelennek meg a parancs előzményeiben.
* A `helm install` használatával diagramokat telepíthet egy Kubernetes-fürtre egy helyi adattár-gyorsítótárból.

Példákat a következő részekben talál.

### <a name="add-repository-to-helm-client"></a>Tárház hozzáadása a Helm-ügyfélhez

Adja hozzá a Azure Container Registry Helm diagram-tárházát a Helm-ügyfeléhez az az [ACR Helm repo Add][az-acr-helm-repo-add] paranccsal. Ez a parancs beolvassa a Helm-ügyfél által használt Azure Container Registry hitelesítési jogkivonatát. A hitelesítési jogkivonat 3 óráig érvényes. A `docker login`hoz hasonlóan a parancs a későbbi CLI-munkamenetekben is futtatható, hogy a Helm-ügyfelet a Azure Container Registry Helm chart adattárral hitelesítse:

```azurecli
az acr helm repo add --name mycontainerregistry
```

### <a name="add-a-chart-to-the-repository"></a>Diagram hozzáadása a tárházhoz

Először hozzon létre egy helyi könyvtárat a *~/ACR-Helm*, majd töltse le a meglévő *stabil/WordPress-* diagramot:

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm repo update
helm fetch stable/wordpress
```

Írja be a `ls` a letöltött diagram listázásához, és jegyezze fel a fájlnévben szereplő WordPress-verziót. A `helm fetch stable/wordpress` parancs nem adott meg egy adott verziót, így a *legújabb* verzió lett beolvasva. A következő példa kimenetében a WordPress-diagram a *8.1.0*verziója:

```output
wordpress-8.1.0.tgz
```

A diagramot leküldheti a Helm chart adattárba Azure Container Registry az az [ACR Helm push][az-acr-helm-push] paranccsal az Azure CLI-ben. Adja meg az előző lépésben letöltött Helm-diagram nevét, például *WordPress-8.1.0. tgz*:

```azurecli
az acr helm push --name mycontainerregistry wordpress-8.1.0.tgz
```

Néhány pillanat elteltével az Azure CLI jelentést készít a diagram mentéséről, ahogy az a következő példában látható:

```output
{
  "saved": true
}
```

### <a name="list-charts-in-the-repository"></a>A tárházban lévő diagramok listázása

Az előző lépésben feltöltött diagram használatához frissíteni kell a helyi Helm adattár-indexet. Átindexelheti a tárházat a Helm-ügyfélben, vagy az Azure CLI használatával frissítheti az adattár-indexet. Minden alkalommal, amikor diagramot vesz fel a tárházba, ezt a lépést végre kell hajtania:

```azurecli
az acr helm repo add --name mycontainerregistry
```

Ha a tárházban tárolt diagramot és a frissített indexet helyileg is elérhetővé szeretné tenni, a kereséshez vagy a telepítéshez használhatja a normál Helm-ügyfél parancsait. A tárház összes diagramjának megjelenítéséhez használja a `helm search` parancsot, amely a saját Azure Container Registry nevét adja meg:

```console
helm search mycontainerregistry
```

Az előző lépésben leküldett WordPress-diagram a következő példában látható módon jelenik meg:

```output
NAME                CHART VERSION   APP VERSION DESCRIPTION
helmdocs/wordpress  8.1.0           5.3.2       Web publishing platform for building blogs and websites.
```

A diagramokat az Azure CLI-vel is listázhatja az az [ACR Helm List][az-acr-helm-list]paranccsal:

```azurecli
az acr helm list --name mycontainerregistry
```

### <a name="show-information-for-a-helm-chart"></a>Helm-diagram adatainak megjelenítése

A tárház adott diagramjának információit a `helm inspect` parancs használatával tekintheti meg.

```console
helm inspect mycontainerregistry/wordpress
```

Ha nincs megadva verziószám, a rendszer a *legújabb* verziót használja. A Helm a diagram részletes információit adja vissza, ahogy az a következő tömörített példában látható:

```output
apiVersion: v1
appVersion: 5.3.2
description: Web publishing platform for building blogs and websites.
engine: gotpl
home: http://www.wordpress.com/
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
  name: Bitnami
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 8.1.0
[...]
```

A diagramra vonatkozó információkat az Azure CLI az [ACR Helm show][az-acr-helm-show] paranccsal is megjelenítheti. A rendszer a diagram *legújabb* verzióját is alapértelmezés szerint visszaadja. `--version` hozzáfűzésével listázhatja a diagram egy adott verzióját, például a *8.1.0*:

```azurecli
az acr helm show --name mycontainerregistry wordpress
```

### <a name="install-a-helm-chart-from-the-repository"></a>Helm-diagram telepítése az adattárból

A tárházban található Helm diagramot a tárház nevének és a diagram nevének megadásával telepítheti. Telepítse a WordPress-diagramot a Helm ügyfél használatával:

```console
helm install mycontainerregistry/wordpress
```

> [!TIP]
> Ha leküldi a Azure Container Registry Helm diagram adattárát, és később visszatér egy új CLI-munkamenetben, a helyi Helm-ügyfélnek frissített hitelesítési tokenre van szüksége. Új hitelesítési jogkivonat beszerzéséhez használja az az [ACR Helm repo Add][az-acr-helm-repo-add] parancsot.

A telepítési folyamat során a következő lépések végezhetők el:

- A Helm-ügyfél a helyi tárház indexét keresi.
- A rendszer letölti a megfelelő diagramot a Azure Container Registry adattárból.
- A diagram a Kubernetes-fürtön a kormányrúd használatával van üzembe helyezve.

A telepítés folytatásához kövesse a parancs kimenetében található utasításokat a WorPress URL-címeinek és hitelesítő adatainak megtekintéséhez. Az `kubectl get pods` parancs futtatásával is megtekintheti a Helm-diagramon üzembe helyezett Kubernetes-erőforrásokat:

```output
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Helm-diagram törlése az adattárból

Ha törölni szeretne egy diagramot a tárházból, használja az az [ACR Helm delete][az-acr-helm-delete] parancsot. Adja meg a diagram nevét, például a *WordPress*nevet és a törölni kívánt verziót, például *8.1.0*.

```azurecli
az acr helm delete --name mycontainerregistry wordpress --version 8.1.0
```

Ha törölni szeretné a megnevezett diagram összes verzióját, hagyja ki a `--version` paramétert.

`helm search`futtatásakor a diagram továbbra is visszakerül. A Helm-ügyfél ismét nem frissíti automatikusan az elérhető diagramok listáját egy adattárban. A Helm Client repo index frissítéséhez használja újra az az [ACR Helm repo Add][az-acr-helm-repo-add] parancsot:

```azurecli
az acr helm repo add --name mycontainerregistry
```

## <a name="next-steps"></a>Következő lépések

Ez a cikk egy meglévő Helm-diagramot használt a nyilvános *stabil* adattárból. A Helm-diagramok létrehozásával és telepítésével kapcsolatos további információkért lásd: [Helm-diagramok fejlesztése][develop-helm-charts].

A Helm-diagramok a tároló-létrehozási folyamat részeként használhatók. További információ: [Azure Container Registry feladatok használata][acr-tasks].

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-v2]: https://v2.helm.sh/docs/using_helm/#installing-helm
[develop-helm-charts]: https://helm.sh/docs/chart_template_guide/
[semver2]: https://semver.org/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm]: /cli/azure/acr/helm
[az-acr-repository]: /cli/azure/acr/repository
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-helm-repo-add]: /cli/azure/acr/helm/repo#az-acr-helm-repo-add
[az-acr-helm-push]: /cli/azure/acr/helm#az-acr-helm-push
[az-acr-helm-list]: /cli/azure/acr/helm#az-acr-helm-list
[az-acr-helm-show]: /cli/azure/acr/helm#az-acr-helm-show
[az-acr-helm-delete]: /cli/azure/acr/helm#az-acr-helm-delete
[acr-tasks]: container-registry-tasks-overview.md
