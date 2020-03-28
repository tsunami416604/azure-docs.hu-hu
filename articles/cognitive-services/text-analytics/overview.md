---
title: Mi a Text Analytics API? - Képességek -
titleSuffix: Azure Cognitive Services
description: Használja az Azure Cognitive Services Szövegelemzésapi-t a hangulatelemzéshez, a kulcskifejezések kinyeréséhez, a nyelvfelismeréshez és az entitásfelismeréshez.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: a9519be591581fa434825f1a1fb31749788a21a8
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "78395734"
---
# <a name="what-is-the-text-analytics-api"></a>Mi a Text Analytics API?

A Text Analytics API egy felhőalapú szolgáltatás, amely fejlett természetes nyelvi feldolgozást biztosít a nyers szövegen keresztül, és négy fő funkciót tartalmaz: hangulatelemzés, kulcskifejezések kinyerése, nyelvfelismerés és elnevezett entitásfelismerés.

Az API az [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/)része, amely gépi tanulási és AI-algoritmusok gyűjteménye a felhőben a fejlesztési projektekhez.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

A szövegelemzés különböző dolgokat jelenthet, de a Cognitive Servicesben a Text Analytics API négyféle elemzést biztosít az alábbiakban leírtak szerint. Ezeket a szolgáltatásokat használhatja a [REST API-val](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/)vagy az [ügyféltárral.](quickstarts/text-analytics-sdk.md)

## <a name="sentiment-analysis"></a>Hangulatelemzés
A [hangulatelemzés segítségével](how-tos/text-analytics-how-to-sentiment-analysis.md) megtudhatja, hogy az ügyfelek mit gondolnak a márkájáról vagy témájáról, ha nyers szöveget elemez a pozitív vagy negatív érzésekkel kapcsolatos nyomokért. Az API minden dokumentumhoz visszaad egy 0 és 1 közötti hangulati pontszámot, ahol az 1 a legpozitívabb pontszám.<br /> Az elemzési modellek előzetes betanítása a Microsoft széles körű szövegtörzs-technológiái és természetes nyelvi technológiái segítségével történik. [Bizonyos nyelvek](text-analytics-supported-languages.md) esetében az API képes a megadott szöveg elemzéséből kiszámított pontszámot közvetlenül visszaadni a hívó alkalmazásnak.

## <a name="key-phrase-extraction"></a>Kulcskifejezések kinyerése
A főbb kifejezések automatikus [kibontása](how-tos/text-analytics-how-to-keyword-extraction.md) a főbb pontok gyors azonosításához. Például „Az étel finom volt, és a személyzet kedves volt” bemeneti szövegből az API a következő fő pontokat adja vissza: „étel” és „személyzet kedves”.

## <a name="language-detection"></a>Nyelvfelismerés
A [szövegbeviteli szöveg et,](how-tos/text-analytics-how-to-language-detection.md) és a kérelemre küldött minden egyes dokumentumhoz egyetlen nyelvkódot jelenthet a nyelvek, változatok, dialektusok és néhány regionális/kulturális nyelv széles körében. A nyelvkód egy pontszámmal párba állítva jelzi a pontszám erősségét.

## <a name="named-entity-recognition"></a>Megnevezett entitások felismerése
[Azonosítsa és kategorizálja](how-tos/text-analytics-how-to-entity-linking.md) a szövegben lévő entitásokat személyek, helyek, szervezetek, dátum/idő, mennyiségek, százalékok, pénznemek stb. A rendszer felismeri a közismert entitásokat, amelyeket összekapcsol az interneten található további információkkal.

## <a name="use-containers"></a>Tárolók használata

[A Text Analytics-tárolók segítségével](how-tos/text-analytics-how-to-install-containers.md) kulcskifejezéseket nyerhet ki, észlelheti a nyelvet, és helyileg elemezheti a véleményeket az adatokhoz közelebb iszkarizált Docker-tárolók telepítésével.

## <a name="typical-workflow"></a>Jellemző munkafolyamat

A munkafolyamat egyszerű: benyújtjuk az adatokat elemzésre és a kódban kezeljük a kimeneteket. Az elemzők használatra készek, esetükben nincs szükség további konfigurációs beállításokra vagy testreszabásra.

1. [Hozzon létre egy Azure-erőforrást](../cognitive-services-apis-create-account.md) a Text Analytics számára. Ezt követően a kérelmek hitelesítéséhez létrehozott [kulcs lekérése.](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)

