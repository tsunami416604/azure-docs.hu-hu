---
title: Használja a Cortana előre elkészített alkalmazást LUIS |} Microsoft Docs
description: A Cortana személyes Segéd, egy előre elkészített alkalmazást a nyelvi ismertetése intelligens szolgáltatások (LUIS) használata.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: c7249cb8d8cff29f419412025c69e3b2b76b49d1
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110657"
---
# <a name="cortana-prebuilt-app"></a>A Cortana előre elkészített alkalmazás

> [!IMPORTANT]
> Azt javasoljuk, hogy használja a [előre elkészített tartományok](./luis-how-to-use-prebuilt-domains.md), a Cortana előre elkészített alkalmazás helyett. Például ahelyett, hogy **builtin.intent.calendar.create_calendar_entry**, használjon **Calendar.Add** a a **naptár** előre elkészített tartomány.
> Az előre elkészített tartományok ezeket az előnyöket biztosítja: 
> * Az előre elkészített és pretrained leképezések és entitások, melyet úgy terveztek, egymáshoz való biztosítanak. Egy előre elkészített tartomány integrálható közvetlenül az alkalmazásba. Például ha egy alkalmasságra követő most létre, adhat hozzá a **alkalmasságra** tartományi és leképezések és entitások nyomon követése alkalmasságra tevékenységeket, beleértve a súlyozás és étele tervezési, hátralévő idő nyomon követése leképezések teljes készlete vagy távolság és mentése alkalmasságra tevékenység megjegyzések.
> * Az előre elkészített tartomány leképezések testre szabható. Például ha lehetővé szeretné tenni a szállodák értékelést, akkor is betanítása és testre szabhatja a **Places.GetReviews** a leképezési a **helyek** tartomány felismerje a szállodák felülvizsgálati kérelmek.
> * Az előre elkészített tartományai bővíthető. Például, ha szeretné használni a **helyek** előre elkészített tartományt, amely megkeresi az éttermekben és megjelölésű szükségességét cuisine típusú első bot, létrehozhatja és betanítása egy **Places.GetCuisine** leképezés.

Amellett, hogy lehetővé teszi a saját alkalmazások összeállítását, LUIS is biztosít a leképezések és entitásokat a Microsoft Cortana személyes Segéd előre elkészített alkalmazásként. A nyilvánosan elérhető LUIS alkalmazás viselkedése nem módosítható. A leképezések az alkalmazás entitások szerkeszteni és nem más LUIS alkalmazások integrálva. Ha szeretné, hogy az ügyfélalkalmazás az előre elkészített alkalmazás és a saját LUIS alkalmazás eléréséhez, majd az ügyfélalkalmazást azt mindkét LUIS alkalmazások hivatkozik.

Az előre elkészített személyes Segéd alkalmazás érhető el a kulturális környezetek (nyelv): angol, francia, olasz, spanyol, és a kínai.

## <a name="get-the-endpoint-for-the-cortana-prebuilt-app"></a>A végpont lekérése a Cortana előre elkészített alkalmazás

Az előre elkészített Cortana-alkalmazásokhoz, a következő végpontok használatával érhető el: 

| Nyelv | Végpont|
|--------| ------------------|
| Angol| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c413b2ef-382c-45bd-8ff0-f76d60e2a821|
|    kínai| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c27c4af7-d44a-436f-a081-841bb834fa29|
|    francia| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/0355ead1-2d08-4955-ab95-e263766e8392|
|    spanyol| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/cb2675e5-fbea-4f8b-8951-f071e9fc7b38|
|    olasz| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/30a0fddc-36f4-4488-b022-03de084c1633|


> [!NOTE]
> A végpont URL-címeket is rendelkezésre állnak a [alkalmazások – első személyes Segéd alkalmazások](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c32) API.

## <a name="try-out-the-personal-assistant-app"></a>Próbálja ki a személyes Segéd alkalmazás
A végpont meghívásához fűzheti a végpont kulcs argumentum és a lekérdezési karakterlánc a végpontnak. 

Például ha a utterance értelmezése lesz "hozzon létre egy időpontot egyeztessen az team értekezlet", majd, hogy utterance hozzáfűzése a végponti URL-cím. 

```
https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c413b2ef-382c-45bd-8ff0-f76d60e2a821?subscription-key={YOUR-SUBSCRIPTION-KEY}&q=create an appointment for team meeting
```

Illessze be az URL-címet egy webböngészőben, és a végpont kulcsát helyettesítse a `{YOUR-SUBSCRIPTION-KEY}` mező.

A böngészőben megtekintheti, hogy a Cortana előre elkészített alkalmazás azonosítja `builtin.intent.calendar.create_calendar_entry` , a célt és `builtin.calendar.title` entitástípus, és a utterance `create an appointment for team meeting`.

![A Cortana előre elkészített alkalmazás használata](./media/luis-how-to-prebuilt-cortana/luis-prebuilt-cortana-browser.png)

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [A Cortana előre elkészített app hivatkozik](./luis-reference-cortana-prebuilt.md)

