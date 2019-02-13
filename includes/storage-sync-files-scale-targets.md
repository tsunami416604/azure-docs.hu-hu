---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 07/18/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: c82717be3791a1bc397bbbdb369c55cd2ba04439
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56213068"
---
| Erőforrás | Cél | Rögzített korlátja |
|----------|--------------|------------|
| Társzinkronizálási szolgáltatás régiónként | 15 Storage Sync Services | Igen |
| Szinkronizálási csoportok tárolási szinkronizálási szolgáltatásonként | 100 szinkronizálási csoportok | Igen |
| Regisztrált kiszolgálók tárolási szinkronizálási szolgáltatásonként | 99 kiszolgálók | Igen |
| Felhőbeli végpont szinkronizálási csoportonként | 1 felhőbeli végpont | Igen |
| Kiszolgálóvégpontok szinkronizálási csoportonként | 50 kiszolgálói végpontot | Nem |
| Kiszolgálóvégpontok kiszolgálónként | 30 kiszolgálói végpontot | Igen |
| Végpont mérete | 4 TiB | Nem |
| Fájlrendszer-objektumok (fájlok és könyvtárak) szinkronizálási csoportonként | 25 millió objektumok | Nem |
| Fájlrendszer-objektumok (fájlok és könyvtárak) egy könyvtár a maximális számát | 1 000 000 objektumok | Igen |
| Maximális (fájlok és könyvtárak) biztonsági leíró objektumméret | 4 KiB | Igen |
| Fájlméret | 100 GiB | Nem |
| A fájlok helyezhető el a minimális fájlméret | 64 KiB | Igen |
| Egyidejű szinkronizálási munkamenetek | V4-ügynököt, és később: Korlát rendelkezésre álló erőforrásoktól függ. <BR> V3 ügynök: processzor- és legfeljebb 8 aktív szinkronizálási munkamenetek kiszolgálónként 2 active sync-munkamenetek | Igen
