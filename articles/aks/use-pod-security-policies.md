---
title: Pod biztonsági szabályzatok használata az Azure Kubernetes-szolgáltatásban (AKS)
description: Ismerje meg, hogyan szabályozhatja a podos beléptetéseket a PodSecurityPolicy használatával az Azure Kubernetes-szolgáltatásban (AKS)
services: container-service
ms.topic: article
ms.date: 04/08/2020
ms.openlocfilehash: 9e3a17e4775150247ef7924dffec68cc86a0bcac
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998359"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>Előzetes verzió – A fürt biztonságossá tétele pod biztonsági szabályzatok használatával az Azure Kubernetes-szolgáltatásban (AKS)

Az AKS-fürt biztonságának növelése érdekében korlátozhatja, hogy milyen podok ütemezhetők. A nem engedélyezett erőforrásokat kérő podok nem futtathatók az AKS-fürtben. Ezt a hozzáférést pod biztonsági szabályzatok használatával határozhatja meg. Ez a cikk bemutatja, hogyan használhatja a pod biztonsági házirendek podok az AKS-ben üzembe helyezésének korlátozásához.

> [!IMPORTANT]
> Az AKS előzetes funkciók önkiszolgáló opt-in. Az előzetes verziók "adott verzióban" és "ahogy elérhetők", és nem tartoznak a szolgáltatási szintre vonatkozó szerződések és a korlátozott jótállás hatálya alá. Az AKS-előzeteseket részben fedezi az ügyfélszolgálat a legjobb erőfeszítés alapján. Mint ilyen, ezek a funkciók nem célja a termelés használatra. További információt az alábbi támogatási cikkekben talál:
>
> * [Az AKS támogatási irányelvei][aks-support-policies]
> * [Azure-támogatás – gyakori kérdések][aks-faq]

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AKS-fürttel. Ha AKS-fürtre van szüksége, tekintse meg az AKS [gyorsútmutatót az Azure CLI használatával][aks-quickstart-cli] vagy az Azure Portal [használatával.][aks-quickstart-portal]