2. [Állítson össze egy kérést](how-tos/text-analytics-how-to-call-api.md#json-schema), amely az adatokat nyers, strukturálatlan szövegként tartalmazza, JSON formátumban.

3. Tegye közzé a kérelmet a regisztráció során létrehozott végpontra, hozzáfűzve a kívánt erőforrást: hangulatelemzés, kulcskifejezések kinyerése, nyelvfelismerés vagy elnevezett entitásfelismerés.

4. A válasz streamelhető vagy helyileg is tárolható. A kéréstől függően az eredmény lehet egy véleménypontszám, kinyert kulcskifejezések gyűjteménye vagy egy nyelvkód.

A kimenetet a rendszer egyetlen JSON-dokumentumban adja vissza, amely tartalmazza az összes elküldött szöveges dokumentum eredményeit, azok azonosítói alapján. Az eredmények ezt követően elemezhetők, vizualizálhatók vagy kategorizálhatók a gyakorlatban használható megállapításokká.

Az adatok nem lesznek tárolva a fiókjában. A Text Analytics API által végrehajtott műveletek állapot nélküliek, ami azt jelenti, hogy a szöveg feldolgozása és az eredmények visszaadása azonnal megtörténik.

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>Szövegelemzés több programozási élményszinthez

Elkezdheti használni a Text Analytics API-t a folyamatokban, még akkor is, ha nincs sok tapasztalata a programozásban. Ezekkel az oktatóanyagokkal megtudhatja, hogyan elemezheti az API-t a szöveg különböző módokon történő elemzésére, hogy illeszkedjen a felhasználói élmény szintjéhez. 

* Minimális programozás szükséges:
    * [Információk kinyerése az Excelben a Szövegelemzés és a Power Automate használatával](tutorials/extract-excel-information.md)
    * [A Text Analytics API és az MS Flow segítségével azonosíthatja a Yammer-csoportban lévő megjegyzések hangulatát](https://docs.microsoft.com/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [A Power BI integrálása a Text Analytics API-val az ügyfelek visszajelzéseinek elemzéséhez](tutorials/tutorial-power-bi-key-phrases.md)
* Programozási tapasztalat ajánlott:
    * [Streamelési adatok hangulatelemzése az Azure Databricks használatával](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Flask alkalmazás létrehozása szöveg fordításához, hangulatelemzéshez és beszédszintetizáláshoz](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Támogatott nyelvek

Ez a szakasz egy külön cikkbe lett áthelyezve a jobb átláthatóság érdekében. A tartalomhoz [lásd a Szövegelemzés I-t](text-analytics-supported-languages.md) támogatott nyelvek.

<a name="data-limits"></a>

## <a name="data-limits"></a>Adatkorlátok

A Text Analytics API minden végpontja nyers szöveges adatokat fogad el. Az aktuális korlát 5120 karakter minden dokumentumesetében; Ha nagyobb dokumentumokat kell elemeznie, kisebb részekre bonthatja őket.

| Korlát | Érték |
|------------------------|---------------|
| Az egyes dokumentumok maximális mérete | 5120 karakter az [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements)alapján mérve. |
| Teljes kérés maximális mérete | 1 MB |
| Egy kérésbe foglalt dokumentumok maximális száma | 1000 dokumentum |

A díjkorlát a tarifacsomagtól függően változik.

| Szint          | Kérések száma másodpercenként | Kérelmek percenként |
|---------------|---------------------|---------------------|
| S / Multi-szolgáltatás | 1000                | 1000                |
| S0 / F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

A kérelmeket az egyes Szövegelemzési funkciókhoz külön mérik. Például egyszerre elküldheti a tarifacsomagra vonatkozó kérelmek maximális számát az egyes funkcióknak.      

## <a name="unicode-encoding"></a>Unicode-kódolás

A Text Analytics API Unicode-kódolást használ a szövegek megjelenítéséhez és a karakterszámok számításához. A kérések elküldhetők UTF-8- és UTF-16-kódolással is, amelyek között nincs számottevő különbség a karakterek számában. A rendszer a Unicode-kódpontokat használja a karakterszám heurisztikus számításához. A két mennyiség a Text Analytics adatkorlátai szempontjából egyenértékű. Ha a [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) karakterszám leszámítására használja, ugyanazt a módszert használja, mint az adatok méretének mérésére.

## <a name="next-steps"></a>További lépések

+ [Hozzon létre egy Azure-erőforrást](../cognitive-services-apis-create-account.md) a Text Analytics számára, hogy kulcs és végpontot szerezzen be az alkalmazásokhoz.

+ A [rövid útmutató](quickstarts/text-analytics-sdk.md) segítségével indítsa el az API-hívások küldését. Ismerje meg, hogyan küldhet el egy szöveget, választhat ki egy elemzést és tekintheti meg az eredményeket minimális kódolással.

+ Az új kiadásokról és funkciókról [a Text Analytics API újdonságai](whats-new.md) ból talál tájékoztatást.

+ Az Azure Databricks használatával egy kicsit mélyebbre áshat ezzel a [hangulatelemzési oktatóanyaggal.](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services)

+ Tekintse meg blogbejegyzések listáját és további videókat arról, hogyan használhatja a Text Analytics API-t más eszközökkel és technológiákkal [a Külső & közösségi tartalom oldalon.](text-analytics-resource-external-community.md)
