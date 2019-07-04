---
title: Használja a pod biztonsági házirendeket Azure Kubernetes Service (AKS)
description: Ismerje meg, hogyan szabályozható a pod tartózkodások PodSecurityPolicy Azure Kubernetes Service (AKS) használatával
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/17/2019
ms.author: iainfou
ms.openlocfilehash: 9da722006651cfc9e9f2a175d5c330ba5df08123
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447070"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>Előzetes verzió – a pod biztonsági házirendeket használó Azure Kubernetes Service (AKS)-fürt biztonságossá tétele

Az AKS-fürt, a biztonság növelése érdekében korlátozhatja a podok lehet ütemezni. Az AKS-fürtöt, amely nem engedélyezi a erőforrás-kérelmek podok nem futtatható. Ezt a hozzáférést pod biztonsági házirendeket definiálhat. Ez a cikk bemutatja, hogyan használandó pod biztonsági szabályzatok központi telepítését az aks-ben podok korlátozza.

> [!IMPORTANT]
> Az AKS előzetes verziójú funkciók önkiszolgáló, a rendszer. A biztosított gyűjthet visszajelzéseket és a hibák kapcsolódóan a Közösség részéről. Előzetes verzióban elérhető ezeket a funkciókat nem üzemi használat céljára. Nyilvános előzetes verzióban érhető el "ajánlott beavatkozást" támogatás keretében tartoznak. Az AKS technikai támogatási csapat segítségét munkaidőben csendes-óceáni időzóna (PST) csak alatt érhető el. További információkért tekintse meg a következő cikkek támogatja:
>
> * [Az AKS támogatási házirendek][aks-support-policies]
> * [Az Azure-támogatás – gyakori kérdések][aks-faq]

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk azt feltételezi, hogy egy meglévő AKS-fürtöt. Ha egy AKS-fürtre van szüksége, tekintse meg az AKS gyors [az Azure CLI-vel][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal].

