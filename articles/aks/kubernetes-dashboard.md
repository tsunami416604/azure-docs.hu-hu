---
title: Azure Kubernetes Service-fürt a webes irányítópult kezelése
description: Ismerje meg, hogyan használhatja a beépített Kubernetes webes felhasználói felületének irányítópultja az Azure Kubernetes Service (AKS)-fürt kezelése
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/08/2018
ms.author: iainfou
ms.openlocfilehash: eb5d1ee88fc95f7d02842ea089f65c6d17a6d69d
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50091741"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Hozzáférés a Kubernetes webes irányítópultot az Azure Kubernetes Service (AKS)

Kubernetes webes irányítópultot is használható alapvető felügyeleti műveletet tartalmaz. Ez az irányítópult lehetővé teszi az alapszintű állapot-állapot és az alkalmazások metrikáinak megtekintése, létrehozása és -szolgáltatások telepítését és szerkesztheti a meglévő alkalmazásokat. Ez a cikk bemutatja, hogyan érhető el az Azure CLI segítségével Kubernetes-irányítópult, és végigvezeti néhány alapvető irányítópult művelet.

A Kubernetes-irányítópult további információkért lásd: [Kubernetes webes felhasználói felületének irányítópultja][kubernetes-dashboard].

## <a name="before-you-begin"></a>Előkészületek

Ebben a dokumentumban ismertetett lépések feltételezik, hogy már létrehozott egy AKS-fürtöt, és létrehozott egy `kubectl` kapcsolatot a fürttel. Ha szeretne létrehozni egy AKS-fürtöt, tekintse meg a [AKS gyors][aks-quickstart].

Emellett az Azure CLI 2.0.46-os vagy újabb, telepített és konfigurált verziójával is rendelkeznie kell. A verzió azonosításához futtassa a következőt:  `az --version` . Ha telepíteni vagy frissíteni, tekintse meg kell [Azure CLI telepítése][install-azure-cli].

## <a name="start-the-kubernetes-dashboard"></a>Indítsa el a Kubernetes-irányítópult

A Kubernetes-irányítópult indításához használja a [az aks browse] [ az-aks-browse] parancsot. Az alábbi példa megnyitja az irányítópultot, a fürt nevű *myAKSCluster* az erőforráscsoport neve *myResourceGroup*:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

> [!NOTE]
> Ha ez a parancs az Azure Cloud Shellben futtatja, kell hozzáadni a `--enable-cloud-console-aks-browse` paraméter segítségével lehet megnyitni az irányítópultot.

Ez a parancs létrehoz egy proxy között a fejlesztői rendszerhez, és a Kubernetes API-t, és a Kubernetes-irányítópult egy webböngészőben megnyílik. Egy webes böngésző nem nyílik meg, a Kubernetes-irányítópultot, ha másolja és illessze be az URL-cím általában az Azure CLI-feljegyzett *http://127.0.0.1:8001*.

![A Kubernetes webes irányítópult áttekintés oldalán](./media/kubernetes-dashboard/dashboard-overview.png)

### <a name="for-rbac-enabled-clusters"></a>A fürtök RBAC-kompatibilis

Ha az AKS-fürtöt használ, az RBAC- *ClusterRoleBinding* megfelelően az irányítópult elérése előtt kell létrehozni. Alapértelmezés szerint a Kubernetes-irányítópult minimális olvasási hozzáféréssel rendelkező telepíti, és megjeleníti az RBAC-hozzáférési hibák. A Kubernetes-irányítópult jelenleg nem támogatja a felhasználó által megadott hitelesítő adatokat határozza meg a hozzáférési szintet, inkább használja a szerepkörök a szolgáltatásfiók számára. A fürt rendszergazdája választhat további hozzáférés adható a *kubernetes-irányítópult* szolgáltatásfiók, azonban ez lehet egy vektoros a jogosultságok eszkalációját. A részletesebb szintű hozzáférés az Azure Active Directory-hitelesítést is integrálhatja.

A kötés létrehozásához használja a [kubectl létrehozása clusterrolebinding] [ kubectl-create-clusterrolebinding] parancsot az alábbi példában látható módon. 

> [!WARNING]
> Ez a minta kötés nem vonatkozik minden további hitelesítés összetevői, és nem biztonságos használat vezethet. A Kubernetes-irányítópult meg nyitva, bárki hozzáférhet az URL-cím hozzáférést. Nem teszik elérhetővé a Kubernetes-irányítópult nyilvánosan.
>
> A különböző hitelesítési módszerekkel további információkért tekintse meg a Kubernetes-irányítópult wiki [hozzáférés-vezérlés][dashboard-authentication].

