---
title: Gyakori kérdések az Azure Kubernetes szolgáltatáshoz (AKS)
description: Válaszok az Azure Kubernetes-szolgáltatással (AKS) kapcsolatos gyakori kérdésekre.
ms.topic: conceptual
ms.date: 10/02/2019
ms.openlocfilehash: c4bb4328af5df7f729967c7b249847b2ab098770
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497765"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Gyakori kérdések az Azure Kubernetes Service-szel (AKS) kapcsolatban

Ez a cikk az Azure Kubernetes-szolgáltatással (AKS) kapcsolatos gyakori kérdésekkel foglalkozik.

## <a name="which-azure-regions-currently-provide-aks"></a>Mely Azure-régiók biztosítják jelenleg az AKS-t?

Az elérhető régiók teljes listáját az [AKS-régiók és elérhetőségek között.][aks-regions]

## <a name="does-aks-support-node-autoscaling"></a>Támogatja az AKS a csomópont automatikus skálázását?

Igen, az a képesség, hogy automatikusan skálázható ügynök csomópontok vízszintesen az AKS-ben jelenleg elérhető előzetes verzióban. Az utasításokat az [AKS alkalmazásigényeinek megfelelően a fürt automatikus méretezése.][aks-cluster-autoscaler] Az AKS automatikus skálázása a [Kubernetes automatikus skálázón][auto-scaler]alapul.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Telepíthetem az AKS-t a meglévő virtuális hálózatomba?

Igen, az AKS-fürtöket a speciális hálózati [szolgáltatás][aks-advanced-networking]sal telepítheti egy meglévő virtuális hálózatba.

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Korlátozhatom, hogy ki férhet hozzá a Kubernetes API-kiszolgálóhoz?

Igen, korlátozhatja a Hozzáférést a Kubernetes API-kiszolgálóhoz az [API-kiszolgáló engedélyezett IP-tartományai][api-server-authorized-ip-ranges]használatával.

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>A Kubernetes API-kiszolgálót csak a virtuális hálózaton belül tudom elérhetővé tenni?

Jelenleg nem, de ez a terv. Nyomon követheti a folyamatot az [AKS GitHub tárban.][private-clusters-github-issue]

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Lehet, hogy a különböző virtuális gép méretek egy fürtben?

Igen, az AKS-fürtben különböző virtuálisgép-méreteket használhat [több csomópontkészlet][multi-node-pools]létrehozásával.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>A biztonsági frissítések az AKS-ügynök csomópontokra vonatkoznak?

Az Azure automatikusan alkalmazza a biztonsági javításokat a fürt Linux-csomópontjaira egy éjszakai ütemezés szerint. Azonban ön felelős annak biztosításáért, hogy ezek a Linux-csomópontok szükség szerint újrainduljanak. A csomópontok újraindítására számos lehetőség közül választhat:

