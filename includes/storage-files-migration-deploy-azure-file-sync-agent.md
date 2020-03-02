---
title: Az Azure File Sync-ügynök üzembe helyezése
description: A Azure File Sync ügynök üzembe helyezése. Egy közös szöveges blokk, amely az áttelepítési dokumentumok között meg van osztva.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 694becc49667204ef2071a140bb6330285089039
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209414"
---
Ebben a szakaszban a Azure File Sync-ügynököt telepíti a Windows-kiszolgálóra.
Az [üzembe helyezési útmutató](../articles/storage/files/storage-sync-files-deployment-guide.md) azt szemlélteti, hogy ki kell kapcsolni az **IE fokozott biztonsági**funkcióit. Az Internet Explorer fokozott biztonsága olyan biztonsági mérték, amely nem alkalmazható a Azure File Sync és a kikapcsolás lehetővé teszi, hogy problémák nélkül hitelesítse magát az Azure-ban.

Nyissa meg a PowerShellt, és telepítse a szükséges PowerShell-modulokat a következő parancsokkal. Ha a rendszer kéri, telepítse a teljes modult és a NuGet-szolgáltatót:

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

Ha problémák merülnek fel az internetről a kiszolgálóról, most már az idő a megoldására. Azure File Sync bármely elérhető hálózati kapcsolatot használ az internethez.
A proxykiszolgáló az Internet elérésének megkövetelése is támogatott. Beállíthat egy gépi szintű proxyt, vagy megadhat egy olyan proxyt, amelyet a rendszer az ügynök telepítése során használ a fájl szinkronizálásához.

Ha ez azt jelenti, hogy meg kell nyitnia a tűzfalat ehhez a kiszolgálóhoz, akkor ez elfogadható megközelítés lehet. A kiszolgáló telepítése után a befejezett kiszolgáló regisztrálása után egy hálózati kapcsolat jelentés jelenik meg, amely az Azure pontos végpont URL-címeit jeleníti meg, ezért a fájl-szinkronizálásnak kommunikálnia kell a kiválasztott régióval. A jelentés arról is tájékoztat, hogy miért van szükség a kommunikációra. A jelentés segítségével az adott URL-címekre állíthatja le a kiszolgáló körüli tűzfalakat.

Azt is megteheti, hogy egy konzervatív megközelítést alkalmaz, amelyben nem nyitja meg a tűzfalak széles körét, hanem korlátozza a kiszolgálót a magasabb szintű DNS-nevekkel való kommunikációra. a [Azure file Sync proxy és a tűzfal beállításai](../articles/storage/files/storage-sync-files-firewall-and-proxy.md) című cikkben további dokumentáció és részletek érhetők el. Kövesse a saját hálózatkezeléssel kapcsolatos ajánlott eljárásait.

A kiszolgáló *telepítése* varázsló végén megjelenik egy kiszolgáló *regisztrálása* varázsló.
Regisztrálja a kiszolgálót a Storage Sync szolgáltatás Azure-erőforrásához korábban.

Ezeket a lépéseket a telepítési útmutató részletesen ismerteti, beleértve a fenti PowerShell-modulokat, amelyeket először telepítenie kell: [Azure file Sync ügynök telepítése](../articles/storage/files/storage-sync-files-deployment-guide.md).

A legújabb ügynököt kell használni, és a Microsoft letöltőközpontból töltheti le: [Azure file Sync-Agent](https://aka.ms/AFS/agent "Azure File Sync ügynök letöltése").

A sikeres telepítés és a kiszolgáló regisztrálása után megtekintheti, hogy sikeresen elvégezte ezt a lépést: lépjen a Storage Sync Service-erőforrásra a Azure Portal, majd kövesse a bal oldali menüt a "regisztrált kiszolgálók" elemre. Itt láthatja, hogy a kiszolgáló azonnal megjelenik.
