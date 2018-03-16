---
title: "Azure-tárolót példányok hibaelhárítása"
description: "Ismerje meg az Azure-tároló példányaival kapcsolatos problémák elhárítása"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 03/14/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: a527939d6bc73e3dee5701bc53ef8312e68d2953
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="troubleshoot-deployment-issues-with-azure-container-instances"></a>Azure-tároló példányaival telepítési problémák elhárításához

Ez a cikk bemutatja, hogyan kapcsolatos problémák elhárítása az Azure-tároló példányokhoz tárolók telepítése során. Ismerteti az egyes a gyakori problémákat mutatjuk be.

## <a name="view-logs-and-stream-output"></a>Naplók megtekintése és adatfolyam-kimenetét

Ha egy átirányítóban tároló, indítsa el a naplók megtekintéséhez [az tároló naplók][az-container-logs], és a standard out és a standard hiba [az tároló csatlakoztatása] [az-container-attach].

### <a name="view-logs"></a>Naplók megtekintése

Az alkalmazás kódjáról olyan tárolóban naplók megtekintéséhez használja a [az tároló naplók] [ az-container-logs] parancsot.

Az alábbiakban található napló kimenetét a példa feladatalapú tároló [indexelése feladat futtatása ACI](container-instances-restart-policy.md), miután rendelkező táplált azt feldolgozni egy érvénytelen URL-CÍMÉT:

```console
$ az container logs --resource-group myResourceGroup --name mycontainer
Traceback (most recent call last):
  File "wordcount.py", line 11, in <module>
    urllib.request.urlretrieve (sys.argv[1], "foo.txt")
  File "/usr/local/lib/python3.6/urllib/request.py", line 248, in urlretrieve
    with contextlib.closing(urlopen(url, data)) as fp:
  File "/usr/local/lib/python3.6/urllib/request.py", line 223, in urlopen
    return opener.open(url, data, timeout)
  File "/usr/local/lib/python3.6/urllib/request.py", line 532, in open
    response = meth(req, response)
  File "/usr/local/lib/python3.6/urllib/request.py", line 642, in http_response
    'http', request, response, code, msg, hdrs)
  File "/usr/local/lib/python3.6/urllib/request.py", line 570, in error
    return self._call_chain(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 504, in _call_chain
    result = func(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 650, in http_error_default
    raise HTTPError(req.full_url, code, msg, hdrs, fp)
urllib.error.HTTPError: HTTP Error 404: Not Found
```

### <a name="attach-output-streams"></a>Kimeneti folyamok csatolása

A [az tároló csatlakoztatása] [ az-container-attach] parancs tároló indításakor diagnosztikai információkat nyújt. Miután elindult a tárolóhoz, akkor az adatfolyamokat, STDOUT és az STDERR helyi konzolra.

Például ez a feladat-alapú tároló kimenete [indexelése feladat futtatása ACI](container-instances-restart-policy.md), rendelkező megadott egy érvényes URL-cím nagy szöveges fájl feldolgozása után:

```console
$ az container attach --resource-group myResourceGroup --name mycontainer
Container 'mycontainer' is in state 'Unknown'...
Container 'mycontainer' is in state 'Waiting'...
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2018-03-09 23:21:33+00:00) pulling image "microsoft/aci-wordcount:latest"
(count: 1) (last timestamp: 2018-03-09 23:21:49+00:00) Successfully pulled image "microsoft/aci-wordcount:latest"
(count: 1) (last timestamp: 2018-03-09 23:21:49+00:00) Created container with id e495ad3e411f0570e1fd37c1e73b0e0962f185aa8a7c982ebd410ad63d238618
(count: 1) (last timestamp: 2018-03-09 23:21:49+00:00) Started container with id e495ad3e411f0570e1fd37c1e73b0e0962f185aa8a7c982ebd410ad63d238618

Start streaming logs:
[('the', 22979),
 ('I', 20003),
 ('and', 18373),
 ('to', 15651),
 ('of', 15558),
 ('a', 12500),
 ('you', 11818),
 ('my', 10651),
 ('in', 9707),
 ('is', 8195)]
```

