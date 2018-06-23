---
title: Szöveg Analytics API áttekintés – Azure kognitív szolgáltatások |} Microsoft Docs
description: Szöveg-Analytics API Azure kognitív véleményeket elemzés, a kulcs kifejezés kivonása és a nyelvi észlelési szolgáltatás.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: article
ms.date: 5/02/2018
ms.author: ashmaka
ms.openlocfilehash: db5ea943f270aa512afb508668aec90cc4c90df4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349411"
---
# <a name="what-is-text-analytics-api-version-20"></a>Mi az a szöveg Analytics API 2.0-s verziójú?

A szöveg Analytics API egy felhőalapú szolgáltatás, amely a nyers szöveg felett feldolgozása speciális természetes nyelvű biztosít, és négy fő funkciókat tartalmazza: véleményeket elemzés, kulcs kifejezés kinyerési, nyelvi észlelését és az entitás csatolása.

Az API biztonsági erőforrások [Microsoft kognitív szolgáltatások](https://docs.microsoft.com/azure/cognitive-services/), gépi tanulási és a felhőben, könnyen használhatóvá a fejlesztési projektekben AI algoritmusokat gyűjteménye.

## <a name="capabilities-in-text-analytics"></a>Szövegelemzések képességei

Szöveg elemzés azt különböző fogalom, de kognitív szolgáltatások, a szöveg Analytics API biztosít az alábbi táblázatban leírtaknak analysis négy típusú.

| Műveletek| Leírás | API-k |
|-----------|-------------|------|
|[**Véleményeket elemzés**](how-tos/text-analytics-how-to-sentiment-analysis.md) | Megtudhatja, mi ügyfelek gondolja, hogy a márka vagy témakör elemzésével kapcsolatos pozitív vagy negatív véleményeket keresik nyers szöveg. Ez az API adja vissza a céggel kapcsolatos véleményeket pontszám 0 és 1 nyilvántartott egyes dokumentumok közötti 1 a legtöbb pozitív.<br /> Az elemzési modellek vannak pretrained használatával egy Microsoft szöveg és a természetes nyelvű technológiák kiterjedt törzsét. A [kiválasztva nyelv](text-analytics-supported-languages.md), az API-t elemezheti és pontszám ad meg, közvetlenül a hívó alkalmazás visszatérő eredmények nyers szöveg. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) <br /> [.NET](https://docs.microsoft.com/en-us/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package)  |
|[**Kulcs kifejezés kivonása**](how-tos/text-analytics-how-to-keyword-extraction.md) | Automatikusan bontsa ki a legfontosabb kifejezések gyorsan azonosíthatja a főbb pontjai. Például, ha a bemeneti szöveg "az étele delicious volt, és nem találhatók a csodálatos személyzet", az API-t adja vissza a fő beszélő pontok: "étele" és "csodálatos személyzet".  | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) <br /> [.NET](https://docs.microsoft.com/en-us/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) |
|[**Nyelvi észlelése**](how-tos/text-analytics-how-to-language-detection.md) | Legfeljebb 120 nyelvek milyen nyelven íródott a bemeneti szöveg észlel, és minden dokumentumhoz, a kérelem küldése egyetlen nyelvkódot jelentik. A nyelvi kódot van párosítva a pontszám erősségével jelző pontszámot. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) <br />  [.NET](https://docs.microsoft.com/en-us/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) | 
|[**Entitás Linking (előzetes verzió)**](how-tos/text-analytics-how-to-entity-linking.md) | A szöveg és a weben további információkra mutató hivatkozás a jól ismert entitások azonosítása. Entitás felismeri és disambiguates, ha a kifejezés egyik külön megkülönböztethető entitásokat, műveletek és más word formában. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) | 

## <a name="typical-workflow"></a>Jellemző munkafolyamat

A munkafolyamat felettébb egyszerű: az elemzés és leíró kimenetek adatokat küld a kódban. Elemzőkkel felhasználni-van, további konfigurációs és testreszabási nélkül.

1. [Regisztráció](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) a egy [hozzáférési kulcs](how-tos/text-analytics-how-to-access-key.md). A kulcsot minden kérelemnél meg kell átadni.

