---
title: Mit jelent a szövegelemzési API-t? -Képességek-
titleSuffix: Azure Cognitive Services
description: A vélemények elemzése, kulcsszókeresés, nyelvfelismerés és entitáskapcsolás az Azure Cognitive Services Text Analytics API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 03/01/2019
ms.author: aahi
ms.openlocfilehash: 1e6aea792666447141e504f45a3ff55cb70f6261
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57533238"
---
# <a name="what-is-text-analytics-api"></a>Mit jelent a szövegelemzési API-t?

A Text Analytics API egy felhőalapú eszköz, amely nyers szövegek fejlett természetes nyelvfeldogozását biztosítja, és négy fő funkciója van: a hangulatelemzés, a kulcsszókeresés, a nyelvfelismerés és az entitáskapcsolás.

Az API-t egy része [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/), gépi tanulási és AI-algoritmusokat a felhőalapú fejlesztési projektjeit gyűjteménye.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

Szövegelemzés különböző dolgok jelenti, de a Cognitive Services, a szövegelemzési API elemzési az alábbiakban leírtak négyféle biztosít.

## <a name="sentiment-analysis"></a>Véleményelemzés
Használat [hangulatelemzés](how-tos/text-analytics-how-to-sentiment-analysis.md) alkotott ügyfélvéleményeket a márkájáról vagy a témakör a keresőmotorok pozitív vagy negatív véleményeket kapcsolatos nyers szöveg elemzésével megállapítása. Az API minden dokumentumhoz visszaad egy 0 és 1 közötti hangulati pontszámot, ahol az 1 a legpozitívabb pontszám.<br /> Az elemzési modellek előzetes betanítása a Microsoft széles körű szövegtörzs-technológiái és természetes nyelvi technológiái segítségével történik. [Bizonyos nyelvek](text-analytics-supported-languages.md) esetében az API képes a megadott szöveg elemzéséből kiszámított pontszámot közvetlenül visszaadni a hívó alkalmazásnak. Használhatja a [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) API vagy a [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) SDK-t.

## <a name="key-phrase-extraction"></a>Kulcskifejezések kinyerése
Automatikusan [kinyerheti a kulcskifejezéseket](how-tos/text-analytics-how-to-keyword-extraction.md) gyorsan azonosíthatja a fő pontokat. Például „Az étel finom volt, és a személyzet kedves volt” bemeneti szövegből az API a következő fő pontokat adja vissza: „étel” és „személyzet kedves”. Használhatja a [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) itt az API-t vagy a [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) SDK-t.

## <a name="language-detection"></a>Nyelvfelismerés
Is [melyik nyelvfelismerés a bemeneti szöveg nyelven van megírva](how-tos/text-analytics-how-to-language-detection.md) és a jelentés minden egyes dokumentum legfeljebb 120 nyelv vonatkozó kérelem küldése egyetlen nyelvkód. A nyelvkód egy pontszámmal párba állítva jelzi a pontszám erősségét. Használhatja a [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) API vagy a [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) SDK-t.

## <a name="entity-recognition-preview"></a>Entitások felismerése (előzetes verzió)
[Azonosítsa és entitások kategorizálása](how-tos/text-analytics-how-to-entity-linking.md) a szövegben, személyek, helyek, szervezetek, dátum és idő, mennyiség, százalékos, pénznemek és több. A rendszer felismeri a közismert entitásokat, amelyeket összekapcsol az interneten található további információkkal. Használhatja a [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634) API-t.

## <a name="use-containers"></a>Tárolók használata

[A Text Analytics tárolókkal](how-tos/text-analytics-how-to-install-containers.md) kinyerheti a kulcsfontosságú kifejezéseket, nyelv észlelése és helyileg, vélemények elemzése a szabványos Docker-tárolók közelebb telepítse az adatokhoz.

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

A Text Analytics API minden végpontja nyers szöveges adatokat fogad el. A jelenlegi maximum az egyes dokumentumok; 5,120 karaktereket Ha nagyobb dokumentumok elemzése van szüksége, bonthatja őket szeletekre. Ha mégis magasabb korlátra van szüksége, [lépjen velünk kapcsolatba](https://azure.microsoft.com/overview/sales-number/), hogy megbeszélhessük a követelményeit.

| Korlát | Érték |
|------------------------|---------------|
| Az egyes dokumentumok maximális mérete | mért 5,120 karakterek [ `StringInfo.LengthInTextElements` ](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Teljes kérés maximális mérete | 1 MB |
| Egy kérésbe foglalt dokumentumok maximális száma | 1000 dokumentum |

A hívások számának korlátja percenként 100 hívás. Vegye figyelembe, hogy egyetlen hívásba nagy mennyiségű (akár 1000) dokumentumot is belefoglalhat.

## <a name="unicode-encoding"></a>Unicode-kódolás

A Text Analytics API Unicode-kódolást használ a szövegek megjelenítéséhez és a karakterszámok számításához. A kérések elküldhetők UTF-8- és UTF-16-kódolással is, amelyek között nincs számottevő különbség a karakterek számában. A rendszer a Unicode-kódpontokat használja a karakterszám heurisztikus számításához. A két mennyiség a Text Analytics adatkorlátai szempontjából egyenértékű. Ha [ `StringInfo.LengthInTextElements` ](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) beolvasni a karakterek számát, adatok méretének mérésére használatával ugyanezt a módszert használja.

## <a name="next-steps"></a>További lépések

+ [Regisztráljon](how-tos/text-analytics-how-to-signup.md) egy hozzáférési kulcs beszerzéséhez és tekintse át [az API meghívásának](how-tos/text-analytics-how-to-call-api.md) lépéseit.

+ A [rövid útmutató](quickstarts/csharp.md) a C# nyelven írt REST API-hívásokat mutatja be részletesen. Ismerje meg, hogyan küldhet el egy szöveget, választhat ki egy elemzést és tekintheti meg az eredményeket minimális kódolással. Ha szeretné, kezdheti a [Python rövid](quickstarts/python.md) helyette.

+ Feltárásához ennek [sentiment analysis oktatóanyag](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services) Azure Databricks használatával.

+ Tekintse meg a blogbejegyzések és további videók a szövegelemzési API használata más eszközök és technológiák listáját az [külső és közösségi tartalom lap](text-analytics-resource-external-community.md).