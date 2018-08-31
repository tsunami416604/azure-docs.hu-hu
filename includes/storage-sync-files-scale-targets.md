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
ms.openlocfilehash: 3f70a8cf2df25f487de7cd1a8c8cbdf9431839f0
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43285628"
---
| Erőforrás | Cél | Rögzített korlátja |
|----------|--------------|------------|
| Társzinkronizálási szolgáltatás előfizetésenként | 15 Társzinkronizálási szolgáltatás | Nem |
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
