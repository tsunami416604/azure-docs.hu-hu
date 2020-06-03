---
title: Egylépéses javítás hozzáadása az egyéni parancsok előnézetében – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan adhat hozzá egylépéses javítást egy parancshoz egyéni parancsok előnézeti alkalmazásban.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 05f63ba4e70f649df33905f1e92fb1fab866a86c
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310427"
---
# <a name="add-a-one-step-correction-to-a-custom-command-in-a-custom-commands-preview-application"></a>Egy lépésből álló javítás hozzáadása egyéni parancsokhoz egyéni parancsok előnézeti alkalmazásban

Ebből a cikkből megtudhatja, hogyan adhat hozzá egy lépésből álló megerősítést egy parancshoz egy egyéni parancsok előzetes verziójának alkalmazásában.

Az egylépéses javítás egy éppen befejezett parancs frissítésére szolgál. Ha egy riasztási parancshoz ad hozzá egy lépésenkénti javítást, megváltoztathatja az elméjét, és frissítheti a riasztás időpontját. Például:

- Bemenet: riasztás beállítása holnap délben
- Kimenet: ok, riasztás beállítása 2020-05-02 12:00:00
- Bemenet: nem, holnap, 01:00
- Kimenet: ok

> [!NOTE]
> Valós forgatókönyv esetén az ügyfél a parancs befejezésének eredményeképpen végrehajt egy műveletet. Ez a cikk azt feltételezi, hogy rendelkezik egy olyan mechanizmussal, amely frissíti a riasztást a háttérben futó alkalmazásban.

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre a következő cikkekben ismertetett lépéseket:
> [!div class="checklist"]

> * [Gyors útmutató: egyéni parancsok előnézeti alkalmazásának létrehozása](./quickstart-custom-speech-commands-create-new.md)
> * [Gyors útmutató: egyéni parancsok előnézeti alkalmazásának létrehozása paraméterekkel](./quickstart-custom-speech-commands-create-parameters.md)
> * [Útmutató: jóváhagyások hozzáadása egy parancshoz egyéni parancsok előzetes verziójának alkalmazásában](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-interaction-rules-for-one-step-correction"></a>Az egylépéses javításhoz tartozó interakciós szabályok hozzáadása

Az egylépéses javítás bemutatásához bővítse ki a **SetAlarm** parancsot, amelyet a következő [témakörben hozott létre: megerősítés hozzáadása parancshoz egyéni parancsok előzetes](./how-to-custom-speech-commands-confirmations.md)verziójában.

1. Adjon hozzá egy kapcsolatitevékenység-szabályt a **SetAlarm** -parancs frissítéséhez.

    Ez a szabály kéri a felhasználót, hogy erősítse meg a riasztás dátumát és időpontját, és megerősítse (igen/nem) a következő bekapcsoláshoz.

   | Beállítás               | Ajánlott érték                                                  | Leírás                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | **Szabály neve**             | **Előző riasztás frissítése**                                            | A szabály célját leíró név          |
   | **Feltételek**            | **Az előző parancsot frissíteni kell & kötelező paraméter-> DateTime**                | Feltételek, amelyek meghatározzák, hogy a szabály futtatható-e    |   
   | **Műveletek**               | **Beszédfelismerési válasz küldése – > egyszerű szerkesztő – > korábbi riasztás frissítése a következőre: {DateTime}**      | A szabály feltételeinek teljesülése esetén végrehajtandó művelet |
   | **Végrehajtás utáni állapot** | **A parancs befejeződött**        | A felhasználó állapota a turn után                   |

1. A ablaktáblán válassza ki az imént létrehozott kapcsolatitevékenység-szabályt. Kattintson a három pont (**..**.) gombra a panel bal felső sarkában. Ezután a **feljebb** nyílra kattintva helyezze át a szabályt a **Kapcsolatitevékenység-szabályok** lista elejére.
   > [!div class="mx-imgBorder"]
   > ![Tartomány érvényesítésének hozzáadása](media/custom-speech-commands/one-step-correction-rules.png)

    > [!NOTE]
    > Egy valós alkalmazásban a **műveletek** szakasz használatával küldjön vissza egy tevékenységet az ügyfélnek, vagy hívja meg a http-végpontot, hogy frissítse a riasztást a rendszeren.

## <a name="try-it-out"></a>Próba

1. Válassza a **betanítás**lehetőséget.

1. A betanítás után válassza a **teszt**lehetőséget, majd próbálja meg ezeket az interakciókat:

   - Bemenet: riasztás beállítása holnap délben
   - Kimenet: biztos, hogy 2020-05-02 12:00:00-as riasztást szeretne beállítani
   - Bemenet: igen
   - Kimenet: ok, riasztás beállítása 2020-05-02 12:00:00
   - Bemenet: nem, holnap, 14:00
   - Kimenet: korábbi riasztás frissítése 2020-05-02 14:00:00-re
