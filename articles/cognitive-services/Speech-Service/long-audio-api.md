---
title: Long audio API (előzetes verzió) – Speech Service
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogy a hosszú hangalapú API hogyan lett kialakítva a hosszú formátumú szöveg és a beszéd közötti aszinkron szintézishez.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: trbye
ms.openlocfilehash: 550579b40470d7a1ad02031b8140e7d0a7164f46
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310597"
---
# <a name="long-audio-api-preview"></a>Long audio API (előzetes verzió)

A hosszú hangalapú API a hosszú formátumú szöveg és a beszéd aszinkron szintéziséhez készült (például hangkönyvek, Hírek és dokumentumok). Ez az API nem adja vissza a szintetizált hangot valós időben, ezért a várt érték az, hogy a válasz (ok) ra fog szavazni, és a kimenet (eke) t a szolgáltatás által elérhetővé tettnek megfelelően használja fel. A Speech SDK által használt szöveg-beszéd API-val ellentétben a hosszú hang-API 10 percnél hosszabb szintetizált hangot is létrehozhat, így a kiadók és a hangtartalom-platformok ideálisak.

A hosszú hang API további előnyei:

* A szolgáltatás által visszaadott szintetizált beszéd a legjobb neurális hangokat használja.
* Nincs szükség hang-végpont üzembe helyezésére, mert a hangokat nem valós idejű batch-módban szintetizálja.

> [!NOTE]
> A Long audio API mostantól támogatja a [nyilvános neurális hangokat](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices) és az [Egyéni neurális hangokat](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices)is.

## <a name="workflow"></a>Munkafolyamat

A hosszú hangalapú API használatakor a rendszer a szintetizált szövegfájlt vagy fájlokat küldi el, lekérdezi az állapotot, majd ha az állapot sikeres, letöltheti az audió kimenetet.

Ez az ábra a munkafolyamat magas szintű áttekintését tartalmazza.

