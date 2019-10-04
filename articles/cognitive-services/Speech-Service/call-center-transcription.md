---
title: Call Center transzkripció-Speech szolgáltatás
titleSuffix: Azure Cognitive Services
description: A beszédfelismerés gyakori forgatókönyve nagy mennyiségű telefonos adatok átírása, amelyek különböző rendszerekről származnak, mint például az interaktív hangválasztó (IVR). A hang lehet sztereó vagy monó, a kimenet pedig nem végezhető el a jeleknél. A Speech Services és az egyesített beszédfelismerési modell használatával a vállalatok kiváló minőségű átírásokat nyerhetnek, és számos hangrögzítési rendszerrel rendelkeznek.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: b7c7bfffb5ddf947dc9bd25e6828e2816a7325cd
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559735"
---
# <a name="speech-services-for-telephony-data"></a>Beszédfelismerési szolgáltatások a telefonos szolgáltatásokhoz

A vezetékes, mobil telefonok és rádiók által generált telefonos adatok jellemzően alacsony minőségűek és 8 KHz-es tartományba tartoznak, ami kihívást jelent a beszédfelismerés szövegének konvertálásakor. Az Azure Speech Services Excel legújabb beszédfelismerési modelljei a telefonos adatok átírásakor még abban az esetben is, ha az adatok nehezen érthetők. Ezek a modellek nagy mennyiségű telefonos adattal vannak betanítva, és a legjobb piaci felismerési pontossággal rendelkeznek, akár zajos környezetekben is.

A beszédfelismerés gyakori forgatókönyve nagy mennyiségű telefonos adatok átírása, amelyek különböző rendszerekről származnak, mint például az interaktív hangválasztó (IVR). Az ezekhez a rendszerekhez tartozó hang lehet sztereó vagy monó, a jeleknél pedig nem végezhető el a RAW. A Speech Services és az egyesített beszédfelismerési modell használatával a vállalatok kiváló minőségű átírásokat nyerhetnek, függetlenül a hangrögzítéshez használt rendszertől.

A telefonos adat használatával jobban megismerheti az ügyfelek igényeit, azonosíthatja az új értékesítési lehetőségeket, vagy kiértékelheti a Call Center-ügynökök teljesítményét. Az adatok beírása után a vállalatok a kimenetet a jobb telemetria, a kulcsfontosságú kifejezések azonosítására vagy az ügyfelek hangulatának elemzésére használhatják.

Az ezen a lapon található technológiákat a Microsoft belsőleg végzi a különböző támogatási hívás-feldolgozási szolgáltatások esetében, valós időben és kötegelt módban egyaránt.

Vizsgáljuk meg a technológia és a kapcsolódó funkciók Azure Speech Services-ajánlatát.

> [!IMPORTANT]
> A Speech Services egyesített modellje változatos adatokkal van ellátva, és egyetlen modell megoldást kínál a telefonos elemzések diktálásának számos forgatókönyvére.

## <a name="azure-technology-for-call-centers"></a>Azure-technológia a call centerek számára

A beszédfelismerési szolgáltatások funkcionális aspektusán felül az elsődleges céljuk – ha a Call Center használatára van alkalmazva –, hogy javítsa a felhasználói élményt. Három egyértelmű tartomány létezik ebben a tekintetben:

* Hívás utáni elemzés, amely a hívási felvételek kötegelt feldolgozását
* A hangjelek valós idejű elemzése a különböző elemzések kinyeréséhez, ahogy a hívás zajlik (a hangulat Kiemelt használati eset), és
* Virtuális asszisztensek (bots), vagy az ügyfél és a robot közötti párbeszédet próbálják megoldani, hogy az ügyfél nem vesz részt az ügynök részvételével, vagy hogy az AI-protokollok alkalmazásával segítséget nyújtson az ügynöknek.

A Batch-forgatókönyv megvalósításának tipikus architektúrás ábrája a Call Center transzkripciós Architecture alábbi ![ábrán látható.](media/scenarios/call-center-transcription-architecture.png)

