---
title: Az Azure Monitor tárolókhoz való engedélyezése | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan engedélyezi és konfigurálja az Azure Monitor tárolók, így megismerheti, hogyan működik a tároló, és milyen teljesítményfüggő problémák at azonosítottak.
ms.topic: conceptual
ms.date: 11/18/2019
ms.openlocfilehash: 7aad7e7dd5ec2569377f9276c2e4793c7afd631a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275307"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Az Azure Monitor tárolókhoz való engedélyezése

Ez a cikk áttekintést nyújt az Azure Monitor tárolók hoz a Kubernetes-környezetekben üzembe helyezett és a kubernetes-környezetekben üzemeltetett számítási feladatok teljesítményének figyeléséhez rendelkezésre álló lehetőségekről:

- [Azure Kubernetes szolgáltatás](https://docs.microsoft.com/azure/aks/) (AKS)

- Az Azure-ban az [AKS-motor](https://github.com/Azure/aks-engine)használatával üzemeltetett, saját felügyelt Kubernetes-fürtök.

- Saját maga által felügyelt Kubernetes-fürtök az [Azure Stacken](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910) vagy a helyszíni AKS-motor használatával üzemeltetett fürtök.

- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)

Az Azure Monitor tárolók engedélyezhető az új, vagy egy vagy több meglévő központi telepítések Kubernetes a következő támogatott módszerekkel:

- Az Azure Portalon, az Azure PowerShellben vagy az Azure CLI-vel

- A [Terraform és az AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md) használata

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy a következőket kapta:

- **Egy Log Analytics-munkaterület.**

    Az Azure Monitor tárolók támogatja a Log Analytics munkaterületet a felsorolt régiókban az [Azure-termékek régiónként.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)

    Létrehozhat egy munkaterületet, ha engedélyezi az új AKS-fürt figyelését, vagy hagyja, hogy a bevezetési élmény hozzon létre egy alapértelmezett munkaterületet az AKS-fürt-előfizetés alapértelmezett erőforráscsoportjában. Ha úgy dönt, hogy saját maga hozza létre, létrehozhatja azt az [Azure Resource Manageren](../platform/template-workspace-configuration.md)keresztül , a [PowerShellen](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)vagy az [Azure Portalon](../learn/quick-create-workspace.md)keresztül. Az alapértelmezett munkaterülethez használt támogatott leképezési párok listáját az [Azure-figyelő tárolókhoz való régióleképezése című témakörben olvashat.](container-insights-region-mapping.md)

- Ön a Log **Analytics közreműködői szerepkör** tagja a tárolófigyelés engedélyezéséhez. A Log Analytics-munkaterülethez való hozzáférés szabályozásáról a [Munkaterületek kezelése című témakörben talál](../platform/manage-access.md)további információt.

- Ön az AKS-fürterőforrás **[tulajdonosi](../../role-based-access-control/built-in-roles.md#owner)** szerepkörének tagja.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

* A Prometheus mérőszámok alapértelmezés szerint nem kerülnek összegyűjtésre. Mielőtt [konfigurálja az ügynököt,](container-insights-prometheus-integration.md) hogy összegyűjtse őket, fontos, hogy tekintse át a Prometheus [dokumentációt,](https://prometheus.io/) hogy megértse, mit lehet kaparni és a módszerek támogatott.

## <a name="supported-configurations"></a>Támogatott konfigurációk

Az alábbiakat hivatalosan is támogatja az Azure Monitor tárolók.

- Környezetek: Azure Red Hat OpenShift, Kubernetes a helyszínen, és az AKS-motor az Azure-ban és az Azure Stack. További információ: [AKS Engine on Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
- A Kubernetes és a támogatási házirend verziói megegyeznek a [támogatott AKS-verziókkal.](../../aks/supported-kubernetes-versions.md) 

## <a name="network-firewall-requirements"></a>Hálózati tűzfal követelményei

Az alábbi táblázatban szereplő információk a proxy- és tűzfal-konfigurációs információkat sorolja fel, amelyek szükségesek ahhoz, hogy a tárolóügynök kommunikáljon az Azure Monitortárolókkal a tárolókhoz. Az ügynök összes hálózati forgalma kimenő az Azure Monitor.

|Ügynök erőforrása|Portok |
|--------------|------|
| *.ods.opinsights.azure.com | 443 |  
| *.oms.opinsights.azure.com | 443 | 
| *.blob.core.windows.net | 443 |
| dc.services.visualstudio.com | 443 |
| *.microsoftonline.com | 443 |
| *.monitoring.azure.com | 443 |
| login.microsoftonline.com | 443 |

Az alábbi táblázatban szereplő információk az Azure China proxy- és tűzfal-konfigurációs adatait sorolják fel.

|Ügynök erőforrása|Portok |Leírás | 
|--------------|------|-------------|
| *.ods.opinsights.azure.cn | 443 | Adatfeldolgozás |
| *.oms.opinsights.azure.cn | 443 | OMS bevezetés |
| *.blob.core.windows.net | 443 | Kimenő kapcsolatok figyelésére szolgál. |
| microsoft.com | 80 | Hálózati kapcsolathoz használható. Ez csak akkor szükséges, ha az ügynöklemezkép verziója ciprod09262019 vagy korábbi. |
| dc.services.visualstudio.com | 443 | Ügynök telemetriai adatok az Azure Public Cloud Application Insights használatával. |

Az alábbi táblázatban szereplő információk az Azure US Government proxy- és tűzfal-konfigurációs adatait sorolják fel.

|Ügynök erőforrása|Portok |Leírás | 
|--------------|------|-------------|
| *.ods.opinsights.azure.us | 443 | Adatfeldolgozás |
| *.oms.opinsights.azure.us | 443 | OMS bevezetés |
| *.blob.core.windows.net | 443 | Kimenő kapcsolatok figyelésére szolgál. |
| microsoft.com | 80 | Hálózati kapcsolathoz használható. Ez csak akkor szükséges, ha az ügynöklemezkép verziója ciprod09262019 vagy korábbi. |
| dc.services.visualstudio.com | 443 | Az Azure Public Cloud Application Insights használatával az ügynöktelemetria. |

## <a name="components"></a>Összetevők

A teljesítmény figyelésének lehetősége egy olyan, a tárolóba tartozó Linuxhoz készült Log Analytics-ügynökre támaszkodik, amelyet kifejezetten az Azure Monitorhoz fejlesztettek ki tárolókhoz. Ez a speciális ügynök a fürt összes csomópontjáról gyűjti a teljesítmény- és eseményadatokat, és az ügynök automatikusan üzembe kerül és regisztrálódik a megadott Log Analytics-munkaterülettel a telepítés során. Az ügyintéző verziója microsoft/oms:ciprod04202018 vagy újabb, és egy dátum képviseli a következő formátumban: *mmddyyyyy*.

>[!NOTE]
>Az AKS-hez nyújtott Windows Server-támogatás előzetes kiadásával a Windows Server-csomópontokkal rendelkező AKS-fürtnem rendelkezik ügynökkel az adatok gyűjtésére és az Azure Monitorra való továbbításra. Ehelyett a standard központi telepítés részeként a fürtben automatikusan üzembe helyezett Linux-csomópont gyűjti és továbbítja az adatokat az Azure Monitornak a fürt összes Windows-csomópontja nevében.  
>

Az ügynök új verziójának megjelenésekor automatikusan frissül az Azure Kubernetes-szolgáltatás (AKS) üzemeltetett felügyelt Kubernetes-fürtökön. A kiadott verziók követéséről az ügynök kiadási közleményei című [témakörben látható.](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)

>[!NOTE]
>Ha már üzembe helyezett egy AKS-fürtöt, engedélyezi a figyelést az Azure CLI vagy egy megadott Azure Resource Manager-sablon használatával, amint azt a cikk későbbi részében is bemutatottak. Nem használható `kubectl` az ügynök frissítésére, törlésére, újratelepítésére és központi telepítésére.
>A sablont ugyanabban az erőforráscsoportban kell telepíteni, mint a fürtöt.

Az alábbi táblázatban ismertetett módszerek egyikével engedélyezheti az Azure Monitortárolók at.

| Telepítési állapot | Módszer | Leírás |
|------------------|--------|-------------|
| Új AKS Kubernetes-fürt | [AKS-fürt létrehozása az Azure CLI használatával](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Engedélyezheti az Azure CLI-vel létrehozott új AKS-fürt figyelését. |
| | [AKS-fürt létrehozása a Terraform használatával](container-insights-enable-new-cluster.md#enable-using-terraform)| Engedélyezheti egy új AKS-fürt figyelését, amelyet a Terraform nyílt forráskódú eszközzel hoz létre. |
| | [OpenShift-fürt létrehozása Azure Resource Manager-sablon nal](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | Engedélyezheti egy előre konfigurált Azure Resource Manager-sablonnal létrehozott új OpenShift-fürt figyelését. |
| | [OpenShift-fürt létrehozása az Azure CLI használatával](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create) | Engedélyezheti a figyelést egy új OpenShift-fürt üzembe helyezése közben az Azure CLI használatával. |
| Meglévő AKS Kubernetes-fürt | [Engedélyezés AKS-fürthöz az Azure CLI használatával](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Engedélyezheti az Azure CLI használatával már üzembe helyezett AKS-fürt figyelését. |
| |[Engedélyezés AKS-fürthöz a Terraform használatával](container-insights-enable-existing-clusters.md#enable-using-terraform) | Engedélyezheti a már üzembe helyezett AKS-fürt figyelését a Terraform nyílt forráskódú eszközzel. |
| | [Engedélyezés AKS-fürthöz az Azure Monitorból](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Engedélyezheti egy vagy több AKS-fürtök már üzembe helyezett a több fürtös lap az Azure Monitor. |
| | [Engedélyezés AKS-fürtből](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Közvetlenül az Azure Portalon engedélyezheti a figyelést egy AKS-fürtről. |
| | [Engedélyezés AKS-fürthöz Egy Azure Resource Manager-sablon használatával](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Az AKS-fürt figyelése egy előre konfigurált Azure Resource Manager sablon használatával engedélyezheti. |
| | [Hibrid Kubernetes-fürt engedélyezése](container-insights-hybrid-setup.md) | Engedélyezheti az Azure Stackben vagy a helyszínen üzemeltetett Kubernetes-motor ban üzemeltetett AKS-motor figyelését. |
| | [OpenShift-fürt engedélyezése Azure Resource Manager-sablon használatával](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | Egy meglévő OpenShift-fürt figyelése egy előre konfigurált Azure Resource Manager sablon használatával engedélyezheti. |
| | [OpenShift-fürt engedélyezése az Azure Monitorból](container-insights-azure-redhat-setup.md#from-the-azure-portal) | Engedélyezheti egy vagy több OpenShift-fürt figyelését, amelyek már üzembe vannak helyezve az Azure Monitor többfürtös lapjáról. |

## <a name="next-steps"></a>További lépések

- Ha a figyelés engedélyezve van, elkezdheti az Azure Kubernetes-szolgáltatás (AKS), az Azure Stack vagy más környezetben üzemeltetett Kubernetes-fürtök teljesítményének elemzését. Az Azure Monitor tárolókhoz való használatáról a [Kubernetes-fürt teljesítményének megtekintése](container-insights-analyze.md)című témakörben olvashat.
