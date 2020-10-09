---
title: 'Útmutató: a beszédfelismerési válaszok nyelvi létrehozási sablonjainak használata – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan használhatja a nyelvi létrehozási sablonokat egyéni parancsokkal. A nyelvi létrehozási sablonok lehetővé teszik az ügyfélnek küldött válaszok testreszabását, és a válaszok eltérésének bevezetését.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 0cbc57922b31f1b3879bb2cad8a988a1ba4cc368
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85307832"
---
# <a name="add-language-generation-templates-for-speech-responses"></a>Nyelvlétrehozási sablonok hozzáadása Speech-válaszokhoz

Ebből a cikkből megtudhatja, hogyan használhatja a nyelvi létrehozási sablonokat egyéni parancsokkal. A nyelvi létrehozási sablonok lehetővé teszik az ügyfélnek küldött válaszok testreszabását, és a válaszok eltérésének bevezetését. A nyelvi létrehozás testreszabását az alábbiak érhetik el:

- Nyelvi létrehozási sablonok használata
- Adaptív kifejezések használata

## <a name="prerequisites"></a>Előfeltételek

A következő cikkekben ismertetett lépéseket kell végrehajtania:

> [!div class="checklist"]
> * [Útmutató: alkalmazás létrehozása egyszerű parancsokkal](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Útmutató: paraméterek hozzáadása parancsokhoz](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="language-generation-templates-overview"></a>Nyelvi létrehozási sablonok – áttekintés

Az egyéni parancsok sablonjai a Botframework témakörben [LG-sablonjain](https://aka.ms/speech/cc-lg-format)alapulnak. Mivel az egyéni parancsok szükség esetén új LG-sablont hoznak létre (azaz a paraméterekben vagy műveletekben való beszédfelismerési válaszokhoz), nem kell megadnia az LG-sablon nevét. Tehát ahelyett, hogy a sablont a következőképpen definiálja:

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

Az LG-sablonok kihasználása lehetővé teszi, hogy az adaptív kifejezéseket használó parancsok összetett beszédes válaszait is meghatározhatja. További részletekért tekintse meg az [LG-sablonok formátumát](https://aka.ms/speech/cc-lg-format) . Az egyéni parancsok alapértelmezés szerint a következő kisebb eltérésekkel rendelkező összes képességet támogatják:

* Az LG-sablonok entitások $ {entityName} néven jelennek meg. Az egyéni parancsokban nem használunk entitásokat, de a paraméterek használhatók változókként a következő ábrázolások egyikével: $ {parameterName} vagy {parameterName}
* Az egyéni parancsok nem támogatják a sablonok összeállítását és bővítését. Ennek az az oka, hogy soha nem szerkeszti a `.lg` fájlt közvetlenül, de csak az automatikusan létrehozott sablonokra adott válaszokat.
* Az LG által befecskendezett egyéni függvények nem támogatottak az egyéni parancsokban. Az előre definiált függvények továbbra is támogatottak.
* A beállítások (Strict, replaceNull & lineBreakStyle) egyéni parancsokban nem támogatottak.

## <a name="add-template-responses-to-turnonoff-command"></a>Sablonra adott válaszok hozzáadása a TurnOnOff parancshoz

Módosítsa a **TurnOnOff** parancsot új paraméter hozzáadásához a következő konfigurációval:

| Beállítás            | Ajánlott érték       | 
| ------------------ | --------------------- | 
| Név               | `SubjectContext`         | 
| Globális          | nincs bejelölve             | 
| Kötelező           | nincs bejelölve               | 
| Típus               | Sztring                |
| Alapértelmezett érték      | `all` |
| Konfiguráció      | Előre megadott bemeneti értékek elfogadása a belső katalógusból | 
| Előre megadott bemeneti értékek | `room`, `bathroom`, `all`|

### <a name="modify-completion-rule"></a>Befejezési szabály módosítása

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
1. Kattintson a **Mentés** gombra.

> [!div class="mx-imgBorder"]
> ![Minta mondatok paraméterekkel](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - A **nyilvános hangok**esetében az **neurális típusok** csak bizonyos régiókban érhetők el. A rendelkezésre állás ellenőrzéséhez tekintse meg a [standard és a neurális hangok régiónként/végpont szerint](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)című témakört.
> - Az **Egyéni hangokat**az egyéni hangprojektek lapról lehet létrehozni. Lásd: Ismerkedés [az egyéni hanggal](./how-to-custom-voice.md).

Mostantól az alkalmazás az alapértelmezett hang helyett a kiválasztott hangon fog válaszolni.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyéni parancsok integrálása a SPEECH SDK használatával](./how-to-custom-commands-setup-speech-sdk.md).