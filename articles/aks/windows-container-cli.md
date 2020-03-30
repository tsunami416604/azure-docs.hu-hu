---
title: Előzetes verzió – Windows Server-tároló létrehozása Azure Kubernetes-szolgáltatás (AKS) fürtön
description: Megtudhatja, hogyan hozhat létre gyorsan egy Kubernetes-fürtöt, hogyan helyezhet üzembe egy alkalmazást egy Windows Server-tárolóban az Azure Kubernetes-szolgáltatásban (AKS) az Azure CLI használatával.
services: container-service
ms.topic: article
ms.date: 01/27/2020
ms.openlocfilehash: 259728da5ea7f71110ce183ae25bb47a0f873614
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475510"
---
# <a name="preview---create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Előzetes verzió – Hozzon létre egy Windows Server-tárolót egy Azure Kubernetes-szolgáltatás (AKS) fürtén az Azure CLI használatával

Az Azure Kubernetes-szolgáltatás (AKS) egy felügyelt Kubernetes-szolgáltatás, amely lehetővé teszi a fürtök gyors üzembe helyezését és kezelését. Ebben a cikkben egy AKS-fürt üzembe helyezése az Azure CLI használatával. Egy Windows Server-tárolóban lévő ASP.NET mintaalkalmazást is telepít a fürtre.

Ez a szolgáltatás jelenleg előzetes kiadásban elérhető.

![A böngészés a ASP.NET mintaalkalmazáshoz](media/windows-container/asp-net-sample-app.png)

Ez a cikk feltételezi a Kubernetes fogalmak alapvető megértését. További információ: [Kubernetes alapfogalmak az Azure Kubernetes Service (AKS)][kubernetes-concepts].

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a CLI-t, ez a cikk megköveteli, hogy az Azure CLI 2.0.61-es vagy újabb verzióját futassza. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="before-you-begin"></a>Előkészületek

A Windows Server-tárolók futtatására képes fürt létrehozása után további csomópontkészletet kell hozzáadnia. Egy további csomópontkészlet hozzáadása egy későbbi lépésben jelenik meg, de először engedélyeznie kell néhány előnézeti funkciót.

