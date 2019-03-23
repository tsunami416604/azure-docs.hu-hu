---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 03/23/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e3d2466f05028a963256dbcc052c46650857836d
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372713"
---
### <a name="premium-performance-block-blob-storage"></a>Prémium szintű teljesítményt blokkblob-tárolás

Prémium szintű teljesítményt block blob storage-fiókban az alkalmazásokat, amelyek kisebb, használja a kilobájtoktól a petabájtokig tartományát, objektumok van optimalizálva. Ideális a rendkívül nagy tranzakciós díjakat biztosítanak vagy konzisztens közel valós idejű tároló igénylő alkalmazásokhoz. Prémium szintű teljesítményt blokkblob típusú tárolás az alkalmazások méretezése a célja. Ha azt tervezi, a kérések száma másodpercenként több ezer vagy több petabájtnyi, a tárolási kapacitást több száz igénylő alkalmazás üzembe helyezése, lépjen kapcsolatba velünk beküld egy támogatási kérést az a [az Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="premium-performance-page-blob-storage"></a>Prémium szintű teljesítményt lap a blob storage

Prémium szintű teljesítményt, általános célú v1 vagy v2-es tárfiókban elérhető a következő teljesítménycélokat:

| Teljes számla kapacitás                            | Helyileg redundáns tárfiókok teljes sávszélesség                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| Lemez kapacitása: 35 TB <br>Pillanatkép-kapacitás: 10 TB | Felfelé 50 Gigabit / másodperc a bejövő<sup>1</sup> + kimenő<sup>2</sup> |

<sup>1</sup> tárfiók küldött összes adatot (kérést)

<sup>2</sup> egy tárfiókból fogadott összes adatot (választ)

Ha nem felügyelt lemezek prémium szintű teljesítményt tárfiókok használja, és az alkalmazás meghaladja a skálázhatósági célokat, az egy tárfiókban, érdemes migrálása felügyelt lemezekre. Ha nem szeretné migrálása felügyelt lemezekre, hozhat létre az alkalmazás több tárfiók használata. Ezt követően az adatok particionálása ezen a tárfiókon keresztül. Például, ha 51 TB-os lemezek csatolása több virtuális gép között elosztva őket két tárfiókot. 35 TB csak az egyetlen prémium szintű storage-fiókok. Győződjön meg arról, hogy a teljesítmény egyetlen premium storage-fiók soha nem rendelkezik több mint 35 TB kiosztott lemezeket.