---
title: Szabványos termékváltozat-terheléselosztó használata
titleSuffix: Azure Kubernetes Service
description: Ismerje meg, hogyan használhatja a terheléselosztót egy standard termékváltozattal a szolgáltatások azure Kubernetes-szolgáltatás (AKS) használatával.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: c1d2c0e48394fbde1b595ae4b405d84f437dc5e4
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392811"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Szabványos termékváltozat terheléselosztó használata az Azure Kubernetes szolgáltatásban (AKS)

Az Azure Kubernetes-szolgáltatás (AKS) kubernetes-szolgáltatásokon `LoadBalancer` keresztül az alkalmazásokhoz való hozzáférés biztosításához használhatja az Azure Load Balancer-t. Az AKS-en futó terheléselosztó belső vagy külső terheléselosztóként is használható. A belső terheléselosztó a Kubernetes-szolgáltatást csak az AKS-fürttel azonos virtuális hálózaton futó alkalmazások számára teszi elérhetővé. Egy külső terheléselosztó egy vagy több nyilvános IP-t kap a be- és a nyilvános IP-k használatával elérhetővé teszi a Kubernetes-szolgáltatást.

Az Azure Load Balancer két termékkódban érhető el – *alapszintű* és *standard*. Alapértelmezés szerint a *standard* termékváltozat az AKS-fürt létrehozásakor használatos. A *szabványos* termékváltozat terheléselosztó használata további funkciókat és funkciókat biztosít, például a nagyobb háttérkészlet méretét és a rendelkezésre állási zónákat. Fontos, hogy a *standard* és *az alapszintű* terheléselosztók közötti különbségeket a használni kívánt beállítás kiválasztása előtt ismerje meg. Miután létrehozott egy AKS-fürtöt, nem módosíthatja az adott fürt terheléselosztó termékváltozatát. Az *alapszintű* és a *standard termékváltozatokkal* kapcsolatos további információkért lásd: [Azure load balancer SKU összehasonlítás.][azure-lb-comparison]

Ez a cikk feltételezi a Kubernetes és az Azure Load Balancer fogalmak alapvető megértését. További információ: [Kubernetes alapfogalmak az Azure Kubernetes service (AKS)][kubernetes-concepts] és [mi az Azure Load Balancer?][azure-lb].

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a CLI-t, ez a cikk megköveteli, hogy az Azure CLI 2.0.81-es vagy újabb verzióját futassza. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy AKS-fürt a *standard* termékváltozat Azure Load Balancer. Ha AKS-fürtre van szüksége, tekintse meg az AKS [gyorsútmutatót az Azure CLI használatával][aks-quickstart-cli] vagy az Azure Portal [használatával.][aks-quickstart-portal]

Meglévő alhálózat vagy erőforráscsoport használata esetén az AKS-fürt egyszerű szolgáltatásának is szüksége van a hálózati erőforrások kezeléséhez. Általában rendelje hozzá a *hálózat közreműködői* szerepkört a szolgáltatásnévhez a delegált erőforrásokon. Egyszerű szolgáltatás helyett használhatja a rendszer hez rendelt felügyelt identitás engedélyeket is használhatja. További információ: [Felügyelt identitások használata.](use-managed-identity.md) Az engedélyekről további információt az [AKS-hozzáférés delegálása más Azure-erőforrásokhoz című témakörben talál.][aks-sp]

### <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Áttérés egyszerű termékváltozatterhelés-elosztóról szabványos termékváltozatra

Ha rendelkezik egy meglévő fürttel az alapszintű termékváltozat terheléselosztóval, fontos viselkedésbeli különbségeket kell figyelembe venni, amikor áttelepíti a fürt használatát a standard termékváltozat terheléselosztóval.

Például a kék/zöld központi telepítések a fürtök áttelepítéséhez általános gyakorlat, mivel a `load-balancer-sku` fürt típusa csak fürtlétrehozáskor határozható meg. Az *alapszintű* termékváltozat-terheléselosztók azonban alapvető Termékváltozat-IP-címeket használnak, amelyek nem kompatibilisek *a szabványos termékváltozat-terheléselosztókkal,* mivel *szabványos Termékváltozat-IP-címeket* igényelnek. *Basic SKU* A fürtök áttelepítésekor a terheléselosztó termékváltozatának frissítéséhez egy új IP-címre, kompatibilis IP-címtermékváltozattal.

