---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/25/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a0a777151216cb70b696da088b8a0d34779ebc39
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47396127"
---
A következő táblázat az Azure Storage alapértelmezett korlátokat ismerteti. A *bejövő* korlát vonatkozik az összes adatot (kérést) küld egy tárfiókba. A *kimenő* korlát vonatkozik egy tárfiók felől fogadott összes adatot (választ).

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Storage-fiókok régiónként és előfizetésenként, beleértve a standard és prémium szintű fiókok száma | 200 |
| Maximális tárfiókok kapacitásával<sup>1</sup> | 500 TiB |
| Blob-tárolók, blobok, fájlmegosztások, táblák, üzenetsorok, entitások vagy tárfiókonként üzenetek maximális száma | Korlátlan |
| Maximális<sup>1</sup> tárfiókonként | másodpercenként 20 000 kérelem |
| Maximális bejövő<sup>1</sup> tárfiókonként (USA régió) | Ha az RA-GRS/GRS engedélyezve van, 20 GB/s az LRS és zrs esetén 10 GB/s<sup>2</sup> |
| Maximális kimenő<sup>1</sup> tárfiókonként (USA régió) | Ha az RA-GRS/GRS engedélyezése esetén 30 GB/s az LRS és zrs esetén 20 GB/s |
| Maximális bejövő<sup>1</sup> tárfiókonként (Amerikai Egyesült régió) | Ha az RA-GRS/GRS engedélyezve van, 10 GB/s az LRS és zrs esetén 5 GB/s<sup>2</sup> |
| Maximális kimenő<sup>1</sup> tárfiókonként (Amerikai Egyesült régió) | Ha az RA-GRS/GRS engedélyezve van, 15 GB/s az LRS és zrs esetén 10 GB/s |

<sup>1</sup> kérés által az azure storage-fiókok támogatják az a kapacitás, a kérelmek arányának, a bejövő és a kimenő forgalom magasabb korlátok. A megnövelt korlátok kapcsolatos további információkért lásd: [nagyobb, magasabb méretezési tárfiókok bejelentése](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/). Korlátok növelését, lépjen kapcsolatba a [Azure-támogatási](https://azure.microsoft.com/support/faq/).

<sup>2</sup>[azure Storage replikáció](https://docs.microsoft.com/azure/storage/common/storage-redundancy) lehetőségek a következők:
* **RA-GRS**: olvasási hozzáférésű georedundáns tárolás. RA-GRS engedélyezése esetén a másodlagos hely kimenő célok megegyeznek az elsődleges helyen.
* **GRS**: georedundáns tárolást. 
* **A ZRS**: zónaredundáns tárolás.
* **LRS**: helyileg redundáns tárolás. 

