---
title: 'Útmutató: Egylépéses javítás hozzáadása egyéni parancshoz (előzetes verzió) - Beszédszolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebben a cikkben bemutatjuk, hogyan valósítható meg egy parancs egylépéses javítása az Egyéni parancsokban.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 86a12bd1dccc2b6ac15010546d7e990b768ebc02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75456453"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>Útmutató: Egylépéses javítás hozzáadása egyéni parancshoz (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan adhat hozzá egy egylépéses megerősítést egy parancshoz.

Az egylépéses javítás az imént befejezett parancsok frissítésére szolgál.

Azaz, ha csak beállít egy riasztást, meggondolhatja magát, és frissítheti a riasztás idejét.

- Bemenet: Állítsa be az ébresztést holnap délben
- Kimenet: "Ok, riasztás beállítása 2019.06.12.06.00- ra"
- Nem, holnap 13:00-kor.
- Kimenet: "Ok

Ne feledje, hogy ez azt jelenti, hogy ön, mint fejlesztő rendelkezik egy mechanizmussal a riasztás frissítésére a háttéralkalmazásban.

## <a name="prerequisites"></a>Előfeltételek

El kell végeznie az alábbi cikkekben leírt lépéseket:

- [Rövid útmutató: Egyéni parancs létrehozása (előzetes verzió)](./quickstart-custom-speech-commands-create-new.md)
- [Rövid útmutató: Egyéni parancs létrehozása paraméterekkel (előzetes verzió)](./quickstart-custom-speech-commands-create-parameters.md)
- [Útmutató: Megerősítés hozzáadása egyéni parancshoz (előzetes verzió)](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-the-advanced-rules-for-one-step-correction"></a>Az egylépéses javítás speciális szabályainak hozzáadása 

Az egylépéses javítás bemutatásához hosszabbítsuk meg a **Megerősítést** [oktatása](./how-to-custom-speech-commands-confirmations.md)i .
 
1. Speciális szabály hozzáadása az előző riasztás frissítéséhez. 

    Ez a szabály megkéri a felhasználót, hogy erősítse meg a riasztás dátumát és időpontját, és megerősítést (igent/nemet) vár a következő fordulóra.

   | Beállítás               | Ajánlott érték                                                  | Leírás                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | Szabály neve             | Előző riasztás frissítése                                            | A szabály célját leíró név          |
   | Feltételek            | UpdateLastCommand & szükséges paraméter - DateTime                | Feltételek, amelyek meghatározzák, hogy a szabály mikor futhat    |   
   | Műveletek               | SpeechResponse - "- Előző riasztás frissítése {DateTime}"-ra       | A szabályfeltétel igaz állapotában végrehajtandó művelet |
   | Állapot a végrehajtás után | Teljes parancs                                                 | A felhasználó állapota a fordulás után                   |

1. Helyezze az imént létrehozott szabályt a speciális szabályok tetejére (görgessen a szabály fölé a panelen, és kattintson a FEL nyílra).
   > [!div class="mx-imgBorder"]
   > ![Tartomány-ellenőrzés hozzáadása](media/custom-speech-commands/one-step-correction-rules.png)

> [!NOTE]
> Egy valós alkalmazásban a szabály Műveletek szakaszában egy tevékenységet is visszaküld az ügyfélnek, vagy http-végpontot hív meg a rendszer riasztásának frissítéséhez.

## <a name="try-it-out"></a>Próba

Válassza ki a Teszt panelt, és próbáljon ki néhány interakciót.

- Bemenet: Állítsa be az ébresztést holnap délben
- Kimenet: "Biztosan beállít riasztást 2019.07.12.12:00:00- ra?"
- Bemenet: Igen
- Kimenet: "Ok, riasztás beállítása 2019.07.12.07.00- ra"
- Nem, holnap 13:00-kor.
- Kimenet: "Előző riasztás frissítése 2019.07.12-re 13:00:00"
