---
title: Alkalmazás lépéseinek importálása
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/22/2020
ms.openlocfilehash: aa0ba1de63cbcf1d9485c71b6bec7a29afb5140f
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91545004"
---
1. A [Luis portál](https://www.luis.ai) **saját alkalmazások** lapján válassza az **+ új alkalmazás a beszélgetéshez**, majd az **Importálás JSON-ként**lehetőséget. Keresse meg az előző lépésben mentett JSON-fájlt. Nem kell módosítania az alkalmazás nevét. Válassza a **kész** lehetőséget

1. A **kezelés** szakasz **verziók** lapján válassza ki a `0.1` verziót, majd válassza a **klónozás** elemet a verzió klónozásához, és adjon neki egy új nevet `ml-entity` , majd válassza a **kész** gombot a klónozási folyamat befejezéséhez. Mivel a verzió neve az URL-útvonal részét képezi, a név nem tartalmazhat olyan karaktert, amely URL-címben nem érvényes.

    > [!TIP]
    > Az alkalmazás módosítása előtt az új verzióra történő klónozás ajánlott eljárás. Ha egy verzió módosításával fejeződik be, exportálja a verziót (. JSON-vagy. lu-fájlként), és keresse meg a fájlt a verziókövetés rendszerében.

1. Válassza **Build** a létrehozás **, majd a szándékok** lehetőséget, hogy megtekintse a Luis-alkalmazás fő építőelemeit.

    ![Váltás a verziók lapról a szándékok lapra.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)