Az Azure CLI 2.0.61-es vagy újabb verziójára van szükség telepítve és konfigurálva. Futtassa `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni kell, olvassa el [az Azure CLI telepítése][install-azure-cli]című témakört.

### <a name="install-aks-preview-cli-extension"></a>Az aks-preview CLI bővítmény telepítése

Pod biztonsági házirendek használatához az *aks-preview* CLI bővítmény 0.4.1-es vagy újabb verziója szükséges. Telepítse az *aks-preview* Azure CLI bővítményt az [az bővítmény hozzáadása][az-extension-add] paranccsal, majd ellenőrizze az elérhető frissítéseket az az extension [update][az-extension-update] paranccsal:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Pod biztonsági házirendszolgáltatás-szolgáltatójának regisztrálása

Hozzon létre vagy frissítsen egy AKS-fürtpod biztonsági szabályzatok használata érdekében, először engedélyezze a szolgáltatásjelzőt az előfizetésen. A *PodSecurityPolicyPreview* szolgáltatásjelző regisztrálásához használja az [az szolgáltatásregiszter][az-feature-register] parancsot az alábbi példában látható módon:

> [!CAUTION]
> Amikor regisztrál egy funkciót egy előfizetésen, jelenleg nem tudja törölni a funkciót. Miután engedélyezte az előzetes verzió néhány szolgáltatását, az alapértelmezett értékek et az összes AKS-fürthöz használni lehet, majd az előfizetésben létre kell hozni. Az éles előfizetések előzetes funkcióinak engedélyezése nem lehetséges. Használjon külön előfizetést az előzetes funkciók teszteléséhez és a visszajelzések összegyűjtéséhez.

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Az állapot megjelenítése néhány percet *vesz igénybe.* A regisztrációs állapotot az [az szolgáltatáslista][az-feature-list] paranccsal ellenőrizheti:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

Ha kész, frissítse a *Microsoft.ContainerService* erőforrás-szolgáltató regisztrációját az [az provider register][az-provider-register] paranccsal:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Pod biztonsági házirendek – áttekintés

A Kubernetes-fürtben egy belépési vezérlő segítségével elhallgathatja az API-kiszolgálóra irányuló kérelmeket, amikor egy erőforrást létre kell hozni. A belépési vezérlő ezután *szabálykészletalapján érvényesítheti* az erőforrás-kérelmet, vagy *mutálhatja* az erőforrást a telepítési paraméterek módosításához.

*PodSecurityPolicy* egy felvételi vezérlő, amely ellenőrzi a pod specifikáció megfelel a megadott követelményeknek. Ezek a követelmények korlátozhatják a kiemelt tárolók használatát, bizonyos típusú tárolókhoz való hozzáférést, vagy azt a felhasználót vagy csoportot, amelyet a tároló futtathat. Ha olyan erőforrást próbál üzembe helyezni, ahol a pod specifikációi nem felelnek meg a pod biztonsági házirendben ismertetett követelményeknek, a rendszer elutasítja a kérést. Ez a képesség, hogy az AKS-fürtben ütemezhető podok szabályozhatók, megakadályozza a biztonsági réseket vagy a jogosultságok eszkalációját.

Ha engedélyezi a pod biztonsági házirend egy AKS-fürtben, néhány alapértelmezett házirendek vonatkoznak. Ezek az alapértelmezett házirendek egy beépített élményt nyújtanak annak meghatározásához, hogy milyen podok ütemezhetők. A fürtfelhasználók azonban problémákba ütközhetnek a podok üzembe helyezésekor, amíg meg nem határozza a saját szabályzatait. Az ajánlott megközelítés a következő:

* AKS-fürt létrehozása
* Saját pod biztonsági házirendek definiálása
* A pod biztonsági házirendjének engedélyezése

Annak bemutatásához, hogy az alapértelmezett szabályzatok korlátozzák pod telepítések, ebben a cikkben először engedélyezzük a pod biztonsági házirendek szolgáltatás, majd hozzon létre egy egyéni szabályzatot.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Pod biztonsági házirend engedélyezése AKS-fürtön

Az [aks update][az-aks-update] paranccsal engedélyezheti vagy letilthatja a pod biztonsági házirendet. A következő példa engedélyezi a pod biztonsági házirendet a *myResourceGroup*nevű erőforráscsoportban található *myAKSCluster* fürtnévn.

> [!NOTE]
> Valós használatra, ne engedélyezze a pod biztonsági szabályzat, amíg nem határozta meg a saját egyéni szabályzatok. Ebben a cikkben a pod biztonsági házirend, mint az első lépés, hogy hogyan az alapértelmezett szabályzatok korlátozza pod telepítések.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Alapértelmezett AKS-házirendek

Ha engedélyezi a pod biztonsági házirend, AKS létrehoz egy alapértelmezett házirend nevű *kiemelt.* Ne szerkesztse vagy távolítsa el az alapértelmezett házirendet. Ehelyett hozzon létre saját házirendeket, amelyek meghatározzák a szabályozni kívánt beállításokat. Először nézzük meg, hogy ezek az alapértelmezett szabályzatok hogyan befolyásolják a pod-telepítéseket.

A rendelkezésre álló házirendek megtekintéséhez használja a [kubectl get psp parancsot,][kubectl-get] ahogy az a következő példában látható

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

A *kiemelt* pod biztonsági házirend az AKS-fürt bármely hitelesített felhasználójára vonatkozik. Ezt a hozzárendelést clusterroles és ClusterRoleBindings szabályozza. Használja a [kubectl get rolebindings parancsot,][kubectl-get] és keresse meg az *default:privileged:* binding a *kube-rendszer* névtérben:

```console
kubectl get rolebindings default:privileged -n kube-system -o yaml
```

Ahogy az a következő tömörített kimenetben is látható, a *psp:restricted* ClusterRole bármely *system:authenticated* users-hez van rendelve. Ez a képesség a saját házirendek definiálása nélkül biztosítja a korlátozások alapvető szintjét.

```
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  [...]
  name: default:privileged
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:privileged
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:masters
```

Fontos megérteni, hogy ezek az alapértelmezett szabályzatok hogyan lépnek kapcsolatba a felhasználói kérésekkel a podok ütemezéséhez, mielőtt elkezdené létrehozni a saját pod biztonsági szabályzatokat. A következő néhány szakaszban ütemezzen néhány podot, hogy ezek az alapértelmezett szabályzatok működés közben jelenjenek meg.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Tesztfelhasználó létrehozása AKS-fürtben

Alapértelmezés szerint az [azakget-hitelesítő adatok][az-aks-get-credentials] parancs használatakor az AKS-fürt *rendszergazdai* `kubectl` hitelesítő adatai hozzáadódnak a konfigurációhoz. A rendszergazdai felhasználó megkerüli a pod biztonsági házirendek kényszerítését. Ha az AKS-fürtök höz azure Active Directory-integrációt használ, bejelentkezhet egy nem rendszergazdai felhasználó hitelesítő adataival, hogy működés közben láthassa a szabályzatok végrehajtását. Ebben a cikkben hozzon létre egy teszt felhasználói fiókot az AKS-fürtben, amely használható.

Hozzon létre egy *psp-aks* nevű mintanévteret a teszterőforrásokhoz a [kubectl create namespace][kubectl-create] paranccsal. Ezután hozzon létre egy *szolgáltatásfiókot nevű nonadmin-user* a [kubectl create serviceaccount][kubectl-create] parancs:

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Ezután hozzon létre egy RoleBinding a *nem admin-felhasználó* alapvető műveleteket a névtérben a [kubectl create rolebinding][kubectl-create] parancs:

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Aliasparancsok létrehozása rendszergazdai és nem rendszergazdai felhasználók számára

Az előző lépésekben létrehozott normál `kubectl` rendszergazdai felhasználó és a nem rendszergazdai felhasználó közötti különbség kiemeléséhez hozzon létre két parancssori aliast:

* A **kubectl-admin** alias a rendszeres rendszergazdai felhasználó, és hatóköre a *psp-aks* névtér.
* A **kubectl-nonadminuser** alias az előző lépésben létrehozott *nemadmin-felhasználóhoz* tartozik, és a *psp-aks* névtérbe van beosztva.

Hozza létre ezt a két aliast a következő parancsok ban látható módon:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Emelt szintű pod létrehozásának tesztelése

Először teszteljük, mi történik, ha egy podot ütemez a biztonsági környezetével. `privileged: true` Ez a biztonsági környezet eszkalálja a pod jogosultságait. Az előző szakaszban, amely az alapértelmezett AKS-pod biztonsági házirendek, a *korlátozott* házirend et kell tagadnia ezt a kérést.

Hozzon létre `nginx-privileged.yaml` egy nevű fájlt, és illessze be a következő YAML-jegyzékfájlt:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: nginx:1.14.2
      securityContext:
        privileged: true
```