## <a name="get-diagnostic-events"></a>Diagnosztikai események

Ha a tároló nem tudja sikeresen telepíteni, akkor tekintse át a diagnosztikai adatokat az Azure-tároló példányok erőforrás-szolgáltató által biztosított. A tároló események megtekintéséhez futtassa a [az tároló megjelenítése] [ az-container-show] parancs:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

A kimenet a tárolót, és telepítési eseményeket (Itt látható csonkolt) core tulajdonságait tartalmazza:

```JSON
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "microsoft/aci-helloworld",
      ...
        "events": [
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:49+00:00",
            "lastTimestamp": "2017-12-21T22:50:49+00:00",
            "message": "pulling image \"microsoft/aci-helloworld\"",
            "name": "Pulling",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Successfully pulled image \"microsoft/aci-helloworld\"",
            "name": "Pulled",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Created container with id 2677c7fd54478e5adf6f07e48fb71357d9d18bccebd4a91486113da7b863f91f",
            "name": "Created",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Started container with id 2677c7fd54478e5adf6f07e48fb71357d9d18bccebd4a91486113da7b863f91f",
            "name": "Started",
            "type": "Normal"
          }
        ],
        "previousState": null,
        "restartCount": 0
      },
      "name": "mycontainer",
      "ports": [
        {
          "port": 80,
          "protocol": null
        }
      ],
      ...
    }
  ],
  ...
}
```

## <a name="common-deployment-issues"></a>Gyakori telepítési problémákkal

A következő szakaszok ismertetik kapcsolatos gyakori hibák leggyakoribb hibák tároló üzembe a fiók:

