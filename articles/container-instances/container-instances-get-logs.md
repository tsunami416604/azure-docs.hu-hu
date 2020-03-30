---
title: Tárolópéldány-naplók & események beszereznie
description: Megtudhatja, hogyan kérheti le a tárolónaplókat és -eseményeket az Azure Container-példányokban a tárolókkal kapcsolatos problémák elhárításához
ms.topic: article
ms.date: 12/30/2019
ms.custom: mvc
ms.openlocfilehash: 0991b9cb1f99606910dbdf2c87b111f67da6da7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78249998"
---
# <a name="retrieve-container-logs-and-events-in-azure-container-instances"></a>Tárolónaplók és -események lekérése az Azure Container Instancesben

Ha az Azure Container Instances szolgáltatásában egy rosszul viselkedő tárolóval rendelkezik, először tekintse meg a naplókat [az az container naplókkal,][az-container-logs]és streamelje a szabványos ki- és szabványos hibáját az az [container attach][az-container-attach]segítségével. Megtekintheti a tárolópéldányok naplóit és eseményeit az Azure Portalon, vagy elküldheti a tárolócsoportok napló- és eseményadatait az [Azure Monitor-naplókba.](container-instances-log-analytics.md)

## <a name="view-logs"></a>Naplók megtekintése

Az alkalmazáskódból származó naplók megtekintéséhez egy tárolóban használhatja az [az container logs parancsot.][az-container-logs]

A következő a naplókimenet a példa feladatalapú tárolóból [a Parancssor beállítása egy tárolópéldányban](container-instances-start-command.md#azure-cli-example), miután egy parancssori felülbírálás használatával érvénytelen URL-címet adott meg:

```azurecli
az container logs --resource-group myResourceGroup --name mycontainer
```

```output
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

Az [az tárolócsatolási][az-container-attach] parancs diagnosztikai információkat biztosít a tároló indításakor. Miután a tároló elindult, az STDOUT és az STDERR streamelése a helyi konzolra.

Itt például a Feladatalapú tárolókimenet a [Parancssor beállítása egy tárolópéldányban](container-instances-start-command.md#azure-cli-example), miután megadott egy feldolgozandó nagy szöveges fájl érvényes URL-címét:

```azurecli
az container attach --resource-group myResourceGroup --name mycontainer
```

```output
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

## <a name="get-diagnostic-events"></a>Diagnosztikai események beszereznie

Ha a tároló telepítése sikertelen, tekintse át az Azure Container Instances erőforrás-szolgáltató által biztosított diagnosztikai információkat. A tároló eseményeinek megtekintéséhez futtassa az [az container show][az-container-show] parancsot:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

A kimenet tartalmazza a tároló alapvető tulajdonságait, valamint a központi telepítési eseményeket (itt csonkolt):

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
Ismerje meg, hogyan háríthatja el az Azure Container Instances [gyakori tároló- és üzembe helyezési problémáit.](container-instances-troubleshooting.md)

Megtudhatja, hogy miként küldhet napló- és eseményadatokat a tárolócsoportokszámára az [Azure Monitor-naplókba.](container-instances-log-analytics.md)

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show