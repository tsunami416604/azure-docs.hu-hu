---
title: A tárolók Azure Monitorának frissítése a metrikák számára | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan frissítheti Azure Monitor a tárolók számára az összesített metrikák vizsgálatát és riasztását támogató egyéni metrikák funkció engedélyezéséhez.
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: d299fc5e6b0c41188fac1fa19bb66387263c12e9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84298261"
---
# <a name="how-to-update-azure-monitor-for-containers-to-enable-metrics"></a>A tárolókhoz készült Azure Monitor frissítése a metrikák engedélyezéséhez

A tárolók Azure Monitor támogatja a metrikák gyűjtését az Azure Kubernetes Services (ak) fürtök csomópontjairól és a hüvelyekről, és azokat a Azure Monitor metrikák tárolójába írja. Ennek a változásnak a célja, hogy jobb ütemezést nyújtson az összesített számítások (AVG, Darabszám, max. min.) bemutatása a teljesítménymutatók esetében, támogatja a teljesítmény-diagramok Azure Portal-irányítópultokon való rögzítését, valamint a metrikai riasztások támogatását.

>[!NOTE]
>Ez a funkció jelenleg nem támogatja az Azure Red Hat OpenShift-fürtöket.
>

A szolgáltatás részeként a következő metrikák engedélyezettek:

| Metrikai névtér | Metric | Leírás |
|------------------|--------|-------------|
| bepillantást nyerhet. tároló/csomópontok | cpuUsageMillicores, cpuUsagePercentage, memoryRssBytes, memoryRssPercentage, memoryWorkingSetBytes, memoryWorkingSetPercentage, nodesCount | Ezek a *csomópont* -metrikák, és a *gazdagép* dimenzióként is szerepelnek, és a<br> a csomópont neve a *gazdagép* dimenziójának értékeként. |
| bepillantást nyerhet. tároló/hüvely | podCount | Ezek a *Pod* mérőszámok, és a következő dimenziókat tartalmazzák: ControllerName, Kubernetes névtér, név, fázis. |

A fürt frissítése az új képességek támogatásához a Azure Portal, Azure PowerShell vagy az Azure CLI használatával végezhető el. A Azure PowerShell és a CLI használatával engedélyezheti ezt a fürtön vagy az előfizetésben lévő összes fürtön. Az AK új üzemelő példányai automatikusan tartalmazzák ezt a konfigurációs változást és képességet.

