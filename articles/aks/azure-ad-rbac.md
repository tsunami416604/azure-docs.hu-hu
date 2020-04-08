---
title: Az Azure AD és az RBAC használata fürtökhöz
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogy miként korlátozhatja az Azure Active Directory csoporttagságot a fürterőforrásokhoz való hozzáférés korlátozására az Azure Kubernetes-szolgáltatás (AKS) szerepköralapú hozzáférés-vezérlésével (RBAC)
services: container-service
ms.topic: article
ms.date: 04/16/2019
ms.openlocfilehash: ad195085c049776bf0db418c57f2c72830f1adff
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803569"
---
# <a name="control-access-to-cluster-resources-using-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Fürterőforrásokhoz való hozzáférés szabályozása szerepköralapú hozzáférés-vezérléssel és Azure Active Directory-identitásokkal az Azure Kubernetes szolgáltatásban

Az Azure Kubernetes-szolgáltatás (AKS) konfigurálható az Azure Active Directory (AD) használatára a felhasználói hitelesítéshez. Ebben a konfigurációban egy AKS-fürtbe jelentkezik be egy Azure AD-hitelesítési jogkivonat használatával. A Kubernetes szerepköralapú hozzáférés-vezérlés (RBAC) konfigurálható úgy, hogy korlátozza a hozzáférést a fürterőforrásokhoz, amelyek a felhasználó identitása vagy csoporttagsága alapján.

Ez a cikk bemutatja, hogyan használhatja az Azure AD-csoporttagság ot a névterekhez és a fürterőforrásokhoz való hozzáférés szabályozására a Kubernetes RBAC használatával egy AKS-fürtben. Példacsoportok és felhasználók jönnek létre az Azure AD, majd szerepkörök és szerepkörkötések jönnek létre az AKS-fürtben, hogy megadja a megfelelő engedélyeket az erőforrások létrehozásához és megtekintéséhez.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AKS-fürt engedélyezve van az Azure AD-integráció. Ha AKS-fürtre van szüksége, olvassa el [az Azure Active Directory integrálása az AKS-sel][azure-ad-aks-cli]lehetőséget.

Az Azure CLI 2.0.61-es vagy újabb verziójára van szükség telepítve és konfigurálva. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="create-demo-groups-in-azure-ad"></a>Bemutatócsoportok létrehozása az Azure AD-ben

Ebben a cikkben hozzon létre két felhasználói szerepkört, amelyek segítségével megmutathatja, hogy a Kubernetes RBAC és az Azure AD hogyan szabályozza a fürterőforrásokhoz való hozzáférést. A következő két példaszerepkör használatos:

* **Alkalmazásfejlesztő**
    * Az *appdev-csoport* részét beálló *aksdev* nevű felhasználó.
* **A webhely megbízhatóságát mérnök**
    * Az *opssre* csoport hoz tartozó *akssre* nevű felhasználó.

Éles környezetekben használhatja a meglévő felhasználók és csoportok egy Azure AD-bérlőn belül.

Először az aKS-show paranccsal [az aks show][az-aks-show] lekell szereznie az AKS-fürt erőforrás-azonosítóját. Rendelje hozzá az erőforrás-azonosítót egy *AKS_ID* nevű változóhoz, hogy további parancsokban is hivatkozhassanak rá.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

Hozza létre az első példacsoportot az Azure AD-ben az alkalmazásfejlesztők számára az [az ad group create][az-ad-group-create] paranccsal. A következő példa létrehoz egy *appdev*nevű csoportot:

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

Most hozzon létre egy Azure-szerepkör-hozzárendelést az *appdev-csoport* hoz létre az [az szerepkör-hozzárendelés create][az-role-assignment-create] parancs használatával. Ez a hozzárendelés lehetővé `kubectl` teszi, hogy a csoport bármely tagja az *Azure Kubernetes szolgáltatásfürt felhasználói szerepkörének*megadásával kommunikáljon egy AKS-fürttel.

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> Ha hibaüzenetet kap, `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.`például , várjon néhány másodpercet, amíg az Azure AD-csoport objektumazonosítója propagál a könyvtáron keresztül, majd próbálja meg újra a `az role assignment create` parancsot.

