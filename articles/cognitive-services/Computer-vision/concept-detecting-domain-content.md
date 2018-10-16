---
title: Tartomány-specifikus tartalommal – a Computer Vision észlelése
titleSuffix: Azure Cognitive Services
description: Ismertető a képek a Computer Vision API használatával kapcsolatos fogalmakat.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: a9c71fa7e5d86cfeb4fe6fab44bbce241546ccb8
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49342558"
---
# <a name="detecting-domain-specific-content"></a>Tartományspecifikus tartalom észlelése

Emellett a címkézési és a legfelső szintű kategorizálási, Computer Vision is támogatja a speciális (vagy tartomány-specifikus) információt. Speciális információk különálló módként alkalmazott, vagy a magas szintű kategorizálással valósítható meg. Tovább finomíthatja a 86-kategóriaelnevezési tartomány-specifikus modelleket hozzáadásával eszközként működik.

A tartomány-specifikus modelleket a két lehetőség van:

* Hatókörön belüli elemzés  
  Csak a kiválasztott modell elemzése egy HTTP POST-hívás meghívásával. Ha tudja, melyik modellt érdemes használni, adja meg a modell neve. Csak kap információkat megfelelő ehhez a modellhez. Például használhatja ezt a beállítást csak a keresett híresség-felismerés. A válasz lehetséges hírességek, megbízhatósági eredményeiket fűzni megfelelő listáját tartalmazza.
* Fejlett elemzés  
  Elemezheti a 86 kategóriaelnevezési kategóriák kapcsolatos további információkat. Ez a beállítás érhető el alkalmazásokban használható ahol felhasználókat szeretné, hogy általános képet elemzési adatok mellett egy vagy több tartomány-specifikus modelleket használva. Ez a metódus meghívásakor 86 kategóriaelnevezési osztályozó először nevezzük. Az ismert vagy egyező modellek, amelyek egyeznek, kategóriákra az osztályozó által igénybe vett meghívásához egy második fázishoz a következő. Például ha a `details` paraméter a HTTP POST-hívás vagy értéke "all" vagy "hírességek" tartalmazza, a metódus meghívja a hírességek osztályozó után a 86-kategória osztályozó nevezzük. Ha a kép akkor minősül `people_` vagy egy kategóriát, majd a hírességek osztályozó alkategóriája nevezzük.

## <a name="listing-domain-specific-models"></a>Tartomány-specifikus modelleket listázása

A Computer Vision által támogatott tartomány-specifikus modelleket listázhatja. Jelenleg a Computer Vision támogatja a következő tartomány-specifikus modelleket a tartomány-specifikus tartalom észlelése:

| Name (Név) | Leírás |
|------|-------------|
| hírességek | Hírességek felismerése, támogatja a lemezképek besorolni a `people_` kategória |
| Arcrész | Tereptárgyak felismerése, támogatja a lemezképek besorolni a `outdoor_` vagy `building_` kategóriák |

### <a name="domain-model-list-example"></a>Tartományi modell példa

A következő JSON-választ a Computer Vision által támogatott tartomány-specifikus modelleket sorolja fel.

```json
{
    "models": [
        {
            "name": "celebrities",
            "categories": ["people_", "人_", "pessoas_", "gente_"]
        },
        {
            "name": "landmarks",
            "categories": ["outdoor_", "户外_", "屋外_", "aoarlivre_", "alairelibre_",
                "building_", "建筑_", "建物_", "edifício_"]
        }
    ]
}
```

## <a name="next-steps"></a>További lépések

Tudnivalók a fogalmak [lemezképek kategorizálásához](concept-categorizing-images.md).