---
title: Call Center Beszédátírási – beszédszolgáltatások
titleSuffix: Azure Cognitive Services
description: Egy általános forgatókönyv a hang-szöveg transzformációs van átírás nagy adatmennyiségek telefonos, amely a különböző rendszerek, például az interaktív hangalapú válasz (IVR) származhatnak. Lehet, hogy a hanganyag sztereó vagy Monó, és a jel kis nem utófeldolgozás a nyers végrehajtani. Beszédszolgáltatások és az egyesített beszédmodellel használ, egy üzleti kaphat kiváló minőségű beszédátírás, számos rendszerhang rögzítése rendszerekkel.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 055d141cab8ece3fcb462573f6ed4d8941c19751
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67064126"
---
# <a name="speech-services-for-telephony-data"></a>A telefonos adatok beszédszolgáltatások

A telefonos landlines mobiltelefonokról és a rádiófrekvenciás kapcsolatokat keresztül létrehozott adatok a következők: jellemzően korlátozott minőség és sávú (8 kHz), amely kihívásokat hoz létre, hang-szöveg konvertálása során tartományán. A legújabb beszédfelismerési modelleket az Azure Speech Services búcsút lefényképezheti a telefonos adatokat, még akkor is azokban az esetekben, amikor az adatok egy emberi megértése nehéz. Ezek a modellek telefonos nagy adatmennyiségek képzett, és piaci pontosságát, még akkor is, zajos környezetek a legjobban rendelkezik.

Egy általános forgatókönyv a hang-szöveg transzformációs van átírás nagy adatmennyiségek telefonos, amely a különböző rendszerek, például az interaktív hangalapú válasz (IVR) származhatnak. Ezek a rendszerek adja meg a hanganyag lehet sztereó vagy Monó, és nyers kis nem utófeldolgozás történik, a jel. Beszédszolgáltatások és az egyesített beszédmodellel használ, egy üzleti is magas színvonalú beszédátírás, függetlenül a rendszerek beolvasása hang rögzítése használja.

A telefonos adatok segítségével jobban megismerheti az ügyfelek igényeinek megfelelő, új értékesítési lehetőségek vagy hívás center-ügynökök teljesítményét értékeli. Miután az adatok a megjelenített érzéseket, egy üzleti használhatja a kimeneti továbbfejlesztett telemetria, kulcskifejezéseket azonosítására, illetve az ügyfelek hangulatának elemzéséhez.

Az ezen a lapon ismertetett technológiák is a Microsoft által belsőleg különböző támogatási hívás a feldolgozási szolgáltatások, mind a valós idejű és kötegelt módban.

Tekintsük át néhány a technológia és a kapcsolódó szolgáltatásokat Azure Speech Services ajánlat.

> [!IMPORTANT]
> Szolgáltatások egységes beszédmodellel változatos adatokkal be van tanítva, és egyetlen modell megoldást kínál a forgatókönyvben számos, a Diktálás telefonos Analytics.

## <a name="azure-technology-for-call-centers"></a>Az Azure technológiát hívás központok

A beszédszolgáltatások működési aspektusa túli elsődleges célja – ügyfélszolgálatával – alkalmazásakor, a felhasználói élmény javítása érdekében. Három egyértelmű tartományok e tekintetben létezik:

* Utáni hívás analytics, batch-hívás felvételek feldolgozása 
* A hang jelzés különböző információk kinyerése, mivel a hívás lefolyása (az folyamatban van egy neves alkalmazási helyzet vélemények) valós idejű elemzések feldolgozását és
* Virtuális asszisztensek (Robotokat), vagy az ügyfél és a robot közötti párbeszéd vezetési oldja meg a probléma nincs ügynök részvételével tett kísérlet, vagy folyamatban van az alkalmazás az AI protokollok, amelyek segítik az ügynököt.

Az alábbi képen egy batch-forgatókönyv végrehajtása egy tipikus Architektúradiagram fejezet ![hívás center beszédátírási architektúra](media/scenarios/call-center-transcription-architecture.png)

