---
title: Szabványos SKU Load Balancer használata
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogyan használhatja a Load balancert standard SKU-val, hogy szolgáltatásai elérhetők legyenek az Azure Kubernetes szolgáltatással (ak).
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: 3be60888d3d12d37650ad2cffc1911fb3b5e6682
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82790677"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Standard SKU Load Balancer használata az Azure Kubernetes Service-ben (ak)

Ahhoz, hogy hozzáférést biztosítson az alkalmazásokhoz az `LoadBalancer` Azure Kubernetes Service (ak) típusú Kubernetes Services használatával, használhat egy Azure Load Balancer. Az AK-on futó Load Balancer belső vagy külső terheléselosztóként is használható. A belső terheléselosztó csak az AK-fürttel azonos virtuális hálózaton futó alkalmazások számára teszi elérhetővé a Kubernetes szolgáltatást. A külső terheléselosztó egy vagy több nyilvános IP-címet kap a bejövő forgalom számára, és a nyilvános IP-címek használatával a Kubernetes szolgáltatás kívülről is elérhetővé válik.

Azure Load Balancer két SKU-ban érhető el – *Alapszintű* és *standard*. Alapértelmezés szerint a *standard* SKU-t használja a rendszer, amikor egy AK-fürtöt hoz létre. A *standard* SKU Load Balancer használata további funkciókat és funkciókat kínál, például nagyobb méretű háttér-készletet és Availability Zones. Fontos, hogy megértse a *standard* és az *alapszintű* terheléselosztó közötti különbségeket, mielőtt kiválasztja, hogy melyiket kívánja használni. Ha egy AK-fürtöt hoz létre, az adott fürthöz tartozó terheléselosztó SKU nem módosítható. Az *alapszintű* és a *standard* SKU-ról további információt az [Azure Load Balancer SKU-összehasonlítását][azure-lb-comparison]ismertető témakörben talál.

Ez a cikk a Kubernetes és a Azure Load Balancer fogalmak alapszintű megismerését feltételezi. További információ: a [Kubernetes alapvető fogalmai az Azure Kubernetes Service-ben (ak)][kubernetes-concepts] és [Mi az Azure Load Balancer?][azure-lb]

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a cikkhez az Azure CLI 2.0.81 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk azt feltételezi, hogy van egy AK-fürt a *standard* SKU Azure Load Balancer. Ha AK-fürtre van szüksége, tekintse meg az AK gyors üzembe helyezését [Az Azure CLI használatával][aks-quickstart-cli] vagy [a Azure Portal használatával][aks-quickstart-portal].

Ha meglévő alhálózatot vagy erőforráscsoportot használ, az AK-fürt egyszerű szolgáltatása a hálózati erőforrások kezeléséhez is szükséges. Általában rendelje hozzá a *hálózati közreműködő* szerepkört az egyszerű szolgáltatáshoz a delegált erőforrásokon. Egyszerű szolgáltatásnév helyett használhatja a rendszerhez rendelt felügyelt identitást is az engedélyekhez. További információ: [felügyelt identitások használata](use-managed-identity.md). Az engedélyekkel kapcsolatos további információkért lásd: [AK-hozzáférés delegálása más Azure-erőforrásokhoz][aks-sp].

### <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Alapszintű SKU-Load Balancerról standard SKU-ra való áttérés

Ha rendelkezik egy meglévő, alapszintű SKU-Load Balancer rendelkező fürttel, akkor fontos, hogy a rendszer az áttelepítés során vegye figyelembe, hogy mikor kell a standard SKU-Load Balancer használatával fürtöt használni.

Tegyük fel például, hogy a fürtök áttelepítésére szolgáló kék/zöld központi telepítések `load-balancer-sku` egy gyakori eljárás, mivel a fürt típusa csak a fürt létrehozási ideje alatt definiálható. Az *alapszintű SKU* -terheléselosztó azonban olyan *alapszintű SKU* IP-címeket használ, amelyek nem kompatibilisek a *standard* SKU-beli Load balancerekkel, mivel *szabványos SKU* IP-címeket igényelnek. Amikor a fürtöket áttelepíti Load Balancer SKU-ra, egy kompatibilis IP-címmel rendelkező új IP-címet kell megadni.

