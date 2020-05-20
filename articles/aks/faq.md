---
title: Gyakran ismételt kérdések az Azure Kubernetes szolgáltatásról (ak)
description: Válaszok az Azure Kubernetes szolgáltatással (ak) kapcsolatos gyakori kérdésekre.
ms.topic: conceptual
ms.date: 05/14/2020
ms.openlocfilehash: 767b5b80aab7d98af92f86bf66cc2ff83242ff92
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83677786"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Gyakori kérdések az Azure Kubernetes Service-szel (AKS) kapcsolatban

Ez a cikk az Azure Kubernetes Service (ak) szolgáltatással kapcsolatos gyakori kérdéseket tárgyalja.

## <a name="which-azure-regions-currently-provide-aks"></a>Mely Azure-régiók jelenleg biztosítanak AK-t?

Az elérhető régiók teljes listájáért lásd: [AK-régiók és rendelkezésre állás][aks-regions].

## <a name="can-i-spread-an-aks-cluster-across-regions"></a>El lehet osztani egy AK-fürtöt régiók között?

Nem. Az AK-fürtök regionális erőforrások, és nem terjedhetnek ki régiókra. Tekintse meg az [ajánlott eljárásokat az üzletmenet folytonossága és a vész-helyreállítás][bcdr-bestpractices] című témakörben, amely útmutatást nyújt a több régiót tartalmazó architektúra létrehozásához.

## <a name="can-i-spread-an-aks-cluster-across-availability-zones"></a>El tudok osztani egy AK-fürtöt a rendelkezésre állási zónák között?

Igen. Egy vagy több [rendelkezésre állási zónában][availability-zones] üzembe HELYEZHET egy AK-fürtöt az [azokat támogató régiók][az-regions]között.

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Korlátozható, hogy ki férhet hozzá a Kubernetes API-kiszolgálóhoz?

Igen. Az API-kiszolgáló elérésének korlátozására két lehetőség áll rendelkezésre:

- Ha egy nyilvános végpontot szeretne fenntartani az API-kiszolgáló számára, használja az [API-kiszolgáló által jogosult IP-tartományokat][api-server-authorized-ip-ranges] , de korlátozza a hozzáférést a megbízható IP-címtartományok készletéhez.
- Használjon [privát fürtöt][private-clusters] , ha korlátozni szeretné az API-kiszolgálót, hogy *csak* a virtuális hálózaton belül legyen elérhető.

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Használhatok különböző virtuálisgép-méreteket egyetlen fürtben?

Igen, [több Node-készlet][multi-node-pools]létrehozásával különböző virtuálisgép-méreteket is HASZNÁLHAT az AK-fürtben.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Az AK-ügynökök csomópontjain vannak biztonsági frissítések alkalmazva?

Az Azure automatikusan a fürt Linux-csomópontjaira alkalmazza a biztonsági javításokat. Azonban Ön felelős annak biztosításáért, hogy a Linux-csomópontok szükség szerint újraindulnak. Több lehetőség is van a csomópontok újraindítására:

