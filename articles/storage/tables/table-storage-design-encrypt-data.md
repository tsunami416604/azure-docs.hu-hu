---
title: Az Azure storage tábla adatok titkosítása |} Microsoft Docs
description: További információk a tábla az adattitkosítást az Azure-tárfiókba.
services: storage
documentationcenter: na
author: MarkMcGeeAtAquent
manager: kfile
ms.assetid: 8e228b0c-2998-4462-8101-9f16517393ca
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/11/2018
ms.author: sngun
ms.openlocfilehash: 082e8a54cc8625a4bbdea2157f73874dbc86fde2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660895"
---
# <a name="encrypt-table-data"></a>Tábla adatok titkosítása
A .NET Azure Storage ügyféloldali kódtár támogatja a titkosítást a karakterlánc az Entitástulajdonságok szúrhatók be, és cserélje le a műveletek. A titkosított karakterláncok tárolja a szolgáltatás bináris tulajdonságként, és telepítésekké lesznek átalakítva vissza karakterláncok a visszafejtés után.    

Táblák, a titkosítási házirenden kívül a felhasználók fiók kell adnia a titkosítani kell. Ezt megteheti megadásával vagy (az POCO entitások, amelyek a TableEntity) [EncryptProperty] attribútum vagy egy titkosítási feloldó lehetőségek. Egy titkosítási feloldó egy delegált veszi a partíciós kulcs, sorkulcsot és tulajdonság nevét, és logikai érték beolvasása, amely jelzi, hogy a tulajdonság titkosítani kell-e. Titkosítás során az ügyféloldali kódtár ezen információk alapján döntse el, hogy egy tulajdonság titkosítani az átvitel közbeni írása közben. A delegált is körül hogyan tulajdonságok vannak titkosítva logika lehetőségét biztosítja. (Például, ha X, majd titkosítása A tulajdonság; ellenkező esetben a Tulajdonságok A és b titkosítása) Nincs szükség arra, hogy ezek az információk olvasása vagy entitás lekérdezése közben.

## <a name="merge-support"></a>Támogatási egyesítése

Egyesítési jelenleg nem támogatott. A Tulajdonságok részhalmazát, előfordulhat, hogy korábban használatával titkosított egy másik kulcsot, mert egyszerűen az új tulajdonságok egyesítése és a metaadatok frissítése eredményezi adatvesztés. Az egyesítés vagy megköveteli a már meglévő entitás olvasni a szolgáltatás további szolgáltatás-hívások, vagy tulajdonságonként egy új kulcsot használ, amelyek mindegyikét nem alkalmasak a teljesítményre vonatkozó megfontolásból.     

További információ a táblabeli adatok titkosítása: [ügyféloldali titkosítás és a Microsoft Azure tárolás az Azure Key Vault](../common/storage-client-side-encryption.md).  

## <a name="next-steps"></a>További lépések

- [Táblázat kialakítási minták](table-storage-design-patterns.md)
- [Modellezési kapcsolatok](table-storage-design-modeling.md)
- [Modellezési kapcsolatok](table-storage-design-modeling.md)
- [Adatmódosítás kialakítása](table-storage-design-for-modification.md)