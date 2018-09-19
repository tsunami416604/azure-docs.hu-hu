---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/13/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: fb349c9ea2178d84e367e763797d5d93d3ae4c53
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46299115"
---
A következő táblázat az Azure Storage alapértelmezett korlátokat ismerteti. A *bejövő* korlát vonatkozik az összes adatot (kérést) küld egy tárfiókba. A *kimenő* korlát vonatkozik egy tárfiók felől fogadott összes adatot (választ).

| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Storage-fiókok régiónként és előfizetésenként, beleértve a standard és prémium szintű fiókok száma | 200 |
| Tárfiókok kapacitásával maximális száma | 500 TiB |
| Blob-tárolók, blobok, fájlmegosztások, táblák, üzenetsorok, entitások vagy tárfiókonként üzenetek maximális száma | Korlátlan |
| Kérelem maximális díj / storage-fiók | másodpercenként 20 000 kérelem |
| Maximális bejövő tárfiókonként (USA régió) | Ha az RA-GRS/GRS engedélyezve van, 20 GB/s az LRS és zrs esetén 10 GB/s<sup>1</sup> |
| Maximális kimenő tárfiókonként (USA régió) | 50 GB/s |
| Maximális bejövő tárfiókonként (Amerikai Egyesült régió) | Ha az RA-GRS/GRS engedélyezve van, 10 GB/s az LRS és zrs esetén 5 GB/s<sup>1</sup> |
| Maximális kimenő tárfiókonként (Amerikai Egyesült régió) | 50 GB/s |

<sup>1</sup>[azure Storage replikáció](https://docs.microsoft.com/azure/storage/common/storage-redundancy) lehetőségek a következők:
* **RA-GRS**: olvasási hozzáférésű georedundáns tárolás. RA-GRS engedélyezése esetén a másodlagos hely kimenő célok megegyeznek az elsődleges helyen.
* **GRS**: georedundáns tárolást. 
* **A ZRS**: zónaredundáns tárolás.
* **LRS**: helyileg redundáns tárolás. 

