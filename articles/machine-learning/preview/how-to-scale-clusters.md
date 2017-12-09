---
title: "Az Azure Tárolószolgáltatás-fürt méretezése a Machine Learning szolgáltatáshoz |} Microsoft Docs"
description: "Az ACS-fürt - automatikus skálázás és statikus skálázás; skálázás a fürt a csomópontok száma skálázás"
services: machine-learning
author: raymondl
ms.author: raymondl
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/04/2017
ms.openlocfilehash: 8d709936bfba5c89091d7f26449d165bddb930de
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="scaling-the-cluster-to-manage-web-service-throughput"></a>Webes szolgáltatás teljesítmény kezelését a fürt méretezése

## <a name="why-scale-the-cluster"></a>Miért érdemes méretezni a fürtön?

Az Azure tároló szolgáltatás (ACS) fürt skálázás be a szolgáltatás teljesítmény optimalizálása ugyanakkor változatlanul megőrizze a fürtméret költségek csökkentése a minimális hatékony módot. 

Jobb megértése érdekében automatikus skálázást, vegye figyelembe a következő példa egy három web services szolgáltatást futtató fürt:

![Példa: Mindhárom szolgáltatást egy fürtön](media/how-to-scale-clusters/three-services.png)

A szolgáltatások rendelkeznek különböző csúcs iránti igények kielégítése érdekében: Service 1 (kék sor) igényel 40 három munkaállomás-csoporttal csúcs igény szerinti, Service 2 (narancssárga sor) igényel csúcs: 38 és Service 3 (szürke sor) igényel a maximális 50. Ha külön-külön lefoglalni a szükséges maximális kapacitás az egyes szolgáltatásokhoz, a fürt legalább 40 + 38 + 50 = 128 kell teljes három munkaállomás-csoporttal.

De vegye figyelembe a tényleges pod használati bármikor időben, a Fekete szaggatott vonal jelzi a grafikonon képviseli. Ebben az esetben a *három munkaállomás-csoporttal egyidejűleg használt legnagyobb száma* van 64, amely akkor fordul elő, ha a szolgáltatás 3 csúcs jelenleg 20:00. Jelenleg a szolgáltatás 3 50 három munkaállomás-csoporttal, használja, de Service 2 csupán 9 három munkaállomás-csoporttal, és Service 1 használja csak az 5. Ne feledje, hogy ez *használati csúcsot* ehhez a fürthöz. Ez azt jelenti, hogy soha nem nem a fürt legfeljebb 64 három munkaállomás-csoporttal – 128 három munkaállomás-csoporttal egymástól függetlenül méretezi a használati csúcsot három szolgáltatások fele a számított követelmény.

Újbóli megadásával a fürt – Ez azt jelenti, hogy három munkaállomás-csoporttal által rescaling – egyszerűen elegendő erőforrással kérése az összes olyan szolgáltatás, a maximális igény szerinti helyett minden szolgáltatás az aktuális igény kielégítésére csökkentheti a fürt méretét. Ez az egyszerű példában automatikus skálázás csökkenti a három munkaállomás-csoporttal való 128 64, a szükséges fürtméret kivágja a fele szükséges számát.

Három munkaállomás-csoporttal számának méretezését egy olyan viszonylag gyors művelet, igénylő egy percen belül, így a szolgáltatás válaszidejét súlyosan nem változik.

> [!NOTE]
> A fürt skálázás nem segít kérelem késési problémák szempontjából. Operationalization célokra vertikális felskálázásával kell sikeres számának növeléséhez és csökkentéséhez a szolgáltatás nem érhető el. 

## <a name="how-to-scale-web-services-on-your-acs-cluster"></a>Webszolgáltatások az ACS-fürt méretezése

A fürt telepítési beállítással a modell felügyeleti parancssori felület alapértelmezés szerint két ügynökök és egy pod konfigurálja a környezetben. A Kubernetes CLI-t is telepíti.

A központilag telepített ACS beállításával webszolgáltatások méretezheti:

* A fürt ügynök csomópontok száma
* Az ügynök csomópontokon futó Kubernetes pod replikák száma

### <a name="scaling-the-number-of-nodes-in-the-cluster"></a>A fürt a csomópontok száma skálázás

A következő parancsot a fürt állítja be az ügynök csomópontok száma:

```
az acs scale -g <resource group> -n <cluster name> --new-agent-count <new scale>
```

