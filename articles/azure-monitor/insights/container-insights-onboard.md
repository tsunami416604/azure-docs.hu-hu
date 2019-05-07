---
title: Hogyan előkészítése az Azure Monitor-tárolókhoz |} A Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan fogunk előkészíteni és az Azure Monitor konfigurálása tárolókhoz, így az képes megérteni, hogyan működik-e a tároló és a teljesítménnyel kapcsolatos problémákat azonosítottuk.
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
ms.openlocfilehash: 10b80a9749c5698195ac5d3493ac3b07fd6e24e1
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65073310"
---
# <a name="how-to-onboard-azure-monitor-for-containers"></a>Hogyan előkészítése az Azure Monitor for containers szolgáltatásban  

Ez a cikk áttekintést állíthatja be az Azure Monitor for containers szolgáltatásban üzembe helyezett Kubernetes-környezetben és lévő üzemeltetett számítási feladatok teljesítményének figyelése rendelkezésre álló beállítások [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/).

Az Azure Monitor for containers szolgáltatásban engedélyezhető az új vagy egy vagy több meglévő telepítéseit az AKS használatával a következő támogatott módszerek:

* Az Azure Portalon, az Azure PowerShell vagy az Azure CLI-vel
* Használatával [Terraform és az AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek 
Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik az alábbiakkal:

- **Log Analytics-munkaterületet.** Létrehozhat, engedélyezze a monitorozást az új AKS-fürt, vagy lehetővé teszik az előkészítési folyamatot, hozzon létre egy alapértelmezett munkaterületet az AKS-fürt előfizetés alapértelmezett az erőforráscsoportban. Ha úgy döntött, hogy saját maga létrehozni, azt a létrehozhat [Azure Resource Manager](../platform/template-workspace-configuration.md)segítségével, [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json), vagy a [az Azure portal](../learn/quick-create-workspace.md).
- Ön a tagja, a **Log Analytics közreműködő szerepkör** engedélyezéséhez a tárolók figyelését. A Log Analytics-munkaterülethez való hozzáférésének kapcsolatos további információkért lásd: [munkaterületeinek kezeléséhez](../platform/manage-access.md).
- Ön a tagja, a **[tulajdonosa](../../role-based-access-control/built-in-roles.md#owner)** az AKS-fürt erőforrás szerepkört. 

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

## <a name="components"></a>Összetevők 

A teljesítmény monitorozását teszi lehetővé a tárolóalapú Log Analytics-ügynököket a Linux-tárolókhoz az Azure Monitor kifejezetten fejlesztett támaszkodik. A speciális ügynök teljesítmény- és esemény-adatokat gyűjt a fürt összes csomópontján, és az ügynök automatikus telepítése és regisztrálása a megadott Log Analytics-munkaterület az üzembe helyezés során. Az ügynök verziószáma microsoft / oms:ciprod04202018 vagy újabb, és a egy dátumot a következő formátumban által jelölt: *mmddyyyy*. 

>[!NOTE]
>A Windows Server támogatása az AKS előzetes verziója egy AKS-fürtöt a Windows Server-csomópontok nem rendelkezik az adatok gyűjtéséhez és az Azure Monitor továbbítja telepített ügynök. Ehelyett a normál telepítésének részeként automatikusan a fürtben telepített Linux csomópont gyűjt, és továbbítja az adatokat az Azure monitornak nevében a fürt összes csomópontján Windows.  
>

Az ügynök új verziójának kiadásakor, a rendszer automatikusan frissíti a felügyelt Azure Kubernetes Service (AKS) az üzemeltetett Kubernetes fürtökön. Tekintse kiadott verziói követéséhez [ügynök közleményekért](https://github.com/microsoft/docker-provider/tree/ci_feature_prod). 

>[!NOTE] 
>Ha már telepített egy AKS-fürtöt, akkor engedélyeznie figyelése Azure CLI vagy a megadott Azure Resource Manager-sablon használatával, ahogyan az a cikk későbbi részében is. Nem használhat `kubectl` frissítése, törlése, telepítse újra vagy telepítheti az ügynököt. A sablonhoz telepíteni szeretné ugyanabban az erőforráscsoportban a fürttel.

Az Azure Monitor for containers szolgáltatásban, a következő táblázat ismerteti a következő módszerek egyikével engedélyeznie.

| Üzembe helyezés állapota | Módszer | Leírás | 
|------------------|--------|-------------| 
| Új AKS-fürt | [Azure CLI-vel fürt létrehozása](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Engedélyezheti egy új AKS-fürt Azure CLI-vel létrehozott figyelését. | 
| | [A Terraform használatával fürt létrehozása](container-insights-enable-new-cluster.md#enable-using-terraform)| Engedélyezheti egy új a nyílt forráskódú eszköz Terraform használatával létrehozott AKS-fürt figyelése. | 
| AKS-fürt | [Engedélyezze az Azure CLI használatával](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Engedélyezheti, hogy már üzembe helyezte az Azure CLI-vel egy AKS-fürt figyelése. | 
| |[Engedélyezze a Terraform használatával](container-insights-enable-existing-clusters.md#enable-using-terraform) | Engedélyezheti, hogy már üzembe helyezte a nyílt forráskódú eszköz Terraform használatával egy AKS-fürt figyelése. | 
| | [Az Azure Monitor engedélyezése](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Figyelés legalább egy AKS-fürtök már üzembe helyezte az Azure Monitor az AKS több fürt lapján engedélyezheti. | 
| | [Az AKS-fürt engedélyezése](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Engedélyezheti, hogy közvetlenül egy AKS-fürtöt az Azure Portalon a figyelést. | 
| | [Engedélyezze az Azure Resource Manager-sablon használatával](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Engedélyezheti egy AKS-fürtöt egy előre konfigurált Azure Resource Manager-sablon figyelését. | 

## <a name="next-steps"></a>További lépések

* A figyelés engedélyezve van az AKS-fürt csomópontok és a podok mérőszámok rögzítéséhez, ezek mérőszámok az Azure Portalon érhető el. Tárolók az Azure Monitor használatával kapcsolatban lásd: [megtekintése az Azure Kubernetes Service health](container-insights-analyze.md).