## <a name="speech-analytics-technology-components"></a>Beszéd analitikai technológia összetevők

Hogy a tartomány utáni hívás vagy a valós idejű, az Azure a felhasználói élmény javításán technológiák érett és újonnan felbukkanó csoportja készletét kínálja. 

### <a name="speech-to-text-stt"></a>Diktálás (STT) 

[Hang-szöveg transzformációs](speech-to-text.md) után a szolgáltatás hívást center megoldásban a egészíti van. Alsóbb rétegbeli elemzési folyamatok átírt szöveg támaszkodhat, mivel a word Hibaarány (WER) van, rendkívül fontos. Hívás center beszédátírási kulcsfontosságú kihívása, nevezetesen egyik, a háttérzaj, amely elterjedt a ügyfélszolgálatával (például más ügynökök, és beszéljen a háttérben), a gazdag különböző nyelvű területi beállításokhoz és dialektusok, valamint a gyenge minőségű, a tényleges telefon jel. A WER magas összefügg az akusztikai és nyelvi modelleket képzett arról, hogy egy adott területi beállításhoz tartozó, így tudja a modellt a területi beállítások testreszabása fontos. Az egyesített verzió legújabb 4.x modellek a megoldás beszédátírási pontosság és a késés is. Több tíz képzett óra akusztikai adathoz, több ezer és lexikális információk egyesített milliárd modellek a piacon elérhető, a hívás center átvezeti a legpontosabb modellek.

### <a name="sentiment"></a>Hangulat
Mérésére, hogy az ügyfél kellett jó élményt az egyik, a beszéd elemzését, a hívás center terület alkalmazásakor a legfontosabb területeket. A [Batch Beszédátírási API](batch-transcription.md) hangulatelemzés kiszolgálónként utterance (kifejezés) kínál. Értékek meghatározásához a vélemény a hívás az ügynökök és az ügyfél egy hívás átiratok részeként kapott összesítheti.

### <a name="silence-non-talk"></a>Csend (nem előadás)
már nem ritka, hogy a támogatási hívás, úgynevezett nem beszédidő kell 35 %-os. Bizonyos alkalmazási akkor fordul elő, mely nem előadás: az ügyfél előzetes eset előzmények keresésekor ügynökök ügynökök eszközökkel, ami lehetővé teszi az ügyfél asztali eléréséhez, és végezze el a függvények, úgy ügyfelek tartsa Várakozás az átvitel és így tovább. Rendkívül fontos, hogy amikor csend hívással történik, mert nincsenek is mérőműszer, amely körül a forgatókönyvek az ilyen típusú fordulhat elő, és ahol a hívást a előforduló fontos ügyfél övvisszahúzónál száma.

### <a name="translation"></a>Fordítás
Egyes vállalatok kísérletezünk biztosít a lefordított szövegekben az idegen nyelvű segélykérő hívások számát, hogy a kézbesítési kezelők képes megérteni az ügyfeleknek világszerte élménye. A [fordítási](translation.md) funkciók a következők unsurpassed. Azt is fordítani hang-, hang- és audiotartalmak szöveggé területi beállítás nagy számú.

### <a name="text-to-speech"></a>Szövegfelolvasás
[Szöveg-hang transzformációs](text-to-speech.md) egy másik fontos terület, az ügyfeleknek a kommunikáló robotokat végrehajtása során. A tipikus út lesz, hogy az ügyfél beszél, beszéddel a megjelenített érzéseket szöveggé, leképezések az elemzett szöveg, választ van synthesized alapján a felismert szándékkal, és majd egy eszköz vagy illesztett az ügyfél vagy egy szintetizált válasz hozza létre. Természetesen ez összes rendelkezik gyorsan – történik, így a késés az ezekben a rendszerekben sikeres fontos összetevője. 

