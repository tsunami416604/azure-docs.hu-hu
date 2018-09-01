---
title: Text Analytics áttekintése – Azure Cognitive Services |} A Microsoft Docs
description: A vélemények elemzése, kulcsszókeresés, nyelvfelismerés és entitáskapcsolás az Azure Cognitive Services Text Analytics.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: article
ms.date: 8/30/2018
ms.author: ashmaka
ms.openlocfilehash: 71af2bcbf58279681bbea66d4f76e951a2efce59
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43341486"
---
# <a name="what-is-text-analytics"></a>Mi az Text Analytics?

A Text Analytics szolgáltatást biztosít a fejlett természetes nyelvi feldolgozási nyers, strukturálatlan szöveghez. Négy fő funkciókat tartalmazza: vélemények elemzése, kulcsszókeresés, nyelvfelismerés és entitáskapcsolás.

## <a name="analyze-sentiment"></a>Vélemények elemzése

[Ismerje meg,](how-tos/text-analytics-how-to-sentiment-analysis.md) alkotott ügyfélvéleményeket a márkájáról vagy a témakör a keresőmotorok pozitív vagy negatív véleményeket kapcsolatos nyers szöveg elemzésével. Az API-t adja vissza egy véleménypontszámot között 0 és 1, egyes dokumentumok, ahol 1 a legtöbb pozitív.<br />
Az elemzési modellek is imagenet-szöveg és a természetes nyelvi technológiát a Microsoft széles körű törzse használatával. A [kiválasztott nyelvek](text-analytics-supported-languages.md), az API-t elemezheti és pontszámot rendelni az Ön által megadott nyers szöveg.

## <a name="extract-key-phrases"></a>Kulcsszavak kinyerése

Automatikusan [kinyerheti a kulcskifejezéseket](how-tos/text-analytics-how-to-keyword-extraction.md) gyorsan azonosíthatja a fő pontokat. A bemeneti szöveg például megadott, "az élelmiszer delicious volt, és izgalommal személyzet volt", a Text Analytics szolgáltatást a fő beszédtémákat adja vissza: "food" és "izgalommal személyzetet".

## <a name="detect-language"></a>Nyelv felismerése

Legfeljebb 120 nyelvet [észlelése](how-tos/text-analytics-how-to-language-detection.md) mely nyelvi a bemeneti szöveg nyelven van megírva és a jelentés minden egyes dokumentum, a kérelem küldése egyetlen nyelvkód. A nyelvi kód párban áll a pontszám erőssége jelző pontszámot.

## <a name="identify-linked-entities-preview"></a>Azonosítsa a kapcsolt entitások (előzetes verzió)

[Azonosítsa](how-tos/text-analytics-how-to-entity-linking.md) jól ismert entitásokat a szöveg és a weben bővebb információira mutató hivatkozásokat. Entitáskapcsolás felismeri és disambiguates, amikor egy kifejezés egyik jelent, műveletek, és egyéb szó.

## <a name="typical-workflow"></a>Jellemző munkafolyamat

A munkafolyamat egyszerű: az elemzés és leíró kimenetek adatokat küld a kódban. Elemző felhasznált-van, további konfigurációs vagy testreszabása nélkül.

1. [Regisztráció](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) számára egy [hívóbetű](how-tos/text-analytics-how-to-access-key.md). A kulcsot kéréseknek kell átadni.

2. [Hozzon létre egy kérést](how-tos/text-analytics-how-to-call-api.md#json-schema) a JSON-ban, amely a nyers, strukturálatlan szöveges adatokat tartalmaz.

3. A regisztráció során létrehozott végpontra irányuló kérelem küldése, Hozzáfűzés, az API-t szeretné meghívni: vélemények elemzése, kulcsszókeresés, nyelvfelismerés vagy entitás azonosítója.

4. Stream, vagy a válasz a helyben tárolja. Attól függően, a kérés eredménye egy véleménypontszámot, kinyert kulcskifejezésekből álló egy gyűjteményt, vagy egy nyelvi kódot.

Kimeneti adja vissza, egy JSON-dokumentumok, az egyes szöveges dokumentum tesznek közzé, hogy az eredmények alapján azonosítója. Majd elemzése, megjelenítése vagy döntéstámogató elemzésekké az eredményeket kategorizálását.

A Text Analytics szolgáltatás által végzett műveletekre állapot nélküli. A fiókban lévő adatok nem tárolja.

<a name="data-limits"></a>

## <a name="specifications"></a>Specifikációk

### <a name="supported-languages"></a>Támogatott nyelvek

Lásd: [támogatott nyelvek Szövegelemzés](text-analytics-supported-languages.md).

### <a name="data-limits"></a>A Data korlátai

A Text Analytics szolgáltatás végpontok fogadja el a nyers szöveg adatokat. A jelenlegi maximum az egyes dokumentumok; 5000 karakternél Ha nagyobb dokumentumok elemzése van szüksége, bonthatja őket szeletekre. Ha továbbra is szüksége van egy magasabb korlátot, [lépjen kapcsolatba velünk](https://azure.microsoft.com/overview/sales-number/) úgy, hogy a követelményeket is megbeszélhetünk.

| Korlát | Érték |
|------------------------|---------------|
| Egyetlen dokumentum maximális mérete | 5000 karakternél által mért `String.Length`. |
| A kérelem teljes maximális mérete | 1 MB |
| A kérelem dokumentumok maximális száma | 1000 dokumentum |

A sávszélesség-korlátjának 100 hívás percenként. Vegye figyelembe, elküldheti a nagy mennyiségű dokumentumok száma (legfeljebb 1000 dokumentumot).

### <a name="unicode-encoding"></a>Unicode kódolással

A Text Analytics szolgáltatás használ, szöveges és a karakterek száma számítások kódolását Unicode. UTF-8 vagy UTF-16, a karakterek száma nincs mérhető különbségek kéréseket küldhet. Ha `String.Length` beolvasni a karakterek számát, adatok méretének mérésére használatával ugyanezt a módszert használ.

## <a name="next-steps"></a>További lépések

Először próbálja meg a [interaktív bemutatót](https://azure.microsoft.com/services/cognitive-services/text-analytics/). Illessze be a szövegbevitelt (5000 karakter maximális) észlelje a nyelvet (legfeljebb 120), kiszámítása egy véleménypontszámot, kinyerheti a kulcsfontosságú kifejezéseket, vagy kapcsolt entitások azonosítása. Nincs előfizetés nem szükséges.

Amikor készen áll a Text Analytics szolgáltatás hívásához közvetlenül:

+ [Regisztráció](how-tos/text-analytics-how-to-signup.md) egy hozzáférési kulcsra, és tekintse át a szükséges lépések [az API meghívása](how-tos/text-analytics-how-to-call-api.md).

+ [A rövid útmutató](quickstarts/csharp.md) , a REST API-t bemutató meghívja a C# nyelven írt. Ismerje meg, hogyan küldje el a szöveget, válassza ki az elemzés, és csak minimális kódírásra eredmények megtekintése.

+ [API-referenciadokumentáció](//go.microsoft.com/fwlink/?LinkID=759346) a műszaki dokumentáció a REST API-t biztosít. A dokumentáció támogatja a beágyazott hívások meg lehet hívni az API-t az egyes dokumentációs oldalon.

+ [Külső & közösségi tartalom](text-analytics-resource-external-community.md) olvashat a blogbejegyzésekben és a Szövegelemzés használata más eszközöket és technológiákat bemutató videók listája.

## <a name="see-also"></a>Lásd még

 [Cognitive Services – dokumentáció lap](https://docs.microsoft.com/azure/cognitive-services/)
