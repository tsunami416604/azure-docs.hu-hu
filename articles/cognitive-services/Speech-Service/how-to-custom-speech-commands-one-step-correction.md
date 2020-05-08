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
ms.openlocfilehash: f43c28d314cb8a0211496664cd20d2c380e4d5b0
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858266"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>Útmutató: egy lépésből álló javítás hozzáadása egyéni parancshoz (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan adhat hozzá egy lépéses megerősítést egy parancshoz.

Az egylépéses javítás egy éppen befejezett parancs frissítésére szolgál. Vagyis ha csak riasztást állít be, megváltoztathatja az elméjét, és frissítheti a riasztás időpontját. Ilyen eset például a következő:

- Bemenet: riasztás beállítása holnap délben
- Kimenet: ok, riasztás beállítása 2020-05-02 12:00:00
- Bemenet: nem, holnap, 01:00
- Kimenet: ok

Valós forgatókönyv, amelyet általában a parancs befejezésének eredményeképpen a művelet végrehajtásával végrehajtó ügyfél egészít ki – ez a cikk azt feltételezi, hogy fejlesztőinek van olyan mechanizmusa, amely frissíti a riasztást a háttérben futó alkalmazásban.

## <a name="prerequisites"></a>Előfeltételek

A következő cikkekben ismertetett lépéseket kell végrehajtania:
> [!div class="checklist"]

> * [Gyors útmutató: Egyéni parancs létrehozása (előzetes verzió)](./quickstart-custom-speech-commands-create-new.md)
> * [Gyors útmutató: Egyéni parancs létrehozása paraméterekkel (előzetes verzió)](./quickstart-custom-speech-commands-create-parameters.md)
> * [Útmutató: megerősítés hozzáadása egyéni parancshoz (előzetes verzió)](./how-to-custom-speech-commands-confirmations.md)


## <a name="add-interaction-rules-for-one-step-correction"></a>Az egylépéses javításhoz tartozó interakciós szabályok hozzáadása 

Az egylépéses javítás bemutatásához terjessze ki a **SetAlarm** parancsot, amely a következő [témakörben létrehozott: megerősítés hozzáadása egyéni parancshoz (előzetes verzió)](./how-to-custom-speech-commands-confirmations.md).
1. A korábban beállított riasztás frissítéséhez adjon hozzá egy kapcsolatitevékenység-szabályt. 

    Ez a szabály kéri a felhasználót, hogy erősítse meg a riasztás dátumát és időpontját, és egy megerősítést (igen/nem) vár a következő bekapcsoláshoz.

   | Beállítás               | Ajánlott érték                                                  | Leírás                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | Szabály neve             | Előző riasztás frissítése                                            | A szabály célját leíró név          |
   | Feltételek            | Az előző parancsot frissíteni kell & kötelező paraméter-> DateTime                | Feltételek, amelyek meghatározzák, hogy a szabály futtatható-e    |   
   | Műveletek               | Beszédfelismerési válasz küldése – > egyszerű szerkesztő – > korábbi riasztás frissítése a következőre: {DateTime}      | A szabály feltételeinek teljesülése esetén végrehajtandó művelet |
   | Végrehajtás utáni állapot | A parancs befejeződött        | A felhasználó állapota a turn után                   |

1. Helyezze át az imént létrehozott szabályt a kapcsolatitevékenység-szabályok tetejére (válassza ki a szabályt a panelen, és kattintson a középső ablaktábla tetején `...` található ikon alatti felfelé mutató nyílra).
   > [!div class="mx-imgBorder"]
   > ![Tartomány érvényesítésének hozzáadása](media/custom-speech-commands/one-step-correction-rules.png)
    > [!NOTE]
    > Egy valós alkalmazásban a szabály műveletek szakaszában egy tevékenységet is küld az ügyfélnek, vagy meghív egy HTTP-végpontot, hogy frissítse a riasztást a rendszeren.

## <a name="try-it-out"></a>Próba

Válassza `Train`a lehetőséget, várjon, amíg befejeződik `Test`a betanítás, majd válassza a lehetőséget.

- Bemenet: riasztás beállítása holnap délben
- Kimenet: biztos, hogy 2020-05-02 12:00:00-as riasztást szeretne beállítani
- Bemenet: igen
- Kimenet: ok, riasztás beállítása 2020-05-02 12:00:00
- Bemenet: nem, holnap, 14:00
- Kimenet: korábbi riasztás frissítése 2020-05-02 14:00:00-re