A fürtök áttelepítésével kapcsolatos további szempontokért látogasson el [az áttelepítési szempontokról szóló dokumentációnkba,](aks-migration.md) és tekintse meg az áttelepítéssorán figyelembe veendő fontos témakörök listáját. Az alábbi korlátozások is fontos viselkedési különbségek et figyelembe kell venni, ha a standard termékváltozat terheléselosztók használata az AKS-ben.

### <a name="limitations"></a>Korlátozások

A következő korlátozások vonatkoznak, amikor olyan AKS-fürtöket hoz létre és kezel, amelyek támogatják a *standard* termékváltozattal rendelkező terheléselosztót:

* Legalább egy nyilvános IP- vagy IP-előtag szükséges az AKS-fürtből való kimenő forgalom engedélyezéséhez. A nyilvános IP- vagy IP-előtag ra van szükség a vezérlősík és az ügynökcsomópontok közötti kapcsolat fenntartásához, valamint az AKS korábbi verzióival való kompatibilitás fenntartásához is. A következő beállításokkal rendelkezik a szabványos *termékváltozat* terheléselosztóval rendelkező nyilvános IP-k vagy IP-előtagok megadásához:
    * Adja meg saját nyilvános IP-k.
    * Adja meg saját nyilvános IP-előtagok.
    * Adjon meg egy legfeljebb 100-as számot, hogy az AKS-fürt létrehozhassa, hogy az AKS-fürttel létrehozott erőforráscsoportban sok *szabványos* termékváltozat nyilvános IP-t hozzon létre, amelyet általában *az elején MC_* neveznek el. Az AKS hozzárendeli a nyilvános IP-címet a *standard* termékváltozat terheléselosztóhoz. Alapértelmezés szerint egy nyilvános IP automatikusan létrejön ugyanabban az erőforráscsoportban, mint az AKS-fürt, ha nincs megadva nyilvános IP-cím, nyilvános IP-előtag vagy IP-címszám. Emellett engedélyeznie kell a nyilvános címeket, és el kell kerülnie az IP-létrehozást tiltó Azure-szabályzat létrehozását.
* Ha a *standard* termékváltozatot használja egy terheléselosztóhoz, a Kubernetes *1.13-as vagy újabb*verzióját kell használnia.
* A terheléselosztó termékváltozatának definiálása csak Akkor végezhető el, ha AKS-fürtöt hoz létre. AKS-fürt létrehozása után nem módosíthatja a terheléselosztó termékváltozatát.
* Egyetlen típusú terheléselosztó Termékváltozat (Alapszintű vagy Standard) csak egy fürtben használható.
* *Standard (standard)* A termékváltozat terheléselosztói csak *a szabványos* Termékváltozat IP-címeit támogatják.

## <a name="use-the-standard-sku-load-balancer"></a>A *szabványos* termékváltozat terheléselosztó használata

AKS-fürt létrehozásakor alapértelmezés szerint a *szabványos* termékváltozat terheléselosztója használatos, amikor az adott fürtben szolgáltatásokat futtat. Például [az Azure CLI használatával a rövid útmutató][aks-quickstart-cli] egy mintaalkalmazást telepít, amely a *standard* termékkészlet-terheléselosztót használja.

> [!IMPORTANT]
> A nyilvános IP-címek a felhasználó által definiált útvonal (UDR) testreszabásával elkerülhetők. Az AKS-fürt kimenő típusának udr-ként történő megadása kihagyhatja az IP-kiépítést és a háttérkészlet beállítását az AKS által létrehozott Azure-terheléselosztóhoz. Lásd: [A fürt `outboundType` beállítása a "userDefinedRouting"](egress-outboundtype.md).

## <a name="configure-the-load-balancer-to-be-internal"></a>A terheléselosztó beállítása belső