> [!IMPORTANT]
> Az AKS előzetes funkciók önkiszolgáló opt-in. Az előzetes verziók "adott verzióban" és "ahogy elérhetők", és nem tartoznak a szolgáltatási szintre vonatkozó szerződések és a korlátozott jótállás hatálya alá. Az AKS-előzeteseket részben fedezi az ügyfélszolgálat a legjobb erőfeszítés alapján. Mint ilyen, ezek a funkciók nem célja a termelés használatra. További infromációért lásd a következő támogatási cikkeket:
>
> * [Az AKS támogatási irányelvei][aks-support-policies]
> * [Azure-támogatás – gyakori kérdések][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>Az aks-preview CLI bővítmény telepítése

A Windows Server-tárolók használatához az *aks-preview* CLI bővítmény 0.4.12-es vagy újabb verziójára van szükség. Telepítse az *aks-preview* Azure CLI bővítményt az [az bővítmény hozzáadása][az-extension-add] paranccsal, majd ellenőrizze az elérhető frissítéseket az az extension [update][az-extension-update] paranccsal::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-windows-preview-feature"></a>A Windows előzetes verzió szolgáltatásának regisztrálása

Ha olyan AKS-fürtöt szeretne létrehozni, amely több csomópontkészletet is képes használni, és Windows Server-tárolókat futtat, először engedélyezze a *WindowsPreview* szolgáltatásjelzőket az előfizetésen. A *WindowsPreview* szolgáltatás többcsomópontos készletfürtöket és virtuálisgép-méretezési készletet is használ a Kubernetes-csomópontok üzembe helyezésének és konfigurációjának kezeléséhez. Regisztrálja a *WindowsPreview* szolgáltatásjelzőt az [az szolgáltatásregiszter][az-feature-register] parancsával, ahogy az a következő példában látható:

```azurecli-interactive
az feature register --name WindowsPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> A *WindowsPreview* szolgáltatásjelző sikeres regisztrálása után létrehozott AKS-fürtök ezt az előnézeti fürtélményt használják. A rendszeres, teljes mértékben támogatott fürtök létrehozásához ne engedélyezze az előzetes verziójú funkciókat éles előfizetéseken. Használjon külön teszt- vagy fejlesztési Azure-előfizetést az előzetes verziójú funkciók teszteléséhez.

A regisztráció befejezéséhez néhány percet is igénybe vesz. Ellenőrizze a regisztrációs állapotot az [az szolgáltatáslista][az-feature-list] paranccsal:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WindowsPreview')].{Name:name,State:properties.state}"
```

Ha a regisztrációs `Registered`állapot, nyomja le a Ctrl-C billentyűt az állapot figyelésének leállításához.  Ezután frissítse a *Microsoft.ContainerService* erőforrás-szolgáltató regisztrációját az [az provider register][az-provider-register] paranccsal:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Korlátozások

A következő korlátozások vonatkoznak a több csomópontkészletet támogató AKS-fürtök létrehozásakor és kezelésében:

* Az első csomópontkészlet nem törölhető.

Amíg ez a funkció előzetes verzióban érhető el, a következő további korlátozások érvényesek:

* Az AKS-fürt legfeljebb nyolc csomópontkészlettel rendelkezhet.
* Az AKS-fürt legfeljebb 400 csomóponttal rendelkezhet a nyolc csomópontkészletközött.
* A Windows Server csomópontkészlet neve legfeljebb 6 karakter ből állhat.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai csoport, amelyben az Azure-erőforrások üzembe helyezése és kezelése zajlik. Az erőforráscsoportok létrehozásakor meg kell adnia egy helyet. Ez a hely az erőforráscsoport metaadatainak tárolása, ahol az erőforrások az Azure-ban futnak, ha nem ad meg másik régiót az erőforrás-létrehozás során. Hozzon létre egy erőforráscsoportot az [az csoport létrehozása][az-group-create] paranccsal.

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

> [!NOTE]
> Ez a cikk bash szintaxist használ az oktatóanyag parancsaihoz.
> Ha Az Azure Cloud Shell, győződjön meg arról, hogy a legördülő a bal felső sarokban a Cloud Shell ablak beállítása **Bash.**

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

A következő példa kimenete a sikeresen létrehozott erőforráscsoportot mutatja:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

A Windows Server-tárolók csomópontkészleteit támogató AKS-fürt futtatásához a fürtnek [az Azure CNI][azure-cni-about] (advanced) hálózati beépülő modult használó hálózati házirendet kell használnia. A szükséges alhálózati tartományok és hálózati szempontok megtervezéséhez szükséges részletes információkért tekintse meg az [Azure CNI-hálózat konfigurálása című témakört.][use-advanced-networking] Az [az aks create][az-aks-create] paranccsal hozzon létre egy *myAKSCluster*nevű AKS-fürtöt. Ez a parancs létrehozza a szükséges hálózati erőforrásokat, ha nem léteznek.
  * A fürt két csomón van konfigurálva
  * A *Windows-admin-password* és *a Windows-admin-username* paraméterek a fürtön létrehozott Windows Server-tárolók rendszergazdai hitelesítő adatait határozzák meg.

> [!NOTE]
> Annak érdekében, hogy a fürt megbízhatóan működjön, legalább 2 (két) csomópontot kell futtatnia az alapértelmezett csomópontkészletben.

Adja meg saját biztonságos *PASSWORD_WIN* (ne feledje, hogy a parancsok ebben a cikkben egy BASH shell):

```azurecli-interactive
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --enable-addons monitoring \
    --kubernetes-version 1.15.7 \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --network-plugin azure
```

> [!Note]
> Ha jelszóérvényesítési hibát kap, próbálja meg létrehozni az erőforráscsoportot egy másik régióban.
> Ezután próbálja meg létrehozni a fürtöt az új erőforráscsoporttal.

> [!Note]
> Ha nem tudja létrehozni az AKS-fürtöt, mert a verzió nem támogatott ebben a régióban, akkor használhatja az [az aks get-versions --location eastus] parancsot a régió támogatott verziólistájának megkereséséhez.


Néhány perc múlva a parancs befejezi és visszaadja a FürtJSON-formátumú adatait. Esetenként a fürt néhány percnél tovább is eltarthat. Ezekben az esetekben legfeljebb 10 percet kell hagyni. 

## <a name="add-a-windows-server-node-pool"></a>Windows Server csomópontkészlet hozzáadása

Alapértelmezés szerint egy AKS-fürt jön létre egy csomópontkészlettel, amely linuxos tárolókfuttatására képes. A `az aks nodepool add` paranccsal további csomópontkészletet adhat hozzá, amely képes Windows Server-tárolók futtatására.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.15.7
```

A fenti parancs létrehoz egy *npwin* nevű új csomópontkészletet, és hozzáadja a *myAKSCluster-hez.* A Windows Server-tárolók futtatásához csomópontkészlet létrehozásakor a *csomópont-vm méret* alapértelmezett értéke *Standard_D2s_v3*. Ha úgy dönt, hogy beállítja a *csomópont-vm méretű* paramétert, ellenőrizze a [korlátozott virtuálisgép-méretek][restricted-vm-sizes]listáját. A minimális ajánlott méret *Standard_D2s_v3*. A fenti parancs a futáskor `az aks create`létrehozott alapértelmezett virtuális hálózat alapértelmezett alhálózatát is használja.

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

A Kubernetes-fürt kezeléséhez [kubectl][kubectl], a Kubernetes parancssori ügyfél használatával kell használnia. Ha az Azure Cloud `kubectl` Shell, már telepítve van. A `kubectl` helyi telepítéshez használja az [aks install-cli][az-aks-install-cli] parancsot:

```azurecli
az aks install-cli
```

Az [aks get-credentials][az-aks-get-credentials] paranccsal konfigurálható`kubectl` a Kubernetes-fürthöz való csatlakozásra. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes CLI-t azok használatára.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get][kubectl-get] parancsot a fürtcsomópontok listájának lekéréséhez.

```console
kubectl get nodes
```

A következő példa kimeneti mutatja az összes csomópont a fürtben. Győződjön meg arról, hogy az összes csomópont állapota *készen áll:*

```output
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.15.7
aksnpwin987654                      Ready    agent   108s   v1.15.7
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

A Kubernetes jegyzékfájl határozza meg a kívánt állapotot a fürt, például milyen tárolólemezképek futtatásához. Ebben a cikkben egy jegyzékfájl segítségével hozza létre az összes olyan objektumot, amely a ASP.NET mintaalkalmazás Windows Server-tárolóban való futtatásához szükséges. Ez a jegyzékfájl tartalmazza a [Kubernetes központi telepítését][kubernetes-deployment] a ASP.NET mintaalkalmazás és egy külső [Kubernetes szolgáltatás][kubernetes-service] az alkalmazás eléréséhez az internetről.

A ASP.NET mintaalkalmazás a [.][dotnet-samples] Az AKS megköveteli, hogy a Windows Server-tárolók *a Windows Server 2019* vagy nagyobb rendszer képein alapuljanak. A Kubernetes jegyzékfájlnak meg kell határoznia egy [csomópontválasztót][node-selector] is, hogy az AKS-fürt a ASP.NET mintaalkalmazás podját futtassa egy olyan csomóponton, amely windows Server-tárolókat futtathat.

Hozzon létre `sample.yaml` egy elnevezett fájlt, és másolja a következő YAML-definícióba. Ha az Azure Cloud Shellt használja, `vi` ez `nano` a fájl úgy hozható létre, mintha virtuális vagy fizikai rendszeren dolgozna:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

Telepítse az alkalmazást a [kubectl apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl apply -f sample.yaml
```

A következő példa kimenete a sikeresen létrehozott üzembe helyezést és szolgáltatást mutatja be:

```output
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Az alkalmazás tesztelése

Amikor az alkalmazás fut, a Kubernetes szolgáltatás elérhetővé teszi az alkalmazás előtér-az interneten. A folyamat eltarthat pár percig. Esetenként a szolgáltatás néhány percnél tovább is eltarthat. Ezekben az esetekben legfeljebb 10 percet kell hagyni.

A folyamat állapotának monitorozásához használja [kubectl get service][kubectl-get] parancsot a `--watch` argumentummal.

```console
kubectl get service sample --watch
```

Kezdetben a *mintaszolgáltatás* *EXTERNAL-IP-je* *függőben lévőként*jelenik meg.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Amikor az *EXTERNAL-IP-cím* *függőben lévőről* tényleges `CTRL-C` nyilvános `kubectl` IP-címre változik, az órafolyamat leállításához használja. A következő példa kimenete a szolgáltatáshoz rendelt érvényes nyilvános IP-címet mutatja:

```output
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

A mintaalkalmazás működés közbeni megtekintéséhez nyisson meg egy webböngészőt a szolgáltatás külső IP-címére.

![A böngészés a ASP.NET mintaalkalmazáshoz](media/windows-container/asp-net-sample-app.png)

> [!Note]
> Ha a lap betöltésekén kap egy csatlakozási időtúlot, akkor ellenőrizze, hogy a mintaalkalmazás készen áll-e a következő paranccsal [kubectl get pods --watch]. Előfordulhat, hogy a Windows-tároló nem indul el, mire a külső IP-cím elérhetővé válik.

## <a name="delete-cluster"></a>Fürt törlése

Ha a fürtre már nincs szükség, az [az group delete][az-group-delete] paranccsal törölheti az erőforráscsoportot, a tárolószolgáltatást és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> A fürt törlésekor az AKS-fürt által használt Azure Active Directory-szolgáltatásnév nem lesz eltávolítva. A szolgáltatásnév eltávolításának lépéseiért lásd [az AKS-szolgáltatásnevekre vonatkozó szempontokat és a szolgáltatásnevek törlését][sp-delete] ismertető cikket.

## <a name="next-steps"></a>További lépések

Ebben a cikkben telepített egy Kubernetes-fürtöt, és egy ASP.NET mintaalkalmazást telepített egy Windows Server-tárolóban. [A Kubernetes webes irányítópultjának elérése][kubernetes-dashboard] az imént létrehozott fürthöz.

Az AKS-sel kapcsolatos további információkért és a kódtól az üzembe helyezésig terjedő teljes útmutatóért folytassa a Kubernetes-fürtöket bemutató oktatóanyaggal.

> [!div class="nextstepaction"]
> [AKS-oktatóanyag][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: https://aka.ms/coingfonboarding
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-provider-register]: /cli/azure/provider#az-provider-register
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[use-advanced-networking]: configure-advanced-networking.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