Ez eltarthat néhány percig. A méretezés a csomópontok száma a fürtben lévő további információkért lásd: [ügynök csomópontok egy Tárolószolgáltatás-fürt méretezése](https://docs.microsoft.com/azure/container-service/container-service-scale).

### <a name="scaling-the-number-of-kubernetes-pod-replicas-in-a-cluster"></a>A fürt skálázás Kubernetes pod replikák száma
 
Méretezheti, a fürt használata az Azure Machine Learning parancssori felület vagy a [Kubernetes irányítópult] pod replikák száma (https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/).

Kubernetes replika három munkaállomás-csoporttal további információkért tekintse meg a [Kubernetes három munkaállomás-csoporttal](https://kubernetes.io/docs/concepts/workloads/pods/pod/) dokumentációját.

#### <a name="scaling-a-cluster-with-the-azure-machine-learning-cli"></a>Az Azure Machine Learning CLI fürt méretezése

A parancssori felület használatával fürt méretezése két módja van:

- Automatikus méretezés
- Statikus méretezési

Automatikus skálázás esetén alapértelmezés szerint aktív, a szolgáltatás jön létre, és a legtöbb esetben az elsődleges méretezési módszer.

##### <a name="autoscale"></a>Automatikus méretezés

A következő parancs lehetővé teszi, hogy automatikus méretezése, és beállítja a szolgáltatás replikák minimális és maximális számát.

```
az ml service update realtime -i <service id> --autoscale-enabled true --autoscale-min-replicas <positive number> --autoscale-max-replicas <positive number>
```

Például beállítása `autoscale-min-replicas` 5 a öt replikákat hoz létre. A webszolgáltatás optimális számú érkeznek, adjon meg az értékeket, például 10 és figyelése 503-as hiba üzenetek száma. Majd módosítsa ennek megfelelően a számot.


| Paraméter neve | Típus | Leírás |
|--------------------|--------------------|--------------------|
| `autoscale-enabled` | Logikai érték | Meghatározza, hogy engedélyezve van-e az automatikus skálázási. Alapértelmezett: igaz |
| `autoscale-min-replicas` | egész szám | Három munkaállomás-csoporttal minimális számát határozza meg. 0 vagy nagyobb szám lehet. Alapértelmezett: 1 |
| `autoscale-max-replicas` | egész szám | Meghatározza a három munkaállomás-csoporttal maximális számát. 1 vagy nagyobb lehet. Ha automatikus skálázás-maximális-replikák kisebb, mint az automatikus skálázás-min-replikák, automatikus skálázás-maximális-replikák figyelmen kívül hagyja. Alapértelmezett: 10 |
| `autoscale-refresh-period-seconds` | egész szám | Megadja azt az időtartamot másodpercben automatikus skálázás frissítései között. Alapértelmezett: 1 |
| `autoscale-target-utilization` | egész szám | Adja meg az automatikus skálázás fertőzi, 1 és 100 közötti kihasználtsági százalék. Alapértelmezett: 70 |

Automatikus skálázási működik, győződjön meg arról az alábbi két feltétel:

1. A cél-felhasználást teljesül.
2. Soha nem skálázás meghaladja a minimális és maximális beállítások

Egy fürt szolgáltatások "versenyeznek" a fürt erőforrásait. Egy skálázva szolgáltatás növeli a fürt erőforrás használatát, a kérések száma második (RPS) növekszik, és lassan ad ki az erőforrásokhoz, mint az RPS csökken. Fürt erőforrásait fogja lehet lekérni az igény szerinti, mindaddig, amíg az beszerzése szolgáltatás létezik ezekhez az erőforrásokhoz.

Az automatikus skálázás paraméterek használatával kapcsolatos további információkért lásd: a [modell kezelése parancssori felület útmutató](model-management-cli-reference.md) dokumentációját.

##### <a name="static-scale"></a>Statikus méretezési

Általában statikus skálázás kerülni kell, mert nem teszik lehetővé a fürt méretének csökkentése automatikus skálázást. Még ha így bizonyos esetekben statikus skálázás előfordulhat, hogy befolyásolható. Például akkor, ha a fürt egyetlen szolgáltatás van hozzárendelve, automatikus skálázás biztosít jár előnnyel; az összes szolgáltatás hozzá kell rendelni.

Ahhoz, hogy a fürt statikusan méretezéséhez automatikus skálázás ki kell kapcsolni. Tiltsa le az automatikus skálázás az alábbi paranccsal:

```
az ml service update realtime -i <service id> --autoscale-enabled false
```

Automatikus skálázás kikapcsolása, után a következő parancsot közvetlenül arányosan szolgáltatás replikák száma.

```
az ml service update realtime -i <service id> -z <replica count>
```
 
Skálázás csomópontok száma a fürtben lévő további információkért lásd: a Tárolószolgáltatás-fürt csomópontjának méretezési ügynök.

#### <a name="scaling-number-of-replicas-using-the-kubernetes-dashboard"></a>Az Kubernetes irányítópulttal replikák méretezési száma

A parancssorban adja meg:

```
kubectl proxy
```

A Windows a Kubernetes telepítési helyére nem automatikusan hozzáadódik az elérési út. Először keresse meg azt a telepítési mappát:

```
c:\users\<user name>\bin
```

A parancs futtatása után a következő tájékoztató üzenetet kell megjelennie:

```
Starting to serve on 127.0.0.1:8001
```

A port már használatban van, az alábbi példához hasonló üzenet jelenik meg:

```
F0612 21:49:22.459111   59621 proxy.go:137] listen tcp 127.0.0.1:8001: bind: address already in use
```

Megadhat egy alternatív port száma használatával a *– port* paraméter.

```
kubectl proxy --port=8010
Starting to serve on 127.0.0.1:8010
```

Az irányítópult kiszolgáló megkezdése után nyisson meg egy böngészőt, és adja meg a következő URL-címe:

```
127.0.0.1:<port number>/ui
```

A fő irányítópultot, kattintson **központi telepítések** a bal oldali navigációs sávon. Ha nem jelennek meg a navigációs panelen, jelölje be erre az ikonra ![menü három rövid vízszintes vonal álló](media/how-to-scale-clusters/icon-hamburger.png) bal felső.

Keresse meg a központi telepítés módosítása, és kattintson az ikonra ![három függőleges pontokból álló ikonjára](media/how-to-scale-clusters/icon-kebab.png) a jobb oldalon, majd **YAM megtekintése/szerkesztése**.

A Szerkesztés telepítési képernyőn, keresse meg a *spec* csomópont, módosítsa a *replikák* értékét, majd kattintson **frissítés**.
