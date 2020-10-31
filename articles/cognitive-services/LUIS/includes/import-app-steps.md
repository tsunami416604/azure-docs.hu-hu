---
title: Alkalmazás lépéseinek importálása
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 10/16/2020
ms.openlocfilehash: b70a22e502de5441b5c91a8f33357375834f54e1
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130731"
---
1. A [Luis portál](https://www.luis.ai) **saját alkalmazások** lapján válassza az **+ új alkalmazás a beszélgetéshez** , majd az **Importálás JSON-ként** lehetőséget. Keresse meg az előző lépésben mentett JSON-fájlt. Nem kell módosítania az alkalmazás nevét. Válassza a **kész** lehetőséget

1. A **kezelés** szakasz **verziók** lapján válassza ki a `0.1` verziót, majd válassza a **klónozás** elemet a verzió klónozásához, és adjon neki egy új nevet `ml-entity` , majd válassza a **kész** gombot a klónozási folyamat befejezéséhez. Mivel a verzió neve az URL-útvonal részét képezi, a név nem tartalmazhat olyan karaktert, amely URL-címben nem érvényes.

    > [!TIP]
    > Az alkalmazás módosítása előtt az új verzióra történő klónozás ajánlott eljárás. Ha egy verzió módosításával fejeződik be, exportálja a verziót (. JSON-vagy. lu-fájlként), és keresse meg a fájlt a verziókövetés rendszerében.

1. Válassza a **Létrehozás** lehetőséget a képernyő tetején, majd a bal oldali navigációs menüben kattintson a **leképezések** elemre. Látni fogja az alkalmazás szándékait, amelyek a LUIS-alkalmazások fő építőelemei.