![Hosszú hang-API munkafolyamat-diagram](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Tartalom előkészítése a szintézishez

A szövegfájl előkészítésekor győződjön meg róla, hogy:

* Egyszerű szöveges (. txt) vagy SSML szöveg (. txt)
* Kódolása [UTF-8, byte Order Mark (BOM)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom) értékkel
* Egyetlen fájl, nem zip
* Több mint 400 karaktert [tartalmaz az egyszerű szöveges vagy a 400](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech#pricing-note) -es SSML-szövegekhez, és kisebb, mint 10 000 bekezdés
  * Egyszerű szöveg esetén az egyes bekezdéseket az ENTER/Return – [egyszerű szöveges beviteli példa](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt) **megadásával** választjuk el.
  * A SSML szövegek esetében az egyes SSML-darabok bekezdésnek tekintendők. A SSML-darabokat különböző bekezdések szerint kell elválasztani – a [SSML szövegének](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt) megjelenítése – példa
> [!NOTE]
> A kínai (anyaországi), a kínai (Hongkong KKT), a kínai (Tajvan), a japán és a Koreai nyelveken egy szót két karakternek számítunk fel. 

## <a name="submit-synthesis-requests"></a>Szintézisi kérelmek elküldése

A bemeneti tartalom előkészítése után a kérelem elküldéséhez kövesse a [hosszú formátumú hangszintézis rövid](https://aka.ms/long-audio-python) útmutatóját. Ha egynél több bemeneti fájllal rendelkezik, több kérést is el kell küldenie. 

A **http-állapotkódok** gyakori hibákat jeleznek.

| API | HTTP-állapotkód | Leírás | Ajánlattétel |
|-----|------------------|-------------|----------|
| Létrehozás | 400 | A hangszintézis nincs engedélyezve ebben a régióban. | A Speech előfizetés kulcsának módosítása egy támogatott régióval. |
|        | 400 | Csak az ehhez a régióhoz tartozó **normál** beszédfelismerési előfizetés érvényes. | Módosítsa a Speech előfizetés kulcsát a "standard" árképzési szintre. |
|        | 400 | Túllépi az Azure-fiókra vonatkozó 20 000-es kérelem korlátját. Új adatbázisok elküldése előtt távolítson el néhány kérelmet. | A kiszolgáló minden Azure-fiók esetében 20 000-kérelmeket fog tartani. Néhány kérelem törlése az újak elküldése előtt. |
|        | 400 | Ez a modell nem használható a következő hangszintézisben: {modelID}. | Győződjön meg arról, hogy a ({modelID}) állapota helyes. |
|        | 400 | A kérelem régiója nem egyezik a következő modell régiójával: {modelID}. | Győződjön meg arról, hogy a {modelID} régiója egyezik a kérelem régiójával. |
|        | 400 | A hangszintézis csak az UTF-8 kódolású szövegfájlt támogatja az byte-Order jelölővel. | Győződjön meg arról, hogy a bemeneti fájlok UTF-8 kódolással vannak ellátva a byte-Order jelölővel. |
|        | 400 | A hangszintézisi kérelemben csak érvényes SSML-bemenetek engedélyezettek. | Győződjön meg arról, hogy a bemeneti SSML-kifejezések helyesek. |
|        | 400 | A (z) {voiceName} nevű hang nem található a bemeneti fájlban. | A bemeneti SSML hangjának neve nincs igazítva a modell azonosítójával. |
|        | 400 | A bemeneti fájlban lévő bekezdések mennyiségének 10 000-nál kisebbnek kell lennie. | Győződjön meg arról, hogy a fájl bekezdése kevesebb, mint 10 000. |
|        | 400 | A bemeneti fájlnak 400 karakternél nagyobbnak kell lennie. | Győződjön meg arról, hogy a bemeneti fájl mérete meghaladja a 400 karaktert. |
|        | 404 | A hangszintézis definíciójában deklarált modell nem található: {modelID}. | Győződjön meg arról, hogy a ({modelID}) helyes. |
|        | 429 | Túllépi az aktív hangszintézis korlátot. Várjon, amíg néhány kérelem befejeződik. | A kiszolgáló az egyes Azure-fiókokhoz legfeljebb 120 kérelem futtatására és várólistára helyezésére jogosult. Várjon, és ne küldje el az új kéréseket, amíg néhány kérelem be nem fejeződik. |
| Mind       | 429 | Túl sok kérés van. | Az ügyfél legfeljebb 5 kérést küldhet a kiszolgálónak másodpercenként minden egyes Azure-fiókhoz. Csökkentse a kérések másodpercenkénti számát. |
| Törlés    | 400 | A hangszintézisi feladat még használatban van. | Csak a **befejezett** vagy **sikertelen**kéréseket lehet törölni. |
| GetByID   | 404 | A megadott entitás nem található. | Győződjön meg arról, hogy a szintézis azonosítója helyes. |

## <a name="regions-and-endpoints"></a>Régiók és végpontok

A hosszú hang API több régióban is elérhető egyedi végpontokkal.

| Régió | Végpont |
|--------|----------|
| Kelet-Ausztrália | `https://australiaeast.customvoice.api.speech.microsoft.com` |
| Közép-Kanada | `https://canadacentral.customvoice.api.speech.microsoft.com` |
| USA keleti régiója | `https://eastus.customvoice.api.speech.microsoft.com` |
| Közép-India | `https://centralindia.customvoice.api.speech.microsoft.com` |
| USA déli középső régiója | `https://southcentralus.customvoice.api.speech.microsoft.com` |
| Délkelet-Ázsia | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Az Egyesült Királyság déli régiója | `https://uksouth.customvoice.api.speech.microsoft.com` |
| Nyugat-Európa | `https://westeurope.customvoice.api.speech.microsoft.com` |
| USA nyugati régiója, 2. | `https://westus2.customvoice.api.speech.microsoft.com` |

## <a name="audio-output-formats"></a>Hangkimeneti formátumok

A rugalmas hangkimeneti formátumokat támogatjuk. A "concatenateResult" paraméter beállításával egy adott kimenetet adhat meg, vagy egy adott kimenetet is összefűzött a hangkimenetekhez. A hosszú hang API a következő hangkimeneti formátumokat támogatja:

> [!NOTE]
> Az alapértelmezett hang formátuma a riff-16khz-16bit-mono-PCM.

* riff-8khz-16bit-mono-PCM
* riff-16khz-16bit-mono-PCM
* riff-24khz-16bit-mono-PCM
* riff-48kHz-16bit-mono-PCM
* hang-16khz-32kbitrate-mono-MP3
* hang-16khz-64kbitrate-mono-MP3
* hang-16khz-128kbitrate-mono-MP3
* hang-24khz-48kbitrate-mono-MP3
* hang-24khz-96kbitrate-mono-MP3
* hang-24khz-160kbitrate-mono-MP3

## <a name="quickstarts"></a>Rövid útmutatók

A hosszú hangalapú API sikeres futtatását segítő gyors útmutatókat kínálunk. Ez a táblázat a hosszú hangalapú API-gyors útmutatók listáját tartalmazza, nyelv szerint rendezve.

* [Gyors útmutató: Python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>Mintakód
A hosszú hang API-mintakód elérhető a GitHubon.

* [Mintakód: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Mintakód: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Mintakód: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
