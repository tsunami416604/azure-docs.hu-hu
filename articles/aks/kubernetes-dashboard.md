---
title: "Webes felhasználói felület Azure Kubernetes fürt kezelése |} Microsoft Docs"
description: "Az Kubernetes irányítópulttal AKS"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 764bbb95b661bb750d7802ee5996d8a491be928d
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2017
---
# <a name="kubernetes-dashboard-with-azure-container-service-aks"></a>Kubernetes irányítópult Azure tároló szolgáltatás (AKS)

Az Azure CLI segítségével a Kubernetes irányítópult elindításához. Ez a dokumentum végigvezeti a Kubernetes irányítópult kezdődően az Azure parancssori felület, és néhány alapvető irányítópult művelet is végigvezeti. További információ a Kubernetes irányítópult lásd [Kubernetes webes felhasználói felületének irányítópultja](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/). 

## <a name="before-you-begin"></a>Előkészületek

A dokumentumban foglalt lépések feltételezik, hogy korábban már létrehozott egy AKS-fürtöt, és kiépített egy kubectl-kapcsolatot a fürttel. Ha ezeket még létre kell hoznia olvassa el az [AKS gyors útmutatóját](./kubernetes-walkthrough.md).

Emellett az Azure CLI 2.0.20-as vagy újabb, telepített és konfigurált verziójával is rendelkeznie kell. A verzió megkereséséhez futtassa a következő parancsot: az --version. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

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
> [Kubernetes webes felhasználói felületének irányítópultja](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)