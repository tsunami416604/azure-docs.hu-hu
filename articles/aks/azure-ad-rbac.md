---
title: Vezérlő fürterőforrások az rbac-RÓL és az Azure ad-ben az Azure Kubernetes Service-ben
description: Ismerje meg, hogyan használja az Azure Active Directory-csoporttagság fürterőforrások szerepköralapú hozzáférés-vezérlés (RBAC) az Azure Kubernetes Service (AKS) használatával való hozzáférés korlátozása
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/16/2019
ms.author: iainfou
ms.openlocfilehash: e974c47d1dfb04f66b622c64a7143d00de87c4cb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60467544"
---
# <a name="control-access-to-cluster-resources-using-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Szerepköralapú hozzáférés-vezérlés és az Azure Active Directory-identitásokkal használatával az Azure Kubernetes Service-fürt erőforrásokhoz való hozzáférés vezérlése

Az Azure Kubernetes Service (AKS) beállítható úgy, hogy a felhasználók hitelesítéséhez az Azure Active Directory (AD) használja. Ebben a konfigurációban, jelentkezzen be egy AKS-fürtöt az Azure AD hitelesítési token használatával. Kubernetes is konfigurálhat szerepköralapú hozzáférés-vezérlés (RBAC) a fürt erőforrásokhoz való hozzáférés korlátozásához a felhasználói identitás vagy csoporttagság alapján.

Ez a cikk bemutatja, hogyan használhatja az Azure AD biztonságicsoport-tagság férhet hozzá a névterek és a fürt erőforrásai az AKS-fürtben Kubernetes RBAC használatával. Példa csoportok és felhasználók jönnek létre az Azure ad-ben, majd a szerepkörök és RoleBindings jönnek létre az AKS-fürtöt hozhat létre és -erőforrások megtekintése a megfelelő engedélyek megadására.

## <a name="before-you-begin"></a>Előzetes teendők

Ez a cikk feltételezi, hogy egy meglévő AKS-fürtöt az Azure AD-integrációval engedélyezve van. Ha egy AKS-fürtre van szüksége, tekintse meg [integrálása az Azure Active Directory az aks-sel][azure-ad-aks-cli].

Az Azure CLI 2.0.61 verziójára van szükség, vagy később telepített és konfigurált. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="create-demo-groups-in-azure-ad"></a>Bemutató csoportok létrehozása az Azure ad-ben

Ebben a cikkben hozzunk létre két felhasználói szerepkörök, amelyek bemutatják, hogyan Kubernetes RBAC és az Azure AD erőforrásokhoz való hozzáférésének fürt segítségével. A következő két példa szerepköröket használja:

* **Alkalmazásfejlesztő**
    * A felhasználó nevű *aksdev* részét képezi, amely a *appdev* csoport.
* **Hely megbízhatóság mérnök**
    * A felhasználó nevű *akssre* részét képezi, amely a *opssre* csoport.

Éles környezetben használhat meglévő felhasználók és csoportok Azure AD-bérlő belül.

Először kérje le az AKS fürt használata az erőforrás-Azonosítóját a [az aks show] [ az-aks-show] parancsot. Az erőforrás-azonosító hozzárendelése nevű változó *AKS_ID* úgy, hogy a további parancsok is lehet hivatkozni.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

Az első példa csoport létrehozása az Azure ad-ben a segítségével az alkalmazásfejlesztők a [az ad-csoport létrehozása] [ az-ad-group-create] parancsot. A következő példában létrehozunk egy nevű csoport *appdev*:

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

Most hozzon létre egy Azure szerepkör-hozzárendelést a *appdev* használó csoport a [az szerepkör-hozzárendelés létrehozása] [ az-role-assignment-create] parancsot. Ez a hozzárendelés lehetővé teszi, hogy bármely tagja a csoport használata `kubectl` azáltal, hogy biztosítja azokat az AKS-fürt interakcióba a *Azure Kubernetes Service fürt felhasználói szerepkör*.

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> Ha hibaüzenetet kap például `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.`, várjon néhány másodpercet, amíg a címtár rendszeren belüli propagálásához, majd próbálja meg az Azure ad-ben csoportobjektum azonosítója a `az role assignment create` újra a parancsot.

Hozzon létre egy második példa csoportot, ez az egyik az SREs nevű *opssre*:

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

Újra, hozzon létre egy Azure szerepkör-hozzárendelés megadása a csoport tagjai a *Azure Kubernetes Service fürt felhasználói szerepkör*:

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Bemutató felhasználók létrehozása az Azure ad-ben

Két példa csoportokkal, az alkalmazásfejlesztők és SREs az Azure AD-ben létrehozott most hozzuk létre a két példa felhasználót. A cikk végén található az RBAC-integráció teszteléséhez, jelentkezzen be az AKS-fürtöt ezeket a fiókokat.

Az első felhasználói fiók létrehozása az Azure AD-t a [az ad-felhasználó létrehozása] [ az-ad-user-create] parancsot.

