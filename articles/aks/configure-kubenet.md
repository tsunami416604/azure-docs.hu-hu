---
title: Kubenet-hálózat konfigurálása az Azure Kubernetes szolgáltatásban (AKS)
description: Ismerje meg, hogyan konfigurálhatja a kubenet (alapszintű) hálózatot az Azure Kubernetes-szolgáltatásban (AKS) egy AKS-fürt meglévő virtuális hálózatba és alhálózatba való üzembe helyezéséhez.
services: container-service
ms.topic: article
ms.date: 06/26/2019
ms.reviewer: nieberts, jomore
ms.openlocfilehash: 119265efa7b6504f3faf2e89cb68b9e9bd70bf9f
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617244"
---
# <a name="use-kubenet-networking-with-your-own-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Kubenet-hálózat használata saját IP-címtartományaival az Azure Kubernetes-szolgáltatásban (AKS)

Alapértelmezés szerint az AKS-fürtök [kubenet][kubenet], és egy Azure virtuális hálózat és alhálózat jön létre az Ön számára. A *kubenet*használatával a csomópontok egy IP-címet kapnak az Azure virtuális hálózati alhálózatból. A podok IP-címe a csomópontok Azure-beli virtuális hálózati alhálózatától logikailag eltérő címtérből származik. A hálózati címfordítás (NAT) konfigurálása lehetővé teszi, hogy a podok hozzáférjenek az Azure-beli virtuális hálózat erőforrásaihoz. A forgalom forrás IP-címe NAT'd a csomópont elsődleges IP-címéhez. Ez a megközelítés jelentősen csökkenti az IP-címek számát, amelyeket le kell foglalnia a hálózati térben a podok számára.

Az [Azure Container Networking Interface (CNI)][cni-networking]segítségével minden pod kap egy IP-címet az alhálózatból, és közvetlenül elérhető. Ezeknek az IP-címeknek egyedinek kell lenniük a hálózati térben, és előre meg kell tervezni őket. Minden csomópont rendelkezik egy konfigurációs paramétera podok maximális általa támogatott. A csomópontonkénti IP-címek megfelelő száma ezután előre le van foglalva az adott csomópont számára. Ez a megközelítés több tervezést igényel, és gyakran az IP-cím kimerüléséhez vagy a fürtök újraépítésének szükségességéhez vezet egy nagyobb alhálózatban, ahogy az alkalmazás igényei nőnek.

Ez a cikk bemutatja, hogyan *kubenet* hálózat létrehozása és használata egy AKS-fürt virtuális hálózati alhálózat létrehozása és használata. A hálózati beállításokkal és a szempontokkal kapcsolatos további információkért lásd: [Hálózati fogalmak a Kubernetes és az AKS számára.][aks-network-concepts]

## <a name="prerequisites"></a>Előfeltételek

