---
title: Call Center transzkripció - beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: A beszéd-szöveg felismerés gyakori forgatókönyve a különböző rendszerekből, például az interaktív hangválaszból (IVR) származó nagy mennyiségű telefonos adat átírása. A beszédfelismerési szolgáltatás és az egyesített beszédmodell használatával a vállalkozások kiváló minőségű átiratokat kaphatnak audiorögzítő rendszerekkel.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: d959f4948d6b848f3b399c1310add06991d72012
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806320"
---
# <a name="speech-service-for-telephony-data"></a>Beszédszolgáltatás a telefonos adatokhoz

A vezetékes, mobiltelefonés rádiók által generált telefonos adatok általában alacsony minőségűek, és keskeny sávúek a 8 KHz-es tartományban, ami kihívást jelent a beszédszöveggé alakításakor. A Beszédszolgáltatás legújabb beszédfelismerési modelljei még akkor is kitűnnek a telefonos adatok átírásában, ha az adatokat egy ember nehezen érti meg. Ezek a modellek nagy mennyiségű telefonos adattal vannak betanítva, és a piacon belüli legjobb felismerési pontossággal rendelkeznek, még zajos környezetben is.

A beszédfelismerési szöveg felismerése gyakori forgatókönyve a különböző rendszerekből, például az interaktív hangválaszból (IVR) származó nagy mennyiségű telefonos adat átírása. A hang ezek a rendszerek biztosítják lehet sztereó vagy monó, és a nyers kis-to-no utófeldolgozás történik a jel. A beszédfelismerési szolgáltatás és az egyesített beszédmodell használatával a vállalkozások kiváló minőségű átiratokat kaphatnak, bármilyen rendszert is használnak a hang rögzítésére.

A telefonos adatok felhasználhatók az ügyfelek igényeinek jobb megértésére, az új marketinglehetőségek azonosítására vagy a telefonos központ-ügynökök teljesítményének értékelésére. Az adatok átírása után egy vállalkozás használhatja a kimenetet olyan célokra, mint a továbbfejlesztett telemetriai adatok, kulcskifejezések azonosítása vagy az ügyfelek véleményének elemzése.

Az ezen a lapon ismertetett technológiákat a Microsoft belsőleg támogatja a hívásfeldolgozási szolgáltatásokhoz, mind valós idejű, mind kötegelt módban.

Tekintsük át a Beszédszolgáltatás néhány technológiai és kapcsolódó funkcióját.

> [!IMPORTANT]
> A beszédszolgáltatás unified modell betanított különböző adatokkal, és egy modelles megoldást kínál számos forgatókönyv diktálás a telefonos elemzés.

## <a name="azure-technology-for-call-centers"></a>Azure-technológia hívásközpontokhoz

A beszédfelismerési szolgáltatás funkcióinak funkcionális vonatkozásain túl elsődleges céljuk – a telefonos ügyfélszolgálaton alkalmazva – az ügyfélélmény javítása. E tekintetben három egyértelmű terület létezik:

- Hívás utáni elemzés, amely lényegében a hívásfelvételek kötegelt feldolgozása a hívás után.
- Valós idejű elemzés, amely a feldolgozás a hangjel kivonat különböző betekintést, mint a hívás zajlik (a hangulat, hogy egy kiemelkedő használati eset).
- Hangasszisztensek (botok), vagy az ügyfél és a robot közötti párbeszéd et vezetik, hogy megpróbálják megoldani az ügyfél problémáját ügynöki részvétel nélkül, vagy mesterséges intelligencia (AI) protokollok alkalmazása az ügynök segítésére.

Egy kötegforgatókönyv megvalósításának tipikus architektúrás diagramja ![a Hívásközpont átírási architektúrája alatti képen látható.](media/scenarios/call-center-transcription-architecture.png)

## <a name="speech-analytics-technology-components"></a>Beszédelemzési technológiai összetevők

