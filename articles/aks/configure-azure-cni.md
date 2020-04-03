---
title: Az Azure CNI-hálózat konfigurálása az Azure Kubernetes-szolgáltatásban (AKS)
description: Ismerje meg, hogyan konfigurálhatja az Azure CNI (advanced) hálózatépítést az Azure Kubernetes-szolgáltatásban (AKS), beleértve egy AKS-fürt üzembe helyezését egy meglévő virtuális hálózatba és alhálózatba.
services: container-service
ms.topic: article
ms.date: 06/03/2019
ms.openlocfilehash: 6f194cb97850fcb24e4789ac0ba39b6f03d99e6e
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617388"
---
# <a name="configure-azure-cni-networking-in-azure-kubernetes-service-aks"></a>Az Azure CNI-hálózat konfigurálása az Azure Kubernetes-szolgáltatásban (AKS)

Alapértelmezés szerint az AKS-fürtök [kubenet][kubenet],, és egy virtuális hálózat és alhálózat jön létre az Ön számára. A *kubenet*segítségével a csomópontok egy virtuális hálózati alhálózatIP-címet kapnak. A hálózati címfordítás (NAT) ezután konfigurálva van a csomópontokon, és a podok "rejtett" IP-címet kapnak a csomópont IP-címe mögött. Ez a megközelítés csökkenti az IP-címek számát, amelyeket le kell foglalnia a hálózati térben a podok számára.

Az [Azure Container Networking Interface (CNI)][cni-networking]segítségével minden pod kap egy IP-címet az alhálózatból, és közvetlenül elérhető. Ezeknek az IP-címeknek egyedinek kell lenniük a hálózati térben, és előre meg kell tervezni őket. Minden csomópont rendelkezik egy konfigurációs paramétera podok maximális általa támogatott. A csomópontonkénti IP-címek megfelelő száma ezután előre le van foglalva az adott csomópont számára. Ez a megközelítés több tervezést igényel, és gyakran az IP-cím kimerüléséhez vagy a fürtök újraépítésének szükségességéhez vezet egy nagyobb alhálózatban, ahogy az alkalmazás igényei nőnek.

Ez a cikk bemutatja, hogyan azure *CNI-hálózat* létrehozása és használata egy AKS-fürt virtuális hálózati alhálózat létrehozása és használata. A hálózati beállításokkal és a szempontokkal kapcsolatos további információkért lásd: [Hálózati fogalmak a Kubernetes és az AKS számára.][aks-network-concepts]

## <a name="prerequisites"></a>Előfeltételek

