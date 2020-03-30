---
title: Liveness-mintavétel beállítása a tárolópéldányon
description: Megtudhatja, hogyan konfigurálhatja a liveness-mintavételeket a nem megfelelő állapotú tárolók újraindításához az Azure Container Instances-ban
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 11c6c9d39067c536bf4325f74eb24b2ab64ef515
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934162"
---
# <a name="configure-liveness-probes"></a>Üzemelési tesztek konfigurálása

A tárolóba rakható alkalmazások hosszabb ideig futhatnak, ami hibás állapotokat eredményezhet, amelyeket a tároló újraindításával esetleg meg kell javítani. Az Azure Container Instances támogatja a liveness mintavételek, így konfigurálhatja a tárolók a tárolócsoporton belül újraindul, ha a kritikus funkciók nem működik. Az élősdi szonda úgy viselkedik, mint egy [Kubernetes élésszonda.](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)

Ez a cikk bemutatja, hogyan telepíthet egy olyan tárolócsoportot, amely egy liveness-mintavételt tartalmaz, amely bemutatja a szimulált nem megfelelő állapotú tároló automatikus újraindítását.

Az Azure Container Instances is támogatja [a készenléti mintavételek,](container-instances-readiness-probe.md)amely konfigurálhatja annak biztosítására, hogy a forgalom eléri a tárolót csak akkor, ha készen áll rá.

> [!NOTE]
> Jelenleg nem használhat liveness-mintavételt egy virtuális hálózatra telepített tárolócsoportban.

## <a name="yaml-deployment"></a>YAML-telepítés

Hozzon `liveness-probe.yaml` létre egy fájlt a következő kódrészlettel. Ez a fájl egy NGNIX-tárolóból álló tárolócsoportot határoz meg, amely végül nem kifogástalanállapotúvá válik.

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

Futtassa a következő parancsot a tárolócsoport fenti YAML-konfigurációval történő központi telepítéséhez:

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>Start parancs

A központi `command` telepítés tartalmaz egy tulajdonságot, amely meghatározza a kiindulási parancsot, amely a tároló első futásakor fut. Ez a tulajdonság karakterláncok tömbjét fogadja el. Ez a parancs szimulálja a sérült állapotba lépő tárolót.

Először is, elindítja a bash `healthy` munkamenetet, és létrehoz egy fájlt hívott a `/tmp` könyvtárban. Ezután 30 másodpercig alvó állapotban van a fájl törlése előtt, majd 10 perces alvó állapotba lép:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

### <a name="liveness-command"></a>Liveness parancs

Ez a központi `livenessProbe` telepítés határozza `exec` meg, amely támogatja a liveness parancsot, amely működik, mint a liveness ellenőrzés. Ha ez a parancs nem nulla értékkel lép ki, a tároló `healthy` leesik és újraindul, jelezve, hogy a fájl nem található. Ha ez a parancs sikeresen kilép a 0-s kilépési kóddal, a művelet nem történik.

A `periodSeconds` tulajdonság azt a liveness parancsot jelöli, amelyet 5 másodpercenként kell végrehajtani.

## <a name="verify-liveness-output"></a>Az élősdés kimenetének ellenőrzése

Az első 30 másodpercen belül létezik a start parancs által létrehozott `healthy` fájl. Amikor a Liveness parancs `healthy` ellenőrzi a fájl létezését, az állapotkód 0 értéket ad vissza, ami sikeres, így nem történik újraindítás.

30 másodperc elteltével a `cat /tmp/healthy` parancs meghibásodik, ami egészségtelen és ölő eseményeket okoz.

Ezek az események az Azure Portalon vagy az Azure CLI-ben tekinthetők meg.

![A portál nem megfelelő állapotú eseménye][portal-unhealthy]

Az Események megtekintése az Azure Portalon, a típusú `Unhealthy` események aktiválódnak a liveness parancs sikertelen. A következő esemény `Killing`típusa , amely egy tároló törlését jelzi, így az újraindítás megkezdődhet. Az újraindítások száma a tároló lépésekben minden egyes alkalommal, amikor ez az esemény bekövetkezik.

Az újraindítások helyben fejeződnek be, így az erőforrások, például a nyilvános IP-címek és a csomópontspecifikus tartalmak megmaradnak.

![Portál újraindítási számlálója][portal-restart]

Ha a liveness-mintavétel folyamatosan meghibásodik, és túl sok újraindítást indít el, a tároló exponenciális visszalépési késleltetésbe lép.

## <a name="liveness-probes-and-restart-policies"></a>Liveness-mintavételek és újraindítási házirendek

A házirendek újraindítása felülírja a liveness-mintavételek által kiváltott újraindítási viselkedést. Ha például beállít `restartPolicy = Never` egy *és* egy liveness mintavételt, a tárolócsoport nem indul újra egy sikertelen liveness-ellenőrzés miatt. A tárolócsoport ehelyett a tárolócsoport újraindítási `Never`házirendjéhez tart.

## <a name="next-steps"></a>További lépések

A feladatalapú forgatókönyvek esetében szükség lehet egy liveness-mintavételre az automatikus újraindításengedélyezéséhez, ha egy előfeltételfunkció nem működik megfelelően. A feladatalapú tárolók futtatásáról a [Tárolóalapú feladatok futtatása az Azure Container Instances alkalmazásban című témakörben talál](container-instances-restart-policy.md)további információt.

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
