---
title: Mi a Beszélgetéstanuló? – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: További információ a Conversation Learnerről és annak működéséről.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 725a1d3628fb320a58e073fe2d825af23b02c0bd
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707019"
---
# <a name="what-is-conversation-learner"></a>Mi a Beszélgetéstanuló?

Conversation Learner lehetővé teszi olyan társalgási felületek összeállítását és megtanulását, amelyek a példa interakcióit tanulják meg. 

A hagyományos megközelítésekkel ellentétben Conversation Learner a párbeszéd végpontok közötti kontextusát veszi figyelembe, hogy javítsa a válaszokat, és vonzóbb felhasználói élményt nyújtson. A feladathoz igazodó használati esetek széles körének bővítése Conversation Learner a jelenetek mögötti gépi tanulást alkalmazza, így a robotok és az intelligens ügynökök kevésbé valószínű, hogy meghiúsítják a felhasználókat, további ügyfélszolgálati költségekkel járnak, és intuitívabb interakciókat ösztönöznek.

A fejlesztők a prototípusos párbeszédpanelek megadásával kezdhetik meg az utánzást. A modell több párbeszédablak beírásával tanul. Ha a modell jól működik, a robot üzembe helyezhető a végfelhasználók számára. Conversation Learner a felhasználókkal folytatott beszélgetéseket, a fejlesztő pedig áttekintheti őket. Ha a hibákat kiszúrták, a fejlesztő helyszíni javítást végezhet, és azonnal elérhetővé teheti a modellt.

Ez a megközelítés csökkenti a párbeszéd-vezérlési logika manuális kódolását, és lehetővé teszi, hogy az üzleti tulajdonosok vagy a tartományi szakértők a korábbi gépi tanulási ismeretek nélkül hozzájáruljanak a társalgási felülethez. Függetlenül attól, hogy a robot, az intelligens eszköz vagy az intelligens ügynök részeként van-e üzembe helyezve, Conversation Learner gyorsan megismételheti az új képességeket, viselkedéseket vagy kompetenciákat, és gyorsan javíthatja a minőséget. 

Conversation Learner lehetővé teszi a fejlesztők számára a gyorsabb és sikeres párbeszédek megnövekedését több társalgási csatornán keresztül a Microsoft bot Framework segítségével, vagy önálló infrastruktúrával.

Összefoglalás és csúcsfények:

- A Conversation Learner a tevékenység-orientált robotok létrehozásának AI-első módszere.

- Egy végpontok közötti, egyidejű neurális hálózatra (LSTM) támaszkodik, és közvetlenül tanul a többes beszélgetésből származó példákkal. 

- Lehetővé teszi a tervezők, a fejlesztők, az üzleti felhasználók és a Call Center-feldolgozók számára a robotok készítését és karbantartását. 

- Lehetővé teszi az üzleti szabályok és a józan ész kifejezését a kódban.

- A tanítási munkamenetek során a neurális hálózati modell a beszélgetésben a várt műveletek következő készletének kiértékelésére szolgál. A robot fejlesztői ezután kiválaszthatják a megfelelő műveletet, és a megfelelő válasz biztosításához betanítják a hálózatot.
 
- A betanítás befejezése után a fejlesztő a felhasználói interakciók napló párbeszédpaneleit használva kijavítani a bot válaszait, és újratanítja a modellt. 

- A a tartományi és külső API-kat is meghívhatja a feladatok elvégzéséhez.

