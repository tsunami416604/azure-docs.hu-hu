---
title: Alkalmazáslépések importálása
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 12/03/2019
ms.author: diberry
ms.openlocfilehash: b0d1735df7a3ac36e20af771939aec6b8013f2df
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74840816"
---
1. Az [előzetes LUIS portálon](https://preview.luis.ai)a **Saját alkalmazások** lapon válassza az **Importálás**lehetőséget, majd az **Importálás JSON-ként**lehetőséget. Keresse meg a mentett JSON-fájlt az előző lépésből. Nem kell módosítania az alkalmazás nevét. Válassza a **Kész** lehetőséget

1. A **Kezelés** szakasz **Verziók** lapján jelölje ki a verziót, majd válassza a **Klónozás** lehetőséget a verzió klónozásához, és adjon neki egy új 10 karakteres nevet, majd a **Kész** lehetőséget a klónozási folyamat befejezéséhez. Mivel a verzió neve az URL-útvonal részét képezi, a név nem tartalmazhat olyan karaktert, amely URL-címben nem érvényes.

    > [!TIP]
    > Az alkalmazás módosítása előtt ajánlott az új verzióba való klónozás. Amikor befejezegy verziót, exportálja a verziót (.json vagy .lu fájlként), és ellenőrizze a fájlt a forrásvezérlő rendszerbe.

1. Válassza **a Build,** majd **leképezések** a leképezések, a LUIS-alkalmazás fő építőkövei.

    ![Váltás a Verziók lapról a Szándékok lapra.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)