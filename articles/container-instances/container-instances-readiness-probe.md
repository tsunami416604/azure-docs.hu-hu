---
title: Készenléti mintavétel beállítása a tárolópéldányon
description: Megtudhatja, hogyan konfigurálhat egy mintavételt annak biztosítására, hogy az Azure Container Instances tárolói csak akkor fogadjanak kéréseket, ha készen állnak
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 64bb4a3e429ce820835abbf8e235600e592f7868
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76935686"
---
# <a name="configure-readiness-probes"></a>Készültségi tesztek konfigurálása

A forgalmat kiszolgáló tárolóba adott alkalmazások esetében érdemes ellenőrizni, hogy a tároló készen áll-e a bejövő kérelmek kezelésére. Az Azure Container Instances támogatja a konfigurációk felvételére szolgáló készenléti mintavételeket, hogy a tároló bizonyos körülmények között ne érhető el. A készenléti szonda úgy viselkedik, mint egy [Kubernetes készenléti szonda.](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/) Előfordulhat például, hogy egy tárolóalkalmazásnak nagy adatkészletet kell betöltenie az indítás során, és nem szeretné, hogy ez idő alatt kéréseket fogadjon.

Ez a cikk bemutatja, hogyan telepíthet egy tárolócsoportot, amely készenléti mintavételt tartalmaz, így a tároló csak akkor kapja meg a forgalmat, ha a mintavétel sikeres.

Az Azure Container Instances is támogatja [a liveness mintavételek,](container-instances-liveness-probe.md)amely konfigurálhatja, hogy egy nem megfelelő állapotú tároló automatikusan újraindul.

> [!NOTE]
> Jelenleg nem használható készenléti mintavétel egy virtuális hálózatra telepített tárolócsoportban.

## <a name="yaml-configuration"></a>YAML-konfiguráció

Például hozzon `readiness-probe.yaml` létre egy fájlt a következő kódrészlet, amely tartalmazza a készenléti mintavétel. Ez a fájl egy tárolócsoportot határoz meg, amely egy kis webalkalmazást futtató tárolóból áll. Az alkalmazás a nyilvános `mcr.microsoft.com/azuredocs/aci-helloworld` lemezképből van telepítve. Ez a tárolóba helyezett alkalmazás is látható [egy tárolópéldány üzembe helyezése az Azure CLI](container-instances-quickstart.md) és más rövid útmutatók használatával.

```yaml
apiVersion: 2018-10-01
location: eastus
name: readinesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      command:
        - "/bin/sh"
        - "-c"
        - "node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait"
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      readinessProbe:
        exec:
          command:
          - "cat"
          - "/tmp/ready"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="start-command"></a>Start parancs

A központi `command` telepítés tartalmaz egy tulajdonságot, amely meghatározza a kiindulási parancsot, amely a tároló első futásakor fut. Ez a tulajdonság karakterláncok tömbjét fogadja el. Ez a parancs szimulálja azt az időpontot, amikor a webalkalmazás fut, de a tároló nem áll készen. 

Először elindítja a rendszerhéj-munkamenetet, és parancsot `node` futtat a webalkalmazás elindításához. Azt is elindítja a parancsot, hogy aludni 240 `ready` másodpercig, ami után létrehoz egy fájlt hívott a `/tmp` könyvtárban:

```console
node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait
```

### <a name="readiness-command"></a>Készenlét parancs

Ez a YAML-fájl határozza meg azt, `readinessProbe` amely támogatja a `exec` készenléti parancsot, amely készenléti ellenőrzésként működik. Ez a példa készen áll a `ready` könyvtárban `/tmp` lévő fájl létezésére.

Ha `ready` a fájl nem létezik, a készenléti parancs nem nulla értékkel lép ki; a tároló továbbra is fut, de nem érhető el. Amikor a parancs sikeresen kilép a 0-s kilépési kóddal, a tároló készen áll a hozzáférésre. 

A `periodSeconds` tulajdonság azt a készenléti parancsot jelöli, amelyet 5 másodpercenként kell végrehajtani. A készenléti mintavétel a tárolócsoport élettartamára fut.

## <a name="example-deployment"></a>Példa telepítésre

Futtassa a következő parancsot az előző YAML-konfigurációval rendelkező tárolócsoport telepítéséhez:

```azurecli-interactive
az container create --resource-group myResourceGroup --file readiness-probe.yaml
```

## <a name="view-readiness-checks"></a>Készenléti ellenőrzések megtekintése

Ebben a példában az első 240 másodpercben a készenléti `ready` parancs sikertelen lesz, amikor ellenőrzi a fájl létezését. Az állapotkód azt jelzi, hogy a tároló nem áll készen.

Ezek az események az Azure Portalon vagy az Azure CLI-ben tekinthetők meg. A portál például azt `Unhealthy` mutatja, hogy a típusú események a készenléti parancs sikertelensége esetén aktiválódnak. 

![A portál nem megfelelő állapotú eseménye][portal-unhealthy]

## <a name="verify-container-readiness"></a>A tároló készültségének ellenőrzése

A tároló elindítása után ellenőrizheti, hogy kezdetben nem érhető-e el. A kiépítés után a tárolócsoport IP-címét:

```azurecli
az container show --resource-group myResourceGroup --name readinesstest --query "ipAddress.ip" --out tsv
```

Próbálja meg elérni a helyet, amíg a készenléti mintavétel sikertelen:

```bash
wget <ipAddress>
```

A kimenet azt mutatja, hogy a hely kezdetben nem érhető el:
```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response... 
```

240 másodperc elteltével a készenléti parancs sikeres, jelezve, hogy a tároló készen áll. Most, amikor futtatja a `wget` parancsot, akkor sikerül:

```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response...200 OK
Length: 1663 (1.6K) [text/html]
Saving to: ‘index.html.1’

index.html.1                       100%[===============================================================>]   1.62K  --.-KB/s    in 0s      

2019-10-15 16:49:38 (113 MB/s) - ‘index.html.1’ saved [1663/1663] 
```

Amikor a tároló készen áll, a webalkalmazás is elérheti az IP-címet egy webböngésző segítségével.

> [!NOTE]
> A készenléti mintavétel továbbra is fut a tárolócsoport élettartama alatt. Ha a készenléti parancs egy későbbi időpontban meghibásodik, a tároló ismét elérhetetlenné válik. 
> 

## <a name="next-steps"></a>További lépések

A készenléti mintavétel hasznos lehet a függő tárolókból álló többtárolós csoportokat érintő forgatókönyvekben. A többtárolós forgatókönyvekről további információt az [Azure Container Instances tárolócsoportok című témakörben talál.](container-instances-container-groups.md)

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-readiness-probe/readiness-probe-failed.png
