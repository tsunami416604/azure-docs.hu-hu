---
title: A fürt automatikus skálázójának használata az Azure Kubernetes-szolgáltatásban (AKS)
description: Ismerje meg, hogyan használhatja a fürt automatikus skálázó automatikus méretezése a fürt alkalmazásigények nek egy Azure Kubernetes-fürt (AKS) fürtben.
services: container-service
ms.topic: article
ms.date: 07/18/2019
ms.openlocfilehash: 2baa64779713d0bac063e1d2c06107ba2ab291fb
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617548"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Fürt automatikus méretezése az Azure Kubernetes-szolgáltatás (AKS) alkalmazáskövetelményeinek megfelelően

Az Azure Kubernetes-szolgáltatás (AKS) alkalmazásigények nek való megfelelés érdekében előfordulhat, hogy módosítania kell a számítási feladatokat futó csomópontok számát. A fürt automatikus skálázó összetevője figyelheti a fürtben lévő podokat, amelyek erőforrás-korlátozások miatt nem ütemezhetők. Problémák észlelésekor a csomópontkészlet csomópontjaiban lévő csomópontok száma az alkalmazás igényének megfelelően növekszik. Csomópontok is rendszeresen ellenőrzik a futó podok hiánya, a csomópontok száma majd szükség szerint csökkent. Ez a képesség az AKS-fürt csomópontjainak automatikus fel- vagy leskálázására lehetővé teszi egy hatékony és költséghatékony fürt futtatását.

Ez a cikk bemutatja, hogyan engedélyezheti és kezelheti a fürt automatikus skálázóját egy AKS-fürtben.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk megköveteli, hogy az Azure CLI 2.0.76-os vagy újabb verzióját futtassa. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="limitations"></a>Korlátozások

A fürt automatikus skálázóját használó AKS-fürtök létrehozásakor és kezelésekor a következő korlátozások érvényesek:

* A HTTP-alkalmazás útválasztási bővítménye nem használható.

## <a name="about-the-cluster-autoscaler"></a>A fürt automatikus skálázója

A változó alkalmazásigényekhez való alkalmazkodáshoz, például a munkanap és az esti vagy hétvégi igények között, a fürtöknek gyakran szükségük van egy módra az automatikus méretezéshez. Az AKS-fürtök kétféleképpen méretezhetők:

* A **fürt automatikus skálázó** figyeli a podok, amelyek nem ütemezhetők a csomópontokon erőforrás-korlátozások miatt. A fürt ezután automatikusan növeli a csomópontok számát.
* A **horizontális pod automatikus skálázó** a Metrika-kiszolgáló egy Kubernetes-fürtben a podok erőforrásigényének figyeléséhez. Ha egy alkalmazásnak több erőforrásra van szüksége, a podok száma automatikusan növekszik az igény kielégítésére.

![A fürt automatikus skálázó és a vízszintes pod automatikus skálázó gyakran együtt, hogy támogassa a szükséges alkalmazás igények](media/autoscaler/cluster-autoscaler.png)

Mind a vízszintes pod automatikus skálázó és a fürt automatikus skálázó is csökkentheti a podok és csomópontok szükség szerint. A fürt automatikus skálázó csökkenti a csomópontok számát, ha egy ideig nem használt kapacitás. A fürt automatikus skálázóáltal eltávolítandó csomópontpodjai biztonságosan vannak ütemezve a fürt más részein. Előfordulhat, hogy a fürt automatikus skálázója nem tud leskálázni, ha a podok nem tudnak mozogni, például a következő esetekben:

* A pod közvetlenül jön létre, és nem támogatja a vezérlő objektum, például egy központi telepítés vagy replika készlet.
* A pod megszakítási költségvetés (PDB) túl korlátozó, és nem teszi lehetővé a podok száma alá egy bizonyos küszöbértéket.
* A pod olyan csomópont-választókat vagy affinitás-ellenességet használ, amelyek nem tarthatók tiszteletben, ha egy másik csomóponton vannak ütemezve.

Ha többet szeretne tudni arról, hogy a fürt automatikus skálázója hogyan nem képes leskálázni, olvassa [el a Milyen típusú podok akadályozhatják meg, hogy a fürt automatikus skálázója eltávolítson egy csomópontot?][autoscaler-scaledown]

A fürt automatikus skálázó indítási paramétereket használ a méretezési események és az erőforrás-küszöbértékek közötti időintervallumokhoz. Ezeket a paramétereket az Azure platform határozza meg, és jelenleg nem vannak elérhetővé téve az Ön számára. A fürt automatikus skálázó által használt paraméterekről a [Mik a fürt automatikus skálázó paraméterei.][autoscaler-parameters]

