---
title: Mi a Text Analytics?
titleSuffix: Azure Cognitive Services
description: Az Azure Cognitive Services-beli Text Analytics a szövegek hangulatelemzéséhez, a kulcskifejezések kinyeréséhez, nyelvfelismeréshez és entitások összekapcsolásához használható.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: overview
ms.date: 10/01/2018
ms.author: ashmaka
ms.openlocfilehash: fcc10723b2fb750b099f99999de01ab699d5d5e8
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2018
ms.locfileid: "48887768"
---
# <a name="what-is-text-analytics"></a>Mi a Text Analytics?

A Text Analytics API egy felhőalapú eszköz, amely nyers szövegek fejlett természetes nyelvfeldogozását biztosítja, és négy fő funkciója van: a hangulatelemzés, a kulcsszókeresés, a nyelvfelismerés és az entitáskapcsolás.

Az API a [Microsoft Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/) erőforrásait használja, amely egy felhőalapú gépi tanulási és mesterséges intelligenciára épülő algoritmusokat biztosító, a fejlesztési projektekben szabadon használható gyűjtemény.

## <a name="capabilities-in-text-analytics"></a>A Text Analytics funkciói

A szövegelemzés különböző dolgokat jelenthet. A Cognitive Servicesben a Text Analytics API négy különböző típusú elemzést biztosít, amelyeket az alábbi táblázat ismertet.

| Műveletek| Leírás | API-k |
|-----------|-------------|------|
|[**Hangulatelemzés**](how-tos/text-analytics-how-to-sentiment-analysis.md) | Ismerje meg, mi az ügyfelek véleménye az Ön márkájáról vagy témaköréről, a nyers szövegek elemzésével pozitív vagy negatív véleményekre utaló jeleket keresve. Az API minden dokumentumhoz visszaad egy 0 és 1 közötti hangulati pontszámot, ahol az 1 a legpozitívabb pontszám.<br /> Az elemzési modellek előzetes betanítása a Microsoft széles körű szövegtörzs-technológiái és természetes nyelvi technológiái segítségével történik. [Bizonyos nyelvek](text-analytics-supported-languages.md) esetében az API képes a megadott szöveg elemzéséből kiszámított pontszámot közvetlenül visszaadni a hívó alkalmazásnak. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) <br /> [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package)  |
|[**Kulcskifejezések kinyerése**](how-tos/text-analytics-how-to-keyword-extraction.md) | Automatikusan kinyeri a kulcskifejezéseket, ezzel gyorsan azonosítva a szöveg mondanivalójának fő pontjait. Például „Az étel finom volt, és a személyzet kedves volt” bemeneti szövegből az API a következő fő pontokat adja vissza: „étel” és „személyzet kedves”.  | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) <br /> [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) |
|[**Nyelvfelismerés**](how-tos/text-analytics-how-to-language-detection.md) | Akár 120 nyelven képes felismerni, hogy a bemeneti szöveg milyen nyelven íródott, és a kérésben szereplő minden dokumentumhoz visszaad egyetlen nyelvkódot. A nyelvkód egy pontszámmal párba állítva jelzi a pontszám erősségét. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) <br />  [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) | 
|[**Entitásfelismerés (előzetes verzió)**](how-tos/text-analytics-how-to-entity-linking.md) | A szövegben szereplő entitások azonosítsa és besorolása mint személyek, helyek, szervezetek, dátum és idő, mennyiségek, százalékos arányok, pénznemek és egyebek. A rendszer felismeri a közismert entitásokat, amelyeket összekapcsol az interneten található további információkkal. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634) | 

## <a name="typical-workflow"></a>Jellemző munkafolyamat

A munkafolyamat egyszerű: benyújtjuk az adatokat elemzésre és a kódban kezeljük a kimeneteket. Az elemzők használatra készek, esetükben nincs szükség további konfigurációs beállításokra vagy testreszabásra.

1. [Regisztráljon](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) egy [hozzáférési kulcs](how-tos/text-analytics-how-to-access-key.md) beszerzéséhez. A kulcsot minden egyes kérés alkalmával meg kell adni.

