---
title: Az Azure File Sync ügynök telepítése
description: Az Azure File Sync ügynök telepítése. Közös szövegterület, amely et megosztanak az áttelepítési dokumentumok között.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 694becc49667204ef2071a140bb6330285089039
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78209414"
---
Ebben a szakaszban telepíti az Azure File Sync ügynök a Windows Server.
A [telepítési útmutató](../articles/storage/files/storage-sync-files-deployment-guide.md) azt mutatja, hogy ki kell kapcsolnia az **IE fokozott biztonságát**. Az IE fokozott biztonsága olyan biztonsági intézkedés, amely nem alkalmazható az Azure File Sync szolgáltatással, és kikapcsolásával minden probléma nélkül hitelesítheti magát az Azure-ban.

Nyissa meg a PowerShellt, és telepítse a szükséges PowerShell-modulokat a következő parancsokkal. A rendszer kéri:

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

Ha bármilyen kérdése van elérte az interneten a szerver, most van itt az ideje, hogy megoldja őket. Az Azure File Sync bármely elérhető hálózati kapcsolatot használ az internethez.
Az internet eléréséhez proxykiszolgáló megkövetelése is támogatott. Most már konfigurálhat egy gépszintű proxyt, vagy megadhat egy proxyt, amelyet csak a fájlszinkronizálás fog használni az ügynök telepítése során.

Ha ez azt jelenti, hogy meg kell nyitnia a tűzfalakat ehhez a kiszolgálóhoz, akkor ez elfogadható megközelítés lehet az Ön számára. A kiszolgáló telepítésének végén, a kiszolgáló regisztrációjának befejezése után, lesz egy hálózati kapcsolati jelentés, amely megmutatja a pontos végpont URL-címeket az Azure-ban, hogy a fájl szinkronizálása kell kommunikálni a kiválasztott régióban. A jelentés azt is megmondja, hogy miért van szükség kommunikációra. A jelentés segítségével a kiszolgáló körüli tűzfalakat zárolhatja adott URL-címekre.

Konzervatívabb megközelítést is követhet, amelyben nem nyitja meg a tűzfalakat széles, hanem korlátozza a kiszolgálót, hogy magasabb szintű DNS-névterekre kommunikáljon – az [Azure File Sync proxy és a tűzfal beállításairól](../articles/storage/files/storage-sync-files-firewall-and-proxy.md) szóló cikkben további dokumentáció és részletek találhatók. Kövesse saját hálózati gyakorlati tanácsait.

A *kiszolgálótelepítő* varázsló végén megjelenik egy *kiszolgálóregisztrációs* varázsló.
Regisztrálja a kiszolgálót a Storage Sync Service Azure-erőforráskorábbi.

Ezeket a lépéseket a központi telepítési útmutató ismerteti részletesebben, beleértve a fenti PowerShell-modulokat, amelyeket először telepítenie kell: [Az Azure File Sync ügynök telepítése](../articles/storage/files/storage-sync-files-deployment-guide.md).

A legújabb ügynököt kell használni, és letölthető a Microsoft Letöltőközpontból: [Azure File Sync - agent](https://aka.ms/AFS/agent "Az Azure File Sync ügynök letöltése").

Sikeres telepítés és a kiszolgáló regisztrációja után ellenőrizheti, hogy sikeresen végrehajtotta-e ezt a lépést: Keresse meg a Storage Sync Service erőforrást az Azure Portalon, majd kövesse a bal oldali menüt a "Regisztrált kiszolgálók" parancsra. Látni fogja, hogy a szerver azonnal ott van.
