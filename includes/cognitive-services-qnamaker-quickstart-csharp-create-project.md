---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: include
ms.custom: include file
ms.date: 09/27/2018
ms.author: diberry
ms.openlocfilehash: 420757c5b7f6d19bb0abe87232e75497b7b97322
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48019094"
---
1. Nyissa meg a Visual Studio 2017 Community Editiont.
1. Hozzon létre egy új **konzolalkalmazás- (.NET Core-)** projektet, és adja neki a `QnaMakerQuickstart` nevet. Fogadja el az alapértelmezett értékeket a többi beállításnál.
1. A Solution Explorer (Megoldáskezelő) ablakban kattintson a jobb gombbal a projekt nevére (**QnaMakerQuickstart**), és válassza a **Manage NuGet Packages...** (NuGet-csomagok kezelése) lehetőséget.
1. A NuGet-ablakban válassza a **Browse** (Tallózás) lehetőséget, és keresse meg és telepítse a **Newtonsoft.JSON** csomagot. Ez a csomag a QnaMaker HTTP-válaszból visszaadott JSON elemzésére szolgál. 