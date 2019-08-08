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
ms.openlocfilehash: 46bf9851ccf8877885d9f98a023aa95ebdc533bc
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857400"
---
| Resource | Target | Rögzített korlát |
|----------|--------------|------------|
| Tárolási szinkronizálási szolgáltatások régiónként | 20 Storage Sync Services | Igen |
| Szinkronizálási csoportok/Storage Sync Service | 100 szinkronizálási csoportok | Igen |
| Regisztrált kiszolgálók/Storage Sync Service | 99-kiszolgálók | Igen |
| Felhőbeli végpontok szinkronizálása csoportonként | 1 Felhőbeli végpont | Igen |
| Kiszolgálói végpontok szinkronizálási csoportonként | 50 kiszolgálói végpontok | Nem |
| Kiszolgálói végpontok száma kiszolgálónként | 30 kiszolgálói végpont | Igen |
| Fájlrendszerbeli objektumok (címtárak és fájlok) szinkronizálási csoportonként | 25 000 000 objektum | Nem |
| A fájlrendszer objektumainak maximális száma (címtárak és fájlok) a címtárban | 5 000 000 objektum | Igen |
| Maximális objektum (címtárak és fájlok) biztonsági leíró mérete | 64 KiB | Igen |
| Fájlméret | 100 GiB | Nem |
| A többszintű fájl minimális fájlmérete | 64 KiB | Igen |
| Egyidejű szinkronizálási munkamenetek | V4-ügynök és újabb verziók: A korlát a rendelkezésre álló rendszererőforrások alapján változhat. <BR> V3-ügynök: Másodpercenként két aktív szinkronizálási munkamenet vagy legfeljebb nyolc aktív szinkronizálási munkamenet kiszolgálónként. | Igen

> [!Note]  
> Egy Azure File Sync végpont akár egy Azure-fájlmegosztás méretére is méretezhető. Ha az Azure-fájlmegosztás mérete eléri a korlátot, a szinkronizálás nem fog működni.
