---
title: Mi az Text Analytics API? Képességek
titleSuffix: Azure Cognitive Services
description: Használja az Azure Cognitive Services Text Analytics API az érzelmek elemzéséhez, a kulcsfontosságú kifejezés kinyeréséhez, a nyelvfelismerés és az entitások felismeréséhez.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: f84d980dd01d1e9f3ffcc00d73f712211524cb42
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667644"
---
# <a name="what-is-text-analytics-api"></a>Mi az Text Analytics API?

A Text Analytics API egy felhőalapú szolgáltatás, amely fejlett természetes nyelvi feldolgozást tesz lehetővé nyers szövegként, és négy fő funkciót tartalmaz: az érzelmek elemzését, a kulcsfontosságú kifejezés kinyerését, a nyelvfelismerés és az entitások felismerését.

Az API az [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/)része, amely a felhőben a gépi tanulási és AI-algoritmusok gyűjteménye a fejlesztési projektekhez.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

A szöveg elemzése különböző dolgokat jelenthet, de a Cognitive Servicesban a Text Analytics API négyféle elemzési típust biztosít az alább leírtak szerint.

## <a name="sentiment-analysis"></a>Véleményelemzés
Az [érzelmek elemzésével](how-tos/text-analytics-how-to-sentiment-analysis.md) megtudhatja, hogy az ügyfelek mit gondolnak a márka vagy a téma alapján, ha a nyers szöveget a pozitív vagy negatív hangulatú nyomokra elemzi. Az API minden dokumentumhoz visszaad egy 0 és 1 közötti hangulati pontszámot, ahol az 1 a legpozitívabb pontszám.<br /> Az elemzési modellek előzetes betanítása a Microsoft széles körű szövegtörzs-technológiái és természetes nyelvi technológiái segítségével történik. [Bizonyos nyelvek](text-analytics-supported-languages.md) esetében az API képes a megadott szöveg elemzéséből kiszámított pontszámot közvetlenül visszaadni a hívó alkalmazásnak. Használhatja a [Rest](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c9) API-t vagy a [.net](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#create-the-visual-studio-solution-and-install-the-sdk) SDK-t.

## <a name="key-phrase-extraction"></a>Kulcskifejezések kinyerése
A fő pontok gyors azonosításához automatikusan Kinyeri a [legfontosabb kifejezéseket](how-tos/text-analytics-how-to-keyword-extraction.md) . Például „Az étel finom volt, és a személyzet kedves volt” bemeneti szövegből az API a következő fő pontokat adja vissza: „étel” és „személyzet kedves”. Használhatja a [Rest](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c6) API-t vagy a [.net](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#create-the-visual-studio-solution-and-install-the-sdk) SDK-t.

## <a name="language-detection"></a>Nyelvfelismerés
Meghatározhatja, hogy [a bemeneti szöveg milyen nyelven legyen beírva](how-tos/text-analytics-how-to-language-detection.md) , és egyetlen nyelvi kódot jelentsen a kérelemben elküldött összes dokumentumhoz számos nyelven, változatban, dialektusban és egyes regionális/kulturális nyelveken. A nyelvkód egy pontszámmal párba állítva jelzi a pontszám erősségét. Használhatja a [Rest](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c7) API-t vagy a [.net](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#create-the-visual-studio-solution-and-install-the-sdk) SDK-t.

## <a name="named-entity-recognition"></a>Megnevezett entitások felismerése
[Azonosíthatja és kategorizálhatja](how-tos/text-analytics-how-to-entity-linking.md) a szövegben lévő entitásokat személyként, helyként, szervezetként, dátum/idő, mennyiség, százalék, pénznem stb. A rendszer felismeri a közismert entitásokat, amelyeket összekapcsol az interneten található további információkkal. Használhatja a [Rest](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/5ac4251d5b4ccd1554da7634) API-t.

## <a name="use-containers"></a>Tárolók használata

[A Text Analytics tárolók használatával](how-tos/text-analytics-how-to-install-containers.md) kinyerheti a kulcsfontosságú kifejezéseket, azonosíthatja a nyelveket és elemezheti a hangulatokat helyileg, ha a szabványosított Docker-tárolókat az adatokhoz közelebb telepíti.

## <a name="typical-workflow"></a>Jellemző munkafolyamat

A munkafolyamat egyszerű: benyújtjuk az adatokat elemzésre és a kódban kezeljük a kimeneteket. Az elemzők használatra készek, esetükben nincs szükség további konfigurációs beállításokra vagy testreszabásra.

1. [Regisztráljon](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) egy [hozzáférési kulcs](how-tos/text-analytics-how-to-access-key.md) beszerzéséhez. A kulcsot minden egyes kérés alkalmával meg kell adni.

2. [Állítson össze egy kérést](how-tos/text-analytics-how-to-call-api.md#json-schema), amely az adatokat nyers, strukturálatlan szövegként tartalmazza, JSON formátumban.

3. Küldje el a kérést a feliratkozáskor megállapított végpontra, és jelölje meg a használni kívánt erőforrást: hangulatelemzés, kulcsszókinyerés, nyelvfelismerés vagy entitásazonosítás.

4. A válasz streamelhető vagy helyileg is tárolható. A kéréstől függően az eredmény lehet egy véleménypontszám, kinyert kulcskifejezések gyűjteménye vagy egy nyelvkód.

A kimenetet a rendszer egyetlen JSON-dokumentumban adja vissza, amely tartalmazza az összes elküldött szöveges dokumentum eredményeit, azok azonosítói alapján. Az eredmények ezt követően elemezhetők, vizualizálhatók vagy kategorizálhatók a gyakorlatban használható megállapításokká.

Az adatok nem lesznek tárolva a fiókjában. A Text Analytics API által végrehajtott műveletek állapot nélküliek, ami azt jelenti, hogy a szöveg feldolgozása és az eredmények visszaadása azonnal megtörténik.

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>Text Analytics a programozási élmény különböző szintjeihez

Elkezdheti használni a Text Analytics API a folyamatokban, még akkor is, ha nem sok tapasztalattal rendelkezik a programozásban. Ezekkel az oktatóanyagokkal megtudhatja, hogyan használhatja az API-t a szöveg különböző módokon történő elemzéséhez a felhasználói élmény szintjéhez. 

* Minimális programozás szükséges:
    * [A Text Analytics API és az MS flow használatával azonosíthatja a Yammer-csoportba tartozó megjegyzések hangulatát](https://docs.microsoft.com/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Power BI integrálása a Text Analytics APIekkel az ügyfelek visszajelzésének elemzéséhez](tutorials/tutorial-power-bi-key-phrases.md)
* Ajánlott programozási élmény:
    * [Az adatátviteli elemzések Azure Databricks használatával](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Hozzon létre egy lombik-alkalmazást a szöveg fordításához, a hangulat elemzéséhez és a beszédfelismerés hangszintéziséhez](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Támogatott nyelvek

Ez a szakasz egy külön cikkbe lett áthelyezve a jobb átláthatóság érdekében. Lásd a [Text Analytics API-ban támogatott nyelveket ismertető](text-analytics-supported-languages.md) cikket.

<a name="data-limits"></a>

## <a name="data-limits"></a>Adatkorlátok

A Text Analytics API minden végpontja nyers szöveges adatokat fogad el. Az aktuális korlát az egyes dokumentumok 5 120 karaktere. Ha nagyobb dokumentumokat kell elemezni, akkor a kisebb adattömbökbe lehet bontani. Ha mégis magasabb korlátra van szüksége, [lépjen velünk kapcsolatba](https://azure.microsoft.com/overview/sales-number/), hogy megbeszélhessük a követelményeit.

| Korlát | Value |
|------------------------|---------------|
| Az egyes dokumentumok maximális mérete | 5 120 karakter, a következőképpen [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements)mérve:. |
| Teljes kérés maximális mérete | 1 MB |
| Egy kérésbe foglalt dokumentumok maximális száma | 1000 dokumentum |

A díjszabási korlátja az Ön díjszabási szintjétől függ.

| Szint          | Kérelmek/másodperc | Percenkénti kérelmek |
|---------------|---------------------|---------------------|
| Több szolgáltatás | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

A kérelmeket külön kell mérni az egyes Text Analytics szolgáltatásokhoz. Elküldheti például az árképzési szintjére vonatkozó kérések maximális számát az egyes szolgáltatásokhoz, egyszerre.      

## <a name="unicode-encoding"></a>Unicode-kódolás

A Text Analytics API Unicode-kódolást használ a szövegek megjelenítéséhez és a karakterszámok számításához. A kérések elküldhetők UTF-8- és UTF-16-kódolással is, amelyek között nincs számottevő különbség a karakterek számában. A rendszer a Unicode-kódpontokat használja a karakterszám heurisztikus számításához. A két mennyiség a Text Analytics adatkorlátai szempontjából egyenértékű. Ha a karakterek [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) számának beolvasására használja, ugyanazt a módszert használja az adatméret mérésére.

## <a name="next-steps"></a>További lépések

+ [Hozzon létre egy Azure](../cognitive-services-apis-create-account.md) -erőforrást az Text Analytics számára az alkalmazások kulcsának és végpontjának beszerzéséhez.

+ A [rövid útmutató](quickstarts/csharp.md) a C# nyelven írt REST API-hívásokat mutatja be részletesen. Ismerje meg, hogyan küldhet el egy szöveget, választhat ki egy elemzést és tekintheti meg az eredményeket minimális kódolással. Ha szeretné, a [Python](quickstarts/python.md) rövid útmutatóval is elvégezheti a használatát.

+ Az új kiadásokkal és szolgáltatásokkal kapcsolatos információkért tekintse [meg a Text Analytics API újdonságait](whats-new.md) .

+ A Azure Databricks használatával mélyebben kihasználhatja ezt az [érzelmi elemzést ismertető oktatóanyagot](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services) .

+ Tekintse meg a blogbejegyzések listáját, valamint további videókat arról, hogyan használhatók a Text Analytics API más eszközökkel és technológiákkal a [külső & közösségi tartalom oldalán](text-analytics-resource-external-community.md).
