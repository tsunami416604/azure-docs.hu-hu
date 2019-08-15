---
title: A fürt automéretező használata az Azure Kubernetes szolgáltatásban (ak)
description: Ismerje meg, hogyan használható a fürt automatikus méretezése, hogy a fürt automatikusan méretezhető legyen az alkalmazások igényeinek kielégítéséhez egy Azure Kubernetes-szolgáltatásbeli (ak-beli) fürtben.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 07/18/2019
ms.author: mlearned
ms.openlocfilehash: dc5e862109a766f708338ebddb91a75ffc550306
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69031922"
---
# <a name="preview---automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Előzetes verzió – a fürt automatikus méretezése az alkalmazások igényeinek kielégítéséhez az Azure Kubernetes Service-ben (ak)

Az alkalmazások az Azure Kubernetes szolgáltatásban (ak) való megtartásához szükség lehet a számítási feladatokat futtató csomópontok számának módosítására. A fürt autoscaleer összetevője megtekintheti a fürtben lévő hüvelyeket, amelyek erőforrás-korlátozások miatt nem ütemezhetők. Problémák észlelése esetén a Node-készlet csomópontjainak száma megnő az alkalmazás iránti igény kielégítése érdekében. A csomópontokat a futó hüvelyek hiánya is rendszeresen ellenőrzi, a csomópontok számával azonban szükség szerint csökken. Ez a lehetőség lehetővé teszi a csomópontok számának automatikus méretezését az AK-fürtben, így hatékony és költséghatékony fürtöt futtathat.

Ez a cikk bemutatja, hogyan engedélyezheti és kezelheti a fürt automéretezőjét egy AK-fürtben. A fürt automéretezőjét csak előzetes verzióban kell tesztelni az AK-fürtökön.

> [!IMPORTANT]
> Az AK előzetes verziójának funkciói önkiszolgáló opt-in. Az előzetes verziók az "adott állapotban" és "ahogy elérhető" módon vannak kizárva, és ki vannak zárva a szolgáltatói szerződésekből és a korlátozott jótállásból. A következő részben az ügyfélszolgálat a lehető leghatékonyabban foglalkozik. Ezért ezeket a funkciókat nem éles használatra szánták. További részletekért tekintse meg a következő támogatási cikkeket:
>
> * [AK-támogatási szabályzatok][aks-support-policies]
> * [Azure-támogatás – gyakori kérdések][aks-faq]

## <a name="before-you-begin"></a>Előkészületek

Ehhez a cikkhez az Azure CLI 2.0.65 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

### <a name="install-aks-preview-cli-extension"></a>Az Kabai szolgáltatás telepítése – előnézeti CLI-bővítmény

A fürt automéretező használatához a CLI *-előnézet CLI-* bővítmény 0.4.4 vagy újabb verziójára van szükség. Telepítse az *AK – előzetes* verzió Azure CLI bővítményét az az [Extension Add][az-extension-add] paranccsal, majd az az [Extension Update][az-extension-update] paranccsal keresse meg a rendelkezésre álló frissítéseket:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-scale-set-feature-provider"></a>Méretezési csoport szolgáltatás szolgáltatójának regisztrálása

A méretezési csoportokat használó AK létrehozásához engedélyeznie kell a szolgáltatás jelölőjét is az előfizetésben. A *VMSSPreview* szolgáltatás jelzőjét a következő példában látható módon regisztrálja az az [Feature Register][az-feature-register] parancs használatával:

> [!CAUTION]
> Ha regisztrál egy szolgáltatást egy előfizetéshez, jelenleg nem tudja regisztrálni a szolgáltatást. Az előzetes verziójú funkciók engedélyezése után az alapértelmezett beállítások az előfizetésben létrehozott összes AK-fürthöz használhatók. Ne engedélyezze az előzetes verziójú funkciókat az éles előfizetésekben. Használjon külön előfizetést az előzetes verziójú funkciók tesztelésére és visszajelzések gyűjtésére.

