---
title: Azure Container Registry eseményrács-forrásként
description: Az Azure Event Grid tárolóbeállítási eseményeihez megadott tulajdonságok ismertetése
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: 7e33feb04edf42f1e2a32b9b8c8e2fd214692f31
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393366"
---
# <a name="azure-container-registry-as-an-event-grid-source"></a>Azure Container Registry eseményrács-forrásként

Ez a cikk a tárolóbeállítási események tulajdonságait és sémáját tartalmazza.Az eseménysémák bemutatása az [Azure Event Grid eseménysémájában.](event-schema.md)

## <a name="event-grid-event-schema"></a>Eseményrács eseménysémája

### <a name="available-event-types"></a>Elérhető eseménytípusok

Az Azure Container Registry a következő eseménytípusokat bocsátja ki:

| Eseménytípus | Leírás |
| ---------- | ----------- |
| Microsoft.ContainerRegistry.ImagePushed | Kép leküldésekor. |
| Microsoft.ContainerRegistry.ImageTörölve | Kép törlésekor előáll. |
| Microsoft.ContainerRegistry.ChartPushed | A Helm-diagram megnyomásakor emelkedik. |
| Microsoft.ContainerRegistry.ChartDeleted | Helm-diagram törlésekor előáll. |

### <a name="example-event"></a>Példa esemény

A következő példa egy leadott rendszersémáját mutatja be: 

```json
[{
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld:v1",
  "eventType": "ImagePushed",
  "eventTime": "2018-04-25T21:39:47.6549614Z",
  "data": {
    "id": "31c51664-e5bd-416a-a5df-e5206bc47ed0",
    "timestamp": "2018-04-25T21:39:47.276585742Z",
    "action": "push",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 3023,
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "length": 3023,
      "repository": "aci-helloworld",
      "tag": "v1"
    },
    "request": {
      "id": "7c66f28b-de19-40a4-821c-6f5f6c0003a4",
      "host": "demo.azurecr.io",
      "method": "PUT",
      "useragent": "docker/18.03.0-ce go/go1.9.4 git-commit/0520e24 os/windows arch/amd64 UpstreamClient(Docker-Client/18.03.0-ce \\\\(windows\\\\))"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

A rendszerkép törölt eseményének sémája hasonló:

```json
[{
  "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld",
  "eventType": "ImageDeleted",
  "eventTime": "2018-04-26T17:56:01.8211268Z",
  "data": {
    "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
    "timestamp": "2018-04-26T17:56:00.996603117Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "repository": "aci-helloworld"
    },
    "request": {
      "id": "aeda5b99-4197-409f-b8a8-ff539edb7de2",
      "host": "demo.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

A séma egy diagram leküldött esemény hasonló a séma egy leküldött leküldött esemény, de nem tartalmaz kérésobjektumot:

```json
[{
  "id": "ea3a9c28-5b17-40f6-a500-3f02b6829277",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "mychart:1.0.0",
  "eventType": "Microsoft.ContainerRegistry.ChartPushed",
  "eventTime": "2019-03-12T22:16:31.5164086Z",
  "data": {
    "id":"ea3a9c28-5b17-40f6-a500-3f02b682927",
    "timestamp":"2019-03-12T22:16:31.0087496+00:00",
    "action":"chart_push",
    "target":{
      "mediaType":"application/vnd.acr.helm.chart",
      "size":25265,
      "digest":"sha256:7f060075264b5ba7c14c23672698152ae6a3ebac1c47916e4efe19cd624d5fab",
      "repository":"repo",
      "tag":"mychart-1.0.0.tgz",
      "name":"mychart",
      "version":"1.0.0"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

A diagram törölt eseményének sémája hasonló a rendszerképekáltal törölt esemény sémájához, de nem tartalmaz kérésobjektumot:

```json
[{
  "id": "39136b3a-1a7e-416f-a09e-5c85d5402fca",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "mychart:1.0.0",
  "eventType": "Microsoft.ContainerRegistry.ChartDeleted",
  "eventTime": "019-03-12T22:42:08.7034064Z",
  "data": {
    "id":"ea3a9c28-5b17-40f6-a500-3f02b682927",
    "timestamp":"2019-03-12T22:42:08.3783775+00:00",
    "action":"chart_delete",
    "target":{
      "mediaType":"application/vnd.acr.helm.chart",
      "size":25265,
      "digest":"sha256:7f060075264b5ba7c14c23672698152ae6a3ebac1c47916e4efe19cd624d5fab",
      "repository":"repo",
      "tag":"mychart-1.0.0.tgz",
      "name":"mychart",
      "version":"1.0.0"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>Esemény tulajdonságai

Egy esemény legfelső szintű adatokat rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| témakör | sztring | Az eseményforrás teljes erőforráselérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg. |
| Tárgy | sztring | Az esemény tárgyra mutató, a közzétevő által megadott elérési út. |
| eventType | sztring | Az eseményforráshoz felvett eseménytípusok egyike. |
| eventTime | sztring | Az esemény létrehozásának időpontja a szolgáltató UTC-ideje alapján. |
| id | sztring | Az esemény egyedi azonosítója |
| data | objektum | Blob tárolási esemény adatai. |
| dataVersion | sztring | Az adatobjektum sémaverziója. A sémaverziót a közzétevő határozza meg. |
| metadataVersion | sztring | Az esemény metaadatok sémaverziója. A legfelső szintű tulajdonságokra az Event Grid határozza meg a sémát. Az értéket az Event Grid adja meg. |

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| id | sztring | Az eseményazonosító. |
| időbélyeg | sztring | Az az időpont, amikor az esemény bekövetkezett. |
| action | sztring | A megadott eseményt magában foglaló művelet. |
| Cél | objektum | Az esemény célpontja. |
| Kérés | objektum | Az eseményt létrehozó kérelem. |

A célobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| mediaType típus | sztring | A hivatkozott objektum MIME-típusa. |
| size | egész szám | A tartalom bájtjainak száma. Megegyezik a Hossz mezővel. |
| digest | sztring | A tartalom kivonatolása a Rendszerleíróadatbázis V2 HTTP API specifikációja szerint. |
| hossz | egész szám | A tartalom bájtjainak száma. Megegyezik a Méret mezővel. |
| Tárház | sztring | A tárház neve. |
| címke | sztring | A címke neve. |
| név | sztring | A diagram neve. |
| version | sztring | A diagram verziója. |

A kérelemobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| id | sztring | Az eseményt kezdeményező kérelem azonosítója. |
| addr | sztring | Az eseményt kezdeményező ügyfélkapcsolat IP- vagy állomásneve és esetleg portja. Ez az érték a standard http-kérelem RemoteAddr értéke. |
| gazda | sztring | A beállításjegyzék-példány külsőleg elérhető állomásneve, ahogy azt a http állomásfejléc a bejövő kérelmeknél meghatározta. |
| method | sztring | Az eseményt létrehozó kérelemmetódus. |
| Useragent | sztring | A kérelem felhasználói ügynökfejléce. |

## <a name="tutorials-and-how-tos"></a>Oktatóanyagok és útmutatók
|Cím |Leírás  |
|---------|---------|
| [Rövid útmutató: tárolóbeállítási események küldése](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Bemutatja, hogyan azure CLI container registry események küldéséhez. |


## <a name="next-steps"></a>További lépések

* Az Azure Event Grid bemutatása a [Mi az eseményrács?](overview.md)
* Az Azure Event Grid-előfizetés ek létrehozásáról az [Event Grid-előfizetésséma](subscription-creation-schema.md)című témakörben talál további információt.