Az Azure CLI 2.0.61 verziójára van szükség, vagy később telepített és konfigurált. Futtatás `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni, tekintse meg kell [Azure CLI telepítése][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Az aks előzetes CLI-bővítmény telepítése

Pod biztonsági szabályzatok használatához szükséges a *aks előzetes* CLI bővítmény verziója 0.4.1 vagy újabb verziója. Telepítse a *aks előzetes* Azure CLI-bővítmény használata a [az bővítmény hozzáadása][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] parancs::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Pod biztonsági házirendet a szolgáltatás-szolgáltató regisztrálása

Hozzon létre, vagy frissíteni egy AKS-fürt pod-biztonsági házirendekkel, először engedélyeznie az előfizetés szolgáltatásjelzőre. Regisztrálja a *PodSecurityPolicyPreview* jelző funkciót, használja a [az a funkció regisztrálása][az-feature-register] parancsot az alábbi példában látható módon:

> [!CAUTION]
> A funkció egy adott előfizetés regisztrálásakor nem jelenleg regisztrációjának ezt a funkciót. Miután engedélyezte az egyes előzetes verziójú funkciók, alapértelmezett érték az összes AKS-fürt, majd az előfizetésben létrehozott használható. Nem engedélyezi az előzetes verziójú funkciók az éles üzemű előfizetéseket. Használjon különálló előfizetést előzetes verziójú funkciók teszteléséhez, és visszajelzést.

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Az állapot megjelenítése néhány percet vesz igénybe *regisztrált*. A regisztrációs állapot használatával ellenőrizheti a [az szolgáltatáslistát][az-feature-list] parancsot:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

Ha elkészült, frissítse a regisztrációját a *Microsoft.ContainerService* erőforrás-szolgáltató használatával a [az provider register][az-provider-register] parancsot:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Podok biztonsági szabályzatok áttekintése

A Kubernetes-fürtökben egy már a betegfelvétel vezérlő segítségével kérések az API-kiszolgálóhoz intercept, amikor az erőforrás létrehozandó. A már a betegfelvétel vezérlő tudja majd *ellenőrzése* , szabályai, az erőforrás kérelmet vagy *mutálódni* az erőforrás üzembe helyezési paraméterek módosítása.

*PodSecurityPolicy* egy már a betegfelvétel vezérlő, amely ellenőrzi a pod megadása a meghatározott igényeknek megfelelő. Ezeket a követelményeket is lehet korlátozni a rendszerjogosultságú tárolók használatát, tárolására, vagy a felhasználó vagy csoport futtathatja, a tároló bizonyos típusú hozzáférést. Ha megpróbál üzembe helyezése egy erőforrást, ahol a pod-specifikációk nem felelnek meg a követelményeket a pod-biztonsági házirendet, a rendszer megtagadja a kérelmet. Ez a képesség podok szabályozására az az aks-ben ütemezett fürt megakadályozza, hogy néhány lehetséges biztonsági rések vagy jogosultsági eszkalálást.

Ha engedélyezi a biztonsági házirend pod egy AKS-fürtöt, néhány alapértelmezett szabályzatok érvényesek. Ezek a szabályzatok alapértelmezett adjon meg egy-az-beépített felhasználói élményt meghatározásához, mit podok ütemezhető. Azonban a fürt felhasználók megadtuk tartalma podok mindaddig, amíg a saját a szabályzatokat. Az ajánlott módszer, hogy:

* AKS-fürt létrehozása
* A saját pod biztonsági szabályzatok meghatározása
* A pod-biztonsági házirend szolgáltatás engedélyezése

A megjelenítendő az alapértelmezett házirendek pod központi telepítések korlátozza, hogy ebben a cikkben azt először a pod biztonsági házirendek szolgáltatást, majd hozzon létre egy egyéni házirendet.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Podok biztonsági házirend engedélyezése az AKS-fürt

Engedélyezheti vagy letilthatja a pod biztonsági házirend használatával a [az aks update][az-aks-update] parancsot. A következő példa lehetővé teszi, hogy pod biztonsági házirendet a fürt neve *myAKSCluster* az erőforráscsoport neve *myResourceGroup*.

> [!NOTE]
> A való életből vett való használatra nem engedélyezi a pod-biztonsági házirend mindaddig, amíg a saját egyéni szabályzatait definiált. Ebben a cikkben megtekintheti, hogyan az alapértelmezett házirendek korlátozzák a pod első lépéseként engedélyeznie pod biztonsági házirend központi telepítések.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Az alapértelmezett AKS házirendek

Ha engedélyezi a pod-biztonsági házirendet, az AKS létrehoz két alapértelmezett házirend nevű *kiemelt* és *korlátozott*. Nem szerkesztheti, vagy távolítsa el ezeket a szabályzatokat. Ehelyett hozzon létre saját szabályzatokat, amelyek meghatározzák a kívánt beállításokat az ellenőrzés. Nézzük első pillantást ezek alapértelmezett házirendek, hogyan pod központi telepítések kerültek.

A rendelkezésre álló szabályzatok megtekintéséhez használja a [kubectl get psp][kubectl-get] parancsot, az alábbi példában látható módon. Az alapértelmezett részeként *korlátozott* házirend, a felhasználó megtagadta *PRIV* kiemelt pod eszkalációs és a felhasználó *MustRunAsNonRoot*.

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
restricted   false          RunAsAny   MustRunAsNonRoot   MustRunAs   MustRunAs   false            configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

A *korlátozott* a hitelesített felhasználók az AKS-fürtöt a pod biztonsági házirend vonatkozik. Ez a hozzárendelés ClusterRoles és ClusterRoleBindings határozza meg. Használja a [kubectl get clusterrolebindings][kubectl-get] parancsot, és keresse meg a *alapértelmezett: korlátozott:* kötést:

```console
kubectl get clusterrolebindings default:restricted -o yaml
```

Az alábbi összevont kimenetben látható módon a *psp: korlátozott* ClusterRole van rendelve, *system: hitelesített* felhasználók. Ez egy alapszintű korlátozások biztosít a saját definiált szabályzatok nélkül.

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  [...]
  name: default:restricted
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:restricted
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:authenticated
```

