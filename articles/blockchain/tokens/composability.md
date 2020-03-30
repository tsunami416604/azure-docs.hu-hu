---
title: Az Azure Blockchain tokenek kompozonhatósága
description: Az Azure Blockchain tokenek komponálhatósága rugalmasságot biztosít a speciális forgatókönyvekhez való jogkivonatok létrehozásához.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: a3fe1b290917de20b7c3af31fe386ed93580d850
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325122"
---
# <a name="azure-blockchain-tokens-composability"></a>Az Azure Blockchain tokenek kompozonhatósága

[!INCLUDE [Preview note](./includes/preview.md)]

A tokenek kompobilitása rugalmasságot biztosít a speciális forgatókönyvekhez való jogkivonatok létrehozásához. Előfordulhat, hogy egy összetett forgatókönyv, amely nem valósítható meg a [négy előre elkészített jogkivonat-sablonok.](templates.md#base-token-types) A tokenek kompobilitása lehetővé teszi, hogy saját jogkivonat-sablonokat tervezzen meghatározott viselkedések hozzáadásával vagy eltávolításával a saját jogkivonatsablon létrehozásához. Új jogkivonatsablon létrehozásakor az Azure Blockchain tokenek ellenőrzi az összes token nyelvtani szabályokat. A komponált sablonokat az Azure Blockchain Tokens szolgáltatás menti a csatlakoztatott blokklánc-hálózatokon történő kibocsátáshoz.

A [jogkivonat-viselkedések](templates.md#token-behaviors) a következő szakaszokban a jogkivonat-sablon megtervezéséhez használhatja.

## <a name="burnable-b"></a>Égethető (b)

Képes eltávolítani a tokeneket a kínálatból.

Ha például online hitelkártyapontokat vált be ajándékkártyára, a hitelkártyapontok elégnek.

## <a name="delegable-g"></a>Delegelható (g)

A saját jogkivonaton végrehajtott műveletek delegálása.

A delegált műveleteket hajthat végre a jogkivonat tulajdonosaként. Például egy delegálható tokent használhat a szavazás végrehajtásához. A delegable token lehetővé teszi, hogy a szavazás token tulajdonosa, hogy valaki más szavazni a nevükben.

## <a name="logable-l"></a>Naplózható (l)

Képes naplózni.

Például kiadhat egy naplózható tokent egy filmterjesztéshez minden olyan színházban, amely egy adott filmet jelenít meg. Ahhoz, hogy a film lejátszható legyen, a bemutatónak minden egyes megjelenítéshez be kell jelentkeznie, mivel a jogdíjkifizetések a film kiadási futtatása során jelennek meg. A színészek építeni használhatja a film tokenek érvényesítésére kifizetések egy film mutatja egy színház a forgalmazás.

## <a name="mint-able-m"></a>Menta képes (m)

További jogkivonatok mentézése a tokenosztályhoz. A minta szerep tartalmazza a mentable viselkedést.

Például egy kiskereskedelmi vállalat, amely egy hűségprogramot kíván megvalósítani, mentálható tokeneket használhat a hűségprogramhoz. Az ügyfélkör növekedésével további hűségpontokat is verhetnek ügyfeleik számára.  

## <a name="non-subdividable-or-whole-d"></a>Nem osztható vagy egész (~d)

Korlátozás, amely megakadályozza, hogy egy token kisebb részekre oszlik.

Egy műfestmény például nem osztható fel több kisebb részre. 

## <a name="non-transferable-t"></a>Nem átruházható (~t)

Korlátozás a kezdeti jogkivonat tulajdonosának tulajdonosváltásának megakadályozására.

Például az egyetemi oklevél nem átruházható token. Ha egy oklevelet egy diplomásnak adnak, az nem ruházható át a diplomáról egy másik személyre.

## <a name="roles-r"></a>Szerepek (r)

Adott viselkedések szerepkörök definiálása a jogkivonatsablon-osztályon belül.

Megadhat egy listát a szerepkörnevek, amelyek a jogkivonat támogatja a jogkivonat létrehozása kor. Ha a szerepkörök meg vannak adva, a felhasználó szerepköröket rendelhet ezekhez a viselkedésekhez. Jelenleg csak a mintaszereper szerepkör támogatott.

## <a name="singleton-s"></a>Singleton (k)

Korlátozás egy token biztosításához.

Például egy múzeumi műtárgy egy singleton token. A múzeumi leletek egyedülállóak. Egy műterméket képviselő jogkivonat csak egy cikket rendelkezik a készletben.

## <a name="subdividable-d"></a>Felosztható (d)

A tokenek kisebb részekre osztásának képessége.

Például egy dollár centekre osztható.

## <a name="transferable-t"></a>Átruházható (t)

A jogkivonat tulajdonjogának átruházásának képessége.

Például egy ingatlancím átruházható token, amely az ingatlan értékesítésekor átruházható egyik személyről a másikra.

## <a name="next-steps"></a>További lépések

További információ az [Azure Blockchain Tokens fiókkezeléséről.](account-management.md)