A végpontok közötti késés viszonylag kevés, figyelembe véve a különböző technológiákkal, mint például az érintett [hang-szöveg transzformációs](speech-to-text.md), [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/), [Bot Framework](https://dev.botframework.com/), [ Szöveg-hang transzformációs](text-to-speech.md). 

Az új beszédhangot is rendelkezésre állnak az emberi beszédhangot megkülönböztetni. Tegye robotjait az egyedi személyiségelemző használható beszédhangot ki.

### <a name="search"></a>Keresés
Az elemzés egy másik tű interakciók azonosításához, ahol egy adott esemény vagy élmény történt. Ez általában két megközelítés, vagy egy ad-hoc keresés ahol a felhasználó egyszerűen beír egy kifejezés reagál a rendszer, és strukturáltabb lekérdezéssel, az elemzőknek hozható létre egy logikai utasítások készletét, amely egy forgatókönyv-hívással azonosítása egyikével történik , és ezután minden hívás lekérdezések ezen készlete alapján lehet indexelni. A helyes keresési például, a széles körben használt kompatibilitási nyilatkozat "a hívás rögzítik minőségi célokra... "–, sok vállalat szeretne győződjön meg arról, hogy az ügynökök ad a jogi nyilatkozat az ügyfelek számára, mielőtt a hívás tényleges rögzíti. A legtöbb elemzési rendszerek tudnak viselkedések található lekérdezési /search algoritmusokkal – trendek, ehhez a jelentéshez a trendek egyike végső soron az analytics rendszer legfontosabb funkcióit. Keresztül [Cognitive services – katalógus](https://azure.microsoft.com/services/cognitive-services/directory/search/) a teljes körű megoldás lehetőségei jelentősen bővülnek indexelési és keresési képességekkel.

### <a name="key-phrase-extraction"></a>Kulcskifejezések kinyerése
Ez a terület az egyik a izgalmasabb adatelemzési alkalmazások futtathatók, a másik mesterséges Intelligencia és gépi tanulás alkalmazásával részesül. Az elsődleges forgatókönyvet, hogy az ügyfél céljaira kikövetkeztetni. Miért van az ügyfél hívása? Mi az az ügyfél a probléma? Miért volt az ügyfél egy negatív élmény? A [Text analytics szolgáltatás](https://azure.microsoft.com/services/cognitive-services/text-analytics/) analytics beépített gyorsan frissítéséhez ezeket a fontos kulcsszavakat vagy kifejezések kinyerése a teljes körű megoldást biztosít.

Most nézzük meg, a kötegelt feldolgozás és a valós idejű folyamatok beszédfelismerési egy kicsit részletesebben.

## <a name="batch-transcription-of-call-center-data"></a>A Batch beszédátírási hívás center adatok

A tömeges hanganyagra lefényképezheti azt fejlesztett a [Batch Beszédátírási API](batch-transcription.md). A Batch Beszédátírási API célja nagy mennyiségű adat hang lefényképezze aszinkron módon történik. Érdemes hívásadatokat center átírás, a megoldás ezen oszlopok alapul:

* **Pontosság**: Negyedik generációs egyesített modellel unsurpassed beszédátírási minőségi biztosítunk.
* **Késés**: Tisztában vagyunk vele, hogy amikor tömeges beszédátírás, a beszédátírás szükséges gyorsan. Az átírási feladatok használatával kezdeményezett a [Batch Beszédátírási API](batch-transcription.md) , azonnal várólistára kerül, és miután a feladat futni kezd, gyorsabb, mint a valós idejű beszédátírási végre.
* **Biztonság**: Tisztában vagyunk vele, hogy a hívások tartalmazhatnak bizalmas adatokat. Nyugodt lehet afelől, hogy a biztonsági egyike a összpontosítsuk. Szolgáltatásunk kapott ISO, SOC, a HIPAA, a PCI tanúsítványt.

Telefonos ügyfélszolgálatok nagy tömegű hívásaiból naponta. Ha az üzleti telefonos adatokat tárol egy központi helyen, például az Azure Storage, használhatja a [Batch Beszédátírási API](batch-transcription.md) aszinkron módon és beszédátírás fogadásához.

Egy tipikus megoldás ezeket a szolgáltatásokat használja:

* Az Azure Speech Services hang-szöveg transzformációs lefényképezze szolgálnak. A beszédszolgáltatások (egyéb) standard előfizetést kell használnia a Batch Beszédátírási API. Ingyenes előfizetés (F0) nem fog működni.
* [Az Azure Storage](https://azure.microsoft.com/services/storage/) telefonos adatokat, és az átiratok a Batch Beszédátírási API által visszaadott tárolására szolgál. Ezt a tárfiókot kell használnia az értesítéseket, különösen ha új fájlokat adnak. Ezek az értesítések használhatók az átírási folyamat aktiválása.
* [Az Azure Functions](https://docs.microsoft.com/azure/azure-functions/) minden felvétel a közös hozzáférésű jogosultságkódok (SAS) URI létrehozásához, és a egy beszédátírási elindítani a HTTP POST-kérés aktiválja. Emellett az Azure Functions beolvasásához, és törölje a Batch Beszédátírási API-val beszédátírás kérelmek létrehozására szolgál.
* [Webhookok](webhooks.md) szolgálnak, hogy értesítéseket kapjon beszédátírás végezhető el.

Belsőleg használjuk a fenti technológiák az ügyfél meghívja a Microsoft támogatja a kötegelt módban.
![Batch-architektúra](media/scenarios/call-center-batch-pipeline.png)

## <a name="real-time-transcription-for-call-center-data"></a>Hívás center adatok valós idejű beszédátírási

Egyes vállalatok valós idejű beszélgetések lefényképezze van szükség. Valós idejű beszédátírási segítségével azonosíthatja a kulcsszavakat és keresi meg a tartalom és erőforrások a beszélgetést, hangulatát, kisegítő lehetőségek javítása, illetve az ügyfelek és az ügynököket, akik nem natív fordítások figyelésére vonatkozó aktiválása előadók.

A valós idejű beszédátírási olyan esetekben, javasoljuk a [beszéd SDK](speech-sdk.md). Hang-szöveg transzformációs jelenleg elérhető [több mint 20 nyelvet](language-support.md), és az SDK-val is elérhető C++, C#, Java, Python, Node.js, Objective-C és a JavaScript. A minták érhetők el az egyes nyelvekhez [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk). A legújabb híreket és frissítéseket, lásd: [kibocsátási megjegyzések](releasenotes.md).

Belsőleg használjuk a fenti technológiák, azok a valós idejű Microsoft ügyfelek hívásainak elemzése.

![Batch-architektúra](media/scenarios/call-center-reatime-pipeline.png)

## <a name="a-word-on-ivrs"></a>Olyan szavak IVRs

Beszédszolgáltatások egyszerűen integrálható megoldásban használatával a [beszéd SDK](speech-sdk.md) vagy a [REST API-val](rest-apis.md). Hívás center beszédátírási azonban szükség lehet további technológiákat. Egy Hangalapú rendszer és az Azure közötti kapcsolatot általában megadása kötelező. Bár nem biztosítunk az ilyen összetevőket, szeretnénk egy kapcsolatot egy Hangalapú jár ismertetik.

Hangalapú vagy telefonos szolgáltatás termékekre (például Genesys vagy AudioCodes) kínálnak adatintegrációs képességeket, amelyek ahhoz, hogy a bejövő és kimenő hang csatlakoztatott egy Azure-szolgáltatáshoz jól használható. Alapvetően egy egyéni Azure-szolgáltatás biztosíthatnak egy adott illesztő határozza meg a telefonhívás munkamenetek (például a hívás Start vagy End hívja), és elérhetővé tehettük a WebSocket API-t fogadni a bejövő adatfolyam hang, a beszéd szolgáltatásokkal használt. Kimenő válaszok, például a beszélgetés átírást vagy a Bot Framework, a kapcsolatok lehetnek synthesized szöveg-beszéd átalakítás a Microsoft-szolgáltatással és a Hangalapú lejátszás céljából vissza.

Egy másik helyzet lehet közvetlen SIP-integráció. Az Azure-szolgáltatások kapcsolódik a SIP-kiszolgálóhoz, így az első egy bejövő adatfolyam és a egy kimenő stream, amely a hang-szöveg és a szöveg-hang transzformációs fázis használható. Szeretne csatlakozni egy SIP-kiszolgálóhoz van kereskedelmi szoftverek ajánlatokat Ozeki SDK-t, vagy [a hívó csapatok és értekezletek API](https://docs.microsoft.com/graph/api/resources/calls-api-overview?view=graph-rest-beta) (jelenleg a bétaverziós), amely tervezték, hogy támogatja ezt a forgatókönyvet a hang-hívásokat.

## <a name="customize-existing-experiences"></a>Meglévő felhasználói környezetet testreszabása

Az Azure Speech Services nagyszerűen működik a beépített modellek, azonban érdemes lehet további testre szabhatja és finomhangolása a termék vagy a környezet élményét. Testreszabási lehetőségek köre, a akusztikai modell finomhangolása a saját márkáját az egyedi hangtípusokkal. Miután létrehozott egy egyéni modell, használhatja az Azure Speech Services is valós időben, vagy kötegelt módban bármelyikével.

| Beszédszolgáltatás | Modell | Leírás |
|----------------|-------|-------------|
| Speech-to-text | [Akusztikai modell](how-to-customize-acoustic-models.md) | Alkalmazások, eszközök, egy egyéni akusztikai modell létrehozásához vagy eszközöket, amelyek a megadott többek között az autó vagy gyárban, mindegyike adott felvétel feltételek. Ilyenek például a ékezetes speech, adott háttér egy olyan vagy a rögzítés adott mikrofon segítségével. |
| | [Nyelvi modell](how-to-customize-language-model.md) | Hozzon létre egy egyéni nyelvi modell, iparág-specifikus szóhasználatának és nyelvtani, például terminológiát vagy informatikai szakkifejezéseivel beszédátírási javítása érdekében. |
| | [Kiejtési modell](how-to-customize-pronunciation.md) | Egy egyéni írásmódja modellel meghatározhatja fonetikus formában és megjelenített egy szó vagy kifejezés. Ez hasznos testre szabott feltételek, például termékneveket vagy betűszavakat kezelése. A kezdéshez szüksége egy írásmódja fájlnevével – egy egyszerű .txt fájlt. |
| Szövegfelolvasás | [Hangtípusok](how-to-customize-voice-font.md) | Egyéni hangtípust lehetővé teszi, hogy hozzon létre egy könnyen felismerhető névre, egy-az-maga nemében egyedülálló hang, a saját márkáját. Csak egy kis mennyiségű adatot a kezdéshez vesz igénybe. Minél több adatot ad meg, a további természetes és az emberi intelligenciára hasonlító elemeket az hangtípusú fog hang-, hogy. |

## <a name="sample-code"></a>Mintakód

Mintakód az Azure Speech Services mindegyike elérhető a Githubon. Ezek a minták például egy fájlhoz vagy adatfolyamhoz, folyamatos és egylépéses felismerés, a hang olvasásakor és egyéni modellek használata gyakori forgatókönyveket fednek le. Ezek a hivatkozások segítségével megtekintheti SDK-t és a REST-minták:

* [Hang-szöveg és beszéd fordítási minták (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [A Batch beszédátírási minták (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [Szöveg-hang transzformációs minták (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Segédanyagok

* [Beszéd SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: Speech-to-text](rest-speech-to-text.md)
* [REST API: Szöveg-hang transzformációs](rest-text-to-speech.md)
* [REST API: A Batch beszédátírási és testreszabás](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ingyenes beszédszolgáltatások előfizetési kulcs lekérése](get-started.md)
