---
title: "Webes felhasználói felület Azure Kubernetes fürt kezelése"
description: "Az Kubernetes irányítópulttal AKS"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 11/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: ca828dab7bdb47e41596be2717598cfe828953ca
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="kubernetes-dashboard-with-azure-container-service-aks"></a>Kubernetes irányítópult Azure tároló szolgáltatás (AKS)

Az Azure CLI segítségével a Kubernetes irányítópult elindításához. Ez a dokumentum végigvezeti a Kubernetes irányítópult kezdődően az Azure parancssori felület, és néhány alapvető irányítópult művelet is végigvezeti. További információ a Kubernetes irányítópult lásd [Kubernetes webes felhasználói felületének irányítópultja][kubernetes-dashboard].

## <a name="before-you-begin"></a>Előkészületek

A dokumentumban foglalt lépések feltételezik, hogy korábban már létrehozott egy AKS-fürtöt, és kiépített egy kubectl-kapcsolatot a fürttel. Ha ezek az elemek megjelenítéséhez a [AKS gyors üzembe helyezés][aks-quickstart].

Emellett az Azure CLI 2.0.21-es vagy újabb, telepített és konfigurált verziójával is rendelkeznie kell. A verzió megkereséséhez futtassa a következő parancsot: az --version. Ha szeretné telepíteni vagy frissíteni, lásd: [Azure CLI telepítése][install-azure-cli].

## <a name="start-kubernetes-dashboard"></a>Indítsa el a Kubernetes irányítópult

Használja a `az aks browse` parancsot a Kubernetes irányítópult elindításához. A parancs futtatásakor cserélje le az erőforrás-csoport és a fürt nevét.

```azurecli
az aks browse --resource-group myResourceGroup --name myK8SCluster
```

A parancs létrehozza a fejlesztői rendszerhez és a Kubernetes API között elhelyezkedő proxy, és a Kubernetes irányítópult egy webböngészőben megnyílik.

## <a name="run-an-application"></a>Alkalmazás futtatása

Kubernetes irányítópultján kattintson a **létrehozása** gombra a jobb felső ablakban. Nevezze el a központi telepítés a `nginx` , és írja be `nginx:latest` képek nevét. A **szolgáltatás**, jelölje be **külső** , és írja be `80` a port és a cél port.

Ha elkészült, kattintson **telepítés** létrehozza a központi telepítést.

![Kubernetes Service létrehozás párbeszédpanel](./media/container-service-kubernetes-ui/create-deployment.png)

## <a name="view-the-application"></a>Az alkalmazás megtekintése

Az alkalmazás állapotát a Kubernetes irányítópulton látható. Ha az alkalmazás fut, minden egyes összetevő rendelkezik egy zöld jelölőnégyzetét.

![Kubernetes három munkaállomás-csoporttal](./media/container-service-kubernetes-ui/complete-deployment.png)

Az alkalmazás három munkaállomás-csoporttal kapcsolatos további információk megtekintéséhez kattintson a **három munkaállomás-csoporttal** a bal oldali menüből, majd válassza ki a **NGINX** pod. Itt látható, például az erőforrás-felhasználás pod-specifikus adatait.

![Kubernetes erőforrások](./media/container-service-kubernetes-ui/running-pods.png)

Az alkalmazás az IP-cím kereséséhez kattintson az **szolgáltatások** a bal oldali menüből, majd válassza ki a **NGINX** szolgáltatás.

![nginx megtekintése](./media/container-service-kubernetes-ui/nginx-service.png)

## <a name="edit-the-application"></a>Az alkalmazás szerkesztése

Létrehozásán és alkalmazások megtekintése, a Kubernetes Irányítópult segítségével módosítsa és frissítse az alkalmazások központi telepítéseit.

A központi telepítés szerkesztéséhez kattintson **központi telepítések** a bal oldali menüből, majd válassza ki a **NGINX** központi telepítés. Végül válassza ki **szerkesztése** jobb felső navigációs sávon.

![Kubernetes szerkesztése](./media/container-service-kubernetes-ui/view-deployment.png)

Keresse meg a `spec.replica` értéket, amely 1 kell lennie, módosítsa ezt az értéket a 3. Ennek során a replika száma az NGINX-telepítés megnövelik 1-3.

Válassza ki **frissítés** Ha készen áll.

![Kubernetes szerkesztése](./media/container-service-kubernetes-ui/edit-deployment.png)

## <a name="next-steps"></a>Következő lépések

A Kubernetes irányítópulttal kapcsolatos további információkért a Kubernetes dokumentációjában talál.

> [!div class="nextstepaction"]
> [Kubernetes webes felhasználói felületének irányítópultja][kubernetes-dashboard]

<!-- LINKS - external -->
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli