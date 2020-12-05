---
title: fájlbefoglalás
description: fájlbefoglalás
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/04/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 372342611265640a2a64100f003880a430d61ca0
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620943"
---
Az előzetes verzióban az NFS a következő korlátozásokkal rendelkezik:

- Az NFS 4,1 jelenleg csak a [protokollok specifikációjának](https://tools.ietf.org/html/rfc5661)legtöbb funkcióját támogatja. Bizonyos funkciók, például a delegálások és a mindenfajta visszahívás, a zárolási frissítések és a visszalépések, a Kerberos-hitelesítés és a titkosítás nem támogatottak.
- Ha a kérések többsége metaadat-központú, akkor a késés az olvasási/írási/frissítési műveletekhez képest rosszabb lesz.
- Létre kell hoznia egy új Storage-fiókot az NFS-megosztás létrehozásához.
- Csak a felügyeleti sík REST API-jai támogatottak. Az adatsíkok REST API-jai nem érhetők el, ami azt jelenti, hogy az olyan eszközök, mint a Storage Explorer nem működnek az NFS-megosztásokkal, és nem fogja tudni megkeresni az NFS-megosztási adatok Azure Portal.
- A AzCopy jelenleg nem támogatott.
- Csak a prémium szint esetén érhető el.
- Az NFS-megosztások csak numerikus UID/GID értékeket fogadnak el. Ha el szeretné kerülni, hogy az ügyfelek alfanumerikus UID/GID-t küldjenek, tiltsa le az azonosító-hozzárendelést.
- A megosztások csak egyetlen Storage-fiókból csatlakoztathatók egy adott virtuális gépen, ha privát hivatkozásokat használnak. A megosztások más Storage-fiókokból való csatlakoztatására tett kísérlet sikertelen lesz.

### <a name="azure-storage-features-not-yet-supported"></a>Az Azure Storage szolgáltatásai még nem támogatottak

Emellett a következő Azure Files szolgáltatások nem érhetők el NFS-megosztásokkal:

- Identitás-alapú hitelesítés
- Azure Backup támogatás
- Pillanatképek
- Helyreállítható törlés
- Teljes körű titkosítás – továbbítási támogatás (részletek: [NFS-biztonság](../articles/storage/files/storage-files-compare-protocols.md#security))
- Azure File Sync (csak Windows-ügyfelek számára érhető el, amelyek esetében az NFS 4,1 nem támogatott)