Fontos tudni, hogyan használják ezek a szabályzatok alapértelmezett podok ütemezése, mielőtt elkezdené a saját pod biztonsági házirendeket hozhat létre a felhasználói kérelmek. A következő néhány szakaszban pedig ütemezése néhány podok ezeket alapértelmezett szabályzatok működésének megtekintéséhez.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Hozzon létre egy tesztfelhasználót az AKS-fürtben

Használata esetén alapértelmezés szerint a [az aks get-credentials][az-aks-get-credentials] parancs, a *rendszergazdai* az AKS-fürt hitelesítő adatait, és hozzáadja a `kubectl` config. A rendszergazdai felhasználó megkerüli a pod biztonsági szabályzatok érvénybe léptetése. Az AKS-fürtök az Azure Active Directory-integrációt használja, ha, sikerült jelentkezzen be egy nem rendszergazdai felhasználó hitelesítő adatait, tekintse meg a művelet a szabályzatok érvénybe léptetése. Ebben a cikkben hozzunk létre egy tesztfelhasználói fiókja a az AKS-fürtöt, amelyet használhat.

Hozzon létre egy minta névteret nevű *psp – aks* a vizsgált erőforrások a [kubectl névtér létrehozása][kubectl-create] parancsot. Ezután hozzon létre egy szolgáltatásfiók nevű *nonadmin elemet – felhasználói* használatával a [kubectl létrehozása serviceaccount][kubectl-create] parancsot:

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Ezután hozzon létre egy RoleBinding a a *nonadmin elemet – felhasználói* hajthat végre alapszintű műveleteket a névtérben található a [kubectl létrehozása rolebinding][kubectl-create] parancsot:

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Rendszergazdai és a nem rendszergazda jogosultságú felhasználó aliasparancsok létrehozása

Jelölje ki a szokásos rendszergazdai felhasználó használatakor közötti különbség `kubectl` és a nem rendszergazdai felhasználó, az előző lépésekben létrehozott két parancssori aliasok létrehozására:

* A **kubectl-rendszergazda** alias a szokásos rendszergazdai felhasználó, és hatókörét a *psp – aks* névtér.
* A **kubectl-nonadminuser** alias szól a *nonadmin elemet – felhasználói* az előző lépésben létrehozott, és hatókörét a *psp – aks* névtér.

E két aliasok létrehozására, ahogyan az az alábbi parancsokat:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Tesztelje a rendszerjogosultságú podot létrehozása

Nézzük először tesztelje, mi történik a biztonsági környezetében rendelkező podot ütemezésekor `privileged: true`. Ez a biztonsági környezet a pod jogosultságokkal kivizsgálásához. Az előző szakaszban az alapértelmezett AKS pod teljesítménydiagram biztonsági házirendek a *korlátozott* házirendet kell elutasítja a kérelmet.

Hozzon létre egy fájlt `nginx-privileged.yaml` , és illessze be a következő YAML-jegyzékfájl:

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

Létrehozhatja a pod a [a kubectl a alkalmazni][kubectl-apply] parancsot, majd adja meg a YAML-jegyzékfájl neve:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

A pod ütemezhető, nem sikerül, az alábbi példa kimenetében látható módon:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: [spec.containers[0].securityContext.privileged: Invalid value: true: Privileged containers are not allowed]
```

A pod nem éri el a ütemezési szakaszban, így nincsenek erőforrások áthelyezése előtt törölni.

## <a name="test-creation-of-an-unprivileged-pod"></a>Egy nem rendszerjogosultságú pod teszt létrehozása

Az előző példában a pod-specifikáció kért jogosultsági szint. Ezt a kérelmet az alapértelmezés szerint nem *korlátozott* pod biztonsági házirend, így a pod nem ütemezhető. Próbáljuk meg most már fut a jogosultság-eszkalációs kérelmet anélkül, hogy ugyanazon NGINX pod.

Hozzon létre egy fájlt `nginx-unprivileged.yaml` , és illessze be a következő YAML-jegyzékfájl:

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

Létrehozhatja a pod a [a kubectl a alkalmazni][kubectl-apply] parancsot, majd adja meg a YAML-jegyzékfájl neve:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

A Kubernetes-ütemező fogadja a pod-kéréseket. Azonban ha megtekinti a pod használatával állapotának `kubectl get pods`, hiba:

```console
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS                       RESTARTS   AGE
nginx-unprivileged   0/1     CreateContainerConfigError   0          26s
```

Használja a [kubectl ismertetik a pod][kubectl-describe] parancsot, és tekintse meg a pod eseményeihez. A következő sűrített példához mutatja, hogy a tároló és a lemezkép szükséges legfelső szintű engedélyeket, annak ellenére, hogy azt kérte őket:

```console
$ kubectl-nonadminuser describe pod nginx-unprivileged

