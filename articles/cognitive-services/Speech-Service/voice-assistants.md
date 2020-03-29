---
title: Hangasszisztensek – Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: A beszédszoftver-fejlesztő isztikával (SDK) a hangsegédek funkcióinak, képességeinek és korlátozásainak áttekintése.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 9219c9a72ce9e1cfba3504b0b8e16ade77f8a5e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369896"
---
# <a name="what-is-a-voice-assistant"></a>Mi az a hangvezérelt asszisztens?

A Beszédszolgáltatást használó hangasszisztensek lehetővé teszik a fejlesztők számára, hogy természetes, emberszerű társalgási felületeket hozzanak létre alkalmazásaikhoz és felhasználói élményeikhez.

A hangsegéd szolgáltatás gyors, megbízható interakciót biztosít egy eszköz és egy asszisztens megvalósítása között, amely (1) a Bot Framework közvetlen vonali beszédcsatornáját vagy (2) az integrált egyéni parancsok (előzetes verzió) szolgáltatást használja a feladat befejezéséhez.

Az alkalmazások a beszédszoftver-fejlesztő készlettel (SDK) csatlakoznak a hangasszisztens-szolgáltatáshoz.

   ![A hangsegéd vezénylési szolgáltatásfolyamatának fogalmi diagramja](media/voice-assistants/overview.png "A hangasszisztens áramlása")

## <a name="choosing-an-assistant-solution"></a>Asszisztensi megoldás kiválasztása

A hangasszisztens létrehozásának első lépése annak eldöntése, hogy mit kell tennie. A Beszédszolgáltatás több egymást kiegészítő megoldást kínál az asszisztensi interakciók megalkotásához. Akár a Bot Framework [közvetlen vonalbeli beszédcsatornája](direct-line-speech.md) által biztosított rugalmasságot és sokoldalúságot, akár az egyszerű forgatókönyvekhez szükséges [egyéni parancsok (előzetes verzió)](custom-commands.md) egyszerűségét szeretné, a megfelelő eszközök kiválasztása a kezdéshez szükséges.

| ha akarod... | Akkor fontolja meg... | Például... |
|-------------------|------------------|----------------|
|Nyílt végű beszélgetés robusztus készségintegrációval és teljes körű üzembe helyezési ellenőrzéssel | A Bot Framework [közvetlen vonalbeszédcsatornája](direct-line-speech.md) | <ul><li>"Seattle-be kell mennem"</li><li>"Milyen pizzát rendelhetek?"</li></ul>
|Irányítás és vezérlés vagy feladatorientált beszélgetés egyszerűsített szerzői és üzemeltetési | [Egyéni parancsok (előzetes verzió)](custom-commands.md) | <ul><li>"Kapcsolja be a felső lámpát"</li><li>"Legyen 5 fokkal melegebb"</ul>

