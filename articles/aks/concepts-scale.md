---
title: Alapelvei – alkalmazások skálázása az Azure Kubernetes-szolgáltatások (AKS)
description: Ismerje meg az Azure Kubernetes Service (AKS), beleértve a podok horizontális méretező, fürt méretező és az Azure Container Instances-összekötő méretezést.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: iainfou
ms.openlocfilehash: c7019eac4edc530de5ef64ba9eb32e8e4994e75b
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57245199"
---
# <a name="scaling-options-for-applications-in-azure-kubernetes-service-aks"></a>Méretezési lehetőségeket biztosít az alkalmazások az Azure Kubernetes Service (AKS)

Alkalmazások futtatása az Azure Kubernetes Service (AKS), mert szükség lehet növelheti vagy csökkentheti a számítási erőforrások mennyiségét. Az alkalmazáspéldányok számának kell alapul szolgáló Kubernetes-csomópontok is szükség lehet módosítani a számának módosításához. Emellett szükség lehet gyorsan üzembe helyezhet további alkalmazáspéldány nagy számú.

Ez a cikk bemutatja az alapvető fogalmakat, amelyek segítségével méretezheti az alkalmazásait az aks-ben:

- [Manuális méretezése](#manually-scale-pods-or-nodes)
- [Podok horizontális méretező (HPA)](#horizontal-pod-autoscaler)
- [Fürt méretező](#cluster-autoscaler)
- [Azure Container Instance (ACI) integrációja az aks-sel](#burst-to-azure-container-instances)

## <a name="manually-scale-pods-or-nodes"></a>Podok vagy a csomópontok manuális méretezése

Replikák (podok) és a csomópont a rendelkezésre álló erőforrások és az állapot változik az alkalmazás reakciója teszteléséhez manuálisan méretezhetők. Erőforrások skálázása a manuálisan is meghatározhatja időkereten erőforrások használatával fenntartani egy állandó költség, például a csomópontok számát. Manuálisan méretezhető, a replika vagy a csomópontok száma és a Kubernetes API-t ütemezések létrehozása az újabb podok vagy a kiürítés csomópontok megadása.

Használatába manuális méretezése látnak podok és a csomópontok [méretezheti az alkalmazásait az aks-ben][aks-scale].

## <a name="horizontal-pod-autoscaler"></a>Podok horizontális méretező

Kubernetes figyelése az erőforrásigény, és automatikus méretezése a replikák száma a podok horizontális méretező (HPA) használja. Alapértelmezés szerint a podok horizontális méretező replikáinak száma a szükséges változtatásokat 30 másodpercenként ellenőrzi a metrikák API. Módosítások szükségesek, amikor a replikák száma nő vagy csökken ennek megfelelően. Podok horizontális méretező működik együtt a Kubernetes 1.8-as + a metrikák kiszolgálón telepített AKS-fürt.

![Kubernetes-podok horizontális felskálázását](media/concepts-scale/horizontal-pod-autoscaling.png)

Egy adott központi telepítés podok horizontális méretező konfigurálásakor futtatható replikák minimális és maximális száma határozza meg. A metrika figyelése és méretezési döntések alapjául, például a CPU-használat is definiál.

Első lépések a podok horizontális méretező az aks-ben, lásd: [az aks-ben podok automatikus méretezésének][aks-hpa].

### <a name="cooldown-of-scaling-events"></a>A méretezés események utáni

Podok horizontális méretező ellenőrzi a metrikák API 30 másodpercenként, mivel előző méretezési eseményeket nem sikeresen befejeződtek előtt egy másik ellenőrzésére kerül sor. Ezt a viselkedést okozhat a podok horizontális méretező a replikák számának módosítása előtt lett, hogy az előző skálázási esemény tudja fogadni az alkalmazás számítási feladatait és az erőforrás-igényű megfelelő beállításához.

Ezek az események versenyhelyzet minimalizálása érdekében utáni vagy késleltetési érték állítható be. Ezeket az értékeket határozza meg, mennyi ideig kell várnia az automatikus méretező podok horizontális skálázási esemény előtt egy másik skálázási esemény kiváltható után. Ez a viselkedés lehetővé teszi, hogy érvénybe léptetéséhez száma az új replikára, és a metrikák API tükrözik az elosztott terheléseknél engedélyezett. Alapértelmezés szerint a vertikális felskálázási események késleltetési idő legyen 3 percenként, és vertikális leskálázási események a késleltetési idő legyen 5 perc

Ezek az értékek utáni finomhangolása szükségessé. Az alapértelmezett utáni értékeket adhat a benyomást, hogy a podok horizontális méretező nem elég gyorsan méretezés a replika száma. Például a használt replikák száma gyorsabban növeléséhez csökkentse a `--horizontal-pod-autoscaler-upscale-delay` létrehozásakor a podok horizontális méretező definíciókat `kubectl`.

## <a name="cluster-autoscaler"></a>Fürt méretező

Kubernetes pod változásaihoz válaszolni, van egy fürt méretező, amely alapján a kért csomópontok alkalmazkodik a számítási erőforrásokat a csomópont a készletben. Fürt automatikus méretező például méretezési eseményeket és erőforrás-küszöbértékek között időintervallumok indítási paraméterei. Ha a fürt automatikus skálázási határozza meg, hogy szükség-e egy az megváltoztatására, az AKS-fürtben található csomópontok számának növelése vagy csökkenése ennek megfelelően. AKS-fürt fürt automatikus méretező használatához futtassa a virtual machine scale sets a méretezési csoport kezeléséhez be, és vertikális leskálázás az AKS-csomópontok események.

![Kubernetes-fürt méretező](media/concepts-scale/cluster-autoscaler.png)

Fürt méretező általában a podok horizontális méretező együtt használatos. Együttesen a podok horizontális méretező növeli vagy csökkenti az alkalmazás igény szerinti podjainak számát, és a fürt méretező ezen újabb podok ennek megfelelően futtatása igény szerint állítja be a csomópontok számát.

Első lépések a fürt méretező az aks-ben, lásd: [fürt méretező az aks-en][aks-cluster-autoscaler].

### <a name="scale-up-events"></a>Vertikális felskálázás események

Ha egy csomópont nem rendelkezik elegendő számítási erőforrás kért podot futtatni, a pod az ütemezési folyamat nem halad. A pod nem indítható el, kivéve, ha a csomópont a készletben lévő érhetők el további számítási erőforrásokat.

Fürt automatikus méretező megjegyzések podok, hogy a csomópont készlethez korlátozott erőforrások miatt nem lehet ütemezni, amikor a csomópont a készletben lévő csomópontok számát adja meg a további számítási erőforrásokat ra emelkedett. Ha további csomópontjaira sikeresen rendszerbe állított és elérhető a csomópont készleten belüli használatra, a podok majd ütemezésére rajtuk.

Ha az alkalmazás számára, szüksége van, néhány podok ütemezve mindaddig, amíg a fürt automatikus méretező által telepített további csomópontokat is fogadja el az ütemezett podok várakozó állapotban maradhatnak. Megnövekvő a növekvő igények szerint rendelkező alkalmazások méretezheti a virtuális csomópontok és az Azure Container Instances szolgáltatásban.

### <a name="scale-down-events"></a>Vertikális leskálázás események

Fürt automatikus méretező is figyeli a pod ütemezés, amely mostanában nem érkezett meg új ütemezési kérések csomópont állapotát. Ebben a forgatókönyvben azt jelzi, hogy a csomópont készlethez rendelkezik-e szükség további számítási erőforrásokat, és, hogy a csomópontok száma csökkenthető.

Egy csomópont, amelyet átad egy küszöbértéket már folyamatban van szükség 10 percig alapértelmezés szerint a törlésre van ütemezve. Ez a helyzet akkor fordul elő, amikor podok ütemezett a csomópont készleten belüli más csomópontokon való futáshoz, és a fürt méretező csökkenti a csomópontok számát.

Az alkalmazások bizonyos megszakítás tapasztalhat, podok különböző csomópontokon van ütemezve, amikor a fürt méretező csökkenti a csomópontok száma szerint. Leskálázáskor, kerülje az alkalmazásokat, amelyek egyetlen pod-példányt használ.

## <a name="burst-to-azure-container-instances"></a>A csúcsterhelések átirányítása az Azure Container Instances szolgáltatásban

Az AKS-fürt gyors vertikális felskálázásához integrálható az Azure Container Instances (ACI). Kubernetes méretezése a replika és a csomópont száma beépített összetevőket tartalmazza. Az alkalmazásnak kell gyorsan méretezhető, ha azonban a podok horizontális méretező ütemezése előfordulhat, hogy további podok, mint a csomópont a készletben található meglévő számítási erőforrások adható meg. Ha konfigurálva, ebben a forgatókönyvben majd indít a fürt méretező üzembe helyezéséhez a csomópont a készletben lévő további csomópontokat, de eltarthat néhány percig, azokat a csomópontokat a sikeres üzembe helyezése, és lehetővé teszi a Kubernetes-ütemező podok futtathatók.

![Kubernetes-kapacitásnövelés méretezés az aci Szolgáltatásban](media/concepts-scale/burst-scaling.png)

Az ACI gyors üzembe helyezése a tárolópéldányok további infrastruktúra terhelését anélkül teszi lehetővé. A csatlakozáskor az aks-sel az ACI válik az AKS-fürt biztonságos, logikai kiterjesztését. A Virtual Kubelet telepítve van a az AKS-fürt, amely az ACI virtuális Kubernetes-csomópontként jeleníti meg. Kubernetes majd ütemezheti az AKS-fürt közvetlenül a Virtuálisgép-csomópontok ACI-példány virtuális csomópontok keresztül, nem pedig a podok futtató podok.

Az alkalmazás használatához a virtuális csomópontok bármilyen módosítás nélkül igényel. Központi telepítések méretezheti az AKS és az ACI és a késleltetés nélkül fürtként méretező helyez üzembe az AKS-fürt az új csomópontok.

Virtuális csomópontok üzembe helyezése az AKS-fürt ugyanazon a virtuális hálózaton található további alhálózathoz. Ez a virtuális hálózati konfiguráció lehetővé teszi, hogy a forgalom között az ACI és az AKS védeni kell. AKS-fürt, például egy ACI-példány, amely el van különítve a többi felhasználó biztonságos, logikai számítási erőforrás.

## <a name="next-steps"></a>További lépések

Alkalmazások méretezése használatának megkezdéséhez először hajtsa végre a [rövid útmutatóban egy AKS-fürt létrehozása az Azure CLI-vel][aks-quickstart]. Ezután megkezdheti manuálisan vagy automatikusan skálázható alkalmazásokat az AKS-fürt:

- Manuális méretezése [podok] [ aks-manually-scale-pods] vagy [csomópontok][aks-manually-scale-nodes]
- Használja a [podok horizontális méretező][aks-hpa]
- Használja a [méretező fürt][aks-cluster-autoscaler]

További információ az alapvető Kubernetes és a AKS fogalmakkal a következő cikkekben talál:

- [Kubernetes AKS-fürtök / és a számítási feladatok][aks-concepts-clusters-workloads]
- [Kubernetes / AKS hozzáférési és azonosító][aks-concepts-identity]
- [Kubernetes / AKS biztonsági][aks-concepts-security]
- [Kubernetes / AKS virtuális hálózatok][aks-concepts-network]
- [Kubernetes és az AKS-tároló][aks-concepts-storage]

<!-- LINKS - external -->

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
