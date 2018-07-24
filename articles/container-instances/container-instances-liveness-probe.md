---
title: Az Azure Container Instances szolgáltatásban liveness mintavétel konfigurálása
description: Ismerje meg, indítsa újra a nem megfelelő állapotú tárolók az Azure Container Instances szolgáltatásban liveness mintavételek konfigurálása
services: container-instances
author: jluk
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: juluk
ms.openlocfilehash: 1582f0d7ec688bc72cc9d1aa6ae0ddb0a6ad3a17
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213071"
---
# <a name="configure-liveness-probes"></a>Üzemelési tesztek konfigurálása

Megszakadt az államok lehet javítani kell, a tároló újraindításával eredményez hosszabb ideig is futhat, tárolóalapú alkalmazásokat. Az Azure Container Instances támogatja liveness mintavételek konfigurációkat tartalmazza, hogy a tároló újraindíthatja, ha kritikus funkció nem működik.

Ez a cikk azt ismerteti, hogyan helyezhet üzembe egy tárolócsoport, amely tartalmazza a működőképesség bemutatásához az automatikus újraindítást egy szimulált nem megfelelő tároló.

## <a name="yaml-deployment"></a>YAML-telepítés

Hozzon létre egy `liveness-probe.yaml` fájlt a következő kódrészletre. Ez a fájl határozza meg egy tárolócsoport, amely egy NGNIX tároló, amely végül sérült áll.

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

Futtassa a következő parancsot az a tárolócsoportot a fenti YAML-konfiguráció üzembe helyezése:

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>Indítási parancs

A központi telepítés kezdési parancsot futtatja, amikor a tároló első elindításakor fut, által meghatározott határozza meg a `command` tulajdonság, amely elfogadja a karakterláncok tömbje. Ebben a példában azt elindítja egy bash-munkamenetet, és hozzon létre egy fájlt nevű `healthy` belül a `/tmp` könyvtárat adja át ezzel a paranccsal:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

 Majd a fájl törlése előtt 30 másodpercig fog alvó állapotba, majd egy 10 perces alvó állapotra váltana.

### <a name="liveness-command"></a>Liveness parancs

A telepítés határozza meg, egy `livenessProbe` támogatja egy `exec` liveness parancsot, amely a liveness ellenőrzés funkcionál. Ez a parancs kilép, nem nulla értékű, ha a tároló leállított és újraindul, jelzés a `healthy` fájl nem található. Ha ez a parancs sikeresen kilép a 0 kilépési kóddal, semmilyen művelet nem lesz végrehajtva.

A `periodSeconds` tulajdonság jelöli meg a liveness parancsot kell végrehajtani, 5 másodpercenként.

## <a name="verify-liveness-output"></a>Liveness kimenet ellenőrzése

Az első 30 másodpercen belül a `healthy` az indítási paranccsal létrehozott fájl létezik-e. Ha a liveness parancs ellenőrzi a `healthy` fájl létezik-e az állapotkódot adja vissza egy nulla jelzés sikeres, így nincs újraindítás történik.

30 másodperc elteltével a `cat /tmp/healthy` elkezdi sikertelenek lesznek, nem megfelelő állapotú és leölését esemény előfordulása okozó.

Az Azure Portalon vagy az Azure CLI ezeket az eseményeket is megtekinthetők.

![Portál sérült állapotot jelző esemény][portal-unhealthy]

Az események megtekintése az Azure Portalon, az események típus szerint `Unhealthy` a liveness parancs sikertelen után aktiválódik. Az ezt követő esemény lesz típusú `Killing`, a tároló törléséhez hangsúlyozva, így megkezdheti az újraindítást. A tároló újraindítási számát növeli minden alkalommal, amikor ez akkor fordul elő.

Újraindítást végezhető el helyben, az erőforrások, például a nyilvános IP-címek és a rendszer megőrzi a konkrét csomóponthoz tartozó tartalom.

![Portál újraindítása számláló][portal-restart]

Ha a működőképesség folyamatosan meghiúsul, és túl sok újraindítást kezdeményez, a tároló egy exponenciális visszatartási késleltetés adja meg.

## <a name="liveness-probes-and-restart-policies"></a>Liveness mintavételek és újraindítási házirendek

Újraindítási házirendek felülírják az újraindítási viselkedés liveness mintavételek váltott. Például, ha egy `restartPolicy = Never` *és* végrehajtandó működőképességi egy, a tárolócsoport nem indítja újra egy sikertelen liveness ellenőrzés esetén. A tárolócsoport helyett csatlakozhat a tárolócsoport újraindítási szabályzata `Never`.

## <a name="next-steps"></a>További lépések

A feladat-alapú forgatókönyvek lehet szükség a működőképesség automatikus újraindításának engedélyezéséhez, ha egy előfeltételként függvény nem működik megfelelően. Futó tárolók feladataival kapcsolatos további információkért lásd: [tárolóalapú feladatok futtatása az Azure Container Instances szolgáltatásban](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