Name:               nginx-unprivileged
Namespace:          psp-aks
Priority:           0
PriorityClassName:  <none>
Node:               aks-agentpool-34777077-0/10.240.0.4
Start Time:         Thu, 28 Mar 2019 22:05:04 +0000
[...]
Events:
  Type     Reason     Age                     From                               Message
  ----     ------     ----                    ----                               -------
  Normal   Scheduled  7m14s                   default-scheduler                  Successfully assigned psp-aks/nginx-unprivileged to aks-agentpool-34777077-0
  Warning  Failed     5m2s (x12 over 7m13s)   kubelet, aks-agentpool-34777077-0  Error: container has runAsNonRoot and image will run as root
  Normal   Pulled     2m10s (x25 over 7m13s)  kubelet, aks-agentpool-34777077-0  Container image "nginx:1.14.2" already present on machine
```

Annak ellenére, hogy minden emelt szintű hozzáférés kérte, az NGINX-tároló rendszerképének hozzon létre port kötést kell *80-as*. Portok kötést *1024* és az alábbi, a *legfelső szintű* felhasználóra szükség. A pod próbál elindulni, ha a *korlátozott* pod biztonsági házirend megtagadja a kérelmet.

Ez a példa bemutatja, hogy az AKS által létrehozott alapértelmezett pod biztonsági szabályzatok vannak érvényben, és korlátozhatja a felhasználók által elvégezhető műveletek. Fontos tudni, hogy ezek a alapértelmezett szabályzatok viselkedését egy alapszintű nginx-et pod elutasítja, előfordulhat, hogy nem várt módon.

Mielőtt továbblép a következő lépéssel, törölje a tesztelési pod használatával a [kubectl törlése pod][kubectl-delete] parancsot:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Egy adott felhasználói környezet rendelkező podot teszt létrehozása

Az előző példában a tárolórendszerkép automatikusan megpróbálta volna használni legfelső szintű NGINX 80-as porthoz kötődni. A kérelem megtagadva az alapértelmezés szerint *korlátozott* pod biztonsági házirend, így a pod nem indul el. Próbáljuk ki, hogy ugyanazon az NGINX-pod való futtatás egy adott felhasználói környezetet, például `runAsUser: 2000`.

Hozzon létre egy fájlt `nginx-unprivileged-nonroot.yaml` , és illessze be a következő YAML-jegyzékfájl:

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

Létrehozhatja a pod a [a kubectl a alkalmazni][kubectl-apply] parancsot, majd adja meg a YAML-jegyzékfájl neve:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

A Kubernetes-ütemező fogadja a pod-kéréseket. Azonban ha megtekinti a pod használatával állapotának `kubectl get pods`, mint az előző példában különböző hiba van:

```console
$ kubectl-nonadminuser get pods

NAME                         READY   STATUS              RESTARTS   AGE
nginx-unprivileged-nonroot   0/1     CrashLoopBackOff    1          3s
```

Használja a [kubectl ismertetik a pod][kubectl-describe] parancsot, és tekintse meg a pod eseményeihez. A következő sűrített példához pod eseményeket jeleníti meg:

```console
$ kubectl-nonadminuser describe pods nginx-unprivileged

