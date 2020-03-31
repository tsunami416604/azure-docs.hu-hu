---
title: Gyakori Azure Kubernetes-szolgáltatással kapcsolatos problémák elhárítása
description: Ismerje meg, hogyan háríthatja el és oldhatja meg a gyakori problémákat az Azure Kubernetes Szolgáltatás (AKS) használata kor
services: container-service
author: sauryadas
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: saudas
ms.openlocfilehash: 7bdabf2ec109fe96c28185bd1a2a680ce19c2650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368332"
---
# <a name="aks-troubleshooting"></a>AKS-hibaelhárítás

Az Azure Kubernetes-szolgáltatás (AKS) fürtjeit létrehozásakor vagy kezelésekor időnként problémák léphetnek fel. Ez a cikk néhány gyakori problémát és hibaelhárítási lépést részletez.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Általában hol találok információt a Kubernetes-problémák hibakereséséről?

Próbálja ki a [Kubernetes-fürtök hibaelhárításának hivatalos útmutatóját.](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/)
Van egy [hibaelhárítási útmutató](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)is, amelyet egy Microsoft-mérnök tett közzé a podok, csomópontok, fürtök és egyéb szolgáltatások hibaelhárításához.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>A létrehozás vagy frissítés során "kvótatúllépés" hibaüzenetjelenik meg. Mit tegyek? 