```console
kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
```

Most már elérheti a Kubernetes-irányítópultot az RBAC-t fürtben. A Kubernetes-irányítópult indításához használja a [az aks browse] [ az-aks-browse] parancsot az előző lépésben leírt módon.

## <a name="create-an-application"></a>Alkalmazás létrehozása

A Kubernetes-irányítópult hogyan csökkentheti a feladatok összetettsége megtekintéséhez hozzunk létre egy alkalmazást. A Kubernetes-irányítópultot azáltal, hogy szövegbevitel, egy YAML-fájlt, vagy egy grafikus varázslóval létrehozhat egy alkalmazást.

Alkalmazás létrehozása a következő lépéseket:

1. Válassza ki a **létrehozás** gombra a jobb felső ablakban.
1. A grafikus varázsló használatához válassza a **hozzon létre egy alkalmazást**.
1. Adjon meg egy nevet a központi telepítést, például: *nginx-et*
1. Adja meg a nevet szeretné használni, mint például a tároló rendszerképét *nginx:1.15.5*
1. Tegye elérhetővé a 80-as portot a webes forgalmat, hozzon létre egy Kubernetes-szolgáltatást. Alatt **szolgáltatás**válassza **külső**, majd adja meg **80-as** a port és a célport.
1. Ha elkészült, válassza ki a **telepítés** az alkalmazás létrehozásához.

![A Kubernetes webes irányítópult-alkalmazás üzembe helyezése](./media/kubernetes-dashboard/create-app.png)

Egy-egy nyilvános külső IP-cím hozzá kell rendelni a Kubernetes-szolgáltatást két percet vesz igénybe. A bal oldali mérete alatt **felderítési és a terheléselosztás** válassza **szolgáltatások**. Az alkalmazás szolgáltatás szerepel a listán, beleértve a *külső végpontokat*, az alábbi példában látható módon:

![Szolgáltatások és a végpontok listájának megtekintése](./media/kubernetes-dashboard/view-services.png)

Válassza ki a végpont-címét, egy webböngésző-ablakot az alapértelmezett NGINX-lap megnyitása:

![Az alapértelmezett NGINX-lap az üzembe helyezett alkalmazás megtekintéséhez.](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>Pod-adatok megtekintése

A Kubernetes-irányítópult biztosíthat az alapvető metrikák figyelési és hibaelhárítási információkat, például a naplók.

További információ az alkalmazás podok megtekintéséhez válasszon **Podok** a bal oldali menüben. A rendelkezésre álló podok listája látható. Válassza ki a *nginx* pod információk, például az erőforrás-használat megtekintése:

![Pod-adatok megtekintése](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>Az alkalmazás szerkesztése

Létrehozása és alkalmazások megtekintése, mellett a Kubernetes-irányítópult szerkesztése, és frissíti az alkalmazás-KözpontiTelepítés használható. Az alkalmazás további redundancia biztosításához hozzunk NGINX replikák számának növelése.

Központi telepítés szerkesztése:

1. Válassza ki **központi telepítések** a bal oldali menüben, majd válassza a *nginx* központi telepítés.
1. Válassza ki **szerkesztése** a jobb felső navigációs sávban.
1. Keresse meg a `spec.replica` érték, a körül sor a 20. Az alkalmazás-replikák számának növelésére, módosítsa ezt az értéket *1* való *3*.
1. Válassza ki **frissítés** Ha készen áll.

![Módosítsa a telepítését frissíteni a replikák száma](./media/kubernetes-dashboard/edit-deployment.png)

A replikakészlet hozhatók létre az új podok néhány másodpercig tart. A bal oldali menüben válassza a **replikakészletekhez**, majd válassza a *nginx* replikakészlethez. Podok listájának most tükrözi a frissített replikáinak száma, az alábbi példa kimenetében látható módon:

![A replikakészlet vonatkozó információk megtekintése](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>További lépések

A Kubernetes-irányítópulttal kapcsolatos további információkért lásd: a [Kubernetes webes felhasználói felületének irányítópultja][kubernetes-dashboard].

<!-- LINKS - external -->
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-browse]: /cli/azure/aks#az-aks-browse