- Manuálisan, az Azure Portalon vagy az Azure CLI-n keresztül.
- Az AKS-fürt frissítésével. A fürt automatikusan frissíti [a kordont és a csatornacsomópontokat,][cordon-drain] majd egy új csomópontot hoz online a legújabb Ubuntu lemezképpel és egy új javítási verzióval vagy egy kisebb Kubernetes verzióval. További információt az [AKS-fürt frissítése című témakörben talál.][aks-upgrade]
- A [Kured](https://github.com/weaveworks/kured)használatával egy nyílt forráskódú újraindítás démon a Kubernetes. Kured [daemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) néven fut, és figyeli az egyes csomópontok egy fájl jelenlétét, amely azt jelzi, hogy újraindításszükséges. A fürtön keresztül az operációs rendszer újraindítása kezeli ugyanaza [kordon és a kiürítési folyamat,][cordon-drain] mint a fürt frissítése.

A kured használatáról további információt a [Biztonsági és kernelfrissítések alkalmazása az AKS csomópontjaira című témakörben talál.][node-updates-kured]

### <a name="windows-server-nodes"></a>Windows Server-csomópontok

Windows Server-csomópontok esetén (jelenleg előzetes verzióban az AKS-ben) a Windows Update nem futtatja automatikusan és nem alkalmazza a legújabb frissítéseket. A Windows Update kiadási ciklusa és a saját ellenőrzési folyamat körüli rendszeres ütemezés szerint frissítenie kell a fürtöt és az AKS-fürt Windows Server csomópontkészlete(i)t. Ez a frissítési folyamat olyan csomópontokat hoz létre, amelyek a legújabb Windows Server-lemezképet és javításokat futtatják, majd eltávolítja a régebbi csomópontokat. Erről a folyamatról további információt az [AKS csomópontkészletének frissítése című témakörben talál.][nodepool-upgrade]

## <a name="why-are-two-resource-groups-created-with-aks"></a>Miért jön létre két erőforráscsoport az AKS-sel?

Az AKS számos Azure-infrastruktúra-erőforrásra épül, beleértve a virtuálisgép-méretezési készleteket, a virtuális hálózatokat és a felügyelt lemezeket. Ez lehetővé teszi, hogy kihasználja az Azure platform számos alapvető képességeit az AKS által biztosított felügyelt Kubernetes környezetben. Például a legtöbb Azure virtuálisgép-típusok közvetlenül használható AKS és az Azure-foglalások segítségével kedvezményeket kap nak az erőforrások automatikusan.

Az architektúra engedélyezéséhez minden AKS-telepítés két erőforráscsoportra terjed ki:

1. Az első erőforráscsoportot hozza létre. Ez a csoport csak a Kubernetes szolgáltatáserőforrást tartalmazza. Az AKS-erőforrás-szolgáltató automatikusan létrehozza a második erőforráscsoportot a telepítés során. A második erőforráscsoport ra van *példa: MC_myResourceGroup_myAKSCluster_eastus.* A második erőforráscsoport nevének megadásáról a következő szakaszban talál tájékoztatást.
1. A második erőforráscsoport, más néven a *csomópont erőforráscsoport*tartalmazza a fürthöz társított összes infrastruktúra-erőforrást. Ezek az erőforrások közé tartozik a Kubernetes csomópont virtuális gépek, a virtuális hálózatés a tárolás. Alapértelmezés szerint a csomópont erőforráscsoportjának neve például *MC_myResourceGroup_myAKSCluster_eastus.* Az AKS automatikusan törli a csomóponterőforrást a fürt törlésekor, ezért csak olyan erőforrásokhoz használható, amelyek megosztják a fürt életciklusát.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Megadhatom a saját nevemet az AKS-csomópont erőforráscsoportnak?

Igen. Alapértelmezés szerint az AKS a csomópont erőforráscsoportját *MC_resourcegroupname_clustername_location*nevezi el, de megis adja a saját nevét is.

Saját erőforráscsoport nevének megadásához telepítse az [aks-preview][aks-preview-cli] Azure CLI bővítmény *0.3.2-es* vagy újabb verzióját. Amikor az [aks create][az-aks-create] paranccsal hoz létre egy AKS-fürtöt, használja a *--node-resource-group* paramétert, és adja meg az erőforráscsoport nevét. Ha [egy AKS-fürt][aks-rm-template] üzembe helyezéséhez Egy Azure Resource Manager-sablont használ, a *nodeResourceGroup* tulajdonság használatával meghatározhatja az erőforráscsoport nevét.

* A másodlagos erőforráscsoportot automatikusan létrehozza az Azure-erőforrás-szolgáltató a saját előfizetésében.
* Egyéni erőforráscsoport nevét csak a fürt létrehozásakor adhatja meg.

A csomópont erőforráscsoportnal végzett munka közben ne feledje, hogy a következők nem tehető:

* Adjon meg egy meglévő erőforráscsoportot a csomópont erőforráscsoporthoz.
* Adjon meg egy másik előfizetést a csomópont erőforráscsoporthoz.
* Módosítsa a csomópont erőforráscsoportjának nevét a fürt létrehozása után.
* Adja meg a csomóponterőforrás-csoporton belüli felügyelt erőforrások nevét.
* A csomóponterőforrás-csoporton belüli felügyelt erőforrások címkéinek módosítása vagy törlése. (További információk a következő szakaszban.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Módosíthatom az AKS-erőforrások címkéit és egyéb tulajdonságait a csomóponterőforrás-csoportban?

Ha módosítja vagy törli az Azure által létrehozott címkéket és más erőforrás-tulajdonságokat a csomópont erőforráscsoportban, váratlan eredményeket kaphat, például skálázási és frissítési hibákat. Az AKS lehetővé teszi egyéni címkék létrehozását és módosítását. Előfordulhat, hogy egyéni címkéket szeretne létrehozni vagy módosítani, például egy részleg vagy költséghely hozzárendeléséhez. Az AKS-fürt csomóponterőforrás-csoportján lévő erőforrások módosításával megszakítja a szolgáltatásszintű célkitűzést (SLO). További információ: [Az AKS szolgáltatásiszint-szerződést kínál?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Milyen Kubernetes felvételi vezérlőket támogat az AKS? Lehet-e belépővezérlőket hozzáadni vagy eltávolítani?

Az AKS a következő [felvételi vezérlőket][admission-controllers]támogatja:

- *NévtérÉletciklus*
- *LimitRanger között*
- *Szolgáltatásfiók*
- *DefaultStorageClass*
- *Alapértelmezett TolerationSeconds*
- *MutingAdmissionWebhook*
- *A felvételiwebhook érvényesítése*
- *Erőforrásquota*

Jelenleg nem módosíthatja a felvételi vezérlők listáját az AKS-ben.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Integrálva van az Azure Key Vault az AKS-sel?

Az AKS jelenleg nincs natív módon integrálva az Azure Key Vaultszolgáltatással. Azonban az [Azure Key Vault FlexVolume for Kubernetes projekt][keyvault-flexvolume] lehetővé teszi a Kubernetes-podok közvetlen integrációját a Key Vault titkos kulcsokkal.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Futtathatok Windows Server-tárolókat az AKS-en?

Igen, a Windows Server-tárolók előzetes verzióban érhetők el. A Windows Server-tárolók AKS-ben való futtatásához hozzon létre egy csomópontkészletet, amely a Windows Server t futtatja vendég operációs rendszerként. A Windows Server-tárolók csak Windows Server 2019-et használhatnak. Első lépésekért olvassa el Az [AKS-fürt létrehozása Windows Server csomópontkészlettel című témakört.][aks-windows-cli]

A Csomópontkészlet Windows Server-támogatása tartalmaz néhány korlátozást, amelyek a Kubernetes-projekt felsőáramú Windows Server-ének részét képezik. Ezekről a korlátozásokról további információt a [Windows Server-tárolók AKS-korlátozásokban című témakörben talál.][aks-windows-limitations]

## <a name="does-aks-offer-a-service-level-agreement"></a>Az AKS kínál szolgáltatásiszint-szerződést?

A szolgáltatásiszint-szerződésben (SLA) a szolgáltató vállalja, hogy megtéríti az ügyfélnek a szolgáltatás költségét, ha a közzétett szolgáltatási szint nem teljesül. Mivel az AKS ingyenes, költség nem térítendő visszatérítésre, így az AKS-nek nincs hivatalos SLA-ja. Az AKS azonban legalább 99,5 százalékos rendelkezésre állást kíván fenntartani a Kubernetes API-kiszolgáló számára.

Fontos felismerni a különbséget az AKS-szolgáltatás rendelkezésre állása, amely a Kubernetes vezérlősík üzemidejére és az Azure virtuális gépeken futó adott számítási feladatok rendelkezésre állása közötti különbséget. Bár a vezérlősík nem érhető el, ha a vezérlősík nem áll készen, az Azure virtuális gépeken futó fürt-munkaterhelések továbbra is működhetnek. Mivel az Azure-beli virtuális gépek fizetős erőforrások, pénzügyi SLA-val vannak támogatva. Itt [további részleteket az](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) Azure VM SLA és hogyan növelheti, hogy a rendelkezésre állás olyan funkciókkal, mint a [rendelkezésre állási zónák.][availability-zones]

## <a name="why-cant-i-set-maxpods-below-30"></a>Miért nem tudom a maxPodot 30 alá állítani?

Az AKS-ben beállíthatja az `maxPods` értéket, amikor létrehozza a fürtet az Azure CLI és az Azure Resource Manager sablonokkal. Azonban mind a Kubenet és az Azure CNI igényel *egy minimális értéket* (érvényesítve a létrehozás időpontjában):

| Hálózat | Minimális | Maximum |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubenet között | 30 | 110 |

Mivel az AKS felügyelt szolgáltatás, a fürt részeként telepítjük és kezeljük a bővítményeket és a podokat. A múltban a felhasználók `maxPods` megadhatnak egy alacsonyabb értéket, mint a felügyelt podok futtatásához szükséges érték (például 30). Az AKS mostantól a következő képlettel számítja ki a podok minimális számát: ((maxPods vagy (maxPods * vm_count)) > felügyelt kiegészítő podok minimális.

A felhasználók nem bírálhatják felül a minimális `maxPods` érvényesítést.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Alkalmazhatok Azure-foglalási kedvezményeket az AKS-ügynökcsomópontjaimra?

Az AKS-ügynökcsomópontok számlázása szabványos Azure-virtuális gépként történik, így ha az AKS-ben használt virtuális gépmérethez vásárolt [Azure-foglalásokat,][reservation-discounts] akkor ezek a kedvezmények automatikusan érvénybe lépnek.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Áthelyezhetem/áttelepíthetem a fürtömet az Azure-bérlők között?

A `az aks update-credentials` parancs segítségével egy AKS-fürt az Azure-bérlők között. Kövesse az [Egyszerű szolgáltatás frissítésének vagy létrehozásának kiválasztása,](https://docs.microsoft.com/azure/aks/update-credentials) majd az [aks-fürt frissítése új hitelesítő adatokkal című](https://docs.microsoft.com/azure/aks/update-credentials#update-aks-cluster-with-new-service-principal-credentials)itt található utasításokat.

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>Áthelyezhetem/áttelepíthetem a fürtömet az előfizetések között?

A fürtök előfizetések közötti mozgása jelenleg nem támogatott.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>Áthelyezhetem az AKS-fürtjeimet az aktuális Azure-előfizetésből egy másikba? 

Az AKS-fürt és az Azure-előfizetések közötti társított erőforrások áthelyezése nem támogatott.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Miért tart ilyen sokáig a fürttörlésem? 

A legtöbb fürt a felhasználó kérésére törlődik; bizonyos esetekben, különösen akkor, ha az ügyfelek saját erőforráscsoportot hoznak, vagy az RG-feladatok közötti törlés további időt vehet igénybe, vagy sikertelen. Ha problémája van a törlésekkel, ellenőrizze, hogy nincs-e zárolás az RG-n, hogy az RG-n kívüli erőforrások nincsenek-e társítva az RG-től stb.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Ha pod / központi telepítések állapotban "NodeLost" vagy "Ismeretlen" lehet továbbra is frissíteni a fürtöt?

Megteheti, de az AKS nem ajánlja ezt. A frissítéseket ideális esetben akkor kell végrehajtani, ha a fürt állapota ismert és kifogástalan.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Ha egy fürt egy vagy több csomópont nem megfelelő állapotban van, vagy leáll, végrehajthatok egy frissítést?

Nem, törölje vagy távolítsa el a sikertelen állapotú vagy a frissítés előtt más módon eltávolított csomópontokat.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>Futottam egy fürt törlése, de lásd a hibát`[Errno 11001] getaddrinfo failed` 

Ezt leggyakrabban az okozza, hogy azok a felhasználók, akikegy vagy több hálózati biztonsági csoportot (NSG) használnak, és a fürthöz vannak társítva.  Távolítsa el őket, és próbálja meg újra törölni őket.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Lefuttattam egy frissítést, de most a hüvelyeim összeomlási hurkokban vannak, és a készenléti szondák sikertelenek?

Ellenőrizze, hogy az egyszerű szolgáltatás nem járt-e le.  Lásd: [Az AKS szolgáltatásnév és](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) [az AKS frissítési hitelesítő adatai](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="my-cluster-was-working-but-suddenly-can-not-provision-loadbalancers-mount-pvcs-etc"></a>Saját klaszter működött, de hirtelen nem tud kiépíteni LoadBalancers, mount PvC-k, stb? 

Ellenőrizze, hogy az egyszerű szolgáltatás nem járt-e le.  Lásd: [Az AKS szolgáltatásnév és](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) [az AKS frissítési hitelesítő adatai](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>Használhatom a virtuálisgép-méretezési készlet API-k at manuálisan skálázható?

Nem, a méretezési műveletek a virtuálisgép-méretezési készlet API-k használatával nem támogatottak. Használja az AKS`az aks scale`API-kat ( ).

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-0-nodes"></a>Használhatom a virtuálisgép-méretezési csoportokat manuálisan 0 csomópontra való méretezéshez?

Nem, a méretezési műveletek a virtuálisgép-méretezési készlet API-k használatával nem támogatottak.

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>Leállíthatom vagy leoszthatom az összes virtuális gépemet?

Bár az AKS rugalmassági mechanizmusokkal rendelkezik, hogy ellenálljon egy ilyen konfigurációnak, és helyreállítsa azt, ez nem ajánlott konfiguráció.

## <a name="can-i-use-custom-vm-extensions"></a>Használhatok egyéni virtuálisgép-bővítményeket?

Az AKS nem felügyelt szolgáltatás, és az IaaS-erőforrások kezelése nem támogatott. Egyéni összetevők telepítése stb. kérjük, használja ki a Kubernetes API-k és mechanizmusok. A DaemonSets for a szükséges összetevők telepítéséhez használja például a DaemonSets elemet.

<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: windows-node-limitations.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md
[availability-zones]: ./availability-zones.md

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
