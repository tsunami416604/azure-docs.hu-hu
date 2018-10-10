---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: 9280790f6692096a0b3909c9d1dfab2e94a8c0d7
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2018
ms.locfileid: "48904602"
---
Az a [Anomáliadetektálási kereső API](https://labs.cognitive.microsoft.com/en-us/project-anomaly-finder), töltse fel az idősoros adatokat JSON formátumban, az API-végpont, és az eredmény ezután olvasni az API-válasz. Feltöltheti az idősorozat-adatok, az egyes tartalmazza:  
* Időbélyeg - az adatpont jelölő időbélyegző. Ellenőrizze, hogy azt használja a UTC dátum-idő karakterláncot, például "2017-08-01T00:00:00Z"
* Érték – az adott adatpont mérése

Az eredmények tartalmazzák:
* Időtartam - a periodicitás, amely az API-t használ a rendellenességek észlelése
* WarningText – figyelmeztetés lehetséges információk
* ExpectedValue – az előre jelzett érték szerint a tanulási modell alapján
* IsAnomaly - arról, hogy az adatpontok a rendellenességeket, vagy nem mindkét irányban (csúcsok és)
* IsAnomaly_Neg – hogy-e az adatpontok rendellenességek negatív irányba (DIP) eredménye
* IsAnomaly_Pos – hogy-e az adatpontok rendellenességek pozitív irányba (ugrásszerűen) eredménye
* UpperMargin - ExpectedValue és UpperMargin összege határozza meg, a felső határa, amely adatpont van továbbra is úgy Gondoltuk szokásos módon
* LowerMargin - (ExpectedValue - LowerMargin) meghatározza, hogy az alsó határérték, hogy adatpont van továbbra is úgy Gondoltuk szokásos módon

Az adatok szerződés részletei találhatók [Itt](../apiref.md).

