---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: b480f9720cabddbba9e9c35c12ca1f8dda7ab5ab
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190029"
---
| Resource | Cél | Rögzített korlátja |
|----------|--------------|------------|
| Társzinkronizálási szolgáltatás régiónként | 20 Társzinkronizálási szolgáltatás | Igen |
| Szinkronizálási csoportok tárolási szinkronizálási szolgáltatásonként | 100 szinkronizálási csoportok | Igen |
| Regisztrált kiszolgálók tárolási szinkronizálási szolgáltatásonként | 99 kiszolgálók | Igen |
| Felhőbeli végpont szinkronizálási csoportonként | 1 felhőbeli végpont | Igen |
| Kiszolgálóvégpontok szinkronizálási csoportonként | 50 kiszolgálói végpontot | Nem |
| Kiszolgálóvégpontok kiszolgálónként | 30 kiszolgálói végpontot | Igen |
| Végpont mérete | 4 TiB | Nem |
| Fájlrendszer-objektumok (fájlok és könyvtárak) szinkronizálási csoportonként | 25 millió objektumok | Nem |
| Fájlrendszer-objektumok (fájlok és könyvtárak) egy könyvtár a maximális számát | 1 millió objektumok | Igen |
| Maximális (fájlok és könyvtárak) biztonsági leíró objektumméret | 64 KiB | Igen |
| Fájlméret | 100 GiB | Nem |
| A fájlok helyezhető el a minimális fájlméret | 64 KiB | Igen |
| Egyidejű szinkronizálási munkamenetek | V4-ügynököt, és később: Elérhető rendszererőforrásokat attól függően változik, a korlát. <BR> V3 ügynök: Két aktív szinkronizálási munkamenetek processzor- és a egy legfeljebb nyolc aktív szinkronizálási munkamenetek kiszolgálónként. | Igen
