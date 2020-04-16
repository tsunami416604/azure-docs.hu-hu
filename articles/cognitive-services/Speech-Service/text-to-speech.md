---
title: Text-to-speech - Beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: A beszédfelismerési szolgáltatás szövegfelolvasó funkciója lehetővé teszi, hogy alkalmazásai, eszközei vagy eszközei a szöveget természetes, emberszerű szintetizált beszédté alakítsák át. Válasszon előre beállított hangokat, vagy hozzon létre saját egyéni hangot.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: trbye
ms.openlocfilehash: 1a8b458eb004b44d0045f36b18d88e11e019c4d2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399501"
---
# <a name="what-is-text-to-speech"></a>Mi az a szövegfelolvasás?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

A beszédfelismerési szolgáltatás szövegfelolvasása lehetővé teszi, hogy alkalmazásai, eszközei vagy eszközei a szöveget emberszerű szintetizált beszédmé alakítsák. Válasszon a szabványos és neurális hangok közül, vagy hozzon létre egy egyedi, a termékre vagy a márkára vonatkozó egyéni hangot. Több mint 75 standard hang érhető el több mint 45 nyelven és területi területen, és 5 neurális hang érhető el kiválasztott számú nyelven és nyelven. A támogatott hangok, nyelvek és területi beállítások teljes listáját a támogatott nyelvek című témakörben tésiterületen [láthatja.](language-support.md#text-to-speech)

> [!NOTE]
> A Bing Speech-t 2019. október 15-én leszerelték. Ha alkalmazásai, eszközei vagy termékei a Bing beszédfelismerési API-kat vagy az egyéni beszédfelismerést használják, útmutatókat hoztunk létre a beszédfelismerési szolgáltatásba való áttelepítéshez.
> - [Áttelepítés a Bing beszédfelismerésről a beszédfelismerési szolgáltatásra](how-to-migrate-from-bing-speech.md)

## <a name="core-features"></a>Alapvető funkciók

* Beszédszintézis – A [beszédfelismerési SDK](quickstarts/text-to-speech-audio-file.md) vagy a [REST API](rest-text-to-speech.md) használatával konvertálhatja a szövegfelolvasást szabványos, neurális vagy egyéni hangokkal.

* Hosszú hang aszinkron szintézise – A [Hosszú hang API-val](long-audio-api.md) 10 percnél hosszabb szövegfelolvasó fájlok (például hangoskönyvek vagy előadások) aszinkron módon szintetizálható. A beszédfelismerési SDK vagy a beszéd-szöveg REST API használatával végrehajtott szintézissel ellentétben a válaszok nem valós időben kerülnek visszaadásra. Az elvárás az, hogy a kérelmek et aszinkron módon küldi el a rendszer, a válaszokat lekérik, és a szintetizált hangot letölti, amikor elérhetővé válik a szolgáltatásból. Csak az egyéni neurális hangok támogatottak.

* Szabványos hangok - Statisztikai parametrikus szintézis és/vagy összefűzési szintézis technikák alkalmazásával hozták létre. Ezek a hangok nagyon érthetőek és természetesnek hangzanak. A hangbeállítások széles választékával könnyedén lehetővé teheti, hogy alkalmazásai több mint 45 nyelven beszéljenek. Ezek a hangok magas kiejtési pontosságot biztosítanak, beleértve a rövidítések, a betűszó bővítések, a dátum/idő értelmezések, a politelefonok és egyebek támogatását. A szabványos hangok teljes listáját a [támogatott nyelvek című témakörben tetszés szerint.](language-support.md#text-to-speech)

* Neurális hangok - Mély neurális hálózatok használják, hogy felszámolja a határértékeket a hagyományos beszédszintézis tekintetében a stressz és a hanglejtés beszélt nyelven. Prosody előrejelzés és a hang szintézis végzik egyszerre, ami több folyadékot és természetes hangzású kimenetek. A neurális hangok segítségével a chatbotokkal és a hangasszisztensekkel való interakciók természetesebbé és vonzóbbá tehetők, digitális szövegeket, például e-könyveket hangoskönyvekké alakíthatnak, és javíthatják az autós navigációs rendszereket. Az emberszerű természetes prosody és tiszta artikuláció a szavak, neurális hangok jelentősen csökkenti a hallgatás fáradtság, ha kölcsönhatásba lépnek a mesterséges őbrendszerek. Az idegi hangok teljes listáját a [támogatott nyelvek című témakörben tési területen láthatja.](language-support.md#text-to-speech)

* Beszédszintetizáló nyelvi (SSML) – A beszéd-szöveg kimenetek testreszabására használt XML-alapú jelölőnyelv. Az SSML segítségével beállíthatja a hangmagasságot, szüneteket adhat hozzá, javíthatja a kiejtést, felgyorsíthatja vagy lelassíthatja a beszédsebességet, növelheti vagy csökkentheti a hangerőt, és több hangot tulajdoníthat egyetlen dokumentumnak. Lásd [SSML](speech-synthesis-markup.md).

## <a name="get-started"></a>Bevezetés

A szövegfelolvasó szolgáltatás a [Speech SDK-n](speech-sdk.md)keresztül érhető el. Számos gyakori forgatókönyv érhető el rövid útmutatóként, különböző nyelveken és platformokon:

* [Beszédszintézis egy hangfájlba](quickstarts/text-to-speech-audio-file.md)
* [Beszédszintézis hangszóróra](quickstarts/text-to-speech.md)
* [Aszinkron módon szintetizálják a hosszú formátumú hangot](quickstarts/text-to-speech/async-synthesis-long-form-audio.md)

Ha szeretné, a szövegfelolvasó szolgáltatás [rest-en](rest-text-to-speech.md)keresztül érhető el.

## <a name="sample-code"></a>Mintakód

A szövegfelolvasás mintakódja elérhető a GitHubon. Ezek a minták a legnépszerűbb programozási nyelveken a szövegfelolvasó-átalakítást fedik le.

- [Szövegfelolvasó minták (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Szövegfelolvasó minták (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customization"></a>Testreszabás

A szabványos és neurális hangok mellett a termékére vagy márkájára egyedi egyéni hangokat is létrehozhat és finomíthat. Mindössze annyit kell tenni az induláshoz, hogy egy maroknyi audio fájlokat és a kapcsolódó átiratok. További információ: [Az Egyéni hang – Első lépések](how-to-custom-voice.md)

## <a name="pricing-note"></a>Árképzési megjegyzés

A szövegfelolvasó szolgáltatás használatakor minden beszédfelismeréssé konvertált karakterért díjat kell fizetnie, beleértve az írásjeleket is. Bár maga az SSML-dokumentum nem számlázható, a szöveg beszédté konvertálódásának beállítására használt választható elemek , például a fonémák és a hangmagasság okán a program számlázható karaktereknek számít. Itt egy lista arról, hogy mi számlázható:

- A kérés SSML-törzsében a szövegfelolvasó szolgáltatásnak átadott szöveg
- Minden jelölés a kérelemtörzs szövegmezőjében SSML formátumban, `<speak>` `<voice>` kivéve és címkék
- Betűk, írásjelek, szóközök, tabulátorok, korrektúrák és minden szóközkarakter
- A Unicode-ban definiált összes kódpont

További információt az Árképzés című témakörben [talál.](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)

> [!IMPORTANT]
> Minden kínai, japán és koreai nyelvű karakter két karakternek számít a számlázáshoz.

## <a name="reference-docs"></a>Referenciadokumentumok

- [Beszéd SDK](speech-sdk.md)
- [REST API: Szövegfelolvasás](rest-text-to-speech.md)

## <a name="next-steps"></a>További lépések

- [Ingyenes beszédszolgáltatás-előfizetés beolvasása](get-started.md)
- [A beszédfelismerési SDK beolvasása](speech-sdk.md)
