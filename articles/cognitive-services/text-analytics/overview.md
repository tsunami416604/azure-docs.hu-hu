---
title: Adatbányászat és-elemzés a Text Analytics API-Azure Cognitive Services
titleSuffix: Azure Cognitive Services
description: Ismerkedjen meg a Text Analytics APIával. Használhatja az érzelmek elemzéséhez, a nyelvfelismerés és a természetes nyelvi feldolgozás más formáihoz.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 09/09/2020
ms.author: aahi
keywords: szöveg-adatbányászat, érzelmek elemzése, szöveges elemzés
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 544de4adb1891c3d558a524466a076daefb42aa4
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "89647460"
---
# <a name="what-is-the-text-analytics-api"></a>Mi a Text Analytics API?

A Text Analytics API egy felhőalapú szolgáltatás, amely természetes nyelvi feldolgozási (NLP) funkciókat biztosít a szöveg-és szöveges elemzésekhez, például a következőkhöz: érzelmek elemzése, közvélemény-kutatás, kulcsfontosságú kifejezés kinyerése, nyelvfelismerés és elnevezett entitások felismerése.

Az API az [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/)része, amely a felhőben a gépi tanulási és AI-algoritmusok gyűjteménye a fejlesztési projektekhez. Ezeket a szolgáltatásokat a [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/)vagy az [ügyféloldali kódtár](quickstarts/text-analytics-sdk.md)használatával is használhatja.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

## <a name="sentiment-analysis"></a>Hangulatelemzés

Használja ki a [hangulat elemzését](how-tos/text-analytics-how-to-sentiment-analysis.md) , és Ismerje meg, hogy az emberek mit gondolnak a márka vagy a téma alapján, hogy kibányászják a pozitív vagy negatív gondolkodással kapcsolatos nyomokat. Ez az API-szolgáltatás az egyes dokumentumokhoz tartozó, 0 és 1 közötti hangulati pontszámot ad vissza, ahol az 1 a legpozitív.

A 3.1-es verziótól kezdődően a vélemény bányászati funkciója Hangulatelemzés. Ez a funkció a természetes nyelvi feldolgozásban (például a termékek vagy szolgáltatások attribútumaiban) lévő véleményekkel kapcsolatos részletesebb információkat tartalmaz a szövegben található, aspektusokon alapuló Hangulatelemzésként is.

## <a name="key-phrase-extraction"></a>Kulcskifejezések kinyerése

A [Key kifejezés kinyerésével](how-tos/text-analytics-how-to-keyword-extraction.md) gyorsan azonosíthatja a szövegben szereplő főbb fogalmakat. Például "az élelmiszer finom volt, és csodálatos volt a személyzet", Kulcsszókeresés a fő beszélő pontokat fogja visszaadni: "Food" és "csodálatos személyzet".

## <a name="language-detection"></a>Nyelvfelismerés

A nyelvfelismerés képes [észlelni, hogy egy szövegbeviteli szöveg beírásra kerül](how-tos/text-analytics-how-to-language-detection.md) , és egyetlen nyelvi kódot jelentsen a kérelemben elküldött minden dokumentumhoz a különböző nyelvek, változatok, dialektusok és egyes regionális/kulturális nyelvek esetében. A nyelvi kód egy megbízhatósági pontszámmal van párosítva.

## <a name="named-entity-recognition"></a>Nevesített entitások felismerése

Az elnevezett entitások felismerése képes [azonosítani és kategorizálni az entitásokat](how-tos/text-analytics-how-to-entity-linking.md) a szövegben, mivel a felhasználók, a helyek, a szervezetek, a mennyiségek és a jól ismert entitások is felismerhetők, és a weben található további információkhoz kapcsolódnak.

## <a name="use-containers"></a>Tárolók használata

[A Text Analytics tárolókat](how-tos/text-analytics-how-to-install-containers.md) helyszíni megoldásként használhatja a bányászati szövegekhez és az API-hoz. Ezek a Docker-tárolók lehetővé teszik a legfontosabb kifejezések kinyerését, a nyelv felismerését, valamint az adatokhoz való alaposabb elemzést.

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
    * [Adatok kinyerése az Excelben a Text Analytics és a Power automatizálás használatával](tutorials/extract-excel-information.md)
    * [A Text Analytics API és az MS flow használatával azonosíthatja a Yammer-csoportba tartozó megjegyzések hangulatát](https://docs.microsoft.com/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Power BI integrálása a Text Analytics APIekkel az ügyfelek visszajelzésének elemzéséhez](tutorials/tutorial-power-bi-key-phrases.md)
* Ajánlott programozási élmény:
    * [Streamelési adatok hangulatelemzése az Azure Databricks használatával](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Hozzon létre egy lombik-alkalmazást a szöveg fordításához, a hangulat elemzéséhez és a beszédfelismerés hangszintéziséhez](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Támogatott nyelvek

Ez a szakasz egy külön cikkbe lett áthelyezve a jobb átláthatóság érdekében. A tartalomhoz [a Text Analytics API támogatott nyelveket](text-analytics-supported-languages.md) tekintheti meg.

<a name="data-limits"></a>

## <a name="data-limits"></a>Adatkorlátok

A Text Analytics API minden végpontja nyers szöveges adatokat fogad el. További információért tekintse meg az [adatokra vonatkozó korlátozásokat](concepts/data-limits.md) ismertető cikket.

## <a name="unicode-encoding"></a>Unicode-kódolás

A Text Analytics API Unicode-kódolást használ a szövegek megjelenítéséhez és a karakterszámok számításához. A kérések elküldhetők UTF-8- és UTF-16-kódolással is, amelyek között nincs számottevő különbség a karakterek számában. A rendszer a Unicode-kódpontokat használja a karakterszám heurisztikus számításához. A két mennyiség a Text Analytics adatkorlátai szempontjából egyenértékű. Ha [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) a karakterek számának beolvasására használja, ugyanazt a módszert használja az adatméret mérésére.

## <a name="next-steps"></a>További lépések

+ [Hozzon létre egy Azure-erőforrást](../cognitive-services-apis-create-account.md) az Text Analytics számára az alkalmazások kulcsának és végpontjának beszerzéséhez.

+ A rövid útmutató segítségével megkezdheti [az API-hívások](quickstarts/text-analytics-sdk.md) küldését. Ismerje meg, hogyan küldhet el egy szöveget, választhat ki egy elemzést és tekintheti meg az eredményeket minimális kódolással.

+ Az új kiadásokkal és szolgáltatásokkal kapcsolatos információkért tekintse [meg a Text Analytics API újdonságait](whats-new.md) .

+ A Azure Databricks használatával mélyebben kihasználhatja ezt az [érzelmi elemzést ismertető oktatóanyagot](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services) .

+ Tekintse meg a blogbejegyzések listáját, valamint további videókat arról, hogyan használhatja a Text Analytics APIt a [külső & közösségi tartalom oldalán](text-analytics-resource-external-community.md)található egyéb eszközökkel és technológiákkal.
