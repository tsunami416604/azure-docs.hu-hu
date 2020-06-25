---
title: 'Útmutató: alkalmazás létrehozása egyszerű parancsokkal – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan hozhat létre és tesztelheti a futtatott egyéni parancsokat az egyszerű parancsokkal.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 8e0927ccd8c94f589adf6eb11004b728f697b6e1
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2020
ms.locfileid: "85362407"
---
# <a name="create-application-with-simple-commands"></a>Alkalmazás létrehozása egyszerű parancsokkal

Ebben a cikkben az alábbiakkal ismerkedhet meg:
 - Üres alkalmazás létrehozása
 - LUIS-erőforrások frissítése
 - Adjon hozzá néhány alapszintű parancsot az egyéni parancsok alkalmazáshoz

## <a name="create-empty-application"></a>Üres alkalmazás létrehozása
Hozzon létre egy üres egyéni parancsok alkalmazást. További részletekért tekintse meg a rövid [útmutatót.](quickstart-custom-commands-application.md) Ezúttal a projekt importálása helyett egy üres projektet kell létrehoznia.

1. A **név** mezőbe írja be a Project Name (projekt neve `Smart-Room-Lite` ) nevet (vagy valami más választott).
1. A **nyelv** listában válassza az **angol (Egyesült Államok)** lehetőséget.
1. Válasszon ki vagy hozzon létre egy tetszőleges LUIS-erőforrást.

   > [!div class="mx-imgBorder"]
   > ![Projekt létrehozása](media/custom-commands/create-new-project.png)

## <a name="update-luis-resources-optional"></a>LUIS-erőforrások frissítése (nem kötelező)

Frissítheti az **új projekt** ablakban kiválasztott szerzői erőforrást, és megadhat egy előrejelzési erőforrást. A prediktív erőforrás az egyéni parancsok alkalmazás közzétételekor való felismeréshez használatos. A fejlesztési és tesztelési fázisokban nincs szükség előrejelzési erőforrásra.

## <a name="add-turnon-command"></a>TurnOn-parancs hozzáadása

Az imént létrehozott **Smart-Room-Lite** egyéni parancsok alkalmazásban adjon hozzá egy egyszerű parancsot, amely feldolgozza a teljes, `turn on the tv` és válaszol az üzenetre `Ok, turning the tv on` .

1. Hozzon létre egy új parancsot a bal oldali ablaktábla tetején található **új parancs** kiválasztásával. Megnyílik az **új parancssori** ablak.
1. Adja meg a **Name (név** ) mező értékét **TurnOn**.
1. Kattintson a **Létrehozás** gombra.

A középső ablaktábla a parancs különböző tulajdonságait listázza. A parancs következő tulajdonságait kell konfigurálnia. A parancsok összes konfigurációs tulajdonságának magyarázatát a [hivatkozások](./custom-commands-references.md)menüpontban találja.

| Konfiguráció            | Leírás                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Példa mondatokra** | Példa arra, hosszúságú kimondott szöveg, hogy a felhasználó el tudja indítani ezt a parancsot                                                                 |
| **Paraméterek**       | A parancs végrehajtásához szükséges információk                                                                                |
| **Befejezési szabályok** | A parancs teljesítéséhez végrehajtandó műveletek. Például a felhasználónak való válaszadásra vagy egy másik webszolgáltatással való kommunikációra. |
| **Interakciós szabályok**   | További szabályok a konkrétabb vagy összetett helyzetek kezelésére                                                              |


> [!div class="mx-imgBorder"]
> ![Parancs létrehozása](media/custom-commands/add-new-command.png)

### <a name="add-example-sentences"></a>Példa mondatok hozzáadása

Kezdjük **például a mondatok** szakaszát, és példát arra, hogy mit tehet a felhasználó.

1. Válassza a középső ablaktábla **példa mondatok** szakaszát.
1. A jobb oldali panelen adjon hozzá példákat:

    ```
    turn on the tv
    ```

1.  Válassza a **Mentés** lehetőséget a panel tetején.

Egyelőre nem rendelkezünk paraméterekkel, így átléphetünk a **befejezési szabályok** szakaszra.

### <a name="add-a-completion-rule"></a>Befejezési szabály hozzáadása

Ezután a parancsnak rendelkeznie kell egy befejezési szabállyal. Ez a szabály azt jelzi, hogy a felhasználó teljesíti a teljesítési műveletet. A szabályokkal és a befejezési szabályokkal kapcsolatos további információkért tekintse meg a [referenciákat](./custom-commands-references.md).

