---
title: Az Azure Red Hat OpenShift v4. x konfigurálása az Azure Monitor for containers szolgáltatással | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja a Kubernetes-fürtök figyelését az Azure Red Hat OpenShift 4-es vagy újabb verziójában üzemeltetett Azure Monitor.
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: 4b827524845874dabaabe535163d99c408f77a60
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196295"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-azure-monitor-for-containers"></a>Az Azure Red Hat OpenShift v4. x konfigurálása Azure Monitor for containers szolgáltatással

A tárolók Azure Monitor széles körű monitorozást biztosítanak az Azure Kubernetes szolgáltatás (ak) és az AK-beli motor fürtök számára. Ez a cikk azt ismerteti, hogyan engedélyezhető a Kubernetes-fürtök figyelése az [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) 4. x verziójában, hogy hasonló figyelési élményt lehessen elérni.

>[!NOTE]
>Az Azure Red Hat OpenShift támogatása jelenleg nyilvános előzetes verzióban érhető el.
>

A tárolók Azure Monitor a következő támogatott módszerek használatával engedélyezhető az Azure Red Hat OpenShift v4. x egy vagy több meglévő központi telepítéséhez:

- Egy meglévő fürthöz a megadott bash-parancsfájl használatával és az [Azure CLI](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create)-ben való futtatásával.

## <a name="supported-and-unsupported-features"></a>Támogatott és nem támogatott funkciók

A tárolók Azure Monitor támogatja az Azure Red Hat OpenShift v4. x figyelését az [áttekintő](container-insights-overview.md) cikkben leírtak szerint, a következő funkciók kivételével:

- Élő adatértékek (előzetes verzió)
- A fürtcsomópontok és a hüvelyek [metrikáinak összegyűjtése](container-insights-update-metrics.md) és tárolása a Azure monitor metrikai adatbázisban

## <a name="prerequisites"></a>Előfeltételek

- Az Azure CLI verziója 2.0.72 vagy újabb