Hozza létre a podot a [kubectl apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

A pod nem ütemezhető, ahogy az a következő példa kimeneti:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: []
```

A pod nem éri el az ütemezési szakaszban, így nincsenek erőforrások törlése, mielőtt továbblépne.

## <a name="test-creation-of-an-unprivileged-pod"></a>Nem privilegizált pod létrehozásának tesztelése

Az előző példában a pod specifikációja emelt szintű eszkalációt kért. Ezt a kérést az alapértelmezett *korlátozott* pod biztonsági házirend elutasítja, így a pod nem ütemezhető. Próbáljuk meg most futtatni ugyanazt az NGINX podot a jogosultságeszkalációs kérelem nélkül.

Hozzon létre `nginx-unprivileged.yaml` egy nevű fájlt, és illessze be a következő YAML-jegyzékfájlt:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
```

Hozza létre a podot a [kubectl apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

A pod nem ütemezhető, ahogy az a következő példa kimeneti:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged.yaml": pods "nginx-unprivileged" is forbidden: unable to validate against any pod security policy: []
```

A pod nem éri el az ütemezési szakaszban, így nincsenek erőforrások törlése, mielőtt továbblépne.

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Pod létrehozásának tesztelése adott felhasználói környezettel

Az előző példában a tárolórendszerkép automatikusan root fájlt próbált használni az NGINX 80-as porthoz való kötéséhez. Ezt a kérést az alapértelmezett *korlátozott* pod biztonsági házirend megtagadta, így a pod nem indul el. Próbáljuk meg most futtatni ugyanazt az NGINX podot `runAsUser: 2000`egy adott felhasználói környezettel, például .

Hozzon létre `nginx-unprivileged-nonroot.yaml` egy nevű fájlt, és illessze be a következő YAML-jegyzékfájlt:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged-nonroot
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
      securityContext:
        runAsUser: 2000
```

Hozza létre a podot a [kubectl apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

A pod nem ütemezhető, ahogy az a következő példa kimeneti:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged-nonroot.yaml": pods "nginx-unprivileged-nonroot" is forbidden: unable to validate against any pod security policy: []
```

A pod nem éri el az ütemezési szakaszban, így nincsenek erőforrások törlése, mielőtt továbblépne.

## <a name="create-a-custom-pod-security-policy"></a>Egyéni pod biztonsági házirend létrehozása

Most, hogy látta az alapértelmezett pod biztonsági házirendek viselkedését, tegyük lehetővé, hogy a *nem admin-felhasználó* sikeresen ütemezze a podokat.

Hozzon létre egy szabályzatot a kiemelt hozzáférést kérő podok elutasítására. Más beállítások, például *a runAsUser* vagy az engedélyezett *kötetek*nincsenek kifejezetten korlátozva. Ez a típusú szabályzat megtagadja a kiemelt hozzáférésre vonatkozó kérelmet, de egyébként lehetővé teszi, hogy a fürt futtassa a kért podokat.

Hozzon létre `psp-deny-privileged.yaml` egy nevű fájlt, és illessze be a következő YAML-jegyzékfájlt:

```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: psp-deny-privileged
spec:
  privileged: false
  seLinux:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
  runAsUser:
    rule: RunAsAny
  fsGroup:
    rule: RunAsAny
  volumes:
  - '*'
```

Hozza létre a házirendet a [kubectl apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Az elérhető házirendek megtekintéséhez használja a [kubectl get psp parancsot,][kubectl-get] ahogy az a következő példában látható. Hasonlítsa össze a *psp-megtagadási jogosultsággal rendelkező* házirendet az alapértelmezett *korlátozott* házirenddel, amely et az előző példákban egy pod létrehozásához kényszerített. A házirend csak a *PRIV* eszkaláció használatát tagadja meg. A *psp-deny-privileged* házirend felhasználójára vagy csoportjára vonatkozóan nincsenek korlátozások.

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Az egyéni pod biztonsági házirendjének használata a felhasználói fiók számára

Az előző lépésben létrehozott egy pod biztonsági szabályzatot a kiemelt hozzáférést kérő podok elutasításához. A házirend használatba hozásához hozzon létre egy *szerepkört* vagy egy *ClusterRole -t.* Ezután egy ilyen szerepkört társítani a *RoleBinding* vagy *a ClusterRoleBinding*használatával társít.

Ebben a példában hozzon létre egy ClusterRole, amely lehetővé teszi az előző lépésben létrehozott *psp-deny-privileged* házirend *használatát.* Hozzon létre `psp-deny-privileged-clusterrole.yaml` egy nevű fájlt, és illessze be a következő YAML-jegyzékfájlt:

```yaml
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: psp-deny-privileged-clusterrole
rules:
- apiGroups:
  - extensions
  resources:
  - podsecuritypolicies
  resourceNames:
  - psp-deny-privileged
  verbs:
  - use
```

Hozza létre a ClusterRole-t a [kubectl apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Most hozzon létre egy ClusterRoleBinding az előző lépésben létrehozott ClusterRole használatához. Hozzon létre `psp-deny-privileged-clusterrolebinding.yaml` egy nevű fájlt, és illessze be a következő YAML-jegyzékfájlt:

```yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: psp-deny-privileged-clusterrolebinding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp-deny-privileged-clusterrole
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:serviceaccounts
```

Hozzon létre egy ClusterRoleBinding-t a [kubectl apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> A cikk első lépésében a pod biztonsági házirend szolgáltatás engedélyezve volt az AKS-fürtön. Az ajánlott gyakorlat az volt, hogy csak a pod biztonsági házirend szolgáltatás engedélyezése, miután saját szabályzatok definiálása után. Ez az a szakasz, ahol engedélyeznie kell a pod biztonsági házirend szolgáltatás. Egy vagy több egyéni házirend definiálva van, és felhasználói fiókok társítva vannak ezekkel a házirendekkel. Most már biztonságosan engedélyezheti a pod biztonsági házirend szolgáltatás, és az alapértelmezett házirendek által okozott problémák minimalizálása.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Egy jogosultsággal nem rendelkező pod létrehozásának tesztelése újra

Az egyéni pod biztonsági házirend alkalmazásával, és egy kötést a felhasználói fiók a szabályzat használata, próbáljunk meg újra létrehozni egy jogosultsággal nem rendelkező pod. Ugyanazzal `nginx-privileged.yaml` a jegyzékfájlval hozd létre a podot a [kubectl apply][kubectl-apply] paranccsal:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

A pod sikeresen ütemezve. Ha ellenőrzi az állapotát a pod segítségével [kubectl get pods][kubectl-get] parancsot, a pod *fut:*

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

Ez a példa bemutatja, hogyan hozhat létre egyéni pod biztonsági házirendeket az AKS-fürthöz való hozzáférés meghatározásához a különböző felhasználók vagy csoportok számára. Az alapértelmezett AKS-házirendek szigorú vezérlőket biztosítanak a podok futtatásához, ezért hozzon létre saját egyéni szabályzatokat, hogy megfelelően határozza meg a szükséges korlátozásokat.

Törölje az NGINX nem privilegizált podot a [kubectl delete][kubectl-delete] paranccsal, és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Pod biztonsági házirend letiltásához használja újra az [az aks update][az-aks-update] parancsot. A következő példa letiltja a pod biztonsági házirendet a *myResourceGroup*nevű erőforráscsoportban lévő *myAKSCluster* fürtnévnél:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

Ezután törölje a ClusterRole és a ClusterRoleBinding:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

Törölje a biztonsági házirendet a [kubectl delete][kubectl-delete] paranccsal, és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Végül törölje a *psp-aks* névteret:

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>További lépések

Ez a cikk bemutatja, hogyan hozhat létre egy pod biztonsági házirendet a kiemelt hozzáférés használatának megakadályozására. A házirendszámos olyan szolgáltatás létezik, amelyet egy házirend kényszeríthet, például a kötet típusa vagy a RunAs felhasználó. A rendelkezésre álló lehetőségekről a [Kubernetes pod biztonsági házirendjének referenciadokumentumai][kubernetes-policy-reference]című témakörben talál további információt.

A pod hálózati forgalmának korlátozásáról a [Podok közötti biztonságos forgalom az AKS hálózati házirendjei használatával című témakörben][network-policies]talál további információt.

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubernetes-policy-reference]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#policy-reference

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policies]: use-network-policies.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
