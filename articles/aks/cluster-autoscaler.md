---
title: A fürt automéretező használata az Azure Kubernetes szolgáltatásban (ak)
description: Ismerje meg, hogyan használható a fürt automatikus méretezése, hogy a fürt automatikusan méretezhető legyen az alkalmazások igényeinek kielégítéséhez egy Azure Kubernetes-szolgáltatásbeli (ak-beli) fürtben.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 07/18/2019
ms.author: mlearned
ms.openlocfilehash: 9d7a404b767d3975cefd55e1db8487fbb45042e2
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174189"
---
# <a name="preview---automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Előzetes verzió – a fürt automatikus méretezése az alkalmazások igényeinek kielégítéséhez az Azure Kubernetes Service-ben (ak)

Az alkalmazások az Azure Kubernetes szolgáltatásban (ak) való megtartásához szükség lehet a számítási feladatokat futtató csomópontok számának módosítására. A fürt autoscaleer összetevője megtekintheti a fürtben lévő hüvelyeket, amelyek erőforrás-korlátozások miatt nem ütemezhetők. Problémák észlelése esetén a Node-készlet csomópontjainak száma megnő az alkalmazás iránti igény kielégítése érdekében. A csomópontokat a futó hüvelyek hiánya is rendszeresen ellenőrzi, a csomópontok számával azonban szükség szerint csökken. Ez a lehetőség lehetővé teszi a csomópontok számának automatikus méretezését az AK-fürtben, így hatékony és költséghatékony fürtöt futtathat.

Ez a cikk bemutatja, hogyan engedélyezheti és kezelheti a fürt automéretezőjét egy AK-fürtben. A fürt automéretezőjét csak előzetes verzióban kell tesztelni az AK-fürtökön.

> [!IMPORTANT]
> Az AK előzetes verziójának funkciói önkiszolgáló opt-in. Az előzetes verziók az "adott állapotban" és "ahogy elérhető" módon vannak kizárva, és ki vannak zárva a szolgáltatói szerződésekből és a korlátozott jótállásból. A következő részben az ügyfélszolgálat a lehető leghatékonyabban foglalkozik. Ezért ezeket a funkciókat nem éles használatra szánták. További részletekért tekintse meg a következő támogatási cikkeket:
>
> * [AK-támogatási szabályzatok][aks-support-policies]
> * [Azure-támogatás – gyakori kérdések][aks-faq]

## <a name="before-you-begin"></a>Előzetes teendők

Ehhez a cikkhez az Azure CLI 2.0.65 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

### <a name="install-aks-preview-cli-extension"></a>Az Kabai szolgáltatás telepítése – előnézeti CLI-bővítmény

A fürt automéretező használatához a CLI *-előnézet CLI-* bővítmény 0.4.12 vagy újabb verziójára van szükség. Telepítse az *AK – előzetes* verzió Azure CLI bővítményét az az [Extension Add][az-extension-add] paranccsal, majd az az [Extension Update][az-extension-update] paranccsal keresse meg a rendelkezésre álló frissítéseket:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="limitations"></a>Korlátozások

A fürt automéretezőjét használó AK-fürtök létrehozásakor és kezelésekor a következő korlátozások érvényesek:

* A HTTP-alkalmazás útválasztási bővítménye nem használható.

## <a name="about-the-cluster-autoscaler"></a>Tudnivalók a fürt autoskálázásáról

Ha módosítani szeretné az alkalmazási igényeket, például a munkanapokat és az estét, vagy egy hétvégét, a fürtöknek gyakran szükségük van az automatikus skálázásra. Az AK-fürtök kétféleképpen méretezhetők a következőképpen:

* A **fürt autoskálázása** figyeli a hüvelyeket, amelyek erőforrás-megkötések miatt nem ütemezhetők a csomópontokon. A fürt ezután automatikusan növeli a csomópontok számát.
* A **horizontális Pod automéretező** a Kubernetes-fürt metrikák kiszolgálóját használja a hüvelyek erőforrás-igényének figyeléséhez. Ha egy alkalmazásnak több erőforrásra van szüksége, a hüvelyek száma automatikusan növekszik az igény kielégítése érdekében.

![A fürt autoskálázása és a horizontális Pod autoskálázás gyakran együtt működik a szükséges alkalmazási igények támogatásához](media/autoscaler/cluster-autoscaler.png)

