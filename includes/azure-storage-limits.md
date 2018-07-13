---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/03/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 6572adb0d8d629910492603a17988b89acce2f17
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38755754"
---
| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Storage-fiókok régiónként és előfizetésenként száma | 200<sup>1</sup> |
| Tárfiókok kapacitásával maximális száma | 500 TiB<sup>2</sup> |
| Blob-tárolók, blobok, fájlmegosztások, táblák, üzenetsorok, entitások vagy tárfiókonként üzenetek maximális száma | Korlátlan |
| Kérelem maximális díj / storage-fiók | másodpercenként 20 000 kérelem<sup>2</sup> |
| Maximális bejövő<sup>3</sup> tárfiókonként (USA régió) | Ha az RA-GRS/GRS engedélyezve van, 20 GB/s az LRS és zrs esetén 10 GB/s<sup>4</sup> |
| Maximális kimenő<sup>3</sup> tárfiókonként (USA régió) | Ha az RA-GRS/GRS engedélyezése esetén 30 GB/s az LRS és zrs esetén 20 GB/s<sup>4</sup> |
| Maximális bejövő<sup>3</sup> tárfiókonként (Amerikai Egyesült régió) | Ha az RA-GRS/GRS engedélyezve van, 10 GB/s az LRS és zrs esetén 5 GB/s<sup>4</sup> |
| Maximális kimenő<sup>3</sup> tárfiókonként (Amerikai Egyesült régió) | Ha az RA-GRS/GRS engedélyezve van, 15 GB/s az LRS és zrs esetén 10 GB/s<sup>4</sup> |

<sup>1</sup>tartalmazza a Standard és prémium szintű storage-fiókok. Ha egy adott régióban több mint 200 tárfiókra van szüksége, győződjön meg a kérést [Azure-támogatási](https://azure.microsoft.com/support/faq/). Az Azure Storage csapata áttekinti az üzleti esetekhez, és jóváhagyhat legfeljebb 250 tárfiókot az adott régió. 

<sup>2</sup> , ha a tárfiók kibontott korlátok van szüksége, forduljon a [Azure-támogatási](https://azure.microsoft.com/support/faq/). Az Azure Storage csapata áttekinti a kérelmet, és előfordulhat, hogy hagyja jóvá a magasabb korlátok eseti alapon. Mindkét általános célú és a Blob storage-fiókok nagyobb kapacitást, a bejövő/kimenő forgalom és a kérelmek arányának támogatja a kérelem által. A Blob storage-fiókok új kapacitástervezés, lásd: [nagyobb, magasabb méretezési tárfiókok bejelentése](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

<sup>3</sup> csak az a fiók bejövő/kimenő forgalom korlátok korlátozzuk. *Bejövő forgalom* küld a storage-fiók összes adatot (kérést) hivatkozik. *Kimenő forgalom* alatt egy tárfiók felől fogadott összes adatot (választ) értjük.  

<sup>4</sup>azure Storage adatredundanciával kapcsolatos lehetőségek a következők:
* **RA-GRS**: olvasási hozzáférésű georedundáns tárolás. RA-GRS engedélyezése esetén a másodlagos hely kimenő célok megegyeznek az elsődleges helyen.
* **GRS**: georedundáns tárolást. 
* **A ZRS**: zónaredundáns tárolás.
* **LRS**: helyileg redundáns tárolás. 
