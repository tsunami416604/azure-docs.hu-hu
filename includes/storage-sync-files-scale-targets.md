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
ms.openlocfilehash: 03fe587ede297ac7dea90b7a5fb2d5323f60659e
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628147"
---
| Erőforrás | Cél | Rögzített korlátja |
|----------|--------------|------------|
| Társzinkronizálási szolgáltatás előfizetésenként | 15 régiónként Társzinkronizálási szolgáltatás | Nem |
| Szinkronizálási csoportok tárolási szinkronizálási szolgáltatásonként | 100 szinkronizálási csoportok | Igen |
| Regisztrált kiszolgálók tárolási szinkronizálási szolgáltatásonként | 99 kiszolgálók | Igen |
| Felhőbeli végpont szinkronizálási csoportonként | 1 felhőbeli végpont | Igen |
| Kiszolgálóvégpontok szinkronizálási csoportonként | 50 kiszolgálói végpontot | Nem |
| Kiszolgálóvégpontok kiszolgálónként | 33 – 99 kiszolgálói végpontot | Igen, de a konfiguráció (CPU, memória, a kötetek, a fájl adatváltozás, a fájlok száma, stb.) alapján változik |
| Végpont mérete | 4 TiB | Nem |
| Fájlrendszer-objektumok (fájlok és könyvtárak) szinkronizálási csoportonként | 25 millió objektumok | Nem |
| Fájlrendszer-objektumok (fájlok és könyvtárak) egy könyvtár a maximális számát | 200 000 objektumok | Igen |
| Maximális objektum (fájlok és könyvtárak) nevének hossza | 255 karakter lehet | Igen |
| Maximális (fájlok és könyvtárak) biztonsági leíró objektumméret | 4 KiB | Igen |
| Fájlméret | 100 GiB | Nem |
| A fájlok helyezhető el a minimális fájlméret | 64 KiB | Igen |
| Egyidejű szinkronizálási munkamenetek | V4 ügynök: korlát elérhető rendszererőforrásokat alapján változik. <BR> V3 ügynök: processzor- és legfeljebb 8 active 2 aktív szinkronizálási munkamenetek szinkronizálása a kiszolgáló-munkamenetek | Igen
