---
title: Az Azure-fájlszinkronizálás konfigurálása
description: Konfigurálja az Azure File Sync konfigurálását. Közös szövegterület, amely et megosztanak az áttelepítési dokumentumok között.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 53d7af6b43ff24ab12501570385162759f4c7633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78209583"
---
Ez a lépés összekapcsolja a Windows Server en az előző lépések során beállított összes erőforrást és mappát.

* Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
* Keresse meg a Storage Sync Service erőforrást.
* Hozzon létre egy új *szinkronizálási csoportot* a Storage Sync Service erőforráson belül minden Egyes Azure-fájlmegosztáshoz. Az Azure File Sync terminológiában az Azure-fájlmegosztás *felhővégpontká* válik a szinkronizálási topológia, amelyet egy szinkronizálási csoport létrehozásával ír le. A szinkronizálási csoport létrehozásakor adjon neki egy ismerős nevet, hogy felismerje, mely fájlkészlet szinkronizálitt. Győződjön meg arról, hogy az Azure-fájlmegosztásra egy megfelelő névvel hivatkozik.
* A szinkronizálási csoport létrehozása után megjelenik egy sor a szinkronizálási csoportok listájában. Kattintson a névre (egy hivatkozásra) a szinkronizálási csoport tartalmának megjelenítéséhez. Az Azure-fájlmegosztás a "Felhővégpontok" alatt jelenik meg.
* Keresse meg a parancsgombot *a + Add Server Endpoint*. A kiépített helyi kiszolgálón lévő mappa lesz a *kiszolgálóvégpont*elérési útja.
