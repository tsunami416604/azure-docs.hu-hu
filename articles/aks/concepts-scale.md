---
title: Fogalmak – Alkalmazások méretezése az Azure Kubernetes-szolgáltatásokban (AKS)
description: Ismerje meg az Azure Kubernetes-szolgáltatás (AKS) méretezését, beleértve a horizontális pod automatikus skálázót, a fürt automatikus skálázóját és az Azure Container Instances-összekötőt.
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: zarhoads
ms.openlocfilehash: 396e5bc31723768ada334dd5043bca724af5e84f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595858"
---
# <a name="scaling-options-for-applications-in-azure-kubernetes-service-aks"></a>Alkalmazásskálázási beállítások az Azure Kubernetes Service-ben (AKS)

Az Azure Kubernetes-szolgáltatás (AKS) alkalmazások futtatásakor előfordulhat, hogy növelnie vagy csökkentenie kell a számítási erőforrások mennyiségét. Az alkalmazáspéldányok számának módosítása, az alapul szolgáló Kubernetes-csomópontok száma is szükség lehet a módosításra. Előfordulhat, hogy gyorsan ki kell építenie számos további alkalmazáspéldányt is.

Ez a cikk bemutatja azokat az alapvető fogalmakat, amelyek segítenek az alkalmazások méretezésében az AKS-ben:

