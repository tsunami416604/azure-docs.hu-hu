---
title: Azure File Sync konfigurálása
description: Azure File Sync konfigurálása. Közös szöveges blokk, amely az áttelepítési dokumentumok között meg van osztva.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 64b99976a306c3c8423f5115c95a15158a3ddb51
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043178"
---
Ez a lépés összekapcsolja a Windows Server-példányon beállított összes erőforrást és mappát az előző lépések során.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keresse meg a Storage Sync szolgáltatás erőforrását.
1. Hozzon létre egy új *szinkronizálási csoportot* a Storage Sync Service-erőforráson belül minden egyes Azure-fájlmegosztás esetében. Azure File Sync terminológiában az Azure-fájlmegosztás egy szinkronizálási csoport létrehozásával megjelenő szinkronizálási topológia *Felhőbeli végpontja* lesz. A szinkronizálási csoport létrehozásakor adjon neki egy ismerős nevet, hogy felismerje, hogy mely fájlok vannak szinkronizálva. Győződjön meg arról, hogy az Azure-fájlmegosztást egyező névvel hivatkozik.
1. A szinkronizálási csoport létrehozása után egy sor jelenik meg a szinkronizálási csoportok listájában. A szinkronizálási csoport tartalmának megjelenítéséhez válassza ki a nevet (egy hivatkozást). Az Azure-fájlmegosztás a **Felhőbeli végpontok** területen jelenik meg.
1. Keresse meg a **+ kiszolgáló-végpont hozzáadása** gombot. Az üzembe helyezett helyi kiszolgálón lévő mappa lesz a *kiszolgálói végpont* elérési útja.
