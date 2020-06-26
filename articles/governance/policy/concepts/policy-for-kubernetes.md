---
title: Előzetes verzió – a Kubernetes Azure Policy megismerése
description: Ismerje meg, hogyan használja a Azure Policy a Rego-t és a nyílt házirend-ügynököt az Azure-ban vagy a helyszínen futó Kubernetes futtató fürtök kezelésére. Ez egy előzetes verziójú szolgáltatás.
ms.date: 06/12/2020
ms.topic: conceptual
ms.openlocfilehash: a044ea33f1a7710c4bb97d30cf8f11d4de2838b1
ms.sourcegitcommit: dfa5f7f7d2881a37572160a70bac8ed1e03990ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2020
ms.locfileid: "85373624"
---
# <a name="understand-azure-policy-for-kubernetes-clusters-preview"></a>A Kubernetes-fürtök Azure Policy megismerése (előzetes verzió)

A Azure Policy kiterjeszti a [forgalomirányító](https://github.com/open-policy-agent/gatekeeper) v3-t, amely egy, a [nyílt házirendügynök](https://www.openpolicyagent.org/) (OPA) által biztosított _belépésvezérlés-webhook_ , amely a fürtökre központosított, konzisztens módon alkalmazható. Azure Policy lehetővé teszi, hogy a Kubernetes-fürtök megfelelőségi állapotát egyetlen helyről kezelhesse és jelentse. A bővítmény a következő funkciókat hozza létre:

- Ellenőrzi a Azure Policy szolgáltatást a fürtre vonatkozó házirend-hozzárendelésekhez.
- A házirend-definíciókat a fürtben, a [korlátozási sablon](https://github.com/open-policy-agent/gatekeeper#constraint-templates) és az egyéni erőforrások [korlátozásával](https://github.com/open-policy-agent/gatekeeper#constraints) telepíti.
- A naplózási és megfelelőségi adatok visszaállítása a Azure Policy szolgáltatásba.

A Kubernetes Azure Policy a következő fürt-környezeteket támogatja:

- [Azure Kubernetes Service (AKS)](../../../aks/intro-kubernetes.md)
- [Azure Arc-kompatibilis Kubernetes](../../../azure-arc/kubernetes/overview.md)
- [AK-motor](https://github.com/Azure/aks-engine/blob/master/docs/README.md)

> [!IMPORTANT]
> A Kubernetes-hez készült Azure Policy előzetes verzióban érhető el, és csak a Linux-csomópontok készleteit és beépített szabályzat-definíciókat támogat. A beépített szabályzat-definíciók a **Kubernetes** kategóriában találhatók. A korlátozott előzetes verziójú házirend-definíciók a **EnforceOPAConstraint** és a **EnforceRegoPolicy** effektussal, a kapcsolódó **Kubernetes-szolgáltatások** kategóriája pedig _elavult_. Ehelyett használja a hatások _naplózása_ és a _Megtagadás_ erőforrás-szolgáltatói módot `Microsoft.Kubernetes.Data` .

## <a name="overview"></a>Áttekintés

A Kubernetes-fürttel való Azure Policy engedélyezéséhez és használatához végezze el a következő műveleteket:

1. Konfigurálja a Kubernetes-fürtöt, és telepítse a bővítményt:
   - [Azure Kubernetes Service (AKS)](#install-azure-policy-add-on-for-aks)
   - [Azure Arc-kompatibilis Kubernetes](#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)
   - [AK-motor](#install-azure-policy-add-on-for-aks-engine)

   > [!NOTE]
   > A telepítéssel kapcsolatos gyakori problémákért lásd: [Hibaelhárítás – Azure Policy bővítmény](../troubleshoot/general.md#add-on-installation-errors).

1. [A Kubernetes Azure Policy nyelvének megismerése](#policy-language)

1. [Beépített definíció társítása a Kubernetes-fürthöz](#assign-a-built-in-policy-definition)

1. [Várakozás az ellenőrzésre](#policy-evaluation)

## <a name="install-azure-policy-add-on-for-aks"></a>Azure Policy bővítmény telepítése az AK-hoz

A Azure Policy bővítmény telepítése vagy a szolgáltatás bármely funkciójának engedélyezése előtt az előfizetésnek engedélyeznie kell a **Microsoft. tárolószolgáltatás** és a **Microsoft. PolicyInsights** erőforrás-szolgáltatót.

1. Szüksége lesz az Azure CLI-verzió 2.0.62 vagy újabb verziójára, és konfigurálva van. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

1. Regisztrálja az erőforrás-szolgáltatókat és az előzetes verziójú funkciókat.

   - Azure Portal:

     1. Regisztrálja a **Microsoft. tárolószolgáltatás** és a **Microsoft. PolicyInsights** erőforrás-szolgáltatókat. A lépéseket lásd: [erőforrás-szolgáltatók és típusok](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

     1. Indítsa el az Azure Policy szolgáltatást az Azure Portalon. Ehhez kattintson a **Minden szolgáltatás** elemre, majd keresse meg, és válassza ki a **Szabályzat** elemet.

        :::image type="content" source="../media/policy-for-kubernetes/search-policy.png" alt-text="Szabályzat keresése az összes szolgáltatásban" border="false":::

     1. A Azure Policy lap bal oldalán válassza a **Csatlakozás előnézete** elemet.

        :::image type="content" source="../media/policy-for-kubernetes/join-aks-preview.png" alt-text="Csatlakozás a Szabályzathoz az AK előzetes verziójához" border="false":::

     1. Válassza ki az előfizetéshez hozzáadni kívánt előfizetés sorát.

     1. Válassza az előfizetések listájának tetején található **opt-in** gombot.

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
     az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzurePolicyAutoApprove')].   {Name:name,State:properties.state}"
     
     # Once the above shows 'Registered' run the following to propagate the update
     az provider register -n Microsoft.ContainerService
     ```

1. Ha a korlátozott előzetes verziójú szabályzat-definíciók telepítve lettek, távolítsa el a bővítményt az AK-fürt **Letiltás** gombjával a **házirendek (előzetes verzió)** lapon.

1. Az AK-fürt _1,14_ -es vagy újabb verziójának kell lennie. A következő parancsfájl használatával ellenőrizze az AK-fürt verzióját:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Telepítse az Azure CLI előzetes verziójának _0.4.0_ verzióját az AK-hoz `aks-preview` :

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > Ha korábban már telepítette az _AK-előnézeti_ bővítményt, telepítse az összes frissítést a `az extension update --name aks-preview` parancs használatával.

A fenti előfeltételként szükséges lépések elvégzése után telepítse a Azure Policy-bővítményt a felügyelni kívánt AK-fürtön.

- Azure Portal

  1. Indítsa el az AK szolgáltatást a Azure Portal a **minden szolgáltatás**elemre kattintva, majd keresse meg és válassza ki a **Kubernetes Services szolgáltatást**.

  1. Válasszon egy AK-beli fürtöt.

  1. A Kubernetes szolgáltatás oldalának bal oldalán válassza a **házirendek (előzetes verzió)** lehetőséget.

     :::image type="content" source="../media/policy-for-kubernetes/policies-preview-from-aks-cluster.png" alt-text="Házirend-definíciók az AK-fürtből" border="false":::

  1. A Főoldalon kattintson a **bővítmény engedélyezése** gombra.

     :::image type="content" source="../media/policy-for-kubernetes/enable-policy-add-on.png" alt-text="A Azure Policy engedélyezése az AK-bővítményhez" border="false":::

     > [!NOTE]
     > Ha a **bővítmény engedélyezése** gomb szürkén jelenik meg, az előfizetés még nem lett hozzáadva az előzetes verzióhoz. Ha a **bővítmény letiltása** gomb engedélyezve van, és az áttelepítés figyelmeztetése v2 üzenet jelenik meg, a Gatekeepver v2 továbbra is telepítve van, ezért el kell távolítani.

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

A következő parancs futtatásával ellenőrizheti, hogy a bővítmény telepítése sikeres volt-e, és hogy fut-e az _Azure-Policy_ és a _forgalomirányító_ hüvelye:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

Végül ellenőrizze, hogy a legújabb bővítmény telepítve van-e az Azure CLI-parancs futtatásával, az `<rg>` erőforráscsoport nevével és `<cluster-name>` az AK-fürt nevével: `az aks show -g <rg> -n <cluster-name>` . Az eredménynek az alábbi kimenethez és konfigurációhoz hasonlóan kell kinéznie **. a verziónak** a következőnek kell lennie `v2` :

```output
"addonProfiles": {
    "azurepolicy": {
        "config": {
            "version": "v2"
        },
        "enabled": true,
        "identity": null
    },
}
```

## <a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes"></a>Azure Policy bővítmény telepítése az Azure arc-kompatibilis Kubernetes

A Azure Policy bővítmény telepítése vagy a szolgáltatás bármely funkciójának engedélyezése előtt az előfizetésnek engedélyeznie kell a **Microsoft. PolicyInsights** erőforrás-szolgáltatót, és létre kell hoznia egy szerepkör-hozzárendelést a fürtszolgáltatási rendszerbiztonsági tag számára.

1. Szüksége lesz az Azure CLI-verzió 2.0.62 vagy újabb verziójára, és konfigurálva van. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

1. Az erőforrás-szolgáltató engedélyezéséhez kövesse az erőforrás- [szolgáltatók és-típusok](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) lépéseit, vagy futtassa az Azure CLI vagy a Azure PowerShell parancsot:

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

1. A Kubernetes-fürt _1,14_ -es vagy újabb verziójúnak kell lennie.

1. Telepítse a [Helm 3](https://v3.helm.sh/docs/intro/install/)programot.

1. A Kubernetes-fürt engedélyezve van az Azure arc számára. További információ: [Kubernetes-fürt előkészítése az Azure-](../../../azure-arc/kubernetes/connect-cluster.md)ba.

1. Az Azure arc-kompatibilis Kubernetes-fürt teljes Azure-erőforrás-azonosítója. 

1. Nyissa meg a bővítmény portjait. A Azure Policy bővítmény ezeket a tartományokat és portokat használja a szabályzat-definíciók és-hozzárendelések beolvasásához, valamint a fürt megfelelőségének visszahívásához a Azure Policy.

   |Domain |Port |
   |---|---|
   |`gov-prod-policy-data.trafficmanager.net` |`443` |
   |`raw.githubusercontent.com` |`443` |
   |`login.windows.net` |`443` |
   |`dc.services.visualstudio.com` |`443` |

1. A házirend-elemzési adatíró (előzetes verzió) szerepkör hozzárendelése az Azure arc-kompatibilis Kubernetes-fürthöz. Cserélje le az értékét az `<subscriptionId>` előfizetés-azonosítóra, `<rg>` Az Azure arc-kompatibilis Kubernetes-fürt erőforráscsoporthoz, valamint az `<clusterName>` Azure arc-kompatibilis Kubernetes-fürt nevével. Tartsa nyomon a _AppID_, a _jelszó_és a _bérlő_ visszaadott értékeit a telepítési lépésekhez.

   - Azure CLI

     ```azurecli-interactive
     az ad sp create-for-rbac --role "Policy Insights Data Writer (Preview)" --scopes "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
     ```

   - Azure PowerShell

     ```azure powershell-interactive
     $sp = New-AzADServicePrincipal -Role "Policy Insights Data Writer (Preview)" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"

     @{ appId=$sp.ApplicationId;password=[System.Runtime.InteropServices.Marshal]::PtrToStringAuto([System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret));tenant=(Get-AzContext).Tenant.Id } | ConvertTo-Json
     ```

   Példa a fenti parancsok kimenetére:

   ```json
   {
       "appId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
       "password": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
       "tenant": "cccccccc-cccc-cccc-cccc-cccccccccccc"
   }
   ```

Az előfeltételként szükséges lépések elvégzése után telepítse a Azure Policy-bővítményt az Azure arc-kompatibilis Kubernetes-fürtön:

1. Adja hozzá a Azure Policy kiegészítő tárházat a Helmhoz:

   ```bash
   helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
   ```

1. Telepítse a Azure Policy bővítményt a Helm diagram használatával:

   ```bash
   # In below command, replace the following values with those gathered above.
   #    <AzureArcClusterResourceId> with your Azure Arc enabled Kubernetes cluster resource Id. For example: /subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>
   #    <ServicePrincipalAppId> with app Id of the service principal created during prerequisites.
   #    <ServicePrincipalPassword> with password of the service principal created during prerequisites.
   #    <ServicePrincipalTenantId> with tenant of the service principal created during prerequisites.
   helm install azure-policy-addon azure-policy/azure-policy-addon-arc-clusters \
       --set azurepolicy.env.resourceid=<AzureArcClusterResourceId> \
       --set azurepolicy.env.clientid=<ServicePrincipalAppId> \
       --set azurepolicy.env.clientsecret=<ServicePrincipalPassword> \
       --set azurepolicy.env.tenantid=<ServicePrincipalTenantId>
   ```

   További információ arról, hogy a Hogyan telepíti a kiegészítő Helm-diagramot: a [Azure Policy a Helm diagram definíciója](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-arc-clusters) a githubon.

A következő parancs futtatásával ellenőrizheti, hogy a bővítmény telepítése sikeres volt-e, és hogy fut-e az _Azure-Policy_ és a _forgalomirányító_ hüvelye:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="install-azure-policy-add-on-for-aks-engine"></a>Azure Policy-bővítmény telepítése az KABAi motorhoz

A Azure Policy bővítmény telepítése vagy a szolgáltatás bármely funkciójának engedélyezése előtt az előfizetésnek engedélyeznie kell a **Microsoft. PolicyInsights** erőforrás-szolgáltatót, és létre kell hoznia egy szerepkör-hozzárendelést a fürtszolgáltatási rendszerbiztonsági tag számára.

1. Szüksége lesz az Azure CLI-verzió 2.0.62 vagy újabb verziójára, és konfigurálva van. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

1. Az erőforrás-szolgáltató engedélyezéséhez kövesse az erőforrás- [szolgáltatók és-típusok](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) lépéseit, vagy futtassa az Azure CLI vagy a Azure PowerShell parancsot:

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

1. Hozzon létre egy szerepkör-hozzárendelést a fürtszolgáltatási rendszerbiztonsági tag számára.

   - Ha nem ismeri a fürtszolgáltatás egyszerű alkalmazásának AZONOSÍTÓját, keresse meg az alábbi parancsot.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system
   
     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - A "Policy betekintő adatíró (előzetes verzió)" szerepkör-hozzárendelés hozzárendelése a fürtszolgáltatás egyszerű _aadClientID_ (az előző lépésből származó érték) az Azure CLI-vel. A helyére írja be az `<subscriptionId>` előfizetés-azonosítóját és `<aks engine cluster resource group>` az erőforráscsoportot, amelyben az AK-motor saját felügyeletű Kubernetes-fürtje található.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

Az előfeltételként szükséges lépések elvégzése után telepítse a Azure Policy bővítményt. A telepítés lehet egy AK-motor létrehozási vagy frissítési ciklusa, vagy egy meglévő fürtön futó önálló művelet.

- Telepítés a létrehozási vagy frissítési ciklus során

  Ha engedélyezni szeretné a Azure Policy bővítményt egy új, önfelügyelt fürt létrehozásakor vagy egy meglévő fürt frissítéseként, adja meg a [bővítmények](https://github.com/Azure/aks-engine/tree/master/examples/addons/azure-policy) tulajdonságának fürtjének definícióját az AK motorhoz.

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true
  }]
  ```

  A szolgáltatással kapcsolatos további információkért tekintse meg a külső útmutató az [AK-motor-fürt definícióját](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).

- Telepítés meglévő fürtben Helm-diagramokkal

  A következő lépésekkel készítse elő a fürtöt, és telepítse a bővítményt:

  1. Telepítse a [Helm 3](https://v3.helm.sh/docs/intro/install/)programot.

  1. Adja hozzá a Azure Policy-tárházat a Helmhoz.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     További információ: [Helm chart – rövid útmutató](https://helm.sh/docs/using_helm/#quickstart-guide).

  1. Telepítse a bővítményt egy Helm diagrammal. A helyére írja be az `<subscriptionId>` előfizetés-azonosítóját és `<aks engine cluster resource group>` az erőforráscsoportot, amelyben az AK-motor saját felügyeletű Kubernetes-fürtje található.

     ```bash
     helm install azure-policy-addon azure-policy/azure-policy-addon-aks-engine --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     További információ arról, hogy a Hogyan telepíti a kiegészítő Helm-diagramot: a [Azure Policy a Helm diagram definíciója](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-aks-engine) a githubon.

     > [!NOTE]
     > A Azure Policy-bővítmény és az erőforráscsoport-azonosító közötti kapcsolat miatt Azure Policy minden erőforráscsoport esetében csak egy AK-beli motor-fürtöt támogat.

A következő parancs futtatásával ellenőrizheti, hogy a bővítmény telepítése sikeres volt-e, és hogy fut-e az _Azure-Policy_ és a _forgalomirányító_ hüvelye:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="policy-language"></a>Házirend nyelve

A Kubernetes kezelésének Azure Policy nyelvi szerkezete a meglévő szabályzat-definíciókat követi. Az [erőforrás-szolgáltatói móddal](./definition-structure.md#resource-provider-modes) `Microsoft.Kubernetes.Data` a Kubernetes-fürtök kezeléséhez a hatások [naplózása](./effects.md#audit) és [elutasítása](./effects.md#deny) történik. A _naplózási_ és a _megtagadási_ adatoknak meg kell adniuk az [Opa-korlátozási keretrendszer](https://github.com/open-policy-agent/frameworks/tree/master/constraint) és a forgalomirányító v3 használatának **részleteit** .

A _details. constraintTemplate_ és a _details. megkötés_ tulajdonságok részeként a házirend-definícióban Azure Policy átadja ezen [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) URI-azonosítóját a bővítménynek. A Rego az a nyelv, amelyet az OPA és a forgalomirányító támogat a Kubernetes-fürtre irányuló kérelem érvényesítéséhez. A Kubernetes-kezelés meglévő szabványának támogatásával a Azure Policy lehetővé teszi a meglévő szabályok újbóli használatát, és azok párosítását Azure Policy egy egységes Felhőbeli megfelelőségi jelentéskészítési élmény érdekében. További információ: [What is Rego?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego).

## <a name="assign-a-built-in-policy-definition"></a>Beépített szabályzat-definíció kiosztása

Ahhoz, hogy szabályzat-definíciót rendeljen a Kubernetes-fürthöz, hozzá kell rendelnie a megfelelő szerepköralapú hozzáférés-vezérlés (RBAC) házirend-hozzárendelési műveleteit. A beépített RBAC-szerepkörök **erőforrás-házirend közreműködője** és **tulajdonosa** rendelkezik ezekkel a műveletekkel. További információ: [RBAC-engedélyek Azure Policy](../overview.md#rbac-permissions-in-azure-policy).

A következő lépésekkel megkeresheti a fürt kezelésére szolgáló beépített szabályzat-definíciókat a Azure Portal használatával:

1. Indítsa el a Azure Policy szolgáltatást a Azure Portal. Válassza a **minden szolgáltatás** lehetőséget a bal oldali ablaktáblán, majd keresse meg és válassza ki a **házirend**elemet.

1. A Azure Policy lap bal oldali ablaktábláján válassza a **definíciók**lehetőséget.

1. A kategória legördülő listából válassza az **összes kijelölése lehetőséget** a szűrő törléséhez, majd válassza a **Kubernetes**lehetőséget.

1. Válassza ki a házirend-definíciót, majd kattintson a **hozzárendelés** gombra.

1. Állítsa a **hatókört** arra a felügyeleti csoportra, előfizetésre vagy Kubernetes, amelyben a házirend-hozzárendelés érvényes lesz.

   > [!NOTE]    
   > A Azure Policy Kubernetes-definícióhoz való hozzárendeléséhez a **hatókörnek** tartalmaznia kell a fürterőforrás-t. Az KABAi motor fürtjében a **hatókörnek** a fürt erőforráscsoport kell lennie.

1. Adja meg a szabályzat hozzárendelésének **nevét** és **leírását** , hogy könnyen azonosítható legyen.    

1. A [szabályzat kényszerítésének](./assignment-structure.md#enforcement-mode) beállítása az értékek egyikére    
   az alábbiakban.   

   - **Engedélyezve** – a szabályzat érvénybe léptetése a fürtön. A Kubernetes vonatkozó beléptetési kérelmek megtagadva.    

   - **Letiltva** – nem kényszeríti ki a szabályzatot a fürtön. A Kubernetes vonatkozó beléptetési kérelmeket nem tagadja meg a rendszer. A megfelelőség értékelésének eredményei továbbra is elérhetők. Amikor új szabályzat-definíciókat hoz létre a fürtök futtatásához, a _letiltott_ beállítás hasznos lehet a szabályzat-definíció teszteléséhez, mivel a rendszer nem tagadja meg a beléptetési kérelmek megsértését.

1. Válassza a **Tovább** lehetőséget. 

1. **Paraméterek értékének** beállítása 

   - Ha ki szeretné zárni a Kubernetes-névtereket a szabályzat kiértékelése alól, a névtér **kizárása**paraméterben határozza meg a névterek listáját. Azt javasoljuk, hogy zárja ki a következőket: _Kube-System_, _forgalomirányító-System_és _Azure-arc_.

1. Válassza az **Áttekintés + létrehozás** lehetőséget.

Másik megoldásként használja a [szabályzat társítása – portál](../assign-policy-portal.md) rövid útmutatót a Kubernetes szabályzat megkereséséhez és hozzárendeléséhez. Keressen egy Kubernetes házirend-definíciót a "naplózási virtuális gépek" minta helyett.

> [!IMPORTANT]
> A beépített szabályzat-definíciók a **Kubernetes**kategóriába tartozó Kubernetes-fürtökhöz érhetők el. A beépített szabályzat-definíciók listáját lásd: Kubernetes- [minták](../samples/built-in-policies.md#kubernetes).

## <a name="policy-evaluation"></a>Szabályzat kiértékelése

A bővítmény a (z) Azure Policy szolgáltatással ellenőrzi, hogy a házirend-hozzárendelések 15 percenként változnak-e.
A frissítési ciklus során a bővítmény ellenőrzi a módosításokat. Ezek a változások a megkötési sablonok és megkötések létrehozásához, frissítéséhez vagy törléséhez jönnek létre.

Kubernetes-fürtben, ha a névtér a következő címkék valamelyikével rendelkezik, a szabályok megsértésével rendelkező beléptetési kérelmeket nem tagadja meg a rendszer. A megfelelőség értékelésének eredményei továbbra is elérhetők.

- `control-plane`
- `admission.policy.azure.com/ignore`

> [!NOTE]
> Habár a fürt rendszergazdája jogosult lehet megkötési sablonok létrehozására és frissítésére, és a Azure Policy bővítmény által telepített erőforrások megkötésére, ezek a nem támogatott forgatókönyvek, mert a manuális frissítések felül vannak írva. A forgalomirányító továbbra is kiértékeli azokat a házirendeket, amelyek a bővítmény telepítése és a Azure Policy szabályzat-definíciók kiosztása előtt léteztek.

A bővítmény 15 percenként meghívja a fürt teljes vizsgálatát. Miután összegyűjtötte a teljes vizsgálat részleteit és minden valós idejű értékelést a fürtön történt megkísérelt módosításokat, a bővítmény jelentést készít az eredményről Azure Policy a [megfelelőségi adatokba](../how-to/get-compliance-data.md) , például bármely Azure Policy hozzárendelésbe való felvételhez. A naplózási ciklusban csak az aktív házirend-hozzárendelések eredményeit adja vissza a rendszer. A naplózási eredmények a sikertelen korlátozás állapota mezőjében megjelenő [szabálysértésként](https://github.com/open-policy-agent/gatekeeper#audit) is megtekinthetők.

> [!NOTE]
> A Kubernetes-fürtök Azure Policy összes megfelelőségi jelentése magában foglalja az elmúlt 45 percen belüli összes jogsértést. Az időbélyegző azt jelzi, hogy mikor történt megsértés.

## <a name="logging"></a>Naplózás

Kubernetes-vezérlőként/tárolóként az _Azure-Policy_ és a _forgalomirányító_ hüvelyek is megőrzik a naplókat a Kubernetes-fürtben. A naplók elérhetők a Kubernetes-fürt elemzések **lapján.**
További információ: [a Kubernetes-fürt teljesítményének figyelése a Azure monitor for containers szolgáltatással](../../../azure-monitor/insights/container-insights-analyze.md).

A bővítmények naplófájljainak megtekintéséhez használja a következőt `kubectl` :

```bash
# Get the azure-policy pod name installed in kube-system namespace
kubectl logs <azure-policy pod name> -n kube-system

# Get the gatekeeper pod name installed in gatekeeper-system namespace
kubectl logs <gatekeeper pod name> -n gatekeeper-system
```

További információ: a forgalomirányító dokumentációjának [hibakeresése](https://github.com/open-policy-agent/gatekeeper#debugging) .

## <a name="remove-the-add-on"></a>A bővítmény eltávolítása

### <a name="remove-the-add-on-from-aks"></a>A bővítmény eltávolítása az AK-ból

Ha el szeretné távolítani a Azure Policy-bővítményt az AK-fürtből, használja a Azure Portal vagy az Azure CLI-t:

- Azure Portal

  1. Indítsa el az AK szolgáltatást a Azure Portal a **minden szolgáltatás**elemre kattintva, majd keresse meg és válassza ki a **Kubernetes Services szolgáltatást**.

  1. Válassza ki az AK-fürtöt, ahol le szeretné tiltani a Azure Policy-bővítményt.

  1. A Kubernetes szolgáltatás oldalának bal oldalán válassza a **házirendek (előzetes verzió)** lehetőséget.

     :::image type="content" source="../media/policy-for-kubernetes/policies-preview-from-aks-cluster.png" alt-text="Házirend-definíciók az AK-fürtből" border="false":::

  1. A Főoldalon kattintson a **bővítmény letiltása** gombra.

     :::image type="content" source="../media/policy-for-kubernetes/disable-policy-add-on.png" alt-text="Az AK-bővítmény Azure Policyának letiltása" border="false":::

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="remove-the-add-on-from-azure-arc-enabled-kubernetes"></a>A bővítmény eltávolítása az Azure arc-kompatibilis Kubernetes

Ha el szeretné távolítani a Azure Policy-bővítményt és a forgalomirányítót az Azure arc-kompatibilis Kubernetes-fürtről, futtassa a következő Helm-parancsot:

```bash
helm uninstall azure-policy-addon
```

### <a name="remove-the-add-on-from-aks-engine"></a>A bővítmény eltávolítása az AK-motorból

Ha el szeretné távolítani a Azure Policy-bővítményt és a forgalomirányító-t az KABAi motor fürtjéből, használja a bővítmény telepítésének módját a következő módon:

- Ha telepítve van, állítsa be az **addons** tulajdonságot az AK-motorhoz tartozó fürt definíciójában:

  Az _Azure-Policy_ **addons** tulajdonságának hamis értékre való módosítása után telepítse újra a fürt definícióját az AK-motorra:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

  További információkért lásd: az [Kabai motor – Azure Policy bővítmény letiltása](https://github.com/Azure/aks-engine/blob/master/examples/addons/azure-policy/README.md#disable-azure-policy-add-on).

- Ha Helm-diagramokkal van telepítve, futtassa a következő Helm-parancsot:

  ```bash
  helm uninstall azure-policy-addon
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Azure Policy bővítmény által gyűjtött diagnosztikai adatok

A Kubernetes Azure Policy-bővítménye korlátozott számú fürt diagnosztikai adatokat gyűjt. A diagnosztikai adat a szoftverrel és teljesítménnyel kapcsolatos létfontosságú technikai jellegű adat. A következő módokon használható:

- Azure Policy bővítmény naprakészen tartása
- Azure Policy-bővítmény biztonságos, megbízható, teljesítménybeli megőrzése
- A bővítmény használatának összesített elemzésével Azure Policy kiegészítő bővítmények továbbfejlesztése

A bővítmény által gyűjtött információk nem személyes adatok. A rendszer jelenleg a következő adatokat gyűjti össze:

- Azure Policy bővítmény ügynökének verziója
- Fürt típusa
- Fürt régiója
- Fürterőforrás-csoport
- Fürterőforrás-azonosító
- Fürt-előfizetés azonosítója
- Fürt operációs rendszer (példa: Linux)
- Fürt városa (példa: Seattle)
- Fürt állapota vagy tartománya (példa: Washington)
- Fürt országa vagy régiója (példa: Egyesült Államok)
- Az ügynök telepítése során Azure Policy bővítmény által észlelt kivételek/hibák a szabályzat kiértékelése során
- A Azure Policy bővítmény által nem telepített forgalomirányító házirend-definíciók száma

## <a name="next-steps"></a>Következő lépések

- Tekintse át a példákat [Azure Policy mintákon](../samples/index.md).
- A [Szabályzatdefiníciók struktúrájának](definition-structure.md) áttekintése.
- A [Szabályzatok hatásainak ismertetése](effects.md).
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](../how-to/programmatically-create.md).
- Ismerje meg, hogyan [kérheti le a megfelelőségi információkat](../how-to/get-compliance-data.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](../how-to/remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).