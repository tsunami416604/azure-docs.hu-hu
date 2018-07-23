---
title: Az Azure Event Griddel Container Registry eseménysémája
description: Az Azure Event Griddel Container Reigstry események biztosított tulajdonságokat ismerteti
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 07/20/2018
ms.author: tomfitz
ms.openlocfilehash: 9ed918a7402abcbe79e302421f3b2ac725857464
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188784"
---
# <a name="azure-event-grid-event-schema-for-container-registry"></a>Tároló-beállításjegyzék Azure Event Grid eseménysémája

Ez a cikk a séma és a Container Registry-események. Eseménysémák szeretné megismerni, lásd: [Azure Event Grid-esemény séma](event-schema.md).

## <a name="available-event-types"></a>Rendelkezésre álló események típusai

A BLOB storage a következő esemény típusú bocsát ki:

| Esemény típusa | Leírás |
| ---------- | ----------- |
| Microsoft.ContainerRegistry.ImagePushed | Jelenik meg, ha a kép leküldésekor. |
| Microsoft.ContainerRegistry.ImageDeleted | Következik be, amikor egy lemezkép törlődik. |

## <a name="example-event"></a>Példa esemény

Az alábbi példa bemutatja egy esemény leküldött rendszerkép sémája: 

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

Egy törölt kép esemény sémája hasonlít:

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

## <a name="event-properties"></a>Esemény tulajdonságai

Egy esemény a következő legfelső szintű adatokat tartalmaz:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| témakör | sztring | A forrás teljes erőforrás elérési útja. Ez a mező nem írható. Event Grid biztosítja ezt az értéket. |
| Tulajdonos | sztring | Az esemény tárgya közzétevő által megadott elérési útja. |
| eventType | sztring | Ehhez eseményre adatforráshoz regisztrált esemény típusok egyikét. |
| eventTime | sztring | Az esemény akkor jön létre az idő alapján a szolgáltató UTC idő. |
| id | sztring | Az esemény egyedi azonosítója. |
| adatok | objektum | A BLOB storage-eseményadatok. |
| dataVersion | sztring | Az adatobjektum sémaverziója. A közzétevő a sémaverziót határozza meg. |
| metadataVersion | sztring | Az esemény-metaadatok sémaverziója. Event Grid sémáját, a legfelső szintű tulajdonságait határozza meg. Event Grid biztosítja ezt az értéket. |

Az objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| id | sztring | Az esemény azonosítója. |
| időbélyeg | sztring | Az az időpont, amikor az esemény történt. |
| művelet | sztring | A művelet, amely magában foglalja a megadott esemény. |
| cél | objektum | A cél az esemény. |
| kérelem | objektum | A kérelem, ami az esemény jön létre. |
| aktor | objektum | Az ügynök által kezdeményezett az eseményt. A legtöbb esetben ez az érték lehet, a kérelem az engedélyezési környezetéből. |
| source | objektum | A beállításjegyzék-csomópontra, ami az esemény jön létre. Eltérően, PUT, amíg az aktor kezdeményezi az esemény, a forrás létrehozza azt. |

A célobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| mediaType | sztring | A hivatkozott objektum MIME-típusát. |
| méret | egész szám | A tartalom bájtok száma. Ugyanaz, mint a Hossz mezőben. |
| a kivonatoló | sztring | A tartalom, ahogyan a beállításjegyzék V2 HTTP API-specifikációnak a kivonat. |
| Hossza | egész szám | A tartalom bájtok száma. Ugyanaz, mint mérete mező. |
| Adattár | sztring | A tárház nevét. |
| url | sztring | A közvetlen URL-címe a tartalmat. |
| címke | sztring | A címke neve. |

A kérelem objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| id | sztring | Az esemény által kezdeményezett kérelem azonosítója. |
| cím | sztring | Az IP vagy állomásnév, esetleg az ügyfél kapcsolata az eseményt inicializáló portjával. Ez az érték a szabványos http-kérelem a RemoteAddr. |
| gazdagép | sztring | A kívülről elérhető-példányának gazdagépnevét a beállításjegyzék, a bejövő kérelem http-állomásfejlécet által megadott. |
| method | sztring | A kérelmi metódust, ami az esemény jön létre. |
| USERAGENT | sztring | A felhasználói ügynök fejléc a kérelem. |

Az aktor objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| név | sztring | A tulajdonos vagy a kérelem környezetéből az eseményt létrehozó társított felhasználónevet. |

Az adatforrás-objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| cím | sztring | A IP-cím vagy állomásnév és a portot, a beállításjegyzék-csomópont, ami az esemény jön létre. Általában ezt az értéket az operációs rendszer megszűnik. Valamint a futó hostname(). |
| instanceID | sztring | Az alkalmazások futó példányát. A módosítások minden egyes újraindítás után. |

## <a name="next-steps"></a>További lépések

* Azure Event Grid bemutatása, lásd: [Mi az Event Grid?](overview.md)
* Az Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid-előfizetés séma](subscription-creation-schema.md).
