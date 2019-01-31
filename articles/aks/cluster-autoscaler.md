---
title: Fürt automatikus méretező használatához az Azure Kubernetes Service (AKS)
description: Megtudhatja, hogyan fürt automatikus méretező használatához az alkalmazás az Azure Kubernetes Service (AKS)-fürt figyelembevételével fürtök automatikus méretezése.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 01/29/2019
ms.author: iainfou
ms.openlocfilehash: 6b2302e69c9412170b55df4bfd8c1df5a9f75ef3
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55479291"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Alkalmazás figyelembevételével Azure Kubernetes Service (AKS) egy fürt automatikus méretezése

Tartani az alkalmazások számára az Azure Kubernetes Service (AKS), szükség lehet a számítási feladatokat futtató csomópontok számának beállításához. A fürt méretező összetevő esetében a fürtben, amely korlátozott erőforrások miatt nem lehet ütemezni podok tekintheti meg. Problémák észlelése, a csomópontok számát az alkalmazás igény nő. Csomópontok futtató podok, majd csökkenthető az igény szerint csomópontok hiánya szintén rendszeresen ellenőrzi. Automatikus méretezése felfelé és lefelé az AKS-fürt a csomópontok számát, ez a képesség lehetővé teszi egy hatékony, költségkímélő fürtöt futtat.

Ez a cikk bemutatja, hogyan engedélyezheti és kezelheti a fürt méretező az AKS-fürtben.

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk megköveteli, hogy futnak-e az Azure CLI 2.0.55 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

AKS-fürtök, amelyek támogatják az automatikus méretező fürt kell használni a virtuálisgép-méretezési csoportok és futtatása a Kubernetes-verzió *1.12.4* vagy újabb. A méretezési csoport támogatni fogja az előzetes verzióban. Engedélyezve van a hibajelentések, és a méretezési csoportok használó fürtök létrehozásához telepítse a *aks előzetes* Azure CLI-bővítmény használata a [az bővítmény hozzáadása] [ az-extension-add] parancsot, az alábbi példában látható módon:

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> Amikor telepíti a *aks előzetes* bővítményt, minden AKS-fürtöt hoz létre a méretezési csoport előzetes telepítési modellt használja. Kikapcsolhatja az újat, és rendszeres, teljes körűen támogatott fürtök létrehozását, távolítsa el a bővítmény használatával `az extension remove --name aks-preview`.

## <a name="about-the-cluster-autoscaler"></a>Fürt automatikus méretező kapcsolatban

Módosíthatja a változó alkalmazások számára, például között a workday és esténként vagy a hétvégi fürtök gyakran van szükség, automatikus méretezés. AKS-fürtök méretezhetők a két módszer egyikével:

* A **méretező fürt** a podok csomópontokon korlátozott erőforrások miatt nem lehet ütemezett órák. A fürt automatikusan majd növeli a csomópontok számát.
* A **podok horizontális méretező** a metrikák kiszolgálót használja egy Kubernetes-fürt figyelése a podok az erőforrásigény. Ha egy szolgáltatás több erőforrást igényel, akkor a podok számát automatikusan növelni, az igény.

![A fürt méretező és podok horizontális méretező gyakran együttműködése szükséges alkalmazás igényeinek támogatásához](media/autoscaler/cluster-autoscaler.png)

A podok horizontális méretező és a fürt méretező is, majd csökkentheti a podok és igény szerint a csomópontok számát. Fürt automatikus méretező csökkenti a csomópontok számát, amikor egy ideig nem használt kapacitás. Podok egy csomóponton el kell távolítani a fürt méretező szerint biztonságosan máshol ütemezése a fürtben. Lehet, hogy a fürt méretező nem lehet, vertikális leskálázás, ha a podok nem helyezhető át, például a következő helyzetekben:

* Podot közvetlenül létrehozott, és a egy tartományvezérlő objektumot, például egy üzembe helyezési vagy replika beállítása nem támogatja.
* A pod megszakítás költségvetés (PDB) túl korlátozó, és nem teszi lehetővé a meghatározott küszöbérték alá csökken kell a podok számát.
* Egy pod csomópont választók vagy nem lesznek figyelembe véve, ha egy másik csomóponton ütemezett affinitást használ.

Hogyan lehet vertikális leskálázás nem sikerült a fürt méretező kapcsolatos további információkért lásd: [podok is milyen típusú megakadályozza, hogy a fürt automatikus méretező csomópont eltávolítása?][autoscaler-scaledown]

Fürt automatikus méretező például méretezési eseményeket és erőforrás-küszöbértékek között időintervallumok indítási paraméterei. Ezeket a paramétereket az Azure platform által meghatározott, és jelenleg nem érhetőek el, Ön módosíthatja. További információ a paramétereket a fürt méretező használja, lásd: [Mik azok a fürt méretező paraméterek?] [autoscaler-parameters].

