---
title: Az élettartam mintavételének beállítása a Container-példányon
description: Megtudhatja, hogyan konfigurálhat élő mintavételt a nem megfelelő állapotú tárolók újraindításához Azure Container Instances
ms.topic: article
ms.date: 06/08/2018
ms.openlocfilehash: 566f7952aff1cf460272fbb418a2a0efff411881
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901892"
---
# <a name="configure-liveness-probes"></a>Üzemelési tesztek konfigurálása

A tároló alkalmazások hosszabb ideig is futhatnak, így a tároló újraindításával megsérült állapotok jelenhetnek meg. Azure Container Instances támogatja az élő mintavételeket, így a tárolók csoportján belül is konfigurálhatja a tárolókat, hogy újrainduljon, ha a kritikus funkció nem működik. Az élettartam-mintavétel a [Kubernetes-élettartam](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)mintavételéhez hasonló.

Ez a cikk azt ismerteti, hogyan helyezhet üzembe olyan tároló csoportot, amely tartalmaz egy élő vizsgálatot, amely egy szimulált, nem kifogástalan állapotú tároló automatikus újraindítását szemlélteti.

A Azure Container Instances támogatja a [készültségi](container-instances-readiness-probe.md)mintavételt is, amelyekkel beállíthatja, hogy a forgalom csak akkor legyen elérhető, amikor készen áll a tárolóra.

> [!NOTE]
> Jelenleg nem használhat élő mintavételt egy virtuális hálózatra központilag telepített tároló-csoportban.

## <a name="yaml-deployment"></a>YAML üzembe helyezése

Hozzon létre egy `liveness-probe.yaml` fájlt a következő kódrészlettel. Ez a fájl egy olyan NGNIX-tárolót határoz meg, amely végül sérült állapotba kerül.

```yaml
apiVersion: 2018-10-01
location: eastus
name: livenesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: nginx
      command:
        - "/bin/sh"
        - "-c"
        - "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      livenessProbe:
        exec:
            command:
                - "cat"
                - "/tmp/healthy"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Futtassa a következő parancsot a tároló csoport a fenti YAML-konfigurációval való üzembe helyezéséhez:

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>Start parancs

Az üzemelő példány egy kiindulási parancsot határoz meg, amely akkor fut le, amikor a tároló először fut, a `command` tulajdonság határozza meg, amely karakterláncok tömbjét fogadja el. Ebben a példában egy bash-munkamenetet indít el, és létrehoz egy `healthy` nevű fájlt a `/tmp` könyvtárban a következő parancs átadásával:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

 Ezután 30 másodpercig alvó állapotba kerül, mielőtt törölné a fájlt, majd 10 perces alvó állapotba lép.

### <a name="liveness-command"></a>Az élettartam parancs

Ez az üzemelő példány olyan `livenessProbe`t határoz meg, amely támogatja az élő ellenőrzések során `exec` életbe lépő parancsot. Ha a parancs nullától eltérő értékkel kilép, a rendszer leállítja és újraindítja a tárolót, és a `healthy` fájl nem található. Ha a parancs sikeresen kilép a 0. kilépési kóddal, a rendszer nem végez műveletet.

Az `periodSeconds` tulajdonság meghatározza, hogy a Lives parancs 5 másodpercenként legyen végrehajtva.

## <a name="verify-liveness-output"></a>Az élettartam kimenetének ellenőrzése

Az első 30 másodpercen belül a Start parancs által létrehozott `healthy` fájl létezik. Ha az élettartam parancs ellenőrzi a `healthy` fájl létezését, az állapotkód nulla értéket ad vissza, ami sikeres, ezért nincs újraindítás.

30 másodperc elteltével a `cat /tmp/healthy` sikertelen lesz, ami nem megfelelő állapotot okoz, és az események megölése történik.

Ezek az események a Azure Portal vagy az Azure CLI-ből is megtekinthetők.

![Portál sérült eseménye][portal-unhealthy]

A Azure Portal eseményeinek megtekintésével a rendszer a `Unhealthy` típusú eseményeket az élettartam parancs végrehajtása után indítja el. A következő esemény `Killing`típusú lesz, amely a tároló törlését jelzi, hogy az újraindítás megkezdődhet. A tároló újraindítási száma az esemény bekövetkezésekor növekszik.

Az újraindítások helyben, így az erőforrások, például a nyilvános IP-címek és a csomópont-specifikus tartalmak is megmaradnak.

![Portál újraindítási számlálója][portal-restart]

Ha az élettartam-mintavétel folyamatosan leáll, és túl sok újraindítást indít el, a tároló exponenciális visszalépési késleltetést ad meg.

## <a name="liveness-probes-and-restart-policies"></a>Élettartam-vizsgálatok és újraindítási szabályzatok

Az újraindítási szabályzatok felülírják az élő tesztek által aktivált újraindítási viselkedést. Ha például beállít egy `restartPolicy = Never`t *és* egy élő mintavételt, a tároló csoport nem indul újra, mert sikertelen az élettartam-ellenőrzési művelet. A tároló csoport Ehelyett a tároló csoport újraindítási szabályzatát fogja betartani `Never`.

## <a name="next-steps"></a>Következő lépések

A feladaton alapuló forgatókönyvek esetében szükség lehet az automatikus újraindításra, ha az előfeltételként szükséges függvény nem működik megfelelően. A Task-alapú tárolók futtatásával kapcsolatos további információkért lásd: [tárolós feladatok futtatása Azure Container Instancesban](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
