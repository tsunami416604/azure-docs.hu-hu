---
title: Ismerje meg az Azure-szabályzatot az AKS-motorhoz
description: Ismerje meg, hogy az Azure Policy hogyan használja a CustomResourceDefinitions és a Open Policy Agent from Gatekeeper v3-ból a fürtök aKS-motorral való kezeléséhez.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: c41a9d84dfe43e356e9a4a17af523a37209c2933
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436422"
---
# <a name="understand-azure-policy-for-aks-engine"></a>Az Azure-szabályzat az AKS-motorhoz

Az Azure Policy integrálható [az AKS-motorral,](https://github.com/Azure/aks-engine/blob/master/docs/README.md)amely kényelmes eszközlehetővé teszi a saját felügyelt Kubernetes-fürt azure-beli gyors indítását. Ez az integráció lehetővé teszi az AKS Engine saját felügyeletű fürtjein a központi, következetes módon történő nagy léptékű kényszerítéseket és védelmet. Az [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3 (béta) használatával, amely a Kubernetes _belépési vezérlő webhookja,_ az Azure-szabályzat lehetővé teszi az Azure-erőforrások és az AKS Engine saját kezelésű fürtök megfelelőségi állapotának kezelését és jelentését egy helyről.

> [!NOTE]
> Az Azure Policy for AKS Engine nyilvános előzetes verzióban érhető el, és nem rendelkezik SLA-val. Gatekeeper v3 béta verzióban érhető el, és a nyílt forráskódú közösség támogatja. A szolgáltatás csak a beépített házirend-definíciókat és egy AKS Engine-fürtöt támogatja minden egyszerű szolgáltatással konfigurált erőforráscsoporthoz.

> [!IMPORTANT]
> Az AKS-motor, az AKS-motor vagy a Gatekeeper v3 Azure-szabályzatának támogatásához hozzon létre egy [új problémát](https://github.com/Azure/aks-engine/issues/new/choose) az AKS Engine GitHub-tárházban.

## <a name="overview"></a>Áttekintés

Az Azure Policy for AKS Engine engedélyezéséhez és használatához az Azure-beli, saját felügyelt Kubernetes-fürtjével a következő műveleteket kell elkövetnie:

- [Előfeltételek](#prerequisites)
- [Az Azure Policy bővítmény telepítése](#installing-the-add-on)
- [Házirend-definíció hozzárendelése az AKS-motorhoz](#built-in-policies)
- [Várakozás az ellenőrzésre](#validation-and-reporting-frequency)

## <a name="prerequisites"></a>Előfeltételek

Az Azure Policy bővítmény telepítése vagy a szolgáltatásszolgáltatások bármelyikének engedélyezése előtt az előfizetésnek engedélyeznie kell a **Microsoft.PolicyInsights** erőforrás-szolgáltatót, és létre kell hoznia egy szerepkör-hozzárendelést a fürtszolgáltatás egyszerű szolgáltatásához. 

1. Az erőforrás-szolgáltató engedélyezéséhez kövesse az [Erőforrás-szolgáltatók és -típusok](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) című lépéseket, vagy futtassa az Azure CLI vagy az Azure PowerShell parancsot:

   - Azure CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell
     
     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell
   
     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell
   
     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. Szerepkör-hozzárendelés létrehozása az egyszerű fürtszolgáltatáshoz

   - Ha nem ismeri a fürtegyszerűs alkalmazásazonosítóját, keresse meg a következő paranccsal.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system
   
     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - A "Policy Insights Data Writer (Preview)" szerepkör-hozzárendelés hozzárendelése a fürt egyszerű alkalmazásazonosítóhoz (az előző lépésből származó _aadClientID_ érték) az Azure CLI-vel. Cserélje `<subscriptionId>` le az előfizetés-azonosítóját és `<aks engine cluster resource group>` az erőforráscsoportot, amelyben az AKS Engine saját felügyelt Kubernetes-fürt található.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

## <a name="azure-policy-add-on"></a>Azure-szabályzat bővítmény

A Kubernetes _Azure Policy bővítménye_ az Azure Policy szolgáltatást a Gatekeeper belépési vezérlőhöz kapcsolja. A _kube-system_ névtérbe telepített bővítmény a következő funkciókat adja meg:

- Az Azure-szabályzattal ellenőrzi az AKS Engine-fürthozzárendeléseit
- A házirend részleteinek, a megkötési sablonoknak és a korlátozásoknak a leése és telepítése
- Teljes vizsgálatmegfelelőség-ellenőrzést futtat az AKS Engine fürtön
- A naplózási és megfelelőségi részletek jelentései visszaaz Azure-szabályzatba

### <a name="installing-the-add-on"></a>A bővítmény telepítése

Miután az előfeltételek befejeződtek, az Azure Policy bővítmény telepíthető. A telepítés lehet egy AKS-motor létrehozása vagy frissítési ciklusa során, vagy egy meglévő fürt független műveleteként.

- Telepítés létrehozási vagy frissítési ciklus ban

  Az Azure Policy add-on engedélyezéséhez egy új, saját felügyelt fürt létrehozása vagy egy meglévő fürt frissítése során, adja meg az AKS-motor **addons** tulajdonságfürt-definícióját.

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true,
      "config": {
          "auditInterval": "30",
          "constraintViolationsLimit": "20"
      }
  }]
  ```

  További információt az [AKS Engine fürtdefiníció](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md)külső útmutatójában talál.

- Telepítés meglévő fürtbe helm diagramokkal

  A fürt előkészítéséhez és a bővítmény telepítéséhez kövesse az alábbi lépéseket:

  1. Telepítse a Forgalomirányítót a _forgalomirányító-rendszer_ névterére.

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```
  1. Adja hozzá _a vezérlősík_ címkéjét a _kube-rendszerhez._ Ez a címke kizárja a _kube-system_ podok és szolgáltatások gatekeeper és az Azure Policy bővítmény naplózását.

     ```bash
     kubectl label namespaces kube-system control-plane=controller-manager
     ```

  1. Szinkronizálja a Kubernetes-adatokat (névtér, pod, be- és szolgáltatás) az OPA-val.

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/gatekeeper-opa-sync.yaml
     ```

     További információ: [OPA - Replikálási adatok](https://github.com/open-policy-agent/gatekeeper#replicating-data).

  1. Adja hozzá az Azure-szabályzat repo a helm.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     További információ: [Helm Chart - Quickstart Guide](https://helm.sh/docs/using_helm/#quickstart-guide).

  1. Telepítse a bővítményt egy Helm Chart segítségével. Cserélje `<subscriptionId>` le az előfizetés-azonosítóját és `<aks engine cluster resource group>` az erőforráscsoportot, amelyben az AKS Engine saját felügyelt Kubernetes-fürt található.

     ```bash
     helm install azure-policy/azure-policy-addon-aks-engine --name azure-policy-addon --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     A bővítmény helm-diagramjainak telepítéséről további információt az [Azure Policy bővítményhelmi diagram definíciója](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts) a GitHubon talál.

     > [!NOTE]
     > Az Azure Policy Add-on és az erőforráscsoport-azonosító közötti kapcsolat miatt az Azure Policy minden erőforráscsoporthoz csak egy AKS Engine-fürtöt támogat.

Annak ellenőrzéséhez, hogy a bővítmény telepítése sikeres volt-e, és hogy az _azure-policy_ pod fut-e, futtassa a következő parancsot:

```bash
kubectl get pods -n kube-system
```

### <a name="validation-and-reporting-frequency"></a>Validálási és jelentéstételi gyakoriság

A bővítmény az Azure Policy segítségével 5 percenként ellenőrzi a házirend-hozzárendelések változásait. Ebben a frissítési ciklusban a bővítmény ellenőrzi a módosításokat. Ezek a változások eseményindító hoz létre, frissítések, vagy törli a megkötés sablonok és korlátozások.

> [!NOTE]
> Bár a _fürt rendszergazdája_ engedélyt kaphat a megkötési sablonok és korlátozások módosítására, nem ajánlott vagy nem támogatott az Azure Policy által létrehozott megszorítási sablonok vagy korlátozások módosítása. A frissítési ciklus során a manuális módosítások elvesznek.

5 percenként a bővítmény a fürt teljes vizsgálatát kéri. Miután összegyűjtötte a teljes vizsgálat részleteit, és a Gatekeeper által a fürt önmegkísérelt módosításainak valós idejű kiértékeléseit, a bővítmény visszajelenti az eredményeket az Azure Policy-nek, hogy tartalmazza a [megfelelőségi adatokat,](../how-to/get-compliance-data.md) például bármely Azure Policy-hozzárendelést. A rendszer csak az aktív házirend-hozzárendelések eredményeit adja vissza a naplózási ciklus során. A naplózási eredmények a sikertelen megkötés állapotmezőjében felsorolt szabálysértésekként is feltekinthetők.

## <a name="policy-language"></a>A házirend nyelve

Az AKS-motor kezelésére szolgáló Azure Policy nyelvi struktúra a meglévő szabályzatok é. Az _EnforceOPAConstraint_ hatás az AKS-motor-fürtök kezelésére szolgál, és az [OPA-megszorítási keretrendszer](https://github.com/open-policy-agent/frameworks/tree/master/constraint) és a forgalomirányító v3-as kezelésére jellemző _részleteket_ veszi figyelembe. A részleteket és példákat az [EnforceOPAConstraint](effects.md#enforceopaconstraint) hatás tartalmazza.

A _details.constraintTemplate_ és a _details.constraint_ tulajdonságok részeként a szabályzatdefinícióban az Azure Policy átadja ezeknek a [CustomResourceDefinitionsnek](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) az URI-kat a bővítménynek. A Rego az a nyelv, amelyet az OPA és a Gatekeeper támogat a Kubernetes-fürtnek küldött kérelem érvényesítéséhez. A Kubernetes-felügyelet meglévő szabványának támogatásával az Azure Policy lehetővé teszi a meglévő szabályok újrafelhasználását és az Azure Policy-del való párosítást az egységes felhőmegfelelőségi jelentéskészítési élmény érdekében. További információ: [Mi az a Rego?](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego)

## <a name="built-in-policies"></a>Beépített szabályzatok

Az AKS Engine-fürt Azure Portalon történő kezelésére szolgáló beépített szabályzatok megkereséséhez kövesse az alábbi lépéseket:

1. Indítsa el az Azure Policy szolgáltatást az Azure Portalon. Válassza az **Összes szolgáltatás lehetőséget** a bal oldali ablaktáblában, majd keresse meg a Házirend elemet, és válassza a **Házirend**lehetőséget.

1. Az Azure Policy lap bal oldali ablaktáblájában válassza a **Definíciók**lehetőséget.

1. A Kategória legördülő listából törölje az **összes et** a szűrőből, majd válassza a **Kubernetes**lehetőséget.

1. Jelölje ki a házirend-definíciót, majd kattintson a **Hozzárendelés** gombra.

> [!NOTE]
> Az Azure-szabályzat AKS-motor definícióhoz hozzárendelések, a **hatókör** kell az AKS Engine-fürt erőforráscsoportja.

Másik lehetőségként használja a [házirend hozzárendelése - Portál](../assign-policy-portal.md) gyorsindítás az AKS-motor házirend megkereséséhez és hozzárendeléséhez. Keressen egy AKS Engine-házirend-definíciót a "naplózási virtuális gépek" minta helyett.

> [!IMPORTANT]
> A **Kubernetes** kategóriába tartozó beépített házirendek csak az AKS Engine-hez használhatók.

## <a name="logging"></a>Naplózás

### <a name="azure-policy-add-on-logs"></a>Azure-szabályzat bővítménynaplói

Kubernetes-vezérlőként/tárolóként az Azure Policy bővítmény naplókat tárol az AKS-motor fürtben.

Az Azure Policy bővítménynaplóinak megtekintéséhez használja a következőket: `kubectl`

```bash
# Get the Azure Policy Add-on pod name
kubectl -n kube-system get pods -l app=azure-policy --output=name

# Get the logs for the add-on
kubectl logs <Azure Policy Add-on pod name> -n kube-system
```

### <a name="gatekeeper-logs"></a>Forgalomirányítói naplók

A Gatekeeper pod, _gatekeeper-controller-manager-0_általában `gatekeeper-system` `kube-system` a vagy a névtérben, de lehet egy másik névtérben attól függően, hogy hogyan van telepítve.

A Forgalomirányító naplóinak `kubectl`megtekintéséhez használja a következőket:

```bash
NAMESPACE=<namespace of gatekeeper>
kubectl logs gatekeeper-controller-manager-0 -n $NAMESPACE
```

További információ: [Debugging Gatekeeper](https://github.com/open-policy-agent/gatekeeper#debugging) az OPA dokumentációban.

## <a name="remove-the-add-on"></a>A bővítmény eltávolítása

Az Azure Policy bővítmény és a Gatekeeper eltávolításához az AKS-motor-fürtből, használja a bővítmény telepítésének módjához igazodó módszert:

- Ha az AKS Engine fürtdefiníciójában az **addons** tulajdonság beállításával van telepítve:

  Telepítse újra a fürtdefiníciót az AKS-motorra, miután az _azure-szabályzat_ **addons** tulajdonságát hamisra cserélte:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

- Ha helm diagramokkal van telepítve:

  1. Régi megkötések eltávolítása

     Jelenleg az eltávolítási mechanizmus csak eltávolítja a Gatekeeper rendszert, nem távolítja el a felhasználó által létrehozott _MegkötésSablon_, _Megkötés_vagy _Config_ erőforrásokat, és nem távolítja el a hozzájuk tartozó _CRD-ket._

     A Forgalomirányító futása esetén a nem kívánt korlátozások a következőkszerint távolíthatók el:

     - A megkötési erőforrás összes példányának törlése
     - A _MegszorításSablon_ erőforrás törlése, amely automatikusan törli a _CRD-t_
     - A _Config_ erőforrás törlése eltávolítja a véglegesítőket a szinkronizált erőforrásokon

  1. Az Azure-szabályzat bővítmény eltávolítása
  
     ```bash
     helm del --purge azure-policy-addon
     ```

  1. Forgalomirányító eltávolítása
  
     ```bash
     kubectl delete -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Az Azure Policy bővítmény által gyűjtött diagnosztikai adatok

A Kubernetes Azure Policy bővítménye korlátozott fürtdiagnosztikai adatokat gyűjt. Ezek a diagnosztikai adatok a szoftverrel és a teljesítménnyel kapcsolatos létfontosságú műszaki adatok. A következő módokon használható:

- Az Azure Policy Add-on naprakészen tartása
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