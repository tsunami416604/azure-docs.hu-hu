---
title: Ismerje meg az Azure Kubernetes szolgáltatáshoz tartozó Azure-szabályzatot
description: Ismerje meg, hogy az Azure Policy hogyan használja a Rego és a Open Policy Agent fürtök et az Azure Kubernetes szolgáltatásban.
ms.date: 03/27/2020
ms.topic: conceptual
ms.openlocfilehash: d77c5cf94a8239f4617e563961cbe1cc40e48fe0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372658"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>Az Azure Kubernetes Service szolgáltatáshoz készült Azure Policy ismertetése

Az Azure Policy integrálható az [Azure Kubernetes szolgáltatás](../../../aks/intro-kubernetes.md) (AKS) a fürtön alkalmazott, nagy léptékű kényszerítések és biztonsági intézkedések központi, konzisztens módon.
A [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3, [a nyílt házirend-ügynök](https://www.openpolicyagent.org/) (OPA) _belépési vezérlő webhook-_ használatának kiterjesztésével az Azure-szabályzat lehetővé teszi az Azure-erőforrások és az AKS-fürtök megfelelőségi állapotának kezelését és jelentését egy helyről.

> [!IMPORTANT]
> Az Azure Policy for AKS előzetes verzióban érhető el, és csak a beépített szabályzatdefiníciókat támogatja. A beépített házirendek a **Kubernetes** kategóriában találhatók. Az **EnforceRegoPolicy** hatás és a kapcsolódó **Kubernetes szolgáltatás** kategóriaházirendek _elavultak._ Ehelyett használja a frissített [EnforceOPAConstraint](./effects.md#enforceopaconstraint) hatást.

> [!WARNING]
> Ez a funkció még nem érhető el minden régióban. A bevezetés állapotáról az [AKS-problémák – A házirendbővítmény módosítása című témakörben van.](https://github.com/Azure/AKS/issues/1529)

## <a name="overview"></a>Áttekintés

Az AKS-fürtdel való Azure Policy for AKS engedélyezéséhez és használatához tegye a következő műveleteket:

- [Feliratkozás az előzetes verziójú funkciókra](#opt-in-for-preview)
- [Az Azure Policy bővítmény telepítése](#installation-steps)
- [Házirend-definíció hozzárendelése az AKS-hez](#built-in-policies)
- [Várakozás az ellenőrzésre](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>Feliratkozás az előzetes verzióhoz

Az Azure Policy bővítmény telepítése vagy a szolgáltatási szolgáltatások bármelyikének engedélyezése előtt az előfizetésnek engedélyeznie kell a **Microsoft.ContainerService** erőforrás-szolgáltatót és a **Microsoft.PolicyInsights** erőforrás-szolgáltatót, majd jóvá kell hagynia az előzetes verzióhoz való csatlakozást. Az előzetes verzióhoz való csatlakozáshoz kövesse az alábbi lépéseket az Azure Portalon vagy az Azure CLI-vel:

- Azure-portál:

  1. Regisztrálja a **Microsoft.ContainerService** és a **Microsoft.PolicyInsights** erőforrás-szolgáltatókat. Lépések: [Erőforrás-szolgáltatók és -típusok](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

  1. Indítsa el az Azure Policy szolgáltatást az Azure Portalon. Ehhez kattintson a **Minden szolgáltatás** elemre, majd keresse meg, és válassza ki a **Szabályzat** elemet.

     ![Házirend keresése minden szolgáltatásban](../media/rego-for-aks/search-policy.png)

  1. Válassza a **Join Preview lehetőséget** az Azure Policy lap bal oldalán.

     ![Csatlakozzon az AKS előzetes verziószabályzatához](../media/rego-for-aks/join-aks-preview.png)

  1. Jelölje ki az előzetes verzióhoz hozzáadni kívánt előfizetés sorát.

  1. Válassza a **Feliratkozás** gombot az előfizetések listájának tetején.

- Azure CLI:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Service provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzurePolicyAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.ContainerService
  
  ```

## <a name="azure-policy-add-on"></a>Azure-szabályzat bővítmény

A Kubernetes _Azure Policy bővítménye_ az Azure Policy szolgáltatást a Gatekeeper belépési vezérlőhöz kapcsolja. A _kube-system_ névtérbe telepített bővítmény a következő funkciókat adja meg:

- Ellenőrzi az Azure Policy szolgáltatás hozzárendeléseit a fürthöz.
- A fürtben lévő házirendeket [kényszersablonként](https://github.com/open-policy-agent/gatekeeper#constraint-templates) és egyéni erőforrások [megkötéseként](https://github.com/open-policy-agent/gatekeeper#constraints) telepíti.
- Jelentések naplózási és megfelelőségi részleteket vissza az Azure Policy szolgáltatás.

### <a name="installing-the-add-on"></a>A bővítmény telepítése

#### <a name="prerequisites"></a>Előfeltételek

Mielőtt telepíti a bővítményt az AKS-fürtbe, telepítenie kell az előnézeti bővítményt. Ez a lépés az Azure CLI-vel történik:

1. Ha a Gatekeeper v2 házirendek telepítve vannak, távolítsa el a bővítményt az AKS-fürt **Letiltás** gombjával a **Házirendek (előzetes verzió)** lap alatt.

1. Az Azure CLI 2.0.62-es vagy újabb verziójára van szükség telepítve és konfigurálva. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

1. Az AKS-fürtnek _legalább 1.14-es_ verziónak kell lennie. Az AKS-fürt verziójának érvényesítéséhez használja a következő parancsfájlt:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Telepítse az Azure CLI előzetes bővítmény _0.4.0-s_ verzióját az AKS-hez, `aks-preview`:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > Ha korábban már telepítette az _aks-preview_ bővítményt, telepítse a frissítéseket a `az extension update --name aks-preview` paranccsal.

#### <a name="installation-steps"></a>A telepítés lépései

Miután az előfeltételek befejeződtek, telepítse az Azure Policy bővítményt a kezelni kívánt AKS-fürtbe.

- Azure portál

  1. Indítsa el az AKS-szolgáltatást az Azure Portalon a **Minden szolgáltatás**elemre kattintva, majd keresse meg és válassza a **Kubernetes-szolgáltatásokat.**

  1. Válassza ki az egyik AKS-fürtöt.

  1. Válassza a **Szabályzatok (előzetes verzió)** lehetőséget a Kubernetes szolgáltatáslap bal oldalán.

     ![Az AKS-fürt házirendjei](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. A főlapon válassza a **Bővítmény engedélyezése** gombot.

     ![Az Azure-szabályzat engedélyezése az AKS-bővítményhez](../media/rego-for-aks/enable-policy-add-on.png)

     > [!NOTE]
     > Ha a **Bővítmény engedélyezése** gomb szürkén jelenik meg, az előfizetés még nem lett hozzáadva az előnézethez. A szükséges [lépésekről az Opt-in előzetes verzióban](#opt-in-for-preview) tekintheti meg a szükséges lépéseket. Ha elérhető a **Letiltás** gomb, a Gatekeeper v2 továbbra is telepítve van, és el kell távolítani.

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>Validálási és jelentéstételi gyakoriság

A bővítmény 15 percenként ellenőrzi az Azure Policy szolgáltatással a házirend-hozzárendelések változásait.
Ebben a frissítési ciklusban a bővítmény ellenőrzi a módosításokat. Ezek a változások eseményindító hoz létre, frissítések, vagy törli a megkötés sablonok és korlátozások.

> [!NOTE]
> Bár a fürtrendszergazdának engedélye lehet a megkötési sablonok és megkötési erőforrások létrehozására és frissítésére, ezek nem támogatottak, mivel a manuális frissítések felülíródnak.

15 percenként a bővítmény a fürt teljes vizsgálatát kéri. Miután összegyűjtötte a teljes vizsgálat részleteit, és a Gatekeeper által a fürt önmegkísérelt módosításainak valós idejű kiértékeléseit, a bővítmény visszajelenti az eredményeket az Azure Policy szolgáltatásnak, hogy tartalmazza a [megfelelőségi adatokat,](../how-to/get-compliance-data.md#portal) például bármely Azure Policy-hozzárendelést. A rendszer csak az aktív házirend-hozzárendelések eredményeit adja vissza a naplózási ciklus során. A naplózási eredmények a sikertelen megkötés állapotmezőjében felsorolt [szabálysértésekként](https://github.com/open-policy-agent/gatekeeper#audit) is feltekinthetők.

## <a name="policy-language"></a>A házirend nyelve

Az Azure Policy nyelvi struktúra kezelésére Kubernetes követi, hogy a meglévő szabályzatok. Az _EnforceOPAConstraint_ hatás a Kubernetes-fürtök kezelésére szolgál, és az [OPA-megszorítási keretrendszer](https://github.com/open-policy-agent/frameworks/tree/master/constraint) és a forgalomirányító v3-as kezelésére jellemző részleteket veszi figyelembe. A részleteket és példákat az [EnforceOPAConstraint](./effects.md#enforceopaconstraint) hatás tartalmazza.
  
A _details.constraintTemplate_ és a _details.constraint_ tulajdonságok részeként a szabályzatdefinícióban az Azure Policy átadja ezeknek a [CustomResourceDefinitionsnek](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) az URI-kat a bővítménynek. A Rego az a nyelv, amelyet az OPA és a Gatekeeper támogat a Kubernetes-fürtnek küldött kérelem érvényesítéséhez. A Kubernetes-felügyelet meglévő szabványának támogatásával az Azure Policy lehetővé teszi a meglévő szabályok újrafelhasználását és az Azure Policy-del való párosítást az egységes felhőmegfelelőségi jelentéskészítési élmény érdekében. További információ: [Mi az a Rego?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego)

## <a name="built-in-policies"></a>Beépített szabályzatok

A fürt Azure Portalon való kezelésére szolgáló beépített szabályzatok megkereséséhez kövesse az alábbi lépéseket:

1. Indítsa el az Azure Policy szolgáltatást az Azure Portalon. Válassza az Összes szolgáltatás lehetőséget a bal oldali ablaktáblában, majd keresse meg a Házirend elemet, és válassza a **Házirend**lehetőséget.

1. Az Azure Policy lap bal oldali ablaktáblájában válassza a **Definíciók**lehetőséget.

1. A Kategória legördülő listából törölje az összes et a szűrőből, majd válassza a **Kubernetes**lehetőséget.

1. Jelölje ki a házirend-definíciót, majd kattintson a **Hozzárendelés** gombra.

1. Állítsa be a **hatókört** a Kubernetes-fürt felügyeleti csoportjára, előfizetésére vagy erőforráscsoportjára, ahol a házirend-hozzárendelés alkalmazandó lesz.

   > [!NOTE]
   > Az Azure-szabályzat AKS-definícióhoz való hozzárendelésekéna a **hatókörnek tartalmaznia** kell az AKS-fürterőforrást.

1. Adjon a házirend-hozzárendelésnek egy **nevet** és **leírást,** amely segítségével könnyen azonosítható.

1. Állítsa a [házirend-kényszerítést](./assignment-structure.md#enforcement-mode) az alábbi értékek egyikére.

   - **Engedélyezve** – A házirend kényszerítése a fürtön. Kubernetes felvételi kérelmek megsértése esetén megtagadják.
   
   - **Disabled** – Ne kényszerítse a házirendet a fürtön. A Kubernetes beismerő kérelmeket nem tagadják meg. A megfelelőségi értékelés eredményei továbbra is rendelkezésre állnak. Amikor új szabályzatokat vezet be a fürtök futtatásához, a _Letilt lehetőség_ hasznos a szabályzatok teszteléséhez, mivel a beléptetési kérelmek megsértése esetén nem tagadják meg.

1. Válassza a **Tovább lehetőséget.**

1. **Paraméterértékek** beállítása
   
   - Ha ki szeretné zárni a Kubernetes-névtereket a házirend-kiértékelésből, adja meg a névterek listáját a **Névtér kizárások**paraméterben. Javasoljuk, hogy kizárja: _kube-rendszer_

1. Válassza az **Áttekintés + létrehozás** lehetőséget.

Másik lehetőségként használja a [házirend hozzárendelése - Portál](../assign-policy-portal.md) gyorsindítás a kereséséhez és hozzárendeléséhez egy AKS-házirend. A "naplózási virtuális gépek" minta helyett keressen egy Kubernetes-házirend-definíciót.

> [!IMPORTANT]
> A **Kubernetes** kategóriába tartozó beépített házirendek csak az AKS-hez használhatók. A beépített házirendek listáját a [Kubernetes-minták ban láthatja.](../samples/built-in-policies.md#kubernetes)

## <a name="logging"></a>Naplózás

### <a name="azure-policy-add-on-logs"></a>Azure-szabályzat bővítménynaplói

Kubernetes-vezérlőként/-tárolóként az Azure Policy Add-on és a Gatekeeper is naplót vezet az AKS-fürtben. A naplók az AKS-fürt **Insights** lapján találhatók. További információ: [AKS-fürt teljesítményének megértése az Azure Monitor tárolókhoz című témakörben.](../../../azure-monitor/insights/container-insights-analyze.md)

## <a name="remove-the-add-on"></a>A bővítmény eltávolítása

Az Azure Policy-bővítmény AKS-fürtből való eltávolításához használja az Azure Portalt vagy az Azure CLI-t:

- Azure portál

  1. Indítsa el az AKS-szolgáltatást az Azure Portalon a **Minden szolgáltatás**elemre kattintva, majd keresse meg és válassza a **Kubernetes-szolgáltatásokat.**

  1. Válassza ki az AKS-fürtöt, ahol le szeretné tiltani az Azure Policy bővítményt.

  1. Válassza a **Szabályzatok (előzetes verzió)** lehetőséget a Kubernetes szolgáltatáslap bal oldalán.

     ![Az AKS-fürt házirendjei](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. A főlapon válassza a **Bővítmény letiltása** gombot.

     ![Az AKS-bővítmény Azure-szabályzatának letiltása](../media/rego-for-aks/disable-policy-add-on.png)

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Az Azure Policy bővítmény által gyűjtött diagnosztikai adatok

A Kubernetes Azure Policy bővítménye korlátozott fürtdiagnosztikai adatokat gyűjt. Ezek a diagnosztikai adatok a szoftverrel és a teljesítménnyel kapcsolatos létfontosságú műszaki adatok. A következő módokon használható:

- Az Azure Policy bővítmény naprakészen tartása
- Az Azure Policy add-on biztonságos, megbízható, teljesítménybeli megtartása
- Az Azure Policy add-on fejlesztése – a bővítmény használatának összesített elemzésével

A bővítmény által gyűjtött adatok nem személyes adatok. A következő adatokat jelenleg gyűjtik:

- Az Azure Policy bővítményügynök verziója
- Fürt típusa
- Fürtrégió
- Fürterőforrás-csoport
- Fürterőforrás-azonosító
- Fürtelőfizetés azonosítója
- Fürt operációs rendszer (példa: Linux)
- Fürtváros (példa: Seattle)
- Fürt állama vagy tartománya (példa: Washington)
- Fürt országa vagy régiója (példa: Egyesült Államok)
- Az Azure Policy bővítmény által a házirend-kiértékelésügynök-telepítés során tapasztalt kivételek/hibák
- Az Azure Policy bővítmény által nem telepített Gatekeeper házirendek száma

## <a name="next-steps"></a>További lépések

- Tekintse át a példákat az [Azure Policy-mintákban.](../samples/index.md)
- A [Szabályzatdefiníciók struktúrájának](definition-structure.md) áttekintése.
- A [Szabályzatok hatásainak ismertetése](effects.md).
- Ismerje meg, hogyan hozhat [létre programozott házirendeket.](../how-to/programmatically-create.md)
- További információ a [megfelelőségi adatok beszedéséről.](../how-to/get-compliance-data.md)
- További információ a [nem megfelelő erőforrások kiújulásáról.](../how-to/remediate-resources.md)
- Tekintse át, hogy mi a felügyeleti csoport az [Erőforrások rendszerezése az Azure felügyeleti csoportokkal.](../../management-groups/overview.md)
