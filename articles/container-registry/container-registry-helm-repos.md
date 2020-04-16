---
title: Store Helm diagramok
description: Ismerje meg, hogyan tárolhatja a Kubernetes-alkalmazások Helm-diagramjait az Azure Container Registry adattárak használatával
ms.topic: article
ms.date: 03/20/2020
ms.openlocfilehash: 3f1a68258b758380a66b63e3c3137f1d460d288c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399380"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Helm-diagramok leküldése és lekérése az Azure-tároló beállításjegyzékébe

A Kubernetes alkalmazáseinek gyors kezeléséhez és üzembe helyezéséhez használhatja a [nyílt forráskódú Helm csomagkezelőt.][helm] A Helm, alkalmazáscsomagok vannak definiálva, mint [a diagramok,](https://helm.sh/docs/topics/charts/)amelyek et gyűjt, és tárolja a [Helm diagram tárház.](https://helm.sh/docs/topics/chart_repository/)

Ez a cikk bemutatja, hogyan üzemeltetheti helm-diagramok tárolók egy Azure-tároló beállításjegyzékben, egy Helm 3 vagy Helm 2 telepítés használatával. Sok esetben saját diagramokat hozhat létre és tölthet fel a kifejlesztett alkalmazásokhoz. A saját Helm-diagramok létrehozásáról a [Diagramsablon fejlesztői útmutatójában olvashat bővebben.][develop-helm-charts] Egy meglévő Helm-diagramot is tárolhat egy másik Helm-tárházból.

> [!IMPORTANT]
> Helm-diagramok támogatása az Azure Container Registry jelenleg előzetes verzióban. Az előzetesek azzal a feltétellel érhetők el, hogy elfogadja a kiegészítő [felhasználási feltételeket.][terms-of-use] A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="helm-3-or-helm-2"></a>Helm 3 vagy Helm 2?

Helm-diagramok tárolásához, kezeléséhez és telepítéséhez egy Helm-ügyfél és a Helm CLI használatával kell használnia. A Helm ügyfél főbb kiadásai közé tartozik a Helm 3 és a Helm 2. A Helm 3 támogatja az új diagramformátumot, és már nem telepíti a Tiller szerveroldali összetevőt. A verziókülönbségekről a gyakran feltett kérdések között talál [részleteket.](https://helm.sh/docs/faq/) Ha korábban már telepítette a Helm 2 diagramokat, olvassa [el a Helm v2 migrálása a 3-as hoz című témakört.](https://helm.sh/docs/topics/v2_v3_migration/)

Helm 3 vagy Helm 2 használatával helm diagramokat üzemeltethet az Azure Container Registry szolgáltatásban, az egyes verziókra jellemző munkafolyamatokkal:

* [Helm 3](#use-the-helm-3-client) ügyfél `helm chart` - parancsok használata a Helm CLI kezelésére diagramok a rendszerleíró adatbázisban, mint [OCI összetevők](container-registry-image-formats.md#oci-artifacts)
* [Helm 2 ügyfél](#use-the-helm-2-client) - használja [az acr helm][az-acr-helm] parancsokat az Azure CLI hozzáadni és kezelni a tároló beállításjegyzék helm diagram tárház

### <a name="additional-information"></a>További információ

* A legtöbb esetben azt javasoljuk, hogy a `helm chart` Helm 3 munkafolyamatnatív parancsokkal kezelje a diagramokat OCI-összetevőkként.
* Használhatja [az örökölt az acr helm][az-acr-helm] Azure CLI parancsok és a munkafolyamat a Helm 3 ügyfél és a diagramok. Bizonyos parancsok azonban `az acr helm list` nem kompatibilisek a Helm 3 diagramokkal.
* A Helm 3-tól az [az acr helm][az-acr-helm] parancsok elsősorban a Helm 2 ügyfél- és diagramformátummal való kompatibilitásérdekében támogatottak. Ezeknek a parancsoknak a jövőbeli fejlesztése jelenleg nincs tervezve.

## <a name="use-the-helm-3-client"></a>A Helm 3 ügyfél használata

### <a name="prerequisites"></a>Előfeltételek

- **Egy Azure-tároló beállításjegyzék** az Azure-előfizetésben. Szükség esetén hozzon létre egy beállításjegyzéket az [Azure Portalon](container-registry-get-started-portal.md) vagy az [Azure CLI](container-registry-get-started-azure-cli.md)használatával.
- **Helm ügyfél verzió 3.1.0** `helm version` vagy újabb - Futtassa, hogy megtalálja a jelenlegi verzió. A Helm telepítésével és frissítésével kapcsolatos további információkért [lásd: Helm telepítése.][helm-install]
- **Egy Kubernetes-fürt,** ahol helmdiagramot telepít. Szükség esetén hozzon létre egy [Azure Kubernetes-szolgáltatásfürtöt.][aks-quickstart] 
- **Azure CLI 2.0.71-es** `az --version` vagy újabb verziója – Futtassa a verzió megkereséséhez. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

### <a name="high-level-workflow"></a>Magas szintű munkafolyamat

A **Helm 3-mal:**

* Létrehozhat egy vagy több Helm-adattárat egy Azure-tároló beállításjegyzékében
* Store Helm 3 diagramok egy rendszerleíró adatbázisban [OCI leletek](container-registry-image-formats.md#oci-artifacts). Jelenleg, Helm 3 támogatása OCI *kísérleti*.
* Hitelesítse magát `helm registry login` a beállításjegyzékkel a paranccsal.
* A `helm chart` Helm CLI parancsaival leküldéses, lekéréses és kezelhetőHelm-diagramok a rendszerleíró adatbázisban
* Segítségével `helm install` telepítheti a diagramokat egy Kubernetes-fürtre egy helyi tárház-gyorsítótárból.

A példákat az alábbi szakaszokban talál.

### <a name="enable-oci-support"></a>OCI-támogatás engedélyezése

Állítsa be a következő környezeti változót az OCI-támogatás engedélyezéséhez a Helm 3 ügyfélben. Jelenleg ez a támogatás kísérleti jellegű. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

### <a name="create-a-sample-chart"></a>Mintadiagram létrehozása

Hozzon létre tesztdiagramot a következő parancsokkal:

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

Alapesetben módosítsa a könyvtárat `templates` a mappára, és először törölje az ott lévő tartalmat:

```console
cd hello-world/templates
rm -rf *
```

A `templates` mappában hozzon `configmap.yaml` létre egy következő tartalmú fájlt:

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

A példa létrehozásáról és futtatásáról az [Első lépések](https://helm.sh/docs/chart_template_guide/getting_started/) a Helm-dokumentumokban című témakörben található.

### <a name="save-chart-to-local-registry-cache"></a>Diagram mentése a helyi rendszerleíró adatbázis gyorsítótárába

Könyvtár módosítása `hello-world` az alkönyvtárra. Ezután `helm chart save` futtassa a diagram egy példányának helyi mentéséhez, és hozzon létre egy aliast a rendszerleíró adatbázis (minden kisbetű) és a céltárház és címke teljesen minősített nevével. 

A következő példában a rendszerleíró adatbázis neve *mycontainerregistry*, a cél tárhét *a hello-world*, és a céldiagram címke *v1*, de helyettesítő értékeket a környezetben:

```console
cd ..
helm chart save . hello-world:v1
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Futtassa `helm chart list` a diagramok helyi rendszerleíró gyorsítótárba való mentéséhez. A kimenet a következőhöz hasonló:

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:v1                                           hello-world       0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:v1       hello-world       0.1.0   5899db0 3.2 KiB        2 minutes
```

### <a name="authenticate-with-the-registry"></a>Hitelesítés a beállításjegyzékkel

Futtassa a `helm registry login` parancsot a Helm 3 CLI-ben [a beállításjegyzékkel való hitelesítéshez a](container-registry-authentication.md) forgatókönyvnek megfelelő hitelesítő adatok használatával.

Hozzon létre például egy Azure Active [Directory-szolgáltatást lekéréses és leküldéses engedélyekkel](container-registry-auth-service-principal.md#create-a-service-principal) (AcrPush szerepkör) a beállításjegyzékbe. Ezután adja meg a `helm registry login`szolgáltatásnév hitelesítő adatait a rendszernek. A következő példa egy környezeti változó használatával biztosítja a jelszót:

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

### <a name="push-chart-to-azure-container-registry"></a>Leküldéses diagram az Azure Container Registry

Futtassa a `helm chart push` parancsot a Helm 3 CLI-ben, hogy a diagramot a teljesen minősített céltárba tolja:

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Sikeres leküldéses után a kimenet hasonló a következőkhöz:

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:v1
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

### <a name="list-charts-in-the-repository"></a>Diagramok listázása a tárházban

Az Azure-tároló beállításjegyzékében tárolt lemezképekhez, az [az acr tárház parancsokkal][az-acr-repository] is megjelenítheti a diagramokat tároló, valamint a diagramcímkéket és jegyzékeket. 

Futtassa például az [az acr repository show-t][az-acr-repository-show] az előző lépésben létrehozott tárház tulajdonságainak megtekintéséhez:

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

Futtassa az [az acr repository show-manifests parancsot][az-acr-repository-show-manifests] a tárházban tárolt diagram részleteinek megtekintéséhez. Például:

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/hello-world --detail
```

Kimenet, rövidítve ebben a példában, azt mutatja, a `configMediaType` következő: `application/vnd.cncf.helm.config.v1+json`

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

Helm-diagram kubernetesre való telepítéséhez a diagramnak a helyi gyorsítótárban kell lennie. Ebben a példában `helm chart remove` először futtassa `mycontainerregistry.azurecr.io/helm/hello-world:v1`a meglévő helyi diagram nevét:

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Futtassa `helm chart pull` a diagram letöltéséhez az Azure container registry a helyi gyorsítótárba:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:v1
```

### <a name="export-helm-chart"></a>Helm exportálása diagram

Ha tovább szeretne dolgozni a diagrammal, `helm chart export`exportálja a helyi könyvtárba a használatával. Exportálja például a `install` könyvtárba lekért diagramot:

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:v1 \
  --destination ./install
```

Az exportált diagram adatainak megtekintéséhez futtassa a `helm show chart` parancsot abban a könyvtárban, amelybe a diagramot exportálta.

```console
cd install
helm show chart hello-world
```

A Helm részletes információkat ad vissza a diagram legújabb verziójáról, ahogy az a következő mintakimenetben látható:

```output
apiVersion: v2
appVersion: 1.16.0
description: A Helm chart for Kubernetes
name: hello-world
type: application
version: 0.1.0    
```

### <a name="install-helm-chart"></a>Helm-diagram telepítése

Futtassa `helm install` a helyi gyorsítótárba lehúzott és exportált Helm-diagram telepítéséhez. Adjon meg egy kiadási nevet, például `--generate-name` *myhelmtest,* vagy adja át a paramétert. Például:

```console
helm install myhelmtest ./hello-world
```

A sikeres diagramtelepítés utáni kimenet hasonló a következőkhöz:

```console
NAME: myhelmtest
LAST DEPLOYED: Fri Mar 20 14:14:42 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

A telepítés ellenőrzéséhez `helm get manifest` futtassa a parancsot. A parancs visszaadja a `configmap.yaml` sablonfájlYAML-adatait.

Futtassa `helm uninstall` a diagramkiadás eltávolításához a fürtön:

```console
helm uninstall myhelmtest
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Helm-diagram törlése a tárházból

Ha törölni szeretne egy diagramot a tárházból, használja az [az acr repository delete][az-acr-repository-delete] parancsot. Futtassa a következő parancsot, és erősítse meg a műveletet, amikor a rendszer kéri:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:v1
```

## <a name="use-the-helm-2-client"></a>A Helm 2 ügyfél használata

### <a name="prerequisites"></a>Előfeltételek

- **Egy Azure-tároló beállításjegyzék** az Azure-előfizetésben. Szükség esetén hozzon létre egy beállításjegyzéket az [Azure Portalon](container-registry-get-started-portal.md) vagy az [Azure CLI](container-registry-get-started-azure-cli.md)használatával.
- **Helm ügyfél verzió 2.11.0 (nem RC verzió) vagy újabb** - Fuss, `helm version` hogy megtalálja a jelenlegi verzió. Egy Kubernetes-fürtön belül inicializált Helm-kiszolgálóra (Tiller) is szüksége van. Szükség esetén hozzon létre egy [Azure Kubernetes-szolgáltatásfürtöt.][aks-quickstart] A Helm telepítésével és frissítésével kapcsolatos további információkért [lásd: Helm telepítése.][helm-install-v2]
- **Azure CLI 2.0.46-os** `az --version` vagy újabb verziója – Futtassa a verzió megkereséséhez. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

### <a name="high-level-workflow"></a>Magas szintű munkafolyamat

A **Helm 2** ön:

* Konfigurálja az Azure container registry *egyetlen* Helm diagram tárházként. Az Azure Container Registry kezeli az indexdefiníciót, ahogy hozzáadja és eltávolítja a diagramokat a tárházhoz.
* Hitelesítse magát az Azure Container beállításjegyzék-beállításjegyzékaz Azure CLI, amely ezután frissíti a Helm-ügyfél automatikusan a rendszerleíró URI-val és a hitelesítő adatokkal. Nem kell manuálisan megadnia ezt a beállításjegyzék-információt, így a hitelesítő adatok nem jelennek meg a parancselőzményekben.
* Az Azure CLI [az acr helm][az-acr-helm] parancsaival az Azure container registry helm-diagramtárként való hozzáadásához, valamint a diagramok leküldéses és kezeléséhez. Ezek az Azure CLI parancsok wrap Helm 2 ügyfél parancsokat.
* Adja hozzá a diagramtárház az Azure-tároló beállításjegyzékben a helyi Helm tárház index, támogatja a diagram keresés.
* Segítségével `helm install` telepítheti a diagramokat egy Kubernetes-fürtre egy helyi tárház-gyorsítótárból.

A példákat az alábbi szakaszokban talál.

### <a name="add-repository-to-helm-client"></a>Tárház hozzáadása a Helm-ügyfélhez

Adja hozzá az Azure Container Registry Helm chart tárház a Helm-ügyfél az [az acr helm repo add][az-acr-helm-repo-add] parancs használatával. Ez a parancs egy hitelesítési jogkivonatot kap az Azure container registry, amely a Helm-ügyfél által használt. A hitelesítési jogkivonat 3 óráig érvényes. A `docker login`példához hasonlóan a parancs futtatható a jövőbeli CLI-munkamenetekben a Helm-ügyfél hitelesítéséhez az Azure Container Registry Helm diagramtárral:

```azurecli
az acr helm repo add --name mycontainerregistry
```

### <a name="add-a-sample-chart-to-the-repository"></a>Mintadiagram hozzáadása a tárházhoz

Először is, hozzon létre egy helyi könyvtárat *~/acr-helm*, majd töltse le a meglévő *stabil / wordpress* chart:

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm repo update
helm fetch stable/wordpress
```

Írja `ls` be a listát a letöltött diagram, és vegye figyelembe a Wordpress változata szerepel a fájlnév. A `helm fetch stable/wordpress` parancs nem adott meg egy adott verziót, így a *legújabb* verziót beolvasásra került. A következő példa kimenet, a Wordpress chart változata *8.1.0*:

```output
wordpress-8.1.0.tgz
```

A diagram leküldése a Helm chart tárház az Azure Container Registry az [az acr helm push][az-acr-helm-push] parancs az Azure CLI használatával. Adja meg a nevét A Helm diagram letöltött az előző lépésben, mint például *a wordpress-8.1.0.tgz:*

```azurecli
az acr helm push --name mycontainerregistry wordpress-8.1.0.tgz
```

Néhány pillanat múlva az Azure CLI jelenti, hogy a diagram mentésre kerül, ahogy az a következő példa kimenet:

```output
{
  "saved": true
}
```

### <a name="list-charts-in-the-repository"></a>Diagramok listázása a tárházban

Az előző lépésben feltöltött diagram használatához a helyi Helm tárház indexét frissíteni kell. Újraindexelheti a helm-ügyfél tárolóit, vagy használhatja az Azure CLI-t a tárház index frissítéséhez. Minden alkalommal, amikor hozzáad egy diagramot a tárházhoz, ezt a lépést el kell végezni:

```azurecli
az acr helm repo add --name mycontainerregistry
```

A tárházban tárolt diagram és a helyileg elérhető frissített index használatával a rendszeres Helm ügyfélparancsok at kereshet vagy telepítheti. A tárházban lévő összes diagram megtekintéséhez `helm search` használja a parancsot, adja meg saját Azure Container Registry nevét:

```console
helm search mycontainerregistry
```

Az előző lépésben megnyomott Wordpress diagram a következő példa szerint szerepel:

```output
NAME                  CHART VERSION    APP VERSION    DESCRIPTION
helmdocs/wordpress    8.1.0           5.3.2          Web publishing platform for building blogs and websites.
```

A diagramokat az [ACR helm list (Az acr helm list)][az-acr-helm-list]segítségével is felsorolhatja az Azure CLI segítségével:

```azurecli
az acr helm list --name mycontainerregistry
```

### <a name="show-information-for-a-helm-chart"></a>Helm-diagram adatainak megjelenítése

Ha meg szeretné tekinteni egy adott diagram adatait `helm inspect` a tárházban, használhatja a parancsot.

```console
helm inspect mycontainerregistry/wordpress
```

Ha nincs megadva verziószám, a *legújabb* verzió lesz használva. A Helm részletes információkat ad vissza a diagramról, ahogy az a következő tömörített példa kimenetben látható:

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

Az Azure CLI [az acr helm show][az-acr-helm-show] paranccsal is megjelenítheti a diagram adatait. A diagram *legújabb* verzióját is alapértelmezés szerint visszaadja a rendszer. A diagram `--version` egy adott verziójának (például *8.1.0)* listázásához hozzáfűzheti:

```azurecli
az acr helm show --name mycontainerregistry wordpress
```

### <a name="install-a-helm-chart-from-the-repository"></a>Helm-diagram telepítése az adattárból

A tárházban lévő Helm-diagram a tárház nevének és a diagram nevének megadásával települ. Használja a Helm klienstelepíteni a Wordpress chart:

```console
helm install mycontainerregistry/wordpress
```

> [!TIP]
> Ha leküldéses az Azure Container Registry Helm chart tárház, és később egy új CLI-munkamenetben, a helyi Helm-ügyfél szüksége van egy frissített hitelesítési jogkivonatot. Új hitelesítési jogkivonat beszerzéséhez használja az [az acr helm add][az-acr-helm-repo-add] parancsot.

A telepítési folyamat során a következő lépések hajtják végre:

- A Helm-ügyfél a helyi tárház-indexben keres.
- A megfelelő diagram az Azure Container Registry repository-ból töltődik le.
- A diagram a Kubernetes-fürtben lévő Tiller használatával van telepítve.

A telepítés előrehaladtával kövesse a parancskimenetutasításait a WorPress URL-címek és hitelesítő adatok megtekintéséhez. A `kubectl get pods` parancs futtatásával is megtekintheti a Helm-diagramon keresztül üzembe helyezett Kubernetes-erőforrásokat:

```output
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Helm-diagram törlése a tárházból

Ha törölni szeretne egy diagramot a tárházból, használja az [az acr helm delete][az-acr-helm-delete] parancsot. Adja meg a diagram nevét, például *a wordpress*és a törlendő verziót, például *a 8.1.0.*

```azurecli
az acr helm delete --name mycontainerregistry wordpress --version 8.1.0
```

Ha a megnevezett diagram összes verzióját törölni `--version` szeretné, hagyja ki a paramétert.

A diagram futásközben `helm search`is visszakerül. Ismét a Helm-ügyfél nem frissíti automatikusan a tárházban elérhető diagramok listáját. A Helm-ügyfél tártárindexének frissítéséhez használja újra az [az acr helm add repo add][az-acr-helm-repo-add] parancsot:

```azurecli
az acr helm repo add --name mycontainerregistry
```

## <a name="next-steps"></a>További lépések

* A Helm-diagramok létrehozásáról és telepítéséről a [Helm-diagramok fejlesztése][develop-helm-charts]című témakörben talál további információt.
* További információ az alkalmazások telepítéséről az [Azure Kubernetes szolgáltatásban (AKS)](../aks/kubernetes-helm.md)a Helm használatával.
* A helm diagramok a tároló összeállítási folyamatának részeként használhatók. További információ: [Azure Container Registry Tasks használata.][acr-tasks]

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
