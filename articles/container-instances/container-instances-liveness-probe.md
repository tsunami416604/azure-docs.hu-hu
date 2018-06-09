---
title: Azure-tároló példányát liveness mintavételt konfigurálása
description: Azure tároló példányban sérült tárolók újraindítására liveness mintavételt konfigurálása
services: container-instances
author: jluk
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: juluk
ms.openlocfilehash: 76ca4db28d99702532ae656a19f0d54b479a13fe
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35249325"
---
# <a name="configure-liveness-probes"></a>Liveness mintavételt konfigurálása

Indexelése alkalmazások futtathatja huzamosabb ideig eredményezve sérült állapotot is előfordulhat, hogy a tároló újraindításával javítani kell. Azure tároló példányokat támogatja liveness mintavételt konfigurációkat tartalmazza, hogy a tároló újraindíthatja, ha kritikus nem működőképes. 

Ez a cikk ismerteti a csoport központi telepítése egy tároló, amely egy liveness mintavételt tartalmazza, amely tartalmazza az automatikus újraindítás a szimulált nem megfelelő tároló.

## <a name="yaml-deployment"></a>YAM központi telepítés

Hozzon létre egy `liveness-probe.yaml` fájl a következő kódtöredékre. Ez a fájl egy tárolócsoport, amely egy NGNIX tároló, amely előfordulhat akkor kerül sérült állapotba áll határozza meg. 

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
  restartPolicy: Never
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

A következő parancsot a tároló csoport, a fenti YAM-konfigurációban való központi telepítéséhez:

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>Parancs indítását.

A központi telepítés határozza meg egy kezdő parancs futtatható a következő indításakor a tárolóban fut, határozzák meg a `command` tulajdonság, amely karakterláncokból álló elfogad. Ebben a példában ez fogja bash munkamenet indításához és nevű fájl létrehozása `healthy` belül a `/tmp` könyvtárhoz, úgy, hogy ezt a parancsot:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

 Fog majd alvó 30 másodpercig a fájl törlése előtt, majd egy 10 perces készenléti kerül.

### <a name="liveness-command"></a>Liveness parancs

A központi telepítés definiál egy `livenessProbe` amely támogatja az `exec` liveness parancs, amely különbséglemezként funkcionál az liveness ellenőrzése. Ha ez a parancs kilép, nem nulla értékű, a tároló leállítása és újraindítása jelzés a `healthy` fájl nem található. Ha ez a parancs sikeresen 0 kilépési kóddal kilép, nem művelet kerül végrehajtásra.

A `periodSeconds` tulajdonság jelöli a liveness parancsot kell végrehajtani minden 5 másodperc.

## <a name="verify-liveness-output"></a>Liveness kimeneti ellenőrzése

Az első 30 másodperce a `healthy` a start paranccsal létrehozott fájl létezik-e. Ha a liveness parancs ellenőrzi a `healthy` fájl megléte, az állapotkód adja vissza egy nulla jelzés sikeres, így nincs újraindítás történik.

30 másodperc után a `cat /tmp/healthy` megkezdődik sikertelen lesz, megtörténik a sérült és leölését események, amely. 

Ezeket az eseményeket az Azure portálon vagy az Azure CLI 2.0 is megtekinthetők.

![Portál sérült állapotot jelző esemény][portal-unhealthy]

Az eseményeket az Azure-portálon típusú események megtekintésével `Unhealthy` a liveness parancs sikertelen után indul. A következő esemény lesz típusú `Killing`, a tároló törlése lehetséges, így megkezdődhet az újraindítást. Az újraindítási számát adja meg a tároló megnövelik minden alkalommal, amikor ez akkor fordulhat elő.

Újraindítás befejeződött helyben, erőforrások – például nyilvános IP-címeket, és konkrét csomóponthoz tartozó tartalmak megőrzi.

![Portál újraindítása számláló][portal-restart]

Ha a liveness mintavétel folyamatosan meghibásodik, és elindítja a túl sok újraindul, a tároló egy exponenciális vissza késést adja meg.

## <a name="liveness-probes-and-restart-policies"></a>Liveness mintavételt és újraindítási házirendek

Újraindítás-házirendek felülbírálják a liveness mintavételt által indított újraindítási viselkedését. Ha például egy `restartPolicy = Never` *és* egy liveness mintavételt a tárolócsoport nem indítja újra a sikertelen liveness ellenőrzés esetén. A tárolócsoport helyett megfeleljenek a tárolócsoport újraindítási házirend a `Never`.

## <a name="next-steps"></a>További lépések

Feladatalapú esetekben szükség lehet egy liveness mintavételi tom automatikus újraindításának engedélyezéséhez, ha egy előfeltételt függvény nem működik megfelelően. Tárolókat feladatalapú futtatásával kapcsolatos további információkért lásd: [indexelése feladatok futtatása az Azure-tároló példányok](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png