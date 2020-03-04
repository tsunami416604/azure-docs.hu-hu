---
title: Mi a Text Analytics API? Képességek
titleSuffix: Azure Cognitive Services
description: Használja az Azure Cognitive Services Text Analytics API az érzelmek elemzéséhez, a kulcsfontosságú kifejezés kinyeréséhez, a nyelvfelismerés és az entitások felismeréséhez.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 11/07/2019
ms.author: aahi
ms.openlocfilehash: 66bc1e5441210b8d9153812c826643159fe53d50
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251330"
---
# <a name="what-is-the-text-analytics-api"></a>Mi a Text Analytics API?

A Text Analytics API egy felhőalapú szolgáltatás, amely fejlett természetes nyelvi feldolgozást tesz lehetővé nyers szövegként, és négy fő funkciót tartalmaz: az érzelmek elemzését, a kulcsfontosságú kifejezés kinyerését, a nyelvfelismerés és az elnevezett entitások felismerését.

Az API az [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/)része, amely a felhőben a gépi tanulási és AI-algoritmusok gyűjteménye a fejlesztési projektekhez.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

A szöveg elemzése különböző dolgokat jelenthet, de a Cognitive Servicesban a Text Analytics API négyféle elemzési típust biztosít az alább leírtak szerint. Ezeket a szolgáltatásokat a [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/)vagy az [ügyféloldali kódtár](quickstarts/text-analytics-sdk.md)használatával is használhatja.

## <a name="sentiment-analysis"></a>Hangulatelemzés
Az [érzelmek elemzésével](how-tos/text-analytics-how-to-sentiment-analysis.md) megtudhatja, hogy az ügyfelek mit gondolnak a márka vagy a téma alapján, ha a nyers szöveget a pozitív vagy negatív hangulatú nyomokra elemzi. Az API minden dokumentumhoz visszaad egy 0 és 1 közötti hangulati pontszámot, ahol az 1 a legpozitívabb pontszám.<br /> Az elemzési modellek előzetes betanítása a Microsoft széles körű szövegtörzs-technológiái és természetes nyelvi technológiái segítségével történik. [Bizonyos nyelvek](text-analytics-supported-languages.md) esetében az API képes a megadott szöveg elemzéséből kiszámított pontszámot közvetlenül visszaadni a hívó alkalmazásnak.

## <a name="key-phrase-extraction"></a>Kulcskifejezések kinyerése
A fő pontok gyors azonosításához automatikusan [Kinyeri a legfontosabb kifejezéseket](how-tos/text-analytics-how-to-keyword-extraction.md) . Például „Az étel finom volt, és a személyzet kedves volt” bemeneti szövegből az API a következő fő pontokat adja vissza: „étel” és „személyzet kedves”.

## <a name="language-detection"></a>Nyelvfelismerés
Meghatározhatja, hogy [a bemeneti szöveg milyen nyelven legyen beírva](how-tos/text-analytics-how-to-language-detection.md) , és egyetlen nyelvi kódot jelentsen a kérelemben elküldött összes dokumentumhoz számos nyelven, változatban, dialektusban és egyes regionális/kulturális nyelveken. A nyelvkód egy pontszámmal párba állítva jelzi a pontszám erősségét.

## <a name="named-entity-recognition"></a>Megnevezett entitások felismerése
[Azonosíthatja és kategorizálhatja](how-tos/text-analytics-how-to-entity-linking.md) a szövegben lévő entitásokat személyként, helyként, szervezetként, dátum/idő, mennyiség, százalék, pénznem stb. A rendszer felismeri a közismert entitásokat, amelyeket összekapcsol az interneten található további információkkal.

## <a name="use-containers"></a>Tárolók használata

[A Text Analytics tárolók használatával](how-tos/text-analytics-how-to-install-containers.md) kinyerheti a kulcsfontosságú kifejezéseket, azonosíthatja a nyelveket és elemezheti a hangulatokat helyileg, ha a szabványosított Docker-tárolókat az adatokhoz közelebb telepíti.