A fürtök áttelepítésével kapcsolatos további szempontokért tekintse meg a [dokumentációt az áttelepítési megfontolásokból](aks-migration.md) , és tekintse meg az áttelepítés során megfontolandó fontos témakörök listáját. Az alábbi korlátozások szintén fontos viselkedési különbségeket is figyelembe vesznek, ha standard SKU Load Balancert használ az AK-ban.

### <a name="limitations"></a>Korlátozások

A következő korlátozások érvényesek a terheléselosztó és a *szabványos* SKU-t támogató AK-fürtök létrehozásakor és kezelésekor:

* Legalább egy nyilvános IP-cím vagy IP-előtag szükséges ahhoz, hogy a kimenő forgalom az AK-fürtből legyen engedélyezve. A nyilvános IP-cím vagy IP-előtag szükséges a vezérlési sík és az ügynök csomópontjai közötti kapcsolat fenntartásához, valamint az AK korábbi verzióival való kompatibilitás fenntartásához is. A következő lehetőségek közül választhat a *szabványos* SKU Load balancerrel rendelkező nyilvános IP-címek vagy IP-előtagok megadásához:
    * Adja meg saját nyilvános IP-címeit.
    * Adja meg saját nyilvános IP-előtagjait.
    * 100-ig terjedő számot kell megadnia, amely lehetővé teszi, hogy az AK-fürt számos *szabványos* SKU-beli nyilvános IP-címet hozzon létre ugyanabban az erőforráscsoporthoz, amely az AK-fürthöz lett létrehozva, amelyet általában a *MC_* elején neveznek el. Az AK a nyilvános IP-címet a *standard* SKU Load Balancerhez rendeli. Alapértelmezés szerint a rendszer automatikusan létrehoz egy nyilvános IP-címet ugyanabban az erőforráscsoportban, mint az AK-fürtöt, ha nincs megadva nyilvános IP-cím, nyilvános IP-előtag vagy IP-címek száma. Emellett engedélyeznie kell a nyilvános címeket, és el kell kerülnie az IP-létrehozást megtiltó Azure Policy létrehozását.
* A terheléselosztó *szabványos* SKU-jának használatakor a *1,13-es vagy újabb*Kubernetes-verziót kell használnia.
* A terheléselosztó SKU definiálása csak akkor hajtható végre, ha AK-fürtöt hoz létre. Egy AK-fürt létrehozása után nem módosíthatja a terheléselosztó SKU-t.
* Egyetlen fürtben csak egyetlen típusú terheléselosztó SKU-t (alapszintű vagy standard) használhat.
* *Standard szintű* Az SKU-terheléselosztó csak a *szabványos* SKU IP-címeket támogatja.

## <a name="use-the-standard-sku-load-balancer"></a>A *standard* SKU Load Balancer használata

Ha egy AK-fürtöt hoz létre, a rendszer alapértelmezés szerint a *standard* SKU Load balancert használja, amikor szolgáltatásokat futtat a fürtben. Az [Azure CLI][aks-quickstart-cli] -t használó rövid útmutató például telepíti a *standard* SKU Load balancert használó minta alkalmazást.

> [!IMPORTANT]
> A nyilvános IP-címek elkerülhetők a felhasználó által megadott útvonal (UDR) testreszabásával. Egy AK-fürt kimenő típusának megadása a UDR kihagyhatja az IP-kiépítés és a háttérbeli készlet beállítását az AK-hoz létrehozott Azure Load Balancer számára. Lásd: [fürtök beállítása `outboundType` a következőre: "userDefinedRouting"](egress-outboundtype.md).

## <a name="configure-the-load-balancer-to-be-internal"></a>A terheléselosztó belső beállítása

Azt is beállíthatja, hogy a terheléselosztó belső legyen, és ne tegye közzé a nyilvános IP-címet. A terheléselosztó belsőként való konfigurálásához vegyen `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` fel jegyzetként a *terheléselosztó* szolgáltatásba. [Itt][internal-lb-yaml]láthat egy példát a YAML-jegyzékre, valamint további részleteket is megtudhat a belső terheléselosztó használatával kapcsolatban.

## <a name="scale-the-number-of-managed-public-ips"></a>A felügyelt nyilvános IP-címek számának méretezése

