---
title: Alkalmazás lépéseinek importálása
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/22/2020
ms.author: diberry
ms.openlocfilehash: 2cf588ed120b353958cc708189c86481cd247d8e
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85445923"
---
1. A [Luis portál](https://www.luis.ai) **saját alkalmazások** lapján válassza az **+ új alkalmazás a beszélgetéshez**, majd az **Importálás JSON-ként**lehetőséget. Keresse meg az előző lépésben mentett JSON-fájlt. Nem kell módosítania az alkalmazás nevét. Válassza a **kész** lehetőséget

1. A **kezelés** szakasz **verziók** lapján válassza ki a `sentiment` verziót, majd válassza a **klónozás** elemet a verzió klónozásához, és adjon neki egy új nevet `ml-entity` , majd válassza a **kész** gombot a klónozási folyamat befejezéséhez. Mivel a verzió neve az URL-útvonal részét képezi, a név nem tartalmazhat olyan karaktert, amely URL-címben nem érvényes.

    > [!TIP]
    > Az alkalmazás módosítása előtt az új verzióra történő klónozás ajánlott eljárás. Ha egy verzió módosításával fejeződik be, exportálja a verziót (. JSON-vagy. lu-fájlként), és keresse meg a fájlt a verziókövetés rendszerében.

1. Válassza **Build** a létrehozás **, majd a szándékok** lehetőséget, hogy megtekintse a Luis-alkalmazás fő építőelemeit.

    ![Váltás a verziók lapról a szándékok lapra.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)