## <a name="speech-analytics-technology-components"></a>A Speech Analytics technológiai összetevői

Ha a tartomány hívás vagy valós idejű, az Azure számos fejlett és feltörekvő technológiával rendelkezik, amelyek javítják a felhasználói élményt.

### <a name="speech-to-text-stt"></a>Beszéd szövege (STT)

A hívási központ bármely megoldásában a leggyakrabban keresett [beszéd – szöveg](speech-to-text.md) . Mivel számos alsóbb rétegbeli elemzési folyamat támaszkodik az átadott szövegre, a Word-hibák aránya (WER) rendkívül fontos. A Call Center átiratának egyik fő kihívása a Call Centerben elterjedt zaj (például a háttérben megjelenő más ügynökök), a különböző nyelvi területi beállítások és dialektusok, valamint a tényleges telefonos jel alacsony minősége. A WER szorosan összefügg azzal, hogy az akusztikai és nyelvi modellek hogyan vannak betanítva egy adott területi beállításhoz, ezért fontos, hogy a modell testreszabható legyen a területi beállításhoz. A legújabb, egységes 4-es verziójú 4. x modellek az átírás pontosságát és késését jelentik. Több tízezer akusztikus adatmennyiséggel és több milliárd lexikális információval rendelkezik, és az egységes modellek a legpontosabb modellek a piacon a Call Center adatainak átírásához.

### <a name="sentiment"></a>Hangulat
Annak mérése, hogy az ügyfél jó tapasztalattal rendelkezik-e a Speech Analytics egyik legfontosabb területén, ha alkalmazva van a Call Center területére. A [Batch](batch-transcription.md) -átírási API-ban a vélemények elemzését is kimondjuk. Összeállíthatja a hívási átirat részeként kapott értékek halmazát, hogy meghatározza az ügynökök és az ügyfél hívásának hangulatát.

### <a name="silence-non-talk"></a>Csend (nem beszélő)
Nem ritka, hogy egy támogatási hívás 35%-ában a nem beszélgetési időpontot nevezzük. Egyes forgatókönyvek nem beszélhetünk a következőkről: az ügynökökkel kapcsolatos korábbi előzményeket felkereső ügynökök, az eszközöket használó ügynökök, amelyek lehetővé teszik számukra az ügyfél asztalának elérését, valamint a funkciók elvégzését, az ügyfelek számára az átvitelre váró várakozásokat és így tovább. Nagyon fontos, hogy mérje fel a csendet a hívás során, mert számos fontos ügyfél-érzékenység fordul elő, amelyek az ilyen típusú forgatókönyvek körül történnek, és amelyek a hívás során történnek.

### <a name="translation"></a>Fordítás
Néhány vállalat kísérletezik, hogy idegen nyelvekről származó lefordított átiratokat biztosít, így a kézbesítési vezetők megismerhetik az ügyfelek globális felhasználói élményét. A [fordítási](translation.md) képességek felülmúlhatatlanok. A hanganyagot hangra vagy hangra is lefordíthatja nagy számú területi beállításból származó szövegre.

### <a name="text-to-speech"></a>Szövegfelolvasás
A [szöveg és a beszéd](text-to-speech.md) egy másik fontos része az ügyfelekkel kommunikáló robotok megvalósításának. A tipikus elérési út az, hogy az ügyfél beszél, a hangja szövegbe kerül, a szöveget pedig a szándékok alapján elemezzük, a rendszer a felismert szándék alapján szintetizálja a választ, majd egy adategységet az ügyfélnek vagy egy szintetizált hangválasztó létrehozott. Természetesen mindeznek gyorsan kell történnie – így a késés a rendszerek sikeres sikerességének fontos eleme.