Ha egy *szabványos* SKU-Load balancert használ a felügyelt kimenő nyilvános IP-címekkel, amelyek alapértelmezés szerint jönnek létre, akkor a felügyelt kimenő nyilvános IP-címeket a *terheléselosztó – felügyelt-IP-Count* paraméter használatával méretezheti.

Meglévő fürt frissítéséhez futtassa a következő parancsot. Ez a paraméter a fürt létrehozási ideje beállításnál is beállítható, hogy több felügyelt kimenő nyilvános IP-cím legyen.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

A fenti példa a felügyelt kimenő nyilvános IP-címek számát állítja be a *myResourceGroup*-ben található *myAKSCluster* - *fürt esetében.* 

A terheléselosztó által *felügyelt-IP-Count* paraméterrel is beállíthatja a felügyelt kimenő nyilvános IP-címek kezdeti számát a fürt létrehozásakor a `--load-balancer-managed-outbound-ip-count` paraméter hozzáfűzésével és a kívánt értékre való beállításával. A felügyelt kimenő nyilvános IP-címek alapértelmezett száma 1.

## <a name="provide-your-own-public-ips-or-prefixes-for-egress"></a>Saját nyilvános IP-címek vagy előtagok megadása a kimenő forgalomhoz

*Standard* SKU Load Balancer használatakor az AK-fürt automatikusan létrehoz egy nyilvános IP-címet ugyanabban az erőforráscsoporthoz, amely az AK-fürthöz lett létrehozva, és hozzárendeli a nyilvános IP-címet a *standard* SKU Load Balancerhez. Azt is megteheti, hogy a fürt létrehozási idején saját nyilvános IP-címet rendel hozzá, vagy frissítheti a meglévő fürt terheléselosztó-tulajdonságait.

Több IP-cím vagy előtag megadásával több háttér-szolgáltatást is meghatározhat, ha az IP-címet egyetlen terheléselosztó objektum mögött definiálja. Az adott csomópontok kimenő végpontja attól függ, hogy milyen szolgáltatáshoz vannak társítva.

> [!IMPORTANT]
> *Szabványos* SKU nyilvános IP-címeket kell használnia a kimenő forgalomhoz a Load Balancer *standard* SKU-jának megfelelően. A nyilvános IP-címek SKU-jának ellenőrzéséhez használja az az [Network Public-IP show][az-network-public-ip-show] parancsot:
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

A nyilvános IP-címek azonosítóinak listázásához használja az az [Network Public-IP show][az-network-public-ip-show] parancsot.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

A fenti parancs a *myPublicIP* nyilvános IP-CÍMÉnek azonosítóját jeleníti meg a *myResourceGroup* -erőforráscsoporthoz.

Használja az az *AK Update* parancsot a *Load-Balancer-kimenő-IPS* paraméterrel, és frissítse a fürtöt a nyilvános IP-címekkel.

A következő példa a *Load-Balancer-kimenő-IPS* paramétert használja az előző parancs azonosítói alapján.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Nyilvános IP-előtagokat is használhat a *standard* SKU Load balancerrel való kimenő forgalomhoz. Az alábbi példa az az [Network Public-IP előtag show][az-network-public-ip-prefix-show] parancsot használja a nyilvános IP-előtagok azonosítóinak listázásához:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

A fenti parancs a *myPublicIPPrefix* nyilvános IP-előtag azonosítóját jeleníti meg a *myResourceGroup* erőforráscsoporthoz.

Az alábbi példa a *Load-Balancer-kimenő-IP-előtag* paramétert használja az előző parancs azonosítói alapján.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> A nyilvános IP-címeknek és az IP-ELŐTAGOKNAK ugyanabban a régióban kell lenniük, és ugyanannak az előfizetésnek kell szerepelniük, mint az AK-fürt. 

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>Saját nyilvános IP-cím vagy előtag definiálása a fürt létrehozási idején

Előfordulhat, hogy saját IP-címeket vagy IP-előtagokat kíván létrehozni a kimenő forgalomhoz a fürt létrehozási ideje alatt, hogy támogassa a kimenő végpontok engedélyezési forgatókönyveit. Fűzze hozzá ugyanezeket a paramétereket a fürt létrehozási lépéseként, hogy meghatározza a saját nyilvános IP-címeit és az IP-előtagokat a fürt életciklusának elején.

