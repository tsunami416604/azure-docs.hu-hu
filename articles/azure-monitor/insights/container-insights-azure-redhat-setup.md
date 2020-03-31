---
title: Az Azure Red Hat OpenShift fürtök konfigurálása az Azure Monitor tárolókkal | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan konfigurálhatja a Kubernetes-fürt figyelését az Azure Red Hat OpenShift en üzemeltetett Azure Monitorszolgáltatással.
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: c2fd3568be2c51296bb1377e91031ebfb7ca6ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275515"
---
# <a name="configure-azure-red-hat-openshift-clusters-with-azure-monitor-for-containers"></a>Az Azure Red Hat OpenShift fürtök konfigurálása az Azure Monitor tárolókkal

Az Azure Monitor tárolók gazdag figyelési élményt nyújt az Azure Kubernetes-szolgáltatás (AKS) és az AKS Engine-fürtök. Ez a cikk ismerteti, hogyan engedélyezheti az Azure [Red Hat OpenShift](../../openshift/intro-openshift.md) en üzemeltetett Kubernetes-fürtök figyelését, hogy hasonló figyelési élményt érjen el.

>[!NOTE]
>Az Azure Red Hat OpenShift támogatása jelenleg nyilvános előzetes verzióban érhető el.
>

Az Azure Monitor tárolók hoz engedélyezhető az új, vagy egy vagy több meglévő telepítések az Azure Red Hat OpenShift a következő támogatott módszerekkel:

- Az Azure Portalon vagy az Azure Resource Manager-sablon használatával egy meglévő fürthöz.
- Új fürt az Azure Resource Manager sablon használatával, vagy az [Azure CLI](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create)használatával új fürt létrehozása kor.

## <a name="supported-and-unsupported-features"></a>Támogatott és nem támogatott funkciók

Az Azure Monitor a tárolók támogatja az Azure Red Hat OpenShift figyelése az áttekintés cikkben [leírtak](container-insights-overview.md) szerint, kivéve az alábbi funkciókat:

- Élő adatok (előzetes verzió)
- [Metrikák gyűjtése](container-insights-update-metrics.md) a fürtcsomópontokból és -podokból, és tárolásuk az Azure Monitor metrika-adatbázisában

## <a name="prerequisites"></a>Előfeltételek

