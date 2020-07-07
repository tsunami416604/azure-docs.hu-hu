---
title: Alkalmazás lépéseinek importálása
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/22/2020
ms.author: diberry
ms.openlocfilehash: 37f1b85b4ce8510d5e288df985a55dba659f0c9b
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86035705"
---
1. A [Luis portál](https://www.luis.ai) **saját alkalmazások** lapján válassza az **+ új alkalmazás a beszélgetéshez**, majd az **Importálás JSON-ként**lehetőséget. Keresse meg az előző lépésben mentett JSON-fájlt. Nem kell módosítania az alkalmazás nevét. Válassza a **kész** lehetőséget

1. A **kezelés** szakasz **verziók** lapján válassza ki a `0.1` verziót, majd válassza a **klónozás** elemet a verzió klónozásához, és adjon neki egy új nevet `ml-entity` , majd válassza a **kész** gombot a klónozási folyamat befejezéséhez. Mivel a verzió neve az URL-útvonal részét képezi, a név nem tartalmazhat olyan karaktert, amely URL-címben nem érvényes.

    > [!TIP]
    > Az alkalmazás módosítása előtt az új verzióra történő klónozás ajánlott eljárás. Ha egy verzió módosításával fejeződik be, exportálja a verziót (. JSON-vagy. lu-fájlként), és keresse meg a fájlt a verziókövetés rendszerében.

1. Válassza **Build** a létrehozás **, majd a szándékok** lehetőséget, hogy megtekintse a Luis-alkalmazás fő építőelemeit.

    ![Váltás a verziók lapról a szándékok lapra.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)
