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
ms.openlocfilehash: a29f1c4a625552dd958884c6a172bee470e61ca6
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2018
ms.locfileid: "49312495"
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
| Egyidejű szinkronizálási munkamenetek | processzor- és legfeljebb 8 aktív szinkronizálási munkamenetek kiszolgálónként 2 active sync-munkamenetek | Igen |
