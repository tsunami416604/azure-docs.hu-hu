---
title: Parancs frissítése webes végpontról
titleSuffix: Azure Cognitive Services
description: parancs frissítése webes végpontról
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: encorona
ms.openlocfilehash: 4432843ac93002bc92068db191706352234d76e6
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94571250"
---
# <a name="update-a-command-from-a-web-endpoint"></a>Parancs frissítése webes végpontról

Ha az ügyfélalkalmazás olyan folyamatban lévő parancs állapotának frissítését igényli, amely hangbemenet nélkül fut, a parancs frissítéséhez használhatja a webes végpont hívását is.

Ebből a cikkből megtudhatja, hogyan frissítheti a folyamatban lévő parancsot egy webes végpontról.

## <a name="prerequisites"></a>Előfeltételek
> [!div class = "checklist"]
> * Egy korábban [létrehozott Custom Commands-alkalmazás](quickstart-custom-commands-application.md)

## <a name="create-an-azure-function"></a>Azure-függvény létrehozása 

Ebben a példában egy HTTP-Triggered [Azure-függvényre](https://docs.microsoft.com/azure/azure-functions/) van szükségünk, amely támogatja a következő bemenetet (vagy a bemenet egy részhalmazát).

```JSON
{
  "conversationId": "SomeConversationId",
  "currentCommand": {
    "name": "SomeCommandName",
    "parameters": {
      "SomeParameterName": "SomeParameterValue",
      "SomeOtherParameterName": "SomeOtherParameterValue"
    }
  },
  "currentGlobalParameters": {
      "SomeGlobalParameterName": "SomeGlobalParameterValue",
      "SomeOtherGlobalParameterName": "SomeOtherGlobalParameterValue"
  }
}
```

Lehetővé teszi a bemenet legfontosabb attribútumainak áttekintését.

| Attribútum | Magyarázat |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Beszélgetésazonosító** | a "beszélgetésazonosító" a beszélgetés egyedi azonosítója, amely azt jelzi, hogy ez az azonosító az ügyfélalkalmazás alapján hozható létre. |
| **currentCommand** | a "currentCommand" az a parancs, amely jelenleg aktív a beszélgetésben. |
| **név** | a "Name" a parancs neve, a "parameters" pedig egy Térkép a paraméterek aktuális értékeivel. |
| **currentGlobalParameters** | a "currentGlobalParameters" olyan Térkép is, mint a "parameters", de a globális paraméterekhez van használatban. |

Az Azure-függvény kimenetének a következő formátumot kell támogatnia.

```JSON
{
  "updatedCommand": {
    "name": "SomeCommandName",
    "updatedParameters": {
      "SomeParameterName": "SomeParameterValue"
    },
    "cancel": false
  },
  "updatedGlobalParameters": {
    "SomeGlobalParameterName": "SomeGlobalParameterValue"
  }
}
```

Ezt a formátumot felismerheti, mivel ez ugyanaz, mint a [parancs ügyfélről való frissítésekor](./how-to-custom-commands-update-command-from-client.md). 

Most hozzon létre egy Azure-függvényt a NodeJS alapján, és másolja be ezt a kódot.

```nodejs
module.exports = async function (context, req) {
    context.log(req.body);
    context.res = {
        body: {
            updatedCommand: {
                name: "IncrementCounter",
                updatedParameters: {
                    Counter: req.body.currentCommand.parameters.Counter + 1
                }
            }
        }
    };
}
```

Ha ezt az Azure-függvényt egyéni parancsokból hívjuk, elküldjük a beszélgetés aktuális értékeit, és visszaállítjuk a frissíteni kívánt paramétereket, vagy ha az aktuális parancsot szeretnénk megszakítani.

## <a name="update-the-existing-custom-commands-app"></a>A meglévő egyéni parancsok alkalmazás frissítése

Most hozzon létre egy Azure-függvényt a meglévő egyéni parancsok alkalmazással.

1. Adjon hozzá egy új, IncrementCounter nevű parancsot.
1. Vegyen fel csak egy példa mondatot a "növekmény" értékkel.
1. Adjon hozzá egy, a számláló nevű új paramétert (a fenti Azure-függvényben megadott nevet), amelynek alapértelmezett értéke 0.
1. Adjon hozzá egy IncrementEndpoint nevű új webes végpontot az Azure-függvény URL-címével, és engedélyezze a távoli frissítéseket.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-web-endpoint-with-remote-updates.png" alt-text="Webes végpont beállítása a távoli frissítésekkel":::
1. Hozzon létre egy "IncrementRule" nevű új kapcsolatitevékenység-szabályt, és adjon hozzá egy hívás webes végpont műveletet.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-rule-web-endpoint.png" alt-text="Növekményes szabály":::
1. A művelet konfigurációjában válassza ki a IncrementEndpoint, és állítsa be a sikerességet, hogy a beszéd választ küldjön a számláló értékével és hibaüzenettel ellátott hibával.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-increment-counter-call-endpoint.png" alt-text="Növekményes számláló hívási végpontjának beállítása":::
1. A szabály végrehajtás utáni állapotának beállítása a felhasználó bevitelére való várakozáshoz

## <a name="test-it"></a>Tesztelje

1. Az alkalmazás mentése és betanítása
1. Kattintson a teszt gombra
1. Küldje el néhányszor a "növekményt" (amely a IncrementCounter parancs példája)
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-counter-example.png" alt-text="Növekmény számláló – példa":::

Figyelje meg, hogy a számláló paraméter értéke eggyel növekszik az Azure-függvény minden egyes bekapcsolásakor.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [CI-/CD-folyamat engedélyezése a Custom Commands-alkalmazás számára](./how-to-custom-commands-deploy-cicd.md)