2. [Állítson össze egy kérelem](how-tos/text-analytics-how-to-call-api.md#json-schema) adatait tartalmazó nyers strukturálatlan szövegként, a JSON-ban.

3. A kérés a végpontra során létesített POST-előfizetés, a kívánt erőforrás hozzáfűzése: véleményeket elemzés, kulcs kifejezés kinyerési, nyelvi észlelési vagy entitás azonosítója.

4. Adatfolyam-, vagy a válasz helyileg tárolja. Attól függően, hogy a kérés eredménye egy véleményeket pontszám, kibontott legfontosabb kifejezések gyűjteménye vagy egy nyelvi kódot.

Kimeneti ad vissza, egy JSON-dokumentumból, minden egyes közzétett, szöveges dokumentum eredményekkel az azonosítója alapján. Ezt követően elemzése, jelenítheti meg vagy az eredményeket a gyakorlatban használható elemzések kategorizálását.

Adatok nem találhatók a fiókját. A szöveg Analytics API által végrehajtott műveletek olyan állapot nélküli, ami azt jelenti, hogy megadja a szöveg feldolgozása és eredményeinek azonnal.

<a name="supported-languages"></a>

## <a name="supported-languages"></a>Támogatott nyelvek

Ez a rész át lett helyezve egy külön cikk tárgyalja hogy könnyebben megtalálható legyen. Tekintse meg [támogatott nyelv szöveg Analytics API](text-analytics-supported-languages.md) ehhez a tartalomhoz.

<a name="data-limits"></a>

## <a name="data-limits"></a>Adatok korlátok

A szöveg Analytics API-végpontok fogadja el a nyers adatokat. A jelenlegi határértéke 5000 karakterek nyilvántartott egyes dokumentumok; Ha nagyobb dokumentumok elemzése van szüksége, bonthatja azokat fel kisebb. Ha továbbra is szüksége van egy magasabb határérték [, lépjen velünk kapcsolatba](https://azure.microsoft.com/overview/sales-number/) , hogy a követelmények is tárgyaljuk.

| Korlát | Érték |
|------------------------|---------------|
| Egyetlen dokumentum maximális mérete | 5000 karakterek mért `String.Length`. |
| A teljes kérelemfeldolgozási maximális mérete | 1 MB |
| Dokumentumok a kérelmek maximális száma | 1000 dokumentumok |

A sávszélesség-korlátjának értéke 100 hívások / perc. Vegye figyelembe, hogy elküldheti a nagy mennyiségű dokumentumok egyetlen hívásban (legfeljebb 1000 dokumentumok).

## <a name="unicode-encoding"></a>Unicode-kódolás

A szöveg Analytics API Unicode kódolást szöveges ábrázolást és karaktert száma számítások használ. Kérelmek nincsenek mérhető különbségek a karakterszám az UTF-8 és UTF-16 is küldheti. Unicode codepoints használják a heurisztika karakter hosszúságú, és egyenértékűnek szöveg analytics adatok korlátok céljából. Ha `String.Length` ahhoz, hogy a karakterszám, ugyanezt a módszert használjuk adatok méretének mérésére használ.

## <a name="next-steps"></a>További lépések

Először próbálja meg a [interaktív bemutató](https://azure.microsoft.com/services/cognitive-services/text-analytics/). Illessze be a nyelv (legfeljebb 120) észlelése, a céggel kapcsolatos véleményeket pontszám kiszámításához szöveges bevitel (legfeljebb 5000 karakter), vagy bontsa ki a legfontosabb kifejezések. Nincs előfizetés szükséges.

Amikor készen áll a API hívása közvetlenül:

+ [Regisztráció](how-tos/text-analytics-how-to-signup.md) egy hozzáférési kulcs, és tekintse át a lépései [hívja az API-t](how-tos/text-analytics-how-to-call-api.md).

+ [Gyors üzembe helyezés](quickstarts/csharp.md) a REST API bemutatóért meghívja a C# nyelven írt. Útmutató szöveg elküldése, válassza ki az elemzés, és minimális kóddal eredmények megtekintése.

+ [API-referenciadokumentáció](//go.microsoft.com/fwlink/?LinkID=759346) biztosít az API-k műszaki dokumentációját. A dokumentáció támogatja a beágyazott hívások, így hívása az API-t minden dokumentáció oldalról.

+ [Külső & közösségi tartalom](text-analytics-resource-external-community.md) blogbejegyzések és a videó bemutatja, hogyan lehet Szövegelemzések használható egyéb eszközök és technológiák listáját tartalmazza.

## <a name="see-also"></a>Lásd még

 [Kognitív szolgáltatások dokumentációja lap](https://docs.microsoft.com/azure/cognitive-services/)
