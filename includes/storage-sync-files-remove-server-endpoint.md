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
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34738683"
---
Nincs: a kiszolgáló végpont eltávolítása nincs például a kiszolgáló újraindítása! Szinte mindig egy megfelelő megoldás, a problémák kijavítása szinkronizálási, rétegezéséhez felhőalapú vagy egyéb szempontok Azure fájlszinkronizálás eltávolításával, majd újbóli létrehozása a kiszolgáló végpont. A kiszolgáló végpont eltávolítása egy felülíró műveletet, és előfordulhat, hogy abban az esetben, ha rétegzett fájlok adatvesztést eredményez meglévő kívül a kiszolgáló végpont névtér (lásd: [miért rétegzett fájlok léteznek kívül a kiszolgáló végpont névtér](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) a További információ) vagy a kiszolgáló végpont névtéren belül meglévő rétegzett fájlok nem érhetők el fájlokat. Ezeket a problémákat nem oldható fel, ha a kiszolgáló végpont újból létrejön. Rétegzett fájlok előfordulhat, hogy a kiszolgáló végpont névtéren belül létezik, akkor is, ha soha nem engedélyezett rétegezéséhez felhő. Ezért azt javasoljuk, hogy nem törli a kiszolgáló végpont kivéve, ha szeretné a továbbiakban használni az Azure fájlszinkronizálás az adott mappa vagy kapott explicit módon utasítást erre a Microsoft mérnöke által. Távolítsa el a kiszolgáló végpontokon további információkért lásd: [távolítsa el a kiszolgáló végpont](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint).    