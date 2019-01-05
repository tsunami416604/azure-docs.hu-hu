---
title: Exportálhatók és nem törli az adatokat – Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogyan exportálhatja, vagy a Custom Vision Service-adatok törlése.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: pafarley
ms.openlocfilehash: e3932c27b7741f04dfeda2a64f88a890b1e908ad
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54054979"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Exportálhatók és nem törli a felhasználói adatokat a Custom Vision

Content Moderator gyűjti a felhasználói adatokat a szolgáltatás, de az ügyfelek teljes hozzáféréssel rendelkeznek megtekintése, exportálása és hogy az adatok törlése a Custom Vision Service segítségével [képzési API](https://go.microsoft.com/fwlink/?linkid=865446).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Hogyan exportálhatja, és törli a felhasználói adatokat a Custom Vision további információkért lásd az alábbi táblázatot.

| Adatok | Az exportálási művelet | Törlési művelet |
| ---- | ---------------- | ---------------- |
| Fiók adatai (előfizetési kulcsok) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Törölje az Azure Portalon (az Azure-előfizetések). Vagy a "Saját fiók törlése" gomb segítségével CustomVision.ai beállítások oldalán (Microsoft-fiók előfizetés) | 
| Iteráció részletei | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Iteráció teljesítmény részletei | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Az ismétlések listája | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Projektek és a projekt részletei | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) és [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Címkéket | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) és [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Képek | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (URI-t biztosít a lemezkép letöltése) és [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (URI-t biztosít a lemezkép letöltése) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Exportált modellek | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | Törölt fiók törlése során | 
