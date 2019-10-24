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
ms.openlocfilehash: 465f24c3d9b7f53c0b494f387cc5d21aa6bd83b5
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754232"
---
# <a name="content-moderator-review-tool"></a>Content Moderator felülvizsgálati eszköz

Az Azure Content Moderator olyan szolgáltatásokat biztosít, amelyekkel egyesítheti a gépi tanulási tartalmak moderálását emberi felülvizsgálatokkal, és a [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com) webhelye egy felhasználóbarát kezelőfelület, amely részletes hozzáférést biztosít ezekhez a szolgáltatásokhoz.

![A felülvizsgálati eszköz irányítópultja egy böngészőben](./images/0-dashboard.png)

## <a name="what-it-does"></a>Funkció

A [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com)a géppel támogatott moderálási API-kkal együtt használva a következő feladatokat hajthatja végre a tartalom-moderálási folyamat során:

- Egyetlen eszközkészletet használhat a tartalom moderálására több formátumban (szöveg, rendszerkép és videó).
- Automatizálhatja az emberi [felülvizsgálatok](../review-api.md#reviews) létrehozását, ha a moderálási API eredményei beérkeznek.
- Több felülvizsgálati csapatnak is hozzárendelhet vagy kiterjesztheti a tartalmi értékeléseket, amelyek a tartalom kategóriája vagy a felhasználói élmény szintjén vannak rendezve.
- Az alapértelmezett vagy az egyéni logikai szűrők ([munkafolyamatok](../review-api.md#workflows)) használatával rendezheti és követheti a tartalmakat anélkül, hogy kódot kellene írnia.
- Használjon [összekötőket](./configure.md#connectors) a tartalmak Microsoft PhotoDNA, Text Analytics és Face API-kkal való feldolgozásához a Content moderator API-k mellett.
- Készítse el saját összekötőjét munkafolyamatok létrehozásához bármely API-vagy üzleti folyamathoz.
- A tartalom moderálási folyamataihoz tartozó fő teljesítmény-mérőszámok beolvasása.

## <a name="review-tool-dashboard"></a>Felülvizsgálati eszköz irányítópultja

Az **irányítópult** lapon megtekintheti az eszközön végzett tartalmi felülvizsgálatok fő mérőszámait. Tekintse meg a képek, szövegek és videók összesített, teljes és függőben lévő felülvizsgálatait. Megtekintheti azon felhasználók és csoportok részletezését is, amelyek elvégezték az értékeléseket, valamint az alkalmazott moderálási címkéket is.

![Az irányítópult megtekintése](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Eszköz hitelesítő adatainak áttekintése

Ha regisztrál a [felülvizsgálati eszközre](https://contentmoderator.cognitive.microsoft.com), a rendszer kérni fogja, hogy válasszon ki egy Azure-régiót a fiókhoz. Ennek az az oka, hogy a [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com) létrehoz egy ingyenes próbaverziót az Azure Content moderator Services szolgáltatáshoz; erre a kulcsra szüksége lesz a REST-hívásokból vagy az ügyféloldali SDK-ból származó szolgáltatások eléréséhez. A kulcs-és API-végpont URL-címét a **beállítások**  >  a**hitelesítő adatok**lehetőség kiválasztásával tekintheti meg.

![Hitelesítő adatok Content Moderator](images/settings-6-credentials.png)

## <a name="next-steps"></a>Következő lépések

Lásd: [a felülvizsgálati eszköz konfigurálása](./configure.md) , amelyből megtudhatja, hogyan érheti el a felülvizsgálati eszköz erőforrásait és módosíthatja a beállításokat.