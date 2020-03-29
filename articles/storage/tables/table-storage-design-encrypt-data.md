---
title: Azure storage-táblaadatok titkosítása | Microsoft dokumentumok
description: Ismerje meg a táblaadatok titkosítását az Azure storage-ban.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/11/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: f56946702011968a0fcb31f6fbecbaacdc89ea42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60326003"
---
# <a name="encrypt-table-data"></a>Táblaadatok titkosítása
A .NET Azure Storage ügyfélkódtár támogatja a karakterláncentitás-tulajdonságok titkosítását a beszúrási és csereműveletekhez. A titkosított karakterláncok bináris tulajdonságokként tárolódnak a szolgáltatásban, és a visszafejtés után karakterláncokká alakulnak vissza.    

Táblák esetén a titkosítási házirendmellett a felhasználóknak meg kell adniuk a titkosítandó tulajdonságokat. Ez történhet egy [EncryptProperty] attribútum megadásával (a TableEntity-ból származó POCO-entitásokhoz), vagy egy titkosítás-feloldó megadásával a kérelembeállításokban. A titkosítási feloldó olyan delegált, amely partíciókulcsot, sorkulcsot és tulajdonságnevet ad vissza, és logikai értéket ad vissza, amely jelzi, hogy a tulajdonságot titkosítani kell-e. A titkosítás során az ügyféltár ezt az információt használja annak eldöntésére, hogy titkosítson-e egy tulajdonságot a vezetékbe írás közben. A delegált is lehetővé teszi a logika körül, hogyan tulajdonságok titkosítását. (Ha például X, akkor titkosítsa az A tulajdonságot; egyébként titkosítsa az A és B tulajdonságokat.) Ezeket az adatokat nem szükséges megadni entitások olvasása vagy lekérdezése közben.

## <a name="merge-support"></a>Támogatás egyesítése

Az egyesítés jelenleg nem támogatott. Mivel előfordulhat, hogy a tulajdonságok egy részhalmaza korábban egy másik kulccsal lett titkosítva, az új tulajdonságok egyesítése és a metaadatok frissítése adatvesztést eredményez. Az egyesítéshez vagy további szolgáltatáshívásokat kell kezdeményezni a már meglévő entitás szolgáltatásból való olvasásához, vagy egy új kulcsot használ tulajdononként, amelyek teljesítménybeli okokból nem alkalmasak.     

A táblaadatok titkosításáról az [Ügyféloldali titkosítás és az Azure Key Vault for Microsoft Azure Storage című témakörben talál](../common/storage-client-side-encryption.md)további információt.  

## <a name="next-steps"></a>További lépések

- [Táblázat tervezési mintái](table-storage-design-patterns.md)
- [Kapcsolatok modellezése](table-storage-design-modeling.md)
- [Kapcsolatok modellezése](table-storage-design-modeling.md)
- [Tervezés adatmódosításhoz](table-storage-design-for-modification.md)
