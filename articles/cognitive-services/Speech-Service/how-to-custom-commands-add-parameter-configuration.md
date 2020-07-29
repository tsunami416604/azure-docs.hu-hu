---
title: 'Útmutató: a paraméter konfigurálása külső entitások entitásként'
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan konfigurálhat egy karakterlánc-paramétert egy webes végponton keresztül elérhető katalógusra való hivatkozáshoz.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: sausin
ms.openlocfilehash: 45dba3b7f46ec558c46b8505da26fd3ef4de4cbc
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284183"
---
# <a name="add-configurations-to-commands-parameters"></a>Konfigurációk hozzáadása parancsparaméterekhez

Ebből a cikkből megismerheti a speciális paraméterek konfigurálását, többek között a következőket:

 - Hogyan tartozhatnak a paraméterek az egyéni parancsok alkalmazáshoz külsőleg definiált készletekhez
 - Érvényesítési záradékok hozzáadása a paraméterek értékéhez

## <a name="prerequisites"></a>Előfeltételek

A következő cikkekben ismertetett lépéseket kell végrehajtania:

> [!div class="checklist"]
> * [Útmutató: alkalmazás létrehozása egyszerű parancsokkal](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Útmutató: paraméterek hozzáadása parancsokhoz](./how-to-custom-commands-add-parameters-to-commands.md)


## <a name="configure-parameter-as-external-catalog-entity"></a>A paraméter konfigurálása külső katalógus entitásként

Ebben a szakaszban a karakterlánc típusú paramétereket úgy konfigurálja, hogy a rendszer a webes végponton futó külső katalógusokra hivatkozzon. Ez lehetővé teszi a külső katalógus egymástól független frissítését anélkül, hogy az egyéni parancsok alkalmazásban szerkeszti őket. Ez a megközelítés olyan esetekben hasznos, amikor a katalógus bejegyzéseinek száma nagy lehet.

Használja újra a **SubjectDevice** paramétert a **TurnOnOff** parancsból. A paraméter aktuális konfigurációja a **belső katalógus előre megadott bemeneteit fogadja el**. Ez a paraméter konfigurációjában meghatározott eszközök statikus listájára vonatkozik. Ezt a tartalmat külső adatforrásra szeretnénk áthelyezni, amely egymástól függetlenül frissíthető.

Ehhez először adjon hozzá egy új webes végpontot. Nyissa meg a **webes végpontok** szakaszt a bal oldali ablaktáblán, és adjon hozzá egy új webes végpontot a következő konfigurációval.

| Beállítás | Ajánlott érték |
|----|----|
| Név | `getDevices` |
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

Ezután válassza a **Mentés**lehetőséget.

> [!IMPORTANT]
> Nem jelenik meg olyan paraméter, amely úgy konfigurálja a paramétereket, hogy fogadja a külső katalógus bemeneteit, kivéve, ha a bal oldali ablaktábla **webes végpont** szakaszában a webes végpont meg van határozva.

### <a name="try-it-out"></a>Próba

Válassza **ki a betanítás lehetőséget** , és várja meg a betanítást. Ha befejeződik a betanítás, válassza a **tesztelés** lehetőséget, és próbálkozzon néhány interakcióval.

* Bemenet: bekapcsolás
* Kimenet: melyik eszközt szeretné vezérelni?
* Bemenet: fények
* Kimenet: ok, a fények bekapcsolása

> [!NOTE]
> Figyelje meg, hogy a webes végponton futó összes eszköz most már kezelhető. Továbbra is be kell tanítania az alkalmazást az új módosítások kipróbálásához, majd újra közzé kell tennie az alkalmazást.

## <a name="add-validation-to-parameters"></a>Érvényesítés hozzáadása a paraméterekhez

Az **érvényesítések** bizonyos paraméterekre érvényesek, amelyek lehetővé teszik a korlátozásoknak a paraméter értékére való konfigurálását, és ha az értékek nem a megkötések alá esnek, a rendszer kijavítást kér. Az ellenőrzési összeállítást kiterjesztő paraméterek teljes listájáért lépjen a [referenciák](./custom-commands-references.md) elemre.

Kipróbálhatja az érvényességeket a **SetTemperature** parancs használatával. A következő lépésekkel adhat hozzá egy érvényesítést a **hőmérséklet** paraméterhez.

1. A bal oldali panelen válassza a **SetTemperature** parancsot.
1. Válassza a **hőmérséklet** elemet a középső ablaktáblán.
1. Válassza a jobb oldali ablaktáblán található **Érvényesítés hozzáadása** elemet.
1. Az **új érvényesítési** ablakban adja meg az érvényesítést az alábbiak szerint, majd válassza a **Létrehozás**lehetőséget.


    | Paraméter konfigurálása | Ajánlott érték | Leírás |
    | ---- | ---- | ---- |
    | Minimális érték | `60` | A szám paraméternél a paraméter által feltételezhető minimális érték |
    | Maximális érték | `80` | A szám paraméternél a paraméter által feltételezhető maximális érték |
    | Sikertelen válasz |  Az egyszerű szerkesztő > az első változatot >`Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | Új érték kérése, ha az érvényesítés sikertelen |

    > [!div class="mx-imgBorder"]
    > ![Tartomány érvényesítésének hozzáadása](media/custom-commands/add-validations-temperature.png)

### <a name="try-it-out"></a>Próba

1. A jobb oldali ablaktábla tetején válassza a **vonat** ikont.

1. Miután a betanítás befejeződik, válassza a **test (tesztelés** ) lehetőséget, és próbálkozzon néhány interakcióval:

    - Bemenet: a hőmérséklet beállítása 72 fokos értékre
    - Kimenet: ok, hőmérséklet beállítása 72 fok
    - Bemenet: a hőmérséklet beállítása 45 fokos értékre
    - Kimenet: sajnos csak 60 és 80 fok közötti hőmérsékletet állíthatok be
    - Bemenet: tegyük 72 fok helyett
    - Kimenet: ok, hőmérséklet beállítása 72 fok

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Útmutató: interakciós szabályok hozzáadása](./how-to-custom-commands-add-interaction-rules.md)
