---
title: Új Azure Kubernetes-szolgáltatás (AKS) fürt figyelése | Microsoft dokumentumok
description: Megtudhatja, hogyan engedélyezheti egy új Azure Kubernetes-szolgáltatás (AKS) fürt figyelését az Azure Monitor tárolók előfizetéséhez.
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: c731826f2780c45358730f9ce20d6a6151f6f259
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275437"
---
# <a name="enable-monitoring-of-a-new-azure-kubernetes-service-aks-cluster"></a>Új Azure Kubernetes-szolgáltatás (AKS) fürt figyelésének engedélyezése

Ez a cikk ismerteti, hogyan állíthatja be az Azure Monitor tárolók az [Azure Kubernetes-szolgáltatásüzemeltetésben](https://docs.microsoft.com/azure/aks/) üzemeltetett felügyelt Kubernetes-fürt, amely az előfizetésben üzembe helyezésre készül.

Az AKS-fürtök figyelését a támogatott módszerek egyikével engedélyezheti:

* Azure CLI
* Terraform

## <a name="enable-using-azure-cli"></a>Engedélyezés az Azure CLI használatával

Az Azure CLI-vel létrehozott új AKS-fürt figyelésének engedélyezéséhez kövesse az [AKS-fürt létrehozása](../../aks/kubernetes-walkthrough.md#create-aks-cluster)című szakasz rövid útmutatójában található lépést.  

>[!NOTE]
>Ha úgy dönt, hogy az Azure CLI, először telepítenie kell, és a CLI helyileg kell használni. Az Azure CLI 2.0.74-es vagy újabb verzióját kell futtatnia. A verzió azonosításához `az --version`futtassa a futtassa a futtassa a futtassa a futtassa Ha telepítenie vagy frissítenie kell az Azure CLI-t, [olvassa el az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli)című témakört. Ha telepítette az aks-preview CLI bővítmény 0.4.12-es vagy újabb verzióját, távolítsa el az előzetes verzió engedélyezéséhez végzett módosításokat, mivel felülírhatja az azure CLI alapértelmezett viselkedését, mivel az AKS előzetes verzió funkciói nem érhetők el az Azure US Governmnet felhőben.

## <a name="enable-using-terraform"></a>Engedélyezés a Terraform használatával

Ha [egy új AKS-fürtet telepít a Terraform használatával,](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)akkor adja meg a profilban a [Log Analytics-munkaterület létrehozásához](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html) szükséges argumentumokat, ha nem dönt úgy, hogy egy meglévőt ad meg. 

>[!NOTE]
>Ha úgy dönt, hogy a Terraform, futtatnia kell a Terraform Azure RM Provider 1.17.0-s vagy újabb verziója.

Ha hozzá szeretné adni az Azure Monitort a tárolókhoz a munkaterülethez, tekintse meg [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) és töltse ki a profilt a [**addon_profile**](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) hozzáadásával, és adja meg a **oms_agent.** 

Miután engedélyezte a figyelést és az összes konfigurációs feladat sikeres befejezését, kétféleképpen figyelheti a fürt teljesítményét:

* Közvetlenül az AKS-fürtben a bal oldali ablaktáblában az **Állapot** lehetőséget választva.
* A **Tárolóelemzésfigyelés csempe figyelése** az AKS-fürtlapon a kijelölt fürthöz. Az Azure Monitor bal oldali ablaktáblájában válassza az **Állapot**lehetőséget. 

  ![Az AKS-tárolók Azure-figyelőjének kiválasztásának lehetőségei](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

Miután engedélyezte a figyelést, körülbelül 15 percet is igénybe vehet, mielőtt megtekintheti a fürt állapotmetrikákat. 

## <a name="verify-agent-and-solution-deployment"></a>Ügynök- és megoldástelepítés ellenőrzése
A *06072018-as* vagy újabb ügynökverzióval ellenőrizheti, hogy az ügynök és a megoldás is sikeresen üzembe lett-e. Az ügynök korábbi verzióival csak az ügynök üzembe helyezését ellenőrizheti.

### <a name="agent-version-06072018-or-later"></a>Ügynök 06072018-as vagy újabb verziója
Futtassa a következő parancsot az ügynök sikeres telepítésének ellenőrzéséhez. 

```
kubectl get ds omsagent --namespace=kube-system
```

A kimenetnek a következőhöz kell hasonlítania, ami azt jelzi, hogy megfelelően lett telepítve:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

A megoldás telepítésének ellenőrzéséhez futtassa a következő parancsot:

```
kubectl get deployment omsagent-rs -n=kube-system
```

A kimenetnek a következőhöz kell hasonlítania, ami azt jelzi, hogy megfelelően lett telepítve:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>A 06072018-nál korábbi ügynökverzió

A *06072018* előtt kiadott Log Analytics-ügynök-verzió megfelelő telepítésének ellenőrzéséhez futtassa a következő parancsot:  

```
kubectl get ds omsagent --namespace=kube-system
```

A kimenetnek a következőhöz kell hasonlítania, ami azt jelzi, hogy megfelelően lett telepítve:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Konfiguráció megtekintése CLI-vel
A `aks show` parancs segítségével olyan részleteket kaphat, mint például a megoldás engedélyezve van-e vagy sem, mi a Log Analytics munkaterület-erőforrásazonosítója, és összegző részleteket a fürtről.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Néhány perc múlva a parancs befejezi és visszaadja a JSON-formátumú információkat a megoldásról.  A parancs eredményeinek meg kell mutatniuk a figyelési bővítmény profilt, és a következő példakimenetre hasonlítanak:

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

* Ha a megoldás bedeszkázása közben problémákat tapasztal, tekintse át a [hibaelhárítási útmutatót](container-insights-troubleshoot.md)

* Ha a figyelés engedélyezve van az AKS-fürt és a rajtuk futó számítási feladatok állapotának és erőforrás-kihasználtságának összegyűjtéséhez, ismerje meg, [hogyan használhatja az](container-insights-analyze.md) Azure Monitort tárolókhoz.
