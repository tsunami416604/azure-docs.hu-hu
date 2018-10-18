---
title: Speciális hálózatkezelés konfigurálva az Azure Kubernetes Service (AKS)
description: Ismerje meg, a speciális hálózati konfigurálása az Azure Kubernetes Service (AKS), többek között üzembe helyezés az AKS-fürt be egy meglévő virtuális hálózatot és alhálózatot.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/11/2018
ms.author: iainfou
ms.openlocfilehash: 87c3ab9624116e9c1c61041531fdf5d3b26117e1
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49381023"
---
# <a name="configure-advanced-networking-in-azure-kubernetes-service-aks"></a>Speciális hálózatkezelés konfigurálása az Azure Kubernetes Service (AKS)

Alapértelmezés szerint az AKS-fürtök használata *alapszintű* hálózatkezelés, amely létrehozza és konfigurálja a fürt egy virtuális hálózatot és alhálózatot használja. A hálózati beállítások, például az IP-címtartományokkal, további ellenőrzésére használhatja *speciális* hálózati. Speciális hálózatkezelés akkor is létrehozhat egy AKS-fürtöt egy meglévő virtuális hálózatot és alhálózatot. A meglévő virtuális hálózat gyakran biztosítja a kapcsolatot a helyszíni hálózathoz az Azure ExpressRoute- vagy Site-to-Site VPN használatával.

Ez a cikk bemutatja, hogyan használhatja a speciális hálózati hozhat létre, és a egy virtuális hálózat használata AKS-fürt. A hálózat további általános információkért lásd: [fogalmak hálózati Kubernetes, az AKS][aks-network-concepts].

## <a name="prerequisites"></a>Előfeltételek