Függetlenül attól, hogy a tartomány hívás utáni vagy valós idejű, az Azure kiforrott és kialakulóban lévő technológiákat kínál az ügyfélélmény javítása érdekében.

### <a name="speech-to-text-stt"></a>Beszéd-szöveg (STT)

[A beszédfelismerési](speech-to-text.md) szolgáltatás a legkeresettebb funkció bármely hívásközpont-megoldásban. Mivel az alsóbb rétegbeli elemzési folyamatok közül sok az átírt szövegre támaszkodik, a hibaarány _(WER_) rendkívül fontos. Az egyik legfontosabb kihívás a call center átírás a zaj, hogy elterjedt a call center (például más ügynökök beszél a háttérben), a gazdag különböző nyelvi helyszínek és dialektusok, valamint az alacsony minőségű a tényleges telefon jel. Wer nagymértékben korrelál azzal, hogy az akusztikai és nyelvi modellek be vannak tanítva egy adott területi beállításhoz, így fontos a modell testreszabása a területi beállításhoz. A legújabb Unified version 4.x modellek a megoldást mind az átírás pontosságát és a látencia. Több tízezer órányi akusztikai adattal és több milliárd lexikális információval betanított egyesített modellek a piac legpontosabb modelljei a hívásközpont adatainak átírásához.

### <a name="sentiment"></a>Hangulat

Annak felmérése, hogy az ügyfél nek jó tapasztalata volt-e, a beszédelemzés egyik legfontosabb területe, amikor a call center területére alkalmazza. A [Batch transzkripciós API-nk](batch-transcription.md) kimondott szövegenkénti hangulatelemzést kínál. A hívásátirat részeként kapott értékek összesítése meghatározhatja a hívás hangulatát mind az ügyintézők, mind az ügyfél számára.

### <a name="silence-non-talk"></a>Csend (nem beszél)

Nem ritka, hogy a támogatási felhívás 35 százaléka az, amit nem beszélünk. Néhány forgatókönyv, amely nem beszél: ügynökök felkeresi a korábbi esetelőzményeket egy ügyféllel, ügynökök olyan eszközöket használva, amelyek lehetővé teszik számukra az ügyfél asztalának elérését és a funkciók elvégzését, az ügyfelek várakozva az átvitelre várva, és így tovább. Rendkívül fontos felmérni, ha csend történik egy hívásban, mivel számos fontos ügyfélérzékenység fordul elő az ilyen típusú forgatókönyvek körül, és ahol azok a hívássorán fordulnak elő.

### <a name="translation"></a>Fordítás

Egyes vállalatok kísérleteznek az idegen nyelvű támogatási hívások lefordított átiratainak biztosításával, hogy a kézbesítési vezetők megértsék ügyfeleik világszintű tapasztalatait. [A fordítási](translation.md) képességeink felülmúlhatatlanok. Mi lehet lefordítani audio-to-audio vagy audio-to-text számos locales.

### <a name="text-to-speech"></a>Szövegfelolvasás

[A szövegfelolvasás](text-to-speech.md) egy másik fontos terület az ügyfelekkel interakcióba lépő robotok megvalósításában. A tipikus út az, hogy az ügyfél beszél, a hangát átírják a szövegbe, a szöveg elemzése a szándékok, a válasz szintetizálódik alapján a felismert szándék, majd egy eszköz vagy felszínre az ügyfél, vagy egy szintetizált hangválasz Generált. Természetesen mindez gyorsan meg történik – így az alacsony késleltetés fontos eleme ezeknek a rendszereknek a sikerében.

