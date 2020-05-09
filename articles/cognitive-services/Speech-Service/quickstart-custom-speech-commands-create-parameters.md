---
title: 'Gyors útmutató: Egyéni parancs létrehozása paraméterekkel (előzetes verzió) – Speech Service'
titleSuffix: Azure Cognitive Services
description: Ebben a cikkben paramétereket adhat hozzá egy egyéni parancsok alkalmazáshoz.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: bf77616123f9311f7384fea515f250e47b354c8c
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82853619"
---
# <a name="quickstart-create-a-custom-commands-application-with-parameters-preview"></a>Gyors útmutató: egyéni parancsokat tartalmazó alkalmazás létrehozása paraméterekkel (előzetes verzió)

Az [előző cikkben](./quickstart-custom-speech-commands-create-new.md)egy egyszerű, egyéni parancsokat tartalmazó alkalmazást hozott létre paraméterek nélkül.

Ebben a cikkben kiterjesztjük az alkalmazást a paraméterek használatára, hogy képes legyen több eszköz bekapcsolásának és kikapcsolásának kezelésére.

## <a name="create-parameters"></a>Paraméterek létrehozása

1. Nyissa meg a [korábban létrehozott](./quickstart-custom-speech-commands-create-new.md) projektet
1. Szerkessze a meglévő parancsot, és kapcsolja ki több eszközt.
1. Mivel a parancs most már be-és kikapcsolja a t `TurnOnOff`, nevezze át a parancsot a következőre:.
   - A bal oldali ablaktáblán válassza ki `TurnOn` a parancsot, majd kattintson `...` a melletti `+ New command` ikonra a panel tetején.
   
   - Válassza `Rename` ki az ikont. Az **Átnevezés parancs** előugró ablakában módosítsa a **nevet** a `TurOnOff`következőre:. Ezután válassza a **Mentés**lehetőséget.

1. Ezután létre kell hoznia egy új paramétert, amely azt jelzi, hogy a felhasználó be-vagy kikapcsolni kívánja-e az eszközt.
   - Válassza `+ Add` a középső ablaktábla tetején található ikont. A legördülő menüben válassza a **paraméter**lehetőséget.
   - A jobb oldali panelen láthatja a **Paraméterek** konfigurációs szakaszt.
   - Adja meg a **név**értékét.
   - Jelölje be a **kötelező** jelölőnégyzetet. A **kötelező paraméter hozzáadása** ablakban válassza az **egyszerű szerkesztő** és az **első változat**lehetőséget, és adja hozzá a következőt:
        ```
        On or Off?
        ```
   - Válassza a **Frissítés** lehetőséget.

       > [!div class="mx-imgBorder"]
       > ![Kötelező paraméter-válasz létrehozása](media/custom-speech-commands/add-required-on-off-parameter-response.png)
   
   - Ezután konfigurálja a paraméter további tulajdonságait a következő módon, és válassza `Save` a beállítást, ha az összes konfigurációt a paraméterre szeretné menteni.
       

       | Konfiguráció      | Ajánlott érték     | Leírás                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | Name               | Javítás OnOff           | A paraméter leíró neve                                                                           |
       | Globális          | nincs bejelölve       | Jelölőnégyzet, amely azt jelzi, hogy a paraméter értéke globálisan az alkalmazás összes parancsára vonatkozik-e|
       | Kötelező           | ellenőrizni         | Jelölőnégyzet, amely azt jelzi, hogy a paraméter értéke kötelező-e a parancs végrehajtása előtt |
       | A kötelező paraméterre adott válasz      |Egyszerű szerkesztő – > be-vagy kikapcsolás?      | A paraméter értékének megadására vonatkozó kérés, ha nem ismert |
       | Típus               | Sztring          | A paraméter típusa (például szám, karakterlánc, dátum vagy földrajzi idő)   |
       | Konfiguráció      | Előre megadott bemeneti értékek elfogadása a belső katalógusból | Karakterláncok esetén ez a beállítás a lehetséges értékek halmazára korlátozza a bemeneteket |
       | Előre megadott bemeneti értékek     | be, ki             | A lehetséges értékek és az aliasok halmaza         |
       
        > [!div class="mx-imgBorder"]
        > ![Paraméter létrehozása](media/custom-speech-commands/create-on-off-parameter.png)

   - Ezután kattintson ismét az `+ Add` ikonra egy második paraméter hozzáadásához, hogy az a következő konfigurációval rendelkező eszközök nevét adja meg.
   

       | Beállítás            | Ajánlott érték       | Leírás                                                                                               |
       | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
       | Name               | SubjectDevice         | A paraméter leíró neve                                                                     |
       | Globális          | nincs bejelölve             | Jelölőnégyzet, amely azt jelzi, hogy a paraméter értéke globálisan az alkalmazás összes parancsára vonatkozik-e |
       | Kötelező           | ellenőrizni               | Jelölőnégyzet, amely azt jelzi, hogy a paraméter értéke kötelező-e a parancs végrehajtása előtt          |
       | Egyszerű szerkesztő      | Melyik eszköz?    | A paraméter értékének megadására vonatkozó kérés, ha nem ismert                                       |
       | Típus               | Sztring                | A paraméter típusa (például szám, karakterlánc, dátum vagy földrajzi idő)                                                |
       | Konfiguráció      | Előre megadott bemeneti értékek elfogadása a belső katalógusból | Karakterláncok esetén a sztringek listája a lehetséges értékek egy halmazára korlátozza a bemeneteket       |
       | Előre megadott bemeneti értékek | TV, ventilátor               | A lehetséges értékek és az aliasok halmaza                               |
       | Aliasok (TV)      | televízió, Teller     | Opcionális aliasok az előre megadott bemeneti értékek minden lehetséges értékéhez                                 |

