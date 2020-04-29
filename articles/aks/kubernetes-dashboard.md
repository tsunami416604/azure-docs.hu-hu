---
title: Azure Kubernetes Service-fürt kezelése a webes irányítópulttal
description: Ismerje meg, hogyan kezelheti az Azure Kubernetes Service (ak) fürtjét a beépített Kubernetes webes felhasználói felületi irányítópult használatával
services: container-service
ms.topic: article
ms.date: 10/08/2018
ms.openlocfilehash: 15fcf765be0a754575713eebcdaa7d68e1c299b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77595348"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>A Kubernetes web Dashboard elérése az Azure Kubernetes szolgáltatásban (ak)

A Kubernetes tartalmaz egy webes irányítópultot, amely alapszintű felügyeleti műveletekhez használható. Ez az irányítópult lehetővé teszi az alkalmazások alapvető állapotának és mérőszámának megtekintését, szolgáltatások létrehozását és üzembe helyezését, valamint meglévő alkalmazások szerkesztését. Ez a cikk bemutatja, hogyan érheti el a Kubernetes-irányítópultot az Azure CLI használatával, majd végigvezeti az irányítópultok alapszintű műveletein.

A Kubernetes-irányítópulttal kapcsolatos további információkért lásd: [Kubernetes webes felhasználói felület irányítópultja][kubernetes-dashboard].

## <a name="before-you-begin"></a>Előkészületek

A dokumentumban részletezett lépések feltételezik, hogy létrehozott egy AK-fürtöt, és létesítettek egy `kubectl` , a fürttel létesített kapcsolatokat. Ha AK-fürtöt kell létrehoznia, tekintse meg az [AK][aks-quickstart]gyors üzembe helyezését ismertető témakört.

Emellett az Azure CLI 2.0.46-os vagy újabb, telepített és konfigurált verziójával is rendelkeznie kell. A verzió azonosításához futtassa a következőt:  `az --version` . Ha telepíteni vagy frissíteni szeretne, tekintse meg az [Azure CLI telepítését][install-azure-cli]ismertető témakört.

## <a name="start-the-kubernetes-dashboard"></a>A Kubernetes irányítópult elindítása

A Kubernetes-irányítópult elindításához használja az az [AK Browse][az-aks-browse] parancsot. Az alábbi példa megnyitja az irányítópultot a *myAKSCluster* nevű fürthöz az *myResourceGroup*nevű erőforráscsoport:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Ez a parancs létrehoz egy proxyt a fejlesztői rendszer és a Kubernetes API között, és egy webböngészőt nyit meg a Kubernetes-irányítópulton. Ha egy webböngésző nem nyílik meg a Kubernetes irányítópultra, másolja és illessze be az Azure CLI-ben feljegyzett URL `http://127.0.0.1:8001`-címet, jellemzően.

<!--
![The login page of the Kubernetes web dashboard](./media/kubernetes-dashboard/dashboard-login.png)

You have the following options to sign in to your cluster's dashboard:

* A [kubeconfig file][kubeconfig-file]. You can generate a kubeconfig file using [az aks get-credentials][az-aks-get-credentials].
* A token, such as a [service account token][aks-service-accounts] or user token. On [AAD-enabled clusters][aad-cluster], this token would be an AAD token. You can use `kubectl config view` to list the tokens in your kubeconfig file. For more details on creating an AAD token for use with an AKS cluster see [Integrate Azure Active Directory with Azure Kubernetes Service using the Azure CLI][aad-cluster].
* The default dashboard service account, which is used if you click *Skip*.

> [!WARNING]
> Never expose the Kubernetes dashboard publicly, regardless of the authentication method used.
> 
> When setting up authentication for the Kubernetes dashboard, it is recommended that you use a token over the default dashboard service account. A token allows each user to use their own permissions. Using the default dashboard service account may allow a user to bypass their own permissions and use the service account instead.
> 
> If you do choose to use the default dashboard service account and your AKS cluster uses RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays RBAC access errors. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
>
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command as shown in the following example. **This sample binding does not apply any additional authentication components and may lead to insecure use.**
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> You can now access the Kubernetes dashboard in your RBAC-enabled cluster. To start the Kubernetes dashboard, use the [az aks browse][az-aks-browse] command as detailed in the previous step.
>
> If your cluster does not use RBAC, it is not recommended to create a *ClusterRoleBinding*.
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].

After you choose a method to sign in, the Kubernetes dashboard is displayed. If you chose to use *token* or *skip*, the Kubernetes dashboard will use the permissions of the currently logged in user to access the cluster.
-->

> [!IMPORTANT]
> Ha az AK-fürt RBAC használ, létre kell hoznia egy *ClusterRoleBinding* ahhoz, hogy megfelelően hozzáférhessen az irányítópulthoz. Alapértelmezés szerint a Kubernetes irányítópult minimális olvasási hozzáféréssel van telepítve, és megjeleníti a RBAC hozzáférési hibáit. A Kubernetes irányítópult jelenleg nem támogatja a felhasználó által megadott hitelesítő adatokat a hozzáférés szintjének meghatározásához, hanem a szolgáltatásfiók által biztosított szerepköröket használja. A fürt rendszergazdája dönthet úgy, hogy további hozzáférést biztosít a *kubernetes-irányítópult* szolgáltatásfiók számára, azonban ez a jogosultság-eszkaláció vektora lehet. A Azure Active Directory hitelesítését is integrálhatja, hogy részletesebb hozzáférési szintet biztosítson.
> 
> Kötés létrehozásához használja a [kubectl Create clusterrolebinding][kubectl-create-clusterrolebinding] parancsot. Az alábbi példa bemutatja, hogyan hozhat létre egy minta kötést, azonban ez a minta kötés nem alkalmazza a további hitelesítési összetevőket, és nem biztonságos használatot eredményezhet. A Kubernetes irányítópultja bárki számára elérhető, aki hozzáfér az URL-címhez. Ne tegye közzé nyilvánosan a Kubernetes-irányítópultot.
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> A különböző hitelesítési módszerek használatával kapcsolatos további információkért tekintse meg a [hozzáférés-vezérlések][dashboard-authentication]Kubernetes irányítópult wikijét.