A végpontok közötti késés meglehetősen alacsony, figyelembe véve az olyan különböző technológiákat, mint például a [beszéd-szöveg](speech-to-text.md), a [Luis](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/), a [bot Framework](https://dev.botframework.com/), a [szöveg és a beszéd](text-to-speech.md).

Az új hangok is megkülönböztetni az emberi hangokat. Felhasználhatja a hangokat, hogy a robotja egyedi személyisége legyen.

### <a name="search"></a>Keresés
Az elemzések egy másik része az olyan interakciók azonosítása, amelyekben egy adott esemény vagy tapasztalat történt. Ez általában két módszer egyikével történik, vagy egy ad hoc keresésre, ahol a felhasználó egyszerűen begépel egy kifejezést és a rendszer válaszait, vagy egy strukturált lekérdezést, ahol egy elemző olyan logikai utasításokat hozhat létre, amelyek azonosítják a hívást. , majd minden hívást indexelni lehet a lekérdezésekkel. Jó keresési példa a mindennapos megfelelőségi nyilatkozat, amely szerint a rendszer a felhívást minőségi célokra rögzíti... "– minél több vállalat szeretné meggyőződni arról, hogy az ügynökök a hívás tényleges rögzítése előtt biztosítják ezt a jogi nyilatkozatot az ügyfeleknek. A legtöbb elemzési rendszer képes a lekérdezési/Search algoritmusok által észlelt viselkedések trendjeire, mivel a trendek ezen jelentése végső soron az analitikai rendszer legfontosabb funkcióinak egyike. A [kognitív szolgáltatások címtárában](https://azure.microsoft.com/services/cognitive-services/directory/search/) a végpontok közötti megoldás jelentősen fokozható az indexelési és keresési képességekkel.

### <a name="key-phrase-extraction"></a>Kulcskifejezések kinyerése
Ez a terület az egyik nagyobb kihívást jelentő elemzési alkalmazás, amely az AI és a ML alkalmazásából származik. Az elsődleges forgatókönyv az, hogy az ügyfél szándékát következteti ki. Miért hívja meg az ügyfelet? Mi az ügyfél problémája? Miért adta meg az ügyfél negatív felhasználói élményt? A [text Analytics szolgáltatás](https://azure.microsoft.com/services/cognitive-services/text-analytics/) számos elemzést biztosít a végpontok közötti gyors verziófrissítéshez a legfontosabb kulcsszavak vagy kifejezések kinyeréséhez.

Most nézzük meg a kötegelt feldolgozást és a beszédfelismerés valós idejű folyamatait egy kicsit részletesebben.

## <a name="batch-transcription-of-call-center-data"></a>A Call Center-adatfeldolgozás kötegelt átírása

A hanganyag nagy részét a [Batch átírási API](batch-transcription.md)-ját fejlesztettük ki. A Batch-átírási API úgy lett kifejlesztve, hogy a nagy mennyiségű hanganyagot aszinkron módon átmásolja. A Call Center-beli adatbevitel tekintetében a megoldás a következő pilléreken alapul:

* **Pontosság**: A negyedik generációs egyesített modellek esetében páratlan átírási minőséget biztosítunk.
* **Késés**: Tisztában vagyunk azzal, hogy a tömeges átírások során a rendszer gyorsan átírásokat igényel. A [Batch átírási API](batch-transcription.md) -n keresztül kezdeményezett átírási feladatok azonnal várólistára kerülnek, és a feladat futásának megkezdése után a valós idejű átírásnál gyorsabban végezhető el.
* **Biztonság**: Tisztában vagyunk azzal, hogy a hívások bizalmas adatokat is tartalmazhatnak. Biztos lehet benne, hogy a biztonság a legmagasabb prioritások egyike. Szolgáltatásunk ISO-, SOC-, HIPAA-és PCI-minősítéseket kapott.

A Call Centers nagy mennyiségű hanganyagot állít elő napi rendszerességgel. Ha a vállalat egy központi helyen, például az Azure Storage-ban tárolja a telefonos adatait, a Batch-átírási [API](batch-transcription.md) használatával aszinkron módon kérhet és fogadhat átírásokat.

Egy tipikus megoldás ezeket a szolgáltatásokat használja:

* Az Azure Speech Services használatával szövegeket lehet átírni a beszédfelismerésből. A Batch-átírási API használatához standard előfizetés szükséges a Speech Serviceshez. Az ingyenes előfizetések (F0-EK) nem fognak működni.
* Az [Azure Storage](https://azure.microsoft.com/services/storage/) szolgáltatás a telefonos és a Batch-átírási API által visszaadott átiratok tárolására szolgál. Ennek a Storage-fióknak az értesítéseket kell használnia, különösen az új fájlok hozzáadásakor. Ezek az értesítések az átírási folyamat elindítására szolgálnak.
* A [Azure functions](https://docs.microsoft.com/azure/azure-functions/) a közös hozzáférésű aláírások (SAS) URI-azonosítójának létrehozására szolgál az egyes rögzítésekhez, és a http post-kérés elindításával indíthatja el az átírást. Emellett Azure Functions a Batch-átírási API-val történő átírások beolvasására és törlésére vonatkozó kérések létrehozására szolgál.
* [](webhooks.md) A webhookok az átírások elvégzése után kapják meg az értesítéseket.

Belsőleg a fenti technológiákat használjuk a Microsoft ügyfél-hívások kötegelt módban történő támogatásához.
![Batch-architektúra](media/scenarios/call-center-batch-pipeline.png)

## <a name="real-time-transcription-for-call-center-data"></a>A Call Center-adattovábbítás valós idejű átírása

Egyes vállalkozásoknak valós időben kell átírniuk a beszélgetéseket. A valós idejű átírással azonosíthatók a beszélgetéshez kapcsolódó tartalom és erőforrások, a figyelési hangulat, a kisegítő lehetőségek, valamint a nem natív ügyfelek és ügynökök számára elérhető fordítások. hangszórók.

A valós idejű átírást igénylő forgatókönyvek esetében javasoljuk, hogy a [SPEECH SDK](speech-sdk.md)-t használja. Jelenleg a beszéd-szöveg [több mint 20 nyelven](language-support.md)érhető el, az SDK pedig a, C++ C#a Java, a Python, a Node. js, az Objective-C és a JavaScript nyelven érhető el. A minták minden nyelven elérhetők a [githubon](https://github.com/Azure-Samples/cognitive-services-speech-sdk). A legfrissebb hírek és frissítések: [kibocsátási megjegyzések](releasenotes.md).

Belsőleg a fenti technológiákat használjuk a Microsoft ügyfeleinek valós időben történő elemzéséhez.

![Batch-architektúra](media/scenarios/call-center-reatime-pipeline.png)

## <a name="a-word-on-ivrs"></a>Egy szó a IVRs

A Speech Services egyszerűen integrálható bármilyen megoldásba a [SPEECH SDK](speech-sdk.md) vagy a [REST API](rest-apis.md)használatával. A Call Center átirata azonban további technológiákat igényelhet. Általában az IVR rendszer és az Azure közötti kapcsolat szükséges. Bár ezek az összetevők nem biztosítanak ilyen összetevőket, szeretnénk leírni, hogy az IVR-kapcsolatok milyen módon járnak.

Számos IVR-vagy telefonos szolgáltatási termék (például a Genesys vagy a AudioCodes) olyan integrációs képességeket kínál, amelyek lehetővé teszik a bejövő és kimenő hangátvitelt az Azure-szolgáltatásba. Az egyéni Azure-szolgáltatás alapvetően egy adott felületet biztosít a telefonhívási munkamenetek definiálásához (például a hívás indítására vagy a hívás végére), és elérhetővé tehet egy WebSocket API-t, amely a beszédfelismerési szolgáltatásokhoz használt bejövő adatfolyam-hangvételt fogadja. A kimenő válaszok, például a beszélgetés átírása vagy a bot-keretrendszerrel létesített kapcsolatok a Microsoft szöveg-beszéd szolgáltatásával állíthatók be, és visszatérhetnek az IVR-hoz a lejátszáshoz.

Egy másik forgatókönyv közvetlen SIP-integráció. Az Azure-szolgáltatás egy SIP-kiszolgálóhoz csatlakozik, így a bejövő adatfolyamot és egy kimenő adatfolyamot kap, amely a beszéd-szöveg és a szöveg-beszéd fázisokhoz használatos. A SIP-kiszolgálóhoz való kapcsolódáshoz olyan kereskedelmi szoftverek állnak rendelkezésre, mint például a Ozeki SDK, vagy [a csapatok hívása és értekezletek API](https://docs.microsoft.com/graph/api/resources/calls-api-overview?view=graph-rest-beta) (jelenleg bétaverzióban), amelyek a hanghívások ilyen típusú forgatókönyvének támogatására lettek kialakítva.

## <a name="customize-existing-experiences"></a>Meglévő tapasztalatok testreszabása

Az Azure Speech Services jól működik a beépített modellekkel, azonban érdemes lehet tovább testreszabni és hangolni a termék vagy a környezet élményét. A testreszabási lehetőségek köre az akusztikus modell finomhangolása és a márka egyedi hangbetűkészletei között. Miután létrehozott egy egyéni modellt, az Azure Speech Services bármelyikével valós időben vagy batch módban is használhatja.

| Beszédfelismerési szolgáltatás | Modell | Leírás |
|----------------|-------|-------------|
| Speech-to-text | [Akusztikai modell](how-to-customize-acoustic-models.md) | Hozzon létre egy egyéni akusztikai modellt olyan alkalmazásokhoz, eszközökhöz vagy eszközökhöz, amelyeket az adott környezetben, például autóban vagy gyári szinten használnak, és amelyek külön rögzítési feltételekkel rendelkeznek. Ilyenek például az ékezetes beszédek, a konkrét háttérzajok, vagy egy adott mikrofon használata a rögzítéshez. |
| | [Nyelvi modell](how-to-customize-language-model.md) | Hozzon létre egy egyéni nyelvi modellt, amellyel javítható az iparágra jellemző szókincs és nyelvtan, például az orvosi szakkifejezések vagy az informatikai szakzsargonk átírása. |
| | [Kiejtési modell](how-to-customize-pronunciation.md) | Egyéni kiejtési modellel megadhatja egy szó vagy kifejezés fonetikus formáját és megjelenítését. Ez hasznos testre szabott feltételek, például termékneveket vagy betűszavakat kezelése. A kezdéshez szüksége egy írásmódja fájlnevével – egy egyszerű .txt fájlt. |
| Szövegfelolvasás | [Hangtípusok](how-to-customize-voice-font.md) | Az egyéni hangbetűkészletek lehetővé teszik a márka felismerhető, egyfajta hangjának létrehozását. A kezdéshez csak kis mennyiségű adat szükséges. Minél több, az Ön által megadott információ, annál természetesebb és emberi – például a hangbetűkészletek is megszólalnak. |

## <a name="sample-code"></a>Mintakód

A mintakód minden Azure Speech Services esetében elérhető a GitHubon. Ezek a minták olyan gyakori forgatókönyveket érintenek, mint például a hang fájlból vagy streamből való olvasása, a folyamatos és az egyszeri felvétel felismerése, valamint az egyéni modellek használata. Az alábbi hivatkozásokkal megtekintheti az SDK-t és a REST-mintákat:

* [Beszéd – szöveg és beszéd fordítási minták (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Batch-átírási minták (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [Szöveg – beszéd minták (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Segédanyagok

* [Beszéd SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: Speech-to-text](rest-speech-to-text.md)
* [REST API: Szöveg – beszéd](rest-text-to-speech.md)
* [REST API: Kötegelt átírás és testreszabás](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ingyenes Speech Services-előfizetési kulcs beszerzése](get-started.md)
