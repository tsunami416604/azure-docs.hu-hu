---
title: Újdonságok – Language Understanding (LUIS)
description: Ez a cikk rendszeresen frissül az Azure Cognitive Services Language Understanding APIával kapcsolatos hírekkel.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 8e3b6f729f69866cdb797cdfd1887de4f5e2be05
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82097727"
---
# <a name="whats-new-in-language-understanding"></a>A Language Understanding újdonságai

Ismerje meg a szolgáltatás újdonságait. Ezek közé tartoznak a kibocsátási megjegyzések, videók, blogbejegyzések és más típusú információk. A lapon lévő könyvjelzővel naprakészen tarthatja a szolgáltatást.

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="march-2020"></a>2020. március

* A TLS 1,2 mostantól a szolgáltatáshoz tartozó összes HTTP-kérelem esetében érvénybe lép. További információ: [Azure Cognitive Services Security](../cognitive-services-security.md).

### <a name="november-4-2019---ignite"></a>November 4., 2019-Ignite

* Video- [Advanced Natural Language Understanding (NLU) modellek a Luis és az Azure Cognitive Services használatával | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

* Továbbfejlesztett fejlesztői hatékonyság
    * Az [előrejelzési végpont v3](luis-migration-api-v3.md)verziójának általános elérhetősége.
    * Alkalmazások importálása és exportálása a. lu ([LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)) formátummal. Ez kikövezi a hatékony CI/CD-folyamat módját.
* Nyelvi bővítés
    * Az [arab és a hindi](luis-language-support.md) nyilvános előzetes verzióban érhető el.
* Előre összeállított modellek
    * Az előre [elkészített tartományok](luis-reference-prebuilt-domains.md) már általánosan elérhetők (GA)
    * Japán előre [elkészített entitások](luis-reference-prebuilt-entities.md#japanese-entity-support) – az életkor, a pénznem, a szám és a százalék nem támogatott a v3-as verzióban.
    * Olasz előre [összeépített entitások](luis-reference-prebuilt-entities.md#italian-entity-support) – az életkor, a pénznem, a dimenzió, a szám és a százalékos felbontás a v2-től módosult.
* Továbbfejlesztett felhasználói élmény a [preview.Luis.ai-portálon](https://preview.luis.ai) – az összetett modellek kiépítése és hibakeresése érdekében feldolgozatlan címkézési élmény. Próbálja ki a betekintő portál oktatóanyagokat:
    * [Csak szándékok](tutorial-intents-only.md)
    * [Felismert gépi megtanult entitás](tutorial-machine-learned-entity.md)
* Előzetes nyelvi ismeretek – a [kifinomult nyelvi modellek fejlesztése](luis-concept-entity-types.md) kevesebb erőfeszítéssel.
* Megadhatja a gépi tanulási funkciókat a modell szintjén, és engedélyezheti a modellek más modellekhez való használatát, például az entitások szolgáltatásként való használatát a szándékok és más entitások számára.
* Új, kibővített [korlátok](luis-limits.md) – a kifejezések listájának és az összes kifejezésnek, valamint az új modellnek a funkció korlátainál nagyobb maximális száma
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
* [Dinamikus lista entitásai](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Külső entitások](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>Blogok

[Robot keretrendszer](https://blog.botframework.com/)

## <a name="videos"></a>Videók

### <a name="2019-ignite-videos"></a>2019 Ignite-videók

[Advanced Natural Language Understanding (NLU) modellek a LUIS és az Azure Cognitive Services használatával | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>2019 videók készítése

[Az Azure társalgási AI használata az üzlet méretezésére a következő generáció számára](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>Szolgáltatási hírek

[Cognitive Services Azure Update-hirdetmények](https://azure.microsoft.com/updates/?product=cognitive-services)