Name:               nginx-unprivileged
Namespace:          psp-aks
Priority:           0
PriorityClassName:  <none>
Node:               aks-agentpool-34777077-0/10.240.0.4
Start Time:         Thu, 28 Mar 2019 22:05:04 +0000
[...]
Events:
  Type     Reason     Age                   From                               Message
  ----     ------     ----                  ----                               -------
  Normal   Scheduled  2m14s                 default-scheduler                  Successfully assigned psp-aks/nginx-unprivileged-nonroot to aks-agentpool-34777077-0
  Normal   Pulled     118s (x3 over 2m13s)  kubelet, aks-agentpool-34777077-0  Container image "nginx:1.14.2" already present on machine
  Normal   Created    118s (x3 over 2m13s)  kubelet, aks-agentpool-34777077-0  Created container
  Normal   Started    118s (x3 over 2m12s)  kubelet, aks-agentpool-34777077-0  Started container
  Warning  BackOff    105s (x5 over 2m11s)  kubelet, aks-agentpool-34777077-0  Back-off restarting failed container
```

Az események azt jelzik, hogy a tároló létrehozásának és lépések. Nincs mit azonnal nyilvánvaló, hogy miért a pod egy hibás állapotban van. A pod-naplók használatával nézzük meg a [kubectl naplók][kubectl-logs] parancsot:

```console
kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous
```

Az alábbi Példakimenet log arra utal, amely az NGINX konfigurációjában magán belül, nincs engedélyekkel kapcsolatos hibát, ha megpróbálnak elindítani a szolgáltatást. Ez a hiba oka újra kellene a 80-as porthoz kötődni. A pod-specifikáció szerinti normál felhasználói fiókot, bár ez a felhasználói fiók nem elegendő az operációs rendszer-szinten az NGINX-szolgáltatás elindítása és a korlátozott porthoz.

```console
$ kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous

