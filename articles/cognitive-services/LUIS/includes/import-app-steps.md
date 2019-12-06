---
title: Alkalmazás lépéseinek importálása
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 12/03/2019
ms.author: diberry
ms.openlocfilehash: b0d1735df7a3ac36e20af771939aec6b8013f2df
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74840816"
---
1. A [Luis-portálon](https://preview.luis.ai)a **saját alkalmazások** oldalon válassza az **Importálás**, majd a **JSON-ként importálás**lehetőséget. Keresse meg az előző lépésben mentett JSON-fájlt. Nem kell módosítania az alkalmazás nevét. Válassza a **kész** lehetőséget

1. A **kezelés** szakasz **verziók** lapján válassza ki a verziót, majd válassza a **klónozás** elemet a verzió klónozásához, és adjon neki egy új 10 karakterből álló nevet, majd válassza a **kész** gombot a klónozási folyamat befejezéséhez. Mivel a verzió neve az URL-útvonal részét képezi, a név nem tartalmazhat olyan karaktert, amely URL-címben nem érvényes.

    > [!TIP]
    > Az alkalmazás módosítása előtt az új verzióra történő klónozás ajánlott eljárás. Ha befejez egy verziót, exportálja a verziót (. JSON-vagy. lu-fájlként), és keresse meg a fájlt a verziókövetés rendszerében.

1. Válassza a létrehozás **, majd a szándékok** lehetőséget, hogy megtekintse a Luis-alkalmazás fő építőelemeit.

    ![Váltás a verziók lapról a szándékok lapra.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)