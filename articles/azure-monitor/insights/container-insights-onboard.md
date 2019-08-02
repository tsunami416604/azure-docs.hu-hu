---
title: A tárolók Azure Monitorának engedélyezése | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan engedélyezheti és konfigurálhatja a tárolók Azure Monitorét, hogy megtudja, hogyan hajtja végre a tárolót, és hogy milyen teljesítménnyel kapcsolatos problémákat észlelt a rendszer.
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
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: 25be8f166fec8a311fdc2ed1fa3fca6339185e94
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2019
ms.locfileid: "67867532"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Azure Monitor engedélyezése tárolók számára

Ez a cikk áttekintést nyújt a Kubernetes környezetekben üzembe helyezett és az [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/)-ben üzemeltetett munkaterhelések teljesítményének figyelésére szolgáló tárolók Azure monitor telepítésének lehetőségeiről.

Az Azure Monitor for containers szolgáltatásban engedélyezhető az új vagy egy vagy több meglévő telepítéseit az AKS használatával a következő támogatott módszerek:

* A Azure Portal, Azure PowerShell vagy az Azure CLI-vel
* Használatával [Terraform és az AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek
Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik az alábbiakkal:

* **Log Analytics munkaterület.**

    A tárolók Azure Monitor Log Analytics munkaterületet támogatnak az Azure [Products By Region](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)régióban felsorolt régiókban, kivéve a régiót **US gov Virginia**.

    Létrehozhat egy munkaterületet, amikor engedélyezi az új AK-fürt figyelését, vagy lehetővé teszi, hogy a bevezetési élmény hozzon létre egy alapértelmezett munkaterületet az AK-fürt előfizetésének alapértelmezett erőforrás-csoportjában. Ha úgy döntött, hogy saját maga létrehozni, azt a létrehozhat [Azure Resource Manager](../platform/template-workspace-configuration.md)segítségével, [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json), vagy a [az Azure portal](../learn/quick-create-workspace.md). Az alapértelmezett munkaterülethez használt támogatott leképezési párok listáját a következő témakörben tekintheti meg: [régió leképezése Azure monitor for containers](container-insights-region-mapping.md).

* Tagja a **log Analytics közreműködő szerepkörnek** a tárolók figyelésének engedélyezéséhez. A Log Analytics-munkaterülethez való hozzáférésének kapcsolatos további információkért lásd: [munkaterületeinek kezeléséhez](../platform/manage-access.md).

* Ön a **[tulajdonos](../../role-based-access-control/built-in-roles.md#owner)** szerepkör tagja az AK-fürt erőforrásán.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

* A Prometheus-metrikákat a rendszer alapértelmezés szerint nem gyűjti. Mielőtt [konfigurálja az ügynököt](container-insights-agent-config.md) a gyűjtéshez, fontos, hogy áttekintse [](https://prometheus.io/) a Prometheus dokumentációját, hogy megtudja, mit határozhat meg.

## <a name="components"></a>Összetevők

A teljesítmény monitorozását teszi lehetővé a tárolóalapú Log Analytics-ügynököket a Linux-tárolókhoz az Azure Monitor kifejezetten fejlesztett támaszkodik. A speciális ügynök teljesítmény- és esemény-adatokat gyűjt a fürt összes csomópontján, és az ügynök automatikus telepítése és regisztrálása a megadott Log Analytics-munkaterület az üzembe helyezés során. Az ügynök verziószáma microsoft / oms:ciprod04202018 vagy újabb, és a egy dátumot a következő formátumban által jelölt: *mmddyyyy*.

>[!NOTE]
>Az AK-hoz készült Windows Server-támogatás előzetes kiadásában a Windows Server-csomópontokkal rendelkező AK-fürthöz nincs telepítve ügynök az adatok összegyűjtésére és a Azure Monitor való továbbítására. Ehelyett a normál telepítés részeként automatikusan a fürtben üzembe helyezett Linux-csomópont gyűjti és továbbítja az adatokat Azure Monitor a fürt összes Windows-csomópontjának nevében.  
>

Az ügynök új verziójának kiadásakor, a rendszer automatikusan frissíti a felügyelt Azure Kubernetes Service (AKS) az üzemeltetett Kubernetes fürtökön. Tekintse kiadott verziói követéséhez [ügynök közleményekért](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

>[!NOTE]
>Ha már telepített egy AKS-fürtöt, akkor engedélyeznie figyelése Azure CLI vagy a megadott Azure Resource Manager-sablon használatával, ahogyan az a cikk későbbi részében is. Nem használhat `kubectl` frissítése, törlése, telepítse újra vagy telepítheti az ügynököt.
>A sablonhoz telepíteni szeretné ugyanabban az erőforráscsoportban a fürttel.

A tárolók Azure Monitor a következő táblázatban leírt módszerek egyikével engedélyezheti.

| Központi telepítés állapota | Módszer | Leírás |
|------------------|--------|-------------|
| Új AK-fürt | [Fürt létrehozása az Azure CLI-vel](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Engedélyezheti az Azure CLI-vel létrehozott új AK-fürtök figyelését. |
| | [Fürt létrehozása a Terraform használatával](container-insights-enable-new-cluster.md#enable-using-terraform)| A nyílt forráskódú eszköz Terraform használatával engedélyezheti a létrehozott új AK-fürtök figyelését. |
| Meglévő AK-fürt | [Engedélyezés az Azure CLI használatával](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Az Azure CLI használatával már üzembe helyezett AK-fürtök figyelését is engedélyezheti. |
| |[Engedélyezés a Terraform használatával](container-insights-enable-existing-clusters.md#enable-using-terraform) | A nyílt forráskódú eszköz Terraform használatával engedélyezheti a már üzembe helyezett AK-fürtök figyelését. |
| | [Engedélyezés Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| A Azure Monitorban engedélyezheti egy vagy több, az AK többfürtes oldaláról már üzembe helyezett AK-fürtök figyelését. |
| | [Engedélyezés az AK-fürtből](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| A figyelést közvetlenül a Azure Portal AK-fürtjéből is engedélyezheti. |
| | [Engedélyezés Azure Resource Manager sablon használatával](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Egy AK-fürt figyelését előre konfigurált Azure Resource Manager sablonnal is engedélyezheti. |

## <a name="next-steps"></a>További lépések

* A figyelés engedélyezve van az AKS-fürt csomópontok és a podok mérőszámok rögzítéséhez, ezek mérőszámok az Azure Portalon érhető el. Tárolók az Azure Monitor használatával kapcsolatban lásd: [megtekintése az Azure Kubernetes Service health](container-insights-analyze.md).
