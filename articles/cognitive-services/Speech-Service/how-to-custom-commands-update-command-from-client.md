---
title: Parancsok frissítése az ügyfélalkalmazásból
titleSuffix: Azure Cognitive Services
description: parancs frissítése az ügyfélalkalmazás alapján
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: encorona
ms.openlocfilehash: 1bffb09d0f49bbd0059e8a528d67bfe215f0650d
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654347"
---
# <a name="update-a-command-from-the-client"></a>Parancs frissítése az ügyfélről

Ebből a cikkből megtudhatja, hogyan frissítheti az ügyfélalkalmazások egy folyamatban lévő parancsát.

## <a name="prerequisites"></a>Előfeltételek
> [!div class = "checklist"]
> * Egy korábban [létrehozott Custom Commands-alkalmazás](quickstart-custom-commands-application.md)

## <a name="update-the-state-of-a-command"></a>Parancs állapotának frissítése

Ha az ügyfélalkalmazás a folyamatban lévő parancs hangbemenet nélküli állapotának frissítését igényli, küldhet egy eseményt a parancs frissítéséhez.

Ha ezt a forgatókönyvet szeretné bemutatni, egy folyamatban lévő parancs (TurnOnOff) állapotának frissítéséhez a következő eseményt küldheti el. 

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "updatedCommand": {
      "name": "TurnOnOff",
      "updatedParameters": {
        "OnOff": "on"
      },
      "cancel": false
    },
    "updatedGlobalParameters": {},
    "processTurn": true
  }
}
```

Lehetővé teszi ennek a tevékenységnek a fő attribútumainak áttekintését.

| Attribútum | Magyarázat |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **típusa** | A tevékenység "Event" típusú, az esemény nevének pedig "RemoteUpdate" értéknek kell lennie. |
| **value** | A "value" attribútum az aktuális parancs frissítéséhez szükséges attribútumokat tartalmazza. |
| **updatedCommand** | A "updatedCommand" attribútum tartalmazza a parancs nevét, a "updatedParameters" pedig a paraméterek nevével és a frissített értékekkel rendelkező térképet. |
| **Mégse** | Ha a folyamatban lévő parancsot meg kell szüntetni, állítsa a "Mégse" attribútumot igaz értékre. |
| **updatedGlobalParameters** | A "updatedGlobalParameters" attribútum egy Térkép is, ugyanúgy, mint a "updatedParameters", de a globális paraméterekhez használatos. |
| **processTurn** | Ha a turnön a tevékenység elküldése után kell feldolgozni, állítsa a "processTurn" attribútumot igaz értékre. |

Ezt a forgatókönyvet az egyéni parancsok portálon ellenőrizheti.

1. Nyissa meg a korábban létrehozott Custom Commands-alkalmazást. 
1. Kattintson a betanítás, majd a tesztelés elemre.
1. Küldés "turn" (bekapcsolás).
1. Nyissa meg az oldalsó panelt, és kattintson a tevékenység-szerkesztő elemre.
1. Írja be és küldje el az előző szakaszban megadott RemoteCommand eseményt.
    > [!div class="mx-imgBorder"]
    > ![Távoli parancs küldése](media/custom-commands/send-remote-command-activity.png)

Figyelje meg, hogy a "javítás OnOff" paraméter értéke "on" értékre van állítva, ha az ügyfél tevékenységét beszéd vagy szöveg helyett használja.

## <a name="update-the-catalog-of-the-parameter-for-a-command"></a>Egy parancs paraméterének katalógusának frissítése

Egy paraméter érvényes beállításainak listájának konfigurálásakor a paraméter értékei globálisan vannak meghatározva az alkalmazáshoz. 

A példánkban a SubjectDevice paraméter a támogatott értékek rögzített listáját fogja tartalmazni, függetlenül a beszélgetéstől.

Ha új bejegyzéseket kell hozzáadnia a paraméter katalógusához, akkor a következő tevékenység elküldésére van szükség.

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "catalogUpdate": {
      "commandParameterCatalogs": {
        "TurnOnOff": [
          {
            "name": "SubjectDevice",
            "values": {
              "stereo": [
                "cd player"
              ]
            }
          }
        ]
      }
    },
    "processTurn": false
  }
}
```
Ezzel a tevékenységgel hozzáadtak egy "sztereó" bejegyzést a "SubjectDevice" paraméter katalógusához a "TurnOnOff" parancsban.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-commands/update-catalog-with-remote-activity.png" alt-text="Frissítési katalógus":::

Jegyezzen fel néhány dolgot.
1. Ezt a tevékenységet csak egyszer kell elküldeni (ideális esetben közvetlenül a kapcsolódás megkezdése után).
1. A tevékenység elküldése után meg kell várnia, hogy az esemény ParameterCatalogsUpdated vissza lehessen küldeni az ügyfélnek.

## <a name="add-additional-context-from-the-client-application"></a>További környezet hozzáadása az ügyfélalkalmazás számára

Az ügyfélalkalmazás további kontextusát is beállíthatja, amely később az egyéni parancsok alkalmazásban használható. 

Gondoljon például arra a forgatókönyvre, amelyben el szeretné küldeni az egyéni parancsok alkalmazáshoz csatlakoztatott eszköz AZONOSÍTÓját és nevét.

A forgatókönyv teszteléséhez hozzon létre egy új parancsot az aktuális alkalmazásban.
1. Hozzon létre egy új, GetDeviceInfo nevű parancsot.
1. Vegyen fel egy példa mondatot az "eszköz adatainak beolvasása" kifejezésre.
1. A befejezési szabály "kész" részében adjon hozzá egy beszédfelismerési válasz küldése műveletet, amely tartalmazza a clientContext attribútumait.
    > ![Beszédfelismerési válasz küldése a kontextussal](media/custom-commands/send-speech-response-context.png)
1. Az alkalmazás mentése, betanítása és tesztelése.
1. A tesztelési ablakban egy tevékenység elküldésével frissítheti az ügyfél környezetét.
    > ```json
    >{
    >   "type": "event",
    >   "name": "RemoteUpdate",
    >   "value": {
    >     "clientContext": {
    >       "deviceId": "12345",
    >       "deviceName": "My device"
    >     },
    >     "processTurn": false
    >   }
    >}
    > ```
1. Küldje el az "eszköz adatainak beolvasása" szöveget.
    > ![Ügyfél-környezeti tevékenység küldése](media/custom-commands/send-client-context-activity.png)

Jegyezze fel néhány dolgot.
1. Ezt a tevékenységet csak egyszer kell elküldeni (ideális esetben közvetlenül a kapcsolódás megkezdése után).
1. Összetett objektumokat használhat a clientContext.
1. A clientContext a beszédfelismerési válaszok, a tevékenységek küldésére és a webes végpontok meghívására használhatja.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Parancsok frissítése webes végpontról](./how-to-custom-commands-update-command-from-web-endpoint.md)
