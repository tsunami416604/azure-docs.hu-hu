---
title: Előzetes verzió – Windows Server-tároló létrehozása Azure Kubernetes Service (ak) fürtön
description: Ismerje meg, hogyan hozhat létre gyorsan Kubernetes-fürtöt, hogyan helyezhet üzembe egy alkalmazást egy Windows Server-tárolóban az Azure Kubernetes szolgáltatásban (ak) az Azure CLI használatával.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/17/2019
ms.author: mlearned
ms.openlocfilehash: ee798ba624aaf9f21886edab36185fb1b6ae67f2
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70387337"
---
# <a name="preview---create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Előzetes verzió – Windows Server-tároló létrehozása Azure Kubernetes szolgáltatásbeli (ak) fürtön az Azure CLI használatával

Az Azure Kubernetes Service (ak) egy felügyelt Kubernetes szolgáltatás, amely lehetővé teszi fürtök gyors üzembe helyezését és kezelését. Ebben a cikkben egy AK-fürtöt helyez üzembe az Azure CLI használatával. Egy ASP.NET-minta alkalmazást is üzembe helyezhet egy Windows Server-tárolóban a fürtön.

Ez a szolgáltatás jelenleg előzetes kiadásban elérhető.

![A ASP.NET-minta alkalmazás tallózásának képe](media/windows-container/asp-net-sample-app.png)

Ez a cikk azt feltételezi, hogy alapvető ismereteket Kubernetes a fogalmakról. További információ: [Az Azure Kubernetes Service (ak) Kubernetes alapfogalmai][kubernetes-concepts].

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a cikkhez az Azure CLI 2.0.61 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="before-you-begin"></a>Előkészületek

A Windows Server-tárolókat futtató fürt létrehozása után hozzá kell adnia egy további csomópont-készletet. Egy későbbi lépésben egy további Node-készlet hozzáadására van szükség, de először engedélyeznie kell néhány előzetes verziót.

