---
title: Elavult kognitív képességek
titleSuffix: Azure Cognitive Search
description: Ez az oldal az elavultnak tekintett kognitív képességek listáját tartalmazza, és az Azure Cognitive Search szakértelmével közeljövőben nem lesz támogatva.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5f3587e4398be28cbaa2372be720258196bb48ff
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "72792022"
---
# <a name="deprecated-cognitive-skills-in-azure-cognitive-search"></a>Elavult kognitív képességek az Azure-ban Cognitive Search

Ez a dokumentum az elavultnak tekintett kognitív képességeket ismerteti. Használja a következő útmutatót a tartalomhoz:

* Szaktudás neve: az elavult képesség neve, amely az @odata.type attribútumhoz lesz leképezve.
* Utolsó elérhető API-verzió: az Azure Cognitive Search nyilvános API utolsó verziója, amely a megfelelő elavult képességet tartalmazó szakértelmével hozható létre/frissíthető.
* Támogatás befejezése: az utolsó nap, amely után a megfelelő képesség nem támogatottnak minősül. A korábban létrehozott szakértelmével továbbra is működőképesnek kell lennie, de a felhasználókat ajánlott áttelepíteni egy elavult képességből.
* Javaslatok: az áttelepítési útvonal egy támogatott képesség használatára való továbbításra szolgál. A felhasználóknak javasoljuk, hogy kövessék az ajánlásokat, hogy továbbra is megkapják a támogatást.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft. Skills. Text. NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>Utolsó elérhető API-verzió

2017. 11. 11. dátumú előzetes verzió

### <a name="end-of-support"></a>Támogatás vége

2019. február 15.

### <a name="recommendations"></a>Javaslatok 

Használja helyette a [Microsoft. Skills. Text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) . A NamedEntityRecognitionSkill legtöbb funkciója magasabb színvonalú. Emellett a komplex kimeneti mezőiben is gazdagabb információk szerepelnek.

Az [entitás-felismerési szakértelemre](cognitive-search-skill-entity-recognition.md)való áttelepítéshez az alábbi módosítások közül egyet vagy többet kell végrehajtania a szaktudás definíciójában. A skill definition [Update KÉSZSÉGKÉSZLET API](https://docs.microsoft.com/rest/api/searchservice/update-skillset)használatával frissítheti.

> [!NOTE]
> A megbízhatósági pontszám jelenleg nem támogatott. A `minimumPrecision` paraméter a későbbi használatra `EntityRecognitionSkill` és a visszamenőleges kompatibilitás érdekében létezik.

1. *(Kötelező)* Módosítsa a `@odata.type` elemet `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` a `"#Microsoft.Skills.Text.EntityRecognitionSkill"`következőre:.

2. *(Nem kötelező)* Ha a `entities` kimenetet használja, használja `namedEntities` `EntityRecognitionSkill` inkább az összetett gyűjtemény kimenetét. A skill definition `targetName` (a) használatával leképezheti a nevű `entities`jegyzetet.

3. *(Nem kötelező)* Ha nem adja meg explicit módon a `categories`-t, `EntityRecognitionSkill` a képes különböző típusú kategóriákat visszaadni a által támogatottak mellett `NamedEntityRecognitionSkill`. Ha ez a viselkedés nem kívánatos, ügyeljen arra, hogy explicit módon állítsa `categories` be a `["Person", "Location", "Organization"]`paramétert a következőre:.

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

+ [Beépített képességek](cognitive-search-predefined-skills.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
+ [Entitás-felismerési szakértelem](cognitive-search-skill-entity-recognition.md)