1. Válassza az alapértelmezett befejezési szabály **kész** lehetőséget, és szerkessze a következő módon:

    
    | Beállítás    | Ajánlott érték                          | Leírás                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **Name (Név)**       | ConfirmationResponse                  | A szabály célját leíró név          |
    | **Feltételek** | None                                     | Feltételek, amelyek meghatározzák, hogy a szabály futtatható-e    |
    | **Műveletek**    | Beszédfelismerési válasz küldése > egyszerű szerkesztő > az első változatban >`Ok, turning the tv on` | A szabály feltételének teljesülésekor végrehajtandó művelet |
    



   > [!div class="mx-imgBorder"]
   > ![Beszédfelismerési válasz létrehozása](media/custom-commands/create-speech-response-action.png)

1. A művelet mentéséhez válassza a **Mentés** lehetőséget.
1. A **befejezési szabályok** szakaszban kattintson a **Mentés** gombra a módosítások mentéséhez. 


 > [!NOTE]
    > A parancshoz tartozó alapértelmezett befejezési szabályt nem szükséges használni. Ha szükséges, törölheti a meglévő alapértelmezett befejezési szabályt, és hozzáadhatja a saját szabályt is.

### <a name="try-it-out"></a>Próba

A viselkedés tesztelése a csevegési teszt panel használatával
1. A jobb oldali ablaktábla tetején válassza a **vonat** ikont.
1. Miután a betanítás befejeződik, válassza a **teszt**lehetőséget. Próbálja ki a következő megnyilatkozás hang/szöveg használatával:
    - Bemenet: kapcsolja be a TV-t
    - Kimenet: ok, a TV bekapcsolása


> [!div class="mx-imgBorder"]
> ![Tesztelés webes csevegéssel](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> A teszt panelen megadhatja, hogy a rendszer hogyan dolgozza fel a hang/szöveg bemenetet a **részletek részleteivel** .  

## <a name="add-settemperature-command"></a>SetTemperature-parancs hozzáadása

Most adjon hozzá még egy parancs- **SetTemperature** , amely egyetlen Kimondás után `set the temperature to 40 degrees` válaszol az üzenetre `Ok, setting temperature to 40 degrees` .

Kövesse a **TurnOn** parancs utasításait egy új parancs létrehozásához a példa mondat használatával: "**a hőmérséklet beállítása 40 fok**".

Ezután szerkessze a meglévő **befejezett** befejezési szabályokat a következőképpen:

| Beállítás    | Ajánlott érték                          |
| ---------- | ---------------------------------------- |
| Name (Név)  | ConfirmationResponse                  |
| Feltételek | None                                     |
| Műveletek    | Beszédfelismerési válasz küldése > egyszerű szerkesztő > az első változatban >`Ok, setting temperature to 40 degrees` |

A **Mentés** gombra kattintva mentse a parancs összes módosítását.

## <a name="add-setalarm-command"></a>SetAlarm-parancs hozzáadása
Hozzon létre egy új parancsot a **SetAlarm** a "**riasztás beállítása a 9. holnaphoz**" című mondat használatával. Ezután szerkessze a meglévő **befejezett** befejezési szabályokat a következőképpen:

| Beállítás    | Ajánlott érték                          |
| ---------- | ---------------------------------------- |
| Szabály neve  | ConfirmationResponse                  |
| Feltételek | None                                     |
| Műveletek    | Beszédfelismerési válasz küldése > egyszerű szerkesztő > az első változatban >`Ok, setting an alarm for 9 am tomorrow` |

A **Mentés** gombra kattintva mentse a parancs összes módosítását.

## <a name="try-it-out"></a>Próba

A viselkedés tesztelése a csevegési teszt panel használatával
1. Válassza a **betanítás**lehetőséget. A sikeres képzés után válassza a **tesztelés** és kipróbálás lehetőséget:
    - Gépelje be a következőt: a hőmérséklet 40 fok.
    - Várt válasz: ok, hőmérséklet beállítása 40 fok
    - Írja be a következőt: TV bekapcsolása
    - Várt válasz: ok, a TV bekapcsolása
    - Gépelje be a következőt: riasztás beállítása 9 holnap
    - Várt válasz: ok, riasztás beállítása 9. holnap

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Útmutató: paraméterek hozzáadása parancsokhoz](./how-to-custom-commands-add-parameters-to-commands.md)
