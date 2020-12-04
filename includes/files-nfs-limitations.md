---
title: fájlbefoglalás
description: fájlbefoglalás
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/02/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: db4bc51b937535b7b53273bf01057d9b6c5198b3
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96584519"
---
Az előzetes verzióban az NFS a következő korlátozásokkal rendelkezik:

- Az NFS 4,1 jelenleg csak a [protokoll specifikációjának](https://tools.ietf.org/html/rfc5661)kötelező funkcióit támogatja. Nem használhatók olyan választható funkciók, mint a delegálások és az összes típusú visszahívás, a zárolási frissítések és a visszalépések, valamint a Kerberos-hitelesítés és a titkosítás.
- Ha a kérések többsége metaadat-központú, akkor a késés az olvasási/írási/frissítési műveletekhez képest rosszabb lesz.
- Létre kell hoznia egy új Storage-fiókot az NFS-megosztás létrehozásához.
- Csak a felügyeleti sík REST API-jai támogatottak. Az adatsíkok REST API-jai nem érhetők el, ami azt jelenti, hogy az olyan eszközök, mint a Storage Explorer nem működnek az NFS-megosztásokkal, és nem fogja tudni megkeresni az NFS-megosztási adatok Azure Portal.
- A AzCopy jelenleg nem támogatott.
- Csak a prémium szint esetén érhető el.
- Jelenleg csak a helyileg redundáns tárolással (LRS) érhető el.

### <a name="azure-storage-features-not-yet-supported"></a>Az Azure Storage szolgáltatásai még nem támogatottak

Emellett a következő Azure Files szolgáltatások nem érhetők el NFS-megosztásokkal:

- Identitás-alapú hitelesítés
- Azure Backup támogatás
- Pillanatképek
- Helyreállítható törlés
- Teljes körű titkosítás – továbbítási támogatás (részletek: [NFS-biztonság](../articles/storage/files/storage-files-compare-protocols.md#security))
- Azure File Sync (csak Windows-ügyfelek számára érhető el, amelyek esetében az NFS 4,1 nem támogatott)
