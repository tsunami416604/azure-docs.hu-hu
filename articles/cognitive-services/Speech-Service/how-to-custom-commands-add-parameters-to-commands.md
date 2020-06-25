---
title: 'Útmutató: egyszerű parancsok hozzáadása egyéni parancsokhoz Application-Speech Service'
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan adhat hozzá paramétereket egyéni parancsokhoz
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: d2a14a501ebcf0913804ce39019a3fa4018ca141
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2020
ms.locfileid: "85362373"
---
# <a name="add-parameters-to-commands"></a>Paraméterek hozzáadása parancsokhoz

Ebből a cikkből megtudhatja, hogyan adhat hozzá paramétereket egyéni parancsokhoz. A paraméterek a feladatok végrehajtásához szükséges adatok. Összetett forgatókönyvekben a paramétereket az egyéni műveleteket kiváltó feltételek definiálására is használhatja.

## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * [Útmutató: alkalmazás létrehozása egyszerű parancsokkal](./how-to-custom-commands-create-application-with-simple-commands.md)

## <a name="configure-parameters-for-turnon-command"></a>Paraméterek konfigurálása a TurnOn parancshoz

Szerkessze a meglévő **TurnOn** -parancsot a több eszköz bekapcsolásához és kikapcsolásához.

1. Mivel a parancs mostantól a be-és kikapcsolási forgatókönyvet is kezeli, nevezze át a parancsot a **TurnOnOff**.
   1. A bal oldali ablaktáblán válassza a **TurnOn** parancsot, majd a panel tetején található **új parancs** mellett kattintson a három pont (...) gombra.
   
   1. Válassza az **Átnevezés**lehetőséget. Az **átnevezési parancs** ablakában módosítsa a **nevet** a **TurnOnOff**értékre.

1. Ezután adjon hozzá egy új paramétert ehhez a parancshoz, amely azt jelzi, hogy a felhasználó be-vagy kikapcsolja az eszközt.
   1. A középső ablaktábla tetején kattintson a **Hozzáadás** elemre. A legördülő menüben válassza a **paraméter**lehetőséget.
   1. A jobb oldali ablaktáblában, a **Parameters (paraméterek** ) szakaszban adja hozzá az értéket a **Name (név** ) mezőben **javítás OnOff**.
   1. Válassza a **kötelező**lehetőséget. A **válasz hozzáadása a kötelező paraméterhez** ablakban válassza az **egyszerű szerkesztő**lehetőséget. Az **első változatban**adja hozzá a
        ```
        On or Off?
        ```
   1. Válassza a **Frissítés** lehetőséget.

       > [!div class="mx-imgBorder"]
       > ![Kötelező paraméter-válasz létrehozása](media/custom-commands/add-required-on-off-parameter-response.png)
   
   1. Most konfigurálja a paraméterek tulajdonságait. A parancsok összes konfigurációs tulajdonságának magyarázatát a [hivatkozások](./custom-commands-references.md)menüpontban találja. Konfigurálja a paraméter további tulajdonságait a következő módon:
      

       | Konfiguráció      | Ajánlott érték     | Leírás                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | Name (Név)               | `OnOff`           | A paraméter leíró neve                                                                           |
       | Globális          | nincs bejelölve       | Jelölőnégyzet, amely azt jelzi, hogy a paraméter értéke globálisan az alkalmazás összes parancsára vonatkozik-e|
       | Kötelező           | ellenőrizni         | Jelölőnégyzet, amely azt jelzi, hogy a paraméter értéke kötelező-e a parancs végrehajtása előtt |
       | A kötelező paraméterre adott válasz      |Egyszerű szerkesztő >`On or Off?`      | A paraméter értékének megadására vonatkozó kérés, ha nem ismert |
       | Típus               | Sztring          | A paraméter típusa (például szám, karakterlánc, dátum vagy földrajzi idő)   |
       | Konfiguráció      | Előre megadott bemeneti értékek elfogadása a belső katalógusból | Karakterláncok esetén ez a beállítás a lehetséges értékek halmazára korlátozza a bemeneteket |
       | Előre megadott bemeneti értékek     | `on`, `off`           | A lehetséges értékek és az aliasok halmaza         |
       
        
   1. Előre definiált bemeneti értékek hozzáadásához válassza az **előre definiált bevitel hozzáadása** lehetőséget, majd az **új elem** ablakban írja be a **nevet** a fenti táblázatban megadott módon. Ebben az esetben nem használunk aliasokat, így üresen hagyhatjuk. 
    > [!div class="mx-imgBorder"]
        > ![Paraméter létrehozása](media/custom-commands/create-on-off-parameter.png)
   1. A paraméter összes konfigurációjának mentéséhez válassza a **Mentés** lehetőséget.
 
 ### <a name="add-subjectdevice-parameter"></a>SubjectDevice paraméter hozzáadása 

   1. Ezután a **Hozzáadás** gombra kattintva vegyen fel egy második paramétert, amely a paranccsal vezérelhető eszközök nevét jelöli. Használja a következő konfigurációt.
   

       | Beállítás            | Ajánlott érték       |
       | ------------------ | --------------------- |
       | Name (Név)               | `SubjectDevice`         |
       | Globális          | nincs bejelölve             |
       | Kötelező           | ellenőrizni               |
       | A kötelező paraméterre adott válasz     | Egyszerű szerkesztő >`Which device do you want to control?`    | 
       | Típus               | Sztring                |          |
       | Konfiguráció      | Előre megadott bemeneti értékek elfogadása a belső katalógusból | 
       | Előre megadott bemeneti értékek | `tv`, `fan`               |
       | Aliasok ( `tv` )      | `television`, `telly`     |

   1. Kattintson a **Mentés** gombra

