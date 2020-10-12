---
title: Rögzítési szabályzat kezelése – Azure
description: Ez a témakör a rögzítési szabályzat kezelését ismerteti.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: d3a1be915dc1cc8714e49cc7b2fe68bbe9cad161
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87011481"
---
# <a name="manage-recording-policy"></a>Felvételkészítési szabályzat kezelése

Élő videó-elemzést használhat IoT Edge a [folyamatos videofelvételek rögzítéséhez](continuous-video-recording-concept.md), amelyekkel hetek vagy hónapok szerint rögzíthet videókat a felhőbe. A Felhőbeli Archívum hossza (nap) az Azure Storage-ba beépített életciklus- [kezelési eszközökkel](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal) kezelhető.  

A Media Service-fiók egy Azure Storage-fiókhoz van csatolva, és amikor videót rögzít a felhőbe, a tartalmat egy Media Service- [eszközbe](../latest/assets-concept.md)írja a rendszer. Minden eszköz egy tárolóhoz van leképezve a Storage-fiókban. az életciklus-kezelés lehetővé teszi, hogy megadjon egy [házirendet](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#policy) egy Storage-fiókhoz, amelyben megadhat egy [szabályt](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#rules) , például az alábbiakat.

```
{
  "rules": [
    {
      "name": "NinetyDayRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "delete": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

A fenti szabály:

* A Storage-fiókban lévő összes blokk blobra vonatkozik.
* Azt határozza meg, hogy amikor a Blobok kora meghaladja a 30 napot, a rendszer áthelyezi őket a [gyors elérési szintről a ritka elérésű](../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal)
* És amikor a Blobok 90 napon túli korukat vesznek fel, azokat törölni kell.

Mivel az élő videó Analytics a megadott időegységben archiválja a videót, az objektum Blobok egy sorozatát fogja tartalmazni, egy blobot egy szegmensben. Amikor az életciklus-kezelési házirend beindul, és törli a régebbi blobokat, továbbra is elérheti és lejátszhatja a fennmaradó blobokat a Media Service API-kon keresztül. További információ: [lejátszási felvételek](playback-recordings-how-to.md). 

## <a name="limitations"></a>Korlátozások

Az alábbiakban néhány ismert korlátozást talál az életciklus-kezeléssel kapcsolatban:

* A szabályzat legfeljebb 100 szabályt tartalmazhat, és minden szabály legfeljebb 10 tárolót határozhat meg. Tehát ha különböző rögzítési szabályzatokra van szüksége (például 3 napos Archívum a parkolóba kerülő kamerához, 30 nap a betöltési Dockon, a kamera pedig 180 nap), majd egy Media Service-fiókkal testreszabhatja a legtöbb 1000 kamera szabályait.
* Az életciklus-kezelési házirend frissítései nem azonnaliek. További részletekért tekintse meg [ezt a GYIK szakaszt](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#faq) .
* Ha úgy dönt, hogy olyan házirendet alkalmaz, amelyben a Blobok átkerülnek a lassú szintre, akkor az Archívum ezen részének lejátszására is hatással lehet. Előfordulhat, hogy további késések vagy szórványos hibák jelentkeznek. A Media Services nem támogatja a tartalom lejátszását az archiválási szinten.

## <a name="next-steps"></a>További lépések

[Felvételek lejátszása](playback-recordings-how-to.md)