- [3. Helm](https://helm.sh/docs/intro/install/) CLI-eszköz

- [Bash 4-es verziója](https://www.gnu.org/software/bash/)

- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) parancssori eszköz

- A tárolók Azure Monitor szolgáltatásainak engedélyezéséhez és eléréséhez legalább az Azure-előfizetéshez tartozó Azure- *közreműködő* szerepkör tagjának kell lennie, és a Log Analytics munkaterület [*log Analytics közreműködő*](../platform/manage-access.md#manage-access-using-azure-permissions) szerepkörének tagjának kell lennie a tárolók Azure monitor.

- A figyelési adat megtekintéséhez a [*log Analytics olvasói*](../platform/manage-access.md#manage-access-using-azure-permissions) szerepkörhöz tartozó jogosultsággal rendelkező log Analytics-munkaterületnek kell lennie a tárolók Azure monitor.

## <a name="enable-for-an-existing-cluster"></a>Meglévő fürt engedélyezése

A következő lépések végrehajtásával engedélyezheti az Azure Red Hat OpenShift 4-es verziójának és az Azure-ban üzembe helyezett nagyobb fürtnek a figyelését a megadott bash-parancsfájl használatával.

1. Bejelentkezés az Azure-ba

    ```azurecli
    az login
    ```

2. Töltse le és mentse a parancsfájlt egy helyi mappába, amely a következő parancsokkal konfigurálja a fürtöt a figyelési bővítmény használatával:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aroV4/onboarding_azuremonitor_for_containers.sh.`

3. A fürt `oc login` `kubectl config current-context` **Kube-környezetének** azonosításához futtassa a parancsot, és másolja az értéket.

### <a name="integrate-with-an-existing-workspace"></a>Integrálás meglévő munkaterülettel

A következő lépés lehetővé teszi a fürt figyelését a korábban letöltött bash-szkript használatával. Egy meglévő Log Analytics-munkaterülettel való integrációhoz hajtsa végre a következő lépéseket a `workspaceResourceId` paraméterhez szükséges log Analytics munkaterület teljes erőforrás-azonosítójának azonosításához, majd futtassa a parancsot a figyelési bővítmény engedélyezéséhez a megadott munkaterületen. Ha nem rendelkezik a megadható munkaterülettel, ugorjon az 5. lépésre, és hagyja, hogy a szkript hozzon létre egy új munkaterületet.

1. Sorolja fel az összes olyan előfizetést, amelyhez hozzáféréssel rendelkezik a következő parancs használatával:

    ```azurecli
    az account list --all -o table
    ```

    A kimenet a következőhöz hasonló lesz:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    Másolja a **SubscriptionId**értékét.

2. Váltson a Log Analytics munkaterületet üzemeltető előfizetésre a következő paranccsal:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. Az alábbi példa az előfizetésekben lévő munkaterületek listáját jeleníti meg az alapértelmezett JSON-formátumban.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    A kimenetben keresse meg a munkaterület nevét, majd másolja az adott Log Analytics munkaterület teljes erőforrás-AZONOSÍTÓját a mező **azonosítója**alá.

4. Futtassa a következő parancsot a figyelés engedélyezéséhez, és cserélje le a `workspaceResourceId` paraméter értékét: 

    `bash onboarding_azuremonitor_for_containers.sh <kube-context> <azureAroV4ResourceId> <LogAnayticsWorkspaceResourceId>`

    Példa:

    `bash onboarding_azuremonitor_for_containers.sh MyK8sTestCluster /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/test-aro-v4-rg/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/test-aro-v4  /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourcegroups/test-la-workspace-rg/providers/microsoft.operationalinsights/workspaces/test-la-workspace`

A figyelés engedélyezése után körülbelül 15 percet is igénybe vehet, mielőtt megtekintheti a fürthöz tartozó állapot mérőszámait.

### <a name="integrate-with-default-workspace"></a>Integrálás az alapértelmezett munkaterülettel

A következő lépés lehetővé teszi az Azure Red Hat OpenShift v4. x fürt monitorozását a letöltött bash-szkript használatával. Ebben a példában nem kell létrehoznia vagy megadnia egy meglévő munkaterületet. Ez a parancs leegyszerűsíti a folyamatot azáltal, hogy létrehoz egy alapértelmezett munkaterületet a fürt-előfizetés alapértelmezett erőforráscsoporthoz, ha az egyik még nem létezik a régióban. A létrehozott alapértelmezett munkaterület a *alapértelmezettmunkaterület-\<GUID>\<-region>* formátumához hasonlít.  

    `bash onboarding_azuremonitor_for_containers.sh <kube-context> <azureAroV4ResourceId>`

    For example:

    `bash onboarding_azuremonitor_for_containers.sh MyK8sTestCluster /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/test-aro-v4-rg/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/test-aro-v4`

A figyelés engedélyezése után körülbelül 15 percet is igénybe vehet, mielőtt megtekintheti a fürthöz tartozó állapot mérőszámait.

### <a name="from-the-azure-portal"></a>Az Azure Portalról

A tárolók Azure Monitor több fürtből álló nézete kiemeli az Azure Red Hat OpenShift-fürtöket, amelyeken a **nem figyelt fürtök** lapon nincs engedélyezve a figyelés. A fürt melletti **Engedélyezés** lehetőség nem kezdeményezi a figyelés bevezetését a portálról. Ezt a cikket átirányítva manuálisan engedélyezheti a figyelést a jelen cikk korábbi részében ismertetett lépéseket követve.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A Azure Portal menüben vagy a Kezdőlap lapon válassza a **Azure monitor**lehetőséget. Az **áttekintések** szakaszban válassza a **tárolók**lehetőséget.

3. A **figyelő-tárolók** lapon válassza a **nem figyelt fürtök**lehetőséget.

4. A nem figyelt fürtök listájából keresse meg a fürtöt a listában, és kattintson az **Engedélyezés**gombra. A listában szereplő eredmények azonosításához keresse meg az **ARO** értéket a **fürt típusa**oszlopban. Miután rákattintott az **Engedélyezés**gombra, a rendszer átirányítja erre a cikkre.

## <a name="next-steps"></a>További lépések

- Megtudhatja, [hogyan használhatja](container-insights-analyze.md) a Azure monitor for containers szolgáltatást, ha az állapot-és erőforrás-használatot a RedHat OpenShift 4. x-es verziójának és az azokon futó számítási feladatoknak a használatával gyűjti.

- Alapértelmezés szerint a tároló ügynök gyűjti az összes névtérben futó összes tároló StdOut/stderr-tárolójának naplóit, kivéve a Kube rendszert. Ha az adott névtérhez vagy névterekhez tartozó tároló-naplózási gyűjteményt szeretne konfigurálni, tekintse át a Container-elemzések [ügynökének konfigurációját](container-insights-agent-config.md) a kívánt adatgyűjtési beállítások ConfigMap-konfigurációs fájlra való konfigurálásához.

- A Prometheus-metrikák a fürtből való beolvasásához és elemzéséhez tekintse át a [Prometheus-metrikák leselejtezésének konfigurálása](container-insights-prometheus-integration.md)

- Ha meg szeretné tudni, hogyan állíthatja le a fürtöt a tárolók Azure Monitorával, tekintse meg [Az Azure Red Hat OpenShift-fürt figyelésének leállítása](container-insights-optout-openshift.md)című témakört.
