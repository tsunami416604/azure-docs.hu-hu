---
title: Az Azure Red Hat OpenShift v3. x konfigurálása az Azure Monitor for containers szolgáltatással | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja a Kubernetes-fürtök figyelését az Azure Red Hat OpenShift 3-as vagy újabb verziójában üzemeltetett Azure Monitor használatával.
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: 98ac5752e047c4f5f6db63d228bec7c47271aa00
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "82166293"
---
# <a name="configure-azure-red-hat-openshift-v3-with-azure-monitor-for-containers"></a>Az Azure Red Hat OpenShift v3 konfigurálása a Azure Monitor for containers szolgáltatással

A tárolók Azure Monitor széles körű monitorozást biztosítanak az Azure Kubernetes szolgáltatás (ak) és az AK-beli motor fürtök számára. Ez a cikk azt ismerteti, hogyan engedélyezhető a Kubernetes-fürtök figyelése az [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) 3. verziójában és a 3. verzió legújabb támogatott verzióján, hogy hasonló figyelési élményt lehessen elérni.

>[!NOTE]
>Az Azure Red Hat OpenShift támogatása jelenleg nyilvános előzetes verzióban érhető el.
>

A tárolók Azure Monitor a következő támogatott módszerek használatával engedélyezhető az új, illetve egy vagy több Azure Red Hat-OpenShift üzemelő példányhoz:

- Egy meglévő fürt Azure Portal vagy Azure Resource Manager sablon használatával.
- Új fürt Azure Resource Manager sablon használatával vagy új fürt létrehozásakor az [Azure CLI](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create)használatával.

## <a name="supported-and-unsupported-features"></a>Támogatott és nem támogatott funkciók

A tárolók Azure Monitor támogatja az Azure Red Hat OpenShift figyelését az [Áttekintés](container-insights-overview.md) című cikkben leírtak szerint, a következő funkciók kivételével:

- Élő adatértékek (előzetes verzió)
- A fürtcsomópontok és a hüvelyek [metrikáinak összegyűjtése](container-insights-update-metrics.md) és tárolása a Azure monitor metrikai adatbázisban

## <a name="prerequisites"></a>Előfeltételek

