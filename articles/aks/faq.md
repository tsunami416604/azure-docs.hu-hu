---
title: Gyakran ismételt kérdések az Azure Kubernetes szolgáltatásról (ak)
description: Válaszok az Azure Kubernetes szolgáltatással (ak) kapcsolatos gyakori kérdésekre.
services: container-service
author: mlearned
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/08/2019
ms.author: mlearned
ms.openlocfilehash: 554eba87efc56e2dadb3fb2d0cb78cd8b7ea7237
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302728"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Az Azure Kubernetes Service (ak) szolgáltatással kapcsolatos gyakori kérdések

Ez a cikk az Azure Kubernetes Service (ak) szolgáltatással kapcsolatos gyakori kérdéseket tárgyalja.

## <a name="which-azure-regions-currently-provide-aks"></a>Mely Azure-régiók jelenleg biztosítanak AK-t?

Az elérhető régiók teljes listájáért lásd: [AK-régiók és rendelkezésre állás][aks-regions].

## <a name="does-aks-support-node-autoscaling"></a>Az AK támogatja a csomópontok automatikus skálázását?

Igen, az ügynökök csomópontjainak horizontálisan történő automatikus skálázása jelenleg előzetes verzióban érhető el. Lásd: for instructions. AKS autoscaling is based on the [Kubernetes autoscaler][auto-scaler] [a fürt automatikus méretezése az alkalmazási igények kielégítéséhez az AK-ban][aks-cluster-autoscaler] .

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Telepíthetek AK-t a meglévő virtuális hálózatba?

Igen, a [speciális hálózatkezelés funkció][aks-advanced-networking]használatával üzembe HELYEZHET egy AK-fürtöt egy meglévő virtuális hálózatban.

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Korlátozható, hogy ki férhet hozzá a Kubernetes API-kiszolgálóhoz?

Igen, korlátozhatja a Kubernetes API-kiszolgálóhoz való hozzáférést az [API-kiszolgáló által engedélyezett IP-címtartományok][api-server-authorized-ip-ranges]használatával, amely jelenleg előzetes verzióban érhető el.

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>Elérhetővé tehetem a Kubernetes API-kiszolgálót csak a saját virtuális hálózaton belül?

Jelenleg nem, de ez tervezett. Nyomon követheti a folyamat előrehaladását az [AK GitHub][private-clusters-github-issue]-tárházon.

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Használhatok különböző virtuálisgép-méreteket egyetlen fürtben?

Igen, különböző virtuálisgép-méreteket használhat az AK-fürtben több, jelenleg előzetes verzióban elérhető [Node-készlet][multi-node-pools]létrehozásával.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Az AK-ügynökök csomópontjain vannak biztonsági frissítések alkalmazva?

Az Azure automatikusan a fürt Linux-csomópontjaira alkalmazza a biztonsági javításokat. Azonban Ön felelős annak biztosításáért, hogy a Linux-csomópontok szükség szerint újraindulnak. Több lehetőség is van a csomópontok újraindítására:

