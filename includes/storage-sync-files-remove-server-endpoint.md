---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 33dff710d83bd12a8db343a89c6e4576d1397ba7
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66114569"
---
Nem: a kiszolgálói végpont eltávolítása nem jelenti például, hogy a kiszolgáló újraindítása! Szinte mindig egy megfelelő megoldást problémáinak szinkronizálási, felhőbeli rétegezés vagy egyéb szempontok az Azure File Sync eltávolításával és ismételt létrehozásával a kiszolgálói végpontot. Kiszolgálói végpont eltávolítása egy destruktív művelet, és előfordulhat, hogy abban az esetben rétegzett fájlok adatvesztést eredményez kívüliek a kiszolgálói végpont névtér (lásd: [miért rétegzett fájlok léteznek a kiszolgálói végpont névtér kívül](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) számára További információ), vagy nem elérhető fájlok, a kiszolgálói végpont névtéren belül meglévő rétegzett fájlok. Ezek a problémák nem oldható fel, ha a kiszolgálói végpont újból létrejön. Rétegzett fájlok a kiszolgálói végpont névtérben előfordulhat, hogy létezik, akkor is, ha soha nem történt felhőbeli rétegezés engedélyezve van. Ezért javasoljuk, hogy nem törli a kiszolgálói végpontot, kivéve, ha szeretné állítsa le az Azure File Sync használata az adott mappa vagy a kapott explicit módon utasítást ehhez egy Microsoft-mérnök szerint. A remove-kiszolgálói végpontot további információkért lásd: [távolítsa el a kiszolgálói végpont](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint).    