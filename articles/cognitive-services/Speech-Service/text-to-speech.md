---
title: Tudnivalók a szöveg-hang transzformációs – beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: A szöveg-hang transzformációs API több mint 75 beszédhangot legfeljebb 45 nyelvet és területi beállításokat kínál. Standard hangtípust használatához meg kell a hangalapú nevének megadásakor a beszédfelismerési szolgáltatás hívásakor néhány más paramétereket.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: b413bd65582dceadd9aab912694c3b560070c4f3
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251554"
---
# <a name="about-the-text-to-speech-api"></a>A szöveg-hang transzformációs API-val kapcsolatos

A **szöveg-hang transzformációs** (Szövegfelolvasás) API természetes hangzó beszéddé átalakítja a bemeneti szöveg (más néven *beszédszintézishez*).

Beszéd létrehozásához, az alkalmazás HTTP POST-kérelmet küld a szöveg-hang transzformációs API-t. Itt szöveg van beszéddé emberi hangzó és a egy hangfájlt értéket küldi vissza. Hangok és nyelvek széles támogatottak.

Melyik beszéd összefoglaló kik forgatókönyvek a következők:

* *Kisegítő lehetőségek javítása:* **szöveg-hang transzformációs** technológia lehetővé teszi a tartalmak tulajdonosai, és különböző módokon személyek válaszolni a kiadók használhatja a saját tartalmakat. Készült kisegítő funkciók vagy olvasási nehézségekkel emberek értékeljük képes arra, hogy a tartalmak felhasználása aurally. Hangalapú kimeneti is megkönnyíti a szöveges tartalmakat, például újságok és blogok, miközben utazás, illetve az ilyen mobileszközök csak a felhasználók számára.

* *Többfeladatos feldolgozáshoz forgatókönyvekben válaszol:* **szöveg-hang transzformációs** lehetővé teszi személyek számára fontos információk gyorsan és kényelmesen vezetési vagy más módon kívül egy kényelmes környezet olvasása közben. Navigáció: egy közös alkalmazás ezen a területen.

* *Több módok learning növelése:* A különböző emberek különböző módon ismerje meg, ajánlott. Internetes tanulás szakértőktől kimutatták, hogy együtt nyújtó hanghívási és SMS is könnyebben információk ismerje meg, és azokat megőrizheti.

* *Intuitív robotokat vagy asszisztensek kidolgozását:* Kapcsolódniuk egy intelligens csevegőrobot, vagy egy virtuális asszisztensek szerves része lehet. Egyre több vállalat fejleszt Csevegés robotokat vonzó ügyfél szolgáltatás élményt biztosít az ügyfelek számára. Hang hozzáadása egy további dimenziót azáltal, hogy a robot válaszok (például telefonon) aurally fogadását.

## <a name="voice-support"></a>Beszédfelismerési támogatása

A Microsoft **szöveg-hang transzformációs** szolgáltatás kínál a több mint 75 beszédhangot legfeljebb 45 nyelvet és területi beállításokat. Ezek szabványos "hangtípust" használatához meg kell adja meg a hangalapú nevét néhány más paraméterekkel, amikor a szolgáltatás REST API-t hívja. További információ a támogatott nyelveket, területi beállítások és beszédhangot: [támogatott nyelvek](language-support.md#text-to-speech).

> [!IMPORTANT]
> Költségek szabványos, egyéni és Neurális beszédhangot eltérőek. További információkért lásd: [díjszabási](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="neural-voices"></a>Neurális beszédhangot

Győződjön meg arról, látás- és a virtuális asszisztensek interakció természetesebb, és vonzó, digitális szövegek, például az e-könyvek átalakítása audiobooks, és javíthatja a autós navigációs rendszerek Neurális szöveg-hang transzformációs használható. Emberszerű természetes prosody és egyértelmű tagolódását szavak Neurális Szövegfelolvasás rendelkezik jelentősen csökkent figyel-e fáradás AI-rendszerekkel való kommunikáció során. Neurális beszédhangot kapcsolatos további információkért lásd: [támogatott nyelvek](language-support.md#text-to-speech).

### <a name="custom-voices"></a>Egyéni beszédhangot

Szövegfelolvasás testreszabás lehetővé teszi, hogy hozzon létre egy könnyen felismerhető névre, egy-az-maga nemében egyedülálló hang, a saját márkáját: egy *hangtípusú.* A hangtípusú létrehozásához, győződjön meg arról, a studio felvételt, és töltse fel a betanítási adatok, a kapcsolódó parancsprogramokat. A szolgáltatás ekkor létrehoz egy egyéni modell a rögzítése használatára vannak konfigurálva. Saját hangtípusú használatával beszédfelismerési szintetizálásához. További információkért lásd: [egyéni hangtípust](how-to-customize-voice-font.md).

## <a name="api-capabilities"></a>API-funkciók

Nagy mennyiségű képességeit a **szöveg-hang transzformációs** API-t, különös tekintettel a testreszabás, érhetők el REST-en keresztül. Az alábbi táblázat foglalja össze az egyes módszerek az API eléréséhez képességeit. Képességek és API-részletek teljes listáját lásd: [referencia Swagger](https://westus.cris.ai/swagger/ui/index).

| Használati eset | REST | SDK-k |
|-----|-----|-----|----|
| Töltse fel az adatkészletek a voice-betanítás | Igen | Nem |
| Hozzon létre & hangvezérelt betűtípus modellek kezelése | Igen | Nem |
| & Hangvezérelt betűtípus központi telepítések felügyeletéhez szükséges létrehozása | Igen | Nem |
| Létrehozása és kezelése a hangalapú betűtípus-tesztek| Igen | Nem |
| Előfizetések kezelése | Igen | Nem |

> [!NOTE]
> Az API-t valósít meg, amely korlátozza az API-kérelmek 25, 5 másodpercenként szabályozás. Fejlécek tájékoztatja korlátját.

## <a name="next-steps"></a>További lépések

* [Ingyenes Speech Services előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [Rövid útmutató: Szöveg-beszéd átalakítás, Python konvertálása](quickstart-python-text-to-speech.md)
* [Rövid útmutató: Szöveg-beszéd átalakítás, .NET Core konvertálása](quickstart-dotnet-text-to-speech.md)
* [REST API – referencia](rest-apis.md)