A két autoscalers hogyan tudnak együttműködni, és gyakran egyaránt telepít egy fürtben. Együttesen a podok horizontális méretező összpontosít futó alkalmazás igényei kielégítéséhez szükséges podok számát. Fürt automatikus méretező összpontosít futtatása az ütemezett podok támogatásához szükséges csomópontok számát.

> [!NOTE]
> Manuális skálázás le lesz tiltva a fürt méretező használatakor. Lehetővé teszik a fürt méretező határozza meg a szükséges csomópontok számát. Ha szeretné manuálisan méretezhető, a fürt [tiltsa le a fürt méretező](#disable-the-cluster-autoscaler).

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>AKS-fürt létrehozása és a fürt automatikus méretező engedélyezése

Ha egy AKS-fürt létrehozásához szükséges, használja a [az aks létrehozása] [ az-aks-create] parancsot. Adjon meg egy *– kubernetes-verzió* , amely megfelel-e vagy meghaladja a minimális verziószám, ahogyan az előző szükséges [megkezdése előtt](#before-you-begin) szakaszban. Engedélyezze és konfigurálja a fürt méretező használja a *– enable-fürt-méretező* paramétert, és adja meg a csomópont *--min-count* és *--maximális darabszám*.

Az alábbi példa egy AKS-fürtöt hoz létre virtuálisgép-méretezési csoportot és a fürt méretező engedélyezve van, és használja legalább *1* és a maximális *3* csomópontok:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location canadaeast

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --kubernetes-version 1.12.4 \
  --node-count 1 \
  --enable-vmss \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

A fürt létrehozásához és a fürt méretező beállításainak konfigurálása néhány percet vesz igénybe.

### <a name="enable-the-cluster-autoscaler-on-an-existing-aks-cluster"></a>Egy meglévő AKS-fürtre a fürt méretező engedélyezése

Engedélyezheti a fürt automatikus méretező egy meglévő AKS-fürt, amely megfelel a követelményeknek, ahogyan az előző [megkezdése előtt](#before-you-begin) szakaszban. Használja a [az aks update] [ az-aks-update] parancsot, majd válassza ki a *– enable-fürt-méretező*, adja meg a csomópont *--min-count* és *--maximális darabszám*. Az alábbi példa lehetővé teszi, hogy egy meglévő fürt legalább használó fürt méretező *1* és a maximális *3* csomópontok:

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Ha a csomópontok minimális száma nagyobb, mint a meglévő száma a fürtben található csomópontok, a további csomópontokat létrehozása néhány percet vesz igénybe.

## <a name="change-the-cluster-autoscaler-settings"></a>A fürt méretező beállításainak módosítása

Az előző lépésben létrehozni vagy frissíteni egy meglévő AKS-fürtöt, a fürt méretező csomópontok minimális számánál állították be *1*, és úgy állították be, a csomópontok maximális számánál *3*. Az alkalmazás a növekvő igények szerint módosítja, szükség lehet a fürtcsomópontok méretező számának módosításához.

A csomópontok száma módosításához használja a [az aks update] [ az-aks-update] parancsot, és adjon meg egy minimális és maximális értéket. A következő példa készletek a *--min-count* való *1* és a *--maximális darabszám* való *5*:

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

> [!NOTE]
> Az előzetes időszakban nem állítható be, mint a fürthöz beállított magasabb csomópontok minimális számánál. Például, ha már van beállítva minimális száma *1*, a minimális száma nem lehet frissíteni *3*.

Monitorozza az alkalmazások és szolgáltatások teljesítményét, és állítsa be a fürt méretező csomópont hibás megfelelően a szükséges teljesítményt.

## <a name="disable-the-cluster-autoscaler"></a>Fürt automatikus méretező letiltása

Ha nem szeretne fürt automatikus méretező használatához, letilthatja a a [az aks update] [ az-aks-update] parancsot. A rendszer nem távolítja el csomópontokat, amikor a fürt méretező le van tiltva.

Fürt automatikus méretező eltávolításához adja meg a *--disable-fürt-méretező* paraméterrel az alábbi példában látható módon:

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

Manuálisan méretezhetők a fürt használata a [az aks méretezési] [ az-aks-scale] parancsot. Ha a podok horizontális méretező használatához ezt a funkciót továbbra is fut a fürtön méretező, le van tiltva a, de podok előfordulhat, hogy végül nem lehet ütemezni, ha a csomópont által az összes használt erőforrások.

## <a name="next-steps"></a>További lépések

Ez a cikk láthatta, hogyan skálázhatja automatikusan az AKS-csomópontok száma. Podok horizontális méretező segítségével is automatikusan beállíthatja a az alkalmazást futtató podok számát. Az automatikus méretező podok horizontális használatával lépéseiért lásd: [méretezheti az alkalmazásait az aks-ben][aks-scale-apps].

<!-- LINKS - internal -->
[aks-upgrade]: upgrade-cluster.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-scale-apps]: tutorial-kubernetes-scale.md
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-scale]: /cli/azure/aks#az-aks-scale

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
