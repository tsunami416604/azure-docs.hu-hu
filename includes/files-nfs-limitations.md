---
title: fájlbefoglalás
description: fájlbefoglalás
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7e009516721473554517d1677bdef7a7451e4007
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90564869"
---
Az előzetes verzióban az NFS a következő korlátozásokkal rendelkezik:

- Az NFS 4,1 jelenleg csak a [protokoll specifikációjának](https://tools.ietf.org/html/rfc5661)kötelező funkcióit támogatja. Nem használhatók olyan választható funkciók, mint a delegálások és az összes típusú visszahívás, a zárolási frissítések és a visszalépések, valamint a Kerberos-hitelesítés és a titkosítás.
- Ha a kérések többsége metaadat-központú, akkor a késés a nyitott/záró műveletekhez képest rosszabb lesz.
- Létre kell hoznia egy új Storage-fiókot az NFS-megosztás létrehozásához.
- Csak a felügyeleti sík REST API-jai támogatottak. Az adatsíkok REST API-jai nem érhetők el, ami azt jelenti, hogy az olyan eszközök, mint a Storage Explorer, nem működnek együtt az NFS-megosztásokkal, és az NFS-megosztási adatok is megtekinthetők a Azure Portalban.
- Csak a prémium szint esetén érhető el.
- Jelenleg csak a helyileg redundáns tárolással érhető el.

### <a name="azure-storage-features-not-yet-supported"></a>Az Azure Storage szolgáltatásai még nem támogatottak

Emellett a következő Azure Files szolgáltatások nem érhetők el NFS-megosztásokkal:

- Identitás-alapú hitelesítés
- Azure Backup támogatás
- Pillanatképek
- Helyreállítható törlés
- Teljes körű titkosítás – továbbítási támogatás (részletek: [NFS-biztonság](../articles/storage/files/storage-files-compare-protocols.md#security))
- Azure File Sync (csak Windows-ügyfelek számára érhető el, amelyek esetében az NFS 4,1 nem támogatott)