A horizontális Pod autoskálázás és a fürt autoskálázása is csökkentheti a hüvelyek és a csomópontok számát igény szerint. A fürt autoskálázása csökkenti a csomópontok számát, ha egy adott időszakban nem használták fel a rendelkezésre álló kapacitást. A fürt automéretezője által eltávolított csomóponton lévő hüvelyek biztonságosan vannak ütemezve a fürtben máshol. Előfordulhat, hogy a fürt autoskálázása nem tud leméretezni, ha a hüvely nem helyezhető át, például a következő helyzetekben:

* A közvetlenül létrehozott és nem egy vezérlő objektum, például egy központi telepítés vagy egy replikakészlet által támogatott Pod.
* A pod-megszakadási költségvetés (PDB) túl korlátozó, és nem teszi lehetővé, hogy a hüvelyek száma egy bizonyos küszöbérték alá kerüljön.
* A pod olyan csomópont-választókat vagy affinitást használ, amelyeket nem lehet tiszteletben venni, ha egy másik csomóponton van ütemezve.

Ha többet szeretne megtudni arról, hogy a fürt automéretezője miért nem tudja méretezni a méretezést, tekintse meg, hogy [milyen típusú hüvelyek akadályozhatják a fürt autoskálázását a csomópontok eltávolításával?][autoscaler-scaledown]

A fürt automatikus méretezése indítási paramétereket használ olyan dolgokhoz, mint a méretezési események és az erőforrás-küszöbértékek közötti időintervallumok. Ezeket a paramétereket az Azure platform határozza meg, és jelenleg nem lehet módosítani. További információ arról, hogy milyen paramétereket használ a fürt autoskálázása. lásd: [Mi a fürt autoskálázási paraméterei?][autoscaler-parameters].

A fürt és a horizontális Pod automéretezők együtt is működhetnek, és a fürtben gyakran is üzembe helyezhetők. Kombinálva a horizontális Pod automéretező az alkalmazás igényének kielégítéséhez szükséges hüvelyek számának futtatására koncentrál. A fürt autoskálázása az ütemezett hüvelyek támogatásához szükséges csomópontok számának futtatására fókuszál.

