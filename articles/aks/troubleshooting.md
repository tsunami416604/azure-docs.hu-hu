---
title: Az Azure Kubernetes szolgáltatással kapcsolatos gyakori problémák elhárítása
description: Útmutató az Azure Kubernetes szolgáltatás (ak) használata során felmerülő gyakori problémák elhárításához és megoldásához
services: container-service
ms.topic: troubleshooting
ms.date: 06/20/2020
ms.openlocfilehash: 4a28ebd047e4d5e610ea0c895063eb87ce051d45
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2020
ms.locfileid: "89460320"
---
# <a name="aks-troubleshooting"></a>AKS-hibaelhárítás

Az Azure Kubernetes szolgáltatásbeli (ak-beli) fürtök létrehozásakor és kezelésekor esetenként problémák merülhetnek fel. Ez a cikk néhány gyakori problémát és hibaelhárítási lépést részletez.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Általánosságban Hol találhatok információt a Kubernetes kapcsolatos hibák elhárításáról?

Próbálja ki a [hivatalos útmutatót a Kubernetes-fürtök hibaelhárításához](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
A Microsoft Engineering által kiadott [hibaelhárítási útmutató](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)a hüvelyek, a csomópontok, a fürtök és az egyéb funkciók hibaelhárítására is használható.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>"A kvóta túllépve" hibaüzenetet kapok a létrehozás vagy a frissítés során. Mit tegyek? 

 [További magok igénylése](../azure-portal/supportability/resource-manager-core-quotas-request.md).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Mekkora a hüvelyek maximális száma az AK-ban?

A hüvelyek maximális száma alapértelmezés szerint 30, ha AK-fürtöt helyez üzembe a Azure Portalban.
Alapértelmezés szerint a hüvelyek maximális száma 110, ha az Azure CLI-ben helyez üzembe egy AK-fürtöt. (Ügyeljen arra, hogy az Azure CLI legújabb verzióját használja). Ez a beállítás a `–-max-pods` parancsban található jelzővel módosítható `az aks create` .

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>InsufficientSubnetSize hibaüzenetet kapok egy AK-fürt speciális hálózatkezeléssel való üzembe helyezése során. Mit tegyek?

Ez a hiba azt jelzi, hogy a fürtben lévő alhálózatok már nem rendelkeznek a CIDR belüli elérhető IP-címekkel a sikeres erőforrás-hozzárendeléshez. A Kubenet-fürtök esetében a követelmény elegendő IP-terület a fürt minden csomópontja számára. Az Azure CNI-fürtök esetében a követelmény elegendő IP-terület a fürt minden egyes csomópontja és Pod számára.
További információk az [Azure-CNI kialakításáról az IP-címek a hüvelyekhez való hozzárendeléséhez](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

Ezeket a hibákat az [AK-diagnosztika](./concepts-diagnostics.md) is felveszi, amely proaktív módon olyan problémákat okoz, mint például a nem megfelelő alhálózat mérete.

A következő három (3) eset nem megfelelő alhálózati méretet okoz:

1. AK Scale vagy AK Nodepool skálázás
   1. Ha Kubenet használ, akkor ez akkor fordul elő, ha a `number of free IPs in the subnet` értéke **kisebb, mint** a `number of new nodes requested` .
   1. Ha az Azure CNI-t használja, akkor ez akkor fordul elő, ha a `number of free IPs in the subnet` értéke **kisebb, mint** a `number of nodes requested times (*) the node pool's --max-pod value` .

1. AK-frissítés vagy AK-Nodepool frissítése
   1. Kubenet használata esetén ez akkor fordul elő, ha a `number of free IPs in the subnet` értéke **kisebb** , mint a `number of buffer nodes needed to upgrade` .
   1. Ha az Azure CNI-t használja, akkor ez akkor fordul elő, ha a `number of free IPs in the subnet` értéke **kisebb, mint** a `number of buffer nodes needed to upgrade times (*) the node pool's --max-pod value` .
   
   Alapértelmezés szerint az AK-fürtök egy (1) maximális túllépési értéket állítanak be, de ez a frissítési viselkedés testreszabható úgy, hogy [egy csomópont-készlet maximális](upgrade-cluster.md#customize-node-surge-upgrade-preview) túllépését állítja be, ami növeli a frissítés befejezéséhez szükséges elérhető IP-címek számát.

1. AK létrehozása vagy AK-Nodepool hozzáadása
   1. Kubenet használata esetén ez akkor fordul elő, ha a `number of free IPs in the subnet` értéke **kisebb** , mint a `number of nodes requested for the node pool` .
   1. Ha az Azure CNI-t használja, akkor ez akkor fordul elő, ha a `number of free IPs in the subnet` értéke **kisebb, mint** a `number of nodes requested times (*) the node pool's --max-pod value` .

Az új alhálózatok létrehozásával a következő enyhítést lehet elvégezni. Az új alhálózat létrehozásához szükséges engedély a meglévő alhálózat CIDR-tartományának frissítése miatti nem lehetséges.

1. Egy olyan új alhálózat újraépítése, amely a műveleti célokhoz elegendő CIDR-tartománnyal rendelkezik:
   1. Hozzon létre egy új alhálózatot egy új kívánt, nem átfedésben lévő tartománnyal.
   1. Hozzon létre egy új nodepool az új alhálózaton.
   1. A lecserélni kívánt régi alhálózatban lévő régi nodepool kiüríti a hüvelyeket.
   1. Törölje a régi alhálózatot és a régi nodepool.

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>A My Pod CrashLoopBackOff módban ragadt. Mit tegyek?

Előfordulhat, hogy a pod nem ragadja meg ezt a módot. A következőket tekintheti meg:

* Maga a pod, a használatával `kubectl describe pod <pod-name>` .
* A naplók a használatával `kubectl logs <pod-name>` .

A pod-problémák hibaelhárításával kapcsolatos további információkért lásd: [alkalmazások hibakeresése](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-receiving-tcp-timeouts-when-using-kubectl-or-other-third-party-tools-connecting-to-the-api-server"></a>`TCP timeouts` `kubectl` Az API-kiszolgálóhoz való csatlakozáskor vagy más, harmadik féltől származó eszközök használatakor Fogadok
Az AK-ban a slo és a szolgáltatói szerződéseket (SLA-kat) biztosító magok száma alapján vertikálisan méretezhető vezérlési síkok vannak. Ha a kapcsolatok időtúllépését tapasztalja, ellenőrizze az alábbi lépéseket:

- **Az API-parancsok konzisztensek, vagy csak néhányat?** Ha csak néhány, a `tunnelfront` Pod vagy a `aks-link` Pod, amely a csomópont-> vezérlési sík kommunikációjának felel meg, előfordulhat, hogy nem fut állapotban van. Győződjön meg arról, hogy a pod helyet adó csomópontok nem túlzott mértékben vannak kihasználva vagy stressz alatt. Érdemes áthelyezni a saját [ `system` Node-készletbe](use-system-pools.md).
- **Megnyitotta az összes szükséges portot, teljes tartománynevet és IP-címet, amelyek a [kimenő forgalomra vonatkozó dokumentumokat korlátozzák](limit-egress-traffic.md)?** Ellenkező esetben több parancs hívása sikertelen lehet.
- **Az aktuális IP-címe a [jóváhagyott API IP-címtartományok](api-server-authorized-ip-ranges.md)szerint van-e kiértékelve?** Ha ezt a funkciót használja, és az IP-címe nem szerepel a tartományokban, a hívások le lesznek tiltva. 
- **Van ügyfél-vagy alkalmazás-visszaszivárgási hívása az API-kiszolgálónak?** Ügyeljen arra, hogy a gyakori Get hívások helyett az órákat használja, és hogy a harmadik féltől származó alkalmazások ne szivárognak ki ilyen hívásokat. A Istio keverőben például egy hiba okozza, hogy egy új API-kiszolgáló figyeli a kapcsolódást minden alkalommal, amikor a titkos kulcs beolvasása történik. Mivel ez a viselkedés rendszeres időközönként történik, figyelje a kapcsolatok gyors összegyűjtését, és végül az API-kiszolgáló túlterhelését eredményezheti a skálázási minta alapján. https://github.com/istio/istio/issues/19481
- **Sok kiadása van a Helm üzemelő példányában?** Ez a forgatókönyv azt eredményezheti, hogy a kormányrúd túl sok memóriát használ a csomópontokon, és nagy mennyiségű `configmaps` , ami szükségtelen tüskéket okozhat az API-kiszolgálón. Érdemes lehet `--history-max` `helm init` az új Helm 3 konfigurálását és kihasználni. További részletek a következő problémákról: 
    - https://github.com/helm/helm/issues/4821
    - https://github.com/helm/helm/issues/3500
    - https://github.com/helm/helm/issues/4543
- **[A csomópontok közötti belső forgalom blokkolva van?](#im-receiving-tcp-timeouts-such-as-dial-tcp-node_ip10250-io-timeout)**

## <a name="im-receiving-tcp-timeouts-such-as-dial-tcp-node_ip10250-io-timeout"></a>Kapok `TCP timeouts` , például `dial tcp <Node_IP>:10250: i/o timeout`

Ezek az időtúllépések a letiltott csomópontok közötti belső forgalomhoz kapcsolódnak. Ellenőrizze, hogy nem blokkolja-e a forgalmat, például a fürt csomópontjaihoz tartozó alhálózat [hálózati biztonsági csoportjaival](concepts-security.md#azure-network-security-groups) .

## <a name="im-trying-to-enable-role-based-access-control-rbac-on-an-existing-cluster-how-can-i-do-that"></a>A szerepköralapú Access Control (RBAC) szolgáltatást próbálom engedélyezni egy meglévő fürtön. Hogyan tehetem meg?

A szerepköralapú hozzáférés-vezérlés (RBAC) a meglévő fürtökön való engedélyezése jelenleg nem támogatott, ezért az új fürtök létrehozásakor be kell állítani. A RBAC alapértelmezés szerint engedélyezve van, ha a parancssori felület, a portál vagy egy API-verziónál újabb verziót használ `2020-03-01` .

## <a name="i-created-a-cluster-with-rbac-enabled-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Létrehoztam egy olyan fürtöt, amelyen engedélyezve van a RBAC, és most már sok figyelmeztetés jelenik meg a Kubernetes-irányítópulton. A figyelmeztetés nélküli működéshez használt irányítópult. Mit tegyek?

A figyelmeztetések oka, hogy a fürtön engedélyezve van a RBAC, és az irányítópulthoz való hozzáférés alapértelmezés szerint korlátozva van. Általánosságban véve ez a megközelítés jó gyakorlat, mert az irányítópultnak a fürt összes felhasználójára vonatkozó alapértelmezett expozíciója biztonsági fenyegetésekhez vezethet. Ha továbbra is engedélyezni szeretné az irányítópultot, kövesse az [ebben a blogbejegyzésben](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/)leírt lépéseket.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Nem tudok naplókat beolvasni a kubectl-naplók használatával, vagy nem tudok csatlakozni az API-kiszolgálóhoz. "Hiba a kiszolgálóról: hiba a háttérrendszer tárcsázásakor: telefonos TCP...". Mit tegyek?

Győződjön meg arról, hogy a 22-es, 9000 és 1194 portok nyitva vannak az API-kiszolgálóhoz való csatlakozáshoz. A parancs használatával győződjön meg arról, hogy a `tunnelfront` vagy a `aks-link` Pod a *Kube-System* névtérben fut-e `kubectl get pods --namespace kube-system` . Ha nem, akkor kényszerítse a pod törlését, és a rendszer újraindul.

## <a name="im-getting-tls-client-offered-only-unsupported-versions-from-my-client-when-connecting-to-aks-api-what-should-i-do"></a>Az `"tls: client offered only unsupported versions"` én ügyfelem az AK API-hoz való csatlakozáskor kapok. Mit tegyek?

A minimálisan támogatott TLS-verzió a TLS 1,2.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Megpróbálok frissíteni vagy méretezni, és `"Changing property 'imageReference' is not allowed"` hibaüzenetet kapok. Hogyan kijavítani ezt a problémát?

Előfordulhat, hogy ez a hiba azért fordul elő, mert a címkéket az AK-fürtön belüli ügynök-csomópontokban módosította. A címkék és az erőforrások egyéb tulajdonságai módosíthatók vagy törölhetők a MC_ * erőforráscsoport váratlan eredményekhez vezethet. Az AK-fürt MC_ * csoportjában található erőforrások módosítása megszakítja a szolgáltatási szint célkitűzését (SLO).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Hibaüzeneteket kapok, hogy a fürtem hibás állapotban van, és a frissítés vagy a skálázás nem fog működni, amíg meg nem történik a javítás.

*Ez a hibaelhárítási segítség a következő címről származik: https://aka.ms/aks-cluster-failed*

Ez a hiba akkor fordul elő, ha a fürtök több okból is hibás állapotba kerülnek. Kövesse az alábbi lépéseket a fürt sikertelen állapotának feloldásához a korábban sikertelen művelet újbóli megkísérlése előtt:

1. Amíg a fürt állapota nem áll fenn `failed` , `upgrade` a `scale` műveletek sikertelenek lesznek. A leggyakoribb gyökérszintű problémák és megoldások a következők:
    * A nem **megfelelő számítási (CRP-) kvóta**skálázása. A megoldáshoz először a kvótán belüli, stabil cél állapotba kell állítani a fürtöt. Ezután kövesse az alábbi [lépéseket a számítási kvóta növelésének](../azure-portal/supportability/resource-manager-core-quotas-request.md) megkezdéséhez, mielőtt a kezdeti kvóta-korlátokon felül ismét fel kellene mérni.
    * Fürt méretezése speciális hálózatkezeléssel és nem **elegendő alhálózat (Hálózatkezelés) erőforrásokkal**. A megoldáshoz először a kvótán belüli, stabil cél állapotba kell állítani a fürtöt. Ezután kövesse az [alábbi lépéseket az erőforrás-kvóta növelésének](../azure-resource-manager/templates/error-resource-quota.md#solution) megkezdéséhez, mielőtt a kezdeti kvóta-korlátokon felül ismét fel kellene mérni a skálázást.
2. Miután megoldotta a frissítési hiba kiváltó okát, a fürtnek sikeres állapotban kell lennie. A sikeres állapot ellenőrzése után próbálja megismételni az eredeti műveletet.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-upgraded-or-has-failed-upgrade"></a>Hibákba ütközik, amikor megpróbálja frissíteni vagy méretezni az adott állapotot a fürt frissítése vagy frissítése sikertelen volt.

*Ez a hibaelhárítási segítség a következő címről származik: https://aka.ms/aks-pending-upgrade*

 Nem lehet egyszerre frissíteni és méretezni a fürt vagy a csomópont készletét. Ehelyett minden Művelettípus csak akkor fejeződik be a célként megadott erőforráson, ha a következő kérelem ugyanarra az erőforrásra van leképezve. Ennek eredményeképpen a műveletek korlátozottak, amikor az aktív verziófrissítési vagy méretezési műveletek történnek vagy megkíséreltek. 

A probléma diagnosztizálásához a `az aks show -g myResourceGroup -n myAKSCluster -o table` fürt részletes állapotának lekéréséhez. Az eredmény alapján:

* Ha a fürt aktívan frissít, várjon, amíg a művelet befejeződik. Ha sikerült, próbálkozzon újra a korábban sikertelen művelettel.
* Ha a fürt nem tudta frissíteni a frissítést, kövesse az előző szakaszban ismertetett lépéseket.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Áthelyezhetem a fürtöt egy másik előfizetésbe vagy az előfizetésem a fürttel egy új bérlőre?

Ha az AK-fürtöt egy másik előfizetésbe helyezte át, vagy a fürt előfizetése egy új bérlőre került át, a fürt nem fog működni a fürt identitásának hiánya miatt. Az **AK nem támogatja a fürtök áthelyezését az előfizetések vagy a bérlők között** a korlátozás miatt.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>A virtuálisgép-méretezési csoportokat igénylő szolgáltatások használatára vonatkozó hibák jelentkeznek

*Ez a hibaelhárítási segítség a aka.ms/aks-vmss-enablement*

Olyan hibák jelenhetnek meg, amelyek jelzik, hogy az AK-fürt nem egy virtuálisgép-méretezési csoporton van, például a következő példában:

**`<agentpoolname>`A AgentPool engedélyezte az automatikus skálázást, de nem Virtual Machine Scale sets**

Az olyan funkciók, mint például a fürt autoskálázása vagy több csomópontos készlet esetén a virtuálisgép-méretezési csoportok szükségesek `vm-set-type` .

A megfelelő dokumentum lépéseinek *megkezdése előtt* kövesse az AK-fürt megfelelő létrehozásához szükséges lépéseket:

* [A fürt automéretező használata](cluster-autoscaler.md)
* [Több Node-készlet létrehozása és használata](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Milyen elnevezési korlátozásokat kell kikényszeríteni az AK-erőforrások és-paraméterek esetében?

*Ez a hibaelhárítási segítség a aka.ms/aks-naming-rules*

Az elnevezési korlátozásokat az Azure platform és az AK is implementálja. Ha egy erőforrás neve vagy paramétere megszakítja az egyik ilyen korlátozást, a rendszer hibaüzenetet küld, amely megkéri, hogy adjon meg egy másik bemenetet. A következő közös elnevezési irányelvek érvényesek:

* A fürt nevének 1-63 karakterből kell állnia. Az egyetlen megengedett karakter betű, szám, kötőjel és aláhúzás. Az első és az utolsó karakternek betűnek vagy számnak kell lennie.
* Az AK-csomópont/*MC_* erőforráscsoport neve kombinálja az erőforráscsoport nevét és az erőforrás nevét. Az automatikusan generált szintaxisának `MC_resourceGroupName_resourceName_AzureRegion` nem lehet nagyobb, mint 80 karakter. Ha szükséges, csökkentse az erőforráscsoport-név vagy az AK-fürt nevének hosszát. [A csomópont-erőforráscsoport nevét is testreszabhatja](cluster-configuration.md#custom-resource-group-name)
* A *dnsPrefix* alfanumerikus értékekkel kell kezdődnie és végződnie, és 1-54 karakter közöttinek kell lennie. Az érvényes karakterek alfanumerikus értékeket és kötőjeleket (-) tartalmazhatnak. A *dnsPrefix* nem tartalmazhat speciális karaktereket, például pontot (.).
* Az AK-csomópontok készletének neve csak kisbetűket tartalmazhat, és 1-11 karakter hosszúnak kell lennie a Linux-csomópontok és a 1-6 karakter Windows-csomópontok számára A névnek betűvel kell kezdődnie, és csak betűket és számokat tartalmazhat.
* A Linux-csomópontok rendszergazdai felhasználónevét beállító rendszergazda *-username*betűvel kell kezdődnie, és csak betűket, számokat, kötőjeleket és aláhúzásokat tartalmazhat, és legfeljebb 64 karakter hosszú lehet.

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Hibák léptek fel a fürt létrehozása, frissítése, skálázása, törlése vagy frissítése során, ez a művelet nem engedélyezett, mert folyamatban van egy másik művelet.

*Ez a hibaelhárítási segítség a aka.ms/aks-pending-operation*

A fürt műveletei korlátozottak, ha egy korábbi művelet még folyamatban van. A fürt részletes állapotának lekéréséhez használja az `az aks show -g myResourceGroup -n myAKSCluster -o table` parancsot. Igény szerint használja a saját erőforráscsoport és az AK-fürt nevét.

A fürt állapotának kimenete alapján:

* Ha a fürt bármilyen kiépítési állapotban van, amely nem *sikeres* vagy *sikertelen volt*, várjon, amíg a művelet (*frissítés/frissítés/létrehozás/méretezés/törlés/áttelepítés*) be nem fejeződik. Az előző művelet befejeződése után próbálja megismételni a fürt legújabb műveletét.

* Ha a fürtön sikertelen volt a frissítés, kövesse az itt leírt lépéseket, [amelyek a fürt hibás állapotba kerülnek, és a frissítés vagy a skálázás nem fog működni, amíg meg nem történik a javítás](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).

## <a name="received-an-error-saying-my-service-principal-wasnt-found-or-is-invalid-when-i-try-to-create-a-new-cluster"></a>Hiba történt, amely azt jelzi, hogy az egyszerű szolgáltatásnév nem található, vagy érvénytelen, ha új fürtöt próbálok létrehozni.

AK-fürt létrehozásakor egy egyszerű szolgáltatásnév vagy felügyelt identitás szükséges ahhoz, hogy erőforrásokat hozzon létre az Ön nevében. Az e-mailek automatikusan létrehozhatnak egy új egyszerű szolgáltatásnevet a fürt létrehozási ideje alatt, vagy megkaphatnak egy meglévőt. Ha automatikusan létrehoz egy-t, Azure Active Directory kell azt minden régióba terjeszteni, hogy a létrehozás sikeres legyen. Ha a propagálás túl hosszú időt vesz igénybe, a fürt sikertelen lesz a létrehozáshoz, mivel nem talál elérhető egyszerű szolgáltatásnevet. 

A probléma a következő megkerülő megoldásokkal használható:
* Egy meglévő egyszerű szolgáltatásnevet használ, amely már propagálva van a régiók között, és létezik, hogy a fürt létrehozási ideje alatt az AK-ba kerül át.
* Ha Automation-parancsfájlokat használ, adja hozzá az egyszerű szolgáltatás létrehozása és az AK-fürt létrehozása közötti késleltetést.
* Ha Azure Portal használ, térjen vissza a fürt beállításaihoz a létrehozás során, és néhány perc múlva próbálja megismételni az érvényesítési oldalt.

## <a name="im-getting-aadsts7000215-invalid-client-secret-is-provided-when-using-aks-api-what-should-i-do"></a>`"AADSTS7000215: Invalid client secret is provided."`Az AK API használatakor kapok. Mit tegyek?

Ez általában az egyszerű szolgáltatásnév hitelesítő adatainak lejárta miatt fordul elő. [Egy AK-fürt hitelesítő adatainak frissítése.](update-credentials.md)

## <a name="im-receiving-errors-after-restricting-egress-traffic"></a>Hibák jelentkeznek a kimenő forgalom korlátozása után

Ha a kimenő forgalmat egy AK-fürtből korlátozza, akkor szükség van a [szükséges és választható](limit-egress-traffic.md) kimeneti portokra/hálózati szabályokra, valamint a teljes tartománynevek/alkalmazási szabályokra az AK-ra vonatkozóan. Ha a beállítások ütköznek ezekkel a szabályokkal, bizonyos `kubectl` parancsok nem fognak megfelelően működni. Egy AK-fürt létrehozásakor hibák is megjelenhetnek.

Győződjön meg arról, hogy a beállítások nem ütköznek a szükséges vagy választható választható kimenő portok/hálózati szabályok, valamint a teljes tartománynév/alkalmazás szabályaival.

## <a name="azure-storage-and-aks-troubleshooting"></a>Azure Storage-és AK-hibaelhárítás

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>Mik az Azure Disk Kubernetes ajánlott stabil verziói? 

| Kubernetes verziója | Ajánlott verzió |
|--|:--:|
| 1.12 | 1.12.9 vagy újabb |
| 1.13 | 1.13.6 vagy újabb |
| 1,14 | 1.14.2 vagy újabb |


### <a name="waitforattach-failed-for-azure-disk-parsing-devdiskazurescsi1lun1-invalid-syntax"></a>A WaitForAttach nem sikerült az Azure Disk esetében: "/dev/disk/Azure/scsi1/lun1" elemzése: érvénytelen szintaxis

A Kubernetes 1,10-es verziójában a MountVolume. WaitForAttach egy Azure-lemez újracsatlakoztatásával meghiúsulhat.

Linux rendszeren helytelen DevicePath formátumú hiba jelenhet meg. Például:

```console
MountVolume.WaitForAttach failed for volume "pvc-f1562ecb-3e5f-11e8-ab6b-000d3af9f967" : azureDisk - Wait for attach expect device path as a lun number, instead got: /dev/disk/azure/scsi1/lun1 (strconv.Atoi: parsing "/dev/disk/azure/scsi1/lun1": invalid syntax)
  Warning  FailedMount             1m (x10 over 21m)   kubelet, k8s-agentpool-66825246-0  Unable to mount volumes for pod
```

A Windows rendszerben hibás DevicePath (LUN) hiba jelenhet meg. Például:

```console
Warning  FailedMount             1m    kubelet, 15282k8s9010    MountVolume.WaitForAttach failed for volume "disk01" : azureDisk - WaitForAttach failed within timeout node (15282k8s9010) diskId:(andy-mghyb
1102-dynamic-pvc-6c526c51-4a18-11e8-ab5c-000d3af7b38e) lun:(4)
```

Ezt a problémát a Kubernetes következő verzióiban rögzítették:

| Kubernetes verziója | Rögzített verzió |
|--|:--:|
| 1.10 | 1.10.2 vagy újabb |
| 1,11 | 1.11.0 vagy újabb |
| 1,12 és újabb verziók | N/A |


### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>Hiba történt az UID és a GID beállításakor az Azure Disk mountOptions esetében

Az Azure Disk alapértelmezés szerint az ext4, a XFS fájlrendszert és a mountOptions (például UID = x, GID = x) nem állítható be a csatlakoztatási időpontban. Ha például megpróbálta beállítani a mountOptions UID = 999, GID = 999, a következőhöz hasonló hibaüzenetet fog látni:

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

A probléma megoldásához hajtsa végre a következőket:

* [Állítsa be a pod biztonsági környezetét](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) úgy, hogy az UID értéket konfigurálja a runAsUser és a GID-ben a fsGroup-ben. A következő beállítás például beállítja a pod futtató gyökérként való futtatását, így bármely fájl számára elérhetővé válik:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 0
    fsGroup: 0
```

  >[!NOTE]
  > Mivel a GID és az UID alapértelmezés szerint root-ként vagy 0-ként van csatlakoztatva. Ha a GID vagy az UID nem legfelső szintűként van beállítva, például 1000, a Kubernetes az `chown` adott lemezen lévő összes könyvtárat és fájlt módosítani fogja. Ez a művelet időt vehet igénybe, és nagyon lassú lehet a lemez csatlakoztatása.

* `chown`A initContainers használata a GID és az UID beállításához. Például:

```yaml
initContainers:
- name: volume-mount
  image: busybox
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Az Azure Disk leválasztása nem sikerült, mert lehetséges a versenyhelyzet problémája, és érvénytelenek az adatlemezek listája

Ha egy Azure-lemez leválasztása sikertelen, akkor a lemez leválasztása akár hatszor is megtörténik. Emellett az adatlemezek listáján 3 percen belül egy csomópont-szintű zárolást is fog tartani. Ha ez idő alatt manuálisan frissíti a lemezeket, akkor a csomópont-szintű zárolás által tárolt lemezek elavultak lesznek, ami instabilitást okoz a csomóponton.

Ezt a problémát a Kubernetes következő verzióiban rögzítették:

| Kubernetes verziója | Rögzített verzió |
|--|:--:|
| 1.12 | 1.12.9 vagy újabb |
| 1.13 | 1.13.6 vagy újabb |
| 1,14 | 1.14.2 vagy újabb |
| 1,15 és újabb verziók | N/A |

Ha olyan Kubernetes-verziót használ, amely nem rendelkezik a probléma javításával, és a csomópont elavult lemezzel rendelkezik, enyhítheti a virtuális gépről a nem létező lemezek tömeges műveletként való leválasztásával. **A nem létező lemezek különálló leválasztása sikertelen lehet.**

### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>A nagy számú Azure-lemez lassú csatolást/leválasztást okoz

Ha az egyetlen csomópontos virtuális GÉPRE irányuló Azure Disk Attach/leválasztási műveletek száma nagyobb, mint 10, vagy nagyobb, mint 3, ha egy virtuálisgép-méretezési csoport készletét célozza, akkor a vártnál lassabban haladnak. Ez a probléma egy ismert korlátozás, és jelenleg nincsenek megkerülő megoldások. [A felhasználói hangtétel támogatja a párhuzamos csatolást/leválasztást a számon túl](https://feedback.azure.com/forums/216843-virtual-machines/suggestions/40444528-vmss-support-for-parallel-disk-attach-detach-for).

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Az Azure Disk leválasztása nem sikerült, mert lehetséges, hogy a csomópont virtuális gépe sikertelen állapotban van

Egyes esetekben előfordulhat, hogy egy Azure-lemez leválasztása részlegesen meghiúsul, és a csomópont virtuális gépe meghibásodott állapotban marad.

Ezt a problémát a Kubernetes következő verzióiban rögzítették:

| Kubernetes verziója | Rögzített verzió |
|--|:--:|
| 1.12 | 1.12.10 vagy újabb |
| 1.13 | 1.13.8 vagy újabb |
| 1,14 | 1.14.4 vagy újabb |
| 1,15 és újabb verziók | N/A |

Ha olyan Kubernetes-verziót használ, amely nem rendelkezik a probléma javításával, és a csomópont meghibásodott állapotban van, a virtuális gép állapotának manuális frissítésével csökkentheti a következő lépések egyikét:

* Rendelkezésre állási csoport alapú fürt esetén:
    ```azurecli
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* VMSS-alapú fürt esetén:
    ```azurecli
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Azure Files-és AK-hibaelhárítás

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>Melyek az Azure Files Kubernetes ajánlott stabil verziói?
 
| Kubernetes verziója | Ajánlott verzió |
|--|:--:|
| 1.12 | 1.12.6 vagy újabb |
| 1.13 | 1.13.4 vagy újabb |
| 1,14 | 1.14.0 vagy újabb |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>Mi az alapértelmezett mountOptions a Azure Files használatakor?

Ajánlott beállítások:

| Kubernetes verziója | fileMode és dirMode érték|
|--|:--:|
| 1.12.0 – 1.12.1 | 0755 |
| 1.12.2 és újabb verziók | 0777 |

A csatlakoztatási beállítások a tárolási osztály objektumban adhatók meg. A következő példa a *0777*-es készletet állítja be:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

Néhány további hasznos *mountOptions* -beállítás:

* a *mfsymlinks* Azure Files csatlakoztatási (CIFS) támogatást nyújt a szimbolikus hivatkozások támogatásához
* a *nobrl* megakadályozza a bájtos tartomány zárolási kérelmeinek küldését a kiszolgálónak. Erre a beállításra akkor van szükség, ha a CIFS-stílusú kötelező bájtos tartományba tartozó zárolásokkal rendelkező alkalmazások számára szükséges. A legtöbb CIFS-kiszolgáló még nem támogatja a tanácsadói bájtok tartományában lévő zárolások igénylését. Ha nem használ *nobrl*-t, akkor a CIFS-stílusú kötelező bájt-tartomány zárolásával megszakított alkalmazások a következőhöz hasonló hibaüzeneteket eredményezhetnek:
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>Hiba: "nem sikerült módosítani az engedélyeket" Azure Files használatakor

Ha a PostgreSQL-t a Azure Files beépülő modulon futtatja, a következőhöz hasonló hibaüzenet jelenhet meg:

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

Ezt a hibát a CIFS/SMB protokollt használó Azure Files beépülő modul okozza. A CIFS/SMB protokoll használatakor a fájl és a könyvtár engedélyei nem módosíthatók a csatlakoztatás után.

A probléma megoldásához használja az *Alútvonalat* az Azure Disk beépülő modullal együtt. 

> [!NOTE] 
> Az ext3/4 lemez típusa esetén a lemez formázása után egy elveszett és talált könyvtár található.

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>Sok kis fájl kezelésekor a Azure Files nagy késéssel van összehasonlítva az Azure-lemezzel

Bizonyos esetekben, például sok kis fájl kezelésekor nagy késés tapasztalható, ha az Azure-lemezhez képest Azure Files használ.

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>Hiba történt a "hozzáférés engedélyezése a kiválasztott hálózatról" beállítás engedélyezésekor a Storage-fiókban

Ha engedélyezi a *hozzáférést a kiválasztott hálózatról* egy olyan Storage-fiókra, amelyet az AK-ban dinamikus kiosztáshoz használ, hibaüzenet jelenik meg, ha az AK létrehoz egy fájlmegosztást:

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

Ez a hiba azért van, mert a Kubernetes *persistentvolume-vezérlő* nem a kiválasztott *hálózatról való hozzáférés engedélyezése*beállításnál kiválasztott hálózaton van.

A probléma megoldásához a Azure Files használatával történő [statikus kiépítés](azure-files-volume.md)segítségével csökkentheti a problémát.

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Azure Files sikertelen újracsatlakoztatás a Windows Pod-ban

Ha egy Azure Files csatlakoztatással rendelkező Windows-Pod törölve lett, majd az ütemezés szerint újra létrejön ugyanazon a csomóponton, a csatlakoztatás sikertelen lesz. Ennek a hibának a végrehajtása a parancs végrehajtása miatt sikertelen, mert `New-SmbGlobalMapping` a Azure Files csatlakoztatás már csatlakoztatva van a csomóponthoz.

Például a következőhöz hasonló hibaüzenet jelenhet meg:

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

Ezt a problémát a Kubernetes következő verzióiban rögzítették:

| Kubernetes verziója | Rögzített verzió |
|--|:--:|
| 1.12 | 1.12.6 vagy újabb |
| 1.13 | 1.13.4 vagy újabb |
| 1,14 és újabb verziók | N/A |

### <a name="azure-files-mount-fails-because-of-storage-account-key-changed"></a>Azure Files csatlakoztatás sikertelen, mert a Storage-fiók kulcsa módosult

Ha a Storage-fiók kulcsa módosult, Azure Files csatlakoztatási hibák merülhetnek fel.

A mezőt manuálisan is frissítheti `azurestorageaccountkey` egy Azure-fájl titkos kódjában, a Base64-kódolású Storage-fiók kulcsa alapján.

A Storage-fiók kulcsának Base64-ben történő kódolásához használhatja a következőt: `base64` . Például:

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

Az Azure-beli titkos fájl frissítéséhez használja a következőt: `kubectl edit secret` . Például:

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

Néhány perc elteltével az ügynök csomópontja újra fogja próbálni az Azure-fájl csatlakoztatását a frissített tárterület-kulccsal.


### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>A fürt automatikus méretezése nem tud méretezni, mert a hiba nem tudta kijavítani a csomópont-csoportok méretét

Ha a fürt automatikus méretezése nem vertikális fel/le, és az alábbihoz hasonló hibaüzenet jelenik meg a [fürt automatikus méretezési naplóiban][view-master-logs].

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

Ennek a hibának az az oka, hogy egy felsőbb rétegbeli fürthöz tartozó autoskálázási versenyhelyzet van. Ebben az esetben a fürt autoskálázása egy másik értékkel végződik, mint a fürtben ténylegesen. Az állapotból való kilépéshez tiltsa le és engedélyezze újra a [fürt automéretezőjét][cluster-autoscaler].

### <a name="slow-disk-attachment-getazuredisklun-takes-10-to-15-minutes-and-you-receive-an-error"></a>Lassú lemez-melléklet, a GetAzureDiskLun 10 – 15 percet vesz igénybe, és hibaüzenetet kap

A 1.15.0- **nál régebbi**Kubernetes-verziók esetén hibaüzenet jelenhet meg, például a **WaitForAttach nem találja a lemez LUN**elemét.  A probléma megkerülő megoldásához várjon körülbelül 15 percet, majd próbálkozzon újra.

<!-- LINKS - internal -->
[view-master-logs]: view-master-logs.md
[cluster-autoscaler]: cluster-autoscaler.md
