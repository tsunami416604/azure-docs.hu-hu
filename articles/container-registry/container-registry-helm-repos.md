---
title: Áruházi Helm-diagramok
description: Megtudhatja, hogyan tárolhatja a Kubernetes-alkalmazásokhoz tartozó Helm-diagramokat a Azure Container Registry-Tárházak használatával
ms.topic: article
ms.date: 03/20/2020
ms.openlocfilehash: 3f1a68258b758380a66b63e3c3137f1d460d288c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81399380"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>A Helm-diagramok leküldése és lekérése egy Azure Container registrybe

A Kubernetes alkalmazások gyors kezeléséhez és üzembe helyezéséhez használhatja a [nyílt forráskódú Helm Package Managert][helm]. A Helm esetében az alkalmazáscsomag olyan [diagramokként](https://helm.sh/docs/topics/charts/)van definiálva, amelyeket a rendszer a [Helm diagram adattárában](https://helm.sh/docs/topics/chart_repository/)gyűjt és tárol.

Ez a cikk bemutatja, hogyan üzemeltetheti a Helm-diagramok tárházait egy Azure Container registryben, a Helm 3 vagy a Helm 2 telepítésének használatával. Számos esetben saját diagramokat hozhat létre és tölthet fel a fejlesztés alatt lévő alkalmazásokhoz. A saját Helm-diagramok létrehozásával kapcsolatos további információkért lásd a [diagram sablon fejlesztői útmutatóját][develop-helm-charts]. Egy másik Helm-tárházból is tárolhat egy meglévő Helm-diagramot.

> [!IMPORTANT]
> A Azure Container Registry Helm-diagramok támogatása jelenleg előzetes verzióban érhető el. Az előzetes verziók abban a feltételben érhetők el, hogy Ön elfogadja a kiegészítő [használati feltételeket][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="helm-3-or-helm-2"></a>3. Helm vagy Helm 2?

A Helm-diagramok tárolásához, kezeléséhez és telepítéséhez egy Helm-ügyfelet és a Helm CLI-t használhatja. A Helm-ügyfél főbb kiadásai között a Helm 3 és a Helm 2 is szerepel. A Helm 3 új diagram formátumot támogat, és már nem telepíti a kormányrúd kiszolgálóoldali összetevőt. A verziók közötti különbségekről a [Gyakori kérdések](https://helm.sh/docs/faq/)című témakörben talál további információt. Ha korábban már telepítette a Helm 2 diagramot, tekintse meg [a Helm v2 áttelepítése v3-](https://helm.sh/docs/topics/v2_v3_migration/)re című témakört.

A Helm 3 vagy a Helm 2 használatával a Helm-diagramokat Azure Container Registry, az egyes verziókhoz tartozó munkafolyamatokkal futtathatja:

* [Helm 3 – ügyfél](#use-the-helm-3-client) – `helm chart` a Helm CLI parancsainak használatával kezelheti a beállításjegyzékben lévő diagramokat [OCI](container-registry-image-formats.md#oci-artifacts) -összetevőkként
* [2. Helm-ügyfél](#use-the-helm-2-client) – használja az az [ACR Helm][az-acr-helm] parancsait az Azure CLI-ben a tároló-beállításjegyzék Helm diagram-tárházként való hozzáadásához és kezeléséhez

### <a name="additional-information"></a>További információ

* A legtöbb esetben javasolt a Helm 3 munkafolyamat használata natív `helm chart` parancsokkal a diagramok OCI-összetevőkként való kezeléséhez.
* Az örökölt az [ACR Helm][az-acr-helm] Azure CLI-parancsokat és-munkafolyamatokat a Helm 3 ügyféllel és diagramokkal is használhatja. Azonban bizonyos parancsok, például `az acr helm list` nem kompatibilisek a Helm 3 diagramjaival.
* A Helm 3 esetében az [az ACR Helm][az-acr-helm] parancsok elsősorban a Helm 2 ügyfél-és diagram-formátumával való kompatibilitás érdekében támogatottak. A parancsok jövőbeli fejlesztése jelenleg nem tervezett.

## <a name="use-the-helm-3-client"></a>A Helm 3-ügyfél használata

### <a name="prerequisites"></a>Előfeltételek

- Azure **Container Registry** az Azure-előfizetésében. Ha szükséges, hozzon létre egy beállításjegyzéket a [Azure Portal](container-registry-get-started-portal.md) vagy az [Azure CLI](container-registry-get-started-azure-cli.md)használatával.
- A **Helm-ügyfél verziója 3.1.0 vagy újabb** verzióra `helm version` , hogy megtalálja az aktuális verziót. A Helm telepítésével és frissítésével kapcsolatos további információkért lásd: a [Helm telepítése][helm-install].
- **Egy Kubernetes-fürt** , ahol egy Helm-diagramot fog telepíteni. Ha szükséges, hozzon létre egy [Azure Kubernetes Service-fürtöt][aks-quickstart]. 
- A verzió megkereséséhez futtassa `az --version` az **Azure CLI 2.0.71 vagy újabb verzióját** . Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

### <a name="high-level-workflow"></a>Magas szintű munkafolyamat

A **Helm 3** esetében:

* Létrehozhat egy vagy több Helm-tárházat egy Azure Container registryben
* A [OCI](container-registry-image-formats.md#oci-artifacts)-összetevőket tartalmazó Helm 3 diagramokat egy beállításjegyzékben tárolja. Jelenleg a Helm 3 támogatja a OCI- *t.*
* A `helm registry login` paranccsal végezze el a hitelesítést a beállításjegyzék használatával.
* A `helm chart` Helm parancssori felületén található parancsok használatával leküldheti, lekérheti és kezelheti a-beállításjegyzékben található Helm-diagramokat
* A `helm install` használatával diagramokat telepíthet egy Kubernetes-fürtre egy helyi adattár-gyorsítótárból.

Példákat a következő részekben talál.

### <a name="enable-oci-support"></a>OCI-támogatás engedélyezése

Állítsa be az alábbi környezeti változót, hogy engedélyezze a OCI támogatását a Helm 3 ügyfélen. Jelenleg ez a támogatás kísérleti jellegű. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

### <a name="create-a-sample-chart"></a>Minta diagram létrehozása

Hozzon létre egy teszt diagramot az alábbi parancsokkal:

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

Alapszintű példaként módosítsa a könyvtárat a `templates` mappára, és először törölje a tartalmát:

```console
cd hello-world/templates
rm -rf *
```

A `templates` mappában hozzon létre egy nevű `configmap.yaml` fájlt a következő tartalommal:

```console
cat <<EOF > configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: hello-world-configmap
data:
  myvalue: "Hello World"
EOF
```

A példa létrehozásával és futtatásával kapcsolatos további információkért lásd: [első lépések](https://helm.sh/docs/chart_template_guide/getting_started/) a Helm docs-ban.

### <a name="save-chart-to-local-registry-cache"></a>Diagram mentése helyi beállításjegyzék-gyorsítótárba

Módosítsa a könyvtárat az `hello-world` alkönyvtárra. Ezután futtassa a `helm chart save` parancsot a diagram helyi másolatának mentéséhez, és hozzon létre egy aliast a beállításjegyzék teljes nevével, valamint a cél tárházat és a címkét is. 

A következő példában a beállításjegyzék neve *mycontainerregistry*, a cél tárháza a *Hello-World*, a TARGET diagram címkéje pedig *v1*, de a környezet helyettesítő értékei:

```console
cd ..
helm chart save . hello-world:v1
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:v1
```

A `helm chart list` futtatásával erősítse meg, hogy mentette a diagramokat a helyi beállításjegyzék-gyorsítótárban. A kimenet a következőhöz hasonló:

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:v1                                           hello-world       0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:v1       hello-world       0.1.0   5899db0 3.2 KiB        2 minutes
```

### <a name="authenticate-with-the-registry"></a>Hitelesítés a beállításjegyzékkel

Futtassa a `helm registry login` parancsot a Helm 3 CLI-ben a forgatókönyvhöz megfelelő hitelesítő adatok használatával történő [hitelesítéshez](container-registry-authentication.md) .

Hozzon létre például egy Azure Active Directory [egyszerű szolgáltatást a lekéréses és leküldéses engedélyekkel](container-registry-auth-service-principal.md#create-a-service-principal) (AcrPush szerepkör) a beállításjegyzékbe. Ezután adja meg `helm registry login`az egyszerű szolgáltatás hitelesítő adatait a következőhöz:. A következő példa környezeti változó használatával szolgáltatja a jelszót:

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

### <a name="push-chart-to-azure-container-registry"></a>Diagram leküldése Azure Container Registry

Futtassa a `helm chart push` parancsot a Helm 3 CLI-ben, hogy a diagramot a teljes cél tárházba küldje:

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Sikeres leküldést követően a kimenet a következőhöz hasonló:

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:v1
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

### <a name="list-charts-in-the-repository"></a>A tárházban lévő diagramok listázása

Az Azure Container registryben tárolt rendszerképekhez hasonlóan az [az ACR adattár][az-acr-repository] parancsaival is megjelenítheti a diagramokat üzemeltető adattárakat, valamint a diagram címkéit és jegyzékeit. 

Futtassa például az az [ACR adattár show][az-acr-repository-show] parancsot az előző lépésben létrehozott tárház tulajdonságainak megtekintéséhez:

```azurecli
az acr repository show \
  --name mycontainerregistry \
  --repository helm/hello-world
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
  "createdTime": "2020-03-20T18:11:37.6701689Z",
  "imageName": "helm/hello-world",
  "lastUpdateTime": "2020-03-20T18:11:37.7637082Z",
  "manifestCount": 1,
  "registry": "mycontainerregistry.azurecr.io",
  "tagCount": 1
}
```

A tárházban tárolt diagram részleteinek megtekintéséhez futtassa az az [ACR repository show-Manifests][az-acr-repository-show-manifests] parancsot. Például:

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/hello-world --detail
```

A példában szereplő kimenet a következőt jeleníti `configMediaType` meg: `application/vnd.cncf.helm.config.v1+json`

```output
[
  {
    [...]
    "configMediaType": "application/vnd.cncf.helm.config.v1+json",
    "createdTime": "2020-03-20T18:11:37.7167893Z",
    "digest": "sha256:0c03b71c225c3ddff53660258ea16ca7412b53b1f6811bf769d8c85a1f0663ee",
    "imageSize": 3301,
    "lastUpdateTime": "2020-03-20T18:11:37.7167893Z",
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "tags": [
      "v1"
    ]
```

### <a name="pull-chart-to-local-cache"></a>Diagram lekérése a helyi gyorsítótárba

Ha egy Helm-diagramot szeretne Kubernetes telepíteni, a diagramnak a helyi gyorsítótárban kell lennie. Ebben a példában először a futtatásával `helm chart remove` távolítsa el a nevű `mycontainerregistry.azurecr.io/helm/hello-world:v1`meglévő helyi diagramot:

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:v1
```

A `helm chart pull` futtatásával töltse le a diagramot az Azure Container registryből a helyi gyorsítótárba:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:v1
```

### <a name="export-helm-chart"></a>Helm-diagram exportálása

A diagram további működéséhez exportálja azt egy helyi könyvtárba a használatával `helm chart export`. Exportálja például a következő `install` könyvtárba húzott diagramot:

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:v1 \
  --destination ./install
```

A tárházban található exportált diagram adatainak megtekintéséhez futtassa a `helm show chart` parancsot abban a könyvtárban, ahol a diagramot exportálta.

```console
cd install
helm show chart hello-world
```

A Helm részletes információkat ad vissza a diagram legújabb verziójáról, ahogy az a következő példában látható:

```output
apiVersion: v2
appVersion: 1.16.0
description: A Helm chart for Kubernetes
name: hello-world
type: application
version: 0.1.0    
```

### <a name="install-helm-chart"></a>A Helm-diagram telepítése

Futtassa `helm install` a parancsot, és telepítse a helyi gyorsítótárba, majd exportálta a Helm-diagramot. Adja meg a kiadás nevét (például *myhelmtest*), vagy `--generate-name` adja át a paramétert. Például:

```console
helm install myhelmtest ./hello-world
```

A sikeres diagram telepítése utáni kimenet a következőhöz hasonló:

```console
NAME: myhelmtest
LAST DEPLOYED: Fri Mar 20 14:14:42 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

A telepítés ellenőrzéséhez futtassa a `helm get manifest` parancsot. A parancs a `configmap.yaml` sablonfájl YAML adatait adja vissza.

Futtassa `helm uninstall` a parancsot a diagram kiadásának eltávolításához a fürtön:

```console
helm uninstall myhelmtest
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Helm-diagram törlése az adattárból

Ha törölni szeretne egy diagramot a tárházból, használja az az [ACR repository delete][az-acr-repository-delete] parancsot. Futtassa a következő parancsot, és erősítse meg a műveletet, amikor a rendszer kéri:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:v1
```

## <a name="use-the-helm-2-client"></a>A Helm 2 ügyfél használata

### <a name="prerequisites"></a>Előfeltételek

- Azure **Container Registry** az Azure-előfizetésében. Ha szükséges, hozzon létre egy beállításjegyzéket a [Azure Portal](container-registry-get-started-portal.md) vagy az [Azure CLI](container-registry-get-started-azure-cli.md)használatával.
- A **Helm-ügyfél verziója 2.11.0 (nem RC verzió), vagy később** futtatva `helm version` megkeresi az aktuális verziót. Szükség van egy Kubernetes-fürtön belül inicializált Helm-kiszolgálóra is. Ha szükséges, hozzon létre egy [Azure Kubernetes Service-fürtöt][aks-quickstart]. A Helm telepítésével és frissítésével kapcsolatos további információkért lásd: a [Helm telepítése][helm-install-v2].
- A verzió megkereséséhez futtassa `az --version` az **Azure CLI 2.0.46 vagy újabb verzióját** . Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

### <a name="high-level-workflow"></a>Magas szintű munkafolyamat

A **Helm 2** esetében:

* Konfigurálja az Azure Container registryt *egyetlen* Helm diagram adattárként. Azure Container Registry kezeli az index definícióját, amikor diagramokat vesz fel és távolít el a tárházba.
* Az Azure CLI használatával hitelesítheti magát az Azure Container registryben, amely ezután automatikusan frissíti a Helm-ügyfelet a beállításjegyzék URI azonosítójának és hitelesítő adataival. Nem kell manuálisan megadnia a beállításjegyzék adatait, így a hitelesítő adatok nem jelennek meg a parancs előzményeiben.
* Az Azure CLI-ben az az [ACR Helm][az-acr-helm] paranccsal adhatja hozzá az Azure Container registryt a Helm chart-tárházhoz, és leküldheti és kezelheti a diagramokat. Ezek az Azure CLI-parancsok a Helm 2-ügyfél parancsait takarják.
* Adja hozzá a diagram adattárat az Azure Container registryben a helyi Helm-tárház indexéhez, amely támogatja a Diagramos keresést.
* A `helm install` használatával diagramokat telepíthet egy Kubernetes-fürtre egy helyi adattár-gyorsítótárból.

Példákat a következő részekben talál.

### <a name="add-repository-to-helm-client"></a>Tárház hozzáadása a Helm-ügyfélhez

Adja hozzá a Azure Container Registry Helm diagram-tárházát a Helm-ügyfeléhez az az [ACR Helm repo Add][az-acr-helm-repo-add] paranccsal. Ez a parancs beolvassa a Helm-ügyfél által használt Azure Container Registry hitelesítési jogkivonatát. A hitelesítési jogkivonat 3 óráig érvényes. `docker login`A következőhöz hasonlóan futtathatja ezt a parancsot a későbbi CLI-munkamenetekben, hogy a Helm-ügyfelet a Azure Container Registry Helm diagram adattárával hitelesítse:

```azurecli
az acr helm repo add --name mycontainerregistry
```

### <a name="add-a-sample-chart-to-the-repository"></a>Minta diagram hozzáadása a tárházhoz

Először hozzon létre egy helyi könyvtárat a *~/ACR-Helm*, majd töltse le a meglévő *stabil/WordPress-* diagramot:

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm repo update
helm fetch stable/wordpress
```

Írja `ls` be a letöltött diagramot a listára, és jegyezze fel a fájlnévben szereplő WordPress-verziót. A `helm fetch stable/wordpress` parancs nem adott meg egy adott verziót, ezért a rendszer beolvasta a *legújabb* verziót. A következő példa kimenetében a WordPress-diagram a *8.1.0*verziója:

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
NAME                  CHART VERSION    APP VERSION    DESCRIPTION
helmdocs/wordpress    8.1.0           5.3.2          Web publishing platform for building blogs and websites.
```

A diagramokat az Azure CLI-vel is listázhatja az az [ACR Helm List][az-acr-helm-list]paranccsal:

```azurecli
az acr helm list --name mycontainerregistry
```

### <a name="show-information-for-a-helm-chart"></a>Helm-diagram adatainak megjelenítése

A tárház egy adott diagramjának információit a `helm inspect` parancs használatával tekintheti meg.

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

A diagramra vonatkozó információkat az Azure CLI az [ACR Helm show][az-acr-helm-show] paranccsal is megjelenítheti. A rendszer a diagram *legújabb* verzióját is alapértelmezés szerint visszaadja. Hozzáfűzheti `--version` a diagram adott verziójának (például *8.1.0*) listázásához:

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

A telepítés folytatásához kövesse a parancs kimenetében található utasításokat a WorPress URL-címeinek és hitelesítő adatainak megtekintéséhez. A következő `kubectl get pods` parancs futtatásával is megtekintheti a Helm diagramon üzembe helyezett Kubernetes-erőforrásokat:

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

A rendszer a futtatáskor `helm search`továbbra is visszaadja a diagramot. A Helm-ügyfél ismét nem frissíti automatikusan az elérhető diagramok listáját egy adattárban. A Helm Client repo index frissítéséhez használja újra az az [ACR Helm repo Add][az-acr-helm-repo-add] parancsot:

```azurecli
az acr helm repo add --name mycontainerregistry
```

## <a name="next-steps"></a>További lépések

* A Helm-diagramok létrehozásával és telepítésével kapcsolatos további információkért lásd: [Helm-diagramok fejlesztése][develop-helm-charts].
* További információ az alkalmazások az [Azure Kubernetes szolgáltatásban (ak)](../aks/kubernetes-helm.md)történő telepítéséről.
* A Helm-diagramok a tároló-létrehozási folyamat részeként használhatók. További információ: [Azure Container Registry feladatok használata][acr-tasks].

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
