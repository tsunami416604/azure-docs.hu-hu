---
title: Kognitív képességeket – Azure Search elavult
description: Ezen a lapon a kognitív keresés képességek, amelyek tekinthetők elavult listáját tartalmazza, és nem támogatja a közeljövőben.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: c35e4253858d6820d86d7d3e0763a3dcc577d09d
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54387945"
---
# <a name="deprecated-cognitive-search-skills"></a>Elavult Cognitive Search-képességek

Ez a dokumentum ismerteti a kognitív képességeket, amelyek tekinthetők elavult. Használja az útmutató tartalma:

* Ismeretek neve: A szakértelem el fognak évülni nevére, vannak leképezve a @odata.type attribútum.
* Utolsó elérhető api-verzió: Az Azure legfrissebb verziója keressen rá a nyilvános API-t, mely szakértelmével keresztül a megfelelő elavult szakértelem tartalmazó lehet létrehozása vagy frissítése.
* Támogatás vége: Az utolsó nap elteltével a megfelelő szakértelmet számít nem támogatott. Korábban létrehozott ismereteket kell továbbra is működik, de felhasználók ajánlott elavult szakértelem migrálni.
* Javaslatok: Migrálási útvonalat előre támogatott műveleteket. Felhasználók szeretne támogatást kapni a folytatáshoz kövesse az alábbi intézkedéseket javasoljuk.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft.Skills.Text.NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>Utolsó elérhető api-verzió

2017. 11. 11 – előzetes verzió

### <a name="end-of-support"></a>Támogatás vége

2019. február 15.

### <a name="recommendations"></a>Javaslatok 

Használat [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) helyette. A legtöbb funkciója a NamedEntityRecognitionSkill, jobb minőségben biztosít. Azt is az összetett táblakimeneti mezők részletesebb információkat tartalmaz.

Áttelepítése a [entitások felismerése szakértelem](cognitive-search-skill-entity-recognition.md), akkor végre kell hajtania egy vagy több, a következő módosításokat a képzettségi definíciós. A képzettségi definíció használatával frissítheti a [indexmezők API frissítése](https://docs.microsoft.com/rest/api/searchservice/update-skillset).

_Megjegyzés_: Magabiztossági pontszámot a fogalom, jelenleg nem támogatott. A közeljövőben megszűnik. A `minimumPrecision` paraméter megtalálható a `EntityRecognitionSkill` későbbi használatra, és a visszamenőleges kompatibilitás.

1. *(Kötelező)*  Módosítása a `@odata.type` a `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` való `"#Microsoft.Skills.Text.EntityRecognitionSkill"`.

2. *(Nem kötelező)*  Ha használja a `entities` kimeneti, használja a `namedEntities` összetett gyűjtemény kimenete a `EntityRecognitionSkill` helyette. Használhatja a `targetName` szakértelem definíció való leképezéséhez jegyzet neve `entities`.

3. *(Nem kötelező)*  Ha Ön kifejezetten nem ad meg a `categories`, a `EntityRecognitionSkill` kategóriák mellett, amelyek a rendszer által támogatott különböző típusú visszatérhessen a `NamedEntityRecognitionSkill`. Ha ez a viselkedés nem kívánatos, ügyeljen arra, hogy explicit módon beállíthat egy a `categories` paramétert `["Person", "Location", "Organization"]`.

    _Mintául szolgáló Migrálási definíciók_

    * Egyszerű áttelepítés

        _(Előtte) NamedEntityRecognition ismeretek meghatározása_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
        _(Után) EntityRecognition ismeretek meghatározása_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
    
    * Némileg összetettebb áttelepítése

        _(Előtte) NamedEntityRecognition ismeretek meghatározása_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "entities"
            }
            ]
        }
        ```
        _(Után) EntityRecognition ismeretek meghatározása_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "namedEntities",
                "targetName": "entities"
            }
            ]
        }
        ```

## <a name="see-also"></a>Lásd még

+ [Előre megadott képesség](cognitive-search-predefined-skills.md)
+ [Hogyan képességcsoport megadása](cognitive-search-defining-skillset.md)
+ [Entitások felismerése szakértelem](cognitive-search-skill-entity-recognition.md)
