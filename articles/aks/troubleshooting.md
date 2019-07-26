---
title: Az Azure Kubernetes szolgáltatással kapcsolatos gyakori problémák elhárítása
description: Útmutató az Azure Kubernetes szolgáltatás (ak) használata során felmerülő gyakori problémák elhárításához és megoldásához
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: 1668e0b3b155804496b190f2ba66d220ba0dd219
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381946"
---
# <a name="aks-troubleshooting"></a>AK-hibaelhárítás

Az Azure Kubernetes szolgáltatásbeli (ak-beli) fürtök létrehozásakor és kezelésekor időnként problémákba ütközhet. Ez a cikk néhány gyakori problémát és hibaelhárítási lépést részletez.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Általánosságban Hol találhatok információt a Kubernetes kapcsolatos hibák elhárításáról?

Próbálja ki a [hivatalos útmutatót a Kubernetes-fürtök hibaelhárításához](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
A Microsoft Engineering által kiadott [hibaelhárítási útmutató](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)a hüvelyek, a csomópontok, a fürtök és az egyéb funkciók hibaelhárítására is használható.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>"A kvóta túllépve" hibaüzenetet kapok a létrehozás vagy a frissítés során. Mit tegyek? 

[Magot](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)kell kérnie.

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Mekkora a hüvelyek maximális száma az AK-ban?

A hüvelyek maximális száma alapértelmezés szerint 30, ha AK-fürtöt helyez üzembe a Azure Portalban.
Alapértelmezés szerint a hüvelyek maximális száma 110, ha az Azure CLI-ben helyez üzembe egy AK-fürtöt. (Ügyeljen arra, hogy az Azure CLI legújabb verzióját használja). Ez az alapértelmezett beállítás a `–-max-pods` `az aks create` parancsban megjelenő jelző használatával módosítható.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>InsufficientSubnetSize hibaüzenetet kapok egy AK-fürt speciális hálózatkezeléssel való üzembe helyezése során. Mit tegyek?

Ha az Azure CNI (speciális Hálózatkezelés) van használatban, a (z) "Max-hüvelyek" által konfigurált csomópontok alapján a rendszer előre lefoglalja az IP-címet. Egy AK-fürt csomópontjainak száma 1 és 110 között lehet. A konfigurált maximális hüvelyek/csomópontok alapján az alhálózat méretének nagyobbnak kell lennie, mint a csomópontok számának és a maximális Pod/csomópontnak a szorzata. A következő alapegyenlet körvonalazza ezt:

Az alhálózati méret > a fürt csomópontjainak száma (a jövőbeli skálázási követelmények figyelembevételével) * a maximális hüvely/csomópont.

További információt [a fürt IP-címzésének](configure-azure-cni.md#plan-ip-addressing-for-your-cluster)megtervezése című témakörben talál.

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>A My Pod CrashLoopBackOff módban ragadt. Mit tegyek?

Előfordulhat, hogy a pod nem ragadja meg ezt a módot. A következőket tekintheti meg:

* Maga a pod, a használatával `kubectl describe pod <pod-name>`.
* A naplók a használatával `kubectl log <pod-name>`.

A pod-problémák hibaelhárításával kapcsolatos további információkért lásd: [alkalmazások hibakeresése](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Megpróbálom engedélyezni a RBAC egy meglévő fürtön. Hogyan tehetem meg?

Sajnos a szerepköralapú hozzáférés-vezérlés (RBAC) a meglévő fürtökön történő engedélyezése jelenleg nem támogatott. Explicit módon létre kell hoznia egy új fürtöt. Ha a CLI-t használja, a RBAC alapértelmezés szerint engedélyezve van. Ha az AK-portált használja, a RBAC engedélyezésére szolgáló váltógomb a létrehozási munkafolyamatban érhető el.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Létrehozott egy RBAC engedélyező fürtöt az alapértelmezett vagy a Azure Portal Azure CLI használatával, és most már sok figyelmeztetés jelenik meg a Kubernetes-irányítópulton. A figyelmeztetés nélküli működéshez használt irányítópult. Mit tegyek?

A figyelmeztetések az irányítópulton az oka, hogy a fürt most már engedélyezve van a RBAC, és a hozzáférése alapértelmezés szerint le van tiltva. Általánosságban véve ez a megközelítés jó gyakorlat, mert az irányítópultnak a fürt összes felhasználójára vonatkozó alapértelmezett expozíciója biztonsági fenyegetésekhez vezethet. Ha továbbra is engedélyezni szeretné az irányítópultot, kövesse az [ebben](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/)a blogbejegyzésben leírt lépéseket.

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Nem tudok csatlakozni az irányítópulthoz. Mit tegyek?

A szolgáltatásnak a fürtön kívülre való hozzáférésének legegyszerűbb módja `kubectl proxy`a Futtatás, amelyet a rendszer a localhost 8001-as portra küldött a Kubernetes API-kiszolgálónak. Innen az API-kiszolgáló proxyt tud a szolgáltatáshoz: `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`.

Ha nem látja a Kubernetes irányítópultot, ellenőrizze, hogy `kube-proxy` a pod fut-e `kube-system` a névtérben. Ha nem fut állapotban van, törölje a pod-t, majd indítsa újra.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Nem tudok naplókat beolvasni a kubectl-naplók használatával, vagy nem tudok csatlakozni az API-kiszolgálóhoz. "Hiba a kiszolgálóról: hiba a háttérrendszer tárcsázásakor: telefonos TCP...". Mit tegyek?

Győződjön meg arról, hogy az alapértelmezett hálózati biztonsági csoport nincs módosítva, és hogy a 22-es és a 9000-as port is nyitva van az API-kiszolgálóhoz való csatlakozáshoz. `tunnelfront` A`kubectl get pods --namespace kube-system` parancs használatával győződjön meg arról, hogy a pod a *Kube-System* névtérben fut-e. Ha nem, akkor kényszerítse a pod törlését, és a rendszer újraindul.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Megpróbálok frissíteni vagy méretezni, és kapok egy üzenetet: A "imageReference" tulajdonság módosítása nem engedélyezett "hiba". Hogyan kijavítani ezt a problémát?

Előfordulhat, hogy ez a hiba azért fordul elő, mert a címkéket az AK-fürtön belüli ügynök-csomópontokban módosította. A címkék és a MC_ * erőforráscsoport erőforrásainak más tulajdonságainak módosítása és törlése váratlan eredményekhez vezethet. Az AK-fürt MC_ * csoportjában található erőforrások módosítása megszakítja a szolgáltatási szint célkitűzését (SLO).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Hibaüzeneteket kapok, hogy a fürtem hibás állapotban van, és a frissítés vagy a skálázás nem fog működni, amíg meg nem történik a javítás.

*Ez a hibaelhárítási segítség a következő címről származik: https://aka.ms/aks-cluster-failed*

Ez a hiba akkor fordul elő, ha a fürtök több okból is hibás állapotba kerülnek. Kövesse az alábbi lépéseket a fürt sikertelen állapotának feloldásához a korábban sikertelen művelet újbóli megkísérlése előtt:

1. Amíg a fürt `failed` állapota nem áll fenn `scale` , `upgrade` a műveletek sikertelenek lesznek. A leggyakoribb gyökérszintű problémák és megoldások a következők:
    * A nem **megfelelő számítási (CRP-) kvóta**skálázása. A megoldáshoz először a kvótán belüli, stabil cél állapotba kell állítani a fürtöt. Ezután kövesse az alábbi [lépéseket a számítási kvóta növelésének](../azure-supportability/resource-manager-core-quotas-request.md) megkezdéséhez, mielőtt a kezdeti kvóta-korlátokon felül ismét fel kellene mérni.
    * Fürt méretezése speciális hálózatkezeléssel és nem **elegendő alhálózat (Hálózatkezelés) erőforrásokkal**. A megoldáshoz először a kvótán belüli, stabil cél állapotba kell állítani a fürtöt. Ezután kövesse az [alábbi lépéseket az erőforrás-kvóta növelésének](../azure-resource-manager/resource-manager-quota-errors.md#solution) megkezdéséhez, mielőtt a kezdeti kvóta-korlátokon felül ismét fel kellene mérni a skálázást.
2. Miután megoldotta a frissítési hiba kiváltó okát, a fürtnek sikeres állapotban kell lennie. A sikeres állapot ellenőrzése után próbálja megismételni az eredeti műveletet.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Hibákba ütközik, amikor megpróbálja frissíteni vagy méretezni az adott állapotot, mert jelenleg folyamatban van a fürt frissítése vagy frissítése

*Ez a hibaelhárítási segítség a következő címről származik: https://aka.ms/aks-pending-upgrade*

A fürtműveleteket az aktív frissítési műveletek vagy a frissítés megkísérlése után korlátozzák, de ezt követően nem sikerült végrehajtani a műveletet. A probléma `az aks show -g myResourceGroup -n myAKSCluster -o table` diagnosztizálásához a fürt részletes állapotának lekéréséhez. Az eredmény alapján:

* Ha a fürt aktívan frissít, várjon, amíg a művelet leáll. Ha ez sikeres volt, próbálkozzon újra a korábban meghiúsult művelettel.
* Ha a fürt frissítése nem sikerült, kövesse a fenti lépéseket.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Áthelyezhetem a fürtöt egy másik előfizetésbe vagy az előfizetésem a fürttel egy új bérlőre?

Ha már áthelyezte az AK-fürtöt egy másik előfizetésbe vagy a fürt tulajdonosának előfizetését egy új bérlőre, akkor a fürt a szerepkör-hozzárendelések és az egyszerű szolgáltatásokra vonatkozó jogosultságok elvesztése miatt elveszíti a funkcionalitást. Az **AK nem támogatja a fürtök áthelyezését** az előfizetések vagy a bérlők között a jelen megkötés miatt.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>A virtuálisgép-méretezési csoportokat igénylő szolgáltatások használatára vonatkozó hibák jelentkeznek

*Ez a hibaelhárítási segítség a aka.ms/aks-vmss-enablement*

Olyan hibák jelenhetnek meg, amelyek jelzik, hogy az AK-fürt nem egy virtuálisgép-méretezési csoporton van, például a következő példában:

**A "AgentPool" AgentPool engedélyezte az automatikus skálázást, de nem Virtual Machine Scale Sets**

Ha olyan szolgáltatásokat szeretne használni, mint például a fürt autoskálázása vagy több csomópontos készlet, a virtuálisgép-méretezési csoportokat használó AK-fürtöket kell létrehoznia. A rendszer hibaüzeneteket küld, ha olyan szolgáltatásokat próbál használni, amelyek a virtuálisgép-méretezési csoportokon alapulnak, és egy normál, nem virtuálisgép-méretezési csoportba tartozó AK-fürtöt céloz meg. A virtuálisgép-méretezési csoport támogatása jelenleg előzetes verzióban érhető el az AK-ban.

A szükséges dokumentációban szereplő lépések elkezdése *előtt* végezze el a megfelelő regisztrációt a virtuálisgép-méretezési csoport funkciójának előzetes verziójában, és hozzon létre egy AK-fürtöt:

* [A fürt automéretező használata](cluster-autoscaler.md)
* [Több Node-készlet létrehozása és használata](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Milyen elnevezési korlátozásokat kell kikényszeríteni az AK-erőforrások és-paraméterek esetében?

*Ez a hibaelhárítási segítség a aka.ms/aks-naming-rules*

Az elnevezési korlátozásokat az Azure platform és az AK is implementálja. Ha egy erőforrás neve vagy paramétere megszakítja az egyik ilyen korlátozást, a rendszer hibaüzenetet küld, amely megkéri, hogy adjon meg egy másik bemenetet. A következő közös elnevezési irányelvek érvényesek:

* Az AK- *MC_* erőforráscsoport neve kombinálja az erőforráscsoport nevét és az erőforrás nevét. Az automatikusan generált szintaxisának `MC_resourceGroupName_resourceName_AzureRegion` nem lehet nagyobb, mint 80 karakter. Ha szükséges, csökkentse az erőforráscsoport-név vagy az AK-fürt nevének hosszát.
* A *dnsPrefix* alfanumerikus értékekkel kell kezdődnie és végződnie. Az érvényes karakterek alfanumerikus értékeket és kötőjeleket (-) tartalmazhatnak. A *dnsPrefix* nem tartalmazhat speciális karaktereket, például pontot (.).

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Hibák léptek fel a fürt létrehozása, frissítése, skálázása, törlése vagy frissítése során, ez a művelet nem engedélyezett, mert folyamatban van egy másik művelet.

*Ez a hibaelhárítási segítség a aka.ms/aks-pending-operation*

A fürt műveletei korlátozottak, ha egy korábbi művelet még folyamatban van. A fürt részletes állapotának lekéréséhez használja az `az aks show -g myResourceGroup -n myAKSCluster -o table` parancsot. Igény szerint használja a saját erőforráscsoport és az AK-fürt nevét.

A fürt állapotának kimenete alapján:

* Ha a fürt bármilyen kiépítési állapotban van, amely nem *sikeres* vagy *sikertelen volt*, várjon, amíg a művelet (*frissítés/frissítés/létrehozás/méretezés/törlés/áttelepítés*) leáll. Ha az előző művelet befejeződött, próbálja meg újra a fürt legújabb műveletét.

* Ha a fürtön sikertelen volt a frissítés, kövesse az itt leírt lépéseket, [amelyek a fürt hibás állapotba kerülnek, és a frissítés vagy a skálázás nem fog működni, amíg meg nem történik a javítás](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).
