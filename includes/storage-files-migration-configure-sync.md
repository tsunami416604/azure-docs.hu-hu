---
title: Azure File Sync konfigurálása
description: Azure File Sync konfigurálása. Egy közös szöveges blokk, amely az áttelepítési dokumentumok között meg van osztva.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 53d7af6b43ff24ab12501570385162759f4c7633
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209583"
---
Ez a lépés az előző lépések során a Windows Serveren beállított összes erőforrást és mappát összekapcsolja.

* Jelentkezzen be az [Azure Portal](https://portal.azure.com).
* Keresse meg a Storage Sync szolgáltatás erőforrását.
* Hozzon létre egy új *szinkronizálási csoportot* a Storage Sync Service-erőforráson belül minden egyes Azure-fájlmegosztás esetében. Azure File Sync terminológiában az Azure-fájlmegosztás *Felhőbeli végpont* lesz a szinkronizálási topológiában, amely a szinkronizálási csoport létrehozásával van leírva. A szinkronizálási csoport létrehozásakor adjon neki egy ismerős nevet, például hogy felismerje, hogy mely fájlok vannak szinkronizálva. Győződjön meg arról, hogy az Azure-fájlmegosztást egyező névvel hivatkozik.
* A szinkronizálási csoport létrehozása után megjelenik egy sor, amely megjelenik a szinkronizálási csoportok listájában. A szinkronizálási csoport tartalmának megjelenítéséhez kattintson a névre (egy hivatkozásra). Az Azure-fájlmegosztás a "Felhőbeli végpontok" alatt jelenik meg.
* Keresse meg a Command gombot a *+ kiszolgáló-végpont hozzáadása*gombra. Az üzembe helyezett helyi kiszolgálón lévő mappa lesz a *kiszolgálói végpont*elérési útja.
