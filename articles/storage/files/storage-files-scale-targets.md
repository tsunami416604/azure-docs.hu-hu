---
title: "Az Azure fájlok méretezhetőségi és Teljesítménycélok |} Microsoft Docs"
description: "További tudnivalók a méretezhetőségi és Teljesítménycélok Azure-fájlok, beleértve a kapacitás, a lekérdezési gyakorisága és a bejövő és kimenő sávszélesség korlátja."
services: storage
documentationcenter: na
author: wmgries
manager: klaasl
editor: tamram
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 10/17/2017
ms.author: wgries
ms.openlocfilehash: 35d430b683224d1035cccdfb58b9db415d47ea3b
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2017
---
# <a name="azure-files-scalability-and-performance-targets"></a>Az Azure fájlok méretezhetőségi és Teljesítménycélok
[Az Azure Files](storage-files-introduction.md) teljes körűen felügyelt fájlmegosztást kínáló a felhőben, amelyek elérhetők az iparági szabványos SMB protokollon keresztül. Ez a cikk ismerteti a méretezhetőségi és Teljesítménycélok Azure fájlok és az Azure fájl Sync (előzetes verzió).

Az itt felsorolt méretezhetőségi és Teljesítménycélok csúcskategóriás célozza, de előfordulhat, hogy a központi telepítésben lévő többi változó nem érinti. Például egy fájl átviteli is korlátozhatja a rendelkezésre álló hálózati sávszélességet, nem csak az Azure Fileshoz szolgáltatást futtató kiszolgálók. Erősen ajánlott a használati mintáját, és határozza meg, hogy a méretezhetőség és teljesítmény Azure fájlok igényeinek tesztelése. Azt is elkötelezettek adott idő alatt a korlátok növelését. Adjon ne habozzon, küldjön visszajelzést, vagy a Megjegyzések alatt vagy a a [Azure fájlok UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files), mely korlátok kapcsolatos szeretne látni, növeli.

## <a name="azure-storage-account-scale-targets"></a>Az Azure storage-fiók méretezési célok
A szülő erőforrás egy Azure fájlmegosztás az Azure storage-fiók. A tárfiók a tárolókészletet, amely az adatok tárolásához több tárolószolgáltatásokra, köztük az Azure fájlok által használható az Azure-ban jelöli. Egyéb szolgáltatások tárolt adatok storage-fiókok Azure Blob Storage tárolóban, az Azure Queue storage és az Azure Table storage. A következő célokat alkalmazni az adattárolás a tárfiókban lévő összes tárolási szolgáltatások:

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> Tárhely fiók kihasználtságát a többitől tárolás az Azure fájlmegosztások a tárfiókban lévő hatással van. Például ha eléri a maximális tárfiókok kapacitásával az Azure Blob storage szolgáltatással, csak akkor hozhat létre új Azure fájlmegosztás fájlokat akkor is, ha az Azure fájlmegosztás maximális megosztási méreténél kisebbre.

## <a name="azure-files-scale-targets"></a>Az Azure fájlok méretezési célok
[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Az Azure fájlszinkronizálás méretezési célok
Azure-fájl szinkronizálással azt próbált korlátlan használatra tervezhet lehetőség szerint ez azonban nem mindig lehetséges. Az alábbi táblázat azt jelzi, a határokat a tesztelés során, és mely célok ténylegesen szigorú korlátozásokat:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="see-also"></a>Lásd még:
- [Az Azure-fájlok központi telepítésének tervezése](storage-files-planning.md)
- [Egy Azure fájlszinkronizálás központi telepítésének tervezése](storage-sync-files-planning.md)
- [Az egyéb tárolási szolgáltatások méretezhetőségi és Teljesítménycélok](../common/storage-scalability-targets.md)