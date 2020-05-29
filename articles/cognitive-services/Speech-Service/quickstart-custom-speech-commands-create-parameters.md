---
title: 'Gyors útmutató: egyéni parancsok előzetes alkalmazásának létrehozása paraméterekkel – Speech Service'
titleSuffix: Azure Cognitive Services
description: Ebben a cikkben paramétereket adhat hozzá egy egyéni parancsok alkalmazáshoz, így több eszközt is be-és kikapcsolhat.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: 5461ef9680ab89c8cc9cc2e1166366abb04a6eab
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2020
ms.locfileid: "84142282"
---
# <a name="quickstart-create-a-custom-commands-preview-application-with-parameters"></a>Gyors útmutató: egyéni parancsok előzetes alkalmazásának létrehozása paraméterekkel

Az [előző cikkben](./quickstart-custom-speech-commands-create-new.md)egy egyszerű, egyéni parancsokat tartalmazó alkalmazást hozott létre paraméterek nélkül.

Ebben a cikkben a paraméterekkel bővíti az alkalmazást, így több eszközt is be-és kikapcsolhat.

## <a name="create-parameters"></a>Paraméterek létrehozása

1. Nyissa meg az [előző cikkben](./quickstart-custom-speech-commands-create-new.md)létrehozott projektet.

   Szerkesztjük a meglévő parancsot, így több eszköz be-és kikapcsolására is használható.
1. Mivel a parancs mostantól be-és kikapcsolja a t, nevezze át a **TurnOnOff**.
   1. A bal oldali ablaktáblán válassza a **TurnOn** parancsot, majd a panel tetején található **új parancs** mellett kattintson a három pont (**..**.) gombra.
   
   1. Válassza az **Átnevezés**lehetőséget. Az **átnevezési parancs** ablakban módosítsa a **nevet** a **TurOnOff**értékre. Kattintson a **Mentés** gombra.

1. Hozzon létre egy paramétert, amely azt jelzi, hogy a felhasználó be-vagy kikapcsolja az eszközt.
   1. A középső ablaktábla tetején válassza a **Hozzáadás** lehetőséget. A legördülő listában válassza a **paraméter**elemet.
   1. A jobb oldali ablaktábla **Paraméterek** szakaszában adjon meg egy értéket a **név** mezőben.
   1. Válassza a **kötelező**lehetőséget. A **válasz hozzáadása a kötelező paraméterhez** ablakban válassza az **egyszerű szerkesztő**lehetőséget. Az **első változat** mezőben adja meg a következő szöveget:
        ```
        On or Off?
        ```
   1. Válassza a **Frissítés** lehetőséget.

       > [!div class="mx-imgBorder"]
       > ![Kötelező paraméter-válasz létrehozása](media/custom-speech-commands/add-required-on-off-parameter-response.png)
   
1. Konfigurálja a paraméter további tulajdonságait a következő módon:
       

    | Konfiguráció      | Ajánlott érték     | Description                                                      |
    | ------------------ | ----------------| ---------------------------------------------------------------------|
    | **Név**               | **Javítás OnOff**           | A paraméter leíró neve.                                                                  |
    | **Globális**          | Törlődik       | Egy jelölőnégyzet, amely azt jelzi, hogy a paraméter értéke globálisan az alkalmazás összes parancsára vonatkozik-e.|
    | **Szükséges**           | Kiválasztva         | Egy jelölőnégyzet, amely azt jelzi, hogy a paraméter értéke kötelező-e.  |
    | **A kötelező paraméterre adott válasz**      |**Egyszerű szerkesztő – > be-vagy kikapcsolás?**      | A paraméter értékének megadására vonatkozó kérés, ha nem ismert. |
    | **Típus**               | **Sztring**          | A paraméter típusa. Például: number, string, Date Time, földrajz.   |
    | **Konfigurálás**      | **Előre megadott bemeneti értékek elfogadása a belső katalógusból** | A karakterláncok esetében ez a beállítás a bemeneteket a lehetséges értékek halmazára korlátozza. |
    | **Előre megadott bemeneti értékek**     | **be**, **ki**             | A lehetséges értékek és az aliasok halmaza.         |
       


    > [!div class="mx-imgBorder"]
    > ![Paraméter létrehozása](media/custom-speech-commands/create-on-off-parameter.png)

