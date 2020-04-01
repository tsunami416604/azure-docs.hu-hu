---
title: Alkalmazáslépések importálása
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/30/2020
ms.author: diberry
ms.openlocfilehash: b7b8befa0f5871b65b9b5621bfb99c659bf07235
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422753"
---
1. Az [előzetes LUIS portálon](https://preview.luis.ai)a **Saját alkalmazások** lapon válassza a **+ Új alkalmazás a beszélgetéshez**lehetőséget, majd **az Importálás JSON-ként**lehetőséget. Keresse meg a mentett JSON-fájlt az előző lépésből. Nem kell módosítania az alkalmazás nevét. Válassza a **Kész** lehetőséget

1. A **Kezelés** csoport **Verziók** lapján `0.1` jelölje ki a verziót, majd válassza a **Klónozás** lehetőséget `ml-entity`a verzió klónozásához, és adjon neki egy új 10 karakteres nevet a lehetőségre, majd válassza a **Kész** lehetőséget a klónozási folyamat befejezéséhez. Mivel a verzió neve az URL-útvonal részét képezi, a név nem tartalmazhat olyan karaktert, amely URL-címben nem érvényes.

    > [!TIP]
    > Az alkalmazás módosítása előtt ajánlott az új verzióba való klónozás. Ha befejezi a verzió módosítását, exportálja a verziót (.json vagy .lu fájlként), és ellenőrizze a fájlt a forrásvezérlő rendszerbe.

1. Válassza **a Build,** majd **leképezések** a leképezések, a LUIS-alkalmazás fő építőkövei.

    ![Váltás a Verziók lapról a Szándékok lapra.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)