---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: e37d3ef5b6f65ad31bc19f9f8c15350014d1c9ad
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348187"
---
Az a [Anomáliadetektálási kereső API](https://labs.cognitive.microsoft.com/en-us/project-anomaly-finder), idő adatsorozat adatok JSON formátumban feltölteni az API-végpontot, és olvassa el az eredmény az API-válaszból. Az idő az adatsorozat adatait feltöltheti, minden adatpontnál tartalmazza:  
* Időbélyeg - adatpont időbélyegző. Győződjön meg arról, hogy azt az UTC dátum idő karakterláncot, például használó "2017-08-01T00:00:00Z"
* Érték –, hogy az adatpontok mérése

Az eredmények foglalják magukban:
* Időszak -, amelyeket az API-t használ a rendellenességek észlelését gyakorisága
* WarningText - lehetséges figyelmeztetés információk
* ExpectedValue – az előre jelzett érték a Learning alapuló modell
* IsAnomaly - az eredmény az adatpont van rendellenességek észlelését, vagy nem mindkét irányban (igényeiben jelentkező vagy immerzióban)
* IsAnomaly_Neg - az eredmény az adatpont van rendellenességeket negatív irányban (immerzióban)
* IsAnomaly_Pos - az eredmény az adatpont van rendellenességeket pozitív irányban (igényeiben jelentkező)
* A felső határa, amely adatpont van még re normál határozza meg a ExpectedValue és UpperMargin UpperMargin-
* LowerMargin - (ExpectedValue - LowerMargin) határozza meg, az alsó határa, hogy az adatpontok az továbbra is vélhetően normál futtatáshoz

Az adategyezmény részleteit található [Itt](../apiref.md).

