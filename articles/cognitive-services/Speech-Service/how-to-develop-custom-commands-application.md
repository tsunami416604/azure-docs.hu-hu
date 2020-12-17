---
title: 'Útmutató: egyéni parancsok alkalmazásának fejlesztése – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebben a útmutatóban megtudhatja, hogyan fejlesztheti és szabhatja testre az egyéni parancsok alkalmazásait. Az egyéni parancsok megkönnyítik a hang-első interakciós funkciókra optimalizált, sokoldalú hangvezérelt alkalmazások készítését, amely a feladatok befejezéséhez és a parancs-és vezérlési forgatókönyvekhez ideális, különösen a eszközök internetes hálózata (IoT), a környezeti és a fej nélküli eszközök esetében.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: trbye
ms.openlocfilehash: 98c0459e0b67102458169147b1d39e98e2b3e2b1
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632750"
---
# <a name="develop-custom-commands-applications"></a>Egyéni parancsok alkalmazásának fejlesztése

Ebben a útmutatóban megtudhatja, hogyan fejlesztheti és konfigurálhatja az egyéni parancsok alkalmazásait. Az egyéni parancsok megkönnyítik a hang-első interakciós funkciókra optimalizált, sokoldalú hangvezérelt alkalmazások készítését, amely a feladatok befejezéséhez és a parancs-és vezérlési forgatókönyvekhez ideális, különösen a eszközök internetes hálózata (IoT), a környezeti és a fej nélküli eszközök esetében.

Ebben a cikkben egy olyan alkalmazást hoz létre, amely be-és kikapcsolhatja a TV-t, beállíthatja a hőmérsékletet, és beállíthatja a riasztást. Az alapszintű parancsok létrehozása után a következő beállítások érhetők el a parancsok testreszabásához:

* Paraméterek hozzáadása parancsokhoz
* Konfigurációk hozzáadása a parancs paramétereinek
* Interakciós szabályok kiépítése
* Nyelvi létrehozási sablonok létrehozása a beszédfelismerési válaszokhoz
* Egyéni hang használata 

## <a name="create-application-with-simple-commands"></a>Alkalmazás létrehozása egyszerű parancsokkal

Először hozzon létre egy üres egyéni parancsok alkalmazást. További részletekért tekintse meg a rövid [útmutatót.](quickstart-custom-commands-application.md) Ezúttal a projekt importálása helyett egy üres projektet kell létrehoznia.

1. A **név** mezőbe írja be a Project Name (projekt neve `Smart-Room-Lite` ) nevet (vagy valami más választott).
1. A **nyelv** listában válassza az **angol (Egyesült Államok)** lehetőséget.
1. Válasszon ki vagy hozzon létre egy tetszőleges LUIS-erőforrást.

   > [!div class="mx-imgBorder"]
   > ![Projekt létrehozása](media/custom-commands/create-new-project.png)

### <a name="update-luis-resources-optional"></a>LUIS-erőforrások frissítése (nem kötelező)

Frissítheti az **új projekt** ablakban kiválasztott szerzői erőforrást, és megadhat egy előrejelzési erőforrást. A prediktív erőforrás az egyéni parancsok alkalmazás közzétételekor való felismeréshez használatos. A fejlesztési és tesztelési fázisokban nincs szükség előrejelzési erőforrásra.

### <a name="add-turnon-command"></a>TurnOn-parancs hozzáadása

Az imént létrehozott **Smart-Room-Lite** egyéni parancsok alkalmazásban adjon hozzá egy egyszerű parancsot, amely feldolgozza a teljes, `turn on the tv` és válaszol az üzenetre `Ok, turning the tv on` .

1. Hozzon létre egy új parancsot a bal oldali ablaktábla tetején található **új parancs** kiválasztásával. Megnyílik az **új parancssori** ablak.
1. Adja meg a **Name (név** ) mező értékét **TurnOn**.
1. Válassza a **Létrehozás** lehetőséget.

A középső ablaktábla a parancs különböző tulajdonságait listázza. A parancs következő tulajdonságait kell konfigurálnia. A parancsok összes konfigurációs tulajdonságának magyarázatát a [hivatkozások](./custom-commands-references.md)menüpontban találja.

| Konfiguráció            | Leírás                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Példa mondatokra** | Példa arra, hosszúságú kimondott szöveg, hogy a felhasználó el tudja indítani ezt a parancsot                                                                 |
| **Paraméterek**       | A parancs végrehajtásához szükséges információk                                                                                |
| **Befejezési szabályok** | A parancs teljesítéséhez végrehajtandó műveletek. Például a felhasználónak való válaszadásra vagy egy másik webszolgáltatással való kommunikációra. |
| **Interakciós szabályok**   | További szabályok a konkrétabb vagy összetett helyzetek kezelésére                                                              |


> [!div class="mx-imgBorder"]
> ![Parancs létrehozása](media/custom-commands/add-new-command.png)

#### <a name="add-example-sentences"></a>Példa mondatok hozzáadása

Kezdjük **például a mondatok** szakaszát, és példát arra, hogy mit tehet a felhasználó.