### <a name="modify-example-sentences"></a>Példa mondatok módosítása

A paramétereket tartalmazó parancsok esetében hasznos lehet olyan példákat felvenni, amelyek az összes lehetséges kombinációra kiterjednek. Például:

* Paraméterek részletes adatai –`turn {OnOff} the {SubjectDevice}`
* Részleges paraméterek adatai –`turn it {OnOff}`
* Nincs paraméter-információ –`turn something`

A különböző szintű információkkal rendelkező mondatok például lehetővé teszik, hogy az egyéni parancsok alkalmazás az egylépéses és a többszörös fordulatú feloldásokat is feloldja a részleges információkkal.

Ezt szem előtt tartva szerkessze az alábbi mondatokat a paraméterek használatára a lent javasolt módon:

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

Kattintson a **Mentés** gombra.

> [!TIP]
> A példában a mondatok szerkesztő kapcsos zárójeleket használ a paraméterekre való hivatkozáshoz. - `turn {OnOff} the {SubjectDevice}`Használja a TAB billentyűt a korábban létrehozott paraméterek által támogatott automatikus befejezéshez.

### <a name="modify-completion-rules-to-include-parameters"></a>A befejezési szabályok módosítása a paraméterek belefoglalásához

Módosítsa a meglévő befejezési szabály **ConfirmationResponse**.

1. A **feltételek** szakaszban válassza a **feltétel hozzáadása**lehetőséget.
1. Az **új feltétel** ablak **típus** listájában válassza a **kötelező paraméterek**elemet. Az alábbi ellenőrzési listán keresse meg a **javítás OnOff** és a **SubjectDevice**is.
1. Kattintson a **Létrehozás** gombra.
1. A **műveletek** szakaszban szerkessze a meglévő **beszédfelismerési választ** a művelet fölé, és válassza a Szerkesztés gombot. Ezúttal használja az újonnan létrehozott **javítás OnOff** és **SubjectDevice** paramétereket

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. Kattintson a **Mentés** gombra.

### <a name="try-it-out"></a>Próba
1. A jobb oldali ablaktábla tetején válassza a **vonat** ikont.

1. Ha a képzés befejeződik, válassza a **teszt**lehetőséget. Megjelenik egy teszt, amelyet **az alkalmazás** ablak fog megjeleníteni.
 Próbáljon ki néhány interakciót.

    - Bemenet: a TV kikapcsolása
    - Kimenet: ok, a TV kikapcsolása
    - Bemenet: a televízió kikapcsolása
    - Kimenet: ok, a TV kikapcsolása
    - Bemenet: kikapcsolás
    - Kimenet: melyik eszközt szeretné vezérelni?
    - Bemenet: a TV
    - Kimenet: ok, a TV kikapcsolása

