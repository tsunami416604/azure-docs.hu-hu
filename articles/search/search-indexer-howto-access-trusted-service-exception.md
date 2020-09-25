---
title: Tárolóhoz való hozzáférés engedélyezése a megbízható szolgáltatásokkal kapcsolatos kivételek használatával
titleSuffix: Azure Cognitive Search
description: Útmutató, amely leírja, hogyan állíthat be megbízható szolgáltatási kivételt a Storage-fiókok adatainak biztonságos eléréséhez.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 30fc71e6f59766a759cdb8e4e503123623f48bd9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320472"
---
# <a name="accessing-data-in-storage-accounts-securely-via-trusted-service-exception"></a>A Storage-fiókokban tárolt adatok biztonságos elérése a megbízható szolgáltatásokkal kapcsolatos kivételek használatával

A Storage-fiókokban tárolt adathozzáférésű indexelő használhatják a [megbízható szolgáltatás kivételi](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) funkcióját az adathozzáférés biztonságos eléréséhez. Ez a mechanizmus olyan ügyfelek számára biztosít egyszerű, biztonságos és ingyenes alternatívát, akik nem tudnak [hozzáférni az indexelő számára az IP-tűzfalszabályok](search-indexer-howto-access-ip-restricted.md) használatával.

> [!NOTE]
> A Storage-fiókokban lévő adatok megbízható szolgáltatási kivételen keresztüli elérésének támogatása az Azure Blob Storage-ra és Azure Data Lake Gen2-tárolóra korlátozódik. Az Azure Table Storage nem támogatott.

## <a name="step-1-configure-connection-to-the-storage-account-via-identity"></a>1. lépés: a Storage-fiókhoz való kapcsolódás konfigurálása identitás használatával

Kövesse a [felügyelt identitás-hozzáférési útmutatóban](search-howto-managed-identities-storage.md) ismertetett részleteket, és konfigurálja az Indexelő szolgáltatást a keresési szolgáltatás felügyelt identitásán keresztül a Storage-fiókok eléréséhez.

## <a name="step-2-allow-trusted-microsoft-services-to-access-the-storage-account"></a>2. lépés: a megbízható Microsoft-szolgáltatások hozzáférésének engedélyezése a Storage-fiókhoz

A Azure Portal navigáljon a Storage-fiók "tűzfalak és virtuális hálózatok" lapjára. Győződjön meg arról, hogy a "megbízható Microsoft-szolgáltatások elérésének engedélyezése a Storage-fiókhoz" beállítás be van jelölve. Ez a beállítás csak az adott keresőszolgáltatás-példány megfelelő szerepköralapú hozzáférését teszi lehetővé a Storage-fiókhoz (erős hitelesítés) a Storage-fiókban lévő adateléréshez, még akkor is, ha az IP-tűzfalszabályok védelme alatt áll.

![Megbízható szolgáltatás kivétele](media\search-indexer-howto-secure-access\exception.png "Megbízható szolgáltatás kivétele")

Az indexelő mostantól hozzáférhetnek a Storage-fiókban lévő adataihoz, még akkor is, ha a fiók IP-tűzfalszabályok használatával védett.

## <a name="next-steps"></a>Következő lépések

További információ az Azure Storage indexelő szolgáltatásáról:

- [Azure Blob-indexelő](search-howto-indexing-azure-blob-storage.md)
- [Azure Data Lake Storage Gen2 indexelő](search-howto-index-azure-data-lake-storage.md)
- [Azure Table indexelő](search-howto-indexing-azure-tables.md)
