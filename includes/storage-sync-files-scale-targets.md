---
title: fájlbefoglalás
description: fájlbefoglalás
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 95c553d26a3e79b53106b933c629c5884c3e004c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84466836"
---
| Erőforrás | Cél | Rögzített korlát |
|----------|--------------|------------|
| Tárolási szinkronizálási szolgáltatások régiónként | 100 Storage Sync Services | Yes |
| Szinkronizálási csoportok/Storage Sync Service | 200 szinkronizálási csoportok | Yes |
| Regisztrált kiszolgálók/Storage Sync Service | 99-kiszolgálók | Yes |
| Felhőbeli végpontok szinkronizálása csoportonként | 1 Felhőbeli végpont | Yes |
| Kiszolgálói végpontok szinkronizálási csoportonként | 50 kiszolgálói végpontok | No |
| Kiszolgálói végpontok száma kiszolgálónként | 30 kiszolgálói végpont | Yes |
| Fájlrendszerbeli objektumok (címtárak és fájlok) szinkronizálási csoportonként | 100 000 000 objektum | No |
| A fájlrendszer objektumainak maximális száma (címtárak és fájlok) a címtárban | 5 000 000 objektum | Yes |
| Maximális objektum (címtárak és fájlok) biztonsági leíró mérete | 64 KiB | Yes |
| Fájlméret | 100 GiB | No |
| A többszintű fájl minimális fájlmérete | V9: fájlrendszerbeli fürt méretétől függően (dupla fájlrendszerű fürt mérete). Ha például a fájlrendszer fürtjének mérete 4kb, a rendszer a minimális fájlméretet fogja 8 kb.<br> V8-as és régebbi: 64 KiB  | Yes |

> [!Note]  
> Egy Azure File Sync végpont akár egy Azure-fájlmegosztás méretére is méretezhető. Ha az Azure-fájlmegosztás mérete eléri a korlátot, a szinkronizálás nem fog működni.