- Az Azure Monitor tárolók funkcióinak engedélyezéséhez és eléréséhez legalább az Azure *Contributor* szerepkör tagjának kell lennie az Azure-előfizetésben, és tagja kell lennie az Azure-figyelővel konfigurált [*Log Analytics-munkaterület Log Analytics-közreműködői*](../platform/manage-access.md#manage-access-using-azure-permissions) szerepkörének.

- A figyelési adatok megtekintéséhez a [*Log Analytics-olvasó*](../platform/manage-access.md#manage-access-using-azure-permissions) szerepkör-engedély tagja a Log Analytics-munkaterület konfigurálva az Azure Monitor tárolók.

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>Új fürt engedélyezése Azure Resource Manager-sablon használatával

Hajtsa végre az alábbi lépéseket egy Azure Red Hat OpenShift fürt üzembe helyezéséhez, ha engedélyezve van a figyelés. A folytatás előtt tekintse át az Azure [Red Hat OpenShift-fürt létrehozása](../../openshift/tutorial-create-cluster.md#prerequisites) című oktatóanyagot, amely ből megismerheti a konfigurálandó függőségeket, hogy a környezet megfelelően legyen beállítva.

Ez a módszer két JSON-sablont tartalmaz. Az egyik sablon a fürt figyelési engedélyezésével történő központi telepítésének konfigurációját határozza meg, a másik pedig a következő ként konfigurált paraméterértékeket tartalmazza:

- Az Azure Red Hat OpenShift fürterőforrás-azonosító.

- Az az erőforráscsoport, amelyben a fürt telepítve van.

- [Az Azure Active Directory-bérlői azonosító](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant) megjegyezte, miután a lépéseket, hogy hozzon létre egy vagy egy már létrehozott.

- [Az Azure Active Directory-ügyfélalkalmazás-azonosító](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration) megjegyezte, miután a lépéseket, hogy hozzon létre egy vagy egy már létrehozott.

- [Az Azure Active Directory-ügyfél titkos megjegyzése](../../openshift/howto-aad-app-configuration.md#create-a-client-secret) a már létrehozott vagy már létrehozott lépések végrehajtása után.

- [Az Azure AD biztonsági csoport](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group) megjegyezte, miután a lépéseket, hogy hozzon létre egy vagy egy már létrehozott.

- Meglévő Log Analytics-munkaterület erőforrásazonosítója.

- A fürtben létrehozandó főcsomópontok száma.

- A számítási csomópontok száma az ügynökkészlet-profilban.

- Az ügynökkészlet-profilban lévő infrastruktúra-csomópontok száma.

Ha nem ismeri az erőforrások sablon használatával történő üzembe helyezésének fogalmát, olvassa el az:

- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/templates/deploy-powershell.md)

- [Erőforrások üzembe helyezése erőforrás-kezelői sablonokkal és az Azure CLI-vel](../../azure-resource-manager/templates/deploy-cli.md)

Ha úgy dönt, hogy az Azure CLI, először telepítenie kell, és a CLI helyileg kell használni. Az Azure CLI 2.0.65-ös vagy újabb verzióját kell futtatnia. A verzió azonosításához `az --version`futtassa a futtassa a futtassa a futtassa a futtassa Ha telepítenie vagy frissítenie kell az Azure CLI-t, [olvassa el az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli)című témakört.

A Log Analytics-munkaterületet létre kell hozni, mielőtt engedélyezne figyelést az Azure PowerShell vagy a CLI használatával. A munkaterület létrehozásához beállíthatja azt az [Azure Resource Manageren](../../azure-monitor/platform/template-workspace-configuration.md)keresztül a [PowerShellen](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)vagy az [Azure Portalon](../../azure-monitor/learn/quick-create-workspace.md)keresztül.

1. Töltse le és mentse egy helyi mappába, az Azure Resource Manager sablonba és paraméterfájlba, hogy fürtöt hozzon létre a figyelési bővítményrel a következő parancsokkal:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json`

2. Bejelentkezés az Azure-ba

    ```azurecli
    az login    
    ```

    Ha több előfizetéshez is hozzáfér, futtassa `az account set -s {subscription ID}` a cserélni `{subscription ID}` a használni kívánt előfizetéssel.

3. Hozzon létre egy erőforráscsoportot a fürthöz, ha még nem rendelkezik ilyentel. Az Azure-alapú OpenShift szolgáltatást támogató Azure-régiók listáját a Támogatott régiók című [témakörben tetszetős ek.](../../openshift/supported-resources.md#azure-regions)

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location>
    ```

4. A JSON **paraméterújClusterWithMonitoringParam.json** paraméterfájl jisaktáját szerkesztheti, és frissítse a következő értékeket:

    - *Helyen*
    - *fürtnév*
    - *aadTenantId*
    - *aadClientId*
    - *aadClientSecret*
    - *aadCustomerAdminGroupId*
    - *workspaceResourceId*
    - *masterNodeCount (főnodeszám)*
    - *computeNodeCount (számítási szám)*
    - *infraNodeCount (na.*

5. A következő lépés telepíti a fürt figyelése engedélyezve az Azure CLI használatával.

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json
    ```

    A kimenet a következőhöz hasonlít:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>Meglévő fürt engedélyezése

Hajtsa végre az alábbi lépéseket az Azure-ban üzembe helyezett Azure Red Hat OpenShift fürt figyelésének engedélyezéséhez. Ezt az Azure Portalon vagy a megadott sablonok használatával valósíthatja meg.

### <a name="from-the-azure-portal"></a>Az Azure Portalról

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

2. Az Azure Portal menüben vagy a kezdőlapon válassza az **Azure Monitor**lehetőséget. Az **Insights (Insights)** csoportban válassza a **Tárolók lehetőséget.**

3. A **Figyelő - tárolók** lapon válassza a **Nem figyelt fürtök lehetőséget.**

4. A nem figyelt fürtök listájában keresse meg a fürtöt a listában, és kattintson az **Engedélyezés gombra.** A listában szereplő eredményeket a **CLUSTER TYPE**oszlopban található **ARO** érték keresésével azonosíthatja.

5. Az **Azure-figyelő tárolókhoz** való bevezetés lapon, ha egy meglévő Log Analytics-munkaterület teljében ugyanabban az előfizetésben, mint a fürt, válassza ki a legördülő listából.  
    A lista előre kiválasztja az alapértelmezett munkaterületet és helyet, amelyhez a fürt telepítve van az előfizetésben.

    ![Nem figyelt fürtök figyelésének engedélyezése](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Ha új Log Analytics-munkaterületet szeretne létrehozni a figyelési adatok fürtből való tárolásához, kövesse a [Log Analytics munkaterület létrehozása című](../../azure-monitor/learn/quick-create-workspace.md)részben található utasításokat. Ügyeljen arra, hogy a munkaterületet ugyanabban az előfizetésben hozza létre, amelybe a RedHat OpenShift fürt telepítve van.

Miután engedélyezte a figyelést, körülbelül 15 percet is igénybe vehet, mielőtt megtekintheti a fürt állapotmetrikákat.

### <a name="enable-using-an-azure-resource-manager-template"></a>Engedélyezés Azure Resource Manager-sablon használatával

Ez a módszer két JSON-sablont tartalmaz. Az egyik sablon a figyelés engedélyezéséhez a konfigurációt adja meg, a másik pedig a következők megadására konfigurált paraméterértékeket tartalmazza:

- Az Azure RedHat OpenShift fürterőforrás-azonosítója.

- Az az erőforráscsoport, amelyben a fürt telepítve van.

- Egy Log Analytics-munkaterület.

Ha nem ismeri az erőforrások sablon használatával történő üzembe helyezésének fogalmát, olvassa el az:

- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/templates/deploy-powershell.md)

- [Erőforrások üzembe helyezése erőforrás-kezelői sablonokkal és az Azure CLI-vel](../../azure-resource-manager/templates/deploy-cli.md)

Ha úgy dönt, hogy az Azure CLI, először telepítenie kell, és a CLI helyileg kell használni. Az Azure CLI 2.0.65-ös vagy újabb verzióját kell futtatnia. A verzió azonosításához `az --version`futtassa a futtassa a futtassa a futtassa a futtassa Ha telepítenie vagy frissítenie kell az Azure CLI-t, [olvassa el az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli)című témakört.

A Log Analytics-munkaterületet létre kell hozni, mielőtt engedélyezne figyelést az Azure PowerShell vagy a CLI használatával. A munkaterület létrehozásához beállíthatja azt az [Azure Resource Manageren](../../azure-monitor/platform/template-workspace-configuration.md)keresztül a [PowerShellen](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)vagy az [Azure Portalon](../../azure-monitor/learn/quick-create-workspace.md)keresztül.

1. Töltse le a sablont és a paraméterfájlt a fürt frissítéséhez a figyelési bővítőhe-upivel a következő parancsokkal:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json`

2. Bejelentkezés az Azure-ba

    ```azurecli
    az login    
    ```

    Ha több előfizetéshez is hozzáfér, futtassa `az account set -s {subscription ID}` a cserélni `{subscription ID}` a használni kívánt előfizetéssel.

3. Adja meg az Azure RedHat OpenShift-fürt előfizetését.

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. A fürt helyének és erőforrás-azonosítójának azonosításához futtassa a következő parancsot:

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName>
    ```

5. Szerkesztheti a JSON paraméterfájlt **existingClusterParam.json** és frissítse az *araResourceId* és *araResoruceLocation*értékeket. A **workspaceResourceId** értéke a Log Analytics-munkaterület teljes erőforrásazonosítója, amely tartalmazza a munkaterület nevét.

6. Az Azure CLI-vel történő üzembe helyezéshez futtassa a következő parancsokat:

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json
    ```

    A kimenet a következőhöz hasonlít:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>További lépések

- Ha a figyelés engedélyezve van a RedHat OpenShift-fürt és a rajtuk futó számítási feladatok állapotának és erőforrás-kihasználtságának összegyűjtésére, ismerje meg, [hogyan használhatja az](container-insights-analyze.md) Azure Monitort tárolókhoz.

- Ha meg szeretné tudni, hogyan állíthatja le a fürt figyelését az Azure Monitor tárolók számára című témakörben: [Hogyan állíthatja le az Azure Red Hat OpenShift-fürt figyelését.](container-insights-optout-openshift.md)
