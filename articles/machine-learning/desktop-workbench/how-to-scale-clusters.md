---
title: A Machine Learning az Azure Container Service-fürt méretezése |} A Microsoft Docs
description: ACS-fürt – az automatikus méretezés és a statikus méretezés; méretezése a fürtben található csomópontok számának méretezése
services: machine-learning
author: aashishb
ms.author: aashishb
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/04/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 9688b9ba305a2eb59b80b02c0b41a7f4855dd051
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50024562"
---
# <a name="scaling-the-cluster-to-manage-web-service-throughput"></a>A web service átviteli sebesség kezelésére fürt méretezése

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]


## <a name="why-scale-the-cluster"></a>A fürt átméretezése miért?

Az Azure Container Service (ACS) fürt méretezése a hatékony módja a fürt méretét a költségek csökkentése érdekében legalább megtartásával szolgáltatás optimalizálása érdekében. 

Automatikus méretezés segít jobban megérteni, vegye figyelembe az alábbi példa három webes szolgáltatásokat futtató fürt:

![Példa: Három szolgáltatás található a fürt](media/how-to-scale-clusters/three-services.png)

A szolgáltatások különböző megnövekedett igényeket kell kielégíteni rendelkezik: Service 1 (kék vonal) igényel, megugrása 40 podok, a szolgáltatás 2 (narancssárga vonal) csúcsidőszakain 38 igényel, és a szolgáltatás 3 (szürke vonal) igényel csúcsidőszakain 50. Ha külön-külön lefoglalni az egyes szolgáltatások a szükséges maximális kapacitás, a fürt legalább 40 + 38 + 50 = 128 kell podok száma.

De vegye figyelembe a tényleges pod használati bármikor időben, a Fekete szaggatott vonal a gráfban. Ebben az esetben a *egyidejűleg használt podok számát vesszük figyelembe* van 64, amely Service 3 csúcsidőszakain esetén 20:00-kor történik. Jelenleg Service 3 50 podok használ, de Service 2 használ csupán 9 podok és Service 1 csak 5 használja. Ne feledje, ez a *használati csúcsot* ehhez a fürthöz. Ez azt jelenti, hogy nem ezeket a fürt használja több mint 64 pods – 128 podok jelentenének egymástól függetlenül méretezi a három szolgáltatások fele a számított követelmény.

Újbóli megadásával podok, hogy a fürt átméretezésekor--által az egyes szolgáltatások az aktuális igényt kielégítő egyszerűen szükséges elegendő erőforrással az összes szolgáltatás megugrása helyett csökkentheti a fürt méretét. Ez az egyszerű példában az automatikus skálázás csökkenti a szükséges 64, a szükséges fürtméret darabolás megfelezése 128 a podok számát.

Podok számának méretezése egy viszonylag gyors művelet, kevesebb mint egy percet igénylő, így a szolgáltatás válaszidejét komolyan nem változik.

> [!NOTE]
> Fürt méretezése nem segít a kérelem késési problémák. Célból porttovábbításon vertikális felskálázása kell növelni a sikeres műveletek számát, így csökken a szolgáltatás nem érhető el. 

## <a name="how-to-scale-web-services-on-your-acs-cluster"></a>Az ACS-fürtben a webszolgáltatások méretezése

A fürt telepítési beállítással a modellkezelési parancssori felület alapértelmezés szerint a két ügynök és a egy pod konfigurálja a környezetben. A Kubernetes parancssori Felületét is telepíti.

Méretezheti a webes szolgáltatások központilag telepített ACS módosításával:

* Az ügynök a fürtben található csomópontok száma
* Az ügynök csomópontokon futó Kubernetes podreplikák száma

### <a name="scaling-the-number-of-nodes-in-the-cluster"></a>A fürtben található csomópontok számának méretezése

A következő parancsot az ügynökcsomópontok számát a fürtben állítja be:

```
az acs scale -g <resource group> -n <cluster name> --new-agent-count <new scale>
```

