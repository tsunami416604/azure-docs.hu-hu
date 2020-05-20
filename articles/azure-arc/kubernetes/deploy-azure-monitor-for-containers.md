---
title: Azure-ív előkészítése Azure Monitor tárolók számára (előzetes verzió)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure-ív előkészítése Azure Monitor tárolók számára
keywords: Kubernetes, arc, Azure, K8s, tárolók
ms.openlocfilehash: 3e1ea96a9241211b25a4e5b356723290fa647412
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680746"
---
# <a name="onboard-azure-monitor-for-containers-with-arc-preview"></a>Azure Monitor az arc (előzetes verzió) tárolók számára

[Azure monitor engedélyezett tárolók](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview) üzembe helyezése az Azure arc-kompatibilis Kubernetes-fürtökön.

## <a name="before-you-begin"></a>Előkészületek

* [Kubernetes-verziók](https://docs.microsoft.com/azure/aks/supported-kubernetes-versions)
* Linux-disztribúciók a fürthöz (Master & Worker) csomópontok – Ubuntu (18,04 LTS és 16,04 LTS)
* Az Azure arc-kompatibilis Kubernetes-fürt Azure-előfizetéséhez tartozó minimális közreműködői RBAC szerepkör engedélyezése
* Az Azure arc-kompatibilis Kubernetes-fürt teljes Azure-erőforrás-azonosítója
* A Kubernetes-fürt Kubeconfig-környezete
* A figyelési ügynöknek cAdvisor van szüksége a Kubelet a következő biztonságos porton: 10250 vagy nem biztonságos port: 10255 a minden csomóponton, hogy lekérje a Teljesítményfigyelő metrikáit   
* Azt javasoljuk, hogy a Kubelet cAdvisor-portot a biztonságos portra konfigurálja: 10250.
* A figyelési ügynöknek a következő kimenő portokra és tartományokra van szüksége ahhoz, hogy a figyelési adatforrást a Azure Monitor háttérbe küldje (ha a proxy/tűzfal blokkolja)
    -  *. ods.opinsights.azure.com 443
    -  *. oms.opinsights.azure.com 443
    -  *. blob.core.windows.net 443
    -  dc.services.visualstudio.com 443

## <a name="onboarding"></a>Előkészítés

### <a name="using-helm-chart"></a>A HELM diagram használata

### <a name="option-1-using-powershell--script"></a>1. lehetőség: PowerShell-szkript használata

1. A bevezetési parancsfájl letöltése

    ```console
    curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/haiku/onboarding_azuremonitor_for_containers.ps1
     ```

2. Telepítse a [PowerShell Core](https://docs.microsoft.com/PowerShell/scripting/install/installing-PowerShell?view=PowerShell-6) -t a fejlesztői gépen a PowerShell bevezetési parancsfájljának végrehajtásához.

3. Bejelentkezés az Azure-ba

    ```console
    az login --use-device-code
    ```

4. Futtassa az alábbi szkriptet a fürt Azure arc K8s-fürt ResourceId és a kubernetes-fürt környezetében

    ```console
    .\onboarding_azuremonitor_for_containers.ps1 -azureArcClusterResourceId <resourcedIdOfAzureArcCluster> -kubeContext <kube-context>

    For Example ..
    .\onboarding_azuremonitor_for_containers.ps1 -azureArcClusterResourceId /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1 -kubeContext MyK8sTestCluster
     ```

### <a name="option-2-using-bash-script"></a>2. lehetőség: bash-szkript használata

> **Tipp:** A szkript bash 4 funkciót használ, ezért győződjön meg róla, hogy a bash naprakész. A jelenlegi verzióját a segítségével tekintheti meg `bash --version` .

1. A bevezetési parancsfájl letöltése

    ```console
    curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/haiku/onboarding_azuremonitor_for_containers.sh
     ```

2. Futtassa az alábbi szkriptet a fürt Azure arc K8s-fürt ResourceId és a kubernetes-fürt környezetében

    ```console
    bash onboarding_azuremonitor_for_containers.sh <resourcedIdOfAzureArcCluster>  <kube-context>

    For Example:
    bash onboarding_azuremonitor_for_containers.sh /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1 MyK8sTestCluster

     ```

## <a name="configure-agent-data-collection"></a>Az ügynök adatgyűjtésének konfigurálása

Alapértelmezés szerint az ügynök nem gyűjti az stdout és a stderr naplóit a Kube-System névtérben.
Tekintse meg az https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-agent-config ügynök konfigurálását a kívánt adatgyűjtési beállításokkal.

## <a name="configure-scraping-of-prometheus-metrics"></a>A Prometheus-metrikák selejtezésének konfigurálása

A tárolók Azure Monitor a Prometheus-metrikákat, és a Azure Monitor háttérbe kerülnek.
A https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-prometheus-integration Prometheus-lekaparás konfigurálására vonatkozó utasításokért tekintse meg a következő témakört:.

## <a name="user-interface"></a>Felhasználói felület

Navigáljon a elemre https://aka.ms/azmon-containers-azurearc , és tekintse meg a beépített fürtöt.

## <a name="disable-monitoring"></a>Figyelés letiltása

Ha valamilyen okból szeretné letiltani a figyelést, egyszerűen törölheti az Azure Monitor for containers HELM diagramot, hogy leállítsa a figyelési adatok gyűjtését és betöltését a tárolók háttérbeli Azure Monitor.

    ```console
    helm del azmon-containers-release-1
    ```

## <a name="next-steps"></a>További lépések

* [A fürt konfigurációjának szabályozása Azure Policy használatával](./use-azure-policy.md)

