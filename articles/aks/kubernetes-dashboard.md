---
title: Azure Kubernetes Service-fürt kezelése a webes irányítópulttal
description: Ismerje meg, hogyan kezelheti az Azure Kubernetes Service (ak) fürtjét a beépített Kubernetes webes felhasználói felületi irányítópult használatával
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 10/08/2018
ms.author: mlearned
ms.openlocfilehash: 5aa8268fee7d43ad13ea8710760ba493683f502e
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126887"
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

![A Kubernetes webes irányítópultjának bejelentkezési lapja](./media/kubernetes-dashboard/dashboard-login.png)

A következő beállításokkal jelentkezhet be a fürt irányítópultján:

* Egy [kubeconfig-fájl][kubeconfig-file]. Létrehozhat egy kubeconfig [-fájlt az az az Kaba Get-hitelesítő adatok][az-aks-get-credentials]használatával.
* Token, például [szolgáltatásfiók-jogkivonat][aks-service-accounts] vagy felhasználói jogkivonat. [HRE-kompatibilis fürtökön][aad-cluster]ez a jogkivonat HRE token lenne. A paranccsal `kubectl config view` listázhatja a kubeconfig-fájlban található jogkivonatokat. Az HRE-jogkivonat AK-fürthöz való létrehozásával kapcsolatos további információkért lásd: [Az Azure Kubernetes szolgáltatással való integráció Azure Active Directory az Azure CLI használatával][aad-cluster].
* Az alapértelmezett irányítópult-szolgáltatásfiók, amely akkor használható, ha a *kihagyás*gombra kattint.

> [!WARNING]
> A használt hitelesítési módszertől függetlenül soha ne tegye közzé nyilvánosan a Kubernetes-irányítópultot.
> 
> A Kubernetes-irányítópult hitelesítésének beállításakor azt javasoljuk, hogy használjon jogkivonatot az alapértelmezett irányítópult-szolgáltatásfiók használatával. A tokenek lehetővé teszik, hogy az egyes felhasználók saját engedélyeiket használják. Az alapértelmezett irányítópult-szolgáltatásfiók lehetővé teheti, hogy a felhasználó megkerüljék a saját engedélyeiket, és inkább a szolgáltatásfiókot használják.
> 
> Ha úgy dönt, hogy az alapértelmezett irányítópult-szolgáltatásfiókot használja, és az AK-fürt RBAC használ, létre kell hoznia egy *ClusterRoleBinding* ahhoz, hogy megfelelően hozzáférhessen az irányítópulthoz. Alapértelmezés szerint a Kubernetes irányítópult minimális olvasási hozzáféréssel van telepítve, és megjeleníti a RBAC hozzáférési hibáit. A fürt rendszergazdája dönthet úgy, hogy további hozzáférést biztosít a *kubernetes-irányítópult* szolgáltatásfiók számára, azonban ez a jogosultság-eszkaláció vektora lehet. A Azure Active Directory hitelesítését is integrálhatja, hogy részletesebb hozzáférési szintet biztosítson.
>
> Kötés létrehozásához használja a [kubectl Create clusterrolebinding][kubectl-create-clusterrolebinding] parancsot az alábbi példában látható módon. **Ez a minta kötés nem alkalmaz további hitelesítési összetevőket, és nem biztonságos használatot eredményezhet.**
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> Most már elérheti a Kubernetes-irányítópultot a RBAC-kompatibilis fürtben. A Kubernetes-irányítópult elindításához használja az az [AK Browse][az-aks-browse] parancsot az előző lépésben részletezett módon.
>
> Ha a fürt nem használ RBAC, nem ajánlott *ClusterRoleBinding*létrehozni.
> 
> A különböző hitelesítési módszerek használatával kapcsolatos további információkért tekintse meg a [hozzáférés][dashboard-authentication]-vezérlések Kubernetes irányítópult wikijét.

Miután kiválasztotta a bejelentkezni kívánt módszert, megjelenik a Kubernetes irányítópult. Ha a *token* vagy a *kihagyás*lehetőséget választotta, a Kubernetes-irányítópult az aktuálisan bejelentkezett felhasználó engedélyeit fogja használni a fürt eléréséhez.

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

Ha többet szeretne megtudni az alkalmazás hüvelyéről, válassza a bal oldali menüben a hüvelyek lehetőséget. Megjelenik az elérhető hüvelyek listája. Válassza ki az *Nginx* Pod-t az információk, például az erőforrás-felhasználás megtekintéséhez:

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
