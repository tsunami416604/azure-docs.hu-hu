---
title: Elavult kognitív képességek – Azure Search
description: Ez az oldal az elavultnak tekintett kognitív keresési képességek listáját tartalmazza, és a közeljövőben nem lesz támogatott.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: 316cfb228050030b15e791e1d890c9048eadc109
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68841092"
---
# <a name="deprecated-cognitive-search-skills"></a>Elavult kognitív keresési képességek

Ez a dokumentum az elavultnak tekintett kognitív képességeket ismerteti. Használja a következő útmutatót a tartalomhoz:

* Szakértelem neve: Az elavult képesség neve, amely le lesz képezve @odata.type az attribútumhoz.
* Utolsó elérhető API-verzió: Az Azure Search nyilvános API utolsó verziója, amely a megfelelő elavult képességet tartalmazó szakértelmével hozható létre/frissíthető.
* Támogatás vége: Az utolsó nap, amely után a megfelelő képesség nem támogatottnak minősül. A korábban létrehozott szakértelmével továbbra is működőképesnek kell lennie, de a felhasználókat ajánlott áttelepíteni egy elavult képességből.
* Javaslatok: A Migrálás útvonala egy támogatott képesség használatára van továbbítva. A felhasználóknak javasoljuk, hogy kövessék az ajánlásokat, hogy továbbra is megkapják a támogatást.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft.Skills.Text.NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>Utolsó elérhető API-verzió

2017. 11. 11. dátumú előzetes verzió

### <a name="end-of-support"></a>Támogatás vége

Február 15., 2019

### <a name="recommendations"></a>Javaslatok 

Használja helyette a [Microsoft. Skills. Text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) . A NamedEntityRecognitionSkill legtöbb funkciója magasabb színvonalú. Emellett a komplex kimeneti mezőiben is gazdagabb információk szerepelnek.

Az [entitás](cognitive-search-skill-entity-recognition.md)-felismerési szakértelemre való áttelepítéshez az alábbi módosítások közül egyet vagy többet kell végrehajtania a szaktudás definíciójában. A skill definition [Update KÉSZSÉGKÉSZLET API](https://docs.microsoft.com/rest/api/searchservice/update-skillset)használatával frissítheti.

> [!NOTE]
> A megbízhatósági pontszám jelenleg nem támogatott. A `minimumPrecision` paraméter a későbbi használatra `EntityRecognitionSkill` és a visszamenőleges kompatibilitás érdekében létezik.

1. *(Kötelező)* Módosítsa a `@odata.type` elemeta`"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` következőre:. `"#Microsoft.Skills.Text.EntityRecognitionSkill"`

2. *(Nem kötelező)* Ha a `entities` kimenetet használja, használja `EntityRecognitionSkill` inkább az `namedEntities` összetett gyűjtemény kimenetét. A skill definition `targetName` (a) használatával leképezheti a nevű `entities`jegyzetet.

3. *(Nem kötelező)* Ha nem adja meg explicit módon a `categories`-t, a `EntityRecognitionSkill` képes különböző típusú kategóriákat visszaadni a által `NamedEntityRecognitionSkill`támogatottak mellett. Ha ez a viselkedés nem kívánatos, ügyeljen arra, hogy explicit módon állítsa `categories` be a `["Person", "Location", "Organization"]`paramétert a következőre:.

    _Példa áttelepítési definíciók_

    * Egyszerű áttelepítés

        _Előtt NamedEntityRecognition-definíció_
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
        _Után EntityRecognition-definíció_
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
    
    * Kis mértékben bonyolult áttelepítés

        _Előtt NamedEntityRecognition-definíció_
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
        _Után EntityRecognition-definíció_
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

+ [Előre definiált képességek](cognitive-search-predefined-skills.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
+ [Entitás-felismerési szakértelem](cognitive-search-skill-entity-recognition.md)
