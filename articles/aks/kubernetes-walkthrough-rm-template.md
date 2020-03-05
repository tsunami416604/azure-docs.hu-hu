---
title: Rövid útmutató – Azure Kubernetes-szolgáltatás (ak) fürt létrehozása
description: Megtudhatja, hogyan hozhat létre gyorsan Kubernetes-fürtöt egy Azure Resource Manager sablonnal, és hogyan helyezhet üzembe alkalmazást az Azure Kubernetes szolgáltatásban (ak)
services: container-service
ms.topic: quickstart
ms.date: 04/19/2019
ms.custom: mvc
ms.openlocfilehash: 9c4a79f196cc0737ddc9490f2fedda99961289f4
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273780"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-an-azure-resource-manager-template"></a>Gyors útmutató: Azure Kubernetes-szolgáltatás (ak) fürt üzembe helyezése Azure Resource Manager sablonnal

Az Azure Kubernetes Service (ak) egy felügyelt Kubernetes szolgáltatás, amely lehetővé teszi fürtök gyors üzembe helyezését és kezelését. Ebben a rövid útmutatóban egy AK-fürtöt telepít Azure Resource Manager sablon használatával. A fürtben fut egy többtárolós alkalmazás, amely tartalmazza a webes kezelőfelületet és a Redis-példányt.

