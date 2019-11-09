---
title: Újdonságok – Language Understanding (LUIS)
titleSuffix: Azure Cognitive Services
description: Ez a cikk a Language Understandingával kapcsolatos híreket tartalmaz.
author: diberry
manager: nitinme
ms.custom: experiment-luis-0519
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: diberry
ms.openlocfilehash: 2e32d0f4906dea69d6eab27faca4a2e41106ed73
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73836841"
---
# <a name="whats-new-in-language-understanding"></a>A Language Understanding újdonságai

Ismerje meg a szolgáltatás újdonságait. Ezek közé tartoznak a kibocsátási megjegyzések, videók, blogbejegyzések és más típusú információk. A lapon lévő könyvjelzővel naprakészen tarthatja a szolgáltatást.  

## <a name="release-notes"></a>Kibocsátási megjegyzések 

### <a name="november-4-2019---ignite"></a>November 4., 2019-Ignite

* Továbbfejlesztett fejlesztői hatékonyság
    * Az [előrejelzési végpont v3](luis-migration-api-v3.md)verziójának általános elérhetősége. 
    * Alkalmazások importálása és exportálása a. lu ([LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)) formátummal. Ez kikövezi a hatékony CI/CD-folyamat módját. 
* Nyelvi bővítés
    * Az [arab és a hindi](luis-language-support.md) nyilvános előzetes verzióban érhető el.
* Előre összeállított modellek
    * Az előre [elkészített tartományok](luis-reference-prebuilt-domains.md) már általánosan elérhetők (GA)
    * Japán előre [elkészített entitások](luis-reference-prebuilt-entities.md#japanese-entity-support) – az Age, a Currency, a number, a százalék nem támogatott a v3-as verzióban.
    * Olasz előre [összeépített entitások](luis-reference-prebuilt-entities.md#italian-entity-support) – kor, pénznem, dimenzió, szám, a százalékos felbontás változása: v2.
* Fokozza a felhasználói élményt a [preview.Luis.ai-portálon](https://preview.luis.ai) – az összetett modellek létrehozásának és hibakeresésének lehetővé tételéhez szükséges, kitatarozt címkézési élmény. Próbálja ki a betekintő portál oktatóanyagokat:
    * [Csak szándékok](tutorial-intents-only.md)
    * [Felismert gépi megtanult entitás](tutorial-machine-learned-entity.md) 
* Előzetes nyelvi ismeretek – a [kifinomult nyelvi modellek fejlesztése](luis-concept-entity-types.md) kevesebb erőfeszítéssel. 
* A gépi tanulási funkciók definiálása a modell szintjén, valamint a modellek más modellként való használatának engedélyezése, például az entitások használata a szándékok és más entitások számára.
* Új, kibővített [korlátok](luis-boundaries.md) – nagyobb maximális érték a kifejezések és az összes kifejezés, valamint az új modell funkció korlátai
* Kinyerheti az adatokat a szövegből a mély hierarchia struktúrájának formátumában, így hatékonyabban fejlesztheti a beszélgetési alkalmazásokat.

    ![gépi megtanult entitás képe](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>Szeptember 3., 2019

* Azure authoring Resource – [Migrálás most](luis-migration-authoring.md).
    * 500 alkalmazás/Azure-erőforrás
    * 100 verzió/alkalmazás
* Török támogatás előre összeépített entitásokhoz
* Olasz datetimeV2-támogatás

### <a name="july-23-2019"></a>2019. július 23.

* A [felismerők frissítése – szöveg](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) – 1.2.3
    * Kor, hőmérséklet, dimenzió és pénznem-felismerők olasz nyelven.
    * A Hálaadás-alapú dátumok helyes kiszámításához az angol nyelvű nyaralás-felismerés javítása.
    * A nem naprakész és a nem időpontú entitások téves pozitív értékének csökkentése érdekében a francia DateTime fejlesztése.
    * A Calendar/School/pénzügyi év és a betűszók támogatása az angol nyelvű DateRange.
    * Továbbfejlesztett telefonszám-felismerés kínai és japán nyelven.
    * Továbbfejlesztett NumberRange-támogatás az angol nyelven.
    * Teljesítménybeli változások.

### <a name="june-24-2019"></a>Június 24., 2019

* A [OrdinalV2 előre elkészített entitást](luis-reference-prebuilt-ordinal-v2.md) támogat, például a következő, az előző és az utolsó sorrendet. Csak angol kultúra.

### <a name="may-6-2019---build-conference"></a>Május 6., 2019 – Build konferencia

A Build 2019 konferencián a következő funkciók jelentek meg:

* [A V3 API áttelepítési útmutatójának előzetes verziója](luis-migration-api-v3.md)
* [Továbbfejlesztett elemzési irányítópult](luis-how-to-use-dashboard.md)
* [Továbbfejlesztett előre összeépített tartományok](luis-reference-prebuilt-domains.md) 
* [Dinamikus lista entitásai](luis-migration-api-v3.md#dynamic-lists-passed-in-at-prediction-time)
* [Külső entitások](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>Blogok

[Bot-keretrendszer](https://blog.botframework.com/)

## <a name="videos"></a>Videók

### <a name="2019-build-videos"></a>2019 videók készítése

[Az Azure társalgási AI használata az üzlet méretezésére a következő generáció számára](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>Szolgáltatási hírek

[Cognitive Services Azure Update-hirdetmények](https://azure.microsoft.com/updates/?product=cognitive-services)
