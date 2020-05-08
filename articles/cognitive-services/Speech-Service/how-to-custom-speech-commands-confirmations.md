---
title: 'Útmutató: megerősítés hozzáadása egyéni parancshoz (előzetes verzió)'
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan valósítja meg az egyéni parancsok parancsainak megerősítését.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: bf1b79c1b5d7b9dfd93b354c6b6ff5a512bb74a5
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858231"
---
# <a name="how-to-add-a-confirmation-to-a-custom-command-preview"></a>Útmutató: megerősítés hozzáadása egyéni parancshoz (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan adhat hozzá egy megerősítést a parancshoz.

## <a name="prerequisites"></a>Előfeltételek

A következő cikkekben ismertetett lépéseket kell végrehajtania:
> [!div class="checklist"]
> *  [Gyors útmutató: Egyéni parancs létrehozása (előzetes verzió)](./quickstart-custom-speech-commands-create-new.md)
> * [Gyors útmutató: Egyéni parancs létrehozása paraméterekkel (előzetes verzió)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>SetAlarm-parancs létrehozása

A megerősítések megjelenítéséhez hozzon létre egy új parancsot, amely lehetővé teszi, hogy a felhasználó riasztást állítson be.

1. Nyissa meg a korábban létrehozott egyéni parancsok alkalmazást a [Speech Studióban](https://speech.microsoft.com/).
1. Hozzon létre egy `SetAlarm`új parancsot.
1. Adjon hozzá egy nevű `DateTime` paramétert a következő konfigurációval.

   | Beállítás                           | Ajánlott érték                     |  Leírás                 |
   | --------------------------------- | -----------------------------------------------------| ------------|
   | Name                              | DateTime                                | A paraméter leíró neve                                |
   | Kötelező                          | ellenőrizni                                 | Jelölőnégyzet, amely azt jelzi, hogy a paraméter értéke kötelező-e a parancs végrehajtása előtt |
   | A kötelező paraméterre adott válasz   | Egyszerű szerkesztő – > milyen időt?                              | A paraméter értékének megadására vonatkozó kérés, ha nem ismert |
   | Típus                              | DateTime                                | A paraméter típusa (például szám, karakterlánc, dátum vagy földrajzi idő)   |
   | Dátum alapértelmezett értékei                     | Ha a Date hiányzik a mai nap használata            | A változó alapértelmezett értéke, amelyet a felhasználó nem biztosít.  |  
   | Idő alapértelmezett értékei                     | Ha hiányzik az idő, használja a nap kezdetét     |  A változó alapértelmezett értéke, amelyet a felhasználó nem biztosít.|

1. Vegyen fel néhány példát a mondatokra.
   
    ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. Adja hozzá a befejezési szabályt az eredmény megerősítéséhez.

   | Beállítás    | Ajánlott érték                               |Leírás                                     |
   | ---------- | ------------------------------------------------------- |-----|
   | Szabály neve  | Riasztás beállítása                                               |    A szabály célját leíró név |
   | Műveletek    | Beszédfelismerési válasz küldése – ok, riasztás beállítva a következőhöz: {DateTime}    |A szabály feltételének teljesülésekor végrehajtandó művelet

## <a name="try-it-out"></a>Próba

1. Válassza `Train` a jobb oldali ablaktábla tetején található ikont.

1. A betanítás befejezése után válassza `Test`a lehetőséget.
    - Bemenet: riasztás beállítása holnap délben
    - Kimenet: ok, riasztás beállítása 2020-05-02 12:00:00
    - Bemenet: riasztás beállítása
    - Kimenet: mennyi idő?
    - Bemenet: 5
    - Kimenet: ok, riasztás beállítása 2020-05-01 17:00:00

## <a name="add-the-advanced-rules-for-confirmation"></a>A jóváhagyáshoz szükséges speciális szabályok hozzáadása

A megerősítés az interakciók szabályainak hozzáadásával érhető el.

1. A `SetAlarm` meglévő parancsban vegyen fel egy **interakciós szabályt** a középső ablaktáblán a kijelölés `+Add` ikonnal, majd válassza a kapcsolatitevékenység- **szabályok** -> **megerősítése parancsot**.

    Ez a szabály kéri a felhasználót, hogy erősítse meg a riasztás dátumát és időpontját, és egy megerősítést (igen/nem) vár a következő bekapcsoláshoz.

   | Beállítás               | Ajánlott érték                                                                  | Leírás                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Szabály neve             | Dátum és idő megerősítése                                                                | A szabály célját leíró név          |
   | Feltételek            | Kötelező paraméter – > DateTime                                                    | Feltételek, amelyek meghatározzák, hogy a szabály futtatható-e    |   
   | Műveletek               | Beszédfelismerési válasz küldése – > biztosan beállít egy riasztást a következőhöz: {DateTime}?     | A szabály feltételének teljesülésekor végrehajtandó művelet |
   | Elvárások          | A rendszer megerősítést vár a felhasználótól                                                 | Várakozás a következő bekapcsolásra                      |
   | Végrehajtás utáni állapot  | Várakozás a felhasználó bemenetére                                                            | A felhasználó állapota a turn után                  |
  
      > [!div class="mx-imgBorder"]
      > ![Kötelező paraméter-válasz létrehozása](media/custom-speech-commands/add-validation-set-temperature.png)

1. Egy másik kapcsolatitevékenység-szabály hozzáadása a sikeres megerősítés kezeléséhez (a felhasználó igent mondott)

   | Beállítás               | Ajánlott érték                                                                  | Leírás                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Szabály neve             | Jóváhagyás elfogadva                                                            | A szabály célját leíró név          |
   | Feltételek            | A megerősítés sikeres volt & kötelező paraméter-> DateTime                      | Feltételek, amelyek meghatározzák, hogy a szabály futtatható-e    |   
   | Végrehajtás utáni állapot | Befejezési szabályok végrehajtása                                                          | A felhasználó állapota a turn után                   |

1. Speciális szabály hozzáadása a megerősítő megtagadásának kezeléséhez (felhasználó által nem említett)

   | Beállítás               | Ajánlott érték                                                                  | Leírás                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Szabály neve             | Megtagadási megerősítés                                                                   | A szabály célját leíró név          |
   | Feltételek            | A megerősítés megtagadva & kötelező paraméter-> DateTime                               | Feltételek, amelyek meghatározzák, hogy a szabály futtatható-e    |   
   | Műveletek               | Paraméter értékének törlése – > DateTime & a beszédfelismerési válasz küldése – > nincs probléma, milyen időt?  | A szabály feltételének teljesülésekor végrehajtandó művelet |
   | Végrehajtás utáni állapot | Várakozás a bevitelre                                                                   | A felhasználó állapota a turn után                   |
   | Elvárások          | Paraméterek (ek) bevitele a felhasználótól – > DateTime                           | Várakozás a következő bekapcsolásra                      |

## <a name="try-out-the-changes"></a>Próbálja ki a módosításokat

Válassza `Train`ki a betanítás befejezését, `Test`és válassza a lehetőséget.

- Bemenet: riasztás beállítása holnap délben
- Kimenet: biztos, hogy 2020-05-02 12:00:00-as riasztást szeretne beállítani?
- Bemenet: nem
- Kimenet: nincs probléma, milyen időt?
- Bemenet: 5
- Kimenet: "biztos, hogy 2020-05-01 17:00:00-as riasztást szeretne beállítani?"
- Bemenet: igen
- Kimenet: ok, riasztás beállítása 2020-05-01 17:00:00

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Útmutató: egy lépésből álló javítás hozzáadása egyéni parancshoz (előzetes verzió)](./how-to-custom-speech-commands-one-step-correction.md)