---
title: Mi az a beszédfelismerés szolgáltatás (előzetes verzió)? | Microsoft Docs
description: 'A beszédfelismerés szolgáltatás, a Microsoft kognitív szolgáltatást, része, korábban külön több Azure beszéd-szolgáltatások több egység: Bing Diktálásfelismerési (beleértve a beszédfelismerés és a szöveg-beszéd átalakítás), az egyéni beszéd és a beszédfelismerés fordítási.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: e7c09eee1634c52e78a523a7cc65641ea99f23e6
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "35349963"
---
# <a name="what-is-the-speech-service-preview"></a>Mi az a beszédfelismerés szolgáltatás (előzetes verzió)?

A beszédfelismerés szolgáltatás, a Microsoft kognitív szolgáltatást, része, korábban külön több Azure beszéd-szolgáltatások több egység: Bing Diktálásfelismerési (beleértve a beszédfelismerés és a szöveg-beszéd átalakítás), az egyéni beszéd és a beszédfelismerés fordítási. Prekurzorainak, például a beszéd-szolgáltatás által használt más Microsoft-termékek, köztük a Cortana és a Microsoft Office technológiákkal van-e kapcsolva.

> [!NOTE]
> A beszédfelismerés szolgáltatás jelenleg nyilvános előzetes verziójához. Térjen vissza ide rendszeresen frissítések dokumentációjában, a további Kódminták és más témákba.

Egy előfizetéssel, az egyesített beszéd szolgáltatása, amely fejlesztők egyszerűen ahhoz, hogy megkapja az alkalmazások hatékony beszéd használatára képes szolgáltatások. Az alkalmazások most kiemelhetők hang parancs, írjanak elő, diktálást, beszéd összefoglaló és fordítási.

|Függvény|Leírás|
|-|-|
|Diktálás|Az alkalmazás bemeneteként használható szöveggé alakít át a folyamatos emberi beszéd. Integrálható a [nyelvi ismertetése szolgáltatás](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) utterances szándékos felhasználói adat származik.|
|Szöveg-hang transzformáció|A természetes hangzó szintetizált hangfájlok szöveget alakítja át.|
|Beszéd&nbsp;fordítás|Szöveg- vagy beszéd kimeneti beszéd más nyelvekre történő fordítása meg.|

## <a name="using-the-speech-service"></a>A Beszédfelismerés szolgáltatással

A beszédfelismerés szolgáltatás szeretné elérhetővé tenni két módon. [Az SDK](speech-sdk.md) számítógépnél kivonatolja a hálózati protokollok részleteit. A [REST API](rest-apis.md) együttműködve biztosítja a programozási nyelv, de nem érhető el az SDK összes funkciójának kínál.

|<br>Módszer|Beszéd<br>a szöveg|A szöveg<br>Beszéd|Beszéd<br>Fordítási|<br>Leírás|
|-|-|-|-|-|
|[SDK-k](speech-sdk.md)|Igen|Nem|Igen|Szalagtárak, amelyek megkönnyítik a fejlesztési adott programozási nyelveket.|
|[REST](rest-apis.md)|Igen|Igen|Nem|Egyszerű HTTP-alapú API-t, amely megkönnyíti a beszédfelismerés hozzá az alkalmazáshoz.|

## <a name="speech-to-text"></a>Diktálás

A [szöveg beszédfelismerés](speech-to-text.md) (STT), vagy a beszédfelismerés, API transcribes hang adatfolyamokat a szöveg, amelyet az alkalmazás fogadhat bemeneti adatként. Az alkalmazás például ezután, adja meg a szöveget egy dokumentumba vagy parancsként működésbe léphessen.

Beszédfelismerés szöveg külön optimalizált interaktív, a beszélgetésnél, és Diktálás forgatókönyvek. Az alábbiakban gyakori alkalmazási esetei szöveg API a beszédfelismerés. 

