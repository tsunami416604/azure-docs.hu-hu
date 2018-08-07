---
title: Az Azure storage table adatok titkosítása |} A Microsoft Docs
description: Ismerje meg a tábla adatok titkosítását az Azure storage-ban.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/11/2018
ms.author: sngun
ms.component: tables
ms.openlocfilehash: 8a7b61705f6efc5a76212fdd1345a022f58e0686
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526466"
---
# <a name="encrypt-table-data"></a>Táblák adatainak titkosítása
Az Azure Storage .NET ügyféloldali kódtár támogatja a titkosítást a karakterlánc-entitás tulajdonságai szúrhatók be, és cserélje le a műveleteket. A titkosított karakterláncokat tárolja a szolgáltatás bináris tulajdonságokként, és azok alakítja vissza karakterláncok a visszafejtés után.    

Felhasználók táblákat, a titkosítási szabályzat mellett titkosítását a tulajdonságokat kell megadnia. Ezt megteheti megadásával vagy (POCO entitások, amelyek TableEntity származtatva) [EncryptProperty] attribútum vagy egy titkosítási feloldási lehetőségek. Egy titkosítási feloldó egy meghatalmazott, amelyek egy partíciókulcsot, egy sorkulcsot és egy tulajdonságnév vesz igénybe, és logikai érték beolvasása, amely azt jelzi, hogy tulajdonság titkosítani kell. Titkosítás során az ügyféloldali kódtár ezen információk segítségével titkosítása egy tulajdonság írása az átvitel közben kell-e. A delegált körül hogyan tulajdonságok vannak titkosítva logikai lehetőségét is biztosít. (Például, ha X, majd titkosítása egy tulajdonság; ellenkező esetben a tulajdonságok a és b titkosítása) Nem kell ezt az információt entitások lekérdezése vagy olvasása közben.

## <a name="merge-support"></a>Támogatási egyesítése

Egyesítési jelenleg nem támogatott. A tulajdonságok egy részének előfordulhat, hogy korábban használatával titkosított egy másik kulcsot, mert csak az új tulajdonságok egyesítése, és a metaadatok frissítése eredményez az adatvesztést. Az egyesítés vagy igényel, a már meglévő entitás olvasni a szolgáltatás további szolgáltatás-hívások, illetve tulajdonságonként egy új kulcsot használ, mindkettő nem alkalmasak a teljesítmény javítása érdekében.     

További információ a táblák adatainak titkosítása: [ügyféloldali titkosítás és a Microsoft Azure Storage for Azure Key Vault](../common/storage-client-side-encryption.md).  

## <a name="next-steps"></a>További lépések

- [Táblázat kialakítási minták](table-storage-design-patterns.md)
- [Kapcsolatok modellezését](table-storage-design-modeling.md)
- [Kapcsolatok modellezését](table-storage-design-modeling.md)
- [Adatmódosítás tervezése](table-storage-design-for-modification.md)