---
title: A tárolók Azure Monitorának engedélyezése | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan engedélyezheti és konfigurálhatja a tárolók Azure Monitorét, hogy megtudja, hogyan hajtja végre a tárolót, és hogy milyen teljesítménnyel kapcsolatos problémákat észlelt a rendszer.
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: e17a346418bc5d38470168339f2078a0a187fe4e
ms.sourcegitcommit: 61d92af1d24510c0cc80afb1aebdc46180997c69
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2020
ms.locfileid: "85338258"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Azure Monitor engedélyezése tárolók számára

Ez a cikk áttekintést nyújt a Kubernetes környezetekben üzembe helyezett munkaterhelések teljesítményének figyeléséhez Azure Monitor a tárolók számára, valamint a következő helyeken:

- [Azure Kubernetes szolgáltatás](https://docs.microsoft.com/azure/aks/) (ak)

- Az Azure-ban üzemeltetett, önfelügyelt Kubernetes-fürtök az [AK motor](https://github.com/Azure/aks-engine)használatával.

- A [Azure stackon](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910) vagy a helyszínen üzemeltetett, az AK-motorral rendelkező, önállóan felügyelt Kubernetes-fürtök.

- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) 3. x és 4. x verzió

- [Red Hat OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) 4. x verzió

- [Arc-kompatibilis Kubernetes-fürt](../../azure-arc/kubernetes/overview.md)

A tárolók Azure Monitor a következő támogatott módszerek használatával engedélyezhető az új, illetve egy vagy több Kubernetes üzemelő példányhoz:

- A Azure Portal, Azure PowerShell vagy az Azure CLI-vel

- A [Terraform és az AK](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md) használata

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a következőkkel:

- **Egy Log Analytics-munkaterület.**

   A tárolók Azure Monitor Log Analytics munkaterületet támogatnak az Azure [Products By Region régióban](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)felsorolt régiókban.

   Létrehozhat egy munkaterületet, amikor engedélyezi az új AK-fürt figyelését, vagy lehetővé teszi, hogy a bevezetési élmény hozzon létre egy alapértelmezett munkaterületet az AK-fürt előfizetésének alapértelmezett erőforrás-csoportjában. Ha úgy dönt, hogy saját maga hozza létre, akkor a [Azure Resource Manageron](../platform/template-workspace-configuration.md)keresztül, a [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)-lel vagy a [Azure Portal](../learn/quick-create-workspace.md). Az alapértelmezett munkaterülethez használt támogatott leképezési párok listáját a következő témakörben tekintheti meg: [régió leképezése Azure monitor for containers](container-insights-region-mapping.md).

- Tagja a **log Analytics közreműködő szerepkörnek** a tárolók figyelésének engedélyezéséhez. A Log Analytics munkaterület elérésének szabályozásáról a [munkaterületek kezelése](../platform/manage-access.md)című témakörben olvashat bővebben.

- Ön a **[tulajdonos](../../role-based-access-control/built-in-roles.md#owner)** szerepkör tagja az AK-fürt erőforrásán.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

- A Prometheus-metrikákat a rendszer alapértelmezés szerint nem gyűjti. Mielőtt [konfigurálja az ügynököt](container-insights-prometheus-integration.md) a gyűjtéshez, fontos, hogy tekintse át a Prometheus [dokumentációját](https://prometheus.io/) , és Ismerje meg, hogy milyen lehet a selejt, és hogyan támogatott a módszer.

## <a name="supported-configurations"></a>Támogatott konfigurációk

A következőt hivatalosan támogatja a tárolók Azure Monitor.

- Környezetek: az Azure Red Hat OpenShift, a helyszíni Kubernetes, valamint az Azure-beli és a Azure Stack-es AK-motor. További információ: az [AK-motor Azure stackon](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
- A Kubernetes és a támogatási szabályzat verziói ugyanazok, mint a [támogatott AK](../../aks/supported-kubernetes-versions.md)-verziók.

## <a name="network-firewall-requirements"></a>Hálózati tűzfalra vonatkozó követelmények

A következő táblázatban található információk a tároló-ügynök által a tárolók Azure Monitor való kommunikációhoz szükséges proxy-és tűzfal-konfigurációs információkat ismertetik. Az ügynöktől érkező összes hálózati forgalom a Azure Monitor felé irányul.

|Ügynök erőforrása|Portok |
|--------------|------|
| `*.ods.opinsights.azure.com` | 443 |
| `*.oms.opinsights.azure.com` | 443 |
| `dc.services.visualstudio.com` | 443 |
| `*.monitoring.azure.com` | 443 |
| `login.microsoftonline.com` | 443 |

Az alábbi táblázatban található információk az Azure China 21Vianet proxy-és tűzfal-konfigurációs információit ismertetik.

|Ügynök erőforrása|Portok |Leírás |
|--------------|------|-------------|
| `*.ods.opinsights.azure.cn` | 443 | Adatfeldolgozás |
| `*.oms.opinsights.azure.cn` | 443 | OMS bevezetése |
| `dc.services.visualstudio.com` | 443 | Az Azure Public Cloud Application Insightst használó ügynök telemetria. |

Az alábbi táblázatban található információk az Azure US government proxy-és tűzfal-konfigurációs információit ismertetik.

|Ügynök erőforrása|Portok |Leírás |
|--------------|------|-------------|
| `*.ods.opinsights.azure.us` | 443 | Adatfeldolgozás |
| `*.oms.opinsights.azure.us` | 443 | OMS bevezetése |
| `dc.services.visualstudio.com` | 443 | Az Azure Public Cloud Application Insightst használó ügynök telemetria. |

## <a name="components"></a>Összetevők

A teljesítmény figyelésének lehetősége a tárolók számára kifejezetten a Azure Monitor a tárolók számára kifejlesztett, Log Analytics ügynökön alapul. Ez a speciális ügynök a fürt összes csomópontjának teljesítményét és eseményeit gyűjti, és az üzembe helyezés során a rendszer automatikusan telepíti és regisztrálja a megadott Log Analytics munkaterületet. Az ügynök verziója Microsoft/OMS: ciprod04202018 vagy újabb, és a következő formátumban egy dátum jelöli: *mmddyyyy*.

>[!NOTE]
>Az AK-hoz készült Windows Server-támogatás általános elérhetősége esetén a Windows Server-csomópontokkal rendelkező AK-fürtök előnézeti ügynöke daemonset elemet Pod-ként van telepítve minden egyes Windows Server-csomóponton, hogy összegyűjtse a naplókat, és továbbítsa azt Log Analytics. A teljesítmény-mérőszámok esetében a normál telepítés részeként automatikusan a fürtben üzembe helyezett Linux-csomópont gyűjti és továbbítja az adatokat Azure Monitor a fürt összes Windows-csomópontjának nevében.
>

Az ügynök új verziójának felszabadításakor a rendszer automatikusan frissíti az Azure Kubernetes szolgáltatásban (ak) üzemeltetett felügyelt Kubernetes-fürtökön. A kiadott verziók követéséhez tekintse meg az [ügynök kiadási hirdetményei](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)című témakört.

>[!NOTE]
>Ha már telepített egy AK-fürtöt, akkor az Azure CLI vagy egy megadott Azure Resource Manager sablon használatával engedélyezheti a figyelést, ahogyan azt a jelen cikk későbbi részében is mutatja. Nem használható `kubectl` az ügynök frissítésére, törlésére, ismételt telepítésére vagy üzembe helyezésére.
>A sablont a fürttel azonos erőforráscsoporthoz kell telepíteni.

A tárolók Azure Monitor a következő táblázatban leírt módszerek egyikével engedélyezheti.

| Központi telepítés állapota | Metódus | Leírás |
|------------------|--------|-------------|
| Új Kubernetes-fürt | [AK-fürt létrehozása az Azure CLI-vel](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Engedélyezheti az Azure CLI-vel létrehozott új AK-fürtök figyelését. |
| | [AK-fürt létrehozása a Terraform használatával](container-insights-enable-new-cluster.md#enable-using-terraform)| A nyílt forráskódú eszköz Terraform használatával engedélyezheti a létrehozott új AK-fürtök figyelését. |
| | [OpenShift-fürt létrehozása Azure Resource Manager sablon használatával](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | Engedélyezheti egy előre konfigurált Azure Resource Manager sablonnal létrehozott új OpenShift-fürt figyelését. |
| | [OpenShift-fürt létrehozása az Azure CLI-vel](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create) | A figyelést engedélyezheti új OpenShift-fürt Azure CLI-vel történő üzembe helyezése során. |
| Meglévő Kubernetes-fürt | [Az AK-fürt engedélyezése az Azure CLI használatával](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Az Azure CLI használatával már üzembe helyezett AK-fürtök figyelését is engedélyezheti. |
| |[Az AK-fürt engedélyezése a Terraform használatával](container-insights-enable-existing-clusters.md#enable-using-terraform) | A nyílt forráskódú eszköz Terraform használatával engedélyezheti a már üzembe helyezett AK-fürtök figyelését. |
| | [Az AK-fürtök engedélyezése Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Engedélyezheti egy vagy több, a Azure Monitor több fürtből származó, már üzembe helyezett AK-fürtök figyelését. |
| | [Engedélyezés az AK-fürtből](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| A figyelést közvetlenül a Azure Portal AK-fürtjéből is engedélyezheti. |
| | [Az AK-fürtök engedélyezése Azure Resource Manager sablon használatával](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Egy AK-fürt figyelését előre konfigurált Azure Resource Manager sablonnal is engedélyezheti. |
| | [Hibrid Kubernetes-fürt engedélyezése](container-insights-hybrid-setup.md) | Engedélyezheti Azure Stack vagy helyszíni Kubernetes üzemeltetett AK-motor figyelését. |
| | [Engedélyezze az ív használatára képes Kubernetes-fürtöt](container-insights-enable-arc-enabled-clusters.md). | Engedélyezheti az Azure-on kívül üzemeltetett Kubernetes-fürtök figyelését, amelyek engedélyezve vannak az Azure arc használatával. |
| | [OpenShift-fürt engedélyezése Azure Resource Manager sablon használatával](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | Az előre konfigurált Azure Resource Manager sablonnal engedélyezheti egy meglévő OpenShift-fürt figyelését. |
| | [OpenShift-fürt engedélyezése Azure Monitor](container-insights-azure-redhat-setup.md#from-the-azure-portal) | Engedélyezheti egy vagy több, a Azure Monitor több fürtből származó OpenShift-fürt figyelését. |

## <a name="next-steps"></a>További lépések

- Ha engedélyezve van a figyelés, megkezdheti az Azure Kubernetes szolgáltatás (ak), Azure Stack vagy más környezetekben üzemeltetett Kubernetes-fürtök teljesítményének elemzését. A Azure Monitor for containers használatának megismeréséhez tekintse meg a [Kubernetes-fürt teljesítményének megtekintése](container-insights-analyze.md)című témakört.