- Manuálisan, a Azure Portalon vagy az Azure CLI-n keresztül.
- Az AK-fürt frissítésével. A fürt a [Cordon és a Drain csomópontokon][cordon-drain] automatically and then bring a new node online with the latest Ubuntu image and a new patch version or a minor Kubernetes version. For more information, see [Upgrade an AKS cluster][aks-upgrade]frissíti.
- A [Kured](https://github.com/weaveworks/kured)egy nyílt forráskódú újraindítási démont használ a Kubernetes. A Kured [daemonset elemet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) fut, és figyeli az egyes csomópontokat egy olyan fájl jelenléte esetén, amely azt jelzi, hogy újraindítás szükséges. A fürtben az operációs rendszer újraindítását a fürt frissítésével megegyező [Kordon-és kiürítési folyamat][cordon-drain] kezeli.

További információ a kured használatáról: [biztonsági és kernel-frissítések alkalmazása a csomópontokra az AK-ban][node-updates-kured].

### <a name="windows-server-nodes"></a>Windows Server-csomópontok

A Windows Server-csomópontok (jelenleg előzetes verzióban az AK-ban) Windows Update nem futtatja automatikusan, és alkalmazza a legújabb frissítéseket. A Windows Update kiadási ciklus és a saját ellenőrzési folyamata körüli rendszeres ütemterv esetén frissíteni kell a Windows Server Node-készlet (ek) et az AK-fürtben. Ez a frissítési folyamat olyan csomópontokat hoz létre, amelyek a legújabb Windows Server-lemezképet és-javításokat futtatják, majd eltávolítja a régebbi csomópontokat. További információ erről a folyamatról: [Node-készlet frissítése az AK-ban][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Miért jött létre két erőforráscsoport az AK-val?

Minden AK-beli üzemelő példány két erőforráscsoportot ölel fel:

1. Hozza létre az első erőforráscsoportot. Ez a csoport csak a Kubernetes szolgáltatás erőforrását tartalmazza. Az AK erőforrás-szolgáltatója automatikusan létrehozza a második erőforráscsoportot az üzembe helyezés során. A második erőforráscsoport példája a *MC_myResourceGroup_myAKSCluster_eastus*. A második erőforráscsoport nevének megadásával kapcsolatos információkért tekintse meg a következő szakaszt.
1. A második erőforráscsoport, azaz a csomópont- *erőforráscsoport*a fürthöz társított összes infrastruktúra-erőforrást tartalmazza. Ezek az erőforrások magukban foglalják a Kubernetes csomópontos virtuális gépeket, a virtuális hálózatkezelést és a tárterületet. Alapértelmezés szerint a csomópont-erőforráscsoport neve például *MC_myResourceGroup_myAKSCluster_eastus*. Az AK automatikusan törli a csomópont-erőforrást, amikor a fürt törlődik, ezért csak olyan erőforrásokhoz használható, amelyek osztoznak a fürt életciklusán.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Megadhatom a saját nevet az AK-csomópont erőforráscsoporthoz?

Igen. Alapértelmezés szerint az AK a csomópont-erőforráscsoport *MC_clustername_resourcegroupname_location*nevezi el, de a saját nevét is megadhatja.

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

Az AK a következő [belépésvezérlés][admission-controllers]-vezérlőket támogatja:

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*
- *DenyEscalatingExec*
- *AlwaysPullImages*

Jelenleg nem módosítható a belépésvezérlés az AK-ban.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Integrálva van Azure Key Vault AK-val?

Az AK jelenleg nincs natív módon integrálva a Azure Key Vaultba. A [Kubernetes project Azure Key Vault FlexVolume][keyvault-flexvolume] azonban lehetővé teszi a Kubernetes-hüvelyek közvetlen integrációját Key Vault titkokat.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Futtathatok Windows Server-tárolókat az AK-ban?

Igen, a Windows Server-tárolók előzetes verzióban érhetők el. A Windows Server-tárolók az AK-ban való futtatásához létre kell hoznia egy Windows Servert futtató csomópont-készletet a vendég operációs rendszerként. A Windows Server-tárolók csak a Windows Server 2019-es kiszolgálókat használhatják. Első lépésként tekintse meg a következőt: [AK-fürt létrehozása Windows Server Node][aks-windows-cli]-készlettel.

A Windows Server-kiszolgáló támogatja a csomópont-készletet, és a Kubernetes-projektben a felsőbb rétegbeli Windows-kiszolgáló részét képező korlátozásokat tartalmaz. További információ ezekről a korlátozásokról: a [Windows Server-tárolók AK-][aks-windows-limitations]beli korlátozásai.

## <a name="does-aks-offer-a-service-level-agreement"></a>Biztosít-e az AK szolgáltatás szintű szerződést?

Egy szolgáltatói szerződés (SLA) esetében a szolgáltató beleegyezik, hogy a szolgáltatás költségeiért visszatéríti az ügyfelet, ha a közzétett szolgáltatási szint nem teljesül. Mivel az AK ingyenes, díjmentesen vehetik igénybe a költségeket, így az AK nem rendelkezik formális SLA-val. Azonban az AK a Kubernetes API-kiszolgáló legalább 99,5%-os rendelkezésre állását igyekszik fenntartani.

## <a name="why-cant-i-set-maxpods-below-30"></a>Miért nem tudom beállítani a maxPods 30 alatt?

Az AK-ban megadhatja `maxPods` az értéket, amikor létrehozza a fürtöt az Azure CLI és Azure Resource Manager sablonok használatával. A Kubenet és az Azure CNI azonban *minimális értéket* igényel (a létrehozáskor érvényesítve):

| Hálózat | Minimális | Maximum |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubenet | 30 | 110 |

Mivel az AK felügyelt szolgáltatás, a bővítmények és a hüvelyek üzembe helyezése és kezelése a fürt részeként történik. A múltban a felhasználók meghatározhatnak egy `maxPods` értéket, ami alacsonyabb a felügyelt hüvely futtatásához szükséges értéknél (például 30). Az AK most kiszámítja a hüvelyek minimális számát a következő képlet használatával: ((maxPods vagy (maxPods * vm_count)) > felügyelt kiegészítő hüvelyek minimuma.

A felhasználók nem írhatják `maxPods` felül a minimális érvényesítést.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Alkalmazhatom az Azure-beli foglalási kedvezményeket az AK-ügynökök csomópontjaira?

Az AK-ügynökök csomópontjai standard Azure-beli virtuális gépekként vannak kiszámlázva, így ha az AK-ban használt virtuálisgép-mérethez vásárolt [Azure][reservation-discounts] -foglalást, a rendszer automatikusan alkalmazza ezeket a kedvezményeket.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Áthelyezhetem/Áttelepíthetem a fürtöt az Azure-bérlők között?

A `az aks update-credentials` parancs használatával egy AK-fürt helyezhető át az Azure-bérlők között. Kövesse a következő témakör utasításait: [frissítés vagy létrehozás egyszerű szolgáltatásnév](https://docs.microsoft.com/azure/aks/update-credentials) , majd az [AK-fürt frissítése új hitelesítő adatokkal](https://docs.microsoft.com/azure/aks/update-credentials#update-aks-cluster-with-new-credentials).

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>Áthelyezhetem/Áttelepíthetem a fürtöt az előfizetések között?

A fürtök előfizetések közötti áthelyezése jelenleg nem támogatott.

<!-- LINKS - internal -->

[aks-regions]: ./quotas-skus-regions.md#region-availability
[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[virtual-kubelet]: virtual-kubelet.md
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
[reservation-discounts]: ../billing/billing-save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