A következő példában létrehozunk egy felhasználó megjelenítési nevű *AKS fejlesztési* és az egyszerű felhasználónév (UPN) az `aksdev@contoso.com`. Frissítése az Azure AD-bérlő ellenőrzött tartományt tartalmazza az egyszerű felhasználónév (cserélje le *contoso.com* saját tartomány), és adja meg a saját biztonságos `--password` hitelesítő adat:

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name aksdev@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)
```

Most adja hozzá a felhasználót a *appdev* az előző szakaszban használatával létrehozott csoport a [az ad a csoporttag felvétele] [ az-ad-group-member-add] parancsot:

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

Hozzon létre egy második felhasználói fiókot. A következő példában létrehozunk egy felhasználó megjelenítési nevű *AKS SRE* és az egyszerű felhasználónév (UPN) az `akssre@contoso.com`. Újra, frissítse az Azure AD-bérlő ellenőrzött tartományt tartalmazza az egyszerű felhasználónév (cserélje le *contoso.com* saját tartomány), és adja meg a saját biztonságos `--password` hitelesítő adat:

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

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>A alkalmazás üzemeltetni az AKS-fürt erőforrásainak létrehozása

Az Azure AD-csoportok és felhasználók most jönnek létre. Azure szerepkör-hozzárendelések létrehozott AKS-fürt normál felhasználóként csatlakozik a csoport tagjai. Most konfiguráljuk az AKS-fürtöt a különböző csoportok adott erőforrásokhoz való hozzáférés engedélyezéséhez.

Először kérje le a fürt rendszergazdai hitelesítő adatok használatával a [az aks get-credentials] [ az-aks-get-credentials] parancsot. A következő szakaszok egyikét, a normál kap *felhasználói* fürt hitelesítő adatait, tekintse meg az Azure AD-hitelesítés folyamat.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Hozzon létre egy névteret a használatával az AKS-fürt a [kubectl névtér létrehozása] [ kubectl-create] parancsot. A következő példában létrehozunk egy névtér neve *fejlesztési*:

```console
kubectl create namespace dev
```

A Kubernetes *szerepkörök* határozza meg az engedélyeket, és *RoleBindings* a alkalmazni kívánt felhasználókat vagy csoportokat. Ezeket a hozzárendeléseket is alkalmazható, egy adott névtérhez vagy az egész fürt között. További információkért lásd: [használatával RBAC-hitelesítés][rbac-authorization].

Először hozzon létre egy szerepkört a *fejlesztési* névtér. Ez a szerepkör teljes körű engedélyt ad a névtérhez. Éles környezetben a részletesebb engedélyek a különböző felhasználók vagy csoportok is megadhat.

Hozzon létre egy fájlt `role-dev-namespace.yaml` , és illessze be a következő YAML-jegyzékfájl:

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

Létrehozhatja a szerepkör a [a kubectl a alkalmazni] [ kubectl-apply] parancsot, majd adja meg a YAML-jegyzékfájl:

```console
kubectl apply -f role-dev-namespace.yaml
```

Ezután kérdezze le az erőforrás-azonosító a *appdev* használó csoport a [az ad-csoport megjelenítése] [ az-ad-group-show] parancsot. Ez a csoport a következő lépésben egy RoleBinding tárgya van beállítva.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

Most hozzon létre egy RoleBinding számára a *appdev* csoport a korábban létrehozott szerepkör névtér elérésére használhat. Hozzon létre egy fájlt `rolebinding-dev-namespace.yaml` , és illessze be a következő YAML-jegyzékfájlt. Az utolsó sorban cserélje le a *groupObjectId* az a csoport objektum-azonosító kimenetét az előző parancs:

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

Létrehozhatja a RoleBinding a [a kubectl a alkalmazni] [ kubectl-apply] parancsot, majd adja meg a YAML-jegyzékfájl:

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>Az AKS-fürt erőforrásainak SREs létrehozása

Most ismételje az előző lépést a SREs egy névtér, szerepkörhöz és RoleBinding létrehozása.

Először is hozzon létre egy névteret *sre* használatával a [kubectl névtér létrehozása] [ kubectl-create] parancsot:

```console
kubectl create namespace sre
```

Hozzon létre egy fájlt `role-sre-namespace.yaml` , és illessze be a következő YAML-jegyzékfájl:

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

Létrehozhatja a szerepkör a [a kubectl a alkalmazni] [ kubectl-apply] parancsot, majd adja meg a YAML-jegyzékfájl:

```console
kubectl apply -f role-sre-namespace.yaml
```

Az erőforrás-azonosító beszerzése a *opssre* használó csoport a [az ad-csoport megjelenítése] [ az-ad-group-show] parancsot:

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

Hozzon létre egy RoleBinding számára a *opssre* csoport a korábban létrehozott szerepkör névtér elérésére használhat. Hozzon létre egy fájlt `rolebinding-sre-namespace.yaml` , és illessze be a következő YAML-jegyzékfájlt. Az utolsó sorban cserélje le a *groupObjectId* az a csoport objektum-azonosító kimenetét az előző parancs:

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

Létrehozhatja a RoleBinding a [a kubectl a alkalmazni] [ kubectl-apply] parancsot, majd adja meg a YAML-jegyzékfájl:

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Fürterőforrások használatával az Azure AD-identitások kezelése

Most pedig a várt engedélyek munkahelyi tesztelése, létrehozásakor és -erőforrásait kezelheti az AKS-fürt. Ezekben a példákban ütemezhet, és megtekintheti a felhasználó hozzárendelt névtér podok. Ezt követően próbálja ütemezés és a nézet podok kívül a hozzárendelt névtér.

Először is alaphelyzetbe állítása a *kubeconfig* környezet használatával a [az aks get-credentials] [ az-aks-get-credentials] parancsot. Az egyik előző szakaszban állítsa be a környezetet, a fürt rendszergazdai hitelesítő adataival. A rendszergazdai felhasználó nincs hatással lévő utasításokat az Azure AD-be. Nélkül a `--admin` paramétert, a felhasználói környezet alkalmazott összes kérelmek hitelesítése az Azure AD szükséges.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Egy alapszintű nginx-et pod az ütemezés a [futtatása kubectl] [ kubectl-run] parancsot a *fejlesztési* névtér:

```console
kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev
```

A bejelentkezés kérése, adja meg a hitelesítő adatokat a saját `appdev@contoso.com` a cikk elején létrehozott fiókot. Miután sikeresen bejelentkezett, a fiók token jövőben van gyorsítótárazva `kubectl` parancsokat. Az NGINX sikeresen ütemezése, az alábbi példa kimenetében látható módon:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

Mostantól használhatja a [kubectl get pods] [ kubectl-get] parancsot podok megtekintéséhez a *fejlesztési* névtér.

```console
kubectl get pods --namespace dev
```

Az alábbi példa kimenetében látható, az NGINX-pod sikeresen van *futó*:

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>Létrehozhat és megjeleníthet a hozzárendelt névtér kívül a fürt erőforrásai

Most megpróbálja kívüli podok megtekintése a *fejlesztési* névtér. Használja a [kubectl get pods] [ kubectl-get] parancs újbóli, hogy `--all-namespaces` módon:

```console
kubectl get pods --all-namespaces
```

A felhasználói csoporttagság nem rendelkezik egy Kubernetes-szerepkör, amely lehetővé teszi, hogy ez a művelet az alábbi példa kimenetében látható módon:

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

Ugyanúgy, próbálkozzon egy másik névtérben pod például ütemezni a *sre* névtér. A felhasználói csoporttagság nem esik egybe a Kubernetes-szerepkör és a RoleBinding az engedélyek megadása az alábbi példa kimenetében látható módon:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>Az AKS-fürt erőforrásainak eléréséhez a SRE tesztelése

Győződjön meg arról, hogy az Azure AD biztonságicsoport-tagság és a Kubernetes RBAC megfelelő működéséhez különböző felhasználók és csoportok között, próbálja ki az előző parancsok jelentkezett be, amikor a *opssre* felhasználói.

Alaphelyzetbe állítása a *kubeconfig* környezet használatával a [az aks get-credentials] [ az-aks-get-credentials] parancsot, amely törli a korábban gyorsítótárazott hitelesítési tokenje a *aksdev*  felhasználói:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Ütemezés és a nézet podok a hozzárendelt próbál *sre* névtér. Ha a rendszer kéri, jelentkezzen be a saját `opssre@contoso.com` a cikk elején létrehozott hitelesítő adatokat:

```console
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre
kubectl get pods --namespace sre
```

Az alábbi példa kimenetében látható, sikeresen létrehozása és a podok megtekintése:

```console
$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

Most próbáljon meg megtekintéséhez, vagy kívül hozzárendelt SRE névtér podok ütemezése:

```console
kubectl get pods --all-namespaces
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
```

Ezek `kubectl` parancsok sikertelenek, az alábbi példa kimenetében látható módon. A felhasználó csoporttagságát és Kubernetes szerepkör és RoleBindings létrehozásához szükséges engedélyek vagy más névtérben manager-erőforrások nem megadása:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben a cikkben létrehozott erőforrásokat az AKS-fürt és a felhasználók és csoportok az Azure ad-ben. Összes erőforrás törléséhez futtassa a következő parancsokat:

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

Kubernetes-fürtök biztonságos kapcsolatos további információkért lásd: [hozzáférési és azonosító beállításai az aks-ben)][rbac-authorization].

Identitás- és erőforrás-vezérlést ajánlott eljárásokat lásd: [gyakorlati tanácsok a hitelesítés és engedélyezés az aks-ben][operator-best-practices-identity].

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