1. Válassza a középső ablaktábla **példa mondatok** szakaszát.
1. A jobb oldali panelen adjon hozzá példákat:

    ```
    turn on the tv
    ```

1.  Válassza a **Mentés** lehetőséget a panel tetején.

Egyelőre nem rendelkezünk paraméterekkel, így átléphetünk a **befejezési szabályok** szakaszra.

#### <a name="add-a-completion-rule"></a>Befejezési szabály hozzáadása

Ezután a parancsnak rendelkeznie kell egy befejezési szabállyal. Ez a szabály azt jelzi, hogy a felhasználó teljesíti a teljesítési műveletet. A szabályokkal és a befejezési szabályokkal kapcsolatos további információkért tekintse meg a [referenciákat](./custom-commands-references.md).

1. Válassza az alapértelmezett befejezési szabály **kész** lehetőséget, és szerkessze a következő módon:

    
    | Beállítás    | Ajánlott érték                          | Leírás                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **Név**       | ConfirmationResponse                  | A szabály célját leíró név          |
    | **Feltételek** | Nincs                                     | Feltételek, amelyek meghatározzák, hogy a szabály futtatható-e    |
    | **Műveletek**    | Beszédfelismerési válasz küldése > egyszerű szerkesztő > az első változatban > `Ok, turning the tv on` | A szabály feltételének teljesülésekor végrehajtandó művelet |

   > [!div class="mx-imgBorder"]
   > ![Beszédfelismerési válasz létrehozása](media/custom-commands/create-speech-response-action.png)

1. A művelet mentéséhez válassza a **Mentés** lehetőséget.
1. A **befejezési szabályok** szakaszban kattintson a **Mentés** gombra a módosítások mentéséhez. 

    > [!NOTE]
    > A parancshoz tartozó alapértelmezett befejezési szabályt nem szükséges használni. Ha szükséges, törölheti a meglévő alapértelmezett befejezési szabályt, és hozzáadhatja a saját szabályt is.

### <a name="add-settemperature-command"></a>SetTemperature-parancs hozzáadása

Most adjon hozzá még egy parancs- **SetTemperature** , amely egyetlen Kimondás után `set the temperature to 40 degrees` válaszol az üzenetre `Ok, setting temperature to 40 degrees` .

Kövesse a **TurnOn** parancs utasításait egy új parancs létrehozásához a példa mondat használatával: "**a hőmérséklet beállítása 40 fok**".

Ezután szerkessze a meglévő **befejezett** befejezési szabályokat a következőképpen:

| Beállítás    | Ajánlott érték                          |
| ---------- | ---------------------------------------- |
| Name  | ConfirmationResponse                  |
| Feltételek | Nincs                                     |
| Műveletek    | Beszédfelismerési válasz küldése > egyszerű szerkesztő > az első változatban > `Ok, setting temperature to 40 degrees` |

A **Mentés** gombra kattintva mentse a parancs összes módosítását.

### <a name="add-setalarm-command"></a>SetAlarm-parancs hozzáadása

Hozzon létre egy új parancsot a **SetAlarm** a "**riasztás beállítása a 9. holnaphoz**" című mondat használatával. Ezután szerkessze a meglévő **befejezett** befejezési szabályokat a következőképpen:

| Beállítás    | Ajánlott érték                          |
| ---------- | ---------------------------------------- |
| Szabály neve  | ConfirmationResponse                  |
| Feltételek | Nincs                                     |
| Műveletek    | Beszédfelismerési válasz küldése > egyszerű szerkesztő > az első változatban >`Ok, setting an alarm for 9 am tomorrow` |

A **Mentés** gombra kattintva mentse a parancs összes módosítását.

### <a name="try-it-out"></a>Próbálja ki

Tesztelje a viselkedést a test chat panel használatával. A jobb oldali ablaktábla tetején válassza a **vonat** ikont. A betanítás befejezése után válassza a **teszt** lehetőséget. Próbálja ki a következő kifejezési példákat hangon vagy szövegen keresztül:

- Gépelje be a következőt: a hőmérséklet 40 fok.
- Várt válasz: ok, hőmérséklet beállítása 40 fok
- Írja be a következőt: TV bekapcsolása
- Várt válasz: ok, a TV bekapcsolása
- Gépelje be a következőt: riasztás beállítása 9 holnap
- Várt válasz: ok, riasztás beállítása 9. holnap