*A* *Load-Balancer-kimenő-IPS* paraméterrel hozzon létre egy új fürtöt a nyilvános IP-címekkel az elején.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Használja az az *AK Create* parancsot a *Load-Balancer-kimenő-IP-előtag* paraméterrel egy új fürt létrehozásához a nyilvános IP-előtagokkal az elején.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

## <a name="configure-outbound-ports-and-idle-timeout"></a>Kimenő portok és Üresjárati időkorlát konfigurálása

> [!WARNING]
> A következő szakasz a nagyobb léptékű hálózatkezelés speciális forgatókönyveit és az alapértelmezett konfigurációk SNAT-kimerülési problémáinak kezelését ismerteti. A virtuális gépek és az IP-címek esetében a rendelkezésre álló kvóta pontos leltározása szükséges ahhoz, hogy a *AllocatedOutboundPorts* vagy a *IdleTimeoutInMinutes* az alapértelmezett értékről az egészséges fürtök fenntartása érdekében módosítsa.
> 
> A *AllocatedOutboundPorts* és a *IdleTimeoutInMinutes* értékeinek módosítása jelentősen befolyásolhatja a terheléselosztó kimenő szabályának viselkedését. Az értékek frissítése előtt tekintse át az [Load Balancer kimenő szabályokat][azure-lb-outbound-rules-overview], a [terheléselosztó kimenő szabályait][azure-lb-outbound-rules]és a [kimenő kapcsolatokat az Azure-ban][azure-lb-outbound-connections] , mielőtt frissíti ezeket az értékeket a módosítások hatásának teljes megértéséhez.

A kimenő lefoglalt portok és a tétlen időtúllépések a [SNAT][azure-lb-outbound-connections]-hez használatosak. Alapértelmezés szerint a *standard* SKU Load Balancer [automatikus hozzárendelést használ a kihelyezett portok számához a háttérbeli készlet mérete alapján][azure-lb-outbound-preallocatedports] , valamint az egyes portok 30 perces üresjárati időkorlátját. Az értékek megtekintéséhez használja az az [Network LB kimenő-Rule List][az-network-lb-outbound-rule-list] lehetőséget a terheléselosztó kimenő szabályának megjelenítéséhez:

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

Az előző parancs a terheléselosztó kimenő szabályát fogja kilistázni, például:

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

A példa kimenete a *AllocatedOutboundPorts* és a *IdleTimeoutInMinutes*alapértelmezett értékét jeleníti meg. A *AllocatedOutboundPorts* 0 értéke állítja be a kimenő portok számát a háttérbeli készlet méretétől függően a kimenő portok számának automatikus hozzárendelése használatával. Ha például a fürt 50 vagy kevesebb csomóponttal rendelkezik, az egyes csomópontok 1024 portjai vannak lefoglalva.

Érdemes lehet módosítani a *allocatedOutboundPorts* vagy a *IdleTimeoutInMinutes* beállítást, ha a fenti alapértelmezett konfiguráció alapján szeretné szembenézni a SNAT-kimerüléssel. Minden további IP-cím lehetővé teszi a 64 000 további portok kiosztását, az Azure-standard Load Balancer azonban nem növekszik automatikusan a portok száma csomóponton, ha további IP-címek vannak hozzáadva. Ezeket az értékeket módosíthatja a *Load-Balancer-kimenő-portok* és a *Load-Balancer – Idle-timeout* paraméterek beállításával. Például:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

> [!IMPORTANT]
> A kapcsolat vagy a skálázási problémák elkerülése érdekében [ki kell számítania a szükséges kvótát][calculate-required-quota] , mielőtt testreszabja a *allocatedOutboundPorts* . A *allocatedOutboundPorts* megadott értéknek a 8-as többszörösének is kell lennie.

Fürt létrehozásakor a *Load-Balancer-kimenő-portok* és a *Load-Balancer-Idle-timeout* paramétereket is használhatja, de a *terheléselosztás által felügyelt-kimenő-IP-darabszám*, a *Load-Balancer-kimenő-* IP-címek vagy a *Load-Balancer-kimenő-IP-előtag* is megadható.  Például:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

