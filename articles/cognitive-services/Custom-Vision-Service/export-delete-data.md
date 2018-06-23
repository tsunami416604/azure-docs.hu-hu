---
title: Exportálása vagy az egyéni stratégiai, Azure kognitív szolgáltatások adatok törlése |} Microsoft Docs
description: Megtudhatja, hogyan exportálása vagy az egyéni stratégiai adatok törlése.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/25/2018
ms.author: v-jaswel
ms.openlocfilehash: 49fc49b3181f56c23167cfbae18911e7db441f8c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349538"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Exportálja, vagy egyéni stratégiai felhasználói adatok törlése

Tartalom moderátor a szolgáltatás működtetéséhez felhasználói adatokat gyűjt, de az ügyfelek teljes hozzáféréssel rendelkeznek megtekintése, exportálás és az adatok törlése az egyéni stratégiai szolgáltatással [képzési API](https://go.microsoft.com/fwlink/?linkid=865446).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Törölni az egyéni stratégiai felhasználói adatokat és exportálása a további információkért lásd az alábbi táblázatot.

| Adatok | Az exportálási művelet | Törlési művelet |
| ---- | ---------------- | ---------------- |
| Fiók adatainak (előfizetés kulcsok) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Törölje az Azure portálon (Azure-előfizetések). Vagy a "A fiók törlése" gombra kattintva CustomVision.ai beállítások oldalán (Microsoft-fiók előfizetés) |
| Iteráció részletei | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Iteráció teljesítmény részletei | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Az ismétlés listája | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Projektek és a projekt részleteit | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) és [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) |
| Kép címkék | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) és [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) |
| Képek | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (a uri biztosít a lemezkép letöltése) és [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (a uri biztosít a lemezkép letöltése) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) |
| Exportált modellek | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | Fiók törlése törölve |