* Ismeri fel a utterance, például a parancs nélkül köztes eredmények rövid
* Hosszú, korábban rögzített utterance, például egy hangposta üzenet átvezeti
* Az adatfolyam-továbbítási beszéd átvezeti valós idejű, a Diktálás részleges eredményt
* Határozza meg a felhasználók választható szóbeli természetes nyelvű kérés alapján

A beszédfelismerés szöveg API támogatja az interaktív beszéd írjanak elő a valós idejű folyamatos felismerési és köztes eredményeit. Vége a beszédfelismerés észlelése, nem kötelező automatikus nagybetűs és írásjelek, Profanitás maszkolási és szöveg normalizálási is támogatja.

Beszédfelismerés akusztikus szöveg és a nyelvi modellek speciális szóhasználatának, zajos környezetek és különböző módokat beszéd személyre is szabhatja.

## <a name="text-to-speech"></a>Szöveg-hang transzformáció

A [szöveg-beszéd átalakítás](text-to-speech.md) (TTS), vagy a beszédfelismerés összefoglaló API alakít egyszerű szöveges természetes hangzó beszéd átalakítás, az alkalmazás a hang-i. Több, különböző nemét vagy hangsúlyos, hangok sok támogatott nyelvek érhetők el.

Az API támogatja beszéd összefoglaló Markup Language (SSML) címkék, így pontos fonetikus kiejtés megtekintenie szavak is megadhat. SSML (beleértve a hangsúlyt, a sebesség, a kötet, a nemét és a betűközű) beszéd jellemzőit is utalhat a szöveg jobb.

Az alábbiakban gyakori alkalmazási esetei a szöveg-hang transzformációs API-t.

* Beszéd kimeneti gyengén látó felhasználók alternatív képernyő kimenetként
* Adatkérés a car alkalmazásokhoz, mint a navigációs hang
* A szöveg API a beszédfelismerés egyeztetve Conversational felhasználói felületek

Ha egy nem támogatott dialektus kell, vagy csak egy egyedi hang az alkalmazáshoz, a szöveg-hang transzformációs API támogatja egyedi Hangüzenetek modellek.

## <a name="speech-translation"></a>Tolmácsolás

A [beszéd fordítási](speech-translation.md) API is használható, a közel valós idejű streamelési hang lefordítani vagy rögzített beszédfelismerő feldolgozni. Adatfolyam-fordítási, a szolgáltatás fordítási folyamatjelző jelzi a felhasználó számára megjelenített köztes eredményeket adja vissza. Az eredmények visszaadott szöveges vagy hangos.

Beszéd fordításhoz használati esetek a következők:

* Egy "conversational" fordítási mobilalkalmazás vagy az eszköz utazók végrehajtása 
* Automatikus fordítások a hang- és videofelvételeket a feliratozás

## <a name="speech-devices-sdk"></a>Speech Devices SDK

Az egyesített beszéd szolgáltatás bevezetésével, a Microsoft és a partnerei kínál egy integrált hardver vagy szoftver platform beszéd-kompatibilis eszközök fejlesztése optimalizálva: a [beszéd eszközök SDK](speech-devices-sdk.md). Ez az SDK alkalmas intelligens beszéd eszközök minden típusú alkalmazások fejlesztéséhez.

A beszédfelismerés eszközök SDK lehetővé teszi a saját testreszabott ébresztési szót, környezeti eszközök létrehozásához, hogy a köteg, amely elindítja a Hangrögzítő a márka egyedi. Felső szintű hang pontosabb beszédfelismerési, például zaj tiltási, szélen mező hang és beamforming többcsatornás forrásokból feldolgozása is tartalmazza.

## <a name="next-steps"></a>További lépések

A beszédfelismerés szolgáltatás egy ingyenes próba-előfizetés kulcs beszerzése.

> [!div class="nextstepaction"]
> [A beszéd service ingyenes kipróbálása](get-started.md)
