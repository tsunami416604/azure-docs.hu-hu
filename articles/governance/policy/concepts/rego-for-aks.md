---
title: Az Azure Kubernetes Service Azure Policy megismerése
description: Megtudhatja, hogyan kezeli a Azure Policy a Rego és a nyílt házirend-ügynököt a fürtök Azure Kubernetes szolgáltatásban való kezeléséhez.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: dbac1ee762066fb59cd57c04839666026a2bd89b
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73959796"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>Az Azure Kubernetes Service Azure Policy megismerése

A Azure Policy integrálható az [Azure Kubernetes szolgáltatással](../../../aks/intro-kubernetes.md) (ak), hogy központosított, konzisztens módon alkalmazza a fürtökön a kikényszerítéseket és a védelmet.
Ha kiterjeszti a [forgalomirányító](https://github.com/open-policy-agent/gatekeeper/tree/master/deprecated) v2-t, az [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) _beléptetési vezérlő webhooka_ , Azure Policy lehetővé teszi az Azure-erőforrások és az AK-fürtök megfelelőségi állapotának kezelését és jelentését egy helyről.

> [!NOTE]
> Az AK-hoz készült Azure Policy korlátozott előzetes verzióban érhető el, és csak a beépített szabályzat-definíciókat támogatja.

## <a name="overview"></a>Áttekintés

A következő műveletek végrehajtásával engedélyezheti és használhatja a Azure Policy for AK-t az AK-fürttel:

- [Az előzetes verzió funkcióinak engedélyezése](#opt-in-for-preview)
- [A Azure Policy bővítmény telepítése](#installation-steps)
- [Házirend-definíció társítása az AK-hoz](#built-in-policies)
- [Várakozás az ellenőrzésre](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>Előzetes verzióra való feliratkozás

A Azure Policy bővítmény telepítése vagy a szolgáltatás bármely funkciójának engedélyezése előtt az előfizetésnek engedélyeznie kell a **Microsoft. tárolószolgáltatás** erőforrás-szolgáltatót és a **Microsoft. PolicyInsights** erőforrás-szolgáltatót, majd jóvá kell hagynia a következőt: csatlakozzon az előzetes verzióhoz. Az előzetes verzióhoz való csatlakozáshoz kövesse az alábbi lépéseket a Azure Portal vagy az Azure CLI használatával:

- Azure Portal:

  1. Regisztrálja a **Microsoft. tárolószolgáltatás** és a **Microsoft. PolicyInsights** erőforrás-szolgáltatókat. A lépéseket lásd: [erőforrás-szolgáltatók és típusok](../../../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

  1. Indítsa el az Azure Policy szolgáltatást az Azure Portalon. Ehhez kattintson a **Minden szolgáltatás** elemre, majd keresse meg és válassza ki a **Szabályzat** elemet.

     ![Szabályzat keresése az összes szolgáltatásban](../media/rego-for-aks/search-policy.png)

  1. A Azure Policy lap bal oldalán válassza a **Csatlakozás előnézete** elemet.

     ![Csatlakozás a Szabályzathoz az AK előzetes verziójához](../media/rego-for-aks/join-aks-preview.png)

  1. Válassza ki az előfizetéshez hozzáadni kívánt előfizetés sorát.

  1. Válassza az előfizetések listájának tetején található **opt-in** gombot.

- Azure CLI:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Services provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzurePolicyAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.ContainerService
  
  # Feature register: enables the add-on to call the Azure Policy resource provider
  az feature register --namespace Microsoft.PolicyInsights --name AKS-DataplaneAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.PolicyInsights/AKS-DataPlaneAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.PolicyInsights
  
  ```

## <a name="azure-policy-add-on"></a>Azure Policy bővítmény

A Kubernetes _Azure Policy-bővítménye_ csatlakoztatja a Azure Policy szolgáltatást a forgalomirányító belépésvezérlés szolgáltatáshoz. Az _Azure-Policy_ névtérbe telepített bővítmény a következő funkciókat hozza létre:

- Az AK-fürthöz való hozzárendelések Azure Policy ellenőrzése
- Letölti és gyorsítótárazza a szabályzat részleteit, beleértve a _Rego_ szabályzat definícióját, mint a **configmaps** .
- Teljes ellenőrzési megfelelőségi ellenőrzés futtatása az AK-fürtön
- A naplózási és megfelelőségi adatok visszaállítása a Azure Policy

### <a name="installing-the-add-on"></a>A bővítmény telepítése

#### <a name="prerequisites"></a>Előfeltételek

Mielőtt telepítené a bővítményt az AK-fürtben, telepítenie kell az előnézeti bővítményt. Ez a lépés az Azure CLI-vel történik:

1. Szüksége lesz az Azure CLI-verzió 2.0.62 vagy újabb verziójára, és konfigurálva van. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

1. Az AK-fürt _1,10_ -es vagy újabb verziójának kell lennie. A következő parancsfájl használatával ellenőrizze az AK-fürt verzióját:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Telepítse az Azure CLI előzetes verziójának _0.4.0_ verzióját az AK-hoz, `aks-preview`:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > Ha korábban már telepítette az _AK-előnézeti_ bővítményt, telepítse a frissítéseket a `az extension update --name aks-preview` parancs használatával.

#### <a name="installation-steps"></a>Telepítési lépések

Az előfeltételek elvégzése után telepítse a Azure Policy-bővítményt a felügyelni kívánt AK-fürtben.

- Azure Portal

  1. Indítsa el az AK szolgáltatást a Azure Portal a **minden szolgáltatás**elemre kattintva, majd keresse meg és válassza ki a **Kubernetes Services szolgáltatást**.

  1. Válasszon egy AK-beli fürtöt.

  1. A Kubernetes szolgáltatás oldalának bal oldalán válassza a **házirendek (előzetes verzió)** lehetőséget.

     ![Szabályzatok az AK-fürtből](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. A Főoldalon kattintson a **bővítmény engedélyezése** gombra.

     ![A Azure Policy engedélyezése az AK-bővítményhez](../media/rego-for-aks/enable-policy-add-on.png)

     > [!NOTE]
     > Ha a **bővítmény engedélyezése** gomb szürkén jelenik meg, az előfizetés még nem lett hozzáadva az előzetes verzióhoz. A szükséges lépések megtekintéséhez tekintse meg a következőt: [opt-in (előzetes](#opt-in-for-preview) verzió).

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>Ellenőrzési és jelentéskészítési gyakoriság

A bővítmény 5 percenként ellenőrzi, hogy van-e Azure Policy a házirend-hozzárendelések változásaihoz. A frissítési ciklus során a bővítmény eltávolítja az összes _configmaps_ az _Azure-Policy_ névtérben, majd újból létrehozza a _configmaps_ a forgalomirányító általi használatra.

> [!NOTE]
> Amíg a _fürt rendszergazdája_ jogosult az _Azure-Policy_ névtérre, a névtér módosítása nem ajánlott vagy nem támogatott. Az elvégzett manuális módosítások elvesznek a frissítési ciklus során.

A bővítmény 5 percenként meghívja a fürt teljes vizsgálatát. Miután összegyűjtötte a teljes vizsgálat részleteit és minden valós idejű értékelést a fürtön történt megkísérelt módosításokat, a bővítmény jelentést készít az eredményről Azure Policy a [megfelelőségi adatokba](../how-to/get-compliance-data.md) , például bármely Azure Policy hozzárendelésbe való felvételhez. A naplózási ciklusban csak az aktív házirend-hozzárendelések eredményeit adja vissza a rendszer.

## <a name="policy-language"></a>Házirend nyelve

Az AK kezelésének Azure Policy nyelvi szerkezete a meglévő szabályzatokat követi. A _EnforceRegoPolicy_ hatására a rendszer az AK-fürtök kezelésére szolgál, és az OPA és a forgalomirányító v2 használatára vonatkozó _részletes_ tulajdonságokat is felhasználja. További részletekért és példákért tekintse meg a [EnforceRegoPolicy](effects.md#enforceregopolicy) hatást.

A _details. Policy_ tulajdonság részeként Azure Policy átadja egy Rego-házirend URI-ját a bővítménynek. A Rego az a nyelv, amelyet az OPA és a forgalomirányító támogat a Kubernetes-fürtre irányuló kérelem érvényesítéséhez vagy átváltoztatásához. A Kubernetes-kezelés meglévő szabványának támogatásával a Azure Policy lehetővé teszi a meglévő szabályok újbóli használatát, és azok párosítását Azure Policy egy egységes Felhőbeli megfelelőségi jelentéskészítési élmény érdekében. További információ: [What is Rego?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego).

## <a name="built-in-policies"></a>Beépített szabályzatok

A következő lépésekkel megkeresheti az ak-nak a Azure Portal használatával történő felügyeletéhez szükséges beépített szabályzatokat:

1. Indítsa el a Azure Policy szolgáltatást a Azure Portal. Válassza a **minden szolgáltatás** lehetőséget a bal oldali ablaktáblán, majd keresse meg és válassza ki a **házirend**elemet.

1. A Azure Policy lap bal oldali ablaktábláján válassza a **definíciók**lehetőséget.

1. A kategória legördülő listából válassza az **összes kijelölése lehetőséget** a szűrő törléséhez, majd válassza a **Kubernetes szolgáltatás**lehetőséget.

1. Válassza ki a házirend-definíciót, majd kattintson a **hozzárendelés** gombra.

> [!NOTE]
> Az AK-definíciók Azure Policyjának kiosztásakor a **hatókörnek** tartalmaznia kell az AK fürterőforrás-erőforrást.

Másik lehetőségként használja a [házirend társítása – portál](../assign-policy-portal.md) rövid útmutatóját egy AK-szabályzat megkereséséhez és hozzárendeléséhez. Keressen egy Kubernetes házirend-definíciót a "naplózási virtuális gépek" minta helyett.

> [!IMPORTANT]
> A beépített szabályzatok a **Kubernetes szolgáltatásban** csak az AK-val használhatók.

## <a name="logging"></a>Naplózás

### <a name="azure-policy-add-on-logs"></a>Azure Policy kiegészítő naplók

Kubernetes-vezérlőként/tárolóként a Azure Policy bővítmény megtartja a naplókat az AK-fürtben. A naplók elérhetők az AK-fürt elemzések **lapján.** További információ: az [AK-fürt teljesítményének megismerése a tárolók Azure Monitorával](../../../azure-monitor/insights/container-insights-analyze.md).

### <a name="gatekeeper-logs"></a>Forgalomirányító-naplók

Az új erőforrás-kérelmekhez tartozó forgalomirányító-naplók engedélyezéséhez kövesse az [Kubernetes fő csomópontjának naplók engedélyezése és áttekintése az AK-ban](../../../aks/view-master-logs.md)című témakör lépéseit.
Íme egy példa az új erőforrás-kérelmek megtagadási eseményeinek megtekintésére:

```kusto
| where Category == "kube-audit"
| where log_s contains "admission webhook"
| limit 100
```

Ha a forgalomirányító tárolók naplóit szeretné megtekinteni, hajtsa végre a [Kubernetes fő csomópontjának naplók engedélyezése és áttekintése az AK-ban](../../../aks/view-master-logs.md) című témakör lépéseit, és ellenőrizze a **diagnosztikai beállítások** ablaktábla _Kube-apiserver_ beállítását.

## <a name="remove-the-add-on"></a>A bővítmény eltávolítása

Ha el szeretné távolítani a Azure Policy-bővítményt az AK-fürtből, használja a Azure Portal vagy az Azure CLI-t:

- Azure Portal

  1. Indítsa el az AK szolgáltatást a Azure Portal a **minden szolgáltatás**elemre kattintva, majd keresse meg és válassza ki a **Kubernetes Services szolgáltatást**.

  1. Válassza ki az AK-fürtöt, ahol le szeretné tiltani a Azure Policy-bővítményt.

  1. A Kubernetes szolgáltatás oldalának bal oldalán válassza a **házirendek (előzetes verzió)** lehetőséget.

     ![Szabályzatok az AK-fürtből](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. A Főoldalon kattintson a **bővítmény letiltása** gombra.

     ![Az AK-bővítmény Azure Policyának letiltása](../media/rego-for-aks/disable-policy-add-on.png)

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Azure Policy bővítmény által gyűjtött diagnosztikai adatok

A Kubernetes Azure Policy-bővítménye korlátozott számú fürt diagnosztikai adatokat gyűjt. A diagnosztikai adat a szoftverrel és teljesítménnyel kapcsolatos létfontosságú technikai jellegű adat. A következő módokon használható:

- Azure Policy bővítmény naprakészen tartása
- Azure Policy-bővítmény biztonságos, megbízható, teljesítménybeli megőrzése
- A bővítmény használatának összesített elemzésével Azure Policy kiegészítő bővítmények továbbfejlesztése

A bővítmény által gyűjtött információk nem személyes adatok. A rendszer jelenleg a következő adatokat gyűjti össze:

- Azure Policy bővítmény ügynökének verziója
- Fürttípus
- Fürt régiója
- Fürterőforrás-csoport
- Fürterőforrás-azonosító
- Fürt-előfizetés azonosítója
- Fürt operációs rendszer (példa: Linux)
- Fürt városa (példa: Seattle)
- Fürt állapota vagy tartománya (példa: Washington)
- Fürt országa vagy régiója (példa: Egyesült Államok)
- Az ügynök telepítése során Azure Policy bővítmény által észlelt kivételek/hibák a szabályzat kiértékelése során
- Azure Policy bővítmény által nem telepített forgalomirányító-házirendek száma

## <a name="next-steps"></a>Következő lépések

- Tekintse át a példákat [Azure Policy mintákon](../samples/index.md).
- A [Szabályzatdefiníciók struktúrájának](definition-structure.md) áttekintése.
- A [Szabályzatok hatásainak ismertetése](effects.md).
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](../how-to/programmatically-create.md).
- Ismerje meg, hogyan [kérheti le a megfelelőségi információkat](../how-to/getting-compliance-data.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](../how-to/remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).