---
title: Azure Red Hat OpenShift-fürtök konfigurálása az Azure Monitor for containers szolgáltatással | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan konfigurálható Azure Monitor a tárolók számára az Azure Red Hat OpenShift üzemeltetett Kubernetes-fürtök figyelésére.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/18/2019
ms.openlocfilehash: 26477eeb00fe7616a8d2f2be343e586042c0d130
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279663"
---
# <a name="configure-azure-red-hat-openshift-clusters-with-azure-monitor-for-containers"></a>Azure Red Hat OpenShift-fürtök konfigurálása a Azure Monitor for containers szolgáltatással

A tárolók Azure Monitor széles körű monitorozást biztosítanak az Azure Kubernetes szolgáltatás (ak) és az AK-beli motor fürtök számára. Ez a cikk azt ismerteti, hogyan engedélyezhető az [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) üzemeltetett Kubernetes-fürtök figyelése hasonló figyelési élmény eléréséhez.

>[!NOTE]
>A Red Hat OpenShift támogatása jelenleg nyilvános előzetes verzióban érhető el.
>

A tárolók Azure Monitor a következő támogatott módszerek használatával engedélyezhető az új, illetve egy vagy több Azure Red Hat-OpenShift üzemelő példányhoz:

- Meglévő fürt Azure Portal vagy Azure Resource Manager sablon használatával
- Új fürt Azure Resource Manager sablon használatával 

## <a name="supported-and-unsupported-features"></a>Támogatott és nem támogatott funkciók

A tárolók Azure Monitor támogatja az Azure Red Hat OpenShift figyelését az [Áttekintés](container-insights-overview.md) című cikkben leírtak szerint, a következő funkciók kivételével:

- Élő adatértékek
- Prometheus-metrikák selejtezése
- A fürtcsomópontok és a hüvelyek metrikáinak összegyűjtése és a Azure Monitor metrikai tárolóba írása
- Állapot funkció

## <a name="prerequisites"></a>Előfeltételek

