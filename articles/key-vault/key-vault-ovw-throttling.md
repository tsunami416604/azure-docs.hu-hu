---
ms.assetid: 
title: "Sávszélesség-szabályozás útmutató az Azure Key Vault |} Microsoft Docs"
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 06/21/2017
ms.openlocfilehash: fe700e22c5323c2a0bdc315e349cd119798bcf40
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="azure-key-vault-throttling-guidance"></a>Az Azure Key Vault útmutatást szabályozása

Sávszélesség-szabályozás elindít egy folyamatot, amely korlátozza az erőforrások túlhasználat megelőzése érdekében az Azure szolgáltatásban az egyidejű hívások száma. Az Azure Key Vault (AKV) nagy mennyiségű kérést kezelésére terveztek. Egy túlságosan kérelmek száma akkor fordul elő, ha az ügyfélkérelmek sávszélesség-szabályozás optimális teljesítménye és megbízhatósága a AKV szolgáltatás karbantartása.

A forgatókönyv függően változhat a sávszélesség-szabályozási korlátok. Például ha nagy mennyiségű írási műveleteket hajt végre, szabályozás lehetőség, magasabb, mint ha olvasási műveletek csak végzik.

## <a name="how-does-key-vault-handle-its-limits"></a>Hogyan nem kezelik a teljes kapacitásukkal működjenek a Key Vault?

A Key Vault szolgáltatásra vonatkozó korlátozások-e erőforrásokhoz való visszaélés és az összes Key Vault ügyfelek minőségének biztosítása érdekében. A szolgáltatás küszöbérték túllépésekor a Key Vault semmilyen további kérelmeit, hogy az ügyfél korlátozza egy ideig. Ha ez történik, a Key Vault HTTP-állapotkód 429 adja vissza (túl sok kérelem), és a kérelem sikertelen lesz. A Key Vault követik szabályozási korlátok felé 429-es jelű szerepleni kérelmek is, sikertelen. 

Ha a nagyobb mértékű késleltetési korlátozás érvényes üzleti eset, lépjen kapcsolatba velünk a következő címen.


## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Hogyan szolgáltatásra vonatkozó korlátozások válaszul Alkalmazáshasználat szabályozása

A következő **ajánlott eljárások** szabályozás az alkalmazást:
- Csökkentse a kérelmenként műveletek számát.
- Csökkentse a kérelmeket.
- Ne használjon közvetlen újrapróbálkozások. 
    - Összes kérelem keletkeznek a használati korlátozások.

Az alkalmazás hibakezelési bevezetésekor használja a HTTP-hibakódot 429 észleli a ügyféloldali szabályozás. Ha a kérelem végrehajtása ismét sikertelen hibakódú HTTP 429, továbbra is találkozik egy Azure-szolgáltatások korlátot. Továbbra is használhatják az ajánlott ügyféloldali szabályozás metódus, a kérelem ismételt próbálkozásra, amíg azt nem jár sikerrel.

### <a name="recommended-client-side-throttling-method"></a>Ajánlott az ügyféloldali szabályozási módszer

A HTTP-hibakódot 429 kezdje a sávszélesség-szabályozás exponenciális leállítási módszer használatát az ügyfél el:

1. Várjon 1 másodperc, ismételje meg a kérést
2. Ha továbbra is halmozódni várjon 2 másodperc, próbálkozzon újra a kéréssel
3. Ha továbbra is halmozódni várakozási 4 másodperc, próbálkozzon újra a kéréssel
4. Ha továbbra is halmozódni várakozási 8 másodperc, próbálkozzon újra a kéréssel
5. Ha továbbra is halmozódni várakozási 16 másodperc, próbálkozzon újra a kéréssel

Ekkor meg kell jut HTTP 429 válaszkódot.

## <a name="see-also"></a>Lásd még:

A sávszélesség-szabályozás a Microsoft Cloud a mélyebb tájolását, lásd: [sávszélesség-szabályozás mintát](https://docs.microsoft.com/azure/architecture/patterns/throttling).