* Az AKS-fürt virtuális hálózatának engedélyeznie kell a kimenő internetkapcsolatot.
* Ne hozzon létre egynél több AKS-fürtöt ugyanabban az alhálózatban.
* Előfordulhat, hogy az AKS-fürtök `169.254.0.0/16`nem használnak `172.30.0.0/16`, , `172.31.0.0/16`vagy `192.0.2.0/24` a Kubernetes szolgáltatás címtartományához.
* Az AKS-fürt által használt egyszerű szolgáltatásnak legalább [hálózati közreműködői](../role-based-access-control/built-in-roles.md#network-contributor) szerepkörrel kell rendelkeznie a virtuális hálózaton belüli alhálózaton. Ha a beépített hálózati közreműködői szerepkör használata helyett [egyéni szerepkört](../role-based-access-control/custom-roles.md) szeretne definiálni, a következő engedélyek szükségesek:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

> [!WARNING]
> A Windows Server-csomópontkészletek használatához (jelenleg előzetes verzióban az AKS-ben), az Azure CNI-t kell használnia. A kubenet hálózati modellként való használata nem érhető el a Windows Server-tárolók hoz.

## <a name="before-you-begin"></a>Előkészületek

Az Azure CLI 2.0.65-ös vagy újabb verziójára van szükség telepítve és konfigurálva. Futtassa `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni kell, olvassa el [az Azure CLI telepítése][install-azure-cli]című témakört.

## <a name="overview-of-kubenet-networking-with-your-own-subnet"></a>A kubenet-hálózat és a saját alhálózat áttekintése

Számos környezetben definiált virtuális hálózatokat és alhálózatokat lefoglalt IP-címtartományokkal. Ezek a virtuális hálózati erőforrások több szolgáltatás és alkalmazás támogatására szolgálnak. A hálózati kapcsolat biztosításához az AKS-fürtök *kubenet* (alapszintű hálózatkezelés) vagy Azure CNI (*speciális hálózati )* használatával is használhatók.

A *kubenet*segítségével csak a csomópontok kapnak IP-címet a virtuális hálózati alhálózatban. A kapszulák nem tudnak közvetlenül kommunikálni egymással. Ehelyett a felhasználó által definiált útválasztás (UDR) és az IP-továbbítás a podok közötti kapcsolat csomópontok közötti. Podokat is üzembe helyezhet egy olyan szolgáltatás mögött, amely egy hozzárendelt IP-címet kap, és a terhelés kiegyensúlyozza az alkalmazás forgalmát. Az alábbi ábra azt mutatja be, hogy az AKS-csomópontok hogyan kapnak IP-címet a virtuális hálózat alhálózatában, de a podokat nem:

![Kubenet hálózati modell AKS-fürttel](media/use-kubenet/kubenet-overview.png)

Az Azure legfeljebb 400 útvonalat támogat egy UDR-ben, így nem rendelkezhet 400 csomópontnál nagyobb AKS-fürttel. Az AKS [virtuális csomópontok][virtual-nodes] és az Azure hálózati házirendek nem támogatottak a *kubenet.*  Használhatja [calico hálózati házirendek][calico-network-policies], mivel azok támogatják a kubenet.

*Az Azure CNI*használatával minden pod kap egy IP-címet az IP-alhálózatban, és közvetlenül kommunikálhat más podok és szolgáltatások. A fürtök akkorák lehetnek, mint a megadott IP-címtartomány. Az IP-címtartományt azonban előre meg kell tervezni, és az Összes IP-címet az AKS-csomópontok használják fel a támogatott podok maximális száma alapján. Az *Azure CNI*speciális hálózati funkciókat és forgatókönyveket, például [a virtuális csomópontokat][virtual-nodes] vagy a hálózati szabályzatokat (azure-t vagy calico-t) támogatja.

### <a name="ip-address-availability-and-exhaustion"></a>Az IP-cím elérhetősége és kimerülése

Az *Azure CNI*használatával gyakori probléma, hogy a hozzárendelt IP-címtartomány túl kicsi ahhoz, hogy a fürt méretezésekor vagy frissítésekor további csomópontokat adjon hozzá. Előfordulhat, hogy a hálózati csapat nem tud elég nagy IP-címtartományt kiadni a várt alkalmazásigények kielégítéséhez.

Ez a kompromisszum, létrehozhat egy AKS-fürt, amely *kubenet,* és csatlakozzon egy meglévő virtuális hálózati alhálózathoz. Ez a megközelítés lehetővé teszi, hogy a csomópontok meghatározott IP-címeket kapjanak, anélkül, hogy nagyszámú IP-címet kellene előre lefoglalnia a fürtben futtatható összes lehetséges pod számára.

A *kubenet*segítségével sokkal kisebb IP-címtartományt használhat, és nagy fürtökés alkalmazásigényeket támogathat. Például még egy */27* IP-címtartomány, futtathat egy 20-25 csomópont fürt elegendő helyet skálázható vagy frissít. Ez a fürtméret legfeljebb *2200-2750* podot támogatna (alapértelmezettlegfeljebb 110 pod ot csomópontonként). Az *AKS-ben a kubenet* tel konfigurálható podok maximális száma 110.

A következő alapszámítások összehasonlítják a hálózati modellek közötti különbséget:

- **kubenet** - egy egyszerű */24* IP-címtartomány legfeljebb *251* csomópontot támogata a fürtben (minden Azure virtuális hálózati alhálózat az első három IP-címet a felügyeleti műveletekhez fenntartja)
  - Ez a csomópontszám legfeljebb 27 610 podot támogathat (az alapértelmezett maximum 110 pod csomópontonként kubenet)This node count could support up to *27,610* pods (with a default maximum of 110 pods per node with *kubenet)*
- **Azure CNI** – ugyanaz az *alapszintű /24* alhálózati tartomány csak *legfeljebb 8* csomópontot támogathat a fürtben
  - Ez a csomópontszám legfeljebb *240* podot támogathat (az *Azure CNI-vel*csomópontonként legfeljebb 30 podot lehet támogatni)

> [!NOTE]
> Ezek a maximumok nem veszik figyelembe a frissítési vagy méretezési műveleteket. A gyakorlatban nem futtathatja az alhálózati IP-címtartomány által támogatott csomópontok maximális számát. A frissítési műveletek méretezése során bizonyos IP-címeket használatra rendelkezésre kell hagynia.

### <a name="virtual-network-peering-and-expressroute-connections"></a>Virtuális hálózati társviszony-létesítési és ExpressRoute-kapcsolatok

A helyszíni kapcsolat biztosításához a *kubenet* és az *Azure-CNI* hálózati megközelítései azure-beli [virtuális hálózati társviszony-létesítési][vnet-peering] vagy [ExpressRoute-kapcsolatokat is használhatnak.][express-route] Gondosan tervezze meg az IP-címtartományokat az átfedések és a helytelen forgalomátirányítás elkerülése érdekében. Például sok helyszíni hálózat *10.0.0.0/8* címtartományt használ, amelyet az ExpressRoute-kapcsolaton keresztül hirdetnek. Javasoljuk, hogy az AKS-fürtöket az Azure virtuális hálózati alhálózataiba hozza létre ezen a címtartományon kívül, például *172.16.0.0/16.*

### <a name="choose-a-network-model-to-use"></a>Válassza ki a használni kívánt hálózati modellt

Az AKS-fürthöz használandó hálózati bővítmény kiválasztása általában a rugalmasság és a speciális konfigurációs igények közötti egyensúly. A következő szempontok segítenek felvázolni, hogy az egyes hálózati modellek mikor lehetnek a legmegfelelőbbek.

Használja *kubenet,* ha:

- Korlátozott az IP-címterület.
- A pod kommunikáció nagy része a fürtön belül van.
- Nincs szüksége speciális AKS-funkciókra, például virtuális csomópontokra vagy Az Azure hálózati szabályzatára.  Használja [a Calico hálózati házirendjeit.][calico-network-policies]

Használja *az Azure CNI-t:*

- Van szabad IP-címterület.
- A pod kommunikáció nagy része a fürtön kívüli erőforrásokhoz kapcsolódik.
- Nem akarod kezelni az UdRs-t.
- Az AKS-hez speciális funkciókra, például virtuális csomópontokra vagy Az Azure hálózati szabályzatra van szüksége.  Használja [a Calico hálózati házirendjeit.][calico-network-policies]

További információt, amely segít eldönteni, hogy melyik hálózati modellt használja, [olvassa el a Hálózati modellek és támogatási hatókörük összehasonlítása című témakört.][network-comparisons]

## <a name="create-a-virtual-network-and-subnet"></a>Virtuális hálózat és alhálózat létrehozása

A *kubenet* és a saját virtuális hálózati alhálózat használatának első lépéseihez hozzon létre egy erőforráscsoportot az [az csoport létrehozása][az-group-create] paranccsal. A következő példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Ha nem rendelkezik meglévő virtuális hálózattal és alhálózattal, hozza létre ezeket a hálózati erőforrásokat az [az hálózati virtuális hálózat create][az-network-vnet-create] paranccsal. A következő példában a virtuális hálózat neve *myVnet* a *192.168.0.0/16*címelőtaggal. A *192.168.1.0/24*címelőtaggal létrejön egy *myAKSSubnet nevű alhálózat.*

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myAKSVnet \
    --address-prefixes 192.168.0.0/16 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-service-principal-and-assign-permissions"></a>Egyszerű szolgáltatás létrehozása és engedélyek hozzárendelése

Ahhoz, hogy egy AKS-fürt kommunikálhasson más Azure-erőforrásokkal, Azure Active Directory-szolgáltatásnevet kell használnia. Az egyszerű szolgáltatásnak rendelkeznie kell az AKS-csomópontok által használt virtuális hálózat és alhálózat kezeléséhez szükséges engedélyekkel. Egyszerű szolgáltatás létrehozásához használja az [ad sp create-for-rbac][az-ad-sp-create-for-rbac] parancsot:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

A következő példa kimeneti mutatja az alkalmazás azonosítóját és jelszavát a szolgáltatásnév. Ezek az értékek további lépésekben használatosak egy szerepkör hozzárendeléséhez a szolgáltatásnévhez, majd létrehozva az AKS-fürtöt:

```azurecli
az ad sp create-for-rbac --skip-assignment
```

```output
{
  "appId": "476b3636-5eda-4c0e-9751-849e70b5cfad",
  "displayName": "azure-cli-2019-01-09-22-29-24",
  "name": "http://azure-cli-2019-01-09-22-29-24",
  "password": "a1024cd7-af7b-469f-8fd7-b293ecbb174e",
  "tenant": "72f998bf-85f1-41cf-92ab-2e7cd014db46"
}
```

A megfelelő delegálások hozzárendeléséhez a fennmaradó lépésekben használja az [az hálózati virtuális hálózat show][az-network-vnet-show] és az az hálózati virtuális hálózat [alhálózat a][az-network-vnet-subnet-show] parancsokat a szükséges erőforrás-azonosítók lekért. Ezek az erőforrásazonosítók változókként tárolódnak, és a további lépésekben hivatkoznak rá:

```azurecli-interactive
VNET_ID=$(az network vnet show --resource-group myResourceGroup --name myAKSVnet --query id -o tsv)
SUBNET_ID=$(az network vnet subnet show --resource-group myResourceGroup --vnet-name myAKSVnet --name myAKSSubnet --query id -o tsv)
```

Most rendelje hozzá az AKS-fürt *közreműködői* engedélyeinek egyszerű szolgáltatását a virtuális hálózaton az [az szerepkör-hozzárendelés létrehozása][az-role-assignment-create] parancs használatával. Adja meg saját * \<appId>* az előző parancs kimenetében látható módon a szolgáltatásnév létrehozásához:

```azurecli-interactive
az role assignment create --assignee <appId> --scope $VNET_ID --role Contributor
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>AKS-fürt létrehozása a virtuális hálózatban

Most létrehozott egy virtuális hálózatot és alhálózatot, és létrehozott és hozzárendelt engedélyeket egy egyszerű szolgáltatáshoz a hálózati erőforrások használatához. Most hozzon létre egy AKS-fürtöt a virtuális hálózatban és az alhálózatban az [az aks create][az-aks-create] paranccsal. Saját egyszerű * \<szolgáltatásappAzonosító>* és * \<jelszó>, *ahogy az előző parancs kimenetében is látható a szolgáltatásnév létrehozásához.

A fürtlétrehozási folyamat részeként a következő IP-címtartományok is meghatározásra kerülnek:

* A *--service-cidr* az AKS-fürt belső szolgáltatásainak IP-cím hozzárendelésére szolgál. Ennek az IP-címtartománynak olyan címterületnek kell lennie, amely et a hálózati környezetmás részein máshol nem használják. Ez a tartomány magában foglalja a helyszíni hálózati tartományokat, ha az Azure virtuális hálózatait express route vagy helyek közötti VPN-kapcsolaton keresztül csatlakoztatja vagy tervezi csatlakoztatni.

* A *--dns-service-ip-cím* nek a szolgáltatás IP-címtartományának *.10-es* címének kell lennie.

* A *--pod-cidr* kell egy nagy címterület, amely nem használatos máshol a hálózati környezetben. Ez a tartomány magában foglalja a helyszíni hálózati tartományokat, ha az Azure virtuális hálózatait express route vagy helyek közötti VPN-kapcsolaton keresztül csatlakoztatja vagy tervezi csatlakoztatni.
    * Ennek a címtartománynak elég nagynak kell lennie ahhoz, hogy elférjen a csomópontok száma, amely várhatóan felskálázási. Ezt a címtartományt nem módosíthatja a fürt telepítése után, ha további címekre van szüksége további csomópontokhoz.
    * A pod IP-címtartománya */24* címterület hozzárendelésére szolgál a fürt minden csomópontjéhez. A következő példában a *--pod-cidr* *10.244.0.0/16* csomópontja az első *csomópontot 10.244.0.0/24*, a második *csomópontot 10.244.1.0/24*, a harmadik csomópontot *pedig a 10.244.2.0/24*csomópontot rendeli hozzá .
    * A fürt méretezése vagy frissítése, az Azure platform továbbra is hozzárendeli a pod IP-címtartomány minden új csomópont.
    
* A *--docker-bridge-address* lehetővé teszi, hogy az AKS-csomópontok kommunikáljanak az alapul szolgáló felügyeleti platformmal. Ez az IP-cím nem lehet a fürt virtuális hálózati IP-címtartományán belül, és nem lehet átfedésben a hálózaton használt más címtartományokkal.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 10.244.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

> [!Note]
> Ha engedélyezni szeretné, hogy egy AKS-fürt [calico hálózati házirendet][calico-network-policies] tartalmazzon, a következő parancsot használhatja.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet --network-policy calico \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 10.244.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

AKS-fürt létrehozásakor létrejön egy hálózati biztonsági csoport és útvonaltábla. Ezeket a hálózati erőforrásokat az AKS vezérlősík kezeli. A hálózati biztonsági csoport automatikusan társítva van a csomópontokon lévő virtuális hálózati adapterekhez. Az útvonaltábla automatikusan a virtuális hálózati alhálózathoz lesz társítva. A hálózati biztonsági csoport szabályai és útvonaltáblái automatikusan frissülnek a szolgáltatások létrehozásakor és elérhetővé téve.

## <a name="next-steps"></a>További lépések

A meglévő virtuális hálózati alhálózatba telepített AKS-fürt esetén most már a szokásos módon használhatja a fürtöt. Ismerkedés az [Azure Dev Spaces vagy][dev-spaces] a Draft [használatával,][use-draft]vagy [alkalmazások üzembe helyezése a Helm használatával.][use-helm]

<!-- LINKS - External -->
[dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[Calico-network-policies]: https://docs.projectcalico.org/v3.9/security/calico-network-policy

<!-- LINKS - Internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-network-concepts]: concepts-network.md
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[use-helm]: kubernetes-helm.md
[use-draft]: kubernetes-draft.md
[virtual-nodes]: virtual-nodes-cli.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[express-route]: ../expressroute/expressroute-introduction.md
[network-comparisons]: concepts-network.md#compare-network-models
