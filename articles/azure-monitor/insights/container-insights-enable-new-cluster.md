---
title: Egy új Azure Kubernetes Service (AKS)-fürt megfigyelése |} A Microsoft Docs
description: Ismerje meg, hogyan engedélyezze a monitorozást az új Azure Kubernetes Service (AKS)-fürt az Azure Monitor szolgáltatással tárolók az előfizetéshez.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: magoedte
ms.openlocfilehash: d73ab2d5cca4f20f954a0b0e972111d3f395c3c8
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "65077530"
---
# <a name="enable-monitoring-of-a-new-azure-kubernetes-service-aks-cluster"></a>Egy új Azure Kubernetes Service (AKS)-fürt figyelés engedélyezése

Ez a cikk bemutatja, hogyan állítható be az Azure Monitor for containers szolgáltatásban felügyelt lévő üzemeltetett Kubernetes-fürt monitorozásához [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/) , amelyek egyesítésére készül az előfizetésében üzembe helyezéséhez.

Engedélyezheti a figyelését egy AKS-fürtöt, a támogatott módszerek egyikének használatával:

* Azure CLI
* Terraform

## <a name="enable-using-azure-cli"></a>Engedélyezze az Azure CLI használatával

Egy új Azure CLI használatával létrehozott AKS-fürt figyelése engedélyezéséhez kövesse a lépés a rövid útmutató a cikkben a szakaszában [létrehozása AKS-fürt](../../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Ha az Azure CLI-vel, akkor először helyi telepítése és használata a parancssori felület. Az Azure CLI 2.0.59 verzióját kell futtatnia vagy újabb. A verzió azonosításához futtassa `az --version`. Ha telepíteni vagy frissíteni szeretné az Azure CLI, lásd: kell [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli). 
>

## <a name="enable-using-terraform"></a>Engedélyezze a Terraform használatával

Ha Ön [Terraform használatával új AKS-fürt üzembe helyezése](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md), az argumentumok megadása kötelező a profilban megadott [Log Analytics-munkaterület létrehozása](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html) Ha nem úgy döntött, hogy adjon meg egy meglévőt. 

>[!NOTE]
>A Terraform használata mellett dönt, akkor kell futnia a Terraform az Azure erőforrás-kezelő szolgáltató verziója 1.17.0 vagy újabb.

Az Azure Monitor-tárolókhoz hozzá a munkaterülethez, lásd: [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) , és fejezze be a profil többek között a [ **addon_profile** ](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) , és adja meg **oms_agent**. 

Miután engedélyezte a figyelés, és minden konfigurációs feladat sikeresen befejeződött, a teljesítmény, a fürt két módon figyelheti:

* Az AKS-fürtöt kiválasztásával közvetlenül a **egészségügyi** a bal oldali panelen.
* Válassza a **figyelő Container insights** az AKS-fürt lapján a kijelölt fürt csempéjén. Az Azure Monitor, a bal oldali ablaktáblán válassza **egészségügyi**. 

  ![Válassza ki az Azure Monitor az aks-ben tárolók lehetőségei](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

Miután engedélyezte a figyelés, a fürt mérőszámok megtekintéséhez nagyjából 15 percet igénybe vehet. 

## <a name="verify-agent-and-solution-deployment"></a>Az ügynök és a megoldás a telepítés ellenőrzése
Az ügynök verziója *06072018* vagy később ellenőrizheti, hogy az ügynök és a megoldás is sikeresen lettek telepítve. Az ügynök korábbi verzióival csak az ügynök telepítésének ellenőrzéséhez.

### <a name="agent-version-06072018-or-later"></a>Az ügynök 06072018 vagy újabb verziójára
A következő paranccsal ellenőrizheti, hogy az ügynök telepítése sikeresen megtörtént. 

```
kubectl get ds omsagent --namespace=kube-system
```

A kimenet a következő, amely azt jelzi, hogy megfelelően lett-e telepítve kell hasonlítania:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

A megoldás üzembe helyezése ellenőrzéséhez futtassa a következő parancsot:

```
kubectl get deployment omsagent-rs -n=kube-system
```

A kimenet a következő, amely azt jelzi, hogy megfelelően lett-e telepítve kell hasonlítania:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>06072018-nél korábbi ügynökverzió

Ellenőrizze, hogy a Log Analytics-ügynök verziója előtt kiadott *06072018* megfelelően üzemel, a következő parancsot:  

```
kubectl get ds omsagent --namespace=kube-system
```

A kimenet a következő, amely azt jelzi, hogy megfelelően lett-e telepítve kell hasonlítania:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Konfiguráció megtekintése a CLI-vel
Használja a `aks show` paranccsal beolvasása – részletek ilyen Ez a megoldás engedélyezve van-e, mi az a Log Analytics munkaterület erőforrás-azonosító és az összefoglaló a fürttel kapcsolatos információk.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Néhány perc múlva a parancs befejeződik, és a megoldás kapcsolatos adatokat JSON formátumban adja vissza.  A parancs eredményét a monitorozási bővítményt profilt kell megjelennie, és hasonlít az alábbi példa kimenetében:

```
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="next-steps"></a>További lépések

* Ha a megoldás való felvétele közben problémákat tapasztal, tekintse át a [hibaelhárítási útmutató](container-insights-troubleshoot.md)

* A figyelés engedélyezve van az AKS-fürt csomópontok és a podok mérőszámok rögzítéséhez, ezek mérőszámok az Azure Portalon érhető el. Tárolók az Azure Monitor használatával kapcsolatban lásd: [megtekintése az Azure Kubernetes Service health](container-insights-analyze.md).