Hozzon létre egy második példa csoportot, ez az sresre nevű *SRE-k*számára:

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

Hozzon létre egy Azure-szerepkör-hozzárendelést, hogy a csoport tagjai az *Azure Kubernetes service cluster felhasználói szerepkör:*

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Bemutató felhasználók létrehozása az Azure AD-ben

Az Azure AD-ben az alkalmazásfejlesztők és az SRE-k számára létrehozott két példacsoporttal mostantól lehetővé teszi, hogy két példafelhasználót hozzon létre. Az RBAC-integráció teszteléséhez a cikk végén, jelentkezzen be az AKS-fürtbe ezekkel a fiókokkal.

Hozza létre az első felhasználói fiókot az Azure AD-ben az [az ad user create][az-ad-user-create] paranccsal.

A következő példa létrehoz egy felhasználót a megjelenítendő neve *AKS Dev* és a felhasználó egyszerű neve (UPN) a. `aksdev@contoso.com` Frissítse az upn-t, hogy az ellenőrzött tartományt tartalmazzon az Azure AD-bérlőhöz (cserélje le *contoso.com* saját tartományára), és adja meg saját biztonságos `--password` hitelesítő adatait:

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name aksdev@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)
```

Most adja hozzá a felhasználót az előző szakaszban létrehozott *alkalmazásfejlesztői* csoporthoz az [az ad group tag hozzáadása][az-ad-group-member-add] paranccsal:

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

Hozzon létre egy második felhasználói fiókot. A következő példa létrehoz egy felhasználót a megjelenítendő neve *AKS SRE* és a felhasználó egyszerű neve (UPN) a. `akssre@contoso.com` Ismét frissítse az upn-t, hogy az ellenőrzött tartományt tartalmazzon az Azure AD-bérlőhöz (cserélje le *contoso.com* a saját tartományára), és adja meg saját biztonságos `--password` hitelesítő adatait:

```azurecli-interactive
# Create a user for the SRE role
AKSSRE_ID=$(az ad user create \
  --display-name "AKS SRE" \
  --user-principal-name akssre@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)

# Add the user to the opssre Azure AD group
az ad group member add --group opssre --member-id $AKSSRE_ID
```

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>Az AKS-fürt erőforrásainak létrehozása alkalmazásfejlesztők számára

Az Azure AD-csoportok és a felhasználók most jönnek létre. Az Azure-szerepkör-hozzárendelések a csoport tagjai számára jöttek létre, hogy rendszeres felhasználóként csatlakozzanak egy AKS-fürthöz. Most konfigurálja az AKS-fürtöt úgy, hogy ezek a különböző csoportok hozzáférjenek bizonyos erőforrásokhoz.

Először is, a fürt felügyeleti hitelesítő adatait az [az aks get-credentials][az-aks-get-credentials] paranccsal. A következő szakaszok egyikében a rendszeres *felhasználói* fürt hitelesítő adatait kapja meg az Azure AD hitelesítési folyamat működés közben.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Hozzon létre egy névteret az AKS-fürtben a [kubectl create namespace][kubectl-create] paranccsal. A következő példa létrehoz egy névtér *névfejlesztési:*

```console
kubectl create namespace dev
```

A Kubernetes, *szerepkörök* határozza meg a megadandó engedélyeket, és *RoleBindings* alkalmazza őket a kívánt felhasználók vagy csoportok. Ezek a hozzárendelések alkalmazhatók egy adott névtérre vagy a teljes fürtre. További információ: [Az RBAC-engedélyezés használata.][rbac-authorization]

Először hozzon létre egy szerepkört a *fejlesztői* névtérhez. Ez a szerepkör teljes körű engedélyeket ad a névtérnek. Éles környezetekben részletesebb engedélyeket adhat meg a különböző felhasználókvagy csoportok számára.

Hozzon létre `role-dev-namespace.yaml` egy nevű fájlt, és illessze be a következő YAML-jegyzékfájlt:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-user-full-access
  namespace: dev
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Hozza létre a szerepkört a [kubectl apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzékfájl fájlnevét:

```console
kubectl apply -f role-dev-namespace.yaml
```

Ezután az *appdev-csoport* erőforrás-azonosítóját az [az ad csoport show][az-ad-group-show] parancsával kapja meg. Ez a csoport a következő lépésben egy RoleBinding tárgyaként van beállítva.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

Most hozzon létre egy RoleBinding az *appdev-csoport* a korábban létrehozott szerepkör névtér-hozzáférés. Hozzon létre `rolebinding-dev-namespace.yaml` egy nevű fájlt, és illessze be a következő YAML-jegyzékfájlt. Az utolsó sorban cserélje le a *groupObjectId objektumazonosítót* az előző parancs csoportobjektum-azonosító kimenetére:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-user-access
  namespace: dev
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: dev-user-full-access
subjects:
- kind: Group
  namespace: dev
  name: groupObjectId
```