Ha még nem biztos abban, hogy az asszisztenst szeretné kezelni, a [közvetlen vonalbeszéd](direct-line-speech.md) a legjobb alapértelmezett beállítás. Ez kínál integrációt a gazdag eszközök és szerzői segédeszközök, mint például a [virtuális asszisztens megoldás és](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) vállalati sablon és a [QnA Maker szolgáltatás](https://docs.microsoft.com/azure/cognitive-services/QnAMaker/Overview/overview) építeni a közös mintákat, és használja a meglévő tudásforrások.

[Az egyéni parancsok (előzetes verzió)](custom-commands.md) egyszerűsített szerzői és üzemeltetési élményt biztosít, amely kifejezetten a természetes nyelvi parancs- és vezérlési forgatókönyvekhez igazodik.

   ![Asszisztensi megoldások összehasonlítása](media/voice-assistants/assistant-solution-comparison.png "Asszisztensi megoldások összehasonlítása")

## <a name="core-features"></a>Alapvető funkciók

Akár [a Direct Line Speech](direct-line-speech.md) vagy az Custom Commands [(Preview)](custom-commands.md) lehetőséget választja a segédműveletek létrehozásához, a testreszabási funkciók gazdag készletével testreszabhatja asszisztensét a márkához, a termékhez és a személyiséghez.

| Kategória | Szolgáltatások |
|----------|----------|
|[Egyéni kulcsszó](speech-devices-sdk-create-kws.md) | A felhasználók beszélgetést kezdeményezhetnek az asszisztensekkel egy olyan egyéni kulcsszóval, mint a "Hey Contoso". Egy alkalmazás ezt egy egyéni kulcsszó motorral a Speech SDK-ban, amely itt [létrehozható](speech-devices-sdk-create-kws.md)egyéni kulcsszóval. A hangasszisztensek a szolgáltatás oldali kulcsszó-ellenőrzés segítségével javíthatják a kulcsszóaktiválás pontosságát (szemben az eszközzel).
|[Beszéd szöveggé](speech-to-text.md) | A hangasszisztensek a beszédfelismerési szolgáltatás [beszédfelismerési](speech-to-text.md) szolgáltatásával valós idejű hangot alakítanak át felismert szöveggé. Ez a szöveg átírása szerint elérhető mind az asszisztens implementációjára, mind az ügyfélalkalmazásra.
|[Szöveg felolvasása](text-to-speech.md) | A segéd szöveges válaszait a beszédfelismerési szolgáltatás [szövegfelolvasó](text-to-speech.md) használatával szintetizálják. Ezt a szintézist ezután elérhetővé teszi az ügyfélalkalmazás audio adatfolyamként. A Microsoft lehetővé teszi, hogy saját egyéni, kiváló minőségű neurális TTS-hangot hozzon létre, amely hangot ad a márkának. Ha többet szeretne megtudni, [lépjen kapcsolatba velünk](mailto:mstts@microsoft.com).

## <a name="getting-started-with-voice-assistants"></a>A hangasszisztensek – első lépések

Rövid útmutatókat kínálunk, amelyek célja, hogy kevesebb mint 10 perc alatt futtassa a kódot. Ez a táblázat a hangasszisztens-rövid útmutatók listáját tartalmazza, nyelv szerint rendezve.

| Első lépések | Platform | API-referencia |
|------------|----------|---------------|
| C#, UWP | Windows | [Tallózás](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Tallózás](https://aka.ms/csspeech/javaref) |
| Java | Android | [Tallózás](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Mintakód

A hangsegéd létrehozásához a GitHubon érhető el mintakód. Ezek a minták az ügyfélalkalmazást fedik le, amely több népszerű programozási nyelven csatlakozik az asszisztenshez.

* [Hangasszisztens-minták (SDK)](https://aka.ms/csspeech/samples)
* [Oktatóanyag: A hang funkcióval engedélyezheti asszisztensét a Beszéd SDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>Oktatóanyag

A [beszédstajk és a közvetlen vonalbeszédcsatornával történő hangengedélyezésről szóló oktatóanyag.](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>Testreszabás

A Beszédszolgáltatással készült hangasszisztensek a [beszédfelismerési](speech-to-text.md), [a szövegfelolvasó](text-to-speech.md)és az [egyéni kulcsszóválasztásra](speech-devices-sdk-create-kws.md)rendelkezésre álló testreszabási lehetőségek teljes skáláját használhatják .

> [!NOTE]
> A testreszabási lehetőségek nyelvtől/területi beállítástól függően változnak [(lásd: Támogatott nyelvek](supported-languages.md)).

## <a name="reference-docs"></a>Referenciadokumentumok

* [Beszéd SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>További lépések

* [Ingyenes beszédfelismerési szolgáltatás-előfizetési kulcs beszerezni](get-started.md)
* [A beszédfelismerési SDK beolvasása](speech-sdk.md)
* [További információ az egyéni parancsokról (előzetes verzió)](custom-commands.md)
* [További információ a közvetlen vonalas beszédről](direct-line-speech.md)
