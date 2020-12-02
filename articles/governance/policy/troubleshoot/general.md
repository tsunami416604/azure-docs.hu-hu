---
title: Gyakori hibák elhárítása
description: Ismerje meg, hogy miként lehet elhárítani a szabályzat-definíciókat, a különböző SDK-t és a Kubernetes bővítményét.
ms.date: 12/01/2020
ms.topic: troubleshooting
ms.openlocfilehash: f3667988d527100507d308887338278e1200d454
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510998"
---
# <a name="troubleshoot-errors-using-azure-policy"></a>Hibák elhárítása a Azure Policy használatával

A házirend-definíciók létrehozásakor, az SDK-val való együttműködésben vagy a Kubernetes-bővítmény [Azure Policy](../concepts/policy-for-kubernetes.md) beállításában hibák léphetnek fel. Ez a cikk az esetlegesen felmerülő általános hibákat és azok megoldását ismerteti.

## <a name="finding-error-details"></a>Hiba részleteinek megállapítása

A hiba részleteinek helye a hibát okozó művelettől függ.

- Ha egyéni házirenddel dolgozik, próbálja ki a Azure Portal a séma vagy az eredményül kapott [megfelelőségi](../how-to/get-compliance-data.md) információk átadásával kapcsolatban, hogy láthassa az erőforrások kiértékelésének módját.
- Ha különböző SDK-val dolgozik, az SDK részletesen ismerteti, hogy a függvény miért nem sikerült.
- A Kubernetes beépülő moduljának használatakor Kezdje a [naplózást](../concepts/policy-for-kubernetes.md#logging) a fürtben.

## <a name="general-errors"></a>Általános hibák

### <a name="scenario-alias-not-found"></a>Forgatókönyv: az alias nem található

#### <a name="issue"></a>Probléma

A Azure Policy [aliasokat](../concepts/definition-structure.md#aliases) használ a Azure Resource Manager tulajdonságok leképezéséhez.

#### <a name="cause"></a>Ok

Egy házirend-definícióban helytelen vagy nem létező alias van használatban.

#### <a name="resolution"></a>Feloldás

Először ellenőrizze, hogy a Resource Manager-tulajdonságnak van-e aliasa. Az elérhető aliasok megkereséséhez használja [Azure Policy bővítményt a Visual Studio Code](../how-to/extension-for-vscode.md), az [Azure Resource Graph](../../resource-graph/samples/starter.md#distinct-alias-values)vagy az SDK használatával. Ha egy Resource Manager-tulajdonság aliasa nem létezik, hozzon létre egy támogatási jegyet.

### <a name="scenario-evaluation-details-not-up-to-date"></a>Forgatókönyv: az értékelés részletei nem naprakészek

#### <a name="issue"></a>Probléma

Egy erőforrás a "nincs elindítva" állapotban van, vagy a megfelelőségi adatok nem aktuálisak.

#### <a name="cause"></a>Ok

Az új szabályzatok vagy kezdeményezési hozzárendelések alkalmazása körülbelül 30 percet vesz igénybe. Egy meglévő hozzárendelés hatókörén belüli új vagy frissített erőforrások körülbelül 15 perccel később elérhetővé válnak. A standard megfelelőségi vizsgálat 24 óránként történik. További információ: [kiértékelési eseményindítók](../how-to/get-compliance-data.md#evaluation-triggers).

#### <a name="resolution"></a>Feloldás

Először is várjon, amíg az értékelés befejeződik, és a megfelelőségi eredmények elérhetővé válnak Azure Portal vagy SDK-ban. Ha Azure PowerShell vagy REST API használatával szeretne új értékelési vizsgálatot kezdeni, tekintse [meg az igény szerinti értékelés vizsgálatát](../how-to/get-compliance-data.md#on-demand-evaluation-scan)ismertető témakört.

### <a name="scenario-compliance-not-as-expected"></a>Forgatókönyv: nem a várt megfelelőség

#### <a name="issue"></a>Probléma

Egy erőforrás nem a _megfelelő_ vagy _nem megfelelő_, az adott erőforrás számára várt értékelési állapotban van.

#### <a name="cause"></a>Ok

Az erőforrás nem a megfelelő hatókörben van a házirend-hozzárendeléshez, vagy a házirend-definíció nem a kívánt módon működik.

#### <a name="resolution"></a>Feloldás

A házirend-definíció hibaelhárításához kövesse az alábbi lépéseket:

1. Először is várjon, amíg az értékelés befejeződik, és a megfelelőségi eredmények elérhetővé válnak Azure Portal vagy SDK-ban. Ha Azure PowerShell vagy REST API használatával szeretne új értékelési vizsgálatot kezdeni, tekintse [meg az igény szerinti értékelés vizsgálatát](../how-to/get-compliance-data.md#on-demand-evaluation-scan)ismertető témakört.
1. Győződjön meg arról, hogy a hozzárendelési paraméterek és a hozzárendelési hatókör helyesen van beállítva.
1. Ellenőrizze a [szabályzatdefiníciós módot](../concepts/definition-structure.md#mode):
   - Az összes erőforrástípus "all" üzemmódja.
   - Az "indexelt" mód, ha a házirend-definíció címkéket vagy helyet keres.
1. Győződjön meg arról, hogy az erőforrás hatóköre nincs [kizárva](../concepts/assignment-structure.md#excluded-scopes) vagy [mentesített](../concepts/exemption-structure.md).
1. Ha a szabályzat-hozzárendelés megfelelősége `0/0` erőforrásokat mutat, a hozzárendelési hatókörön belül nem határoztak meg erőforrásokat. A házirend-definíciót és a hozzárendelési hatókört egyaránt ellenőriznie kell.
1. Ahhoz, hogy egy nem megfelelő erőforrást a megfelelőnek kellene lennie, vizsgálja meg a meg [nem felelés okainak megállapítását](../how-to/determine-non-compliance.md). A definíció és a kiértékelt tulajdonság értékének összehasonlítása azt jelzi, hogy az erőforrás miért nem megfelelő.
   - Ha a **célként megadott érték** helytelen, módosítsa a házirend-definíciót.
   - Ha az **aktuális érték** helytelen, ellenőrizze az erőforrás adattartalmát a használatával `resources.azure.com` .
1. Tekintse meg a hibaelhárítást: a többi gyakori probléma és megoldás esetében [nem a várt módon érvényesíthető](#scenario-enforcement-not-as-expected) .

Ha továbbra is problémája van a duplikált és testreszabott beépített szabályzat-definícióval vagy egyéni definícióval, hozzon létre egy támogatási jegyet a **szabályzat készítése** területen a probléma helyes átirányításához.

### <a name="scenario-enforcement-not-as-expected"></a>Forgatókönyv: a végrehajtás nem a várt módon történik

#### <a name="issue"></a>Probléma

Az Azure Policy által várhatóan végrehajtott erőforrás nem, és nincs bejegyzés az Azure-beli [tevékenység naplójában](../../../azure-monitor/platform/platform-logs-overview.md).

#### <a name="cause"></a>Ok

A házirend-hozzárendelés _le_ lett állítva a [enforcementMode](../concepts/assignment-structure.md#enforcement-mode) . Míg a kényszerítési mód le van tiltva, a házirend hatálya nem kényszerített, és nincs bejegyzés a tevékenység naplójában.

#### <a name="resolution"></a>Feloldás

A szabályzat-hozzárendelés kényszerítésének hibaelhárításához kövesse az alábbi lépéseket:

1. Először is várjon, amíg az értékelés befejeződik, és a megfelelőségi eredmények elérhetővé válnak Azure Portal vagy SDK-ban. Ha Azure PowerShell vagy REST API használatával szeretne új értékelési vizsgálatot kezdeni, tekintse [meg az igény szerinti értékelés vizsgálatát](../how-to/get-compliance-data.md#on-demand-evaluation-scan)ismertető témakört.
1. Győződjön meg arról, hogy a hozzárendelési paraméterek és a hozzárendelési hatókör helyesen van beállítva, és hogy a **EnforcementMode** _engedélyezve_ van.
1. Ellenőrizze a [szabályzatdefiníciós módot](../concepts/definition-structure.md#mode):
   - Az összes erőforrástípus "all" üzemmódja.
   - Az "indexelt" mód, ha a házirend-definíció címkéket vagy helyet keres.
1. Győződjön meg arról, hogy az erőforrás hatóköre nincs [kizárva](../concepts/assignment-structure.md#excluded-scopes) vagy [mentesített](../concepts/exemption-structure.md).
1. Ellenőrizze, hogy az erőforrás hasznos adatai megegyeznek-e a szabályzat logikájával. Ezt a [har-nyomkövetés rögzítésével](../../../azure-portal/capture-browser-trace.md) vagy az ARM-sablon tulajdonságainak áttekintésével végezheti el.
1. Hibaelhárítás ellenőrzése: az egyéb gyakori problémák és megoldások esetében [nem a várt megfelelőség](#scenario-compliance-not-as-expected) .

Ha továbbra is problémája van a duplikált és testreszabott beépített szabályzat-definícióval vagy egyéni definícióval, hozzon létre egy támogatási jegyet a **szabályzat készítése** területen a probléma helyes átirányításához.

### <a name="scenario-denied-by-azure-policy"></a>Forgatókönyv: Azure Policy megtagadva

#### <a name="issue"></a>Probléma

Egy erőforrás létrehozása vagy frissítése megtagadva.

#### <a name="cause"></a>Ok

A hatókörhöz tartozó szabályzat-hozzárendelés az új vagy frissített erőforrás megfelel egy [megtagadási](../concepts/effects.md#deny) hatású házirend-definíció feltételeinek. Ezek a definíciók nem hozhatók létre vagy nem frissíthetők.

#### <a name="resolution"></a>Feloldás

Egy megtagadási szabályzat-hozzárendelés hibaüzenete tartalmazza a szabályzat-definíciót és a szabályzat-hozzárendelési azonosítókat. Ha az üzenetben szereplő hibaüzenetek nem maradnak meg, akkor a [tevékenység naplójában](../../../azure-monitor/platform/activity-log.md#view-the-activity-log)is elérhető. Ezekkel az információkkal további részleteket tudhat meg az erőforrás-korlátozásokról, és módosíthatja a kérésben szereplő erőforrás-tulajdonságokat az engedélyezett értékek egyeztetéséhez.

## <a name="template-errors"></a>Sablon hibái

### <a name="scenario-policy-supported-functions-processed-by-template"></a>Forgatókönyv: a szabályzat által támogatott függvények sablon szerint dolgozva

#### <a name="issue"></a>Probléma

Azure Policy számos Azure Resource Manager sablon (ARM-sablon) függvényt és függvényt támogat, amelyek csak egy házirend-definícióban érhetők el. A Resource Manager ezeket a függvényeket egy központi telepítés részeként dolgozza fel a házirend-definíció részeként.

#### <a name="cause"></a>Ok

A támogatott függvények, például a vagy a használata esetén a `parameter()` `resourceGroup()` függvény feldolgozott eredménye a központi telepítéskor, a házirend-definícióhoz való kilépés és a Azure Policy motor feldolgozása helyett.

#### <a name="resolution"></a>Feloldás

Ha egy függvényt át szeretne adni egy házirend-definíció részévé, a teljes karakterláncot a `[` tulajdonsághoz hasonló módon kell kinéznie `[[resourceGroup().tags.myTag]` . A escape-karakter hatására a Resource Manager az értéket karakterláncként kezeli a sablon feldolgozásakor. Azure Policy ezután a függvényt a házirend-definícióba helyezi, ami lehetővé teszi, hogy az a várt módon dinamikus legyen. További információ: [szintaxis és kifejezések Azure Resource Manager sablonokban](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="add-on-for-kubernetes-installation-errors"></a>Bővítmény a Kubernetes telepítési hibáihoz

### <a name="scenario-install-using-helm-chart-fails-on-password"></a>Forgatókönyv: a Helm diagram használatával történő telepítés sikertelen a jelszóval

#### <a name="issue"></a>Probléma

A `helm install azure-policy-addon` parancs a következő üzenetek egyikével meghiúsul:

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>Ok

A generált jelszó tartalmaz egy vesszőt ( `,` ), amely a Helm diagramra van felosztva.

#### <a name="resolution"></a>Feloldás

A jelszó értékében válassza a vessző ( `,` ) értéket, ha `helm install azure-policy-addon` fordított perjelet ( `\` ) használ.

### <a name="scenario-install-using-helm-chart-fails-as-name-already-exists"></a>Forgatókönyv: a telepítés a Helm diagram használatával meghiúsul, mert a név már létezik

#### <a name="issue"></a>Probléma

A `helm install azure-policy-addon` parancs a következő üzenettel meghiúsul:

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>Ok

A (z) nevű Helm `azure-policy-addon` -diagram már telepítve van vagy részben telepítve van.

#### <a name="resolution"></a>Feloldás

Kövesse az utasításokat a [Kubernetes-bővítmény Azure Policy eltávolításához](../concepts/policy-for-kubernetes.md#remove-the-add-on), majd futtassa újra a `helm install azure-policy-addon` parancsot.

### <a name="scenario-azure-virtual-machine-user-assigned-identities-are-replaced-by-system-assigned-managed-identities"></a>Forgatókönyv: az Azure-beli virtuális gépek felhasználó által hozzárendelt identitásai a rendszer által hozzárendelt felügyelt identitások helyére kerülnek.

#### <a name="issue"></a>Probléma

Miután hozzárendelte a vendég-konfigurációs házirend kezdeményezéseit a számítógépeken található beállítások naplózásához, a rendszer már nem rendeli hozzá a számítógéphez rendelt, felhasználóhoz rendelt felügyelt identitásokat. Csak rendszerhez rendelt felügyelt identitás van hozzárendelve.

#### <a name="cause"></a>Ok

A vendég konfiguráció DeployIfNotExists-definíciókban korábban használt szabályzat-definíciók biztosítják, hogy a rendszerhez hozzárendelt identitás hozzá legyen rendelve a géphez, de a felhasználó által hozzárendelt identitás-hozzárendelések is el lettek távolítva.

#### <a name="resolution"></a>Feloldás

Azok a definíciók, amelyek korábban a problémát okozták, elavultként jelennek meg, \[ \] és az előfeltételeket a felhasználó által hozzárendelt felügyelt identitás eltávolítása nélkül kezelő szabályzat-definíciók váltja fel. Manuális lépésre van szükség. Törölje az elavultként megjelölt házirend-hozzárendeléseket, \[ \] és cserélje le azokat a frissített előfeltétel-házirend kezdeményezésre, valamint az eredetivel megegyező nevű házirend-definícióra.

Részletes tájékoztatást a következő blogbejegyzésben talál:

[A vendég konfigurációjának naplózási házirendjeihez kiadott fontos változás](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316)

## <a name="add-on-for-kubernetes-general-errors"></a>Bővítmény a Kubernetes általános hibáihoz

### <a name="scenario-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-due-to-egress-restrictions"></a>Forgatókönyv: a bővítmény nem tudja elérni a Azure Policy szolgáltatási végpontot a kimenő forgalomra vonatkozó korlátozások miatt

#### <a name="issue"></a>Probléma

A bővítmény nem tudja elérni a Azure Policy szolgáltatás végpontját, és a következő hibák valamelyikét adja vissza:

- `failed to fetch token, service not reachable`
- `Error getting file "Get https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml: dial tcp 151.101.228.133.443: connect: connection refused`

#### <a name="cause"></a>Ok

Ez a probléma akkor fordul elő, ha a kimenő forgalom zárolva van.

#### <a name="resolution"></a>Feloldás

Győződjön meg arról, hogy a következő cikkek tartományai és portjai nyitva vannak:

- [Szükséges kimenő hálózati szabályok és teljes tartománynevek az AK-fürtökhöz](../../../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
- [Azure Policy bővítmény telepítése az Azure arc használatára képes Kubernetes (előzetes verzió)](../concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)

### <a name="scenario-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-due-to-aad-pod-identity-configuration"></a>Forgatókönyv: a bővítmény nem tudja elérni a Azure Policy szolgáltatási végpontot a HRE-Pod-Identity konfiguráció miatt

#### <a name="issue"></a>Probléma

A bővítmény nem tudja elérni a Azure Policy szolgáltatás végpontját, és a következő hibák valamelyikét adja vissza:

- `azure.BearerAuthorizer#WithAuthorization: Failed to refresh the Token for request to https://gov-prod-policy-data.trafficmanager.net/checkDataPolicyCompliance?api-version=2019-01-01-preview: StatusCode=404`
- `adal: Refresh request failed. Status Code = '404'. Response body: getting assigned identities for pod kube-system/azure-policy-8c785548f-r882p in CREATED state failed after 16 attempts, retry duration [5]s, error: <nil>`

#### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha a _Add-Pod-Identity_ telepítve van a fürtön, és a _Kube rendszer_ nem zárja ki a _HRE-Pod identitást_.

A _HRE-Pod-Identity_ összetevő csomópont felügyelt identitás (NMI) hüvelye módosítja a csomópontok iptables-t az Azure-példány metaadatainak végpontjának hívására. Ez a beállítás azt jelenti, hogy a metaadat-végpontra vonatkozó összes kérést a NMI akkor is elfogja, ha a pod nem használja a _HRE-Pod-Identity_ kapcsolót.
**AzurePodIdentityException** A CRD úgy konfigurálható, hogy tájékoztassa a _HRE-Pod-Identity_ attribútumot arról, hogy a CRD-ben definiált címkével rendelkező Pod-ból származó metaadatokra irányuló kérések a NMI-ben végzett feldolgozás nélkül legyenek proxy.

#### <a name="resolution"></a>Feloldás

Zárja ki a System hüvelyt a `kubernetes.azure.com/managedby: aks` _Kube-System_ névtérben a _HRE-Pod-Identity_ címkével a **AzurePodIdentityException** CRD konfigurálásával.

További információ: [a HRE Pod Identity letiltása egy adott Pod/alkalmazáshoz](https://azure.github.io/aad-pod-identity/docs/configure/application_exception).

A kivételek konfigurálásához tekintse meg a következő példát:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: mic-exception
  namespace: default
spec:
  podLabels:
    app: mic
    component: mic
---
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: aks-addon-exception
  namespace: kube-system
spec:
  podLabels:
    kubernetes.azure.com/managedby: aks
```

### <a name="scenario-the-resource-provider-isnt-registered"></a>Forgatókönyv: az erőforrás-szolgáltató nincs regisztrálva

#### <a name="issue"></a>Probléma

A bővítmény elérheti a Azure Policy szolgáltatás végpontját, de a következő hibák valamelyikét látja a bővítmény naplófájljaiban:

```
The resource provider 'Microsoft.PolicyInsights' is not registered in subscription '{subId}'. See
https://aka.ms/policy-register-subscription for how to register subscriptions.
```

vagy

```
policyinsightsdataplane.BaseClient#CheckDataPolicyCompliance: Failure responding to request:
StatusCode=500 -- Original Error: autorest/azure: Service returned an error. Status=500
Code="InternalServerError" Message="Encountered an internal server error."
```

#### <a name="cause"></a>Ok

Az `Microsoft.PolicyInsights` erőforrás-szolgáltató nincs regisztrálva, és a bővítménynek regisztrálnia kell a szabályzat-definíciók beolvasásához és a megfelelőségi részletek visszaadásához.

#### <a name="resolution"></a>Feloldás

Regisztrálja az `Microsoft.PolicyInsights` erőforrás-szolgáltatót a fürt előfizetésében. Útmutatásért lásd: [erőforrás-szolgáltató regisztrálása](../../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider).

### <a name="scenario-the-subscription-is-disabled"></a>Forgatókönyv: az előfizetés le van tiltva

#### <a name="issue"></a>Probléma

A bővítmény elérheti a Azure Policy szolgáltatás végpontját, de a következő hibaüzenetet látja:

```
The subscription '{subId}' has been disabled for azure data-plane policy. Please contact support.
```

#### <a name="cause"></a>Ok

Ez a hiba azt jelzi, hogy az előfizetés problémás, és a szolgáltatás jelzője `Microsoft.PolicyInsights/DataPlaneBlocked` hozzá lett adva az előfizetés letiltásához.

#### <a name="resolution"></a>Feloldás

A `azuredg@microsoft.com` probléma kivizsgálásához és megoldásához lépjen kapcsolatba a szolgáltatás csapatával.

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

- Választ kaphat a szakértőktől a [Microsoft Q&A](/answers/topics/azure-policy.html)használatával.
- Az [@AzureSupport](https://twitter.com/azuresupport) Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával, a hivatalos Microsoft Azure fiókkal csatlakozhat a felhasználói élmény fokozásához: válaszok, támogatás és szakértők.
- Ha további segítségre van szüksége, egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése** lehetőséget.