2. [Állítson össze egy kérést](how-tos/text-analytics-how-to-call-api.md#json-schema), amely az adatokat nyers, strukturálatlan szövegként tartalmazza, JSON formátumban.

3. Küldje el a kérést a feliratkozáskor megállapított végpontra, és jelölje meg a használni kívánt erőforrást: hangulatelemzés, kulcsszókinyerés, nyelvfelismerés vagy entitásazonosítás.

4. A válasz streamelhető vagy helyileg is tárolható. A kéréstől függően az eredmény lehet egy véleménypontszám, kinyert kulcskifejezések gyűjteménye vagy egy nyelvkód.

A kimenetet a rendszer egyetlen JSON-dokumentumban adja vissza, amely tartalmazza az összes elküldött szöveges dokumentum eredményeit, azok azonosítói alapján. Az eredmények ezt követően elemezhetők, vizualizálhatók vagy kategorizálhatók a gyakorlatban használható megállapításokká.

Az adatok nem lesznek tárolva a fiókjában. A Text Analytics API által végrehajtott műveletek állapot nélküliek, ami azt jelenti, hogy a szöveg feldolgozása és az eredmények visszaadása azonnal megtörténik.

<a name="supported-languages"></a>

## <a name="supported-languages"></a>Támogatott nyelvek

Ez a szakasz egy külön cikkbe lett áthelyezve a jobb átláthatóság érdekében. Lásd a [Text Analytics API-ban támogatott nyelveket ismertető](text-analytics-supported-languages.md) cikket.

<a name="data-limits"></a>

## <a name="data-limits"></a>Adatkorlátok

A Text Analytics API minden végpontja nyers szöveges adatokat fogad el. A jelenlegi korlát dokumentumonként 5000 karakter. Ha ennél hosszabb dokumentumok elemzésére van szüksége, felbonthatja őket kisebb darabokra. Ha mégis magasabb korlátra van szüksége, [lépjen velünk kapcsolatba](https://azure.microsoft.com/overview/sales-number/), hogy megbeszélhessük a követelményeit.

| Korlát | Érték |
|------------------------|---------------|
| Az egyes dokumentumok maximális mérete | 5000 karakter, `String.Length` szerint számolva. |
| Teljes kérés maximális mérete | 1 MB |
| Egy kérésbe foglalt dokumentumok maximális száma | 1000 dokumentum |

A hívások számának korlátja percenként 100 hívás. Vegye figyelembe, hogy egyetlen hívásba nagy mennyiségű (akár 1000) dokumentumot is belefoglalhat.

## <a name="unicode-encoding"></a>Unicode-kódolás

A Text Analytics API Unicode-kódolást használ a szövegek megjelenítéséhez és a karakterszámok számításához. A kérések elküldhetők UTF-8- és UTF-16-kódolással is, amelyek között nincs számottevő különbség a karakterek számában. A rendszer a Unicode-kódpontokat használja a karakterszám heurisztikus számításához. A két mennyiség a Text Analytics adatkorlátai szempontjából egyenértékű. Ha a `String.Length` használatával számítja a karakterszámot, akkor ugyanazt a módszert használja az adatok méréséhez, mint mi.

## <a name="next-steps"></a>További lépések

Először próbálja ki az [interaktív bemutatót](https://azure.microsoft.com/services/cognitive-services/text-analytics/). Beilleszthet egy bemeneti szöveget (legfeljebb 5000 karakter hosszúságig), amelynek felismerheti a nyelvét (120 nyelv közül), kiszámíthatja a véleménypontszámát, vagy kinyerheti a kulcskifejezéseit. Ehhez nem szükséges feliratkozni.

Ha kész közvetlenül meghívni az API-t:

+ [Regisztráljon](how-tos/text-analytics-how-to-signup.md) egy hozzáférési kulcs beszerzéséhez és tekintse át [az API meghívásának](how-tos/text-analytics-how-to-call-api.md) lépéseit.

+ A [rövid útmutató](quickstarts/csharp.md) a C# nyelven írt REST API-hívásokat mutatja be részletesen. Ismerje meg, hogyan küldhet el egy szöveget, választhat ki egy elemzést és tekintheti meg az eredményeket minimális kódolással.

+ Az [API referenciadokumentációja](//go.microsoft.com/fwlink/?LinkID=759346) tartalmazza az API-k műszaki dokumentációját. A dokumentáció támogatja a beágyazott hívásokat, így bármely dokumentációs oldalról meghívhatja az API-t.

+ A [Külső és közösségi tartalom](text-analytics-resource-external-community.md) oldalon találhat egy listát azokról a blogbejegyzésekről és videókról, amelyek bemutatják, hogyan használható a Text Analytics egyéb eszközökkel és technológiákkal.

## <a name="see-also"></a>Lásd még

 [A Cognitive Services dokumentációjának oldala](https://docs.microsoft.com/azure/cognitive-services/)