2019/03/28 22:38:29 [warn] 1#1: the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
nginx: [warn] the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
2019/03/28 22:38:29 [emerg] 1#1: mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
nginx: [emerg] mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
```

Újra fontos tudni, hogy a biztonsági szabályzatok alapértelmezett pod viselkedését. Ez a hiba volt kissé nehezebb nyomon követheti, és újra, nem lehetséges, hogy várhatóan egy alapszintű NGINX-pod való hozzáférést.

Mielőtt továbblép a következő lépéssel, törölje a tesztelési pod használatával a [kubectl törlése pod][kubectl-delete] parancsot:

```console
kubectl-nonadminuser delete -f nginx-unprivileged-nonroot.yaml
```

## <a name="create-a-custom-pod-security-policy"></a>Hozzon létre egy egyéni pod biztonsági házirend

Most, hogy megismerte a pod biztonsági szabályzatok alapértelmezett viselkedését, nézzük lehetőséget biztosíthat az *nonadmin elemet – felhasználói* podok sikerült ütemezni.

Hozzunk létre egy szabályzatot, amely az emelt szintű hozzáférés kérése podok elutasítása. Egyéb beállítások, például *felhasználó* vagy engedélyezett *kötetek*, nem kifejezetten tiltott. Ilyen típusú szabályzat elutasítja a kérést a privilegizált hozzáférés érdekében, de egyébként lehetővé teszi, hogy a fürt futtatása a kért podokat.

Hozzon létre egy fájlt `psp-deny-privileged.yaml` , és illessze be a következő YAML-jegyzékfájl:

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

Létrehozhatja a házirendet a [a kubectl a alkalmazni][kubectl-apply] parancsot, majd adja meg a YAML-jegyzékfájl neve:

```console
kubectl apply -f psp-deny-privileged.yaml
```

A rendelkezésre álló szabályzatok megtekintéséhez használja a [kubectl get psp][kubectl-get] parancsot, az alábbi példában látható módon. Hasonlítsa össze a *psp megtagadása – kiemelt jogosultságú* az alapértelmezett házirend *korlátozott* szabályzatot, amely az előző lépéseknél hozhat létre egy pod kényszerítve lenne. Használatát csak *PRIV* eszkalációs megtagadja a szabályzatot. Nem vonatkoznak korlátozások a felhasználó vagy csoport számára a *psp megtagadása – kiemelt jogosultságú* házirend.

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
restricted            false          RunAsAny   MustRunAsNonRoot   MustRunAs   MustRunAs   false            configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Az egyéni pod biztonsági házirend használni kívánt felhasználói fiók engedélyezése

Az előző lépésben pod biztonsági szabályzatot hozott podok visszautasítja a kérelmet az emelt szintű hozzáférés. Ahhoz, hogy a szabályzat használni, hozzon létre egy *szerepkör* vagy egy *ClusterRole*. Ezt követően társítsa, ezek szerepkörök valamelyikét egy *RoleBinding* vagy *ClusterRoleBinding*.

Ebben a példában a létrehozása, amely lehetővé teszi, hogy egy ClusterRole *használata* a *psp megtagadása – kiemelt jogosultságú* az előző lépésben létrehozott szabályzat. Hozzon létre egy fájlt `psp-deny-privileged-clusterrole.yaml` , és illessze be a következő YAML-jegyzékfájl:

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

Létrehozhatja a ClusterRole a [a kubectl a alkalmazni][kubectl-apply] parancsot, majd adja meg a YAML-jegyzékfájl neve:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Most hozzon létre egy ClusterRoleBinding az az előző lépésben létrehozott ClusterRole használatára. Hozzon létre egy fájlt `psp-deny-privileged-clusterrolebinding.yaml` , és illessze be a következő YAML-jegyzékfájl:

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

Hozzon létre egy ClusterRoleBinding a [a kubectl a alkalmazni][kubectl-apply] parancsot, majd adja meg a YAML-jegyzékfájl neve:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> Ez a cikk az első lépés a pod házirend-szolgáltatás volt engedélyezve a az AKS-fürtöt. Az ajánlott eljárás csak a pod biztonsági házirend funkció engedélyezéséhez, miután meghatározta a saját házirendek volt. Ekkor vált ahol lehetővé tenné a pod-biztonsági házirend szolgáltatás. Egy vagy több egyéni házirendek meghatározva, és felhasználói fiókok lettek társítva ezen házirendek. Most, nyugodtan a pod-biztonsági házirend funkciót, és az alapértelmezett házirendek által okozott problémák minimalizálása érdekében.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Egy nem rendszerjogosultságú pod újra létrehozásának tesztelése

Az a alkalmazni egyéni pod-biztonsági házirend és a egy kötést a felhasználói fiók a házirend használatára próbáljuk meg egy nem rendszerjogosultságú pod újra létrehozni. Ugyanaz, mint `nginx-privileged.yaml` jegyzékfájl létrehozásához a pod használatával a [a kubectl a alkalmazni][kubectl-apply] parancsot:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

A pod sikeresen van ütemezve. A pod használatával állapotának ellenőrzésekor a [kubectl get pods][kubectl-get] parancs van a pod *futó*:

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

Ez a példa bemutatja, hogyan hozhat létre egyéni pod biztonsági szabályzatok az AKS-fürtöt a különböző felhasználók vagy csoportok való hozzáférés meghatározásához. Az alapértelmezett AKS házirendek biztosítják, hogy milyen podok szoros vezérlőit futtatható, így hozhat létre a saját egyéni szabályzatait megfelelően meg kell a korlátozások.

Az NGINX-jogosultságokkal nem rendelkező podot használatával törölje a [kubectl törlése][kubectl-delete] parancsot, majd adja meg a YAML-jegyzékfájl neve:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Podok biztonsági szabályzat letiltásához használja a [az aks update][az-aks-update] újra a parancsot. A következő példa letiltja a pod biztonsági házirendet a fürt neve *myAKSCluster* az erőforráscsoport neve *myResourceGroup*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

Ezután törölje a ClusterRole és ClusterRoleBinding:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

Törli a hálózati házirend használatával [kubectl törlése][kubectl-delete] parancsot, majd adja meg a YAML-jegyzékfájl neve:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Végül törli a *psp – aks* névtér:

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>További lépések

Ez a cikk bemutatta, letilthatja az emelt szintű hozzáférés pod biztonsági szabályzat létrehozása. Sokféle szolgáltatások házirend kényszerítheti, például a kötet vagy a futtató felhasználó típusa. A rendelkezésre álló beállításokkal kapcsolatos további információkért lásd: a [Kubernetes-podok biztonsági szabályzat-referenciadokumentumok][kubernetes-policy-reference].

Ezzel a pod hálózati forgalom kapcsolatos további információkért lásd: [között pods házirendekkel az aks-ben hálózati forgalmának biztonságossá tétele][network-policies].

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
