---
title: Ismerje meg a felülvizsgálati eszköz alapelvei – a Content Moderator
titlesuffix: Azure Cognitive Services
description: Ismerje meg, a Content Moderator, tekintse át az eszközről, olyan webhely, amely egy kombinált mesterséges Intelligencia és emberi moderálás erőfeszítés koordinálja.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: sajagtap
ms.openlocfilehash: b7ec997fd3e9bfe294050893d80fd57a96a47aae
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58755881"
---
# <a name="content-moderator-review-tool"></a>Content Moderator felülvizsgálati eszköz

Az Azure Content Moderator úgy, hogy az emberi ellenőrzések, machine learning tartalommoderálási szolgáltatásokat biztosít, és a [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com) webhely el egy felhasználóbarát előtér, részletes hozzáférést biztosító ezeket a szolgáltatásokat.

![A felülvizsgálati eszköz irányítópult böngészőben](./images/0-dashboard.png)

## <a name="what-it-does"></a>Művelet

A [vizsgálóeszköz](https://contentmoderator.cognitive.microsoft.com), amikor a gépi támogatású képmoderálás API-kkal együtt használva lehetővé teszi, hogy a tartalom-jóváhagyás folyamatban a következő feladatok elvégzését:

- Adott eszközök használatával többféle formátumban (szöveg, kép- és) tartalommoderálás.
- Automatizálhatja az emberi [felülvizsgálatok](../review-api.md#reviews) mikor a moderálási API eredmények származnak az.
- Tartalom kategória vagy felhasználói szinten szerint vannak rendezve, több felülvizsgálati csapatok tartalom felülvizsgálatok eszkalálása vagy rendelhet hozzá.
- Használja az alapértelmezett vagy egyéni logikai szűrők ([munkafolyamatok](../review-api.md#workflows)) rendezéséhez és nyomon követheti a tartalmat, kód írása nélkül.
- Használat [összekötők](./configure.md#connectors) a tartalom Microsoft PhotoDNA, a Szövegelemzés és a Face API-k a tartalmak Moderator API-k mellett.
- Saját összekötők hozhat létre munkafolyamatokat bármilyen API-hoz vagy az üzleti folyamat.
- Bevezetés a fő teljesítménymutatói a tartalom-jóváhagyás folyamatokat.

## <a name="review-tool-dashboard"></a>Felülvizsgálati eszköz irányítópult

Az a **irányítópult** lapon látható tartalom felülvizsgálatok belül az eszköz kész lényeges metrikákat. Tekintsük meg a teljes összeg, teljes körű, és a függőben lévő kép, szöveg és videótartalmak felülvizsgálatának. Megtekintheti a felhasználók és csoportok értékelések, valamint a moderálás címkék alkalmazott befejezett áttekintését is.

![Az irányítópult megtekintése](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Tekintse át az eszköz hitelesítő adatai

Regisztráció az a [vizsgálóeszköz](https://contentmoderator.cognitive.microsoft.com), kérni fogja egy Azure-régiót válassza azt a fiókot. Ennek az az oka az [vizsgálóeszköz](https://contentmoderator.cognitive.microsoft.com) kulcsot hoz létre egy ingyenes próbaverziós az Azure Content Moderator szolgáltatások; ezt a kulcsot egy REST-hívást vagy ügyféloldali SDK-val a szolgáltatások eléréséhez szüksége lesz. A kulcs és az API végpont URL-címe kiválasztásával megtekintheti **beállítások** > **hitelesítő adatok**.

![A Content Moderator hitelesítő adatok](images/settings-6-credentials.png)

## <a name="next-steps"></a>További lépések

Lásd: [konfigurálása a vizsgálóeszközt](./configure.md) megtudhatja, hogyan felülvizsgálati eszköz erőforrások eléréséhez és módosításához a beállítások.