A terheléselosztót belsőként is konfigurálhatja, és nem teheti elérhetővé a nyilvános IP-címet. A terheléselosztó belső beállítása belsőként, adja hozzá `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` a *LoadBalancer* szolgáltatás hoz. Láthatjuk egy példa yaml nyilvánvaló, valamint további részleteket egy belső terheléselosztó [itt][internal-lb-yaml].

## <a name="scale-the-number-of-managed-public-ips"></a>A felügyelt nyilvános IP-k számának méretezése

Ha egy *standard* termékváltozat terheléselosztó felügyelt kimenő nyilvános IP-címekkel, amelyek alapértelmezés szerint létrehozott, a felügyelt kimenő nyilvános IP-k száma a *terhelés-elosztó-felügyelt-ip-count* paraméter használatával skálázhatja a felügyelt nyilvános IP-címzési.

Meglévő fürt frissítéséhez futtassa a következő parancsot. Ez a paraméter fürtlétrehozási időpontban is beállítható, hogy több felügyelt nyilvános IP-szolgáltatóval legyen.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

A fenti példa a managed to-out-outout IP-k számát *2-re* állítja a *myResourceGroup* *myAKSCluster* fürtjéhez. 

A *terheléselosztó-felügyelt-ip-count* paraméter rel is beállíthatja a felügyelt kimenő nyilvános IP-k kezdeti számát `--load-balancer-managed-outbound-ip-count` a fürt létrehozásakor a paraméter hozzáfűzésével és a kívánt értékre állításával. A felügyelt nyilvános IP-k alapértelmezett száma 1.

## <a name="provide-your-own-public-ips-or-prefixes-for-egress"></a>Adja meg saját nyilvános IP-kvagy előtagok a kimenő forgalom

*Szabványos* termékváltozat terheléselosztó használataesetén az AKS-fürt automatikusan létrehoz egy nyilvános IP-címet az AKS-fürthöz létrehozott erőforráscsoportban, és hozzárendeli a nyilvános IP-címet a *standard* termékváltozat terheléselosztóhoz. Azt is megteheti, hogy a fürt létrehozásakor hozzárendeli saját nyilvános IP-címét, vagy frissítheti egy meglévő fürt terheléselosztó tulajdonságait.

Több IP-cím vagy előtag létrehozásával több háttérszolgáltatást is definiálhatsz, amikor egyetlen terheléselosztó objektum mögött definiálja az IP-címet. Adott csomópontok kimenő végpontja attól függ, hogy milyen szolgáltatáshoz vannak társítva.

> [!IMPORTANT]
> *A standard* termékelosztóval a *normál* termékváltozattal rendelkező kimenő forgalomhoz szabványos termékváltozatok használatával kell használnia a nyilvános ip-eket. A nyilvános IP-k termékváltozatát az [az-hálózat nyilvános ip show][az-network-public-ip-show] parancsával ellenőrizheti:
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

Használja az [az-hálózat nyilvános ip show][az-network-public-ip-show] parancsot a nyilvános IP azonosítók listázásához.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

A fenti parancs a *myPublicIP* nyilvános IP azonosítóját jeleníti meg a *myResourceGroup* erőforráscsoportban.

Használja az *aks update* parancsot a *terheléselosztó-kimenő ips* paraméterrel a fürt nyilvános IP-címekkel való frissítéséhez.

A következő példa a *terheléselosztó-kimenő ips paramétert* használja az előző parancs azonosítóival.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Nyilvános IP-előtagokat is használhat a kimenő forgalomhoz a *standard* termékváltozat terheléselosztójával. A következő példa az [az hálózati nyilvános ip-előtag show][az-network-public-ip-prefix-show] parancsot használja a nyilvános IP-előtagok azonosítóinak felsorolására:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

A fenti parancs a *myResourceGroup* erőforráscsoport *myPublicIPPrefix* nyilvános IP-előtagának azonosítóját jeleníti meg.