> [!NOTE]
> A manuális skálázás le van tiltva a fürt automatikus méretezésének használatakor. Hagyja, hogy a fürt autoskálázása határozza meg a csomópontok szükséges számát. Ha manuálisan szeretné méretezni a fürtöt, [Tiltsa le a fürt automéretezőjét](#disable-the-cluster-autoscaler).

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>AK-fürt létrehozása és a fürt autoskálázásának engedélyezése

Ha AK-fürtöt kell létrehoznia, használja az az [AK Create][az-aks-create] parancsot. A fürthöz tartozó, a fürthöz tartozó autoskálázás engedélyezéséhez és konfigurálásához használja a *--enable-cluster-autoskálázási* paramétert, és adja meg a csomópont *--min-Count* és a *--Max-Count*értékét.

> [!IMPORTANT]
> A fürt automéretezője egy Kubernetes-összetevő. Bár az AK-fürt egy virtuálisgép-méretezési készletet használ a csomópontokhoz, ne manuálisan engedélyezzen vagy szerkessze a méretezési csoport autoskálázásának beállításait a Azure Portal vagy az Azure CLI használatával. Lehetővé teszi, hogy a Kubernetes-fürt autoskálázása felügyelje a szükséges méretezési beállításokat. További információ: [módosíthatom a csomópont-ERŐFORRÁSCSOPORT AK-erőforrásait?](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group)

A következő példa létrehoz egy AK-fürtöt egyetlen, virtuálisgép-méretezési csoporttal támogatott egycsomópontos készlettel. Emellett lehetővé teszi a fürthöz tartozó automéretezőt a fürt csomópont-készletén, és legalább *1* és legfeljebb *3* csomópontot állít be:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --enable-vmss \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

> [!NOTE]
> Ha a *--kubernetes-Version* értéket ad meg `az aks create`-nél, a verziónak meg kell felelnie vagy meg kell haladnia a szükséges minimális verziószámot, amelyet az előzőekben ismertetett [lépések szakaszban talál](#before-you-begin) .

A fürt létrehozása és a fürt autoskálázási beállításainak konfigurálása néhány percet vesz igénybe.

## <a name="change-the-cluster-autoscaler-settings"></a>A fürt autoskálázási beállításainak módosítása

> [!IMPORTANT]
> Ha az előfizetésben engedélyezve van *több ügynök-készlet* funkció, ugorjon az [autoskálázás több ügynök-készlettel szakaszra](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled). A több ügynököt tartalmazó fürtök esetében a `az aks nodepool` parancs használatára van szükség, hogy a csomópont-készletre vonatkozó tulajdonságok a `az aks` helyett legyenek módosítva. Az alábbi utasítások feltételezik, hogy nem engedélyezte a több csomópontos készletet. Ha szeretné megnézni, hogy engedélyezve van-e, futtassa `az feature  list -o table` parancsot, és keresse meg a `Microsoft.ContainerService/multiagentpoolpreview` értéket.

Az előző lépésben egy AK-fürt létrehozásához vagy egy meglévő csomópont-készlet frissítéséhez a fürthöz tartozó minimális csomópontok számának értéke *1*, a csomópontok maximális száma pedig *3*. Az alkalmazás követelményeinek változása esetén előfordulhat, hogy módosítania kell a fürt automatikusan méretezhető csomópontjának darabszámát.

A csomópontok számának módosításához használja az az [AK Update][az-aks-update] parancsot.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

A fenti példa frissíti a fürt automéretezőjét az egyetlen csomópontos készleten a *myAKSCluster* -ben legalább *1* és legfeljebb *5* csomópontra.

> [!NOTE]
> Az előzetes verzióban nem állítható be magasabb minimális csomópontok száma, mint a jelenleg a csomópont-készlethez. Ha például jelenleg a minimális darabszám értéke *1*, akkor a percek száma nem frissíthető *3*értékre.

Figyelje az alkalmazások és szolgáltatások teljesítményét, és állítsa be úgy a fürt automatikusan méretezhető csomópontjának számát, hogy az megfeleljen a szükséges teljesítménynek.

## <a name="disable-the-cluster-autoscaler"></a>A fürt autoskálázásának letiltása

Ha már nem kívánja használni a fürt automéretezőjét, letilthatja az az [AK Update][az-aks-update] paranccsal, és megadhatja a *--Letiltás-cluster-autoskálázási* paramétert. A csomópontok nem törlődnek, ha a fürt autoskálázása le van tiltva.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

A fürt automéretezőjét az az [AK Scale][az-aks-scale] paranccsal manuálisan is méretezheti. Ha a horizontális Pod automéretezőt használja, a funkció továbbra is fut a fürt automéretezője szolgáltatással, de a hüvelyek nem lesznek ütemezve, ha az összes csomópont-erőforrás használatban van.

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>Letiltott fürt autoskálázásának újbóli engedélyezése

Ha újra engedélyezni szeretné a fürt automéretezőjét egy meglévő fürtön, újra engedélyezheti azt az az [AK Update][az-aks-update] paranccsal, amely az *--enable-cluster-autoscaleer* paramétert is megadja.

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>A fürt autoskálázásának használata több Node-készlettel engedélyezve

A fürt automéretezője együtt használható a [több csomópontos készletek előzetes verziójának funkciójának](use-multiple-node-pools.md) használatával. Ezt a dokumentumot követve megtudhatja, hogyan engedélyezheti több csomópontos készletet, és hogyan adhat hozzá további csomópont-készleteket egy meglévő fürthöz. Mindkét funkció együttes használatakor engedélyezheti a fürt automatikus méretezését a fürt minden egyes csomópontján, és egyedi automatikus skálázási szabályokat adhat át mindegyiknek.

Az alábbi parancs feltételezi, hogy követte a jelen dokumentum korábbi részében ismertetett [kezdeti útmutatást](#create-an-aks-cluster-and-enable-the-cluster-autoscaler) , és frissíteni szeretné egy meglévő csomópont-készlet maximális darabszámát *3* és *5*között. A meglévő Node-készlet beállításainak frissítéséhez használja az az [AK nodepool Update][az-aks-nodepool-update] parancsot.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name multipoolcluster \
  --name mynodepool \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

A fürt automéretezője le lehet tiltani az [az AK nodepool Update paranccsal][az-aks-nodepool-update] , és a `--disable-cluster-autoscaler` paramétert kell átadni.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name multipoolcluster \
  --name mynodepool \
  --disable-cluster-autoscaler
```

## <a name="next-steps"></a>Következő lépések

Ez a cikk azt mutatja be, hogyan lehet automatikusan méretezni az AK-csomópontok számát. A vízszintes Pod automatikus méretezés használatával automatikusan módosíthatja az alkalmazást futtató hüvelyek számát. A horizontális Pod automéretező használatának lépéseiért lásd: [alkalmazások méretezése az AK-ban][aks-scale-apps].

<!-- LINKS - internal -->
[aks-upgrade]: upgrade-cluster.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-scale-apps]: tutorial-kubernetes-scale.md
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