## <a name="add-example-sentences"></a>Példa mondatok hozzáadása

A paraméterekkel rendelkező parancsokkal hasznos lehet olyan példákat felvenni, amelyek az összes lehetséges kombinációra kiterjednek. Például:

1. Paraméterek részletes adatai –`turn {OnOff} the {SubjectDevice}`
1. Részleges paraméterek adatai –`turn it {OnOff}`
1. Nincs paraméter-információ –`turn something`

A különböző szintű információkkal rendelkező mondatok például lehetővé teszik, hogy az egyéni parancsok alkalmazás az egylépéses és a többszörös fordulatú feloldásokat is feloldja a részleges információkkal.

Ezt szem előtt tartva szerkessze a fenti mondatokat, hogy a paramétereket az alább javasolt módon használják.

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```
> [!TIP]
> A példában a mondatok szerkesztő kapcsos zárójeleket használ a paraméterekre való hivatkozáshoz. - `turn {OnOff} the {SubjectDevice}`Használja a TAB billentyűt a korábban létrehozott paraméterek által támogatott automatikus befejezéshez.

## <a name="add-parameters-to-completion-rules"></a>Paraméterek hozzáadása a befejezési szabályokhoz

Módosítsa az [előző](./quickstart-custom-speech-commands-create-new.md)rövid útmutatóban létrehozott befejezési szabályt.

1. A **feltételek** szakaszban adjon hozzá egy új feltételt a **+ feltétel hozzáadása** elem kiválasztásával.
1. Az új előugró **új állapotban**válassza `Required parameters` a **típus** legördülő listából. Az alábbi ellenőrzési listán keresse meg a és `OnOff` `SubjectDevice`a is.
1. Kattintson a **Létrehozás** lehetőségre.
1. A **műveletek** szakaszban szerkessze a meglévő beszédfelismerési választ a művelet fölé, és kattintson a Szerkesztés ikonra. Most végezzük el az újonnan létrehozott `OnOff` és `SubjectDevice` paraméterek használatát

    ```
    Ok, turning {OnOff} the {SubjectDevice}
    ```

## <a name="try-it-out"></a>Próba
1. Válassza `Train` a jobb oldali ablaktábla tetején található ikont.

1. Ha a képzés befejeződik, válassza `Test`a lehetőséget.
    - Ekkor megjelenik egy új **teszt az alkalmazás** ablakában.
    - Próbáljon ki néhány interakciót.

        - Bemenet: a TV kikapcsolása
        - Kimenet: ok, a TV kikapcsolása        
        - Bemenet: a televízió kikapcsolása
        - Kimenet: ok, a TV kikapcsolása
        - Bemenet: kikapcsolás
        - Kimenet: melyik eszköz?
        - Bemenet: a TV
        - Kimenet: ok, a TV kikapcsolása

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Gyors útmutató: egyéni parancsok használata egyéni hanggal (előzetes verzió)](./quickstart-custom-speech-commands-select-custom-voice.md)