Hozza létre a [RoleBinding-t a kubectl apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzékfájl fájlnevét:

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>Az AKS-fürt erőforrásainak létrehozása az SRE-k számára

Most ismételje meg az előző lépéseket egy névtér, szerepkör és rolebinding létrehozásához az SRE-k számára.

Először hozzon létre egy névteret a *sre* számára a [kubectl create névtér][kubectl-create] paranccsal:

```console
kubectl create namespace sre
```

Hozzon létre `role-sre-namespace.yaml` egy nevű fájlt, és illessze be a következő YAML-jegyzékfájlt:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: sre-user-full-access
  namespace: sre
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Hozza létre a szerepkört a [kubectl apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzékfájl fájlnevét:

```console
kubectl apply -f role-sre-namespace.yaml
```

Az [az ad group show][az-ad-group-show] paranccsal az *opssre* csoport erőforrás-azonosítójának beszereznie:

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

Hozzon létre egy RoleBinding az *opssre* csoport számára a korábban létrehozott szerepkör névtér-hozzáféréshez. Hozzon létre `rolebinding-sre-namespace.yaml` egy nevű fájlt, és illessze be a következő YAML-jegyzékfájlt. Az utolsó sorban cserélje le a *groupObjectId objektumazonosítót* az előző parancs csoportobjektum-azonosító kimenetére:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: sre-user-access
  namespace: sre
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: sre-user-full-access
subjects:
- kind: Group
  namespace: sre
  name: groupObjectId
```

Hozza létre a [RoleBinding-t a kubectl apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzékfájl fájlnevét:

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Fürterőforrások használata az Azure AD-identitások használatával

Most teszteljük a várt engedélyeket, amikor erőforrásokat hoz létre és kezel egy AKS-fürtben. Ezekben a példákban ütemezi és tekintse meg a podok a felhasználó hozzárendelt névterében. Ezután megpróbálja ütemezni és megtekinteni a podokat a hozzárendelt névtéren kívül.

Először állítsa alaphelyzetbe a *kubeconfig* környezetet az [az aks get-credentials][az-aks-get-credentials] paranccsal. Egy előző szakaszban a fürt felügyeleti hitelesítő adataival állíthatja be a környezetet. A rendszergazdafelhasználó megkerüli az Azure AD bejelentkezési utasításokat. A `--admin` paraméter nélkül a felhasználói környezet ben, amely megköveteli az összes kérelmet az Azure AD használatával kell hitelesíteni.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Ütemezzen egy alapvető NGINX-podot a [kubectl futtatás][kubectl-run] paranccsal a *fejlesztői* névtérben:

```console
kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev
```

A bejelentkezési kérdés, adja meg a `appdev@contoso.com` hitelesítő adatokat a saját fiókjához létrehozott elején a cikk. Miután sikeresen bejelentkezett, a fiók token `kubectl` gyorsítótárba kerül a jövőbeli parancsokhoz. Az NGINX ütemezése sikeresen megtörtént, ahogy az a következő példa kimenetben látható:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

Most használja a [kubectl get pods][kubectl-get] parancsot a podok megtekintéséhez a *fejlesztői* névtérben.

```console
kubectl get pods --namespace dev
```

Ahogy az alábbi példa kimenetben látható, az NGINX pod sikeresen *fut:*

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>Fürterőforrások létrehozása és megtekintése a hozzárendelt névtéren kívül

Most próbálja meg a *podok* a fejlesztési névtéren kívül. Használja a [kubectl get hüvelyek][kubectl-get] parancsot `--all-namespaces` újra, ezúttal, hogy az alábbiak szerint:

```console
kubectl get pods --all-namespaces
```

A felhasználó csoporttagsága nem rendelkezik kubernetes szerepkörrel, amely lehetővé teszi ezt a műveletet, ahogy az a következő példa kimenetben látható:

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

Ugyanígy próbáljon meg ütemezni egy podot különböző névtérben, például a *sre* névtérben. A felhasználó csoporttagsága nem igazodik a Kubernetes szerepkörhöz és a RoleBinding-hez, hogy megadja ezeket az engedélyeket, ahogy az a következő példakimenetben látható:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>Az SRE-hozzáférés tesztelése az AKS-fürt erőforrásaihoz

Annak ellenőrzéséhez, hogy az Azure AD-csoporttagság és a Kubernetes RBAC megfelelően működik-e a különböző felhasználók és csoportok között, próbálja meg az előző parancsokat, amikor bejelentkezett, mint a *opssre* felhasználó.

Állítsa alaphelyzetbe a *kubeconfig* környezetet az [az aks get-credentials][az-aks-get-credentials] paranccsal, amely törli az *aksdev-felhasználó* korábban gyorsítótárazott hitelesítési jogkivonatát:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Próbálja meg ütemezni és megtekinteni a podokat a hozzárendelt *sre* névtérben. Amikor a rendszer kéri, `opssre@contoso.com` jelentkezzen be a cikk elején létrehozott saját hitelesítő adataival:

```console
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre
kubectl get pods --namespace sre
```

Ahogy az a következő példa kimenet, sikeresen létrehozhatja és megtekintheti a podok:

```console
$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

Most próbálja meg megtekinteni vagy ütemezni a podokat a hozzárendelt SRE névtéren kívül:

```console
kubectl get pods --all-namespaces
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
```

Ezek `kubectl` a parancsok sikertelenek, ahogy az a következő példa kimenetben látható. A felhasználó csoporttagsága és a Kubernetes szerepkör és szerepkörkötések nem adnak engedélyt más névterekben lévő erőforrások létrehozására vagy kezelőire:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben a cikkben erőforrásokat hozott létre az AKS-fürtben, valamint az Azure AD-ben felhasználókat és csoportokat. Az erőforrások karbantartásához futtassa a következő parancsokat:

```azurecli-interactive
# Get the admin kubeconfig context to delete the necessary cluster resources
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin

# Delete the dev and sre namespaces. This also deletes the pods, Roles, and RoleBindings
kubectl delete namespace dev
kubectl delete namespace sre

# Delete the Azure AD user accounts for aksdev and akssre
az ad user delete --upn-or-object-id $AKSDEV_ID
az ad user delete --upn-or-object-id $AKSSRE_ID

# Delete the Azure AD groups for appdev and opssre. This also deletes the Azure role assignments.
az ad group delete --group appdev
az ad group delete --group opssre
```

## <a name="next-steps"></a>További lépések

A Kubernetes-fürtök védelméről az [AKS hozzáférési és identitáskezelési beállításai című témakörben][rbac-authorization]talál további információt.

Az identitás- és erőforrás-vezérléssel kapcsolatos gyakorlati tanácsok az [AKS-ben a hitelesítéssel és engedélyezéssel kapcsolatos gyakorlati tanácsok című témakörben található.][operator-best-practices-identity]

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-run]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-ad-aks-cli]: azure-ad-integration-cli.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-ad-group-create]: /cli/azure/ad/group#az-ad-group-create
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-ad-user-create]: /cli/azure/ad/user#az-ad-user-create
[az-ad-group-member-add]: /cli/azure/ad/group/member#az-ad-group-member-add
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
