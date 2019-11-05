---
title: Az Azure Blockchain tokenek megkomponálása
description: Az Azure Blockchain-tokenek megkomponálása rugalmasságot biztosít a speciális forgatókönyvekhez tartozó jogkivonatok létrehozásához.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
ms.openlocfilehash: fe932d3ae1874e7a35abb9729a0b80e4fa3512eb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512300"
---
# <a name="azure-blockchain-tokens-composability"></a>Az Azure Blockchain tokenek megkomponálása

[!INCLUDE [Preview note](./includes/preview.md)]

A jogkivonat-megkomponálás rugalmasságot biztosít a speciális forgatókönyvek jogkivonatok létrehozásához. Előfordulhat, hogy olyan összetett forgatókönyvvel rendelkezik, amely nem valósítható meg a [négy előre elkészített jogkivonat-sablon](templates.md#base-token-types)használatával. A jogkivonat-létrehozás lehetővé teszi saját jogkivonat-sablonok megtervezését a definiált viselkedésmódok hozzáadásával vagy eltávolításával saját jogkivonat-sablon létrehozásához. Új jogkivonat-sablon létrehozásakor az Azure Blockchain-tokenek ellenőrzi az összes jogkivonat nyelvtani szabályát. A komponált sablonok az Azure Blockchain tokens szolgáltatásban lesznek mentve a csatlakoztatott Blockchain-hálózatokon való közzétételhez.

A token-sablon kialakításához a következő részben szereplő [jogkivonat-viselkedéseket](templates.md#token-behaviors) használhatja.

## <a name="burnable-b"></a>Írható (b)

Lehetőség a tokenek eltávolítására a szolgáltatásból.

Ha például beváltja az online hitelkártya-pontokat egy ajándék kártyára, a rendszer megégeti a hitelkártya-pontokat.

## <a name="delegable-g"></a>Delegálható (g)

Az Ön tulajdonában lévő jogkivonaton végrehajtott műveletek delegálásának lehetősége.

A delegált a jogkivonat tulajdonosaként hajthat végre műveleteket. Használhat például egy delegálható tokent egy szavazás megvalósításához. A delegálható-token lehetővé teszi, hogy a szavazati jogkivonat tulajdonosa valaki másnak szavazzon a nevében.

## <a name="logable-l"></a>Logable (l)

Naplózási lehetőség.

Kiállíthat például egy logable jogkivonatot a mozgóképek terjesztésére egy adott filmet bemutató egyes színházokban. A mozgókép lejátszásához a bemutatónak be kell jelentkeznie az egyes megjelenítésekhez, mivel a jogdíj-kifizetések megjelenítése a film kiadásának futtatásakor történik. A színészek Build a film jogkivonatait használva ellenőrizheti, hogy a kifizetések száma moziban, a disztribúcióban.

## <a name="mint-able-m"></a>Mentás-képes (m)

További tokenek megszerzésének lehetősége a jogkivonat osztályhoz. A Minter szerepkör tartalmazza a felmenthető viselkedést.

Egy olyan kiskereskedelmi vállalat például, amely egy hűségprogram megvalósítását kívánja végrehajtani, felhasználhatja a saját lojalitási programjának felmenthető jogkivonatait. További lojalitási pontokat szereznek az ügyfelek számára, amikor az Ügyfélkörük növekszik.  

## <a name="non-subdividable-or-whole-d"></a>Nem osztható vagy teljes (~ d)

Korlátozás annak megakadályozására, hogy a token kisebb részekre legyen osztva.

Például egyetlen művészi festés nem osztható több kisebb részre. 

## <a name="non-transferable-t"></a>Nem átvihető (~ t)

Korlátozás a kezdeti jogkivonat tulajdonosának tulajdonjogának megváltozásának megakadályozására.

Egy egyetemi diploma például nem átvihető jogkivonat. Ha diplomát kap egy diploma, nem vihető át a diplomából egy másik személynek.

## <a name="roles-r"></a>Szerepkörök (r)

Szerepkörök definiálása a jogkivonat-sablon osztályán belül adott viselkedések esetében.

Megadhatja azoknak a szerepkör-neveknek a listáját, amelyeket a jogkivonat a jogkivonat létrehozási idején támogat. Ha a szerepkörök meg vannak adva, a felhasználó szerepköröket rendelhet ezekhez a viselkedésekhez. Jelenleg csak a Minter szerepkör támogatott.

## <a name="singleton-s"></a>Egyszeres (s)

Egyetlen jogkivonat ellátását engedélyező korlátozás.

Egy múzeumi összetevő például egy egyedi jogkivonat. A múzeumi összetevők egyediek. Egy összetevőt jelképező jogkivonat csak egyetlen elemmel rendelkezik a kínálatban.

## <a name="subdividable-d"></a>Osztható (d)

Kisebb részekre oszthatók ki a tokenek.

Például a dollár felosztható százalékokra.

## <a name="transferable-t"></a>Átvihető (t)

Lehetőség a jogkivonat tulajdonjogának átadására.

Például egy tulajdonság címe egy átruházható token, amely az egyik személyről a másikra vihető át a tulajdonság eladásakor.

## <a name="next-steps"></a>További lépések

Ismerje meg az [Azure Blockchain-tokenek fiókjának kezelését](account-management.md).
