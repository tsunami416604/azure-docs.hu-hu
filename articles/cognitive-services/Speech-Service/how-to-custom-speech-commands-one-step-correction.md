---
title: 'Útmutató: egy lépésből álló javítás hozzáadása egyéni parancshoz (előzetes verzió) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan valósítható meg a parancsok egylépéses javítása az egyéni parancsokban.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 86a12bd1dccc2b6ac15010546d7e990b768ebc02
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456453"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>Útmutató: egy lépésből álló javítás hozzáadása egyéni parancshoz (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan adhat hozzá egy lépéses megerősítést egy parancshoz.

Az egylépéses javítás egy éppen befejezett parancs frissítésére szolgál.

Ha csak riasztást állít be, megváltoztathatja az elméjét, és frissítheti a riasztás időpontját.

- Bemenet: riasztás beállítása holnap délben
- Output: "ok, riasztás beállítása 12/06/2019 12:00:00"
- Bemenet: nem, holnap, 01:00
- Kimenet: "OK

Ne feledje, hogy ez azt jelenti, hogy fejlesztőként olyan mechanizmust kell használni, amely frissíti a riasztást a háttérbeli alkalmazásban.

## <a name="prerequisites"></a>Előfeltételek

A következő cikkekben ismertetett lépéseket kell végrehajtania:

- [Gyors útmutató: Egyéni parancs létrehozása (előzetes verzió)](./quickstart-custom-speech-commands-create-new.md)
- [Gyors útmutató: Egyéni parancs létrehozása paraméterekkel (előzetes verzió)](./quickstart-custom-speech-commands-create-parameters.md)
- [Útmutató: megerősítés hozzáadása egyéni parancshoz (előzetes verzió)](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-the-advanced-rules-for-one-step-correction"></a>Az egylépéses javítás speciális szabályainak hozzáadása 

Az egylépéses javítás bemutatásához terjessze ki a **SetAlarm** -parancsot, amelyet a [megerősítések című témakörben](./how-to-custom-speech-commands-confirmations.md)hozott létre.
 
1. Adjon hozzá egy speciális szabályt az előző riasztás frissítéséhez. 

    Ez a szabály kéri a felhasználót, hogy erősítse meg a riasztás dátumát és időpontját, és egy megerősítést (igen/nem) vár a következő bekapcsoláshoz.

   | Beállítás               | Ajánlott érték                                                  | Leírás                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | Szabály neve             | Előző riasztás frissítése                                            | A szabály célját leíró név          |
   | Feltételek            | UpdateLastCommand & kötelező paraméter-DateTime                | Feltételek, amelyek meghatározzák, hogy a szabály futtatható-e    |   
   | Műveletek               | SpeechResponse – "a korábbi riasztás frissítése a következőre: {DateTime}"       | A szabály feltételének teljesülésekor végrehajtandó művelet |
   | Végrehajtás utáni állapot | Teljes parancs                                                 | A felhasználó állapota a turn után                   |

1. Helyezze át az imént létrehozott szabályt a speciális szabályok tetejére (görgessen a szabály fölé a panelen, és kattintson a felfelé mutató nyílra).
   > [!div class="mx-imgBorder"]
   > ![tartomány-ellenőrzés hozzáadása](media/custom-speech-commands/one-step-correction-rules.png)

> [!NOTE]
> Egy valós alkalmazásban a szabály műveletek szakaszában egy tevékenységet is küld az ügyfélnek, vagy meghív egy HTTP-végpontot, hogy frissítse a riasztást a rendszeren.

## <a name="try-it-out"></a>Próbálja ki!

Válassza ki a teszt panelt, és próbálkozzon néhány interakcióval.

- Bemenet: riasztás beállítása holnap délben
- Kimenet: "biztos, hogy 12/07/2019 12:00:00-as riasztást szeretne beállítani?"
- Bemenet: igen
- Output: "ok, riasztás beállítása 12/07/2019 12:00:00"
- Bemenet: nem, holnap, 01:00
- Kimenet: "előző riasztás frissítése 12/07/2019 13:00:00-re"