```azurecli-interactive
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

Néhány percet vesz igénybe, amíg az állapot *regisztrálva*jelenik meg. A regisztrációs állapotot az az [Feature List][az-feature-list] parancs használatával tekintheti meg:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

Ha elkészült, frissítse a *Microsoft. tárolószolgáltatás* erőforrás-szolgáltató regisztrációját az az [Provider Register][az-provider-register] paranccsal:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Korlátozások

A fürt automéretezőjét használó AK-fürtök létrehozásakor és kezelésekor a következő korlátozások érvényesek:

* A HTTP-alkalmazás útválasztási bővítménye nem használható.

## <a name="about-the-cluster-autoscaler"></a>Tudnivalók a fürt autoskálázásáról

Ha módosítani szeretné az alkalmazási igényeket, például a munkanapokat és az estét, vagy egy hétvégét, a fürtöknek gyakran szükségük van az automatikus skálázásra. Az AK-fürtök kétféleképpen méretezhetők a következőképpen:

* A **fürt** autoskálázása figyeli a hüvelyeket, amelyek erőforrás-megkötések miatt nem ütemezhetők a csomópontokon. A fürt automatikusan megnöveli a csomópontok számát.
* A **horizontális Pod automéretező** a Kubernetes-fürt metrikák kiszolgálóját használja a hüvelyek erőforrás-igényének figyeléséhez. Ha egy szolgáltatásnak több erőforrásra van szüksége, a hüvelyek száma automatikusan növekszik az igény kielégítése érdekében.

![A fürt autoskálázása és a horizontális Pod autoskálázás gyakran együtt működik a szükséges alkalmazási igények támogatásához](media/autoscaler/cluster-autoscaler.png)

A horizontális Pod autoskálázás és a fürt autoskálázása is igény szerint csökkentheti a hüvelyek és a csomópontok számát. A fürt autoskálázása csökkenti a csomópontok számát, ha egy adott időszakban nem használták fel a rendelkezésre álló kapacitást. A fürt automéretezője által eltávolított csomóponton lévő hüvelyek biztonságosan vannak ütemezve a fürtben máshol. Előfordulhat, hogy a fürt autoskálázása nem tud leméretezni, ha a hüvely nem helyezhető át, például a következő helyzetekben:

* A közvetlenül létrehozott és nem egy vezérlő objektum, például egy központi telepítés vagy egy replikakészlet által támogatott Pod.
* A pod-megszakadási költségvetés (PDB) túl korlátozó, és nem teszi lehetővé, hogy a hüvelyek száma egy bizonyos küszöbérték alá kerüljön.
* A pod olyan csomópont-választókat vagy affinitást használ, amelyeket nem lehet tiszteletben venni, ha egy másik csomóponton van ütemezve.

Ha többet szeretne megtudni arról, hogy a fürt automéretezője miért nem tudja méretezni a méretezést, tekintse meg, hogy [milyen típusú hüvelyek akadályozhatják a fürt][autoscaler-scaledown] autoskálázását a csomópontok eltávolításával?

A fürt automatikus méretezése indítási paramétereket használ olyan dolgokhoz, mint a méretezési események és az erőforrás-küszöbértékek közötti időintervallumok. Ezeket a paramétereket az Azure platform határozza meg, és jelenleg nem lehet módosítani. További információ arról, hogy milyen paramétereket használ a fürt autoskálázása. lásd: [Mi a fürt autoskálázási paraméterei?][autoscaler-parameters].

A két automatikus skálázás egyszerre működhet együtt, és a fürtben gyakran is üzembe helyezhetők. Kombinálva a horizontális Pod automéretező az alkalmazás igényének kielégítéséhez szükséges hüvelyek számának futtatására koncentrál. A fürt autoskálázása az ütemezett hüvelyek támogatásához szükséges csomópontok számának futtatására fókuszál.

> [!NOTE]
> A manuális skálázás le van tiltva a fürt automatikus méretezésének használatakor. Hagyja, hogy a fürt autoskálázása határozza meg a csomópontok szükséges számát. Ha manuálisan szeretné méretezni a fürtöt, [Tiltsa le a fürt](#disable-the-cluster-autoscaler)automéretezőjét.

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>AK-fürt létrehozása és a fürt autoskálázásának engedélyezése

Ha AK-fürtöt kell létrehoznia, használja az az [AK Create][az-aks-create] parancsot. A fürthöz tartozó, a fürthöz tartozó autoskálázás engedélyezéséhez és konfigurálásához használja a *--enable-cluster-* autoskálázási paramétert, és adja meg a csomópont *--min-Count* és a *--Max-Count*értékét.

> [!IMPORTANT]
> A fürt automéretezője egy Kubernetes-összetevő. Bár az AK-fürt egy virtuálisgép-méretezési készletet használ a csomópontokhoz, ne manuálisan engedélyezzen vagy szerkessze a méretezési csoport autoskálázásának beállításait a Azure Portal vagy az Azure CLI használatával. Lehetővé teszi, hogy a Kubernetes-fürt autoskálázása felügyelje a szükséges méretezési beállításokat. További információ: módosíthatom [a csomópont-ERŐFORRÁSCSOPORT AK-erőforrásait?](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group)

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
> Ha a *--kubernetes-Version* `az aks create`értéket ad meg, az adott verziónak meg kell felelnie vagy meg kell haladnia a szükséges minimális verziószámot, amelyet az előzőekben ismertetett [lépések szakaszban talál](#before-you-begin) .

A fürt létrehozása és a fürt autoskálázási beállításainak konfigurálása néhány percet vesz igénybe.

### <a name="update-the-cluster-autoscaler-on-an-existing-node-pool-in-a-cluster-with-a-single-node-pool"></a>Fürthöz tartozó automéretező frissítése egy olyan fürt meglévő csomópont-készletén, amely egyetlen csomópont-készlettel rendelkezik

Frissítheti az előző fürthöz tartozó autoskálázási beállításokat egy olyan fürtön, amely megfelel az előzőekben leírt követelményeknek, [mielőtt elkezdené](#before-you-begin) a szakasz lépéseit. Az az [AK Update][az-aks-update] paranccsal engedélyezheti a fürtben lévő automéretezőt a fürtön *egyetlen* csomópontos készlet használatával.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

Ezt követően a fürt autoskálázása engedélyezhető vagy letiltható `az aks update --disable-cluster-autoscaler` a vagy a `az aks update --enable-cluster-autoscaler` parancs használatával.

### <a name="enable-the-cluster-autoscaler-on-an-existing-node-pool-in-a-cluster-with-multiple-node-pools"></a>Fürthöz tartozó automéretező engedélyezése egy olyan fürt meglévő csomópont-készletén, amely több Node-készlettel rendelkezik

A fürt autoskálázása is használható a [több csomópontos készletek előzetes](use-multiple-node-pools.md) verziójú funkciójának használatával. Engedélyezheti a fürt automéretezőjét az egyes csomópont-készleteken egy olyan AK-fürtön belül, amely több csomópontot tartalmaz, és megfelel az előzőekben [](#before-you-begin) ismertetett követelményeknek. Az az [AK nodepool Update][az-aks-nodepool-update] paranccsal engedélyezheti a fürt automéretezőjét egy adott csomópont-készleten.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Ezt követően a fürt autoskálázása engedélyezhető vagy letiltható `az aks nodepool update --disable-cluster-autoscaler` a vagy a `az aks nodepool update --enable-cluster-autoscaler` parancs használatával.

## <a name="change-the-cluster-autoscaler-settings"></a>A fürt autoskálázási beállításainak módosítása

Az előző lépésben egy AK-fürt létrehozásához vagy egy meglévő csomópont-készlet frissítéséhez a fürthöz tartozó minimális csomópontok számának értéke *1*, a csomópontok maximális száma pedig *3*. Az alkalmazás követelményeinek változása esetén előfordulhat, hogy módosítania kell a fürt automatikusan méretezhető csomópontjának darabszámát.

A csomópontok számának módosításához használja az az [AK nodepool Update][az-aks-nodepool-update] parancsot.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

A fenti példa frissíti a fürt automéretezőjét a *myAKSCluster* lévő *mynodepool* csomópont-készleten legalább *1* és legfeljebb *5* csomópont számára.

> [!NOTE]
> Az előzetes verzióban nem állítható be magasabb minimális csomópontok száma, mint a jelenleg a csomópont-készlethez. Ha például jelenleg a minimális darabszám értéke *1*, akkor a percek száma nem frissíthető *3*értékre.

Figyelje az alkalmazások és szolgáltatások teljesítményét, és állítsa be úgy a fürt automatikusan méretezhető csomópontjának számát, hogy az megfeleljen a szükséges teljesítménynek.

## <a name="disable-the-cluster-autoscaler"></a>A fürt autoskálázásának letiltása

Ha már nem kívánja használni a fürt automéretezőjét, letilthatja azt az az [AK nodepool Update][az-aks-nodepool-update] paranccsal, amely megadja a *--disable-cluster-* autoscaleer paramétert. A csomópontok nem törlődnek, ha a fürt autoskálázása le van tiltva.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --disable-cluster-autoscaler
```

A fürtöt manuálisan is méretezheti az az [AK Scale][az-aks-scale] paranccsal. Ha a horizontális Pod automéretezőt használja, akkor a szolgáltatás továbbra is fut a fürt autoskálázásával, de előfordulhat, hogy a hüvelyek nem lesznek ütemezve, ha a csomópont erőforrásai mind használatban vannak.

## <a name="next-steps"></a>További lépések

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