* Az AKS-fürt virtuális hálózatának engedélyeznie kell a kimenő internetkapcsolatot.
* Előfordulhat, hogy az AKS-fürtök `169.254.0.0/16`nem használnak `172.30.0.0/16`, , `172.31.0.0/16`vagy `192.0.2.0/24` a Kubernetes szolgáltatás címtartományához.
* Az AKS-fürt által használt egyszerű szolgáltatásnak legalább [hálózati közreműködői](../role-based-access-control/built-in-roles.md#network-contributor) engedélyekkel kell rendelkeznie a virtuális hálózaton belüli alhálózaton. Ha a beépített hálózati közreműködői szerepkör használata helyett [egyéni szerepkört](../role-based-access-control/custom-roles.md) szeretne definiálni, a következő engedélyek szükségesek:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`
* Egyszerű szolgáltatás helyett használhatja a rendszer hozzárendelt felügyelt identitás engedélyeket. További információ: [Felügyelt identitások használata.](use-managed-identity.md)
* Az AKS csomópontkészlethez rendelt alhálózat nem lehet [delegált alhálózat.](../virtual-network/subnet-delegation-overview.md)

## <a name="plan-ip-addressing-for-your-cluster"></a>A fürt IP-címzésének megtervezése

Az Azure CNI-hálózattal konfigurált fürtök további tervezést igényelnek. A virtuális hálózat és az alhálózat méretének figyelembe kell adnia a futtatni kívánt podok számát és a fürt csomópontjainak számát.

A podok és a fürt csomópontjaiIP-címek a virtuális hálózaton belül a megadott alhálózatból vannak hozzárendelve. Minden csomópont elsődleges IP-címmel van konfigurálva. Alapértelmezés szerint 30 további IP-címek előre konfigurált az Azure CNI, amelyek a csomóponton ütemezett podok. A fürt horizontális felskálázásakor minden csomópont hasonlóan van konfigurálva az alhálózat IP-címeivel. Megtekintheti a [csomópontonkénti maximális podokat](#maximum-pods-per-node)is.

> [!IMPORTANT]
> A szükséges IP-címek számának tartalmaznia kell a frissítési és skálázási műveletek szempontjait. Ha úgy állítja be az IP-címtartományt, hogy csak egy meghatározott számú csomópontot támogasson, nem frissítheti és nem méretezheti a fürtöt.
>
> - Az AKS-fürt **frissítésekén** egy új csomópont lesz telepítve a fürtben. A szolgáltatások és a munkaterhelések az új csomóponton kezdenek futni, és egy régebbi csomópont törlődik a fürtből. Ehhez a működés közbeni frissítési folyamathoz legalább egy további IP-címblokkra van szükség. Ekkor a csomópontszám `n + 1`.
>   - Ez a szempont különösen fontos a Windows Server csomópontkészletek használatakor (jelenleg előzetes verzióban az AKS-ben). Az AKS Windows Server-csomópontjai nem alkalmazzák automatikusan a Windows-frissítéseket, hanem frissítést hajt végre a csomópontkészleten. Ez a frissítés új csomópontokat telepít a windows Server 2019 alapcsomópontrendszerképével és biztonsági javításaival. A Windows Server csomópontkészlet frissítéséről az [AKS csomópontkészlet frissítése][nodepool-upgrade]című témakörben talál további információt.
>
> - AKS-fürt **méretezésekénél** egy új csomópont lesz telepítve a fürtben. A szolgáltatások és a munkaterhelések az új csomóponton kezdenek futni. Az IP-címtartományban figyelembe kell venni, hogyan érdemes bővíteni a csomópontok és podok a fürt támogatja. A frissítési műveletekhez egy további csomópontot is tartalmaznia kell. Ekkor a csomópontszám `n + number-of-additional-scaled-nodes-you-anticipate + 1`.

Ha azt várja, hogy a csomópontok a podok maximális számát futtassák, és rendszeresen megsemmisítsék és üzembe adják a podokat, akkor csomópontonként további IP-címeket is figyelembe kell vennie. Ezek a további IP-címek figyelembe veszik, hogy eltarthat néhány másodpercig egy szolgáltatás törlése, és az IP-cím kiadott egy új szolgáltatás telepítése és megszerezni a címet.

Az AKS-fürt IP-címterve egy virtuális hálózatból, a csomópontok és a podok legalább egy alhálózatából és egy Kubernetes-szolgáltatás címtartományából áll.

| Címtartomány / Azure-erőforrás | Korlátok és méretezés |
| --------- | ------------- |
| Virtuális hálózat | Az Azure virtuális hálózat a /8 méretű lehet, de 65 536 konfigurált IP-címre korlátozódik. |
| Alhálózat | Elég nagynak kell lennie ahhoz, hogy elférjen a csomópontok, a podok, valamint az összes Kubernetes és Azure-erőforrások, amelyek kiépíthetők a fürtben. Ha például egy belső Azure-terheléselosztót telepít, az előtér-IP-k a fürt alhálózatából vannak lefoglalva, nem nyilvános IP-kből. Az alhálózat méretének figyelembe kell vennie a frissítési műveleteket vagy a jövőbeli méretezési igényeket is.<p />A *minimális* alhálózati méret kiszámításához, beleértve egy további csomópontot a frissítési műveletekhez:`(number of nodes + 1) + ((number of nodes + 1) * maximum pods per node that you configure)`<p/>Példa 50 csomós fürtre: `(51) + (51  * 30 (default)) = 1,581` (/21 vagy nagyobb)<p/>Példa egy 50 csomópontos fürtre, amely további 10 csomópont `(61) + (61 * 30 (default)) = 1,891` méretezését is magában foglalja: (/21 vagy nagyobb)<p>Ha a fürt létrehozásakor nem adja meg csomópontonkénti podok maximális számát, a csomópontonkénti podok maximális száma *30*lesz. A szükséges IP-címek minimális száma ezen az értéken alapul. Ha a minimális IP-cím követelményeit egy másik maximális értékalapján számítja ki, olvassa el, [hogyan állíthatja be a podok csomópontonkénti maximális számát,](#configure-maximum---new-clusters) hogy ezt az értéket állítsa be a fürt telepítésekor. |
| A Kubernetes Service címtartománya | Ezt a tartományt nem használhatják a virtuális hálózaton lévő vagy ahhoz kapcsolódó hálózati elemek. A CIDR szolgáltatáscímnek /12-nél kisebbnek kell lennie. Ezt a tartományt újra felhasználhatja a különböző AKS-fürtökközött. |
| A Kubernetes DNS-szolgáltatás IP-címe | A Kubernetes szolgáltatás címtartományában lévő IP-cím, amelyet a fürtszolgáltatás felderítése (kube-dns) fog használni. Ne használja a címtartomány első IP-címét, például az .1 címet. Az alhálózati tartomány első címe a *kubernetes.default.svc.cluster.local* címhez használatos. |
| Docker-híd címe | A Docker-híd hálózati címe az összes Docker-telepítésben található alapértelmezett *docker0* híd hálózati címe. Míg *a docker0-hidat* nem használják az AKS-fürtök vagy maguk a podok, be kell állítania ezt a címet, hogy továbbra is támogassa a forgatókönyveket, például a *docker build* az AKS-fürtön belül. A Docker-híd hálózati címéhez cidr-t kell választani, mert ellenkező esetben a Docker automatikusan kiválaszt egy alhálózatot, amely ütközhet más CIDR-ekkel. Olyan címteret kell választania, amely nem ütközik a hálózatok többi CIDR-jével, beleértve a fürt cidr-szolgáltatását és a pod CIDR-t. 172.17.0.1/16 alapértelmezett érték. Ezt a tartományt újra felhasználhatja a különböző AKS-fürtökközött. |

## <a name="maximum-pods-per-node"></a>Maximális podok csomópontonként

Az AKS-fürtcsomópontonkénti podok maximális száma 250. A podok csomópontonkénti *alapértelmezett* maximális száma *a kubenet* és az *Azure CNI-hálózat,* valamint a fürt üzembe helyezésének módja között változik.

| Központi telepítési módszer | Kubenet alapértelmezett | Az Azure CNI alapértelmezett | Üzembe helyezéskor konfigurálható |
| -- | :--: | :--: | -- |
| Azure CLI | 110 | 30 | Igen (250-ig) |
| Resource Manager-sablon | 110 | 30 | Igen (250-ig) |
| Portál | 110 | 30 | Nem |

### <a name="configure-maximum---new-clusters"></a>Maximális - új fürtök konfigurálása

A podok csomópontonkénti maximális számát *csak fürttelepítési időben*állíthatja be. Ha az Azure CLI-vel vagy egy Resource Manager-sablonnal telepíti a központi telepítést, beállíthatja a csomópontonkénti maximális podok értékét 250-ig.

A fürt állapota szempontjából kritikus rendszerpodok számára a maximális podok csomópontonkénti minimális értéke kivan kényszerítve, hogy biztosítsa a helyet a rendszerpodok számára. A minimális érték, amely beállítható a maximális podok csomópontonként 10, ha és csak akkor, ha a konfiguráció minden csomópont készlet legalább 30 podok. Ha például a csomópontonkénti maximális podokat legalább 10-re állítja be, minden egyes csomópontkészletnek legalább 3 csomót kell rendelkeznie. Ez a követelmény minden új csomópontkészlethez is vonatkozik, így ha 10 csomópontonként maximális podként van meghatározva, minden további csomópontkészlethez hozzá kell adni legalább 3 csomót.

| Hálózat | Minimális | Maximum |
| -- | :--: | :--: |
| Azure CNI | 10 | 250 |
| Kubenet között | 10 | 110 |

> [!NOTE]
> A fenti táblázatban szereplő minimális értéket az AKS szolgáltatás szigorúan betartatja. Nem lehet beállítani a maxPods érték alacsonyabb, mint a minimális látható, mivel ezzel megakadályozhatja a fürt indítását.

* **Azure CLI:** `--max-pods` Adja meg az argumentumot, amikor az [aks create][az-aks-create] paranccsal telepít egy fürtöt. A maximális érték 250.
* **Erőforrás-kezelő sablon : Erőforrás-kezelő sablonnal**rendelkező fürt telepítésekor adja meg a `maxPods` Property tulajdonságot a [ManagedClusterAgentProfile] objektumban. A maximális érték 250.
* **Azure Portal:** Nem módosíthatja a podok csomópontonkénti maximális számát, amikor fürtöt telepít az Azure Portalon. Az Azure CNI hálózati fürtök csomópontonként legfeljebb 30 podok, ha az Azure Portalhasználatával telepíti.

### <a name="configure-maximum---existing-clusters"></a>Maximális - meglévő fürtök konfigurálása

Egy meglévő AKS-fürt csomópontonkénti maximális podok módosítása nem módosítható. A számot csak a fürt kezdeti üzembe helyezésekor módosíthatja.

## <a name="deployment-parameters"></a>Telepítési paraméterek

AKS-fürt létrehozásakor a következő paraméterek konfigurálhatók az Azure CNI-hálózathoz:

**Virtuális hálózat**: Az a virtuális hálózat, amelybe a Kubernetes-fürtöt telepíteni szeretné. Ha új virtuális hálózatot szeretne létrehozni a fürthöz, válassza az *Új létrehozása lehetőséget,* és kövesse a Virtuális hálózat létrehozása szakasz *lépéseit.* Az Azure virtuális hálózatra vonatkozó korlátozásokról és kvótákról az [Azure-előfizetési és szolgáltatáskorlátok, kvóták és korlátozások](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits)című témakörben talál.

**Alhálózat**: A virtuális hálózaton belüli alhálózat, amelyen a fürtöt telepíteni szeretné. Ha új alhálózatot szeretne létrehozni a fürt virtuális hálózatában, válassza az *Új létrehozása lehetőséget,* és kövesse az Alhálózat létrehozása szakasz *lépéseit.* Hibrid kapcsolat esetén a címtartomány nem lehet átfedésben a környezet más virtuális hálózataival.

**Kubernetes szolgáltatás címtartománya:** Ez a virtuális IP-cím, amelyet a Kubernetes a fürt belső [szolgáltatásaihoz][services] rendel hozzá. Bármilyen privát címtartományt használhat, amely megfelel az alábbi követelményeknek:

* Nem lehet a fürt virtuális hálózati IP-címtartományán belül
* Nem lehet átfedés más olyan virtuális hálózatokkal, amelyekkel a fürt virtuális hálózata
* Nem lehet átfedésben a helyszíni IP-kkel
* Nem lehet a tartományokon `172.30.0.0/16` `172.31.0.0/16` `169.254.0.0/16`belül, , , vagy`192.0.2.0/24`

Bár technikailag lehetséges a fürttel azonos virtuális hálózaton belüli szolgáltatáscímtartomány megadása, ez nem ajánlott. Kiszámíthatatlan viselkedés következhet be, ha egymást átfedő IP-tartományokat használ. További információt a cikk [GYIK](#frequently-asked-questions) részében talál. A Kubernetes-szolgáltatásokkal kapcsolatos további információkért lásd: [Szolgáltatások][services] a Kubernetes dokumentációjában.

**Kubernetes DNS-szolgáltatás IP-címe**: A fürt DNS-szolgáltatásának IP-címe. A címnek a *Kubernetes szolgáltatási címtartományába* kell tartoznia. Ne használja a címtartomány első IP-címét, például az .1 címet. Az alhálózati tartomány első címe a *kubernetes.default.svc.cluster.local* címhez használatos.

**Docker-híd címe**: A Docker-híd hálózati címe az összes Docker-telepítésben található alapértelmezett *docker0* hídhálózati címet jelöli. Míg *a docker0-hidat* nem használják az AKS-fürtök vagy maguk a podok, be kell állítania ezt a címet, hogy továbbra is támogassa a forgatókönyveket, például a *docker build* az AKS-fürtön belül. A Docker-híd hálózati címéhez cidr-t kell választani, mert ellenkező esetben a Docker automatikusan kiválaszt egy alhálózatot, amely ütközhet más CIDR-ekkel. Olyan címteret kell választania, amely nem ütközik a hálózatok többi CIDR-jével, beleértve a fürt cidr-szolgáltatását és a pod CIDR-t.

## <a name="configure-networking---cli"></a>Hálózatkonfigurálás - CLI

Amikor létrehoz egy AKS-fürtöt az Azure CLI-vel, konfigurálhatja az Azure CNI-hálózatot is. A következő parancsokkal hozzon létre egy új AKS-fürtöt, amelyen engedélyezve van az Azure CNI-hálózatkezelés.

Először vegye le annak a meglévő alhálózatnak az alhálózati erőforrásazonosítóját, amelybe az AKS-fürt csatlakozik:

```azurecli-interactive
$ az network vnet subnet list \
    --resource-group myVnet \
    --vnet-name myVnet \
    --query "[0].id" --output tsv

/subscriptions/<guid>/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Használja az [aks create][az-aks-create] `--network-plugin azure` parancsot az argumentummal, hogy hozzon létre egy fürtet fejlett hálózatkezeléssel. Frissítse `--vnet-subnet-id` az értéket az előző lépésben gyűjtött alhálózati azonosítóval:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --generate-ssh-keys
```

## <a name="configure-networking---portal"></a>Hálózatkonfigurálás - portál

Az alábbi képernyőkép az Azure Portalon egy példát mutat be a beállítások konfigurálása során AKS-fürt létrehozása során:

![Speciális hálózati konfiguráció az Azure Portalon][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>Gyakori kérdések

A következő kérdések és válaszok az **Azure CNI** hálózati konfigurációra vonatkoznak.

* *Telepíthetek virtuális gépeket a fürt alhálózatában?*

  Nem. A Kubernetes-fürt által használt alhálózatban nem támogatott a virtuális gépek üzembe helyezése. Virtuális gépek lehet telepíteni ugyanabban a virtuális hálózatban, de egy másik alhálózatban.

* *Konfigurálhatom a podonkénti hálózati házirendeket?*

  Igen, a Kubernetes hálózati házirendje elérhető az AKS-ben. Első lépések: [Biztonságos forgalom a podok között a hálózati házirendek használatával az AKS-ben.][network-policy]

* *Konfigurálható a podok maximális száma egy csomópontra?*

  Igen, ha üzembe helyez egy fürtöt az Azure CLI vagy egy Resource Manager sablonhasználatával. Lásd: [Podok maximális száma csomópontonként](#maximum-pods-per-node).

  Egy meglévő fürt csomópontonkénti podok maximális száma nem módosítható.

* *Hogyan konfigurálhatok további tulajdonságokat az AKS-fürt létrehozása során létrehozott alhálózathoz? Például a szolgáltatás végpontjai.*

  Az AKS-fürt létrehozása során létrehozott virtuális hálózat és alhálózatok tulajdonságainak teljes listája konfigurálható az Azure Portal szabványos virtuális hálózati konfigurációs lapján.

* *Használhatok másik alhálózatot a fürt virtuális hálózatán belül a* **Kubernetes szolgáltatás címtartományához?**

  Nem ajánlott, de ez a konfiguráció lehetséges. A szolgáltatás címtartománya a virtuális IP-k (VIP) készlete, amelyet a Kubernetes a fürt belső szolgáltatásaihoz rendel hozzá. Az Azure Networking nem rendelkezik a Kubernetes-fürt szolgáltatás IP-tartományának láthatóságával. A fürt szolgáltatáscímtartományának láthatóságának hiánya miatt később létrehozhat egy új alhálózatot a fürt virtuális hálózatában, amely átfedésben van a szolgáltatás címtartományával. Ha ilyen átfedés következik be, a Kubernetes olyan IP-címet rendelhet hozzá, amelyet már használ az alhálózat egy másik erőforrása, ami előre nem látható viselkedést vagy hibákat okoz. Ha biztosítja, hogy a fürt virtuális hálózatán kívüli címtartományt használjon, elkerülheti ezt az átfedési kockázatot.

## <a name="next-steps"></a>További lépések

Az AKS-ben a hálózatépítésről az alábbi cikkekben olvashat bővebben:

- [Statikus IP-cím használata az Azure Kubernetes-szolgáltatás (AKS) terheléselosztójával](static-ip.md)
- [Belső terheléselosztó használata az Azure Container Service (AKS) szolgáltatással](internal-lb.md)

- [Egyszerű be- és átszivárgási vezérlő létrehozása külső hálózati kapcsolattal][aks-ingress-basic]
- [A HTTP-alkalmazásútválasztási bővítmény engedélyezése][aks-http-app-routing]
- [Belső, magánhálózati és IP-címet használó be- és áthálózaton belüli adatélmény-vezérlő létrehozása][aks-ingress-internal]
- [Dinamikus nyilvános IP-című bejövő forgalom vezérlő létrehozása és a TLS-tanúsítványok automatikus létrehozásához szükséges Titkosítás konfigurálása][aks-ingress-tls]
- [Bejövő forgalom vezérlő létrehozása statikus nyilvános IP-címvel, és konfigurálása A titkosítás konfigurálása a TLS-tanúsítványok automatikus létrehozásához][aks-ingress-static-tls]

### <a name="aks-engine"></a>AKS motor

[Az Azure Kubernetes Service Engine (AKS Engine)][aks-engine] egy nyílt forráskódú projekt, amely Azure Resource Manager-sablonokat hoz létre a Kubernetes-fürtök Azure-beli üzembe helyezéséhez.

Az AKS Engine-motorral létrehozott Kubernetes-fürtök támogatják a [kubenet][kubenet] és az [Azure CNI][cni-networking] beépülő modulokat. Így mindkét hálózati forgatókönyvet az AKS Engine támogatja.

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-ssh]: ssh.md
[ManagedClusterAgentClusterProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
[aks-network-concepts]: concepts-network.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-internal]: ingress-internal-ip.md
[network-policy]: use-network-policies.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[network-comparisons]: concepts-network.md#compare-network-models
