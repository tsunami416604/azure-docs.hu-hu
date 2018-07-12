---
title: Használatát Cortana előre létrehozott alkalmazást, LUIS |} A Microsoft Docs
description: Használja az Cortana személyi asszisztenssel, egy előre létrehozott alkalmazást, a Language Understanding Intelligent Services (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/03/2018
ms.author: v-geberr
ms.openlocfilehash: f5cc97e49d3c127ffe7f53a315c42ab872782a49
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968724"
---
# <a name="cortana-prebuilt-app"></a>Előre összeállított Cortana-alkalmazás

> [!IMPORTANT]
> Azt javasoljuk, hogy használja a [előre összeállított tartományok](./luis-how-to-use-prebuilt-domains.md), előre összeállított Cortana-alkalmazás helyett. Például, nem pedig **builtin.intent.calendar.create_calendar_entry**, használja **Calendar.Add** a a **naptár** előre összeállított tartományhoz.
> Az előre összeállított tartományok ezeket az előnyöket biztosítják: 
> * Ezek biztosítanak az előre elkészített és imagenet szándékok és entitások, amelyek egymással jól működik. Egy előre elkészített tartomány integrálható közvetlenül az alkalmazásba. Például ha egy mentességre tracker fejleszt, hozzáadhatja a **mentességre** tartományban szándékok és entitások mentességre tevékenységeket, beleértve a leképezések súly és étele tervezés, hátralévő idő nyomon követési teljes készletét, és vagy távolság, és mentése mentességre tevékenység megjegyzések.
> * Az előre összeállított tartományban szándékok testre szabhatók. Például, ha szeretne biztosítani a szállodák felülvizsgálatai, is betanítása és testre szabhatja a **Places.GetReviews** a szándék a **helyek** tartomány felismerje Szálloda felülvizsgálati kérelmek.
> * Az előre összeállított tartományok olyan bővíthető. Például, ha használni szeretné a **helyek** előre összeállított tartományban az éttermek és megjelölésű szükségességét cuisine típusú első keresési robotprogramok, létrehozhat és betanítunk egy **Places.GetCuisine** szándékot.

Lehetővé teszi, hogy saját alkalmazást fejlesszen, LUIS is nyújt szándékok és entitások a Microsoft Cortana személyes Segéd előre összeállított alkalmazásként. A nyilvánosan elérhető a LUIS alkalmazás működését nem módosítható. A szándékok és entitások az alkalmazás nem szerkeszthető és más LUIS alkalmazás integrálva. Ha szeretné, hogy az ügyfélalkalmazás az előre összeállított alkalmazás és a LUIS alkalmazás eléréséhez, majd az ügyfélalkalmazásnak mindkét LUIS-alkalmazások hivatkozhat.

Az előre összeállított személyi asszisztenssel alkalmazás ezek kulturális környezetek (nyelv) érhető el: angol, francia, olasz, spanyol és kínai.

## <a name="get-the-endpoint-for-the-cortana-prebuilt-app"></a>A végpont beszerzése az előre összeállított Cortana-alkalmazás

Az előre összeállított Cortana-alkalmazás, az alábbi végpontok használatával érhető el: 

| Nyelv | Végpont|
|--------| ------------------|
| Angol| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c413b2ef-382c-45bd-8ff0-f76d60e2a821|
|    kínai| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c27c4af7-d44a-436f-a081-841bb834fa29|
|    francia| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/0355ead1-2d08-4955-ab95-e263766e8392|
|    spanyol| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/cb2675e5-fbea-4f8b-8951-f071e9fc7b38|
|    olasz| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/30a0fddc-36f4-4488-b022-03de084c1633|


A végpont URL-címeket is rendelkezésre állnak a [apps - alkalmazások beszerzése személyi](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c32) API-t.

## <a name="try-out-the-personal-assistant-app"></a>Próbálja ki a személyi alkalmazás
Például ha az utterance (kifejezés) szeretné értelmezése nem "értekezlet használható találkozó létrehozása", a utterance (kifejezés) a végpont URL-címhez fűzze hozzá a. 

```
https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c413b2ef-382c-45bd-8ff0-f76d60e2a821?subscription-key=YOUR-SUBSCRIPTION-KEY&q=create%20an%20appointment%20for%20team%20meeting
```

Illessze be az URL-CÍMÉT egy webböngészőben, és helyettesítse be a végpont kulcs a `YOUR-SUBSCRIPTION-KEY` mező.

A böngésző azt láthatja, hogy az előre összeállított Cortana-alkalmazás azonosítja `builtin.intent.calendar.create_calendar_entry` , a célt és `builtin.calendar.title` a entitástípusként, valamint az utterance (kifejezés) a `create an appointment for team meeting`.

```JSON
{
  "query": "create an appointment for team meeting",
  "topScoringIntent": {
    "intent": "builtin.intent.calendar.create_calendar_entry"
  },
  "entities": [
    {
      "entity": "team meeting",
      "type": "builtin.calendar.title"
    }
  ]
}
```

Előre összeállított Cortana-alkalmazás használja, mert a LUIS nem ad vissza a pontszámokat. 

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Előre összeállított Cortana-alkalmazás-hivatkozás](./luis-reference-cortana-prebuilt.md)