A következő példa a *terheléselosztó-kimenő-ip-előtagok* paramétert használja az előző parancs azonosítóival.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> A nyilvános IP-címeket és AZ IP-előtagoknak ugyanabban a régióban és az AKS-fürtelőfizetés egy részében kell lenniük. 

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>Saját nyilvános IP-cím vagy előtagok definiálása a fürt létrehozási idején

Előfordulhat, hogy szeretné, hogy a saját IP-címek vagy IP-előtagok a kimenő forgalom a fürt létrehozása kor, hogy támogassa a forgatókönyvek, például az engedélyezési kimenő végpontok. Fűzz hozzá ugyanazokat a paramétereket a fürt létrehozási lépéséhez a saját nyilvános IP-címeket és IP-előtagokat a fürt életciklusának kezdetén.

Használja az *aks create* parancsot a *terheléselosztó-kimenő ips* paraméterrel, hogy hozzon létre egy új fürtöt a nyilvános IP-címekkel az elején.

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

Használja az *aks create* parancsot a *terheléselosztó-kimenő-ip-előtagok* paraméterrel egy új fürt létrehozásához a nyilvános IP-előtagokkal az elején.

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

## <a name="configure-outbound-ports-and-idle-timeout"></a>Kimenő portok és tétlen időeltetés konfigurálása

> [!WARNING]
> A következő szakasz a nagyobb léptékű hálózatspeciális forgatókönyveihez vagy az snat-kimerülési problémák kezelésére szolgál az alapértelmezett konfigurációkkal. A megfelelő fürtök karbantartása érdekében pontos leltárral kell rendelkeznie a virtuális gépek és IP-címek rendelkezésre álló kvótáiról, mielőtt a *HozzárendeltoutboundPorts* vagy *az IdleTimeoutInMinutes alapértelmezett* értékről való módosítása érdekében az alapértelmezett értéket szeretné módosítani.
> 
> A *lefoglaltkimenő portok* és az *IdleTimeoutInMinutes* értékeinek módosítása jelentősen megváltoztathatja a terheléselosztó kimenő szabályának viselkedését. Tekintse át a [terheléselosztó kimenő szabályok,][azure-lb-outbound-rules-overview] [terheléselosztó kimenő szabályok][azure-lb-outbound-rules], és a kimenő kapcsolatok az [Azure-ban][azure-lb-outbound-connections] frissítése előtt ezeket az értékeket, hogy teljes mértékben megértsék a módosítások hatását.

A kimenő lefoglalt portok és azok tétlen időhahatárolói az [SNAT-hoz][azure-lb-outbound-connections]használatosak. Alapértelmezés szerint a *standard* termékváltozat terheléselosztó automatikus hozzárendelést használ a háttérkészlet mérete és az egyes portok 30 perces tétlen időtúlszáma [alapján a kimenő portok számához.][azure-lb-outbound-preallocatedports] Ezeknek az értékeknek a megtekintéséhez használja [az hálózati lb kimenő szabálylistát][az-network-lb-outbound-rule-list] a terheléselosztó kimenő szabályának megjelenítéséhez:

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

Az előző parancsok a terheléselosztó kimenő szabályát sorolják fel, például:

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

A példakimenet a *AllocatedOutboundPorts* és az *IdleTimeoutInMinutes*alapértelmezett értékét jeleníti meg. A 2-es érték a *hozzárendeltkimenő portok* esetében beállítja az automatikus hozzárendelést használó kimenő portok számát a háttérkészlet mérete alapján. Ha például a fürt 50 vagy kevesebb csomót foglal el, minden csomóponthoz 1024 port van lefoglalva.

Fontolja meg a *lefoglaltOutboundPorts* vagy *az IdleTimeoutInMinutes* beállításának módosítását, ha a fenti alapértelmezett konfiguráció alapján az SNAT-kimerültség várható. Minden további IP-cím 64 000 további portot tesz lehetővé a leosztáshoz, azonban az Azure Standard Load Balancer nem növeli automatikusan a csomópontonkénti portokat, ha több IP-címet ad hozzá. Ezeket az értékeket módosíthatja a *terheléselosztó kimenő portok* és a *terheléselosztó-üresjárati időtúllépésre* paraméterek beállításával. Például:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

