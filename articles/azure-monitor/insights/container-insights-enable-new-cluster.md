---
title: Új Azure Kubernetes Service-(ak-) fürt figyelése | Microsoft Docs
description: Megtudhatja, hogyan engedélyezheti a figyelést egy új Azure Kubernetes Service (ak) fürthöz a Azure Monitor for containers előfizetéssel.
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: c731826f2780c45358730f9ce20d6a6151f6f259
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78385477"
---
# <a name="enable-monitoring-of-a-new-azure-kubernetes-service-aks-cluster"></a>Új Azure Kubernetes Service-(ak-) fürt figyelésének engedélyezése

Ez a cikk azt ismerteti, hogyan állítható be Azure Monitor for containers az [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/) -ben üzemeltetett felügyelt Kubernetes-fürt figyelésére, amelyet az előfizetésében üzembe helyezésre készül.

A támogatott módszerek egyikének használatával engedélyezheti az AK-fürtök figyelését:

* Azure CLI
* Terraform

## <a name="enable-using-azure-cli"></a>Engedélyezze az Azure CLI használatával

Az Azure CLI-vel létrehozott új AK-fürt figyelésének engedélyezéséhez kövesse a rövid útmutató című cikk lépéseit az [AK-fürt létrehozása](../../aks/kubernetes-walkthrough.md#create-aks-cluster)című szakaszban.  

>[!NOTE]
>Ha az Azure CLI-vel, akkor először helyi telepítése és használata a parancssori felület. Az Azure CLI 2.0.74 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa `az --version`. Ha telepítenie vagy frissítenie kell az Azure CLI-t, tekintse meg [Az Azure CLI telepítését](https://docs.microsoft.com/cli/azure/install-azure-cli)ismertető témakört. Ha telepítette az AK-előnézet CLI-bővítményének 0.4.12 vagy újabb verzióját, távolítsa el az előnézeti bővítmény engedélyezéséhez szükséges módosításokat, mivel ez felülbírálhatja az alapértelmezett Azure CLI-viselkedést, mivel az AK előzetes verziójának funkciói nem érhetők el az Azure US Governmnet-felhőben.

## <a name="enable-using-terraform"></a>Engedélyezze a Terraform használatával

Ha [új AK-fürtöt helyez üzembe a Terraform használatával](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md), akkor a profilban szükséges argumentumokat kell megadnia [log Analytics munkaterület létrehozásához](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html) , ha a felhasználó nem ad meg egy meglévőt. 

>[!NOTE]
>A Terraform használata mellett dönt, akkor kell futnia a Terraform az Azure erőforrás-kezelő szolgáltató verziója 1.17.0 vagy újabb.

Ha Azure Monitor szeretne hozzáadni a tárolóhoz a munkaterülethez, tekintse meg a [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) és a profil befejezéséhez a [**addon_profile**](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) , majd a **oms_agent**megadása című témakört. 

Miután engedélyezte a figyelés, és minden konfigurációs feladat sikeresen befejeződött, a teljesítmény, a fürt két módon figyelheti:

* Közvetlenül az AK-fürtben válassza az **állapot** lehetőséget a bal oldali ablaktáblán.
* A kiválasztott fürt AK-fürt lapjának **figyelés tároló-bepillantások** csempéjét választva. Azure Monitor a bal oldali ablaktáblán válassza az **állapot**lehetőséget. 

  ![Válassza ki az Azure Monitor az aks-ben tárolók lehetőségei](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

Miután engedélyezte a figyelés, a fürt mérőszámok megtekintéséhez nagyjából 15 percet igénybe vehet. 

## <a name="verify-agent-and-solution-deployment"></a>Az ügynök és a megoldás a telepítés ellenőrzése
Az ügynök *06072018* -es vagy újabb verziójával ellenőrizheti, hogy az ügynök és a megoldás telepítése is sikeres volt-e. Az ügynök korábbi verzióival csak az ügynök telepítésének ellenőrzéséhez.

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

A következő parancs futtatásával ellenőrizheti, hogy a Log Analytics ügynöknek a *06072018* -es verziójának megfelelő telepítését adta-e meg:  

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
A `aks show` parancs használatával olyan részleteket kaphat, mint például a megoldás engedélyezve vagy sem, mi a Log Analytics munkaterület resourceID, valamint a fürt összegző információi.  

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

## <a name="next-steps"></a>Következő lépések

* Ha problémákat tapasztal a megoldás bevezetésére tett kísérlet során, tekintse át a [hibaelhárítási útmutatót](container-insights-troubleshoot.md) .

* Megtudhatja, [hogyan használhatja](container-insights-analyze.md) a Azure monitor a tárolók számára, hogy az AK-fürtök és a rajtuk futó munkaterhelések állapotát és erőforrás-kihasználtságát összegyűjtse.
