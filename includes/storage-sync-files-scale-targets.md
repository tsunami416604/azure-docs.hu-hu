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
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "74795968"
---
| Erőforrás | Cél | Rögzített korlát |
|----------|--------------|------------|
| Tárolási szinkronizálási szolgáltatások régiónként | 20 Storage Sync Services | Igen |
| Szinkronizálási csoportok/Storage Sync Service | 100 szinkronizálási csoportok | Igen |
| Regisztrált kiszolgálók/Storage Sync Service | 99-kiszolgálók | Igen |
| Felhőbeli végpontok szinkronizálása csoportonként | 1 Felhőbeli végpont | Igen |
| Kiszolgálói végpontok szinkronizálási csoportonként | 50 kiszolgálói végpontok | No |
| Kiszolgálói végpontok száma kiszolgálónként | 30 kiszolgálói végpont | Igen |
| Fájlrendszerbeli objektumok (címtárak és fájlok) szinkronizálási csoportonként | 100 000 000 objektum | No |
| A fájlrendszer objektumainak maximális száma (címtárak és fájlok) a címtárban | 5 000 000 objektum | Igen |
| Maximális objektum (címtárak és fájlok) biztonsági leíró mérete | 64 KiB | Igen |
| Fájlméret | 100 GiB | No |
| A többszintű fájl minimális fájlmérete | V9: fájlrendszerbeli fürt méretétől függően (dupla fájlrendszerű fürt mérete). Ha például a fájlrendszer fürtjének mérete 4kb, a rendszer a minimális fájlméretet fogja 8 kb.<br> V8-as és régebbi: 64 KiB  | Igen |

> [!Note]  
> Egy Azure File Sync végpont akár egy Azure-fájlmegosztás méretére is méretezhető. Ha az Azure-fájlmegosztás mérete eléri a korlátot, a szinkronizálás nem fog működni.