Ez eltarthat pár percig. A fürtben található csomópontok számának méretezéssel kapcsolatos további információkért lásd: [ügynökcsomópontok méretezése a Container Service-fürt](https://docs.microsoft.com/azure/container-service/container-service-scale).

### <a name="scaling-the-number-of-kubernetes-pod-replicas-in-a-cluster"></a>A Kubernetes podreplikák száma méretezése a fürtben
 
Méretezhető, a fürt az Azure Machine Learning parancssori felület használatával podreplikák száma vagy a [Kubernetes-irányítópult](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/).

A Kubernetes-podok replika további információkért lásd: a [Kubernetes-Podok](https://kubernetes.io/docs/concepts/workloads/pods/pod/) dokumentációját.

#### <a name="scaling-a-cluster-with-the-azure-machine-learning-cli"></a>Az Azure Machine Learning parancssori fürtök méretezése

A parancssori felületről fürtök skálázásának két módja van:

- Automatikus méretezés
- Statikus skála

Automatikus skálázási alapértelmezés szerint aktív, ha a szolgáltatás jön létre, és a legtöbb esetben az előnyben részesített méretezési módszer.

##### <a name="autoscale"></a>Automatikus méretezés

A következő parancs lehetővé teszi az automatikus méretezés, és a szolgáltatás-replikák minimális és maximális számát.

```
az ml service update realtime -i <service id> --autoscale-enabled true --autoscale-min-replicas <positive number> --autoscale-max-replicas <positive number>
```

Például beállítás `autoscale-min-replicas` 5 hoz létre öt replikákat. A webszolgáltatás optimális számú érkeznek, száma például 10-es értékre állítva, és figyelheti az 503-as hiba üzenetek száma. Majd ennek megfelelően módosítsa a számot.


| Paraméter neve | Típus | Leírás |
|--------------------|--------------------|--------------------|
| `autoscale-enabled` | logikai | Itt adhatja meg, hogy engedélyezve van-e az automatikus méretezés. Alapértelmezett: igaz |
| `autoscale-min-replicas` | egész szám | Megadja a podok minimális számát. 0 vagy nagyobb lehet. Alapértelmezett: 1 |
| `autoscale-max-replicas` | egész szám | Megadja a podok maximális számát. 1 vagy nagyobb lehet. Ha a replikák maximális száma az automatikus méretezés kisebb, mint a replikák minimális száma az automatikus méretezés, a replikák maximális száma az automatikus méretezés figyelmen kívül. Alapértelmezett: 10 |
| `autoscale-refresh-period-seconds` | egész szám | Megadja azt az időtartamot másodpercben között az automatikus méretezés. Alapértelmezett: 1 |
| `autoscale-target-utilization` | egész szám | Határozza meg, hogy automatikus méretezési célokat, 1 és 100 közötti kihasználtsági százalék. Alapértelmezett: 70 |

Automatikus skálázási annak biztosítása érdekében az alábbi két feltétel használható:

1. A célkihasználtság teljesül.
2. Skálázás soha nem meghaladja a minimális és maximális beállítások

Fürterőforrások versenyezzen szolgáltatások a fürtben. Egy maximumára szolgáltatás növeli a fürt erőforrás-használat, a kérések száma második (RPS) növeli, és lassan közzéteszik erőforrásokhoz, mint az RPS csökken. Fürterőforrások fog lehet lekérni az igény szerinti, mindaddig, amíg az ilyen erőforrások léteznek beszerezni a szolgáltatás.

Az automatikus skálázási paraméterek használatával kapcsolatos további információkért lásd: a [felügyeleti parancssori felület hivatkozás](model-management-cli-reference.md) dokumentációját.

##### <a name="static-scale"></a>Statikus skála

Általában statikus skálázás el kell kerülni, mivel nem teszi lehetővé a fürt méretének csökkentése az automatikus skálázás. Még ha így bizonyos helyzetekben statikus skálázás előfordulhat, hogy felhasználóitevékenység. Például ha egy fürt egy egyetlen szolgáltatással van hozzárendelve, az automatikus skálázás haszna nincs; az összes szolgáltatás hozzá kell rendelni.

Annak érdekében, hogy statikusan fürt horizontális fel-, az automatikus skálázás ki kell kapcsolni. Tiltsa le az automatikus méretezés, a következő paranccsal:

```
az ml service update realtime -i <service id> --autoscale-enabled false
```

Után kapcsolja ki az automatikus méretezés, a következő parancsot közvetlenül skálázható szolgáltatás replikák száma.

```
az ml service update realtime -i <service id> -z <replica count>
```
 
A fürtben található csomópontok számának méretezéssel kapcsolatos további információkért tekintse meg az ügynökcsomópontok méretezése a Container Service-fürtökben.

#### <a name="scaling-number-of-replicas-using-the-kubernetes-dashboard"></a>A Kubernetes-irányítópult alkalmazó méretezési száma

A parancssorban adja meg:

```
kubectl proxy
```

A Windows a Kubernetes telepítési helyet nem automatikusan kerül az elérési úthoz. Először keresse meg a telepítési mappát:

```
c:\users\<user name>\bin
```

A parancs futtatása után a következő tájékoztató üzenetnek kell megjelennie:

```
Starting to serve on 127.0.0.1:8001
```

Ha a port már használatban van, az alábbi példához hasonló üzenet jelenik meg:

```
F0612 21:49:22.459111   59621 proxy.go:137] listen tcp 127.0.0.1:8001: bind: address already in use
```

Megadhat egy alternatív port számát a a *– port* paraméter.

```
kubectl proxy --port=8010
Starting to serve on 127.0.0.1:8010
```

Az irányítópult kiszolgáló megkezdése után nyisson meg egy böngészőt, és adja meg a következő URL-címe:

```
127.0.0.1:<port number>/ui
```

Az irányítópult fő képernyőjén kattintson **központi telepítések** a bal oldali navigációs sávon. A navigációs ablaktáblában nem jelenik meg, ha ez az ikon kiválasztása ![rövid három vízszintes vonal álló menü](media/how-to-scale-clusters/icon-hamburger.png) a bal felső sarokban.

Keresse meg és módosításához kattintson erre az ikonra az üzembe helyezés ![három függőleges pontot alkotó ikon álló menüikonra](media/how-to-scale-clusters/icon-kebab.png) a jobb oldali majd **megtekintése és szerkesztése YAML**.

Keresse meg a központi telepítési szerkesztési képernyőhöz, a *specifikációja* csomópont, módosítsa a *replikák* értéket, és kattintson a **frissítés**.