> [!IMPORTANT]
> Az AK előzetes verziójának funkciói önkiszolgáló opt-in. Az előzetes verziók az "adott állapotban" és "ahogy elérhető" módon vannak kizárva, és ki vannak zárva a szolgáltatói szerződésekből és a korlátozott jótállásból. A következő részben az ügyfélszolgálat a lehető leghatékonyabban foglalkozik. Ezért ezeket a funkciókat nem éles használatra szánták. További részletekért tekintse meg a következő támogatási cikkeket:
>
> * [AK-támogatási szabályzatok][aks-support-policies]
> * [Azure-támogatás – gyakori kérdések][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>Az Kabai szolgáltatás telepítése – előnézeti CLI-bővítmény

A Windows Server-tárolók használatához a CLI *-előnézeti CLI-* bővítmény 0.4.1 vagy újabb verziójára van szükség. Telepítse az *AK – előzetes* verzió Azure CLI bővítményét az az [Extension Add][az-extension-add] paranccsal, majd az az [Extension Update][az-extension-update] paranccsal keresse meg az elérhető frissítéseket:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-windows-preview-feature"></a>A Windows előzetes verzió funkciójának regisztrálása

Ha olyan AK-fürtöt szeretne létrehozni, amely több Node-készletet is használhat, és Windows Server-tárolókat futtat, először engedélyezze a *WindowsPreview* funkció jelzőit az előfizetésében. A *WindowsPreview* funkció több csomópontos készlet-fürtöket és virtuálisgép-méretezési készletet is használ a Kubernetes-csomópontok központi telepítésének és konfigurálásának kezeléséhez. Regisztrálja a *WindowsPreview* szolgáltatás jelölőjét az az [Feature Register][az-feature-register] paranccsal az alábbi példában látható módon:

```azurecli-interactive
az feature register --name WindowsPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> A *WindowsPreview* funkció jelzőjének sikeres regisztrálását követően létrehozott AK-fürtök ezt az előzetes verziójú fürtöt használják. A rendszeres, teljes mértékben támogatott fürtök létrehozásához ne engedélyezze az előzetes verziójú funkciókat az éles előfizetésekben. Az előzetes verzió funkcióinak teszteléséhez használjon külön tesztelési vagy fejlesztési Azure-előfizetést.

A regisztráció elvégzése néhány percet vesz igénybe. A regisztrációs állapotot az az [Feature List][az-feature-list] paranccsal ellenőrizhetjük:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WindowsPreview')].{Name:name,State:properties.state}"
```

Ha a regisztrációs állapot van `Registered`, nyomja le a CTRL-C billentyűkombinációt az állapot figyelésének leállításához.  Ezután frissítse a *Microsoft. tárolószolgáltatás* erőforrás-szolgáltató regisztrációját az az [Provider Register][az-provider-register] paranccsal:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Korlátozások

A több csomópontot támogató AK-fürtök létrehozásakor és kezelésekor a következő korlátozások érvényesek:

* Több Node-készlet is elérhető a *WindowsPreview*sikeres regisztrálását követően létrehozott fürtökhöz. Ha regisztrálja az előfizetéshez tartozó *MultiAgentpoolPreview* funkciót, több Node-készlet is elérhető. A szolgáltatás sikeres regisztrálása előtt nem adhat hozzá és nem kezelhet meglévő AK-fürtöt tartalmazó csomópont-készleteket.
* Az első csomópont-készlet nem törölhető.

Habár ez a funkció előzetes verzióban érhető el, a következő további korlátozások érvényesek:

* Az AK-fürt legfeljebb nyolc csomópont-készletet tartalmazhat.
* Az AK-fürt legfeljebb 400 csomóponttal rendelkezhet a nyolc csomópontos készletben.
* A Windows Server-csomópontok készletének neve legfeljebb 6 karakterből állhat.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Azure-erőforráscsoport olyan logikai csoport, amelyben az Azure-erőforrások üzembe helyezése és kezelése zajlik. Az erőforráscsoportok létrehozásakor meg kell adnia egy helyet. Ez a hely határozza meg, hogy az erőforráscsoport metaadatai hol vannak tárolva, és az erőforrások hol futnak az Azure-ban, ha nem ad meg másik régiót az erőforrások létrehozásakor. Hozzon létre egy erőforráscsoportot az az [Group Create][az-group-create] paranccsal.

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

> [!NOTE]
> Ez a cikk bash-szintaxist használ az oktatóanyag parancsaihoz.
> Ha Azure Cloud Shell használ, győződjön meg arról, hogy a Cloud Shell ablak bal felső részén található legördülő lista **bash**értékre van állítva.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

A következő példa kimenete azt mutatja, hogy az erőforráscsoport sikeresen létrejött:

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

Ha olyan AK-fürtöt szeretne futtatni, amely támogatja a Windows Server-tárolók csomópont-készleteit, a fürtnek olyan hálózati házirendet kell használnia, amely az [Azure CNI][azure-cni-about] (Advanced) hálózati beépülő modult használja. További információ a szükséges alhálózati tartományok és hálózati megfontolások megtervezéséhez: az [Azure CNI hálózatkezelésének konfigurálása][use-advanced-networking]. Az az [AK Create][az-aks-create] paranccsal hozzon létre egy *myAKSCluster*nevű AK-fürtöt. Ez a parancs létrehozza a szükséges hálózati erőforrásokat, ha nem léteznek.
  * A fürt két csomóponttal van konfigurálva
  * A *Windows-admin-password* és a *Windows-admin-username* paraméterek a fürtön létrehozott Windows Server-tárolók rendszergazdai hitelesítő adatait adja meg.

> [!NOTE]
> Annak biztosítása érdekében, hogy a fürt megbízhatóan működjön, legalább 2 (két) csomópontot kell futtatnia az alapértelmezett csomópont-készletben.

Adja meg saját biztonságos *PASSWORD_WIN* (ne feledje, hogy a cikkben szereplő parancsok egy bash-rendszerhéjba kerülnek):

```azurecli-interactive
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --enable-addons monitoring \
    --kubernetes-version 1.14.6 \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --enable-vmss \
    --network-plugin azure
```

> [!Note]
> Ha jelszó-érvényesítési hibát kap, próbálja meg létrehozni az erőforráscsoportot egy másik régióban.
> Ezután próbálja meg létrehozni a fürtöt az új erőforrás-csoporttal.

Néhány perc elteltével a parancs befejeződik, és a fürthöz tartozó JSON-formátumú adatokat adja vissza.

## <a name="add-a-windows-server-node-pool"></a>Windows Server Node-készlet hozzáadása

Alapértelmezés szerint a rendszer egy AK-fürtöt hoz létre egy olyan csomópont-készlettel, amely képes Linux-tárolók futtatására. A `az aks nodepool add` parancs használatával hozzáadhat egy további, Windows Server-tárolókat futtató csomópont-készletet.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.14.6
```

A fenti parancs létrehoz egy *npwin* nevű új csomópont-készletet, és hozzáadja azt a *myAKSCluster*. Ha Windows Server-tárolókat futtató csomópont-készletet hoz létre, a *Standard_D2s_v3*alapértelmezett értéke a *csomópont-VM-size* . Ha úgy dönt, hogy beállítja a *Node-VM-size* paramétert, ellenőrizze a korlátozott virtuálisgép- [méretek][restricted-vm-sizes]listáját. A minimális ajánlott méret a *Standard_D2s_v3*. A fenti parancs a futtatáskor `az aks create`létrehozott alapértelmezett vnet található alapértelmezett alhálózatot is használja.

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

A Kubernetes-fürtök kezeléséhez a [kubectl][kubectl], a Kubernetes parancssori ügyfélprogramot kell használnia. Ha Azure Cloud shellt használ, `kubectl` már telepítve van. A helyi `kubectl` telepítéshez használja az az [AK install-CLI][az-aks-install-cli] parancsot:

```azurecli
az aks install-cli
```

A Kubernetes `kubectl` -fürthöz való kapcsolódás konfigurálásához használja az az az [AK Get-hitelesítőadats][az-aks-get-credentials] parancsot. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes CLI-t a használatára.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get][kubectl-get] parancsot a fürtcsomópontok listájának lekéréséhez.

```azurecli-interactive
kubectl get nodes
```

A következő példa kimenete a fürt összes csomópontját mutatja. Győződjön meg arról, hogy az összes csomópont állapota *kész*:

```
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.6
aksnpwin987654                      Ready    agent   108s   v1.14.6
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

A Kubernetes jegyzékfájl a fürt kívánt állapotát határozza meg, például a tároló lemezképeit. Ebben a cikkben egy jegyzékfájlt használunk a ASP.NET-minta alkalmazás Windows Server-tárolóban való futtatásához szükséges összes objektum létrehozásához. Ez a jegyzékfájl egy [Kubernetes-telepítést][kubernetes-deployment] tartalmaz a ASP.net-minta alkalmazáshoz és egy külső [Kubernetes szolgáltatáshoz][kubernetes-service] az alkalmazás internetről való eléréséhez.

A ASP.NET minta alkalmazás a [.NET-keretrendszer mintáinak][dotnet-samples] részeként van megadva, és Windows Server-tárolóban fut. Az AK megköveteli, hogy a Windows Server-tárolók a *Windows server 2019* -es vagy újabb rendszerképein alapulnak. A Kubernetes jegyzékfájljának meg kell határoznia egy [csomópont-választót][node-selector] is, hogy a ASP.net-minta alkalmazás Pod-t futtasson a Windows Server-tárolókat futtató csomóponton.

Hozzon létre egy `sample.yaml` nevű fájlt, és másolja a következő YAML-definícióba. Ha a Azure Cloud Shell használja, akkor ez a fájl a vagy `vi` `nano` a használatával hozható létre, ha virtuális vagy fizikai rendszeren dolgozik:

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

Telepítse az alkalmazást a [kubectl Apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzékfájl nevét:

```azurecli-interactive
kubectl apply -f sample.yaml
```

A következő példa kimenete a központi telepítés és a szolgáltatás sikeres létrehozását mutatja be:

```
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Az alkalmazás tesztelése

Az alkalmazás futtatásakor a Kubernetes szolgáltatás az előtér-alkalmazást az internethez teszi elérhetővé. A folyamat eltarthat pár percig.

A folyamat állapotának monitorozásához használja [kubectl get service][kubectl-get] parancsot a `--watch` argumentummal.

```azurecli-interactive
kubectl get service sample --watch
```

Kezdetben a *minta* szolgáltatás *külső IP-címe* *függőben*jelenik meg.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Ha a *külső IP-* cím *függőben* állapotról tényleges nyilvános IP-címről változik, `CTRL-C` akkor a `kubectl` figyelési folyamat leállításához használja a következőt:. A következő példa kimenete a szolgáltatáshoz hozzárendelt érvényes nyilvános IP-címet jeleníti meg:

```
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

A minta alkalmazás működés közbeni megtekintéséhez nyisson meg egy webböngészőt a szolgáltatás külső IP-címére.

![A ASP.NET-minta alkalmazás tallózásának képe](media/windows-container/asp-net-sample-app.png)

## <a name="delete-cluster"></a>Fürt törlése

Ha a fürtre már nincs szükség, az az [Group delete][az-group-delete] paranccsal távolítsa el az erőforráscsoportot, a Container Service-t és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> A fürt törlésekor az AKS-fürt által használt Azure Active Directory-szolgáltatásnév nem lesz eltávolítva. Az egyszerű szolgáltatás eltávolításának lépéseiért lásd: az [AK szolgáltatással kapcsolatos főbb megfontolások és törlés][sp-delete].

## <a name="next-steps"></a>További lépések

Ebben a cikkben üzembe helyezett egy Kubernetes-fürtöt, és telepített egy ASP.NET-minta alkalmazást egy Windows Server-tárolóban. Nyissa meg az imént létrehozott fürthöz tartozó [Kubernetes webes irányítópultot][kubernetes-dashboard] .

Az AKS-sel kapcsolatos további információkért és a kódtól az üzembe helyezésig terjedő teljes útmutatóért folytassa a Kubernetes-fürtöket bemutató oktatóanyaggal.

> [!div class="nextstepaction"]
> [AK-oktatóanyag][aks-tutorial]

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
