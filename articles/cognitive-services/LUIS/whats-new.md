---
title: Újdonságok – Nyelvi ismeretek (LUIS)
description: Ezt a cikket rendszeresen frissítik az Azure Cognitive Services language understanding API-val kapcsolatos hírekkel.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 6334b157f68f09f2b165c6073ba63f45a0caf3c4
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538018"
---
# <a name="whats-new-in-language-understanding"></a>A nyelvi ismeretek újdonságai

Ismerje meg a szolgáltatás újdonságait. Ezek közé tartoznak a kibocsátási megjegyzések, videók, blogbejegyzések és egyéb típusú információk. Jelölje be ezt az oldalt, hogy naprakész legyen a szolgáltatással.

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="march-2020"></a>2020. március

* A TLS 1.2 mostantól a szolgáltatáshoz érkező összes HTTP-kérelemre érvényes. További információ: [Azure Cognitive Services security.](../cognitive-services-security.md)

### <a name="november-4-2019---ignite"></a>2019. november 4.: Ignite

* Videó – [Advanced Natural Language Understanding (NLU) modellek a LUIS és az Azure Cognitive Services használatával | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

* Továbbfejlesztett fejlesztői hatékonyság
    * A [V3 előrejelzési végpontáltalános](luis-migration-api-v3.md)elérhetősége .
    * Alkalmazások importálása és exportálása .lu ([LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)) formátumban. Ez kikövezi az utat egy hatékony CI/ CD folyamat előtt.
* Nyelvi bővítés
    * [Arab és hindi](luis-language-support.md) nyilvános előzetes verzióban.
* Előre összeállított modellek
    * [Az előre összeállított tartományok](luis-reference-prebuilt-domains.md) már általánosan elérhetők (GA)
    * Japán előre összeállított entitások - kor, pénznem, szám és százalékos nem támogatott a V3.Japanese [prebuilt entities](luis-reference-prebuilt-entities.md#japanese-entity-support) - age, currency, number, and percentage are not supported in V3.
    * Olasz [előre összeállított entitások](luis-reference-prebuilt-entities.md#italian-entity-support) - kor, pénznem, dimenzió, szám és százalékos felbontás megváltozott V2.
* Továbbfejlesztett felhasználói élmény [preview.luis.ai portálon](https://preview.luis.ai) – megújult címkézési élmény az összetett modellek létrehozásához és hibakereséséhez. Próbálja ki az előnézeti portál oktatóanyagait:
    * [Csak leképezések](tutorial-intents-only.md)
    * [Lebomló, gépmegtanuló entitás](tutorial-machine-learned-entity.md)
* A nyelvtudás fejlődése – [kifinomult nyelvi modellek létrehozása](luis-concept-entity-types.md) kevesebb erőfeszítéssel.
* A gépi tanulási funkciókat modellszinten definiálhatja, és lehetővé teszi, hogy a modellek más modellek számára signalsként használhatók, például entitásokat használjon leképezések és más entitások számára.
* Új, kibővített [korlátok](luis-boundaries.md) - magasabb maximális kifejezéslisták és összes mondatok, új modell, mint jellemző korlátok
* Információk kinyerése a szövegből a mély hierarchia szerkezetének formátumában, hatékonyabbá téve a beszélgetési alkalmazásokat.

    ![gépáltal megtanult entitás kép](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>2019. szeptember 3.September 3, 2019

* Azure szerzői erőforrás – [most migráljon.](luis-migration-authoring.md)
    * 500 alkalmazás Azure-erőforrásonként
    * 100 verzió alkalmazásonként
* Előre összeállított szervezetek török támogatása
* Olasz támogatás datetimeV2

### <a name="july-23-2019"></a>2019. július 23.July 23, 2019

* A [felismerők szövegének](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) frissítése az 1.2.3-as szintre
    * Kor, hőmérséklet, dimenzió és pénznem felismerők olaszul.
    * Az ünnepi felismerés javítása angol nyelven a Hálaadás-alapú dátumok helyes kiszámításához.
    * A francia DateTime fejlesztései a nem dátumnélküli és nem idő típusú entitások hamis pozitívjainak csökkentése érdekében.
    * Támogatás a naptár / iskola / pénzügyi év és rövidítések angol DateRange.
    * Továbbfejlesztett PhoneNumber felismerés kínai és japán nyelven.
    * A NumberRange jobb támogatása angol nyelven.
    * Teljesítménybeli fejlesztések.

### <a name="june-24-2019"></a>2019. június 24.June 24, 2019

* [OrdinalV2 előre elkészített entitás,](luis-reference-prebuilt-ordinal-v2.md) amely támogatja a rendelést, például a következő, az előző és az utolsó. Csak angol kultúrát.

### <a name="may-6-2019---build-conference"></a>2019. május 6., //Build Konferencia

A Build 2019 konferencián a következő funkciók jelentek meg:

* [A V3 API áttelepítési útmutatójának előzetes verziója](luis-migration-api-v3.md)
* [Továbbfejlesztett elemzési irányítópult](luis-how-to-use-dashboard.md)
* [Továbbfejlesztett előre összeállított tartományok](luis-reference-prebuilt-domains.md)
* [Dinamikus listaentitások](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Külső entitások](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>Blogok

[Robot keretrendszer](https://blog.botframework.com/)

## <a name="videos"></a>Videók

### <a name="2019-ignite-videos"></a>2019 Ignite videók

[Advanced Natural Language Understanding (NLU) modellek a LUIS és az Azure Cognitive Services használatával | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>2019 Videók készítése

[Az Azure Conversational AI használata a vállalkozás méretezéséhez a következő generációszámára](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>Szolgáltatási hírek

[Azure-frissítési közlemények a Cognitive Services hez](https://azure.microsoft.com/updates/?product=cognitive-services)