Be kell [kérni magok](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Mi a maximális podok-per-node beállítás AKS?

A podok csomópontonkénti maximális beállítása alapértelmezés szerint 30, ha egy AKS-fürtaz Azure Portalon.
A podok csomópontonkénti maximális beállítása alapértelmezés szerint 110, ha egy AKS-fürtaz Azure CLI-ben üzembe helyezése. (Győződjön meg arról, hogy az Azure CLI legújabb verzióját használja). Ez az alapértelmezett beállítás a `–-max-pods` `az aks create` parancsjelzővel módosítható.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>ElégtelenSubnetSize hibát kapok egy speciális hálózati aks-fürt telepítése közben. Mit tegyek?

Ha Az Azure CNI (speciális hálózatkezelés) használatos, az AKS lefoglalja az IP-címeket a "max-pods" csomópontonként konfigurált. A konfigurált maximális podok csomópontonként, az alhálózat méretének nagyobbnak kell lennie, mint a csomópontszám és a maximális pod csomópontonkénti beállításszorzata. A következő egyenlet ezt körvonalazza:

Alhálózati méret > csomópontok száma a fürtben (figyelembe véve a jövőbeli skálázási követelmények) * max podok csomópontonként készletenként.

További információt a [Fürt IP-címzésének megtervezése című témakörben talál.](configure-azure-cni.md#plan-ip-addressing-for-your-cluster)

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Saját pod beragadt CrashLoopBackOff módban. Mit tegyek?

Lehet, hogy különböző okok miatt a pod, hogy megragadt ebben a módban. Lehet, hogy vizsgálja meg:

* A pod maga, `kubectl describe pod <pod-name>`segítségével .
* A naplók használatával `kubectl logs <pod-name>`.

A podokkal kapcsolatos problémák elhárításáról további információt a [Hibakeresési alkalmazások című](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods)témakörben talál.

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Egy meglévő fürtrést próbálok engedélyezni. Hogy tehetném?

Sajnos a szerepköralapú hozzáférés-vezérlés (RBAC) engedélyezése a meglévő fürtökön jelenleg nem támogatott. Explicit módon új fürtöket kell létrehoznia. Ha a CLI-t használja, az RBAC alapértelmezés szerint engedélyezve van. Ha az AKS-portált használja, az RBAC engedélyezéséhez egy váltógomb érhető el a létrehozási munkafolyamatban.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Létrehoztam egy fürtrBAC engedélyezve használatával vagy az Azure CLI alapértelmezett vagy az Azure Portalon, és most már sok figyelmeztetést látok a Kubernetes irányítópulton. Az irányítópult figyelmeztetés nélkül működött. Mit tegyek?

Az irányítópulton megjelenő figyelmeztetések oka az, hogy a fürt most már engedélyezve van az RBAC-mal, és a hozzáférés alapértelmezés szerint le van tiltva. Ez a megközelítés általában ajánlott, mert az irányítópult alapértelmezett expozíciója a fürt összes felhasználója számára biztonsági fenyegetésekhez vezethet. Ha továbbra is engedélyezni szeretné az irányítópultot, kövesse a [blogbejegyzésben](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/)leírt lépéseket.

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Nem lehet csatlakozni az irányítópulthoz. Mit tegyek?

A szolgáltatás fürtön kívüli elérésének legegyszerűbb `kubectl proxy`módja a futtatás, amely proxykat küld a localhost 8001-es helyi gazdagépnek a Kubernetes API-kiszolgálóra. Innen az API-kiszolgáló proxya a `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`szolgáltatás: .

Ha nem látja a Kubernetes irányítópultot, `kube-proxy` ellenőrizze, hogy `kube-system` a pod fut-e a névtérben. Ha nem futó állapotban van, törölje a podot, és újraindul.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Kubectl-naplók használatával nem tudok naplókat beszerezni, vagy nem tudok csatlakozni az API-kiszolgálóhoz. "Hiba a kiszolgálóról: hiba a háttérrendszer tárcsázása: tcp tárcsázása...". Mit tegyek?

Győződjön meg arról, hogy az alapértelmezett hálózati biztonsági csoport nincs módosítva, és hogy a 22-es és a 9000-es port is nyitva van az API-kiszolgálóhoz való csatlakozáshoz. Ellenőrizze, `tunnelfront` hogy a pod fut-e a *kube-rendszer* névtérben a `kubectl get pods --namespace kube-system` parancs használatával. Ha nem, a pod kényszerítése, és újraindul.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Próbálok frissíteni vagy méretezni, és kapok egy "üzenet: Változó tulajdonság "imageReference" nem engedélyezett" hiba. Hogyan oldhatom meg a problémát?

Előfordulhat, hogy ez a hiba azért jelenik meg, mert módosította az AKS-fürtben lévő ügynökcsomópontok címkéit. A címkék és az erőforrások egyéb tulajdonságainak módosítása és törlése a MC_* erőforráscsoportban nem várt eredményekhez vezethet. Az AKS-fürt MC_* csoportjába tartozó erőforrások módosítása megszakítja a szolgáltatásszintű célkitűzést (SLO).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Olyan hibákat kapok, amelyek szerint a fürt meghibásodott állapotban van, és a frissítés vagy méretezés nem fog működni, amíg ki nem javította

*Ez a hibaelhárítási segítség a következőhttps://aka.ms/aks-cluster-failed*

Ez a hiba akkor fordul elő, ha a fürtök több okból is hibás állapotba lépnek. A fürt meghibásodott állapotának feloldásához kövesse az alábbi lépéseket a korábban meghibásodott művelet újbóli megkísérlése előtt:

1. Amíg a fürt `failed` állapoton kívül nem van, `upgrade` és `scale` a műveletek nem lesznek sikeresek. A leggyakoribb gyökérproblémák és állásfoglalások a következők:
    * Méretezés **elégtelen számítási (CRP) kvótával**. A feloldáshoz először méretezze vissza a fürtöt egy stabil célállapotba a kvótán belül. Ezután kövesse ezeket [a lépéseket a számítási kvóta növelésének kéréséhez,](../azure-portal/supportability/resource-manager-core-quotas-request.md) mielőtt újra megpróbálna a kezdeti kvótakorlátokon túli horizontális felskálázást.
    * Fürt méretezése fejlett hálózati és **elégtelen alhálózati (hálózati) erőforrásokkal.** A feloldáshoz először méretezze vissza a fürtöt egy stabil célállapotba a kvótán belül. Ezután [hajtsa végre az alábbi lépéseket az erőforráskvóta-növelés kérelmezéséhez,](../azure-resource-manager/templates/error-resource-quota.md#solution) mielőtt újra megpróbálna a kezdeti kvótakorlátokon túli horizontális felskálázást.
2. A frissítési hiba kiváltó okának feloldása után a fürtnek sikeres állapotban kell lennie. A sikeres állapot ellenőrzése után próbálkozzon újra az eredeti művelettel.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Hibákat kapok, amikor olyan frissítést vagy méretezést kísérelek meg, amely szerint a fürt frissítése folyamatban van, vagy nem sikerült a frissítés

*Ez a hibaelhárítási segítség a következőhttps://aka.ms/aks-pending-upgrade*

Az egyetlen csomópontkészlettel vagy [több csomópontkészlettel](use-multiple-node-pools.md) rendelkező fürttel rendelkező fürt műveleteinek frissítése és méretezése kölcsönösen kizárják egymást. Fürt- vagy csomópontkészlet nem rendelkezhet egyidejűleg frissítéssel és méretezéssel. Ehelyett minden művelettípusnak be kell fejeződnie a célerőforráson, mielőtt az adott erőforrásra vonatkozó következő kérést végrehajtana. Ennek eredményeképpen a műveletek korlátozottak, ha aktív frissítési vagy méretezési műveletek történnek vagy megkísérelnek, és ezt követően sikertelenek. 

A probléma diagnosztizálása érdekében futtassa `az aks show -g myResourceGroup -n myAKSCluster -o table` a fürt részletes állapotát. Az eredmény alapján:

* Ha a fürt aktívan frissít, várja meg, amíg a művelet befejeződik. Ha sikerült, próbálkozzon újra a korábban sikertelen művelettel.
* Ha a fürt frissítése nem sikerült, kövesse az előző szakaszban ismertetett lépéseket.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Áthelyezhetem a fürtömet egy másik előfizetésre, vagy a fürtdel való előfizetésemet egy új bérlőre?

Ha az AKS-fürtöt egy másik előfizetésre helyezte át, vagy az előfizetést birtokló fürtegy új bérlőre, a fürt elveszíti a funkcióit a szerepkör-hozzárendelések és a szolgáltatásnévi jogok elvesztése miatt. **Az AKS nem támogatja a fürtök áthelyezését az előfizetések vagy a bérlők között** a megkötés miatt.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Hibákat kapok a virtuálisgép-méretezési csoportokat igénylő szolgáltatások használatában

*Ez a hibaelhárítási segítség a aka.ms/aks-vmss-enablement*

Olyan hibák jelenhetnek meg, amelyek azt jelzik, hogy az AKS-fürt nem virtuálisgép-méretezési csoporton van, például a következő példa:

**Az AgentPool "agentpool" az automatikus méretezést engedélyezettként állította be, de nem rendelkezik a virtuálisgép-méretezési készleteken**

Az olyan szolgáltatások használatához, mint például a fürt automatikus skálázója vagy több csomópontkészlet, olyan AKS-fürtöket kell létrehozni, amelyek virtuálisgép-méretezési készleteket használnak. Hibák at ad vissza, ha olyan szolgáltatásokat próbál használni, amelyek a virtuálisgép-méretezési csoportoktól függenek, és egy normál, nem virtuális gépméretezési készletet, az AKS-fürtöt célozza meg.

Kövesse a *Mielőtt elkezdené* a lépéseket a megfelelő doc helyesen hozzon létre egy AKS-fürt:

* [A fürt automatikus skálázójának használata](cluster-autoscaler.md)
* [Több csomópontkészlet létrehozása és használata](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Milyen elnevezési korlátozások vannak érvényben az AKS-erőforrások és -paraméterek esetében?

*Ez a hibaelhárítási segítség a aka.ms/aks-naming-rules*

Az elnevezési korlátozásokat az Azure platform és az AKS is megvalósítja. Ha egy erőforrás név vagy paraméter megtöri az egyik ilyen korlátozást, hibaüzenetet ad vissza, amely egy másik bemenet megadását kéri. A következő általános elnevezési irányelvek érvényesek:

* A fürtneveknek 1–63 karakterből kell lenniük. Az engedélyezett karakterek csak betűk, számok, kötőjelek és aláhúzásjelek. Az első és az utolsó karakternek betűnek vagy számnak kell lennie.
* Az AKS *MC_* erőforráscsoport neve egyesíti az erőforráscsoport nevét és az erőforrásnevét. Az automatikusan generált szintaxis nem `MC_resourceGroupName_resourceName_AzureRegion` lehet nagyobb 80 karakternél. Szükség esetén csökkentse az erőforráscsoport vagy az AKS-fürt nevének hosszát.
* A *dnsPrefix* nek alfanumerikus értékekkel kell kezdődnie és végződnie, és 1-54 karakter között kell lennie. Az érvényes karakterek alfanumerikus értékeket és kötőjeleket (-) tartalmaznak. A *dnsPrefix* nem tartalmazhat speciális karaktereket, például pont (.).

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Hibákat kapok, amikor fürtöt próbál létrehozni, frissíteni, méretezni, törölni vagy frissíteni, ez a művelet nem engedélyezett, mivel egy másik művelet folyamatban van.

*Ez a hibaelhárítási segítség a aka.ms/aks-pending-operation*

A fürtműveletek korlátozottak, ha egy korábbi művelet még folyamatban van. A fürt részletes állapotának beolvasásához használja a `az aks show -g myResourceGroup -n myAKSCluster -o table` parancsot. Szükség szerint használja a saját erőforráscsoportot és az AKS-fürt nevét.

A fürt állapotának kimenete alapján:

* Ha a fürt nem *sikerült* vagy *sikertelen*kiépítési állapotban van, várjon, amíg a művelet ( Frissítés / Frissítés / Méretezés / Törlés */ Áttelepítés*) befejeződik. Az előző művelet befejeződése után próbálkozzon újra a legújabb fürtművelettel.

* Ha a fürt frissítése sikertelen, kövesse a következő lépéseket [kapok hibákat, hogy a fürt hibás állapotban van, és a frissítés vagy méretezés nem fog működni, amíg ki nem javították](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>Olyan hibákat kapok, amelyeket az egyszerű szolgáltatás nem talált, amikor új fürtöt próbálok létrehozni anélkül, hogy egy meglévőt átadnám.

AKS-fürt létrehozásakor szükség van egy egyszerű szolgáltatásra, hogy erőforrásokat hozzon létre az Ön nevében. Az AKS lehetővé teszi, hogy a fürt létrehozásakor újat hozlétre, de ehhez az Azure Active Directorynak ésszerű időn át teljes mértékben propagálnia kell az új egyszerű szolgáltatást annak érdekében, hogy a fürt sikeres legyen a létrehozásban. Ha ez a propagálás túl sokáig tart, a fürt nem sikerül létrehozni, mivel nem talál egy rendelkezésre álló egyszerű szolgáltatás erre. 

Az alábbi kerülő megoldásokat használja ehhez:
1. Használjon egy meglévő egyszerű szolgáltatás, amely már propagált régiók között, és létezik, hogy adja át az AKS fürt létrehozása idején.
2. Automatizálási parancsfájlok használata esetén adja hozzá az egyszerű szolgáltatás létrehozása és az AKS-fürt létrehozása közötti időbeli késéseket.
3. Az Azure Portal használata esetén a létrehozás során térjen vissza a fürtbeállításokhoz, majd néhány perc múlva próbálkozzon újra az érvényesítési lappal.

## <a name="im-receiving-errors-after-restricting-my-egress-traffic"></a>A kimenő forgalom korlátozása után kapok hibákat

Az AKS-fürtről érkező kimenő forgalom korlátozásakor szükség van [a kötelező és nem kötelező ajánlott](limit-egress-traffic.md) kimenő portokra / hálózati szabályokra és az AKS FQDN / alkalmazásszabályaira. Ha a beállítások ütköznek a szabályok bármelyikével, előfordulhat, `kubectl` hogy bizonyos parancsokat nem tud futtatni. Az AKS-fürt létrehozásakor hibák is megjelenhetnek.

Ellenőrizze, hogy a beállítások nem ütköznek-e a szükséges vagy választható ajánlott kimenő portokkal / hálózati szabályokkal és az FQDN / alkalmazásszabályokkal.

## <a name="azure-storage-and-aks-troubleshooting"></a>Az Azure Storage és az AKS hibaelhárítása

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>Melyek a Kubernetes ajánlott stabil verziói az Azure-lemezhez? 

| Kubernetes verzió | Ajánlott verzió |
| -- | :--: |
| 1.12 | 1.12.9. |
| 1.13 | 1.13.6. |
| 1.14 | 1.14.2. |


### <a name="what-versions-of-kubernetes-have-azure-disk-support-on-the-sovereign-cloud"></a>A Kubernetes mely verziói támogatják az Azure Disk-et a Sovereign Cloud szolgáltatásban?

| Kubernetes verzió | Ajánlott verzió |
| -- | :--: |
| 1.12 | 1.12.0 vagy újabb |
| 1.13 | 1.13.0 vagy újabb |
| 1.14 | 1.14.0 vagy újabb |


### <a name="waitforattach-failed-for-azure-disk-parsing-devdiskazurescsi1lun1-invalid-syntax"></a>WaitForAttach nem sikerült az Azure Disk: a "/dev/disk/azure/scsi1/lun1" elemzési

A Kubernetes 1.10-es verziójában a MountVolume.WaitForAttach sikertelen lehet az Azure Disk újracsatlakoztatásával.

Linux on előfordulhat, hogy helytelen DevicePath formátumhiba jelenik meg. Példa:

```console
MountVolume.WaitForAttach failed for volume "pvc-f1562ecb-3e5f-11e8-ab6b-000d3af9f967" : azureDisk - Wait for attach expect device path as a lun number, instead got: /dev/disk/azure/scsi1/lun1 (strconv.Atoi: parsing "/dev/disk/azure/scsi1/lun1": invalid syntax)
  Warning  FailedMount             1m (x10 over 21m)   kubelet, k8s-agentpool-66825246-0  Unable to mount volumes for pod
```

Windows rendszerben előfordulhat, hogy nem megfelelő A DevicePath(LUN) számhiba jelenik meg. Példa:

```console
Warning  FailedMount             1m    kubelet, 15282k8s9010    MountVolume.WaitForAttach failed for volume "disk01" : azureDisk - WaitForAttach failed within timeout node (15282k8s9010) diskId:(andy-mghyb
1102-dynamic-pvc-6c526c51-4a18-11e8-ab5c-000d3af7b38e) lun:(4)
```

A problémát a Kubernetes következő verzióiban javítottuk:

| Kubernetes verzió | Rögzített verzió |
| -- | :--: |
| 1.10 | 1.10.2. |
| 1.11 | 1.11.0 vagy újabb |
| 1.12 és újabb | N/A |

### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>Hiba az uid és a gid beállításakor az Azure-lemezhez való csatlakoztatási beállításokban

Az Azure Disk alapértelmezés szerint az ext4,xfs fájlrendszert használja, és a mountOptions, például az uid=x, a gid=x nem állítható be csatlakoztatáskor. Ha például a mountOptions uid=999,gid=999 kapcsolót próbálta beállítani, a következő höz hasonló hibaüzenet jelenik meg:

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

A problémát az alábbi módon enyhítheti:

* [Konfigurálja a pod biztonsági környezetét a](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) uid beállításával a runAsUser és a gid az fsGroup-ban. A következő beállítás például a pod futtatását gyökérként állítja be, és bármely fájl számára elérhetővé teszi azt:

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
  > Mivel a gid és uid vannak csatlakoztatva root vagy 0 alapértelmezés szerint. Ha a gid vagy uid nem gyökérként van beállítva, például `chown` 1000, a Kubernetes a lemez alatti összes könyvtár és fájl módosítására használja. Ez a művelet időigényes lehet, és nagyon lelassíthatja a lemez felszerelését.

* Használja `chown` initContainers beállítani gid és uid. Példa:

```yaml
initContainers:
- name: volume-mount
  image: busybox
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="error-when-deleting-azure-disk-persistentvolumeclaim-in-use-by-a-pod"></a>Hiba az Azure Disk PersistentVolumeClaim pod általi használata során

Ha megpróbál törölni egy Azure Disk PersistentVolumeClaim, amely egy pod által használt, előfordulhat, hogy egy hiba. Példa:

```console
$ kubectl describe pv pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06
...
Message:         disk.DisksClient#Delete: Failure responding to request: StatusCode=409 -- Original Error: autorest/azure: Service returned an error. Status=409 Code="OperationNotAllowed" Message="Disk kubernetes-dynamic-pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06 is attached to VM /subscriptions/{subs-id}/resourceGroups/MC_markito-aks-pvc_markito-aks-pvc_westus/providers/Microsoft.Compute/virtualMachines/aks-agentpool-25259074-0."
```

A Kubernetes 1.10-es és újabb verziójában alapértelmezés szerint engedélyezve van egy PersistentVolumeClaim védelmi szolgáltatás a hiba megelőzésére. Ha a Kubernetes olyan verzióját használja, amely nem rendelkezik a probléma javításával, enyhítheti ezt a problémát a pod persistentVolumeClaim használatával történő törlésével a PersistentVolumeClaim törlése előtt.


### <a name="error-cannot-find-lun-for-disk-when-attaching-a-disk-to-a-node"></a>Hiba: "Nem található a lemez logikai információja" hiba, amikor lemezt csatlakoztat egy csomóponthoz

Amikor lemezt csatlakoztat egy csomóponthoz, a következő hiba jelenhet meg:

```console
MountVolume.WaitForAttach failed for volume "pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6" : Cannot find Lun for disk kubernetes-dynamic-pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6
```

A problémát a Kubernetes következő verzióiban javítottuk:

| Kubernetes verzió | Rögzített verzió |
| -- | :--: |
| 1.10 | 1.10.10 vagy újabb |
| 1.11 | 1.11.5. |
| 1.12 | 1.12.3. |
| 1.13 | 1.13.0 vagy újabb |
| 1.14 és újabb | N/A |

Ha a Kubernetes olyan verzióját használja, amely nem rendelkezik a probléma javításával, néhány perc várakozással és újrapróbálkozva enyhítheti a problémát.

### <a name="azure-disk-attachdetach-failure-mount-issues-or-io-errors-during-multiple-attachdetach-operations"></a>Az Azure Disk csatolási/leválasztási hibája, csatlakoztatási problémái vagy I/O-hibák több csatolási/leválasztási művelet során

A Kubernetes 1.9.2-es verziójától kezdve, ha több csatolási/leválasztó műveletet futtat párhuzamosan, a következő lemezproblémák jelenhetnek meg egy piszkos virtuális gép gyorsítótára miatt:

* Lemezcsatolási/-leválasztási hibák
* Lemez I/O-hibái
* Váratlan lemezleválasztás a virtuális gépről
* Nem létező lemez csatolása miatt sikertelen állapotba lépő virtuális gép

A problémát a Kubernetes következő verzióiban javítottuk:

| Kubernetes verzió | Rögzített verzió |
| -- | :--: |
| 1.10 | 1.10.12. |
| 1.11 | 1.11.6. |
| 1.12 | 1.12.4. |
| 1.13 | 1.13.0 vagy újabb |
| 1.14 és újabb | N/A |

Ha a Kubernetes olyan verzióját használja, amely nem rendelkezik a probléma javításával, az alábbi verzióval enyhítheti a problémát:

* Ha a lemez hosszabb ideig vár a leválasztásra, próbálja meg manuálisan leválasztani a lemezt

### <a name="azure-disk-waiting-to-detach-indefinitely"></a>Az Azure Disk határozatlan időre vár a leválasztásra

Bizonyos esetekben, ha egy Azure Disk leválási művelet sikertelen az első kísérlet, nem próbálja meg újra a leválási műveletet, és továbbra is csatolva marad az eredeti csomópont virtuális gép. Ez a hiba akkor fordulhat elő, ha egy lemezt egyik csomópontról a másikra helyez át. Példa:

```console
[Warning] AttachVolume.Attach failed for volume "pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" : Attach volume "kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" to instance "/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-0" failed with compute.VirtualMachinesClient#CreateOrUpdate: Failure sending request: StatusCode=0 -- Original Error: autorest/azure: Service returned an error. Status= Code="ConflictingUserInput" Message="Disk '/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/disks/kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9' cannot be attached as the disk is already owned by VM '/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-1'."
```

A problémát a Kubernetes következő verzióiban javítottuk:

| Kubernetes verzió | Rögzített verzió |
| -- | :--: |
| 1.11 | 1.11.9. |
| 1.12 | 1.12.7. |
| 1.13 | 1.13.4. |
| 1.14 és újabb | N/A |

Ha a Kubernetes olyan verzióját használja, amely nem rendelkezik a probléma megoldásához, a lemez manuális leválasztásával enyhítheti a problémát.

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Az Azure Disk leválasztja a potenciális versenyhelyzeti problémát és az érvénytelen adatlemezlistát eredményező hibát

Ha egy Azure Disk nem sikerül leválasztani, akkor újra hat alkalommal leválasztja a lemezt exponenciális vissza. Az adatlemez-listán körülbelül 3 percig csomópontszintű zárolást is tart. Ha a lemezlistát manuálisan frissítik ebben az időszakban, például egy manuális csatolási vagy leválasztási műveletet, akkor a csomópontszintű zárolás által tartott lemezlista elavulttá válik, és instabilitást okoz a virtuális csomóponton.

A problémát a Kubernetes következő verzióiban javítottuk:

| Kubernetes verzió | Rögzített verzió |
| -- | :--: |
| 1.12 | 1.12.9. |
| 1.13 | 1.13.6. |
| 1.14 | 1.14.2. |
| 1.15 és újabb | N/A |

Ha a Kubernetes olyan verzióját használja, amely nem rendelkezik a probléma javításával, és a virtuális csomópont elavult lemezlistával rendelkezik, enyhítheti a problémát azáltal, hogy az összes nem létező lemezt egyetlen, tömeges műveletként leválasztja a virtuális gépről. **Előfordulhat, hogy a nem létező lemezek egyenkénti leválasztása sikertelen.**


### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>Az Azure-lemezek nagy száma lassú csatolást/leválasztást okoz

Ha a csomópont virtuális géphez csatlakoztatott Azure-lemezek száma 10-nél nagyobb, a csatolási és leválasztási műveletek lassúak lehetnek. Ez a probléma ismert probléma, és jelenleg nincsenek megoldások.

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Az Azure Disk leválasztási hibája potenciális csomóponti virtuális géphez vezet sikertelen állapotban

Bizonyos peremhálózati esetekben az Azure Disk leválása részben sikertelen lehet, és a csomópont virtuális gép e sikertelen állapotban marad.

A problémát a Kubernetes következő verzióiban javítottuk:

| Kubernetes verzió | Rögzített verzió |
| -- | :--: |
| 1.12 | 1.12.10 vagy újabb |
| 1.13 | 1.13.8. |
| 1.14 | 1.14.4. |
| 1.15 és újabb | N/A |

Ha a Kubernetes olyan verzióját használja, amely nem rendelkezik a probléma javításával, és a virtuális csomópont hibás állapotban van, enyhítheti a problémát a virtuális gép állapotának manuális frissítésével az alábbi egyik használatával:

* Rendelkezésre állási csoporton alapuló fürt esetén:
    ```azurecli
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* VMSS-alapú fürt esetén:
    ```azurecli
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Azure Files és AKS hibaelhárítás

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>Melyek a Kubernetes ajánlott stabil verziói az Azure-fájlokhoz?
 
| Kubernetes verzió | Ajánlott verzió |
| -- | :--: |
| 1.12 | 1.12.6. |
| 1.13 | 1.13.4. |
| 1.14 | 1.14.0 vagy újabb |

### <a name="what-versions-of-kubernetes-have-azure-files-support-on-the-sovereign-cloud"></a>A Kubernetes mely verziói támogatják az Azure Files-t a Sovereign Cloud szolgáltatásban?

| Kubernetes verzió | Ajánlott verzió |
| -- | :--: |
| 1.12 | 1.12.0 vagy újabb |
| 1.13 | 1.13.0 vagy újabb |
| 1.14 | 1.14.0 vagy újabb |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>Melyek az azure-fájlok használatakor az alapértelmezett mountOptions?What are the default mountOptions when using Azure Files?

Ajánlott beállítások:

| Kubernetes verzió | fileMode és dirMode érték|
| -- | :--: |
| 1.12.0 - 1.12.1 | 0755 |
| 1.12.2. | 0777 |

Ha egy fürt használata Kubernetes 1.8.5-ös vagy nagyobb verziójú, és dinamikusan hozza létre az állandó kötet egy tárolási osztály, csatlakoztatási beállításokat lehet megadni a tárolási osztály objektum. A következő példa *0777-et*állít be:

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

Néhány további hasznos *mountOptions* beállítások:

* *Az mfsymlinks* az Azure Files mount (CIFs) rendszert támogatja a szimbolikus hivatkozások számára
* *A nobrl* megakadályozza a bájttartomány-zárolási kérelmek küldését a kiszolgálóra. Ez a beállítás bizonyos alkalmazások esetében szükséges, amelyek cif stílussal szakítanak kötelező bájttartomány-zárolásokkal. A legtöbb CIF-kiszolgáló még nem támogatja a tanácsadói bájttartomány-zárolások kérelmezését. Ha nem használ *nobrl,* alkalmazások, amelyek szakítanak a CIF stílus kötelező bájt tartomány zárak okozhat hibaüzenetek hasonló:
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>Az Azure Files használata esetén a "nem módosítható engedélyek" hiba

Amikor a PostgreSQL-t futtatja az Azure Files beépülő modulon, a következőhöz hasonló hibaüzenet jelenhet meg:

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

Ezt a hibát az Azure Files beépülő modul okozza a CIFS/SMB protokoll használatával. A CIF/SMB protokoll használatakor a fájl- és könyvtárengedélyek et nem lehetett módosítani a csatlakoztatás után.

A probléma megoldásához használja *a subPath-ot* az Azure Disk beépülő modullal együtt. 

> [!NOTE] 
> Az ext3/4 lemeztípus esetén a lemez formázása után található egy elveszett+talált könyvtár.

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>Az Azure Files nagy késést tartalmaz az Azure Diskhez képest, amikor sok kis fájlt kezel

Bizonyos esetekben, például számos kis méretű fájlok kezelése, nagy késést tapasztalhat az Azure Files használata esetén az Azure Disk.

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>Hiba a "Hozzáférés engedélyezése a kiválasztott hálózatról a hozzáférés engedélyezése a kiválasztott hálózatról" beállítás engedélyezésekor a tárfiókban

Ha engedélyezi a hozzáférést a *kiválasztott hálózatról* egy tárfiókon, amely et az AKS dinamikus kiépítéséhez használják, hibaüzenet jelenik meg, amikor az AKS fájlmegosztást hoz létre:

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

Ez a hiba oka az, hogy a Kubernetes *állandókötet-vezérlő* nem a kiválasztott hálózaton van kiválasztva, amikor beállítja a *hozzáférést a kiválasztott hálózatról.*

A probléma az [Azure Files statikus kiépítésével](azure-files-volume.md)mérsékelheti a problémát.

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Az Azure Files nem csatlakoztatása a Windows podban

Ha egy Azure-fájlcsatlakoztatóval rendelkező Windows-pod törlődik, majd ugyanazon a csomóponton újra létre jön, a csatlakoztatás sikertelen lesz. Ez a hiba `New-SmbGlobalMapping` oka, hogy a parancs meghibásodott, mivel az Azure Files csatlakoztatása már csatlakoztatva van a csomóponton.

Előfordulhat például, hogy a következőhöz hasonló hibaüzenet jelenik meg:

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

A problémát a Kubernetes következő verzióiban javítottuk:

| Kubernetes verzió | Rögzített verzió |
| -- | :--: |
| 1.12 | 1.12.6. |
| 1.13 | 1.13.4. |
| 1.14 és újabb | N/A |

### <a name="azure-files-mount-fails-due-to-storage-account-key-changed"></a>Az Azure Files csatlakoztatása sikertelen a tárfiók kulcsának módosítása miatt

Ha a tárfiók kulcsa megváltozott, előfordulhat, hogy az Azure Files csatlakoztatási hibák.

A probléma mérsékelheti a problémát manuálisan az *azurestorageaccountkey* mező manuális frissítése az Azure-fájl titkos a base64-kódolású tárfiók kulcs.

A tárfiók kulcsának base64-ben történő kódolásához használhatja a használatát. `base64` Példa:

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

Az Azure titkos fájljának frissítéséhez használja a használatát. `kubectl edit secret` Példa:

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

Néhány perc múlva az ügynök csomópont újra megpróbálja az azure file mount a frissített tárolókulcs.

### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>A fürt automatikus skálázója nem skálázható, hiba miatt nem sikerült kijavítani a csomópontcsoport méreteit

Ha a fürt automatikus skálázója nem skálázható fel/le, és a [fürt automatikus méretezőnaplóiban][view-master-logs]az alábbihoz hasonló hiba jelenik meg.

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

Ez a hiba egy upstream fürt automatikus skálázó versenyállapotának köszönhető, ahol a fürt automatikus skálázója a fürtben lévőtől eltérő értékkel végződik. Ha ki szeretne jutni ebből az állapotból, egyszerűen tiltsa le és engedélyezze újra a [fürt automatikus skálázóját.][cluster-autoscaler]

### <a name="slow-disk-attachment-getazuredisklun-takes-10-to-15-minutes-and-you-receive-an-error"></a>Lassú lemez mellékletet, GetAzureDiskLun tart 10-15 perc, és hibaüzenetjelenik meg

Az **1.15.0-nál régebbi** Kubernetes-verziókon olyan hibaüzenet jelenhet meg, mint például **a Hiba WaitForAttach Nem található lun a lemezhez.**  A megoldás az, hogy várjon körülbelül 15 percet, és próbálja meg újra.

<!-- LINKS - internal -->
[view-master-logs]: view-master-logs.md
[cluster-autoscaler]: cluster-autoscaler.md
