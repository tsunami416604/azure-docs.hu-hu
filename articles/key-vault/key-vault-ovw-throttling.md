---
title: Az Azure Key Vault szabályozási útmutatója
description: Key Vault-szabályozás korlátozza az erőforrások túlhasználat megelőzése érdekében az egyidejű hívások száma.
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: ''
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: article
ms.date: 05/10/2018
ms.author: alleonar
ms.openlocfilehash: 59968f2bccbe2828ebe5fb33c57ed28d4f8509b6
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
ms.locfileid: "34067690"
---
# <a name="azure-key-vault-throttling-guidance"></a>Az Azure Key Vault szabályozási útmutatója

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

## <a name="see-also"></a>Lásd még

A sávszélesség-szabályozás a Microsoft Cloud a mélyebb tájolását, lásd: [sávszélesség-szabályozás mintát](https://docs.microsoft.com/azure/architecture/patterns/throttling).