## <a name="typical-workflow"></a>Jellemző munkafolyamat

A munkafolyamat egyszerű: benyújtjuk az adatokat elemzésre és a kódban kezeljük a kimeneteket. Az elemzők használatra készek, esetükben nincs szükség további konfigurációs beállításokra vagy testreszabásra.

1. [Hozzon létre egy Azure-erőforrást](../cognitive-services-apis-create-account.md) a Text Analyticshoz. Ezt követően [szerezze be a](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) kérések hitelesítéséhez létrehozott kulcsot.

2. [Állítson össze egy kérést](how-tos/text-analytics-how-to-call-api.md#json-schema), amely az adatokat nyers, strukturálatlan szövegként tartalmazza, JSON formátumban.

3. Tegye közzé a kérelmet a regisztráció során létrejött végponton, fűzze hozzá a kívánt erőforrást: érzelmek elemzése, kulcsfontosságú kifejezés kinyerése, nyelvfelismerés vagy elnevezett entitások felismerése.

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

Ez a szakasz egy külön cikkbe lett áthelyezve a jobb átláthatóság érdekében. A tartalomhoz [a Text Analytics API támogatott nyelveket](text-analytics-supported-languages.md) tekintheti meg.

<a name="data-limits"></a>

## <a name="data-limits"></a>Adatkorlátok

A Text Analytics API minden végpontja nyers szöveges adatokat fogad el. Az aktuális korlát az egyes dokumentumok 5 120 karaktere. Ha nagyobb dokumentumokat kell elemezni, akkor a kisebb adattömbökbe lehet bontani.

| Korlát | Érték |
|------------------------|---------------|
| Az egyes dokumentumok maximális mérete | 5 120 karakter a [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements)alapján mérve. |
| Teljes kérés maximális mérete | 1 MB |
| Egy kérésbe foglalt dokumentumok maximális száma | 1000 dokumentum |

A díjszabási korlátja az Ön díjszabási szintjétől függ.

| Szint          | Kérelmek/másodperc | Percenkénti kérelmek |
|---------------|---------------------|---------------------|
| S/több szolgáltatás | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

A kérelmeket külön kell mérni az egyes Text Analytics szolgáltatásokhoz. Elküldheti például az árképzési szintjére vonatkozó kérések maximális számát az egyes szolgáltatásokhoz, egyszerre.      

## <a name="unicode-encoding"></a>Unicode-kódolás

A Text Analytics API Unicode-kódolást használ a szövegek megjelenítéséhez és a karakterszámok számításához. A kérések elküldhetők UTF-8- és UTF-16-kódolással is, amelyek között nincs számottevő különbség a karakterek számában. A rendszer a Unicode-kódpontokat használja a karakterszám heurisztikus számításához. A két mennyiség a Text Analytics adatkorlátai szempontjából egyenértékű. Ha a karakterek számának beolvasásához [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) használ, ugyanazt a módszert használja az adatméret mérésére.

## <a name="next-steps"></a>Következő lépések

+ [Hozzon létre egy Azure-erőforrást](../cognitive-services-apis-create-account.md) az Text Analytics számára az alkalmazások kulcsának és végpontjának beszerzéséhez.

+ A rövid útmutató segítségével megkezdheti [az API-hívások](quickstarts/text-analytics-sdk.md) küldését. Ismerje meg, hogyan küldhet el egy szöveget, választhat ki egy elemzést és tekintheti meg az eredményeket minimális kódolással.

+ Az új kiadásokkal és szolgáltatásokkal kapcsolatos információkért tekintse [meg a Text Analytics API újdonságait](whats-new.md) .

+ A Azure Databricks használatával mélyebben kihasználhatja ezt az [érzelmi elemzést ismertető oktatóanyagot](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services) .

+ Tekintse meg a blogbejegyzések listáját, valamint további videókat arról, hogyan használhatja a Text Analytics APIt a [külső & közösségi tartalom oldalán](text-analytics-resource-external-community.md)található egyéb eszközökkel és technológiákkal.
