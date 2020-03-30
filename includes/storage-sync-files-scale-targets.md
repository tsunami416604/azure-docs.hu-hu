---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6a053b94813145f9ccd69158d18edb728d5dad61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74795968"
---
| Erőforrás | Cél | Szigorú korlát |
|----------|--------------|------------|
| Tárolási szinkronizálási szolgáltatások régiónként | 20 Tárolási szinkronizálási szolgáltatások | Igen |
| Szinkronizálási csoportok tárolószinkronizálási szolgáltatásonként | 100 szinkronizálási csoport | Igen |
| Regisztrált kiszolgálók tárolószinkronizálási szolgáltatásonként | 99 szerver | Igen |
| Felhővégpontok szinkronizálási csoportonként | 1 felhővégpont | Igen |
| Kiszolgálóvégpontok szinkronizálási csoportonként | 50 kiszolgálói végpont | Nem |
| Kiszolgálóvégpontok kiszolgálónként | 30 kiszolgálói végpont | Igen |
| Fájlrendszer-objektumok (könyvtárak és fájlok) szinkronizálási csoportonként | 100 millió tárgy | Nem |
| A könyvtárban lévő fájlrendszer-objektumok (könyvtárak és fájlok) maximális száma | 5 millió tárgy | Igen |
| Maximális objektum (könyvtárak és fájlok) biztonsági leíró mérete | 64 KiB | Igen |
| Fájlméret | 100 GiB | Nem |
| A rétegzendő fájl minimális fájlmérete | V9: A fájlrendszer fürtmérete (dupla fájlrendszer-fürtméret) alapján. Ha például a fájlrendszer fürtjének mérete 4 kb, a minimális fájlméret 8 kb lesz.<br> V8 és az idősebb: 64 KiB  | Igen |

> [!Note]  
> Az Azure File Sync végpontok egy Azure-fájlmegosztás méretére skálázható. Ha az Azure fájlmegosztás méretkorlátját eléri, a szinkronizálás nem fog tudni működni.
