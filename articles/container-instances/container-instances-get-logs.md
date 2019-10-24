---
title: Tároló naplóinak és eseményeinek beolvasása Azure Container Instances
description: Megtudhatja, hogyan végezhet hibakeresést a tároló naplóival és eseményeivel Azure Container Instances
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 03/21/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 8ae7ab3f53f480f46165800504fbb1eb6649c3e2
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68325969"
---
# <a name="retrieve-container-logs-and-events-in-azure-container-instances"></a>Tároló-naplók és-események beolvasása Azure Container Instances

Ha nem megfelelő tárolót használ, először tekintse meg a naplókat az [az Container logs][az-container-logs]paranccsal, és a standard kimenő és standard hibáját az [az Container Attach][az-container-attach]paranccsal.

## <a name="view-logs"></a>Naplók megtekintése

Ha az alkalmazás kódjában lévő naplókat szeretné megtekinteni egy tárolóban, használhatja az az [Container logs][az-container-logs] parancsot.

A következő lépés a példaként szolgáló feladaton alapuló tároló kimenete, amely az [ACI-ban futtatott tároló feladat futtatása](container-instances-restart-policy.md)után a feldolgozáshoz érvénytelen URL-címet adott meg:

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

## <a name="attach-output-streams"></a>Kimeneti stream csatolása

Az az [Container Attach][az-container-attach] parancs diagnosztikai adatokat biztosít a tároló indításakor. A tároló megkezdése után az STDOUT és a STDERR a helyi konzolra kerül.

Itt látható például a feladat-alapú tároló kimenete a tárolóban lévő sablon futtatásakor az [ACI-ban](container-instances-restart-policy.md), miután a nagy szövegfájl érvényes URL-címét adta meg a feldolgozáshoz:

```console
$ az container attach --resource-group myResourceGroup --name mycontainer
Container 'mycontainer' is in state 'Unknown'...
Container 'mycontainer' is in state 'Waiting'...
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 19:42:39+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
Container 'mycontainer1' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 19:42:39+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
(count: 1) (last timestamp: 2019-03-21 19:42:52+00:00) Successfully pulled image "mcr.microsoft.com/azuredocs/aci-wordcount:latest"
(count: 1) (last timestamp: 2019-03-21 19:42:55+00:00) Created container
(count: 1) (last timestamp: 2019-03-21 19:42:55+00:00) Started container

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

## <a name="get-diagnostic-events"></a>Diagnosztikai események beolvasása

Ha a tároló telepítése sikertelen, akkor át kell tekintenie a Azure Container Instances erőforrás-szolgáltató által megadott diagnosztikai adatokat. A tároló eseményeinek megtekintéséhez futtassa az [az Container show] [az-Container-show] parancsot:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

A kimenet tartalmazza a tároló alapvető tulajdonságait, valamint az üzembe helyezési eseményeket (Itt láthatók a csonkítva):

```JSON
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "mcr.microsoft.com/azuredocs/aci-helloworld",
      ...
        "events": [
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:22+00:00",
            "lastTimestamp": "2019-03-21T19:46:22+00:00",
            "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-helloworld\"",
            "name": "Pulling",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:28+00:00",
            "lastTimestamp": "2019-03-21T19:46:28+00:00",
            "message": "Successfully pulled image \"mcr.microsoft.com/azuredocs/aci-helloworld\"",
            "name": "Pulled",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:31+00:00",
            "lastTimestamp": "2019-03-21T19:46:31+00:00",
            "message": "Created container",
            "name": "Created",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2019-03-21T19:46:31+00:00",
            "lastTimestamp": "2019-03-21T19:46:31+00:00",
            "message": "Started container",
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
## <a name="next-steps"></a>További lépések
Ismerje meg, hogy miként lehet elhárítani a Azure Container Instances [gyakori tároló-és üzembe helyezési problémáit](container-instances-troubleshooting.md) .

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-logs]: /cli/azure/container#az-container-logs