- [Manuális méretezés](#manually-scale-pods-or-nodes)
- [Vízszintes pod automatikus méretező (HPA)](#horizontal-pod-autoscaler)
- [Fürt automatikus skálázója](#cluster-autoscaler)
- [Azure Container Instance (ACI) integráció az AKS-sel](#burst-to-azure-container-instances)

## <a name="manually-scale-pods-or-nodes"></a>Manuálisan méretezhető konkretor- vagy csomópont

A replikák (podok) és a csomópontok manuálisan skálázhatja, hogy tesztelje, hogyan reagál az alkalmazás a rendelkezésre álló erőforrások és állapot változására. Az erőforrások manuális méretezése lehetővé teszi a rögzített költségek, például a csomópontok számának fenntartásához használt erőforrások meghatározott mennyiségének meghatározását is. A manuális méretezéshez meg kell határoznia a replika vagy a csomópontszámát. A Kubernetes API-t, majd ütemezi a további podok létrehozása vagy a csomópontok kiürítése alapján, hogy a replika vagy a csomópont száma.

A csomópontok leskálázásakor a Kubernetes API meghívja a megfelelő Azure Compute API-t a fürt által használt számítási típushoz kötve. Például a virtuálisgép-méretezési csoportokra épülő fürtök esetében a virtuálisgép-méretezési készletek API határozza meg az eltávolítandó csomópontok kiválasztásának logikáját. Ha többet szeretne megtudni arról, hogy a csomópontok hogyan vannak kiválasztva a leskálázáskor történő eltávolításhoz, olvassa el a [VMSS gyakori kérdések című témakört.](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed)

A manuális méretezési podok és csomópontok első lépései: [Alkalmazások méretezése az AKS-ben.][aks-scale]

## <a name="horizontal-pod-autoscaler"></a>Vízszintes pod automatikus méretező

A Kubernetes a horizontális pod automatikus skálázót (HPA) használja az erőforrás-igény figyeléséhez és a replikák számának automatikus méretezéséhez. Alapértelmezés szerint a horizontális pod automatikus skálázó ellenőrzi a Metrikák API-t 30 másodpercenként a replikaszám szükséges módosításait. Ha módosításokra van szükség, a replikák száma ennek megfelelően növekszik vagy csökken. A horizontális pod automatikus skálázó olyan AKS-fürtökkel működik, amelyek a Metrika-kiszolgálót telepítették a Kubernetes 1.8+-hoz.

![Kubernetes vízszintes pod automatikus skálázás](media/concepts-scale/horizontal-pod-autoscaling.png)

Ha konfigurálja a horizontális pod automatikus skálázó egy adott központi telepítés, megadhatja a minimális és maximális számú replikák futtatható. Azt is megadhatja a metrika a skálázási döntések figyeléséhez és alapja, például a CPU-használat.

A horizontális pod automatikus skálázó az AKS-ben, lásd: [Automatikus skálázási podok az AKS-ben.][aks-hpa]

### <a name="cooldown-of-scaling-events"></a>Skálázási események újratöltődési idő

Mivel a horizontális pod automatikus skálázó ellenőrzi a Metrikák API 30 másodpercenként, előfordulhat, hogy a korábbi méretezési események nem fejeződött be sikeresen, mielőtt egy másik ellenőrzés történik. Ez a viselkedés azt eredményezheti, hogy a horizontális pod automatikus skálázó a replikák száma módosítása előtt az előző méretezési esemény kaphat alkalmazás számítási feladatok és az erőforrás-igények ennek megfelelően.

A versenyesemények minimalizálása érdekében a letöltő- vagy késleltetési értékek et kell beállítani. Ezek az értékek határozzák meg, hogy a horizontális pod automatikus skálázó nak mennyi ideig kell várnia egy méretezési esemény után, mielőtt egy másik méretezési esemény aktiválódna. Ez a viselkedés lehetővé teszi, hogy az új replika száma érvénybe lépjen, és a Metrikák API-t, hogy tükrözze az elosztott számítási feladatok. Alapértelmezés szerint a felskálázási események késleltetése 3 perc, a leskálázási események késleltetése pedig 5 perc

Jelenleg nem lehet behangolni ezeket a újratöltődési értékeket az alapértelmezett.

## <a name="cluster-autoscaler"></a>Fürt automatikus skálázója

A pod-igények rekedése érdekében a Kubernetes rendelkezik egy fürt automatikus skálázóval, amely a csomópontkészletben kért számítási erőforrások alapján módosítja a csomópontok számát. Alapértelmezés szerint a fürt automatikus skálázó ellenőrzi a metrikák API-kiszolgáló 10 másodpercenként a csomópontszám szükséges módosításait. Ha a fürt automatikus skálázása azt állapítja meg, hogy szükség van módosításra, az AKS-fürt csomópontjainak száma ennek megfelelően nő vagy csökken. A fürt automatikus skálázó együttműködik RBAC-kompatibilis AKS-fürtök, amelyek kubernetes 1.10.x vagy újabb.

![Kubernetes fürt automatikus skálázó](media/concepts-scale/cluster-autoscaler.png)

Fürt automatikus skálázó általában a vízszintes pod automatikus skálázó mellett használják. Kombinálva a horizontális pod automatikus skálázó növeli vagy csökkenti a podok száma az alkalmazás igénye alapján, és a fürt automatikus skálázó beállítja a csomópontok számát, ha szükséges a további podok futtatásához kell megfelelően.

A fürt automatikus skálázójának az AKS-ben való ismerkedéséhez olvassa el [az AKS fürtautomatikus skálázóját.][aks-cluster-autoscaler]

### <a name="scale-up-events"></a>Események felskálázása

Ha egy csomópont nem rendelkezik elegendő számítási erőforrásokkal a kért pod futtatásához, hogy a pod nem tud végighaladni az ütemezési folyamaton. A pod nem indítható el, ha további számítási erőforrások érhetők el a csomópontkészleten belül.

Amikor a fürt automatikus skálázó észleli a csomópontok készlet erőforrás-megkötések miatt nem ütemezhető podok, a csomópontkészleten belüli csomópontok száma növekszik a további számítási erőforrások biztosításához. Ha ezek a további csomópontok sikeresen üzembe helyezése és a csomópontkészleten belül használható, a podok majd ütemezett futtatni őket.

Ha az alkalmazás gyorsan kell skálázni, egyes podok maradhatnak olyan állapotban, amely arra vár, hogy ütemezve, amíg a fürt automatikus skálázó által üzembe helyezett további csomópontok el tudja fogadni az ütemezett podok. A nagy burst igényekkel rendelkező alkalmazások esetében virtuális csomópontokkal és Azure Container-példányokkal méretezhető.

### <a name="scale-down-events"></a>Események lekicsinylése

A fürt automatikus skálázó is figyeli a pod ütemezési állapotát csomópontok, amelyek nem nemrég kapott új ütemezési kérelmeket. Ez a forgatókönyv azt jelzi, hogy a csomópontkészlet a szükségesnél több számítási erőforrással rendelkezik, és a csomópontok száma csökkenthető.

Törlésre van ütemezve egy csomópont, amely alapértelmezés szerint 10 percig nem szükséges. Ebben az esetben a podok a csomópontkészlet más csomópontjain futnak, és a fürt automatikus skálázója csökkenti a csomópontok számát.

Az alkalmazások tapasztalhatnak némi fennakadást, mivel a podok vannak ütemezve a különböző csomópontokon, amikor a fürt automatikus skálázó csökkenti a csomópontok számát. A megszakítások minimalizálása érdekében kerülje az egyetlen podpéldányt használó alkalmazásokat.

## <a name="burst-to-azure-container-instances"></a>Felszakítás az Azure container-példányokba

Az AKS-fürt gyors méretezéséhez integrálható az Azure Container Instances (ACI) használatával. A Kubernetes beépített összetevőket tartalmaz a replika és a csomópontszám méretezéséhez. Azonban ha az alkalmazás gyorsan skálázható, a horizontális pod automatikus skálázó ütemezhet több podok, mint a csomópontkészlet meglévő számítási erőforrások által biztosított. Ha konfigurálva van, ez a forgatókönyv majd elindítja a fürt automatikus skálázó további csomópontok üzembe helyezése a csomópontkészletben, de eltarthat néhány percig, amíg ezek a csomópontok sikeresen kiépítése, és lehetővé teszi a Kubernetes-ütemező podok futtatásához rajtuk.

![Kubernetes burst méretezés ACI](media/concepts-scale/burst-scaling.png)

Az ACI lehetővé teszi a tárolópéldányok gyors üzembe helyezését további infrastruktúra-terhelés nélkül. Amikor csatlakozik az AKS-hez, az ACI az AKS-fürt biztonságos, logikai kiterjesztésévé válik. A [virtual kubelet][virtual-kubelet]alapú [virtuális csomópontok][virtual-nodes-cli] összetevője telepítve van az AKS-fürtben, amely az ACI-t virtuális Kubernetes csomópontként mutatja be. A Kubernetes ezután ütemezheti az ACI-példányként futó podokat virtuális csomópontokon keresztül, nem pedig a virtuálisgép-csomópontokon lévő podokként közvetlenül az AKS-fürtben. A virtuális csomópontok jelenleg előzetes verzióban vannak az AKS-ben.

Az alkalmazás nem igényel módosítást a virtuális csomópontok használatához. A központi telepítések az AKS és az ACI között skálázhatók, és késedelem nélkül, mivel a fürt automatikus skálázója új csomópontokat telepít az AKS-fürtben.

A virtuális csomópontok egy további alhálózatba vannak telepítve az AKS-fürttel azonos virtuális hálózatban. Ez a virtuális hálózati konfiguráció lehetővé teszi az ACI és az AKS közötti forgalom védelmét. Az AKS-fürthöz hasonlóan az ACI-példány is biztonságos, logikai számítási erőforrás, amely más felhasználóktól van elkülönítve.

## <a name="next-steps"></a>További lépések

A méretezési alkalmazások első lépéseihez először kövesse a [rövid útmutatót, és hozzon létre egy AKS-fürtöt az Azure CLI-vel.][aks-quickstart] Ezután elkezdheti manuálisan vagy automatikusan méretezni az Alkalmazásokat az AKS-fürtben:

- [Manuálisan méretezhető konkretor-][aks-manually-scale-pods] vagy [csomópont][aks-manually-scale-nodes]
- A [vízszintes pod automatikus méretező][aks-hpa] használata
- A [fürt automatikus skálázójának][aks-cluster-autoscaler] használata

A Kubernetes és az AKS alapfogalmairól az alábbi cikkekben talál további információt:

- [Kubernetes / AKS-fürtök és munkaterhelések][aks-concepts-clusters-workloads]
- [Kubernetes / AKS hozzáférés és identitás][aks-concepts-identity]
- [Kubernetes / AKS biztonság][aks-concepts-security]
- [Kubernetes / AKS virtuális hálózatok][aks-concepts-network]
- [Kubernetes / AKS tároló][aks-concepts-storage]

<!-- LINKS - external -->
[virtual-kubelet]: https://virtual-kubelet.io/

<!-- LINKS - internal -->
[aks-quickstart]: kubernetes-walkthrough.md
[aks-hpa]: tutorial-kubernetes-scale.md#autoscale-pods
[aks-scale]: tutorial-kubernetes-scale.md
[aks-manually-scale-pods]: tutorial-kubernetes-scale.md#manually-scale-pods
[aks-manually-scale-nodes]: tutorial-kubernetes-scale.md#manually-scale-aks-nodes
[aks-cluster-autoscaler]: autoscaler.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-network]: concepts-network.md
[virtual-nodes-cli]: virtual-nodes-cli.md