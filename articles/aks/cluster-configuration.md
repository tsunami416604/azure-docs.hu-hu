---
title: Fürtkonfiguráció az Azure Kubernetes Servicesben (ak)
description: Megtudhatja, hogyan konfigurálhat fürtöt az Azure Kubernetes szolgáltatásban (ak)
services: container-service
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: f1329aa056e8d1db951e01555634cf1ea709608b
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86252011"
---
# <a name="configure-an-aks-cluster"></a>AKS-fürt konfigurálása

Az AK-fürtök létrehozásának részeként előfordulhat, hogy a fürt konfigurációját testre kell szabnia az igényeinek megfelelően. Ez a cikk néhány lehetőséget mutat be az AK-fürt testreszabásához.

## <a name="os-configuration-preview"></a>Operációs rendszer konfigurációja (előzetes verzió)

Az AK mostantól támogatja az Ubuntu 18,04-as verziót a Node operációs rendszerként (OS) az előzetes verzióban. Az előzetes verzió ideje alatt az Ubuntu 16,04 és az Ubuntu 18,04 egyaránt elérhető.

> [!IMPORTANT]
> A Kubernetes v 1.18-es vagy újabb verziójában létrehozott Node-készletek egy szükséges `AKS Ubuntu 18.04` csomópont-rendszerképhez. A 1,18-nál kisebb, támogatott Kubernetes-verzióban található csomópont `AKS Ubuntu 16.04` -készletek csomóponti képként jelennek meg, de `AKS Ubuntu 18.04` a csomópont-készlet Kubernetes verziójának frissítése a v 1.18-ra vagy újabbra történik.
> 
> Erősen ajánlott az AK Ubuntu 18,04 Node-készletekben lévő munkaterhelések tesztelése az 1,18-es vagy újabb fürtök használata előtt. Olvassa el az [Ubuntu 18,04 Node-készletek tesztelését](#use-aks-ubuntu-1804-existing-clusters-preview)ismertető témakört.

A következő erőforrásokat kell telepítenie:

- [Az Azure CLI, a][azure-cli-install]2.2.0 vagy újabb verzió
- Az AK – előzetes verziójú 0.4.35 bővítmény

A következő Azure CLI-parancsokkal telepítheti a 0.4.35-bővítményt vagy újabb verziót:

```azurecli
az extension add --name aks-preview
az extension list
```

Regisztrálja a `UseCustomizedUbuntuPreview` szolgáltatást:

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

Több percet is igénybe vehet, amíg az állapot **regisztrálva**jelenik meg. A regisztrációs állapotot az az [Feature List](/cli/azure/feature?view=azure-cli-latest#az-feature-list) parancs használatával tekintheti meg:

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Ha az állapot regisztrálva értékre van állítva, frissítse az `Microsoft.ContainerService` erőforrás-szolgáltató regisztrációját az az [Provider Register](/cli/azure/provider?view=azure-cli-latest#az-provider-register) paranccsal:

```azurecli
az provider register --namespace Microsoft.ContainerService
```

### <a name="use-aks-ubuntu-1804-on-new-clusters-preview"></a>Az AK Ubuntu 18,04 használata új fürtökön (előzetes verzió)

Konfigurálja a fürtöt az Ubuntu 18,04 használatára a fürt létrehozásakor. Az `--aks-custom-headers` Ubuntu 18,04 alapértelmezett operációs rendszerként való beállításához használja a jelzőt.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Ha az AK Ubuntu 16,04 rendszerképpel rendelkező fürtöket szeretne létrehozni, ezt az egyéni címke kihagyásával teheti meg `--aks-custom-headers` .

### <a name="use-aks-ubuntu-1804-existing-clusters-preview"></a>AK Ubuntu 18,04 meglévő fürtök használata (előzetes verzió)

Konfigurálja az új csomópont-készletet az Ubuntu 18,04 használatára. Használja a `--aks-custom-headers` jelzőt az Ubuntu 18,04 alapértelmezett operációs rendszerként való beállításához az adott csomópont-készlethez.

```azurecli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Ha a Node-készleteket az AK Ubuntu 16,04-lemezképpel szeretné létrehozni, ezt az egyéni címke kihagyásával teheti meg `--aks-custom-headers` .


## <a name="container-runtime-configuration-preview"></a>Tároló futásidejű konfigurációja (előzetes verzió)

A Container Runtime olyan szoftver, amely tárolókat hajt végre, és a tároló lemezképeit kezeli egy csomóponton. A futtatókörnyezet segíti az absztrakt el-hívásokat vagy az operációs rendszer (OS) specifikus funkcióit a tárolók Linux vagy Windows rendszeren való futtatásához. A mai AK jelenleg a [Moby](https://mobyproject.org/) (upstream Docker) szolgáltatást használja tároló-futtatókörnyezetként. 
    
![Docker ICC](media/cluster-configuration/docker-cri.png)

[`Containerd`](https://containerd.io/)a egy [OCI](https://opencontainers.org/) (Open Container Initiative) szabványnak megfelelő alapszintű tároló-futtatókörnyezet, amely a szükséges funkciók minimális készletét biztosítja a tárolók végrehajtásához és a lemezképek egy csomóponton való kezeléséhez. A Felhőbeli natív számítási alaprendszer (CNCF) a 2017 márciusában lett [adományozva](https://www.cncf.io/announcement/2017/03/29/containerd-joins-cloud-native-computing-foundation/) . A jelenlegi Moby-verzió, amelyet az AK jelenleg használ, és amely a `containerd` fent látható módon épül fel. 

A tárolón alapuló csomópont-és Node-készletek használata helyett a `dockershim` kubelet közvetlenül az `containerd` ICC (Container Runtime Interface) beépülő modulon keresztül fog kommunikálni, és a DOCKer ICC-implementációhoz képest eltávolítja a folyamat további ugrásait. Így jobb lesz a pod indítási késés és kevesebb erőforrás (CPU és memória) használata.

Az `containerd` AK-csomópontok használata esetén a pod indítási késése növeli a tároló futtatókörnyezetét, és csökkenti a csomópontok erőforrás-felhasználását. Ezek a Újdonságok olyan új architektúrán keresztül érhetők el, ahol `containerd` a kubelet közvetlenül az ICC beépülő modulon keresztül beszél a Moby/Docker architektúrán kubelet `dockershim` `containerd` , így a folyamat további ugrásokkal is rendelkezik.

![Docker ICC](media/cluster-configuration/containerd-cri.png)

`Containerd`a kubernetes minden egyes, az AK-ban található verziójában működik, és minden, a v 1.10-es kubernetes-verzióban, és támogatja az összes kubernetes-és AK-funkciót.

> [!IMPORTANT]
> Miután `containerd` általánosan elérhetővé válik az AK-on, az alapértelmezett és csak az új fürtökön elérhető tároló-futtatókörnyezethez választható lehetőség lesz. A korábbi támogatott verziókon továbbra is használhatja a Moby nodepools és a fürtöket, amíg azok nem támogatják a támogatást. 
> 
> Javasoljuk, hogy a munkaterheléseket a `containerd` csomópont-készleteken tesztelje, mielőtt frissítené vagy új fürtöket hozna létre ezzel a tároló-futtatókörnyezettel.

### <a name="use-containerd-as-your-container-runtime-preview"></a>Használat `containerd` tároló-futtatókörnyezetként (előzetes verzió)

A következő előfeltételek szükségesek:

- [Az Azure CLI, a][azure-cli-install]2.8.0 vagy újabb verzió telepítve
- Az AK-előzetes verziójú bővítmény 0.4.53 vagy újabb verziója
- A `UseCustomizedContainerRuntime` szolgáltatás jelzője regisztrálva
- A `UseCustomizedUbuntuPreview` szolgáltatás jelzője regisztrálva

A következő Azure CLI-parancsokkal telepítheti a 0.4.53-bővítményt vagy újabb verziót:

```azurecli
az extension add --name aks-preview
az extension list
```

A `UseCustomizedContainerRuntime` és szolgáltatások regisztrálása `UseCustomizedUbuntuPreview` :

```azurecli
az feature register --name UseCustomizedContainerRuntime --namespace Microsoft.ContainerService
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService

```

Több percet is igénybe vehet, amíg az állapot **regisztrálva**jelenik meg. A regisztrációs állapotot az az [Feature List](/cli/azure/feature?view=azure-cli-latest#az-feature-list) parancs használatával tekintheti meg:

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedContainerRuntime')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Ha az állapot regisztrálva értékre van állítva, frissítse az `Microsoft.ContainerService` erőforrás-szolgáltató regisztrációját az az [Provider Register](/cli/azure/provider?view=azure-cli-latest#az-provider-register) paranccsal:

```azurecli
az provider register --namespace Microsoft.ContainerService
```  

### <a name="use-containerd-on-new-clusters-preview"></a>Használat `containerd` új fürtökön (előzetes verzió)

Konfigurálja a fürtöt, amelyet `containerd` a fürt létrehozásakor használ. Használja a `--aks-custom-headers` jelzőt a `containerd` tároló futtatókörnyezetként való beállításhoz.

> [!NOTE]
> A `containerd` futtatókörnyezetet csak a csomópontok és csomópont-készletek támogatják az AK Ubuntu 18,04-rendszerkép használatával.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804,ContainerRuntime=containerd
```

Ha a Moby (Docker) futtatókörnyezettel rendelkező fürtöket szeretne létrehozni, ezt az egyéni címke kihagyásával teheti meg `--aks-custom-headers` .

### <a name="use-containerd-on-existing-clusters-preview"></a>Használat `containerd` meglévő fürtökön (előzetes verzió)

Konfigurálja a használni kívánt új csomópont-készletet `containerd` . Használja a `--aks-custom-headers` jelzőt az `containerd` adott csomópont-készlet futtatókörnyezetének beállításához.

```azurecli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804,ContainerRuntime=containerd
```

Ha a Moby (Docker) futtatókörnyezettel rendelkező csomópont-készleteket szeretne létrehozni, ezt az egyéni címke kihagyásával teheti meg `--aks-custom-headers` .


### <a name="containerd-limitationsdifferences"></a>`Containerd`korlátozások/különbségek

* `containerd`A tároló-futtatókörnyezet használatához a Base os Ubuntu 18,04-et kell használnia az alap operációsrendszer-rendszerképként.
* Amíg a Docker eszközkészlet továbbra is megtalálható a csomópontokon, a Kubernetes `containerd` a tároló futtatókörnyezetét használja. Ezért mivel a Moby/Docker nem kezeli a Kubernetes által létrehozott tárolókat a csomópontokon, nem tudja megtekinteni és használni a tárolókat a Docker-parancsokkal (például `docker ps` ) vagy a Docker API-val.
* A `containerd` (z) esetében javasoljuk, hogy [`crictl`](https://kubernetes.io/docs/tasks/debug-application-cluster/crictl) a DOCKer parancssori felület helyett a helyettesítő CLI-t használja a hüvelyek, tárolók és a Kubernetes-csomópontokon lévő tároló-lemezképek (például:) **hibaelhárításához** `crictl ps` . 
   * Nem biztosítja a Docker parancssori felületének teljes funkcionalitását. Csak hibaelhárításra szolgál.
   * `crictl`a a tárolók kubernetes, például a hüvelyek, például a hüvelyek és a hasonló fogalmak megjelenítését kínálja.
* `Containerd`beállítja a naplózást a szabványosított `cri` naplózási formátum használatával (amely eltér a Docker JSON-illesztőprogramtól származó aktuálisan lekérdezett adatoktól). A naplózási megoldásnak támogatnia kell a `cri` naplózási formátumot (például [a tárolók Azure monitor](../azure-monitor/insights/container-insights-enable-new-cluster.md))
* Már nem fér hozzá a Docker-motorhoz, `/var/run/docker.sock` vagy használhatja a Docker-in-Docker-t (DinD).
  * Ha jelenleg az alkalmazás naplófájljait kinyeri vagy a Docker-motorból figyeli az adatait, akkor használjon hasonló [Azure monitor a tárolók](../azure-monitor/insights/container-insights-enable-new-cluster.md) helyett. Emellett az AK nem támogatja a sávon kívüli parancsok futtatását az ügynök csomópontjain, amelyek instabilitást okozhatnak.
  * A fenti módszerekkel még a Moby/Docker használatakor is felhasználhatja a lemezképek kiépítését, és közvetlenül kihasználhatja a Docker-motort a fenti módszerek segítségével. A Kubernetes nem teljesen [tisztában van a](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/) felhasznált erőforrásokkal, és ezek a módszerek számos olyan problémát [jelentenek, mint](https://securityboulevard.com/2018/05/escaping-the-whale-things-you-probably-shouldnt-do-with-docker-part-1/)például a.
* Rendszerképek készítése – a rendszerképek létrehozásához ajánlott módszer az [ACR-feladatok](../container-registry/container-registry-quickstart-task-cli.md)használata. Alternatív módszer a fürt biztonságosabb, például a [Docker buildx](https://github.com/docker/buildx)használata.

## <a name="generation-2-virtual-machines-preview"></a>2. generációs virtuális gépek (előzetes verzió)

Az Azure támogatja a [2. generációs (Gen2) virtuális gépeket (VM)](../virtual-machines/windows/generation-2.md). A 2. generációs virtuális gépek támogatják az 1. generációs virtuális gépeken (Gen1) nem támogatott főbb funkciókat. A szolgáltatások közé tartozik a megnövekedett memória, az Intel Software Guard Extensions (Intel SGX ENKLÁVÉHOZ) és a virtualizált állandó memória (vPMEM).

A 2. generációs virtuális gépek az új UEFI-alapú rendszerindítási architektúrát használják az 1. generációs virtuális gépek által használt BIOS-alapú architektúra helyett.
A Gen2 virtuális gépeket csak a megadott SKU-és méretek támogatják. Tekintse át a [támogatott méretek listáját](../virtual-machines/windows/generation-2.md#generation-2-vm-sizes), és ellenőrizze, hogy az SKU támogatja vagy igényli-e a Gen2.

Emellett nem minden virtuálisgép-lemezkép támogatja a Gen2-t, az AK-Gen2 virtuális gépeken az új [AK Ubuntu 18,04-lemezképet](#os-configuration-preview)fogja használni. Ez a rendszerkép az összes Gen2 SKU-t és méretet támogatja.

Ha az előzetes verzióban szeretné használni a Gen2 virtuális gépeket, a következőkre lesz szüksége:
- A `aks-preview` CLI-bővítmény telepítve van.
- A `Gen2VMPreview` szolgáltatás jelzője regisztrálva van.

Regisztrálja a `Gen2VMPreview` szolgáltatást:

```azurecli
az feature register --name Gen2VMPreview --namespace Microsoft.ContainerService
```

Több percet is igénybe vehet, amíg az állapot **regisztrálva**jelenik meg. A regisztrációs állapotot az az [Feature List](/cli/azure/feature?view=azure-cli-latest#az-feature-list) parancs használatával tekintheti meg:

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/Gen2VMPreview')].{Name:name,State:properties.state}"
```

Ha az állapot regisztrálva értékre van állítva, frissítse az `Microsoft.ContainerService` erőforrás-szolgáltató regisztrációját az az [Provider Register](/cli/azure/provider?view=azure-cli-latest#az-provider-register) paranccsal:

```azurecli
az provider register --namespace Microsoft.ContainerService
```

A következő Azure CLI-parancsokkal telepítheti az AK-előnézeti CLI-bővítményt:

```azurecli
az extension add --name aks-preview
```

A következő Azure CLI-parancsokkal frissítheti az AK-előnézeti CLI-bővítményt:

```azurecli
az extension update --name aks-preview
```

### <a name="use-gen2-vms-on-new-clusters-preview"></a>Gen2 virtuális gépek használata új fürtökön (előzetes verzió)
Konfigurálja úgy a fürtöt, hogy Gen2 virtuális gépeket használjon a kiválasztott SKU-hoz a fürt létrehozásakor. A `--aks-custom-headers` jelzővel beállíthatja, hogy a Gen2 a virtuális gép létrehozásakor egy új fürtön legyen.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

Ha az 1. generációs (Gen1) virtuális gépek használatával szeretne normál fürtöt létrehozni, ezt az egyéni címke kihagyása mellett teheti meg `--aks-custom-headers` . Azt is megteheti, hogy az alábbi módon további Gen1 vagy Gen2 virtuális gépeket ad hozzá.

### <a name="use-gen2-vms-on-existing-clusters-preview"></a>Gen2 virtuális gépek használata meglévő fürtökön (előzetes verzió)
Konfiguráljon egy új csomópont-készletet a Gen2 virtuális gépek használatára. A `--aks-custom-headers` jelzővel állítsa be a Gen2 virtuálisgép-generációként az adott csomópont-készlethez.

```azure-cli
az aks nodepool add --name gen2 --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

Ha normál Gen1-csomópont-készleteket szeretne létrehozni, ezt az egyéni címke kihagyása mellett teheti meg `--aks-custom-headers` .

## <a name="custom-resource-group-name"></a>Egyéni erőforráscsoport neve

Ha Azure Kubernetes Service-fürtöt helyez üzembe az Azure-ban, a rendszer létrehoz egy második erőforráscsoportot a munkavégző csomópontokhoz. Alapértelmezés szerint az AK a csomópont erőforráscsoportot nevezi el `MC_resourcegroupname_clustername_location` , de a saját nevét is megadhatja.

A saját erőforráscsoport-név megadásához telepítse az AK – előzetes verziójú Azure CLI-bővítmény 0.3.2 vagy újabb verzióját. Az Azure CLI használatával a `--node-resource-group` parancs paraméterével `az aks create` adhatja meg az erőforráscsoport egyéni nevét. Ha Azure Resource Manager sablont használ egy AK-fürt üzembe helyezéséhez, megadhatja az erőforráscsoport nevét a `nodeResourceGroup` tulajdonság használatával.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

Az Azure erőforrás-szolgáltató automatikusan létrehozza a másodlagos erőforráscsoportot a saját előfizetésében. Az egyéni erőforráscsoport neve csak a fürt létrehozásakor adható meg. 

A csomópont-erőforráscsoport használata során ne feledje, hogy a következők nem használhatók:

- Válasszon egy meglévő erőforráscsoportot a csomópont-erőforráscsoport számára.
- Eltérő előfizetést kell megadni a csomópont-erőforráscsoport számára.
- Módosítsa a csomópont-erőforráscsoport nevét a fürt létrehozása után.
- A csomópont erőforráscsoporthoz tartozó felügyelt erőforrások nevének megadása.
- Módosíthatja vagy törölheti az Azure-ban létrehozott, felügyelt erőforrások címkéit a csomópont-erőforráscsoporton belül.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan `Kured` [alkalmazhatja a biztonsági és a kernel-frissítéseket a fürt Linux-csomópontjaira](node-updates-kured.md) .
- Lásd: [Azure Kubernetes Service-(ak-) fürt frissítése](upgrade-cluster.md) , amelyből megtudhatja, hogyan frissítheti a fürtöt a Kubernetes legújabb verziójára.
- További információ [ `containerd` és Kubernetes](https://kubernetes.io/blog/2018/05/24/kubernetes-containerd-integration-goes-ga/)
- Tekintse meg az [AK-val kapcsolatos gyakori kérdések](faq.md) listáját, ahol válaszokat talál a gyakori AK-kérdésekre.


<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