> [!IMPORTANT]
> A kapcsolati és méretezési problémák elkerülése érdekében ki kell [számítania a szükséges kvótát a][calculate-required-quota] *lefoglaltkimenőportok* testreszabása előtt. A *lefoglaltKimenőportokhoz* megadott értéknek szintén 8 többszörösének kell lennie.

Fürt létrehozásakor használhatja a *terheléselosztó kimenő portokat* és a *terheléselosztó-üresjárati időtúlterhelési* paramétereket is, de meg kell adnia a *terheléselosztó által kezelt-kimenő ip-szám,* *a terheléselosztó-kimenő ips,* vagy *a terhelés-elosztó-kimenő-ip-előtagok* at is.  Például:

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

Ha módosítja a *terheléselosztó kimenő portok* és *a terheléselosztó-üresjárati időtúlidőszak* paramétereit az alapértelmezett, ez befolyásolja a viselkedését a terheléselosztó profil, amely hatással van a teljes fürtre.

### <a name="required-quota-for-customizing-allocatedoutboundports"></a>A lefoglalt helyek testreszabásához szükségeskimenő portok
Elegendő kimenő IP-kapacitással kell rendelkeznie a csomópontvirtuális gépek és a kívánt lefoglalt kimenő portok száma alapján. Annak ellenőrzéséhez, hogy elegendő kimenő IP-kapacitással rendelkezik, használja a következő képletet: 
 
*kimenő IP-k* \* 64 000 \> *nodeVM* \* *desiredAllocatedOutboundPorts*.
 
Ha például 3 *nodevm-je*van, és 50 000 *desiredAllocatedOutboundPorts*rendszerű, legalább 3 *kimenő IP-szolgáltatóval*kell rendelkeznie. Javasoljuk, hogy a szükségesnél nagyobb kimenő IP-kapacitást építsen be. Emellett figyelembe kell venni a fürt automatikus skálázóját és a csomópontkészlet-frissítések lehetőségét a kimenő IP-kapacitás kiszámításakor. A fürt automatikus skálázó, tekintse át az aktuális csomópontok száma és a maximális csomópont száma, és használja a magasabb értéket. A frissítéshez egy további csomópont virtuális gép minden csomópont készlet, amely lehetővé teszi a frissítést.
 
Ha az *IdleTimeoutInMinutes értékét* az alapértelmezett30 perctől eltérő értékre állítja, fontolja meg, hogy a számítási feladatoknak mennyi ideig lesz szükségük kimenő kapcsolatra. Is fontolja meg az alapértelmezett időtúlterhelési érték egy *standard* termékváltozat terheléselosztó kívül használt AKS 4 perc. Az *IdleTimeoutInMinutes* érték, amely pontosabban tükrözi az adott AKS-számítási feladatok at segíthet csökkenteni az SNAT kimerültség által okozott lekötési kapcsolatok már nem használják.

## <a name="restrict-access-to-specific-ip-ranges"></a>Adott IP-tartományokhoz való hozzáférés korlátozása

A terheléselosztó virtuális hálózatához társított hálózati biztonsági csoport (NSG) alapértelmezés szerint rendelkezik egy olyan szabállyal, amely engedélyezi az összes bejövő külső forgalmat. Ez a szabály frissítheti, hogy csak adott IP-tartományok at bejövő forgalom. A következő jegyzékfájl *loadBalancerSourceRanges* segítségével új IP-tartományt ad meg a bejövő külső forgalomhoz:

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

A fenti példa frissíti a szabályt, hogy csak a bejövő külső forgalmat a *MY_EXTERNAL_IP_RANGE* tartományból. A terheléselosztó szolgáltatáshoz való hozzáférés korlátozására vonatkozó módszer használatáról a [Kubernetes dokumentációjában][kubernetes-cloud-provider-firewall]talál további információt.

## <a name="next-steps"></a>További lépések

A Kubernetes szolgáltatásairól a [Kubernetes-szolgáltatások dokumentációjában][kubernetes-services]olvashat bővebben.

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
[azure-lb-comparison]: ../load-balancer/concepts-limitations.md#skus
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
