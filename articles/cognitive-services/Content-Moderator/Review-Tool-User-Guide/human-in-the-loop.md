---
title: További tudnivalók az eszközök áttekintése – Content Moderator
titleSuffix: Azure Cognitive Services
description: Ismerje meg a Content Moderator felülvizsgálati eszközt, amely egy olyan webhely, amely összehangolja a kombinált AI-és emberi felülvizsgálati moderálási erőfeszítéseket.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: pafarley
ms.openlocfilehash: a23e6d46ee6e79fd7a5cabf4434c561f7d83b31b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "76169502"
---
# <a name="content-moderator-review-tool"></a>Content Moderator felülvizsgálati eszköz

Az Azure Content Moderator olyan szolgáltatásokat biztosít, amelyekkel egyesítheti a gépi tanulási tartalmak moderálását emberi felülvizsgálatokkal, és a [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com) webhelye egy felhasználóbarát kezelőfelület, amely részletes hozzáférést biztosít ezekhez a szolgáltatásokhoz.

![A felülvizsgálati eszköz irányítópultja egy böngészőben](./images/0-dashboard.png)

## <a name="what-it-does"></a>Művelet

A [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com)a géppel támogatott moderálási API-kkal együtt használva a következő feladatokat hajthatja végre a tartalom-moderálási folyamat során:

- Egyetlen eszközkészletet használhat a tartalom moderálására több formátumban (szöveg, rendszerkép és videó).
- Automatizálhatja az emberi [felülvizsgálatok](../review-api.md#reviews) létrehozását, ha a moderálási API eredményei beérkeznek.
- Több felülvizsgálati csapatnak is hozzárendelhet vagy kiterjesztheti a tartalmi értékeléseket, amelyek a tartalom kategóriája vagy a felhasználói élmény szintjén vannak rendezve.
- Az alapértelmezett vagy az egyéni logikai szűrők ([munkafolyamatok](../review-api.md#workflows)) használatával rendezheti és követheti a tartalmakat anélkül, hogy kódot kellene írnia.
- Az [Összekötők](./configure.md#connectors) segítségével a Content moderator API-k mellett a Microsoft PhotoDNA, a Text Analytics és a Face Services használatával is feldolgozhatja a tartalmakat.
- Készítse el saját összekötőjét munkafolyamatok létrehozásához bármely API-vagy üzleti folyamathoz.
- A tartalom moderálási folyamataihoz tartozó fő teljesítmény-mérőszámok beolvasása.

## <a name="review-tool-dashboard"></a>Felülvizsgálati eszköz irányítópultja

Az **irányítópult** lapon megtekintheti az eszközön végzett tartalmi felülvizsgálatok fő mérőszámait. Tekintse meg a képek, szövegek és videók összesített, teljes és függőben lévő felülvizsgálatait. Megtekintheti azon felhasználók és csoportok részletezését is, amelyek elvégezték az értékeléseket, valamint az alkalmazott moderálási címkéket is.

![Irányítópult megtekintése](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Eszköz hitelesítő adatainak áttekintése

Ha regisztrál a [felülvizsgálati eszközre](https://contentmoderator.cognitive.microsoft.com), a rendszer kérni fogja, hogy válasszon ki egy Azure-régiót a fiókhoz. Ennek az az oka, hogy a [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com) létrehoz egy ingyenes próbaverziót az Azure Content moderator Services szolgáltatáshoz; erre a kulcsra szüksége lesz a REST-hívásokból vagy az ügyféloldali SDK-ból származó szolgáltatások eléréséhez. A kulcs-és API-végpont URL-címét a **Beállítások**  >  **hitelesítő adatai**lehetőség kiválasztásával tekintheti meg.

![Hitelesítő adatok Content Moderator](images/settings-6-credentials.png)

## <a name="next-steps"></a>További lépések

Lásd: [a felülvizsgálati eszköz konfigurálása](./configure.md) , amelyből megtudhatja, hogyan érheti el a felülvizsgálati eszköz erőforrásait és módosíthatja a beállításokat.