A végpontok között késleltetés ünk jelentősen alacsony az olyan különböző technológiák esetében , mint a [szöveggé ,](speech-to-text.md) [a LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/), a [Bot Framework](https://dev.botframework.com/), [a Text-to-speech](text-to-speech.md).

Az új hangjaink is megkülönböztethetetlenek az emberi hangoktól. Használhatja a hangunkat, hogy a bot egyedülálló személyiség.

### <a name="search"></a>Keresés

Az elemzés másik alappillére az olyan interakciók azonosítása, amelyekben egy adott esemény vagy élmény történt. Ez általában történik az egyik a két megközelítés; vagy egy ad hoc keresés, ahol a felhasználó egyszerűen beír egy kifejezést, és a rendszer válaszol, vagy egy strukturáltabb lekérdezést, ahol az elemző létrehozhat egy logikai kimutatásokat, amelyek azonosítják a forgatókönyvet egy hívásban, majd minden hívás indexelhető az adott lekérdezéskészlethez. Egy jó keresési példa a mindenütt jelenlévő megfelelőségi nyilatkozat "ezt a felhívást fel kell jegyezni minőségi célokra ... ". Sok vállalat szeretné, hogy megbizonyosodjon arról, hogy az ügynökök biztosítják ezt a felelősséget kizáró nyilatkozatot az ügyfelek előtt a hívás ténylegesen rögzíteni. A legtöbb elemzési rendszer képes a lekérdezési/keresési algoritmusok által talált viselkedéstrend-trendeket feltrendre, és a trendek jelentése végső soron az elemzési rendszer egyik legfontosabb funkciója. A [Cognitive Services címtáron](https://azure.microsoft.com/services/cognitive-services/directory/search/) keresztül a végpontok között megoldás jelentősen fokozható az indexelési és keresési lehetőségekkel.

### <a name="key-phrase-extraction"></a>Kulcskifejezések kinyerése

Ez a terület az egyik legnagyobb kihívást jelentő elemzési alkalmazás, amely a mi és a gépi tanulás alkalmazásából profitál. Ebben az esetben az elsődleges forgatókönyv az ügyfél szándékának kikövetkeztetése. Miért hív az ügyfél? Mi az ügyfél probléma? Miért volt az ügyfélnek negatív tapasztalata? [Szövegelemzési szolgáltatásunk](https://azure.microsoft.com/services/cognitive-services/text-analytics/) egy sor elemzést biztosít a teljes körű megoldás gyors frissítéséhez a fontos kulcsszavak vagy kifejezések kinyeréséhez.

Most egy kicsit részletesebben vessünk részt a kötegelt feldolgozásés a beszédfelismerés valós idejű folyamatai között.

## <a name="batch-transcription-of-call-center-data"></a>A call center adatainak kötegelt átírása

A tömeges hang átírásához kifejlesztettük a [Batch Transcription API-t.](batch-transcription.md) A Batch Transcription API-t úgy fejlesztették ki, hogy nagy mennyiségű hangadatot aszinkron módon átírjon. A call center adatok átírása tekintetében a megoldásunk a következő pilléreken alapul:

- **Pontosság** - A negyedik generációs Unified modellekkel felülmúlhatatlan transzkripciós minőséget kínálunk.
- **Késés** – Megértjük, hogy tömeges átírások során az átiratok gyorsan szükségesek. A [batch-transzkripciós API-n](batch-transcription.md) keresztül kezdeményezett átírási feladatok azonnal várólistára kerülnek, és amint a feladat futása elindul, gyorsabban hajtják végre, mint a valós idejű átírás.
- **Biztonság** – Megértjük, hogy a hívások bizalmas adatokat tartalmazhatnak. Biztos lehet benne, hogy a biztonság az egyik legfontosabb prioritásunk. Szolgáltatásunk ISO, SOC, HIPAA, PCI minősítéseket kapott.

A hívásközpontok naponta nagy mennyiségű hangadatot generálnak. Ha a vállalkozás a telefonos adatokat egy központi helyen tárolja, például az Azure Storage-ban, a [Batch Transcription API-val](batch-transcription.md) aszinkron módon kérheti és fogadhatja az átiratokat.

Egy tipikus megoldás a következő szolgáltatásokat használja:

- A beszédszolgáltatás a beszéd-szöveg átírására szolgál. A függőbeszédszolgáltatás hoz egy standard előfizetés (S0) szükséges a Batch Transcription API használatához. Az ingyenes előfizetések (F0) nem fognak működni.
- [Az Azure Storage](https://azure.microsoft.com/services/storage/) a telefonos adatok tárolására szolgál, és a Batch Transcription API által visszaadott átiratok. Ennek a tárfióknak értesítéseket kell használnia, különösen új fájlok hozzáadásakor. Ezek az értesítések az átírási folyamat indítására szolgálnak.
- [Az Azure Functions](https://docs.microsoft.com/azure/azure-functions/) a megosztott hozzáférés-hozzáférés-aláírások (SAS) URI-k létrehozására szolgál minden egyes felvételhez, és elindítja a HTTP POST-kérelmet egy átírás elindításához. Emellett az Azure Functions segítségével kérelmeket hozhat létre a Batch Transcriptionapi használatával az átírások lekéréséhez és törléséhez.

Belsőleg a fenti technológiákat használjuk a Microsoft ügyfélhívásainak batch módban történő támogatásához.
![Kötegelt architektúra](media/scenarios/call-center-batch-pipeline.png)

## <a name="real-time-transcription-for-call-center-data"></a>Valós idejű átírás a hívásközpont adataihoz

Egyes vállalkozásoknak valós időben kell átírniuk a beszélgetéseket. A valós idejű átírás használható a kulcsszavak azonosítására és a beszélgetéshez kapcsolódó tartalmak és erőforrások keresésének elindítására, a hangulat figyelésére, a hozzáférhetőség javítására, vagy fordítások nyújtására a nem natív ügyfelek és ügynökök számára Hangszórók.

A valós idejű átírást igénylő esetekben a [beszédfelismerési SDK](speech-sdk.md)használatát javasoljuk. Jelenleg a beszédfelismerés több [mint 20 nyelven](language-support.md)érhető el, az SDK pedig C++, C#, Java, Python, Node.js, Objective-C és JavaScript nyelven. A minták a [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk)on minden nyelven elérhetők. A legfrissebb híreket és frissítéseket a [Kibocsátási megjegyzések című témakörben található.](releasenotes.md)

Belsőleg a fenti technológiákat használjuk a Microsoft valós idejű hívásainak elemzésére, amint azt az alábbi ábra is szemlélteti.

![Kötegelt architektúra](media/scenarios/call-center-reatime-pipeline.png)

## <a name="a-word-on-ivrs"></a>Egy szó az IVRs

A beszédfelismerési szolgáltatás könnyen integrálható bármely megoldásba a [Speech SDK](speech-sdk.md) vagy a [REST API](rest-apis.md)használatával. A hívásközpont átírása azonban további technológiákat igényelhet. Általában az IVR-rendszer és az Azure közötti kapcsolat szükséges. Bár nem kínálunk ilyen összetevőket, itt van egy leírás, amit az IVR-hez való kapcsolat jár.

Számos IVR vagy telefonos szolgáltatás termékek (például a Genesys vagy AudioCodes) kínálnak integrációs képességek, amelyek segítségével lehetővé teszi a bejövő és kimenő hang-átvitel egy Azure-szolgáltatás. Alapvetően egy egyéni Azure-szolgáltatás előfordulhat, hogy egy adott felületet a telefonhívás-munkamenetek (például a hívás kezdete vagy a hívás vége) meghatározásához, és egy WebSocket API-t tesz elérhetővé a beszédfelismerési szolgáltatással használt bejövő adatfolyam-hang fogadásához. A kimenő válaszok, például a beszélgetésátírás vagy a Bot Framework-kapcsolat, szintetizálhatók a Microsoft szövegfelolvasó szolgáltatásával, és visszaadhatók az IVR-nek lejátszáscéljából.

Egy másik forgatókönyv a közvetlen integráció a munkamenet-kezdeményezési protokoll (SIP) protokollal. Egy Azure-szolgáltatás csatlakozik egy SIP-kiszolgálóhoz, így egy bejövő adatfolyamot és egy kimenő adatfolyamot kap, amely a beszéd-szöveg és a szövegfelolvasás fázisaihoz használatos. A SIP-kiszolgálóhoz való csatlakozáshoz vannak kereskedelmi szoftverajánlatok, például az Ozeki SDK vagy [a Teams hívási és értekezleti API](/graph/api/resources/communications-api-overview) (jelenleg béta verzióban), amelyek az ilyen típusú forgatókönyvek támogatását szolgálják a hanghívásokhoz.

## <a name="customize-existing-experiences"></a>Meglévő élmények testreszabása

 A beszédszolgáltatás jól működik a beépített modellekkel. Előfordulhat azonban, hogy tovább szeretné testreszabni és finomítani a termékvagy környezet felhasználói élményét. A testreszabási lehetőségek az akusztikai modellhangolástól a márkához kapcsolódó egyedi hangbetűtípusokig terjednek. Miután készített egy egyéni modellt, használhatja azt a beszédfelismerési szolgáltatás bármely szolgáltatásával valós idejű vagy kötegelt módban.

| Speech szolgáltatás | Modell | Leírás |
| -------------- | ----- | ----------- |
| Diktálás | [Akusztikai modell](how-to-customize-acoustic-models.md) | Hozzon létre egy egyéni akusztikai modellt olyan alkalmazásokhoz, eszközökhöz vagy eszközökhöz, amelyeket bizonyos környezetekben, például autóban vagy gyári szinten használnak, és amelyek mindegyike meghatározott felvételi feltételekkel van. Ilyenek például az ékezetes beszéd, a konkrét háttérzajok, vagy egy adott mikrofon használata a felvételhez. |
|                | [Nyelvi modell](how-to-customize-language-model.md) | Hozzon létre egy egyéni nyelvi modellt az iparág-specifikus szókincs és nyelvtan , például az orvosi terminológia vagy az informatikai zsargon átírásának javításához. |
|                | [Kiejtési modell](how-to-customize-pronunciation.md) | Egyéni kiejtési modellel meghatározhatja a fonetikus űrlapot, és megjelenítheti egy szót vagy kifejezést. Ez akkor hasznos, kezelésére szabott kifejezések, mint például a termék nevét vagy rövidítések. Az első lépésekhez mindössze egy kiejtési fájlra van szükség, amely egy egyszerű `.txt` fájl. |
| Szövegfelolvasás | [Hangtípusok](how-to-customize-voice-font.md) | Az egyéni hangbetűtípusok lehetővé teszik, hogy felismerhető, egyedi hangot hozzon létre a márkája számára. A kezdéshez csak kis mennyiségű adat szükséges. Minél több adatot ad meg, annál természetesebb és emberibb lesz a hangbetűtípusa. |

## <a name="sample-code"></a>Mintakód

Mintakód érhető el a GitHubon a beszédfelismerési szolgáltatás egyes funkcióihoz. Ezek a minták olyan gyakori forgatókönyveket fednek le, mint a hang fájlból vagy adatfolyamból történő olvasása, a folyamatos és az egyesélyes felismerés, valamint az egyéni modellek kezelése. Az alábbi hivatkozások segítségével megtekintheti az SDK- és REST-mintákat:

- [Szöveggé és beszédfordítási mintákból (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Tételtranszkripciós minták (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Szövegfelolvasó minták (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Referenciadokumentumok

- [Beszéd SDK](speech-sdk-reference.md)
- [Beszédeszközök SDK](speech-devices-sdk.md)
- [REST API: Beszéd-szöveg](rest-speech-to-text.md)
- [REST API: Szövegfelolvasás](rest-text-to-speech.md)
- [REST API: Kötegátírás és testreszabás](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ingyenes beszédfelismerési szolgáltatás-előfizetési kulcs beszerezni](get-started.md)