![Az Azure Vote keresését ábrázoló kép](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

A rövid útmutató feltételezi, hogy rendelkezik a Kubernetes használatára vonatkozó alapvető ismeretekkel. További információ: [Az Azure Kubernetes Service (ak) Kubernetes alapfogalmai][kubernetes-concepts].

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.61 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="prerequisites"></a>Előfeltételek

Az AK-fürtök Resource Manager-sablonnal történő létrehozásához meg kell adnia egy nyilvános SSH-kulcsot és egy Azure Active Directory egyszerű szolgáltatást. Ha ezekre az erőforrásokra szüksége van, tekintse meg a következő szakaszt: Ellenkező esetben ugorjon az [AK-fürt létrehozása](#create-an-aks-cluster) szakaszra.

### <a name="create-an-ssh-key-pair"></a>SSH-kulcs létrehozása

Az AK-csomópontok eléréséhez SSH kulcspár használatával csatlakozhat. Használja az `ssh-keygen` parancsot az SSH nyilvános és titkos kulcs fájljainak létrehozásához. Alapértelmezés szerint ezek a fájlok a *~/.ssh* könyvtárban jönnek létre. Ha a megadott helyen létezik egy azonos nevű SSH-kulcspár, a rendszer felülírja ezeket a fájlokat.

Nyissa meg a [https://shell.azure.com](https://shell.azure.com) a Cloud Shell megnyitásához a böngészőben.

Az alábbi parancs egy SSH-kulcspárt hoz létre RSA-titkosítással és 2048-es hosszúságú használatával:

```console
ssh-keygen -t rsa -b 2048
```

Az SSH-kulcsok létrehozásával kapcsolatos további információkért lásd: [ssh-kulcsok létrehozása és kezelése az Azure-ban történő hitelesítéshez][ssh-keys].

### <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Ahhoz, hogy egy AKS-fürt kommunikálhasson más Azure-erőforrásokkal, Azure Active Directory-szolgáltatásnevet kell használnia. Hozzon létre egy szolgáltatásnevet az [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] paranccsal. A `--skip-assignment` paraméter korlátozza a további engedélyek hozzárendelését. Alapértelmezés szerint ez az egyszerű szolgáltatás egy évig érvényes.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

A kimenet a következő példához hasonló:

```json
{
  "appId": "8b1ede42-d407-46c2-a1bc-6b213b04295f",
  "displayName": "azure-cli-2019-04-19-21-42-11",
  "name": "http://azure-cli-2019-04-19-21-42-11",
  "password": "27e5ac58-81b0-46c1-bd87-85b4ef622682",
  "tenant": "73f978cf-87f2-41bf-92ab-2e7ce012db57"
}
```

Jegyezze fel az *appID* és a *password* értékét. A következő lépésekben szükség lesz ezekre az értékekre.

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

Az ebben a rövid útmutatóban használt sablon [egy Azure Kubernetes Service-fürt üzembe helyezésére](https://azure.microsoft.com/resources/templates/101-aks/)szolgál. További AK-mintákért tekintse meg az AK gyors üzembe helyezési [sablonok][aks-quickstart-templates] webhelyét.

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához.

    [![Üzembe helyezés az Azure-ban](./media/kubernetes-walkthrough-rm-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

2. Válassza ki vagy adja meg a következő értékeket.  

    Ebben a rövid útmutatóban hagyja meg az operációsrendszer- *lemez mérete (GB*), az *ügynökök száma*, az ügynök virtuálisgép- *mérete*, az *operációs rendszer típusa*és a *Kubernetes-verzió*alapértelmezett értékeit. Adja meg a saját értékeit a következő sablon paramétereinek:

    * **Előfizetés**: válasszon ki egy Azure-előfizetést.
    * **Erőforráscsoport**: válassza az **új létrehozása**lehetőséget. Adja meg az erőforráscsoport egyedi nevét, például *myResourceGroup*, majd kattintson **az OK gombra**.
    * **Hely**: válasszon egy helyet, például az **USA keleti**régióját.
    * **Fürt neve**: adjon meg egy egyedi nevet az AK-fürthöz, például *myAKSCluster*.
    * **DNS-előtag**: adjon meg egy egyedi DNS-előtagot a fürthöz, például *myakscluster*.
    * **Linux-rendszergazdai Felhasználónév**: adjon meg egy felhasználónevet az SSH-val való kapcsolódáshoz, például: *azureuser*.
    * **Nyilvános SSH RSA-kulcs**: másolja és illessze be az SSH-kulcspár *nyilvános* részét (alapértelmezés szerint a *~/.ssh/id_rsa. pub*fájl tartalma).
    * **Egyszerű szolgáltatásnév ügyfél-azonosítója**: másolja és illessze be az egyszerű szolgáltatásnév *AppID* az `az ad sp create-for-rbac` parancsból.
    * **Egyszerű szolgáltatás ügyfél titka**: másolja és illessze be a szolgáltatásnév *jelszavát* a `az ad sp create-for-rbac` parancsból.
    * Elfogadom **a fenti feltételeket és kikötéseket**: jelölje be ezt a jelölőnégyzetet az egyeztetéshez.

    ![Resource Manager-sablon Azure Kubernetes Service-fürt létrehozásához a portálon](./media/kubernetes-walkthrough-rm-template/create-aks-cluster-using-template-portal.png)

3. Válassza a **Beszerzés** lehetőséget.

Az AK-fürt létrehozása néhány percet vesz igénybe. Várjon, amíg a fürt sikeresen üzembe helyezhető, mielőtt továbblép a következő lépésre.

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

A Kubernetes-fürtök kezeléséhez a [kubectl][kubectl], a Kubernetes parancssori ügyfélprogramot kell használnia. Ha Azure Cloud Shell használ, `kubectl` már telepítve van. `kubectl` helyi telepítéséhez használja az az [AK install-CLI][az-aks-install-cli] parancsot:

```azurecli
az aks install-cli
```

Ha `kubectl` szeretne konfigurálni a Kubernetes-fürthöz való kapcsolódáshoz, használja az az [AK Get-hitelesítőadats][az-aks-get-credentials] parancsot. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes CLI-t a használatára.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get][kubectl-get] parancsot a fürtcsomópontok listájának lekéréséhez.

```console
kubectl get nodes
```

A következő példa kimenetében az előző lépésekben létrehozott csomópontok láthatók. Győződjön meg arról, hogy az összes csomópont állapota *kész*:

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-agentpool-41324942-0   Ready    agent   6m44s   v1.12.6
aks-agentpool-41324942-1   Ready    agent   6m46s   v1.12.6
aks-agentpool-41324942-2   Ready    agent   6m45s   v1.12.6
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

A Kubernetes jegyzékfájl a fürt kívánt állapotát határozza meg, például a tároló lemezképeit. Ebben a rövid útmutatóban egy jegyzékfájlt használunk az Azure Vote-alkalmazás futtatásához szükséges összes objektum létrehozásához. Ez a jegyzékfájl két [Kubernetes-telepítést][kubernetes-deployment] tartalmaz – egyet az Azure-beli szavazás Python-alkalmazásaihoz, a másikat pedig egy Redis-példányhoz. Két [Kubernetes][kubernetes-service] -szolgáltatás is létrejön – egy belső szolgáltatás a Redis-példányhoz, és egy külső szolgáltatás, amely az Azure vote alkalmazást az internetről éri el.

> [!TIP]
> A rövid útmutatóban manuálisan hozza létre és helyezi üzembe az alkalmazásjegyzék-fájlokat az AKS-fürtön. A valós forgatókönyvekben az [Azure dev Spaces][azure-dev-spaces] használatával gyorsan megismételheti a kódot, és hibakeresést végezhet közvetlenül az AK-fürtben. A Dev Spaces több operációsrendszer-platformon és fejlesztői környezetben használható, és támogatja a csapaton belüli együttműködést.

Hozzon létre egy `azure-vote.yaml` nevű fájlt, és másolja a következő YAML-definícióba. Ha a Azure Cloud Shell használja, akkor ez a fájl `vi` vagy `nano` használatával hozható létre, ha virtuális vagy fizikai rendszeren dolgozik:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Telepítse az alkalmazást a [kubectl Apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl apply -f azure-vote.yaml
```

A következő példa kimenete a sikeresen létrehozott központi telepítéseket és szolgáltatásokat mutatja:

```output
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Az alkalmazás tesztelése

Az alkalmazás futtatásakor a Kubernetes szolgáltatás az előtér-alkalmazást az internethez teszi elérhetővé. A folyamat eltarthat pár percig.

A folyamat állapotának monitorozásához használja [kubectl get service][kubectl-get] parancsot a `--watch` argumentummal.

```console
kubectl get service azure-vote-front --watch
```

Kezdetben a *külső IP-cím* az *Azure-vote-elülső* szolgáltatáshoz *függőben*jelenik meg.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Ha a *külső IP-* cím *függőben* ÁLLAPOTRÓL tényleges nyilvános IP-címről változik, a `CTRL-C` használatával állítsa le a `kubectl` figyelési folyamatát. A következő példa kimenete a szolgáltatáshoz hozzárendelt érvényes nyilvános IP-címet jeleníti meg:

```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Az Azure vote alkalmazás működés közbeni megtekintéséhez nyisson meg egy webböngészőt a szolgáltatás külső IP-címére.

![Az Azure Vote keresését ábrázoló kép](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="delete-cluster"></a>A fürt törlése

Ha a fürtre már nincs szükség, az az [Group delete][az-group-delete] paranccsal távolítsa el az erőforráscsoportot, a Container Service-t és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> A fürt törlésekor az AKS-fürt által használt Azure Active Directory-szolgáltatásnév nem lesz eltávolítva. Az egyszerű szolgáltatás eltávolításának lépéseiért lásd: az [AK szolgáltatással kapcsolatos főbb megfontolások és törlés][sp-delete].

## <a name="get-the-code"></a>A kód letöltése

Ebben a rövid útmutatóban előre létrehozott tároló-lemezképeket használtak a Kubernetes központi telepítésének létrehozásához. A kapcsolódó alkalmazáskód, Docker-fájl és Kubernetes-jegyzékfájl a GitHubon érhetőek el.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy Kubernetes-fürtöt és azon egy többtárolós alkalmazást helyezett üzembe. Nyissa meg a létrehozott fürt [Kubernetes webes irányítópultját][kubernetes-dashboard] .

Az AKS-sel kapcsolatos további információkért és a kódtól az üzembe helyezésig terjedő teljes útmutatóért folytassa a Kubernetes-fürtöket bemutató oktatóanyaggal.

> [!div class="nextstepaction"]
> [AK-oktatóanyag][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[aks-quickstart-templates]: https://azure.microsoft.com/resources/templates/?term=Azure+Kubernetes+Service

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: https://aka.ms/coingfonboarding
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[ssh-keys]: ../virtual-machines/linux/create-ssh-keys-detailed.md
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