## <a name="configure-parameters-for-settemperature-command"></a>Paraméterek konfigurálása a SetTemperature parancshoz

Módosítsa a **SetTemperature** parancsot úgy, hogy az a felhasználó által irányított hőmérsékletet állítsa be.

Új paraméter **hőmérsékletének** hozzáadása a következő konfigurációval

| Konfiguráció      | Ajánlott érték     |
| ------------------ | ----------------|
| Name (Név)               | `Temperature`           |
| Kötelező           | ellenőrizni         |
| A kötelező paraméterre adott válasz      | Egyszerű szerkesztő >`What temperature would you like?`
| Típus               | Szám          |


Szerkessze a példában szereplő hosszúságú kimondott szöveg a következő értékekre.

```
set the temperature to {Temperature} degrees
change the temperature to {Temperature}
set the temperature
change the temperature
```

Szerkessze a meglévő befejezési szabályokat a következő konfigurációnak megfelelően.

| Konfiguráció      | Ajánlott érték     |
| ------------------ | ----------------|
| Feltételek         | A kötelező paraméter > hőmérséklete           |
| Műveletek           | Beszédfelismerési válasz küldése >`Ok, setting temperature to {Temperature} degrees` |

### <a name="try-it-out"></a>Próba

A módosításokat **betaníthatja** és **tesztelheti** néhány interakcióval.

- Bemenet: hőmérséklet beállítása
- Kimenet: milyen hőmérsékletet szeretne?
- Bemenet: 50 fok
- Kimenet: ok, hőmérséklet beállítása 50 fok

## <a name="configure-parameters-to-the-setalarm-command"></a>Paraméterek konfigurálása a SetAlarm parancshoz

Adja hozzá a **datetime** nevű paramétert a következő konfigurációval.

   | Beállítás                           | Ajánlott érték                     | 
   | --------------------------------- | ----------------------------------------|
   | Name (Név)                              | `DateTime`                               |
   | Kötelező                          | ellenőrizni                                 |
   | A kötelező paraméterre adott válasz   | Egyszerű szerkesztő >`For what time?`            | 
   | Típus                              | DateTime                                |
   | Dátum alapértelmezett értékei                     | Ha a Date hiányzik a mai nap használata            |
   | Idő alapértelmezett értékei                     | Ha hiányzik az idő, használja a nap kezdetét     |


> [!NOTE]
> Ebben a cikkben a karakterlánc, a szám és a DateTime paraméter típusait használjuk. Az összes támogatott paraméter-típus és a hozzájuk tartozó tulajdonságok listáját a [hivatkozások](./custom-commands-references.md)menüpontban érheti el.


Szerkesszen például hosszúságú kimondott szöveg a következő értékekre.

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

Szerkessze a meglévő befejezési szabályokat a következő konfigurációnak megfelelően.

   | Beállítás    | Ajánlott érték                               |
   | ---------- | ------------------------------------------------------- |
   | Műveletek    | Beszédfelismerési válasz küldése –`Ok, alarm set for {DateTime}`  |


### <a name="try-it-out"></a>Próba

A módosítások **betanítása** és **tesztelése** .
- Bemenet: riasztás beállítása holnap délben
- Kimenet: ok, riasztás beállítása 2020-05-02 12:00:00
- Bemenet: riasztás beállítása
- Kimenet: mennyi idő?
- Bemenet: 5
- Kimenet: ok, riasztás beállítása 2020-05-01 17:00:00


## <a name="try-out-all-the-commands"></a>Próbálja ki az összes parancsot

Tesztelje a mindhárom parancsot együtt a különböző parancsokhoz kapcsolódó hosszúságú kimondott szöveg használatával. Vegye figyelembe, hogy a különböző parancsok közötti váltásra is lehetőség van.

- Bemenet: riasztás beállítása
- Kimenet: milyen időpontra?
- Bemenet: kapcsolja be a TV-t
- Kimenet: ok, a TV bekapcsolása
- Bemenet: riasztás beállítása
- Kimenet: milyen időpontra?
- Bemenet: 5
- Kimenet: ok, riasztás beállítása 2020-05-01 17:00:00

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Útmutató: konfigurációk hozzáadása parancsok paramétereinek](./how-to-custom-commands-add-parameter-configuration.md)
