---
title: Kézbesítés és újrapróbálkozás – Azure Event Grid IoT Edge | Microsoft dokumentumok
description: Kézbesítés és újrapróbálkozás az Event Gridben az IoT Edge-en.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 7df283b12a0d04d2b785c13a2f12b03115581e79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841712"
---
# <a name="delivery-and-retry"></a>Teljesítés és újrapróbálkozás

Az Event Grid tartós kézbesítést biztosít. Minden egyes üzenetet megpróbál azonnal kézbesíteni minden egyes megfelelő előfizetéshez. Ha egy előfizető végpontja nem nyugtázza egy esemény bevételét, vagy ha hiba történt, az Event Grid újrapróbálkozik a kézbesítéssel egy rögzített **újrapróbálkozási ütemezés** és **újrapróbálkozási házirend**alapján.  Alapértelmezés szerint az Event Grid modul egyszerre csak egy eseményt szállít az előfizetőnek. A hasznos teher azonban egy tömb egyetlen esemény. Beállíthatja, hogy a modul egyszerre több eseményt biztosítson a kimeneti kötegelési funkció engedélyezésével. A funkcióval kapcsolatos részletekért tekintse meg a [kimeneti kötegelést.](delivery-output-batching.md)  

> [!IMPORTANT]
>Az eseményadatok nem nyújtanak megőrzési támogatást. Ez azt jelenti, hogy az Event Grid modul újratelepítése vagy újraindítása miatt elveszíti a még nem kézbesített eseményeket.

## <a name="retry-schedule"></a>Újrapróbálkozásütemezés

Az Event Grid legfeljebb 60 másodpercet vár a válaszra az üzenet kézbesítése után. Ha az előfizető végpontja nem ACK a választ, majd az üzenet lesz sorban az egyik vissza a várólisták a későbbi újrapróbálkozások.

Két előre konfigurált visszalépési várólista határozza meg azt az ütemezést, amelyen az újrapróbálkozást megkísérli. Ezek a következők:

| Ütemezés | Leírás |
| ---------| ------------ |
| 1 perc | Az itt végződő üzeneteket minden percben megkíséreli a kísérlet.
| 10 perc | Az itt végződő üzeneteket minden tizedik percben megkísérelik.

### <a name="how-it-works"></a>Működés

1. Az üzenet megérkezik az Event Grid modulba. Megkíséreljük azonnal kézbesíteni.
1. Ha a kézbesítés sikertelen, akkor az üzenet 1 perces várólistába kerül, és egy perc múlva újra megpróbálja.
1. Ha a kézbesítés továbbra is sikertelen, akkor az üzenet 10 perces várólistába kerül, és 10 percenként újra próbálkozik.
1. A sikeres vagy újrapróbálkozási házirend-korlátok eléréséig a kézbesítések megkísérlése megkísérlésre.

## <a name="retry-policy-limits"></a>Házirend-korlátok újrapróbálkozása

Két konfiguráció határozza meg az újrapróbálkozási házirendet. Ezek a következők:

* A kísérletek maximális száma
* Az esemény ideje az életben (TTL)

Az újrapróbálkozási házirend bármelyikének elérésekor egy esemény ellesz dobva. Magát az újrapróbálkozási ütemezést az Újrapróbálkozásütemezés szakasz ban ismertetjük. Ezeknek a korlátoknak a konfigurálása minden előfizetőre vagy előfizetésenként végezhető el. A következő szakasz ismerteti mindegyik további részleteket.

## <a name="configuring-defaults-for-all-subscribers"></a>Alapértelmezett beállítások konfigurálása az összes előfizető számára

Két tulajdonság létezik: `brokers__defaultMaxDeliveryAttempts` és `broker__defaultEventTimeToLiveInSeconds` ez az Event Grid központi telepítésének részeként konfigurálható, amely az összes előfizető újrapróbálkozási házirend-alapértelmezéseit szabályozza.

| Tulajdonság neve | Leírás |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | Az esemény kézbesítésére tett kísérletek maximális száma. Alapértelmezett érték: 30.
| `broker__defaultEventTimeToLiveInSeconds` | Esemény TTL másodpercben, amely után egy esemény el kell dobni, ha nem kézbesítik. Alapértelmezett érték: **7200** másodperc

## <a name="configuring-defaults-per-subscriber"></a>Alapértelmezett beállítások konfigurálása előfizetőnként

Az újrapróbálkozási szabályzat-korlátokat előfizetésenként is megadhatja.
Az [API dokumentációjában](api.md) tájékozódhat arról, hogyan konfigurálhatja az alapértelmezett beállításokat előfizetőnként. Az előfizetési szint alapértelmezései felülbírálják a modulszint-konfigurációkat.

## <a name="examples"></a>Példák

A következő példa beállítja az újrapróbálkozási házirendet az Event Grid modulban a maxNumberOfAttempts = 3 és a 30 perces Esemény TTL értékkel

```json
{
  "Env": [
    "broker__defaultMaxDeliveryAttempts=3",
    "broker__defaultEventTimeToLiveInSeconds=1800"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```

A következő példa egy webhook-előfizetést állít be a maxNumberOfAttempts = 3 és az Event TTL 30 percértékkel

```json
{
 "properties": {
  "destination": {
   "endpointType": "WebHook",
   "properties": {
    "endpointUrl": "<your_webhook_url>",
    "eventDeliverySchema": "eventgridschema"
   }
  },
  "retryPolicy": {
   "eventExpiryInMinutes": 30,
   "maxDeliveryAttempts": 3
  }
 }
}
```