* [Lemezkép-verziója nem támogatott](#image-version-not-supported)
* [Nem sikerült lekéréses kép](#unable-to-pull-image)
* [Tároló folyamatosan kilép, és újraindul](#container-continually-exits-and-restarts)
* [Tároló start hosszú időt vesz igénybe.](#container-takes-a-long-time-to-start)
* ["Erőforrás nem érhető el" hiba](#resource-not-available-error)

## <a name="image-version-not-supported"></a>Lemezkép-verziója nem támogatott

Ha megad egy olyanra, amely nem támogatja az Azure tároló példányok, egy `ImageVersionNotSupported` hibát ad vissza. A hiba az érték `The version of image '{0}' is not supported.`, és a jelenleg Windows 1709 lemezképbe vonatkozik. A probléma orvoslása érdekében LTS Windows-lemezkép használata. Windows 1709 képek támogatása a feldolgozása folyamatban van.

## <a name="unable-to-pull-image"></a>Nem sikerült lekéréses kép

Nem sikerült a lemezkép kezdetben lekéréses Azure tároló példányok esetén újrapróbálja valamennyi ideje előtt esetleg sikertelenek lesznek. Ha nem kell húzni a lemezképet, a következő események jelennek meg kimenete [az tároló megjelenítése][az-container-show]:

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"microsoft/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"microsoft/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"microsoft/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```

Oldja meg, a tároló törlése, és ismételje meg a központi telepítés, fizető zárja be a figyelmet, hogy a lemezkép nevét helyesen adta.

## <a name="container-continually-exits-and-restarts"></a>Tároló folyamatosan kilép, és újraindul

Ha a tároló létrehozása után futtatott, és automatikusan újraindul, szükség lehet beállítani a [indítsa újra a házirend](container-instances-restart-policy.md) a **OnFailure** vagy **soha**. Ha megad **OnFailure** és továbbra is folyamatosan lásd újraindul, előfordulhat, hogy az alkalmazás vagy a parancsfájl végrehajtása a tárolóban kapcsolatos problémát.

A tároló példányok API tartalmaz egy `restartCount` tulajdonság. Egy tároló újraindítások számát ellenőrzéséhez használja a [az tároló megjelenítése] [ az-container-show] az Azure CLI 2.0 parancsot. A következő egy példa a kimenetre (amely kivonatosan mutatja csonkolódtak), hogy a `restartCount` tulajdonság a kimeneti végén.

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> A legtöbb tároló képek Linux terjesztésekről, bash, például a rendszerhéj állítja be az alapértelmezett parancs. Mivel a rendszerhéj önmagában nem hosszan futó szolgáltatás, ezekhez a tárolókhoz azonnal való kilépéshez és az alapértelmezett konfigurálásakor újraindítás hurok sorolhatók **mindig** indítsa újra a házirendet.

## <a name="container-takes-a-long-time-to-start"></a>Tároló start hosszú időt vesz igénybe.

A két elsődleges tényező Azure tároló példányát tároló indítási ideje befolyásolja a következők:

* [A kép mérete](#image-size)
* [Kép](#image-location)

Windows-lemezképek rendelkezik [további szempontok](#use-recent-windows-images).

### <a name="image-size"></a>A kép mérete

Ha a tároló indítása, de végül hosszú ideig tart a sikeres, indítsa el a tároló-lemezkép mérete alapján. Azure tároló példányok az igény szerinti kéri le a tároló lemezképet, mert az indítási idő tapasztal közvetlenül kapcsolódik a méretét.

A tároló-lemezkép mérete használatával megtekintheti a `docker images` a Docker parancssori parancsot:

```console
$ docker images
REPOSITORY                  TAG       IMAGE ID        CREATED        SIZE
microsoft/aci-helloworld    latest    7f78509b568e    13 days ago    68.1MB
```

A kulcsot a lemezkép mérete kisebb tartása annak ellenőrzése, hogy a végső kép nem tartalmaz semmit, amelyre nincs szükség a futási időben. Ehhez a egyirányú azt a [többlépcsős buildek][docker-multi-stage-builds]. Többlépcsős épít, ellenőrizze, hogy a végső kép csak az alkalmazás van szüksége az összetevők tartalmazza, és nem egy, az új tartalom, amely egyszerűen volt szükség összeállítása során.

### <a name="image-location"></a>Kép

Egy másik, csökken a kép lekéréses a tároló indítási idővel módja a tároló lemezkép a gazdagép [Azure tároló beállításjegyzék](/azure/container-registry/) ugyanabban a régióban, ahol szeretné telepíteni a tároló példányok. Ez lerövidíti a hálózati elérési útját, amelyet a tároló kép utaznak, jelentősen lerövidíteni a letöltési időt.

### <a name="use-recent-windows-images"></a>Legutóbbi Windows lemezképeket

Az Azure tároló példányok gyorsítótárazást a sebesség tároló indítási idő alapján a bizonyos Windows-telepítési lemezképek használja.

Ahhoz, hogy a legjobb Windows tároló indítási idő, valamelyikével a **legutóbbi három** a következő verziói **két lemezképet** az alapjául szolgáló lemezképhez:

* [Windows Server 2016] [ docker-hub-windows-core] (csak LTS)
* [Windows Server 2016 Nano Server][docker-hub-windows-nano]

## <a name="resource-not-available-error"></a>Erőforrás nem érhető el hiba

Különböző területi erőforrás miatt betöltése az Azure-ban, és a következő hiba akkor fordulhat elő, amikor egy tároló-példány telepítése:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Ez a hiba azt jelzi, hogy a régióban, amelyben telepíteni kívánt túl nagy terhelés miatt a tároló megadott erőforrások nem rendelhető hozzá jelenleg. Legalább egy, a megoldás lépések segítségével a probléma megoldása érdekében.

* Ellenőrizze a definiált paraméterek tartoznak a tároló központi telepítési beállítások [kvótái és az Azure-tároló példányok régiónkénti elérhetőség](container-instances-quotas.md#region-availability)
* A tároló alacsonyabb CPU és memória beállításainak megadása
* Egy másik Azure-régiót telepítése
* Egy későbbi időpontban telepítése

<!-- LINKS - External -->
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/r/microsoft/windowsservercore/
[docker-hub-windows-nano]: https://hub.docker.com/r/microsoft/nanoserver/

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az_container_attach
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show