1. A beállítások mentéséhez válassza a **Mentés** lehetőséget.

 1. A második paraméter hozzáadásához kattintson ismét a **Hozzáadás** gombra. Ez a paraméter az eszköz nevét jelöli. Használja ezeket a beállításokat:
   

       | Beállítás            | Ajánlott érték       | Description                                                                                               |
       | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
       | **Név**               | **SubjectDevice**         | A paraméter leíró neve.                                                                     |
       | **Globális**          | Törlődik             | Egy jelölőnégyzet, amely azt jelzi, hogy a paraméter értéke globálisan az alkalmazás összes parancsára vonatkozik-e. |
       | **Szükséges**           | Kiválasztva               | Egy jelölőnégyzet, amely azt jelzi, hogy a paraméter értéke kötelező-e.          |
       | **Egyszerű szerkesztő**      | **Melyik eszköz?**    | A paraméter értékének megadására vonatkozó kérés, ha nem ismert.                                       |
       | **Típus**               | **Sztring**                | A paraméter típusa. Például: number, string, Date Time, földrajz.                                                |
       | **Konfigurálás**      | **Előre megadott bemeneti értékek elfogadása a belső katalógusból** | A karakterláncok esetében ez a beállítás a bemeneteket a lehetséges értékek halmazára korlátozza.       |
       | **Előre megadott bemeneti értékek** | **TV**, **ventilátor**               | A lehetséges értékek és az aliasok halmaza.                               |
       | **Aliasok** (TV)      | **televízió**, **Teller**     | Az előre meghatározott bemeneti értékeknél nem kötelező aliasok.                                 |

## <a name="add-example-sentences"></a>Példa mondatok hozzáadása

A paramétereket tartalmazó parancsok esetében hasznos lehet olyan példákat felvenni, amelyek az összes lehetséges kombinációra kiterjednek. Például:

- Paraméterek teljes körű adatai:`turn {OnOff} the {SubjectDevice}`
- Részleges paraméterek adatai:`turn it {OnOff}`
- Nincs paraméter-információ:`turn something`

A különböző mennyiségű információval rendelkező mondatok például lehetővé teszik, hogy az egyéni parancsok alkalmazás a részleges információkkal rendelkező egylépéses és többfordulatú felbontásokat is oldja fel.

Ezt szem előtt tartva szerkessze a példa mondatokat, hogy az itt javasolt paramétereket használja:

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```
> [!TIP]
> A példa a mondatok szerkesztőjében kapcsos zárójelek használatával tekintheti meg a paramétereket: `turn {OnOff} the {SubjectDevice}` .
>
> A korábban létrehozott paraméterek által meghatározott automatikus befejezéshez használja a TAB billentyűt.

## <a name="add-parameters-to-completion-rules"></a>Paraméterek hozzáadása a befejezési szabályokhoz

Módosítsa az [előző](./quickstart-custom-speech-commands-create-new.md)rövid útmutatóban létrehozott befejezési szabályt.

1. A **feltételek** szakaszban válassza a **feltétel hozzáadása**lehetőséget.
1. Az **új feltétel** ablak **típus** listájában válassza a **kötelező paraméterek**elemet. A listában válassza a **javítás OnOff** és a **SubjectDevice**lehetőséget.
1. Kattintson a **Létrehozás** gombra.
1. A **műveletek** szakaszban szerkessze a meglévő **beszédfelismerési választ** a művelet fölé, és válassza a Szerkesztés gombot. Ezúttal használja az új és a `OnOff` `SubjectDevice` paramétereket:

    ```
    Ok, turning {OnOff} the {SubjectDevice}
    ```

## <a name="try-it-out"></a>Próba
1. A jobb oldali ablaktábla tetején válassza a **betanítás** lehetőséget.

1. Ha betanítást végez, válassza a **teszt**lehetőséget.
    
    Megjelenik egy teszt, amelyet **az alkalmazás** ablak fog megjeleníteni.

1. Próbáljon ki néhány interakciót.

        - Input: turn off the tv
        - Output: Ok, turning off the tv        
        - Input: turn off the television
        - Output: Ok, turning off the tv
        - Input: turn it off
        - Output: Which device?
        - Input: the tv
        - Output: Ok, turning off the tv

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Gyors útmutató: egyéni parancsok használata egyéni hanggal (előzetes verzió)](./quickstart-custom-speech-commands-select-custom-voice.md)