A fürt és a vízszintes pod automatikus skálázók együtt működhetnek együtt, és gyakran mindkettő telepítve van a fürtben. Kombinálva a horizontális pod automatikus skálázó az alkalmazásigény kielégítéséhez szükséges podok számának futtatására összpontosít. A fürt automatikus skálázó az ütemezett podok támogatásához szükséges csomópontok számának futtatására összpontosít.

> [!NOTE]
> A manuális méretezés le van tiltva a fürt automatikus skálázójának használatakor. Hagyja, hogy a fürt automatikus skálázó határozza meg a szükséges számú csomópontok. Ha manuálisan szeretné méretezni a fürtöt, [tiltsa le a fürt automatikus méretezőját.](#disable-the-cluster-autoscaler)

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>AKS-fürt létrehozása és a fürt automatikus méretezőjének engedélyezése

Ha AKS-fürtöt kell létrehoznia, használja az [aks create][az-aks-create] parancsot. A fürt automatikus skálázójának engedélyezéséhez és konfigurálásához a fürt csomópontkészletében használja a *--enable-cluster-autoscaler* paramétert, és adjon meg egy csomópontot *-min-count* és *--max-count*.

> [!IMPORTANT]
> A fürt automatikus skálázó egy Kubernetes-összetevő. Bár az AKS-fürt egy virtuálisgép-méretezési készletet használ a csomópontokhoz, ne manuálisan engedélyezze vagy sirassa a méretezési csoport automatikus skálázásának beállításait az Azure Portalon vagy az Azure CLI használatával. Hagyja, hogy a Kubernetes-fürt automatikus skálázó jaa a szükséges méretezési beállításokat kezelje. További információ: [Módosítható az AKS-erőforrások a csomópont erőforráscsoportban?](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group)

A következő példa létrehoz egy AKS-fürt egy virtuálisgép-méretezési készlet által támogatott egyetlen csomópontkészlettel. Emellett engedélyezi a fürt automatikus skálázóját a fürt csomópontkészletén, és legalább *1* és legfeljebb *3* csomópontot állít be:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

A fürt létrehozása és a fürt automatikus méretezőbeállításainak konfigurálása néhány percet vesz igénybe.

## <a name="change-the-cluster-autoscaler-settings"></a>A fürt automatikus méretezőbeállításainak módosítása

> [!IMPORTANT]
> Ha több csomópontkészlet van az AKS-fürtben, ugorjon az [automatikus skálázásra több ügynökkészlettel szakaszban.](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled) A több ügynökkészlettel rendelkező fürtöknek a parancskészlet et kell használniuk a `az aks nodepool` csomópontkészlet specifikus tulajdonságainak módosításához a helyett. `az aks`

Az előző lépésben egy AKS-fürt létrehozásához vagy egy meglévő csomópontkészlet frissítéséhez a *1*fürt automatikus skálázójának minimális csomópontszáma 1-re, a csomópontmaximális száma *pedig 3-ra*volt állítva. Az alkalmazás igényeinek módosítása, előfordulhat, hogy módosítania kell a fürt automatikus skálázó csomópont száma.

A csomópontszám módosításához használja az [aks update][az-aks-update] parancsot.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

A fenti példa frissíti a fürt automatikus skálázó az egycsomópontos készlet *myAKSCluster* legalább *1* és legfeljebb *5* csomópont.

> [!NOTE]
> Nem állíthat be magasabb minimális csomópontszámot, mint a csomópontkészlethez jelenleg beállított. Ha például a min-szám *1-re*van állítva, akkor a min-szám nem frissíthető *3-ra.*

Figyelje az alkalmazások és szolgáltatások teljesítményét, és állítsa be a fürt automatikus skálázó csomópontszámát a szükséges teljesítménynek megfelelően.

## <a name="using-the-autoscaler-profile"></a>Az automatikus méretező profil használata

A fürt automatikus skálázójának részletesebb részleteit is konfigurálhatja a fürtszintű automatikus méretező profil alapértelmezett értékeinek módosításával. Például egy leskálázási esemény történik, miután csomópontok kihasználatlan 10 perc után. Ha 15 percenként futtatott számítási feladatokat, érdemes lehet módosítani az automatikus skálázási profilt a használt csomópontok alatt 15 vagy 20 perc után. Ha engedélyezi a fürt automatikus skálázóját, a rendszer egy alapértelmezett profilt használ, hacsak nem ad meg különböző beállításokat. A fürt automatikus méretező profilja a következő beállításokkal rendelkezik, amelyek frissíthetők:

| Beállítás                          | Leírás                                                                              | Alapértelmezett érték |
|----------------------------------|------------------------------------------------------------------------------------------|---------------|
| beszkaval                    | A fürt újraértékelésének gyakran fel- és leskálázása                                    | 10 másodperc    |
| lekicsinyítés-késleltetés-hozzáadása után       | Mennyi ideig a skála felskálázása után folytatódik a leskálázási értékelés                               | 10 perc    |
| lekicsinylés-késleltetés-törlés után    | Mennyi ideig folytatódik a csomópont törlése után, amely lekicsinyítette a kiértékelést?                          | beszkaval |
| lekicsinyített-késleltetés-hiba után   | Mennyi ideig a lekicsinylési hiba után folytatódik a lekicsinylési kiértékelés?                     | 3 perc     |
| kicsinyített-nem szükséges idő         | Mennyi ideig kell egy csomópontot szükségtelennek lennie ahhoz, hogy jogosult legyen a leskálázásra?                  | 10 perc    |
| lekicsinylés-felkészületlen idő          | Mennyi ideig nem kell egy nem kész csomópontot szükségtelennek lennie ahhoz, hogy jogosult legyen a leskálázásra?         | 20 perc    |
| lekicsinyítés-kihasználtság-küszöbérték | A csomópont kihasználtsági szintje, amely a kért erőforrások és a kapacitás hányadosaként van meghatározva, amely alatt egy csomópont leskálázásnak tekinthető | 0,5 |
| max-kecses-felmondás-sec     | A fürt automatikus skálázója legfeljebb másodpercek alatt várakozik a pod leállítására, amikor megpróbál csökkenteni egy csomópontot. | 600 másodperc   |
| egyenleg-hasonló-csomópont-csoportok | Hasonló csomópontkészletek észlelése és a köztük lévő csomópontok számának egyensúlya | hamis |

> [!IMPORTANT]
> A fürt automatikus skálázó profilja a fürt automatikus skálázóját használó összes csomópontkészletre hatással van. Csomópontkészletenként nem állítható be automatikus méretező profil.

### <a name="install-aks-preview-cli-extension"></a>Az aks-preview CLI bővítmény telepítése

A fürt automatikus méretezőbeállítási profiljának beállításához az *aks-preview* CLI bővítmény 0.4.30-as vagy újabb verziójára van szükség. Telepítse az *aks-preview* Azure CLI bővítményt az [az bővítmény hozzáadása][az-extension-add] paranccsal, majd ellenőrizze az elérhető frissítéseket az az extension [update][az-extension-update] paranccsal:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="set-the-cluster-autoscaler-profile-on-an-existing-aks-cluster"></a>A fürt automatikus méretezőprofiljának beállítása meglévő AKS-fürtön

Használja az [aks update][az-aks-update] parancsot a *fürt-autoscaler-profile* paraméterrel a fürt automatikus méretezőprofiljának beállításához a fürtön. A következő példa a bekövetkező betekintési időközt 30-asként állítja be a profilban.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile scan-interval=30s
```

Ha engedélyezi a fürt automatikus skálázóját a fürt csomópontkészletein, ezek a fürtök a fürt automatikus méretező profilját is használni fogják. Például:

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

> [!IMPORTANT]
> A fürt automatikus méretezőprofiljának beállításakor a fürt automatikus skálázóval rendelkező meglévő csomópontkészletek azonnal elkezdik használni a profilt.

### <a name="set-the-cluster-autoscaler-profile-when-creating-an-aks-cluster"></a>AKS-fürt létrehozásakor a fürt automatikus méretezőprofiljának beállítása

A *fürt automatikus skálázási profil* paraméterét is használhatja a fürt létrehozásakor. Például:

```azurecli-interactive
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3 \
  --cluster-autoscaler-profile scan-interval=30s
```

A fenti parancs létrehoz egy AKS-fürtöt, és a betekintési időközt 30 másodpercként határozza meg a fürtszintű automatikus méretező profilhoz. A parancs azt is lehetővé teszi, hogy a fürt automatikus skálázó a kezdeti csomópont készlet, beállítja a minimális csomópontszáma 1 és a maximális csomópont száma 3.

### <a name="reset-cluster-autoscaler-profile-to-default-values"></a>Fürt automatikus méretezőprofiljának visszaállítása az alapértelmezett értékekre

Az [aks update][az-aks-update] paranccsal alaphelyzetbe állíthatja a fürt automatikus méretezőprofilját a fürtön.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile ""
```

## <a name="disable-the-cluster-autoscaler"></a>A fürt automatikus skálázójának letiltása

Ha a továbbiakban nem kívánja használni a fürt automatikus skálázóját, letilthatja azt az [aks update][az-aks-update] paranccsal, megadva a *--disable-cluster-autoscaler* paramétert. A csomópontok nem törlődnek, ha a fürt automatikus skálázója le van tiltva.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

A fürt automatikus méretezőjének letiltása után manuálisan skálázhatja az [aks méretezési][az-aks-scale] parancsot. Ha a horizontális pod automatikus skálázó, hogy a szolgáltatás továbbra is fut a fürt automatikus skálázó le van tiltva, de a podok a végén nem lehet ütemezni, ha az összes csomópont-erőforrások használatban vannak.

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>Letiltott fürt automatikus méretezőjének újbóli engedélyezése

Ha újra engedélyezni szeretné a fürt automatikus skálázóját egy meglévő fürtön, újra engedélyezheti azt az [aks update][az-aks-update] paranccsal, megadva a *--enable-cluster-autoscaler*, *--min-count*és *--max-count* paramétereket.

## <a name="retrieve-cluster-autoscaler-logs-and-status"></a>Fürt automatikus méretezőnaplóinak és állapotának beolvasása

Az automatikus skálázó események diagnosztizálásához és hibakereséséhez a naplók és az állapot az automatikus skálázó bővítményből olvashatók le.

Az AKS kezeli a fürt automatikus skálázóját az Ön nevében, és futtatja azt a felügyelt vezérlősíkon. A fő csomópontnaplókat úgy kell konfigurálni, hogy ennek eredményeképpen megtekinthetők legyenek.

Konfigurálása naplók leküldéses a fürt automatikus skálázó a Log Analytics, kövesse az alábbi lépéseket.

1. Állítson be egy szabályt a diagnosztikai naplók leküldésefürt-automatikus skálázó naplók a Log Analytics. [Az utasításokat itt részletezve,](https://docs.microsoft.com/azure/aks/view-master-logs#enable-diagnostics-logs) `cluster-autoscaler` győződjön meg róla, hogy bejelöli a négyzetet, amikor kiválasztja a "Naplók" beállításait.
1. Kattintson a "Naplók" szakaszra a fürtön az Azure Portalon keresztül.
1. Adja meg a következő példalekérdezést a Log Analytics szolgáltatásba:

```
AzureDiagnostics
| where Category == "cluster-autoscaler"
```

A következő példához hasonló naplókat kell látnia, feltéve, hogy vannak lekért naplók.

![Log Analytics-naplók](media/autoscaler/autoscaler-logs.png)

A fürt automatikus méretezője egy konfigurációs `cluster-autoscaler-status`térképállapotát is kiírja. A naplók beolvasásához hajtsa végre a következő `kubectl` parancsot. A fürt automatikus skálázójával konfigurált csomópontkészletek hez a program állapotjelentést fog jelenteni.

```
kubectl get configmap -n kube-system cluster-autoscaler-status -o yaml
```

Ha többet szeretne megtudni arról, hogy mi van naplózva az automatikus skálázóból, olvassa el a Gyakori kérdéseket a [Kubernetes/autoscaler GitHub projektről.](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why)

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>A fürt automatikus skálázójának használata több csomópontkészlet engedélyezésével

A fürt automatikus skálázó használható együtt [több csomópont készletek](use-multiple-node-pools.md) engedélyezve. A dokumentum ból megtudhatja, hogyan engedélyezheti a több csomópontkészletet, és hogyan adhat hozzá további csomópontkészleteket egy meglévő fürthöz. Ha mindkét szolgáltatást együtt használja, engedélyezi a fürt automatikus skálázóját a fürt minden egyes csomópontkészletén, és mindegyiknek egyedi automatikus skálázási szabályokat adhat át.

Az alábbi parancs feltételezi, hogy a dokumentum korábbi [utasításait](#create-an-aks-cluster-and-enable-the-cluster-autoscaler) követte, és egy meglévő csomópontkészlet maximális számát *3-ról* *5-re*szeretné frissíteni. Az [aks nodepool update][az-aks-nodepool-update] paranccsal frissítheti egy meglévő csomópontkészlet beállításait.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

A fürt automatikus skálázó letiltható [az aks nodepool frissítés][az-aks-nodepool-update] és a `--disable-cluster-autoscaler` paraméter átadása.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --disable-cluster-autoscaler
```

Ha újra engedélyezni szeretné a fürt automatikus skálázóját egy meglévő fürtön, újra engedélyezheti azt az [aks nodepool update][az-aks-nodepool-update] paranccsal, megadva a *--enable-cluster-autoscaler*, *--min-count*és *--max-count* paramétereket.

## <a name="next-steps"></a>További lépések

Ez a cikk bemutatja, hogyan lehet automatikusan méretezni az AKS-csomópontok számát. A vízszintes pod automatikus skálázó automatikusan módosíthatja az alkalmazást futtató podok számát. A vízszintes pod automatikus skálázó használatának lépéseit az [Alkalmazások méretezése az AKS-ben című témakörben][aks-scale-apps]található.

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
