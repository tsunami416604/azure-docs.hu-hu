---
title: Tárolónaplók és az Azure Container Instances események beolvasása
description: Ismerje meg, a tárolónaplók és az események az Azure Container Instances hibakeresése
services: container-instances
author: jluk
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 05/30/2018
ms.author: juluk
ms.custom: mvc
ms.openlocfilehash: 21e75beffbf592f25257b0dca7ba48b0ffe560a8
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669771"
---
# <a name="retrieve-container-logs-and-events-in-azure-container-instances"></a>Tárolónaplók és az Azure Container Instances szolgáltatásban események beolvasása

Egy rosszul tároló van, indítsa el a naplóinak megtekintésével [az tárolónaplók][az-container-logs], és a standard kimeneti- és standard hiba a streamelési [az container attach] [az-container-attach].

## <a name="view-logs"></a>Naplók megtekintése

Az alkalmazás kódja egy tárolóból származó naplók megtekintéséhez használja a [az tárolónaplók] [ az-container-logs] parancsot.

Az alábbiakban található a példa a feladat-alapú tárolót a napló kimenetét [tárolóalapú feladatok futtatása a ACI](container-instances-restart-policy.md), hogy adatkéréseket, érvénytelen URL-cím feldolgozása után:

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

A [az container attach] [ az-container-attach] parancs biztosít a diagnosztikai adatokat tároló indítása során. Miután a tároló elindult, elemzésének lehetőségeit az STDOUT és STDERR a helyi konzolra.

Például itt van a feladat-alapú tárolóba kimenete [tárolóalapú feladatok futtatása a ACI](container-instances-restart-policy.md), kellene megadott egy érvényes URL-CÍMÉT egy nagy méretű szöveges fájl feldolgozása után:

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

## <a name="get-diagnostic-events"></a>Diagnosztikai események beolvasása

Ha a tároló üzembe helyezése sikeresen nem sikerül, tekintse át az Azure Container Instances szolgáltatásban az erőforrás-szolgáltató által biztosított diagnosztikai adatokat szeretné. Az események a tároló megtekintéséhez futtassa a [az container show] [az-container-show] parancsot:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

A kimenet az alapvető tulajdonságok a tároló üzembe helyezési események (Itt csonkolva látható) együtt tartalmazza:

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
## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan [gyakori tároló és az üzembe helyezés hibáinak elhárítása](container-instances-troubleshooting.md) Azure Container Instances.

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-logs]: /cli/azure/container#az-container-logs
