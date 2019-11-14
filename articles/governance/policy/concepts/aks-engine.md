---
title: Ismerkedjen meg Azure Policy az AK-motorral
description: Megtudhatja, hogyan használja a Azure Policy a CustomResourceDefinitions-t és a házirend-ügynököt a forgalomirányító v3-mel a fürtök és az AK-motor
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 904575680b62233ad5ec7422abbf66cf57dc4e11
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072067"
---
# <a name="understand-azure-policy-for-aks-engine"></a>Az AK-motor Azure Policy ismertetése

A Azure Policy az [AK-motorral](https://github.com/Azure/aks-engine/blob/master/docs/README.md), egy olyan rendszerrel, amely kényelmes eszközöket biztosít az Azure-beli önálló felügyelt Kubernetes-fürtök gyors elindításához. Ez az integráció lehetővé teszi, hogy központosított, konzisztens módon kezelje a saját AK-beli motorja által felügyelt fürtöket. Azáltal, hogy kiterjeszti a [nyílt házirendügynök](https://www.openpolicyagent.org/) (OPA) [forgalomirányító](https://github.com/open-policy-agent/gatekeeper) v3 (bétaverzió), a Kubernetes-hez készült _belépésvezérlés webhook_ használatát, Azure Policy lehetővé teszi az Azure-erőforrások és az AK-motor megfelelőségi állapotának kezelését és jelentését önálló felügyelt fürtök egy helyről.

> [!NOTE]
> Az AK-motor Azure Policy nyilvános előzetes verzióban érhető el, és nem rendelkezik SLA-val. A forgalomirányító v3 béta-verzióban érhető el, és a nyílt forráskódú Közösség támogatja. A szolgáltatás csak a beépített szabályzat-definíciókat és egyetlen AK-beli motor-fürtöt támogat minden egyszerű szolgáltatással konfigurált erőforráscsoport számára.

> [!IMPORTANT]
> Ahhoz, hogy támogatást kapjon a Azure Policy az AK-motor, az AK-motor vagy a forgalomirányító v3 számára, hozzon létre egy [új problémát](https://github.com/Azure/aks-engine/issues/new/choose) az AK-motor GitHub-tárházában.

## <a name="overview"></a>Áttekintés

Az alábbi műveletek végrehajtásával engedélyezheti és használhatja a Azure Policy a saját felügyelt Kubernetes-fürtökkel rendelkező, az AK-beli motorhoz:

- [Előfeltételek](#prerequisites)
- [A Azure Policy bővítmény telepítése](#installing-the-add-on)
- [Házirend-definíció társítása az AK-motorhoz](#built-in-policies)
- [Várakozás az ellenőrzésre](#validation-and-reporting-frequency)

## <a name="prerequisites"></a>Előfeltételek

A Azure Policy bővítmény telepítése vagy a szolgáltatás bármely funkciójának engedélyezése előtt az előfizetésnek engedélyeznie kell a **Microsoft. PolicyInsights** erőforrás-szolgáltatót, és létre kell hoznia egy szerepkör-hozzárendelést a fürtszolgáltatási rendszerbiztonsági tag számára. 

1. Az erőforrás-szolgáltató engedélyezéséhez kövesse az erőforrás- [szolgáltatók és-típusok](../../../azure-resource-manager/resource-manager-supported-services.md#azure-portal) lépéseit, vagy futtassa az Azure CLI vagy a Azure PowerShell parancsot:

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

1. Szerepkör-hozzárendelés létrehozása a fürtszolgáltatási egyszerű szolgáltatáshoz

   - Ha nem ismeri a fürtszolgáltatás egyszerű alkalmazásának AZONOSÍTÓját, keresse meg az alábbi parancsot.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system
   
     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - A "Policy betekintő adatíró (előzetes verzió)" szerepkör-hozzárendelés hozzárendelése a fürtszolgáltatás egyszerű _aadClientID_ (az előző lépésből származó érték) az Azure CLI-vel. Cserélje le a `<subscriptionId>`t az előfizetési AZONOSÍTÓra, és `<aks engine cluster resource group>` az erőforráscsoporthoz, amely a saját maga által felügyelt Kubernetes-fürt.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

## <a name="azure-policy-add-on"></a>Azure Policy bővítmény

A Kubernetes _Azure Policy-bővítménye_ csatlakoztatja a Azure Policy szolgáltatást a forgalomirányító belépésvezérlés szolgáltatáshoz. A _Kube-System_ névtérbe telepített bővítmény a következő funkciókat hozza létre:

- Az AK-motor fürtjéhez való hozzárendelések Azure Policy ellenőrzése
- Letölti és telepíti a szabályzat részleteit, a korlátozási sablonokat és a megkötéseket.
- Teljes ellenőrzési megfelelőségi ellenőrzés futtatása az KABAi motor fürtjén
- A naplózási és megfelelőségi adatok visszaállítása a Azure Policy

### <a name="installing-the-add-on"></a>A bővítmény telepítése

Az előfeltételek elvégzése után a Azure Policy bővítmény telepíthető. A telepítés lehet egy AK-motor létrehozási vagy frissítési ciklusa, vagy egy meglévő fürtön futó önálló művelet.

- Telepítés a létrehozási vagy frissítési ciklus során

  Ha engedélyezni szeretné a Azure Policy bővítményt egy új, önfelügyelt fürt létrehozásakor vagy egy meglévő fürt frissítéseként, adja meg a **bővítmények** tulajdonságának fürtjének definícióját az AK motorhoz.

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

  A szolgáltatással kapcsolatos további információkért tekintse meg a külső útmutató az [AK-motor-fürt definícióját](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).

- Telepítés meglévő fürtben Helm-diagramokkal

  A következő lépésekkel készítse elő a fürtöt, és telepítse a bővítményt:

  1. Telepítse a forgalomirányítót a _forgalomirányító-System_ névtérbe.

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```
  1. Adja hozzá a _vezérlő-sík_ címkét a _Kube-System elemhez_. Ez a címke kizárja a _Kube-_ rendszerhüvelyek és-szolgáltatások a forgalomirányító és a Azure Policy-bővítmény általi naplózását.

     ```bash
     kubectl label namespaces kube-system control-plane=controller-manager
     ```

  1. Kubernetes-adatszinkronizálás (névtér, Pod, bejövő forgalom, szolgáltatás) az OPA-szel.

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/gatekeeper-opa-sync.yaml
     ```

     További információ: [Opa – adatok replikálása](https://github.com/open-policy-agent/gatekeeper#replicating-data).

  1. Adja hozzá a Azure Policy-tárházat a Helmhoz.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     További információ: [Helm chart – rövid útmutató](https://helm.sh/docs/using_helm/#quickstart-guide).

  1. Telepítse a bővítményt egy Helm diagrammal. Cserélje le a `<subscriptionId>`t az előfizetési AZONOSÍTÓra, és `<aks engine cluster resource group>` az erőforráscsoporthoz, amely a saját maga által felügyelt Kubernetes-fürt.

     ```bash
     helm install azure-policy/azure-policy-addon-aks-engine --name azure-policy-addon --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     További információ arról, hogy a Hogyan telepíti a kiegészítő Helm-diagramot: a [Azure Policy a Helm diagram definíciója](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts) a githubon.

     > [!NOTE]
     > A Azure Policy-bővítmény és az erőforráscsoport-azonosító közötti kapcsolat miatt Azure Policy minden erőforráscsoport esetében csak egy AK-beli motor-fürtöt támogat.

A következő parancs futtatásával ellenőrizheti, hogy a bővítmény telepítése sikeres volt-e, és hogy az _Azure-Policy_ Pod fut-e:

```bash
kubectl get pods -n kube-system
```

### <a name="validation-and-reporting-frequency"></a>Ellenőrzési és jelentéskészítési gyakoriság

A bővítmény 5 percenként ellenőrzi, hogy van-e Azure Policy a házirend-hozzárendelések változásaihoz. A frissítési ciklus során a bővítmény ellenőrzi a módosításokat. Ezek a változások a megkötési sablonok és megkötések létrehozásához, frissítéséhez vagy törléséhez jönnek létre.

> [!NOTE]
> Habár a _fürt rendszergazdája_ jogosult a korlátozási sablonok és a megkötések módosítására, nem ajánlott vagy nem támogatott a Azure Policy által létrehozott korlátozási sablonok vagy megkötések módosítására. Az elvégzett manuális módosítások elvesznek a frissítési ciklus során.

A bővítmény 5 percenként meghívja a fürt teljes vizsgálatát. Miután összegyűjtötte a teljes vizsgálat részleteit és minden valós idejű értékelést a fürtön történt megkísérelt módosításokat, a bővítmény jelentést készít az eredményről Azure Policy a [megfelelőségi adatokba](../how-to/get-compliance-data.md) , például bármely Azure Policy hozzárendelésbe való felvételhez. A naplózási ciklusban csak az aktív házirend-hozzárendelések eredményeit adja vissza a rendszer. A naplózási eredmények a sikertelen korlátozás állapota mezőjében megjelenő szabálysértésként is megtekinthetők.

## <a name="policy-language"></a>Házirend nyelve

Az AK-motor kezelésének Azure Policy nyelvi szerkezete a meglévő szabályzatokat követi. A _EnforceOPAConstraint_ hatására a rendszer az AK-motor fürtjének kezelésére szolgál, és _részletesen ismerteti_ az [Opa-korlátozási keretrendszer](https://github.com/open-policy-agent/frameworks/tree/master/constraint) és a forgalomirányító v3 használatára vonatkozó tulajdonságokat. További részletekért és példákért tekintse meg a [EnforceOPAConstraint](effects.md#enforceopaconstraint) hatást.

A _details. constraintTemplate_ és a _details. megkötés_ tulajdonságok részeként a házirend-definícióban Azure Policy átadja ezen [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) URI-azonosítóját a bővítménynek. A Rego az a nyelv, amelyet az OPA és a forgalomirányító támogat a Kubernetes-fürtre irányuló kérelem érvényesítéséhez. A Kubernetes-kezelés meglévő szabványának támogatásával a Azure Policy lehetővé teszi a meglévő szabályok újbóli használatát, és azok párosítását Azure Policy egy egységes Felhőbeli megfelelőségi jelentéskészítési élmény érdekében. További információ: [What is Rego?](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego).

## <a name="built-in-policies"></a>Beépített szabályzatok

Az alábbi lépéseket követve megkeresheti a beépített házirendeket az AK-motor fürtjének a Azure Portal használatával történő kezeléséhez:

1. Indítsa el a Azure Policy szolgáltatást a Azure Portal. Válassza a **minden szolgáltatás** lehetőséget a bal oldali ablaktáblán, majd keresse meg és válassza ki a **házirend**elemet.

1. A Azure Policy lap bal oldali ablaktábláján válassza a **definíciók**lehetőséget.

1. A kategória legördülő listából válassza az **összes kijelölése lehetőséget** a szűrő törléséhez, majd válassza a **Kubernetes**lehetőséget.

1. Válassza ki a házirend-definíciót, majd kattintson a **hozzárendelés** gombra.

> [!NOTE]
> Az AK-motor definíciójának Azure Policy kiosztásakor a **hatókörnek** az AK-motor fürtjének erőforráscsoporthoz kell tartoznia.

Másik lehetőségként használja a [házirend társítása – portál](../assign-policy-portal.md) rövid útmutatóját egy AK-motor házirendjének megkereséséhez és hozzárendeléséhez. Keressen egy AK-motor házirend-definícióját a "naplózási virtuális gépek" minta helyett.

> [!IMPORTANT]
> A **Kubernetes** kategóriába tartozó beépített szabályzatok csak az AK-motorral használhatók.

## <a name="logging"></a>Naplózás

### <a name="azure-policy-add-on-logs"></a>Azure Policy kiegészítő naplók

Kubernetes-vezérlőként/tárolóként a Azure Policy-bővítmény megtartja a naplókat az AK-motor fürtjében.

A Azure Policy-bővítmény naplófájljainak megtekintéséhez használja a `kubectl`:

```bash
# Get the Azure Policy Add-on pod name
kubectl -n kube-system get pods -l app=azure-policy --output=name

# Get the logs for the add-on
kubectl logs <Azure Policy Add-on pod name> -n kube-system
```

### <a name="gatekeeper-logs"></a>Forgalomirányító-naplók

A forgalomirányító Pod, a _forgalomirányító-Controller-Manager-0_általában a `gatekeeper-system` vagy `kube-system` névtérben található, de az üzembe helyezéstől függően más névtérben is lehet.

A forgalomirányító naplófájljainak megtekintéséhez használja a `kubectl`:

```bash
NAMESPACE=<namespace of gatekeeper>
kubectl logs gatekeeper-controller-manager-0 -n $NAMESPACE
```

További információ: [forgalomirányító hibakeresése](https://github.com/open-policy-agent/gatekeeper#debugging) az OPA dokumentációjában.

## <a name="remove-the-add-on"></a>A bővítmény eltávolítása

Ha el szeretné távolítani a Azure Policy-bővítményt és a forgalomirányító-t az KABAi motor fürtjéből, használja a bővítmény telepítésének módját a következő módon:

- Ha telepítve van, állítsa be az **addons** tulajdonságot az AK-motorhoz tartozó fürt definíciójában:

  Az _Azure-Policy_ **addons** tulajdonságának hamis értékre való módosítása után telepítse újra a fürt definícióját az AK-motorra:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

- Ha Helm-diagramokkal van telepítve:

  1. Régi korlátozások eltávolítása

     Jelenleg az eltávolítási mechanizmus csak a forgalomirányító rendszer eltávolítását végzi, nem távolítja el a felhasználó által létrehozott _ConstraintTemplate_, _korlátozásokat_vagy _konfigurációs_ erőforrásokat, és nem távolítja el a kapcsolódó _CRDs_ .

     Ha a forgalomirányító fut, lehetséges, hogy a következő módon távolíthatja el a nemkívánatos korlátozásokat:

     - A megkötés erőforrás összes példányának törlése
     - A _ConstraintTemplate_ -erőforrás törlése, amely automatikusan törli a _CRD_ -t
     - A _konfigurációs_ erőforrás törlése a szinkronizált erőforrásokról eltávolítja a véglegesítéseket

  1. Azure Policy bővítmény eltávolítása
  
     ```bash
     helm del --purge azure-policy-addon
     ```

  1. Forgalomirányító eltávolítása
  
     ```bash
     kubectl delete -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Azure Policy bővítmény által gyűjtött diagnosztikai adatok

A Kubernetes Azure Policy-bővítménye korlátozott számú fürt diagnosztikai adatokat gyűjt. A diagnosztikai adat a szoftverrel és teljesítménnyel kapcsolatos létfontosságú technikai jellegű adat. A következő módokon használható:

- Tartsa naprakészen Azure Policy bővítményt
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

## <a name="next-steps"></a>További lépések

- Tekintse át a példákat [Azure Policy mintákon](../samples/index.md).
- A [Szabályzatdefiníciók struktúrájának](definition-structure.md) áttekintése.
- A [Szabályzatok hatásainak ismertetése](effects.md).
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](../how-to/programmatically-create.md).
- Ismerje meg, hogyan [kérheti le a megfelelőségi információkat](../how-to/getting-compliance-data.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](../how-to/remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).