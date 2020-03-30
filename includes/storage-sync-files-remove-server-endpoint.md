---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: be3ce42ccdb9bedd02b8dead2426ac629fa12ef2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "72391669"
---
Nem: a kiszolgálóvégpont eltávolítása nem olyan, mint egy kiszolgáló újraindítása! A kiszolgálóvégpont eltávolítása és újbóli létrehozása szinte soha nem megfelelő megoldás a szinkronizálási, felhőrétegezési vagy az Azure File Sync egyéb szempontjaival kapcsolatos problémák megoldására. A kiszolgálóvégpont eltávolítása romboló művelet. Adatvesztést okozhat abban az esetben, ha rétegzett fájlok a kiszolgálóvégpont i. További információért olvassa el, [hogy miért léteznek rétegzett fájlok a kiszolgálóvégpont-névtéren kívül.](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) Vagy elérhetetlen fájlokat eredményezhet a kiszolgálóvégpont itörzsében található rétegzett fájlok hoz. Ezek a problémák nem oldódnak meg a kiszolgálóvégpont újbóli létrehozásakor. Rétegzett fájlok akkor is létezhetnek a kiszolgálóvégpont-névtérben, ha a felhőrétegezés még soha nem volt engedélyezve. Ezért javasoljuk, hogy ne távolítsa el a kiszolgálóvégpontot, hacsak nem szeretné leállítani az Azure File Sync használatát ezzel a mappával, vagy ha egy Microsoft-mérnök kifejezetten erre utasítja. A kiszolgálóvégpontok eltávolításáról a [Kiszolgálóvégpont eltávolítása](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint)című témakörben talál további információt.    
