---
title: Ismerje meg az Ellenőrzés eszköz fogalmait - Tartalommoderátor
titleSuffix: Azure Cognitive Services
description: Ismerje meg a Tartalommoderátor-ellenőrző eszközt, amely egy olyan webhely, amely koordinálja a kombinált AI-t és az emberi ellenőrzés moderálási erőfeszítéseit.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: pafarley
ms.openlocfilehash: a23e6d46ee6e79fd7a5cabf4434c561f7d83b31b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169502"
---
# <a name="content-moderator-review-tool"></a>Tartalommoderátor-ellenőrző eszköz

Az Azure Content Moderator szolgáltatásokat nyújt a gépi tanulási tartalom moderálásés az emberi felülvizsgálatok kombinálására, és a [Felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com) webhelye egy felhasználóbarát előtér, amely részletes hozzáférést biztosít ezekhez a szolgáltatásokhoz.

![A felülvizsgálati eszköz irányítópultja a böngészőben](./images/0-dashboard.png)

## <a name="what-it-does"></a>Művelet

A [Véleményezés eszköz](https://contentmoderator.cognitive.microsoft.com), ha a gépáltal támogatott moderálási API-kkal együtt használja, lehetővé teszi a következő feladatok elvégzését a tartalommoderálási folyamatban:

- Egyetlen eszközkészlet segítségével több formátumban (szövegben, képben és videóban) moderálja a tartalmat.
- Automatizálja az emberi [vélemények](../review-api.md#reviews) létrehozását, amikor a moderálásapi-eredmények beérkeznek.
- A tartalomértékelésekhozzárendelése vagy eszkalálása több ellenőrzési csoporthoz, tartalomkategória vagy élményszint szerint rendezve.
- Az alapértelmezett vagy egyéni logikai szűrők[(munkafolyamatok](../review-api.md#workflows)) segítségével kód írása nélkül rendezheti és nyomon követheti a tartalmat.
- [Összekötők](./configure.md#connectors) használatával a Tartalommoderátor API-k mellett a Microsoft PhotoDNA, a Text Analytics és a Face szolgáltatásokkal is feldolgozhat tartalmakat.
- Saját összekötőt hozhat létre, hogy munkafolyamatokat hozzon létre bármely API-hoz vagy üzleti folyamathoz.
- A tartalommoderálási folyamatok legfontosabb teljesítménymutatóit kaphatja meg.

## <a name="review-tool-dashboard"></a>Az eszköz irányítópultjának áttekintése

Az **Irányítópult** lapon megtekintheti az eszközben végzett tartalomértékelések fő mutatóit. Megtekintheti a kép-, szöveg- és videotartalomösszes, teljes és függőben lévő értékeléseinek számát. Megtekintheti az értékeléseket elvégzett felhasználók és csoportok, valamint az alkalmazott moderálási címkék lebontását is.

![Irányítópult megtekintése](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Az eszköz hitelesítő adatainak áttekintése

Amikor regisztrál a [Véleményezés eszközzel,](https://contentmoderator.cognitive.microsoft.com)a rendszer kéri, hogy válasszon ki egy Azure-régiót a fiókjához. Ennek az az oka, hogy a [Véleményezés eszköz](https://contentmoderator.cognitive.microsoft.com) ingyenes próbakulcsot hoz létre az Azure Content Moderator szolgáltatásaihoz; szüksége lesz erre a kulcsra, hogy hozzáférjen bármelyik szolgáltatáshoz egy REST-hívásból vagy ügyfél SDK-ból. A kulcs- és API-végpont URL-címét a **Beállítások** > hitelesítő adatok lehetőséget választva**tekintheti**meg.

![Tartalommoderátor hitelesítő adatai](images/settings-6-credentials.png)

## <a name="next-steps"></a>További lépések

A [Véleményezés eszköz konfigurálása](./configure.md) című témakörből megtudhatja, hogyan érheti el az Eszköz erőforrásait, és hogyan módosíthatja a beállításokat.