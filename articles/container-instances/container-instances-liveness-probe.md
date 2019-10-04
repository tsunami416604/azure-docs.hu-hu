---
title: Az élettartam-mintavétel konfigurálása Azure Container Instances
description: Megtudhatja, hogyan konfigurálhat élő mintavételt a nem megfelelő állapotú tárolók újraindításához Azure Container Instances
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: danlep
ms.openlocfilehash: 28205d6db85d7a5051f283445d95dd2375e174c8
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325874"
---
# <a name="configure-liveness-probes"></a>Üzemelési tesztek konfigurálása

A tároló alkalmazások hosszabb ideig futhatnak, így a tároló újraindításával megsérült állapotokat okozhatnak. Azure Container Instances támogatja az élettartam-mintavételt a konfigurációk belefoglalásához, hogy a tároló újraindítható legyen, ha a kritikus funkció nem működik.

Ez a cikk azt ismerteti, hogyan helyezhet üzembe olyan tároló csoportot, amely tartalmaz egy élő vizsgálatot, amely egy szimulált, nem kifogástalan állapotú tároló automatikus újraindítását szemlélteti.

## <a name="yaml-deployment"></a>YAML üzembe helyezése

Hozzon `liveness-probe.yaml` létre egy fájlt a következő kódrészlettel. Ez a fájl egy olyan NGNIX-tárolót határoz meg, amely végül sérült állapotba kerül.

```yaml
apiVersion: 2018-06-01
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

Az üzemelő példány azt a kiindulási parancsot határozza meg, amely akkor fut le, amikor `command` a tároló először fut, és a tulajdonság határozza meg, amely karakterláncok tömbjét fogadja el. Ebben a példában egy bash-munkamenetet indít el, és létrehoz egy nevű `healthy` fájlt a `/tmp` címtárban a következő parancs átadásával:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

 Ezután 30 másodpercig alvó állapotba kerül, mielőtt törölné a fájlt, majd 10 perces alvó állapotba lép.

### <a name="liveness-command"></a>Az élettartam parancs

Ez az üzemelő `livenessProbe` példány egy olyan `exec` élet-ellenőrzési parancsot támogat, amely az élő ellenőrzések során működik. Ha a parancs nem nulla értékkel kilép, a rendszer leállítja és újraindítja a tárolót, és nem találta meg a `healthy` fájlt. Ha a parancs sikeresen kilép a 0. kilépési kóddal, a rendszer nem végez műveletet.

Ha `periodSeconds` a tulajdonságot kijelöli, a Lives parancsot 5 másodpercenként végre kell hajtani.

## <a name="verify-liveness-output"></a>Az élettartam kimenetének ellenőrzése

Az első 30 másodpercen `healthy` belül a Start parancs által létrehozott fájl létezik. Ha az élettartam parancs megkeresi a `healthy` fájl létezését, az állapotkód nulla értéket ad vissza, ami sikeres, ezért nem történik újraindítás.

30 másodperc elteltével a megkezdi a `cat /tmp/healthy` hibát, ami nem megfelelő állapotba kerül, és az események megölése történik.

Ezek az események a Azure Portal vagy az Azure CLI-ből is megtekinthetők.

![Portál sérült eseménye][portal-unhealthy]

Ha megtekinti az eseményeket a Azure Portalban, a `Unhealthy` típusú események az élettartam parancs meghibásodása után lesznek aktiválva. A következő esemény típusa `Killing`lesz, és a tároló törlését jelzi, hogy az újraindítás megkezdődhet. A tároló újraindítási száma minden alkalommal megnövekszik.

Az újraindítások helyben, így az erőforrások, például a nyilvános IP-címek és a csomópont-specifikus tartalmak is megmaradnak.

![Portál újraindítási számlálója][portal-restart]

Ha az élettartam-mintavétel folyamatosan leáll, és túl sok újraindítást indít el, a tároló exponenciális visszalépési késleltetést ad meg.

## <a name="liveness-probes-and-restart-policies"></a>Élettartam-vizsgálatok és újraindítási szabályzatok

Az újraindítási szabályzatok felülírják az élő tesztek által aktivált újraindítási viselkedést. Ha például beállít egy `restartPolicy = Never` *és* egy élő mintavételt, akkor a rendszer nem indítja újra a tároló csoportot a sikertelenség-ellenőrzések esetén. A tároló csoport Ehelyett a tároló csoport újraindítási szabályzatát `Never`fogja követni.

## <a name="next-steps"></a>További lépések

A feladaton alapuló forgatókönyvek esetében szükség lehet az automatikus újraindításra, ha az előfeltételként szükséges függvény nem működik megfelelően. A Task-alapú tárolók futtatásával kapcsolatos további információkért lásd: [tárolós feladatok futtatása Azure Container Instancesban](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
