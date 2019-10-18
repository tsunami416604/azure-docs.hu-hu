---
title: Az Azure Kubernetes szolgáltatással kapcsolatos gyakori problémák elhárítása
description: Útmutató az Azure Kubernetes szolgáltatás (ak) használata során felmerülő gyakori problémák elhárításához és megoldásához
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: 2c25069ce5231a1f89027dea69579231f0fe4bcd
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72517080"
---
# <a name="aks-troubleshooting"></a>AK-hibaelhárítás

Az Azure Kubernetes szolgáltatásbeli (ak-beli) fürtök létrehozásakor és kezelésekor időnként problémákba ütközhet. Ez a cikk néhány gyakori problémát és hibaelhárítási lépést részletez.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Általánosságban Hol találhatok információt a Kubernetes kapcsolatos hibák elhárításáról?

Próbálja ki a [hivatalos útmutatót a Kubernetes-fürtök hibaelhárításához](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
A Microsoft Engineering által kiadott [hibaelhárítási útmutató](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)a hüvelyek, a csomópontok, a fürtök és az egyéb funkciók hibaelhárítására is használható.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>"A kvóta túllépve" hibaüzenetet kapok a létrehozás vagy a frissítés során. Mit tegyek? 

[Magot kell kérnie](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Mekkora a hüvelyek maximális száma az AK-ban?

A hüvelyek maximális száma alapértelmezés szerint 30, ha AK-fürtöt helyez üzembe a Azure Portalban.
Alapértelmezés szerint a hüvelyek maximális száma 110, ha az Azure CLI-ben helyez üzembe egy AK-fürtöt. (Ügyeljen arra, hogy az Azure CLI legújabb verzióját használja). Ezt az alapértelmezett beállítást a `az aks create` parancs `–-max-pods` jelzője segítségével módosíthatja.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>InsufficientSubnetSize hibaüzenetet kapok egy AK-fürt speciális hálózatkezeléssel való üzembe helyezése során. Mit tegyek?

Ha az Azure CNI (speciális Hálózatkezelés) van használatban, a (z) "Max-hüvelyek" által konfigurált csomópontok alapján a rendszer előre lefoglalja az IP-címet. Egy AK-fürt csomópontjainak száma 1 és 110 között lehet. A konfigurált maximális hüvelyek/csomópontok alapján az alhálózat méretének nagyobbnak kell lennie, mint a csomópontok számának és a maximális Pod/csomópontnak a szorzata. A következő alapegyenlet körvonalazza ezt:

Az alhálózati méret > a fürt csomópontjainak száma (a jövőbeli skálázási követelmények figyelembevételével) * a maximális hüvely/csomópont.

További információt [a fürt IP-címzésének megtervezése](configure-azure-cni.md#plan-ip-addressing-for-your-cluster)című témakörben talál.

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>A My Pod CrashLoopBackOff módban ragadt. Mit tegyek?

Előfordulhat, hogy a pod nem ragadja meg ezt a módot. A következőket tekintheti meg:

* Maga a pod `kubectl describe pod <pod-name>` használatával.
* A naplók `kubectl log <pod-name>` használatával.

A pod-problémák hibaelhárításával kapcsolatos további információkért lásd: [alkalmazások hibakeresése](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Megpróbálom engedélyezni a RBAC egy meglévő fürtön. Hogyan tehetem meg?

Sajnos a szerepköralapú hozzáférés-vezérlés (RBAC) a meglévő fürtökön történő engedélyezése jelenleg nem támogatott. Explicit módon létre kell hoznia egy új fürtöt. Ha a CLI-t használja, a RBAC alapértelmezés szerint engedélyezve van. Ha az AK-portált használja, a RBAC engedélyezésére szolgáló váltógomb a létrehozási munkafolyamatban érhető el.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Létrehozott egy RBAC engedélyező fürtöt az alapértelmezett vagy a Azure Portal Azure CLI használatával, és most már sok figyelmeztetés jelenik meg a Kubernetes-irányítópulton. A figyelmeztetés nélküli működéshez használt irányítópult. Mit tegyek?

A figyelmeztetések az irányítópulton az oka, hogy a fürt most már engedélyezve van a RBAC, és a hozzáférése alapértelmezés szerint le van tiltva. Általánosságban véve ez a megközelítés jó gyakorlat, mert az irányítópultnak a fürt összes felhasználójára vonatkozó alapértelmezett expozíciója biztonsági fenyegetésekhez vezethet. Ha továbbra is engedélyezni szeretné az irányítópultot, kövesse az [ebben a blogbejegyzésben](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/)leírt lépéseket.

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Nem tudok csatlakozni az irányítópulthoz. Mit tegyek?

A szolgáltatás fürtön kívüli elérésének legegyszerűbb módja a `kubectl proxy` futtatása, amely a 8001-es localhost-portra küldött kérelmeket a Kubernetes API-kiszolgálóra továbbítja. Az API-kiszolgáló innen proxyt tud a szolgáltatáshoz: `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`.

Ha nem látja a Kubernetes irányítópultot, ellenőrizze, hogy fut-e a `kube-proxy` Pod a `kube-system` névtérben. Ha nem fut állapotban van, törölje a pod-t, majd indítsa újra.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Nem tudok naplókat beolvasni a kubectl-naplók használatával, vagy nem tudok csatlakozni az API-kiszolgálóhoz. "Hiba a kiszolgálóról: hiba a háttérrendszer tárcsázásakor: telefonos TCP...". Mit tegyek?

Győződjön meg arról, hogy az alapértelmezett hálózati biztonsági csoport nincs módosítva, és hogy a 22-es és a 9000-as port is nyitva van az API-kiszolgálóhoz való csatlakozáshoz. Győződjön meg arról, hogy a `tunnelfront` Pod a `kubectl get pods --namespace kube-system` paranccsal fut-e a *Kube-System* névtérben. Ha nem, akkor kényszerítse a pod törlését, és a rendszer újraindul.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Megpróbálok frissíteni vagy méretezni, és kapok "üzenetet: a imageReference tulajdonság módosítása nem engedélyezett" hibaüzenet. Hogyan kijavítani ezt a problémát?

Előfordulhat, hogy ez a hiba azért fordul elő, mert a címkéket az AK-fürtön belüli ügynök-csomópontokban módosította. A címkék és a MC_ * erőforráscsoport erőforrásainak más tulajdonságainak módosítása és törlése váratlan eredményekhez vezethet. Az AK-fürt MC_ * csoportjában található erőforrások módosítása megszakítja a szolgáltatási szint célkitűzését (SLO).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Hibaüzeneteket kapok, hogy a fürtem hibás állapotban van, és a frissítés vagy a skálázás nem fog működni, amíg meg nem történik a javítás.

*Ez a hibaelhárítási segítségnyújtás https://aka.ms/aks-cluster-failed*

Ez a hiba akkor fordul elő, ha a fürtök több okból is hibás állapotba kerülnek. Kövesse az alábbi lépéseket a fürt sikertelen állapotának feloldásához a korábban sikertelen művelet újbóli megkísérlése előtt:

1. Amíg a fürt nincs `failed` állapotban, `upgrade` és `scale` műveletek sikertelenek lesznek. A leggyakoribb gyökérszintű problémák és megoldások a következők:
    * A nem **megfelelő számítási (CRP-) kvóta**skálázása. A megoldáshoz először a kvótán belüli, stabil cél állapotba kell állítani a fürtöt. Ezután kövesse az alábbi [lépéseket a számítási kvóta növelésének](../azure-supportability/resource-manager-core-quotas-request.md) megkezdéséhez, mielőtt a kezdeti kvóta-korlátokon felül ismét fel kellene mérni.
    * Fürt méretezése speciális hálózatkezeléssel és nem **elegendő alhálózat (Hálózatkezelés) erőforrásokkal**. A megoldáshoz először a kvótán belüli, stabil cél állapotba kell állítani a fürtöt. Ezután kövesse az [alábbi lépéseket az erőforrás-kvóta növelésének](../azure-resource-manager/resource-manager-quota-errors.md#solution) megkezdéséhez, mielőtt a kezdeti kvóta-korlátokon felül ismét fel kellene mérni a skálázást.
2. Miután megoldotta a frissítési hiba kiváltó okát, a fürtnek sikeres állapotban kell lennie. A sikeres állapot ellenőrzése után próbálja megismételni az eredeti műveletet.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Hibákba ütközik, amikor megpróbálja frissíteni vagy méretezni az adott állapotot, mert jelenleg folyamatban van a fürt frissítése vagy frissítése

*Ez a hibaelhárítási segítségnyújtás https://aka.ms/aks-pending-upgrade*

Egyetlen csomóponttal rendelkező fürtön lévő műveletek frissítése és méretezése, illetve a [több csomóponttal](use-multiple-node-pools.md) rendelkező fürtök kölcsönösen kizárják egymást. Nem lehet egyszerre frissíteni és méretezni a fürt vagy a csomópont készletét. Ehelyett minden Művelettípus a következő, ugyanazon az erőforráson megjelenő kérelem előtt fejeződik be a cél erőforráson. Ennek eredményeképpen a műveletek korlátozottak, ha az aktív verziófrissítési vagy méretezési műveletek történnek, és a későbbiekben sikertelenek voltak. 

A probléma megoldásához futtassa `az aks show -g myResourceGroup -n myAKSCluster -o table` a fürt részletes állapotának lekéréséhez. Az eredmény alapján:

* Ha a fürt aktívan frissít, várjon, amíg a művelet leáll. Ha sikerült, próbálkozzon újra a korábban sikertelen művelettel.
* Ha a fürt nem tudta frissíteni a frissítést, kövesse az előző szakaszban ismertetett lépéseket.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Áthelyezhetem a fürtöt egy másik előfizetésbe vagy az előfizetésem a fürttel egy új bérlőre?

Ha már áthelyezte az AK-fürtöt egy másik előfizetésbe vagy a fürt tulajdonosának előfizetését egy új bérlőre, akkor a fürt a szerepkör-hozzárendelések és az egyszerű szolgáltatásokra vonatkozó jogosultságok elvesztése miatt elveszíti a funkcionalitást. Az **AK nem támogatja a fürtök áthelyezését az előfizetések vagy a bérlők között** a jelen megkötés miatt.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>A virtuálisgép-méretezési csoportokat igénylő szolgáltatások használatára vonatkozó hibák jelentkeznek

*Ez a hibaelhárítási segítség a aka.ms/aks-vmss-enablement*

Olyan hibák jelenhetnek meg, amelyek jelzik, hogy az AK-fürt nem egy virtuálisgép-méretezési csoporton van, például a következő példában:

**A "AgentPool" AgentPool engedélyezte az automatikus skálázást, de nem Virtual Machine Scale Sets**

Ha olyan szolgáltatásokat szeretne használni, mint például a fürt autoskálázása vagy több csomópontos készlet, a virtuálisgép-méretezési csoportokat használó AK-fürtöket kell létrehoznia. A rendszer hibaüzeneteket küld, ha olyan szolgáltatásokat próbál használni, amelyek a virtuálisgép-méretezési csoportokon alapulnak, és egy normál, nem virtuálisgép-méretezési csoportba tartozó AK-fürtöt céloz meg.

A megfelelő dokumentum lépéseinek *megkezdése előtt* kövesse az AK-fürt megfelelő létrehozásához szükséges lépéseket:

* [A fürt automéretező használata](cluster-autoscaler.md)
* [Több Node-készlet létrehozása és használata](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Milyen elnevezési korlátozásokat kell kikényszeríteni az AK-erőforrások és-paraméterek esetében?

*Ez a hibaelhárítási segítség a aka.ms/aks-naming-rules*

Az elnevezési korlátozásokat az Azure platform és az AK is implementálja. Ha egy erőforrás neve vagy paramétere megszakítja az egyik ilyen korlátozást, a rendszer hibaüzenetet küld, amely megkéri, hogy adjon meg egy másik bemenetet. A következő közös elnevezési irányelvek érvényesek:

* Az AK- *MC_* erőforráscsoport neve kombinálja az erőforráscsoport nevét és az erőforrás nevét. @No__t_0 automatikusan generált szintaxisa nem lehet nagyobb, mint 80 karakter. Ha szükséges, csökkentse az erőforráscsoport-név vagy az AK-fürt nevének hosszát.
* A *dnsPrefix* alfanumerikus értékekkel kell kezdődnie és végződnie. Az érvényes karakterek alfanumerikus értékeket és kötőjeleket (-) tartalmazhatnak. A *dnsPrefix* nem tartalmazhat speciális karaktereket, például pontot (.).

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Hibák léptek fel a fürt létrehozása, frissítése, skálázása, törlése vagy frissítése során, ez a művelet nem engedélyezett, mert folyamatban van egy másik művelet.

*Ez a hibaelhárítási segítség a aka.ms/aks-pending-operation*

A fürt műveletei korlátozottak, ha egy korábbi művelet még folyamatban van. A fürt részletes állapotának beolvasásához használja a `az aks show -g myResourceGroup -n myAKSCluster -o table` parancsot. Igény szerint használja a saját erőforráscsoport és az AK-fürt nevét.

A fürt állapotának kimenete alapján:

* Ha a fürt bármilyen kiépítési állapotban van, amely nem *sikeres* vagy *sikertelen volt*, várjon, amíg a művelet (*frissítés/frissítés/létrehozás/méretezés/törlés/áttelepítés*) leáll. Ha az előző művelet befejeződött, próbálja meg újra a fürt legújabb műveletét.

* Ha a fürtön sikertelen volt a frissítés, kövesse az itt leírt lépéseket, [amelyek a fürt hibás állapotba kerülnek, és a frissítés vagy a skálázás nem fog működni, amíg meg nem történik a javítás](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>Hibaüzenetet kapok, hogy a szolgáltatásnév nem található, amikor új fürtöt próbálok létrehozni anélkül, hogy egy meglévőt kellene átadni.

AK-fürt létrehozásakor a szolgáltatáshoz egy egyszerű szolgáltatásnév szükséges, amely az Ön nevében hoz létre erőforrásokat. Az AK lehetővé teszi, hogy egy újat hozzon létre a fürt létrehozási időpontjában, de ehhez Azure Active Directory szükséges, hogy az új egyszerű szolgáltatást ésszerű időn belül teljes mértékben propagálja ahhoz, hogy a fürt sikeres legyen a létrehozásban. Ha ez a propagálás túl hosszú időt vesz igénybe, a fürt nem fogja tudni létrehozni az érvényesítést, mert nem talál elérhető egyszerű szolgáltatásnevet. 

Ehhez használja a következő megkerülő megoldásokat:
1. Olyan meglévő szolgáltatásnevet használjon, amely már propagálva van a régiók között, és létezik, hogy a fürt létrehozási ideje alatt adja át az ak-nak.
2. Ha Automation-parancsfájlokat használ, adja hozzá az egyszerű szolgáltatás létrehozása és az AK-fürt létrehozása közötti késleltetést.
3. Ha Azure Portal használ, térjen vissza a fürt beállításaihoz a létrehozás során, és néhány perc múlva próbálja megismételni az érvényesítési oldalt.

## <a name="im-receiving-errors-after-restricting-my-egress-traffic"></a>Hibák jelentkeznek a kimenő forgalom korlátozása után

Ha a kimenő forgalmat egy AK-fürtből korlátozza, akkor szükség van a [szükséges és választható](limit-egress-traffic.md) kimeneti portokra/hálózati szabályokra, valamint a teljes tartománynevek/alkalmazási szabályokra az AK-ra vonatkozóan. Ha a beállítások ütköznek ezekkel a szabályokkal, előfordulhat, hogy nem fog tudni bizonyos `kubectl` parancsokat futtatni. Egy AK-fürt létrehozásakor hibák is megjelenhetnek.

Győződjön meg arról, hogy a beállítások nem ütköznek a szükséges vagy választható választható kimenő portok/hálózati szabályok, valamint a teljes tartománynév/alkalmazás szabályaival.

## <a name="azure-storage-and-aks-troubleshooting"></a>Azure Storage-és AK-hibaelhárítás

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>Mik az Azure Disk Kubernetes ajánlott stabil verziói? 

| Kubernetes verziója | Ajánlott verzió |
| -- | :--: |
| 1,12 | 1.12.9 vagy újabb |
| 1,13 | 1.13.6 vagy újabb |
| 1,14 | 1.14.2 vagy újabb |


### <a name="what-versions-of-kubernetes-have-azure-disk-support-on-the-sovereign-cloud"></a>A Kubernetes mely verziói támogatják az Azure Disk supportot a szuverén felhőben?

| Kubernetes verziója | Ajánlott verzió |
| -- | :--: |
| 1,12 | 1.12.0 vagy újabb |
| 1,13 | 1.13.0 vagy újabb |
| 1,14 | 1.14.0 vagy újabb |


### <a name="waitforattach-failed-for-azure-disk-parsing-devdiskazurescsi1lun1-invalid-syntax"></a>A WaitForAttach nem sikerült az Azure Disk esetében: "/dev/disk/Azure/scsi1/lun1" elemzése: érvénytelen szintaxis

A Kubernetes 1,10-es verziójában a MountVolume. WaitForAttach sikertelen lehet az Azure-lemez újracsatlakoztatásával.

Linux rendszeren helytelen DevicePath formátumú hiba jelenhet meg. Példa:

```console
MountVolume.WaitForAttach failed for volume "pvc-f1562ecb-3e5f-11e8-ab6b-000d3af9f967" : azureDisk - Wait for attach expect device path as a lun number, instead got: /dev/disk/azure/scsi1/lun1 (strconv.Atoi: parsing "/dev/disk/azure/scsi1/lun1": invalid syntax)
  Warning  FailedMount             1m (x10 over 21m)   kubelet, k8s-agentpool-66825246-0  Unable to mount volumes for pod
```

A Windows rendszerben hibás DevicePath (LUN) hiba jelenhet meg. Példa:

```console
Warning  FailedMount             1m    kubelet, 15282k8s9010    MountVolume.WaitForAttach failed for volume "disk01" : azureDisk - WaitForAttach failed within timeout node (15282k8s9010) diskId:(andy-mghyb
1102-dynamic-pvc-6c526c51-4a18-11e8-ab5c-000d3af7b38e) lun:(4)
```

Ezt a problémát a Kubernetes következő verzióiban rögzítették:

| Kubernetes verziója | Rögzített verzió |
| -- | :--: |
| 1,10 | 1.10.2 vagy újabb |
| 1,11 | 1.11.0 vagy újabb |
| 1,12 és újabb verziók | – |

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

A probléma megoldásához hajtsa végre a következő műveleteket:

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
  > Mivel a GID és az UID alapértelmezés szerint root-ként vagy 0-ként van csatlakoztatva. Ha a GID vagy az UID nem legfelső szintűként van beállítva, például 1000, a Kubernetes `chown` fogja használni az adott lemezen található összes könyvtár és fájl módosítására. Ez a művelet időt vehet igénybe, és nagyon lassú lehet a lemez csatlakoztatása.

* A GID és az UID beállításához használja a initContainers `chown` a következőben:. Példa:

```yaml
initContainers:
- name: volume-mount
  image: busybox
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="error-when-deleting-azure-disk-persistentvolumeclaim-in-use-by-a-pod"></a>Hiba történt az Azure-lemezek Pod-beli PersistentVolumeClaim való törlésekor

Ha olyan Azure-beli PersistentVolumeClaim próbál törölni, amelyet egy Pod használ, előfordulhat, hogy hibaüzenet jelenik meg. Példa:

```console
$ kubectl describe pv pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06
...
Message:         disk.DisksClient#Delete: Failure responding to request: StatusCode=409 -- Original Error: autorest/azure: Service returned an error. Status=409 Code="OperationNotAllowed" Message="Disk kubernetes-dynamic-pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06 is attached to VM /subscriptions/{subs-id}/resourceGroups/MC_markito-aks-pvc_markito-aks-pvc_westus/providers/Microsoft.Compute/virtualMachines/aks-agentpool-25259074-0."
```

A Kubernetes 1,10-es és újabb verzióiban a PersistentVolumeClaim védelmi funkció alapértelmezés szerint engedélyezve van a hiba megelőzése érdekében. Ha olyan Kubernetes-verziót használ, amely nem rendelkezik a probléma javításával, a PersistentVolumeClaim törlése előtt a PersistentVolumeClaim törlésével csökkentheti a problémát.


### <a name="error-cannot-find-lun-for-disk-when-attaching-a-disk-to-a-node"></a>Hiba: a lemez csomóponthoz csatolásakor nem található a lemez LUN

Amikor egy lemezt csatlakoztat egy csomóponthoz, a következő hibaüzenet jelenhet meg:

```console
MountVolume.WaitForAttach failed for volume "pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6" : Cannot find Lun for disk kubernetes-dynamic-pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6
```

Ezt a problémát a Kubernetes következő verzióiban rögzítették:

| Kubernetes verziója | Rögzített verzió |
| -- | :--: |
| 1,10 | 1.10.10 vagy újabb |
| 1,11 | 1.11.5 vagy újabb |
| 1,12 | 1.12.3 vagy újabb |
| 1,13 | 1.13.0 vagy újabb |
| 1,14 és újabb verziók | – |

Ha olyan Kubernetes-verziót használ, amely nem rendelkezik a probléma javításával, a probléma megoldásához várjon néhány percet, és próbálkozzon újra.

### <a name="azure-disk-attachdetach-failure-mount-issues-or-io-errors-during-multiple-attachdetach-operations"></a>Az Azure lemez csatlakoztatási/leválasztási hibája, csatlakoztatási problémái vagy I/O-hibák több csatlakoztatási/leválasztási művelet során

A Kubernetes verziójának 1.9.2 kezdődően több csatlakoztatási/leválasztási művelet párhuzamos futtatásakor a következő lemezekkel kapcsolatos problémák jelenhetnek meg egy inkonzisztens VM-gyorsítótár miatt:

* Lemez csatlakoztatási/leválasztási hibái
* Lemez I/O-hibák
* Váratlan lemez leválasztása a virtuális gépről
* Nem létező lemez csatolása miatt sikertelen állapotú virtuális gép

Ezt a problémát a Kubernetes következő verzióiban rögzítették:

| Kubernetes verziója | Rögzített verzió |
| -- | :--: |
| 1,10 | 1.10.12 vagy újabb |
| 1,11 | 1.11.6 vagy újabb |
| 1,12 | 1.12.4 vagy újabb |
| 1,13 | 1.13.0 vagy újabb |
| 1,14 és újabb verziók | – |

Ha olyan Kubernetes-verziót használ, amely nem rendelkezik a probléma javításával, a probléma megoldásához próbálkozzon az alábbiakkal:

* Ha egy lemez hosszú időn keresztül leválasztásra vár, próbálja meg manuálisan leválasztani a lemezt

### <a name="azure-disk-waiting-to-detach-indefinitely"></a>Az Azure-lemez határozatlan idejű leválasztásra vár

Bizonyos esetekben, ha az első kísérlet során az Azure Disk leválasztási művelete meghiúsul, nem próbálkozik újra a leválasztási művelettel, és továbbra is az eredeti csomópont virtuális géphez lesz csatolva. Ez a hiba akkor fordulhat elő, ha a lemezt egyik csomópontról a másikra helyezi át. Példa:

```console
[Warning] AttachVolume.Attach failed for volume “pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9” : Attach volume “kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" to instance “/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-0” failed with compute.VirtualMachinesClient#CreateOrUpdate: Failure sending request: StatusCode=0 -- Original Error: autorest/azure: Service returned an error. Status= Code=“ConflictingUserInput” Message=“Disk ‘/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/disks/kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9’ cannot be attached as the disk is already owned by VM ‘/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-1’.”
```

Ezt a problémát a Kubernetes következő verzióiban rögzítették:

| Kubernetes verziója | Rögzített verzió |
| -- | :--: |
| 1,11 | 1.11.9 vagy újabb |
| 1,12 | 1.12.7 vagy újabb |
| 1,13 | 1.13.4 vagy újabb |
| 1,14 és újabb verziók | – |

Ha olyan Kubernetes-verziót használ, amely nem rendelkezik a probléma javításával, a lemez manuális leválasztásával enyhítheti a problémát.

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Az Azure Disk leválasztása nem sikerült, mert lehetséges a versenyhelyzet problémája, és érvénytelenek az adatlemezek listája

Ha egy Azure-lemez leválasztása sikertelen, akkor a lemez leválasztása akár hatszor is megtörténik. Emellett az adatlemezek listáján 3 percen belül egy csomópont-szintű zárolást is fog tartani. Ha a lemezek listája az adott időszakban manuálisan, például manuális csatolású vagy leválasztásos művelettel frissül, akkor a csomópont-szint zárolása elavult, és instabillá válik a csomópont virtuális gépén.

Ezt a problémát a Kubernetes következő verzióiban rögzítették:

| Kubernetes verziója | Rögzített verzió |
| -- | :--: |
| 1,12 | 1.12.9 vagy újabb |
| 1,13 | 1.13.6 vagy újabb |
| 1,14 | 1.14.2 vagy újabb |
| 1,15 és újabb verziók | – |

Ha olyan Kubernetes-verziót használ, amely nem rendelkezik a probléma javításával, és a csomópont virtuális gépe elavult lemezzel rendelkezik, a probléma megoldásához leválaszthatja a virtuális gépről származó összes nem létező lemezt egyetlen, tömeges műveletként. **A nem létező lemezek különálló leválasztása sikertelen lehet.**


### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>A nagy számú Azure-lemez lassú csatolást/leválasztást okoz

Ha a csomópont virtuális géphez csatolt Azure-lemezek száma nagyobb, mint 10, a csatolási és leválasztási műveletek lassúak lehetnek. Ez a probléma egy ismert probléma, és jelenleg nem kerül megkerülő megoldás.

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Az Azure Disk leválasztása nem sikerült, mert lehetséges, hogy a csomópont virtuális gépe sikertelen állapotban van

Egyes esetekben előfordulhat, hogy egy Azure-lemez leválasztása részlegesen meghiúsul, és a csomópont virtuális gépe meghibásodott állapotban marad.

Ezt a problémát a Kubernetes következő verzióiban rögzítették:

| Kubernetes verziója | Rögzített verzió |
| -- | :--: |
| 1,12 | 1.12.10 vagy újabb |
| 1,13 | 1.13.8 vagy újabb |
| 1,14 | 1.14.4 vagy újabb |
| 1,15 és újabb verziók | – |

Ha olyan Kubernetes-verziót használ, amely nem rendelkezik a probléma javításával, és a csomópont virtuális gépe hibás állapotban van, a probléma megoldásához manuálisan frissítse a virtuális gép állapotát az alábbi lépések egyikével:

* Rendelkezésre állási csoport alapú fürt esetén:
    ```console
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* VMSS-alapú fürt esetén:
    ```console
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Azure Files-és AK-hibaelhárítás

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>Melyek az Azure Files Kubernetes ajánlott stabil verziói?
 
| Kubernetes verziója | Ajánlott verzió |
| -- | :--: |
| 1,12 | 1.12.6 vagy újabb |
| 1,13 | 1.13.4 vagy újabb |
| 1,14 | 1.14.0 vagy újabb |

### <a name="what-versions-of-kubernetes-have-azure-files-support-on-the-sovereign-cloud"></a>A Kubernetes mely verziói támogatják Azure Files támogatást a szuverén felhőben?

| Kubernetes verziója | Ajánlott verzió |
| -- | :--: |
| 1,12 | 1.12.0 vagy újabb |
| 1,13 | 1.13.0 vagy újabb |
| 1,14 | 1.14.0 vagy újabb |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>Mi az alapértelmezett mountOptions a Azure Files használatakor?

Ajánlott beállítások:

| Kubernetes verziója | fileMode és dirMode érték|
| -- | :--: |
| 1.12.0 – 1.12.1 | 0755 |
| 1.12.2 és újabb verziók | 0777 |

Ha olyan fürtöt használ, amelynek Kuberetes-verziója 1.8.5 vagy nagyobb, és dinamikusan hozza létre az állandó kötetet egy tárolási osztállyal, a csatlakoztatási beállítások megadhatók a tárolási osztály objektumban. A következő példa a *0777*-es készletet állítja be:

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

Ha engedélyezi a *hozzáférést a kiválasztott hálózatról* egy olyan Storage-fiókra, amelyet az AK-ban dinamikus kiosztáshoz használ, hibaüzenet jelenik meg, amikor az AK létrehoz egy fájlmegosztást:

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

Ez a hiba azért van, mert a Kubernetes *persistentvolume-vezérlő* nem a kiválasztott *hálózatról való hozzáférés engedélyezése*beállításnál kiválasztott hálózaton van.

A probléma megoldásához a Azure Files használatával történő [statikus kiépítés](azure-files-volume.md)segítségével csökkentheti a problémát.

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Azure Files sikertelen újracsatlakoztatás a Windows Pod-ban

Ha egy Azure Files csatlakoztatással rendelkező Windows-Pod törölve lett, majd az ütemezés szerint újra létrejön ugyanazon a csomóponton, a csatlakoztatás sikertelen lesz. Ezt a hibát a `New-SmbGlobalMapping` parancs nem hajtja végre, mert a Azure Files csatlakoztatás már csatlakoztatva van a csomóponthoz.

Például a következőhöz hasonló hibaüzenet jelenhet meg:

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

Ezt a problémát a Kubernetes következő verzióiban rögzítették:

| Kubernetes verziója | Rögzített verzió |
| -- | :--: |
| 1,12 | 1.12.6 vagy újabb |
| 1,13 | 1.13.4 vagy újabb |
| 1,14 és újabb verziók | – |

### <a name="azure-files-mount-fails-due-to-storage-account-key-changed"></a>A Azure Files csatlakoztatása a Storage-fiók kulcsának módosítása miatt meghiúsult

Ha a Storage-fiók kulcsa módosult, Azure Files csatlakoztatási hibák merülhetnek fel.

A probléma megoldásához végezze el manuálisan a *azurestorageaccountkey* -mező manuális frissítését az Azure file Secret-ben a Base64-kódolású Storage-fiók kulcsával.

A Storage-fiók kulcsa Base64-ben történő kódolásához használhatja a `base64`. Példa:

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

Az Azure Secret-fájl frissítéséhez használja a `kubectl edit secret`. Példa:

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

Néhány perc elteltével az ügynök csomópontja újra fogja próbálni az Azure-fájl csatlakoztatását a frissített tárterület-kulccsal.
