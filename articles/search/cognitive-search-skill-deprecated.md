---
title: Elavult kognitív képességek
titleSuffix: Azure Cognitive Search
description: Ez a lap tartalmazza a kognitív képességek listáját, amelyek elavultnak minősülnek, és nem támogatottak a közeljövőben az Azure Cognitive Search skillsets.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5f3587e4398be28cbaa2372be720258196bb48ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792022"
---
# <a name="deprecated-cognitive-skills-in-azure-cognitive-search"></a>Elavult kognitív képességek az Azure Cognitive Search-ben

Ez a dokumentum ismerteti a kognitív képességek, amelyek elavultnak minősülnek. A tartalomhoz használja az alábbi útmutatót:

* Szakértelem neve: Az elavult szakértelem neve, leképezve az @odata.type attribútumra.
* Utolsó elérhető api-verzió: Az Azure Cognitive Search nyilvános API utolsó verziója, amelyen keresztül a megfelelő elavult szakértelem tartalmazó skillset-ek hozhatók létre/frissíthetők.
* Támogatás vége: Az utolsó nap, amely után a megfelelő szakértelem nem támogatottnak minősül. A korábban létrehozott skillsets továbbra is működnie kell, de a felhasználók számára ajánlott, hogy migrálja el egy elavult szakértelem.
* Javaslatok: Áttelepítési útvonal előre egy támogatott szakértelem használatához. A felhasználóknak azt tanácsoljuk, hogy kövessék az ajánlásokat, hogy továbbra is támogatást kapjanak.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft.Skills.Text.NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>Utolsó elérhető api verzió

2017. 11. 11. dátumú előzetes verzió

### <a name="end-of-support"></a>A támogatás megszűnése

2019. február 15.

### <a name="recommendations"></a>Javaslatok 

Használja inkább [a Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) fájlt. A NamedEntityRecognitionSkill legtöbb funkcióját jobb minőségben biztosítja. Összetettebb kimeneti mezőiben is gazdagabb információkkal rendelkezik.

Az [Entitásfelismerési szakértelemre](cognitive-search-skill-entity-recognition.md)való átgráshoz a következő módosításokközül egyet vagy többet kell végrehajtania a szakértelem-definíción. A szakértelem-definíció frissíthető a [Skillset frissítése API-val.](https://docs.microsoft.com/rest/api/searchservice/update-skillset)

> [!NOTE]
> Jelenleg a megbízhatósági pontszám fogalmaként nem támogatott. A `minimumPrecision` paraméter a `EntityRecognitionSkill` jövőbeli és a visszamenőleges kompatibilitáshoz létezik.

1. *(Kötelező)* Módosítsa `@odata.type` a `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` `"#Microsoft.Skills.Text.EntityRecognitionSkill"`következőt a-ra.

2. *(Nem kötelező)* Ha a `entities` kimenetet használja, használja `namedEntities` a `EntityRecognitionSkill` komplex gyűjtemény kimenetét. A `targetName` szakértelem-definícióban a segítségével leképezheti a `entities`.

3. *(Nem kötelező)* Ha nem adja meg `categories`kifejezetten `EntityRecognitionSkill` a , a különböző típusú kategóriákat `NamedEntityRecognitionSkill`a. Ha ez a viselkedés nem kívánatos, győződjön meg arról, hogy a `categories` paramétert explicit módon a. `["Person", "Location", "Organization"]`

    _Mintaáttelepítési definíciók_

    * Egyszerű áttelepítés

        _(Előtte) NamedEntityRecognition szakértelem definíciója_
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
        _(Utána) EntityRecognition szakértelem definíciója_
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
    
    * Kissé bonyolult migráció

        _(Előtte) NamedEntityRecognition szakértelem definíciója_
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
        _(Utána) EntityRecognition szakértelem definíciója_
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
+ [Hogyan definiálni a skillset](cognitive-search-defining-skillset.md)
+ [Entitás-kivezetési szakértelem](cognitive-search-skill-entity-recognition.md)