- A tárolók Azure Monitor szolgáltatásainak engedélyezéséhez és eléréséhez legalább az Azure-előfizetéshez tartozó Azure- *közreműködő* szerepkör tagjának kell lennie, és a Log Analytics munkaterület [*log Analytics közreműködő*](../platform/manage-access.md#manage-access-using-azure-permissions) szerepkörének tagjának kell lennie a tárolók Azure monitor.

- A figyelési adat megtekintéséhez a [*log Analytics olvasói*](../platform/manage-access.md#manage-access-using-azure-permissions) szerepkörhöz tartozó jogosultsággal rendelkező log Analytics-munkaterületnek kell lennie a tárolók Azure monitor.

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>Új fürt engedélyezése Azure Resource Manager sablon használatával

Az alábbi lépések végrehajtásával telepítsen egy Azure Red Hat OpenShift-fürtöt a figyelés engedélyezve lehetőséggel. A továbblépés előtt tekintse át az oktatóanyag [Azure Red Hat OpenShift-fürt létrehozásával](../../openshift/tutorial-create-cluster.md#prerequisites) foglalkozó témakört, hogy megtudja, milyen függőségek szükségesek a környezet beállításához.

Ez a metódus két JSON-sablont tartalmaz. Az egyik sablon meghatározza azt a konfigurációt, amellyel a fürtöt a figyelés engedélyezve állapotba helyezi, a másik pedig a konfigurált paramétereket adja meg a következők megadásához:

- Az Azure Red Hat OpenShift-fürt erőforrás-azonosítója.

- Az az erőforráscsoport, amelyben a fürt telepítve van.

- [Azure Active Directory a bérlő azonosítóját](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant) , miután elvégezte a létrehozásához szükséges lépéseket.

- [Azure Active Directory ÜGYFÉLALKALMAZÁS azonosítóját](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration) , miután elvégezte a létrehozásához szükséges lépéseket.

- [Azure Active Directory az ügyfél titkos kulcsát](../../openshift/howto-aad-app-configuration.md#create-a-client-secret) , miután elvégezte a létrehozásához szükséges lépéseket.

- Az [Azure ad biztonsági csoport](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group) a létrehozásuk lépéseinek elvégzése után megjegyezte, hogy egy vagy több már létrejött.

- Meglévő Log Analytics munkaterület erőforrás-azonosítója.

- A fürtben létrehozandó főcsomópontok száma.

- Az ügynök-készlet profiljában lévő számítási csomópontok száma.

- Az ügynök-készlet profiljában található infrastruktúra-csomópontok száma.

Ha nem ismeri az erőforrások sablon használatával történő központi telepítésének fogalmát, tekintse meg a következőt:

- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/templates/deploy-powershell.md)

- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](../../azure-resource-manager/templates/deploy-cli.md)

Ha úgy dönt, hogy az Azure CLI-t használja, először telepítenie és használnia kell a CLI-t helyileg. Az Azure CLI 2.0.65 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a `az --version`parancsot. Ha telepítenie vagy frissítenie kell az Azure CLI-t, tekintse meg [Az Azure CLI telepítését](https://docs.microsoft.com/cli/azure/install-azure-cli)ismertető témakört.

A Log Analytics munkaterületet létre kell hozni, mielőtt engedélyezi a figyelést a Azure PowerShell vagy a parancssori felület használatával. A munkaterület létrehozásához a [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), a [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)vagy a [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)használatával állíthatja be.

1. Töltse le és mentse a fájlt egy helyi mappába, a Azure Resource Manager sablonra és a paraméterre, és hozzon létre egy olyan fürtöt a figyelési bővítménnyel, amely a következő parancsokat használja:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json`

2. Bejelentkezés az Azure-ba

    ```azurecli
    az login    
    ```

    Ha több előfizetéshez is rendelkezik hozzáféréssel, `az account set -s {subscription ID}` futtassa `{subscription ID}` a cserét a használni kívánt előfizetéssel.

3. Hozzon létre egy erőforráscsoportot a fürthöz, ha még nem rendelkezik ilyennel. Az Azure-OpenShift támogató Azure-régiók listáját a [támogatott régiók](../../openshift/supported-resources.md#azure-regions)című részben tekintheti meg.

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location>
    ```

4. Szerkessze a **newClusterWithMonitoringParam. JSON** JSON-paramétert, és frissítse a következő értékeket:

    - *helyen*
    - *clusterName*
    - *aadTenantId*
    - *aadClientId*
    - *aadClientSecret*
    - *aadCustomerAdminGroupId*
    - *workspaceResourceId*
    - *masterNodeCount*
    - *computeNodeCount*
    - *infraNodeCount*

5. A következő lépés az Azure CLI használatával helyezi üzembe a fürtöt a figyeléssel.

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json
    ```

    A kimenet a következőhöz hasonló:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>Meglévő fürt engedélyezése

A következő lépések végrehajtásával engedélyezheti az Azure-ban üzembe helyezett Azure Red Hat OpenShift-fürtök figyelését. Ezt a Azure Portal vagy a megadott sablonok használatával végezheti el.

### <a name="from-the-azure-portal"></a>Az Azure Portalról

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A Azure Portal menüben vagy a Kezdőlap lapon válassza a **Azure monitor**lehetőséget. Az **áttekintések** szakaszban válassza a **tárolók**lehetőséget.

3. A **figyelő-tárolók** lapon válassza a **nem figyelt fürtök**lehetőséget.

4. A nem figyelt fürtök listájából keresse meg a fürtöt a listában, és kattintson az **Engedélyezés**gombra. A listában szereplő eredmények azonosításához keresse meg az **ARO** értéket a **fürt típusa**oszlopban.

5. Ha egy meglévő Log Analytics munkaterülettel rendelkezik, amely a fürttel azonos előfizetésben található, **Azure monitor a tárolók** lapon, válassza ki a kívánt elemet a legördülő listából.  
    A lista előjelöli az alapértelmezett munkaterületet és helyet, amelyet a fürt az előfizetésben üzembe helyez.

    ![Nem figyelt fürtök figyelésének engedélyezése](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Ha új Log Analytics munkaterületet szeretne létrehozni a figyelési adatok fürtből való tárolásához, kövesse az [log Analytics munkaterület létrehozása](../../azure-monitor/learn/quick-create-workspace.md)című témakör utasításait. Ügyeljen arra, hogy a munkaterületet ugyanabban az előfizetésben hozza létre, amelyben a RedHat OpenShift-fürtöt telepítette.

A figyelés engedélyezése után körülbelül 15 percet is igénybe vehet, mielőtt megtekintheti a fürthöz tartozó állapot mérőszámait.

### <a name="enable-using-an-azure-resource-manager-template"></a>Engedélyezés Azure Resource Manager sablon használatával

Ez a metódus két JSON-sablont tartalmaz. Az egyik sablon meghatározza a figyelés engedélyezésének konfigurációját, a másik pedig a konfigurált paramétereket az alábbiak megadásához:

- Az Azure RedHat OpenShift-fürt erőforrás-azonosítója.

- Az az erőforráscsoport, amelyben a fürt telepítve van.

- Egy Log Analytics-munkaterület.

Ha nem ismeri az erőforrások sablon használatával történő központi telepítésének fogalmát, tekintse meg a következőt:

- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/templates/deploy-powershell.md)

- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](../../azure-resource-manager/templates/deploy-cli.md)

Ha úgy dönt, hogy az Azure CLI-t használja, először telepítenie és használnia kell a CLI-t helyileg. Az Azure CLI 2.0.65 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a `az --version`parancsot. Ha telepítenie vagy frissítenie kell az Azure CLI-t, tekintse meg [Az Azure CLI telepítését](https://docs.microsoft.com/cli/azure/install-azure-cli)ismertető témakört.

A Log Analytics munkaterületet létre kell hozni, mielőtt engedélyezi a figyelést a Azure PowerShell vagy a parancssori felület használatával. A munkaterület létrehozásához a [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), a [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)vagy a [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)használatával állíthatja be.

1. Töltse le a sablon és a paraméter fájlját, hogy a következő parancsokkal frissítse a fürtöt a figyelési bővítmény használatával:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json`

2. Bejelentkezés az Azure-ba

    ```azurecli
    az login    
    ```

    Ha több előfizetéshez is rendelkezik hozzáféréssel, `az account set -s {subscription ID}` futtassa `{subscription ID}` a cserét a használni kívánt előfizetéssel.

3. Az Azure RedHat OpenShift-fürt előfizetésének meghatározása.

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. Futtassa a következő parancsot a fürt helyének és erőforrás-AZONOSÍTÓjának azonosításához:

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName>
    ```

5. Szerkessze a **existingClusterParam. JSON** JSON-paramétert, és frissítse a *AraResourceId* és a *araResoruceLocation*értékeket. A **workspaceResourceId** értéke a log Analytics munkaterület teljes erőforrás-azonosítója, amely tartalmazza a munkaterület nevét.

6. Az Azure CLI-vel való üzembe helyezéshez futtassa a következő parancsokat:

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json
    ```

    A kimenet a következőhöz hasonló:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>További lépések

- Ha a figyelés engedélyezve van a RedHat OpenShift-fürt és a rajtuk futó munkaterhelések állapotának és erőforrás-felhasználásának összegyűjtéséhez, Ismerje meg, [hogyan használhatja](container-insights-analyze.md) a Azure monitor for containers szolgáltatást.

- Alapértelmezés szerint a tároló ügynök gyűjti az összes névtérben futó összes tároló StdOut/stderr-tárolójának naplóit, kivéve a Kube rendszert. Ha az adott névtérhez vagy névterekhez tartozó tároló-naplózási gyűjteményt szeretne konfigurálni, tekintse át a Container-elemzések [ügynökének konfigurációját](container-insights-agent-config.md) a kívánt adatgyűjtési beállítások ConfigMap-konfigurációs fájlra való konfigurálásához.

- A Prometheus-metrikák a fürtből való beolvasásához és elemzéséhez tekintse át a [Prometheus-metrikák leselejtezésének konfigurálása](container-insights-prometheus-integration.md)

- Ha meg szeretné tudni, hogyan állíthatja le a fürtöt a tárolók Azure Monitorával, tekintse meg [Az Azure Red Hat OpenShift-fürt figyelésének leállítása](container-insights-optout-openshift.md)című témakört.
