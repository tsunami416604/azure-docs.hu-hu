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
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34852058"
---
| Erőforrás | Alapértelmezett korlát |
| --- | --- |
| Régiónként storage-fiókok száma | 200<sup>1</sup> |
| A maximális tárolókapacitás fiók | 500 TiB<sup>2</sup> |
| Blob tárolók, blobok, fájlmegosztások, táblák, üzenetsorok, entitásokat vagy tárfiók üzenetek maximális száma | Korlátlan |
| A tárfiók / maximális lekérdezési gyakorisága | 20 000 kérelmek / másodperc<sup>2</sup> |
| Maximális érkező<sup>3</sup> tárolási fiókonként (US régió) | Ha az RA-GRS vagy GRS engedélyezve van, az LRS/ZRS 20 GB/s 10 GB/s<sup>4</sup> |
| Maximális kilépő<sup>3</sup> tárolási fiókonként (US régió) | Ha az RA-GRS vagy GRS engedélyezve van, az LRS/ZRS 30 GB/s 20 GB/s<sup>4</sup> |
| Maximális érkező<sup>3</sup> tárolási fiókonként (Amerikai Egyesült régiókban) | Ha az RA-GRS vagy GRS engedélyezve van, az LRS/ZRS 10 GB/s 5 GB/s<sup>4</sup> |
| Maximális kilépő<sup>3</sup> tárolási fiókonként (Amerikai Egyesült régiókban) | Ha az RA-GRS vagy GRS engedélyezve van, az LRS/ZRS 15 GB/s 10 GB/s<sup>4</sup> |

<sup>1</sup>magában foglalja a Standard és prémium szintű storage-fiókok. Ha több mint 200 storage-fiókok egy adott régióban van szüksége, ellenőrizze a kérelmet [Azure támogatási](https://azure.microsoft.com/support/faq/). Az Azure Storage csapat rendszer tekintse át az Ön üzleti esetében, és előfordulhat, hogy jóváhagyásához legfeljebb 250 tárfiókok az adott régió. 

<sup>2</sup> Ha a tárfiók bővített korlátok van szüksége, lépjen kapcsolatba [Azure támogatási](https://azure.microsoft.com/support/faq/). Az Azure Storage csapat fog tekintse át a kérést, és előfordulhat, hogy hagyja jóvá a magasabb korlátok eseti alapon. Általános célú mindkét és Blob storage-fiókok támogatási kérelem nagyobb kapacitást, belépés és kilépés és kérelmek aránya. A Blob storage-fiókok új maximális értéket, lásd: [nagyobb, magasabb méretezési tárfiókok bejelentése](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

<sup>3</sup> tárfiókonként csak a fiók be-és kilépési korlátok alapján. *Érkező* küldi el a tárfiók (kérelmek) összes adatokra vonatkozik. *Kimenő forgalom* alatt egy tárfiók felől fogadott összes adatot (választ) értjük.  

<sup>4</sup>azure Storage redundancia lehetőségek a következők:
* **RA-GRS**: írásvédett georedundáns tárolás. RA-GRS engedélyezve van, ha a másodlagos hely kimenő forgalom cél megegyeznek az elsődleges hely.
* **Georedundáns**: georedundáns tárolást. 
* **A ZRS**: zónaredundáns tárolás.
* **LRS**: helyileg redundáns tárolás. 
