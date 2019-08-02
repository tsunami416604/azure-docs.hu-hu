---
title: További tudnivalók az eszközök áttekintése – Content Moderator
titleSuffix: Azure Cognitive Services
description: Ismerje meg a Content Moderator felülvizsgálati eszközt, amely egy olyan webhely, amely összehangolja a kombinált AI-és emberi felülvizsgálati moderálási erőfeszítéseket.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: sajagtap
ms.openlocfilehash: 7f20b9c824045ac2f8c13df3ed8f776195de611a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564519"
---
# <a name="content-moderator-review-tool"></a>Content Moderator felülvizsgálati eszköz

Az Azure Content Moderator olyan szolgáltatásokat biztosít, amelyekkel egyesítheti a gépi tanulási tartalmak moderálását emberi felülvizsgálatokkal, és a [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com) webhelye egy felhasználóbarát kezelőfelület, amely részletes hozzáférést biztosít ezekhez a szolgáltatásokhoz.

![A felülvizsgálati eszköz irányítópultja egy böngészőben](./images/0-dashboard.png)

## <a name="what-it-does"></a>Művelet

A [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com)a géppel támogatott moderálási API-kkal együtt használva a következő feladatokat hajthatja végre a tartalom-moderálási folyamat során:

- Egyetlen eszközkészletet használhat a tartalom moderálására több formátumban (szöveg, rendszerkép és videó).
- Automatizálhatja az emberi [felülvizsgálatok](../review-api.md#reviews) létrehozását, ha a moderálási API eredményei beérkeznek.
- Több felülvizsgálati csapatnak is hozzárendelhet vagy kiterjesztheti a tartalmi értékeléseket, amelyek a tartalom kategóriája vagy a felhasználói élmény szintjén vannak rendezve.
- Az alapértelmezett vagy az egyéni logikai szűrők[](../review-api.md#workflows)(munkafolyamatok) használatával rendezheti és követheti a tartalmakat anélkül, hogy kódot kellene írnia.
- Használjon [összekötőket](./configure.md#connectors) a tartalmak Microsoft PhotoDNA, Text Analytics és Face API-kkal való feldolgozásához a Content moderator API-k mellett.
- Készítse el saját összekötőjét munkafolyamatok létrehozásához bármely API-vagy üzleti folyamathoz.
- A tartalom moderálási folyamataihoz tartozó fő teljesítmény-mérőszámok beolvasása.

## <a name="review-tool-dashboard"></a>Felülvizsgálati eszköz irányítópultja

Az **irányítópult** lapon megtekintheti az eszközön végzett tartalmi felülvizsgálatok fő mérőszámait. Tekintse meg a képek, szövegek és videók összesített, teljes és függőben lévő felülvizsgálatait. Megtekintheti azon felhasználók és csoportok részletezését is, amelyek elvégezték az értékeléseket, valamint az alkalmazott moderálási címkéket is.

![Irányítópult megtekintése](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Eszköz hitelesítő adatainak áttekintése

Ha regisztrál a [felülvizsgálati eszközre](https://contentmoderator.cognitive.microsoft.com), a rendszer kérni fogja, hogy válasszon ki egy Azure-régiót a fiókhoz. Ennek az az oka, hogy a [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com) létrehoz egy ingyenes próbaverziót az Azure Content moderator Services szolgáltatáshoz; erre a kulcsra szüksége lesz a REST-hívásokból vagy az ügyféloldali SDK-ból származó szolgáltatások eléréséhez. A kulcs-és API-végpont URL-címét a **Beállítások** > **hitelesítő adatai**lehetőség kiválasztásával tekintheti meg.

![Hitelesítő adatok Content Moderator](images/settings-6-credentials.png)

## <a name="next-steps"></a>További lépések

Lásd: [a felülvizsgálati eszköz konfigurálása](./configure.md) , amelyből megtudhatja, hogyan érheti el a felülvizsgálati eszköz erőforrásait és módosíthatja a beállításokat.