- A tárolók Azure Monitor szolgáltatásainak engedélyezéséhez és eléréséhez legalább az Azure-előfizetéshez tartozó Azure- *közreműködő* szerepkör tagjának kell lennie, és a Log Analytics munkaterület [*log Analytics közreműködő*](../platform/manage-access.md#manage-access-using-azure-permissions) szerepkörének tagjának kell lennie a tárolók Azure monitor.

- A figyelési adat megtekintéséhez a [*log Analytics olvasói*](../platform/manage-access.md#manage-access-using-azure-permissions) szerepkörhöz tartozó jogosultsággal rendelkező log Analytics-munkaterületnek kell lennie a tárolók Azure monitor.

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>Új fürt engedélyezése Azure Resource Manager sablon használatával

Az alábbi lépések végrehajtásával telepítsen egy Azure Red Hat OpenShift-fürtöt a figyelés engedélyezve lehetőséggel. A továbblépés előtt tekintse át az oktatóanyag [Azure Red Hat OpenShift-fürt létrehozásával](../../openshift/tutorial-create-cluster.md#prerequisites) foglalkozó témakört, hogy megtudja, milyen függőségek szükségesek a környezet beállításához.

Ez a módszer két JSON-sablont tartalmaz. Az egyik sablon meghatározza azt a konfigurációt, amellyel a fürtöt a figyelés engedélyezve állapotba helyezi, a másik pedig a konfigurált paramétereket adja meg a következők megadásához:

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

Ha ismeri a sablon segítségével üzembe helyezni erőforrásokat fogalmát, lásd:

- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/resource-group-template-deploy.md)

- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Ha az Azure CLI-vel, akkor először helyi telepítése és használata a parancssori felület. Az Azure CLI 2.0.65 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa `az --version`. Ha telepíteni vagy frissíteni szeretné az Azure CLI, lásd: kell [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli). 

A Log Analytics munkaterületet létre kell hozni, mielőtt engedélyezi a figyelést a Azure PowerShell vagy a parancssori felület használatával. A munkaterület létrehozásához, beállíthatja azt keresztül [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)segítségével, [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json), vagy a [az Azure portal](../../azure-monitor/learn/quick-create-workspace.md).

1. Töltse le és mentse a fájlt egy helyi mappába, a Azure Resource Manager sablonra és a paraméterre, és hozzon létre egy olyan fürtöt a figyelési bővítménnyel, amely a következő parancsokat használja:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json` 

2. Bejelentkezés az Azure-ba 

    ```azurecli
    az login    
    ```
    
    Ha több előfizetéshez is rendelkezik hozzáféréssel, futtassa `az account set -s {subscription ID}` a használni kívánt előfizetéssel `{subscription ID}` cserélje le.
 
3. Hozzon létre egy erőforráscsoportot a fürthöz, ha még nem rendelkezik ilyennel. Az Azure-OpenShift támogató Azure-régiók listáját a [támogatott régiók](../../openshift/supported-resources.md#azure-regions)című részben tekintheti meg. 

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location> 
    ```

4. Szerkessze a **newClusterWithMonitoringParam. JSON** JSON-paramétert, és frissítse a következő értékeket:

    - *hely*
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

    ```azurecli
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>Meglévő fürt engedélyezése

A következő lépések végrehajtásával engedélyezheti az Azure-ban üzembe helyezett Azure Red Hat OpenShift-fürtök figyelését. Ezt a Azure Portal vagy a megadott sablonok használatával végezheti el.

### <a name="from-the-azure-portal"></a>A Azure Portal
 
1. Bejelentkezés az [Azure Portalra](https://portal.azure.com).

2. A Azure Portal menüben vagy a Kezdőlap lapon válassza a **Azure monitor**lehetőséget. Alatt a **Insights** szakaszban jelölje be **tárolók**. 

3. Az a **figyelője – tárolók** lapon jelölje be **a nem felügyelt fürtöket**.

4. A nem figyelt fürtök listájából keresse meg a fürtöt a listában, és kattintson az **Engedélyezés**gombra. A listában szereplő eredmények azonosításához keresse meg az **ARO** értéket a **fürt típusa**oszlopban.

5. Az a **üzembe helyezése az Azure Monitor-tárolókhoz** lapon, ha rendelkezik egy meglévő Log Analytics munkaterület ugyanabban az előfizetésben a fürttel, válassza a legördülő listából.  
    A lista előjelöli az alapértelmezett munkaterületet és helyet, amelyet a fürt az előfizetésben üzembe helyez. 

    ![Nem figyelt fürtök figyelésének engedélyezése](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Ha azt szeretné, a fürtből a figyelési adatok tárolására szolgáló új Log Analytics-munkaterület létrehozásához kövesse a [hozzon létre egy Log Analytics-munkaterület](../../azure-monitor/learn/quick-create-workspace.md). Ügyeljen arra, hogy a munkaterületet ugyanabban az előfizetésben hozza létre, amelyben a RedHat OpenShift-fürtöt telepítette. 
 
Miután engedélyezte a figyelés, a fürt mérőszámok megtekintéséhez nagyjából 15 percet igénybe vehet. 

### <a name="enable-using-an-azure-resource-manager-template"></a>Engedélyezés Azure Resource Manager sablon használatával

Ez a módszer két JSON-sablont tartalmaz. Egy sablon határozza meg, a konfigurációt a figyelés, és a másik paraméterértékeket, hogy adja meg az alábbiakat tartalmazza:

- Az Azure RedHat OpenShift-fürt erőforrás-azonosítója. 

- Az az erőforráscsoport, amelyben a fürt telepítve van.

- Egy Log Analytics-munkaterület.

Ha ismeri a sablon segítségével üzembe helyezni erőforrásokat fogalmát, lásd:

- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/resource-group-template-deploy.md)

- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Ha az Azure CLI-vel, akkor először helyi telepítése és használata a parancssori felület. Az Azure CLI 2.0.65 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa `az --version`. Ha telepíteni vagy frissíteni szeretné az Azure CLI, lásd: kell [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli). 

A Log Analytics munkaterületet létre kell hozni, mielőtt engedélyezi a figyelést a Azure PowerShell vagy a parancssori felület használatával. A munkaterület létrehozásához, beállíthatja azt keresztül [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)segítségével, [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json), vagy a [az Azure portal](../../azure-monitor/learn/quick-create-workspace.md).

1. Töltse le a sablon és a paraméter fájlját, hogy a következő parancsokkal frissítse a fürtöt a figyelési bővítmény használatával:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json` 

2. Bejelentkezés az Azure-ba 

    ```azurecli
    az login    
    ```

    Ha több előfizetéshez is rendelkezik hozzáféréssel, futtassa `az account set -s {subscription ID}` a használni kívánt előfizetéssel `{subscription ID}` cserélje le.

3. Az Azure RedHat OpenShift-fürt előfizetésének meghatározása.

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. Futtassa a következő parancsot a fürt helyének és erőforrás-AZONOSÍTÓjának azonosításához:

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName> 
    ```

5. Szerkessze a **existingClusterParam. JSON** JSON-paramétert, és frissítse a *AraResourceId* és a *araResoruceLocation*értékeket. Az érték **workspaceResourceId** a Log Analytics-munkaterületet, amely tartalmazza a következő munkaterület nevének teljes erőforrás-Azonosítójára van. 

6. Az Azure CLI-vel való üzembe helyezéshez futtassa a következő parancsokat: 

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json 
    ```

    A kimenet a következőhöz hasonló:

    ```azurecli
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>Következő lépések

Ha a figyelés engedélyezve van a RedHat OpenShift-fürt és a rajtuk futó munkaterhelések állapotának és erőforrás-felhasználásának összegyűjtéséhez, Ismerje meg, [hogyan használhatja](container-insights-analyze.md) a Azure monitor for containers szolgáltatást.