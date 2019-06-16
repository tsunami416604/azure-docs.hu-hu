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
ms.openlocfilehash: 2614c9290bf31813d59ee753a31622bccf0682b8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66114519"
---
| Resource | Target | Rögzített korlátja |
|----------|--------------|------------|
| Társzinkronizálási szolgáltatás régiónként | 20 Társzinkronizálási szolgáltatás | Igen |
| Szinkronizálási csoportok tárolási szinkronizálási szolgáltatásonként | 100 szinkronizálási csoportok | Igen |
| Regisztrált kiszolgálók tárolási szinkronizálási szolgáltatásonként | 99 kiszolgálók | Igen |
| Felhőbeli végpont szinkronizálási csoportonként | 1 felhőbeli végpont | Igen |
| Kiszolgálóvégpontok szinkronizálási csoportonként | 50 kiszolgálói végpontot | Nem |
| Kiszolgálóvégpontok kiszolgálónként | 30 kiszolgálói végpontot | Igen |
| Fájlrendszer-objektumok (fájlok és könyvtárak) szinkronizálási csoportonként | 25 millió objektumok | Nem |
| Fájlrendszer-objektumok (fájlok és könyvtárak) egy könyvtár a maximális számát | 1 millió objektumok | Igen |
| Maximális (fájlok és könyvtárak) biztonsági leíró objektumméret | 64 KiB | Igen |
| Fájlméret | 100 GiB | Nem |
| A fájlok helyezhető el a minimális fájlméret | 64 KiB | Igen |
| Egyidejű szinkronizálási munkamenetek | V4-ügynököt, és később: Elérhető rendszererőforrásokat attól függően változik, a korlát. <BR> V3 ügynök: Két aktív szinkronizálási munkamenetek processzor- és a egy legfeljebb nyolc aktív szinkronizálási munkamenetek kiszolgálónként. | Igen

> [!Note]  
> Az Azure File Sync végpont is vertikális felskálázás az Azure-fájlmegosztás méretét. Ha eléri az Azure-beli fájlmegosztás méretének korlátját, szinkronizálási nem tudják megfelelően működjenek.