- Manuálisan, a Azure Portalon vagy az Azure CLI-n keresztül.
- Az AK-fürt frissítésével. A fürt automatikusan frissíti a [Cordon lévő és a kiürítési csomópontokat][cordon-drain] , majd egy új csomópontot online állapotba helyez a legújabb Ubuntu-lemezképpel és egy új patch-verzióval vagy egy kisebb Kubernetes-verzióval. További információ: AK- [fürt frissítése][aks-upgrade].
- A [Kured](https://github.com/weaveworks/kured)egy nyílt forráskódú újraindítási démont használ a Kubernetes. A Kured [daemonset elemet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) fut, és figyeli az egyes csomópontokat egy olyan fájl jelenléte esetén, amely azt jelzi, hogy újraindítás szükséges. A fürtben az operációs rendszer újraindítását a fürt frissítésével megegyező [Kordon-és kiürítési folyamat][cordon-drain] kezeli.

További információ a kured használatáról: [biztonsági és kernel-frissítések alkalmazása a csomópontokra az AK-ban][node-updates-kured].

### <a name="windows-server-nodes"></a>Windows Server-csomópontok

A Windows Server-csomópontok esetében Windows Update nem fut automatikusan, és nem alkalmazza a legújabb frissítéseket. A Windows Update kiadási ciklus és a saját ellenőrzési folyamata körüli rendszeres ütemterv esetén frissíteni kell a fürtöt és a Windows Server-csomópontok készletét az AK-fürtben. Ez a frissítési folyamat olyan csomópontokat hoz létre, amelyek a legújabb Windows Server-lemezképet és-javításokat futtatják, majd eltávolítja a régebbi csomópontokat. További információ erről a folyamatról: [Node-készlet frissítése az AK-ban][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Miért jött létre két erőforráscsoport az AK-val?

Az AK számos Azure-infrastruktúra-erőforrásra épül, beleértve a virtuálisgép-méretezési csoportokat, a virtuális hálózatokat és a felügyelt lemezeket. Ez lehetővé teszi az Azure platform számos alapvető funkciójának kihasználása az AK által biztosított felügyelt Kubernetes-környezeten belül. Például az Azure-beli virtuálisgép-típusok többsége közvetlenül is használható az AK-val, és a Azure Reservations használatával automatikusan kedvezményeket kaphat ezekről az erőforrásokról.

Az architektúra engedélyezéséhez minden AK-beli telepítés két erőforráscsoportot ölel fel:

1. Hozza létre az első erőforráscsoportot. Ez a csoport csak a Kubernetes szolgáltatás erőforrását tartalmazza. Az AK erőforrás-szolgáltatója automatikusan létrehozza a második erőforráscsoportot az üzembe helyezés során. A második erőforráscsoport példája *MC_myResourceGroup_myAKSCluster_eastus*. A második erőforráscsoport nevének megadásával kapcsolatos információkért tekintse meg a következő szakaszt.
1. A második erőforráscsoport, azaz a csomópont- *erőforráscsoport*a fürthöz társított összes infrastruktúra-erőforrást tartalmazza. Ezek az erőforrások magukban foglalják a Kubernetes csomópontos virtuális gépeket, a virtuális hálózatkezelést és a tárterületet. Alapértelmezés szerint a csomópont-erőforráscsoport neve például *MC_myResourceGroup_myAKSCluster_eastus*. Az AK automatikusan törli a csomópont-erőforrást, amikor a fürt törlődik, ezért csak olyan erőforrásokhoz használható, amelyek osztoznak a fürt életciklusán.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Megadhatom a saját nevet az AK-csomópont erőforráscsoporthoz?

Igen. Alapértelmezés szerint a (z) *MC_resourcegroupname_clustername_location*csomópont-erőforráscsoport neve lesz, de a saját nevét is megadhatja.

A saját erőforráscsoport-név megadásához telepítse az [AK – előzetes][aks-preview-cli] VERZIÓJÚ Azure CLI-bővítmény *0.3.2* vagy újabb verzióját. Ha az az [AK Create][az-aks-create] paranccsal hoz létre egy AK-fürtöt, használja a *--Node-Resource-Group* paramétert, és adja meg az erőforráscsoport nevét. Ha [Azure Resource Manager sablont használ][aks-rm-template] egy AK-fürt üzembe helyezéséhez, megadhatja az erőforráscsoport nevét a *nodeResourceGroup* tulajdonság használatával.

* Az Azure erőforrás-szolgáltató automatikusan létrehozza a másodlagos erőforráscsoportot a saját előfizetésében.
* Egyéni erőforráscsoport-nevet csak a fürt létrehozásakor adhat meg.

A csomópont-erőforráscsoport használata során ne feledje, hogy a következők nem használhatók:

* Válasszon egy meglévő erőforráscsoportot a csomópont-erőforráscsoport számára.
* Eltérő előfizetést kell megadni a csomópont-erőforráscsoport számára.
* Módosítsa a csomópont-erőforráscsoport nevét a fürt létrehozása után.
* A csomópont erőforráscsoporthoz tartozó felügyelt erőforrások nevének megadása.
* A kezelt erőforrások címkéit módosíthatja vagy törölheti a csomópont-erőforráscsoport alatt. (További információt a következő szakaszban talál.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Módosíthatom a csomóponti erőforráscsoporthoz tartozó AK-erőforrások címkéit és egyéb tulajdonságait?

Ha módosítja vagy törli az Azure által létrehozott címkéket és az egyéb erőforrás-tulajdonságokat a csomópont-erőforráscsoport esetében, váratlan eredményekhez juthat, például a skálázási és a frissítési hibákhoz. Az AK lehetővé teszi egyéni címkék létrehozását és módosítását. Előfordulhat, hogy egyéni címkéket kíván létrehozni vagy módosítani, például egy üzleti egység vagy költséghely hozzárendeléséhez. Ha módosítja az AK-fürtben található csomópont-erőforráscsoport erőforrásait, megszakítja a szolgáltatási szint célkitűzését (SLO). További információkért lásd: az [AK-t kínáló szolgáltatás szintű szerződés?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Milyen Kubernetes-beléptetési vezérlőket támogat az AK? Hozzáadhatók vagy eltávolíthatók a beléptetési vezérlők?

Az AK a következő [belépésvezérlés-vezérlőket][admission-controllers]támogatja:

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*

Jelenleg nem módosítható a belépésvezérlés az AK-ban.

## <a name="can-i-use-admission-controller-webhooks-on-aks"></a>Használhatom a belépésvezérlés webhookokat az AK-on?

Igen, használhatja a belépésvezérlés webhookokat az AK-ban. Javasoljuk, hogy zárja ki a **vezérlő-sík címkével** megjelölt belső AK-névtereket. Például az alábbi hozzáadásával a webhook-konfigurációhoz:

```
namespaceSelector:
    matchExpressions:
    - key: control-plane
      operator: DoesNotExist
```

## <a name="can-admission-controller-webhooks-impact-kube-system-and-internal-aks-namespaces"></a>A beléptetési vezérlő webhookok hatással vannak a Kube-rendszerek és a belső AK-beli névterekre?

A rendszer stabilitásának biztosítása és az egyéni belépésvezérlés megakadályozása a Kube-rendszer belső szolgáltatásainak befolyásolása érdekében a névtér AK-ban van egy **beléptetési végrehajtó**, amely automatikusan kizárja a Kube-rendszer és az AK belső névtereit. Ez a szolgáltatás biztosítja, hogy az egyéni belépésvezérlés ne befolyásolja a Kube-rendszeren futó szolgáltatásokat.

Ha olyan kritikus használati esettel rendelkezik, amely a Kube-rendszeren (nem ajánlott) van telepítve, amelyet az egyéni beléptetési webhooknak kell megadnia, akkor az alábbi címkét vagy jegyzetet is hozzáadhatja, hogy a felvételi kényszerítő figyelmen kívül hagyja azt.

Címke: ```"admissions.enforcer/disabled": "true"``` vagy Megjegyzés:```"admissions.enforcer/disabled": true```

## <a name="is-azure-key-vault-integrated-with-aks"></a>Integrálva van Azure Key Vault AK-val?

Az AK jelenleg nincs natív módon integrálva a Azure Key Vaultba. A [CSI Secrets áruház Azure Key Vault szolgáltatója][csi-driver] azonban lehetővé teszi a közvetlen integrációt a Kubernetes-hüvelyből a Key Vault titkokba.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Futtathatok Windows Server-tárolókat az AK-ban?

Igen, a Windows Server-tárolók elérhetők az AK-ban. A Windows Server-tárolók az AK-ban való futtatásához létre kell hoznia egy Windows Servert futtató csomópont-készletet a vendég operációs rendszerként. A Windows Server-tárolók csak a Windows Server 2019-es kiszolgálókat használhatják. Első lépésként tekintse meg a következőt: [AK-fürt létrehozása Windows Server Node-készlettel][aks-windows-cli].

A Windows Server rendszerhez készült csomópont-támogatás olyan korlátozásokat tartalmaz, amelyek a felsőbb rétegbeli Windows-kiszolgáló részét képezik a Kubernetes projektben. További információ ezekről a korlátozásokról: a [Windows Server-tárolók AK-beli korlátozásai][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>Biztosít-e az AK szolgáltatás szintű szerződést?

Az AK SLA-garanciákat biztosít a választható szolgáltatásokhoz, és a [rendelkezésre állási SLA][uptime-sla]-t is igénybe veheti.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Alkalmazhatom az Azure-beli foglalási kedvezményeket az AK-ügynökök csomópontjaira?

Az AK-ügynökök csomópontjai standard Azure-beli virtuális gépekként vannak kiszámlázva, így ha az AK-ban használt virtuálisgép-mérethez vásárolt [Azure-foglalást][reservation-discounts] , a rendszer automatikusan alkalmazza ezeket a kedvezményeket.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Áthelyezhetem/Áttelepíthetem a fürtöt az Azure-bérlők között?

A `az aks update-credentials` parancs használatával egy AK-fürt helyezhető át az Azure-bérlők között. Kövesse a következő témakör utasításait: [frissítés vagy létrehozás egyszerű szolgáltatásnév](https://docs.microsoft.com/azure/aks/update-credentials) , majd az [AK-fürt frissítése új hitelesítő adatokkal](https://docs.microsoft.com/azure/aks/update-credentials#update-aks-cluster-with-new-service-principal-credentials).

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>Áthelyezhetem/Áttelepíthetem a fürtöt az előfizetések között?

A fürtök előfizetések közötti áthelyezése jelenleg nem támogatott.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>Áthelyezhetem az AK-fürtöket az aktuális Azure-előfizetésből egy másikba? 

Az AK-fürt áthelyezése és az Azure-előfizetések közötti társított erőforrások nem támogatottak.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Miért törli a fürtem a munkaidőt? 

A rendszer a legtöbb fürtöt törli a felhasználói kérelem után; bizonyos esetekben – különösen abban az esetben, ha az ügyfelek a saját erőforráscsoportot használják, vagy ha a több-RG feladatok törlésével – további időt vagy hibát okozhatnak. Ha problémák merülnek fel a törléssel kapcsolatban, ellenőrizze, hogy nincsenek-e zárolások a RG-on, hogy a RG-on kívüli összes erőforrás társítva van-e a RG-hoz stb.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Ha a "NodeLost" vagy az "Unknown" állapotú Pod/üzemelő példányok továbbra is frissíthetem a fürtöt?

De az AK nem javasolja ezt. A frissítéseket ideális esetben kell végrehajtani, ha a fürt állapota ismert és kifogástalan.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Ha egy vagy több csomóponttal rendelkező fürt nem kifogástalan állapotú vagy leáll, frissíthetem a frissítést?

Nem, törölje vagy távolítsa el a meghibásodott állapotú csomópontokat, vagy távolítsa el a fürtöt a frissítés előtt.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>Futtattam egy fürtöt, de a következő hibaüzenet jelenik meg:`[Errno 11001] getaddrinfo failed` 

Ezt általában az okozza, hogy a felhasználók egy vagy több hálózati biztonsági csoporttal (NSG) rendelkeznek még használatban, és a fürthöz vannak társítva.  Távolítsa el őket, és próbálkozzon újra a törléssel.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Futtattam egy frissítést, de most a hüvelyem összeomlott hurkokban van, és a készültségi mintavétel meghiúsul?

Győződjön meg arról, hogy a szolgáltatásnév nem járt le.  Lásd: az [AK](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) -beli szolgáltatásnév és az AK-beli [frissítési hitelesítő adatok](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="my-cluster-was-working-but-suddenly-cannot-provision-loadbalancers-mount-pvcs-etc"></a>A fürtem működik, de hirtelen nem lehet kiépíteni a LoadBalancers, a csatlakoztatási és a PVC-ket? 

Győződjön meg arról, hogy a szolgáltatásnév nem járt le.  Lásd: az [AK](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) -beli szolgáltatásnév és az AK-beli [frissítési hitelesítő adatok](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>Használhatom a virtuálisgép-méretezési csoport API-jait a manuális méretezéshez?

Nem, a virtuálisgép-méretezési csoport API-jai nem támogatják a méretezési műveleteket. Használja az AK API-kat ( `az aks scale` ).

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-0-nodes"></a>Használhatom a virtuálisgép-méretezési csoportokat a 0 csomópontra való manuális méretezéshez?

Nem, a virtuálisgép-méretezési csoport API-jai nem támogatják a méretezési műveleteket.

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>Leállíthatom vagy Lefoglalhatom az összes virtuális gépet?

Míg az AK rugalmassági mechanizmusokkal rendelkezik, amelyek egy ilyen konfigurációt és helyreállítást biztosítanak, ez nem ajánlott.

## <a name="can-i-use-custom-vm-extensions"></a>Használhatok egyéni virtuálisgép-bővítményeket?

Az AK nem felügyelt szolgáltatás, és a IaaS-erőforrások kezelése nem támogatott. Egyéni összetevők telepítése stb. használja ki a Kubernetes API-kat és mechanizmusokat. Például használja a DaemonSets a szükséges összetevők telepítéséhez.

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
[private-clusters]: ./private-clusters.md
[bcdr-bestpractices]: ./operator-best-practices-multi-region.md#plan-for-multiregion-deployment
[availability-zones]: ./availability-zones.md
[az-regions]: ../availability-zones/az-region.md
[uptime-sla]: ./uptime-sla.md

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
[csi-driver]: https://github.com/Azure/secrets-store-csi-driver-provider-azure
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines/