---
title: Azure-beli Kubernetes-fürt webes felhasználói felületének kezelése
description: Ismerje meg, hogyan használható a beépített Kubernetes webes felhasználói felületének irányítópultja az Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/09/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: e197a251df3f34e5416bafacfd54a3fc7f51d503
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928216"
---
# <a name="access-the-kubernetes-dashboard-with-azure-kubernetes-service-aks"></a>Hozzáférés a Kubernetes-irányítópultot az Azure Kubernetes Service (AKS)

Kubernetes webes irányítópultot is használható alapvető felügyeleti műveletet tartalmaz. Ez a cikk bemutatja, hogyan érhető el az Azure CLI segítségével Kubernetes-irányítópult, és végigvezeti néhány alapvető irányítópult művelet. A Kubernetes-irányítópult további információkért lásd: [Kubernetes webes felhasználói felületének irányítópultja][kubernetes-dashboard].

## <a name="before-you-begin"></a>Előkészületek

Ebben a dokumentumban ismertetett lépések feltételezik, hogy már létrehozott egy AKS-fürtöt, és létrehozott egy `kubectl` kapcsolatot a fürttel. Ha szeretne létrehozni egy AKS-fürtöt, tekintse meg a [AKS gyors][aks-quickstart].

Emellett az Azure CLI 2.0.27-es vagy újabb, telepített és konfigurált verziójával is rendelkeznie kell. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="start-kubernetes-dashboard"></a>Indítsa el a Kubernetes-irányítópult

A Kubernetes-irányítópult indításához használja a [az aks browse] [ az-aks-browse] parancsot. Az alábbi példa megnyitja az irányítópultot, a fürt nevű *myAKSCluster* az erőforráscsoport neve *myResourceGroup*:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Ez a parancs létrehoz egy proxy között a fejlesztői rendszerhez, és a Kubernetes API-t, és a Kubernetes-irányítópult egy webböngészőben megnyílik.

### <a name="for-rbac-enabled-clusters"></a>A fürtök RBAC-kompatibilis

Ha az AKS-fürtöt használ, az RBAC- *ClusterRoleBinding* léteznie kell az irányítópult eléréséhez. Egy szerepkör-kötés nélkül az Azure CLI hibát ad vissza a következő példához hasonló:

```
error: unable to forward port because pod is not running. Current status=Pending
```

Hozzon létre egy kötést, hozzon létre egy fájlt *irányítópult-admin.yaml* , és illessze be a következő mintát. Ez a minta kötés nem vonatkozik minden további hitelesítés összetevői. Használhatja a mechanizmusokkal, mint például a tulajdonosi jogkivonatokat vagy férhet hozzá az irányítópultot, és mi felhasználónév/jelszó engedélyekkel rendelkeznek. Hitelesítési módszerekkel kapcsolatos további információkért tekintse meg a Kubernetes-irányítópult wiki [hozzáférés-vezérlés][dashboard-authentication].

```yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: kubernetes-dashboard
  labels:
    k8s-app: kubernetes-dashboard
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: kubernetes-dashboard
  namespace: kube-system
```

A kötés a alkalmazni [a kubectl a alkalmazni] [ kubectl-apply] , és adja meg a *irányítópult-admin.yaml*, az alábbi példában látható módon:

```
$ kubectl apply -f dashboard-admin.yaml

clusterrolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
```

Most már elérheti a Kubernetes-irányítópultot az RBAC-t fürtben. A Kubernetes-irányítópult indításához használja a [az aks browse] [ az-aks-browse] parancsot az előző lépésben leírt módon.


## <a name="run-an-application"></a>Alkalmazás futtatása

A Kubernetes-irányítópultot, kattintson a **létrehozás** gombra a jobb felső ablakban. Nevezze el az üzembe helyezés a `nginx` , és adja meg `nginx:latest` a tároló-rendszerkép neveként. Alatt **szolgáltatás**válassza **külső** , és adja meg `80` a port és a célport.

Ha elkészült, kattintson a **telepítés** a központi telepítés létrehozásához.

![Kubernetes-szolgáltatás létrehozása párbeszédpanel](./media/container-service-kubernetes-ui/create-deployment.png)

## <a name="view-the-application"></a>Az alkalmazás megtekintése

Az alkalmazás állapota a Kubernetes-irányítópult látható. Miután az alkalmazás fut, az egyes összetevők rendelkezik egy zöld jelölőnégyzetét.

![Kubernetes-Podok](./media/container-service-kubernetes-ui/complete-deployment.png)

További információ az alkalmazás podok megtekintéséhez kattintson a **Podok** a bal oldali menüben, és válassza ki a **NGINX** pod. Itt láthatja a pod-specifikus információkat, például az erőforrás-használat.

![Kubernetes-erőforrást](./media/container-service-kubernetes-ui/running-pods.png)

Az alkalmazás IP-cím kereséséhez kattintson a **szolgáltatások** a bal oldali menüben, és válassza ki a **NGINX** szolgáltatás.

![az nginx-nézet](./media/container-service-kubernetes-ui/nginx-service.png)

## <a name="edit-the-application"></a>Az alkalmazás szerkesztése

Létrehozása és alkalmazások megtekintése, mellett a Kubernetes-irányítópult szerkesztése, és frissíti az alkalmazás-KözpontiTelepítés használható.

Központi telepítés szerkesztéséhez kattintson **központi telepítések** a bal oldali menüben, és válassza ki a **NGINX** üzembe helyezési. Végül válassza **szerkesztése** a jobb felső navigációs sávban.

![Kubernetes Edit](./media/container-service-kubernetes-ui/view-deployment.png)

Keresse meg a `spec.replica` érték, amely 1 kell lennie, módosíthatja ezt az értéket a 3. Ennek során az NGINX üzembe helyezés a replika száma 1-ra emelkedett 3.

Válassza ki **frissítés** Ha készen áll.

![Kubernetes Edit](./media/container-service-kubernetes-ui/edit-deployment.png)

## <a name="next-steps"></a>További lépések

A Kubernetes-irányítópulttal kapcsolatos további információkért tekintse meg a Kubernetes dokumentációját.

> [!div class="nextstepaction"]
> [Kubernetes webes felhasználói felületének irányítópultja][kubernetes-dashboard]

<!-- LINKS - external -->
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-browse]: /cli/azure/aks#az-aks-browse