Ha módosítja a terheléselosztó *– kimenő – portok* és a *terheléselosztó – Üresjárati időkorlát* paramétereit az alapértelmezett értéktől, akkor ez a beállítás hatással van a terheléselosztó-profil viselkedésére, ami hatással van a teljes fürtre.

### <a name="required-quota-for-customizing-allocatedoutboundports"></a>A allocatedOutboundPorts testreszabásához szükséges kvóta
Elegendő kimenő IP-kapacitásra van szükség a csomópontok és a kívánt kimenő portok számától függően. A következő képlettel ellenőrizheti, hogy van-e elegendő kimenő IP-kapacitása: 
 
*outboundIPs* \* 64 000 \> *nodeVMs* nodeVMs \* *desiredAllocatedOutboundPorts*.
 
Ha például 3 *nodeVMs*van, és 50 000 *desiredAllocatedOutboundPorts*, legalább 3 *outboundIPs*kell lennie. Javasoljuk, hogy a szükségesnél újabb kimenő IP-kapacitást építsen ki. Emellett a fürt automéretezőjét és a csomópont-készlet frissítésének lehetőségét is figyelembe kell vennie a kimenő IP-kapacitás kiszámításakor. A fürt autoskálázása esetében tekintse át az aktuális csomópontok darabszámát és a csomópontok maximális darabszámát, és használja a magasabb értéket. A frissítéshez az összes olyan csomópont-készlethez, amely lehetővé teszi a frissítését, egy további csomópontos virtuális gép számára.
 
Ha a *IdleTimeoutInMinutes* eltérő értékre állítja be, mint az alapértelmezett 30 perc, akkor vegye figyelembe, hogy a számítási feladatoknak mennyi ideig kell kiadniuk a kimenő kapcsolatokat. Azt is vegye figyelembe, hogy egy *standard* SKU-Load Balancer alapértelmezett időtúllépési értéke 4 perc. Egy olyan *IdleTimeoutInMinutes* -érték, amely pontosabban tükrözi az adott AK-beli munkaterhelést, csökkentheti a SNAT okozta kimerültséget, mivel a kapcsolatok már nincsenek használatban.

## <a name="restrict-access-to-specific-ip-ranges"></a>Meghatározott IP-tartományokhoz való hozzáférés korlátozása

A terheléselosztó virtuális hálózatához társított hálózati biztonsági csoport (NSG) alapértelmezés szerint rendelkezik egy olyan szabállyal, amely engedélyezi az összes bejövő külső forgalmat. Ezt a szabályt úgy frissítheti, hogy csak adott IP-tartományokat engedélyezzen a bejövő forgalom számára. A következő jegyzékfájl a *loadBalancerSourceRanges* -t használja a bejövő külső forgalomhoz tartozó új IP-címtartomány megadásához:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
  loadBalancerSourceRanges:
  - MY_EXTERNAL_IP_RANGE
```

A fenti példa frissíti a szabályt úgy, hogy csak a *MY_EXTERNAL_IP_RANGE* tartomány bejövő külső forgalmát engedélyezze. Az ezzel a módszerrel a terheléselosztó szolgáltatáshoz való hozzáférés korlátozására vonatkozó további információk a [Kubernetes dokumentációjában][kubernetes-cloud-provider-firewall]találhatók.

## <a name="next-steps"></a>További lépések

További információ a Kubernetes Services szolgáltatásról a [Kubernetes Services dokumentációjában][kubernetes-services].

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubernetes-cloud-provider-firewall]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-cloud-provider-firewall/#restrict-access-for-loadbalancer-service
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-network-lb-outbound-rule-list]: /cli/azure/network/lb/outbound-rule?view=azure-cli-latest#az-network-lb-outbound-rule-list
[az-network-public-ip-show]: /cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/skus.md
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-rules-overview.md#snatports
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#snat
[azure-lb-outbound-preallocatedports]: ../load-balancer/load-balancer-outbound-connections.md#preallocatedports
[azure-lb-outbound-rules-overview]: ../load-balancer/load-balancer-outbound-rules-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[calculate-required-quota]: #required-quota-for-customizing-allocatedoutboundports
