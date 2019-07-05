---
title: Megismerheti a tartalmát, a virtuális gépek naplózása
description: Ismerje meg, hogyan Azure Policy segítségével Rego, és nyissa meg a házirend-ügynök az Azure Kubernetes Service-fürtök kezelése.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: fdb392533e28df1d50e90c842d0117385afb254b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453904"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>Az Azure Policy az Azure Kubernetes Service ismertetése

Az Azure Policy integrálható a [Azure Kubernetes Service](../../../aks/intro-kubernetes.md) ipari méretekben enforcements és védelmének a fürtök a alkalmazni központosított, konzisztens módon-(AKS).
Használatát kiterjesztésével [forgalomirányító](https://github.com/open-policy-agent/gatekeeper), egy _már a betegfelvétel vezérlő webhook_ a [nyissa meg a házirendügynök](https://www.openpolicyagent.org/) (OPA), az Azure Policy lehetővé teszi a kezelése, valamint a megfelelőségi jelentés az Azure-erőforrások és az AKS-fürt egyetlen helyről állapotáról.

> [!NOTE]
> Az aks-ben az Azure Policy korlátozott előzetes verzióban érhető el, és csak a beépített szabályzatdefiníciókat támogatja.

## <a name="overview"></a>Áttekintés

Engedélyezi, és az aks-ben az Azure Policy használata az AKS-fürt, hajtsa végre a következő műveleteket:

- [Feliratkozás az előzetes verziójú funkciók](#opt-in-for-preview)
- [Az Azure Policy-bővítmény telepítése](#installation-steps)
- [Szabályzatdefiníció hozzárendelése az aks-ben](#built-in-policies)
- [Várjon, amíg érvényesítése](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>Feliratkozás az előzetes verzió

Az Azure-házirend bővítmény telepítése, illetve a szolgáltatások bármelyike engedélyezése előtt engedélyeznie kell az előfizetését a **Microsoft.ContainerService** erőforrás-szolgáltató és a **Microsoft.PolicyInsights**erőforrás-szolgáltató, majd jóvá kell hagyni az előzetes verzióra. Csatlakozni az előzetes verzióhoz, kövesse az alábbi lépéseket az Azure Portalon vagy az Azure CLI használatával:

- Az Azure Portalon:

  1. Regisztrálja a **Microsoft.ContainerService** és **Microsoft.PolicyInsights** erőforrás-szolgáltatók. Útmutató: [erőforrás-szolgáltatókat és típusaikat](../../../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

  1. Indítsa el az Azure Policy szolgáltatást az Azure Portalon. Ehhez kattintson a **Minden szolgáltatás** elemre, majd keresse meg és válassza ki a **Szabályzat** elemet.

     ![Az összes szolgáltatás szabályzat keresése](../media/rego-for-aks/search-policy.png)

  1. Válassza ki **csatlakozzon előzetes** az Azure Policy oldal bal oldalán.

     ![Csatlakozás az AKS előzetes verziójának a házirend](../media/rego-for-aks/join-aks-preview.png)

  1. Válassza ki az előfizetést szeretne az előzetes sorát.

  1. Válassza ki a **választható** gombot a felső részén az előfizetések listáját.

- Azure CLI:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Services provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove

  # Feature register: enables the add-on to call the Azure Policy resource provider
  az feature register --namespace Microsoft.PolicyInsights --name AKS-DataplaneAutoApprove
  ```

## <a name="azure-policy-add-on"></a>Az Azure Policy-bővítmény

A _Azure házirend bővítmény_ a Kubernetes az Azure Policy szolgáltatást csatlakozik a forgalomirányító már a betegfelvétel vezérlő. A bővítményt, amely, települ az _azure-szabályzat_ névtér, ír elő a következő funkciókat:

- Az Azure Policy segítségével ellenőrzi az AKS-fürt-hozzárendelések
- Letölti és gyorsítótárazza a szabályzat adatait, például a _rego_ szabályzatdefiníciót, mint **configmaps**
- Az AKS-fürtöt a teljes vizsgálat megfelelőségi ellenőrzés
- Azure Policy biztonsági jelentéseket, naplózást és megfelelőségi részletei

### <a name="installing-the-add-on"></a>A bővítmény telepítése

#### <a name="prerequisites"></a>Előfeltételek

Mielőtt telepíti a bővítményt az AKS-fürt, az előzetes verzió bővítmény kell telepíteni. Ebben a lépésben az Azure CLI használatával történik:

1. Az Azure CLI 2.0.62 verziójára van szükség, vagy később telepített és konfigurált. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

1. Az AKS-fürtöt verziójúnak kell lennie _1.10_ vagy újabb verziója. Az AKS-fürt verziója érvényesítéséhez használja a következő szkriptet:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Verzió telepítése _0.4.0_ az Azure CLI előzetes verzió bővítményt az aks-ben, `aks-preview`:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > Ha korábban telepítette a _aks előzetes_ bővítményt használja, telepítse minden frissítéseit a `az extension update --name aks-preview` parancsot.

#### <a name="installation-steps"></a>Telepítési lépések

Az Előfeltételek követően telepítse az Azure Policy-bővítmény az AKS-fürtöt szeretne kezelni.

- Azure Portal

  1. Indítsa el az Azure Portalon az AKS szolgáltatás kattintva **minden szolgáltatás**, majd keresése és kiválasztása **Kubernetes-szolgáltatás**.

  1. Válasszon egyet az AKS-fürtök.

  1. Válassza ki **házirendek (előzetes verzió)** a Kubernetes szolgáltatás lap bal oldalán.

     ![Az AKS-fürtöt az szabályzatok](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. A fő oldalon válassza ki a **bővítmény engedélyezése** gombra.

     ![Az Azure Policy az AKS-bővítmény engedélyezése](../media/rego-for-aks/enable-policy-add-on.png)

     > [!NOTE]
     > Ha a **bővítmény engedélyezése** gomb szürkén jelenik meg, az előfizetés még nem még lett hozzáadva az előzetes verzióra. Lásd: [választható az előzetes verzió](#opt-in-for-preview) a szükséges lépéseket.

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>Érvényesítési és jelentések gyakorisága

A bővítmény bejelentkezik az Azure Policy a módosításokat a szabályzat-hozzárendelések 5 percenként. A frissítési ciklus során a bővítmény eltávolítja az összes _configmaps_ a a _azure-szabályzat_ névtér majd újra létrehozza a _configmaps_ forgalomirányító használatra.

> [!NOTE]
> Bár egy _felügyeleti fürt_ előfordulhat, hogy rendelkezik engedéllyel a _azure-szabályzat_ névtér, nem ajánlott, vagy módosíthatja a névtér támogatott. A frissítési ciklus során végzett manuális módosítások elvesznek.

5 percenként, a bővítményt meghívja a fürt teljes vizsgálat. Után összegyűjtése a teljes vizsgálat és a valós idejű értékelések által megkísérelt változások forgalomirányító a fürthöz, a bővítmény jelentést az eredményekről vissza az Azure Policy való részvétel [megfelelőségi részletek](../how-to/get-compliance-data.md) , mint bármely Azure Policy hozzárendelés. Csak aktív szabályzat-hozzárendelések eredményeit a rendszer a naplózási ciklus során adja vissza.

## <a name="policy-language"></a>Házirend-nyelve

Az Azure Policy nyelvi struktúra az AKS kezeléséhez következik, hogy a meglévő szabályzatokat. A hatás _EnforceRegoPolicy_ az AKS-fürtök kezelésére szolgál, és tart _részletek_ OPA és a forgalomirányító jellemző tulajdonságokat. Részletek és példák: a [EnforceRegoPolicy](effects.md#enforceregopolicy) érvénybe.

Részeként a _details.policy_ szabályzatdefinícióban, az Azure Policy tulajdonság a bővítmény URI-ját rego házirend továbbítja. Rego a nyelvet támogató OPA és a forgalomirányító ellenőrzésére és a Kubernetes-fürt kérést mutálódni. Egy meglévő standard Kubernetes Management támogatásával az Azure Policy lehetővé teszi újból felhasználhatja a meglévő szabályok és a egy egységes felhő megfelelőségi jelentéskészítési funkciók az Azure Policyvel párosítsa őket. További információkért lásd: [Rego mi?](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego).

## <a name="built-in-policies"></a>Beépített szabályzatok

Keresse meg a beépített szabályzatokat az Azure portal használatával AKS kezelésére szolgáló, kövesse az alábbi lépéseket:

1. Indítsa el az Azure Policy szolgáltatást az Azure Portalon. Válassza ki **minden szolgáltatás** a bal oldali panelen, és ezután keresse meg és válassza ki a **házirend**.

1. Az Azure Policy oldalára bal oldali panelén válassza **definíciók**.

1. A kategória legördülő listában, használjon **válassza ki az összes** törölheti a szűrőt, és jelölje ki **Kubernetes-szolgáltatást**.

1. Válassza ki a szabályzat-definíció, majd válassza ki a **hozzárendelése** gombra.

> [!NOTE]
> Az AKS-definíciót, az Azure Policy hozzárendelésekor a **hatókör** tartalmaznia kell az AKS-fürt erőforrás.

Azt is megteheti, használja a [szabályzat hozzárendelése – portál](../assign-policy-portal.md) gyors útmutató: keresse meg és a egy AKS-szabályzat hozzárendelése. Keresse meg a minta "virtuális gépek naplózása" helyett egy Kubernetes-definíció.

## <a name="logging"></a>Naplózás

### <a name="azure-policy-add-on-logs"></a>Szabályzat-bővítmény az Azure naplói

A Kubernetes tartományvezérlő/tároló, mint az Azure Policy-bővítmény tartja naplók az AKS-fürtöt. A naplók a érhetők el a **Insights** az AKS-fürt lapján. További információkért lásd: [AKS ismertetése a fürt-tárolókhoz az Azure Monitor szolgáltatással teljesítmény](../../../azure-monitor/insights/container-insights-analyze.md).

### <a name="gatekeeper-logs"></a>Forgalomirányító-naplók

Az új erőforrás-kéréseket a forgalomirányító naplók engedélyezéséhez kövesse [engedélyezze, és tekintse át a fő csomópont naplózza az aks-ben a Kubernetes](../../../aks/view-master-logs.md).
Itt láthat egy példalekérdezést, elutasított események új erőforrás-kérelmek megtekintése:

```kusto
| where Category == "kube-audit"
| where log_s contains "admission webhook"
| limit 100
```

A forgalomirányító tárolókból naplók megtekintéséhez kövesse [engedélyezze, és tekintse át a fő csomópont naplózza az aks-ben a Kubernetes](../../../aks/view-master-logs.md) , és ellenőrizze a _kube-apiserver_ beállítást a **Diagnosztikaibeállítások** ablaktáblán.

## <a name="remove-the-add-on"></a>Távolítsa el a bővítményt

Az Azure Portalon vagy az Azure CLI használatával az AKS-fürt az Azure Policy-bővítmény eltávolításához:

- Azure Portal

  1. Indítsa el az Azure Portalon az AKS szolgáltatás kattintva **minden szolgáltatás**, majd keresése és kiválasztása **Kubernetes-szolgáltatás**.

  1. Válassza ki az AKS-fürt, ahol szeretné az Azure Policy-bővítmény letiltása.

  1. Válassza ki **házirendek (előzetes verzió)** a Kubernetes szolgáltatás lap bal oldalán.

     ![Az AKS-fürtöt az szabályzatok](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. A fő oldalon válassza ki a **bővítmény letiltását** gombra.

     ![Az Azure Policy az AKS-bővítmény letiltása](../media/rego-for-aks/disable-policy-add-on.png)

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

## <a name="next-steps"></a>További lépések

- Tekintse át a következő példák [Azure Policy minták](../samples/index.md).
- A [Szabályzatdefiníciók struktúrájának](definition-structure.md) áttekintése.
- A [Szabályzatok hatásainak ismertetése](effects.md).
- Megismerheti, hogyan [szabályzatok létrehozása programozott módon](../how-to/programmatically-create.md).
- Ismerje meg, hogyan [megfelelőségi adatok](../how-to/getting-compliance-data.md).
- Ismerje meg, hogyan [javítani a nem megfelelő erőforrások](../how-to/remediate-resources.md).
- Tekintse át a felügyeleti csoport van [az erőforrások rendszerezéséhez az Azure felügyeleti csoportok](../../management-groups/index.md).