* Az AKS-fürtöt a virtuális hálózat engedélyeznie kell a kimenő internetkapcsolat.
* Ne hozzon létre több mint egy AKS-fürt ugyanazon az alhálózaton.
* AKS-fürt nem használhatja `169.254.0.0/16`, `172.30.0.0/16`, vagy `172.31.0.0/16` a Kubernetes szolgáltatás címtartományt.
* Az AKS-fürt által használt egyszerű szolgáltatás rendelkeznie kell legalább [hálózati közreműködő](../role-based-access-control/built-in-roles.md#network-contributor) az alhálózaton belül a virtuális hálózat engedélyeit. Ha meg szeretné egy [egyéni szerepkör](../role-based-access-control/custom-roles.md) helyett használja a hálózati közreműködő szerepkört, a következő engedélyek szükségesek:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

## <a name="plan-ip-addressing-for-your-cluster"></a>A fürt IP-címzés tervezése

A speciális hálózati szolgáltatással konfigurált fürtöknél szükség további tervezésre. A virtuális hálózat és az alhálózat méretét is futtatni tervezett podjainak számát, valamint a fürt csomópontszámának kell elhelyezni.

IP-címek a podok és a fürtcsomópontok vannak rendelve az adott alhálózatról a virtuális hálózaton belül. Minden egyes csomópont egy elsődleges IP-címmel van konfigurálva, ez az IP-címét a csomópont- és 30 további IP-címek Azure CNI által előre konfiguráltan vannak rendelve a csomóponthoz ütemezett podok. Horizontális felskálázás a fürthöz, minden egyes csomópont hasonló módon van konfigurálva az alhálózat IP-címeket.

Az IP-cím terv az AKS-fürt tartalmaz egy virtuális hálózat, legalább egy alhálózatot a csomópontok és a podok és a egy Kubernetes-szolgáltatás-címtartományt.

| Címtartomány / Azure erőforrás | Korlátok és méretezés |
| --------- | ------------- |
| Virtuális hálózat | Az Azure virtuális hálózat /8 méretűek is lehetnek, de legfeljebb 65 536 IP-címeit. |
| Alhálózat | Elég nagy a csomópontok, a podok és a Kubernetes és az Azure-erőforrások, előfordulhat, hogy építhető ki a fürtben kell lennie. Például ha telepít egy belső Azure Load Balancert, az előtérbeli IP-címek vannak lefoglalva a fürt alhálózatról, nem a nyilvános IP-címek. <p/>Kiszámításához *minimális* alhálózat mérete: `(number of nodes) + (number of nodes * pods per node)` <p/>Példa egy 50 csomópontot tartalmazó fürtben: `(50) + (50 * 30) = 1,550` (/ 21, vagy nagyobb) |
| Kubernetes szolgáltatás címtartomány | Ezt a tartományt nem szabad a bármely hálózati elem által használt vagy a virtuális hálózathoz csatlakozik. Szolgáltatás címének CIDR /12 kisebbnek kell lennie. |
| Kubernetes DNS szolgáltatás IP-címe | IP-címnek a Kubernetes-címtartományt, amely a fürtszolgáltatás felderítése (kube-dns) által használt szolgáltatás. |
| Docker híd címe | IP-címet (a CIDR-jelölés) használja a Docker-hidat, IP-cím csomópontokon. Alapértelmezés szerint 172.17.0.1/16. |

## <a name="maximum-pods-per-node"></a>Csomópontonkénti maximális podok

Podok száma csomópontonként az AKS-fürt alapértelmezett maximális száma alapszintű és speciális hálózatkezelés és a fürt telepítése közé esik.

| Az üzembe helyezési módszer | Alapszintű | Extra szintű | Üzembe helyezés konfigurálhatók |
| -- | :--: | :--: | -- |
| Azure CLI | 110 | 30 | Igen (legfeljebb 110) |
| Resource Manager-sablon | 110 | 30 | Igen (legfeljebb 110) |
| Portál | 110 | 30 | Nem |

### <a name="configure-maximum---new-clusters"></a>Maximum – új fürtök konfigurálása

Podok csomópontonkénti maximális számának konfigurálásához tudja *csak fürt üzembe helyezéskor*. Ha az Azure CLI-vel vagy a Resource Manager-sablonnal helyezi üzembe, beállíthatja a maximális podok száma csomópont értéke magas, mint a 110-es.

* **Az Azure CLI**: Adja meg a `--max-pods` argumentum, a fürt telepítésekor a [az aks létrehozása] [ az-aks-create] parancsot. A maximális értéke 110-es.
* **Resource Manager-sablon**: Adja meg a `maxPods` tulajdonságot a [ManagedClusterAgentPoolProfile] objektum a fürt Resource Manager-sablonnal üzembe. A maximális értéke 110-es.
* **Az Azure portal**: podok csomópontonkénti maximális száma nem módosítható, ha telepít egy fürtöt az Azure portal használatával. A speciális hálózati fürtök korlátozva, 30 podok száma csomópontonként, ha telepít, az Azure portal használatával.

### <a name="configure-maximum---existing-clusters"></a>Maximum – meglévő fürtök konfigurálása

A meglévő AKS-fürt a csomópontonkénti maximális podok nem módosítható. Csak akkor, ha a fürt első üzembe helyezésekor módosíthatja is a számot.

## <a name="deployment-parameters"></a>Üzembe helyezési paraméterek

AKS-fürt létrehozásakor konfigurálható speciális hálózati a következő paraméterekkel:

**Virtuális hálózat**: A virtuális hálózatot, amelybe át szeretné a Kubernetes-fürt üzembe helyezéséhez. Ha azt szeretné, a fürt számára egy új virtuális hálózat létrehozásához, válassza ki a *új létrehozása* kövesse a lépéseket a *virtuális hálózat létrehozása* szakaszban. További információ a korlátok és kvóták az Azure-beli virtuális hálózathoz: [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

**Alhálózat**: az alhálózaton, ahol szeretné a fürt üzembe helyezése virtuális hálózaton belül. Ha azt szeretné, hozzon létre egy új alhálózatot a virtuális hálózat a fürt számára, válassza *új létrehozása* kövesse a lépéseket a *alhálózat létrehozásához* szakaszban.

**Kubernetes szolgáltatás címtartomány**: Ez a Kubernetes rendel a virtuális IP-címek készletét [szolgáltatások] [ services] a fürtben. Használhatja bármely magánhálózati címtartomány, amely eleget tesz a következő követelményeknek:

* Nem lehet a fürt a virtuális hálózati IP-címtartomány
* Nem lehet átfedésben, amellyel a fürt virtuális hálózaton is társul más virtuális hálózatokhoz
* Nem lehet átfedésben bármilyen helyszíni IP-címek
* Nem lehet a tartományokon belül `169.254.0.0/16`, `172.30.0.0/16`, vagy `172.31.0.0/16`

Bár technikailag lehetséges, adja meg a fürt egy szolgáltatás-címtartományt az adott virtuális hálózaton belül, ez nem ajánlott. Előre nem látható viselkedéshez vezethet, átfedő IP-címtartományok használatakor. További információkért lásd: a [– gyakori kérdések](#frequently-asked-questions) című szakaszát. Kubernetes-szolgáltatásokra vonatkozó további információkért lásd: [szolgáltatások] [ services] a Kubernetes dokumentációjában.

**Kubernetes DNS szolgáltatás IP-cím**: a DNS szolgáltatás a fürt IP-cím. Ez a cím belül kell lennie a *Kubernetes szolgáltatás címtartomány*.

**Docker-híd cím**: IP-cím és a Docker-híd hozzárendelése hálózati maszkot. Az IP-címet nem lehet a virtuális hálózati IP-címtartomány a fürt belül.

## <a name="configure-networking---cli"></a>Hálózatkezelés – parancssori felület beállítása

Amikor egy AKS-fürtöt az Azure CLI-vel hoz létre, fejlett hálózatkezelési is konfigurálhatja. A következő parancsokat használja egy új AKS-fürt létrehozása engedélyezve van a speciális hálózati szolgáltatásokkal.

Először kérje le a meglévő alhálózat, amelybe tartományhoz fog csatlakozni az AKS-fürtöt az alhálózat erőforrás-azonosító:

```console
$ az network vnet subnet list --resource-group myVnet --vnet-name myVnet --query [].id --output tsv

/subscriptions/d5b9d4b7-6fc1-46c5-bafe-38effaed19b2/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Használja a [az aks létrehozása] [ az-aks-create] parancsot a `--network-plugin azure` fürt létrehozása a speciális hálózati argumentum. Frissítés a `--vnet-subnet-id` az alhálózati azonosító értékét az előző lépésben gyűjtött:

```azurecli
az aks create --resource-group myAKSCluster --name myAKSCluster --network-plugin azure --vnet-subnet-id <subnet-id> --docker-bridge-address 172.17.0.1/16 --dns-service-ip 10.2.0.10 --service-cidr 10.2.0.0/24
```

## <a name="configure-networking---portal"></a>Konfigurálja a hálózati használata – portál

Az Azure Portalról az alábbi képernyőképen látható egy példa az AKS-fürt létrehozása során ezek a beállítások:

![Speciális hálózati konfiguráció az Azure Portalon][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>Gyakori kérdések

A alkalmazni az alábbi kérdések és válaszok a **speciális** hálózati konfiguráció.

* *Telepíthetek virtuális gépeket saját fürt alhálózat?*

  Nem. Virtuális gépek üzembe helyezéséhez a Kubernetes-fürt által használt alhálózaton nem támogatott. Virtuális gépek ugyanazon a virtuális hálózaton, de egy másik alhálózatot is üzembe helyezhetők.

* *Konfigurálhatja a-pod hálózati házirendeket?*

  Nem. Pod hálózati házirendek olyan jelenleg nem támogatott.

* *Az üzembe helyezhető, amely konfigurálható a csomópontra a podok maximális számát?*

  Igen, a fürt az Azure CLI vagy a Resource Manager-sablonnal üzembe. Lásd: [csomópontonkénti maximális podok](#maximum-pods-per-node).

  Podok egy meglévő fürt csomópontonkénti maximális száma nem módosítható.

* *Hogyan konfigurálhatom az alhálózatot, amelyet az AKS-fürt létrehozása során létrehozott további tulajdonságok? Ha például a Szolgáltatásvégpontok.*

  A virtuális hálózatot és alhálózatot hoz létre az AKS-fürt létrehozása során, tulajdonságainak teljes listáját a normál virtuális hálózat konfigurációs lapja az Azure Portalon konfigurálhatók.

* *Használhatok egy másik alhálózatot a saját fürt virtuális hálózaton belül a* **Kubernetes szolgáltatás címtartomány**?

  Nem ajánlott, de ez a konfiguráció lehetséges. A szolgáltatás-címtartomány egy virtuális IP-címek (VIP), amelyet a szolgáltatások a fürtben Kubernetes rendel hozzá. Az Azure-hálózatok nem a szolgáltatás IP-címtartomány a Kubernetes-fürt láthassa. A fürt service címtartomány betekintést hiánya miatt is lehet később hozzon létre egy új alhálózatot a fürt virtuális hálózatban, amely átfedésben van a szolgáltatás-címtartományt. Ilyen átfedés esetén Kubernetes hozzárendelhet egy szolgáltatás IP-címet már használja egy másik erőforráshoz az alhálózatban, előre nem látható viselkedéshez vagy hibákat okoz. Úgy, hogy a fürt virtuális hálózaton kívül címtartományt használja, a átfedés kockázat elkerülése érdekében.

## <a name="next-steps"></a>További lépések

### <a name="networking-in-aks"></a>Hálózatkezelés az aks-ben

További információ a hálózatkezelés az aks-ben a következő cikkeket:

- [Az Azure Kubernetes Service (AKS) terheléselosztót statikus IP-cím használata](static-ip.md)
- [Belső terheléselosztó az Azure Container Service (AKS) használatához](internal-lb.md)

- [Hozzon létre egy alapszintű bejövőforgalom-vezérlőjéhez külső hálózatok közötti kapcsolatokkal][aks-ingress-basic]
- [A HTTP-kérelem útválasztási bővítmény engedélyezése][aks-http-app-routing]
- [Hozzon létre egy bejövőforgalom-vezérlőt, amely egy belső, saját hálózat és IP-cím][aks-ingress-internal]
- [Hozzon létre egy bejövőforgalom-vezérlőjéhez dinamikus nyilvános IP-cím, és nézzük titkosítása automatikusan létrehozni a TLS-tanúsítványok konfigurálása][aks-ingress-tls]
- [Hozzon létre egy bejövőforgalom-vezérlőjéhez statikus nyilvános IP-cím, és nézzük titkosítása automatikusan létrehozni a TLS-tanúsítványok konfigurálása][aks-ingress-static-tls]

### <a name="acs-engine"></a>ACS Engine

[Az Azure Container Service-motor (ACS Engine)] [ acs-engine] egy nyílt forráskódú projekt, amely létrehozza az Azure Resource Manager-sablonok üzembe helyezése a Docker-kompatibilis fürtök az Azure-on is használhat. Kubernetes, DC/OS, Swarm módú és Swarm vezénylők telepíthetők az ACS-motor.

ACS Engine használatával létrehozott Kubernetes-fürtök támogatják mind a [kubenet] [ kubenet] és [Azure CNI] [ cni-networking] beépülő modulokat. ACS Engine, alapvető és speciális hálózati forgatókönyveket támogatja.

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[acs-engine]: https://github.com/Azure/acs-engine
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-ssh]: ssh.md
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
[aks-network-concepts]: concepts-network.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-internal]: ingress-internal-ip.md
