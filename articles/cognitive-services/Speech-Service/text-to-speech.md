---
title: Szöveg-beszéd átalakítás kapcsolatos |} Microsoft Docs
description: Szöveg-beszéd átalakítás funkcióinak áttekintése.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 84baf03c83bb63883b80982056cdf6e1e25b3fb7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349706"
---
# <a name="about-the-text-to-speech-api"></a>A szöveg-hang transzformációs API kapcsolatos

A **szöveg-beszéd átalakítás** (TTS) API a beszédfelismerés szolgáltatás bemeneti szöveg alakítja természetes hangzó beszéd (más néven *beszéd összefoglaló*).

Beszéd létrehozásához az az alkalmazás HTTP POST kérelmek küldése a beszédfelismerés szolgáltatás. Hiba, szöveges az emberi hangzó beszéd synthesized-e és hangfájl adott vissza. Számos hangok és nyelvek támogatottak.

Mely beszéd összefoglaló elfogadott forgatókönyvek a következők:

* *Kisegítő lehetőségek javítása:* **szöveg-beszéd átalakítás** technológia lehetővé teszi, hogy a tartalmak tulajdonosai és közzétevők a különböző módokon személyek válaszolni a tartalmuk. Készült kisegítő funkciók és olvasási nehézségek értékeljük igényt aurally tartalmakat használhat fel. Hang kimeneti is egyszerűbbé teszi a személyeknek lehetővé teszik a szöveges tartalmat, például hírlapok vagy blogok, miközben ingázást, illetve az ilyen mobileszközök.

* *Többfeladatos forgatókönyvekben válaszol:* **szöveg-beszéd átalakítás** lehetővé teszi, hogy a felhasználók befogadására fontos információk gyorsan és kényelmesen befolyásoló tényezők vagy egyéb kívül kényelmes környezet olvasása közben. Navigáció egy közös alkalmazást ezen a területen. 

* *Learning több módú továbbfejlesztésének:* más személyek további ajánlott különböző módon. Online tanulási szakértők kimutatták, hogy hang és a szöveg megadásával együtt is könnyebben információk ismerje meg, és tartsa meg.

* *Intuitív botok vagy segédek továbbítása:* kapcsolódniuk egy intelligens Csevegés botot vagy egy virtuális Segéd szerves része lehet. A vállalatok egyre több fejleszt Csevegés botok biztosításához kommunikáció zajlik ügyfél szolgáltatás feladatait az ügyfelek számára. Hang aurally fogadhatják (például a telefonszám) a botot válaszokat azáltal ad hozzá egy másik dimenziót.

## <a name="voice-support"></a>Hang-támogatás

A Microsoft **szöveg-beszéd átalakítás** szolgáltatás nyújt több mint 75 hangok legfeljebb 45 nyelv és területi beállításokat. Ezek szabványos "voice betűtípusok" használatához meg kell adja meg a hang nevét néhány más paraméterekkel, amikor a szolgáltatás REST API-t hívja. A támogatott hangok részletekért lásd: [támogatott nyelv](supported-languages.md). 

Ha az alkalmazás egyedi hangot, létrehozhat [egyedi Hangüzenetek betűtípusok](how-to-customize-voice-font.md) saját beszéd minták.

## <a name="next-steps"></a>További lépések

* [Beszéd próbaverziós előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [Lásd: how to a C# beszéd felismerésére](quickstart-csharp-windows.md)
