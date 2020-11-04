---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 10/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 29518fb971649087d8a5afa39d69c7fc2c014f98
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93331158"
---
| Erőforrás | Cél | Rögzített korlát |
|----------|--------------|------------|
| Tárolási szinkronizálási szolgáltatások régiónként | 100 Storage Sync Services | Igen |
| Szinkronizálási csoportok/Storage Sync Service | 200 szinkronizálási csoportok | Igen |
| Regisztrált kiszolgálók/Storage Sync Service | 99-kiszolgálók | Igen |
| Felhőbeli végpontok szinkronizálása csoportonként | 1 Felhőbeli végpont | Igen |
| Kiszolgálói végpontok szinkronizálási csoportonként | 100 kiszolgálói végpontok | Igen |
| Kiszolgálói végpontok száma kiszolgálónként | 30 kiszolgálói végpont | Igen |
| Fájlrendszerbeli objektumok (címtárak és fájlok) szinkronizálási csoportonként | 100 000 000 objektum | Nem |
| A fájlrendszer objektumainak maximális száma (címtárak és fájlok) a címtárban | 5 000 000 objektum | Igen |
| Maximális objektum (címtárak és fájlok) biztonsági leíró mérete | 64 KiB | Igen |
| Fájlméret | 100 GiB | Nem |
| A többszintű fájl minimális fájlmérete | V9-es és újabb verziók: fájlrendszerbeli fürt méretétől függően (dupla fájlrendszerű fürt mérete). Ha például a fájlrendszer fürtjének mérete 4kb, a rendszer a minimális fájlméretet fogja 8 kb.<br> V8-as és régebbi: 64 KiB  | Igen |

> [!Note]  
> Egy Azure File Sync végpont akár egy Azure-fájlmegosztás méretére is méretezhető. Ha az Azure-fájlmegosztás mérete eléri a korlátot, a szinkronizálás nem fog működni.