> [!div class="mx-imgBorder"]
> ![Tesztelés webes csevegéssel](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> A teszt panelen megadhatja, hogy a rendszer hogyan dolgozza fel a hang/szöveg bemenetet a **részletek részleteivel** .

## <a name="add-parameters-to-commands"></a>Paraméterek hozzáadása parancsokhoz

Ebből a szakaszból megtudhatja, hogyan adhat hozzá paramétereket a parancsokhoz. A paraméterek a feladatok végrehajtásához szükséges adatok. Összetett forgatókönyvekben a paramétereket az egyéni műveleteket kiváltó feltételek definiálására is használhatja.

### <a name="configure-parameters-for-turnon-command"></a>Paraméterek konfigurálása a TurnOn parancshoz

Először szerkessze a meglévő **TurnOn** -parancsot a több eszköz bekapcsolásához és kikapcsolásához.

1. Most, hogy a parancs mostantól a be-és kikapcsolási forgatókönyveket is kezeli, nevezze át a parancsot a **TurnOnOff**.
   1. A bal oldali ablaktáblán válassza a **TurnOn** parancsot, majd a panel tetején található **új parancs** mellett kattintson a három pont (...) gombra.
   
   1. Válassza az **Átnevezés** lehetőséget. Az **átnevezési parancs** ablakában módosítsa a **nevet** a **TurnOnOff** értékre.

1. Ezután adjon hozzá egy új paramétert ehhez a parancshoz, amely azt jelzi, hogy a felhasználó be-vagy kikapcsolja az eszközt.
   1. A középső ablaktábla tetején kattintson a  **Hozzáadás** elemre. A legördülő menüben válassza a **paraméter** lehetőséget.
   1. A jobb oldali ablaktáblában, a **Parameters (paraméterek** ) szakaszban adja hozzá az értéket a **Name (név** ) mezőben **javítás OnOff**.
   1. Válassza a **kötelező** lehetőséget. A **válasz hozzáadása a kötelező paraméterhez** ablakban válassza az **egyszerű szerkesztő** lehetőséget. Az **első változatban** adja hozzá a
        ```
        On or Off?
        ```
   1. Válassza a **Frissítés** lehetőséget.

       > [!div class="mx-imgBorder"]
       > ![Kötelező paraméter-válasz létrehozása](media/custom-commands/add-required-on-off-parameter-response.png)
   
   1. Most konfigurálja a paraméterek tulajdonságait. A parancsok összes konfigurációs tulajdonságának magyarázatát a [hivatkozások](./custom-commands-references.md)menüpontban találja. Konfigurálja a paraméter tulajdonságait a következőképpen:
      

       | Konfiguráció      | Ajánlott érték     | Leírás                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | Név               | `OnOff`           | A paraméter leíró neve                                                                           |
       | Globális          | nincs bejelölve       | Jelölőnégyzet, amely azt jelzi, hogy a paraméter értéke globálisan az alkalmazás összes parancsára vonatkozik-e|
       | Kötelező           | ellenőrizni         | Jelölőnégyzet, amely azt jelzi, hogy a paraméter értéke kötelező-e a parancs végrehajtása előtt |
       | A kötelező paraméterre adott válasz      |Egyszerű szerkesztő > `On or Off?`      | A paraméter értékének megadására vonatkozó kérés, ha nem ismert |
       | Típus               | Sztring          | A paraméter típusa (például szám, karakterlánc, dátum vagy földrajzi idő)   |
       | Konfiguráció      | Előre megadott bemeneti értékek elfogadása a belső katalógusból | Karakterláncok esetén ez a beállítás a lehetséges értékek halmazára korlátozza a bemeneteket |
       | Előre megadott bemeneti értékek     | `on`, `off`           | A lehetséges értékek és az aliasok halmaza         |
       
        
   1. Előre definiált bemeneti értékek hozzáadásához válassza az **előre definiált bevitel hozzáadása** lehetőséget, majd az **új elem**  ablakban írja be a **nevet** a fenti táblázatban megadott módon. Ebben az esetben nem használunk aliasokat, így üresen hagyhatjuk.
   
      > [!div class="mx-imgBorder"]
      > ![Paraméter létrehozása](media/custom-commands/create-on-off-parameter.png)

   1. A paraméter összes konfigurációjának mentéséhez válassza a **Mentés** lehetőséget.
 
#### <a name="add-subjectdevice-parameter"></a>SubjectDevice paraméter hozzáadása 

1. Ezután a **Hozzáadás** gombra kattintva vegyen fel egy második paramétert, amely a paranccsal vezérelhető eszközök nevét jelöli. Használja a következő konfigurációt.


    | Beállítás            | Ajánlott érték       |
    | ------------------ | --------------------- |
    | Name               | `SubjectDevice`         |
    | Globális          | nincs bejelölve             |
    | Kötelező           | ellenőrizni               |
    | A kötelező paraméterre adott válasz     | Egyszerű szerkesztő > `Which device do you want to control?`    | 
    | Típus               | Sztring                |          |
    | Konfiguráció      | Előre megadott bemeneti értékek elfogadása a belső katalógusból | 
    | Előre megadott bemeneti értékek | `tv`, `fan`               |
    | Aliasok ( `tv` )      | `television`, `telly`     |

1. Válassza a **Mentés** lehetőséget

#### <a name="modify-example-sentences"></a>Példa mondatok módosítása

A paramétereket tartalmazó parancsok esetében hasznos lehet olyan példákat felvenni, amelyek az összes lehetséges kombinációra kiterjednek. Például:

* Paraméterek részletes adatai – `turn {OnOff} the {SubjectDevice}`
* Részleges paraméterek adatai – `turn it {OnOff}`
* Nincs paraméter-információ – `turn something`

A különböző szintű információkkal rendelkező mondatok például lehetővé teszik, hogy az egyéni parancsok alkalmazás az egylépéses és a többszörös fordulatú feloldásokat is feloldja a részleges információkkal.

Ezt szem előtt tartva szerkessze az alábbi mondatokat a paraméterek használatára a lent javasolt módon:

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

Válassza a **Mentés** lehetőséget.

> [!TIP]
> A példában a mondatok szerkesztő kapcsos zárójeleket használ a paraméterekre való hivatkozáshoz. - `turn {OnOff} the {SubjectDevice}` Használja a TAB billentyűt a korábban létrehozott paraméterek által támogatott automatikus befejezéshez.

#### <a name="modify-completion-rules-to-include-parameters"></a>A befejezési szabályok módosítása a paraméterek belefoglalásához

Módosítsa a meglévő befejezési szabály **ConfirmationResponse**.

1. A **feltételek** szakaszban válassza a **feltétel hozzáadása** lehetőséget.
1. Az **új feltétel** ablak **típus** listájában válassza a **kötelező paraméterek** elemet. Az alábbi ellenőrzési listán keresse meg a **javítás OnOff** és a **SubjectDevice** is.
1. A **IsGlobal** ne legyen bejelölve.
1. Válassza a **Létrehozás** lehetőséget.
1. A **műveletek** szakaszban szerkessze a meglévő **beszédfelismerési választ** a művelet fölé, és válassza a Szerkesztés gombot. Ezúttal használja az újonnan létrehozott **javítás OnOff** és **SubjectDevice** paramétereket

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. Válassza a **Mentés** lehetőséget.

Próbálja ki a módosításokat úgy, hogy kiválasztja a **vonat** ikont a jobb oldali ablaktábla tetején. Ha a képzés befejeződik, válassza a **teszt** lehetőséget. Megjelenik egy teszt, amelyet **az alkalmazás** ablak fog megjeleníteni. Próbálja ki a következő interakciókat.

- Bemenet: a TV kikapcsolása
- Kimenet: ok, a TV kikapcsolása
- Bemenet: a televízió kikapcsolása
- Kimenet: ok, a TV kikapcsolása
- Bemenet: kikapcsolás
- Kimenet: melyik eszközt szeretné vezérelni?
- Bemenet: a TV
- Kimenet: ok, a TV kikapcsolása

### <a name="configure-parameters-for-settemperature-command"></a>Paraméterek konfigurálása a SetTemperature parancshoz

Módosítsa a **SetTemperature** parancsot úgy, hogy az a felhasználó által irányított hőmérsékletet állítsa be.

Új paraméter **hőmérsékletének** hozzáadása a következő konfigurációval

| Konfiguráció      | Ajánlott érték     |
| ------------------ | ----------------|
| Name               | `Temperature`           |
| Kötelező           | ellenőrizni         |
| A kötelező paraméterre adott válasz      | Egyszerű szerkesztő > `What temperature would you like?`
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
| Műveletek           | Beszédfelismerési válasz küldése > `Ok, setting temperature to {Temperature} degrees` |

### <a name="configure-parameters-to-the-setalarm-command"></a>Paraméterek konfigurálása a SetAlarm parancshoz

Adja hozzá a **datetime** nevű paramétert a következő konfigurációval.

   | Beállítás                           | Ajánlott érték                     | 
   | --------------------------------- | ----------------------------------------|
   | Name                              | `DateTime`                               |
   | Kötelező                          | ellenőrizni                                 |
   | A kötelező paraméterre adott válasz   | Egyszerű szerkesztő > `For what time?`            | 
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
   | Műveletek    | Beszédfelismerési válasz küldése – `Ok, alarm set for {DateTime}`  |

Tesztelje a mindhárom parancsot együtt a különböző parancsokhoz kapcsolódó hosszúságú kimondott szöveg használatával. Vegye figyelembe, hogy a különböző parancsok közötti váltásra is lehetőség van.

- Bemenet: riasztás beállítása
- Kimenet: milyen időpontra?
- Bemenet: kapcsolja be a TV-t
- Kimenet: ok, a TV bekapcsolása
- Bemenet: riasztás beállítása
- Kimenet: milyen időpontra?
- Bemenet: 5
- Kimenet: ok, riasztás beállítása 2020-05-01 17:00:00

## <a name="add-configurations-to-commands-parameters"></a>Konfigurációk hozzáadása parancsparaméterekhez

Ebben a szakaszban további tudnivalókat talál a speciális paraméterek konfigurálásáról, beleértve a következőket:

 - Hogyan tartozhatnak a paraméterek az egyéni parancsok alkalmazáshoz külsőleg definiált készletekhez
 - Érvényesítési záradékok hozzáadása a paraméterek értékéhez

### <a name="configure-parameter-as-external-catalog-entity"></a>A paraméter konfigurálása külső katalógus entitásként

Az egyéni parancsok lehetővé teszik a karakterlánc típusú paraméterek konfigurálását, hogy a webes végponton futó külső katalógusokra hivatkozzon. Ez lehetővé teszi a külső katalógus egymástól független frissítését anélkül, hogy az egyéni parancsok alkalmazásban szerkeszti őket. Ez a megközelítés olyan esetekben hasznos, amikor a katalógus bejegyzéseinek száma nagy lehet.

Használja újra a **SubjectDevice** paramétert a **TurnOnOff** parancsból. A paraméter aktuális konfigurációja a **belső katalógus előre megadott bemeneteit fogadja el**. Ez a paraméter konfigurációjában meghatározott eszközök statikus listájára vonatkozik. Ezt a tartalmat külső adatforrásra szeretnénk áthelyezni, amely egymástól függetlenül frissíthető.

Ehhez először adjon hozzá egy új webes végpontot. Nyissa meg a **webes végpontok** szakaszt a bal oldali ablaktáblán, és adjon hozzá egy új webes végpontot a következő konfigurációval.

| Beállítás | Ajánlott érték |
|----|----|
| Name | `getDevices` |
| URL-cím | `https://aka.ms/speech/cc-sampledevices` |
| Metódus | GET |


Ha az URL-cím javasolt értéke nem működik, egy egyszerű webes végpontot kell konfigurálnia és üzemeltetni, amely egy olyan JSON-t ad vissza, amely az ellenőrzött eszközök listájából áll. A webes végpontnak az alábbiak szerint formázott JSON-t kell visszaadnia:
    
```json
{
    "fan" : [],
    "refrigerator" : [
        "fridge"
    ],
    "lights" : [
        "bulb",
        "bulbs",
        "light"
        "light bulb"
    ],
    "tv" : [
        "telly",
        "television"
        ]
}

```

Ezután nyissa meg a **SubjectDevice** paraméter beállításai lapot, és módosítsa a tulajdonságokat a következőre.

| Beállítás | Ajánlott érték |
| ----| ---- |
| Konfiguráció | Előre megadott bemenetek elfogadása a külső katalógusból |                               
| Katalógus végpontja | getDevices |
| Metódus | GET |

Ezután válassza a **Mentés** lehetőséget.

> [!IMPORTANT]
> Nem jelenik meg olyan paraméter, amely úgy konfigurálja a paramétereket, hogy fogadja a külső katalógus bemeneteit, kivéve, ha a bal oldali ablaktábla **webes végpont** szakaszában a webes végpont meg van határozva.

Próbálja ki a betanítás **lehetőséget,** és várja meg a betanítás befejezését. Ha befejeződik a betanítás, válassza a **tesztelés** lehetőséget, és próbálkozzon néhány interakcióval.

* Bemenet: bekapcsolás
* Kimenet: melyik eszközt szeretné vezérelni?
* Bemenet: fények
* Kimenet: ok, a fények bekapcsolása

> [!NOTE]
> Figyelje meg, hogy a webes végponton futó összes eszköz most már kezelhető. Továbbra is be kell tanítania az alkalmazást az új módosítások kipróbálásához, majd újra közzé kell tennie az alkalmazást.

### <a name="add-validation-to-parameters"></a>Érvényesítés hozzáadása a paraméterekhez

Az **érvényesítések** bizonyos paraméterekre érvényesek, amelyek lehetővé teszik a korlátozásoknak a paraméter értékére való konfigurálását, és ha az értékek nem a megkötések alá esnek, a rendszer kijavítást kér. Az ellenőrzési összeállítást kiterjesztő paraméterek teljes listájáért lépjen a [referenciák](./custom-commands-references.md) elemre.

Kipróbálhatja az érvényességeket a **SetTemperature** parancs használatával. A következő lépésekkel adhat hozzá egy érvényesítést a **hőmérséklet** paraméterhez.

1. A bal oldali panelen válassza a **SetTemperature** parancsot.
1. Válassza a  **hőmérséklet** elemet a középső ablaktáblán.
1. Válassza a jobb oldali ablaktáblán található **Érvényesítés hozzáadása** elemet.
1. Az **új érvényesítési** ablakban adja meg az érvényesítést az alábbiak szerint, majd válassza a **Létrehozás** lehetőséget.


    | Paraméter konfigurálása | Ajánlott érték | Leírás |
    | ---- | ---- | ---- |
    | Minimális érték | `60` | A szám paraméternél a paraméter által feltételezhető minimális érték |
    | Maximális érték | `80` | A szám paraméternél a paraméter által feltételezhető maximális érték |
    | Sikertelen válasz |  Az egyszerű szerkesztő > az első változatot > `Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | Új érték kérése, ha az érvényesítés sikertelen |

    > [!div class="mx-imgBorder"]
    > ![Tartomány érvényesítésének hozzáadása](media/custom-commands/add-validations-temperature.png)

Próbálja ki a jobb oldali ablaktábla tetején látható **vonat** ikon kiválasztásával. Ha befejeződik a betanítás, válassza a **tesztelés** lehetőséget, és próbálkozzon néhány interakcióval:

- Bemenet: a hőmérséklet beállítása 72 fokos értékre
- Kimenet: ok, hőmérséklet beállítása 72 fok
- Bemenet: a hőmérséklet beállítása 45 fokos értékre
- Kimenet: sajnos csak 60 és 80 fok közötti hőmérsékletet állíthatok be
- Bemenet: tegyük 72 fok helyett
- Kimenet: ok, hőmérséklet beállítása 72 fok

## <a name="add-interaction-rules"></a>Használati szabályok hozzáadása

A kapcsolatitevékenység-szabályok az adott vagy összetett helyzetek kezelésére szolgáló *További szabályok* . Habár szabadon létrehozhat egyéni interakciós szabályokat, ebben a példában a következő célcsoportokra vonatkozó interakciós szabályokat kell használnia:

* Parancsok megerősítése
* Egy lépésből álló korrekció hozzáadása parancsokhoz

Ha többet szeretne megtudni a kapcsolati szabályokról, ugorjon a [hivatkozások](./custom-commands-references.md) szakaszra.

### <a name="add-confirmations-to-a-command"></a>Megerősítések hozzáadása parancshoz

Megerősítés hozzáadásához használja a **SetTemperature** parancsot. A megerősítéshez a következő lépésekkel hozhat létre interakciós szabályokat.

1. Válassza ki a **SetTemperature** parancsot a bal oldali ablaktáblán.
1. A középső ablaktáblán a **Hozzáadás** gombra kattintva vegyen fel kapcsolatitevékenység-szabályokat. Ezután válassza a **kapcsolati szabályok**  >  **megerősítése parancsot**.

    Ez a művelet három interakciós szabályt ad hozzá, amely arra kéri a felhasználót, hogy erősítse meg a riasztás dátumát és időpontját, és megerősítse (igen/nem) a következő bekapcsoláshoz.

    1. Módosítsa a **parancs megerősítése** beavatkozási szabályt a következő konfiguráció szerint:
        1. Nevezze át a **nevet** a **hőmérséklet megerősítéséhez**.
        1. Adjon hozzá egy új feltételt a **kötelező paraméterek**  >  **hőmérsékletének** megfelelően.
        1. Új művelet hozzáadása a   >  **beszédfelismerési válasz** típusaként  >  **biztos, hogy a hőmérsékletet {hőmérséklet} fok értékre szeretné beállítani?**
        1. Az **elvárások** szakaszban hagyja meg az alapértelmezett érték **megérkezését a felhasználótól** .
      
         > [!div class="mx-imgBorder"]
         > ![Kötelező paraméter-válasz létrehozása](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Módosítsa a **megerősítő** sikeres interakciós szabályt a sikeres megerősítés kezelésére (a felhasználó azt mondta, hogy igen).
      
          1. A **név** **megerősítő hőmérsékletre** való módosítása sikeres volt.
          1. Hagyja, hogy a már meglévő **megerősítés sikeres** állapotú legyen.
          1. Adjon hozzá egy új feltételt **típus**  >  **kötelező paraméterek**  >  **hőmérsékletének** beállításához.
          1. Hagyja meg a **végrehajtás utáni állapot** alapértelmezett értékét **végrehajtási befejezési szabályokként**.

    1. Módosítsa a **megerősítő megtagadási** kapcsolati szabályt úgy, hogy kezelni tudja a megerősítés megtagadásakor jelentkező forgatókönyveket (a felhasználó nem).

          1. Módosítsa a **nevet** a **megerősítési hőmérséklet megtagadására**.
          1. Hagyja meg a már meglévő **megerősítés** feltételét.
          1. Adjon hozzá egy új feltételt **típus**  >  **kötelező paraméterek**  >  **hőmérsékletének** beállításához.
          1. Adjon hozzá egy új műveletet **típusként**, mert a  >  **beszédfelismerési válasz küldése**  >  **nem jelent problémát. Milyen hőmérsékletet?**
          1. A **felhasználó bemenetének várakozás** után hagyja meg a **végrehajtás utáni állapot** alapértelmezett értékét.

> [!IMPORTANT]
> Ebben a cikkben a beépített megerősítő funkciót használta. A kapcsolatitevékenység-szabályokat manuálisan is hozzáadhatja egyenként.
   
Próbálja ki a módosításokat a **betanítás lehetőség kiválasztásával**, várjon, amíg befejeződik a betanítás, és válassza a **teszt** lehetőséget.

- **Bemenet**: hőmérséklet beállítása 80 fokos értékre
- **Kimenet**: biztos, hogy 80 fokos hőmérsékletet kíván beállítani?
- **Bemenet**: nem
- **Kimenet**: nincs probléma. Milyen hőmérsékletet?
- **Bemenet**: 72 fok
- **Kimenet**: biztos, hogy 72 fokos hőmérsékletet kíván beállítani?
- **Bemenet**: igen
- **Kimenet**: ok, hőmérséklet beállítása 83 fok

### <a name="implement-corrections-in-a-command"></a>Javítások implementálása egy parancsban

Ebben a szakaszban egy egylépéses korrekciót állít be, amelyet a rendszer a teljesítési művelet végrehajtása után használ. Azt is láthatja, hogyan engedélyezhető a javítás alapértelmezés szerint, ha a parancs még nem teljesül. Ha a parancs nem fejeződött be, adja hozzá az új **AlarmTone** paramétert a javításhoz.

Válassza ki a **SetAlarm** parancsot a bal oldali ablaktáblán, és adja hozzá az új **AlarmTone** paramétert.
        
- **Név**  >  **AlarmTone**
- **Típus**  >  **Karakterlánc**
- **Alapértelmezett érték**  >  **Harangjáték**
- **Konfiguráció**  >  **Előre megadott bemeneti értékek elfogadása a belső katalógusból**
- **Előre megadott bemeneti értékek**  >  **Harangjáték**, **Jingle** és **echo** egyéni előre definiált bemenetként


Ezután frissítse a **datetime** paraméterre adott választ úgy, hogy a **riasztást a (z) {AlarmTone} hangjelzéssel állítsa be. Milyen időpontra?** Ezután módosítsa a befejezési szabályt a következőképpen:

1. Válassza ki a meglévő befejezési szabályt **ConfirmationResponse**.
1. A jobb oldali ablaktáblán vigye a kurzort a meglévő művelet fölé, és válassza a **Szerkesztés** lehetőséget.
1. Frissítse a beszédfelismerési választ az OK értékre **, a riasztási készletet pedig a következőre: {DateTime}. Az ébresztési hang: {AlarmTone}.**

> [!IMPORTANT]
> Az ébresztési hang egy folyamatban lévő parancs explicit konfigurációja nélkül is módosítható, például ha a parancs még nem fejeződött be. *Alapértelmezés szerint a parancs összes paramétere esetében engedélyezve van a javítás, függetlenül attól, hogy a parancs még teljesítve van-e.*

#### <a name="correction-when-command-is-completed"></a>Javítás a parancs befejezésekor

Az egyéni parancsok platform lehetővé teszi egy egylépéses javítás lehetőségét is, még akkor is, ha a parancs befejeződött. Ez a funkció alapértelmezés szerint nincs engedélyezve. Explicit módon kell konfigurálni. A következő lépésekkel konfigurálhat egy egylépéses javítást.

1. A **SetAlarm** parancsban vegyen fel egy interakciós szabályt az **előző verzió frissítése paranccsal** a korábban beállított riasztás frissítéséhez. Nevezze át a kapcsolatitevékenység-szabály alapértelmezett **nevét** az **előző riasztás frissítéséhez**.
1. Hagyja meg az alapértelmezett feltétel **előző parancsának frissítését** .
1. Adjon hozzá egy új feltételt **típus**  >  **kötelező paraméterének**  >  **datetime** értékként.
1. Vegyen fel egy új műveletet **típusként** a  >  **beszédfelismerési válasz**  >  **egyszerű szerkesztője**  >  **a korábbi riasztási idő frissítése a következőre: {DateTime}.**
1. A **végrehajtás utáni állapot** alapértelmezett értékének meghagyása a **parancs végrehajtása után**.

Próbálja ki a módosításokat a **betanítás lehetőség kiválasztásával**, várjon, amíg befejeződik a betanítás, és válassza a **teszt** lehetőséget.

- **Bemenet**: riasztás beállítása.
- **Kimenet**: készen áll a riasztás hangjelzéssel való beállítására. Mennyi ideig?
- **Bemenet**: állítson be egy riasztást a csengőhangként a 9. holnapnál.
- **Kimenet**: ok, riasztás beállítva a 2020-05-21 09:00:00-hez. Az ébresztési hang a Jingle.
- **Bemenet**: nem, 8.
- **Kimenet**: az előző riasztási idő frissítése 2020-05-29 08:00-re.

> [!NOTE]
> Egy valós alkalmazásban a helyesbítő szabály **műveletek** szakaszában is el kell küldenie egy tevékenységet az ügyfélnek, vagy egy http-végpontot kell meghívnia, hogy frissítse a riasztási időt a rendszeren. Ennek a műveletnek kizárólag a riasztási idő frissítéséhez kell tartoznia, és nem a parancs egyéb attribútumait. Ebben az esetben ez az ébresztési hang lenne.

## <a name="add-language-generation-templates-for-speech-responses"></a>Nyelvlétrehozási sablonok hozzáadása Speech-válaszokhoz

A nyelvi létrehozási sablonok lehetővé teszik az ügyfélnek küldött válaszok testreszabását, és a válaszok eltérésének bevezetését. A nyelvi létrehozás testreszabását az alábbiak érhetik el:

* Nyelvi létrehozási sablonok használata
* Adaptív kifejezések használata

Az egyéni parancsok sablonjai a Botframework témakörben [LG-sablonjain](/azure/bot-service/file-format/bot-builder-lg-file-format#templates)alapulnak. Mivel az egyéni parancsok szükség esetén új LG-sablont hoznak létre (azaz a paraméterekben vagy műveletekben való beszédfelismerési válaszokhoz), nem kell megadnia az LG-sablon nevét. Tehát ahelyett, hogy a sablont a következőképpen definiálja:

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

A sablon törzsét csak a név nélkül kell meghatároznia, az alábbiak szerint.

> [!div class="mx-imgBorder"]
> ![Példa sablon-szerkesztőre](./media/custom-commands/template-editor-example.png)


Ez a módosítás bevezeti az ügyfélnek küldött beszédfelismerési válaszok változásait. Tehát a megfelelő beszéd választ véletlenszerűen kiválaszthatja a rendelkezésre álló lehetőségek közül.

Az LG-sablonok kihasználása lehetővé teszi, hogy az adaptív kifejezéseket használó parancsok összetett beszédes válaszait is meghatározhatja. További részletekért tekintse meg az [LG-sablonok formátumát](/azure/bot-service/file-format/bot-builder-lg-file-format#templates) . Az egyéni parancsok alapértelmezés szerint a következő kisebb eltérésekkel rendelkező összes képességet támogatják:

* Az LG-sablonok entitások $ {entityName} néven jelennek meg. Az egyéni parancsokban nem használunk entitásokat, de a paraméterek használhatók változókként a következő ábrázolások egyikével: $ {parameterName} vagy {parameterName}
* Az egyéni parancsok nem támogatják a sablonok összeállítását és bővítését. Ennek az az oka, hogy soha nem szerkeszti a `.lg` fájlt közvetlenül, de csak az automatikusan létrehozott sablonokra adott válaszokat.
* Az LG által befecskendezett egyéni függvények nem támogatottak az egyéni parancsokban. Az előre definiált függvények továbbra is támogatottak.
* A beállítások (Strict, replaceNull & lineBreakStyle) egyéni parancsokban nem támogatottak.

### <a name="add-template-responses-to-turnonoff-command"></a>Sablonra adott válaszok hozzáadása a TurnOnOff parancshoz

Módosítsa a **TurnOnOff** parancsot új paraméter hozzáadásához a következő konfigurációval:

| Beállítás            | Ajánlott érték       | 
| ------------------ | --------------------- | 
| Name               | `SubjectContext`         | 
| Globális          | nincs bejelölve             | 
| Kötelező           | nincs bejelölve               | 
| Típus               | Sztring                |
| Alapértelmezett érték      | `all` |
| Konfiguráció      | Előre megadott bemeneti értékek elfogadása a belső katalógusból | 
| Előre megadott bemeneti értékek | `room`, `bathroom`, `all`|

#### <a name="modify-completion-rule"></a>Befejezési szabály módosítása

Szerkessze a meglévő befejezési szabály **ConfirmationResponse** **műveletek** szakaszát. A **szerkesztési művelet** előugró ablakában váltson a **sablon-szerkesztőre** , és cserélje le a szöveget a következő példára.

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

Az alábbiak szerint **tanítsa** be és **tesztelje** alkalmazását. Figyelje meg a válaszok variációját a sablon értékének több alternatívájának használata miatt, valamint az adaptív kifejezések használatát is.

* Bemenet: kapcsolja be a TV-t
* Kimenet: ok, a TV bekapcsolása
* Bemenet: kapcsolja be a TV-t
* Kimenet: kész, bekapcsolva a tévén
* Bemenet: a fények kikapcsolása
* Kimenet: ok, az összes jelzőfény kikapcsolása
* Bemenet: a helyiségek fényének kikapcsolása
* Kimenet: ok, a helyiség fényének kikapcsolása

## <a name="use-custom-voice"></a>A Custom Voice használata

Az egyéni parancsok válaszának egy másik módja az egyéni kimeneti hang kiválasztása. Az alábbi lépések végrehajtásával válthat az alapértelmezett hang egyéni hangra.

1. Az egyéni parancsok alkalmazásban válassza a bal oldali ablaktábla **Beállítások** elemét.
1. Válassza az **egyéni hang** lehetőséget a középső ablaktáblán.
1. Válassza ki a kívánt egyéni vagy nyilvános hangot a táblából.
1. Válassza a **Mentés** lehetőséget.

> [!div class="mx-imgBorder"]
> ![Minta mondatok paraméterekkel](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - A **nyilvános hangok** esetében az **neurális típusok** csak bizonyos régiókban érhetők el. A rendelkezésre állás ellenőrzéséhez tekintse meg a [standard és a neurális hangok régiónként/végpont szerint](./regions.md#standard-and-neural-voices)című témakört.
> - Az **Egyéni hangokat** az egyéni hangprojektek lapról lehet létrehozni. Lásd: Ismerkedés [az egyéni hanggal](./how-to-custom-voice.md).

Mostantól az alkalmazás az alapértelmezett hang helyett a kiválasztott hangon fog válaszolni.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [integrálható az egyéni parancsok alkalmazása](how-to-custom-commands-setup-speech-sdk.md) egy ügyfélalkalmazás segítségével a Speech SDK használatával.
* [Állítsa be a folyamatos üzembe helyezést](how-to-custom-commands-deploy-cicd.md) az egyéni parancsok alkalmazáshoz az Azure DevOps. 
                      