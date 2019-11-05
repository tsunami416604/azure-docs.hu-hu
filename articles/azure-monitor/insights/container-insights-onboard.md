---
title: A tárolók Azure Monitorának engedélyezése | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan engedélyezheti és konfigurálhatja a tárolók Azure Monitorét, hogy megtudja, hogyan hajtja végre a tárolót, és hogy milyen teljesítménnyel kapcsolatos problémákat észlelt a rendszer.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: dd58ec08c6ec372cf53a79b75162748cfe336b23
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73477127"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Azure Monitor engedélyezése tárolók számára

Ez a cikk áttekintést nyújt a Kubernetes környezetekben üzembe helyezett és az [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/)-ben üzemeltetett munkaterhelések teljesítményének figyelésére szolgáló tárolók Azure monitor telepítésének lehetőségeiről [Azure stack ](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)vagy a helyszínen üzembe helyezett Kubernetes.

A tárolók Azure Monitor a következő támogatott módszerek használatával engedélyezhető az új, illetve egy vagy több AK-beli üzemelő példányhoz:

* A Azure Portal, Azure PowerShell vagy az Azure CLI-vel
* A [Terraform és az AK](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md) használata

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a következőkkel:

* **Log Analytics munkaterület.**

    A tárolók Azure Monitor Log Analytics munkaterületet támogatnak az Azure [Products By Region régióban](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)felsorolt régiókban.

    Létrehozhat egy munkaterületet, amikor engedélyezi az új AK-fürt figyelését, vagy lehetővé teszi, hogy a bevezetési élmény hozzon létre egy alapértelmezett munkaterületet az AK-fürt előfizetésének alapértelmezett erőforrás-csoportjában. Ha úgy dönt, hogy saját maga hozza létre, akkor a [Azure Resource Manageron](../platform/template-workspace-configuration.md)keresztül, a [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)-lel vagy a [Azure Portal](../learn/quick-create-workspace.md). Az alapértelmezett munkaterülethez használt támogatott leképezési párok listáját a következő témakörben tekintheti meg: [régió leképezése Azure monitor for containers](container-insights-region-mapping.md).

* Tagja a **log Analytics közreműködő szerepkörnek** a tárolók figyelésének engedélyezéséhez. A Log Analytics munkaterület elérésének szabályozásáról a [munkaterületek kezelése](../platform/manage-access.md)című témakörben olvashat bővebben.

* Ön a **[tulajdonos](../../role-based-access-control/built-in-roles.md#owner)** szerepkör tagja az AK-fürt erőforrásán.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

* A Prometheus-metrikákat a rendszer alapértelmezés szerint nem gyűjti. Mielőtt [konfigurálja az ügynököt](container-insights-prometheus-integration.md) a gyűjtéshez, fontos, hogy áttekintse a Prometheus [dokumentációját](https://prometheus.io/) , hogy megtudja, mit határozhat meg.

## <a name="network-firewall-requirements"></a>Hálózati tűzfalra vonatkozó követelmények

A következő táblázatban található információk a tároló-ügynök által a tárolók Azure Monitor való kommunikációhoz szükséges proxy-és tűzfal-konfigurációs információkat ismertetik. Az ügynöktől érkező összes hálózati forgalom a Azure Monitor felé irányul.

|Ügynök erőforrása|Portok |
|--------------|------|
| *.ods.opinsights.azure.com | 443 |  
| *.oms.opinsights.azure.com | 443 | 
| *.blob.core.windows.net | 443 |
| dc.services.visualstudio.com | 443 |
| *.microsoftonline.com | 443 |
| *. monitoring.azure.com | 443 |
| login.microsoftonline.com | 443 |

Az alábbi táblázatban található információk az Azure China proxy-és tűzfal-konfigurációs információit ismertetik.

|Ügynök erőforrása|Portok |Leírás | 
|--------------|------|-------------|
| *. ods.opinsights.azure.cn | 443 | Adatfeldolgozás |
| *. oms.opinsights.azure.cn | 443 | OMS bevezetése |
| *.blob.core.windows.net | 443 | A kimenő kapcsolatok figyelésére szolgál. |
| microsoft.com | 80 | Hálózati kapcsolathoz használatos. Erre csak akkor van szükség, ha az ügynök rendszerképének verziója ciprod09262019 vagy korábbi. |
| dc.services.visualstudio.com | 443 | Az Azure Public Cloud Application Insightst használó ügynök telemetria. |

Az alábbi táblázatban található információk az Azure US government proxy-és tűzfal-konfigurációs információit ismertetik.

|Ügynök erőforrása|Portok |Leírás | 
|--------------|------|-------------|
| *. ods.opinsights.azure.us | 443 | Adatfeldolgozás |
| *. oms.opinsights.azure.us | 443 | OMS bevezetése |
| *.blob.core.windows.net | 443 | A kimenő kapcsolatok figyelésére szolgál. |
| microsoft.com | 80 | Hálózati kapcsolathoz használatos. Erre csak akkor van szükség, ha az ügynök rendszerképének verziója ciprod09262019 vagy korábbi. |
| dc.services.visualstudio.com | 443 | Az Azure Public Cloud Application Insightst használó ügynök telemetria. |

## <a name="components"></a>Összetevők

A teljesítmény figyelésének lehetősége a tárolók számára kifejezetten a Azure Monitor a tárolók számára kifejlesztett, Log Analytics ügynökön alapul. Ez a speciális ügynök a fürt összes csomópontjának teljesítményét és eseményeit gyűjti, és az üzembe helyezés során a rendszer automatikusan telepíti és regisztrálja a megadott Log Analytics munkaterületet. Az ügynök verziója Microsoft/OMS: ciprod04202018 vagy újabb, és a következő formátumban egy dátum jelöli: *mmddyyyy*.

>[!NOTE]
>Az AK-hoz készült Windows Server-támogatás előzetes kiadásában a Windows Server-csomópontokkal rendelkező AK-fürthöz nincs telepítve ügynök az adatok összegyűjtésére és a Azure Monitor való továbbítására. Ehelyett a normál telepítés részeként automatikusan a fürtben üzembe helyezett Linux-csomópont gyűjti és továbbítja az adatokat Azure Monitor a fürt összes Windows-csomópontjának nevében.  
>

Az ügynök új verziójának felszabadításakor a rendszer automatikusan frissíti az Azure Kubernetes szolgáltatásban (ak) üzemeltetett felügyelt Kubernetes-fürtökön. A kiadott verziók követéséhez tekintse meg az [ügynök kiadási hirdetményei](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)című témakört.

>[!NOTE]
>Ha már telepített egy AK-fürtöt, akkor az Azure CLI vagy egy megadott Azure Resource Manager sablon használatával engedélyezheti a figyelést, ahogyan azt a jelen cikk későbbi részében is mutatja. A `kubectl` nem használható az ügynök frissítésére, törlésére, ismételt telepítésére vagy üzembe helyezésére.
>A sablont a fürttel azonos erőforráscsoporthoz kell telepíteni.

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
| | [Hibrid Kubernetes-fürt engedélyezése](container-insights-hybrid-setup.md) | Engedélyezheti Azure Stack vagy helyszíni Kubernetes üzemeltetett AK-motor figyelését. |

## <a name="next-steps"></a>További lépések

* Ha a figyelés engedélyezve van az AK-fürtcsomópontok és-hüvelyek állapot-metrikáinak rögzítéséhez, ezek az állapot-mérőszámok a Azure Portal érhetők el. Az Azure Monitor for containers használatának megismeréséhez tekintse meg az [Azure Kubernetes szolgáltatás állapotának megtekintése](container-insights-analyze.md)című témakört.
