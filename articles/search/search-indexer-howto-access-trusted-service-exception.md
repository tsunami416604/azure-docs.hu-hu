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
ms.openlocfilehash: 1400f3c3d15698a5f1a145e8e0750ad7c4e9cec8
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90971404"
---
# <a name="accessing-data-in-storage-accounts-securely-via-trusted-service-exception"></a>A Storage-fiókokban tárolt adatok biztonságos elérése a megbízható szolgáltatásokkal kapcsolatos kivételek használatával

A Storage-fiókokban tárolt adathozzáférésű indexelő használhatják a [megbízható szolgáltatás kivételi](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) funkcióját az adathozzáférés biztonságos eléréséhez. Ez a mechanizmus olyan ügyfelek számára biztosít egyszerű, biztonságos és ingyenes alternatívát, akik nem tudnak [hozzáférni az indexelő számára az IP-tűzfalszabályok](search-indexer-howto-access-ip-restricted.md) használatával.

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