![A Kubernetes webes irányítópultjának Áttekintés lapja](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>Alkalmazás létrehozása

Ha szeretné megtudni, hogyan csökkentheti a Kubernetes irányítópultja a felügyeleti feladatok összetettségét, hozzon létre egy alkalmazást. Létrehozhat egy alkalmazást a Kubernetes-irányítópulton egy szövegbeviteli, egy YAML-fájl vagy egy grafikus varázsló segítségével.

Alkalmazás létrehozásához hajtsa végre a következő lépéseket:

1. Válassza a **Létrehozás** gombot a jobb felső ablakban.
1. A grafikus varázsló használatához válassza az **alkalmazás létrehozása**lehetőséget.
1. Adja meg az üzemelő példány nevét, például *Nginx*
1. Adja meg a használni kívánt tároló-rendszerkép nevét, például *Nginx: 1.15.5*
1. Ahhoz, hogy a 80-es port elérhető legyen a webes forgalom számára, létre kell hoznia egy Kubernetes szolgáltatást. A **szolgáltatás**területen válassza a **külső**lehetőséget, majd adja meg a **80** értéket a port és a célport esetében is.
1. Ha elkészült, válassza a **telepítés** lehetőséget az alkalmazás létrehozásához.

![Alkalmazás üzembe helyezése a Kubernetes webes irányítópultján](./media/kubernetes-dashboard/create-app.png)

A Kubernetes szolgáltatáshoz hozzárendelt nyilvános külső IP-címekhez egy-két percet vesz igénybe. A bal oldali méretnél a **felderítés és** terheléselosztás területen válassza a **szolgáltatások**lehetőséget. Az alkalmazás szolgáltatása szerepel a listáján, beleértve a *külső végpontokat*is, ahogy az az alábbi példában is látható:

![Szolgáltatások és végpontok listájának megtekintése](./media/kubernetes-dashboard/view-services.png)

Válassza ki a végpontot, és nyissa meg a webböngésző ablakát az alapértelmezett NGINX-lapra:

![Az üzembe helyezett alkalmazás alapértelmezett NGINX-lapjának megtekintése](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>Pod-adatok megtekintése

A Kubernetes irányítópulton alapszintű figyelési mérőszámok és hibaelhárítási információk, például naplók adhatók meg.

Ha többet szeretne megtudni az alkalmazás hüvelyéről, válassza a bal oldali menüben a **hüvelyek** lehetőséget. Megjelenik az elérhető hüvelyek listája. Válassza ki az *Nginx* Pod-t az információk, például az erőforrás-felhasználás megtekintéséhez:

![Pod-adatok megtekintése](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>Az alkalmazás szerkesztése

Az alkalmazások létrehozása és megtekintése mellett a Kubernetes irányítópultja is használható az alkalmazások központi telepítésének szerkesztéséhez és frissítéséhez. Az alkalmazás további redundancia biztosítása érdekében növeljük az NGINX-replikák számát.

Központi telepítés szerkesztése:

1. Válassza a **központi telepítések** lehetőséget a bal oldali menüben, majd válassza ki a *Nginx* -telepítést.
1. Válassza a **Szerkesztés** lehetőséget a jobb felső navigációs sávon.
1. Keresse meg `spec.replica` az értéket a következő helyen: 20. sor. Az alkalmazás replikáinak számának növeléséhez módosítsa az értéket *1* és *3*között.
1. Ha elkészült, válassza a **frissítés** lehetőséget.

![A központi telepítés szerkesztése a replikák számának frissítéséhez](./media/kubernetes-dashboard/edit-deployment.png)

Néhány percet vesz igénybe, hogy az új hüvelyek létre legyenek hozva egy replikakészlet-készleten belül. A bal oldali menüben válassza a **replika készletek**lehetőséget, majd válassza ki az *Nginx* -replikát. A hüvelyek listája most már a frissített replikák számát tükrözi, ahogy az a következő példában látható:

![A replikakészlet információinak megtekintése](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>További lépések

A Kubernetes-irányítópulttal kapcsolatos további információkért tekintse meg a [Kubernetes webes felhasználói felületének irányítópultját][kubernetes-dashboard].

<!-- LINKS - external -->
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubeconfig-file]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

<!-- LINKS - internal -->
[aad-cluster]: ./azure-ad-integration-cli.md
[aks-quickstart]: ./kubernetes-walkthrough.md
[aks-service-accounts]: ./concepts-identity.md#kubernetes-service-accounts
[az-account-get-access-token]: /cli/azure/account?view=azure-cli-latest#az-account-get-access-token
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