Bármelyik folyamat hozzárendeli a figyelési **metrika közzétevői** szerepkörét a fürt egyszerű vagy felhasználó által hozzárendelt MSI-fiókjához a figyelési bővítményhez, így az ügynök által gyűjtött adatok közzétehetők a fürtök erőforrásában. A monitorozási metrikák közzétevője csak az erőforráshoz tartozó mérőszámok leküldésére jogosult, nem változtathat meg semmilyen állapotot, nem frissítheti az erőforrást, illetve nem olvashatja el az adatokat. További információ a szerepkörről: a [metrikák közzétevői szerepkörének figyelése](../../role-based-access-control/built-in-roles.md#monitoring-metrics-publisher).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, erősítse meg a következőket:

* Az egyéni metrikák csak az Azure-régiók egy részhalmazában érhetők el. A támogatott régiók listáját [itt](../platform/metrics-custom-overview.md#supported-regions)dokumentáljuk.
* Az AK-fürterőforrás **[tulajdonosi](../../role-based-access-control/built-in-roles.md#owner)** szerepkörének tagja, hogy engedélyezze a Node és a pod egyéni teljesítmény-mérőszámok gyűjteményét. 

Ha úgy dönt, hogy az Azure CLI-t használja, először telepítenie és használnia kell a CLI-t helyileg. Az Azure CLI 2.0.59 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a parancsot `az --version` . Ha telepítenie vagy frissítenie kell az Azure CLI-t, tekintse meg [Az Azure CLI telepítését](https://docs.microsoft.com/cli/azure/install-azure-cli)ismertető témakört. 

## <a name="upgrade-a-cluster-from-the-azure-portal"></a>Fürt frissítése a Azure Portal

A tárolók Azure Monitor által figyelt meglévő AK-fürtök esetében, **Miután kiválasztotta** a fürtöt, hogy az állapotát a többfürtes nézetből Azure monitor vagy közvetlenül a fürtből tekintse meg, a bal oldali ablaktáblán található elemzések megadásával tekintse meg a portál felső részén látható szalagcímet.

![Az AK-fürt szalagcímének frissítése Azure Portal](./media/container-insights-update-metrics/portal-banner-enable-01.png)

Ha az **Engedélyezés** gombra kattint, a rendszer elindítja a fürt frissítésének folyamatát. Ez a folyamat több másodpercig is eltarthat, és a menü értesítések részén nyomon követheti a folyamat állapotát.

## <a name="upgrade-all-clusters-using-bash-in-azure-command-shell"></a>Az összes fürt frissítése a bash használatával az Azure Command shellben

A következő lépésekkel frissítheti az előfizetésben lévő összes fürtöt a bash használatával az Azure Command shellben.

1. Futtassa az alábbi parancsot az Azure CLI használatával.  Szerkessze a **subscriptionId** értékét az AK-fürt **AK-áttekintés** oldalának értékével.

    ```azurecli
    az login
    az account set --subscription "Subscription Name"
    curl -sL https://aka.ms/ci-md-onboard-atscale | bash -s subscriptionId   
    ```

    A konfiguráció módosítása néhány másodpercig is eltarthat. Ha elkészült, egy üzenet jelenik meg, amely a következőhöz hasonló, és az eredményt tartalmazza:

    ```azurecli
    completed role assignments for all AKS clusters in subscription: <subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-cli"></a>Frissítés egy fürtön az Azure CLI használatával

A következő lépésekkel frissítheti az előfizetéséhez tartozó adott fürtöt az Azure CLI használatával.

1. Futtassa az alábbi parancsot az Azure CLI használatával. Szerkessze a **subscriptionId**, a **ResourceGroupName**és a **clusterName** értékeit az AK-fürt **AK-áttekintés** lapján található értékek használatával.  A **clientIdOfSPN**értékének lekéréséhez a parancs az `az aks show` alábbi példában látható módon fog visszakerülni.

    ```azurecli
    az login
    az account set --subscription "<subscriptionName>"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPN> --scope <clusterResourceId> --role "Monitoring Metrics Publisher" 
    ```

    A **clientIdOfSPNOrMsi**értékének lekéréséhez futtassa a parancsot az `az aks show` alábbi példában látható módon. Ha a **servicePrincipalProfile** objektum érvényes *ClientID* -értékkel rendelkezik, ezt használhatja. Ellenkező esetben, ha az *MSI*értékre van állítva, át kell adnia a ClientID a következőből: `addonProfiles.omsagent.identity.clientId` .

    ```azurecli
    az login
    az account set --subscription "<subscriptionName>"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPNOrMsi> --scope <clusterResourceId> --role "Monitoring Metrics Publisher"
    ```

## <a name="upgrade-all-clusters-using-azure-powershell"></a>Az összes fürt frissítése Azure PowerShell használatával

A következő lépések végrehajtásával frissítheti az előfizetésben lévő összes fürtöt a Azure PowerShell használatával.

1. [Töltse le](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/docs/aks/mdmonboarding/mdm_onboarding_atscale.ps1) a **mdm_onboarding_atscale.ps1** -szkriptet, és mentse egy helyi mappába a GitHub-adattárból.
2. Futtassa a következő parancsot a Azure PowerShell használatával.  Szerkessze a **subscriptionId** értékét az AK-fürt **AK-áttekintés** oldalának értékével.

    ```powershell
    .\mdm_onboarding_atscale.ps1 subscriptionId
    ```
    A konfiguráció módosítása néhány másodpercig is eltarthat. Ha elkészült, egy üzenet jelenik meg, amely a következőhöz hasonló, és az eredményt tartalmazza:

    ```powershell
    Completed adding role assignment for the aks clusters in subscriptionId :<subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-powershell"></a>Frissítés egy fürtön Azure PowerShell használatával

A következő lépések végrehajtásával frissítheti egy adott fürtöt a Azure PowerShell használatával.

1. [Töltse le](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/docs/aks/mdmonboarding/mdm_onboarding.ps1) a **mdm_onboarding.ps1** -szkriptet, és mentse egy helyi mappába a GitHub-adattárból.

2. Futtassa a következő parancsot a Azure PowerShell használatával. Szerkessze a **subscriptionId**, a **ResourceGroupName**és a **clusterName** értékeit az AK-fürt **AK-áttekintés** lapján található értékek használatával.

    ```powershell
    .\mdm_onboarding.ps1 subscriptionId <subscriptionId> resourceGroupName <resourceGroupName> clusterName <clusterName>
    ```

    A konfiguráció módosítása néhány másodpercig is eltarthat. Ha elkészült, egy üzenet jelenik meg, amely a következőhöz hasonló, és az eredményt tartalmazza:

    ```powershell
    Successfully added Monitoring Metrics Publisher role assignment to cluster : <clusterName>
    ```

## <a name="verify-update"></a>Frissítés ellenőrzése

Miután a korábban ismertetett módszerek valamelyikével kezdeményezte a frissítést, használhatja Azure Monitor metrikák Explorert, és ellenőrizheti, hogy a **metrikai névtér** tartalmazza-e az **észlelt** adatokat. Ha igen, ez azt jelzi, hogy mehet előre, és megkezdheti a [metrikus riasztások](../platform/alerts-metric.md) beállítását, illetve a diagramok [irányítópultokon](../../azure-portal/azure-portal-dashboards.md)való rögzítését.  
